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
    +drehen(): void
    +einwerfen() : void
    +überprüfeMünzeinwurf() : Boolean
    +prüfeSchiebetürenGeschlossen() : Boolean
    +berechneGesamtwertAktuellerWarenbestand() : Double
    +berechneGesamtbetragVerkaufteWaren() : Double
    +öffnenBedienAnzeigePanel() : void
}

class Drehteller {
    -istOffen: Boolean
    +öffnen(): Boolean
    +entriegeln(): void
    +schliessen(): void
    -aktuellesFach: Fach
    +neueWareVonBarcodeLeser(Warenname: String, Preis: Fixed(2,2), Verfallsdatum: Date): void
    +drehen() : void
    +berechneAktuellenWarenwert(Double) : Double
}


class Fach {
}

class Ware {
    -name: String
    -preis: Double
    -verfallsdatum: Date
    +Ware(in Preis: Double, in Name: String, in Verfallsdatum: Date) : Ware
    +gibAktuellenWarenwert() : Double
    +istAbgelaufen() : Boolean
}

class Kaufeintrag {
    -ware: Ware
    -verkaufsdatum: Date
    +Kaufeintrag(in Ware: Ware, in Verkaufsdatum: Date)
}

class Kasse {
    -kundenguthaben: Double
    +setzteAnzahlEingeworfen() : void
    +einwerfen(Fixed [1,2]) : Boolean
    +prüfeEingeworfeneMünzart() : MuenzSaeule
    +gibGeldZurück() : void
    +prüfeKundenguthaben(Kaufpreis: Double) : Boolean
    +prüfeWechselgeld(Kaufpreis: Double) : Boolean
    +aktualisiereKundenGuthaben(Double) : Double
}

class MuenzSaeule {
    -MAX = 100
    -wert : Fixed [1,2]
    +anzahlEingeworfen: UInteger
    -anzahl : UInteger
    +addiereMünze() : Boolean
    +setzeAnzahlEingeworfeneMünzenZurück() : void
}

class Drehtelleranzeige {
    -preis: Anzeige
    -warenzustand: Lampe
    +anzeigen(Ware) : void
}

class GeldbetragStatusAnzeige{
    -geldbetrag: Anzeige
    -statusWechselgeld: Lampe
    -statusGenugGeld: Lampe
    -zurueckgebenGeld() : void
    +zeigeKundenguthaben(Kundenguthaben: Double) : void
}

class BedienAnzeigePanel{
    -istGeöffnet : Boolean
    +hinzufuegenMuenzen(münzWert: Fixed [1,2], Anzahl: Integer) : Integer
    +zeigeGesamtwertAktuellerWarenbestand() : Double
    +zeigeGesamtbetragVerkaufteWaren() : Double
    +ausgebenStatistik(wahrenbezeichnung: String, Datum: Date) : UInteger
}

abstract "AnzeigeGruppe {abstract}"{
    -anzeige1: Anzeige
    -anzeige2: Anzeige
    -anzeige3: Anzeige
}

class Anzeige{
    -text: String
    +anzeigen(Text: String)
}

class Lampe {
    -leuchtet: Boolean
    -farbe: String[6]
    +ausschalten() : void
    +zeigeWeiss() : void
    +zeigeGrün() : void
    +zeigeRot() : void

}


class WechselgeldbestandAnzeigeGruppe{
    +zeigeWertVerkaufteWare(wert: Fixed[5,2]): void
    +zeigeMuenzArt(wert: int): void
    +zeigeAnzahl(anzahl: int): void
    +zeigeGesamtwert(Gesamtbetrag: Double) : void
}

class WarenVerwaltungAnzeigeGruppe{
    +zeigeWare(ware: Ware)
}

class VerkaufserfolgAnzeigeGruppe {
    +zeigeWare(ware: Ware) : void
    +zeigeDatumPer(datum: Date) : void
    +zeigeAnzahl() : void
}

Automat "1" -- "7" Drehteller
Drehteller "1" -- "16" Fach
Fach "1" -- "0..1" Ware
Automat "1" -- "0..*" Kaufeintrag
Ware "1" -- "0..1" Kaufeintrag
Automat "1" -- "1" Kasse
Kasse "1" -- "5" MuenzSaeule
Drehteller "1" -- "1" Drehtelleranzeige
Drehtelleranzeige "1" -- "1" Lampe
Drehtelleranzeige "1" -- "1" Anzeige
Automat "1" -- "1" BedienAnzeigePanel
BedienAnzeigePanel "1" -- "3" "AnzeigeGruppe {abstract}"
Automat "1" -- "1" GeldbetragStatusAnzeige
GeldbetragStatusAnzeige "1" -- "1" Anzeige
GeldbetragStatusAnzeige "1" -- "2" Lampe
"AnzeigeGruppe {abstract}" "1" -- "3" Anzeige
"AnzeigeGruppe {abstract}" <|-- WechselgeldbestandAnzeigeGruppe
"AnzeigeGruppe {abstract}" <|-- WarenVerwaltungAnzeigeGruppe
"AnzeigeGruppe {abstract}" <|-- VerkaufserfolgAnzeigeGruppe

