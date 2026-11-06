# Vorrat Scanner 1.1

**Version 1.1** · Neu: kompaktere Rückseiten-Ansicht, damit Text, Foto und „Auslesen“ auf dem iPhone ohne Scrollen passen.

Begleit-App zum **Vorrat Rechner**. Du fotografierst eine Gruppe gleicher Dosen (z. B. 10 × Thunfisch), die App liest Name, Anzahl, Gewicht und Kalorien per KI-Bilderkennung aus (standardmäßig **Grok von xAI**, alternativ **OpenAI**), du prüfst die Werte, und am Ende exportierst du alles als JSON-Datei für den Vorrat Rechner.

Ohne Build-Schritt und ohne externe Bibliotheken. Dateien: `index.html` (die App), `manifest.json` (Name und Symbol für den Home-Bildschirm), `sw.js` (Offline-Speicher), `icon-192.png` und `icon-512.png` (App-Symbol), `README.md`. Alle Dateien gemeinsam in denselben Ordner auf GitHub Pages hochladen. Gebaut für Safari auf dem iPhone.

## Grok-Schlüssel (xAI) einrichten
1. Auf <https://console.x.ai> anmelden oder registrieren. Du kannst dich mit demselben Konto wie bei Grok anmelden.
2. Unter **Billing → API spend management** Guthaben vorab kaufen, **mindestens 5 $**. Wichtig: Das API-Guthaben ist **nicht** im SuperGrok- oder X-Abo enthalten, es wird getrennt abgerechnet und ist nicht erstattbar.
3. Das **Invoiced billing limit** auf 0 $ lassen (Standard). Dann stoppen die Anfragen einfach, wenn das Guthaben aufgebraucht ist, und es kann nichts darüber hinaus abgebucht werden.
4. Unter **API Keys** einen neuen Schlüssel erstellen. Ablauf (*Expiration*) auf **No expiry** lassen. Der Schlüssel beginnt mit `xai-` und wird **nur einmal angezeigt**, also sofort kopieren.
5. In der App unter **Einstellungen** den Anbieter **Grok (xAI)** wählen, den Schlüssel einfügen und **Speichern und testen** tippen. Das Modell `grok-4.20-non-reasoning` ist schon eingetragen.

Kosten (Schätzung): etwa 0,3–0,4 US-Cent pro Foto, also rund 0,7 US-Cent pro Produkt (Vorder- und Rückseite). Mit 5 $ kommst du auf ungefähr 700 Produkte. Das Guthaben siehst du jederzeit in der xAI Console unter **Billing**.

## Alternative: OpenAI-Schlüssel
1. Auf <https://platform.openai.com> anmelden oder registrieren.
2. Unter **Billing** etwas Guthaben aufladen. Ein Foto kostet mit `gpt-4o-mini` etwa 0,4 US-Cent, mit `gpt-4o` etwa einen Cent. Hinweis: OpenAI hat `gpt-4o-mini` als veraltet (*deprecated*) markiert; das Modell lässt sich in den Einstellungen ändern.
3. Unter <https://platform.openai.com/api-keys> **Create new secret key** wählen und den Schlüssel (`sk-…`) kopieren.
4. In der App unter **Einstellungen** den Anbieter **OpenAI** wählen, den Schlüssel einfügen und **Speichern und testen** tippen.

Für jeden Anbieter wird ein eigener Schlüssel gespeichert; beim Umschalten bleibt der andere erhalten. Wer die App schon mit einem OpenAI-Schlüssel benutzt hat, landet nach dem Update automatisch bei OpenAI und muss nichts neu eintragen.

## Datenschutz
Der Schlüssel wird nur auf diesem Gerät im Browser-Speicher (localStorage) abgelegt und nur direkt an den gewählten Anbieter gesendet: `api.x.ai` (Grok) bzw. `api.openai.com` (OpenAI). Wer Zugriff auf das entsperrte Gerät hat, kann ihn auslesen. Geht das Handy verloren, den Schlüssel in der Console des Anbieters löschen.

