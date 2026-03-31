# Herausforderungen & Lösungen

Diese Seite dokumentiert die zentralen technischen Herausforderungen, die während der Entwicklung von PetBuddy aufgetreten sind, und beschreibt jeweils die gewählte Lösung.

---

### KI-Modell nicht pet-spezifisch

**Problem:** Das verwendete Vision-Transformer-Modell (`google/vit-base-patch16-224`) ist ein allgemeines Bilderkennungsmodell (trainiert auf ImageNet mit 1000 Klassen). Es liefert englische Labels wie „golden_retriever" oder „tabby", aber keine deutschen Rassenamen und keine explizite Zuordnung zu „Hund" oder „Katze".

**Lösung:** Es wurde eine eigene Mapping-Logik implementiert:

1. **Tierart-Erkennung:** Über Keyword-Listen werden ImageNet-Labels als „Hund" oder „Katze" klassifiziert.
2. **Übersetzungstabelle:** Eine manuell gepflegte Tabelle übersetzt die häufigsten englischen Rassenamen ins Deutsche (z. B. „french bulldog" → „Französische Bulldogge").
3. **Konfidenz-Schwelle:** Ergebnisse mit weniger als 30 % Konfidenz werden als unsicher markiert und der Nutzer wird aufgefordert, die Rasse manuell einzugeben.

---

### Umkreissuche mit Koordinaten

**Problem:** Die Suche nach Meldungen in einem bestimmten Umkreis (5/10/25/50 km) erforderte eine Distanzberechnung zwischen Geo-Koordinaten, die nicht nativ in Supabase verfügbar war.

**Lösung:** Die Umkreissuche wurde clientseitig über die **Haversine-Formel** implementiert, die den Abstand zwischen zwei GPS-Koordinaten auf der Erdkugel berechnet. Zusätzlich wurde die UX verbessert:

- Umkreis-Auswahl ist nur möglich, wenn ein Ort angegeben wurde
- Ergebnisse werden nach Entfernung angereichert und können sortiert werden
- Mapbox-Geocoding liefert die Koordinaten für den eingegebenen Ortsnamen

---

### Karte: Kommunikation zwischen JavaScript und Python

**Problem:** Die interaktive Karte ist eine Folium-HTML-Seite, die in einem Flet-WebView läuft. Klick-Events auf Marker werden von Leaflet (JavaScript) ausgelöst – es gibt jedoch keinen direkten Kanal, um diese Events an den Python-Code weiterzuleiten.

**Lösung:** Ein JavaScript Click-Handler setzt bei Marker-Klick `window.location.href` auf eine speziell formatierte URL (`about:blank?map_pin_click=<postId>`). Flet erkennt die URL-Änderung im WebView und extrahiert die Post-ID aus dem Query-Parameter, um den Detail-Dialog zu öffnen.

---

### PDF-Download in Flet Web

**Problem:** Flet bietet keinen nativen Mechanismus, um Dateien im Browser herunterzuladen. Ein direktes „Speichern unter"-Verhalten für generierte PDFs war mit Flet-Bordmitteln nicht umsetzbar.

**Lösung:** `main.py` startet parallel zur Flet-App einen **FastAPI-Server** über Uvicorn. Eine dedizierte Route (`/download/{filename}`) liefert PDFs aus dem `assets/pdf_exports/`-Verzeichnis als HTTP-Response mit `Content-Disposition: attachment`. Die Flet-UI öffnet diese URL im Browser, der dann den nativen Download-Dialog anzeigt. Flet-App und FastAPI teilen sich dabei denselben Port – FastAPI wird als ASGI-App gestartet und Flet als Sub-Mount eingehängt.

---