@enduml
```

### 4.2.2 Objektdiagramm

```plantuml
@startuml
skinparam objectFontSize 15
skinparam objectAttributeFontSize 15
skinparam arrowFontSize 20

object ":Automat" as Automat {

}

object "1:Drehteller" as Drehteller1 {
    -istOffen: Boolean
}

object "2:Drehteller" as Drehteller2 {
    -istOffen: Boolean
}

object "1:Fach" as Fach {
}

object "2:Fach" as Fach {
}

object "3:Fach" as Fach {
}

object "Mars" as Ware {
    -name: Mars
    -preis: 1.50
    -verfallsdatum: 20.08.2022
}

object Kaufeintrag {
    -ware: Ware
    -verkaufsdatum: Date
    +Kaufeintrag(in Ware: Ware, in Verkaufsdatum: Date)
}

object Kasse {
    -kundenguthaben: Double
}

object MuenzSaeule {
    -MAX = 100
    -wert : Fixed [1,2]
    +anzahlEingeworfen: UInteger
    -anzahl : UInteger
}


@enduml
```

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
            participant "Drehteller:Anzeige" as Anzeige2
            Drehtelleranzeige -> Anzeige2 : anzeigen("1.50")
            activate Anzeige2
            deactivate Anzeige2
            participant ":Lampe" as Lampe
            Drehtelleranzeige -> Lampe : zeigeGrün()
            activate Lampe
            deactivate Lampe
        deactivate Drehteller
        deactivate Drehtelleranzeige
    deactivate Automat
    
Kundin -> Automat : einwerfen()
activate Automat
Automat -> Automat : überprüfeMünzeinwurf()
activate Automat
deactivate Automat
participant ":Kasse" as Kasse
Automat -> Kasse : einwerfen()
        activate Kasse
        participant "2.00:MuenzSaeule" as MuenzSaeule
        Kasse -> Kasse : prüfeEingeworfeneMünzart() : MuenzSaeule
        activate Kasse
        deactivate Kasse
        Kasse -> MuenzSaeule : addiereMünze()
        activate MuenzSaeule
        MuenzSaeule -> MuenzSaeule: +1 anzahl
        activate MuenzSaeule
        deactivate MuenzSaeule
        MuenzSaeule -> MuenzSaeule: +1 anzahlEingeworfene
        activate MuenzSaeule
        deactivate MuenzSaeule
        deactivate MuenzSaeule
        Kasse -> Kasse : +2 Kundenguthaben
        activate Kasse
        deactivate Kasse
        Kasse -> Automat : getGeldbetragStatusAnzeige()
        activate Automat
        deactivate Automat
        participant ":GeldbetragStatusAnzeige" as GeldbetragStatusAnzeige
        Kasse -> GeldbetragStatusAnzeige : zeigeKundenguthaben(2.00)
        activate GeldbetragStatusAnzeige
        participant "Guthaben:Anzeige" as Anzeige
        GeldbetragStatusAnzeige -> Anzeige : anzeigen("2.00")
        activate Anzeige
        deactivate Anzeige
        deactivate GeldbetragStatusAnzeige
        deactivate Kasse
        deactivate Automat

Kundin -> Automat : einwerfen()
activate Automat
Automat -> Automat : überprüfeMünzeinwurf()
activate Automat
deactivate Automat
participant ":Kasse" as Kasse
Automat -> Kasse : einwerfen()
        activate Kasse
        participant "0.50:MuenzSaeule" as MuenzSaeule2
        Kasse -> Kasse : prüfeEingeworfeneMünzart() : MuenzSaeule2
        activate Kasse
        deactivate Kasse
        Kasse -> MuenzSaeule2 : addiereMünze()
        activate MuenzSaeule2
        MuenzSaeule2 -> MuenzSaeule2: +1 anzahl
        activate MuenzSaeule2
        deactivate MuenzSaeule2
        MuenzSaeule2 -> MuenzSaeule2: +1 anzahlEingeworfene
        activate MuenzSaeule2
        deactivate MuenzSaeule2
        deactivate MuenzSaeule2
        Kasse -> Kasse : +0.5 Kundenguthaben
        activate Kasse
        deactivate Kasse
        Kasse -> Automat : getGeldbetragStatusAnzeige()
        activate Automat
        deactivate Automat
        participant ":GeldbetragStatusAnzeige" as GeldbetragStatusAnzeige
        Kasse -> GeldbetragStatusAnzeige : zeigeKundenguthaben(2.50)
        activate GeldbetragStatusAnzeige
        participant "Guthaben:Anzeige" as Anzeige
        GeldbetragStatusAnzeige -> Anzeige : anzeigen("2.50")
        activate Anzeige
        deactivate Anzeige
        deactivate GeldbetragStatusAnzeige
        deactivate Kasse
        deactivate Automat

Kundin -> Drehteller : öffnen()
    activate Drehteller
    Drehteller -> Automat : prüfeSchiebetürenGeschlossen
    activate Automat
    deactivate Automat
    Drehteller -> Fach : getWare()
    note right
        Wenn getWare() keine Ware zurück gibt, dann ist das Fach leer
        end note
    activate Fach
    deactivate Fach
    Drehteller -> Ware : getPreis()
    activate Ware
    deactivate Ware
    Drehteller -> Kasse : prüfeKundenguthaben(Preis : Double) : Boolean
    activate Kasse
    deactivate Kasse
    Drehteller -> Kasse : prüfeWechselgeld(Preis : Double) : Boolen
    activate Kasse
    deactivate Kasse
    Drehteller -> Ware : istAbgelaufen()
    activate Ware
    
    participant ":Kaufeintrag" as Kaufeintrag
    Ware --> Kaufeintrag : create
    activate Kaufeintrag
    Ware -> Kaufeintrag : Kaufeintrag(Ware: Ware, Datum: Date )
    deactivate Kaufeintrag
    Ware -> Fach : getDrehteller()
    activate Fach
    Fach -> Drehteller : getAutomat
    activate Drehteller
    deactivate Drehteller
    deactivate Fach
    Drehteller -> Automat : getKasse()
    activate Automat
    deactivate Automat
    Ware -> Kasse : aktualisiereKundenGuthaben(Preis : Double)
    activate Kasse
    Kasse -> Automat : getGeldbetragStatusAnzeige
    activate Automat
    deactivate Automat
    Kasse -> GeldbetragStatusAnzeige : zeigeKundenguthaben(1.00)
    activate GeldbetragStatusAnzeige
    deactivate GeldbetragStatusAnzeige
    Ware -> Kasse : setzteAnzahlEingeworfen()
    deactivate Kasse
    deactivate Ware
    Drehteller -> Drehteller :entriegeln()
    deactivate Drehteller

Kundin -> Drehteller : schliessen()
    activate Drehteller
    deactivate Drehteller

Kundin -> Drehteller : oeffneFach()
    activate Drehteller
    Drehteller -> Automat : prüfeSchiebetürenGeschlossen()
    activate Automat
    deactivate Automat
    Drehteller -> Fach : getWare()
    note right
        Wenn getWare() keine Ware zurück gibt, dann ist das Fach leer
        end note
    activate Fach
    deactivate Fach
    Drehteller -> Ware : getPreis()
    activate Ware
    deactivate Ware
    Drehteller -> Kasse : prüfeKundenguthaben(Preis : Double) : Boolean
    activate Kasse
    deactivate Kasse
    Drehteller -> Automat : getGeldbetragStatusAnzeige()
    activate Automat
    deactivate Automat
    Drehteller -> GeldbetragStatusAnzeige : getStatusGenugGeld()
    activate GeldbetragStatusAnzeige
    deactivate GeldbetragStatusAnzeige
    participant "statusGenugGeld:Anzeige" as statusGenugGeld
    Drehteller -> statusGenugGeld : zeigeRot()
    activate statusGenugGeld
    deactivate statusGenugGeld
    deactivate Drehteller

Kundin -> Kasse : gibGeldZurück()
    activate Kasse
    Kasse -> MuenzSaeule2 : addiereMünze(-2)
    activate MuenzSaeule2
    deactivate MuenzSaeule2
    deactivate Kasse

@enduml
```

