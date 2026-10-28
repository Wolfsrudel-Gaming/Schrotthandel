# Konzept: Schrotthandel (wr_scrap)

Ein Hardcore-RP-Schrottplatz für ESX: legaler Recycling- und Verwertungsbetrieb,
der mit echter Handarbeit funktioniert, dazu eine illegale Seite, die jeder
nutzen kann, der weiß wie.

> Alle Einzelentscheidungen stehen in [ENTSCHEIDUNGEN.md](ENTSCHEIDUNGEN.md),
> die technische Umsetzung in [ARCHITEKTUR.md](ARCHITEKTUR.md),
> die Reihenfolge der Bausteine in [ROADMAP.md](ROADMAP.md).

---

## 1. Leitprinzipien

1. **Handwerk statt Tastendruck.** Jede Tätigkeit ist eine eigene Interaktion mit
   Werkzeug, Prop, Animation, Sound und Minigame. Es gibt keine "E drücken, 10 s
   warten"-Abkürzung.
2. **Realismus vor Komfort.** Reihenfolgen, Gewichte, Gefahren, Wartung und Papierkram
   sind echt. Alles ist per Config skalierbar, aber der Default ist Hardcore.
3. **Verschrotten heißt verschrotten.** Ein verwertetes Fahrzeug existiert danach in
   keiner Tabelle mehr. Admins können es nur über ein separates Archiv zurückholen.
4. **Das Spiel verrät nichts.** Illegale Möglichkeiten haben keine Menüs und keine
   Labels. Sie ergeben sich aus normalen Werkzeug-Interaktionen und Wissen.
5. **Ermittler müssen ermitteln.** Es gibt keine automatischen Strafen oder Alarme,
   nur Spuren, Register und Akten, die man finden und verstehen muss.
6. **Läuft standalone.** ESX Legacy, ox_lib, ox_inventory, ox_target und oxmysql
   genügen. Alles andere (Sky, okok, VMS, Banking, Tank …) hängt über Bridges
   mit Fallback dran.
7. **Offen und andockbar.** Quelloffen (GPL-3.0), dokumentierte Exports und Events,
   Adapter können von Dritten registriert werden.
8. **Server-autoritativ.** Werte, Items, Fortschritt und Geld entstehen nur auf dem Server.

---

## 2. Akteure

| Akteur | Was er tut |
|--------|------------|
| **Schrotthandel-Mitarbeiter** | Zerlegen, Sortieren, Maschinen bedienen, Kran/LKW fahren, Annahme am Wertstoffhof, Aufträge erledigen |
| **Chef** | Firmenkonto, Löhne, Personal, Lizenzen, Preise im Ankauf, Upgrades, Betriebsbuch |
| **Bürger** | Bringen Wertstoffe, lassen eigene Autos verschrotten, kaufen Gebrauchtteile |
| **Jeder mit Wissen** | Chop-Shop, VIN-Manipulation, Fälschungen, Zusammenbau, Kat-/Kupfer-/Räderdiebstahl, Geldwäsche |
| **Polizei / DOJ** | VIN-Prüfung, Historienabfrage, Teile-Rückverfolgung, Razzien, Proben, Akteneinsicht |
| **Zulassungsbehörde (Spieler)** | Prüft Papiere und Fahrzeug, lässt Fahrzeuge zu, vergibt Kennzeichen |
| **Mechaniker** | Verbaut Gebrauchtteile (Seriennummern wandern ins Fahrzeug) |
| **Feuerwehr / Rettung** | Reagieren auf Brände (Kraftstoff, Akku) und Arbeitsunfälle |

---

## 3. Standorte

Standorte sind frei definierbar (Config-Defaults + Ingame-Editor) und jeweils
einem ESX-Job zugeordnet. Ein Standort besteht aus **Modulen**, die man beliebig
kombiniert:

- **Annahme**: Fahrzeugwaage, Plattformwaage, Annahmeschalter, Büro-PC
- **Zerlegehalle**: Hebebühnen, Montageböcke, Motorkran, Werkbänke, Auffangwannen, Absauggeräte
- **Lager**: Regalplätze, Palettenstellplätze, Gitterboxen (Stashes)
- **Verarbeitung**: Maschinen (siehe Abschnitt 10)
- **Kranbereich**: stationäre Kräne, Umschlagbagger-Zone, Haufen/Boxen
- **Containerplatz**: Abrollcontainer und Absetzmulden
- **Wertstoffhof**: Bürger-Annahme mit Containern je Fraktion
- **Tankstelle/Generator**: Diesel für Hofgeräte
- **Illegale Werkstatt** (nur Config, nicht im Editor beschriftet)

Externe Ziele: NPC-Hütte (Metallabnehmer), Hafen (Export), Kunden-Containerplätze,
Abrissobjekte, Hehler-Treffpunkte, Zulassungsstelle (Spieler-Terminal).

---

## 4. Materialsystem

### 4.1 Formen eines Materials
Jedes Material durchläuft Formen mit eigenem Item:

`Bauteil` → `Rohschrott (kg, Mischung)` → `sortiert (kg)` → `zerkleinert/Granulat (kg)` → `Barren/Ballen/Würfel (Stück)`

