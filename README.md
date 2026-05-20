
Claude hat die Antwort abgeschlossen
In Timberborn gibt es Automatisierungsoptionen. Eine Option ist ein http relais oder so. Wie kann ich das im Spiel verwenden? Was muss ich dafür außerhalb des Spiels einrichten? Kann ich damit z.B. jemandem über eine Website oder kleine Handy Anwendung die Möglichkeit geben irgendwie mit meinem Spiel zu interagieren, wenn ich spiele?

19. Mai

Überlege ich, wie HTTP-Relais in Timberborn funktionieren und externe Spielinteraktion ermöglichen
Überlege ich, wie HTTP-Relais in Timberborn funktionieren und externe Spielinteraktion ermöglichen
Das ist ein richtig cooles Feature! Hier ist eine vollständige Erklärung:

Die zwei HTTP-Komponenten in Timberborn
Es gibt zwei Gebäude, die zusammen ein vollständiges bidirektionales System bilden:

HTTP Lever (Input — von außen ins Spiel) Im Gegensatz zu einem normalen Hebel akzeptiert der HTTP Lever Ein/Aus-Befehle von externen Programmen, Skripten oder Geräten über HTTP-Requests an den lokalen API-Server des Spiels, der auf localhost:8080 läuft. Kostet 5.000 Wissenschaftspunkte. 
The Timberborn Wiki

HTTP Adapter (Output — aus dem Spiel nach außen) Der HTTP Adapter empfängt Signale von beliebigen Automationsquellen (Sensoren, Zähler, Logikgatter, Relays) und übersetzt diese Spielereignisse in echte Aktionen über HTTP — z. B. eine Verseuchungswarnung als Handybenachrichtigung, eine Nahrungsknappheit als Discord-Nachricht, oder ein Dürresignal als Licht in einem Smart-Home-System. Kostet 7.500 Wissenschaftspunkte. 
The Timberborn Wiki

Wie das im Spiel funktioniert
Die API ist case-sensitive. Alle Teile der URL müssen kleingeschrieben sein — also api, nicht API. Das gilt auch für die Namen deiner HTTP Lever und Adapter. „Lever1", „LEVER1" und „lever1" sind unterschiedliche Namen. Am einfachsten verwendest du nur Buchstaben und Ziffern für Namen. 
Timberborn Wiki

Die wichtigsten API-Endpunkte:

Aktion	URL
Lever einschalten	http://localhost:8080/api/switch-on/<lever-name>
Lever ausschalten	http://localhost:8080/api/switch-off/<lever-name>
Alle Lever abrufen	http://localhost:8080/api/levers
Adapter-Status abrufen	http://localhost:8080/api/adapters/<adapter-name>
Das kannst du sogar einfach im Browser testen — einfach die URL eintippen und du siehst den JSON-Status.

Dein Ziel: Andere interagieren lassen
Ja, das ist grundsätzlich möglich! Das Problem ist, dass die API aus Sicherheitsgründen nur an localhost gebunden ist. Wenn du sie über das Netzwerk oder das Internet erreichbar machen willst, brauchst du einen Proxy-Dienst wie Ngrok, Localtunnel, Cloudflare Tunnel oder Pinggy. 
Timberborn

Option A: Tunnel-Dienst (einfachste Methode)
Mit Cloudflare Tunnel (kostenlos) oder Ngrok leitest du localhost:8080 nach außen weiter. Ablauf:

Cloudflare Tunnel installieren und einrichten
Den Tunnel auf localhost:8080 richten → du bekommst eine öffentliche URL wie https://dein-name.trycloudflare.com
Eine kleine Webseite bauen, die Buttons anzeigt und beim Klick einen Fetch-Request an diese URL schickt
Den Link teilen — fertig
Option B: Mod für lokales Netzwerk
Es gibt den Mod „Remote API Access" auf mod.io, der die eingebaute HTTP API so erweitert, dass sie Verbindungen aus dem lokalen Netzwerk akzeptiert (nicht nur localhost) — inklusive CORS-Header und mDNS-Erkennung. Praktisch z. B. wenn du vom Handy im selben WLAN steuern willst, ohne Tunnel. 
mod.io

Option C: Python-Bibliothek als Backend
Für etwas ausgefeiltere Lösungen gibt es sogar eine fertige Python-Bibliothek:

python
from timberborn_http import TimberbornAPI

api = TimberbornAPI("http://localhost:8080")
api.switch_on("mein-lever")  # Lever einschalten
Damit kannst du z. B. ein kleines Flask-/FastAPI-Webserver-Backend bauen, das als Mittelsmann dient.

