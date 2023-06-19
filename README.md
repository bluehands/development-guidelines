# bluehands development guidelines

Hier sind allgemeine Leitfäden und Vorgehensweise zur Softwareentwicklung bei [bluehands](http://www.bluehands.de) zusammengetragen. Sie dienen dazu klarheit zu schafen, wie wir Software erstellen. Unsere Softwareprojekte spiegeln immer diese Guidelines wieder. Anpassungen und Weiterentwicklungen in den Projekten führen zu Anpassungen in diesem Dokument.

>Dogmen sind wie Straßenlaternen. Sie weisen in der Nacht den Irrenden den Weg. Aber nur Betrunkene halten sich daran fest.
>
>Karl Rahner

## Clean Code

Unser Ziel ist es hochwertige, evolvierbare Software basierend auf moderne Software-Engineering Praktiken zu erstellen. Die Hinweise der [Clean Code Developer Initiative](http://clean-code-developer.de/) sind einzuhalten. Es ist mindestens der Grüne-Grad anzustreben. 

## Projektstruktur

Ein Projekt besteht mehr aus nur Quellcode. Um ein Projekt zu strukturieren, verwenden wir folgende Ordnerstruktur, welches als ZIP-Datei zur Verfügung steht.

Siehe: ".\Project Folder Template\Project Template.zip"

## Application Lifecycle Management (ALM) Plattform

Als ALM Plattform benutzen wir Azure DevOps (aka TFS online) um unsere Projekte zu verwalten. Als Projekttemplate wird **bluehands Scrum** und als Quellcodeverwaltungssystem **Git** verwendet.

Im VSTS werden folgende Themen verwaltet:

* Quellcodeverwaltung
* Backlog und alle Scrum-Artefakte wie Sprints usw.
* Das Build-Management inklusive continues integration
* Das Release-Management inklusive continues deployment
* Private projekt spezifische NuGet-Feeds
* Bluehands allgemeine NuGet-Feeds 

Jedes Projekt hat ein continues integration und wenn möglich ein continues deployment konfiguriert. Alle Artefakte die zum Kunden gehen, werden über das Build-Management erstellt und nicht händisch in Visual Studio. Um Artefakte downloadbar zu machen, kann ein *Publish Artifact* Task verwendet werden.

## Versionierung

Jedes Artefakt hat eine Version, die direkt der Quellcodeverwaltung zugeordnet werden kann. So hat z.B. eine Dll eine Version, die den commit-hash im git enthält. Falls es zu einem Problem bei der Software gibt, kann genau diese Version abgerufen und analysiert werden.

Mit dem Bluehands.Versioning-Package wird die Versionierung automatisch bei jedem Build umgesetzt. Alle weiteren Informationen finden sich dann in Version.README.txt im Projektordner.

Zum Integrieren des Nuget-Package in den Azure DevOps-Build-Prozess muss der entsprechende Feed in der Build-Definition hinzugefügt werden:

1. *Nuget Installer*-Task zur Build-Definition hinzufügen, falls dieser noch nicht existiert
2. Im *Nuget Installer*-Task müssen folgende Einstellungen vorgenommen werden:
    * NuGet arguments: `-Source "https://api.nuget.org/v3/index.json"`
    * Advanced > NuGet version: `3.5.0` (oder höher) auswählen

## Branching

Wir verwenden [Git-Flow](http://nvie.com/posts/a-successful-git-branching-model/) als Pattern zur Verwaltung von branches. Für Visual Studio 2017 kann man ein [PlugIn](https://marketplace.visualstudio.com/items?itemName=vs-publisher-57624.GitFlowforVisualStudio) installieren. 
#### Pull Request ####
Die Branches Develop und Master sind per Branch Policy geschützt. D.h. nur mit Pull Requests und Code Review (Definition of Done, diese Guidlines) kann hier commited werden.

## Automatisches Deployment & Qualitätssicherung

Wir möchten unsere Software (genauer das Artefakt) in gleichbleibender und guter Qualität liefern. Dies stellen wir durch einen hohen automatisierungsgrad bei der Qualitätssicherung mithilfe von VSTS und dem Release Management sicher. Wir verwenden folgende Vorgehensweise:

* Wir haben die GitFlow Branches mail, develop, feature\\\*, release\\\* und hotfix\\\*
* Es gibt eine Continuous Build auf alle Branches  
* Es gibt eine Release-Definition auf diese Build-Definition
* Für alle Branches wird in die Umgebung **Continuous Integration** deployed und automatisch getestet
* Für die branches release\\\* und hotfix\\\* wird in die Umgebung **QS Abnahme** deployed und automatisch und manuell getestet. Es wird ein approval vor dem Deployment angefordert (damit findet eine synchronisierung auf die Umgebung statt). Nach den Tests wird ein approval zur Freigabe angefordert.   
* Für die branches release\\\* und hotfix\\\* wird in die Umgebung **Kunde Abnahme** deployed und automatisch und manuell getestet. Es wird ein approval vor dem Deployment angefordert. Nach den Tests wird ein approval zur Freigabe angefordert.   
* Nach **QS Abnahme** wird der branch automatisch in master zurückgeführt. Für develop wird ein pull request erzeugt. Technisch erzeugen wir hier eine **Pseudo Umgebung**, welche automatisch nach **QS Abnahme** deployed wird. Diese Umgebung hat dann die git-Tasks.


## Unittests und Integrationstests

Wir führen Unittests meistens aus folgenden Beweggründen durch:
* Spezifikation von Verhalten (z.B. Protokolldefinitionen)
* Das Festzurren von Verhalten um beim Refaktoring von Code Seiteneffekte zu vermeinden

Das Festzurren der bestehenden Implementierung führt jedoch auch dazu, dass der Code sich nicht mehr leicht verändern lässt, ohne ganz viele Tests anzupassen. 

Das Problem lässt sich dadurch lösen, dass Methoden immer in zwei Kategorien unterteilt werden können:

* Methoden, die nur andere Methoden komponieren 
* Methoden, die nur Logik (If-Blöcke, Schleifen, ...) enthalten, und keine weitere Methoden aufrufen.

Somit sind die Logik-Blöcke immer ganz unten in den Blätter. [Siehe auch Ralf Westphal](http://blog.ralfw.de/2015/04/die-ioda-architektur.html)

*Dies Logik-Methoden werden mit Unittests zu einem ganz hohem Grade abgedeckt.*
 
*Die Komponierenden-Methoden müssen nicht unit getestet werden, da hier nur der Compiler getestet wird. Um trotzdem das Verhalten festzuzurren, wird das System durch integrationstest getestet.*

### Unittest Style

Unittest sollen in Form von Given/When/Then geschrieben werden. [Siehe auch Martin Fowler](https://martinfowler.com/bliki/GivenWhenThen.html) 

tbd für weitere Erklärungen und Beispiele.

## Rail Road Oriented Programming

Nach möglichkeit wird das Rail Road Oriented mit Option & Result verwendet. Rückgabetypen sind entweder Result<T> oder alternativ ein Union-Type. Siehe [Funicular Switch](https://github.com/bluehands/Funicular-Switch) und [Switchyard](https://github.com/bluehands/Switchyard)


## Security
Wir haben eine hohe Verantwortung gegenüber unsere Anwender*innen und Partner und müssen sicherstellen, dass unsere Software keine Schwachstellen enthält.

### Information Protection

* Die Kommunikation über das Netzwerk ist immer verschlüsselt. In Web-Projekten immer HTTPS erforderlich einschalten.
* Nur mit sicheren Gegenstellen kommunizieren. Das Überprüfen von Zertifikaten darf nur kurzfristig ausgeschaltet werden. Diese Umgehung wird in einer eigenen Compiler-Konstante ummantelt, so dass sie im Release- und Debug-Build nicht wirkt.
* Keine Geheimnisse (Schlüssel, Kennwörter) im Code. 
* Keine Ausgabe von Kennwörter, Session-Token im Log. *Vorsicht bei der Ausgabe von ganzen Objkten, diese könnten Geheimnisse enthalten.* Falls ein Tracking von solchen Daten notwendig ist, dann dise Information erst hashen und dann ausgeben.
* Kennwörter und andere Zugangesdaten in Konfigurationsdateien verschlüsseln. Die Verschlüsselung erfolgt mit Framework-Mitteln (aspnet_regiis, .Net core secret manager tool, Azure KeyVault).

### Injection

Benutzereingaben sind grundsätzlich nicht vertrauenswürdig. Bevor diese verarbeitet werden, müssen diese *entschärft* werden. 
* Sql-Injection: Keine string konkatenierung auf Benutzereingabe. Immer parametrierte Abfragen benutzen. 
* Script-Injection: Eingaben in Web-Anwendungen die wiederum ausgegeben werden (Chat, Kommentare, Namen) immer mit Framework-Mitteln escapen.
#### Fremd-Bibliotheken ####
Bibliotheken können potenziell Sicherheitslücken aufweisen oder Schadcode beinhalten.
* Nur Bibliotheken von Publisher mit einer hohen Reputation verwenden (Anzahl Downloads, GitHub Aktivitäten).
* Die Bibilotheken werden automatisch auf Malware gescannt. Hierzu wird WhiteSource Bolt in Azure DevOps eingebunden. Details [hier](https://bolt.whitesourcesoftware.com/azure/faq/).
* Bei Client-Software, die von uns ausgeliefert wird, werden alle Pakete in unserem privaten Feed kopiert und von da aus referenziert. 

## Commit Messages

Commit Messages sollen dem Benutzer eine Geschichte erzählen. Ein Why, What & How. 
Statt der Nachricht *String Escape in XXX hinzugefügt*, schreiben wir *Sql-Injection in XXX nicht mehr möglich, da der String jetzt escapt werden*.

[Siehe auch Ilker Cetinkayas Vortrag auf der NRWConf.](https://www.google.de/search?q=Commitmessages+ilker&ie=&oe=#q=Commit+Messages+ilker+Cetinkaya)
 
## Entwicklungsprozess

Wir verwenden Scrum als Entwicklungsprozess. D.h. wir haben: 

* Ein Backlog mit Userstories die geschätzt sind. 
* Bei Scrum mit Festpreis steht die Anzahl der Storypoints fest und ist transparent kommuniziert.
* Ein Sprintbacklog mit Tasks die im Planning erstellt wurden. Evtl. kommen noch weitere im Verlauf der Entwicklung hinzu. Keine Handlung ohne Task.
* Bei mehreren Beteiligten ein Dailyscrum
* Eine vorbereitete Sprint Demo mit dem Kunden
* Eine Sprint Retroperpektive

## Verantwortlichkeiten

In jedem Projekt gibt es eine Reihe von Verantwortlichkeiten, die erfüllt seien müssen. Das kann auch in Personalunion geschehen. Im einzelnen sind diese:

* Technischer Skope (Der Baumeister). Dies bedeutet, dass das Projekt in Absprache mit dem PO in Zeit und in Funktionalität abgeschlossen wird.
    * Erstellt Sprint Burndown (PBI) und Release Burnup Chart (Features)   
    * Kommuniziert Zeit/Funktions-Horizont an den PO und Beteiligten     
* Kaufmänischer Skope (Der Kaufmann). Achtet darauf, dass das Projekt in Budget abgeschlossen wird. Hier wird ein kleinteiliges Reporting für alle Beteiligten erstellt.
    * Erstellt ein Budget-Report (X-Prozent von Budget im zusammenhang mit Y-Prozent der Storypoints). 
    * Kummuniziert an bluehands-Team
* Funktionaler Skope (Der Dichter). Achtet darauf, dass das Projekt vollständig und verständlich im Backlog beschrieben ist. Pflegt das Backlog
    * Alle Features haben Storypoints und alle PBI haben Effort-Punkte 
    * Unterstützt den PO beim Schreiben und Priorisieren
    * Achtet darauf, dass der Fortschritt im Backlog sichtbar ist. Z.B. der State von Task und PBI sind gesetzt
    * Achtet darauf, dass die Änderungen im Backlog dokumentiert sind
* Ansprache Kunde (Der Bote). Stellt sicher, dass der Kunde immer mit an Board ist. Er/Sie weiß was gerade entwicklet wird, wann was fertig wird und wie der Status gerade ist. 
    * Koordiniert Termine mit Kunden.
    * Stellt Zugänge für den Kunden zur Verfügung (VSTS Account u.Ä.) 
    * Kummuniziert die Ansprechpartner
    * Kümmert sich darum, dass die Sprint-Demo vorbereitet ist.

## Clean Architecture

In der Regel verwenden wir für eine Applikation die [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html) bzw. die [Hexagonale Architektur](https://en.wikipedia.org/wiki/Hexagonal_architecture_(software)). Ziel ist die Abhängigkeit der Kerndomäne von der Infrastruktur zu lösen.

## Coding-Style

Naming Conventions und Coding Styles werden als eine Resharper-Datei zur Verfügung gestellt.

Siehe: tbd 
 

