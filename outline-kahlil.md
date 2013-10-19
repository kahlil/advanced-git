# Git Best Practices

## Filemode

git config core.filemode true|false

> core.fileMode
	If false, the executable bit differences between the index and the
	working copy are ignored; useful on broken filesystems like FAT.
	See git-update-index(1). True by default.

Mit dieser Option kann man einstellen ob geänderte Dateiberechtigungen beim einchecken geändert werden oder nicht. Windows ändert Dateiberechtigungen gerne mal, das macht oft nichts aber kann manchmal problematisch sein wenn auf verschiedenen Betriebssystemen gearbeitet wird.

## Zeilenendungen

Git passt beim auschecken die Zeilenenden dem System an und checkt alle Dateien mit LF ein.
Best Practice ist in der Regel eine .gitattributes Datei im Root des Projektes abzulegen und dort das gewünschte Verhalten zu konfigurieren.

text=auto

sichert das automatisch umschreiben der Zeielenenden.

Wenn man auf Windows das Umschreiben der Zeilenenden von LF auf CRLF vermeiden will schreibt man

text eol=lf

in die .gitattributes Datei. Diese wird dann mit eingecheckt.

## Rebase

Merge ist das Standardverhalten und ist am einfachsten.

Rebase erzeugt im Gegensatz zu merge eine gerade Timeline es sind aber ein paar Punkte zu beachten.

* Niemals Commits die irgendwo veröffentlicht wurden rebasen.
* Nur lokale Änderungen rebasen
* Rebase on Pull kann für viele Workflows Sinn machen

## Branchen, Branchen, Branchen

Da das Branchen in Git so einfach ist, sollte man sich angewöhnen im Prinzip für alles zu branchen: für Features, für Bugs, für Experimente. Müssen nicht gepusht werden.

## Stashen, Stashen, Stashen

Es macht Sinn Änderungen die man nicht commiten möchte immer wieder zu stashen. Somit erzeugt man eine weiter Undo-History die nicht in Git selber landet.

Best Practice: `git stash save "My message"` da dann die Stashliste die über `git stash list` aufgerufen werden kann viel mehr Sinn macht. Gesetzt den Fall man schreibt sinnvolle Messages.

## CLI!!!11!!1

Benutzt und beherrscht git in der Kommandozeile bevor ihr mit Tools arbeitet!
In Windows Git Bash verwenden.

* Bash Autocompletion

## Saubere Commitmessages

* Auf Teamweiten Standard einigen
* Nicht `git commit -m` nutzen, da es zu schlampigen Commit-Messages verleitet.
* Der Artikel der als Standard gilt: http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

--
**Capitalized, short (50 chars or less) summary**

**More detailed explanatory text, if necessary.**  Wrap it to about 72
characters or so.  In some contexts, the first line is treated as the
subject of an email and the rest of the text as the body.  The blank
line separating the summary from the body is critical (unless you omit
the body entirely); tools like rebase can get confused if you run the
two together.

**Write your commit message in the imperative: "Fix bug" and not "Fixed bug"
or "Fixes bug."**  This convention matches up with commit messages generated
by commands like git merge and git revert.

Further paragraphs come after blank lines.

- Bullet points are okay, too

- Typically a hyphen or asterisk is used for the bullet, preceded by a
  single space, with blank lines in between, but conventions vary here

- Use a hanging indent
--

## Integration mit externen Tools

* Jira: Wie auch schon mit SVN, kann durch Nennung der Ticketnummer der Commit direkt im Ticket verlinkt werden.

* Jabber: mit post-commit-Hooks können bei Pushes oder deploys Meldungen in Gruppenchats automatisch abgesetzt werden.

* gitweb

## Im Zentralen Repo nur Fast Forwards erlauben

`git init --shared` initialisiert ein Repo mit "receive.denyNonFastForwards = true"

## Das löschen des Repositories verbieten

* "receive.denyDeletes = true"

## Checken was in meinem Branch fehlt

* git log subsets ( git log origin/master ^master )

## Workflows

### Der einfache Commit

* git add .; git commit'

### Feature-spezifisch committen

* mit `git status` nachschauen welche Dateien man pro Feature stagen und commiten will. Dann nur diese Dateien stagen und commiten. Wenn es eine Ticketnummer gibt wir diese in der Commitmessage angegeben.

* `git add -p` bzw. `git add --patch`
   * Dieser Befehl durchläuft sogenannte "hunks" in den ungestageten Dateien. Ein hunk ist ein geänderter Textbereich. Für jeden Hunk können verschiedene Aktionen durchgeführt werden wie: stagen, ändern und überspringen. So können ganz gezielt einzelne Änderungen in Dateien gestaged werden um diese Änderungen dann zu committen.

## Ein einfacher Branching Workflow

Branches: master, dev, feature-***, bug-***, hotfix-***
In master direkt wird nie gearbeitet. master ist immer der Stand der nach Produktion deployt werden kann. Zu jeder Zeit.

Features werden in feature-*** Branches entwickelt und dann nach dev gerebased oder gemerged. dev wird regelmässig automatisch auf einen dev-Server deployed.

Hotfixes werden immer in einem eigenen Branch hotfix-*** entwickelt dann in dev integriert und getestet und dann in master integriert und deployed.

Bugs werden in bug-*** Branches entwicklet und nach dev gemerged.

Aus dev wird ein Releasebranch erzeugt der nochmal getestet werden kann und am Schluß nach master gemerged wird und deployt wird.

## Gitflow

Gitflow ist im Prinzip der gerade beschriebene Workflow mit genaueren Abstufungen für Releases und Branches es lohnt sich dafür den Referenzartikel zu lesen und dann im Team zu entscheiden wie euer Workflow aussehen soll. Konventionen.

## Abschluß

* Man muss sich befassen
* Ist wichtig
* Quellcode ist was wir produzieren
* Nimmt Arbeit ab
* Erleichtert es die Versionierungqualität zu erhöhen
* Kämpfen gegen CVS kann entfallen
* Auch wenn man Zeit investieren muss > zahlt sich aus
* Einfach ausprobieren
* Mit Testrepos spielen
* Git KnowHow ist da, bei Unsicherheiten einfach fragen

## Interessante Links
### Scott Chacon's Git Einführung Video und Slides
http://www.youtube.com/watch?v=ZDR433b0HJY
https://speakerdeck.com/schacon/introduction-to-git

### Advanced Git Video
https://vimeo.com/49444883

### Git Bücher
http://git-scm.com/book
http://www-cs-students.stanford.edu/~blynn/gitmagic/

### Git Webseite
http://git-scm.com/

