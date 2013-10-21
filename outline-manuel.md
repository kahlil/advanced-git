# GIT Fundamentals

  * Vorstellen

# Agenda

  * Agenda
  * Wer hat noch keine Erfahrung mit Git? 

# Prinzipien (10min)

  * Dezentral: Gesamtes Repo lokal verfügbar, Effizientes Checkout, Branching, Logs, ...
  * Nicht-lineare Entwicklung: Branchen sehr einfach und effizient
  * Sicher: Aktuelle Revisionshash wird Berechnet aus der gesamten History -> Fälschungssicher
  * Effizient: Einfache Speicherung ähnlich Posix-Dateisystem

## Git vs. X (SVN)

  * Jedes andere "traditionelle" Versionskontrollsystem: File basiert, Delta-Storage
    * Initial File + Deltas oder Aktuelles File + Deltas
    * Für Operationen, die mehr als den aktuellen Stand behandeln ineffizient / komplex / langsam
    * Änderung des Delta-Algorithmus führt zu Änderung des Repo-Dateisystems

  * In traditionellen VCSen: Rename, Copy, Move etc. wird als Operation gespeichert
    * Problemanfällig, "aufwändig"

## Git

  * File basiert, Snapshot-Speicherung
    * Jedes File wird (lokal) vollständig vorgehalten
    * Bei Remote-Operationen Delta-Kompression über das gesamte Repo

  * Berechnung von Hashes aus Dateiinhalten und Filegröße
    * Commit somit Ansammlung von "Hardlinks" und Dateinamen, -berechtigungen
    * Rename, Move etc. wird aus Diff der Commits extrapoliert
    * Repo-Dateisystem seit Version 1.0 (Dezember 2005) unverändert

## Grundlagen (20min bis einschl. Demo)

## Plumbing vs Porcelain Features => Unix Prinzip
  
  * Plumbing = Verdrahtung
  * Porcelain = Porzelan
  * Porzelan nutzt Verdrahtung, aber Verdraht nicht versteckt sondern zugänglich

## Begriffe

  * Repository: Sammlung von Commits. Hat eine HEAD-Referenz auf den aktuellen Commit, der im Working Tree vorliegt (ggf. verändert). Sammlung von Branches und Tags, die Commits idenitifieren.
  * Commit: Zustand des Working Trees zu einem gewissen Zustand. 
  * Working Tree: Das Arbeitsverzeichnis, in dem ein Git-Repo vorhanden ist
  * HEAD: Zeigt auf den aktuellen Commit (bzw. Branch) der ausgecheckt ist, unabhängig, ob dieser bereits verändert wurde. Aber auch der aktuellste Commit eines Branches wird so genannt.
  * Staging Area / Index: Veränderungen im Working Tree werden zuerst im Index registriert, bevor sie commited werden. 
  * Master: Der "Standard"-Branch, der immer vorhanden ist und je nach Konvention in verschiedener Weise genutzt wird.
  * Branch: Zeigt immer auf den aktuellsten Commit eines Entwicklungszweiges (+ Name)
  * Tag: Zeigt immer auf den gleichen Commit innerhalb des Repositories (+ Name, Beschreibung)

  * Ablaufdiagramm: Repository - Working Tree - Staging Area

## Repository

  * Dateien werden in Blobs gespeichert
  * Zustand von dem Repository-Zustand in Baum-Objekten (Tree)
    * Hier sind die Datei-Metadaten enthalten, z.B. Permissions, Datum, ...

## Blob

  * Name vom Blob: SHA1( Dateiname + Dateigröße)
  * Damit wird jede Datei nur Anhand ihres Inhalts (nicht Meta-Daten) identifiziert
  * Jede eindeutige Datei wird nur einmal gespeichert, auch wenn über Pulls/Merges/etc. andere Repositories/Branches/etc. in das eigene Repo genommen werden
  * Daten sind unveränderbar (immutable) in GIT


## Bäume


Im Repo also - ein Blob (das File), ein Tree Objekt, das das File referenziert und ein Commit, das auf den Tree zeigt. 


## Commits

  * Commits sind das "gröbste" Artefakt in Git
  * Branches sind nur Referenzen auf Commits (idR der HEAD des Branches)
  * Ein Commit hat 1..n Parents, diese haben wiederum Parents...
    * Aus den Daten eines Commits kann man die ganze History ableiten
    * Ein Commit mit mehreren Eltern ist ein Merge-Commit
    * Ein Commit mit mehreren Kindern ist das Eltern von einem neuen Branch

