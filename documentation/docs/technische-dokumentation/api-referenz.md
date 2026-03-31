# API-Referenz / Modulbeschreibung

Da PetBuddy eine **Flet-Anwendung** ist (kein REST-Backend), liegt die Geschäftslogik in der Service-Schicht. Nachfolgend die wichtigsten Module und ihre Funktionen.

---

## `services/account/` – Benutzerverwaltung

| Modul | Klasse | Wichtige Methoden |
|-------|--------|-------------------|
| `auth.py` | `AuthService` | `login()`, `register()`, `reset_password()` |
| `profile.py` | `ProfileService` | `get_current_user()`, `update_display_name()`, `get_user_profiles()` |
| `profile_image.py` | `ProfileImageService` | `upload_profile_image()`, `delete_profile_image()` |
| `account_deletion.py` | `AccountDeletionService` | `delete_account()` – kaskadiert Posts, Bilder, Kommentare |

---

## `services/posts/` – Meldungsverwaltung

| Modul | Klasse / Funktion | Wichtige Methoden |
|-------|-------------------|-------------------|
| `post.py` | `PostService` | `create()`, `update()`, `delete()`, `get_by_id()`, `get_all()` |
| `search.py` | `SearchService` | `search_posts()` – kombiniert Filter, Sortierung, Standort |
| `filters.py` | Hilfsfunktionen | `filter_by_search()`, `filter_by_colors()`, `filter_by_location()`, `sort_by_event_date()`, `enrich_with_distance()` |
| `comment.py` | `CommentService` | `get_comments()`, `create_comment()`, `toggle_reaction()` |
| `favorites.py` | `FavoritesService` | `get_favorites()`, `add_favorite()`, `remove_favorite()` |
| `saved_search.py` | `SavedSearchService` | `get_saved_searches()`, `save_search()`, `delete_search()` |
| `references.py` | `ReferenceService` | `get_post_statuses()`, `get_species()`, `get_breeds_by_species()`, `get_colors()`, `get_sex()` |
| `post_image.py` | `PostStorageService` | `upload_post_image()`, `remove_post_image()` – JPEG-Komprimierung |
| `post_relations.py` | `PostRelationsService` | `add_color()`, `update_colors()`, `add_photo()` |
| `map_service.py` | `MapDataService` | `posts_to_geojson()`, `get_map_bounds()`, `get_center_point()` – GeoJSON für Kartenansicht |
| `queries.py` | Query-Konstanten | `POST_SELECT_FULL`, `POST_SELECT_LIST`, `POST_SELECT_FAVORITES` – zentrale Select-Statements |

---

## `services/geocoding/` – Standortdienste

| Modul | Funktion | Beschreibung |
|-------|----------|--------------|
| `mapbox_geocoding.py` | `geocode_suggestions()` | Mapbox-Autocomplete → `{text, lat, lon}` |

---

## `services/ai/` – Künstliche Intelligenz

| Modul | Klasse | Beschreibung |
|-------|--------|--------------|
| `pet_recognition.py` | `PetRecognitionService` | `recognize_pet()` – Bilderkennung via Hugging Face ViT (google/vit-base-patch16-224) |

---

## `main.py` – FastAPI Server

Neben der Flet-App stellt `main.py` einen FastAPI-Server bereit:

| Endpoint | Methode | Beschreibung |
|----------|---------|--------------|
| `/download/{filename}` | `GET` | PDF-Download von exportierten Meldungen aus `assets/pdf_exports/` |

---

## `utils/` – Hilfsfunktionen

| Modul | Zweck |
|-------|-------|
| `logging_config.py` | Zentrales Logging (Konsole + Datei) |
| `pdf_generator.py` | PDF-Export von Meldungen (ReportLab) |
| `map_generator.py` | Kartenansicht (Folium/Leaflet) |
| `validators.py` | Eingabevalidierung (E-Mail, Passwort, Pflichtfelder) |
| `constants.py` | App-weite Konstanten (Backend) |
