# Integracija forme sa Google Sheets (najjednostavniji način)

Ovo uputstvo će vam pokazati kako da povežete vašu postojeću HTML formu sa Google Sheet-om na najjednostavniji mogući način, koristeći servis za automatizaciju kao što je Make (ranije Integromat) ili Zapier. Ovi servisi eliminišu potrebu za pisanjem backend koda i direktnim upravljanjem Google API kredencijalima, što proces čini mnogo bržim i lakšim.

## Koncept

Ideja je sledeća:

1.  Kada korisnik popuni i pošalje formu na vašem sajtu, podaci se šalju na **Webhook URL** koji obezbeđuje servis za automatizaciju (npr. Make ili Zapier).
2.  Servis za automatizaciju (Make/Zapier) prima te podatke.
3.  Servis za automatizaciju zatim uzima te podatke i automatski ih upisuje u vaš **Google Sheet**.

Ovo je rešenje koje ne zahteva nikakav backend kod sa vaše strane, a forma ostaje lepo dizajnirana kao što je sada.

## Korak 1: Priprema Google Sheet-a

Pre nego što počnete, uverite se da je vaš Google Sheet spreman za prijem podataka:

1.  **Kreirajte novi Google Sheet** (ili koristite postojeći) i dajte mu jasan naziv, npr. "Goran Fitness Prijave".
2.  U prvom redu (zaglavlju) Sheet-a, unesite nazive kolona koje odgovaraju poljima u vašoj formi. Važno je da nazivi kolona budu jasni i da odgovaraju podacima koje očekujete. Preporučujem sledeće kolone:
    *   `Datum i Vreme`
    *   `Ime i Prezime`
    *   `Email`
    *   `Telefon`
    *   `Godine`
    *   `Iskustvo`
    *   `Ciljevi`
    *   `Paket`

    Primer kako bi prvi red izgledao:
    `Datum i Vreme | Ime i Prezime | Email | Telefon | Godine | Iskustvo | Ciljevi | Paket`

## Korak 2: Kreiranje Webhook-a u Make (ili Zapier)

### Korišćenje Make (preporučeno zbog fleksibilnosti i cene)

1.  Idite na [Make.com](https://www.make.com/) i prijavite se ili kreirajte nalog.
2.  Kliknite na **"Create a new scenario"**.
3.  Kao prvi modul, pretražite i izaberite **"Webhooks"**.
4.  Izaberite triger **"Custom webhook"**.
5.  Kliknite na **"Create a webhook"** i dajte mu ime (npr. "GoranFitnessForm").
6.  Make će vam generisati **Webhook URL**. Kopirajte ovaj URL – on će vam trebati u sledećem koraku.
7.  Make će sada čekati da primi prve podatke kako bi automatski detektovao strukturu forme. Ostavite ovaj prozor otvoren.

### Korišćenje Zapier (alternativa)

1.  Idite na [Zapier.com](https://zapier.com/) i prijavite se ili kreirajte nalog.
2.  Kliknite na **"Create Zap"**.
3.  Kao triger, pretražite i izaberite **"Webhooks by Zapier"**.
4.  Izaberite triger **"Catch Hook"** i kliknite "Continue".
5.  Zapier će vam generisati **Webhook URL**. Kopirajte ovaj URL.
6.  Zapier će sada čekati da primi prve podatke. Ostavite ovaj prozor otvoren.

## Korak 3: Modifikacija HTML Forme

Sada treba da izmenite vaš `index.html` fajl kako bi forma slala podatke na Webhook URL koji ste dobili u Koraku 2.

1.  Otvorite `index.html` fajl u tekst editoru.
2.  Pronađite `<form>` tag. Trenutno izgleda ovako:
    ```html
    <form id="coaching-form">
    ```
3.  Izmenite ga tako da uključuje `action` i `method` atribute sa vašim Webhook URL-om. **Zamenite `YOUR_WEBHOOK_URL` sa URL-om koji ste kopirali iz Make-a ili Zapier-a.**
    ```html
    <form id="coaching-form" action="YOUR_WEBHOOK_URL" method="POST">
    ```
    **Važno:** Uklonite `event.preventDefault();` iz `script.js` fajla za formu, jer sada želimo da se forma ponaša kao standardna HTML forma i šalje podatke na `action` URL. Pronađite deo koda u `script.js` koji počinje sa `form.addEventListener(\'submit\', function(e) { e.preventDefault();` i obrišite `e.preventDefault();` liniju.

4.  Sačuvajte izmenjeni `index.html` fajl.

## Korak 4: Slanje test podataka i mapiranje u Make (ili Zapier)

1.  Postavite izmenjeni `index.html` fajl na vaš web server (ili ga otvorite lokalno u pretraživaču ako testirate). 
2.  Idite na vaš sajt i **popunite formu sa test podacima**, a zatim kliknite na dugme "Pošaljite Prijavu".
3.  Vratite se u Make (ili Zapier) prozor. Trebalo bi da vidite da je Webhook modul primio podatke i da je uspešno detektovao strukturu. Potvrdite da su svi podaci iz forme prepoznati.

### U Make:

1.  Nakon što Webhook primi podatke, kliknite na **"OK"**.
2.  Dodajte novi modul klikom na **"Add another module"** (znak plus).
3.  Pretražite i izaberite **"Google Sheets"**.
4.  Izaberite akciju **"Add a Row"**.
5.  Povežite svoj Google nalog sa Make-om (pratićete uputstva za autorizaciju).
6.  Izaberite **Spreadsheet** (vaš "Goran Fitness Prijave" Sheet) i **Sheet Name** (obično "Sheet1").
7.  Make će vam prikazati kolone iz vašeg Google Sheet-a. Sada treba da **mapirate podatke** koje je primio Webhook sa odgovarajućim kolonama u Google Sheet-u. Na primer, `name` iz Webhook-a mapirajte u kolonu `Ime i Prezime`, `email` u `Email`, itd.
8.  Za kolonu `Datum i Vreme`, možete koristiti funkciju `now` iz Make-a da automatski unosite trenutni datum i vreme prijave.
9.  Sačuvajte i aktivirajte vaš scenario u Make-u.

### U Zapier:

1.  Nakon što Webhook primi podatke, kliknite na **"Continue"**.
2.  Dodajte novi korak klikom na **"Add a step"**.
3.  Pretražite i izaberite **"Webhooks by Zapier"**.
4.  Izaberite akciju **"Create Spreadsheet Row"**.
5.  Povežite svoj Google nalog sa Zapier-om.
6.  Izaberite **Spreadsheet** i **Worksheet**.
7.  Zapier će vam prikazati kolone iz vašeg Google Sheet-a. Sada treba da **mapirate podatke** koje je primio Webhook sa odgovarajućim kolonama. 
8.  Za kolonu `Datum i Vreme`, možete koristiti ugrađenu Zapier funkciju za trenutni datum i vreme.
9.  Sačuvajte i aktivirajte vaš Zap.

## Korak 5: Testiranje i postavljanje

1.  Ponovo popunite formu na vašem sajtu sa novim podacima.
2.  Proverite vaš Google Sheet. Podaci bi trebalo da se automatski pojave u novom redu.
3.  Kada ste sigurni da sve funkcioniše, možete postaviti izmenjeni `index.html` fajl na vaš hosting (ili ga gurnuti na GitHub Pages ako ste tamo hostovani).

Ovo rešenje je izuzetno fleksibilno i omogućava vam da lako menjate destinaciju podataka (npr. da ih šaljete i na email, CRM sistem, itd.) bez menjanja koda na sajtu, samo kroz konfiguraciju u Make-u ili Zapier-u.