## Speicherung (Schaubild)
  
  * Git trackt Dateien/Trees/Commits über das gesamte Repo
  * Solange ein Hash für ein Objekt das gleiche ist, wird es nicht doppelt gespeichert
  * Das gilt für quasi alles, also Commits, Trees, Blobs
  * Und das gilt über Branches, Tags etc. hinweg!
  * Deshalb sind diese Operationen auch so "günstig" in Git

## Commits 

  * Tags sind also das gleiche wie Branches, nur haben sie noch ne zusätzliche Beschreibung
  * Alle Aktionen in GIT, die mit Namen (für Branches, Tags) ausgeführt werden, können auch mit Hashes ausgeführt werden

  * Schaubild History mit Branch und Tag

## Demo

~~~
$ mkdir sample; cd sample
$ echo 'Hello, world!' > greeting
$ git hash-object greeting
af5626b4a114abcb82d63db7c8082c3c4756e51b

$ git init
$ git add greeting
$ git commit -m "Added greeting"

$ git cat-file -t af5626b
blob
$ git cat-file blob af5626b
Hello, world!
~~~
~~~
$ git ls-tree HEAD
100644 blob af5626b4a114abcb82d63db7c8082c3c4756e51b greeting

$ git rev-parse HEAD # zeig Revision Hash für HEAD
2c87275b325b704a6978f3ed1d16c6ab4912064e

$ git cat-file -t HEAD
$ git cat-file -t 2c87275b325b704a6978f3ed1d16c6ab4912064e
commit

$ git cat-file commit HEAD
  # Commit Informationt

$ git ls-tree 2c87275b325b704a6978f3ed1d16c6ab4912064e
100644 blob af5626b4a114abcb82d63db7c8082c3c4756e51b greeting
~~~
~~~
$ find .git/objects/ -type f | sort
.git/objects/05/63f77d884e4f79ce95117e2d686d7d6e282887
.git/objects/2c/87275b325b704a6978f3ed1d16c6ab4912064e
.git/objects/af/5626b4a114abcb82d63db7c8082c3c4756e51b
~~~

Erste zwei Buchstaben vom Hash = Directory. Man kann also:

~~~
$ git cat-file -t 0563f77d884e4f79ce95117e2d686d7d6e282887
tree

...
~~~

~~~
$ git branch -v
  master      8dc350a SW-5671 - Add parellel lint processing
* shop-ekz-de ac9e8af Konfiguration der OxaionDB jetzt per PDO
~~~

## Branching, Merging und Rebase (10min)

  * Da Branches (im Gegensatz zu SVN) einfach nur eine Referenz auf ein Commit sind, ist es natürlich deutlich einfacher, solche zu erstellen und zu verwenden
  * Zum "Zusammenführen" von Branches gibt es zwei Möglichkeiten: Mergen und Rebasen
  * TODO: Diagramm Zwei Branches

~~~
  0 - A - B - C -D  (dev)
   \
    - W - X - Y - Z (master)
~~~

### Mergen

  * Ziel: Änderungen von Commit D in Branch mit Commit Z verfügbar machen

~~~
git checkout master
git merge dev
~~~

~~~
  0 - A - B - C - D-\  (dev)
   \                 \ 
    - W - X - Y - Z - Z' (master)
~~~

  * Ggf. Konflikte
  * Mehrere Merge-Algorithmen

### Rebasen

  * Rebasen: Verändern des "Base-Commits" eines Branches

~~~
git checkout dev
git rebase master
~~~

   * wird zu:
   
~~~
0 - W - X - Y - Z - A - B - C - D (master)
~~~

  * Dabei verändert Rebase (offensichtlich) die History des Repositories
    * Mehr dazu in den Best Practises
  * Aber: Schönere (einfachere) History
  
  * Nicht nur Zusammenführen, sondern auch "Umpflanzen" möglich

  * Feature soll aus Dev gebrancht sein

~~~
git rebase --onto dev master feature # --onto new-branch oldbranch current-branchname
~~~

~~~
                 1 - 2 - 3 (dev2)
                 |
     A - B - C - D  (dev)
   /
  0 - W - X - Y - Z (master)
~~~

  * Mit interaktivem Rebase weiteres Verändern der History
    * Pick: Auswählen, welche Commits übernommen werden sollen
    * Squash: Zusammenführen von mehreren Commits zu einem
    * Edit: Verändern von Commits
    * Drop: Rauswerfen eines Commits

  * Rebase on Pull: Lineare Entwicklungshistory, keine Merge nötig beim Pullen

### Anwendungsbeispiel: Shopware / Kunde EKZ

  * Branch von Tag von Shopware-Version
  * Eigene Module, Templates als Submodul eingebunden
  * Release-Tags definieren Stand von Shopware, Module, Template
  * Versionsupgrade von Shopware? Rebase mit --onto, Umsetzen des eigenen Branches von einer Version zur nächsten


