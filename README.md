# SuperPlayer-v8.0.0-----SamplerateChanger-v1.0.0
**Currently under construction**

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

![SuperPlayer-settings Logo](/SuperPlayer.png)

![SuperPlayer-tweaks Logo](/SuperPlayer.png)
