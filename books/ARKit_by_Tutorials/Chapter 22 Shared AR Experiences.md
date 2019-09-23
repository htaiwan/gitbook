# Chapter 22: Shared AR Experiences

#### 前言

這章的內容之前在hack day曾經玩過，就是多人分享AR體驗。以這個app為例，某個人在某地留個塗鴉，另外一個人可以在同個地方看到這個塗鴉。

------

#### 大綱

- [Getting started](#1)
- [Establishing a peer connection](#2)
- [Sending data to peers](#3)
- [Receiving data and relocalizing](#4)

------

<h2 id="1">Getting started</h2>
本章重點就是實作share button, 讓多人可以互動進行留下塗鴉在不同地方。



------



<h2 id="2">Establishing a peer connection</h2>

- **The Multipeer Connectivity framework** on iOS uses either Wi-Fi or Bluetooth to transport data. 
- 建立連線有兩種方式
  - 利用`MCNearbyServiceBrowser`，讓app可以邀請別人來加入一個session中。
  - 利用`MCBrowserViewController`，這個方式會提供內建原生UI，讓app尋找可用的session建立連線。(這一章是利用這個方式)

```swift
  @IBAction func shareWorldMap(_ sender: Any) {
    // 利用MCBrowserViewController尋找附近可用的session
    let mcBrowserVC = MCBrowserViewController(serviceType: PeerSession.serviceType, session: peerSession.mcSession)
    mcBrowserVC.delegate = self
    self.present(mcBrowserVC, animated: true, completion: nil)
  }
}

extension ViewController: MCBrowserViewControllerDelegate {

  func browserViewControllerDidFinish(_ browserViewController: MCBrowserViewController) {
    browserViewController.dismiss(animated: true, completion: nil)
  }

  func browserViewControllerWasCancelled(_ browserViewController: MCBrowserViewController) {
    browserViewController.dismiss(animated: true, completion: nil)
  }
  
}

// MARK: - Shared AR
extension ViewController {
  func handleReceivedData(_ data: Data, from peer: MCPeerID) {

  }
}

```

- `MCAdvertiserAssistant` object to tell the peers that your app can join a multipeer session of a particular service type. The advertiserAssistant also provides a standard user interface for the local peer to accept or decline an invitation to connect to a session.

```swift
import MultipeerConnectivity

class PeerSession: NSObject, MCAdvertiserAssistantDelegate {

  private let peerID = MCPeerID(displayName: UIDevice.current.name)
  static let serviceType = "arsketchsession"
  private(set) var mcSession: MCSession!
  private var advertiserAssistant: MCAdvertiserAssistant!
  private let receivedDataHandler: (Data, MCPeerID) -> Void

  init(receivedDataHandler: @escaping (Data, MCPeerID) -> Void) {
    // 在呼叫init之前，要把這個class中宣告let的參數都進行初始化
    self.receivedDataHandler = receivedDataHandler
    super.init()

    mcSession = MCSession(peer: peerID, securityIdentity: nil, encryptionPreference: .required)
    mcSession.delegate = self
		// 透過這個MCAdvertiserAssistant進行連線處理
    advertiserAssistant = MCAdvertiserAssistant(serviceType: PeerSession.serviceType, discoveryInfo: nil, session: self.mcSession)
    advertiserAssistant.delegate = self
    advertiserAssistant.start()
  }

}

extension PeerSession: MCSessionDelegate {

  func session(_ session: MCSession, peer peerID: MCPeerID, didChange state: MCSessionState) {
  }

  func session(_ session: MCSession, didReceive data: Data, fromPeer peerID: MCPeerID) {
  }

  // 目前這個app並不支援stream的資料傳輸，只支援data傳輸
  func session(_ session: MCSession, didReceive stream: InputStream, withName streamName: String, fromPeer peerID: MCPeerID) {
    fatalError("This service does not send/receive streams.")
  }

  // 目前這個app並不支援resource的資料傳輸，若有支援則progress會提供目前傳輸的進度
  func session(_ session: MCSession, didStartReceivingResourceWithName resourceName: String, fromPeer peerID: MCPeerID, with progress: Progress) {
    fatalError("This service does not send/receive resources.")
  }

  // 當支援resource的資料傳輸，且傳輸完畢，則會提供localURL告訴目前resource的位置
  func session(_ session: MCSession, didFinishReceivingResourceWithName resourceName: String, fromPeer peerID: MCPeerID, at localURL: URL?, withError error: Error?) {
    fatalError("This service does not send/receive resources.")
  }

}
```



------



<h2 id="3">Sending data to peers</h2>


傳送資料

```Swift
  func sendToAllPeers(_ data: Data) {
    do {
      try mcSession.send(data, toPeers: mcSession.connectedPeers, with: .reliable)
    } catch {
      print("""
            error sending data to peers:
            \(error.localizedDescription)
            """)
    }
  }
```

```swift
  func browserViewControllerDidFinish(_ browserViewController: MCBrowserViewController) {
    sceneView.session.getCurrentWorldMap { (worldMap, error) in
        guard let map = worldMap else {
          print("Error: \(error!.localizedDescription)")
          return
        }
        // 取得產生AR物件的環境截圖，這樣對方才知道在哪個地方會出現AR物件
        guard let snapshotAnchor = SnapshotAnchor(capturing: self.sceneView) else {
          fatalError("Can't take snapshot")
        }
        map.anchors.append(snapshotAnchor)
        // 將資料進行encode
        guard let data =
          try? NSKeyedArchiver.archivedData(withRootObject: map,
                                            requiringSecureCoding: true)else {
                                              fatalError("can't encode map")
                      
      }
        self.peerSession.sendToAllPeers(data)
    }
    browserViewController.dismiss(animated: true, completion: nil)
  }

```





------





<h2 id="4">Receiving data and relocalizing</h2>

接收資料，這裡要注意的地方，須先取得worldmap才可以把ar object添加上去

```swift
extension ViewController {
  func handleReceivedData(_ data: Data, from peer: MCPeerID) {
    do {
      // parse data
      if let worldMap = try NSKeyedUnarchiver.unarchivedObject(ofClass: ARWorldMap.self, from: data) {
        DispatchQueue.main.async {
          // restore virtual content to the screen
          self.displaySnapshotImage(from: worldMap)
        }
        // configure the AR session using the world map
        configureARSession(for: worldMap)
        // set mapProvider to remember the sender
        mapProvider = peer
        return
      }
    } catch {
      print("can't decode data received from \(peer)")
    }
    // 表示之前已經有取得ARWorldMap
    if !isRelocalizingMap {
      do {
        // 等待ARLineAnchor的資料
        if let anchor = try NSKeyedUnarchiver.unarchivedObject(ofClass: ARLineAnchor.self, from: data) {
          sceneView.session.add(anchor: anchor)
        }
      } catch {
        print("unknown data received from \(peer)")
      }
    }

  }
}
```

