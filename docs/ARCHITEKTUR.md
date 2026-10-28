# Architektur

Technische Grundlage für alle Bausteine. Gameplay steht in [KONZEPT.md](KONZEPT.md).

## 1. Abhängigkeiten

**Pflicht** (läuft damit auf einem nackten Testserver):
- ESX Legacy 1.10+ (`es_extended`)
- `ox_lib` (Lua-Utilities: Callbacks, Points/Zones, Model-Loading, Cache. **Keine** ox_lib-UI)
- `ox_inventory`
- `ox_target`
- `oxmysql`
- OneSync (Infinity/Legacy)

**Optional** (über Bridges): Garagen (okokGarage, VMS), Fahrzeugschlüssel, Tank,
Banking/Society, Sky-Feuerwehr, Sky-Ambulance, Sky-MDT, Dokumentensystem,
Mechaniker, Versicherung, xsound/interact-sound, externe Skill-Systeme.

## 2. Ressourcen

```
[wr_scrap]/
├── wr_scrap_core/        Bridges, Config, Locale, DB-Migrationen, Item-/Materialkatalog,
│                         Logs, Rechte, Weltobjekte, Standorte, Skills, Public API
├── wr_scrap_ui/          Zentrale NUI (React + Vite + TS): alle Oberflächen, Minigames,
│                         Tablet, PC, Dokumente, Kran-HUD, 3D-Sound-Engine
├── wr_scrap_admin/       Platzierungs-Editor, Restore, Markt-/Debug-Werkzeuge, Log-Ansicht
├── wr_scrap_workshop/    Werkzeuge, PSA, Verletzungen, Tragen/Handling, Hebebühnen,
│                         Motorkran, Lager (Regale/Paletten/Stashes)
├── wr_scrap_identity/    VIN-Register, Historie, Seriennummern, Dokumente, Zulassungsstelle,
│                         Behörden-Werkzeuge, Beweise, Spuren, MDT-Bridge
├── wr_scrap_dismantle/   Bauteil-Modell, Zerlegeprozess, Flüssigkeiten, Hochvolt,
│                         legale Verschrottung, Karosse
├── wr_scrap_business/    Job/Firmen, Ränge, Lizenzen, Konto, Lohn, Betriebsbuch,
│                         Tablet-/PC-Apps, Markt
├── wr_scrap_processing/  Material-Engine, Waagen, Maschinen, Energie, Wertstoffhof
├── wr_scrap_crane/       Kransystem (auch eigenständig nutzbar)
├── wr_scrap_logistics/   Ladungssicherung, Transportfahrzeuge, Container, Aufträge, Export
├── wr_scrap_illegal/     Wissen, Chop-Shop, Hehler, VIN-Manipulation, Fälschung,
│                         Zusammenbau, Diebstähle, Geldwäsche
└── wr_scrap_assets/      (optional) Streams: lizenzgeprüfte Addon-Modelle und Props
install/                  ox_inventory-Items, Bilder, server.cfg-Beispiel, Installationsanleitung
tools/                    Generatoren (Itemliste, Icons), Hilfsskripte
docs/                     Konzept, Architektur, Roadmap, Entscheidungen, Tests, API
```

### Abhängigkeitsgraph
```
core        Basis für alles
ui          ← core
admin       ← core, ui
workshop    ← core, ui
identity    ← core, ui
dismantle   ← core, ui, workshop, identity      (business optional)
business    ← core, ui
processing  ← core, ui, workshop, business
crane       ← core, ui                          (eigenständig nutzbar)
logistics   ← core, ui, workshop, crane, business
illegal     ← core, ui, workshop, identity, dismantle
```
Jede Ressource deklariert ihre Abhängigkeiten im `fxmanifest.lua`. Optionale Module
dürfen fehlen: Fehlt z.B. `wr_scrap_illegal`, gibt es keine illegalen Funktionen,
alles andere läuft.

## 3. Verzeichnisaufbau einer Ressource

