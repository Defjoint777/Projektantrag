# Titel der Projektarbeit

Ein Oszilloskop wird verwendet, um elektrische Signale an Sensoren,
Aktoren, Steuergeräten und Bus-Systemen sichtbar zu machen. MetaScope:
Erweiterung eines portablen Oszilloskops für die Automobildiagnose.

# Projektbeschreibung und Ziel

Im Rahmen des Projekts werde ich mehrere zentrale Systemkomponenten
eigenständig entwerfen und implementieren. Dazu gehört zunächst ein
Python-basierter Receiver, der über eine WebSocket-Verbindung
kontinuierlich Messdaten von einem bestehenden C++-Signalserver
entgegennimmt. Diese Daten werden im Receiver vorverarbeitet,
beispielsweise durch ein Batching-Verfahren, um sie strukturiert und
effizient an das Backend weiterzuleiten.

Das Backend selbst wird auf Basis von FastAPI entwickelt. Es nimmt die
Messdaten vom Receiver entgegen, validiert sie anhand definierter Regeln
und vergleicht sie mit bereits vorhandenen Referenzwerten, die in einer
Datenbank gespeichert sind. Ziel ist es, auffällige Abweichungen zu
erkennen und so die Analyseprozesse zu beschleunigen.

Für die Speicherung der Daten entwerfe ich ein eigenes Datenbankmodell,
das sowohl die erfassten Messwerte als auch relevante Metadaten -- wie
etwa Fahrzeugmodell, Baujahr oder Messstelle -- strukturiert abbildet.
Die konkrete Wahl der Datenbanktechnologie ist zum aktuellen Stand noch
offen und wird im Verlauf des Projekts auf Basis technischer und
funktionaler Anforderungen entschieden. Vorrangiges Ziel ist eine
saubere Trennung zwischen dynamischen Messdaten und statischen
Referenzwerten, um einen schnellen und zuverlässigen Abgleich zu
ermöglichen.

# Ist-Zustand und Problemstellung

Der Techniker erhält den Auftrag, einen Drucksensor zu überprüfen,
verfügt jedoch nicht über die herstellerseitigen Referenzspannungswerte.
Er bereitet das Oszilloskop vor, indem er es mit einem
BNC-auf-Bananen-Adapter ausstattet und die Bananenstecker einsteckt. Am
anderen Ende sind zwei Krokodilklemmen angebracht: Eine wird an die
Masseleitung des 3-Pin-Sensors angeschlossen, die andere an den
Signal-Pin. Nach Anpassung von Abtastrate und Trigger-Einstellungen
startet er die Messung. Das Oszilloskop zeigt die Differenz zwischen
Masse und Signal in Form einer Kurve auf den X- und Y-Achsen an. Da
jedoch keine Hersteller-Referenzwerte vorliegen, kann der Techniker
nicht beurteilen, ob die aufgezeichneten Spannungswerte innerhalb der
zulässigen Toleranzen liegen oder ob ein Defekt vorliegt.

# Soll-Analyse und Anforderungen

Um dieses Problem zu lösen mein Konzept sieht vor, dass ich einen
Receiver, ein Backend und eine zugehörige Datenbank entwerfe. Der
Receiver nimmt während der Messung die Rohdaten vom bestehenden
C++-Signalserver entgegen, zerlegt sie in verarbeitbare Einheiten und
leitet sie an das Backend weiter. Im Backend, das als REST-API
ausgeführt wird, werden diese Datensegmente mit den vom Nutzer im
Frontend ausgewählten Metadaten -- etwa Marke, Modell, Baujahr und
Messstelle -- angereichert und unmittelbar gegen eine lokal betriebene
Referenztabelle abgeglichen. Diese Datenbank beinhaltet zuvor ermittelte
Grenzwerte für jede Kombination aus Fahrzeugtyp und Messpunkt. Die
Validierung in der REST-API stellt sicher, dass nur Messwerte
gespeichert werden, die innerhalb der zulässigen Spannungsbereiche
liegen; Abweichungen werden markiert. Schließlich liefert die REST-API
die passenden Referenzwerte an die Angular-Oberfläche, wo sie neben der
Live-Messkurve als schattierter Bereich oder Linienband dargestellt
werden. Auf diese Weise gewinnt der Techniker während der Messung sofort
Klarheit darüber, ob die aufgezeichneten Spannungswerte im vorgegebenen
Toleranzbereich bleiben, und das System bleibt zugleich modular und
erweiterbar für künftige Funktionserweiterungen.

# Umsetzung und Endpunkte