Konkrete Ideen für dein Szenario
Zuschauer/Freunde stimmen ab: Eine Webseite mit zwei Buttons „Fluttor öffnen" / „Fluttor schließen" — jeder Klick triggert den entsprechenden Lever
Dürre-Alarm: HTTP Adapter an Wetterstation anschließen → sendet Webhook an einen Server → pusht eine Handybenachrichtigung
Lever an einen Feuerwerk-Launcher anschließen und externe Ereignisse damit feiern — z. B. Nutzeraktionen in einem Livestream 
Timberborn Wiki
Das Grundprinzip ist also: Spiel → localhost:8080 → Tunnel → öffentliche URL → deine Webseite/App. Der technische Aufwand ist überschaubar, wenn man etwas mit Webentwicklung vertraut ist.

Was muss ich tun, wenn ich folgendes machen will. Eine ganz simple Website für Freunde. 

Darauf soll immer das Wetter angezeigt werden. Also 3 Boxen oder so für Gemäßigtes Wetter, Dürre und Faulwasser und die entsprechende soll immer leuchten oder hervorgehoben sein. Ich habe je einen Messer für alle Zustände also kann ich jede Box an den entsprechenden Messer binden.
Außerdem will ich, dass man über einen Button ein Feuerwerk starten kann. Das mache ich erst mal über einen simplen Hebel
16:36

Architektierte interaktive Website mit Wetter-Anzeige und Feuerwerk-Button
Architektierte interaktive Website mit Wetter-Anzeige und Feuerwerk-Button
Ich baue dir das direkt als fertige HTML-Datei. Du brauchst nur eine einzige Datei, die du deinen Freunden schicken oder irgendwo hosten kannst.


Datei erstellt, datei lesen
Datei erstellt, datei lesen
Hier ist dein Dashboard! Jetzt zur Einrichtung:

Was du im Spiel brauchst
3× HTTP Adapter bauen und benennen (z. B. gemässigt, dürre, faulwasser) — jeden an den entsprechenden Wettersensor anschließen
1× HTTP Lever bauen und benennen (z. B. feuerwerk) — an den Feuerwerk-Launcher anschließen
Die Namen musst du 1:1 in den Dashboard-Einstellungen eintragen. Groß-/Kleinschreibung zählt!

Damit Freunde drauf zugreifen können
Das Spiel lauscht nur auf localhost — du brauchst zwei Dinge:

Mod installieren: Remote API Access auf mod.io — der aktiviert CORS-Support, damit externe Seiten die API aufrufen dürfen.

Tunnel einrichten (einmalig, kostenlos):

bash
# Cloudflare Tunnel (empfohlen)
cloudflared tunnel --url http://localhost:8080
# → gibt dir eine URL wie https://xyz.trycloudflare.com
Diese URL trägst du im Dashboard unter Einstellungen → API-Basis-URL ein. Dann die HTML-Datei einfach per Link schicken oder z. B. auf GitHub Pages hosten.

Was das Dashboard kann
Wetterstatus wird alle 3 Sekunden automatisch abgefragt — die aktive Karte leuchtet auf
Feuerwerk-Button mit 5-Sekunden-Cooldown (der Lever wird nach 2,5 s automatisch wieder ausgeschaltet)
Einstellungen direkt auf der Seite — kein Code bearbeiten nötig
Alle Einstellungen werden im Browser gespeichert (localStorage)

Timberborn dashboard
Code · HTML 
1779289314844_image.png
Bevor ich in die Details gehe und die Sachen in Timberborn korrekt aufsetze, will ich das grob testen bzw. die Struktur bauen.

Ich habe einen github Account und dort bereits ein Repository wo ich eine andere kleine Website gebaut habe und die über github pages gehostet wird.
Was sind die konkreten Schritte um jetzt einfach mal meine leere Test Seite zu bauen. Ich kenne mich mit Github nicht aus. So sieht meine Home Page dort aus

17:01

Orchestrierte konkrete Schritte für GitHub Pages Hosting-Setup
Orchestrierte konkrete Schritte für GitHub Pages Hosting-Setup
Ich sehe dein GitHub-Dashboard. Hier sind die konkreten Schritte — alles direkt im Browser, kein Code-Wissen nötig:

Schritt 1 — Neues Repository erstellen
Klick oben links auf den grünen „New"-Button.

Füll das Formular so aus:

