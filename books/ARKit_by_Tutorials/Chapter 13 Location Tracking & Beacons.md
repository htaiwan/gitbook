# Chapter 13: Location Tracking & Beacons

## 前言

這張大部分時間是都講Location處理，當user進入到感興趣的區域，可以主動提醒使用者一些內容。

------

### 大綱

- Getting started
- [Locating the user](#1)
  - Enabling Core Location
- [Geofencing](#2)
  - Start region monitoring
  - Monitoring a region
  - Reacting to a region change
  - Stop region monitoring
  - Bonus: Distance calculation
  - Time for testing
- [Beacons](#3)
  - Detecting a beacon
  - Managing beacons monitoring
  - Manually stopping region and beacon monitoring
  - Consuming the beacon detection
  - Responding to beacon notifications
  - Timer and QR code detection
- Testing

------

<h2 id="1">Locating the user</h2>

位置權限要求有兩種`when in use`和`always`

在iOS10之前只有always這個選項，在iOS11之後，必須加入這兩個選項到plist中。

```xml
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>RazeAd requires access to your phone's location to
  notify when you enter a geofence containing ads</string>
<key>NSLocationWhenInUseUsageDescription</key>
<string>RazeAd requires access to your phone's location to
  notify when you enter a geofence containing ads</string>
```

```Swift

    func initialize() {
        locationManager.delegate = self
        locationManager.activityType = .otherNavigation
        locationManager.requestAlwaysAuthorization()
        locationManager.stopUpdatingLocation()
    }
```

In fact, LocationManager will implement a total of **nine methods** defined in CLLocationManagerDelegate, **four of which will be forwarded via the custom LocationManagerDelegate** to AdViewController.


```Swift
// MARK: - LocationManagerDelegate
extension AdViewController: LocationManagerDelegate {
    // MARK: Location
    func locationManager(_ locationManager: LocationManager,
                         didEnterRegionId regionId: String) {
    }

    func locationManager(_ locationManager: LocationManager,
                         didExitRegionId regionId: String) {
    }

    // MARK: Beacons
    func locationManager(_ locationManager: LocationManager,
                         didRangeBeacon beacon: CLBeacon) {
    }

    func locationManager(_ locationManager: LocationManager,
                         didLeaveBeacon beacon: CLBeacon) {
    }
}
```

------

<h2 id="2">Geofencing</h2>

Geofencing翻譯成地理圍欄，也就是現實世界中的虛擬邊界。在上一節中，我們可以獲取使用者的當前位置，但我們目的並不是要位置，而是想要知道使用者是否在某個特定區域的範圍中。

- **Monitoring a region**

我們可以在單一裝置最多moniter20個regions。

```Swift
    func startMonitoring(location: CLLocationCoordinate2D, radius: Double, identifier: String) throws {
        guard CLLocationManager.isMonitoringAvailable(for: CLCircularRegion.self) else {
            throw GeofencingError.notSupported
        }
        guard CLLocationManager.authorizationStatus() == .authorizedAlways else {
            throw GeofencingError.notAuthorized
        }

        trackedLocation = location

        let region = CLCircularRegion(center: location, radius: radius, identifier: identifier)

        region.notifyOnExit = true
        region.notifyOnEntry = true

        locationManager.startMonitoring(for: region)

        // Delay state request by 1 second due to an old bug
        // http://www.openradar.me/16986842
        DispatchQueue.main.asyncAfter(deadline: DispatchTime.now() + DispatchTimeInterval.seconds(1), qos: .default, flags: []) {
            self.locationManager.requestState(for: region)
        }
    }
}

//////////////////////////////////////////////

    func locationManager(_ manager: CLLocationManager, didEnterRegion region: CLRegion) {
        if let region = region as? CLBeaconRegion {

        } else {
            print("Entered in region: \(region)")
            delegate?.locationManager(self, didEnterRegionId: region.identifier)
        }
    }

    func locationManager(_ manager: CLLocationManager, didExitRegion region: CLRegion) {
        print("Left region \(region)")
        delegate?.locationManager(self, didExitRegionId: region.identifier)
    }


    func locationManager(_ manager: CLLocationManager, didDetermineState state: CLRegionState, for region: CLRegion) {
        switch state {
        case .inside:
            locationManager(manager, didEnterRegion: region)
        case .outside, .unknown:
            break
        }
    }

    func locationManager(_ manager: CLLocationManager, monitoringDidFailFor region: CLRegion?, withError error: Error) {
        print("Geofencing monitoring failed for region " + "\(String(describing: region?.identifier))," + "error: \(error.localizedDescription)")
    }

```

- **Bonus: Distance calculation**

```Swift
    func locationManager(_ manager: CLLocationManager, didUpdateLocations locations: [CLLocation]) {
        guard let currentLocation = locations.last else { return }
        guard let trackedLocation = trackedLocation else { return }

        let location = CLLocation(latitude: trackedLocation.latitude, longitude: trackedLocation.longitude)
        let distance = currentLocation.distance(from: location)

        print("Distance: \(distance)")
    }
```

------

<h2 id="3">Beacon</h2>

Beacon也可以用來做到像Geofencing的功能，只是可以用在更細微的區域，進行所謂的微定位，如大型買場，博物館..等。距離範圍分成三種Far(10公尺以上)，Near(10公尺以內), Immediate(幾公分內)。

- **Managing beacons monitoring**

```Swift

    func startMonitoring(beacons: [CLBeaconRegion]) {
        for beacon in beacons {
            startMonitoring(beacon: beacon)
        }
    }

    func startMonitoring(beacon: CLBeaconRegion) {
        guard CLLocationManager.isRangingAvailable() else {
            print("[ERROR] Beacon ranging is not available")
            return
        }

        locationManager.startMonitoring(for: beacon)
    }

    func stopMonitoring(beacons: [CLBeaconRegion]) {
        for beacon in beacons {
            stopMonitoring(beacon: beacon)
        }
    }

    func stopMonitoring(beacon: CLBeaconRegion) {
        locationManager.stopRangingBeacons(in: beacon)
        locationManager.stopMonitoring(for: beacon)
    }

```

- **Consuming the beacon detection**

最後要把beacon偵測跟QR Code掃描綁在一起。當進到某個特定beacon範圍內會自動觸發QR Code掃描。

```Swift
    func locationManager(_ locationManager: LocationManager,
                         didRangeBeacon beacon: CLBeacon) {
        beaconStatusImage.isHidden = false
        beaconStatusLabel.isHidden = false

        switch beacon.proximity {
        case .immediate:
            beaconStatusImage.image = #imageLiteral(resourceName: "arKit-marker-1")
        case .near:
            beaconStatusImage.image = #imageLiteral(resourceName: "arKit-marker-2")
        case .far:
            beaconStatusImage.image = #imageLiteral(resourceName: "arKit-marker-3")
        case .unknown:
            beaconStatusImage.image = #imageLiteral(resourceName: "arKit-marker-4")
        }

        // Start auto scan, but only if the app is in the foreground
        // and there is no active billboard
        if UIApplication.shared.applicationState == .active &&
            (billboard == nil || billboard?.hasBillboardNode == false) {
            startAutoscanTimer()
        }
    }

    func locationManager(_ locationManager: LocationManager,
                         didLeaveBeacon beacon: CLBeacon) {
        stopAutoscanTimer()

        beaconStatusImage.isHidden = true
        beaconStatusLabel.isHidden = true
    }
```

