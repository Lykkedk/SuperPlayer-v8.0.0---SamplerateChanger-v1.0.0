# SuperPlayer-v8.0.0-----SamplerateChanger-v1.0.0
**Currently under construction**

```
tce-load -w git
tce-load -i git
git clone https://github.com/Lykkedk/SuperPlayer-v8.0.0---SamplerateChanger-v1.0.0.git
cd SuperPlayer-v8.0.0---SamplerateChanger-v1.0.0
cp *.tcz /mnt/mmcblk0p2/tce/optional
cp *.tcz.dep /mnt/mmcblk0p2/tce/optional
cd ..
rm -fr SuperPlayer-v8.0.0---SamplerateChanger-v1.0.0

```

SuperPlayer-Samplerate with .py (python) executed samplerate changer\
Changes the samplerates without stopping & starting CamillaGUI
```
SuperPlayer-Samplerate-v8.0.0
├── home
│   └── tc
│       └── camilladsp
│           └── cdsp_templates.tar
└── usr
    └── local
        ├── bin
        │   └── SuperPlayer-Samplerate.py
        ├── etc
        │   └── init.d
        │       └── squeezelite-cdsp
        └── tce.installed
            └── SuperPlayer-Samplerate-v8.0.0 
```
SuperPlayer-Samplerate with alsa_cdsp plugin execute samplerate change with native alsa plugin\
Change the samplerate makes CamillaGUI stop & start
```
superplayer-alsa_cdsp-v8.0.0
├── home
│   └── tc
│       └── camilladsp
│           ├── alsa_cdsp.tar
│           └── asound.conf.tar
└── usr
    └── local
        ├── lib
        │   └── alsa-lib
        │       └── libasound_module_pcm_cdsp.so
        └── tce.installed
            └── superplayer-alsa_cdsp-v8.0.0
```
SuperPlayer-GameChanger is (sorry for the lame name) the script which is used to choose the way of doing the samplerate change in CamillaDSP 
```
SuperPlayer-GameChanger
└── usr
    └── local
        └── bin
            └── SuperPlayer-GameChanger
```

Take a backup of the pCP asound.conf :
```sudo cp /etc/asound.conf /home/tc/asound.conf-pCP```\
Then delete the /etc/asound.conf ```sudo rm /etc/asound.conf``` and create a new empty one ```sudo nano /etc/asound.conf```\
The SuperPlayer /etc/asound.conf should look like this:
```
#    --- sound_out is the real hardware card ---
#    --- SuperPlayer default ---
pcm.sound_out {
type hw
card 0
device 0
}

ctl.sound_out {
type hw
card 0
}
```
But change the card 0/device 0 to the actual hardware, if it's not right.
The cards can be seen by execute ```aplay -l```
```
**** List of PLAYBACK Hardware Devices ****
card 0: Amanero [Combo384 Amanero], device 0: USB Audio [USB Audio]
  Subdevices: 0/1
  Subdevice #0: subdevice #0
```


```cat /opt/bootlocal.sh```
```
#!/bin/sh
# put other system startup commands here

GREEN="$(echo -e '\033[1;32m')"

echo
echo "${GREEN}Running bootlocal.sh..."
#pCPstart------
/usr/local/etc/init.d/pcp_startup.sh 2>&1 | tee -a /var/log/pcp_boot.log
#pCPstop------

# SuperPlayer ------
sudo SuperPlayer-GameChanger py-cdsp > /dev/null 2>&1
#sudo SuperPlayer-GameChanger alsa-cdsp > /dev/null 2>&1
camillagui  > /dev/null 2>&1 &
# SuperPlayer ------
```

```cat /mnt/mmcblk0p2/tce/onboot.lst```
```
pcp.tcz
pcp-8.0.0-www.tcz
nano.tcz
python3.8.tcz
camilladsp-0.6.3.tcz
camillagui-v0.6.0.tcz
superplayer-samplerate-v8.0.0.tcz
superplayer-alsa_cdsp-v8.0.0.tcz
SuperPlayer-GameChanger.tcz
```

```sudo SuperPlayer-GameChanger py-cdsp``` or ```sudo SuperPlayer-GameChanger alsa-cdsp```

```
sudo SuperPlayer-GameChanger alsa-cdsp
Starting ----> SuperPlayer-Samplerate with native alsa_cdsp samplerate changer

sudo SuperPlayer-GameChanger status

SuperPlayer-Samplerate with native alsa_cdsp samplerate changer

```

```
sudo SuperPlayer-GameChanger py-cdsp
Starting ----> SuperPlayer-Samplerate with python executed samplerate changer

sudo SuperPlayer-GameChanger status

SuperPlayer-Samplerate with python executed samplerate changer

```

![SuperPlayer-settings Logo](/Squeezelite_settings.png)

![SuperPlayer-tweaks Logo](/Squeezelite_tweaks.png)