Repository name: timberborn-dashboard (oder was du willst)
Public auswählen (wichtig für GitHub Pages!)
Haken bei „Add a README file" setzen
Dann „Create repository" klicken
Schritt 2 — HTML-Datei hochladen
Du bist jetzt in deinem neuen Repository. Klick auf „Add file" → „Upload files".

Zieh die timberborn-dashboard.html Datei rein (die du gerade heruntergeladen hast) — aber benenne sie vorher in index.html um, das ist wichtig! Dann unten auf „Commit changes" klicken.

Schritt 3 — GitHub Pages aktivieren
Geh in deinem Repository auf Settings (oben in der Tab-Leiste) → links im Menü auf Pages.

Unter „Branch" wähle main aus dem Dropdown, dann Save klicken.

Nach 1–2 Minuten erscheint dort eine URL nach dem Muster:

https://flower1457.github.io/timberborn-dashboard/
Das ist die Adresse, die du deinen Freunden schickst.

Was du dann siehst
Die Seite lädt, aber der Wetterstatus zeigt ? an — das ist normal, weil das Spiel noch nicht verbunden ist. Der Feuerwerk-Button gibt einen roten Rand, wenn du drückst. Das ist der erwartete Zustand für den Test ohne laufendes Spiel.

Wenn du magst, kannst du mir die URL schicken und ich schaue ob alles korrekt aufgesetzt ist.

https://flower1457.github.io/timberborn-dashboard/

17:06

Thinking about the Timberborn dashboard website and its content
Thinking about the Timberborn dashboard website and its content
Alles perfekt! 🎉 Die Seite läuft korrekt unter:

https://flower1457.github.io/timberborn-dashboard/

Der Inhalt ist vollständig da — alle drei Wetterkarten, der Feuerwerk-Button und das Einstellungsmenü sind geladen. Das Verbinde… und die — bei den Karten sind genau richtig so, weil das Spiel noch nicht verbunden ist.

Nächste Schritte wenn du Timberborn aufgesetzt hast
Im Spiel die HTTP Adapter und den HTTP Lever bauen und benennen
Mod installieren: Remote API Access
Cloudflare Tunnel starten: cloudflared tunnel --url http://localhost:8080 → du bekommst eine https://xyz.trycloudflare.com URL
Auf der Website unten auf ⚙ Einstellungen klicken und die Tunnel-URL + deine Adapter-/Hebel-Namen eintragen → Speichern & verbinden
Dann sollte es live funktionieren. Meld dich wenn du beim Spiel-Setup Fragen hast!

1779289759028_image.png
Bevor wir weitermachen, würde ich die Website gerne anpassen.

der Text unter Biberkolonie kann weg. Also den Text "LIVE-DASHBOARD - WETTERSTATUS & STEUERUNG" einfach entfernen
"STEUERUNG" über dem Feuerwerksbutton entfernen
Kann man die Einstellungen entfernen? Ich will nicht, dass jeder einfach da was ändern kann (siehe Screenshot)
17:09

Orchestrierte Entfernung von Untertitel, Sektion und Einstellungs-UI
Orchestrierte Entfernung von Untertitel, Sektion und Einstellungs-UI
Drei saubere Änderungen. Ich mache das direkt in der Datei:


4 Dateien bearbeitet, datei lesen
4 Dateien bearbeitet, datei lesen
Alle drei Änderungen sind drin. Die Konfiguration (API-URL, Adapter-Namen, Hebel-Name) steht jetzt direkt im Code — wenn du die Werte später anpassen willst, öffnest du einfach die HTML-Datei mit einem Texteditor und änderst die Zeilen ganz oben im Script-Block:

js
const cfg = {
  base:             'http://localhost:8080',   // ← Tunnel-URL hier
  adapterTemperate: 'gemässigt',               // ← dein In-Game-Name
  adapterDrought:   'dürre',
  adapterBadtide:   'faulwasser',
  leverFirework:    'feuerwerk',
};
Dann die Datei einfach wieder auf GitHub hochladen (gleicher Dateiname index.html, einfach ersetzen) — fertig.


Timberborn dashboard
Code · HTML 
Super, jetzt können wir das richtig verbinden. Gib mir eine Schritt-für-schritt Anleitung

17:13

Orchestrierte Verbindungsschritte zwischen Spiel, Tunnel und Website
Orchestrierte Verbindungsschritte zwischen Spiel, Tunnel und Website
Gerne! Das sind vier klar getrennte Teile:

Teil 1 — Mod installieren
Der Mod „Remote API Access" ist nötig damit die Website überhaupt mit dem Spiel reden darf (CORS).

