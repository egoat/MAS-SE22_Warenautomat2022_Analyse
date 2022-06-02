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
    einwerfen() : void
    zeigeGesamtbetrag(Gesamtbetrag: Double) : void
    prüfeSchiebetürenGeschlossen() : Boolean
    gibGesamtWarenWert() : Double
}

class Drehteller {
    istOffen: Boolean
    öffnen(): Boolean
    entriegeln(): void
    schliessen(): void
    AktuellesFach: Fach
    neueWareVonBarcodeLeser(Warenname: String, Preis: Fixed(2,2), Verfallsdatum: Date): void
    drehen() : void
    Drehteller(in AktuellesFach: Fach)
    gibGesamtwarenWert() : Double
    berechneAktuellenWarenwert(Double) : Double
}


class Fach {
    LinkerNachbar: Fach
    Fach(in LinkerNachbar: Fach)
}

class Ware {
    Name: String
    Preis: Double
    Verfallsdatum: Date
    Ware(in Preis: Double, in Name: String, in Verfallsdatum: Date) : Ware
    gibAktuellenWarenwert() : Double
    istAbgelaufen() : Boolean
}

class Kaufeintrag {
    Ware: Ware
    Verkaufsdatum: Date
    Kaufeintrag(in Ware: Ware, in Verkaufsdatum: Date)
}

class Kasse {
    Gesamtbetrag: Double
    setzteAnzahlEingeworfen() : void
    einwerfen(Fixed [1,2]) : Boolean
    prüfeEingeworfeneMünze() : Boolean
    gibGeldZurück() : void
    prüfeKaufOk(Ware: Ware) : Boolean
}

class MuenzSaeule {
    Wert : Fixed [1,2]
    AnzahlEingeworfen: UInteger
    Anzahl : UInteger
    addiereMünze() : Boolean
    setzeAnzahlEingeworfen() : void
}

class Drehtelleranzeige {
    Preis: Anzeige
    Warenzustand: Lampe
    anzeigen(Ware) : void
}

class GeldbetragStatusAnzeige{
    Geldbetrag: Anzeige
    StatusWechselgeld: Lampe
    StatusGenugGeld: Lampe
    zurueckgebenGeld() : void
    zeigeGesamtbetrag(Gesamtbetrag: Double) : void
}

class BedienAnzeigePanel{
    öffnen() : void
    schliessen() : void
    hinzufuegenMuenze(münzWert: Fixed [1,2], Anzahl: Integer) : Integer
    anzeigenGesamtwarenWert() : Double
    ausgebenStatistik(wahrenbezeichnung: String, Datum: Date) : UInteger
}

abstract "AnzeigeGruppe {abstract}"{
    Anzeige1: Anzeige
    Anzeige2: Anzeige
    Anzeige3: Anzeige
}

class Anzeige{
    Text: String
    anzeigen(Text: String)
}

class Lampe {
    Leuchtet: Boolean
    Farbe: String[6]
    ausschalten() : void
    zeigeWeiss() : void
    zeigeGrün() : void
    zeigeRot() : void

}


class WechselgeldbestandAnzeigeGruppe{
    zeigeWertVerkaufteWare(wert: Fixed[5,2]): void
    zeigeMuenzArt(wert: int): void
    zeigeAnzahl(anzahl: int): void
    zeigeGesamtwert(Gesamtbetrag: Double) : void
}

class WarenVerwaltungAnzeigeGruppe{
    zeigeWare(ware: Ware)
}

