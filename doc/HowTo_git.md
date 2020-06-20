# git

## Repository anlegen und verbinden

* Wechseln in das Verzeichnis, das versioniert werden soll. Dort dann eine initiale `.gitignore`-Datei mit folgendem Inhalt anlegen. Mit dieser gitignore-Datei wird sichergestellt, dass nicht versehentlich eine Datei gepublished wird, die _Geheimnisse_ enthält. Später werden dann alle Dateien oder Verzeichnisse einzeln wieder hinzugefügt.  

```
# /* ensures that everything will be ignored.
/*  
# Now include files and/or directories
!.gitignore
```

* Auf github.com ein leeres (und evtl. anfänglich privates) Repository anlegen.  

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

Um das Repository auf den Laptop zu clonen, wechselt man in das Verzeichnis, in dem der neue Projekt-Ordner geclont werden soll. Dort dann einfach das folgende Kommando ausführen:  
`git clone https://github.com/hajo62/HomeAssistant.git`  
Hierdurch wird das Verzeichnis HomeAssistant angelegt und mit dem Inhalt aus github gefüllt.  

### Dateien auf dem Laptop bearbeiten

* Eine neue Datei in einem neuen Verzeichnis (z.B. `doc/HowTo_git.md`) anlegen.  
*  Die Datei `.gitignore` anpassen, so dass die neuen Dateien dem Repository hinzugefügt werden. Dazu folgende Zeilen ergänzen:  

```
!doc
!doc/HowTo_git.md
```

* Mit folgenden Kommandos die Änderungen ins git-Repository übertragen (_committen_):

```
# Geänderte Dateien hinzufügen
git add .

# Ggf. Status anzeigen lassen
git status

# Dateien einchecken
git commit -m "How to git"

# Änderungen an github übertragen
git push
```

## Änderungen auf dem RPi aus github holen

Wenn ausschließlich auf dem Laptop Dateien geändert wurde, genügt das Kommando `git pull`, um die Änderungen von github auf den RPi zu übertragen.

---

## git-secret

Mit git-secret kann man Dateien mit sensitivem Inhalt verschlüsselt im git-Repo ablegen. Ich fand die Handhabung aber recht uständlich, so dass ich dies nach ersten Versuchen wieder eingestellt habe.  

Eine Beschreibung zur git-secret findet sich [hier](https://git-secret.io/installation). 

### Erstellen eine RSA-Schlüsselpaar

Für die Verschlüsselung wird ein RSA-Key benötigt, der mit der eigenen eMail _verknüpft_ist. Erstellen kann man disen mit:

```
gpg --full-generate-key
  > Select (1) RSA and RSA (default)
  > keysize: 4096
  > valid for: 0 = key does not expire
  > Real name: Hans Joachim Pross
  > Email address: hajo62@gmail.com
  > Comment: For gitsecret 
```

Nun noch ein langes Passwort eingeben und das Schlüsselpaar wird erzeugt.

> Anmerkung: Hatte im zweiten Versuch folgende Fehlermeldung:  
gpg: agent_genkey failed: No such file or directory  
Key generation failed: No such file or directory  
Abhilfe hat geschaffen:  
mkdir -p ~/.gnupg/private-keys-v1.d
chmod 700 ~/.gnupg/private-keys-v1.d

### Installation von gitsecret
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

### Nutzung

#### Initialisierung

Das Verzeichnis `.gitsecret` darf nicht in der Datei `.gitignore` ausgeschlossen sein.  

```
git secret init
```
Hierbei wird der Eintrag `.gitsecret/keys/random_seed` in die Datei `.gitignore` eingetragen.  

Als nächstes fügt man sich selbst als User hinzu:

```
git secret tell hajo62@gmail.com
```

git secret add  docker/letsencrypt/config/nginx//site-confs/default  
git status  
git add .  
git status  
git secret hide  
git commit -m "nginx"  
git push  
history  

#### Public- uns Secret-Key Dateien für Laptop erstellen

Mit dem Kommando `gpg --output` wird der Public Key (auf dem RPi) in eine Datei geschrieben und diese anschließend z.B. mit `scp` auf den Laptop übertragen. Um den Public Key exportieren zu können, muss man ein Kennzeichen des Key - z.B. die ID oder die eMail-Adresse - angeben:  
```
gpg --output gitpublic.gpg --export <eMail>
gpg --output gitsecret.gpg --export-secret-keys <eMail>
```

Nach der Übertragung werden die Keys importiert und dem gitrepo bekannt gemacht:  
```
# Import Keys
gpg --import /tmp/gitpublic.gpg
gpg --import /tmp/gitsecret.gpg
# Add to git secrets repo
git secret tell <eMail>
```

#### Dateien entschlüsseln

```
 git secret reveal
```

Nun fehlt noch, dies zu vereinfachen.
hooks, damit man Änderungen an verschlüsselten Dateien auch überträgt
Kennwort für gitsecret hinterlegen.
Auf dem Rapsi noch das github-Kennwort hinterlegen.