Geh auf mod.io/g/timberborn/m/remote-api-access im Browser
Klick auf „Subscribe" — du brauchst einen kostenlosen mod.io Account
Starte Timberborn → im Hauptmenü auf „Mods" → der Mod sollte dort auftauchen und aktiv sein
Teil 2 — Im Spiel aufbauen
Starte deine Kolonie. Du brauchst drei Dinge:

3× HTTP Adapter (einer pro Wetter):

Bau einen HTTP Adapter, klick drauf → vergib den Namen gemässigt
Verbinde ihn mit deinem Wettersensor für normales Wetter
Wiederhole das für dürre (an den Dürre-Sensor) und faulwasser (an den Faulwasser-Sensor)
1× HTTP Lever:

Bau einen HTTP Lever, nenn ihn feuerwerk
Verbinde ihn mit dem Feuerwerk-Launcher
⚠️ Die Namen müssen exakt so geschrieben sein wie in der HTML-Datei — Groß-/Kleinschreibung zählt!

Teil 3 — Tunnel einrichten
Das Spiel läuft nur auf deinem PC — der Tunnel macht es von außen erreichbar.

Einmalig: cloudflared herunterladen

Geh auf github.com/cloudflare/cloudflared/releases/latest
Lade cloudflared-windows-amd64.exe herunter
Leg die Datei irgendwo ab wo du sie wiederfindest, z. B. C:\cloudflared\cloudflared.exe
Jeden Spieltag: Tunnel starten

Öffne die Eingabeaufforderung (Windows-Taste → cmd tippen → Enter)
Tippe:
   C:\cloudflared\cloudflared.exe tunnel --url http://localhost:8080
Warte 5–10 Sekunden — du siehst eine Zeile wie:
   Your quick Tunnel has been created! Visit it at:
   https://irgendwas-xyz-abc.trycloudflare.com
Diese URL kopieren — du brauchst sie gleich
ℹ️ Das Fenster muss während des Spielens offen bleiben. Die URL ändert sich bei jedem Neustart.

