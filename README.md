#thinkgear

A node.js client for the ThinkGear Socket Protocol supporting Neurosky EEG headsets

Electroencephalography is the full name for EEG which is the recording of electrical activity along the scalp.  Our Brains are a mass of interconnected Neurons that interact with each other.  As they do, they create patterns that become our thoughts. 

The [ThinkGear Socket Protocol](http://developer.neurosky.com/docs/lib/exe/fetch.php?media=thinkgear_socket_protocol.pdf) (TGSP) is a JSON-based protocol for the transmission and receipt of ThinkGear EEG headset data.

The ThinkGear Connector (TGC) runs as a background process on your computer and is responsible for directing headset data from the serial port to an open network socket.

The table below gives a general synopsis of some of the commonly-recognized frequencies that tend to be generated by different types of activity in the brain:


| Brainwave Type | Frequency range | Mental states and conditions                           |
|----------------|-----------------|--------------------------------------------------------|
| Delta          | 0.1Hz to 3Hz    | Deep dreamless sleep, non-REM sleep, unconscious       |
| Theta          | 4Hz to 7Hz      | Intuitive, creative, recall, fantasy, imaginary, dream, meditative |
| Alpha          | 8Hz to 12Hz     | Relaxed (but not drowsy) tranquil, conscious           |
| Low Beta       | 12Hz to 15Hz    | Formerly SMR, relaxed yet focused, integrated          |
| Midrange Beta  | 16Hz to 20Hz    | Thinking, aware of self & surroundings                 |
| High Beta      | 21Hz to 30Hz    | Alertness, agitation                                   |
| Low Gamma      | 31Hz to 40hz    | Multi-sensory processing                               |
| High Gamma     | 41Hz to 50hz    |                               |




##Installation


```
$ npm install thinkgear
```

##Usage

Require the thinkgear module

```
var thinkgear = require('thinkgear');
```

Create a mindwave client, and add listeners for the various event emitter based events that are supported:

```

var thinkgear = require('thinkgear');

var options = {
	appName: 'openminded',
	appKey: '0fc2141b1b45c573cc2d3a763b8d71c5bde2391',
	enableRawOutput: false,
	format: 'Json' //'BinaryPacket'
};

var client = thinkgear.create(options);

client.on('data',function(data){
	console.log(data);
});

client.on('error',function(error){
	console.log(error);
});

client.on('close',function(){
	console.log('closing.');
});

client.connect();

```

##Authorization
- `appName`. Required. A human-readable name identifying the client application. This can be a maximum of 255 characters.

- `appKey`. Required. The key used by the client application to identify itself. This must be 40 hexadecimal characters, ideally generated using an SHA-1 digest. See the Note below.


##Configuration

- `enableRawOutput` - Optional. Whether raw sensor output should be included in the transmit- ted data. The value of this parameter should be either true or false (default).

- `format` - Optional. The format in which headset data should be transmitted to the client. The value of this parameter should be either "BinaryPacket" (default) or "Json". When specifying this value, make note of the capitalization!


##Response

- `poorSignalLevel`. A quantfier of the quality of the brainwave signal. This is an integer value that is generally in the range of 0 to 200, with 0 indicating a good signal and 200 indicating an off-head state.

- `blinkStrength`. The strength of a detected blink. This is an integer in the range of 0-255. This data is represented in its own JSON object, as in the sample below.
- `eSense`. A container for the eSenseTM attributes. These are integer values between 0 and 100, where 0 is perceived as a lack of that attribute and 100 is an excess of that attribute.

  - attention. The eSense Attention value.
  - meditation. The eSense Meditation value.

- `eegPower`. A container for the EEG powers. These may be either integer or Floating-point
values.

  - `delta`. The "delta" band of EEG.
  - `theta` The "theta" band of EEG.
  - `lowAlpha` The "low alpha" band of EEG.
  - `highAlpha` The "high alpha" band of EEG.
  - `lowBeta` The "low beta" band of EEG.
  - `highBeta` The "high beta" band of EEG.
  - `lowGamma` The "low gamma" band of EEG.
  - `highGamma` The "high gamma" band of EEG.


##Format json

Even though JSON is the preferred transmission format, the binary packet format (used in earlier versions of TGSP) is the default format.  The Binary Socket Packet Format will eventually be deprecated.

When `enableRawOutput` is set to false, you get a json response.

```
{
	eSense: {
		attention: 61,
		meditation: 56
	},
	eegPower: {
		delta: 416462,
		theta: 33581,
		lowAlpha: 3876,
		highAlpha: 3141,
		lowBeta: 1568,
		highBeta: 3124,
		lowGamma: 3519,
		highGamma: 1469
	},
	poorSignalLevel: 0,
	blinkStrength: 65
}
```


##Setup a Neurosky EEG mindwave headset

Please refer to your user manual to setup your Neurosky EEG mindwave headset.

This unsigned one-byte integer value describes how poor the signal measured by the ThinkGear is. It ranges in value from 0 to 255. Any non-zero value indicates that some sort of noise contamination is detected. The higher the number, the more noise is detected. A value of 200 has a special meaning, specifically that the ThinkGear electrodes aren't contacting a person's skin.

```
{ poorSignalLevel: 200 }
```

Using the Mindwave can be a little tricky at first. You must position the electrode that goes on your forehead (FP1) correctly. Just as important is the clip on your ear lobe which is an essential part of the noise cancellation that is required for it to work.


##Test

```
make test
```

##Code Coverage

```
make cover
```


##Debug

In order to run the examples for this module in debug mode, we use debug.js internally.

```
DEBUG=thinkgear node example
```

.