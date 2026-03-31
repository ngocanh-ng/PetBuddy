# Voraussetzungen

Vor der Installation müssen folgende Software-Komponenten und externe Dienste bereitgestellt sein. Eine Internetverbindung ist während der Installation zwingend erforderlich.

## Laufzeitumgebung

| Software | Version | Zweck |
|----------|---------|-------|
| **Python** | 3.13 oder höher | Laufzeitumgebung für lokale Entwicklung und Ausführung |
| **pip** | (in Python enthalten) | Paketmanager für Python-Abhängigkeiten |
| **Git** | beliebig | Repository klonen |

!!! info "Python-Version prüfen"
    ```bash
    python3 --version   # macOS/Linux
    python --version    # Windows
    ```
    Falls die Version unter 3.13 liegt, lade Python von [python.org](https://www.python.org/downloads/) herunter.

!!! note "Docker-Deployment"
    Beim Deployment via Docker wird Python 3.11 verwendet (siehe `deploy/Dockerfile`), da PyTorch CPU-only Wheels für Python 3.13 zum Entwicklungszeitpunkt nicht zuverlässig verfügbar waren. Für den lokalen Betrieb wird Python 3.13 empfohlen.

## Datenbanksystem

PetBuddy verwendet **Supabase** – eine gehostete Open-Source-Plattform auf Basis von **PostgreSQL**. Eine lokale Datenbankinstallation ist **nicht erforderlich**. Supabase stellt Datenbank, Authentifizierung und Bildspeicher (Storage) als Cloud-Dienst bereit.

**Benötigt wird:**

- Ein kostenloses Supabase-Projekt unter [supabase.com](https://supabase.com)
- Die **Project URL** und der **Anon Key** (unter *Project Settings → API* im Supabase Dashboard)

## Sonstige Abhängigkeiten

Alle Python-Abhängigkeiten werden über `requirements.txt` installiert. Die wichtigsten Pakete:

| Paket | Zweck |
|-------|-------|
| `flet` / `flet-web` | UI-Framework (Web- und Desktop-Darstellung) |
| `supabase` | Python-Client für Supabase (Datenbank, Auth, Storage) |
| `pillow` | Bildkomprimierung beim Upload |
| `transformers` / `torch` | KI-Rassenerkennung (Hugging Face ViT) |
| `folium` | Interaktive Kartenansicht (OpenStreetMap) |
| `reportlab` | PDF-Export von Meldungen |
| `python-dotenv` | Laden der `.env`-Konfigurationsdatei |
| `fastapi` / `uvicorn` | Webserver für die Flet-Anwendung |