Metadaten: `kg` (bei Schüttgut), `purity` (0–100 %), `origin` (optional: Herkunft für Ermittlungen),
`batch` (Charge, wichtig für das Betriebsbuch).

### 4.2 Materialkatalog (Auszug, 60+ Einträge)

| Gruppe | Materialien |
|--------|-------------|
| **Eisen & Stahl** | Stahlschrott, Karosserieblech, Profil-/Trägerschrott, Federstahl, Gusseisen, Edelstahl V2A, Edelstahl V4A, Fe-Shredderschrott, Stahlcord (Reifendraht) |
| **Aluminium** | Alu-Guss (Felgen, Motorblöcke), Alu-Profil, Alu-Blech, Alu-Dosen, Alu-Späne |
| **Kupfer & Legierungen** | Kupfer blank (Millberry), Kupfer gemischt, Kupferkabel (isoliert), Kupfergranulat, Elektromotor-Wicklungen, Messing, Rotguss, Bronze |
| **Sonstige NE-Metalle** | Blei, Zink, Zinn, Nickel, Magnesium, Titan |
| **Edelmetalle** | Kat-Monolith (Keramik), Platin, Palladium, Rhodium, Silber, Gold (aus E-Schrott) |
| **Batterien** | Starterbatterie (Blei-Säure), Batteriesäure, Li-Ion-Modul, Lithiumcarbonat, Kobalt, Nickel-Mangan-Konzentrat, Graphit |
| **Kunststoffe** | PP (Stoßfänger), PE (Tanks), ABS (Verkleidungen), PVC (Isolierung), PA (Ansaugteile), PC (Scheinwerfergläser), PUR-Schaum (Sitze), Mischkunststoff, Kunststoffgranulat je Sorte |
| **Gummi** | Altreifen (PKW/LKW), Gummigranulat, Gummi-Mischabfall (Schläuche, Dichtungen) |
| **Glas** | Verbundglas (Frontscheibe), ESG (Seiten-/Heckscheiben), Altglas, Glasbruch |
| **Elektro** | Kabelbaum, Platinen/E-Schrott, Steuergeräte, Elektrokleingeräte, Großgeräte (Weiße Ware) |
| **Flüssigkeiten & Gefahrstoffe** | Altöl, Kühlmittel (Glykol), Bremsflüssigkeit, Kraftstoff (Benzin/Diesel), Kältemittel, Airbag-Treibsätze |
| **Sonstiges** | Textil/Polster, Altholz, Papier/Pappe, Schlacke, Shredderleichtfraktion (Restabfall, kostet Entsorgung) |

Endprodukte: Barren (Alu, Kupfer, Messing, Bronze, Blei, Zink, Zinn, Edelmetalle),
Stahlbrammen, Schrottwürfel, Ballen (Blech, Kunststoff, Kabel), Big Bags (Granulat).

Alles wird als **fertige ox_inventory-Itemliste** mit **generierten Icons** geliefert.
Jedes Material kann per **Alias** auf ein Item eines anderen Scripts umgebogen werden
(z.B. `wr_copper_ingot` → `copper` aus einem Mining-Script).

### 4.3 Reinheit
- Mischschrott startet niedrig (z.B. 50–70 %).
- Sauberes Zerlegen, manuelles Sortieren und Kabelschälen erhöhen sie.
- Schmelzen mit sauberem Schlackeabzug bringt Barren auf 95–99,9 %.
- Fehler (falsche Fraktion, verunreinigter Ofen) senken sie.
- Preis = Marktpreis × Reinheitsfaktor (nichtlinear: hohe Reinheit wird überproportional bezahlt).

---

## 5. Werkzeuge, PSA & Verletzungen

### 5.1 Werkzeuge
Jedes Werkzeug ist ein Item mit Prop in der Hand, Animation, Partikeln und 3D-Sound.

| Werkzeug | Verbrauch | Einsatz |
|----------|-----------|---------|
| Ratschenkasten (Nüsse 8–24 mm) | Verschleiß | Schrauben lösen, Nussgröße muss passen |
| Akku-Schlagschrauber | Akku, Verschleiß | Radmuttern, große Verschraubungen |
| Rostlöser-Spray | Füllstand | Festgerostete Schrauben |
| Gasbrenner | Gas | Schrauben erhitzen, Schlacke |
| Brechstange | Verschleiß | Verkleidungen, Clips, Scheiben |
| Hammer / Vorschlaghammer | Verschleiß | Scharniere, Richten, Bolzen |
| Winkelschleifer (Flex) | Trennscheiben, Akku/Strom | Schneiden, VIN abschleifen |
| Plasmaschneider | Strom, Druckluft, Düsen | Karosse zerlegen (schneller als Flex) |
| Schweißgerät (MAG) | Draht, Schutzgas, Strom | Zusammenbau, Reparaturen |
| Absauggerät / Auffangwanne | Behälterfüllstand | Flüssigkeiten |
| Kältemittel-Absaugstation | Flaschenfüllstand | Klimaanlage |
| Hochvolt-Set (Isolierwerkzeug, Messgerät) | Verschleiß | E-Autos spannungsfrei schalten |
| Scheibenschneider (Draht) | Draht | Verklebte Scheiben |
| Schlagzahlen-Satz | Verschleiß | VIN/Seriennummern einschlagen |
| Rangierwagenheber, Unterstellböcke | — | Mobile Arbeit (Diebstahl, mobile Werkstatt) |

