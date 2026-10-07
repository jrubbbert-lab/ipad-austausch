# iPad-Austausch – Terminbuchung (PEBK)

Eine einzige Datei (`index.html`): Lehrkräfte geben ihr Kürzel ein, wählen 5 Min. (Übergabe) oder 20 Min. (Übergabe + Datensicherung) und buchen einen freien Termin in Raum B113. Das iPad-Team verwaltet die Zeitfenster über „Verwaltung (iPad-Team)“ unten auf der Seite.

Ohne Firebase-Eintrag läuft das Tool im **lokalen Testmodus**. Die Daten bleiben dann nur in diesem Browser.

## 1. Firebase einrichten (einmalig, ca. 10 Min.)

1. <https://console.firebase.google.com> → **Projekt hinzufügen** (z. B. `ipad-austausch-pebk`). Google Analytics kannst du ausschalten.
2. **Build → Realtime Database → Datenbank erstellen**, Standort **europe-west1 (Belgien)**, im **gesperrten Modus** starten.
3. **Build → Authentication → Jetzt starten → E-Mail/Passwort** aktivieren.
   Unter **Users → Nutzer hinzufügen** ein Konto für dich anlegen (bei Bedarf auch für KRS und SCD).
   Die **Nutzer-UID** in der Liste kopieren.
4. **Realtime Database → Regeln**: Inhalt von `database.rules.json` einfügen, `HIER_DEINE_UID` (dreimal) durch deine UID ersetzen und **Veröffentlichen**.
   Sollen mehrere Personen verwalten, ersetzt du `auth.uid === 'UID'` durch `(auth.uid === 'UID1' || auth.uid === 'UID2')`.
5. **Projekteinstellungen (Zahnrad) → Allgemein**:
   - **Web-API-Schlüssel** kopieren.
   - Die **Datenbank-URL** steht oben in der Realtime Database.
6. In `index.html` ganz oben im Skript eintragen:
   ```js
   var FIREBASE_DB_URL  = "https://…firebasedatabase.app";
   var FIREBASE_API_KEY = "AIza…";
   ```
   Der API-Schlüssel darf öffentlich sein. Geschützt wird über die Regeln und das Login.

## 2. Auf GitHub Pages veröffentlichen

1. Neues **öffentliches** Repository auf GitHub anlegen, z. B. `ipad-austausch`.
2. `index.html` hochladen (**Add file → Upload files**). README und Regeln sind optional.
3. **Settings → Pages → Branch `main` / root → Save**. Nach ca. 1 Min. ist das Tool erreichbar unter
   `https://DEIN-NAME.github.io/ipad-austausch/`. Diesen Link setzt du in der Mail für `[LINK]` ein.

## 3. Zeitfenster eintragen und ändern

1. Unten auf **Verwaltung (iPad-Team)** klicken und anmelden.
2. **Tabelle übernehmen**: Die Tabelle mit Datum und je einer Spalte pro Kürzel aus Word, Excel oder Numbers kopieren, einfügen, **Prüfen**, dann **Übernehmen**.
   - Bereits vorhandene Fenster werden übersprungen.
   - Vergangene Tage und Tage nach dem 06.11. (Frist) werden nicht übernommen.
   - Mit * markierte Tage werden übersprungen, solange der Haken gesetzt ist.
3. Einzelne Fenster kannst du mit dem Formular hinzufügen oder in der Liste löschen. Beim Löschen werden die Buchungen in diesem Fenster mitgelöscht, und das Tool nennt dir die betroffenen Kürzel.
4. **Buchungen**: Liste mit Kürzel, Uhrzeit und Betreuung. Du kannst sie als CSV herunterladen, drucken oder einzelne Buchungen stornieren.

## Wer sieht was?

Alle Termine sind **ohne Login für alle sichtbar**: Unter „Wer hat wann gebucht?“ steht jede kommende Buchung mit Uhrzeit, Kürzel, Terminart und Betreuung. Über das Suchfeld findet man ein Kürzel schnell.

Ein Login brauchst nur das iPad-Team, und zwar zum Anlegen und Löschen von Zeitfenstern und zum Stornieren fremder Buchungen. Lehrkräfte buchen und sagen ihren eigenen Termin über ihr Kürzel ab.

## Wie Doppelbuchungen verhindert werden

Jede Buchung belegt alle 5-Minuten-Zellen, die sie abdeckt (`ipad/belegt/…`), und liegt selbst unter dem Kürzel (`ipad/buchungen/RUB`). Alles wird in **einer** Anfrage geschrieben; die Regeln erlauben nur das Schreiben in **leere** Stellen. Ist eine Zelle schon belegt oder hat das Kürzel bereits einen Termin, lehnt Firebase die ganze Buchung ab.

## Datenschutz

Gespeichert werden nur Lehrerkürzel, Terminart und Uhrzeit, also keine Namen und keine E-Mail-Adressen. Nach Abschluss des Austauschs löschst du die Daten in der Firebase-Konsole unter **Realtime Database → `ipad` → Löschen**.
