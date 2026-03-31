# Klassendiagramm

Das folgende Diagramm zeigt die wichtigsten Service-Klassen und ihre Beziehungen.

![Klassendiagramm](../assets/diagrams/class-diagram.png)

> [Interaktive Version auf Mermaid Chart öffnen](https://mermaid.ai/d/25660b9b-b0dc-465d-9b25-2ceaf18a27c7){:target="_blank"}

---

## Service-Klassen im Überblick

### Benutzerverwaltung (`services/account/`)

| Klasse | Verantwortung |
|--------|---------------|
| `AuthService` | Login, Registrierung, Passwort-Reset |
| `ProfileService` | Benutzerprofil lesen und aktualisieren |
| `ProfileImageService` | Profilbild hochladen und löschen (Supabase Storage) |
| `AccountDeletionService` | Account löschen mit Kaskadierung (Posts, Bilder, Kommentare) |

### Meldungsverwaltung (`services/posts/`)

| Klasse | Verantwortung |
|--------|---------------|
| `PostService` | CRUD-Operationen für Meldungen |
| `SearchService` | Kombinierte Suche mit Filtern, Sortierung und Standort |
| `CommentService` | Kommentare mit Threading und Emoji-Reaktionen |
| `FavoritesService` | Favoriten verwalten |
| `SavedSearchService` | Gespeicherte Suchen verwalten |
| `ReferenceService` | Lookup-Tabellen (Tierarten, Rassen, Farben, Geschlecht) |
| `PostStorageService` | Bildupload mit JPEG-Komprimierung |
| `PostRelationsService` | Farben und Fotos zu Meldungen zuordnen |
| `MapDataService` | GeoJSON-Aufbereitung für die Kartenansicht |

### Standort & KI (`services/geocoding/`, `services/ai/`)

| Klasse / Funktion | Verantwortung |
|-------------------|---------------|
| `geocode_suggestions()` | Mapbox-Autocomplete → `{text, lat, lon}` |
| `PetRecognitionService` | Bilderkennung via Hugging Face ViT (Tierart + Rasse) |

---

## Gemeinsame Abhängigkeit

Alle Service-Klassen erhalten den **Supabase-Client** als Konstruktor-Parameter (`sb`). Dieser wird einmalig über `get_client()` in `services/supabase_client.py` erzeugt und von `PetBuddyApp` an die UI-Views weitergegeben, die ihn an die Services durchreichen.