### 5.2 Persönliche Schutzausrüstung (PSA)
Schweißhelm, Schutzbrille, Arbeitshandschuhe, Gehörschutz, Sicherheitsschuhe,
Atemschutz, Hochvolt-Handschuhe. PSA wird angelegt (Prop bzw. Kleidungskomponente)
und wirkt sich mechanisch aus.

### 5.3 Verletzungen
| Ursache | Ohne PSA / bei Fehler | Folge |
|---------|-----------------------|-------|
| Flex verkantet | Schnittwunde | Schaden + Blutung (Ambulance-Bridge) |
| Schweißen ohne Helm | Verblitzen | Sicht verschwimmt für einige Minuten |
| Funkenflug an Kraftstoff | Brand | Feuer (Feuerwehr-Bridge), Verbrennungen |
| Airbag nicht deaktiviert | Auslösung | Schaden, Gehörschaden-Effekt |
| Hochvolt ohne Freischalten | Stromschlag | Ragdoll, schwerer Schaden |
| Schweres Teil fallen gelassen | Quetschung | Schaden, Humpeln |
| Laute Arbeit ohne Gehörschutz | Tinnitus | Audio-Effekt |

Ohne Ambulance-Script gibt es normalen Schaden plus Bildschirm- und Audioeffekte.

---

## 6. Minigames

Alle Minigames laufen in der eigenen NUI, sind diegetisch (Nahansicht des Bauteils)
und werden **serverseitig plausibilisiert** (Mindestdauer, Ergebnisbereich, Token pro Schritt).
Skills machen sie toleranter, ersetzen aber nie das Können.

| Minigame | Mechanik | Ergebnis |
|----------|----------|----------|
| **Schrauben** | Nuss wählen → ansetzen → Maus kreisen bzw. Schlagschrauber halten, Drehmomentanzeige im grünen Bereich halten. Festgerostet: Rostlöser + Wartezeit oder Brenner. Abgerissen: ausbohren | Gelöst / Kopf rundgedreht / Schraube abgerissen |
| **Hebeln** | Ansatzpunkt wählen, Kraft dosieren (Spannungsbalken) | Teil heil / Clips gebrochen / Teil beschädigt |
| **Hämmern** | Timing-Ring + Kraftwahl, Trefferzone | Fortschritt / Fehlschlag / Verletzung |
| **Ablassen/Absaugen** | Ablassschraube finden, Wanne bzw. Schlauch platzieren, Ventil dosieren, Füllstand beobachten | Sauber / verschüttet (Öllache = Spur) |
| **Flexen** | Schnittlinie mit der Maus folgen, Andruck dosieren, Scheibentemperatur beachten | Schnittqualität, Scheibenverschleiß, ggf. Verkanten |
| **Plasma** | Wie Flex, schneller, Abstand zur Fläche halten | Wie Flex |
| **Schweißen** | Naht abfahren: Tempo und Abstand (Lichtbogenlänge) halten | Nahtqualität 0–100 (Festigkeit) |
| **Hochvolt** | Freischalten nach Sicherheitsregeln: Wartungsstecker ziehen, sichern, Spannungsfreiheit an Messpunkten prüfen | Freigeschaltet / Stromschlag |
| **Scheibe ausschneiden** | Draht um die Scheibe führen (Koop ideal) | Scheibe heil / gesprungen |
| **Sortieren** | Teile vom laufenden Band in Fraktionsboxen ziehen | Reinheit der Fraktionen |
| **Gießen** | Gießstrahl dosieren, Form nicht überlaufen | Barrenqualität |
| **Schlagzahlen** | Stempel ausrichten, gleichmäßig schlagen (Tiefe, Abstand, Fluchtlinie) | Einschlagqualität (Erkennbarkeit bei Prüfung) |
| **Ladungssicherung** | Zurrpunkt wählen, Gurt führen, Ratsche spannen | Gesichert / locker |

---

## 7. Fahrzeugzerlegung

### 7.1 Ablauf
1. Fahrzeug kommt per Abschleppen, Schieben, Stapler oder Kran auf die **Hebebühne** bzw. den Montagebock.
2. **Sichern**: Batterie abklemmen, Airbags deaktivieren (Wartezeit für Kondensatoren),
   bei E-Autos Hochvolt freischalten.
3. **Trockenlegen**: Kraftstoff absaugen, Motoröl, Getriebeöl, Kühlmittel und Bremsflüssigkeit
   ablassen, Kältemittel absaugen.
