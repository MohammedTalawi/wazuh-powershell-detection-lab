# Wazuh-Labor: Erkennung verdächtiger PowerShell-Aktivitäten

Wie verändert sich die Erkennung, wenn Wazuh zusätzlich zu seinen Standardregeln den Kontext eines PowerShell-Aufrufs berücksichtigt? Ein Beispiel aus dem geplanten Testkatalog sind kodierte Aufrufe: Sie kommen sowohl in legitimen Administrationsaufgaben als auch in verdächtigen Szenarien vor. Deshalb soll geprüft werden, was sich aus Befehlszeile, Elternprozess und Skriptinhalt ableiten lässt.

## Stand: Laboraufbau

Auf dem Ubuntu-Host ist die eigene VirtualBox-VM `Wazuh-Lab` registriert. Das [Laborjournal](docs/laborjournal.md) dokumentiert die Bestandsprüfung und den Befehl zum Anlegen der VM. Die Installation von Ubuntu Server und Wazuh steht noch aus.

## Geplanter Vergleich

- **Regelsatz A:** unveränderte Wazuh-Standardregeln.
- **Regelsatz B:** Standardregeln mit vier bis sechs eigenen oder angepassten Erkennungsregeln.
- **Datenquelle:** Ein Windows-Testsystem soll Sysmon- und PowerShell-Ereignisse über den Wazuh-Agenten an eine separate VM auf dem Ubuntu-Host senden.
- **Auswertung:** Mit ungefährlichen Simulationen und legitimen Vergleichsfällen werden Recall, False-Positive-Rate und ergänzend Precision untersucht. Für die Evaluation sind 20 von der Entwicklung getrennte Varianten mit je fünf Wiederholungen pro Regelsatz geplant. Der Pilottest soll zeigen, ob dieser Umfang praktikabel ist.

Der erste technische Meilenstein ist ein nachvollziehbarer Ereignisweg: eine bekannte PowerShell-Aktion auf Windows ausführen und das zugehörige Ereignis in Wazuh wiederfinden. Erst danach beginnt die Bewertung der Regeln.
