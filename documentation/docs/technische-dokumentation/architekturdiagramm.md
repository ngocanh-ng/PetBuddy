# Architekturdiagramm

PetBuddy folgt einer **Schichtenarchitektur** mit klarer Trennung von UI, Geschäftslogik und Datenzugriff.

![Architekturdiagramm](../assets/diagrams/architecture.png)

> [Interaktive Version auf Mermaid Chart öffnen](https://mermaid.ai/d/3497aad6-0509-456e-8836-5054213118c5){:target="_blank"} 

---

## Schichten im Überblick

| Schicht | Verzeichnis | Aufgabe |
|---------|-------------|---------|
| **Einstiegspunkt** | `main.py` → `app/app.py` | Flet-App starten, FastAPI-Server für PDF-Downloads |
| **App-Steuerung** | `app/` | Navigation, Drawer, Auth-Flow, Dialoge |
| **UI** | `ui/` | Views, Components, Handler (je Feature) |
| **Services** | `services/` | Geschäftslogik, Supabase-Zugriff |
| **Utils** | `utils/` | Logging, PDF-Export, Kartenrendering, Validierung |
| **Externe Dienste** | — | Supabase (DB + Auth + Storage), Mapbox, Hugging Face |

---

## Datenfluss

1. `main.py` startet die Flet-App und initialisiert `PetBuddyApp`.
2. `PetBuddyApp` erstellt die UI-Views (`DiscoverView`, `PostForm`, `ProfileView`, `AuthView`) und übergibt den Supabase-Client.
3. Die UI-Views rufen **Services direkt** auf (z. B. `PostService.create()`, `SearchService.search()`).
4. Services kommunizieren mit den externen Diensten (Supabase, Mapbox, Hugging Face).
5. Ergebnisse fließen zurück an die UI, die das Rendering aktualisiert.

---
