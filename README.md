# bluehands development guidelines

Hier sind allgemeine Leitf�den und Vorgehensweise zur Softwareentwicklung bei [bluehands](http://www.bluehands.de) zusammengetragen. Sie dienen dazu klarheit zu schafen, wie wir Software erstellen. Unsere Softwareprojekte spiegeln immer diese Guidelines wieder. Anpassungen und Weiterentwicklungen in den Projekten f�hren zu Anpassungen in diesem Dokument.

>Dogmen sind wie Stra�enlaternen. Sie weisen in der Nacht den Irrenden den Weg. Aber nur Betrunkene halten sich daran fest.
>
>Karl Rahner

### Clean Code ###

Unser Ziel ist es hochwertige, evolvierbare Software basierend auf moderne Software-Engineering Praktiken zu erstellen. Die Hinweise der [Clean Code Developer Initiative](http://clean-code-developer.de/) sind einzuhalten. Es ist mindestens der Gr�ne-Grad anzustreben. 

### Projektstruktur ###

Ein Projekt besteht mehr aus nur Quellcode. Um ein Projekt zu strukturieren, verwenden wir folgende Ordnerstruktur, welches als ZIP-Datei zur Verf�gung steht.

Siehe: ".\Project Folder Template\Project Template.zip"

### Application Lifecycle Management (ALM) ###

#### Plattform ####

Als ALM Plattform benutzen wir Visual Studio Team Services (aka TFS online) um unsere Projekte zu verwalten. Als Projekttemplate wird **bluehands Scrum** und als Quellcodeverwaltungssystem **Git** verwendet. 

Im VSTS werden folgende Themen verwaltet:
* Quellcodeverwaltung
* Backlog und alle Scrum-Artefakte wie Sprints usw.
* Das Build-Management inklusive continues integration
* Das Release-Management inklusive continues deployment
* Private projekt spezifische NuGet-Feeds
* Bluehands allgemeine NuGet-Feeds 

Jedes Projekt hat ein continues integration und wenn m�glich ein continues deployment konfiguriert. Alle Artefakte die zum Kunden gehen, werden �ber das Build-Management erstellt und nicht h�ndisch in Visual Studio. Um Artefakte downloadbar zu machen, kann ein *Publish Artifact* Task verwendet werden.

#### Versionierung ####

Jedes Artefakt hat eine Version, die direkt der Quellcodeverwaltung zugeordnet werden kann. So hat z.B. eine Dll eine Version, die den commit-hash im git enth�lt. Falls es zu einem Problem bei der Software gibt, kann genau diese Version abgerufen und analysiert werden.
*tbd: Verweis auf NuGet-Packet*

#### Branching ####

Wir verwenden [Git-Flow](http://nvie.com/posts/a-successful-git-branching-model/) als Pattern zur Verwaltung von branches. F�r Visual Studio kann man ein [PlugIn](https://marketplace.visualstudio.com/items?itemName=vs-publisher-57624.GitFlowforVisualStudio) installieren.


### Unittests und Integrationstests ###

Wir f�hren Unittests meistens aus folgenden Beweggr�nden durch:
* Spezifikation von Verhalten (z.B. Protokolldefinitionen)
* Das Festzurren von Verhalten um beim Refaktoring von Code Seiteneffekte zu vermeinden

Das Festzurren der bestehenden Implementierung f�hrt jedoch auch dazu, dass der Code sich nicht mehr leicht ver�ndern l�sst, ohne ganz viele Tests anzupassen. 

Das Problem l�sst sich dadurch l�sen, dass Methoden immer in zwei Kategorien unterteilt werdne k�nnen:
* Methoden, die nur andere Methoden komponieren 
* Methoden, die nur Logik (If-Bl�cke, Schleifen, ...) enthalten, und keine weitere Methoden aufrufen.

Somit sind die Logik-Bl�cke immer ganz unten in den Bl�tter. [Siehe auch Ralf Westphal](http://blog.ralfw.de/2015/04/die-ioda-architektur.html)

*Dies Logik-Methoden werden mit Unittests zu einem ganz hohem Grade abgedeckt.*
 
*Die Komponierenden-Methoden m�ssen nicht unit getestet werden, da hier nur der Compiler getestet wird. Um trotzdem das Verhalten festzuzurren, wird das System durch integrationstest getestet.*

### Unittest Style ###

Unittest sollen in Form von Given/When/Then geschrieben werden. [Siehe auch Martin Fownler](https://martinfowler.com/bliki/GivenWhenThen.html) 

tbd f�r weitere Erkl�rungen und Beispiele.

### Commit Messages ###

Commit Messages sollen dem Benutzer eine Geschichte erz�hlen. Ein Why, What & How. 
Statt der Nachricht *String Escape in XXX hinzugef�gt*, schreiben wir *Sql-Injection in XXX nicht mehr m�glich, da der Strings jetzt escapt werden*.

[Siehe auch Ilker Cetinkayas Vortrag auf der NRWConf.](https://www.google.de/search?q=Commitmessages+ilker&ie=&oe=#q=Commit+Messages+ilker+Cetinkaya)
 

### Coding-Style ###

Naming Conventions und Coding Styles werden als eine Resharper-Datei zur Verf�gung gestellt.

Siehe: tbd 
 