#### b) Warenwert des Automaten berechnen

```plantuml
@startuml

mainframe ** Warenwert des Automaten berechnen **

participant ":Automat" as Automat   
participant ":Drehteller" as Drehteller
participant ":Fach" as Fach

activate Automat
    Automat -> Automat : berechneGesamtwertAktuellerWarenbestand() : Double
    activate Automat
        loop Drehteller <= 7
            Automat -> Drehteller : gibGesamtWert(): Double
            participant ":Ware" as Ware
            activate Drehteller
                loop Fach <= 16
                    Drehteller -> Fach : gibWare(): Ware
                    activate Fach
                    deactivate Fach
                    opt Wenn Ware in Fach vorhanden
                        Drehteller -> Ware : gibAktuellenWarenwert() : aktuellerWert
                        activate Ware
                        deactivate Ware
                        note right
                            Es wird geprüft, ob Ware abgelaufen ist. Wenn ja,
                            wird Warenwert um 75% reduziert und auf 10 Rappen gerundet.
                            end note
                        Drehteller -> Drehteller : berechneAktuellenWarenwert(aktuellerWert) : Double
                        note right
                            Der in "gibAktuellenWarenwert" ermittelte Wert wird
                            zur Gesamtsumme des Drehtellers hinzugefuegt.
                            end note
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
