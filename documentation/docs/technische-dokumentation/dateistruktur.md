# Dateistruktur

```
Projektarbeit_2026/
├── main.py                      # Einstiegspunkt (Flet-App + FastAPI)
├── app/
│   ├── app.py                   # PetBuddyApp – Hauptklasse
│   ├── navigation.py            # Drawer, AppBar, Routing
│   ├── auth_flow.py             # Login/Logout-Workflow
│   └── dialogs.py               # Wiederverwendbare Dialoge
├── ui/                          # Feature-basierte UI-Module
│   ├── auth/                    # Anmeldung / Registrierung
│   ├── discover/                # Entdecken, Suche, Karte
│   │   ├── components/          # UI-Komponenten (Karten, Filter, Kommentare)
│   │   ├── handlers/            # Event-Handler (Suche, Favoriten, Kommentare)
│   │   └── map/                 # Kartenansicht (Folium/Leaflet)
│   ├── post_form/               # Meldung erstellen / bearbeiten
│   │   ├── components/          # Formular-Komponenten
│   │   └── handlers/            # Upload, KI-Erkennung, Referenzdaten
│   ├── profile/                 # Profil, Favoriten, Einstellungen
│   │   ├── components/          # Profil-Komponenten
│   │   └── handlers/            # Profil-Handler
│   ├── theme.py                 # ThemeManager (Hell/Dunkel)
│   ├── constants.py             # UI-Konstanten (Farben, Abstände)
│   ├── helpers.py               # UI-Hilfsfunktionen
│   └── shared_components.py     # Gemeinsame UI-Elemente
├── services/                    # Geschäftslogik & Datenzugriff
│   ├── supabase_client.py       # Singleton Supabase-Client
│   ├── account/                 # Auth, Profil, Löschung
│   ├── posts/                   # CRUD, Suche, Kommentare
│   │   ├── post.py              # PostService (CRUD)
│   │   ├── search.py            # SearchService (Filter + Sortierung)
│   │   ├── filters.py           # Filterfunktionen
│   │   ├── comment.py           # CommentService
│   │   ├── favorites.py         # FavoritesService
│   │   ├── saved_search.py      # SavedSearchService
│   │   ├── references.py        # ReferenceService (Lookup-Tabellen)
│   │   ├── post_image.py        # PostStorageService (Bildupload)
│   │   ├── post_relations.py    # PostRelationsService
│   │   ├── map_service.py       # MapDataService (GeoJSON)
│   │   └── queries.py           # Zentrale Select-Statements
│   ├── geocoding/               # Mapbox-Integration
│   └── ai/                      # Tiererkennung (ViT)
├── utils/                       # Logging, PDF, Karten, Validierung
├── deploy/
│   ├── Dockerfile               # Multi-Stage Docker Build
│   └── fly.toml                 # Fly.io Konfiguration
└── documentation/               # MkDocs-Dokumentation
```
