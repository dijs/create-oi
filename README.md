create-oi: iRobot Create&#174; driver for node
========================================

An API for interacting with an iRobot Create. Because robots are fun.

---
The iRobot Create natively provides a low-level serial protocol called the "Open Interface" (OI) documented [here](http://www.irobot.com/filelibrary/pdfs/hrd/create/Create%20Open%20Interface_v2.pdf).
What this library attempts to do is implement a simple and intuitive API on top of OI in [node.js](http://nodejs.org/) using the great [serialport](https://github.com/voodootikigod/node-serialport) library.

Prerequisites
-------------
 * Have an iRobot Create 
 * Installed usb-serial drivers and connected laptop, Raspberry Pi, Gumstix or similarly capable machine to your Create
   * see: [Windows installation instructions](http://homesupport.irobot.com/app/answers/detail/a_id/362/~/installing-the-usb-serial-port) if using a windows laptop (linux comes with drivers, macs will need [FTDI drivers](http://www.ftdichip.com/Drivers/D2XX.htm))
 * node and npm are installed on said machine

Installation
------------
```sh
npm install create-oi
```

Getting Started
-----
After plugging in your Create USB cable you'll need to find 
out what serial port your operating system has assigned your Create.

On linux:
```sh
$ dmesg | grep tty
```
On a mac:
```sh
$ ls /dev/tty.*
```
On a windows box, open device manager and look under "Ports":
```bat
C:\> mmc devmgmt.msc
```

In your code you'll probably want to start with these two lines:
```javascript
var robot = require("create-oi");

robot.init({ serialport: "/dev/tty.usbserial-A2001nf6" });
```
Make sure to set your `serialport` to the device name you found earlier. 
On my mac for me this is `"/dev/tty.usbserial-A2001nf6"`, yours will be different.

The API is event-based, meaning all the important stuff happens in event callbacks.
The first event you'll need to deal with is the `ready` event which gets fired when
the module sucessfully connects to the Create over the serial port. Note that the 
`this` context for all your callback handlers will be set to the `create-oi` module 
itself, so you can easily call `drive`, `rotate` or any other module method within a 
callback. Several events such as `bump` or `wheeldrop` make contain information about 
which specific sensor was triggered since there are multiple on the Create.

```
robot.on('ready', function() {
    // start by going forward
    this.drive(100, 0);
});

robot.on('bump', function(e) {
    console.log(e.direction);
    ...
});

```
