# Entscheidungsprotokoll

Ergebnis der Konzeptrunde (60 Hauptfragen + Nachfragen) vom 28.10.2026.
Dieses Dokument ist die verbindliche Grundlage. Ändert sich eine Entscheidung,
wird sie hier nachgetragen (mit Datum), statt stillschweigend im Code abzuweichen.

## Technische Basis

| # | Thema | Entscheidung |
|---|-------|--------------|
| 1 | Framework | ESX Legacy 1.10+ |
| 2 | Inventar | ox_inventory (Metadaten für Zustand, Gewicht, Seriennummer, Herkunfts-VIN) |
| 3 | Interaktion | ox_target (Bone-Targeting an Fahrzeugteilen) |
| 4 | UI | Komplett eigene NUI mit **React + Vite + TypeScript** |
| 5 | Datenbank | oxmysql |
| 6 | Garagen | Bridge; aktuell **okokGarage**, künftig **VMS**; Standalone-Fallback `owned_vehicles` + frei konfigurierbare Tabellenliste |
| 6a | Fahrzeugschlüssel | Bridge mit Adaptern für gängige Systeme (welches genutzt wird, ist unbekannt) |
| 7 | Dispatch | Eigene Benachrichtigung, keine Dispatch-Abhängigkeit |
| 8 | Zielgruppe | Eigener Server, aber modular. Wird **quelloffen** und soll nativ von jedem integrierbar sein |
| 9 | RP-Stil | **Hardcore RP**; alle Zeiten per Config skalierbar |
| 10 | Spielerzahl | unter 32 |
| — | Game-Build / OneSync | Unbekannt: OneSync wird vorausgesetzt, Build wird zur Laufzeit erkannt, Features mit Build-Abhängigkeit haben Fallbacks |
| — | Testumgebung | Eigener Testserver **nur mit Standard-Ressourcen** (ESX, ox_*). Ohne Sky und Co. muss alles standalone laufen, Schnittstellen müssen offen sein |
| — | Lieferung | Direkt auf `main`, Updates werden selbst gezogen |
| — | Lizenz | GPL-3.0 |
| — | Frameworks | ESX zuerst; alle Zugriffe über Bridge-Schicht, damit QBCore/Qbox/andere Inventare später als Adapter ergänzt werden können |
| — | Ressourcen-Struktur | Mehrere Ressourcen, Präfix **`wr_`** (Wolfsrudel) |
| — | Item-Namen | Präfix + Alias-Mapping (jedes Material kann per Config auf ein fremdes Item umgebogen werden) |
| — | Sprache | Code und Kommentare Englisch, Doku Deutsch; Ingame **Deutsch + Englisch** über Locale-Dateien |
| — | DB-Updates | Automatische Migrationen beim Ressourcenstart (Versionstabelle, nie destruktiv) |
| — | Admin-Rechte | ACE-Permissions + ESX-Gruppen per Config |
| — | Logs | DB-Log-Tabelle (im Admin-Panel durchsuchbar) + Discord-Webhooks |
| — | Sounds | Eigene 3D-Sounds über die NUI (CC0), Bridge zu xsound/interact-sound |
| — | NUI-Design | Industrie/Werkstatt-Look (Metall, Warnfarben, Klemmbrett, robustes Tablet) |
| — | Addon-Modelle | Kostenlose Modelle suchen, ein bis zwei günstige sind okay. **Vanilla-Fallback ist Pflicht.** Ins Repo nur eindeutig lizenzierte Modelle, sonst Linkliste + vorbereiteter Stream-Ordner |

## Standorte, Job & Firma

