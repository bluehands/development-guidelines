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
Statt der Nachricht *String Escape in XXX hinzugefügt*, schreiben wir *Sql-Injection in XXX nicht mehr möglich, da der Strings jetzt escapt werden*.

[Siehe auch Ilker Cetinkayas Vortrag auf der NRWConf.](https://www.google.de/search?q=Commitmessages+ilker&ie=&oe=#q=Commit+Messages+ilker+Cetinkaya)
 

### Coding-Style ###

Naming Conventions und Coding Styles werden als eine Resharper-Datei zur Verfügung gestellt.

Siehe: tbd 
 