4. **Demontage** nach Abhängigkeitsgraph (Beispiel PKW, ca. 35 Schritte):
   - Außen: 4 Räder (danach Reifen von Felge), Stoßfänger v/h, Scheinwerfer, Rückleuchten,
     Spiegel, Motorhaube, Heckklappe, Türen (Kabel trennen → Scharniere), Kotflügel, Kennzeichen
   - Glas: Seitenscheiben, Heckscheibe, Frontscheibe (verklebt)
   - Innen: Airbag-Module, Sitze, Rückbank, Cockpit, Radio/Steuergeräte, Teppich/Dämmung
   - Motorraum: Batterie, Kühler + Lüfter, Lichtmaschine, Anlasser, Klimakompressor, Kabelbaum
   - Unterboden (Bühne oben): Auspuff mit Kat, Tank, Antriebswellen, Getriebe, Motor
     (Motorkran oder zwei Personen), Achsen, Federbeine, Bremsen
5. **Karosse**: Autopresse (Würfel) oder mit Flex/Plasma in Segmente schneiden.

### 7.2 Abhängigkeiten (Beispiele)
- Motor ← Haube ab, Kühler ab, Kabelbaum Motor getrennt, Auspuffkrümmer ab, Öl abgelassen, Getriebe getrennt
- Sitze ← zugehörige Tür offen bzw. ab, Seitenairbag deaktiviert
- Tank ← Kraftstoff abgesaugt, Bühne oben
- Flex an der Karosse ← Tank raus, Kraftstoff abgesaugt (sonst Brandgefahr)

### 7.3 Bauteile
Jedes Bauteil hat: Typ, Gewichtsklasse, Gewicht, Materialzusammensetzung
(z.B. Tür: 78 % Stahl, 8 % PP, 6 % Glas, 4 % Kupfer, 4 % Sonstiges), **Zustand**
(aus Fahrzeugschaden und Minigame-Ergebnissen), optional **Seriennummer**
(Motor, Getriebe, Karosse, Kat, Türen, Airbag-Module), Herkunfts-VIN und Plattform.

Ein Bauteil kann als Gebrauchtteil verkauft (an Mechaniker), eingelagert,
in den illegalen Zusammenbau gegeben oder **in Materialien zerlegt** werden.

### 7.4 Fahrzeugtypen
Templates pro Typ und Klasse: PKW (Kompakt bis Supersport), SUV, Transporter,
Motorrad, Quad, LKW, Bus, Boot, Flugzeug, Heli. Sonderfälle wie Boote und
Luftfahrzeuge können per Config an bestimmte Standorte gebunden werden (Hafen, Flugplatz).
**E- und Hybridfahrzeuge** (Config-Liste) haben Hochvoltakku, E-Motor und
Leistungselektronik statt Tank und Teilen der Abgasanlage.

### 7.5 Sichtbarkeit
Türen, Hauben, Kofferraum, Scheiben und Räder verschwinden sichtbar vom Fahrzeug.
Innenteile, Motor und Getriebe sind in GTA keine eigenen Meshes. Sie werden logisch
entfernt, erscheinen als Prop (z.B. Motor am Kran), und das Fahrzeug wird dadurch
fahruntüchtig. Siehe Abschnitt 22.

### 7.6 Koop
Mehrere Spieler arbeiten gleichzeitig an **verschiedenen** Teilen (Teil-Sperre).
Schwere Teile erfordern zwei Spieler oder einen Motorkran.

---

## 8. Legale Verschrottung & Datenbank

1. **Annahme**: Kennzeichen, VIN-Abgleich am Fahrzeug, Fahrzeugbrief. Besitzer
   anwesend **oder** Vollmacht. Alternativ NPC-Wrack-Auftrag bzw. beschlagnahmtes
   Fahrzeug mit Behördenauftrag. Blacklist-Prüfung.
2. **Schätzung**: Das Tablet zeigt einen Schrottwert (Gewicht × Materialmix × Marktpreise
   × Zustand + Wert verwertbarer Teile).
3. **Verhandlung**: Freies Angebot, der Besitzer nimmt an oder lehnt ab. Zahlung vom Firmenkonto.
4. **Verwertungsauftrag**: Der Besitzer unterschreibt (Maus-Unterschrift in der NUI).
5. **Übernahme**: Das Fahrzeug wird **hart aus allen Tabellen gelöscht**
   (owned_vehicles, Garage, Schlüssel, Kofferraum, Tuning …) über die Garagen- und
   Schlüssel-Bridge. Ein Snapshot geht ins Admin-Archiv. VIN-Register: Status `verwertet`.
6. **Dokumente**: Verwertungsnachweis und Abmeldung gehen an den Besitzer.
7. Ab jetzt existiert das Fahrzeug nur noch als **Zerlegeauftrag** des Betriebs,
   bis es vollständig zerlegt ist.

> Wird ein Fahrzeug mit Status `verwertet` später wieder auf der Straße gesehen
> (z.B. weil es nicht zerlegt, sondern weiterverkauft wurde), fällt das bei einer
> VIN-Prüfung auf.

**Admin-Restore**: stellt alle gelöschten Zeilen aus dem Snapshot wieder her.
Es gibt einen **Dry-Run-Modus**, der das Löschen nur protokolliert (für Tests).

---

## 9. Lager & Handling

