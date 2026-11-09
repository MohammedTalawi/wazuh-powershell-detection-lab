# Wazuh-Labor: Erkennung verdächtiger PowerShell-Aktivitäten

Wie verändert sich die Erkennung, wenn Wazuh zusätzlich zu seinen Standardregeln den Kontext eines PowerShell-Aufrufs berücksichtigt? Ein Beispiel aus dem geplanten Testkatalog sind kodierte Aufrufe: Sie kommen sowohl in legitimen Administrationsaufgaben als auch in verdächtigen Szenarien vor. Deshalb soll geprüft werden, was sich aus Befehlszeile, Elternprozess und Skriptinhalt ableiten lässt.

## Stand: Wazuh-Server eingerichtet (09.11.2026)

Die VirtualBox-VM `Wazuh-Lab` läuft mit **Ubuntu Server 24.04.5 LTS**. Wazuh Manager, Indexer, Dashboard und Filebeat wurden installiert; alle vier Dienste wurden als `active` geprüft. Das Dashboard ist vom Ubuntu-Host über die isolierte Host-only-Verbindung erreichbar (`https://192.168.56.10`; laborspezifische Adresse).

**Noch nicht umgesetzt:** Windows-Testsystem, Sysmon, Wazuh-Agent, Nachweis des Windows-Ereigniswegs und Regelsatzvergleich. Bei der ersten Dashboard-Prüfung waren noch keine Agents registriert. Die Einrichtung und Prüfbelege sind im [Laborjournal](docs/laborjournal.md) dokumentiert.

## Geplanter Vergleich

- **Regelsatz A:** unveränderte Wazuh-Standardregeln.
- **Regelsatz B:** Standardregeln mit vier bis sechs eigenen oder angepassten Erkennungsregeln.
- **Datenquelle:** Ein Windows-Testsystem soll Sysmon- und PowerShell-Ereignisse über den Wazuh-Agenten an eine separate VM auf dem Ubuntu-Host senden.
- **Auswertung:** Mit ungefährlichen Simulationen und legitimen Vergleichsfällen werden Recall, False-Positive-Rate und ergänzend Precision untersucht. Für die Evaluation sind 20 von der Entwicklung getrennte Varianten mit je fünf Wiederholungen pro Regelsatz geplant. Der Pilottest soll zeigen, ob dieser Umfang praktikabel ist.

Der erste technische Meilenstein ist ein nachvollziehbarer Ereignisweg: eine bekannte PowerShell-Aktion auf Windows ausführen und das zugehörige Ereignis in Wazuh wiederfinden. Erst danach beginnt die Bewertung der Regeln.
