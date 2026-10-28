# Roadmap

Das Projekt ist in **Bausteine** zerlegt, die nacheinander gebaut werden. Jeder
Baustein ist in sich testbar, wird direkt auf `main` gepusht und liefert eine
Testcheckliste unter `docs/tests/<id>.md`.

Status: ⬜ offen · 🟨 in Arbeit · ✅ fertig (getestet) · 🧪 fertig (ungetestet)

Testbasis für jeden Baustein: ESX Legacy + ox_lib + ox_inventory + ox_target + oxmysql,
**ohne** Sky, okok, VMS und Co. (Standalone-Adapter).

---

## Phase 0: Konzept ✅
| ID | Baustein | Status |
|----|----------|--------|
| B0 | Fragenkatalog, Konzept, Architektur, Roadmap, Entscheidungsprotokoll | ✅ |

---

## Phase 1: Fundament
Ziel: Alle Ressourcen starten sauber, Bridges funktionieren standalone, Items sind
importierbar, die NUI läuft, Objekte lassen sich platzieren.

| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B1.1 | Repo & Tooling | Ordnerstruktur `[wr_scrap]/`, fxmanifests, `install/`, `tools/`, Lua-Lint-Config, NUI-Build-Setup, Installationsanleitung | ⬜ |
| B1.2 | Core: Bridges | Bridge-Framework (auto-detect, Adapter-Registry), Adapter: ESX, ox_inventory, ox_target + Standalone-Fallbacks aller Kategorien | ⬜ |
| B1.3 | Core: Basisdienste | Config-Loader, Locales de/en, Migrations-Runner, Logs (DB + Discord), Rechte (ACE + ESX-Gruppen), Rate-Limit-/Token-Helfer | ⬜ |
| B1.4 | Item- & Materialkatalog | 60+ Materialien, Formen, Werkzeuge, PSA, Dokumente, Teile-Typen; Generator → ox_inventory-Itemliste, **generierte Icons**, Bilderliste; Alias-Mapping | ⬜ |
| B1.5 | UI-Grundgerüst | React/Vite/TS, Industrie-Theme, App-Registry, Messaging, Notifications, 3D-Sound-Engine, eingecheckter Build | ⬜ |
| B1.6 | Weltobjekte & Editor | Persistente Weltobjekte (Streaming, lokal ↔ vernetzt), Standorte/Module, **Ingame-Platzierungs-Editor**, `wr_scrap_admin`-Basis | ⬜ |

**Meilenstein M1**: Server startet fehlerfrei, Items importiert, Editor platziert und speichert Objekte.

---

## Phase 2: Werkstatt & Fahrzeugzerlegung (Kernerlebnis, Priorität)
Ziel: Ein Auto wird komplett, realistisch und kooperativ zerlegt, eingelagert und legal
verschrottet, einschließlich DB-Löschung.

| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B2.1 | Werkzeuge & PSA | Werkzeug-Items mit Props, Animationen, Partikeln, Sounds, Akku/Gas/Verschleiß; PSA anlegen | ⬜ |
| B2.2 | Verletzungen & Gefahren | Verletzungsarten, Ambulance-Bridge (Standalone-Effekte), Feuer-Bridge (native Fires), Stromschlag, Airbag | ⬜ |
| B2.3 | Minigames I + Skills | Schrauben, Hebeln, Hämmern, Ablassen/Absaugen; Skill-System (XP, Toleranzen); Server-Plausibilisierung | ⬜ |
| B2.4 | Minigames II | Flexen, Plasma, Schweißen, Hochvolt-Freischalten, Scheibe ausschneiden | ⬜ |
| B2.5 | Handling | Gewichtsklassen, Tragen (Props, Bewegungseinschränkung), Zwei-Personen-Tragen, Rollwagen, Paletten, Motorkran | ⬜ |
| B2.6 | Hebebühne & Arbeitsplatz | Hebebühnen/Montageböcke, Fahrzeug schieben/positionieren/fixieren, Fahrzeuge auf dem Hof persistent | ⬜ |
| B2.7 | VIN-Register & Dokument-Basis | VIN-Generator (Prüfziffer), Register, Historie, Fahrzeugzustands-Overlay; Dokument-Items + NUI (Unterschrift, Stempel), Echtheitsregister, Dokumenten-Bridge | ⬜ |
| B2.8 | Bauteil-Modell | Templates je Fahrzeugtyp/Klasse (inkl. Motorrad, LKW, Boot, Luftfahrzeug, E/Hybrid), Abhängigkeitsgraph, Materialzusammensetzung, Gewichte, Zustand, Seriennummern, Plattformen | ⬜ |
| B2.9 | Zerlegeprozess | Sichern, Trockenlegen (Tank-Bridge), Hochvolt, alle Demontageschritte, Koop + Teil-Sperren, Visualisierung, Gefahren, Spuren (Öllachen) | ⬜ |
| B2.10 | Karosse manuell | Karosse mit Flex/Plasma in Segmente schneiden → Blech-Props/Material | ⬜ |
| B2.11 | Lager | Regal-/Palettenplätze (sichtbar, persistent), Gitterbox-Stashes, Lagerplatznummern | ⬜ |
| B2.12 | Legale Verschrottung | Annahme, Blacklist, Schätzung, Verhandlung, Verwertungsauftrag, **hartes Löschen** über Garagen-/Schlüssel-Bridge (Standalone + Tabellenliste), Archiv, Admin-Restore, Dry-Run, Versicherungs-Events, Verwertungsnachweis + Abmeldung | ⬜ |