### 9.1 Gewichtsklassen
| Klasse | Beispiele | Handling |
|--------|-----------|----------|
| Klein (< 8 kg) | Sensoren, Steuergeräte, Schrauben, Kabel | Inventar |
| Mittel (8–40 kg) | Tür, Rad, Sitz, Kühler, Stoßfänger | Prop in den Händen, langsamer gehen, kein Sprinten/Springen |
| Schwer (> 40 kg) | Motor, Getriebe, Achse, Akku | Motorkran, Rollwagen, Palette + Stapler, oder zu zweit tragen |

### 9.2 Lager
- **Regalplätze und Paletten** in der Welt: eingelagerte Teile sind sichtbar und persistent.
- **Gitterboxen/Schubladen** als ox_inventory-Stashes für Kleinteile.
- Lagerplatznummern (z.B. `R03-E2-P1`), Lagerverwaltung am Büro-PC (Suche nach Teil, Modell, Zustand).

---

## 10. Materialverarbeitung

Alle Maschinen arbeiten als **Echtzeit-Prozess**: befüllen, Parameter überwachen,
bei Störungen eingreifen, Ergebnis entnehmen. Sie laufen weiter, während man anderes
macht, und überstehen Restarts.

| Maschine | Eingang → Ausgang | Gameplay |
|----------|-------------------|----------|
| **Sortierband** | Mischschrott → Fraktionen | Manuelles Sortier-Minigame (hohe Reinheit) |
| **Magnet-/Wirbelstromabscheider** | Mischschrott → Fe / NE / Rest | Automatisch, geringere Reinheit, Strom |
| **Schrottschere** | Träger, lange Teile → chargierfähiger Schrott | Teil per Kran einlegen, Schnitte setzen, Messerverschleiß |
| **Schredder** | Bleche, Kleinteile → Fe-Shredder + Leichtfraktion | Beschicken per Greifer, Verstopfung lösen, Hämmer wechseln |
| **Ballenpresse** | Blech/Kunststoff/Kabel → Ballen | Füllen, pressen, umreifen |
| **Autopresse** | Karosse → Schrottwürfel (Weltobjekt) | Karosse per Kran einlegen, Hydraulikdruck beobachten |
| **Kabelschäler** | Kupferkabel → Kupfer blank + PVC | Kabeldicke wählen, Messer einstellen |
| **Granulator** | Kunststoff/Gummi/Kabel → Granulat | Sieb wählen, Überhitzung vermeiden |
| **Reifenrecycling** | Altreifen → Gummigranulat + Stahlcord | Wulst schneiden, granulieren |
| **Batterie-Recycling** | Blei- und Li-Akkus → Blei, Säure, Li/Co/Ni | Gefahrstoffhandling, Brandgefahr bei Li |
| **Schmelzofen** | Sortierter Schrott → Barren | Vorheizen, Temperatur halten, Schlacke abziehen, gießen |
| **Kat-Aufbereitung** | Kat-Monolith → Platin/Palladium/Rhodium | Zerkleinern, Konzentrat, Schmelze (lange Prozesskette) |

**Verschleiß**: Messer und Hämmer werden stumpf, die Ofenausmauerung verschleißt,
die Hydraulik leckt. Reparatur mit Ersatzteilen + Minigame.
**Upgrades**: Kapazität, Tempo, Ausbeute, Energieeffizienz (Firmenkonto).

### Energie
- **Strom**: stationäre Maschinen. Verbrauch wird periodisch vom Firmenkonto abgebucht,
  ohne Deckung schaltet die Anlage ab.
- **Diesel**: Hofgeräte, Generator, LKW. Nachfüllen per Kanister bzw. Tankwagen (Tank-Bridge).

---

## 11. Wertstoffhof (Bürger)

- Bürger bringen Wertstoffe: Dosen, Flaschen, Kabel, Altmetall, Elektrogeräte,
  Batterien, Altöl, Reifen, Sperrmüll.
- **Mit Personal**: Mitarbeiter wiegt, prüft Sorte und Reinheit, zahlt aus der Firmenkasse
  (Preise legt der Chef fest, Orientierung am Markt).
- **Ohne Personal**: NPC-Automat zu schlechteren Konditionen (Ware landet trotzdem im Lager des Betriebs).
- **Quellen für Bürger**: Mülltonnen und Container durchsuchen, Sperrmüllhaufen in der Welt
  (persistent, rotierend), kaputte bzw. verbrauchte Items (leere Flaschen, alte Handys),
  Item-Mapping zu anderen Scripts (Müllabfuhr, Angeln, Bergbau …).

---

## 12. Kräne & Hebezeuge

Eigenes Kransystem mit **Seilphysik**, **Magnet/Greifer/Haken** und **Synchronisation**.
Gesteuert wird **aus der Kabine oder per Funkfernbedienung** (Item; man läuft mit der Last mit).