***Ab hier Bonusmaterial***

## Reset vs Checkout

### Checkout

  * Checkout: Kopieren von Files aus der History oder dem Stage in das Working Directory 
    * Mit --force Überschreiben von lokalen Änderungen an bereits eingecheckten Files

~~~
git checkout c10b9 files
~~~

  * Checkout: Verschieben des aktuellen HEADs auf eine Referenz (Branch, Tag) oder ein Commit:

~~~
git checkout master
git checkout v1.0
git checkout c10b9
~~~

  * Checken wir ein Commit aus, das keine Referenz hat, so müssen wir aufpassen - dieser Fall wird detached Mode genannt
  * Wir können commiten, aber da keine Referenz auf diesen Commit zeigt, "kennt" das niemand
  * Möglichkeit zu Experimenten etc.
  * Checken wir jetzt wieder master aus, so ist dieser Commit "weg" (nirgendwo referenziert)
  * Im Reflog ist dieser Commit vorhanden und kann wieder ausgecheckt werden
  * Oder in den Branch gemerged/rebased/gecherry-picked werden
  * Oder nachträglich zu einem Branch gemacht werden


### Reset

  * Unstagen von Änderungen: `git add file`, `git reset file`

  * Verschiebt die Referenz des aktuellen Branches zu einer anderen Position (DIAGRAMM) und entfernt ggf. Files aus dem Stage
    * Die Änderungen aus den "übersprungenen" Commits werden bei `git status` als Änderung angezeigt
  * Optional: Verändern des Working Trees ohne Wegwerfen von Änderungen (--soft)
  * Optional: Verändern des Working Trees mit Wegwerfen von Änderungen (--hard)

  * Ohne Angabe einer Position (bzw. unter Angabe von `HEAD`) wird die Stage auf den Inhalt des letzten Commits gesetzt (bzw. mit --hard das Working Directory verändert): `git reset --hard`

  * Reset ist sehr gefährlich, da es Änderungen verwirft und Branches verändert
  * Besser: `git stash` (siehe unten) und `git checkout -b newbranch commit-hash` - neuen Branch in der Vergangenheit aufmachen und Änderungen sichern!

### Bonus: Clean

  * Zum Aufräumen im Working-Directory: Nicht ausgecheckte Dateien, Build-Artefakte (ignorierte Dateien) wegwerfen: `git clean`

## History vs Reflog

## History

  * Zeigt alle Commits, die referenziert sind

## Reflog

  * Zusätzlich zur normalen History gibt es noch das Reflog
  * Hier werden alle Commits aufgeführt, auch solche, die nicht durch Referenzen aufgefunden werden können
    * Dangling Heads, Anonyme Branches
    * Stashes
  * Nach 30 Tagen (default) werden alle Dangling Heads etc. durch die Garbage Collection von Git gelöscht

~~~
git reflog
~~~
  * Konsole: Auf Konsole unterschied zeigen zwischen History mit Branches und Reflog

## Stashing

  * Änderungen an dem Working-Tree gegenüber von HEAD können als Spezial-Commit abgelegt werden
    * Falls gewünscht imkl. untracked Dateien
  * Damit kann man, z.B. am Ende vom Arbeitstag, seinen Fortschritt "einchecken", ohne einen "echten" Commit zu machen
    * Nie mehr WIP-Commits!
  * Am nächsten Morgen holt man den Stash wieder zurück, belässt diesen aber im Repository
    * Kostenlose History der täglichen Arbeit!
    * Ggf. Kann man das auch bei einem erreichten persönlichen Milestone machen, um z.B. einen funktionierenden Stand einzufrieren
  * Stashes kann man beim Zurückholen automatisch löschen lassen oder nach z.B. 30 Tagen ablaufen lassen

~~~
git stash save -u "message"
git stash list
git stash apply stash@{1}
git stash pop
~~~

## Submodules

  * Mit Submodules kann man andere Repositories in sein Repository integrieren
  * Beispiele: Einbinden von Vendor-Libraries, Plugins, ...
  * Dabei wird auf ein Repository mit URL und auf einen bestimmter Stand in Form des Commit-Hashes verlinkt
  * Mit Commits im Haupt-Repository können damit verschiedene Stände der Unterrepositories in Verbindung mit normalen Code-Änderungen im Haupt-Repo "festgehalten" werden
    * Version 1.0 von Plugin A, Version 2.1 von Modul B sowie alle Änderung bis hierher
    * Tag Setzen
    * Release ist definiert

