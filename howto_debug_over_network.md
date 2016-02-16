## How to debug over network
If your device support network debugging, you can connect adb and debug via a local network(wifi).
Please try following steps;

1. Connect your device via USB cable.
2. run "adb tcpip 5555" on console.
3. run "adb connect xxx.xxx.xxx.xxx:5555" on console
xxx.xxx.xxx.xxx is local IP address of your device.
4. Disconnect USB cable.

If you can see message of "connect to xxx.xxx.xxx.xxx:5555" on step3, now you can debug via network(wifi).
If you could't see the message, unfortunately your device does not support network debugging...

Note: If you connect multiple devices at the same time (including both devices over USB and over network), you need to specify which device you want to connect over network on step 2. using `-s device-name` option.
```
adb -s device-name tcpip 5555
```
