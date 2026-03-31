# Datenbankmodell

Die Daten liegen in **Supabase (PostgreSQL)**. Nachfolgend das logische Datenmodell.

![ER-Diagramm](../assets/diagrams/er-diagram.svg)

> [Interaktive Version auf dbdiagram.io öffnen](https://dbdiagram.io/d/PetBuddy_aktualisiert-69cab413fb2db18e3b3cb630){:target="_blank"} 

---

## Lookup-Tabellen

| Tabelle | Inhalt |
|---------|--------|
| `post_status` | „Vermisst", „Fundtier", „Wiedervereint" |
| `species` | „Hund", „Katze", „Kleintier" |
| `breed` | Rassen je Tierart (dynamisch geladen) |
| `color` | Fellfarben |
| `sex` | „Männlich", „Weiblich" |

---

## Storage Buckets (Supabase)

| Bucket | Zweck | Limits |
|--------|-------|--------|
| `pet-images` | Meldungsfotos | max. 10 MB, komprimiert auf 1920×1920 px |
| `profile-images` | Profilbilder | — |

---
