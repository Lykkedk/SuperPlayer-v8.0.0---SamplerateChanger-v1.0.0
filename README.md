# SuperPlayer-v8.0.0-----SamplerateChanger-v1.0.0

**Tested on pCP 8, 32bit version**

[!NEW! - Upgrade guide](#upgrade-guide) (camilladsp-1.0.1)\
*This is ment as a future upgrade, for upgrading the CamillaDSP binary, tricks and tracks etc...*\
*Use when you allready have a working SuperPlayer SamplerateChanger installation*

This guide is ment as a guide to install a working CamillaDSP + tool's for an allready working piCorePlayer installation.\
There seem's to be some confusion about the part SuperPlayer-GameChanger which i called one of the tool's provided along.\
The SuperPlayer-GameChanger is just a script which are used to change the way CamillaDSP handles the automatic switching of samplerates,\
e.g. when you are using fir filter's for say 44.1kHz, 48.0kHz, 88.2kHz, 96kHz etc...\
I provide here two different way's of doing this (alsa-cdsp & py-cdsp - explained in the guide)

CamillaDSP here :: https://www.diyaudio.com/forums/pc-based/349818-camilladsp-cross-platform-iir-fir-engine-crossovers-correction-etc.html
Thread and help to my .tcz stuff here :: https://www.diyaudio.com/forums/pc-based/361429-superplayer-dsp_engine-camilladsp-samplerate-switching-esp32-remote-control.html

It all started a "long" time ago ;-)... (Not in a far away galaxy through)\
I've been using CamillaDSP for 1+year now, having had a lot of fun & pleasure with this DSP thing.

I had created some .tcz, which are the pCP / piCore (TinyCore Linux) way of packing things up.\
The goal with this is to have CamillaDSP running along with Squeezelite and automatically change filters on CamillaDSP, when samplerate's are changing.

If you like to have the CamillaDSP gui installed also, giving you a nice interface, you should install my .tcz'z from here https://github.com/Lykkedk/SuperPlayer-v8.0.0--CamillaGUI-v0.6.0 

**Here we go ::**

(Nevermind if something is repeated from my Camillagui install, if you did this first)

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
And ```tce-load -i SuperPlayer-GameChanger.tcz```
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
When the python daemon are started, it generates a new file named ```cdsp_template_active.yml```, which are the one CamillaDSP will use.\
Using e.g fir filter's in the .yml - just name the filter's with this <<sample_rate>> ... Say /home/tc/myfilter/MYFIR<<sample_rate>>FOO.yml\
Or again most like the same way when using the native alsa_cdsp. https://github.com/scripple/alsa_cdsp

But just leave it as is for now...\
Looking at the pictures at bottom of this readme, disable squeezelite at boot, and set the **Max sample rate to 44100-192000** not 441000-384000\
as in the picture below, because my Python daemon does not support this by now.

Execute ```sudo SuperPlayer-GameChanger py-cdsp``` and wait for it to start...
```
tc@SuperPlayer:~/camilladsp$ sudo SuperPlayer-GameChanger py-cdsp
Starting ----> SuperPlayer-Samplerate with python executed samplerate changer

```
It should work at this stage... Try it out before continue :)
Remember to backup the pCP with ```pcp bu```, before rebooting or powering pCP off.

If everything is working as expected, you can paste the lines in bootlocal & onboot.lst to have it start at boot.\
(Comment (#) out the ```camillagui  > /dev/null 2>&1 &``` line if not used)

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

```nano /mnt/mmcblk0p2/tce/onboot.lst```
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
You can (#) Comment out the line ```superplayer-alsa_cdsp-v8.0.0.tcz``` if you like the way it's working with the Python daemon & script's.\
I use this method myself, but honestly i think most people prefer the native way.

When you are ready and everything is working, **try the alsa_cdsp method also**, and choose for yourself what you like best!\
When you load, either by onboot.lst or by ```tce-load -i superplayer-alsa_cdsp-v8.0.0.tcz```, the asound.conf are modified\
and it might be necessary to edit it to youre card, soundcard, dac etc... again, just like we did at the beginning.\
The **squeezelite output must be set to camilladsp** for native alsa method (look at pictures) - at the pCP webinterface.

```cat /etc/asound.conf```
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

#   --- CamillaDSP with Seashell's alsa-plugin ---
# Howto here : https://github.com/scripple/alsa_cdsp 
pcm.camilladsp {
    type cdsp
      cpath "/usr/local/bin/camilladsp"
      config_in "/home/tc/camilladsp/alsa_cdsp_template.yml"
      config_out "/home/tc/camilladsp/camilladsp.yml"
      cargs [
        -l warn
        -p "1234"
      ]
      channels 2
      rates = [
        44100 
        48000 
        88200 
        96000
        176400
        192000
        352800
        384000
      ]
}
```
If this also work's, you can have it load at boot, by inserting it into bootlocal & onboot.lst as i explained before.

When the superplayer-alsa_cdsp-v8.0.0.tcz are loaded, it creates some new files.
alsa_cdsp_template.yml & camilladsp.yml (please ref. to https://github.com/scripple/alsa_cdsp for good howto)
```
camilladsp
         ├── alsa_cdsp_template.yml
         ├── camilladsp.yml
         ├── cdsp_template_active.yml
         ├── cdsp_template.yml
         ├── coeffs
         ├── configs
         └── SuperPlayer-Backups
            └── asound.conf-BACKUP
```

Hope it all went good.... 

**Good luck (Jesper / lykkedk over at diyaudio.com)**

Some examples of use ::
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



## Upgrade Guide

I have uploaded the newest (04.08.2022) version of CamillaDSP (camilladsp-1.0.1.tcz) to this repo.\
Just gitclone again as in the guide and copy the new binary to correct directory :
```
tce-load -w git
tce-load -i git
git clone https://github.com/Lykkedk/SuperPlayer-v8.0.0---SamplerateChanger-v1.0.0.git
cd SuperPlayer-v8.0.0---SamplerateChanger-v1.0.0
cp camilladsp-1.0.1.tcz /mnt/mmcblk0p2/tce/optional
cd ..
rm -fr SuperPlayer-v8.0.0---SamplerateChanger-v1.0.0

```

Now edit the onboot.lst, to have the new CamillaDSP loaded at boot.\
*Please ignore them other lines, just edit the one with the camilladsp-xxxx.tcz*

```nano /mnt/mmcblk0p2/tce/onboot.lst```
```
pcp.tcz
pcp-8.0.0-www.tcz
nano.tcz
python3.8.tcz
camilladsp-1.0.1.tcz
camillagui-v0.6.0.tcz
superplayer-samplerate-v8.0.0.tcz
superplayer-alsa_cdsp-v8.0.0.tcz
SuperPlayer-GameChanger.tcz
```

When done, execute ```pcp bu``` & then reboot the Pi-machine\
Everything should hopefully work.\
Checkout the newest version by executing after logging in via SSH :

```
tc@SuperPlayer:~$ camilladsp --version
CamillaDSP 1.0.1
```
### That's all for now... Enjoy ;-)
