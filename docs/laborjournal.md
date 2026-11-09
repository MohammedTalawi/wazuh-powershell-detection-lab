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