```
wr_scrap_xyz/
├── fxmanifest.lua
├── config/          *.lua (Defaults, ausführlich kommentiert, Deutsch)
├── locales/         de.lua, en.lua
├── shared/          Typen, Konstanten, reine Funktionen
├── client/          Client-Logik
├── server/          Server-Logik (Quelle der Wahrheit)
├── bridge/          (nur core) <kategorie>/<adapter>.lua
└── migrations/      0001_init.sql, 0002_….sql
```

## 4. Bridge-Schicht

Jede externe Abhängigkeit läuft über eine **Kategorie** mit festem Interface und
austauschbaren **Adaptern**.

```lua
-- config: 'auto' | Adaptername
Config.Bridges = {
    framework = 'auto', inventory = 'auto', target = 'auto',
    garage = 'auto', keys = 'auto', fuel = 'auto', banking = 'auto',
    fire = 'auto', ambulance = 'auto', mdt = 'auto', documents = 'auto',
    dispatch = 'auto', sound = 'auto', skills = 'auto', mechanic = 'auto',
}
```

- `auto` prüft per `GetResourceState`, welche bekannte Ressource läuft. Sonst
  kommt der **Standalone-Adapter**.
- Dritte registrieren eigene Adapter zur Laufzeit:
  `exports.wr_scrap_core:registerAdapter('garage', 'my_garage', impl)`.
- Jeder Adapter erfüllt das Interface vollständig. Nicht unterstützte Funktionen
  geben definierte Fallback-Werte zurück und loggen einmalig eine Warnung.

| Kategorie | Standalone-Fallback | Geplante Adapter |
|-----------|--------------------|------------------|
| framework | ESX Legacy | QBCore/Qbox (vorbereitet) |
| inventory | ox_inventory | weitere (vorbereitet) |
| target | ox_target | qb-target |
| garage | `owned_vehicles` + konfigurierbare Tabellen-/Spaltenliste | okokGarage, VMS |
| keys | keine Schlüssel (nur Besitz) | wasabi_carlock, qs-vehiclekeys u.a. |
| fuel | Native Fuel-Level / Statebag `fuel` | ox_fuel, LegacyFuel u.a. |
| banking | esx_addonaccount (`society_<job>`) | okokBanking u.a. |
| fire | Native Script-Fires | Sky-Feuerwehr |
| ambulance | Schaden + Bildschirm-/Audioeffekte | Sky, esx_ambulancejob, wasabi_ambulance |
| mdt | eigene Behörden-Oberfläche (VIN-Register) | Sky-MDT |
| documents | eigene Dokument-Items + NUI | euer Dokumentensystem |
| dispatch | eigene Benachrichtigung an konfigurierte Jobs | — (kein Auto-Alarm vorgesehen) |
| sound | eigene NUI-3D-Engine | xsound, interact-sound |
| skills | interne Skills | externe Skill-Systeme |
| mechanic | Exports für Gebrauchtteile | konkrete Mechaniker-Scripts |
| insurance | Events/Exports | — |

### Garagen-Adapter (kritisch: hartes Löschen)
Ein Garagen-Adapter muss können:
- `getVehicleByPlate(plate)`, `getOwner(plate)`
- `snapshot(plate)`: alle Zeilen aller betroffenen Tabellen als JSON
- `deleteVehicle(plate)`: transaktional aus allen Tabellen löschen
- `restore(snapshot)`: Wiederherstellung
- `insertVehicle(owner, props, plate)`: für die Zulassung

Der Standalone-Adapter arbeitet mit einer Config-Liste
`{ table = 'owned_vehicles', column = 'plate' }, { table = 'trunk_inventory', column = 'plate' } …`,
sodass jedes unbekannte Garagen-Script ohne Code angebunden werden kann.

## 5. Datenmodell (Übersicht)

Alle Tabellen haben das Präfix `wr_`. JSON-Spalten für flexible Zustände.

