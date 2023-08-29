# oneERNIglobalHackathon
This is a basic example on how to remote control the Picar-X through a (web-) interface.
It uses a MQTT message broker (mosquitto) installed on the raspberry to exchange JSON messages between the Picar-X and a client.
Following commands bundled into an array may be sent to the Picar-X:

## Supported operations

```
{
    "operation" : "set_speed",
    "speed" : 5
}
```
This sets the speed of the motor. Negative numbers move the Picar-X backwards, 0 to stop the motor.
```
{ 
    "operation" : "stop"
}
```
Stops the Picar-X.
```
{ 
    "operation" : "set_direction",
    "angle" : 5
}
```
This steers the Picar-X into the given direction.
```
{ 
    "operation" : "set_head_rotate",
    "angle" : 5
}
```
Rotates the camera head of the Picar-X to the specified angle.
```
{ 
    "operation" : "set_head_tilt",
    "angle" : 5
}
```
Tilts the camera head of the Picar-X to the specified angle.

```
{
    "operation" : "say",
    "text : "Hello World!"
}
```

Uses the text-to-speach feature to vocalize the provided text.

An array of above listed commands must be sent to the "command" topic of the MQTT broker to control the Picar-X.

In the other direction, the Picar-X produces events with the current state of it's sensors (ultrasonic andd grayscale):
```
{
    "distance" : 10,
    "grayscale" : [ 1, 2, 3]
}
```
Those events may be consumed by connecting to the "state" topic on the MQTT Broker.

## Setup the Raspberry Pi 4B

### Prepare OS Image and start Raspberry Pi
- Follow the instructions here https://docs.sunfounder.com/projects/picar-x/en/latest/python/python_start/installing_the_os.html to write the Raspberry Pi OS Lite (Legacy) onto the SD card (use the Raspberry Pi OS (Legacy) if you insist to use a Desktop :smirk:)
- Insert the SD-Card with the OS image into your Raspberry and start it. The Raspberry Pi 4B boots up and connects to your WLAN (with the credentials provided when the OS image was written)
- Find the IP-Address of your Raspberry
- Connect to your Raspberry via SSH and the user credentials defined when writing the OS image to the SD card

### Update the OS
- Execute "apt-get update && apt-get upgrade" as root. This will download and install the updates of the OS
- Reboot

### Install python packages
- Execute "sudo apt install git python3-pip python3-setuptools python3-smbus"

### Install the Picar-X HAT python packages
```
cd
git clone https://github.com/sunfounder/robot-hat.git
cd robot-hat
sudo python3 setup.py install
```

### Install the vilib module
```
cd
git clone https://github.com/sunfounder/vilib.git
cd vilib
sudo python3 install.py
```

### Install the picar-x module
```
cd
git clone -b v2.0 https://github.com/sunfounder/picar-x.git
cd picar-x
sudo python3 setup.py install
sudo bash i2samp.sh (answer all questions with 'y')
```
The Raspberry Pi reboots now.

### Enable I2C and Camera Interface
- Run "sudo raspi-config" and follow the instructions here: https://docs.sunfounder.com/projects/picar-x/en/latest/python/python_start/enable_i2c_camera.html
- Reboot again

### Servo adjust and calibration
- Follow the instructions here https://docs.sunfounder.com/projects/picar-x/en/latest/python/python_start/py_servo_adjust.html and here https://docs.sunfounder.com/projects/picar-x/en/latest/python/python_calibrate.html

## Install & Configure the Mosquitto MQTT-Broker
```
sudo apt-get install mosquitto
sudo /etc/init.d/mosquitto stop
```
Update the mosquitto configuration file (/etc/mosquitto/mosquitto.conf) by adding the following lines:
```
allow_anonymous true
listener 1883
listener 9001
protocol websockets
```
Restart mosquitto:
```
sudo /etc/init.d/mosquitto start
```

## Run the Picar-X Daemon
```
cd
git clone https://github.com/dani72/oneERNIglobalHackathon.git
cd oneERNIglobalHackathon/px_server
sudo python3 picarx_daemon.py
```

## Access the camera
The video stream is accessible through http://<raspi-ip>:9000/mjpg

## Run the web user interface
On a client machine, clone the Git repository:

```
git clone https://github.com/dani72/oneERNIglobalHackathon.git
```

Open the oneERNIglobalHackathon project with e.g. Visual Studio Code and replace the IP-Adresses of my raspberry with the IP address of your raspberry in your LAN (both devices must be in the same network) in the px_client/picarx.html file. Use e.g. the "Live Server" extension to start up a local web server. A browser opens and displays the UI. Use the developer tools console to track the messages.

Have fun!!!!