| # | Thema | Entscheidung |
|---|-------|--------------|
| 11 | MLO | MLO-unabhängig; alles über Config bzw. Ingame-Editor platzierbar |
| 12 | Standorte | Beliebig viele, per Config/Editor |
| — | Platzierung | **Ingame-Editor + Config** (Config liefert Defaults, Editor speichert in DB) |
| 13 | Legal/Illegal | Legaler Job + illegale Seite getrennt |
| 47 | Kartell | **Nicht Teil des Systems.** Keine Org-Verwaltung, keine Ränge/Reputation/Kasse |
| — | Illegaler Zugang | **Jeder darf.** Es gibt keine Rechteprüfung, nur Wissen, Ausrüstung und Orte begrenzen |
| 14 | Betrieb | Spielergeführt mit Chef, Bossmenü, Firmenkonto |
| — | Firmen | Jeder Standort wird einem ESX-Job zugeordnet: eine Firma für alles **oder** mehrere Konkurrenzfirmen möglich |
| 15 | Ränge | Ränge + Lizenzen (Kranschein, Schweißschein, Staplerschein, LKW, Hochvolt) |
| — | Lizenzerwerb | Pro Lizenz konfigurierbar: praktische Prüfung **oder** Vergabe durch den Chef |
| 16 | Bezahlung | Erlöse aufs Firmenkonto, Chef zahlt Löhne und Prämien |
| — | Firmenkonto | Bridge (System unbekannt), Fallback esx_addonaccount |
| — | Aufträge/Verwaltung | Firmen-Tablet (Item) + Büro-PC am Hof |
| — | Verdienst | Frei einstellbar (Config-Ankerwerte) |

## Materialien & Verarbeitung

| # | Thema | Entscheidung |
|---|-------|--------------|
| 17 | Materialtiefe | **Realistisch, 50+ Materialien** (Legierungen, Kunststoffsorten, Kat-Edelmetalle, E-Schrott …) |
| — | Auslieferung | Vorkonfiguriert als ox_inventory-Itemliste zum Importieren + **generierte, einheitliche Icons** + Liste aller Bilddateien zum späteren Austausch |
| 18 | Qualität | Reinheit in % als Item-Metadaten, bestimmt den Preis |
| 19 | Mengen | Mix: Rohschrott/Granulat in kg (Waagen), Endprodukte als Stück (Barren, Ballen, Würfel) |
| 20 | Quellen | Fahrzeugverschrottung, Bürger-Anlieferung, Abholaufträge (Container), Abriss-/Entrümpelungsaufträge |
| 21 | Maschinen | Schredder, Schrottschere, Schmelzofen + Gießformen, Ballenpresse, Autopresse, Kabelschäler, Granulator |
| 22 | Maschinenlogik | Echtzeit-Prozess (befüllen, überwachen, Störungen, entnehmen, läuft parallel weiter) |
| — | Energie | Diesel (mobile Geräte, Generator, per Kanister/Tankwagen) + Stromkosten aufs Firmenkonto |
| — | Tank-Script | Bridge (unbekannt), Fallback auf Native-Fuel-Level |
| 23 | Verschleiß | Verschleiß, Defekte, Reparatur per Minigame + Ersatzteile **und** kaufbare Upgrades |
| 24 | Sortierung | Manuelles Sortierband (bessere Reinheit) **oder** Magnet-/Wirbelstromabscheider (schneller, schlechter) |
| 56 | Wertstoffhof | Annahme mit Waage und Prüfung durch Mitarbeiter, ohne Personal ein NPC-Automat zu schlechteren Preisen |
| — | Bürger-Quellen | Mülltonnen/Container durchsuchen, Item-Mapping zu anderen Scripts, kaputte Geräte, Sperrmüll in der Welt |

## Fahrzeugzerlegung

