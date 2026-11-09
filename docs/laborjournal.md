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
