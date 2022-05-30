# Projektarbeit Objektorientierte Softwareentwicklung

## Aufgabe 1: Entwicklung von Use Cases

Akteure:
- Kunde
- Service-Personal

### a) Entwerfen Sie ein Use Case Diagramm

```plantuml
@startuml
left to right direction

actor Kunde as K
actor Service as S

rectangle {
    usecase UC1 as "Ware kaufen"

    usecase UC2 as "Automat mit Ware füllen"
    
    usecase UC3 as "Wechselgeldbestand verwalten"

    usecase UC4 as "Verkaufserfolg anfordern"

    K -- UC1
    UC2 -- S
    UC3 -- S
    UC4 -- S

}
@enduml
```

### b) Spezifizieren Sie die Details des Use Cases „Ware kaufen“

| | |
|---|---|
|**Use Case Name**          | Ware kaufen
|**Auslösender Aktor**      | Kunde
|**Zweck / Ziel**           | Kunde wählt gewünschte Ware und kann sie mit Münzen kaufen.
|**Eingehende Information** | geöffnetes Fach, Münzeinwurf, Drehknopf drücken, Restgeldknopf drücken
|**Ergebnis**               | Der Kunde kann die Ware aus dem Fach entnehmen. Der Automat gibt korrektes Rückgeld.
|**Grundlegender Ablauf**   |
|                           | 1. Kunde drückt auf Drehknopf.
|                           | 2. Automat dreht alle  Drehteller einen Schritt nach rechts.
|                           | 3. Automat zeigt Warenpreis und -status (grün,rot,weiss) für das vorderste Fach auf jedem Drehteller.
|                           | 4. Kunde wirft Münzen ein.
|                           | 5. Automat prüft Münzwährung und Münzsäulenbestand und Münzwert
|                           | 6. Automat zählt Münzarten, merkt sich diese und addiert den Gesamtbetrag und aktualisiert Münzenbestand
|                           | 7. Kunde versucht eine Schiebetür zu öffnen
|                           | 8. Automat prüft Status (Ware abgelaufen, nicht erhältlich, erhältlich)
|                           | 9. Automat prüft korrekten Betrag
|                           | 10. Automat prüft ob Wechselgeld vorhanden
|                           | 11. Automat entriegelt Schiebetür
|                           | 12. Kunde öffnet Tür und entnimmt Ware
|                           | 13. Automat aktualisiert Statuszeige
|                           | 14. Automat aktualisiert interne Statistik (Ware)
|                           | 15. Automat aktualisiert Anzeige Geldbetrag
|                           | 16. Kunde schliesst Schiebetür
|                           | 17. Automat verriegelt Schiebetür
|                           | 18. Kunde drückt Geldrückgabeknopf
|                           | 19. Automat gibt Rückgeld aus falls geschuldet und aktualisiert Münzenbestand
|                           | 20. Automat aktualisiert Anzeige Geldbetrag
|                           | 21. Kunde entnimmt Rückgeld
|**Erweiterungen**          | -
|**Alternativen**           | 5a [Münzsäule voll] Eingeworfene Münze wird wieder ausgegeben.
|                           | 8a [abgelaufen, leer] Schiebetür bleibt geschlossen.
|                           | 9a [nicht genug Geld eingeworfen] Anezige "nicht genug Geld" wird angezeigt. Schiebetür bleibt geschlossen.
|                           | 10a [kein Wechselgeld vorhanden] Anzeige "kein Wechselgeld" wird angezeigt. Schiebetür bleibt geschlossen.
|                           | 16a [Kunde schliesst Schiebetür nicht] Drehteller können nicht gedreht werden.
|                           | 18a [Kunde wählt eine weitere Ware] Zurück zu 1.

---

## Aufgabe 2: Statisches und dynamisches Modell


### 4.2.1/4.2.2 Klassendiagram

