# Controlling robots with NativeScript Bluetooth

## Introduction

Building an army of robots has never been easier than now. 

All you need is a team of obedient robots like [Wowwee Mip](http://wowwee.com/mip/) and an app to control them.

<!--Should I add pictures of the robot and drone???-->

The Mip uses BLE (Bluetooth Low Energy) as the protocol for communication.

In this article we will look into the core concepts of Bluetooth, how to use `nativescript-bluetooth` and how to build an app for the robot. 
We will also try to spice up the app with a bit of rxjs where applicable.

This article will focus solely on the mechanics of using bluetooth, however it won't provide any UI code. To see a fully working example see (nativescript-mip-simple-ng)[https://github.com/sebawita/nativescript-mip-simple-ng];

## Explaining Bluetooth core concepts

### Services, Characteristics, Instructions

Bluetooth commands are divided into a group of `Services`, like `Dashboard service`, `Weapons service` and `Drive Control service`. Then each service contains a group of `characteristics`, for example the `Dashboard service` contains `Speed Indicator characteristic` and `Fuel Indicator characteristic`. 

Each service is designated to either receive `read instructions` to provide feedback (Dashboard service and its characteristics) or to receive `write instructions` to perform operations (Weapons and Drive Control services).

Note that each service and characteristic can be identified with the help of its UUID. The default services tend to be 4 characters long (i.e. `fa00`) while other services are a bit longer and look like this `9a66fb00-0800-9191-11e4-012d1540cb8e`.

![Services and Characteristics](./Services-Characteristics.png?raw=true "Services and Characteristics")

### The overall steps to communicate with BLE devices

Regardless of what technology you use to communicate with a BLE device you always have to follow a similar set of steps.

 * Make sure Bluetooth is enabled and available
 * Scan for BLE devices in the area
 * Allow user to select a device and connect
 * Use Service + Characteristic to send Read/Write commands

<!--
 * Add nativescript-bluetooth
 * Request permissions
 * Scan for device
 * Connect to it
 * Read / Write from the Characteristic
 -->

## Working with a Wowee Mip robot

For the purposes of this article we will be using [Wowwee Mip](http://wowwee.com/mip/). 

In order to send instructions to a MiP robot we need to use `serviceUUID = 'fe05'` and `characteristicUUID = 'ffe9'`. 
Wowee has also prepared a [MiP Protocol documentation](https://github.com/WowWeeLabs/MiP-BLE-Protocol/blob/master/MiP-Protocol.md) that lists all available commands.


For example the protocol provides us with the description of how to change the color of the chest LED.
It contains the code for this instruction `0x84` and then the required parameters 3 byte values for Red, Green and Blue. 

![Set Chest Led](./Set-Chest-Led.png?raw=true "Set Chest Led")

So if we want to call it with an RGB value of `FFAA88`, the command should be something like: '0x84,0xFF,0xAA,0x88'

## NativeScript Bluetooth plugin api

In NativeScript the best plugin for bluetooth communications is [nativescript-bluetooth](https://www.npmjs.com/package/nativescript-bluetooth). It allows you to do everything you need to command an army or robots.

### Setup

First we need to add the plugin to your NativeScript project:

```javascript
tns plugin add nativescript-bluetooth
```

### Require

Then we need to require the module.

For Vanilla JS use:

```javascript
var bluetooth = require("nativescript-bluetooth");
```

For TypeScript (strongly recommended) use:

```javascript
import bluetooth = require('nativescript-bluetooth');
```

### Check if bluetooth is available

Before we start communicating with the devices, we need to:

#### first check whether bluetooth is enabled 

To do this just call `isBluetoothEnabled`, which will return a promise with a boolean flag.

```javascript
bluetooth.isBluetoothEnabled().then(
  enabled => console.log("Enabled? " + enabled)
);
```

<!--HINT-->
We could spice this up a little with rxjs and create an Observable that will emit an event every time bluetooth is enabled or disabled.

```javascript
import { Observable } from 'rxjs/Observable';
import 'rxjs/add/operator/distinctUntilChanged';

listenToBluetoothEnabled(): Observable<boolean> {
	return new Observable(observer => {
		bluetooth.isBluetoothEnabled()
			.then(enabled => observer.next(enabled))

		let intervalHandle = setInterval(
			() => {
				bluetooth.isBluetoothEnabled()
					.then(enabled => observer.next(enabled))
			}
			, 1000);

		// stop checking every second on unsubscribe
		return () => clearInterval(intervalHandle);
	})
  .distinctUntilChanged();
}
```

and then subscribe to it, so that we get notified about changes:

```javascript
this.listenToBluetoothEnabled()
.subscribe(enabled => this.isBluetoothEnabled = enabled);
```

This gives as an elegant mechanism to handle this scenario and ask the user to enable bluetooth if it is disabled.

#### second check/request app permissions to use Bluetooth

This step is only required for Android, as iOS handles this automatically.

To check the permissions call: `bluetooth.hasCoarseLocationPermission()`, which will return a promise with the result.

```javascript
bluetooth.hasCoarseLocationPermission()
.then(granted =>
  console.log("Has Location Permission? " + granted);
);
```

If false, then you should request the permissions by calling: `bluetooth.requestCoarseLocationPermission()`.

<!--HINT-->
I tend to skip the permission check and always call the request function when I am loading the first page of my app. The plugin is smart enough to ignore my request if the permission has been already granted.

For example in an Angular project I do it from `ngOnInit` like this:

```javascript
ngOnInit() {
  bluetooth.requestCoarseLocationPermission();
}
```

### Search

Once bluetooth is ready to go it is time for us to search for devices.

To do that we need to use the `startScanning` function and provide how long you want to scan for as `seconds` and a callback for each discovered device, as `onDiscovered`.

<!--Add serviceUUIDs - if Ed fixes the issue 
We can use serviceUUIDs to filter out any devices that don't provide the services we are interested in. 
In the case of our robot we need the serviceUUID `ffe5` -->

The two most important pieces of information we get from `onDiscovered` are:

 * peripheral.name - which you can use to display to the user for selection
 * peripheral.UUID - the UUID for this device, which you need to connect to the device.

We will use the results of `onDiscovered` to populate the `devices` array, which you can use to display the data in a listview.

```javascript
private devices: any[] = [];

scan() {
  this.devices = [];

  bluetooth.startScanning({
    seconds: 3,
    onDiscovered: (peripheral: Peripheral) => {
      if(peripheral.name) {
        console.log(`UUID: ${peripheral.UUID} name: ${peripheral.name}`)
        this.devices.push(peripheral);
      }
    }
  })
}
```


### Connect

If everything goes well you should see a list of devices including our MiP robot.
To connect to it we need to call the `connect` function and provide:

 * UUID - the UUID of your device
 * onConnected function - a callback function, which is called when the connection is fully established. Use this function to navigate to a view with device controls (like a view with goForward, goBack buttons)
 * onDisconnected function - a callback function, which is called when the device gets disconnected. Use this function to move back to the scaner view
 
```javascript
connect(UUID: string) {
  bluetooth.connect({
    UUID: UUID,
    onConnected: (peripheral: Peripheral) => {
      alert('Connected');
      //here you can navigate to the controller view
    },
    onDisconnected: (peripheral: Peripheral) => {
      alert('Device Disconnected');
      //here you can navigate to the scan view
    }
  })
}
```

### Send Command

Once we are connected to our robot we can start sending some instructions.

According to the protocol we can tell the robot to move forward and back using the below params.

![Move Forward and Back](./Move-FWD-BACK.png?raw=true "Move Forward and Back")

To move forward at speed 20 for 500ms and we need provide the following values:

 * Instruction code: `0x71`
 * Speed: 20 in hex `0x14`
 * Time: 500/7 = 71 in hex `0x47`

We can provide the value in two different ways:

 * as a String of comma separated hex values, i.e. `value: '0x71,0x14,0x47'`
 * as a `Uint8Array` with numeric values, i.e. `value: new Uint8Array([0x71,0x14,0x47])` or `value: new Uint8Array([0x71,20,71])`

<!--Hint-->
String seems to be a good solution for hardcoded values, however Uint8Array is much better when we need to deal with a constant stream of values coming from the UI.

To issue a command we need to call the `writeWithoutResponse` function which takes
 
 * serviceUUID - in our case this is `ffe5`
 * characteristicUUID - in our case this is `ffe9`
 * peripheralUUID - in our case this is the UUID of the robot we connected to

```javascript
	moveForward() {
		bluetooth.writeWithoutResponse({
			serviceUUID: 'ffe5',
			characteristicUUID: 'ffe9',
			peripheralUUID: this.deviceUUID,
			value: '0x71,0x14,0x47'
		})
	}

	moveBack() {
		bluetooth.writeWithoutResponse({
			serviceUUID: 'ffe5',
			characteristicUUID: 'ffe9',
			peripheralUUID: this.deviceUUID,
			value: new Uint8Array([0x72,0x20,0x40])
		})
	}
```


### Fun with accelerometer + continuous move

To make it more fun we can add the (nativescript-accelerometer)[https://www.npmjs.com/package/nativescript-accelerometer] plugin and control the robot with hand gestures.

First let's add the plugin to our project.

```javascript
tns plugin add nativescript-accelerometer
```

### Accelerometer with rxjs Observable

### Listen

### Converting numbers (the bit about positive 0-127 then 255-128)

<!--### Compare to web-bluetooth?-->