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
            #!/bin/sh

            #
            # superplayer-alsa_cdsp.tcz - This script executes on every boot
            #

            HOME_DIR=/home/tc
            CONFIG_DIR=/home/tc/camilladsp
            CONFIGS=/home/tc/camilladsp/configs
            COEFFS=/home/tc/camilladsp/coeffs
            TAR_BALL_YMLS=alsa_cdsp.tar
            YML_TEMPLATE=alsa_cdsp_template.yml
            TAR_BALL_ALSA_CONF=asound.conf.tar
            ASOUND_LOCATION=/etc
            ASOUND_CONF=/etc/asound.conf
            BACKUP_DIR=/home/tc/camilladsp/SuperPlayer-Backups

            # Install only config directory's [camilladsp + freinds] in /home/tc first time the .tcz is executed
            if [ ! -d $CONFIGS ]; then                                          # If one of the camilladsp directory's does NOT exist
                mkdir -p $CONFIGS                                               # Create them ;-)
                mkdir -p $COEFFS                                                #
                chown -R tc:staff $CONFIG_DIR                                   # Set owner and group
                chmod -R 755 $CONFIG_DIR                                        # Set read/write etc... bits
            fi

            # Extract only .tarball in /home/tc/camilladsp the first time this .tcz/script is executed.
            if [ ! -f $CONFIG_DIR/$YML_TEMPLATE ]; then                         # If one of the alsa_cdsp_templates.ymls does NOT exist
                                                                                # (alsa_cdsp_template.yml & camilladsp.yml are in the same tarball)
                tar xf $CONFIG_DIR/$TAR_BALL_YMLS -C $CONFIG_DIR/               # Unpack the tarball
                chown -R tc:staff $CONFIG_DIR                                   # Set owner and group
                 chmod -R 755 $CONFIG_DIR                                        # Set read/write etc... bits
            fi

            # Backup directory check
            if [ ! -d $BACKUP_DIR ]; then                                       # Create backup directory if NOT exist
                mkdir -p $BACKUP_DIR                                            #
                chown tc:staff $BACKUP_DIR                                      # Set owner and group
                chmod 755 $BACKUP_DIR                                           # Set read/write etc... bits
            fi

            # Check if asound.conf has a backup - if NOT make a backup and untar the alsa_cdsp [asound.conf] to /etc
            # At next boot, the asound.conf backup is there, and we do not overwrite asound.conf again ;-)
            #
            if [ ! -f $BACKUP_DIR/asound.conf ]; then                           # if asound.conf [BACKUP] does NOT exist
                cp $ASOUND_CONF $BACKUP_DIR/asound.conf-BACKUP                  # Take a backup of file
                tar xf $CONFIG_DIR/$TAR_BALL_ALSA_CONF -C $ASOUND_LOCATION/     # Untar alsa_cdsp.tar [asound.conf] to /etc
            fi

            # Remove .tar balls
            rm $CONFIG_DIR/$TAR_BALL_YMLS                                       # Remove the tarball
            rm $CONFIG_DIR/$TAR_BALL_ALSA_CONF                                  # Remove tarball
            chown tc:staff $HOME_DIR .                                          # Set owner and group

            exit

```