```plantuml
@startuml
skinparam classFontSize 15
skinparam classAttributeFontSize 15
skinparam arrowFontSize 20

class Automat {
    drehen(): void
    istInServicemode : Boolean
    Kaufeintraege: Kaufeintrag[]
}

class Drehteller {
    istOffen: Boolean
    öffnen(): Boolean
    schliessen(): void
    entriegeln(): void
    Fach: Fach
    neueWareVonBarcodeLeser(Warenname: String, Preis: Fixed(2,2), Verfallsdatum: Date): void
    drehen(Fach) : Fach
}


class Fach {
    LinkerNachbar: Fach
}

class Ware {
    Name: String
    Preis: Double
    Verfallsdatum: Date
    ware(preis: Double, name: String, verfallsdatum: Date) : Ware
}

class Kaufeintrag {
    Ware: Ware
    Datum: Date
}

class Kasse {
    addiereMuenze(muenzSaeule: MuenzSaeule) : UInteger
    entferneGemerkt() : void
}

class MuenzSaeule {
    Wert : Fixed [1,2]
    Merken: UInteger
    Anzahl : UInteger
    addiereMuenze() : void
    entferneGemerkte() : void
}

class Drehtelleranzeige {
    Anzeige: Anzeige
    Lampe: Lampe
}

class BedienPanel{
    Geldbetrag: Anzeige
    StatusWechselgeld: Lampe
    StatusGenugGeld: Lampe
    zurueckgebenGeld() : void
}

class ServicePanel{
    öffnePanel() : void
    schliessePanel() : void
    hinzufuegenMuenze(münzWert: Fixed [1,2], Anzahl: Integer) : Integer
    anzeigenGesamtanzahl() : UInteger
    auffüllenWare(preis: Double, name: String, verfallsdatum: Date) : void
    ausgebenStatistik(wahrenbezeichnung: String, Datum: Date) : UInteger
}

abstract "AnzeigeGruppe {abstract}"{
    Anzeige1: Anzeige
    Anzeige2: Anzeige
    Anzeige3: Anzeige
}

abstract Anzeige{
    Text: String
    zeigen(text: String)
}

class Lampe {
    Leuchtet: Boolean
    Farbe: String[6]
}


class WechselgeldbestandAnzeigeGruppe{
--    BestaetigungsKnopf: Knopf
    zeigeWert(wert: Fixed[5,2]): void
    zeigeMuenzArt(wert: int): void
    zeigeAnzahl(anzahl: int): void
    zeigeGesamtwert() : void
}

class WarenVerwaltungAnzeigeGruppe{
    zeigeWare(ware: Ware): void
    zeigePreis(preis: Fixed[1,2]): void
    zeigeAnzahl(anzahl: int)
}

class VerkaufserfolgAnzeigeGruppe {
    zeigeWare(ware: Ware) : void
    zeigeDatum(datum: Date) : void
}

Automat "1" -- "7" Drehteller
Drehteller "1" -- "16" Fach
Fach "1" -- "0..1" Ware
Fach  -- "linker Nachbar" Fach
Automat "1" -- "0..*" Kaufeintrag
Ware "1" -- "0..1" Kaufeintrag
Automat "1" -- "1" Kasse
Kasse "1" -- "5" MuenzSaeule
Drehteller -- Drehtelleranzeige
Drehtelleranzeige -- Lampe
Drehtelleranzeige -- Anzeige
Automat -- ServicePanel
ServicePanel "1" -- "3" "AnzeigeGruppe {abstract}"
Automat -- BedienPanel
BedienPanel "1" -- "1" Anzeige
BedienPanel "1" -- "2" Lampe
"AnzeigeGruppe {abstract}" "1" -- "3" Anzeige
"AnzeigeGruppe {abstract}" <|-- WechselgeldbestandAnzeigeGruppe
"AnzeigeGruppe {abstract}" <|-- WarenVerwaltungAnzeigeGruppe
"AnzeigeGruppe {abstract}" <|-- VerkaufserfolgAnzeigeGruppe

@enduml
```

### 4.2.2 Objektdiagramm

### 4.2.3 Squenzdiagramme

#### a) Waren kaufen