**Meilenstein M2**: Ein Spieler bringt sein Auto, verhandelt, unterschreibt. Das Auto
verschwindet aus der DB, wird in 45–60 min zerlegt, Teile liegen im Regal, die Karosse ist zerschnitten.

---

## Phase 3: Betrieb
| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B3.1 | Firmen & Job | Standort↔Job, Ränge, Firmenkonto-Bridge (Standalone: esx_addonaccount), Lohn/Prämien | ⬜ |
| B3.2 | Lizenzen | Lizenztypen, Prüfungen im Spiel **oder** Vergabe (pro Lizenz konfigurierbar) | ⬜ |
| B3.3 | Tablet & Büro-PC | App-Gerüst, Personal, Buchhaltung, Lagerverwaltung, **Betriebsbuch** + unveränderliche Rohdaten | ⬜ |
| B3.4 | Markt & Verkauf | Dynamischer Markt, Börsentafel, Verkauf an NPC-Händler, Gebrauchtteile-Verkauf, Firmenhandel mit Spieler-Jobs | ⬜ |

**Meilenstein M3**: Spielergeführter Betrieb mit Konto, Personal, Lizenzen, Markt.

---

## Phase 4: Materialverarbeitung
| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B4.1 | Material-Engine & Waagen | Formen, kg, Reinheit, Chargen; Plattformwaage, Fahrzeugwaage (Protokolle) | ⬜ |
| B4.2 | Maschinen-Framework | Echtzeit-Prozesse, Persistenz, Strom (Firmenkonto) + Diesel (Tank-Bridge, Generator), Verschleiß, Störungen, Reparatur, Upgrades | ⬜ |
| B4.3 | Sortierung | Sortierband-Minigame, Magnet-/Wirbelstromabscheider | ⬜ |
| B4.4 | Zerkleinern & Pressen | Schrottschere, Schredder, Ballenpresse, **Autopresse** (Würfel als Weltobjekt) | ⬜ |
| B4.5 | Spezial-Recycling | Kabelschäler, Granulator, Reifenrecycling, Batterie-Recycling (Blei, Li-Ion) | ⬜ |
| B4.6 | Schmelzen | Schmelzofen, Schlacke, Gießformen, Barren; Kat-Aufbereitung (Edelmetalle) | ⬜ |
| B4.7 | Wertstoffhof | Bürger-Annahme mit Waage + NPC-Automat; Mülltonnen/Container, Sperrmüll in der Welt, kaputte Geräte, Item-Mapping | ⬜ |

**Meilenstein M4**: Vom Bauteil bis zum Barren läuft alles über echte Maschinen.

---

## Phase 5: Kräne
| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B5.1 | Kran-Kern (Prototyp zuerst) | Seilphysik, Gelenke, Sync-Modell, Lastaufnahme Haken/Magnet/Greifer, Überlast/Seilriss | ⬜ |
| B5.2 | Magnetkran & Hallenkran | Stationärer Magnetkran (Props), Hallen-/Portalkran | ⬜ |
| B5.3 | Umschlagbagger | Fahrbarer Bagger mit Polypgreifer (Vanilla-Fallback + Addon-Slot) | ⬜ |
| B5.4 | LKW-Ladekran | Abstützen, Ausleger, Teleskop | ⬜ |
| B5.5 | Steuerung & Prüfung | Kabinenansicht, Funkfernbedienung, Kran-HUD, Kranschein-Prüfung | ⬜ |

**Meilenstein M5**: Karossen per Magnetkran in die Presse, Würfel auf den Stapel.

