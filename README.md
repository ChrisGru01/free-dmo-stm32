## FreeDMO

[![Release](https://img.shields.io/github/release/queengooborg/free-dmo-stm32.svg?maxAge=60)](https://github.com/queengooborg/free-dmo-stm32/releases/latest) <== Klicke hier zum downloaden

**solltest du RFID-Tag-Daten kopiert haben für Etiketten, kannst du sie uns hier bereitstellen** (see: https://github.com/free-dmo/free-dmo-tag-dump).

## Installation

Diese Modifikation zu installieren, erfordert Grundkenntnisse in folgenden Bereichen: Löten, Mikroprozessoren, Elektronik. Die Installation erfolgt auf eigene Gefahr  

### benötigtes Werkzeug

- Schraubendrehler
- Lötstation/Lötkolben, Lötzinn
- Einer der beiden Adapter: ST-LINK V2 oder UART Adapter<br>
  (benötigt zum programmieren da dies nicht über den Micro-USB-Port erfolgen kann).

### weitere benötigte Sachen

- 1× D.MO 550-Serie Etikettendrucker<br />
  getestet auf: 550, 550 Turbo und 5XL.

- 1× STM32F103C8T6 bluepill<br/>
  sucht nach: `STM32F103 bluepill` / `STM32F103 bluepill with STLinkV2`<br/>
  stellt sicher dass eure Platine den folgenden Chip verbaut hat: STM32F103C**8**T6 und nicht: STM32F103C**6**T6.
  **ACHTUNG:** viele Händler verkaufen [gefälschte bluepills](https://github.com/keirf/greaseweazle/wiki/STM32-Fakes). Fälschungen funktionieren villeicht nicht.

- 1× 6-pin Molex PicoBlade Kabel<br/>
  sucht nach: `6 pin Molex PicoBlade 1.25 mm` / `6pin 1.25mm MX Wire To Board Connector` / `JST MX 6pin`<br />
  Ihr braucht zwei Kabel mit jeweils eine der genannten Stecker. Sollte euer Kabel zwei Stecker haben, schneidet es einfach in der Mitte auseinander.

- 2× `4.7kΩ` Wiederstand<br />
  OPTIONAL und wird nur benötigt wenn ich die Echten RFID-Tags scannen wollt.

### Zusammenbau

Zuerst nehmt ihr euer Kabel.  Solltest du ein Kit zum selbst crimpen haben, crimpt nun eure Stecker.


**→ Achtet auf die richtige Verdrahtung der Kabel.**<br/>
Die im Bild gezeigten rechten Kabel gehen zum Etiktettendrucker und die linken Kabel zur RFID-Platine wenn ihr diese verwenden wollt.

_Verbinden der RFID-Platine ist optional._ Wenn du nur ein einziges Label drucken willst und keine echten RFID-Chips nutzen willst, musst du auch keine Kabel für die RFID-Platine anlöten und auch keine Wiederstände.

STM32F103 blue pill:
![BLUE PILL](ASSEMBLY_PICTURES/bluepill.jpg)

Verbindung der RFID Platine:
![RFID BOARD](ASSEMBLY_PICTURES/rfid.jpg)

Verbindung zum Drucker-Mainboard:
![MAIN BOARD](ASSEMBLY_PICTURES/mainboard.jpg)

**Prüft die Verkabelung bevor ihr den Etikettendrucker mit Strom versorgt.**

### vorbereiten der Firmware

Es gibt zwei Wege die Firmware für den Bluepill vorzubereiten:

#### Nutzung einer fertigen Firmware

fertige und kompilierte Firmware gibt es hier: [Download](https://github.com/queengooborg/free-dmo-stm32/releases/latest)

Du kannst eine der bereits kompilierten Firmwares nutzen die bereits einen Standart-Tag definiert haben. (wird genutzt wenn kein Orginaler RFID-Tag vorhanden ist).

#### Source-Code kompilieren

um die Firmware vom Source-Code zu kompilieren, befolge folgende Schritte:

1. Installiere die erforderliche ARM-Toolchain
   - Debian Linux:
     - `sudo apt install gcc-arm-none-eabi libnewlib-arm-none-eabi`
   - Windows/macOS/Andere Linux:
     - https://developer.arm.com/downloads/-/gnu-rm
     - öffne die "Makefile" und editiere den Pfad in Zeile 1 `GCC_PATH` um auf den toolchain `bin` Ordner zu verweisen (z.B. `GCC_PATH = ../gcc-arm-none-eabi-10.3-2021.07/bin`)
2. führe folgenden Befehl aus: `make DMO_SKU={ausgewählte SKU zum kompilieren}` um die Firmware zu erstellen.

A `freedmo.bin` Datei wird im `build` Ordner erstellt.

### Installiere dir Firmware

1. Installiere [`STM32CubeProgrammer`](https://www.st.com/en/development-tools/stm32cubeprog.html) (falls du es noch nicht hast)
2. Setze den Bluepill in den Wartungsmodus [Boot Brücke von 0 auf 1 setzen auf der Platine](ASSEMBLY_PICTURES/dfu.jpg)
   - Nutzt du den ST-Link Adapter, ist das nicht zwingend benötigt. Aber es wird empfohlem um sicherzustellen dass keine Firmware während der Programmierung ausgeführt wird.
3. Verbinde den Bluepill mit einem Adapter mit dem Computer:
   - Einen ST-LINK V2 (SWD Download)
     1. Verbinde alle Pins mit der Gegenseite (3.3V zu 3.3V, etc.) zwischen Bluepill und ST-Link
        - Notiz: "SWIO" und "SWDIO" sind dieselben Pins
     2. Verbinde den ST-Link Adapter mit dem Computer
        - Wenn du es noch nicht gemacht hast, führe das Update für den ST-Link Adapter aus. 
   - UART Adapter (serial download)
     1. Vom UART Adapter zum Bluepill, [verbinde VCC zu 5V, GND zu GND, TX0 zu A10, und RX1 zu A9](https://www.electronicshub.org/wp-content/uploads/2020/02/Connections-for-Programming-STM32.jpg)
     2. Verbinde den UART-Adapter mit dem Computer
4. Starte `STM32CubeProgrammer`
5. In der oberen rechte Ecke wählst du deine Verbindungsmethode (`ST-LINK` oder `UART`)
   - Wird der ST-Link Adapter erkannt, [findest du dessen Seriennummer im dafür vorgesehenen Feld](ASSEMBLY_PICTURES/st-link-programming.png)
   - Wenn du einen UART-Adapter nutzt, [wähle auch den entsprechenden Port](https://www.electronicshub.org/wp-content/uploads/2020/02/STM32CubeProgrammer-Config-1.jpg)
6. Klicke `Connect` in der oberen rechten Ecke.
7. Wähle den [`Erasing & Programming` Tab auf der linken Seite](https://www.electronicshub.org/wp-content/uploads/2020/02/STM32CubeProgrammer-Config-2.jpg) on the left side
8. Im `File path`-Feld, klicke `Browse` und wähle die entsprechende Firmware.
   - Tipp: Stelle sicher, dass `Start address` folgendes eingetragen ist: `0x08000000`
9. Klicke `Start Programming`
10. Wenn die Firmware auf den Bluepill geschrieben ist, klicke `Disconnect` in der oberen rechte Ecke und trenne den Bluepill vom Computer um vom Adapter.
11. Stelle den Bluepill wieder zurück aus dem Wartungsmodus: [Boot Brücke von 1 wieder auf 0](ASSEMBLY_PICTURES/dfu.jpg)

Frohes drucken... 😈


## Verwendung

Der Bluepill emuliert den RFID-Tag einer D.MO-Etikettenrolle, wenn noch kein RFID-Tag erkannt wurde, und zählt die verbleibenden Etiketten herunter. Um die Anzahl der verbleibenden Etiketten zurückzusetzen, schalte den Drucker einfach aus und wieder ein.

## Fehlerbehebung

### Ich kann den Bluepill nicht mit dem Micro-USB-Anschluss programmieren. Was kann ich tun?

Leider kannst du den Bluepill nicht mit dem Micro-USB-Anschluss programmieren. Du musst einen ST-LINK v2 oder einen UART-Adapter verwenden.

### Ich habe meinen Drucker zusammengebaut, aber er druckt nicht und zeigt „unbekannte Etiketten geladen“ an.

Überprüfe die Verbindungen zwischen dem Bluepill und der Hauptplatine noch einmal. Möglicherweise liegt eine lose Verbindung vor, die die Kommunikation zwischen dem Bluepill und der Hauptplatine verhindert. Überprüfe außerdem, ob sich zwischen den Pins Lötbrücken befinden.

### Ich kann keine echten RFID-Tags lesen, ich kann sie nur emulieren.

Überprüfe die Verbindungen zwischen dem Bluepill und der RFID-Platine noch einmal. Stelle außerdem sicher, dass die beiden Widerstände richtig verlötet sind und sich die Pins nicht berühren.

Hinweis: du solltest auch noch einmal überprüfen, ob du einen orginalen [STM32F103 Bluepill](https://github.com/keirf/greaseweazle/wiki/STM32-Fakes) hast.

## Technische Informationen

### D.MO RFID-Tag-Emulation

Nach dem Start des Druckers wird eine Standard-Tag-Emulation verwendet, die in der Firmware definiert werden kann.
Die Emulation zählt korrekt herunter, bis das Ende der Rolle erreicht ist. Ein Aus- und Wiedereinschalten bzw. Ruhe- und Wiederaufwachen des Druckers reicht aus, um den emulierten Tag-Zähler auf sein Maximum zurückzusetzen.

Wenn auch die ursprüngliche RFID-Platine angeschlossen ist und eine Spule mit D.MO RFID-Tag gefunden wird, werden die Emulationsdaten mit den Daten dieses RFID-Tags aktualisiert. In diesem Fall wird nur der Zähler emuliert. Das Etikett, das beim Einschalten erkannt und emuliert wird, ist das einzige Etikett, das im Drucker erkannt wird, bis der Drucker erneut eingeschaltet wird. Dies ermöglicht die Verwendung jedes D.MO-Formats, sofern du nicht mindestens eine Originalspule hast (du kannst das RFID-Etikett dieser Rolle abziehen und an der Innenseite des Druckers anbringen). Wenn du Originaletiketten (mit RFID-Etikett) wechselst, musst du den Drucker aus- und wieder einschalten, damit das neue Etikett erkannt wird.

**Bitte ziehe in Erwägung, diese Daten zu dumpen und sie hier für andere bereitzustellen** (siehe: https://github.com/free-dmo/free-dmo-tag-dump).

D.MO verwendet seine eigene Originalitätssignatur (eigener Signaturschlüssel), mit der die UID des Etiketts signiert wird.

Dies wird verwendet, um nur D.MOs eigene SLIX2-Etiketten zuzulassen. Wenn du jedoch die UID emulieren kannst, kannst du auch die entsprechenden Signaturbytes emulieren, du musst sie nur von einem gültigen Etikett dumpen.<br/>(siehe: https://github.com/free-dmo/free-dmo-tag-dump).

Die Firmware enthält einige D.MO SLIX2-Tag-Dumps, aus denen du wählen kannst: <br/>
Datei: `Src/main.c`

```C
#define SLIX2_TAG_EMU 1
//#define SLIX2_TAG_EMU 2
//#define SLIX2_TAG_EMU 3
```

Es spielt keine Rolle, welches TAG_EMU du verwendest. Alle funktionieren im Moment. Vielleicht... in Zukunft wird D.MO neue Drucker/Firmware herausbringen, die diese UIDs blockieren könnten... aber dann müssen wir nur eine neue Spule Etiketten dumpen, um eine neue und gültige UID+Signatur zu erhalten.

Die Daten zu den Etiketten (SKU, Größe, Anzahl, ...) sind in den Standard-SLIX2-Datenblöcken kodiert.
**Innerhalb dieser Daten besteht keine Abhängigkeit zur UID oder Signatur**. Dies ermöglicht ein „Mix and Match“ des SLIX2-Tags (UID+Signatur) und der vom Drucker verwendeten Mediendaten. Leider verwenden die codierten Daten einen unbekannten CRC32-Algorithmus, der uns darauf beschränkt, nur vorhandene gedumpte Etikettenformate zu verwenden.

Du kannst aus der Liste der enthaltenen Etikettendaten auswählen, indem du eine SKU wählst: <br/>
Datei: `Src/main.c`

```C
#define DMO_SKU_S0722430 // 54 x 101 mm, 220 Stk.
//#define DMO_SKU_S0722550 // 19 x 51 mm, 500 Stk.
//#define DMO_SKU_S0722400 // 36 x 89 mm, 50 Stk.
```

### STM32CubeMx

Das Projekt wird mit der CubeMX .ioc-Datei geliefert, die zum Ändern von Pins und/oder zum Wechseln zu anderen CPU-Typen verwendet werden kann. Der komplette Code befindet sich innerhalb der magischen `BEGIN_USER_CODE` / `END_USER_CODE`-Markierungen von ST, sodass "Code generieren" in CubeMX problemlos verwendet werden kann.

#### STM32F103-Pinbelegung

![CUBEMX](ASSEMBLY_PICTURES/pin-assignment.png)
