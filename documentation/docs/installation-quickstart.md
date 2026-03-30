# Installation & Schnellstart

<a id="installation-schnellstart"></a>

Diese Anleitung ermöglicht eine reproduzierbare Inbetriebnahme von PetBuddy. Ein technisch versierter Dritter soll das Projekt ohne Rückfragen lauffähig machen können.

---

## Voraussetzungen

### Laufzeitumgebung

| Software | Version | Zweck |
|----------|---------|-------|
| **Python** | 3.13 oder höher | Laufzeitumgebung für die gesamte Anwendung |
| **pip** | (in Python enthalten) | Paketmanager für Python-Abhängigkeiten |
| **Git** | beliebig | Repository klonen |

!!! info "Python-Version prüfen"
    ```bash
    python3 --version   # macOS/Linux
    python --version    # Windows
    ```
    Falls die Version unter 3.13 liegt, lade Python von [python.org](https://www.python.org/downloads/) herunter.

### Datenbanksystem

PetBuddy verwendet **Supabase** – eine gehostete Open-Source-Plattform auf Basis von **PostgreSQL**. Eine lokale Datenbankinstallation ist **nicht erforderlich**. Supabase stellt Datenbank, Authentifizierung und Bildspeicher (Storage) als Cloud-Dienst bereit.

**Benötigt wird:**

- Ein kostenloses Supabase-Projekt unter [supabase.com](https://supabase.com)
- Die **Project URL** und der **Anon Key** (unter *Project Settings → API* im Supabase Dashboard)

### Sonstige Abhängigkeiten

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

---

## Schritt-für-Schritt-Anleitung

### 1. Repository klonen

```bash
git clone https://github.com/dietaube007/Projektarbeit_2026.git
cd Projektarbeit_2026
```

### 2. Virtuelle Umgebung erstellen und aktivieren

```bash
# macOS/Linux
python3 -m venv .venv
source .venv/bin/activate

# Windows (PowerShell)
python -m venv .venv
.venv\Scripts\Activate.ps1
```

### 3. Abhängigkeiten installieren

```bash
pip install -r requirements.txt
```

!!! info "Erstinstallation"
    Die Installation kann einige Minuten dauern, da unter anderem PyTorch (~2 GB) für die KI-Rassenerkennung heruntergeladen wird.

### 4. Konfigurationsdatei erstellen

```bash
cp .env.example .env
```

Danach die Pflicht-Werte in der `.env`-Datei eintragen (siehe Abschnitt [Konfiguration](#konfiguration)).

### 5. Anwendung starten

```bash
python main.py
```

Die App startet über Uvicorn und ist unter `http://localhost:8080` erreichbar. Lokal wird der Browser automatisch geöffnet.

### 6. Funktionscheck

| Prüfpunkt | Erwartetes Verhalten |
|------------|---------------------|
| Startseite öffnet sich | Browser zeigt die Entdecken-Seite |
| Anmeldung funktioniert | Login mit gültigen Supabase-Zugangsdaten erfolgreich |
| Meldungen werden geladen | Bestehende Meldungen erscheinen als Karten auf der Startseite |
| Geocoding liefert Vorschläge | Nur wenn `MAPBOX_TOKEN` gesetzt ist |

---

## Konfiguration

Die Konfiguration erfolgt über Umgebungsvariablen in der Datei `.env` im Projektverzeichnis. Die Vorlage `.env.example` enthält alle verfügbaren Variablen.

### Konfigurationsparameter

#### Pflichtparameter

| Variable | Beschreibung | Beispielwert |
|----------|-------------|--------------|
| `SUPABASE_URL` | URL des Supabase-Projekts. Zu finden im Supabase Dashboard unter *Project Settings → API → Project URL*. | `https://abc123.supabase.co` |
| `SUPABASE_ANON_KEY` | Öffentlicher API-Schlüssel (Anon Key) für clientseitige Operationen. Zu finden unter *Project Settings → API → Project API keys*. | `eyJhbGciOiJIUzI1Ni...` |
| `FLET_SECRET_KEY` | Geheimer Schlüssel für Flet-Upload-Sessions. Muss ein zufälliger, langer String sein (mind. 32 Zeichen empfohlen). | `mein-geheimer-schluessel-123!` |

#### Optionale Parameter

| Variable | Beschreibung | Standardwert |
|----------|-------------|--------------|
| `MAPBOX_TOKEN` | Access Token für die Mapbox Geocoding API. Aktiviert Orts-Autocomplete bei der Standorteingabe. Ohne Token läuft die App weiter, aber ohne Geocoding-Vorschläge. | kein Standardwert |
| `PORT` | Port, auf dem der Webserver lauscht. | `8080` |
| `LOG_LEVEL` | Detailgrad der Log-Ausgaben. Mögliche Werte: `DEBUG`, `INFO`, `WARNING`, `ERROR`. | `INFO` |
| `LOG_TO_FILE` | Aktiviert die Protokollierung in eine Log-Datei zusätzlich zur Konsolenausgabe. Mögliche Werte: `true`, `false`. | `true` |

### Sicherheitshinweise

!!! warning "Umgang mit Zugangsdaten und API-Keys"
    - Die `.env`-Datei enthält sensible Zugangsdaten und darf **niemals** in das Git-Repository committet werden. Sie ist bereits in `.gitignore` eingetragen.
    - Der **Supabase Anon Key** ist ein öffentlicher Schlüssel für clientseitige Anfragen. Er ist durch Row Level Security (RLS) auf Datenbankebene abgesichert. Den **Service Role Key** (voller Datenbankzugriff) niemals in der App verwenden.
    - Der **Mapbox Token** ist an ein kostenloses Kontingent gebunden (100.000 Anfragen/Monat). Bei Missbrauch kann der Token im Mapbox Dashboard widerrufen und neu erstellt werden.
    - Der **Flet Secret Key** sichert Upload-Sessions ab. Einen zufälligen Wert verwenden und nicht zwischen Umgebungen teilen.

---

## Häufige Fehlerbilder

| Fehlerbild | Ursache | Lösung |
|-----------|---------|--------|
| `Supabase-Konfiguration unvollständig` | `SUPABASE_URL` oder `SUPABASE_ANON_KEY` fehlt oder ist falsch | Werte im Supabase Dashboard prüfen und in `.env` eintragen |
| Kein Geocoding trotz laufender App | `MAPBOX_TOKEN` fehlt oder ist ungültig | Token unter [mapbox.com](https://account.mapbox.com/) erstellen und in `.env` eintragen |
| App startet nicht mit `python main.py` | Falsche Python-Version oder venv nicht aktiviert | `python3 --version` prüfen (≥ 3.13), venv mit `source .venv/bin/activate` aktivieren |
| `ModuleNotFoundError` | Abhängigkeiten nicht installiert | `pip install -r requirements.txt` ausführen |
