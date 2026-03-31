# Technische Strategie

<a id="technische-strategie"></a>

Dieses Kapitel beschreibt die bewussten Technologieentscheidungen hinter PetBuddy und reflektiert die zentralen Herausforderungen während der Entwicklung.

---

## Technologie-Stack – Begründung

### Python + Flet (UI-Framework)

**Gewählt:** Python 3.13 mit Flet 0.28.3

**Begründung:** Das Team beherrscht Python aus dem Schulunterricht – es war die einzige Programmiersprache, mit der alle Mitglieder vertraut waren. Flet ermöglicht die Entwicklung vollständiger Web- und Desktop-Anwendungen **rein in Python**, ohne JavaScript oder HTML/CSS schreiben zu müssen. Flet orientiert sich in seinem Programmiermodell an Flutter, einem Framework, das auf der letztjährigen Techniker-Börse als moderner Ansatz für UI-Entwicklung vorgestellt wurde.

**Verglichene Alternativen:**

| Alternative | Warum nicht gewählt |
|-------------|---------------------|
| **Flutter** (Dart) | Neue Programmiersprache (Dart) erforderlich – zu hohe Einarbeitungszeit |
| **React + Django** | Zwei Sprachen nötig (JavaScript + Python), Frontend/Backend-Split erhöht die Komplexität |
| **Django allein** | Keine moderne, reaktive UI möglich – nur klassische Server-Side-Rendered-Seiten |

**Bewertung:**

- **Wartbarkeit:** Sehr gut – ein einziger Tech-Stack (Python), kein Frontend/Backend-Split. Jedes Teammitglied kann an jeder Stelle arbeiten.
- **Skalierbarkeit:** Flet-Apps laufen als Web-Server und können über Docker horizontal skaliert werden.
- **Performance:** Flet rendert UI-Updates inkrementell über WebSocket, was für die Anwendungsgröße von PetBuddy ausreichend performant ist.

---

### Supabase (Datenbank, Auth, Storage)

**Gewählt:** Supabase (PostgreSQL + Auth + Storage)

**Begründung:** Supabase ist eine kostenlose, Open-Source-Plattform, die PostgreSQL als Datenbank nutzt. Da das Team MySQL und relationale Datenbanken bereits aus dem Schulunterricht kennt, waren die SQL-Kenntnisse direkt übertragbar. Supabase bietet zusätzlich eine eingebaute Authentifizierung (E-Mail + Passwort) und einen Storage-Service für Bildupload – alles in einem Dienst, ohne eigene Server betreiben zu müssen.

**Verglichene Alternativen:**

| Alternative | Warum nicht gewählt |
|-------------|---------------------|
| **Firebase** (Google) | Proprietär, NoSQL-Datenbank (Firestore) – SQL-Kenntnisse nicht nutzbar |
| **Eigener PostgreSQL-Server** | Zu hoher Wartungsaufwand (Hosting, Backups, Sicherheit) für ein Schulprojekt |
| **MongoDB** | NoSQL-Datenbank – keine Relationen, ungeeignet für das stark relationale Datenmodell von PetBuddy |

**Bewertung:**

- **Wartbarkeit:** Sehr gut – Supabase übernimmt Datenbank-Wartung, Backups und Auth-Logik. Das Team muss sich nur um die Anwendungslogik kümmern.
- **Skalierbarkeit:** Supabase skaliert die PostgreSQL-Instanz automatisch. Row Level Security (RLS) sichert den Datenzugriff auf Datenbankebene ab.
- **Performance:** PostgreSQL bietet performante Abfragen durch Indizes. Die Standortsuche (Umkreisfilter) wird clientseitig per Haversine-Formel berechnet, da eine native Geo-Distanzfunktion in Supabase nicht verfügbar war.

---

### Mapbox API (Geocoding)

**Gewählt:** Mapbox Geocoding API

**Begründung:** Mapbox bietet eine kostenlose Geocoding-API mit einem großzügigen Free-Tier (100.000 Anfragen/Monat). Die API liefert Orts-Autocomplete-Vorschläge mit Koordinaten, die für die Umkreissuche benötigt werden.

**Verglichene Alternativen:**

| Alternative | Warum nicht gewählt |
|-------------|---------------------|
| **Google Maps API** | Kostenpflichtig ab der ersten Anfrage (Kreditkarte erforderlich) |
| **Nominatim (OpenStreetMap)** | Langsamer, weniger präzise Autocomplete-Vorschläge, restriktive Nutzungsbedingungen |

**Bewertung:**

- **Wartbarkeit:** Gut – einfache REST-API, ein einziger Service-Aufruf für Autocomplete. Die Funktion ist optional – ohne Mapbox-Token läuft die App weiter, nur ohne Geocoding.
- **Skalierbarkeit:** Free-Tier reicht für ein Schulprojekt vollständig aus.
- **Performance:** Schnelle Antwortzeiten (<200 ms) für Autocomplete-Vorschläge.

