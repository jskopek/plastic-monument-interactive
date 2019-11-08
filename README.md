# Plastic Monument - Interactive Test

The playground for experimenting with a web-based, interactive light base for the Plastic Monument




# Install

1. `npm init`
2. `npm install --save johnny-five` - arduino communication library
3. `npm install nodebots-interchange --save`  - simplifies process of flashing firmware on artudino
4. `npm install node-pixel --save  `  - control neopixel lights
4. `npm install firmata@1.0.0 --save`  - fix connection bug



# Install Arduino Firmware

I'm running an Ardunio Mega, so I type:

 `npx interchange install git+https://github.com/ajfisher/node-pixel -a mega --firmata`

 Uno users would replace with `-a uno`


# Hook up lights

![Example](https://github.com/ajfisher/node-pixel/raw/4047c7e1222d892e5a80665491302e4aa56eb91d/docs/breadboard/custom_firmata_bb.png)

[Here is a guide](https://github.com/ajfisher/node-pixel/blob/4047c7e1222d892e5a80665491302e4aa56eb91d/docs/firmata.md#wiring)


# Create the Test code

Create a file named pixeltest.js and populate it with the following


```
var firmata = require("firmata");
var pixel = require("node-pixel");

var opts = {};
if (process.argv[2] == undefined) {
    console.log("Please supply a device port to connect to");
    process.exit();
}

opts.port = process.argv[2];

var strip = null;

var board = new firmata.Board(opts.port, function() {

    console.log("Firmata ready, lets add light");

    strip = new pixel.Strip({
        data: 6,
        length: 4,
        firmata: board,
    });

    var pos = 0;
    var colors = ["red", "green", "blue", "yellow", "cyan", "magenta", "white"];
    var current_color = 0;

    var blinker = setInterval(function() {

        strip.color("#000"); // blanks it out

        if (++pos >= strip.length) {
            pos = 0;
            if (++current_color>= colors.length) current_color = 0;
        }
        strip.pixel(pos).color(colors[current_color]);

        strip.show();
    }, 1000/2);
});
```

# Run test command

`npm run pixeltest.js /dev/cu.usbmodem14201`

Will likely need to replace port with the one your arduino is conneted to. On a mac, do an `'ls -lA /dev/* | grep usb` to find it.
