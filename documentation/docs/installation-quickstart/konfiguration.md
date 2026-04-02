# Konfiguration

Die Konfiguration erfolgt über Umgebungsvariablen in der Datei `.env` im Projektverzeichnis. Die Vorlage `.env.example` enthält alle verfügbaren Variablen.

---

## Konfigurationsparameter

### Pflichtparameter

| Variable | Beschreibung | Beispielwert |
|----------|-------------|--------------|
| `SUPABASE_URL` | URL des Supabase-Projekts. Zu finden im Supabase Dashboard. | `https://abc123.supabase.co` |
| `SUPABASE_ANON_KEY` | Öffentlicher API-Schlüssel (Anon Key) für clientseitige Operationen. Zu finden unter *Project Settings → API Keys → Legacy anon, service_role API keys → anon public*. | `eyJhbGciOiJIUzI1Ni...` |
| `FLET_SECRET_KEY` | Geheimer Schlüssel für Flet-Upload-Sessions. Muss ein zufälliger, langer String sein (mind. 32 Zeichen empfohlen). | `mein-geheimer-schluessel-123!` |

### Optionale Parameter

| Variable | Beschreibung | Standardwert |
|----------|-------------|--------------|
| `MAPBOX_TOKEN` | Access Token für die Mapbox Geocoding API. Erhältlich im Mapbox Dashboard unter *Account → Access tokens* (https://account.mapbox.com/access-tokens/). Aktiviert Orts-Autocomplete bei der Standorteingabe. Ohne Token läuft die App weiter, aber ohne Geocoding-Vorschläge. | kein Standardwert |
| `PORT` | Port, auf dem der Webserver lauscht. | `8080` |
| `LOG_LEVEL` | Detailgrad der Log-Ausgaben. Mögliche Werte: `DEBUG`, `INFO`, `WARNING`, `ERROR`. | `INFO` |
| `LOG_TO_FILE` | Aktiviert die Protokollierung in eine Log-Datei zusätzlich zur Konsolenausgabe. Mögliche Werte: `true`, `false`. | `true` |

---

## Sicherheitshinweise

!!! warning "Umgang mit Zugangsdaten und API-Keys"
    - Die `.env`-Datei enthält sensible Zugangsdaten und darf **niemals** in das Git-Repository committet werden. Sie ist bereits in `.gitignore` eingetragen.
    - Der **Supabase Anon Key** ist ein öffentlicher Schlüssel für clientseitige Anfragen. Er ist durch Row Level Security (RLS) auf Datenbankebene abgesichert. Den **Service Role Key** (voller Datenbankzugriff) niemals in der App verwenden.
    - Der **Mapbox Token** ist an ein kostenloses Kontingent gebunden (100.000 Anfragen/Monat). Bei Missbrauch kann der Token im Mapbox Dashboard widerrufen und neu erstellt werden.
    - Der **Flet Secret Key** sichert Upload-Sessions ab. Einen zufälligen Wert verwenden und nicht zwischen Umgebungen teilen.
