# M300-Services
## TBZ M300 LB02
### K1
VirtualBox, Vagrant und VSCode sind installiert.
Git-Client ist installiert und konfiguriert.
SSH-Key-Pair ist erstellt und zum SSH-Agent hinzugefügt worden.

### K2
GitHub Account ist erstellt.
Git-Client wurde für Initialisierung des Repos verwendet. Push-Pull & Commit wird via VSCode erledigt.

VSCode wird als Markdown Editor verwendet, mit installiertem Markdown Paket vom VSCode Marketplace.

### K3
Zwei CentOS VMs sind eingerichtet. VM01 als Webserver, VM02 als DB-Server.
Die wichtigsten Vagrant Befehle kenne ich:
| Befehl                    | Beschreibung                                                      |
| ------------------------- | ----------------------------------------------------------------- | 
| `vagrant init`            | Initialisiert im aktuellen Verzeichnis eine Vagrant-Umgebung und erstellt, falls nicht vorhanden, ein Vagrantfile |
| `vagrant up`              |  Erzeugt und Konfiguriert eine neue Virtuelle Maschine, basierend auf dem Vagrantfile |
| `vagrant ssh`             | Baut eine SSH-Verbindung zur gewünschten VM auf                   |
| `vagrant status`          | Zeigt den aktuellen Status der VM an                              |
| `vagrant port`            | Zeigt die Weitergeleiteten Ports der VM an                        |
| `vagrant halt`            | Stoppt die laufende Virtuelle Maschine                            |
| `vagrant destroy`         | Stoppt die Virtuelle Maschine und zerstört sie.                   |

### K4
Firewall ist eingerichtet. Auf den VMs wird firewalld enabled und Rules werden definiert.
Ein Apache-Reverse-Proxy wurde nicht eingerichtet. Man könnte aber wie folgt einen einrichten:

#### Reverse Proxy
***
Der Apache-Webserver kann auch als Reverse Proxy eingerichtet werden. 

**Installation**
Dazu müssen folgende Module installiert werden:
```Shell
    $ sudo apt-get install libapache2-mod-proxy-html --> ist schon im apache2-bin enthalten
    $ sudo apt-get install libxml2-dev
```

Anschliessend die Module in Apache aktivieren:
```Shell
    $ sudo a2enmod proxy
    $ sudo a2enmod proxy_html
    $ sudo a2enmod proxy_http 
```

Die Datei /etc/apache2/apache2.conf wie folgt ergänzen:
```Shell
    ServerName localhost 
```

Apache-Webserver neu starten:
```Shell
    $ sudo service apache2 restart
```

**Konfiguration** <br>
Die Weiterleitungen sind z.B. in `sites-enabled/001-reverseproxy.conf` eingetragen:
```Shell
    # Allgemeine Proxy Einstellungen
    ProxyRequests Off
    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    # Weiterleitungen master
    ProxyPass /master http://master
    ProxyPassReverse /master http://master
```

Es gibt keine Benutzerverwaltung und keine Rechtevergabe.
Ein SSH-Tunnel wurde nicht eingerichtet. Man könnte aber wie folgt einen einrichten:

Weiterleitung von Port 8000 auf dem lokalen System (database/db01) an den Webserver web/web01 (192.168.55.101:80):

	cd user
	vagrant ssh database
	# Wechsel auf User admin01
	sudo su - admin01
	# in der VM
	ssh -L 8000:localhost:80 web01 -N &
	netstat -tulpen
	curl http://localhost:8000

Umgekehrte Richtung. Benutzern auf web/web01 wird ermöglicht, über localhost:3307 auf den MySQL-Server auf database/db01 zuzugreifen:

	vagrant ssh database
	# in der VM database
	ssh -R 3307:localhost:3306 web01 -N &
	ssh web01
	# in der VM web
	netstat -tulpen
	curl http://localhost:3307

	
**ACHTUNG:** Der db01 Server muss über einen Privaten SSH-Key verfügen und der Public SSH-Key muss in web01 eingetragen sein. Zusätzlich muss bereits einmal via `ssh` von db01 in den web01 Server gewechselt worden sein.