class VerkaufserfolgAnzeigeGruppe {
    zeigeWare(ware: Ware) : void
    zeigeDatumPer(datum: Date) : void
    zeigeAnzahl() : void
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
Automat -- BedienAnzeigePanel
BedienAnzeigePanel "1" -- "3" "AnzeigeGruppe {abstract}"
Automat -- GeldbetragStatusAnzeige
GeldbetragStatusAnzeige "1" -- "1" Anzeige
GeldbetragStatusAnzeige "1" -- "2" Lampe
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
Kundin -> Automat : drehen()
activate Automat
    Automat -> Automat : prüfeSchiebetürenGeschlossen
    participant ":Drehteller" as Drehteller
    Automat -> Drehteller : drehen()
        activate Drehteller
        participant ":Fach" as Fach
        Drehteller -> Fach : getWare()
        activate Fach
        deactivate Fach
        participant ":Drehtelleranzeige" as Drehtelleranzeige
        Drehteller -> Drehtelleranzeige : anzeigen(Ware)
        activate Drehtelleranzeige
            participant ":Ware" as Ware
            Drehtelleranzeige -> Ware : getPreis()
            activate Ware
            deactivate Ware
            Drehtelleranzeige -> Ware : istAbgelaufen()
            activate Ware
            deactivate Ware
            participant ":Anzeige" as Anzeige
            Drehtelleranzeige -> Anzeige : anzeigen("1.50")
            activate Anzeige
            deactivate Anzeige
            participant ":Lampe" as Lampe
            Drehtelleranzeige -> Lampe : zeigeGrün()
            activate Lampe
            deactivate Lampe
        deactivate Drehteller
        deactivate Drehtelleranzeige
    deactivate Automat
    
Kundin -> Automat : einwerfen()
activate Automat
participant ":Kasse" as Kasse
Automat -> Kasse : einwerfen()
        activate Kasse
        Kasse -> Kasse : prüfeEingeworfeneMünze
        Kasse -> Automat : zeigeGesamtbetrag(Gesamtbetrag)
        activate Automat
        participant ":GeldbetragStatusAnzeige" as GeldbetragStatusAnzeige
        Automat -> GeldbetragStatusAnzeige : zeigeGesamtbetrag(Gesamtbetrag)
            activate GeldbetragStatusAnzeige
            deactivate GeldbetragStatusAnzeige
        deactivate Automat
        deactivate Kasse
    deactivate Automat
    deactivate Automat

Kundin -> Automat : einwerfen()
activate Automat
participant ":Kasse" as Kasse
Automat -> Kasse : einwerfen()
        activate Kasse
        Kasse -> Kasse : prüfeEingeworfeneMünze
        Kasse -> Automat : zeigeGesamtbetrag(Gesamtbetrag)
        activate Automat
        participant ":GeldbetragStatusAnzeige" as GeldbetragStatusAnzeige
        Automat -> GeldbetragStatusAnzeige : zeigeGesamtbetrag(Gesamtbetrag)
            activate GeldbetragStatusAnzeige
            deactivate GeldbetragStatusAnzeige
        deactivate Automat
        deactivate Kasse
    deactivate Automat
    deactivate Automat

Kundin -> Drehteller : öffnen()
    activate Drehteller
    Drehteller -> Automat : prüfeSchiebetürenGeschlossen
    activate Automat
    deactivate Automat
    Drehteller -> Fach : getWare()
    activate Fach
    deactivate Fach
    Drehteller -> Kasse : prüfeKaufOk(Ware: Ware) : Boolean
    activate Kasse
    deactivate Kasse

    Drehteller -> Lampe : melden(-)
        activate Lampe
        deactivate Lampe

    Drehteller -> Lampe : melden(-)
        activate Lampe
        deactivate Lampe

    Drehteller -> Fach :entriegeln()
        activate Fach
        Fach -> Fach : istGefüllt(-)
        Fach -> Ware : produktVerkauft(TellerNr,PositionNr)
            activate Ware
            participant ":Kaufeintrag" as Kaufeintrag
            Ware -> Kaufeintrag : macheEintrag()
                activate Kaufeintrag
                deactivate Kaufeintrag
            deactivate Ware
        deactivate Fach

    Drehteller -> Kasse : resetStatistik()
        activate Kasse
        Kasse -> Geldanzeige : anzeigenBetrag(1.00)
            activate Geldanzeige
            deactivate Geldanzeige
        deactivate Kasse
    deactivate Drehteller
    

Kundin -> Drehteller : schliesseFach()
    activate Drehteller
    deactivate Drehteller
   
Kundin -> Drehteller : oeffneFach()
    activate Drehteller
    Drehteller -> Automat : prüfeSchiebetürenGeschlossen
    activate Automat
    deactivate Automat
    Drehtell -> Fach : getWare()
    Drehteller -> Kasse : PrüfungOK(WechselGeld, GenugGeld)
        activate Kasse
        Kasse -> Drehteller : AntwortPrüfung(OK,NOK)
        deactivate Kasse
    Drehteller -> Lampe : melden(-)
    Drehteller -> Lampe : melden(+)
    deactivate Drehteller

Kundin -> Automat : drückeRückgabeKnopf
    activate Automat
   Automat -> Kasse : aufbereitenRestgeld(1.00)
        activate Kasse
        Kasse -> Geldanzeige : zeigeBetrag(0.00)
            activate Geldanzeige
            deactivate Geldanzeige
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


#### b) Warenwert des Automaten berechnen (V2 ANDREA)

```plantuml
@startuml

mainframe ** Warenwert des Automaten berechnen V2 ANDREA NOT FINISHED **

participant ":Automat" as Automat   
participant ":Drehteller" as Drehteller
participant ":Fach" as Fach

activate Automat
    Automat -> Automat : zeigeGesamtbetrag(): Double
    activate Automat
        loop Drehteller <= 7
            Automat -> Drehteller : gibGesamtWert(): Double
            participant ":Ware" as Ware
            activate Drehteller
                loop Fach <= 16
                    Drehteller -> Fach : gibWare(): Ware
                    activate Fach
                    deactivate Fach
                    loop Wenn Ware in Fach vorhanden
                        Drehteller -> Ware : gibAktuellenWarenwert: Double
                        activate Ware
                        deactivate Ware
                        note right
                            Warenwert abgelaufener Ware ist
                            um 75% reduziert und auf 10 Rappen gerundet.
                            end note
                        Drehteller -> Drehteller : berechneAktuelleWarenwert(Double, istAbgelaufen)
                    end
                        activate Drehteller
                        deactivate Drehteller     

                end 
            deactivate Drehteller      
        end   
    deactivate Automat     
deactivate Automat 


@enduml
```
