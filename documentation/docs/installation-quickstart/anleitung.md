# Schritt-für-Schritt-Anleitung

Die folgenden Schritte führen von einem leeren Rechner zur lauffähigen PetBuddy-Instanz. Alle Befehle sind für macOS, Linux und Windows angegeben – Abweichungen sind entsprechend markiert.

---

## 1. Repository klonen

```bash
git clone https://github.com/dietaube007/Projektarbeit_2026.git
cd Projektarbeit_2026
```

---

## 2. Virtuelle Umgebung erstellen und aktivieren

```bash
# macOS/Linux
python3 -m venv .venv
source .venv/bin/activate

# Windows (PowerShell)
python -m venv .venv
.venv\Scripts\Activate.ps1
```

---

## 3. Abhängigkeiten installieren

```bash
pip install -r requirements.txt
```

!!! info "Erstinstallation"
    Die Installation kann einige Minuten dauern, da unter anderem PyTorch (~2 GB) für die KI-Rassenerkennung heruntergeladen wird.

---

## 4. Konfigurationsdatei erstellen

```bash
# macOS/Linux
cp .env.example .env

# Windows (CMD)
copy .env.example .env

# Windows (PowerShell)
Copy-Item .env.example .env
```

Danach die Pflicht-Werte in der `.env`-Datei eintragen (siehe Abschnitt [Konfiguration](konfiguration.md)).

---

## 5. Anwendung starten

```bash
# macOS/Linux
python3 main.py

# Windows
python main.py
```

Die App startet über Uvicorn und ist unter `http://localhost:8080` erreichbar. Lokal wird der Browser automatisch geöffnet.

---

## 6. Funktionscheck

| Prüfpunkt | Erwartetes Verhalten |
|------------|---------------------|
| Startseite öffnet sich | Browser zeigt die Startseite |
| Anmeldung funktioniert | Login mit gültigen Supabase-Zugangsdaten erfolgreich |
| Meldungen werden geladen | Bestehende Meldungen erscheinen als Karten auf der Startseite |
| Geocoding liefert Vorschläge | Nur wenn `MAPBOX_TOKEN` gesetzt ist |