```plantuml
@startuml
mainframe **   Ware kaufen   **

actor "Kundin" as Kundin
participant ":Automat" as Automat 
participant ":Kasse" as Kasse 
participant ":Geldanzeige" as GA 
participant ":Drehteller" as DT
participant ":Fach" as Fach  
participant ":Ware" as Ware
participant ":Kaufeintrag" as KE
participant ":Log" as Log
participant ":Drehtelleranzeige" as D
participant ":Wechselgeldanzeige" as WAZ
participant ":NichtGenugGeldAnzeige" as NGGAZ
 

Kundin -> Automat : drückeDrehknopf()
activate Automat
    Automat -> DT : drehen()

    activate DT
    DT -> D : zeigeWarenPreis(1.5)
    activate D
    DT -> D : zeigeStatus(grün,rot,ausgeschaltet)
    deactivate DT
    deactivate D
    deactivate Automat
    

Kundin -> Automat : einwerfenGeld(2)
    activate Automat
    Automat -> Automat : prüfeGeld()
    Automat -> Kasse   : prüfeWechselgeld()
    activate Kasse
    Kasse --> Automat : WechselgeldVorhanden()
    
    Automat  -> Kasse : transeferiereGeld()
    Kasse -> Kasse : merkeGeld()
    Kasse -> Kasse : addiereMünze()
   
    Kasse -> GA : zeigeBetrag
    activate GA
    deactivate GA
    deactivate Kasse
    deactivate Automat

Kundin -> Automat : einwerfenGeld(0.5)
    activate Automat
    Automat -> Automat : prüfeGeld()
    Automat -> Kasse   : prüfeWechselgeld()
    activate Kasse
    Kasse --> Automat : WechselgeldVorhanden()
    
    Automat  -> Kasse : transeferiereGeld()
    Kasse -> Kasse : merkeGeld()
    Kasse -> Kasse : addiereMünze()
   
    Kasse -> GA : zeigeBetrag
    activate GA
    deactivate GA
    deactivate Kasse
    deactivate Automat

Kundin -> DT : oeffneFach()
    activate DT
    DT -> Kasse : "PrüfungOK(WechselGeld, GenugGeld)"
        activate Kasse
        DT <- Kasse : "AntwortPrüfung(OK,OK)
        deactivate Kasse

    DT -> WAZ : melden(-)
        activate WAZ
        deactivate WAZ

    DT -> NGGAZ : melden(-)
        activate NGGAZ
        deactivate NGGAZ

    DT -> Fach :entriegeln()
        activate Fach
        Fach -> Fach : istGefüllt(-)
        Fach -> Ware : produktVerkauft(TellerNr,PositionNr)
            activate Ware
            Ware -> KE : macheEintrag()
                activate KE
                KE -> Log : erfasseStatistik()
                    activate Log
                    deactivate Log
                deactivate KE
            deactivate Ware
        deactivate Fach

    DT -> Kasse : resetStatistik()
        activate Kasse
        Kasse -> GA : anzeigenBetrag(1.00)
            activate GA
            deactivate GA
        deactivate Kasse
    deactivate DT
    

Kundin -> DT : schliesseFach()
    activate DT
    deactivate DT
   
Kundin -> DT : oeffneFach()
    activate DT
    DT -> Kasse : PrüfungOK(WechselGeld, GenugGeld)
        activate Kasse
        Kasse -> DT : AntwortPrüfung(OK,NOK)
        deactivate Kasse
    DT -> WAZ : melden(-)
    DT -> NGGAZ : melden(+)
    deactivate DT

Kundin -> Automat : drückeRückgabeKnopf
    activate Automat
   Automat -> Kasse : aufbereitenRestgeld(1.00)
        activate Kasse
        Kasse -> GA : zeigeBetrag(0.00)
            activate GA
            deactivate GA
        deactivate Kasse
    Automat --> Kundin : ausgebenRestgeld(1.00,-1 GR in Bestand)

@enduml
```

#### b) Warenwert des Automaten berechnen

```plantuml
@startuml

mainframe ** Warenwert des Automaten berechnen **

actor "Service" as Service
participant ":ServicePanel" as ServicePanel

Service -> ServicePanel : öffnePanel
activate ServicePanel
    participant ":WechselgeldbestandAnzeigeGruppe" as WechselgeldbestandAnzeigeGruppe
    ServicePanel -> WechselgeldbestandAnzeigeGruppe : zeigeGesamtwert()
    activate WechselgeldbestandAnzeigeGruppe
        participant ":Ware" as Ware
        loop Ware in Waren
            WechselgeldbestandAnzeigeGruppe -> Ware : getPreis()
                activate Ware
                WechselgeldbestandAnzeigeGruppe <-- Ware : preis
                deactivate Ware
            WechselgeldbestandAnzeigeGruppe -> WechselgeldbestandAnzeigeGruppe : add(preis)
                activate WechselgeldbestandAnzeigeGruppe
                deactivate WechselgeldbestandAnzeigeGruppe
        end
    deactivate WechselgeldbestandAnzeigeGruppe
deactivate ServicePanel

@enduml
```