Teil 4 — HTML-Datei aktualisieren & hochladen
Öffne die index.html mit dem Editor (Rechtsklick → „Öffnen mit" → Editor/Notepad)
Suche diese Zeile (ganz unten im <script>-Block):
js
   base: 'http://localhost:8080',
Ersetze sie mit deiner Tunnel-URL:
js
   base: 'https://irgendwas-xyz-abc.trycloudflare.com',
Speichern
Geh auf github.com → dein Repository timberborn-dashboard → klick auf index.html → dann auf das Stift-Symbol (Edit) → oder zieh die Datei einfach per „Add file → Upload files" rein und überschreib die alte
Nach 1–2 Minuten ist die Seite live und deine Freunde können sie aufrufen.

Schnelltest
Wenn alles läuft, öffne kurz http://localhost:8080/api/levers im Browser während Timberborn läuft — du solltest eine JSON-Antwort mit deinem feuerwerk-Lever sehen. Dann weißt du, dass das Spiel korrekt antwortet.

Zu Teil 1: ich habe die Mods einfach direkt über Timberborn > Steam abonniert
Zu Teil 2: sind umlaute wie ü und ä okay bei der Benennung?

17:20
Teil 1 — Perfekt, das ist sogar der einfachere Weg. Mod ist aktiv sobald du das Spiel (neu) startest.

Teil 2 — Lieber nicht. Die API-URL muss korrekt enkodiert sein, und Umlaute können dabei zu schwer nachvollziehbaren Fehlern führen. Zum Beispiel wird ü in URLs zu %C3%BC — das funktioniert zwar technisch, aber nur wenn die Enkodierung auf beiden Seiten stimmt.

Einfacher und sicherer: nur normale Buchstaben und Zahlen verwenden.

Statt dürre → durre Statt gemässigt → gemaessigt oder einfach normal Statt faulwasser → bleibt faulwasser (kein Umlaut, passt)

Ich passe die HTML-Datei direkt an — sag mir kurz wie du die drei Adapter und den Hebel nennen willst, dann ändere ich die Standardwerte im Code.

Ich folge einer leicht anderen Benennung: httpGemaessigt, httpDurre und httpFaulwasser. Lass uns erst mal nur die Wetter sachen machen

17:23

Erfasste Anforderung zur Anpassung der Adapter-Benennungen
Erfasste Anforderung zur Anpassung der Adapter-Benennungen
Erledigt. Die drei Namen im Code sind jetzt httpGemaessigt, httpDurre und httpFaulwasser — genau so müssen sie auch im Spiel heißen.

Lad die Datei auf GitHub hoch und dann kannst du im Spiel die drei HTTP Adapter bauen und benennen. Sag Bescheid wenn du soweit bist, dann testen wir den Tunnel.


Timberborn dashboard
Code · HTML 
1779290763077_image.png
Die Adapter sind gebaut und an das jeweilige Wetter gebunden im Spiel. Was jetzt? Was muss ich da einstellen?

17:26

Analysierte API-Konfiguration und identifizierte notwendige Startschritte
Analysierte API-Konfiguration und identifizierte notwendige Startschritte
Gut, das sieht perfekt aus! Du musst dort eigentlich kaum etwas einstellen.

Was du bei jedem der 3 Adapter tun musst
1. Die beiden Checkboxen „Aufrufen, wenn eingeschaltet" und „Aufrufen, wenn ausgeschaltet" — lass sie deaktiviert. Die sind für Webhooks (Adapter ruft aktiv eine URL an), aber wir machen es andersherum: die Website fragt den Adapter ab. Das brauchen wir nicht.

2. Port bleibt 8080 — nichts ändern.

3. Klick auf „API starten" — der rote Button. Der Status unten sollte dann von „API gestoppt" auf „API läuft" oder ähnliches wechseln.

4. Wiederhole das für httpGemaessigt und httpFaulwasser.

Schnelltest danach
Öffne in deinem Browser (während Timberborn läuft):

http://localhost:8080/api/adapters/httpDurre
Du solltest eine Antwort wie diese sehen:

json
{ "name": "httpDurre", "state": false }
Wenn das klappt, funktioniert die API. Dann können wir den Tunnel einrichten.

Es ist gerade Dürre und meine Antwort zu state ist true. Das heißt es klappt, oder?

17:27
Ja, genau! state: true bei aktiver Dürre ist exakt das erwartete Verhalten. Die API funktioniert perfekt.

Weiter zu Teil 3 — Tunnel einrichten. Hast du die cloudflared.exe schon heruntergeladen?

nein

17:28
Dann los:

Geh auf github.com/cloudflare/cloudflared/releases/latest
Scroll runter zu „Assets"
Klick auf cloudflared-windows-amd64.exe zum Herunterladen
Leg die Datei in einen einfachen Ordner, z. B. C:\cloudflared\
Sag Bescheid wenn sie heruntergeladen ist.

Ist heruntergeladen

17:29
Gut. Jetzt den Tunnel starten:

Öffne die Eingabeaufforderung — Windows-Taste, cmd tippen, Enter
Tippe exakt das hier und drück Enter:
C:\cloudflared\cloudflared-windows-amd64.exe tunnel --url http://localhost:8080
Warte 10–15 Sekunden. Du siehst dann irgendwo eine Zeile mit einer URL die so aussieht:

https://irgendwas-xyz-abc.trycloudflare.com
Kopiere diese URL und schick sie mir.

Die Datei liegt aber hier: D:\Documents\05_Hobby\Timberborn

17:30
Dann entsprechend anpassen:

D:\Documents\05_Hobby\Timberborn\cloudflared-windows-amd64.exe tunnel --url http://localhost:8080
In die Eingabeaufforderung einfügen und Enter drücken.

https://protection-mrs-recreational-ward.trycloudflare.com

17:31
Perfekt! Jetzt die HTML-Datei aktualisieren:


Hat eine Datei bearbeitet, datei lesen
Hat eine Datei bearbeitet, datei lesen
Datei ist aktualisiert. Jetzt auf GitHub hochladen:

Geh auf github.com → timberborn-dashboard
Klick auf index.html → dann das Stift-Symbol oben rechts
Klick auf „Upload files" — oder einfacher: direkt auf den Stift, dann den gesamten Inhalt der neuen Datei reinkopieren
Unten auf „Commit changes" klicken
Danach kurz 1–2 Minuten warten und dann https://flower1457.github.io/timberborn-dashboard/ aufrufen. Die Dürre-Karte sollte leuchten!


Timberborn dashboard
Code · HTML 
Was ist das mit dem Tunnel? Muss ich die Eingabeaufforderung jetzt immer offen halten?

17:33
Ja, genau — das Fenster muss offen bleiben solange du spielst und die Website aktiv sein soll. Sobald du es schließt, ist der Tunnel weg.

Außerdem ändert sich die URL bei jedem Neustart des Tunnels. Das heißt jedes Mal eine neue URL → HTML-Datei aktualisieren → auf GitHub hochladen. Das ist auf Dauer lästig.

Es gibt zwei Wege das zu lösen:

Option A — Einfach damit leben (für gelegentliches Spielen mit Freunden) Kurz vor dem Spielen Tunnel starten, URL in die Datei eintragen, hochladen, fertig. Dauert 2 Minuten wenn man den Ablauf kennt.

Option B — Feste URL (einmalig mehr Aufwand, dann nie wieder) Mit einem kostenlosen Cloudflare-Account und einer eigenen Domain bekommst du eine permanente URL wie timberborn.deinname.de — die bleibt immer gleich, egal wie oft du den Tunnel neu startest. Kostet nichts, aber du brauchst eine Domain (ab ~10€/Jahr).

Für den Anfang würde ich Option A empfehlen — erst mal testen ob das Konzept Spaß macht, bevor du mehr Aufwand reinsteckst. Was meinst du?

Kann ich in die html Datei kommentieren? Um den Aufwand zu verringern würde ich gerne einfügen in welcher Zeile die url ersetzt werden muss. Aktuell ist es 436. Wenn wir ganz oben eine Zeile hinzufügen dann 437

17:36

Analysierte HTML-Datei zur Kommentierung der URL-Zeile
Analysierte HTML-Datei zur Kommentierung der URL-Zeile
Erledigt. Im JavaScript-Code macht man Kommentare mit // — alles dahinter in der gleichen Zeile wird ignoriert. Der Kommentar steht jetzt direkt über der URL, du kannst ihn auch nach Belieben anpassen.


Timberborn dashboard
Code · HTML 
Ne ich will das einfach ganz oben am Anfang der Datei reinschreiben: // change tunnel url in line 437 'https://xxxxxx.trycloudflare.com'

17:38

Debattierte HTML-Kommentar-Syntax und Zeilennummern-Verschiebung
Debattierte HTML-Kommentar-Syntax und Zeilennummern-Verschiebung
Im HTML-Bereich oben muss der Kommentar mit <!-- und --> umschlossen sein, sonst bricht die Seite. Ich schreib es trotzdem so ähnlich wie du es wolltest:


2 Dateien bearbeitet, datei lesen
2 Dateien bearbeitet, datei lesen
Steht jetzt ganz oben in Zeile 1. Im HTML-Bereich funktioniert // leider nicht — <!-- ... --> ist die HTML-Variante des Kommentars, hat aber exakt denselben Effekt.


Timberborn dashboard
Code · HTML 
Jetzt will ich noch anfangen die REadme zu schreiben. Ich will die cloudflare Anleitung da rein machen. In Englisch bitte. Also grob so:

Open Eingabeaufforderung (Win > cmd)
Type xxx and click enter
copy the url iinto the index.html file
17:41

Konzipierte englischsprachige README mit Cloudflare-Anleitung
Konzipierte englischsprachige README mit Cloudflare-Anleitung
Das wird eine README.md Datei für GitHub — die wird dort automatisch schön formatiert angezeigt.


Datei erstellt, datei lesen
Datei erstellt, datei lesen
Da ist die README. Auf GitHub einfach die bestehende README.md damit ersetzen — genau wie mit der index.html, über den Stift oder „Upload files".

Wenn du später noch mehr Abschnitte hinzufügen willst (z. B. wie die In-Game-Adapter heißen, oder was der Feuerwerk-Button macht), einfach sagen.


Readme
Dokument · MD 




Claude ist eine KI und kann Fehler machen. Bitte überprüfe die Antworten.
Readme · MD
# Timberborn Dashboard
 
A simple live dashboard for friends to watch the current weather state of my Timberborn colony and trigger fireworks.
 
## Starting the Tunnel (every session)
 
The game's API only runs locally, so a tunnel is needed to make it accessible from outside.
 
1. Open Command Prompt — press the Windows key, type `cmd`, press Enter
2. Run the following command:
   ```
   D:\Documents\05_Hobby\Timberborn\cloudflared-windows-amd64.exe tunnel --url http://localhost:8080
   ```
3. Wait ~10 seconds until a URL appears, e.g.:
   ```
   https://something-xyz-abc.trycloudflare.com
   ```
4. Copy that URL and paste it into `index.html` line 438:
   ```js
   base: 'https://something-xyz-abc.trycloudflare.com',
   ```
5. Upload `index.html` to this repository — the site updates after ~2 minutes
> The Command Prompt window must stay open while playing. The URL changes every time the tunnel is restarted.
