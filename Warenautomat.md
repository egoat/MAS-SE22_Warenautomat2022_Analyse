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
|**Eingehende Information** | geöffnetes Fach, Münzeinwurf
|**Ergebnis**               | Der Kunde kann das Produkt aus dem Fach entnehmen. Der Automat gibt korrektes Rückgeld.
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
|                           | 12. Kunde öffnet Tür und entnimmt Produkt
|                           | 13. Automat aktualisiert Statuszeige
|                           | 14. Automat aktualisiert interne Statistik (Produkt)
|                           | 15. Automat aktualisiert Anzeige Geldbetrag
|                           | 16. Kunde schliesst Schiebetür
|                           | 17. Automat verriegelt Schiebetür
|                           | 18. Kunde drückt Geldrückgabeknopf
|                           | 19. Automat gibt Rückgeld aus falls geschuldet und aktualisiert Münzenbestand
|                           | 20. Automat aktualisiert Anzeige Geldbetrag
|                           | 21. Kunde entnimmt Rückgeld
|**Erweiterungen**          | -
|**Alternativen**           | 5a [Münzsäule voll] Eingeworfene Münze wird wieder ausgegeben.
|                           | 9a [nicht genug Geld eingeworfen] Anezige "nicht genug Geld" wird angezeigt. Schiebetür bleibt geschlossen.
|                           | 10a [kein Wechselgeld vorhanden] Anzeige "kein Wechselgeld" wird angezeigt. Schiebetür bleibt geschlossen.
|                           | 16a [Kunde schliesst Schiebetür nicht] Drehteller können nicht gedreht werden.
|                           | 18a [Kunde wählt ein weiteres Produkt] Zurück zu 1.

---

## Aufgabe 2: Statisches und dynamisches Modell

```plantuml
@startuml
skinparam classFontSize 15
skinparam classAttributeFontSize 15
skinparam arrowFontSize 20

class "Automat" as A {
    drehen(): void
}

class "Drehteller" as D {
    istOffen: Boolean
    istVerriegelt: Boolean
    öffnen(): Boolean
    schliessen(): void
    entriegeln(): void
    neueWareVonBarcodeLeser(Warenname: String, Preis: Fixed(2,2), Verfallsdatum: Date): void
}

class "Fach" as F {
    istGefüllt: Boolean
}


class "Produkt" as P {
    Name: String
    Preis: Double
    Verfallsdatum: Date
}

class "Log" as L {

}

class "Kaufeintrag" as E {
    datum: Date


}

class "Kasse" as K {
    Anz10Rappen: Integer
    Anz20Rappen: Integer
    Anz50Rappen: Integer
    Anz1Franken: Integer
    Anz2Franken: Integer

    MünzeAddieren()
    

}

class "Drehtelleranzeige" as DAZ {
    Warenpreis: Fixed [1,2]
    Status: UInteger

}




A "1" -- "1" L
A "1" -- "1" K
L "1" -- "0..*" E

A "1" -- "7" D
D "1" -- "16" F
F "1" -- "0..1" P

D -- DAZ

P "1" -- "0..1" E

@enduml
```

## UML-Squenzdiagramme

```plantuml
@startuml

actor " " as B
participant ":Auftrag" as A
participant ":Auftragsposition" as P
participant ":Maschine" as M
participant ":Kunde" as K

activate B

B -> A : berechneKosten()
activate A

loop 1..*
    A -> P : getStückzahl()
    activate P
    A <-- P : Stückzahl
    deactivate P

    A -> P : getMaschine()
    activate P
    A <-- P : M
    deactivate P

    A -> M : getMaschinenleistung()
    activate M
    A <-- M : Leistung
    deactivate M

    A -> M : getKostenProStunde()
    activate M
    A <-- M : Kosten
    deactivate M
end

A -> K : getBestanskunde()
activate K
A <-- K : istBestandskunde
deactivate K

opt "ist Bestandskunde"
    A -> A : berechneRabatt(istBestandskunde)
end

B <-- A : Kosten
deactivate A

@enduml
```