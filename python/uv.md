---
title: "Der Paket- und Projekt-Manager UV"
---

[← Zurück zu Python](./)

[← Zurück zur Startseite](../)


# Der Paket- und Projekt-Manager `UV`


## Links

*   [UV für Python… (Fast) alle Batterien inklusive](https://www.youtube.com/watch?v=qh98qOND6MI&t=698s)

## Install `UV`

```bat
python -m pip install uv
```


## Initialiseren eines neuen Projekts

CLI Applikation erstellen (default):

```bat
uv init --app
```

Python Bibliothek erstellen:

```bat
uv init --lib
```

Python Package erstellen:

```
uv init --package
```

Neues Projekt für eine Applikation erstellen:

```
uv init --app PROJECT_NAME
```

wobei `PROJECT_NAME` auch gleichzeitig der Verzeichnisname ist!

Beispiel: `uv init --app my_project`

Das erstellt folgende Verzeichnisse und Dateien:

```
my_project
├── .git ... (Verzeichnis)
├── .gitignore
├── .python-version
├── main.py
├── pyproject.toml
└── README.md
```

*HINWEIS*:

Die lokale Git Verwaltung wird bei jeder Nutzung des Befehls `uv init` 
automatisch aktiviert.


## Erstellen des `VENV`

Ein *virtual Environment* wird mit dem `UV` Befehl erstellt:

```bat
cd my_project
uv venv
```

Ein Verzeichnis `.venv` und die Lock-Datei `uv.lock` werden angelegt:

```
my_project
├── .git ... (Verzeichnis)
├── .gitignore
├── .python-version
├── .venv ... (Verzeichnis)
├── main.py
├── pyproject.toml
├── README.md
└── uv.lock
```

## Hinzufügen von erforderlichen Bibliotheken

Um dem virtuellen Environment weitere Bibliotheken lokal hinzuzufügen, kann
folgender `uv` Befehl genutzt werden:

```bat
uv add BIBLIOTHEK
```

Beispiel:

```bat
uv add pyyaml
```

Die Änderungen können in der Datei `pyproject.toml` eingesehen werden.


## Entfernen von bestehenden Bibliotheken

Um dem virtuellen Environment bereits installierte Bibliotheken wieder zu 
entfernen, kann volgender `uv` Befehl genutzt werden:

```bat
uv remove BIBLIOTHEK
```

Beispiel:

```bat
uv remove pyyaml
```

Die Änderungen können in der Datei `pyproject.toml` eingesehen werden.


## Abhängigkeiten aller lokal installierten Pakete prüfen

Um die Abhängigkeiten aller lokal installierten Pakete prüfen und ggf. 
korrigieren zu lassen, kann volgender `uv` Befehl genutzt werden:

```bat
uv sync
```

Danach wird eine Datei `uv.lock` im Projektverzeichnis erstellt.


## Abhängigkeiten hierarchisch darstellen

Um die Abhängigkeiten aller lokal installierten Bibliotheken hierarchisch
darzustellen, kann volgender `uv` Befehl genutzt werden:

```bat
uv tree
```

Beispiel:

```
Resolved 4 packages in 1ms
converter-xlsx-json v0.1.0
├── openpyxl v3.1.5
│   └── et-xmlfile v2.0.0
└── pyyaml v6.0.2
```

## Auflisten aller lokal installierten Python Versionen

Um alle lokal installierten Python Version aufzulisten, kann folgender 
`uv` Befehl genutzt werden:

```bat
uv python list
```


## Installieren einer konkreten Python Version

Um eine konkrete Python Version lokal zu installieren, kann folgender 
`uv` Befehl genenutzt werden:

```bat
uv python install VERSION
```

Beispiel:

```bat
uv python install 3.12.0
```


## Setzten der Python Version im lokalen VENV des Projekts

Standardmäßig wird für das lokale VENV des aktuellen Projekts die jeweils 
aktuelle, stabile Python Version automatisch gesetzt.

Soll standardmäßig eine andere lokal verfügbare Python Version für das VENV
des aktuellen Projekts gesetzt werden, kann folgender `uv` Befehl genenutzt 
werden:

```bat
uv venv --python VERSION
```

Beispiel:

```bat
uv venv --python 3.12.0
```


## Setzen des VENV eines `uv`-verwalteten Projekts in Visual Studio Code

