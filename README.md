# SuperPlayer-v8.0.0-----SamplerateChanger-v1.0.0

**Tested on pCP 8, 32bit version**

CamillaDSP here :: https://www.diyaudio.com/forums/pc-based/349818-camilladsp-cross-platform-iir-fir-engine-crossovers-correction-etc.html
Thread and help to my .tcz stuff here :: https://www.diyaudio.com/forums/pc-based/361429-superplayer-dsp_engine-camilladsp-samplerate-switching-esp32-remote-control.html

It all started a "long" time ago ;-)... (Not in a far away galaxy through)\
I've been using CamillaDSP for 1+year now, having had a lot of fun & pleasure with this DSP thing.

I had created some .tcz, which are the pCP / piCore (TinyCore Linux) way of packing things up.\
The goal with this is to have CamillaDSP running along with Squeezelite and automatically change filters on CamillaDSP, when samplerate's are changing.

If you like to have the CamillaDSP gui installed also, giving you a nice interface, you should install my .tcz'z from here https://github.com/Lykkedk/SuperPlayer-v8.0.0--CamillaGUI-v0.6.0 

**Here we go ::**

(Nevermind is something is repeated from my Camillagui install, if you did this first)

Install pCP v8 - 32bit version... https://repo.picoreplayer.org/insitu/piCorePlayer8.0.0/piCorePlayer8.0.0.zip

**First thing to do, is to have the pCP working** with your'e soundcard/dac or whatever used; when this is done continue...

Extend filesystem as instructed for piCorePlayer. [Main page, Resize FS]

SSH into the pCP/RPI ssh tc@192.168.1.95 (with the right ip number)\
Default password is: piCore\

Install needed editor:\
```tce-load -w -i nano``` (RPI downloads and installs nano now)

Nano is an easy texteditor\
Quick use : [ctrl] + o = save, [ctrl] + x = exit

Install python3.8.tcz - ```tce-load -w -i python3.8.tcz```

Install git & clone my repo. + copy the files into the right location on the pCP\
(You can copy & paste, one line at a time into the pCP SSH console/terminal if you like)
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
**The structure of the .tcz's are like this ::**

[SuperPlayer-Samplerate-v8.0.0.tcz]\
SuperPlayer-Samplerate with .py (python) executed samplerate changer\
Changes the samplerates with Python daemon's and script's (Thanks to user "pi r" frome diyaudio.com for this solution)
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

[superplayer-alsa_cdsp-v8.0.0]\
SuperPlayer-Samplerate with alsa_cdsp plugin execute samplerate change with native alsa plugin\
Change the samplerate with the "native" solution https://github.com/scripple/alsa_cdsp
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
[SuperPlayer-GameChanger.tcz]
SuperPlayer-GameChanger is (sorry for the lame name) the script which is used to choose between them (alsa-cdsp native way & py-cdsp python way)
```
SuperPlayer-GameChanger
└── usr
    └── local
        └── bin
            └── SuperPlayer-GameChanger
```
While the .tcz's are looking for the folder and files to prevent overwriting (/home/tc/camilladsp/etc... etc..), there is ofcause a chance that something\
could go wrong, so please backup thoose dir's if you have them.

Also take a backup of the pCP asound.conf - Nice to have for troubleshooting :
```sudo cp /etc/asound.conf /home/tc/asound.conf-pCP```\
Then delete the /etc/asound.conf ```sudo rm /etc/asound.conf``` and create a new one ```sudo nano /etc/asound.conf```\
and copy/paste the underneeth into it.

(SuperPlayer /etc/asound.conf)
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
**Now change the card 0/device 0 to the actual hardware**, if it's not right.
The cards can be seen by execute ```aplay -l```
```
**** List of PLAYBACK Hardware Devices ****
card 0: Amanero [Combo384 Amanero], device 0: USB Audio [USB Audio]
  Subdevices: 0/1
  Subdevice #0: subdevice #0
```

Everything should be ready now to try the Python way of doing this (py-cdsp).\
Execute ```tce-load -i superplayer-samplerate-v8.0.0.tcz```\
If not allready there, the .tcz should create the following directory's and files ::
```
/home/tc/camilladsp
                  ├── cdsp_template.yml
                  ├── cdsp_template_active.yml
                  ├── coeffs
                  ├── configs

```
The cdsp_template.yml is the file we must edit to our need's (filter's, EQ's, gain, volume etc...)\
The file could look like this ::
```
devices:
  samplerate: <<sample_rate>>
  chunksize: <<chunk_size>>
  queuelimit: 1
  capture:
    type: File
    channels: 2
    filename: "/dev/stdin"
    format: S32LE
  playback:
    type: ALSA
    channels: 2
    device: "sound_out"
    format: S32LE
filters:
  Gain_L:
    parameters:
      gain: 0
      inverted: false
      mute: false
    type: Gain
  Gain_R:
    parameters:
      gain: 0
      inverted: false
      mute: false
    type: Gain
  Volume_L:
    parameters:
      ramp_time: 200
    type: Volume
  Volume_R:
    parameters:
      ramp_time: 200
    type: Volume
mixers: {}
pipeline:
- channel: 0
  names:
  - Gain_L
  type: Filter
- channel: 1
  names:
  - Gain_R
  type: Filter
- channel: 0
  names:
  - Volume_L
  type: Filter
- channel: 1
  names:
  - Volume_R
  type: Filter

```
The two line's ::
```
  samplerate: <<sample_rate>>
  chunksize: <<chunk_size>>
```
Are automatically changed to the played samplerate with a prober chunksize, really close to the way the native alsa_cdsp plugin work's.\
When the python daemon are started, it generates a new file named ```cdsp_template_active.yml```, which are the one CamillaDSP will use.

```nano /opt/bootlocal.sh```
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
