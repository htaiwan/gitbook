# BaedekAR

------

## 大綱

- [**Introduction**](#1)
- [**Importing the Reference Images**](#2)
- [**Detecting Reference Images in the Real World**](#3)
- [**Making Detected Images Tappable**](#4)
- [**Annotating Detected Images With AR text**](#5)
- [**Blocking Images**](#6)
- [**An AR Cure for Clown Phobia**](#7)
- [**Conclusion**](#8)

------

<h2 id="1">Introduction</h2>

- In this section, you’ll be building an AR guidebook — a Baedeker — for paintings and photos in galleries, museums, or any place that displays 2D art.

------

<h2 id="2">Importing the Reference Images</h2>

- The first step in building BaedekAR is providing it with the set of images that it should detect and recognize. You’ll do that in this step.

![](../.gitbook/assets/118.png)

------

<h2 id="3">Detecting Reference Images in the Real World</h2>

- With the reference images imported into the app, it’s time to take the first step and detect those images in the real world, making note of their location and size.

```swift
  func startARSession() {
    // Make sure that we have an AR resource group.
    guard let referenceImages = ARReferenceImage.referenceImages(inGroupNamed: "AR Resources", bundle: nil) else {
      fatalError("This app doesn't have an AR resource group named 'AR Resources'!")
    }

    // Set up the AR configuration.
    config.worldAlignment = .gravityAndHeading
    config.detectionImages = referenceImages

    // Start the AR session.
    sceneView.session.run(config, options: [.resetTracking, .removeExistingAnchors])

    statusViewController.scheduleMessage("Look around for art!",
                                         inSeconds: 7.5,
                                         messageType: .contentPlacement)
  }
```

```swift
 // This delegate method gets called whenever the node corresponding to
  // a new AR anchor is added to the scene.
  func renderer(_ renderer: SCNSceneRenderer, didAdd node: SCNNode, for anchor: ARAnchor) {
    // We only want to deal with image anchors, which encapsulate
    // the position, orientation, and size, of a detected image that matches
    // one of our reference images.
    guard let imageAnchor = anchor as? ARImageAnchor else { return }
    let referenceImage = imageAnchor.referenceImage
    let imageName = referenceImage.name ?? "[Unknown]"

    let isArtImage = !imageName.hasPrefix("block_this-")
    var statusMessage = ""
    if isArtImage {
      statusMessage = "Found \(artworkDisplayNames[imageName] ?? "artwork")."
    } else {
      statusMessage = "Unpleasant image blocked."
    }

    DispatchQueue.main.async {
      self.statusViewController.cancelAllScheduledMessages()
      self.statusViewController.showMessage(statusMessage)
    }

    // Draw the appropriate plane over the image.
    updateQueue.async {

    }

    DispatchQueue.main.async {
      self.statusViewController.cancelAllScheduledMessages()
      self.statusViewController.showMessage(statusMessage)
    }
  }
```



------

<h2 id="4">Making Detected Images Tappable</h2>

- Now that the app can detect the reference images in the real world, let’s make it so that the user can tap them to find out more about them.

```swift
 // Draw the appropriate plane over the image.
    updateQueue.async {
      var planeNode = SCNNode()

      if isArtImage {
        // If the detected artwork is one that we’d like to highlight (and one which we’d
        // like the user to tap to find out more), draw an “artwork” plane and
        // the name of the artwork over the image.
        planeNode = self.createArtworkPlaneNode(withReferenceImage: referenceImage, andImageName: imageName)
        let nameNode = self.createArtworkNameNode(withImageName: imageName)
        node.addChildNode(nameNode)
      } else {
        // If the detected artwork is one that we’d like to obscure,
        // draw a “blocker” plane over the image.
        planeNode = self.createBlockerPlaneNode(withReferenceImage: referenceImage, andImageName: imageName)
        planeNode.name = self.blockedName
      }
```

```swift
// Create a translucent plane that will overlay the detected artwork,
  // which the user can tap for more information.
  // The plane will flash momentarily when it first appears.
  func createArtworkPlaneNode(withReferenceImage referenceImage: ARReferenceImage,
                              andImageName imageName: String) -> SCNNode {
    // "Flash" the plane so the user is aware that it’s now available.
    // Called when the plane first appears.
    let flashPlaneAction = SCNAction.sequence([
      .wait(duration: 0.25),
      .fadeOpacity(to: 0.85, duration: 0.25),
      .fadeOpacity(to: 0.25, duration: 0.25),
      .fadeOpacity(to: 0.85, duration: 0.25),
      .fadeOpacity(to: 0.25, duration: 0.25),
      .fadeOpacity(to: 0.85, duration: 0.25),
      .fadeOpacity(to: 0.25, duration: 0.25),
      ])

    // Draw the plane.
    let plane = SCNPlane(width: referenceImage.physicalSize.width * 1.5,
                         height: referenceImage.physicalSize.height * 1.5)
    let planeNode = SCNNode(geometry: plane)
    planeNode.opacity = 0.25
    planeNode.runAction(flashPlaneAction)
    planeNode.name = imageName
    return planeNode
  }
```

```swift
  @objc func handleScreenTap(sender: UITapGestureRecognizer) {
    let tappedSceneView = sender.view as! ARSCNView
    let tapLocation = sender.location(in: tappedSceneView)
    let tapsOnPlane = tappedSceneView.hitTest(tapLocation,
                                              options: nil)
    if !tapsOnPlane.isEmpty {
      let firstObject = tapsOnPlane.first
      if let firstObjectName = firstObject?.node.name {
        performSegue(withIdentifier: "showArtNotes",
                     sender: firstObjectName)
      }
    }
  }
```

------

<h2 id="5">Annotating Detected Images With AR text</h2>

- Let’s improve the app by displaying the name of the detected image using AR text. We’ll also make use of billboard constraints to make sure that the text is always facing the user, so that it’s easy to read.

```swift
  // Create a text node to display the name of an artwork.
  func createArtworkNameNode(withImageName imageName: String) -> SCNNode {
    let textScaleFactor: Float = 0.15
    let textFont = "AvenirNext-BoldItalic"
    let textSize: CGFloat = 0.2
    let textDepth: CGFloat = 0.02

    let artworkNameText = SCNText(string: artworkDisplayNames[imageName],
                                  extrusionDepth: 0.02)
    artworkNameText.font = UIFont(name: textFont, size: textSize)?.withTraits(traits: .traitBold)
    artworkNameText.alignmentMode = kCAAlignmentCenter

    artworkNameText.firstMaterial?.diffuse.contents = UIColor.orange
    artworkNameText.firstMaterial?.specular.contents = UIColor.white

    artworkNameText.firstMaterial?.isDoubleSided = true
    artworkNameText.chamferRadius = CGFloat(textDepth)

    let artworkNameTextNode = SCNNode(geometry: artworkNameText)
    artworkNameTextNode.scale = SCNVector3(textScaleFactor, textScaleFactor, textScaleFactor)
    artworkNameTextNode.name = imageName

    let (minBound, maxBound) = artworkNameText.boundingBox
    artworkNameTextNode.pivot = SCNMatrix4MakeTranslation((maxBound.x - minBound.x) / 2,
                                                          minBound.y,
                                                          0)

    let billboardConstraint = SCNBillboardConstraint()
    billboardConstraint.freeAxes = SCNBillboardAxis.Y
    artworkNameTextNode.constraints = [billboardConstraint]

    return artworkNameTextNode
  }
```



------

<h2 id="6">Blocking Images</h2>

- What if there were images that we wanted to block from the user?

------

<h2 id="7">An AR Cure for Clown Phobia</h2>

- Let’s protect the user from scary clown images by blocking them with a soothing picture of Ray.

```swift
  // Create an opaque plane featuring the soothing image of Ray Wenderlich,
  // which will be used to obscure unwanted images.
  // Yes, we’re in “Black Mirror” territory now.
  func createBlockerPlaneNode(withReferenceImage referenceImage: ARReferenceImage,
                              andImageName imageName: String) -> SCNNode {
    // Draw the plane.
    let plane = SCNPlane(width: referenceImage.physicalSize.width * 2.0,
                         height: referenceImage.physicalSize.height * 2.0)
    let planeNode = SCNNode(geometry: plane)
    planeNode.geometry?.firstMaterial?.diffuse.contents = UIImage(named: "ray")
    planeNode.name = imageName
    return planeNode
  }
```

------

<h2 id="8">Conclusion</h2>