| Kran | Einsatz | Steuerung |
|------|---------|-----------|
| **Magnetkran** (stationär) | Karossen, Würfel, Stahlschrott | Drehen, Katze/Ausleger, Seil auf/ab, Magnet an/aus (aus = Last fällt) |
| **Umschlagbagger mit Polypgreifer** | Mischschrott, Schredder beschicken | Fahren, Oberwagen drehen, Ausleger, Stiel, Greifer auf/zu |
| **Hallen-/Portalkran** | Motoren, Getriebe, Teile in der Halle | Längs/quer fahren, heben |
| **Motorkran** (mobil) | Motor/Getriebe aus- und einbauen | Schieben, pumpen, ablassen |
| **LKW-Ladekran** | Abholaufträge, Container | Abstützen, Ausleger, Teleskop, Seil |

- Pendeln, Trägheit und Last beeinflussen das Handling. **Überlast** blockiert die
  Hydraulik oder lässt das Seil reißen.
- Magnet: nimmt nur ferromagnetische Lasten auf, Stärke abhängig von Strom und Last.
- **Kranschein** nötig (Prüfung oder Vergabe, je nach Config).

---

## 13. Transport & Logistik

| Fahrzeug | Aufgabe |
|----------|---------|
| Abschlepper / Flatbed | Autos und Wracks laden, festzurren |
| Abrollcontainer-LKW / Absetzkipper | Container bei Kunden abstellen, voll abholen, am Hof abkippen |
| Sattelzug + Schrottauflieger | Würfel, Ballen, Barren zur Hütte bzw. zum Hafen |
| Gabelstapler, Radlader, Teleskoplader | Umsetzen auf dem Hof (Paletten, Haufen, Karossen) |

**Ladungssicherung**: Ladung wird physisch per Kran/Stapler abgesetzt und an Zurrpunkten
mit Gurten bzw. Ketten gesichert. Ungesichert verrutscht sie bei harter Fahrt oder fällt herunter.
Gewicht beeinflusst das Fahrverhalten.

**Container** sind persistente Weltobjekte mit Inhalt (kg je Material), die bei
Kunden stehen und sich füllen (Abholaufträge).

---

## 14. Aufträge, Abnehmer & Markt

### Aufträge (Tablet)
- Containertausch / Abholung bei Firmen und Baustellen
- Abriss & Entrümpelung: vor Ort demontieren (Heizkörper, Kabel, Stahlträger, Maschinen)
- NPC-Wracks abschleppen (Behörden- und Pannenaufträge)
- Lieferaufträge an die Hütte, Exportaufträge am Hafen (Menge, Reinheit, Termin)

### Abnehmer
- **NPC-Hütte/Großhändler**: kauft Barren, Ballen, Würfel, Granulat (Lieferung per LKW)
- **Hafen-Export**: Container befüllen, verladen, große Aufträge mit Bonus
- **Spieler-Jobs**: Mechaniker (Gebrauchtteile), Crafting/Bau (Rohstoffe) über Firmenhandel
- **Schwarzmarkt/Hehler**: heiße Teile, Kat-Edelmetalle, Fälschungen

### Dynamischer Markt
Jedes Material hat einen Basispreis, einen Trend (Zufallsdrift) und eine Angebotskomponente.
Wer viel verkauft, drückt den Preis, die Menge erholt sich über Zeit. Kurse sind auf einer
**Börsentafel** am Hof und im Tablet sichtbar (mit Verlauf). Admins können eingreifen.

---

## 15. Firmenführung

- **Ränge** (Default): Azubi, Arbeiter, Facharbeiter, Meister, Betriebsleiter, Chef.
- **Lizenzen**: Kranschein, Staplerschein, LKW-Führerschein (C/CE), Schweißschein,
  Hochvoltschein, Gefahrstoffschein. Pro Lizenz konfigurierbar: praktische Prüfung
  im Spiel oder Vergabe durch den Chef.
- **Firmenkonto** (Bridge): alle Erlöse, Ankäufe, Strom, Upgrades, Löhne.
- **Lohn**: Der Chef zahlt Löhne und Prämien (Einzel- oder Sammelzahlung).
- **Büro-PC**: Buchhaltung, Personal, Lizenzen, Lager, Preise, Betriebsbuch, Upgrades.
- **Tablet**: Aufträge, Markt, Lager-Suche, Navigation, Fahrzeugschätzung, Verträge.

### Betriebsbuch
Jeder Vorgang erzeugt einen Eintrag (Annahme, Waage, Zerlegung, Entsorgung, Verkauf).
Einträge sind **realistisch manipulierbar**: Man kann Mengen "anpassen", Einträge
vergessen oder Belege fehlen lassen. Das System markiert nichts automatisch.
Es speichert aber Rohdaten (Waagenprotokolle, Konto-Buchungen, Seriennummern), deren
**Widersprüche** ein Ermittler finden kann, wenn er weiß, wo er suchen muss.

---

## 16. Skills

Pro Tätigkeit: Schrauben, Flexen, Schweißen, Hebeln/Hämmern, Hochvolt, Kran,
Stapler/LKW, Sortieren, Schmelzen, Fälschen (Schlagzahlen, Papiere).
Skills steigen durch Praxis und machen Minigames toleranter, senken Ausschuss und
erhöhen Reinheit bzw. Fälschungsqualität. Sie schalten **nichts frei**.
Eine Bridge zu externen Skill-Systemen ist vorgesehen.

---

## 17. Identität: VIN, Seriennummern, Dokumente, Zulassung

