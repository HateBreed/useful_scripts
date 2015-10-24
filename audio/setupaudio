#!/bin/bash

JACKFOLDER="$HOME/jackd"
if [ $# -gt 0 ] ; then
  MODE="$1"
else
  MODE="desktop"
fi

if [ $# -eq 2 ] ; then
  SOUNDCARD="$2"
fi


echo "MODE=$MODE"

if pidof pulseaudio ; then pulseaudio -k ; fi
if pidof pulseaudio ; then killall pulseaudio ; fi
if pidof pulseaudio ; then killall -9 pulseaudio ; fi

if [ -f $JACKFOLDER/.jackdrc ]
  then
    if pidof jackd ; then killall jackd ; sleep 2 ; fi
    if [ "$MODE" = "record" ] || [ "$MODE" = "normal" ] || [ "$MODE" = "hdmi" ] ; then
      while ! pidof jackd ; do
        sh $JACKFOLDER/.jackdrc &
        sleep 4
      done
      renice -n -12 -p `pidof jackd`
   fi
fi

if which pulseaudio
  then
    cp "$HOME/.pulse/default.pa.$MODE" "$HOME/.pulse/default.pa"
    pulseaudio &
    if [ "$MODE" = "desktop" ] || [ "$MODE" = "game" ] ; then 
      sleep 1
      renice -n 0 -p `pidof pulseaudio`
      sleep 1
      pacmd set-card-profile 0 "$3"
    fi
fi

sleep 1

if pidof xfce4-mixer-plugin
  then
    killall xfce4-mixer-plugin
fi

#cp "$HOME/.xfcemixerprofiles/$MODE.xml" "$HOME/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-mixer.xml"
#echo "$HOME/.xfcemixerprofiles/$MODE.xml" "$HOME/.config/xfce4/xfconf/xfce-perchannel-xml/xfce4-mixer.xml"

MIXER=$(ps aux|grep -e "mixer"|grep -v -e "grep"|cut -d' ' -f2,3,4)
for P in ${MIXER[@]} ; do
  kill $P
done

if [ $SOUNDCARD ] ; then
  xfconf-query -c xfce4-mixer -p /sound-card -s "$SOUNDCARD"
  xfconf-query -c xfce4-mixer -p /active-card -s "$SOUNDCARD"
  xfconf-query -c xfce4-panel -p $(xfconf-query -c xfce4-panel -l -p /plugins|grep sound-card) -s "$SOUNDCARD"
fi