| # | Thema | Entscheidung |
|---|-------|--------------|
| 25 | Detailgrad | Sehr detailliert (25–40 Schritte pro PKW) |
| 26 | Werkzeuge | Echte Props + Animationen, Werkzeuge mit Akku/Gas/Verschleiß |
| 27 | Minigames | Eigenes Minigame pro Werkzeug |
| 28 | Reihenfolge | Realistische Abhängigkeiten werden erzwungen |
| 29 | Flüssigkeiten | Ja, mit Konsequenzen (Brand, Airbag, Stromschlag). Feuer über **Bridge zum Sky-Feuerwehr-Script**, ohne Sky native Feuer |
| 29a | Behörden | **Keine automatischen Strafen/Kontrollen.** Nur Spuren, die Polizei/DOJ finden *können*: physische Spuren (Öllachen, Kontamination), Beweis-Items, MDT-/Akten-Schnittstelle, Betriebsbuch |
| 29b | Betriebsbuch | Realistisch, man kann als Werkstatt "drum herum" arbeiten. Es nimmt Ermittlern keine Arbeit ab, sie müssen sich in ein für sie fremdes System eindenken |
| 30 | Arbeitsplatz | Physisch: schleppen/schieben/heben auf Hebebühne bzw. Montagebock |
| 31 | Karosse | Wahlweise Autopresse (Würfel) oder manuell flexen (sortenreiner, mehr Erlös) |
| 32 | Koop | Mehrere Spieler gleichzeitig an verschiedenen Teilen; Motor/Getriebe nur zu zweit oder mit Motorkran |
| — | Dauer | **45–60 Minuten** allein für einen Mittelklasse-PKW (Default) |
| — | Schwere Teile | Gewichtsklassen: klein ins Inventar, mittel als Prop in den Händen, schwer nur mit Motorkran/Rollwagen/Palette/zu zweit |
| — | Lager | Physische Regale/Paletten (sichtbar, persistent) + Stashes für Kleinteile, Lagerplatznummern |
| — | Arbeitsschutz | PSA (Schweißhelm, Handschuhe, Gehörschutz, Sicherheitsschuhe, Atemschutz) + Verletzungen. Ambulance-Bridge (Sky genutzt, offen für andere) |
| — | Fahrzeugtypen | **Alles, was nicht auf der Blacklist steht**: PKW, SUV, Transporter, Motorräder, Quads, LKW, Busse, Boote, Flugzeuge, Helis |
| — | E-/Hybridautos | Hochvolt-Prozess (spannungsfrei schalten, Hochvoltschein, Akkubrand, Li/Co/Ni-Recycling) |
| 57 | Persistenz | Alles überdauert Restarts: halb zerlegte Autos, Maschinenzustände, Weltobjekte, Spuren, Lager |
| 58 | Skills | Skills pro Tätigkeit verbessern Toleranz, Ausschuss, Reinheit und Fälschungsqualität (kein Freischalten) |

## Legale Verschrottung & Datenbank

| # | Thema | Entscheidung |
|---|-------|--------------|
| 33 | Zulässig | Eigene Fahrzeuge, mit Vollmacht, NPC-Wracks/Aufträge, beschlagnahmte Fahrzeuge von Polizei/Justiz |
| — | Blacklist | Config-Liste (Modelle, Klassen, Kennzeichen-Muster, Jobs) |
| 34 | Löschen | **Hart löschen** aus allen Tabellen (Garage, Schlüssel, Kofferraum …) + Snapshot im Admin-Archiv + **Admin-Restore** |
| 35 | Ankauf | Freie Verhandlung, das Script zeigt einen geschätzten Schrottwert |
| 36 | Dokumente | Verwertungsauftrag (Unterschrift), Verwertungsnachweis, Abmeldung |
| — | Dokumentensystem | Euer eigenes System wird später angebunden. Bis dahin eigene Dokument-Items, offen für weitere Systeme (Bridge) |
| — | Versicherung | Nur Events/Exports, kein eigenes Versicherungssystem |

## Illegaler Teil

