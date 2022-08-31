# GEKKOSCIENCE COMPAC F - USB MINER SOFTWARE


Um den Raspberry Pi vorzubereiten und die Mining Software zu installieren, müssen folgende Befehle im Terminal eingegeben werden:


```
sudo apt-get update

sudo apt-get upgrade -y
```

```
sudo apt-get install -y build-essential autoconf automake libtool pkg-config libcurl4-openssl-dev libudev-dev libusb-1.0-0-dev screen libncurses5-dev zlib1g-dev git
```

```
git clone https://github.com/kanoi/cgminer.git
```

```
cd cgminer
```

```
CFLAGS="-O2 -march=native -fcommon" ./autogen.sh --enable-gekko --enable-icarus
```

```
make
```

-----------------------------------------------------------------------------------------------------------------------------------------------------------
Jetzt kann getestet werden, ob der Miner über den USB Hub gefunden wird:

``` 
sudo ./cgminer -n
```

folgendes sollte angezeigt werden:


> Manufacturer: "GekkoScience'
> 
>   Product: ' CompacF Bitcoin Miner'

---

Nun erstellen wir die cgminer.sh Datei, wo unser Start Befehl gespeichert wird

Wir gehen in den Ordner "cgminer" (wenn ihr dort nicht schon seid) mit 

```
cd cgminer
```

und schreiben die Datei

```
nano cgminer.sh
```

Es öffnet sich ein Fenster und hier drin fügen wir folgendes ein

```
#!/bin/bash
cd /home/user einsetzen/cgminer

sudo ./cgminer -o stratum+tcp://solo.ckpool.org:3333 -u BtcAdresse.Miner1 -p x --gekko-compacf-freq 350 --gekko-start-freq 260 --gekko-mine2 --gekko-tune2 60 2> "run-`date +%Y%m%d%H%M%S`.log"
```

"user einsetzen" muss dann mit dem jeweiligen Usernamen ersetzt werden + bei "BtcAdresse.Miner1" kommt eure BTC Adresse hinein, wohin der Reward geschickt werden soll


Damit der Miner nach jedem Neustart direkt wieder von alleine seine Arbeit aufnimmt, erstellen wir nun den Autostart:

Öffnen der Datei mit

```
sudo nano /etc/rc.local 
```
Dort fügen wir über "exit 0" folgendes ein


```
sudo su - -c "screen -dm -S miner /home/user einsetzen/cgminer/cgminer.sh"
```

"user einsetzen" muss dann mit dem jeweiligen Usernamen ersetzt werden

nun sieht diese Datei so aus

```
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

# Print the IP address
_IP=$(hostname -I) || true
if [ "$_IP" ]; then
  printf "My IP address is %s\n" "$_IP"
fi

sudo su - -c "screen -dm -S miner /home/gekkoscience/cgminer/cgminer.sh"

exit 0
```

Jetzt fehlt nur noch, die cgminer.sh in eine Script Datei umzuwandeln, dieses geht mit

```
sudo chmod +x cgminer.sh
```

---

fertig ! --> ```sudo reboot```  damit der Raspberry Pi neustartet

---

Wir können überprüfen, ob der Miner läuft, indem wir uns wieder auf den Pi einloggen, aber nach dem einloggen uns als superuser anmelden mit

```
sudo su
```

Hier sehen wir nun auch den im Hintergrund laufenden Screen, mit

```
screen -ls
```

Natürlch kann dieser geöffnet werden mit

```
screen -r
```

Um den Screen wieder zu verlassen, ohne ihn zu stoppen, drücken wir einfach "Control + A" und danach "Control + D"

---



## sonstige Befehle

Start Befehl ohne Autostart (hier wird aber der Miner gestoppt, sobald das Terminal geschlossen wird

```
sudo ./cgminer -o stratum+tcp://solo.ckpool.org:3333 -u BtcAdresse.Miner1 -p x --gekko-compacf-freq 350 --gekko-start-freq 260 --gekko-mine2 --gekko-tune2 60 2> "run-`date +%Y%m%d%H%M%S`.log"
```

freien Speicherplatz überprüfen
```
df -h
```

cgminer config löschen, falls die Frequenz nicht mehr geändert werden kann

```
sudo rm  /root/.cgminer/cgminer.conf
```

CPU Auslastung prüfen
```
top
```
oder
```
htop
```
