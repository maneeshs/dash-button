# Dash Button

Dash Button is a small server that reacts to Amazon Dash buttons on your WiFi network. You can write event handlers that Dash Button will run when it detects someone has pressed your Dash button.

Dash Button is designed to run on a Raspberry Pi. Specifically, it runs on [Raspbian](https://www.raspbian.org/) (Jessie or newer) and supports modern Node.js.

## Installation and Setup

Dash Button's runs on Node 4 and up on OS X and Linux. It depends on [libpcap](http://www.tcpdump.org/):

```sh
# Ubuntu and Debian
sudo apt-get install libpcap-dev
# Fedora and CentOS
sudo yum install libpcap-devel
```

Install Dash Button in your project using npm:

```sh
npm install --save dash-button
```

You will need to configure Dash Button with the MAC address of each of your Dash buttons, plus code to run when you press them.

The examples here use ES2016. You can run them with Babel configured for Stage 0 or just manually translate the code to whatever programming language you like as long as it runs on Node.

### Setting Up Your Dash Button

Follow Amazon's instructions to add your WiFi credentials to your Dash button, but skip the last step of choosing which product to order when you press the button. Your button is correctly configured if its LED flashes white for a few seconds before turning red when you press it. Note that the Dash button throttles presses, so you may have to wait a minute if you've pressed it recently.

### Finding the MAC Address of Your Dash Button

The dash-button package includes a script that prints the MAC addresses of devices sending ARP probes, which the Dash button emits when pressed. Use this to learn the MAC address of your Dash button by pressing it.

Add a new script to the `scripts` section of your package.json file:

```json
{
  "scripts": {
    "scan": "dash-button scan"
  }
}
```

Run it with `sudo npm run scan`:
```
$ sudo npm run scan
```

By default it will listen on the first external network interface, which is commonly `en0` or `wlan0`, for example. You can listen on another interface with the `--interface` option:
```
sudo npm run scan -- --interface en1
```

### Telling Dash Button about Your Dash Button

Once you know your Dash button's MAC address you need to tell Dash Button about it:

```js
import DashButton from 'dash-button';

const DASH_BUTTON_MAC_ADDRESS = 'xx:xx:xx:xx:xx:xx';

let button = new DashButton(DASH_BUTTON_MAC_ADDRESS);
```

### Running Code When You Press Your Dash Button

Add a listener to your button. The listener will run when you press the button.

```js
let subscription = button.addListener(async () => {
  let nest = require('unofficial-nest-api');
  await nest.login(username, password);
  nest.setFanModeOn();
});

// Later, if you want to remove the listener do so with the subscription:
subscription.remove();
```

You can add both normal and async functions. If you add an async function, Dash Button waits for the promise to settle before listening to new button presses.

## API

The code is the documentation for now.

## Help Wanted

### Green Light

The coolest feature would be to control the light on the Dash button so it turns green. Currently it turns white when broadcasting an ARP packet and then red when it doesn't receive a response from Amazon. But when you use a Dash button in the normal way, the light turns green after Amazon has placed your order. It would be great and make custom Dash apps feel more responsive if Dash Button could send back some kind of packet to trick the Dash button's light into turning green.

You probably can figure out what's going on with a packet capturing library or a tool like Wireshark. Once we know what Amazon's response looks like, then we need to spoof it. This might be impossible because of TLS but it's worth a shot.

### Unit Tests

Dash Button uses Jest for testing because it's all about mocking modules, which is perfect for Dash Button.

## Acknowledgements

These posts and projects were helpful for making Dash Button:
- ["How I Hacked Amazon’s $5 WiFi Button to track Baby Data"](https://medium.com/@edwardbenson/how-i-hacked-amazon-s-5-wifi-button-to-track-baby-data-794214b0bdd8) by @eob
- [uber-dash](https://github.com/geoffrey/uber-dash) by @geoffrey
- [node_pcap](https://github.com/mranney/node_pcap) by @mranney

## License

This source code is released under [the MIT license](./LICENSE). It is not affiliated with Amazon.
