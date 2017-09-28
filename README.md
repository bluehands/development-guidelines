# bluehands development guidelines

Hier sind allgemeine Leitfäden und Vorgehensweise zur Softwareentwicklung bei [bluehands](http://www.bluehands.de) zusammengetragen. Sie dienen dazu klarheit zu schafen, wie wir Software erstellen. Unsere Softwareprojekte spiegeln immer diese Guidelines wieder. Anpassungen und Weiterentwicklungen in den Projekten führen zu Anpassungen in diesem Dokument.

>Dogmen sind wie Straßenlaternen. Sie weisen in der Nacht den Irrenden den Weg. Aber nur Betrunkene halten sich daran fest.
>
>Karl Rahner

### Clean Code ###

Unser Ziel ist es hochwertige, evolvierbare Software basierend auf moderne Software-Engineering Praktiken zu erstellen. Die Hinweise der [Clean Code Developer Initiative](http://clean-code-developer.de/) sind einzuhalten. Es ist mindestens der Grüne-Grad anzustreben. 

### Projektstruktur ###

Ein Projekt besteht mehr aus nur Quellcode. Um ein Projekt zu strukturieren, verwenden wir folgende Ordnerstruktur, welches als ZIP-Datei zur Verfügung steht.

Siehe: ".\Project Folder Template\Project Template.zip"

### Application Lifecycle Management (ALM) Plattform ###

Als ALM Plattform benutzen wir Visual Studio Team Services (aka TFS online) um unsere Projekte zu verwalten. Als Projekttemplate wird **bluehands Scrum** und als Quellcodeverwaltungssystem **Git** verwendet. 

Im VSTS werden folgende Themen verwaltet:
* Quellcodeverwaltung
* Backlog und alle Scrum-Artefakte wie Sprints usw.
* Das Build-Management inklusive continues integration
* Das Release-Management inklusive continues deployment
* Private projekt spezifische NuGet-Feeds
* Bluehands allgemeine NuGet-Feeds 

Jedes Projekt hat ein continues integration und wenn möglich ein continues deployment konfiguriert. Alle Artefakte die zum Kunden gehen, werden über das Build-Management erstellt und nicht händisch in Visual Studio. Um Artefakte downloadbar zu machen, kann ein *Publish Artifact* Task verwendet werden.

#### Versionierung ####

Jedes Artefakt hat eine Version, die direkt der Quellcodeverwaltung zugeordnet werden kann. So hat z.B. eine Dll eine Version, die den commit-hash im git enthält. Falls es zu einem Problem bei der Software gibt, kann genau diese Version abgerufen und analysiert werden.

Mit dem Bluehands.Versioning-Package wird die Versionierung automatisch bei jedem Build umgesetzt. Dazu muss ein neuer Nuget-Feed in Visual Studio hinzugefügt werden:

1. Tools > NuGet Package Manager > Package Manager Settings
2. Im neuen Fenster links "Package Sources" auswählen
3. Oben rechts auf das Plus-Symbol (+) klicken
4. Unten folgendes eingeben:
    * Name: `Bluehands`
    * Source: `https://bluehands.pkgs.visualstudio.com/_packaging/default/nuget/v3/index.json`
5. Auf "Update" und dann "OK" klicken

Danach ganz normal das Bluehands.Versioning-Package installieren. Dabei muss im Nuget-Fenster oben rechts bei "Package Source" entweder "All" oder "Bluehands" ausgewählt sein. Alle weiteren Informationen finden sich dann in Version.README.txt im Projektordner.

Zum Integrieren des Nuget-Package in den TFS-Build-Prozess muss der entsprechende Feed in der Build-Definition hinzugefügt werden:

1. *Nuget Installer*-Task zur Build-Definition hinzufügen, falls dieser noch nicht existiert
2. Im *Nuget Installer*-Task müssen folgende Einstellungen vorgenommen werden:
    * NuGet arguments: `-Source "https://bluehands.pkgs.visualstudio.com/_packaging/default/nuget/v3/index.json;https://api.nuget.org/v3/index.json"`
    * Advanced > NuGet version: `3.5.0` (oder höher) auswählen

### Branching ###

Wir verwenden [Git-Flow](http://nvie.com/posts/a-successful-git-branching-model/) als Pattern zur Verwaltung von branches. Für Visual Studio kann man ein [PlugIn](https://marketplace.visualstudio.com/items?itemName=vs-publisher-57624.GitFlowforVisualStudio) installieren.

### Automatisches Deployment & Qualitätssicherung ###

Wir möchten unsere Software (genauer das Artefakt) in gleichbleibender und guter Qualität liefern. Dies stellen wir durch einen hohen automatisierungsgrad bei der Qualitätssicherung mithilfe von VSTS und dem Release Management sicher. Wir verwenden folgende Vorgehensweise:

* Wir haben die GitFlow Branches master, develop, feature\\\*, release\\\* und hotfix\\\*
* Es gibt eine Continuous Build auf alle Branches  
* Es gibt eine Release-Definition auf diese Build-Definition
* Für alle Branches wird in die Umgebung **Continuous Integration** deployed und automatisch getestet
* Für die branches release\\\* und hotfix\\\* wird in die Umgebung **QS Abnahme** deployed und automatisch und manuell getestet. Es wird ein approval vor dem Deployment angefordert (damit findet eine synchronisierung auf die Umgebung statt). Nach den Tests wird ein approval zur Freigabe angefordert.   
* Für die branches release\\\* und hotfix\\\* wird in die Umgebung **Kunde Abnahme** deployed und automatisch und manuell getestet. Es wird ein approval vor dem Deployment angefordert. Nach den Tests wird ein approval zur Freigabe angefordert.   
* Nach **QS Abnahme** wird der branch automatisch in master zurückgeführt. Für develop wird ein pull request erzeugt. Technisch erzeugen wir hier eine **Pseudo Umgebung**, welche automatisch nach **QS Abnahme** deployed wird. Diese Umgebung hat dann die git-Tasks.


### Unittests und Integrationstests ###

Wir führen Unittests meistens aus folgenden Beweggründen durch:
* Spezifikation von Verhalten (z.B. Protokolldefinitionen)
* Das Festzurren von Verhalten um beim Refaktoring von Code Seiteneffekte zu vermeinden

Das Festzurren der bestehenden Implementierung führt jedoch auch dazu, dass der Code sich nicht mehr leicht verändern lässt, ohne ganz viele Tests anzupassen. 

Das Problem lässt sich dadurch lösen, dass Methoden immer in zwei Kategorien unterteilt werdne können:
* Methoden, die nur andere Methoden komponieren 
* Methoden, die nur Logik (If-Blöcke, Schleifen, ...) enthalten, und keine weitere Methoden aufrufen.

Somit sind die Logik-Blöcke immer ganz unten in den Blätter. [Siehe auch Ralf Westphal](http://blog.ralfw.de/2015/04/die-ioda-architektur.html)

*Dies Logik-Methoden werden mit Unittests zu einem ganz hohem Grade abgedeckt.*
 
*Die Komponierenden-Methoden müssen nicht unit getestet werden, da hier nur der Compiler getestet wird. Um trotzdem das Verhalten festzuzurren, wird das System durch integrationstest getestet.*

### Unittest Style ###

Unittest sollen in Form von Given/When/Then geschrieben werden. [Siehe auch Martin Fownler](https://martinfowler.com/bliki/GivenWhenThen.html) 

tbd für weitere Erklärungen und Beispiele.

### Commit Messages ###

Commit Messages sollen dem Benutzer eine Geschichte erzählen. Ein Why, What & How. 
Statt der Nachricht *String Escape in XXX hinzugefügt*, schreiben wir *Sql-Injection in XXX nicht mehr möglich, da der String jetzt escapt werden*.

[Siehe auch Ilker Cetinkayas Vortrag auf der NRWConf.](https://www.google.de/search?q=Commitmessages+ilker&ie=&oe=#q=Commit+Messages+ilker+Cetinkaya)
 
### Entwicklungsprozess ###

Wir verwenden Scrum als Entwicklungsprozess. D.h. wir haben: 

* Ein Backlog mit Userstories die geschätzt sind. 
* Bei Scrum mit Festpreis steht die Anzahl der Effortpunkte fest und ist transparent kommuniziert.
* Ein Sprintbacklog mit Tasks die im Planning erstellt wurden. Evtl. kommen noch weitere im Verlauf der Entwicklung hinzu. Keine Handlung ohne Task.
* Bei mehreren Beteiligten ein Dailyscrum
* Eine vorbereitete Sprint Demo mit dem Kunden
* Eine Sprint Retroperpektive

### Verantwortlichkeiten ###

In jedem Projekt gibt es eine Reihe von Verantwortlichkeiten, die erfüllt seien müssen. Das kann auch in Personalunion geschehen. Im einzelnen sind diese:

* Technischer Skope. Dies bedeutet, dass das Projekt in Absprache mit dem PO in Zeit und in Funktionalität abgeschlossen wird.
* Kaufmänischer Skope. Achtet darauf, dass das Projekt in Budget abgeschlossen wird. Hier wird ein kleinteiliges Reporting für alle Beteiligten erstellt. Z.B. ein Burndown/Burnup Chart. 
* Funktionaler Skope. Pflegt das Backlog.
* Ansprache Kunde. Stellt sicher, dass der Kunde immer mit an Board ist. Er/Sie weiß was gerade entwicklet wird, wann was fertig wird und wie der Status gerade ist. Koordiniert Termine mit Kunden. Kümmert sich darum, dass die Sprint-Demo vorbereitet ist.

 
### Coding-Style ###

Naming Conventions und Coding Styles werden als eine Resharper-Datei zur Verfügung gestellt.

Siehe: tbd 
 

