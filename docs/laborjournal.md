# Laborjournal

## 09.11.2026 – Bestand der virtuellen Maschinen geprüft

**Ziel:** Vor dem Aufbau prüfen, ob auf dem Ubuntu-Host bereits eine eigene VM für Wazuh registriert ist.

```bash
VBoxManage list vms
```

**Beobachtung:** VirtualBox listete genau eine registrierte VM: `Parrot OS 7.1 KDE Security Edition`. Eine VM namens `Wazuh-Lab` war nicht aufgeführt. Der Befehl zeigt registrierte Maschinen an; ob sie gerade laufen, wurde damit nicht geprüft.

**Entscheidung:** Für den Vergleich der Wazuh-Regelsätze ist eine separate VM vorgesehen. Die bestehende Parrot-Umgebung wird dafür nicht benötigt.

**Nächster Nachweis:** Nach dem Anlegen von `Wazuh-Lab` die Liste erneut prüfen. Ein Eintrag bestätigt die Registrierung der VM; die Installation von Ubuntu Server, Wazuh und der Empfang eines Windows-Ereignisses sind eigene Meilensteine.
