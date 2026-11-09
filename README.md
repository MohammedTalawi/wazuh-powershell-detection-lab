# Wazuh-Labor: Erkennung verdächtiger PowerShell-Aktivitäten

Wie verändert sich die Erkennung, wenn Wazuh zusätzlich zu seinen Standardregeln den Kontext eines PowerShell-Aufrufs berücksichtigt? Ein Beispiel aus dem geplanten Testkatalog sind kodierte Aufrufe: Sie kommen sowohl in legitimen Administrationsaufgaben als auch in verdächtigen Szenarien vor. Deshalb soll geprüft werden, was sich aus Befehlszeile, Elternprozess und Skriptinhalt ableiten lässt.

## Stand: Laboraufbau

Der VirtualBox-Bestand auf dem Ubuntu-Host wurde geprüft. Der erste Eintrag im [Laborjournal](docs/laborjournal.md) enthält den Befehl, die Beobachtung und die daraus abgeleitete Entscheidung. Messwerte liegen noch nicht vor.

## Geplanter Vergleich

- **Regelsatz A:** unveränderte Wazuh-Standardregeln.
- **Regelsatz B:** Standardregeln mit vier bis sechs eigenen oder angepassten Erkennungsregeln.
- **Datenquelle:** Ein Windows-Testsystem soll Sysmon- und PowerShell-Ereignisse über den Wazuh-Agenten an eine separate VM auf dem Ubuntu-Host senden.
- **Auswertung:** Mit ungefährlichen Simulationen und legitimen Vergleichsfällen werden Recall, False-Positive-Rate und ergänzend Precision untersucht. Für die Evaluation sind 20 von der Entwicklung getrennte Varianten mit je fünf Wiederholungen pro Regelsatz geplant. Der Pilottest soll zeigen, ob dieser Umfang praktikabel ist.

Der erste technische Meilenstein ist ein nachvollziehbarer Ereignisweg: eine bekannte PowerShell-Aktion auf Windows ausführen und das zugehörige Ereignis in Wazuh wiederfinden. Erst danach beginnt die Bewertung der Regeln.
