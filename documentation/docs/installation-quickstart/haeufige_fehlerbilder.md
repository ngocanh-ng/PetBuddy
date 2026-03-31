# Häufige Fehlerbilder

Diese Seite listet typische Probleme, die beim Einrichten oder Starten von PetBuddy auftreten können, zusammen mit der wahrscheinlichen Ursache und der empfohlenen Lösung.

| Fehlerbild | Ursache | Lösung |
|-----------|---------|--------|
| `Supabase-Konfiguration unvollständig` | `SUPABASE_URL` oder `SUPABASE_ANON_KEY` fehlt oder ist falsch | Werte im Supabase Dashboard prüfen und in `.env` eintragen |
| Kein Geocoding trotz laufender App | `MAPBOX_TOKEN` fehlt oder ist ungültig | Token unter [mapbox.com](https://account.mapbox.com/) erstellen und in `.env` eintragen |
| App startet nicht mit `python main.py` | Falsche Python-Version oder venv nicht aktiviert | `python3 --version` prüfen (≥ 3.13), venv aktivieren: macOS/Linux `source .venv/bin/activate`, Windows `.venv\Scripts\Activate.ps1` |
| `ModuleNotFoundError` | Abhängigkeiten nicht installiert | `pip install -r requirements.txt` ausführen |
| KI-Modell lädt nicht beim ersten Start | Keine Internetverbindung oder Hugging Face nicht erreichbar | Internetverbindung prüfen und App neu starten – das Modell wird nur beim allerersten Start heruntergeladen (~2 GB) |
| `OSError: [Errno 48] Address already in use` | Port 8080 ist bereits durch einen anderen Prozess belegt | Prozess auf Port 8080 beenden (macOS/Linux: `lsof -i :8080`, dann `kill <PID>`; Windows: `netstat -ano \| findstr :8080`, dann `taskkill /PID <PID> /F`) oder in `.env` einen anderen Port setzen (`PORT=8081`) |
