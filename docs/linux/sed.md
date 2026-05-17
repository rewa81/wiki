## `sed`: Der Stream-Editor für die Kommandozeile

`sed` (**s**tream **ed**itor) ist ein leistungsstarkes Kommandozeilen-Tool unter Unix und Linux, das zum Filtern und Transformieren von Text verwendet wird. Es arbeitet zeilenweise und ermöglicht es, komplexe Änderungen an Textdateien vorzunehmen, ohne sie manuell öffnen und bearbeiten zu müssen. 

### Grundlagen

Die grundlegende Syntax von `sed` lautet:

```bash
sed 'Befehl' Datei
```

* `Befehl`: Der `sed`-Befehl, der die gewünschte Aktion beschreibt.
* `Datei`: Die Datei, auf die der Befehl angewendet werden soll.

### Einfache Ersetzungen (Search and Replace)

Eine der häufigsten Anwendungen von `sed` ist das Ersetzen von Textmustern.

#### Beispiel 1: Einfache Ersetzung

```bash
sed 's/alt/neu/g' datei.txt
```

Dieser Befehl ersetzt alle Vorkommen von "alt" durch "neu" in der Datei `datei.txt`.

* `s`: steht für "substitute" (ersetzen).
* `/alt/neu/`: definiert das zu ersetzende Muster ("alt") und den Ersatztext ("neu").
* `g`: steht für "global" und sorgt dafür, dass alle Vorkommen in einer Zeile ersetzt werden.

#### Beispiel 2: Ersetzung mit regulären Ausdrücken

```bash
sed 's/[0-9]\{3\}/-/g' datei.txt
```

Dieser Befehl ersetzt alle Vorkommen von drei aufeinanderfolgenden Ziffern durch einen Bindestrich.

* `[0-9]\{3\}`: Ein regulärer Ausdruck, der drei Ziffern übereinstimmt.

### Änderungen in YAML- und JSON-Dateien

`sed` kann auch zum Ändern von Werten in YAML- und JSON-Dateien verwendet werden, obwohl spezielle Tools wie `yq` für YAML und `jq` für JSON oft besser geeignet sind, da sie die Struktur dieser Dateiformate verstehen.

#### Beispiel 3: YAML-Attributwert ersetzen

```bash
sed -i 's/^\(name:\s*\).*/\1Neuer Name/' config.yml
```

Dieser Befehl ersetzt den Wert des Attributs `name` in der Datei `config.yml` durch "Neuer Name".

* `-i`: Bearbeitet die Datei direkt (**in-place**).
* `^\(name:\s*\).*/`: Ein regulärer Ausdruck, der die Zeile mit dem Attribut `name` findet und den aktuellen Wert erfasst.
* `\1Neuer Name/`: Ersetzt die gesamte Zeile durch den erfassten Attributnamen (`\1`) und den neuen Wert.

#### Beispiel 4: JSON-Wert ersetzen (mit Einschränkungen)

```bash
sed -i 's/"alter_wert"/"neuer_wert"/g' data.json
```

Dieser Befehl ersetzt alle Vorkommen von `"alter_wert"` durch `"neuer_wert"` in der Datei `data.json`.

**Achtung:** `sed` ist nicht ideal für komplexe JSON-Änderungen, da es die JSON-Struktur nicht berücksichtigt. Für solche Fälle ist `jq` die bessere Wahl.

### Änderungen in XML-Dateien

Ähnlich wie bei JSON ist `sed` für einfache Ersetzungen in XML-Dateien geeignet, aber für komplexere Änderungen sind spezielle XML-Tools empfehlenswert.

#### Beispiel 5: XML-Wert ersetzen

```bash
sed -i 's/\(<tag>alter_wert<\/tag>\)/\1neuer_wert\2/g' data.xml
```

Dieser Befehl ersetzt den Wert zwischen den Tags `<tag>` und `</tag>` von "alter_wert" durch "neuer_wert" in der Datei `data.xml`.

### Weitere `sed`-Befehle

`sed` bietet eine Vielzahl weiterer Befehle für komplexere Textmanipulationen. Hier sind einige Beispiele:

* `d`: Löscht eine Zeile.
* `a`: Fügt Text nach einer Zeile hinzu.
* `i`: Fügt Text vor einer Zeile hinzu.
* `c`: Ändert den Inhalt einer Zeile.

### Fazit

`sed` ist ein vielseitiges Werkzeug für die Textverarbeitung in der Kommandozeile. Es ermöglicht schnelle und effiziente Änderungen an Dateien, ohne sie manuell öffnen zu müssen. Obwohl es für einfache Ersetzungen in YAML-, JSON- und XML-Dateien verwendet werden kann, sind spezielle Tools für diese Dateiformate oft besser geeignet, insbesondere bei komplexeren Änderungen.

**Hinweis:** Beim Arbeiten mit `sed`, insbesondere mit der `-i`-Option, ist es ratsam, vorher eine Sicherungskopie der Datei zu erstellen, um Datenverlust zu vermeiden.
