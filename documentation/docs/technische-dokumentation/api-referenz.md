# API-Referenz / Funktionsbeschreibung

Da PetBuddy eine **Flet-Anwendung** ist (kein REST-Backend), liegt die Geschäftslogik in der **Service-Schicht**. Die UI-Komponenten greifen ausschließlich über Service-Klassen auf Supabase zu — es gibt keinen direkten Datenbankzugriff aus der Präsentationsschicht.

Alle Service-Klassen erhalten den Supabase-`Client` per **Constructor Injection**, was die Abhängigkeiten explizit und testbar macht. Rückgabewerte sind durchgängig typisiert (`AuthResult`, `Dict`, `List[Dict]`, `Optional`).

---

## Externe Schnittstellen

### HTTP-Endpoint (`main.py`)

Der einzige HTTP-Endpoint der Anwendung:

| Endpoint | Methode | Rückgabe | Beschreibung |
|----------|---------|----------|--------------|
| `/download/{filename}` | `GET` | `FileResponse` | PDF-Download von exportierten Meldungen |

### Mapbox Geocoding (`services/geocoding/`)

| Methode | Rückgabe | Beschreibung |
|---------|----------|--------------|
| `geocode_suggestions(query: str, limit=5, language="de", country="de")` | `List[Dict]` | Autocomplete → `[{text, lat, lon}, ...]` |

### Hugging Face KI (`services/ai/`)

Bilderkennung mit **Lazy-Loading** des Modells (`google/vit-base-patch16-224`).

| Methode | Rückgabe | Beschreibung |
|---------|----------|--------------|
| `recognize_pet(image_data: bytes, species_filter: Optional[str])` | `Dict` | Erkennt Tierart und Rasse aus einem Bild |

Rückgabe: `{success: bool, species: str, breed: str, confidence: float, error: str}`

---

## Service-Schicht (intern, von UI aufgerufen)

Die folgenden Klassen bilden die interne API zwischen UI und Datenschicht. Sie werden nicht über HTTP exponiert, sondern direkt von den Flet-Komponenten instanziiert.

### AuthService (`services/account/auth.py`)

```python
@dataclass
class AuthResult:
    success: bool
    message: str = ""
    code: Optional[str] = None  # z. B. "invalid_credentials", "email_exists"
```

| Methode | Rückgabe | Beschreibung |
|---------|----------|--------------|
| `login(email: str, password: str)` | `AuthResult` | Benutzer anmelden |
| `register(email: str, password: str, username: str)` | `AuthResult` | Registrierung mit Bestätigungs-E-Mail |
| `logout()` | `AuthResult` | Benutzer abmelden |
| `reset_password(email: str)` | `AuthResult` | Passwort-Reset-E-Mail senden |
| `change_password(new_password: str)` | `AuthResult` | Passwort ändern |

### PostService (`services/posts/post.py`)

| Methode | Rückgabe | Beschreibung |
|---------|----------|--------------|
| `create(payload: Dict)` | `Dict` | Neuen Post erstellen |
| `update(post_id: str, payload: Dict)` | `Dict` | Post aktualisieren |
| `delete(post_id: str)` | `bool` | Post löschen (kaskadiert: Bilder, Farben, Storage) |
| `get_by_id(post_id: str)` | `Optional[Dict]` | Einzelnen Post mit Relationen laden |
| `get_all(limit: int)` | `List[Dict]` | Alle Posts absteigend nach Erstelldatum |

### SearchService (`services/posts/search.py`)

| Methode | Rückgabe | Beschreibung |
|---------|----------|--------------|
| `search_posts(filters, search_query, selected_colors, sort_option, location_lat, location_lon, radius_km, ...)` | `List[Dict]` | Kombinierte Suche mit Filtern, Umkreis und Sortierung |

Wichtige Filter-Parameter: Meldungstyp, Tierart, Geschlecht, Rasse, Farben, Freitext, Umkreis (Haversine-Formel), Sortierung (`created_at`, `event_date`, `distance`).

### CommentService (`services/posts/comment.py`)

| Methode | Rückgabe | Beschreibung |
|---------|----------|--------------|
| `create_comment(post_id: str, content: str)` | `Dict` | Kommentar erstellen |
| `delete_comment(comment_id: str)` | `bool` | Kommentar löschen |
| `toggle_reaction(comment_id: str, reaction_type: str)` | `bool` | Emoji-Reaktion setzen/entfernen |

### Weitere Services (Übersicht)

| Service | Modul | Verantwortung |
|---------|-------|---------------|
| `ProfileService` | `services/account/profile.py` | Profildaten lesen/aktualisieren, Batch-Abfrage von Benutzernamen |
| `ProfileImageService` | `services/account/profile_image.py` | Profilbild hochladen/löschen (400×400, JPEG Q85) |
| `AccountDeletionService` | `services/account/account_deletion.py` | Kaskadierte Kontolöschung (Posts, Bilder, Kommentare, Favoriten) |
| `FavoritesService` | `services/posts/favorites.py` | Favoriten verwalten (hinzufügen, entfernen, prüfen) |
| `SavedSearchService` | `services/posts/saved_search.py` | Gespeicherte Suchen verwalten |
| `ReferenceService` | `services/posts/references.py` | Lookup-Tabellen (Tierarten, Rassen, Farben) mit In-Memory-Cache |
| `PostStorageService` | `services/posts/post_image.py` | Bild-Upload mit Komprimierung (1920×1920, JPEG Q85, max 10 MB) |

---

## Interne Hilfsmodule (nicht Teil der API)

| Modul | Zweck |
|-------|-------|
| `services/posts/filters.py` | Filterfunktionen (Freitext, Farben, Umkreis, Sortierung) |
| `services/posts/queries.py` | Zentrale Select-Statements (`POST_SELECT_FULL`, `…LIST`, `…FAVORITES`) |
| `services/posts/post_relations.py` | Post-Farben- und Post-Foto-Zuordnungen |
| `services/posts/map_service.py` | GeoJSON-Konvertierung für Kartenansicht |
| `utils/validators.py` | Eingabevalidierung (E-Mail, Passwort, Pflichtfelder) |
| `utils/pdf_generator.py` | PDF-Export von Meldungen (ReportLab) |
| `utils/map_generator.py` | Kartenansicht (Folium/Leaflet) |
| `utils/logging_config.py` | Zentrales Logging (Konsole + Datei) |
| `utils/constants.py` | App-weite Konstanten (Limits, Standardwerte) |
