```package
iot-cube=github:Smartfeld/pxt-iot-cube#v1.1.2
sensors=github:Smartfeld/pxt-sensorikAktorikSmartfeld
```
### @explicitHints false

# IoT Tutorial Teil 2


## 📗 Einführung, Teil 2

Vorraussetzungen: 🌱 IoT Basics abgeschlossen und IoT Tutorial [Teil 1 - noch ohne Internetverbindung](https://makecode.microbit.org/#tutorial:github:reifab/pxt-iot-tutorial/docs/tutorials/seifenspender-part-1) abgeschlossen.
Schwierigkeitsgrad: 🔥🔥⚪⚪

Aus dem Tutorial Teil 1 hast du bereits ein Programm, das den Seifenstand simuliert. 
Nun wollen wir über LoRa🛜 den Seifenstand ins Internet senden. Am Ende hast du ein
funktionsfähiges Programm, das:

* Eine LoRa-Verbindung🛜 aufbaut. 
* Den Seifenstand🧼  über LoRa🛜 sendet. 
* Eine Ladebalken-Animation⏳ für Wartezeiten darstellt.

## 👁️ Vorraussetzungen @showdialog
* Du benötigst einen IoT Cube dessen OLED Display 🖥️ an J5 angeschlossen ist.
* Schliesse den Cube so an, falls noch nicht gemacht:
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-anschliessen-klein.png)
* Stelle die Schalter vorerst so ein:
    * Battery Switch: **off**
    * LoRa Module: **on**
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/iot-cube-power-switches-klein.png)

