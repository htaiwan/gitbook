# Chapter 20: Advanced Game Physics

#### 前言

上一章我們已經完成相關的前置作業了，完成車子各種的物理行爲設置，接下來就是要開始進行車子的操控，看車子移動方式是否有貼近真實。

------

#### 大綱

- Getting started
- [Creating an infinite ground plane](#1)
  - Updating the floor’s position
- [Adding engine force](#2)
- [Adding steering](#3)
- [Limiting the speed](#4)
- Resetting the game

------

<h2 id="1">Creating an infinite ground plane</h2>

因為車子會在我們提供的虛擬平面進行移動，所以建立無限平面讓車子移動不會受到局限，利用`SCNFloor`來達到無限平面的效果。

```swift
var groundNode: SCNNode!

/////////////////////////////

    func createFloorNode() -> SCNNode {
        // 利用SCNFloor
        let floorGeometry = SCNFloor()
        floorGeometry.reflectivity = 0.0

        let floorMaterial = SCNMaterial()
        floorMaterial.diffuse.contents = UIColor.white
        // This is a clever hack that hides the floor but keeps things like reflections and shadows visible.
        floorMaterial.blendMode = .multiply
        floorGeometry.materials = [floorMaterial]

        let floorNode = SCNNode(geometry: floorGeometry)

        floorNode.position = SCNVector3Zero
        floorNode.physicsBody = SCNPhysicsBody(type: .static, shape: nil)
        floorNode.physicsBody?.restitution = 0.5
        floorNode.physicsBody?.friction = 4.0
        floorNode.physicsBody?.rollingFriction = 0.0

        return floorNode
    }
```

------

<h2 id="2">Adding engine force</h2>

車子已經設置好他移動時的物理行為，但車子要怎樣才會移動，那就是要受力才會移動，因此如何添加力到車子上。

```Swift

    var isThrottling = false
    var engineForce: CGFloat = 0
    let defaultEngineForce: CGFloat = 10.0

///////////////////


    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        isThrottling = true
    }

    override func touchesEnded(_ touches: Set<UITouch>, with event: UIEvent?) {
        isThrottling = false
    }

    func updateVehiclePhysics() {
        guard self.gameState == .playGame else { return }

        if isThrottling {
            engineForce = defaultEngineForce
        } else {
            engineForce = 0
        }

        // 將力量賦予給需要的輪子
        physicsVehicle.applyEngineForce(engineForce, forWheelAt: 0)
        physicsVehicle.applyEngineForce(engineForce, forWheelAt: 1)
        physicsVehicle.applyEngineForce(engineForce, forWheelAt: 2)
        physicsVehicle.applyEngineForce(engineForce, forWheelAt: 3)
    }
```

------

<h2 id="3">Adding steering</h2>

控制車子的輪胎的左右，利用`CoreMotion`

```Swift
import CoreMotion

    let motionManager = CMMotionManager()
    let steeringClamp: CGFloat = 0.6
    var steeringAngle: CGFloat = 0

////////////////////////////////////////////

    func stopAccelerometer() {
        motionManager.stopAccelerometerUpdates()
    }

    func startAccelerometer() {
        guard motionManager.isAccelerometerAvailable else { return }
        // 每秒60次的資料更新
        motionManager.accelerometerUpdateInterval = 1/60.0
        // 每次資料更新，都進行對應的輪子角度轉移控制
        motionManager.startAccelerometerUpdates(
            to: OperationQueue.main,
            withHandler: { (accelerometerData: CMAccelerometerData?,
                error: Error?) in
                self.updateSteeringAngle(acceleration:
                    accelerometerData!.acceleration)
        })
    }

    func updateSteeringAngle(acceleration: CMAcceleration) {
        steeringAngle = (CGFloat)(acceleration.y)

        // 控制輪子的偏移角度在一定範圍
        if steeringAngle < -steeringClamp {
            steeringAngle = -steeringClamp;
        } else if steeringAngle > steeringClamp {
            steeringAngle = steeringClamp;
        }
    }

////////////////////////////////////////////

        // 控制前面兩個輪子的轉移角度
        physicsVehicle.setSteeringAngle(steeringAngle, forWheelAt: 0)
        physicsVehicle.setSteeringAngle(steeringAngle, forWheelAt: 1)

```

------

<h2 id="4">Limiting the speed</h2>

為了控制速度在一定程度，必須不斷重複添加或移除engine force，就像在開車時，需要不斷地踏油門，放油門保持車子的速度。在AR世界中，我們利用給速度一個限值來達到類似的結果。

```swift
  var maximumSpeed: CGFloat = 2.0


 if self.physicsVehicle.speedInKilometersPerHour > maximumSpeed {
            engineForce = 0.0
 }

```

