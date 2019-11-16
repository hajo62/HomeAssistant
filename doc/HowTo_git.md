# git
## Repository anlegen und verbinden

* Wechseln in das Verzeichnis, das versioniert werden soll. Dort dann eine initiale `.gitignore`-Datei mit folgendem Inhalt anlegen:  
```
# A /* ensures that everything will be ignored.
/*  
# Now include files and/or directories
!.gitignore
```
* Auf <github.com> ein leeres (und evtl. anfänglich privates) Repository anlegen.  

* Anschließend der Anweisung unter **"…or create a new repository on the command line"** folgen, um das lokale Verzeichnis mit dem git-Repository zu _verbinden_:  

```
git init
git add .
git commit -m "Init"
git remote add origin https://github.com/hajo62/HomeAssistant.git
git push -u origin master
```

Nach dem Refresh der github-Seite sollte nun ausschließlich die Datei `.gitignore` angezeigt werden.  

## Repository auf den Laptop clonen und bearbeiten
### Clonen

Um das Repository auf den Laptop zu clonen, wechselt man in das Verzeichnis, in dem der neue Projekt-Ordner geclont werden soll. Dort dann einfach das Kommando `git clone https://github.com/hajo62/HomeAssistant.git` ausführen. Hierdurch wird das Verzeichnis HomeAssistant angelegt und mit dem Inhalt aus github gefüllt.  






## git-secret

Siehe [hier](https://git-secret.io/installation).  

### Installation
#### RaspberryPi

```
echo "deb https://dl.bintray.com/sobolevn/deb git-secret main" | sudo tee -a /etc/apt/sources.list
wget -qO - https://api.bintray.com/users/sobolevn/keys/gpg/public.key | sudo apt-key add -
sudo apt-get update && sudo apt-get install git-secret
```

#### MacOS

```
brew install git-secret
```
