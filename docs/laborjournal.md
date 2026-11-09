# Laborjournal

## 09.11.2026 – Bestand der virtuellen Maschinen geprüft

**Ziel:** Vor dem Aufbau prüfen, ob auf dem Ubuntu-Host bereits eine eigene VM für Wazuh registriert ist.

```bash
VBoxManage list vms
```

**Beobachtung:** VirtualBox listete genau eine registrierte VM: `Parrot OS 7.1 KDE Security Edition`. Eine VM namens `Wazuh-Lab` war nicht aufgeführt. Der Befehl zeigt registrierte Maschinen an; ob sie gerade laufen, wurde damit nicht geprüft.

**Entscheidung:** Für den Vergleich der Wazuh-Regelsätze ist eine separate VM vorgesehen. Die bestehende Parrot-Umgebung wird dafür nicht benötigt.

**Nächster Nachweis:** Nach dem Anlegen von `Wazuh-Lab` die Liste erneut prüfen. Ein Eintrag bestätigt die Registrierung der VM; die Installation von Ubuntu Server, Wazuh und der Empfang eines Windows-Ereignisses sind eigene Meilensteine.

## 09.11.2026 – Eigene Labor-VM registriert

```bash
VBoxManage createvm --name "Wazuh-Lab" --ostype Ubuntu_64 --register
```

**Ergebnis:** VirtualBox bestätigte: `Virtual machine 'Wazuh-Lab' is created and registered.` Damit ist die VM für den geplanten Wazuh-Server angelegt. `Ubuntu_64` bezeichnet den gewählten Gasttyp; Ubuntu Server und Wazuh sind noch nicht installiert.

**Nächster Schritt:** 4 virtuelle CPUs und 8192 MB Arbeitsspeicher zuweisen und die VM-Konfiguration prüfen.

## 09.11.2026 – CPU und Arbeitsspeicher zugewiesen

```bash
VBoxManage modifyvm "Wazuh-Lab" --cpus 4 --memory 8192
VBoxManage showvminfo "Wazuh-Lab" --machinereadable | grep -E '^(cpus|memory)='
```

Ausgabe der Konfigurationsprüfung:

```text
memory=8192
cpus=4
```

Damit sind für die VM 8 GiB Arbeitsspeicher und vier virtuelle CPUs konfiguriert. Die Prüfung bestätigt die gespeicherten Einstellungen; eine Leistungsmessung wurde nicht durchgeführt.

## 09.11.2026 – Virtuelle Festplatte erstellt

```bash
VBoxManage createmedium disk \
  --filename "$HOME/VirtualBox VMs/Wazuh-Lab/Wazuh-Lab.vdi" \
  --size 102400 --format VDI --variant Standard
```

VirtualBox meldete `Medium created.` Die VDI-Datei wächst mit der Nutzung bis zur virtuellen Kapazität von 100 GiB. Sie ist für Ubuntu Server und die späteren Wazuh-Daten vorgesehen. Der Befehl erstellt das Medium; der Anschluss an die VM steht noch aus.

## 09.11.2026 – SATA-Controller für die Labor-VM

```bash
VBoxManage storagectl "Wazuh-Lab" --name "SATA" --add sata --controller IntelAhci
```

Die Ausführung wurde bestätigt. Der Controller stellt die virtuellen Anschlüsse für die Speichermedien bereit. Die Zuordnung der VDI-Datei zu einem Anschluss wird im nächsten Schritt vorgenommen und geprüft.

## 09.11.2026 – Anschluss der virtuellen Festplatte geprüft

```bash
VBoxManage storageattach "Wazuh-Lab" --storagectl "SATA" --port 0 --device 0 --type hdd --medium "$HOME/VirtualBox VMs/Wazuh-Lab/Wazuh-Lab.vdi"
VBoxManage showvminfo "Wazuh-Lab" --machinereadable | grep -F 'Wazuh-Lab.vdi'
```

**Prüfergebnis:** In der gespeicherten VM-Konfiguration verweist `SATA-0-0` auf `Wazuh-Lab.vdi`. Die Festplatte ist damit am SATA-Controller auf Port 0 als Gerät 0 angeschlossen. Ein Gastbetriebssystem wurde in diesem Schritt noch nicht installiert.

