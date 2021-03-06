# Patchwire for iOS
iOS Library for the [Patchwire](https://github.com/twisterghost/patchwire) multiplayer server framework created by Michael Barrett.

[![Build Status](https://travis-ci.org/VictorBX/patchwire-ios.svg?branch=master)](https://travis-ci.org/VictorBX/patchwire-ios)

## Requirements

- iOS 8.0+
- Xcode 7.3+

## Installation

#### Carthage
[Carthage](https://github.com/Carthage/Carthage) is an easy to use dependency manager. Once you have [installed](https://github.com/Carthage/Carthage#installing-carthage) Carthage, create a [Cartfile](https://github.com/Carthage/Carthage#if-youre-building-for-ios-tvos-or-watchos) with the following:

```
github "VictorBX/patchwire-ios"
```

#### CocoaPods
Coming soon

#### Manually

You can simply drag and drop the files within `Source` into your project.

## Usage

Once you have installed Patchwire-iOS into your project, we can start setting it up. Fist import patchwire.
```swift
import PatchwireiOS
```

#### Connecting to the server
```swift
Patchwire.sharedInstance.verboseLogging = false
Patchwire.sharedInstance.configure(serverIP: "localhost", serverPort: 3001)
Patchwire.sharedInstance.connect()
```

#### Sending a command
With every command, you can send a dictionary containing some information.
```swift
let chatCommand = Command(command: "chat", data: ["username": "victor", "message": "hello"])
Patchwire.sharedInstance.sendCommand(chatCommand)
```

#### Receiving a command
To receive incoming commands from the server, use `NSNotificationCenter`.
```swift
var chatCommandKey = Patchwire.sharedInstance.notificationKey(forCommand: "chat")
NSNotificationCenter.defaultCenter().addObserver(self, selector: #selector(ChatTableViewController.didReceiveChatCommand(_:)), name: chatCommandKey, object: nil)
```

The `NSNotification`'s `userInfo` dictionary will contain the JSON blob sent from the server.

#### Receiving IO Stream Events
As the client sends and receives events from the server, the input and output streams send out a message when certain events are triggered. To get those `NSStreamEvent`s from `NSStreamDelegate`'s `- stream:handleEvent:`, you will need to use `NSNotificationCenter`. A notification will be sent out by the notification center with names from the `PatchwireStreamEventKey` enum. The `userInfo` dictionary will contain the stream event.
```swift
// Receiving input stream events
NSNotificationCenter.defaultCenter().addObserver(self, selector: ViewController.didReceiveInputStreamEvent(_:), name: PatchwireStreamEventKey.InputStreamEvent.rawValue, object: nil)

// Receiving output stream events
NSNotificationCenter.defaultCenter().addObserver(self, selector: ViewController.didReceiveOutputStreamEvent(_:), name: PatchwireStreamEventKey.OutputStreamEvent.rawValue, object: nil)
```

#### Disconnect/Reconnect
```swift
// To disconnect from the server
Patchwire.sharedInstance.disconnect()

// To reconnect to the server
Patchwire.sharedInstance.reconnect(connectAfterSeconds: 5)
```

## Example Chat Project

#### Server Side

To run the example chat project, first let's setup the Patchwire server.

1. Open a terminal and go to the `Example/server` directory
2. Install Patchwire using `npm install patchwire@0.2.0` if you haven't done so already
3. Run the server using `node example.js`

Your server should now be running locally on `localhost:3001`.

#### Client Side

Next setup the iOS client. For this example, we're using two iOS simulators that will have a build of the chat app.

1. Open `Patchwire-iOS.xcodeproj` using Xcode
2. Select an iOS simulator (ex. iPhone 6s)
3. Hit run (⌘R)
4. Stop the simulator (⌘.)
5. Repeat step 2, 3, and 4 but select another simulator (ex. iPhone 6s Plus)
6. Close all simulators
7. Open a terminal and: `cd /Applications/Xcode.app/Contents/Developer/Applications`
8. Type `open -n Simulator.app`
9. Repeat step 8 (a warning will pop up, ignore it)
10. On the second simulator, under `Hardware > Device` select a device with the chat app installed (ex. if the first simulator is an iPhone 6s Plus, select iPhone 6s)
11. Run the chat app on both devices.

Result:

[![ScreenShot](/Resources/PatchwireExample.png)](https://www.youtube.com/watch?v=xg4aGimb_Rk)

## License

patchwire-ios is released under the MIT license. See LICENSE for details.