## Benutzung
1. **Vorderseite:** Alle gleichen Dosen nebeneinander fotografieren, Vorschau prüfen, **Auslesen**. Die App erkennt Name, Marke und Anzahl.
2. **Rückseite:** Das Etikett mit Füllmenge, Abtropfgewicht und Nährwerttabelle fotografieren, **Auslesen**. Fett, Kohlenhydrate, Zucker, Eiweiß und Salz werden zur Kontrolle angezeigt, wenn lesbar, aber nicht exportiert.
3. **Prüfen:** Alle Werte korrigieren, wenn nötig, die Anzahl einstellen und **Zur Liste hinzufügen**. Fotos lassen sich überspringen, um alles von Hand einzugeben.
4. **Liste:** Einträge antippen, um sie zu bearbeiten. **Löschen** muss zweimal angetippt werden. Die Summen stehen oben.
5. **Export:** **JSON erstellen**, dann **Speichern / Download** (landet unter *Dateien › Downloads*), **Teilen** oder **Kopieren**. Dateiname: `vorrat-JJJJ-MM-TT.json`.

Regeln beim Auslesen: Nicht lesbare Werte bleiben leer, die KI soll nie raten. Das **Abtropfgewicht** zählt, wenn es angegeben ist, sonst das Nettogewicht (das steht dann in der Notiz). Stehen nur kJ auf dem Etikett, rechnet die App kJ ÷ 4,184 in kcal um und markiert das. Gibt es nur Angaben je Portion, rechnet sie auf 100 g um, wenn die Portionsgröße bekannt ist; sonst bleiben die kcal leer.

## JSON-Format (für den Import in den Vorrat Rechner)
```json
{ "format": "vorrat-scanner", "version": 1, "exportedAt": "2026-11-01T12:00:00.000Z",
  "totals": { "cans": 10, "grams": 1200, "kcal": 2294, "itemsWithoutKcal": 0 },
  "items": [ { "id": "scan-…", "name": "Thunfisch Filets", "kcalPer100": 191.2, "grams": 1200,
      "cans": 10, "brand": "Rio Mare", "netWeightPerCan": 160, "drainedWeightPerCan": 120,
      "weightBasis": "abtropfgewicht", "kcalPerCan": 229, "kcalTotal": 2294,
      "servingSizeG": null, "kcalPerServing": null, "calorieBasis": "per100g",
      "kcalSource": "kj", "note": "Abtropfgewicht verwendet … kcal aus kJ umgerechnet …" } ] }
```
- `id`, `name`, `kcalPer100`, `grams` entsprechen genau den Feldern eines Lebensmittels im Vorrat Rechner (`vorrat-rechner.v1` → `items`). `grams` ist das Gesamtgewicht aller Dosen (Abtropf- bzw. Nettogewicht × Anzahl) und immer eine Zahl.
- `kcalPer100` ist **`null`**, wenn das Etikett keine verwertbare Kalorienangabe hat. Ein Import muss solche Einträge überspringen oder zum Nachtragen markieren. **Achtung:** Die aktuelle Prüfung im Rechner (`isFinite(null)` ist in JavaScript `true`) würde `null` zu 0 kcal machen, statt den Eintrag zu verwerfen. Der Import sollte deshalb `typeof kcalPer100 === "number"` prüfen.
- Alle weiteren Felder sind zusätzliche Informationen, die der Rechner ignorieren kann: `weightBasis` (`abtropfgewicht`/`nettogewicht`), `calorieBasis` (`per100g`/`perServing`/`null`), `kcalSource` (`etikett`, `kj`, `portion`, `manuell` oder `null`).

Import: Im Vorrat Rechner (ab Version 1.2) auf der Seite **Vorrat** auf **Importieren** tippen und die JSON-Datei wählen oder den Text einfügen. Die Einträge werden zum vorhandenen Vorrat hinzugefügt; Einträge ohne Kalorien werden markiert und können ergänzt oder übersprungen werden.

## Offline nutzen

Die App speichert sich beim ersten Öffnen mit Internet selbst auf dem iPhone (über `sw.js` und `manifest.json`). Danach öffnet sie sich auch ohne Verbindung, zum Beispiel im Flugmodus. Offline funktionieren die Liste, Werte von Hand eintragen, Bearbeiten und „JSON erstellen“. Nur das Auslesen der Fotos braucht Internet, weil die Bilder an die KI geschickt werden.

Nach einem Update alle Dateien hochladen und die App einmal mit Internet öffnen.