* Ein LoRa- Gateway🛜 muss in Reichweite sein, welches mit TTN (The Things Network) verbunden ist.
Dies ist im Klassensatz einmal vorhanden und kann hunderte von IoT- Cubes bedienen.
![Bild](https://reifab.github.io/pxt-iot-tutorial/static/tutorials/gateway-klein.png)

## 🖥️ Statusmeldung anzeigen auf OLED  
Auf dem kleinen Display auf dem IoT Cube wollen wir den Text "Verbinde" anzeigen. 

* Hol dir einen Block 🖥️ ``||SmartfeldAktoren:init OLED Breite 128 Höhe 64||`` .
und ergänze ihn unten im Block ``||basic:beim Start||``. 
* Darunter setzt du den Block 🖥️ ``||SmartfeldAktoren:Lösche Displayinhalt||`` .
ein.
Damit löschst du bestehende Inhalte auf dem Display.
* Jetzt verwendest du den Block ``||SmartfeldAktoren:schreibe String||``.
Damit schreibst du den Text "Verbinde" auf das Display.
* Drücke 📥`|Download|` und kontrolliere die Anzeige am OLED Display 🖥️.

Wird dir "Verbinde" angezeigt?

```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
// @highlight
smartfeldAktoren.oledInit(128, 64)
// @highlight
smartfeldAktoren.oledClear()
// @highlight
smartfeldAktoren.oledWriteStr("Verbinde")
```

## 🛜 Verbindung mit Internet aufbauen
Nun bauen wir eine Verbindung zum Internet auf.
Auf dem OLED- Display🖥️ wollen wir den Verbindungsaufbau mit **...** anzeigen.
* Ziehe den Block 🛜``||IoTCube:LoRa Netzwerk-Verbindung||`` zuunterst in 
``||basic:beim Start||`` hinein.
* Ziehe darunter den Block ``||loops:während falsch mache||`` hinein. Weil
das Verbinden je nach Umständen 5 is 30 Sekunden dauert, wollen wir in dieser
Schleife verbleiben, solange die Verbindung noch **nicht** besteht.  
* Ziehe dazu den Block ``||Logic:nicht||`` auf die Schleife,
um den Wahrheitswert zu negieren.
* Füge in den **nicht** Block nun 🛜``||IoTCube:Lese Gerätestatus-Bit||`` ein.
Ändere darin das Bit auf "Verbunden". Der Code in der Schleife lautet nun "während nicht Lese gerätestatus-Bit verbunden". Programmierer/innen lesen den Code so: 
"Während das Gerät nicht verbunden ist." 
* Warte in der Schleife 1 Sekunde (1000 ms). Nutze ``||basic:pausiere (ms)||``.
* Schreibe mit dem Block 
🖥️ ``||SmartfeldAktoren:schreibe String||`` bei jedem Schleifendurchlauf ein "."
auf das Display.

```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
// @highlight
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
// @highlight
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    smartfeldAktoren.oledWriteStr(".")
    basic.pause(1000)
}
```

## 🛜 Status Verbunden anzeigen
Die Schleife wird beendet, wenn die Verbindung besteht, d.h. wir können nach der Schleife "Verbunden" am Display anzeigen:

* Unter der Schleife löschst Du den Displayinhalt mittels 🖥️``||SmartfeldAktoren:Lösche Displayinhalt||``.
* Im Anschluss schreibst du ein "Verbunden!" auf das OLED- Display🖥️.
* Drücke 📥`|Download|` und kontrolliere die Anzeige am OLED- Display🖥️.

Wird dir zuvor "Verbinde" und im Anschluss "Verbunden" angezeigt?
Werden dir die zunehmend mehr Punkte angezeigt, während die Verbindung aufgebaut wird?

```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
// @highlight
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    smartfeldAktoren.oledWriteStr(".")
    basic.pause(1000)
}
// @highlight
smartfeldAktoren.oledClear()
// @highlight
smartfeldAktoren.oledWriteStr("Verbunden!")
```

## 🧼 Seifenstand senden beim Start

Zu Beginn ist der Seifenstand 100 %.
Diesen wollen wir nach dem Verbindungsaufbau senden.

* ``||basic:pausiere (ms)||`` für 2 Sekunden (=2000 ms), nachdem der Text "Vebunden!" ausgegeben wurde,
damit dieser Text mindestens für diese Zeit auf dem Display steht.
* Lösche danach den Text mit ``||SmartfeldAktoren:Lösche Displayinhalt||`` 
um Energie zu sparen.
* Darunter setzt du den Block ``||IoTCube:Ganzzahl mit ID_0 = 0 hinzufügen||`` ein.
* Die 0 ersetzt du nun mit der Variable ``||variables:seifenstandInProzent||``.
* Schicke nun den Seifendstand mit dem Befehl ``||IoTCube:Sende Daten||`` in die ☁️ Cloud!
* Drücke 📥`|Download|`.

```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    smartfeldAktoren.oledWriteStr(".")
    basic.pause(1000)
}
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbunden!")
// @highlight
basic.pause(2000)
// @highlight
smartfeldAktoren.oledClear()
// @highlight
basic.clearScreen()
// @highlight
IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
// @highlight
IoTCube.SendBufferSimple()
```

## ☁️ Dashboard erstellen auf Clavis Cloud 

Nun geht es an die Visualisierung der Daten auf der  Clavis Cloud ☁️.
* Rufe die Website [🌍iot.claviscloud.ch](https://iot.claviscloud.ch/home) auf.
* Melde dich an (Login- Informationen kriegst Du von der Lehrperson/ Kursleitung).
* Schau dir dieses [📹 Video](https://wiki.smartfeld.ch/lib/exe/fetch.php?media=dashboard_erstellen_seifenspender.mp4) an. Es beinhaltet folgende Schritte. 
Führe diese selbst aus und erstelle somit ein Dashboard.
 * Erstelle ein neues Dashboard in euerer Klassengruppe 
 * Erstelle ein Widget für die Anzeige des Seifenstandes🧼

## 🧼 Seifenstand bei Änderung senden

Immer wenn sich der Seifenstand ändert, dann soll der aktuelle Stand an die Cloud geschickt werden. Führe die nötigen Schritte durch:

* Wenn Knopf A geklickt ist, schickst Du 
  den aktuellen Seifenstand mithilfe der Blöcke ``||IoTCube:Ganzzahl mit ID_0 = 0 hinzufügen||``, 
  und ``||IoTCube:Sende Daten||`` ins Internet. Falls du unsicher bist, klicke
  auf die 💡 Glühbirne links unten.
* Dasselbe machst du, wenn Knopf B geklickt wurde. Falls du unsicher bist, klicke
  auf die 💡 Glühbirne links unten.
* Vergiss nicht die Variable zu senden (nicht einfach eine 0).
* Hinweis: Grundsätzlich könntest du dein Programm jetzt schon wieder testen, aber bearbeite besser die nächsten beiden Schritte um Nebeneffekte zu vermeiden.

```blocks
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        if (seifenstandInProzent < 0) {
            seifenstandInProzent = 0
        }
        // @highlight
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        // @highlight
        IoTCube.SendBufferSimple()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        // @highlight
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        // @highlight
        IoTCube.SendBufferSimple()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.pause(100)
})
```

## ⏱️ Funktion für Wartezeit erstellen

Nach dem Versenden von Daten über LoRa ist eine Wartezeit von mindestens 
5 Sekunden erforderlich, bevor erneut Daten übertragen werden können. 
Hintergrund: Während dieser 5 Sekunden steht ein Empfangsfenster zur Verfügung, 
über das Daten von der Cloud zum Cube übertragen werden können.

Baue dir mit folgenden Blöcken die Wartefunktion nach, welche im Tootip
(die 💡 Glühbirne links unten) angezeigt wird.

* ``||function:Erstelle eine Funktion||`` (im Bereich Fortgeschritten zu finden)
    * Benenne die Funktion mit "warte_5_Sekunden_mit_Anzeige" und klicke auf **Fertig**.
    * Lösche darin den Displayinhalt mit ``||SmartfeldAktoren:Lösche Displayinhalt||`` 
    * ``||loops:für Index von 0 bis 4||`` 
        * klicke auf Index --> klicke auf **Neue Variable**, Neuer Variablenname: **fortschritt**
        * ersetze die 4 mit 100.
    * ``||SmartfeldAktoren:zeichne Ladebalken bei 0 Prozent||`` 
        * ersetze die 0 mit der Variable **fortschritt**
    * Warte in der Schleife mit ``||basic:pausiere (ms)||`` jeweils für 50 ms
     (100x 50ms = 5 Sekunden)
    * Lösche den Displayinhalt erneut nach der Schleife.

```blocks
// @highlight
function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
```

## ⏱️ Wartezeit einbauen

* Nach jedem Befehl ``||IoTCube:Sende Daten||`` fügst Du die Funktion aus dem Bereich Fortgeschritten ``||function:warte_5_Sekunden_mit_Anzeige||`` ein.
* 📥 Drücke `|Download|` und kontrolliere ob...
    * der Ladebalken am OLED-Display bei Tastendruck angezeigt wird
    * die Daten auf deinem Dashboard angezeigt werden: [iot.claviscloud.ch](https://iot.claviscloud.ch/dashboards/)

```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    smartfeldAktoren.oledWriteStr(".")
    basic.pause(1000)
}
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbunden!")
basic.pause(2000)
smartfeldAktoren.oledClear()
basic.clearScreen()
IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
IoTCube.SendBufferSimple()
// @highlight
warte_5_Sekunden_mit_Anzeige ()

basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        if (seifenstandInProzent < 0) {
            seifenstandInProzent = 0
        }
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        // @highlight
        warte_5_Sekunden_mit_Anzeige ()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
       
        IoTCube.SendBufferSimple()
        // @highlight
        warte_5_Sekunden_mit_Anzeige ()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.pause(100)
})


function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}
```

## 🪫 Energie sparen

* Um Energie zu sparen kannst du die Anzeige des Seifenstandes🧼 in der Dauerhaftschleife nach jedem Durchlauf löschen.
Nutze dazu ``||basic:Bildschirminhalt löschen||``.
* 📥 Drücke `|Download|` und teste Dein fertiges Programm!

```blocks
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
smartfeldAktoren.oledInit(128, 64)
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbinde")
IoTCube.LoRa_Join(
eBool.enable,
eBool.enable,
10,
8
)
while (!(IoTCube.getStatus(eSTATUS_MASK.JOINED))) {
    smartfeldAktoren.oledWriteStr(".")
    basic.pause(1000)
}
smartfeldAktoren.oledClear()
smartfeldAktoren.oledWriteStr("Verbunden!")
basic.pause(2000)
smartfeldAktoren.oledClear()
basic.clearScreen()
IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
IoTCube.SendBufferSimple()
warte_5_Sekunden_mit_Anzeige ()

basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        if (seifenstandInProzent < 0) {
            seifenstandInProzent = 0
        }
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
        IoTCube.SendBufferSimple()
        warte_5_Sekunden_mit_Anzeige ()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        IoTCube.addUnsignedInteger(eIDs.ID_0, seifenstandInProzent)
       
        IoTCube.SendBufferSimple()
        warte_5_Sekunden_mit_Anzeige ()
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.clearScreen()
    basic.pause(100)
})


function warte_5_Sekunden_mit_Anzeige () {
    smartfeldAktoren.oledClear()
    for (let fortschritt = 0; fortschritt <= 100; fortschritt++) {
        smartfeldAktoren.oledLoadingBar(fortschritt)
        basic.pause(50)
    }
    smartfeldAktoren.oledClear()
}

```


```template
let seifenstandInProzent = 100
led.plotBarGraph(
seifenstandInProzent,
100
)
basic.forever(function () {
    if (input.buttonIsPressed(Button.A)) {
        seifenstandInProzent = seifenstandInProzent - 20
        if (seifenstandInProzent < 0) {
            seifenstandInProzent = 0
        }
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    if (input.buttonIsPressed(Button.B)) {
        seifenstandInProzent = 100
        led.plotBarGraph(
        seifenstandInProzent,
        100
        )
    }
    basic.pause(100)
})
```