## 09.11.2026 – Installationsmedium heruntergeladen

Für den Gast ist Ubuntu Server 24.04.5 LTS (amd64) vorgesehen. Die Suche im Home-Verzeichnis ergab keinen Treffer für das erwartete ISO-Dateinamensmuster. Anschließend wurde das Installationsmedium von Ubuntu heruntergeladen:

```bash
wget -c -P "$HOME/ISO" "https://releases.ubuntu.com/24.04/ubuntu-24.04.5-live-server-amd64.iso"
```

`wget` meldete 100 % und **4.080.486.400 von 4.080.486.400 Bytes** gespeichert. Damit ist der Download abgeschlossen. Anschließend wurde die lokale ISO-Datei geprüft:

```bash
sha256sum "$HOME/ISO/ubuntu-24.04.5-live-server-amd64.iso"
```

Die berechnete Prüfsumme `97f3d7ffb032c3eb3b23d2c8be9cc76e60c2c1f2c0146ba5ba9fe01cafae0fd8` stimmt mit dem Eintrag für diese Server-ISO in [Ubuntus SHA256SUMS](https://releases.ubuntu.com/24.04/SHA256SUMS) überein.

## 09.11.2026 – Installationsmedium als DVD eingebunden

```bash
VBoxManage storageattach "Wazuh-Lab" --storagectl "SATA" --port 1 --device 0 --type dvddrive --medium "$HOME/ISO/ubuntu-24.04.5-live-server-amd64.iso"
VBoxManage showvminfo "Wazuh-Lab" --machinereadable | grep -F 'ubuntu-24.04.5-live-server-amd64.iso'
```

**Prüfergebnis:** `SATA-1-0` verweist auf die geprüfte Ubuntu-Server-ISO. Der virtuelle Datenträger an Port 0 bleibt das Installationsziel. Der Eintrag bestätigt die Zuordnung; der Start vom Installationsmedium wird separat geprüft.

## 09.11.2026 – Erster Start vom Installationsmedium

Die VM `Wazuh-Lab` lief im VirtualBox-Fenster. Das GRUB-Menü der Ubuntu-Server-ISO zeigte den markierten Eintrag `Try or Install Ubuntu Server`. Damit ist der Start vom Installationsmedium bestätigt. Eine Installation auf die virtuelle Festplatte ist zu diesem Zeitpunkt noch nicht nachgewiesen.

## 09.11.2026 – Netzwerkzugriff im Installer nachgewiesen

Im Ubuntu-Server-Installer erhielt die Schnittstelle `enp0s3` per DHCP die IPv4-Adresse `10.0.2.15/24`. Auf der folgenden Mirror-Seite bestand `http://de.archive.ubuntu.com/ubuntu` den Verbindungstest; der Installer rief die Release-Metadaten für `noble`, `noble-updates` und `noble-backports` ab.

Damit ist ausgehender Zugriff auf diesen Ubuntu-Paketserver nachgewiesen. Die Erreichbarkeit der VM vom separaten Windows-System und vom Host-Browser ist damit noch nicht geprüft. Die Netzwerkverbindung für den späteren Wazuh-Agenten wird nach der Betriebssysteminstallation eingerichtet und getestet.

## 09.11.2026 – Ubuntu Server installiert und per SSH erreichbar

Ubuntu Server **24.04.5 LTS** wurde auf der virtuellen Festplatte der VM `Wazuh-Lab` installiert. Der Installer meldete `Installation complete!`; `openssh-server` wurde im Installationsablauf installiert. Nach dem Neustart war die Anmeldung als normaler Benutzer am Hostnamen `wazuh-manager` möglich. Die Serverkonsole zeigte die IPv4-Adresse `10.0.2.15` für `enp0s3` (VirtualBox-NAT).

Das System wurde anschließend über `sudo apt update` und `sudo apt upgrade -y` aktualisiert. Der Upgrade-Prozess kehrte ohne sichtbare Fehler zum Prompt zurück. Ein Upgrade der Ubuntu-Hauptversion wurde nicht durchgeführt.

## 09.11.2026 – Host-only-Netzwerk und statische Serveradresse

Für die Laborverbindung wurde auf dem Ubuntu-Host ein VirtualBox-Host-only-Netzwerk mit der Schnittstelle `vboxnet0` eingerichtet. Die VM verwendet **Adapter 1: NAT** für ausgehende Verbindungen und **Adapter 2: Host-only (`vboxnet0`)** für die Kommunikation mit Host und späterem Windows-Gast. Der Ubuntu-Host hat auf dieser Laborverbindung `192.168.56.1/24`.

Der VirtualBox-DHCP-Dienst für dieses Host-only-Netzwerk war aktiviert mit `192.168.56.100` als DHCP-Serveradresse und dem Vergabebereich `192.168.56.101` bis `192.168.56.254`. Zunächst erhielt `enp0s8` per DHCP `192.168.56.101/24`. Anschließend wurde außerhalb des Vergabebereichs die statische Adresse **`192.168.56.10/24`** für den Wazuh-Server konfiguriert. Die Adresse ist eine laborspezifische Beispielkonfiguration und muss in einer anderen Umgebung gegebenenfalls angepasst werden.

Die Konfiguration wurde in `/etc/netplan/99-wazuh-hostonly.yaml` hinterlegt:

```yaml
network:
  version: 2
  ethernets:
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.56.10/24
```

Die vorhandene NAT-Konfiguration für `enp0s3` (`dhcp4: true`) blieb erhalten. Nach `sudo netplan generate` und `sudo netplan try` wurde die Konfiguration bestätigt (`Configuration accepted.`). `ip -br a` zeigte `enp0s3` als `UP` mit `10.0.2.15/24` und `enp0s8` als `UP` mit `192.168.56.10/24`.

**SSH-Nachweis:** Vom Ubuntu-Host gelang die Verbindung mit `ssh mohammed@192.168.56.10`. `echo "$SSH_CONNECTION"` zeigte `192.168.56.1 47366 192.168.56.10 22`. Port `47366` ist ein temporärer Clientport, `22` der SSH-Port des Servers.

## 09.11.2026 – Ressourcenprüfung und Sicherungspunkt

Im Gast wurden die Ressourcen wie folgt geprüft:

```bash
nproc
free -h
df -h /
```

**Beobachtete Werte:** 4 vCPU; ca. 7,8 GiB RAM; 4,0 GiB Swap; Root-Dateisystem ca. 98 GB groß, davon ca. 87 GB frei (zum Messzeitpunkt).

Vor der Wazuh-Installation wurde in VirtualBox ein Snapshot angelegt, der in der VM-Titelleiste als `Ubuntu-Clean-Before-Wazuh-01` angezeigt wurde. Dieser Sicherungspunkt dokumentiert den Ausgangszustand vor der Installation; spätere Änderungen erfolgen im aktuellen VM-Zustand.

## 09.11.2026 – Wazuh All-in-One installiert und geprüft

Der Wazuh-Installationsassistent wurde vom Wazuh-Paketserver (Pfad `4.14`) heruntergeladen und als All-in-One-Installation ausgeführt:

```bash
curl -fsSLO https://packages.wazuh.com/4.14/wazuh-install.sh
sudo bash ./wazuh-install.sh -a
```

Der Assistent meldete am **09.11.2026 um 19:26:55**: `INFO: Installation finished.` Die vier Dienste wurden anschließend mit folgendem Befehl geprüft:

```bash
systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard filebeat
```

**Ergebnis:** Alle vier Dienste meldeten `active` (vom Benutzer bestätigt). Die genaue installierte Paketversion wurde noch nicht gesondert ausgelesen; der Pfad `4.14` bezeichnet den Download-Zweig des Installationsskripts.

Das Dashboard konnte vom Ubuntu-Host unter `https://192.168.56.10` geöffnet und nach Anmeldung angezeigt werden. Auf der Übersichtsseite stand **`This instance has no agents registered`**. Angezeigte lokale Alerts sind noch kein Nachweis für erkannte Windows-/PowerShell-Ereignisse.

**Offener nächster Meilenstein:** Windows-VM einrichten, Sysmon und PowerShell-Protokollierung vorbereiten, Wazuh-Agent anbinden und die vollständige Ereigniskette mit einem reproduzierbaren Testfall nachweisen. Erst anschließend werden Regelsatz A/B und Messungen angegangen. Zugangsdaten, Zertifikatsschlüssel und Installationsarchive mit Secrets werden nicht ins Repository übernommen.