### VIN-Register
- Jedes Fahrzeug erhält eine **17-stellige VIN** (ISO-3779-Aufbau mit fiktiven
  Herstellercodes **und Prüfziffer**). Schlecht gefälschte VINs mit falscher Prüfziffer
  fallen auf, wenn jemand nachrechnet.
- Die VIN steht an **mehreren Stellen**: Rahmen, B-Säule (Typenschild), Motor.
  Manipulation an einer Stelle erzeugt Widersprüche zu den anderen.
- **Historie**: Erstzulassung, Halterwechsel, Diebstahlmeldung, Verwertung,
  Umbauten, Prüfungen.
- Status: `zugelassen`, `nicht zugelassen`, `gestohlen`, `verwertet`, `zerstört`.

### Seriennummern
Hauptteile tragen Seriennummern, die auf die Herkunfts-VIN zeigen. Werden sie
verbaut (Mechaniker, Zusammenbau), merkt sich das Zielfahrzeug, welche Teile
mit welcher Nummer es enthält.

### Dokumente
Fahrzeugbrief, Zulassungsbescheinigung, Verwertungsauftrag, Verwertungsnachweis,
Abmeldung, Vollmacht. Das sind Items mit Metadaten und NUI-Ansicht (Formular-Look,
Unterschrift, Stempel). Jedes echte Dokument hat eine Dokumentnummer im
Echtheitsregister. Euer eigenes Dokumentensystem wird später per Bridge angebunden.

### Zulassung
Nur über die **Spieler-Behörde** (Job per Config): Terminal zur Prüfung von Papieren
und Fahrzeug (VIN-Stellen ansehen), Zulassung → Eintrag in owned_vehicles über die
Garagen-Bridge, Kennzeichenvergabe.

---

## 18. Behörden & Ermittlung

Es gibt **keine automatischen Alarme oder Strafen**. Ermittler finden nur, was
Spieler hinterlassen haben:

| Spur / Werkzeug | Beschreibung |
|-----------------|--------------|
| **VIN-Prüfung** | Alle VIN-Stellen am Fahrzeug ansehen. Abgeschliffene und neu eingeschlagene Zahlen sind bei genauer Untersuchung sichtbar, eindeutig erst mit **Ätzmittel** (macht Original-Zahlen sichtbar) |
| **Historienabfrage** | VIN-Register (nur so gut wie die eingetragenen Daten) |
| **Teile-Rückverfolgung** | Seriennummer eines Teils → Herkunftsfahrzeug |
| **Razzia** | Durchsuchen von Lager, Regalen, Stashes, Werkstätten, Sicherstellung als Beweis-Items |
| **Physische Spuren** | Öllachen, kontaminierter Boden, illegal abgekippter Schrott (persistent). Probenahme-Kit für Polizei/Feuerwehr |
| **Beweis-Items** | Sichergestellte Teile, Proben, Fotos mit Metadaten (Ort, Zeit, Herkunft) |
| **Betriebsbuch / Akten** | Einsicht am Büro-PC, Abgleich mit Waagenprotokollen und Kontobewegungen |
| **MDT-Schnittstelle** | Exports/Events für Sky-MDT (Bridge) und andere Aktensysteme |

---

## 19. Illegaler Teil

**Jeder darf.** Begrenzt wird nur durch Wissen, Ausrüstung und Orte.

### 19.1 Wissen
- **Frei**: Einfache Dinge wie gestohlene Autos zerlegen, falsche Kennzeichen anschrauben,
  Kats klauen.
- **Wissen nötig**: VIN neu einschlagen, Papiere fälschen, Doubletten bauen, Kat-Edelmetall-Scheidung.
- **Erwerb**: Notizen/Anleitungen als Items (kopierbar, bei Razzia ein Beweis),
  versteckte NPC-Kontakte, Fundstücke in Wracks und alten Werkstätten, Weitergabe von
  Spieler zu Spieler durch gemeinsame Übung (dauerhaft gelernt).
- Das Spiel zeigt **keine** illegalen Menüpunkte: Wer das Wissen hat, bekommt die
  passende Interaktion beim Werkzeug, alle anderen sehen nichts.

### 19.2 Chop-Shop
- Werkstätten an Config-Orten. Per Config-Schalter zusätzlich mobile Ausrüstung
  (Wagenheber, Böcke, mobiler Motorkran) überall nutzbar.
- Gestohlene Spieler- und NPC-Fahrzeuge werden genauso zerlegt wie legale.
- **Spielerfahrzeuge**: Gelöscht wird, sobald das Fahrzeug **physisch aufhört zu existieren**
  (Karosse gepresst/zerschnitten) oder seine Identität ersetzt wird. Bis dahin besteht es in
  der DB, mit fehlenden Teilen, die auch nach dem Ausparken fehlen. Ein Versicherungs-Event wird ausgelöst.
- **NPC-Hehler**: kauft gestohlene Autos und heiße Teile, vergibt **Bestellaufträge**
  (Modell, Farbe, bestimmte Teile, Frist).

