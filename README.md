# Wessen Reel?

Partyspiel für 2–8 Leute: Jeder legt heimlich Instagram- oder TikTok-Links rein, die Reels laufen gemischt nacheinander, alle raten am eigenen Handy, von wem welches ist, und vergeben Sterne.

- **Online-Modus:** Jeder am eigenen Handy, ohne Anmeldung. Einer eröffnet, schickt den Einladungslink in die Gruppe, fertig.
- **Ein-Handy-Modus:** Das Handy geht reihum. Funktioniert auch ohne Firebase.

Die Reels laufen direkt in der App (Instagram, TikTok, YouTube Shorts). Lässt sich ein Link nicht einbetten, gibt es einen Knopf zum Öffnen in der jeweiligen App.

## Einrichtung (einmalig, ca. 10–15 Minuten)

### 1. Firebase-Projekt anlegen (kostenlos)

1. <https://console.firebase.google.com> öffnen, mit Google-Konto anmelden.
2. **Projekt erstellen** → Name z. B. `wessen-reel` → Google Analytics **aus** → erstellen.
3. Links **Build › Authentication** → **Jetzt starten** → Reiter **Anmeldemethode** → **Anonym** aktivieren → speichern.
4. Links **Build › Realtime Database** → **Datenbank erstellen** → Standort **Belgien (europe-west1)** → **Im gesperrten Modus starten** → aktivieren.
5. In der Realtime Database den Reiter **Regeln** öffnen, alles ersetzen durch den Inhalt von [`database.rules.json`](database.rules.json) → **Veröffentlichen**.
6. Zahnrad oben links › **Projekteinstellungen** → unten bei „Meine Apps“ das Web-Symbol `</>` → App-Name `wessen-reel` → **App registrieren** (Hosting nicht ankreuzen).
7. Es erscheint ein Code-Block mit `firebaseConfig = { apiKey: ..., authDomain: ..., databaseURL: ..., projectId: ..., appId: ... }`. Diese Werte in [`firebase-config.js`](firebase-config.js) eintragen.
   Fehlt `databaseURL`, steht sie oben in der Realtime Database (Form `https://<projekt>-default-rtdb.europe-west1.firebasedatabase.app`).

Die Werte in `firebase-config.js` sind nicht geheim; jede Firebase-Web-App liefert sie im Klartext aus. Geschützt wird über die Regeln: nur angemeldete Geräte (anonym), nur einzelne Spiele per Code, keine Liste aller Spiele.

### 2. Seite veröffentlichen (GitHub Pages)

1. Repo öffentlich machen: **Settings › General › Danger Zone › Change visibility › Public**. (Kostenloses GitHub Pages geht nur bei öffentlichen Repos.)
2. **Settings › Pages** → Source **Deploy from a branch** → Branch `main`, Ordner `/ (root)` → Save.
3. Nach 1–2 Minuten ist die App erreichbar unter `https://leobendner-svg.github.io/Wessen-reel/`.

### 3. Spielen

Einer öffnet die Seite, gibt seinen Spitznamen ein, tippt **Neues Spiel eröffnen** und dann **Freunde einladen**. Der Link enthält den Spielcode; die anderen tippen ihn an, geben ihren Namen ein und sind drin.

## Kosten und Grenzen

- Firebase Spark-Tarif (kostenlos): 1 GB Speicher, 10 GB Download/Monat, 100 gleichzeitige Verbindungen. Ein Spielabend braucht davon nur einen winzigen Bruchteil.
- Ob ein Video eingebettet abspielt, entscheiden Instagram und TikTok. Private Accounts oder gesperrte Videos laufen nicht eingebettet; dann hilft der Öffnen-Knopf.
- TikTok-Kurzlinks (`vm.tiktok.com/...`) werden über TikToks oEmbed-Schnittstelle aufgelöst. Klappt das nicht, wird der Link nur als Öffnen-Knopf angezeigt.
- Instagram-Links der Form `instagram.com/share/...` lassen sich nicht einbetten (Öffnen-Knopf).
- Wer technisch versiert ist, kann in der Datenbank sehen, wer welchen Link eingereicht hat. Für ein Spiel unter Freunden ist das bewusst so einfach gehalten.
- Spiele bleiben in der Datenbank liegen. Bei Bedarf in der Firebase-Konsole unter Realtime Database den Knoten `games` löschen.

## Lokal testen (Entwickler)

```bash
npx firebase-tools emulators:start --only auth,database --project demo-wessen
python3 -m http.server 8123
# http://127.0.0.1:8123/index.html?emu=1
```