| # | Thema | Entscheidung |
|---|-------|--------------|
| 37 | Fahrzeugquelle | Freie Anlieferung gestohlener Autos + Bestellaufträge (von NPC-Hehlern, da kein Kartellsystem) |
| 38 | Spielerautos | Werden zerstört (hart gelöscht), Versicherungs-Event, Teile über Seriennummern rückverfolgbar |
| 39 | Orte | Per Config definierte Werkstätten. **Config-Schalter**, ob zusätzlich mobile Ausrüstung überall erlaubt ist (Default: nur feste Orte) |
| 40 | Seriennummern | Hauptteile (Motor, Getriebe, Karosse/Rahmen, Kat, Türen, Airbag-Module) |
| 41 | Zusammenbau | Komplett aus Einzelteilen (Rohkarosse → alle Teile einbauen) |
| 42 | Kompatibilität | Plattform-System (Antrieb plattformweit, Karosserieteile modellgleich) |
| 43 | VIN | Eigene VIN-Tabelle mit Historie, verknüpft über Kennzeichen, unabhängig vom Garagen-Script |
| — | Zulassung | Nur über **Spieler-Behörde** (Job per Config), mit Papieren |
| 44 | Fälschung | VIN abschleifen + neu einschlagen, gefälschte Papiere, falsche Kennzeichen, Doubletten |
| 45 | Polizei | VIN-Prüfung am Fahrzeug, Historienabfrage, Teile-Rückverfolgung, Razzia/Beweissicherung |
| — | MDT | Wahrscheinlich Sky, Bridge + generische Exports |
| 46 | Alarm | **Kein automatischer Alarm.** Polizei ermittelt selbst |
| — | Wissen | Mix: Einfaches ist frei, Komplexes (VIN einschlagen, Papiere, Doubletten) braucht Wissen. Das Spiel verrät nichts |
| — | Wissenserwerb | Notizen/Anleitungen als Items, NPC-Kontakte, Fundstücke in der Welt, von Spielern beigebracht |
| 48 | Geldwäsche | Fingierte Ankäufe über das Firmenkonto, erzeugt auffindbare Buchungslücken |
| — | Extras | Kat-Diebstahl, Kupfer-/Kabeldiebstahl, Felgen-/Reifendiebstahl, Metalldiebstahl an Objekten |

## Transport, Kräne, Wirtschaft

| # | Thema | Entscheidung |
|---|-------|--------------|
| 49 | Fahrzeuge | Abschlepper/Flatbed, Abrollcontainer/Absetzkipper, Sattelzug + Schrottauflieger, Hofgeräte (Stapler, Radlader, Teleskoplader) |
| 50 | Beladung | Physisch mit Kran/Stapler + Ladungssicherung (ungesichert verrutscht/fällt) |
| 51 | Kräne | Magnetkran, Umschlagbagger mit Polypgreifer, Hallen-/Motorkran, LKW-Ladekran |
| 52 | Kran-Technik | Eigenes Kransystem (Seilphysik, Magnet/Greifer, synchronisiert) |
| — | Kransteuerung | Kabine **und** Funkfernbedienung |
| 53 | Preise | Dynamischer Markt (Angebot/Nachfrage + Trend, Börsentafel) |
| 54 | Abnehmer | NPC-Hütte/Großhändler, Hafen-Export, andere Spieler-Jobs, Schwarzmarkt/Hehler |
| 55 | Mechaniker | Gebrauchtteile per Bridge verbaubar; Seriennummer wird am Fahrzeug gespeichert |
| 59 | Logs | DB + Discord |
| 60 | Start | Nach dem Fundament zuerst die **Fahrzeugzerlegung** |

## Noch zu klären (blockiert nichts, wird beim jeweiligen Baustein nachgefragt)

- Exakte Ressourcennamen und Exports der Sky-Scripts (Feuerwehr, Ambulance, MDT)
- Tabellenstruktur von okokGarage und VMS-Garagen (für die Lösch-/Zulassungs-Adapter)
- Welches Fahrzeugschlüssel-, Tank- und Banking-Script tatsächlich läuft
- Name/API eures Dokumentensystems
- Welche Jobs als Zulassungsbehörde bzw. Polizei/DOJ gelten (Config-Defaults: `police`, `doj`)
- Konkrete Ankerwerte fürs Balancing ($/h legal und illegal)