### 19.3 Fälschung
| Fälschung | Ablauf | Entdeckungsrisiko |
|-----------|--------|-------------------|
| **VIN umschlagen** | Original abschleifen (Flex) → Schlagzahlen-Minigame. An allen VIN-Stellen nötig | Einschlagqualität, vergessene VIN-Stelle, Prüfziffer, Ätzmittel |
| **Papiere** | Blanko-Vordrucke, Stempel, Drucker, Unterschrift nachahmen | Dokumentnummer nicht im Echtheitsregister, Stempelqualität |
| **Kennzeichen** | Prägen bzw. fremde Schilder | Kennzeichen passt nicht zur VIN |
| **Doublette** | Identität eines legalen Fahrzeugs gleichen Modells übernehmen | Beide Fahrzeuge gleichzeitig unterwegs, Historie widersprüchlich |

### 19.4 Zusammenbau
- **Plattform-System**: Modelle sind in Plattformen gruppiert. Antriebsteile passen
  plattformweit, Karosserieteile nur modellgleich.
- Rohkarosse auf dem Montagebock → alle Teile einbauen (Motor per Motorkran, Getriebe,
  Achsen, Räder, Türen, Scheiben, Sitze, Elektrik) mit Schraub- und Schweiß-Minigames.
- Zustand der Teile + Nahtqualität bestimmen Zustand und Haltbarkeit des Fahrzeugs.
- Das fertige Fahrzeug ist **nicht zugelassen**. Erst mit (gefälschten) Papieren über
  die Spieler-Zulassungsbehörde wird es ein reguläres Spielerfahrzeug.

### 19.5 Diebstähle
| Aktivität | Ablauf | Besonderheit |
|-----------|--------|--------------|
| **Kat-Diebstahl** | Unter geparktes Auto legen (Wagenheber), Kat rausflexen | Auto danach laut + Motorwarnung, Seriennummer fehlt im Fahrzeug |
| **Kupfer/Kabel** | Baustellen, Bahnstrecken, Umspannwerke | Stromschlaggefahr, Freischalten nötig |
| **Räder** | Aufbocken, Radmuttern lösen | Auto steht auf Steinen (Props), bis ein Mechaniker kommt |
| **Metallobjekte** | Gullideckel, Geländer, Schilder, Bronzefiguren abflexen | Objekt fehlt in der Welt (Respawn-Timer) |

### 19.6 Geldwäsche
Fingierte Ankäufe (Ware ohne Waagenprotokoll) bzw. Verkäufe über das Firmenkonto.
Gebühr + Tageslimit. Erzeugt **Buchungslücken**, die bei einer Prüfung des Betriebsbuchs
gegen Waage und Lager auffallen können.

---

## 20. Administration

- **Platzierungs-Editor**: Standorte, Module, Maschinen, Regale, Kräne, Container und
  illegale Werkstätten mit Vorschau platzieren, drehen und speichern.
- **Fahrzeug-Restore** aus dem Archiv, **Dry-Run** fürs Löschen.
- **Markt**: Kurse ansehen und setzen, Ereignisse auslösen (Preissturz, Boom).
- **Logs**: durchsuchbare DB-Logs, Discord-Webhooks je Kategorie.
- **Debug**: Befehle zum Spawnen von Testfahrzeugen, Items, Teilen, Maschinen-Reset.
- **Rechte**: ACE (`wr_scrap.admin.*`) + ESX-Gruppen.

---

## 21. Balancing (Defaults, alles per Config)

- Zerlegen eines Mittelklasse-PKW allein: **45–60 min**, zu zweit ca. 30–40 min.
- Globaler Zeitmultiplikator + Multiplikatoren je Tätigkeit.
- Verdienst-Anker ($/h legal und illegal) frei einstellbar. Preise, Löhne und Hehlerpreise
  werden daraus abgeleitet.

---

## 22. Grenzen von GTA/FiveM und wie wir damit umgehen

| Grenze | Lösung |
|--------|--------|
| Motor, Getriebe, Sitze sind keine abnehmbaren Meshes | Logisches Entfernen + Prop-Darstellung, Fahrzeug wird fahruntüchtig (Engine Health, Handling) |
| Türen, Hauben, Scheiben | Echte Entfernung über Natives |
| Räder | Abtrennen bzw. Ausblenden über Natives (buildabhängig) mit Fallback |
| Seile (Rope) sind nur clientseitig | Der Bediener simuliert die Physik, die Last ist ein vernetztes Entity, alle anderen rendern das Seil lokal zwischen synchronisierten Punkten |
| Kein Vanilla-Schrottkran, keine Autopresse, kein Hakenlift-LKW | Aus Props zusammengesetzte Maschinen und Kräne (Vanilla-Fallback) + Addon-Slots |
| ox_inventory kann keine Items zur Laufzeit registrieren | Mitgelieferte Itemliste zum Eintragen in `ox_inventory/data/items.lua` |
| Neu gespawnte Fahrzeuge sind "heil" | Fahrzeugzustands-Overlay: fehlende Teile werden beim Spawn per VIN/Kennzeichen erneut angewendet |
| Ingame-Tests nicht durch mich möglich | Testcheckliste pro Baustein, Debug-Modus, Dry-Run |
