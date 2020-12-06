# Bluetooth Player

Raspberry Pi can be turned to a bluetooth player. Just plug in the audio jack and use the phone to play music.

## Bluetooth device

The instructions are based on Pacman's Revenge tutorial available on [YouTube](https://www.youtube.com/watch?v=SyrJ1_kKOh4) and [blog post](http://pacmans-revenge.blogspot.com/2018/08/raspberry-pi-bluetooth-speakers.html).

```bash
# Install all needed packages for Bluez and PulseAudio
sudo apt-get install bluez pulseaudio-module-bluetooth python-gobject python-gobject-2

# Enable user to access and configure Bluetooth devices
sudo usermod -a -G lp pi

# Change the resample method by adding this config to /etc/pulse/daemon.conf
# resample-method = trivial

# Enable bluetooth functionality by adding this config to /etc/bluetooth/main.conf
# Enable=Source,Sink,Media,Socket

# Reboot
sudo reboot

# Activate Bluetooth
sudo service bluetooth start

# Scan for the device to connect and point down its address
sudo hcitool scan

# Enable the Raspberry Pi to be found
sudo hciconfig hci0 piscan

# Pair
bluetoothctl

# Enter
agent on
default-agent
trust <ADDRESS_OF_THE_DEVICE_TO_CONNECT>
quit

# Take note of the connected device source
pactl list sources short

# Take note of the sink (where the music will be played to)
pactl list sinks short

# The next step (redirect audio) may not be necessary.
# Consider instead checking if default sink is the one you want
# and change it if necessary. The default is the one with `*`
pacmd list-sinks
# Change it with
pacmd set-default-sink <INDEX_OF_THE_SINK>

# Redirect audio (something along the lines of)
# pactl load-module module-loopback source=bluez_source.XX_XX_XX_XX_XX_XX.a2dp_source sink=alsa_output.platform-soc_audio.analog-stereo

# Turn it up!
amixer set Master 100%

# I'm not quite sure if this step is necessary
# pacmd set-sink-volume 0 65537

# Optional step to redirect sound from HDMI to audio jack (only if necessary)
# amixer cset numid=3 1

# All good!

# Reboot
sudo reboot

# Music should be playing now, but with no volume adjustment from device
```

## Volume adjustment

The instructions are based on Raspberry Pi's forum post [Another How to turn your Pi in a Bluetooth Speaker Tutorial](https://www.raspberrypi.org/forums/viewtopic.php?t=235519).

```bash
# Add the following config to /etc/systemd/system/bluetooth.target.wants/bluetooth.service
ExecStart=/usr/lib/bluetooth/bluetoothd --noplugin=avrcp

# Reboot
sudo reboot

# Music should be playing
```

## Known issues

Audio may stutter, lag and be distorted on lower frequencies (bass). This seems to only happen at the very start and for a short period of time. Pausing and returning to play seems to fix it. Raspberry Pi holds the load. I suspect that this is due to the initial bits on the transfer to get lost. Anyway, this could be improved but it is manageable.