Für mein Projektentwurf ist geplant, zunächst einen Python-basierten
Receiver zu entwickeln, der während der Messung die Rohdaten vom
C++-Server empfängt, sie in passende Datenobjekte überführt und diese in
definierten Batches an das Backend weiterleitet. Das Backend selbst wird
als REST-API realisiert; dort werden eingehende Datensätze
entgegengenommen, hinsichtlich ihrer Konsistenz und ihrer Wertebereiche
validiert und im Messungsverlauf automatisiert auf Mindest- und
Höchstwerte überprüft. Außerdem stellt die REST-API die abgefragten
Mess- und Referenzdaten auf Anforderung bereit. Die zugehörige
SQL-Datenbank übernimmt dabei sämtliches Persistenzmanagement: Sie
speichert validierte Messungen, referenziert sie mit zuvor ermittelten
Grenzwerten und stellt über optimierte Abfragen die gespeicherten
Ergebnisse für das Frontend zur Verfügung.

# Zeitplanung und Projektphasen

  **Phase**                                          **Aufwand (Stunden)**
  ------------------------------------------------- -----------------------
  Analyse und Planung                                          5
  Architekturentwurf                                           4
  Implementierung (Receiver, REST-API, Datenbank)             45
  Debugging                                                   10
  Dokumentation                                               12
  Puffer                                                       4

  : Zeitplanung inkl. Puffer (Gesamt 80 Stunden)

# Wirtschaftlichkeit

Im Rahmen des Projekts findet eine zeitliche Optimierung bestehender
Abläufe statt. Durch die Automatisierung bzw. Vereinfachung einzelner
Schritte wird der manuelle Aufwand reduziert, was langfristig
effizienteres Arbeiten ermöglicht. Obwohl konkrete Zeit- oder
Kosteneinsparungen im Vorfeld nicht quantifiziert werden können, ist von
einer Verbesserung der Prozessgeschwindigkeit und Ressourcennutzung
auszugehen. Eine detaillierte Darstellung der monetären und
nicht-monetären Vorteile erfolgt in der Projektdokumentation.

# Herausforderungen

Im Projekt werden verschiedene Programmiersprachen, Bibliotheken und
Frameworks eingesetzt, etwa Python mit FastAPI, um den jeweiligen
Teilaufgaben optimal gerecht zu werden. Dabei ist in den einzelnen
Schnittstellen -- vom Receiver über die REST-API bis hin zur
Datenbankanbindung -- jeweils eine eigene Geschäftslogik zu
implementieren. Ein zentrales Merkmal ist die Live-Datenkommunikation,
bei der eingehende Messwerte unmittelbar verarbeitet und validiert
werden. Um eine hohe Datenqualität und Konsistenz zu gewährleisten,
erfolgt außerdem eine automatisierte Überprüfung der Messwerte gegen
vordefinierte Grenzbereiche.

# Technologieentscheidungen

Im Verlauf der Umsetzung werde ich für jede technische Komponente
gezielt abwägen, welches Werkzeug am besten zu den Anforderungen passt.
Für das Backend stehen Python mit FastAPI, Node.js mit Express oder
alternativ eine Go-basierte Lösung (zum Beispiel mit dem Gin-Framework)
zur Diskussion. Bei der Wahl der Datenbank entscheide ich zwischen einer
file-basierten SQLite-Instanz in Kombination mit SQLAlchemy, einer
NoSQL-Datenbank wie MongoDB oder einer SQL-Datenbank wie PostgreSQL. Die
Entwicklungsumgebung wähle ich aus Visual Studio Code, WebStorm oder
PyCharm aus, während die Versionskontrolle grundsätzlich mit Git
erfolgt. Die Projektdokumentation kann je nach Bedarf in LaTeX, in
Microsoft Word oder in Markdown verfasst werden. Schließlich prüfe ich
für Diagramme den Einsatz von PlantUML, draw.io oder Software Ideas
Modeler.

# Projektumfeld

Die Entwicklung findet im Büro der AI-Group Bochum an der Husemanplatz
5a, Bochum.Die AI-Group Bochum entwickelt Hard- und Software für die
Automobilindustrie.Von Diagnosetools im Kfz-Bereich über
Auftragsentwicklung für Prozessdiagnostik über effiziente
Marketinglösungen beliefert AI-Gruppe Kunden mit innovativen Produkten
und maßgeschneiderten Lösungen. Die Erweiterung findet Anwendung in
Werkstätten, bei Hobbyschraubern und im Schulungsumfeld der TH Georg
Agricola.

# Vorgehensmodell

Für die strukturierte Umsetzung setze ich ein erweitertes
Wasserfallmodell ein. Dieses Vorgehensmodell gewährleistet einerseits
Planbarkeit und Transparenz und erlaubt zugleich eine flexible Reaktion
auf entstehende Herausforderungen.

# Schnittstellen

Während der Durchführung stehen mir folgende Mitarbeiter der AI-Gruppe
im Büro am Husemannplatz 5a als Ansprechpartner zur Verfügung:

-   Stephan Böckelmann

-   Tabea Röthemeyer

-   Rene Glitza

-   Anna Theimann

# Form der Dokumentation

Dokumentation erfolgt in LaTeX mit eingebundenen PlantUML-Diagrammen.

# Präsentationsmittel

Laptop, Beamer, PowerPoint-Präsentation