| Tabelle | Zweck |
|---------|-------|
| `wr_migrations` | Migrationsstand je Ressource |
| `wr_locations` | Standorte und Module aus dem Editor (Typ, Job, Koordinaten, Daten) |
| `wr_world_objects` | Persistente Weltobjekte (Würfel, Ballen, Container, Teile auf Regalen, Spuren …) |
| `wr_vehicle_registry` | VIN, Kennzeichen, Modell, Halter, Status, Plattform, VIN-Stellen-Zustand, verbaute Seriennummern, fehlende Teile |
| `wr_vehicle_history` | Ereignisse je VIN |
| `wr_vehicle_archive` | Snapshots gelöschter Fahrzeuge (Admin-Restore) |
| `wr_part_serials` | Seriennummer, Teiltyp, Herkunfts-VIN, aktueller Verbleib, Manipulationsstatus |
| `wr_dismantle_jobs` | Fahrzeuge in Zerlegung: Fahrzeug-Props, Position, entfernte Teile, Flüssigkeiten, Sicherungen |
| `wr_storage` | Regal- und Palettenplätze mit Inhalt |
| `wr_machines` | Maschinenzustand (Füllung, Temperatur, Verschleiß, Upgrades, Störung) |
| `wr_market` / `wr_market_history` | Kurse und Verlauf |
| `wr_companies` | Firmen-Einstellungen je Job |
| `wr_licenses` | Lizenzen je Spieler |
| `wr_logbook` | Betriebsbuch (editierbar) |
| `wr_raw_records` | Unveränderliche Rohdaten (Waagenprotokolle, Buchungen) für Ermittlungen |
| `wr_skills` | Skill-XP je Spieler und Tätigkeit |
| `wr_knowledge` | Gelerntes Wissen je Spieler |
| `wr_documents` | Echtheitsregister ausgestellter Dokumente |
| `wr_orders` | Aufträge (Abholung, Export, Hehler-Bestellungen) |
| `wr_containers` | Container in der Welt mit Inhalt |
| `wr_traces` | Spuren (Öllachen, Kontamination) |
| `wr_logs` | Admin-Logs |

Migrationen laufen beim Start automatisch, nur additiv (keine `DROP`s ohne
ausdrücklichen Major-Schritt), mit Backup-Hinweis in der Konsole.

## 6. Synchronisation & Autorität

- **Server = Quelle der Wahrheit** für Fortschritt, Items, Geld, Zustände.
- Client meldet Absichten (`startStep`, `finishStep` mit Minigame-Ergebnis).
  Der Server prüft Distanz, Werkzeug, Abhängigkeiten, Teil-Sperre, Mindestdauer
  und ob das Ergebnis plausibel ist. Dann vergibt er Items und schreibt den Zustand.
- **Statebags** an Entities für sichtbare Zustände (z.B. entfernte Teile am Fahrzeug,
  Hebebühnenhöhe, Maschinenstatus). Clients reagieren per `AddStateBagChangeHandler`.
- **Teil-Sperren**: Ein Teil kann nur von einem Spieler gleichzeitig bearbeitet werden
  (Server-Lock mit Timeout).
- **Rate-Limits** und Token pro Arbeitsschritt gegen Event-Spam und Replay.

## 7. Weltobjekte & Persistenz

- Statische Objekte (Regalinhalte, Würfel im Lager, Spuren) werden **clientseitig lokal**
  gespawnt, gestreamt nach Entfernung (billig, kein Netzwerk-Entity).
- Bewegt jemand ein Objekt (Kran, Stapler, Tragen), wird es zum **vernetzten Entity**.
  Nach dem Absetzen wird es wieder lokal und die Position wird gespeichert.
- Fahrzeuge in Zerlegung werden beim Serverstart aus `wr_dismantle_jobs` serverseitig
  gespawnt und bekommen ihren Zustand per Statebag.
- **Fahrzeugzustands-Overlay**: Spawnt irgendwo ein Fahrzeug, dessen VIN bzw. Kennzeichen
  fehlende Teile im Register hat (z.B. Kat geklaut, Tür fehlt), wendet der Client
  den Zustand erneut an.

## 8. Kran-Technik (Kurzfassung)