!!! info "Kartenansicht"
    Für die **Kartenansicht** wird OpenStreetMap (via Folium/Leaflet) verwendet – komplett kostenlos und ohne API-Key. Mapbox wird nur für die Geocoding-Suche eingesetzt.

---

### Hugging Face ViT (KI-Rassenerkennung)

**Gewählt:** Google Vision Transformer (`google/vit-base-patch16-224`)

**Begründung:** PetBuddy nutzt ein vortrainiertes Vision-Transformer-Modell von Hugging Face für die automatische Erkennung von Tierrassen aus Fotos. Das Modell ist kostenlos verfügbar, läuft **lokal auf dem Server** (kein API-Key nötig) und ist daher **datenschutzfreundlich** – hochgeladene Bilder verlassen den Server nicht.

Das Modell basiert auf ImageNet (1000 Klassen) und ist nicht speziell auf Haustiere trainiert, erkennt aber zahlreiche Hunde- und Katzenrassen. Eine eigene Übersetzungstabelle mappt die englischen ImageNet-Labels auf deutsche Rassenamen (z. B. „golden_retriever" → „Golden Retriever", „tabby" → „Getigerte Katze").

**Verglichene Alternativen:**

| Alternative | Warum nicht gewählt |
|-------------|---------------------|
| **Google Cloud Vision API** | Kostenpflichtig, Bilder werden an Google gesendet (Datenschutz) |
| **AWS Rekognition** | Kostenpflichtig, Vendor-Lock-in |
| **Eigenes Modell trainieren** | Zu aufwendig – fehlendes Trainingsdatenmaterial und ML-Expertise |

**Bewertung:**

- **Wartbarkeit:** Gut – das Modell wird einmalig beim ersten Aufruf von Hugging Face heruntergeladen und anschließend lokal gecached. Lazy Loading verhindert unnötigen Speicherverbrauch.
- **Skalierbarkeit:** Begrenzt – Inference auf CPU ist langsamer als auf GPU. Für PetBuddy als Schulprojekt ausreichend.
- **Performance:** Lazy Loading des Modells – erst bei der ersten Anfrage wird das Modell geladen (~2 GB RAM). Die Erkennung selbst dauert wenige Sekunden.

---

### Fly.io (Deployment)

**Gewählt:** Fly.io mit Docker

**Begründung:** Fly.io bietet kostengünstiges Hosting mit Docker-Support und einem Serverstandort in Frankfurt (niedrige Latenz für deutsche Nutzer). Die Konfiguration erfolgt über eine einfache `fly.toml`-Datei und einen Multi-Stage Docker-Build.

!!! info "Python-Version im Docker-Container"
    Der Docker-Container verwendet **Python 3.11** (statt 3.13), da die PyTorch CPU-only Wheels für Python 3.13 zum Zeitpunkt der Entwicklung nicht zuverlässig verfügbar waren. Lokal wird Python 3.13 verwendet.

**Verglichene Alternativen:**

| Alternative | Warum nicht gewählt |
|-------------|---------------------|
| **Heroku** | Free-Tier abgeschafft, deutlich teurer geworden |
| **Vercel** | Primär für JavaScript/Node.js – kein nativer Python-Support |
| **Eigener Server** | Zu hoher Wartungsaufwand für ein Schulprojekt |

**Bewertung:**

- **Wartbarkeit:** Gut – Deployment über `fly deploy`, automatische SSL-Zertifikate, einfaches Monitoring.
- **Skalierbarkeit:** Fly.io ermöglicht horizontales Skalieren über mehrere Instanzen. PetBuddy läuft auf einer Instanz mit 2 GB RAM.
- **Performance:** Serverstandort Frankfurt sorgt für niedrige Latenz (<50 ms) für Nutzer in Deutschland.

---

### MkDocs Material (Dokumentation)

**Gewählt:** MkDocs mit Material-Theme

**Begründung:** MkDocs wurde im Schulunterricht vorgestellt und ermöglicht eine professionelle Dokumentation auf Basis von Markdown-Dateien. Das Material-Theme bietet eine moderne Optik mit Suchfunktion, Dark Mode, Mermaid-Diagramm-Support und responsivem Design.

**Verglichene Alternativen:**

| Alternative | Warum nicht gewählt |
|-------------|---------------------|
| **GitBook** | Eingeschränkter Free-Tier, weniger Flexibilität |
| **Docusaurus** | React-basiert, komplexer aufzusetzen |

**Bewertung:**

- **Wartbarkeit:** Sehr gut – Dokumentation liegt als Markdown-Dateien direkt im Repository und ist per Git versioniert. Änderungen an Inhalt und Struktur erfordern keine besonderen Werkzeuge.
- **Skalierbarkeit:** Sehr gut – MkDocs generiert statische HTML-Seiten, die ohne Serverressourcen auskommen und über GitHub Pages kostenlos gehostet werden.
- **Performance:** Sehr gut – statische Seiten laden schnell, ohne Datenbankabfragen oder dynamische Inhalte.
