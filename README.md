from datetime import datetime, timedelta

class Szoba:
    def __init__(self, szobaszam, ar):
        self.szobaszam = szobaszam
        self.ar = ar

class EgyagyasSzoba(Szoba):
    def __init__(self, szobaszam):
        super().__init__(szobaszam, 10000)  # Példaként az egyágyas szoba ára 10000 HUF

class Ketagyasszoba(Szoba):
    def __init__(self, szobaszam):
        super().__init__(szobaszam, 15000)  # Példaként a kétágyas szoba ára 15000 HUF

class Szalloda:
    def __init__(self, nev):
        self.nev = nev
        self.szobak = []

    def uj_szoba(self, szoba):
        self.szobak.append(szoba)

class Foglalas:
    def __init__(self, szoba, datum):
        self.szoba = szoba
        self.datum = datum

class FoglalasKezelo:
    def __init__(self, szalloda):
        self.szalloda = szalloda
        self.foglalasok = []

    def foglalas(self, szobaszam, datum):
        for szoba in self.szalloda.szobak:
            if szoba.szobaszam == szobaszam:
                foglalas_datum = datetime.strptime(datum, "%Y-%m-%d")
                if foglalas_datum >= datetime.now() and not self.szoba_foglalt(szobaszam, datum):
                    foglalas = Foglalas(szoba, foglalas_datum)
                    self.foglalasok.append(foglalas)
                    return f"A(z) {szobaszam}. számú szoba foglalása sikeres volt {datum}-ra."
                else:
                    return "Hiba: A megadott dátum nem érvényes vagy a szoba már foglalt."
        return "Hiba: A megadott szoba nem található."

    def szoba_foglalt(self, szobaszam, datum):
        foglalas_datum = datetime.strptime(datum, "%Y-%m-%d")
        for foglalas in self.foglalasok:
            if foglalas.szoba.szobaszam == szobaszam and foglalas.datum == foglalas_datum:
                return True
        return False

    def lemondas(self, szobaszam, datum):
        foglalas_datum = datetime.strptime(datum, "%Y-%m-%d")
        for foglalas in self.foglalasok:
            if foglalas.szoba.szobaszam == szobaszam and foglalas.datum == foglalas_datum:
                self.foglalasok.remove(foglalas)
                return f"A(z) {szobaszam}. számú szoba foglalása törölve lett {datum}-ra."
        return "Hiba: A megadott foglalás nem található."

    def listaz_foglalasok(self):
        if self.foglalasok:
            foglalasok_str = "Foglalások:\n"
            for foglalas in self.foglalasok:
                foglalasok_str += f"{foglalas.szoba.szobaszam} szoba - {foglalas.datum.strftime('%Y-%m-%d')}\n"
            return foglalasok_str
        else:
            return "Nincsenek foglalások."

# Teszt adatok
szalloda = Szalloda("Teszt Szalloda")
szalloda.uj_szoba(EgyagyasSzoba("101"))
szalloda.uj_szoba(Ketagyasszoba("201"))
szalloda.uj_szoba(EgyagyasSzoba("102"))

foglalaskezelo = FoglalasKezelo(szalloda)
foglalaskezelo.foglalas("101", "2024-05-15")
foglalaskezelo.foglalas("201", "2024-05-16")
foglalaskezelo.foglalas("102", "2024-05-17")
foglalaskezelo.foglalas("101", "2024-05-18")
foglalaskezelo.foglalas("201", "2024-05-19")

# Felhasználói interfész
print("Üdvözöljük a Teszt Szállodában!")
while True:
    print("\nVálasszon műveletet:")
    print("1 - Foglalás")
    print("2 - Lemondás")
    print("3 - Foglalások listázása")
    print("0 - Kilépés")
    valasztas = input("Művelet kiválasztása: ")

    if valasztas == "1":
        szobaszam = input("Kérem adja meg a szoba számát: ")
        datum = input("Kérem adja meg a foglalás dátumát (ÉÉÉÉ-HH-NN): ")
        print(foglalaskezelo.foglalas(szobaszam, datum))
    elif valasztas == "2":
        szobaszam = input("Kérem adja meg a szoba számát: ")
        datum = input("Kérem adja meg a foglalás dátumát (ÉÉÉÉ-HH-NN): ")
        print(foglalaskezelo.lemondas(szobaszam, datum))
    elif valasztas == "3":
        print(foglalaskezelo.listaz_foglalasok())
    elif valasztas == "0":
        print("Kilépés...")
        break
    else:
        print("Hibás választás. Kérem válasszon újra.")