- Kranaufbau aus Props (Vanilla) oder Addon-Modell; Gelenke sind relativ attachte
  Objekte mit eigener Rotation.
- Seil über Rope-Natives, **nur lokal**. Simuliert wird beim Bediener: Haken bzw.
  Magnet ist ein vernetztes Physik-Entity, das der Bediener besitzt (Network Owner).
- Andere Clients bekommen Gelenkstellungen per Statebag (~10 Hz, interpoliert) und
  zeichnen das Seil lokal zwischen Auslegerspitze und Haken.
- Lastaufnahme: Magnet/Greifer verbindet die Last physikalisch mit dem Haken.
  Lastgrenze je Kran, Überlast → Blockade oder Seilriss.
- Steuerung aus der Kabine (Sitz-Attachment + Kamera) oder per Funkfernbedienung (Item).

## 9. NUI

- Eine zentrale NUI in `wr_scrap_ui` (React + Vite + TypeScript), **gebautes `dist/`
  wird mit eingecheckt**, damit Server-Betreiber kein npm brauchen.
- App-Registry: Jede Oberfläche (Minigame, Tablet-App, Dokument, HUD) ist eine App mit ID.
- Andere Ressourcen öffnen Apps per Export:
  `exports.wr_scrap_ui:open('minigame.screw', data, cbId)`. Ergebnisse kommen per Event zurück.
- Theme: Industrie/Werkstatt (CSS-Variablen, austauschbar).
- **3D-Sound**: Die NUI spielt Samples ab, Lautstärke und Panning berechnet der Client aus
  Entfernung und Blickrichtung. Andere Spieler hören Werkzeuge in der Nähe
  (Server verteilt Sound-Events an Spieler im Radius).
- Steuerung: Maus + Tastatur, kein `SetNuiFocus` ohne sichtbares UI, ESC beendet immer.

## 10. Items

- **Ein Katalog** (`wr_scrap_core/shared/catalog/*.lua`) als einzige Wahrheit für
  Materialien, Teile, Werkzeuge, PSA und Dokumente.
- Generator (`tools/`) erzeugt daraus:
  - `install/ox_inventory/items.lua` (zum Einfügen in `ox_inventory/data/items.lua`)
  - Icons `install/ox_inventory/images/*.png` (einheitlicher Stil, je Material eingefärbt)
  - `install/ox_inventory/IMAGES.md` (Liste aller Bilder mit Beschreibung zum Austauschen)
- **Alias-Mapping**: `Config.ItemAliases = { wr_copper_ingot = 'copper' }`.
  Alle Ressourcen sprechen intern nur die Katalog-ID, die Inventar-Bridge übersetzt.
- Metadaten: `kg`, `purity`, `condition`, `serial`, `originVin`, `platform`,
  `model`, `batch`, `durability`, `charge`.

## 11. Logs & Rechte

- `Log(category, level, message, data)` → `wr_logs` + optional Discord-Webhook
  pro Kategorie (`scrapping`, `restore`, `market`, `money`, `exploit`, `admin`).
- Rechte über `HasPermission(source, 'wr_scrap.admin.restore')`: ACE **oder**
  ESX-Gruppe laut Config.

## 12. Performance-Ziele (< 32 Spieler)

- Idle client-seitig ≤ 0,02 ms je Ressource (resmon). Keine Dauerschleifen ohne aktive Tätigkeit.
- Interaktionen über ox_target und ox_lib Points (distanzbasiert).
- Weltobjekte gestreamt (Radius per Config), Kran-Sync nur aktiv, wenn bewegt.

## 13. Konventionen

- Code, Bezeichner und Kommentare: **Englisch**. Doku und Config-Erklärungen: **Deutsch**.
- Ingame-Texte ausschließlich über Locales (`de`, `en`).
- Events: `wr_scrap:<ressource>:<aktion>`; Exports dokumentiert in `docs/API.md`.
- Keine fremden Addon-Modelle ohne eindeutige Lizenz im Repo.
- Jeder Baustein liefert eine Testcheckliste unter `docs/tests/`.