---

## Phase 6: Logistik
| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B6.1 | Ladungssystem | Physisches Absetzen, Zurrpunkte, Gurte/Ketten, Verrutschen/Herunterfallen, Gewicht → Fahrverhalten | ⬜ |
| B6.2 | Abschlepper & Hofgeräte | Flatbed/Abschlepper (Fahrzeuge laden), Stapler, Radlader, Teleskoplader | ⬜ |
| B6.3 | Container | Abrollcontainer, Absetzmulden (persistent, Inhalt in kg), Hakenlift-/Absetz-Mechanik | ⬜ |
| B6.4 | Fernverkehr & Abnehmer | Sattelzug + Auflieger, Lieferung an die Hütte, Hafen-Export | ⬜ |
| B6.5 | Auftragssystem | Abholung, Containertausch, Abriss/Entrümpelung vor Ort, NPC-Wracks, Exporte mit Termin | ⬜ |

**Meilenstein M6**: Kompletter Kreislauf: Auftrag → Abholung → Verarbeitung → Export.

---

## Phase 7: Identität & Behörden
| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B7.1 | VIN vollständig | Mehrere VIN-Stellen, Seriennummern verbauter Teile, Mechaniker-Bridge | ⬜ |
| B7.2 | Zulassungsstelle | Spieler-Behörde: Terminal, Papier- und Fahrzeugprüfung, Zulassung → Garagen-Bridge, Kennzeichen | ⬜ |
| B7.3 | Ermittlungswerkzeuge | VIN-Prüfung, Ätzmittel, Historienabfrage, Teile-Rückverfolgung, Probenahme, Beweis-Items, Razzia | ⬜ |
| B7.4 | Akten-Schnittstellen | MDT-Bridge (Sky + generisch), Betriebsbuch-Einsicht, Versicherungs-Events, Public API | ⬜ |

**Meilenstein M7**: Polizei kann einen Fall vom Teil bis zum Täter ermitteln.

---

## Phase 8: Illegaler Teil
| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B8.1 | Wissenssystem | Notizen-Items, NPC-Kontakte, Fundstücke, Spieler-zu-Spieler-Lehre; versteckte Interaktionen | ⬜ |
| B8.2 | Chop-Shop | Config-Werkstätten, Schalter für mobile Ausrüstung, gestohlene Spieler-/NPC-Fahrzeuge, Löschlogik | ⬜ |
| B8.3 | Hehler & Schwarzmarkt | NPC-Hehler, Bestellaufträge, heiße Ware | ⬜ |
| B8.4 | VIN-Manipulation | Abschleifen, Schlagzahlen, Doubletten | ⬜ |
| B8.5 | Fälschungen | Papiere (Vordrucke, Stempel, Drucker, Unterschrift), Kennzeichen prägen | ⬜ |
| B8.6 | Zusammenbau | Plattformen, Montagebock, Einbau aller Teile, Zustand, unzugelassenes Fahrzeug | ⬜ |
| B8.7 | Diebstähle | Kat, Kupfer/Kabel, Räder (auf Steinen), Metallobjekte | ⬜ |
| B8.8 | Geldwäsche | Fingierte Ankäufe/Verkäufe, Limits, Buchungslücken | ⬜ |

**Meilenstein M8**: Gestohlenes Auto → zerlegt → neues Auto aus Teilen → gefälschte
Papiere → bei der Behörde zugelassen. Jeder Schritt hinterlässt Spuren.

---

## Phase 9: Abschluss
| ID | Baustein | Inhalt | Status |
|----|----------|--------|--------|
| B9.1 | Addon-Modelle | Lizenzgeprüfte Modelle in `wr_scrap_assets`, Linkliste für den Rest, Config-Slots | ⬜ |
| B9.2 | Balancing & Audit | Verdienst-Anker, Zeiten, Preise; Performance-Profiling; Security-Audit | ⬜ |
| B9.3 | Doku | Installationsanleitung, Config-Referenz, `docs/API.md` für Adapter-Entwickler, Englisch vollständig | ⬜ |

---

## Arbeitsweise pro Baustein
1. Offene Detailfragen zum Baustein klären (falls vorhanden).
2. Implementieren (Code EN, Doku/Config DE, Locales de/en).
3. Testcheckliste `docs/tests/<id>.md` schreiben.
4. Commit + Push auf `main`, Roadmap-Status auf 🧪 setzen.
5. Nach eurem Test: Fehler mit F8-Konsole bzw. Server-Log melden → Fix → ✅.
