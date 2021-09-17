---
title: Liste mit unterstützten Klassifizierungen
description: Auf dieser Seite sind die in Azure Purview unterstützten Systemklassifizierungen aufgelistet.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 4/1/2021
ms.openlocfilehash: 9eed079b7beda59f4835bb1263a226e2aa7f74c2
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272618"
---
# <a name="supported-classifications-in-azure-purview"></a>Unterstützte Klassifizierungen in Azure Purview

In diesem Artikel sind die Systemklassifizierungen aufgelistet, die in Azure Purview (Vorschauversion) unterstützt werden und definiert sind.


- **Schwellenwert für Datenunterschiede**: Die Gesamtanzahl der unterschiedlichen Datenwerte, die in einer Spalte gefunden werden müssen, bevor der Scanner das Datenmuster für die Spalte ausführt. Der Schwellenwert für Datenunterschiede hat nichts mit dem Musterabgleich zu tun, aber er ist eine Voraussetzung für den Musterabgleich. Unsere Systemklassifizierungsregeln erfordern, dass in jeder Spalte mindestens 8 unterschiedliche Werte vorhanden sein müssen, um sie einer Klassifizierung zu unterziehen. Das System benötigt diesen Wert, um sicherzustellen, dass die Spalte genügend Daten enthält, damit der Scanner sie genau klassifizieren kann. Beispielsweise wird eine Spalte, die mehrere Zeilen enthält, die alle den Wert 1 enthalten, nicht klassifiziert. Spalten, in denen eine Zeile einen Wert enthält und die restlichen Zeilen NULL-Werte enthalten, werden ebenfalls nicht klassifiziert. Wenn Sie mehrere Muster angeben, gilt dieser Wert für jedes dieser Muster.

- **Schwellenwert für Mindestübereinstimmung** (Minimum match threshold): Der Mindestprozentsatz für die Datenwertübereinstimmungen in einer Spalte, die vom Scanner gefunden werden müssen, damit die Klassifizierung angewendet wird. Der Systemklassifizierungswert ist auf 60 % festgelegt.


## <a name="defined-system-classifications"></a>Definierte Systemklassifizierungen

In Azure Purview werden Daten basierend auf [regulären Ausdrücken](https://wikipedia.org/wiki/Regular_expression) und einem [Bloom-Filter](https://wikipedia.org/wiki/Bloom_filter) klassifiziert. In der folgenden Liste sind das Format, das Muster und die Schlüsselwörter für die definierten Systemklassifizierungen von Azure Purview beschrieben.

Jeder Klassifizierungsname enthält das Präfix „MICROSOFT“.

## <a name="bloom-filter-classifications"></a>Klassifizierungen für Bloom-Filter

## <a name="city-country-and-place"></a>City, Country & Place (Ort, Land und Standort)

Für die Filter vom Typ „Ort, Land und Standort“ wurden die besten verfügbaren Datasets für die Aufbereitung der Daten genutzt.

## <a name="person"></a>Person

Für die Datenaufbereitung für den Bloom-Filter vom Typ „Person“ wurden die beiden folgenden Datasets verwendet:

- [US-Volkszählungsdaten aus dem Jahr 2010: Nachnamen (162.000 Einträge)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Beliebte Babynamen (Quelle: US-Sozialversicherungsnummer): Jahre 1880 bis 2019 (98.000 Einträge)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Reguläre Ausdrücke: Klassifizierungen

## <a name="aba-routing"></a>ABA-Routing

### <a name="format"></a>Format

Neun Ziffern (formatiertes oder unformatiertes Muster)

### <a name="pattern"></a>Muster

Formatiert:

- Vier Ziffern, beginnend mit 0, 1, 2, 3, 6, 7 oder 8
- Ein Bindestrich
- Vier Ziffern
- Ein Bindestrich
- Eine Ziffer

Unformatiert: neun aufeinanderfolgende Ziffern, beginnend mit 0, 1, 2, 3, 6, 7 oder 8

### <a name="keywords"></a>Keywords

#### <a name="keyword_aba_routing"></a>Keyword\_aba\_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Nationale argentinische ID-Nummer (DNI)

### <a name="format"></a>Format

Acht Ziffern mit oder ohne Punkte

### <a name="pattern"></a>Muster

Acht Ziffern:

- Zwei Ziffern
- Optionaler Punkt
- Drei Ziffern
- Optionaler Punkt
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_argentina_national_id"></a>Keyword\_argentina\_national\_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Australische Bankkontonummer

### <a name="format"></a>Format

Sechs bis zehn Ziffern mit oder ohne Bank State Branch-Nummer

### <a name="pattern"></a>Muster

Die Kontonummer ist sechs bis zehn Ziffern lang.

Bank State Branch-Nummer für Australien:

- Drei Ziffern
- Ein Bindestrich
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_bank_account_number"></a>Keyword\_australia\_bank\_account\_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```
## <a name="australia-business-number"></a>Australische Geschäftsnummer

### <a name="format"></a>Format
elf Ziffern mit optionalen Trennzeichen

### <a name="pattern"></a>Muster
elf Ziffern mit optionalen Trennzeichen:

- Zwei Ziffern
- optionaler Bindestrich oder optionales Leerzeichen
- Drei Ziffern
- optionaler Bindestrich oder optionales Leerzeichen
- Drei Ziffern
- optionaler Bindestrich oder optionales Leerzeichen
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_business_number"></a>Keyword\_australia\_business\_number
```
australia business no
business number
abn#
businessid#
business id
abn
businessno#
```

## <a name="australia-company-number"></a>Australische Unternehmensnummer 

### <a name="format"></a>Format
neun Ziffern mit Trennzeichen

### <a name="pattern"></a>Muster
neun Ziffern mit Trennzeichen:

- Drei Ziffern
- Eine Leerstelle
- Drei Ziffern
- Eine Leerstelle
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_company_number"></a>Keyword\_australia\_company\_number
```
acn
australia company no
australia company no#
australia company number
australian company no
australian company no#
australian company number
```
## <a name="australia-drivers-license-number"></a>Australische Führerscheinnummer

### <a name="format"></a>Format
Neun Buchstaben und Ziffern

### <a name="pattern"></a>Muster
Neun Buchstaben und Ziffern:

- Zwei Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Zwei Ziffern
- Fünf Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)

oder

- Ein bis zwei optionale Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Vier bis neun Ziffern

oder

- Neun Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_drivers_license_number"></a>Keyword\_australia\_drivers\_license\_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword\_australia\_drivers\_license\_number\_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Australische Medicare-Nummer

### <a name="format"></a>Format

Zehn bis elf Ziffern

### <a name="pattern"></a>Muster

Zehn bis elf Ziffern:

- Erste Ziffer im Bereich 2 bis 6
- Neunte Ziffer ist eine Prüfziffer
- Zehnte Ziffer ist die Ausstellungsziffer
- Elfte Ziffer (optional) ist die individuelle Zahl

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_medicare_number"></a>Keyword\_Australia\_Medicare\_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Australische Reisepassnummer

### <a name="format"></a>Format

Ein Buchstabe, gefolgt von sieben Ziffern

### <a name="pattern"></a>Muster

Ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung), gefolgt von sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Keyword\_australia\_passport\_number

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Australische Steuernummer

### <a name="format"></a>Format

Acht bis neun Ziffern

### <a name="pattern"></a>Muster

Acht bis neun Ziffern, normalerweise wie folgt mit Leerstellen:

- Drei Ziffern
- Eine optionale Leerstelle
- Drei Ziffern
- Eine optionale Leerstelle
- Zwei bis drei Ziffern, wobei die letzte Ziffer eine Prüfziffer ist

### <a name="keywords"></a>Keywords

#### <a name="keyword_australia_tax_file_number"></a>Keyword\_australia\_tax\_file\_number

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```
## <a name="austria-identity-card"></a>Österreichischer Personalausweis

### <a name="format"></a>Format

Eine Kombination aus Buchstaben, Ziffern und Sonderzeichen mit 24 Zeichen

### <a name="pattern"></a>Muster

24 Zeichen:

- 22 Buchstaben (keine Beachtung der Groß-/Kleinschreibung), Ziffern, umgekehrte Schrägstriche, Schrägstriche oder Pluszeichen
- zwei Buchstaben (keine Beachtung der Groß-/Kleinschreibung), Ziffern, umgekehrte Schrägstriche, Schrägstriche, Pluszeichen oder Gleichheitszeichen

### <a name="keywords"></a>Keywords

#### <a name="keyword_austria_eu_national_id_card"></a>Keyword\_austria\_eu\_national\_id\_card

```
identity number
national id
personalausweis republik österreich
```
## <a name="austria-tax-identification-number"></a>Österreichische Abgabenkontonummer

### <a name="format"></a>Format

neun Ziffern mit optionalem Bindestrich und Schrägstrich

### <a name="pattern"></a>Muster

neun Ziffern mit optionalem Bindestrich und Schrägstrich:

- Zwei Ziffern
- Ein Bindestrich (optional)
- Drei Ziffern
- Ein Schrägstrich (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_austria_eu_tax_file_number"></a>Keywords\_austria\_eu\_tax\_file\_number

```
österreich
st.nr.
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
tax number
```
## <a name="austria-passport-number"></a>Österreichische Reisepassnummer

### <a name="format"></a>Format
Ein Buchstabe, gefolgt von einem optionalen Leerzeichen und sieben Ziffern

### <a name="pattern"></a>Muster
Eine Kombination aus einem Buchstaben, sieben Ziffern und einem Leerzeichen:

- ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung)
- eine Leerstelle (optional)
- Sieben Ziffern

### <a name="keywords"></a>Keywords
#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_austria_eu_passport_number"></a>Keywords\_austria\_eu\_passport\_number
```
reisepassnummer
reisepasse
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="austria-social-security-number"></a>Österreichische Sozialversicherungsnummer 

### <a name="format"></a>Format
Zehn Ziffern im angegebenen Format

### <a name="pattern"></a>Muster
Zehn Ziffern:

- drei Ziffern, die einer Seriennummer entsprechen
- eine Prüfziffer
- sechs Ziffern, die dem Geburtsdatum (TTMMJJ) entsprechen

### <a name="keywords"></a>Keywords

#### <a name="keywords_austria_eu_ssn_or_equivalent"></a>Keywords\_austria\_eu\_ssn\_or\_equivalent
```
austrian ssn
ehic number
ehic no
insurance code
insurancecode#
insurance number
insurance no
krankenkassennummer
krankenversicherung
socialsecurityno
socialsecurityno#
social security no
social security number
social security code
sozialversicherungsnummer
sozialversicherungsnummer#
soziale sicherheit kein
sozialesicherheitkein#
ssn#
ssn
versicherungscode
versicherungsnummer
zdravstveno zavarovanje
```

## <a name="austria-tax-identification-number"></a>Österreichische Abgabenkontonummer 

### <a name="format"></a>Format
neun Ziffern mit optionalem Bindestrich und Schrägstrich

### <a name="pattern"></a>Muster
neun Ziffern mit optionalem Bindestrich und Schrägstrich:

- Zwei Ziffern
- Ein Bindestrich (optional)
- Drei Ziffern
- Ein Schrägstrich (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_austria_eu_tax_file_number"></a>Keywords\_austria\_eu\_tax\_file\_number
```
österreich
st.nr.
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
tax number
```

## <a name="austria-value-added-tax"></a>Österreichische Umsatzsteuer-Identifikationsnummer 
### <a name="format"></a>Format
alphanumerisches Muster mit 11 Zeichen

### <a name="pattern"></a>Muster
alphanumerisches Muster mit 11 Zeichen:

- A oder a
- T oder t
- optionale Leerstelle
- U oder u
- optionale Leerstelle
- zwei oder drei Ziffern
- optionale Leerstelle
- Vier Ziffern
- optionale Leerstelle
- ein oder zwei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_austria_value_added_tax"></a>Keyword\_austria\_value\_added\_tax
```
vat number
vat#
austrian vat number
vat no.
vatno#
value added tax number
austrian vat
mwst
umsatzsteuernummer
mwstnummer
ust.-identifikationsnummer
umsatzsteuer-identifikationsnummer
vat identification number
atu number
uid number
```
## <a name="belgium-drivers-license-number"></a>Belgische Führerscheinnummer 

### <a name="format"></a>Format
Zehn Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
 
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_belgium_eu_drivers_license_number"></a>Keywords\_belgium\_eu\_driver's\_license\_number
```
rijbewijs
rijbewijsnummer
führerschein
führerscheinnummer
füehrerscheinnummer
fuhrerschein
fuehrerschein
fuhrerscheinnummer
fuehrerscheinnummer
permis de conduire
numéro permis conduire
```
## <a name="belgium-national-number"></a>Belgische Nationalregisternummer

### <a name="format"></a>Format
Elf Ziffern plus optionale Trennzeichen

### <a name="pattern"></a>Muster
Elf Ziffern plus Trennzeichen:

- Sechs Ziffern und zwei optionale Punkte im Format „JJ.MM.TT“ für das Geburtsdatum
- Ein optionales Trennzeichen: Punkt, Bindestrich, Leerstelle
- Drei aufeinanderfolgende Ziffern (ungerade für männlich, gerade für weiblich)
- Ein optionales Trennzeichen: Punkt, Bindestrich, Leerstelle
- Zwei Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_belgium_national_number"></a>Keyword\_belgium\_national\_number
```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="belgium-passport-number"></a>Belgische Reisepassnummer 

### <a name="format"></a>Format
zwei Buchstaben, gefolgt von sechs Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
zwei Buchstaben, gefolgt von sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_belgium_eu_passport_number"></a>Keywords\_belgium\_eu\_passport\_number
```
numéro passeport
paspoort nr
paspoort-nr
paspoortnummer
paspoortnummers
Passeport carte
Passeport livre
Pass-Nr
Passnummer
reisepass kein
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="belgium-value-added-tax-number"></a>Belgische Umsatzsteuer-Identifikationsnummer 
### <a name="format"></a>Format
alphanumerisches Muster mit 12 Zeichen

### <a name="pattern"></a>Muster
alphanumerisches Muster mit 12 Zeichen:

- Buchstabe B oder b
- Buchstabe E oder e
- Ziffer 0
- Ziffer zwischen 1 und 9
- optionaler Punkt oder Bindestrich bzw. optionales Leerzeichen
- Vier Ziffern
- optionaler Punkt oder Bindestrich bzw. optionales Leerzeichen
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_belgium_value_added_tax_number"></a>Keyword\_belgium\_value\_added\_tax\_number
```
nº tva
vat number
vat no
numéro t.v.a
umsatzsteuer-identifikationsnummer
umsatzsteuernummer
btw
btw#
vat#
```

## <a name="brazil-cpf-number"></a>Brasilianische CPF-Nummer

### <a name="format"></a>Format

Elf Ziffern, einschließlich einer Prüfziffer (formatiert oder unformatiert)

### <a name="pattern"></a>Muster

Formatiert:

- Drei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Bindestrich
- Zwei Ziffern als Prüfziffern

Unformatiert:

- Elf Ziffern, bei denen die letzten zwei Ziffern Prüfziffern sind

### <a name="keywords"></a>Keywords

#### <a name="keyword_brazil_cpf"></a>Keyword\_brazil\_cpf
```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Brasilianische Nummer juristischer Personen (CNPJ)

### <a name="format"></a>Format

14 Ziffern, einschließlich Registrierungsnummer, Zweigstellennummer und Prüfziffern sowie Trennzeichen

### <a name="pattern"></a>Muster

14 Ziffern plus Trennzeichen:

- Zwei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Punkt
- Drei Ziffern (diese ersten acht Ziffern stehen für die Registrierungsnummer)
- Ein Schrägstrich
- Vierstellige Zweigstellennummer
- Ein Bindestrich
- Zwei Ziffern als Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_brazil_cnpj"></a>Keyword\_brazil\_cnpj
```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>Brasilianischer Personalausweis (RG)

### <a name="format"></a>Format

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Muster

:::no-loc text="Registro Geral (old format):":::

- Zwei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Punkt
- Drei Ziffern
- Ein Bindestrich
- Eine Ziffer als Prüfziffer

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 Stellen
- Ein Bindestrich
- Eine Ziffer als Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_brazil_rg"></a>Keyword\_brazil\_rg
```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```
## <a name="bulgaria-passport-number"></a>Bulgarische Reisepassnummer 

### <a name="format"></a>Format
Neun Ziffern
### <a name="pattern"></a>Muster
neun Ziffern ohne Leerstellen und Trennzeichen
### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_bulgaria_eu_passport_number"></a>Keywords\_bulgaria\_eu\_passport\_number
```
номер на паспорта
номер на паспорт
паспорт №
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="bulgaria-uniform-civil-number"></a>Bulgarische Personenkennziffer

### <a name="format"></a>Format
Zehn Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Zehn Ziffern ohne Leerstellen und Trennzeichen

- sechs Ziffern, die dem Geburtsdatum (JJMMTT) entsprechen
- zwei Ziffern, die der Geburtenfolge entsprechen
- eine Ziffer, die dem Geschlecht entspricht. Eine gerade Ziffer steht für „männlich“, eine ungerade für „weiblich“
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_bulgaria_eu_national_id_card"></a>Keywords\_bulgaria\_eu\_national\_id\_card
```
bnn#
bnn
bucn#
bucn
edinen grazhdanski nomer
egn#
egn
identification number
national id
national number
nationalnumber#
nationalnumber
personal id
personal no
personal number
personalidnumber#
social security number
ssn#
ssn
uniform civil id
uniform civil no
uniform civil number
uniformcivilno#
uniformcivilno
uniformcivilnumber#
uniformcivilnumber
unique citizenship number
егн#
егн
единен граждански номер
идентификационен номер
личен номер
лична идентификация
лично не
национален номер
номер на гражданството
униформ id
униформ граждански id
униформ граждански не
униформ граждански номер
униформгражданскиid#
униформгражданскине.#
```

## <a name="canada-bank-account-number"></a>Kanadische Bankkontonummer

### <a name="format"></a>Format

Sieben oder zwölf Ziffern

### <a name="pattern"></a>Muster

Eine kanadische Bankkontonummer ist sieben oder zwölf Ziffern lang.

Kanadische Bankkonto-Überweisungsnummer:

- Fünf Ziffern
- Ein Bindestrich
- Drei Ziffern ODER
- Eine Null (&quot;0&quot;)
- Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_bank_account_number"></a>Keyword\_canada\_bank\_account\_number
```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Kanadische Führerscheinnummer

### <a name="format"></a>Format

Variiert je nach Provinz

### <a name="pattern"></a>Muster

Unterschiedliche Muster für Alberta, British Columbia, Manitoba, New Brunswick, Neufundland/Labrador, Nova Scotia, Ontario, Prince Edward Island, Quebec und Saskatchewan

### <a name="keywords"></a>Keywords

#### <a name="keyword_province_name_drivers_license_name"></a>Keyword\_[Name_der\_Provinz]\_drivers\_license\_name
```
- The province abbreviation, for example AB
- The province name, for example Alberta
```
#### <a name="keyword_canada_drivers_license"></a>Keyword\_canada\_drivers\_license

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Nummer des kanadischen Gesundheitsdiensts

### <a name="format"></a>Format

10 Stellen

### <a name="pattern"></a>Muster

10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_health_service_number"></a>Keyword\_canada\_health\_service\_number

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Kanadische Reisepassnummer

### <a name="format"></a>Format

Zwei Großbuchstaben, gefolgt von sechs Ziffern

### <a name="pattern"></a>Muster

Zwei Großbuchstaben, gefolgt von sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_passport_number"></a>Keyword\_canada\_passport\_number

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Persönliche kanadische Krankenversicherungsnummer (PHIN, Personal Health Identification Number)

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_canada_phin"></a>Keyword\_canada\_phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Keyword\_canada\_provinces

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Kanadische Sozialversicherungsnummer

### <a name="format"></a>Format

Neun Ziffern mit optionalen Bindestrichen oder Leerstellen

### <a name="pattern"></a>Muster

Formatiert:

- Drei Ziffern
- Ein Bindestrich oder eine Leerstelle
- Drei Ziffern
- Ein Bindestrich oder eine Leerstelle
- Drei Ziffern

Unformatiert: neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_sin"></a>Keyword\_sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Keyword\_sin\_collaborative

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Chilenische Personalausweisnummer

### <a name="format"></a>Format

Sieben bis acht Ziffern plus Trennzeichen, eine Prüfziffer oder ein Buchstabe

### <a name="pattern"></a>Muster

Sieben bis acht Ziffern plus Trennzeichen:

- Ein bis zwei Ziffern
- Optionaler Punkt
- Drei Ziffern
- Optionaler Punkt
- Drei Ziffern
- Ein Bindestrich
- Eine Ziffer oder ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung) als Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_chile_id_card"></a>Keyword\_chile\_id\_card

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Personalausweisnummer für Personen mit Wohnsitz in China (VRC)

### <a name="format"></a>Format

18 Ziffern

### <a name="pattern"></a>Muster

18 Ziffern:

- Sechs Ziffern als Adresscode
- Acht Ziffern im Format JJJJMMTT für das Geburtsdatum
- Drei Ziffern als Sortiercode
- Eine Ziffer als Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_china_resident_id"></a>Keyword\_china\_resident\_id

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Kreditkartennummer

### <a name="format"></a>Format

14 bis 16 Ziffern, die formatiert oder unformatiert sein können (dddddddddddddddd) und den Luhn-Test bestehen müssen.

### <a name="pattern"></a>Muster

Komplexes und zuverlässiges Muster, mit dem Karten aller Marken weltweit erkannt werden können, z. B. Visa, MasterCard, Discover Card, JCB, American Express, Geschenkkarten und Diner Card.

### <a name="keywords"></a>Keywords

#### <a name="keyword_cc_verification"></a>Keyword\_cc\_verification

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Keyword\_cc\_name

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Kroatische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist nur unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ verfügbar.

### <a name="format"></a>Format

Acht Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Keywords\_croatia\_eu\_driver's\_license\_number

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Kroatische ID-Kartennummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Personalausweisnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_croatia_id_card"></a>Keyword\_croatia\_id\_card

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="croatia-passport-number"></a>Kroatische Reisepassnummer
### <a name="format"></a>Format
neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_croatia_eu_passport_number"></a>Keywords\_croatia\_eu\_passport\_number
```
broj putovnice
br. Putovnice
br putovnice
```
## <a name="croatia-personal-identification-oib-number"></a>Kroatische persönliche ID-Nummer (OIB)

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

Elf Ziffern:

- 10 Stellen
- Letzte Ziffer ist eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_croatia_oib_number"></a>Keyword\_croatia\_oib\_number

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```
## <a name="cyprus-drivers-license-number"></a>Zypriotische Führerscheinnummer 

### <a name="format"></a>Format
zwölf Ziffern ohne Leerstellen und Trennzeichen
### <a name="pattern"></a>Muster
12 Stellen
### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_cyprus_eu_drivers_license_number"></a>Keywords\_cyprus\_eu\_driver's\_license\_number
```
άδεια οδήγησης
αριθμό άδειας οδήγησης
άδειες οδήγησης
```
## <a name="cyprus-identity-card"></a>Zypriotische Personalausweisnummer

### <a name="format"></a>Format
Zehn Ziffern ohne Leerstellen und Trennzeichen
### <a name="pattern"></a>Muster

10 Stellen
### <a name="keywords"></a>Keywords

#### <a name="keywords_cyprus_eu_national_id_card"></a>Keywords\_cyprus\_eu\_national\_id\_card
```
id card number
identity card number
kimlik karti
national identification number
personal id number
ταυτοτητασ
```
## <a name="cyprus-passport-number"></a>Zypriotische Reisepassnummer
### <a name="format"></a>Format
ein Buchstabe, gefolgt von 6 bis 8 Ziffern ohne Leerstellen oder Trennzeichen
### <a name="pattern"></a>Muster
ein Buchstabe, gefolgt von sechs bis acht Ziffern
### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_cyprus_eu_passport_number"></a>Keywords\_cyprus\_eu\_passport\_number
```
αριθμό διαβατηρίου
pasaportu
Αριθμός Διαβατηρίου
κυπριακό διαβατήριο
διαβατήριο#
διαβατήριο
αριθμός διαβατηρίου
Pasaport Kimliği
pasaport numarası
Pasaport no.
Αρ. Διαβατηρίου
```
#### <a name="keywords_cyprus_eu_passport_date"></a>Keywords\_cyprus\_eu\_passport\_date
```
expires on
issued on
```
## <a name="cyprus-tax-identification-number"></a>Zypriotische Steueridentifikationsnummer

### <a name="format"></a>Format
acht Ziffern und ein Buchstabe im angegebenen Muster

### <a name="pattern"></a>Muster

acht Ziffern und ein Buchstabe:

- 0 oder 9
- Sieben Ziffern
- ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords
#### <a name="keywords_cyprus_eu_tax_file_number"></a>Keywords\_cyprus\_eu\_tax\_file\_number
```
tax id
tax identification code
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tic#
tic
tin id
tin no
tin#
vergi kimlik kodu
vergi kimlik numarası
αριθμός φορολογικού μητρώου
κωδικός φορολογικού μητρώου
φορολογική ταυτότητα
φορολογικού κωδικού
```
## <a name="czech-republic-drivers-license-number"></a>Führerscheinnummer der Tschechischen Republik 
### <a name="format"></a>Format
zwei Buchstaben, gefolgt von sechs Ziffern
### <a name="pattern"></a>Muster
acht Buchstaben und Ziffern:

- Buchstabe E (keine Beachtung der Groß-/Kleinschreibung)
- ein Buchstabe
- Eine Leerstelle (optional)
- Sechs Ziffern
### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_czech_republic_eu_drivers_license_number"></a>Keywords\_czech\_republic\_eu\_driver's\_license\_number
```
řidičský prúkaz
řidičské průkazy
číslo řidičského průkazu
čísla řidičských průkazů
```

## <a name="czech-passport-number"></a>Reisepassnummer der Tschechischen Republik 
### <a name="format"></a>Format
acht Ziffern ohne Leerstellen und Trennzeichen
### <a name="pattern"></a>Muster
acht Ziffern ohne Leerstellen und Trennzeichen

### <a name="keywords"></a>Keywords
#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_czech_republic_eu_passport_number"></a>Keywords\_czech\_republic\_eu\_passport\_number
```
cestovní pas
číslo pasu
cestovní pasu
passeport no
čísla pasu
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="denmark-drivers-license-number"></a>Dänische Führerscheinnummer 

### <a name="format"></a>Format
Acht Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_denmark_eu_drivers_license_number"></a>Keywords\_denmark\_eu\_driver's\_license\_number
```
kørekort
kørekortnummer
```
## <a name="denmark-passport-number"></a>Dänische Reisepassnummer 
### <a name="format"></a>Format
neun Ziffern ohne Leerstellen und Trennzeichen
### <a name="pattern"></a>Muster
Neun Ziffern
### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_denmark_eu_passport_number"></a>Keywords\_denmark\_eu\_passport\_number
```
pasnummer
Passeport n°
pasnumre
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="denmark-personal-identification-number"></a>Dänische persönliche ID-Nummer

### <a name="format"></a>Format

Zehn Ziffern mit einem Bindestrich

### <a name="pattern"></a>Muster

Zehn Ziffern:

- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Ein Bindestrich
- Vier Ziffern, wobei die letzte eine Prüfziffer ist

### <a name="keywords"></a>Keywords

#### <a name="keyword_denmark_id"></a>Keyword\_denmark\_id

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```
## <a name="estonia-drivers-license-number"></a>Estnische Führerscheinnummer 
### <a name="format"></a>Format
zwei Buchstaben, gefolgt von sechs Ziffern
### <a name="pattern"></a>Muster
zwei Buchstaben und sechs Ziffern:

- die Buchstaben ET (keine Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_estonia_eu_drivers_license_number"></a>Keywords\_estonia\_eu\_driver's\_license\_number
```
-- permis de conduire
juhilubade numbrid
juhiloa number
juhiluba
```
## <a name="estonia-passport-number"></a>Estnische Reisepassnummer 
### <a name="format"></a>Format
ein Buchstabe, gefolgt von sieben Ziffern ohne Leerstellen oder Trennzeichen
### <a name="pattern"></a>Muster
ein Buchstabe, gefolgt von sieben Ziffern
### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_estonia_eu_passport_number"></a>Keywords\_estonia\_eu\_passport\_number
```
eesti kodaniku pass passi number passinumbrid document number document no dokumendi nr
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="estonia-personal-identification-code-isikukood"></a>Estnischer persönlicher Identifikationscode (isikukood)

### <a name="format"></a>Format
elf Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Elf Ziffern:

- eine Ziffer, die dem Geschlecht und dem Geburtsjahrhundert entspricht (ungerade Zahl „männlich“, gerade Zahl „weiblich“; 1-2: 19. Jahrhundert; 3-4: 20. Jahrhundert, 5-6: 21. Jahrhundert)
- sechs Ziffern, die dem Geburtsdatum (JJMMTT) entsprechen
- drei Ziffern, die einer Seriennummer entsprechen, um zwischen Personen zu unterscheiden, die am selben Tag geboren wurden
- eine Prüfziffer
### <a name="keywords"></a>Keywords
#### <a name="keywords_estonia_eu_national_id_card"></a>Keywords\_estonia\_eu\_national\_id\_card
```
id-kaart
ik
isikukood#
isikukood
maksu id
maksukohustuslase identifitseerimisnumber
maksunumber
national identification number
national number
personal code
personal id number
personal identification code
personal identification number
personalidnumber#
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="eu-debit-card-number"></a>EU-Debitkartennummer

### <a name="format"></a>Format

16 Ziffern

### <a name="pattern"></a>Muster

Komplexes und zuverlässiges Muster

### <a name="keywords"></a>Keywords

#### <a name="keyword_eu_debit_card"></a>Keyword\_eu\_debit\_card

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Keyword\_card\_terms\_dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Keyword\_card\_security\_terms\_dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Keyword\_card\_expiration\_terms\_dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>EU-Führerscheinnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Führerscheinnummer“.

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Schweden
- Vereinigtes Königreich:

## <a name="eu-national-identification-number"></a>EU-Personalausweisnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Personalausweisnummer“.

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Vereinigtes Königreich:

## <a name="eu-passport-number"></a>EU-Reisepassnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Reisepassnummer“ (Entitäten im EU-Reisepassnummer-Paket).

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Schweden
- Vereinigtes Königreich:

## <a name="eu-social-security-number-or-equivalent-identification"></a>EU-Sozialversicherungsnummer oder vergleichbare Nummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Sozialversicherungsnummer“ oder einer vergleichbaren vertraulichen Nummer.

- Österreich
- Belgien
- Kroatien
- Tschechisch
- Dänemark
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Portugal
- Spanien
- Schweden

## <a name="eu-tax-identification-number"></a>EU-Steueridentifikationsnummer

Dies sind die Entitäten des Vertraulichkeitsinformationstyps „EU-Steueridentifikationsnummer“.

- Österreich
- Belgien
- Bulgarien
- Kroatien
- Zypern
- Tschechisch
- Dänemark
- Estland
- Finnland
- Frankreich
- Deutschland
- Griechenland
- Ungarn
- Irland
- Italien
- Lettland
- Litauen
- Luxemburg
- Malta
- Niederlande
- Polen
- Portugal
- Rumänien
- Slowakei
- Slowenien
- Spanien
- Schweden
- Vereinigtes Königreich:

## <a name="finland-drivers-license-number"></a>Finnische Führerscheinnummer 

### <a name="format"></a>Format
Zehn Ziffern mit einem Bindestrich

### <a name="pattern"></a>Muster
zehn Ziffern mit einem Bindestrich:

- Sechs Ziffern
- Ein Bindestrich
- Drei Ziffern
- Eine Ziffer oder ein Buchstabe

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_finland_eu_drivers_license_number"></a>Keywords\_finland\_eu\_driver's\_license\_number
```
ajokortti
permis de conduire
ajokortin numero
kuljettaja lic.
körkort
körkortnummer
förare lic.
ajokortit
ajokortin numerot
```
## <a name="finland-european-health-insurance-number"></a>Europäische Krankenversicherungsnummer Finnland

### <a name="format"></a>Format
20-stellige Zahl
### <a name="pattern"></a>Muster
20-stellige Zahl
- 10 Ziffern – 8024680246
- optionale Leerstelle oder optionaler Bindestrich
- 10 Stellen

### <a name="keywords"></a>Keywords
#### <a name="keyword_finland_european_health_insurance_number"></a>Keyword\_finland\_european\_health\_insurance\_number
```
ehic#
ehic
finlandehicnumber#
finska sjukförsäkringskort
health card
health insurance card
health insurance number
hälsokort
sairaanhoitokortin
sairausvakuutuskortti
sairausvakuutusnumero
sjukförsäkring nummer
sjukförsäkringskort
suomen sairausvakuutuskortti
terveyskortti
```
## <a name="finland-national-id"></a>Nationale finnische ID-Nummer

### <a name="format"></a>Format

Sechs Ziffern plus ein Zeichen für das Jahrhundert plus drei Ziffern und eine Prüfziffer

### <a name="pattern"></a>Muster

Das Muster muss alle folgenden Elemente enthalten:

- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Jahrhundertkennzeichnung („-“, „+“ oder „a“)
- Drei Ziffern als persönliche ID-Nummer
- Eine Ziffer oder ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung) als Prüfziffer

### <a name="keywords"></a>Keywords

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Finnische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ verfügbar und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus neun Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Kombination aus neun Buchstaben und Ziffern:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Keyword\_finland\_passport\_number

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Französische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

12 Stellen

### <a name="pattern"></a>Muster

Zwölf Ziffern mit Überprüfung zur Unterscheidung von ähnlichen Mustern, z. B. französischen Telefonnummern

### <a name="keywords"></a>Keywords

#### <a name="keyword_french_drivers_license"></a>Keyword\_french\_drivers\_license

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```
## <a name="france-health-insurance-number"></a>Französische Krankenversicherungsnummer
### <a name="format"></a>Format
21-stellige Zahl
### <a name="pattern"></a>Muster
21-stellige Zahl:

- 10 Stellen
- Eine optionale Leerstelle
- 10 Stellen
- Eine optionale Leerstelle
- Eine Ziffer
### <a name="keywords"></a>Keywords
#### <a name="keyword_france_health_insurance_number"></a>Keyword\_France\_health\_insurance\_number
```
insurance card
carte vitale
carte d'assuré social
```
## <a name="france-national-id-card-cni"></a>Französischer Personalausweis (Carte Nationale d'Identité, CNI)

### <a name="format"></a>Format

12 Stellen

### <a name="pattern"></a>Muster

12 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keywords_france_eu_national_id_card"></a>Keywords\_france\_eu\_national\_id\_card

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Französische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ verfügbar und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Neun Ziffern und Buchstaben

### <a name="pattern"></a>Muster

Neun Ziffern und Buchstaben:

- Zwei Ziffern
- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Fünf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Französische Sozialversicherungsnummer (INSEE) oder vergleichbare Nummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Sozialversicherungsnummer“ und vergleichbaren vertraulichen Nummern enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

15 Stellen

### <a name="pattern"></a>Muster

Muss eines der beiden folgenden Muster aufweisen:

- 13 Ziffern, gefolgt von einer Leerstelle, gefolgt von zwei Ziffern ODER
- 15 aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_fr_insee"></a>Keyword\_fr\_insee

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```
## <a name="france-tax-identification-number-numro-spi"></a>Französische Steueridentifikationsnummer (SPI) 
### <a name="format"></a>Format 
13 Ziffern
### <a name="pattern"></a>Muster
13 Ziffern

- eine Ziffer: 0, 1, 2 oder 3
- Eine Ziffer
- eine Leerstelle (optional)
- zwei Ziffern
- eine Leerstelle (optional)
- Drei Ziffern
- eine Leerstelle (optional)
- Drei Ziffern
- eine Leerstelle (optional)
- drei Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_france_eu_tax_file_number"></a>Keywords\_france\_eu\_tax\_file\_number
```
numéro d'identification fiscale
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="france-value-added-tax-number"></a>Französische Umsatzsteuer-Identifikationsnummer 

### <a name="format"></a>Format 
alphanumerisches Muster mit 13 Zeichen

### <a name="pattern"></a>Muster
alphanumerisches Muster mit 13 Zeichen:

- zwei Buchstaben – FR (keine Beachtung nach Groß-/Kleinschreibung)
- optionale Leerstelle oder optionaler Bindestrich
- zwei Buchstaben oder Ziffern
- optionales Leerzeichen oder Komma bzw. optionaler Punkt oder Bindestrich
- Drei Ziffern
- optionales Leerzeichen oder Komma bzw. optionaler Punkt oder Bindestrich
- Drei Ziffern
- optionales Leerzeichen oder Komma bzw. optionaler Punkt oder Bindestrich
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_france_value_added_tax_number"></a>Keyword\_France\_value\_added\_tax\_number
```
vat number
vat no
vat#
value added tax
siren identification no numéro d'identification taxe sur valeur ajoutée
taxe valeur ajoutée
taxe sur la valeur ajoutée
n° tva
numéro de tva
numéro d'identification siren
```
## <a name="germany-drivers-license-number"></a>Deutsche Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus elf Ziffern und Buchstaben

### <a name="pattern"></a>Muster

Elf Ziffern und Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)

- Eine Ziffer oder ein Buchstabe
- Zwei Ziffern
- Sechs Ziffern oder Buchstaben
- Eine Ziffer
- Eine Ziffer oder ein Buchstabe

### <a name="keywords"></a>Keywords

#### <a name="keyword_german_drivers_license_number"></a>Keyword\_german\_drivers\_license\_number

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Deutsche Personalausweisnummer

### <a name="format"></a>Format

Seit dem 1. November 2010: Neun Buchstaben und Ziffern

Zwischen 1. April 1987 und 31. Oktober 2010: Zehn Ziffern

### <a name="pattern"></a>Muster

Seit dem 1. November 2010:

- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)
- Acht Ziffern

Zwischen 1. April 1987 und 31. Oktober 2010:

- 10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keyword_germany_id_card"></a>Keyword\_germany\_id\_card

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Deutsche Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Zehn Ziffern oder Buchstaben

### <a name="pattern"></a>Muster

Das Muster muss alle folgenden Elemente enthalten:

- Das erste Zeichen ist eine Ziffer oder einer der folgenden Buchstaben: C, F, G, H, J, K
- Drei Ziffern
- Fünf Ziffern oder Buchstaben: C bis H, J bis N, P, R, T, V bis Z
- Eine Ziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_german_passport"></a>Keyword\_german\_passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```
## <a name="germany-tax-identification-number"></a>Deutsche Steueridentifikationsnummer 

### <a name="format"></a>Format 
elf Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Elf Ziffern

- zwei Ziffern
- optionale Leerstelle
- Drei Ziffern
- optionale Leerstelle
- Drei Ziffern
- optionale Leerstelle
- zwei Ziffern
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_germany_eu_tax_file_number"></a>Keywords\_germany\_eu_tax\_file\_number
```
identifikationsnummer
steuer id
steueridentifikationsnummer
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
zinn#
zinn
zinnnummer
```
## <a name="germany-value-added-tax-number"></a>Deutsche Umsatzsteuer-Identifikationsnummer 

### <a name="format"></a>Format 
alphanumerisches Muster mit 11 Zeichen

### <a name="pattern"></a>Muster
alphanumerisches Muster mit 11 Zeichen:

- Buchstabe D oder d
- Buchstabe E oder e
- Eine optionale Leerstelle
- Drei Ziffern
- optionales Leerzeichen oder Komma
- Drei Ziffern
- optionales Leerzeichen oder Komma
- Drei Ziffern

### <a name="keywords"></a>Keywords 

#### <a name="keyword_germany_value_added_tax_number"></a>Keyword\_germany\_value\_added\_tax\_number
```
vat number
vat no
vat#
vat#  mehrwertsteuer
mwst
mehrwertsteuer identifikationsnummer
mehrwertsteuer nummer
```
## <a name="greece-drivers-license-number"></a>Griechische Führerscheinnummer 
neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="format"></a>Format 
neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_greece_eu_drivers_license_number"></a>Keywords\_greece\_eu\_driver's\_license\_number
```
δεια οδήγησης
Adeia odigisis
Άδεια οδήγησης
Δίπλωμα οδήγησης
```
## <a name="greece-national-id-card"></a>Griechische Personalausweisnummer

### <a name="format"></a>Format

Kombination aus sieben bis acht Buchstaben und Ziffern plus ein Bindestrich

### <a name="pattern"></a>Muster

Sieben Buchstaben und Ziffern (altes Format):

- Ein Buchstabe (beliebiger Buchstabe des griechischen Alphabets)
- Ein Bindestrich
- Sechs Ziffern

Acht Buchstaben und Ziffern (neues Format):

- Zwei Buchstaben, wobei die Großbuchstaben sowohl im griechischen als auch im lateinischen Alphabet vorkommen müssen (ABEZHIKMNOPTYX)
- Ein Bindestrich
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_greece_id_card"></a>Keyword\_greece\_id\_card

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```
## <a name="greece-passport-number"></a>Griechische Reisepassnummer

### <a name="format"></a>Format 
zwei Buchstaben, gefolgt von sieben Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
zwei Buchstaben, gefolgt von sieben Ziffern

### <a name="keywords"></a>Keywords
#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_greece_eu_passport_number"></a>Keywords\_greece\_eu\_passport\_number
```
αριθμός διαβατηρίου
αριθμούς διαβατηρίου
αριθμός διαβατηριο
```
## <a name="greece-social-security-number-amka"></a>Griechische Sozialversicherungsnummer (AMKA)

### <a name="format"></a>Format 
elf Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
- sechs Ziffern, die dem Geburtsdatum entsprechen (JJMMTT)
- Vier Ziffern
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_greece_eu_ssn_or_equivalent"></a>Keywords\_greece\_eu\_ssn\_or\_equivalent
```
- ssn
- ssn#
- social security no
- socialsecurityno#
- social security number
- amka
- a.m.k.a.
- Αριθμού Μητρώου Κοινωνικής Ασφάλισης
```
## <a name="greece-tax-identification-number"></a>Griechische Steueridentifikationsnummer

### <a name="format"></a>Format 
neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_greece_eu_tax_file_number"></a>Keywords\_greece\_eu\_tax\_file\_number
```
afm#
afm
aφμ|aφμ αριθμός
aφμ
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax registry no
tax registry number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
taxregistryno#
tin id
tin no
tin#
αριθμός φορολογικού μητρώου
τον αριθμό φορολογικού μητρώου
φορολογικού μητρώου νο
```
## <a name="hong-kong-identity-card-hkid-number"></a>ID-Kartennummer aus Hongkong (HKID)

### <a name="format"></a>Format

Kombination aus acht bis neun Buchstaben und Ziffern plus optionaler Klammern um das letzte Zeichen

### <a name="pattern"></a>Muster

Kombination aus acht bis neun Buchstaben:

- Ein oder zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern
- Das letzte Zeichen (beliebige Ziffer oder der Buchstabe A) ist das Prüfzeichen und kann in Klammern stehen.

### <a name="keywords"></a>Keywords

#### <a name="keyword_hong_kong_id_card"></a>Keyword\_hong\_kong\_id\_card

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```
## <a name="hungary-drivers-license-number"></a>Ungarische Führerscheinnummer 

### <a name="format"></a>Format 
zwei Buchstaben, gefolgt von sechs Ziffern

### <a name="pattern"></a>Muster
zwei Buchstaben und sechs Ziffern:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_hungary_eu_drivers_license_number"></a>Keywords\_hungary\_eu\_driver's\_license\_number
```
vezetoi engedely
vezetői engedély
vezetői engedélyek
```
## <a name="hungary-passport-number"></a>Ungarische Reisepassnummer 

### <a name="format"></a>Format 
zwei Buchstaben, gefolgt von sechs oder sieben Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
zwei Buchstaben, gefolgt von sechs oder sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_hungary_eu_passport_number"></a>Keywords\_hungary\_eu\_passport\_number
```
útlevél száma
Útlevelek száma
útlevél szám
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
Ausstellungsdatum, Ablaufdatum

## <a name="hungary-personal-identification-number"></a>Ungarische persönliche ID-Nummer 

### <a name="format"></a>Format 
Elf Ziffern

### <a name="pattern"></a>Muster
Elf Ziffern:

- Eine Ziffer, die dem Geschlecht entspricht: 1 für „männlich“, 2 für „weiblich“. Für Bürger, die vor 1900 geboren wurden, oder für Bürger mit doppelter Staatsbürgerschaft, sind weitere Zahlen möglich.
- Sechs Ziffern, die dem Geburtsdatum (JJMMTT) entsprechen
- Drei Ziffern, die einer Seriennummer entsprechen
- Eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_hungary_eu_national_id_card"></a>Keywords\_hungary\_eu\_national\_id\_card
```
id number
identification number
sz ig
sz. ig.
sz.ig.
személyazonosító igazolvány
személyi igazolvány
```
## <a name="hungary-social-security-number-taj"></a>Ungarische Sozialversicherungsnummer (TAJ) 

### <a name="format"></a>Format 
neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_hungary_eu_ssn_or_equivalent"></a>Keywords\_hungary\_eu\_ssn\_or\_equivalent
```
- hungarian social security number
- social security number
- socialsecuritynumber#
- hssn#
- socialsecuritynno
- hssn
- taj
- taj#
- ssn
- ssn#
- social security no
- áfa
- közösségi adószám
- általános forgalmi adó szám
- hozzáadottérték adó
- áfa szám
- magyar áfa szám
```
## <a name="hungary-value-added-tax-number"></a>Ungarische Umsatzsteuer-Identifikationsnummer 

### <a name="format"></a>Format 
alphanumerisches Muster mit 10 Zeichen

### <a name="pattern"></a>Muster
alphanumerisches Muster mit 10 Zeichen:

- zwei Buchstaben: HU oder hu
- optionale Leerstelle
- Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_hungary_value_added_tax_number"></a>Keyword\_Hungary\_value\_added\_tax\_number
```
vat
value added tax number
vat#
vatno#
hungarianvatno#
tax no.
value added tax áfa
közösségi adószám
általános forgalmi adó szám
hozzáadottérték adó
áfa szám
```

## <a name="hungary-tax-identification-number"></a>Ungarische Steueridentifikationsnummer 

### <a name="format"></a>Format 
10 Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
Zehn Ziffern:

- eine Ziffer: 8
- acht Ziffern
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_hungary_eu_tax_file_number"></a>Keywords\_hungary\_eu\_tax\_file\_number
```
adóazonosító szám
adóhatóság szám
adószám
hungarian tin
hungatiantin#
tax authority no
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
vat number
```
## <a name="ip-address"></a>IP-Adresse

### <a name="format"></a>Format

#### <a name="ipv4"></a>IPv4:

Komplexes Muster als formatierte (Punkte) oder unformatierte (keine Punkte) Version der IPv4-Adressen

#### <a name="ipv6"></a>IPv6:

Komplexes Muster für formatierte IPv6-Nummern (mit Doppelpunkten)

### <a name="pattern"></a>Muster

### <a name="keywords"></a>Keywords

#### <a name="keyword_ipaddress"></a>Keyword\_ipaddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Indische PAN (Permanent Account Number)

### <a name="format"></a>Format

Zehn Buchstaben oder Ziffern

### <a name="pattern"></a>Muster

Zehn Buchstaben oder Ziffern:

- Drei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Buchstabe: C, P, H, F, A, T, B, L, J, G (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Buchstabe
- Vier Ziffern
- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_india_permanent_account_number"></a>Keyword\_india\_permanent\_account\_number

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Indische eindeutige ID-Nummer (Aadhaar)

### <a name="format"></a>Format

Zwölf Ziffern mit optionalen Leerstellen oder Bindestrichen

### <a name="pattern"></a>Muster

Zwölf Ziffern:

- Eine Ziffer (nicht 0 oder 1)
- Drei Ziffern
- Eine optionale Leerstelle oder ein optionaler Bindestrich
- Vier Ziffern
- Eine optionale Leerstelle oder ein optionaler Bindestrich
- Letzte Ziffer (Prüfziffer)

### <a name="keywords"></a>Keywords

#### <a name="keyword_india_aadhar"></a>Keyword\_india\_aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Indonesische ID-Kartennummer (KTP)

### <a name="format"></a>Format

16 Ziffern mit optionalen Punkten

### <a name="pattern"></a>Muster

16 Ziffern:

- Zwei Ziffern als Provinzcode
- Ein Punkt (optional)
- Zwei Ziffern als Code für Regierungsbezirk oder Ort
- Zwei Ziffern als Code für den Unterbezirk
- Ein Punkt (optional)
- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Ein Punkt (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_indonesia_id_card"></a>Keyword\_indonesia\_id\_card

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>IBAN (International Banking Account Number)

### <a name="format"></a>Format

Ländercode (zwei Buchstaben) plus Prüfziffern (zwei Ziffern) plus :::no-loc text="bban":::-Zahlen (bis zu 30 Zeichen)

### <a name="pattern"></a>Muster

Das Muster muss alle folgenden Elemente enthalten:

- Zwei Buchstaben als Ländercode
- Zwei Prüfziffern (gefolgt von einer optionalen Leerstelle)
- Ein bis sieben Gruppen mit vier Buchstaben oder Ziffern (kann durch Leerstellen getrennt werden)
- Ein bis drei Buchstaben oder Ziffern

Die Formate der einzelnen Länder unterscheiden sich geringfügig. Der Vertraulichkeitsinformationstyp „IBAN“ gilt für diese 60 Länder:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Keywords

Keine

## <a name="ireland-drivers-license-number"></a>Irische Führerscheinnummer 

### <a name="format"></a>Format 
Sechs Ziffern, gefolgt von vier Buchstaben

### <a name="pattern"></a>Muster
Sechs Ziffern und vier Buchstaben:

- Sechs Ziffern
- Vier Buchstaben (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_ireland_eu_drivers_license_number"></a>Keywords\_ireland\_eu\_driver's\_license\_number
```
ceadúnas tiomána
ceadúnais tiomána 
```
## <a name="ireland-passport-number"></a>Irische Reisepassnummer 


### <a name="format"></a>Format 
Zwei Buchstaben oder Ziffern, gefolgt von sieben Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
Zwei Buchstaben oder Ziffern, gefolgt von sieben Ziffern:

- Zwei Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_ireland_eu_passport_number"></a>Keywords\_ireland\_eu\_passport\_number
```
passeport numero
uimhreacha pasanna
uimhir pas
uimhir phas
uimhreacha pas
uimhir cárta
uimhir chárta
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="ireland-personal-public-service-pps-number"></a>Irische Sozialversicherungsnummer (Personal Public Service, PPS)

### <a name="format"></a>Format

Altes Format (bis 31. Dezember 2012):

- Sieben Ziffern, gefolgt von ein bis zwei Buchstaben

Neues Format (ab 1. Jan 2013):

- Sieben Ziffern, gefolgt von zwei Buchstaben

### <a name="pattern"></a>Muster

Altes Format (bis 31. Dezember 2012):

- Sieben Ziffern
- Ein oder zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)

Neues Format (ab 1. Jan 2013):

- Sieben Ziffern
- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung) als alphabetisches Prüfzeichen
- Ein optionaler Buchstabe aus dem Bereich A bis I oder &quot;W&quot;

### <a name="keywords"></a>Keywords

#### <a name="keywords_ireland_eu_national_id_card"></a>Keywords\_ireland\_eu\_national\_id\_card

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Israelische Bankkontonummer

### <a name="format"></a>Format

13 Ziffern

### <a name="pattern"></a>Muster

Formatiert:

- Zwei Ziffern
- Ein Bindestrich
- Drei Ziffern
- Ein Bindestrich
- Acht Ziffern

Unformatiert:

- 13 aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_israel_bank_account_number"></a>Keyword\_israel\_bank\_account\_number

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Israelische Personalausweisnummer

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_israel_national_id"></a>Keyword\_Israel\_National\_ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Italienische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus zehn Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Kombination aus zehn Buchstaben und Ziffern:

- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)
- Buchstabe &quot;A&quot; oder &quot;V&quot; (ohne Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern
- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_italy_drivers_license_number"></a>Keyword\_italy\_drivers\_license\_number

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```
## <a name="italy-fiscal-code"></a>Italienische Steuernummer 

### <a name="format"></a>Format 
eine Kombination aus 16 Zeichen, die aus Buchstaben und Ziffern im angegebenen Muster besteht

### <a name="pattern"></a>Muster
Eine Kombination aus Buchstaben und Ziffern mit 16 Zeichen:

- drei Buchstaben, die den ersten drei Konsonanten im Nachnamen entsprechen
- drei Buchstaben, die dem ersten, dritten und vierten Konsonanten im Vornamen entsprechen
- zwei Ziffern, die den letzten Ziffern des Geburtsjahrs entsprechen
- ein Buchstabe für den Geburtsmonat – Buchstaben werden in alphabetischer Reihenfolge verwendet, es werden aber nur die Buchstaben A bis E, H, L, M, P, R bis T verwendet (Januar ist A, Oktober ist R)
- zwei Ziffern, die dem Tag des Geburtsmonats entsprechen. Um zwischen den Geschlechtern zu unterscheiden, wird dem Geburtsdatum von Frauen die Ziffernfolge 40 hinzugefügt
- vier Ziffern, die der Ortskennzahl des Geburtsorts entsprechen (bei Geburtsorten im Ausland werden landesweite Codes verwendet)
- eine Paritätsziffer 

### <a name="keywords"></a>Keywords

#### <a name="keywords_italy_eu_national_id_card"></a>Keywords\_italy\_eu\_national\_id\_card
```
codice fiscal
codice fiscale
codice id personale
codice personale
fiscal code
numero certificato personale
numero di identificazione fiscale
numero id personale
numero personale
personal certificate number
personal code
personal id code
personal id number
personalcodeno#
tax code
tax id
tax identification no
tax identification number
tax identity number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="italy-passport-number"></a>Italienische Reisepassnummer 

### <a name="format"></a>Format 
zwei Buchstaben oder Ziffern, gefolgt von sieben Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
zwei Buchstaben oder Ziffern, gefolgt von sieben Ziffern:

- zwei Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_italy_eu_passport_number"></a>Keywords\_italy\_eu\_passport\_number
```
italiana passaporto
passaporto italiana
passaporto numero
numéro passeport
numero di passaporto
numeri del passaporto
passeport italien
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="italy-value-added-tax"></a>Italienische Umsatzsteuer-Identifikationsnummer 

### <a name="format"></a>Format 
alphanumerisches Muster mit 13 Zeichen und optionalen Trennzeichen

### <a name="pattern"></a>Muster
alphanumerisches Muster mit 13 Zeichen und optionalen Trennzeichen:

- I oder i
- T oder t
- optionales Leerzeichen oder Komma bzw. optionaler Punkt oder Bindestrich
- Elf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_italy_value_added_tax_number"></a>Keyword\_italy\_value\_added\_tax\_number
```
vat number
vat no
vat#
iva
iva#
```
## <a name="japan-bank-account-number"></a>Japanische Bankkontonummer

### <a name="format"></a>Format

Sieben oder acht Ziffern

### <a name="pattern"></a>Muster

Bankkontonummer:

- Sieben oder acht Ziffern
- Code für Bankkonto-Zweigstelle:
- Vier Ziffern
- Eine Leerstelle oder ein Bindestrich (optional)
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_bank_account"></a>Keyword\_jp\_bank\_account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Keyword\_jp\_bank\_branch\_code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Japanische Führerscheinnummer

### <a name="format"></a>Format

12 Stellen

### <a name="pattern"></a>Muster

Zwölf aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_drivers_license_number"></a>Keyword\_jp\_drivers\_license\_number

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```
## <a name="japanese-my-number--corporate"></a>Japanische „Meine Nummer“, geschäftlich 


### <a name="format"></a>Format 
13-stellige Zahl

### <a name="pattern"></a>Muster
13-stellige Zahl:

- eine Ziffer von eins bis neun
- 12 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keyword_japan_my_number_corporate"></a>Keyword\_japan\_my\_number\_corporate
```
corporate number
マイナンバー
共通番号
マイナンバーカード
マイナンバーカード番号
個人番号カード
個人識別番号
個人識別ナンバー
法人番号
指定通知書
```
## <a name="japanese-my-number--personal"></a>Japanische „Meine Nummer“, privat 


### <a name="format"></a>Format 
12-stellige Zahl

### <a name="pattern"></a>Muster
12-stellige Zahl:

- Vier Ziffern
- optionale Leerstelle bzw. optionaler Punkt oder Bindestrich
- Vier Ziffern
- optionale Leerstelle bzw. optionaler Punkt oder Bindestrich
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_japan_my_number_personal"></a>Keyword\_japan\_my\_number\_personal
```
my number
マイナンバー
個人番号
共通番号
マイナンバーカード
マイナンバーカード番号
個人番号カード
個人識別番号
個人識別ナンバー
通知カード
```
## <a name="japan-passport-number"></a>Japanische Reisepassnummer

### <a name="format"></a>Format

Zwei Buchstaben, gefolgt von sieben Ziffern

### <a name="pattern"></a>Muster

Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von sieben Ziffern

#### <a name="keyword_jp_passport"></a>Keyword\_jp\_passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Japanische Personalausweisnummer

### <a name="format"></a>Format

Zwölf Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Zwölf Buchstaben und Ziffern:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Acht Ziffern
- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_residence_card_number"></a>Keyword\_jp\_residence\_card\_number

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Japanische Melderegisternummer

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

Elf aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_resident_registration_number"></a>Keyword\_jp\_resident\_registration\_number

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Japanische Sozialversicherungsnummer (SIN)

### <a name="format"></a>Format

Sieben bis zwölf Ziffern

### <a name="pattern"></a>Muster

Sieben bis zwölf Ziffern:

- Vier Ziffern
- Ein Bindestrich (optional)
- Sechs Ziffern ODER
- Sieben bis zwölf aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_jp_sin"></a>Keyword\_jp\_sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```
## <a name="latvia-drivers-license-number"></a>Lettische Führerscheinnummer

### <a name="format"></a>Format
drei Buchstaben, gefolgt von sechs Ziffern

### <a name="pattern"></a>Muster
drei Buchstaben und sechs Ziffern:

- drei Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_latvia_eu_drivers_license_number"></a>Keywords\_latvia\_eu\_driver's\_license\_number
```
autovadītāja apliecība
autovadītāja apliecības
vadītāja apliecība
```
## <a name="latvia-passport-number"></a>Lettische Reisepassnummer 

### <a name="format"></a>Format 
zwei Buchstaben oder Ziffern, gefolgt von sieben Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
zwei Buchstaben oder Ziffern, gefolgt von sieben Ziffern:

- zwei Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number_common"></a>Keywords\_eu\_passport\_number\_common
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_latvia_eu_passport_number"></a>Keywords\_latvia\_eu\_passport\_number
```
pase numurs
pase numur
pases numuri
pases nr
passeport no
n° du Passeport
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="latvia-personal-code-personas-kods-or-pic"></a>Lettischer persönlicher Code (Personas kods/PIC)  

### <a name="format"></a>Format 
elf Ziffern und ein optionaler Bindestrich

### <a name="pattern"></a>Muster
Altes Format

elf Ziffern und ein Bindestrich:

- sechs Ziffern, die dem Geburtsdatum (TTMMJJ) entsprechen
- Ein Bindestrich
- eine Ziffer, die dem Geburtsjahrhundert entspricht (0 für das 19. Jahrhundert, 1 für das 20. Jahrhundert und 2 für das 21. Jahrhundert)
- vier Ziffern, zufällig generiert
- Neues Format

Elf Ziffern

- die zwei Ziffern 32
- neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_latvia_eu_national_id_card"></a>Keywords\_latvia\_eu\_national\_id\_card
```
administrative number
alvas nē
birth number
citizen number
civil number
electronic census number
electronic number
fiscal code
healthcare user number
id#
id-code
identification number
identifikācijas numurs
id-number
individual number
latvija alva
nacionālais id
national id
national identifying number
national identity number
national insurance number
national register number
nodokļa numurs
nodokļu id
nodokļu identifikācija numurs
personal certificate number
personal code
personal id code
personal id number
personal identification code
personal identifier
personal identity number
personal number
personal numeric code
personalcodeno#
personas kods
population identification code
public service number
registration number
revenue number
social insurance number
social security number
state tax code
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
voter’s number
```
## <a name="lithuania-drivers-license-number"></a>Litauische Führerscheinnummer 

### <a name="format"></a>Format 
Acht Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_lithuania_eu_drivers_license_number"></a>Keywords\_lithuania\_eu\_driver's\_license\_number
```
vairuotojo pažymėjimas
vairuotojo pažymėjimo numeris
vairuotojo pažymėjimo numeriai
```
## <a name="lithuania-passport-number"></a>Litauische Reisepassnummer 

### <a name="format"></a>Format 
acht Ziffern oder Buchstaben ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
acht Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_lithuania_eu_passport_number"></a>Keywords\_lithuania\_eu\_passport\_number
```
paso numeris
paso numeriai
paso nr
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="lithuania-personal-code-asmens-kodas"></a>Litauischer persönlicher Code (Asmens kodas)  

### <a name="format"></a>Format 
elf Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
elf Ziffern ohne Leerstellen und Trennzeichen:

- eine Ziffer (1-6), die dem Geschlecht und dem Geburtsjahrhundert der Person entspricht
- sechs Ziffern, die dem Geburtsdatum (JJMMTT) entsprechen
- drei Ziffern, die der Seriennummer des Geburtsdatums entsprechen
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_lithuania_eu_national_id_card"></a>Keywords\_lithuania\_eu\_national\_id\_card
```
asmeninis skaitmeninis kodas
asmens kodas
citizen service number
mokesčių id
mokesčių identifikavimas numeris
mokesčių identifikavimo numeris
mokesčių numeris
national identification number
personal code
personal numeric code
piliečio paslaugos numeris
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unikalus identifikavimo kodas
unikalus identifikavimo numeris
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="luxembourg-drivers-license-number"></a>Luxemburgische Führerscheinnummer 

### <a name="format"></a>Format 
sechs Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_luxemburg_eu_drivers_license_number"></a>Keywords\_luxemburg\_eu\_driver's\_license\_number
```
fahrerlaubnis
Führerschäin
```
## <a name="luxembourg-passport-number"></a>Luxemburgische Reisepassnummer 

### <a name="format"></a>Format 
acht Ziffern oder Buchstaben ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
acht Ziffern oder Buchstaben (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_luxemburg_eu_passport_number"></a>Keywords\_luxemburg\_eu\_passport\_number
```
ausweisnummer
luxembourg pass
luxembourg passeport
luxembourg passport
no de passeport
no-reisepass
nr-reisepass
numéro de passeport
pass net
pass nr
passnummer
passeport nombre
reisepässe
reisepass-nr
reisepassnummer
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="luxemburg-national-identification-number---natural-persons"></a>Luxemburgische nationale Identifikationsnummer (natürliche Personen)

### <a name="format"></a>Format 
13 Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster
13 Ziffern:

- Elf Ziffern
- Zwei Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_luxemburg_eu_national_id_card"></a>Keywords\_luxemburg\_eu\_national\_id\_card
```
eindeutige id
eindeutige id-nummer
eindeutigeid#
id personnelle
idpersonnelle#
idpersonnelle
individual code
individual id
individual identification
individual identity
numéro d'identification personnel
personal id
personal identification
personal identity
personalidno#
personalidnumber#
persönliche identifikationsnummer
unique id
unique identity
uniqueidkey#
```
## <a name="luxemburg-national-identification-number---non-natural-persons"></a>Luxemburgische nationale Identifikationsnummer (juristische Personen)

### <a name="format"></a>Format 
Elf Ziffern

### <a name="pattern"></a>Muster
Elf Ziffern

- Zwei Ziffern
- Eine optionale Leerstelle
- Drei Ziffern
- Eine optionale Leerstelle
- Drei Ziffern
- Eine optionale Leerstelle
- Zwei Ziffern
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_luxemburg_eu_tax_file_number"></a>Keywords\_luxemburg\_eu\_tax\_file\_number
```
carte de sécurité sociale
étain non
étain#
identifiant d'impôt
luxembourg tax identifikatiounsnummer
numéro d'étain
numéro d'identification fiscal luxembourgeois
numéro d'identification fiscale
social security
sozialunterstützung
sozialversécherung
sozialversicherungsausweis
steier id
steier identifikatiounsnummer
steier nummer
steuer id
steueridentifikationsnummer
steuernummer
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
zinn#
zinn
zinnzahl
```
## <a name="malaysia-identification-card-number"></a>Malaiische Personalausweisnummer

### <a name="format"></a>Format

Zwölf Ziffern mit optionalen Bindestrichen

### <a name="pattern"></a>Muster

Zwölf Ziffern:

- Sechs Ziffern im Format JJMMTT als Geburtsdatum
- Ein Bindestrich (optional)
- Zwei Buchstaben als Code für den Geburtsort
- Ein Bindestrich (optional)
- Drei zufällige Ziffern
- Einstelliger Code für das Geschlecht

### <a name="keywords"></a>Keywords

#### <a name="keyword_malaysia_id_card_number"></a>Keyword\_malaysia\_id\_card\_number

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```
## <a name="malta-drivers-license-number"></a>Maltesische Führerscheinnummer 

### <a name="format"></a>Format 
Kombination aus zwei Zeichen und sechs Ziffern im angegebenen Muster

### <a name="pattern"></a>Muster
Kombination aus zwei Zeichen und sechs Ziffern:

- zwei Zeichen (Ziffern oder Buchstaben, keine Beachtung der Groß-/Kleinschreibung)
- Eine Leerstelle (optional)
- Drei Ziffern
- Eine Leerstelle (optional)
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_malta_eu_drivers_license_number"></a>Keywords\_malta\_eu\_driver's\_license\_number
```
liċenzja tas-sewqan
liċenzji tas-sewwieq
```
## <a name="malta-identity-card-number"></a>Maltesische ID-Kartennummer 

### <a name="format"></a>Format 
sieben Ziffern, gefolgt von einem Buchstaben

### <a name="pattern"></a>Muster
sieben Ziffern, gefolgt von einem Buchstaben:

- Sieben Ziffern
- ein Buchstabe aus „M, G, A, P, L, H, B, Z“ (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keywords_malta_eu_national_id_card"></a>Keywords\_malta\_eu\_national\_id\_card
```
citizen service number
id tat-taxxa
identifika numru tal-biljett
kodiċi numerali personali
numru ta 'identifikazzjoni personali
numru ta 'identifikazzjoni tat-taxxa
numru ta 'identifikazzjoni uniku
numru ta' identità uniku
numru tas-servizz taċ-ċittadin
numru tat-taxxa
personal numeric code
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="malta-passport-number"></a>Maltesische Reisepassnummer 

### <a name="format"></a>Format 
sieben Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster
Sieben Ziffern

### <a name="keywords"></a>Keywords
#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_malta_eu_passport_number"></a>Keywords\_malta\_eu\_passport\_number
```
numru tal-passaport
numri tal-passaport
Nru tal-passaport
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="malta-tax-id-number"></a>Maltesische Steueridentifikationsnummer 

### <a name="format"></a>Format 
Für maltesische Staatsangehörige:

- sieben Ziffern und ein Buchstabe im angegebenen Muster

Nicht maltesische Staatsangehörige und maltesische Einrichtungen:

- Neun Ziffern

### <a name="pattern"></a>Muster

Maltesische Staatsangehörige: sieben Ziffern und ein Buchstabe

- Sieben Ziffern
- ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung)

Nicht maltesische Staatsangehörige und maltesische Einrichtungen: neun Ziffern

- Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_malta_eu_tax_file_number"></a>Keywords\_malta\_eu\_tax\_file\_number
```
citizen service number
id tat-taxxa
identifika numru tal-biljett
kodiċi numerali personali
numru ta 'identifikazzjoni personali
numru ta 'identifikazzjoni tat-taxxa
numru ta 'identifikazzjoni uniku
numru ta' identità uniku
numru tas-servizz taċ-ċittadin
numru tat-taxxa
personal numeric code
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="netherlands-citizens-service-bsn-number"></a>Niederländische persönliche Identifikationsnummer (BSN)

### <a name="format"></a>Format

Acht bis neun Ziffern mit optionalen Leerstellen

### <a name="pattern"></a>Muster

Acht bis neun Ziffern:

- Drei Ziffern
- Eine Leerstelle (optional)
- Drei Ziffern
- Eine Leerstelle (optional)
- Zwei bis drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_netherlands_eu_national_id_card"></a>Keywords\_netherlands\_eu\_national\_id\_card

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```
## <a name="netherlands-drivers-license-number"></a>Niederländische Führerscheinnummer 

### <a name="format"></a>Format 

Zehn Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_netherlands_eu_drivers_license_number"></a>Keywords\_netherlands\_eu\_driver's\_license\_number
```
permis de conduire
rijbewijs
rijbewijsnummer
rijbewijzen
rijbewijs nummer
rijbewijsnummers
```

## <a name="netherlands-passport-number"></a>Niederländische Reisepassnummer 

### <a name="format"></a>Format 

neun Buchstaben oder Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster

neun Buchstaben oder Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_netherlands_eu_passport_number"></a>Keywords\_netherlands\_eu\_passport\_number
```
paspoort nummer
paspoortnummers
paspoortnummer
paspoort nr
```
## <a name="netherlands-tax-identification-number"></a>Niederländische Steueridentifikationsnummer 

### <a name="format"></a>Format 

neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_netherlands_eu_tax_file_number"></a>Keywords\_netherlands\_eu\_tax\_file\_number
```
btw nummer
hollânske tax identification
hulandes impuesto id number
hulandes impuesto identification
identificatienummer belasting
identificatienummer van belasting
impuesto identification number
impuesto number
nederlands belasting id nummer
nederlands belasting identificatie
nederlands belasting identificatienummer
nederlands belastingnummer
nederlandse belasting identificatie
netherlands tax identification
netherland's tax identification
netherlands tin
netherland's tin
tax id
tax identification no
tax identification number
tax identification tal
tax no#
tax no
tax number
tax registration number
tax tal
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="netherlands-value-added-tax-number"></a>Niederländische Umsatzsteuer-Identifikationsnummer 

### <a name="format"></a>Format 

alphanumerisches Muster mit 14 Zeichen

### <a name="pattern"></a>Muster

alphanumerisches Muster mit 14 Zeichen:

- N oder n
- L oder l
- optionale Leerstelle bzw. optionaler Punkt oder Bindestrich
- Neun Ziffern
- optionale Leerstelle bzw. optionaler Punkt oder Bindestrich
- B oder b
- Zwei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_netherlands_value_added_tax_number"></a>Keyword\_netherlands\_value\_added\_tax\_number
```
vat number
vat no
vat#
wearde tafoege tax getal
btw nûmer
btw-nummer
```
## <a name="new-zealand-bank-account-number"></a>Neuseeländische Bankkontonummer 

### <a name="format"></a>Format 

14- bis 16-stelliges Muster mit optionalem Trennzeichen

### <a name="pattern"></a>Muster

14- bis 16-stelliges Muster mit optionalem Trennzeichen:

- Zwei Ziffern
- optionaler Bindestrich oder optionales Leerzeichen
- drei bis vier Ziffern
- optionaler Bindestrich oder optionales Leerzeichen
- Sieben Ziffern
- optionaler Bindestrich oder optionales Leerzeichen
- zwei bis drei Ziffern
- optionaler Bindestrich oder optionales Leerzeichen

### <a name="keywords"></a>Keywords

#### <a name="keyword_new_zealand_bank_account_number"></a>Keyword\_new\_zealand\_bank\_account\_number
```
account number
bank account
bank_acct_id
bank_acct_branch
bank_acct_nbr
```
## <a name="new-zealand-driver-license"></a>Neuseeländische Führerscheinnummer 

### <a name="format"></a>Format 

alphanumerisches Muster mit acht Zeichen

### <a name="pattern"></a>Muster

alphanumerisches Muster mit acht Zeichen

- Zwei Buchstaben
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_new_zealand_drivers_license_number"></a>Keyword\_new\_zealand\_drivers\_license\_number
```
driverlicence
driverlicences
driver lic
driver licence
driver licences
driverslic
driverslicence
driverslicences
drivers lic
drivers lics
drivers licence
drivers licences
driver'lic
driver'lics
driver'licence
driver'licences
driver' lic
driver' lics
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's licence
driver's licences
driverlic#
driverlics#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver licence#
driver licences#
driverslic#
driverslics#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's licence#
driver's licences#
international driving permit
international driving permits
nz automobile association
new zealand automobile association
```
## <a name="new-zealand-inland-revenue-number"></a>Neuseeländische Steuernummer 

### <a name="format"></a>Format 

acht oder neun Ziffern mit optionalen Trennzeichen

### <a name="pattern"></a>Muster

acht oder neun Ziffern mit optionalen Trennzeichen

- zwei oder drei Ziffern
- optionale Leerstelle oder optionaler Bindestrich
- Drei Ziffern
- optionale Leerstelle oder optionaler Bindestrich
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_new_zealand_inland_revenue_number"></a>Keyword\_new\_zealand\_inland\_revenue\_number
```
ird no.
ird no#
nz ird
new zealand ird
ird number
inland revenue number
```
## <a name="new-zealand-social-welfare-number"></a>Neuseeländische Sozialversicherungsnummer

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun Ziffern

- Drei Ziffern
- optionaler Bindestrich
- Drei Ziffern
- optionaler Bindestrich
- Drei Ziffern

#### <a name="keyword_new_zealand_social_welfare_number"></a>Keyword\_new\_zealand\_social\_welfare\_number
```
social welfare #
social welfare#
social welfare No.  
social welfare number
swn#
```
## <a name="new-zealand-ministry-of-health-number"></a>Neuseeländische Nummer des Gesundheitsministeriums

### <a name="format"></a>Format

Drei Buchstaben, eine Leerstelle (optional) und vier Ziffern

### <a name="pattern"></a>Muster

- Drei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung) außer „I“ und „O“
- Eine Leerstelle (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_nz_terms"></a>Keyword\_nz\_terms

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Norwegische Identifikationsnummer

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

Elf Ziffern:

- Sechs Ziffern im Format TTMMJJ als Geburtsdatum
- Dreistellige individuelle Nummer
- Zwei Prüfziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_norway_id_number"></a>Keyword\_norway\_id\_number

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Philippinische einheitliche Mehrzweck-Identifikationsnummer

### <a name="format"></a>Format

Zwölf Ziffern, getrennt durch Bindestriche

### <a name="pattern"></a>Muster

Zwölf Ziffern:

- Vier Ziffern
- Ein Bindestrich
- Sieben Ziffern
- Ein Bindestrich
- Eine Ziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_philippines_id"></a>Keyword\_philippines\_id

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-drivers-license-number"></a>Polnische Führerscheinnummer

### <a name="format"></a>Format

14 Ziffern mit zwei Schrägstrichen

### <a name="pattern"></a>Muster

14 Ziffern und zwei Schrägstriche:

- Fünf Ziffern
- Ein Schrägstrich
- Zwei Ziffern
- Ein Schrägstrich
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_poland_eu_drivers_license_number"></a>Keywords\_poland\_eu\_driver's\_license\_number
```
prawo jazdy
prawa jazdy
```

## <a name="poland-identity-card"></a>Polnischer Personalausweis

### <a name="format"></a>Format

Drei Buchstaben und sechs Ziffern

### <a name="pattern"></a>Muster

Drei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>Nationale polnische ID-Nummer (PESEL)

### <a name="format"></a>Format

Elf Ziffern

### <a name="pattern"></a>Muster

- Sechs Ziffern als Geburtsdatum im Format JJMMTT
- Vier Ziffern
- Eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_pesel_identification_number"></a>Keyword\_pesel\_identification\_number

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Polnische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Zwei Buchstaben und sieben Ziffern

### <a name="pattern"></a>Muster

Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_poland_national_id_passport_number"></a>Keyword\_poland\_national\_id\_passport\_number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="poland-regon-number"></a>Polnische REGON-Nummer

### <a name="format"></a>Format

9- oder 14-stellige Zahl

### <a name="pattern"></a>Muster

9- oder 14-stellige Zahl:

- neun Ziffern oder
- Neun Ziffern
- Bindestrich
- Fünf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_poland_regon_number"></a>Keywords\_poland\_regon\_number
```
regon id
statistical number
statistical id
statistical no
regon number
regonid#
regonno#
company id
companyid#
companyidno#
numer statystyczny
numeru regon
numerstatystyczny#
numeruregon#
```
## <a name="poland-tax-identification-number"></a>Polnische Steueridentifikationsnummer

### <a name="format"></a>Format

elf Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster

Elf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_poland_eu_tax_file_number"></a>Keywords\_poland\_eu\_tax\_file\_number
```
nip#
nip
numer identyfikacji podatkowej
numeridentyfikacjipodatkowej#
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
vat id#
vat id
vat no
vat number
vatid#
vatid
vatno#
```
## <a name="portugal-citizen-card-number"></a>Portugiesische ID-Kartennummer (Citizen Card)

### <a name="format"></a>Format

Acht Ziffern

### <a name="pattern"></a>Muster

Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_portugal_citizen_card"></a>Keyword\_portugal\_citizen\_card

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Portugiesische Führerscheinnummer

Diese als vertraulich eingestufte Entität ist nur unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ verfügbar.

### <a name="format"></a>Format

Zwei Muster: zwei Buchstaben, gefolgt von fünf bis acht Ziffern mit Sonderzeichen

### <a name="pattern"></a>Muster

Muster 1: Zwei Buchstaben, gefolgt von fünf bis sechs Ziffern mit Sonderzeichen:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Bindestrich
- Fünf oder sechs Ziffern
- Eine Leerstelle
- Eine Ziffer

Muster 2: Ein Buchstabe, gefolgt von sechs/acht Ziffern mit Sonderzeichen:

- Ein Buchstabe (ohne Beachtung der Groß-/Kleinschreibung)
- Ein Bindestrich
- Sechs oder acht Ziffern
- Eine Leerstelle
- Eine Ziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Keywords\_portugal\_eu\_driver's\_license\_number

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```
## <a name="portugal-passport-number"></a>Portugiesische Reisepassnummer

### <a name="format"></a>Format

ein Buchstabe, gefolgt von sechs Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster

ein Buchstabe, gefolgt von sechs Ziffern:

- ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_portugal_eu_passport_number"></a>Keywords\_portugal\_eu\_passport\_number
```
número do passaporte
portuguese passport
portuguese passeport
portuguese passaporte
passaporte nº
passeport nº
números de passaporte
portuguese passports
número passaporte
números passaporte
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```

## <a name="portugal-tax-identification-number"></a>Portugiesische Steueridentifikationsnummer

### <a name="format"></a>Format

neun Ziffern mit optionalen Leerstellen

### <a name="pattern"></a>Muster

- Drei Ziffern
- Eine optionale Leerstelle
- Drei Ziffern
- Eine optionale Leerstelle
- Drei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_portugal_eu_tax_file_number"></a>Keywords\_portugal\_eu\_tax\_file\_number
```
cpf#
cpf
nif#
nif
número de identificação fisca
numero fiscal
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="romania-drivers-license-number"></a>Rumänische Führerscheinnummer

### <a name="format"></a>Format

ein Zeichen, gefolgt von acht Ziffern

### <a name="pattern"></a>Muster

ein Zeichen, gefolgt von acht Ziffern:

- ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung) oder eine Ziffer
- Acht Ziffern

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_romania_eu_drivers_license_number"></a>Keywords\_romania\_eu\_driver's\_license\_number
```
permis de conducere
permisului de conducere
permisului conducere
permisele de conducere
permisele conducere
permis conducere
```

## <a name="romania-passport-number"></a>Rumänische Reisepassnummer

### <a name="format"></a>Format

acht oder neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

acht oder neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_romania_eu_passport_number"></a>Keywords\_romania\_eu\_passport\_number
```
numărul pașaportului numarul pasaportului numerele pașaportului Pașaport nr
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```

## <a name="romania-personal-numeric-code-cnp"></a>Rumänischer persönlicher numerischer Code (CNP)

### <a name="format"></a>Format

13 Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

- eine Ziffer von 1 bis 9
- sechs Ziffern, die das Geburtsdatum (JJMMTT) darstellen
- zwei Ziffern (01-52 oder 99)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_romania_eu_national_id_card"></a>Keywords\_romania\_eu\_national\_id\_card
```
cnp#
cnp
cod identificare personal
cod numeric personal
cod unic identificare
codnumericpersonal#
codul fiscal nr.
identificarea fiscală nr#
id-ul taxei
insurance number
insurancenumber#
national id#
national id
national identification number
număr identificare personal
număr identitate
număr personal unic
număridentitate#
număridentitate
numărpersonalunic#
numărpersonalunic
număru de identificare fiscală
numărul de identificare fiscală
personal numeric code
pin#
pin
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
unique identification number
unique identity number
uniqueidentityno#
uniqueidentityno
```

## <a name="russia-passport-number-domestic"></a>Russische Reisepassnummer (Inland)

### <a name="format"></a>Format

10-stellige Zahl

### <a name="pattern"></a>Muster

10-stellige Zahl:

- Zwei Ziffern
- optionale Leerstelle oder optionaler Bindestrich
- Zwei Ziffern
- Eine optionale Leerstelle
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_russia_passport_number_domestic"></a>Keyword\_russia\_passport\_number\_domestic
```
passport number
passport no
passport #
passport id
passportno#
passportnumber#
паспорт нет
паспорт id
pоссийской паспорт
pусский номер паспорта
паспорт#
паспортid#
номер паспорта
номерпаспорта#
```
## <a name="russia-passport-number-international"></a>Russische Reisepassnummer (international)

### <a name="format"></a>Format

9-stellige Zahl

### <a name="pattern"></a>Muster

9-stellige Zahl:

- Zwei Ziffern
- optionale Leerstelle oder optionaler Bindestrich
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_russia_passport_number_international"></a>Keywords\_russia\_passport\_number\_international
```
passport number
passport no
passport #
passport id
passportno#
passportnumber#
паспорт нет
паспорт id
pоссийской паспорт
pусский номер паспорта
паспорт#
паспортid#
номер паспорта
номерпаспорта#
```

## <a name="saudi-arabia-national-id"></a>Nationale saudi-arabische ID-Nummer

### <a name="format"></a>Format

10 Stellen

### <a name="pattern"></a>Muster

Zehn aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_saudi_arabia_national_id"></a>Keyword\_saudi\_arabia\_national\_id

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Singapur: Nationale Registrierungs-ID-Kartennummer (NRIC)

### <a name="format"></a>Format

Neun Buchstaben und Ziffern

### <a name="pattern"></a>Muster

- Neun Buchstaben und Ziffern:
- Buchstabe &quot;F&quot;, &quot;G&quot;, &quot;S&quot; oder &quot;T&quot; (ohne Beachtung der Groß-/Kleinschreibung)
- Sieben Ziffern
- Ein alphabetisches Prüfzeichen

### <a name="keywords"></a>Keywords

#### <a name="keyword_singapore_nric"></a>Keyword\_singapore\_nric

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```
## <a name="slovakia-drivers-license-number"></a>Slowakische Führerscheinnummer

### <a name="format"></a>Format

ein Zeichen, gefolgt von sieben Ziffern

### <a name="pattern"></a>Muster

ein Zeichen, gefolgt von sieben Ziffern

- ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung) oder eine Ziffer
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_slovakia_eu_drivers_license_number"></a>Keywords\_slovakia\_eu\_driver's\_license\_number
```
vodičský preukaz
vodičské preukazy
vodičského preukazu
vodičských preukazov
```
## <a name="slovakia-personal-number"></a>Slowakische Personennummer

### <a name="format"></a>Format
Neun oder zehn Ziffern mit optionalem umgekehrten Schrägstrich

### <a name="pattern"></a>Muster
- sechs Ziffern, die das Geburtsdatum darstellen
- optionaler Schrägstrich (/)
- Drei Ziffern
- eine optionale Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_slovakia_eu_national_id_card"></a>Keywords\_slovakia\_eu\_national\_id\_card
```
azonosító szám
birth number
číslo národnej identifikačnej karty
číslo občianského preukazu
daňové číslo
id number
identification no
identification number
identifikačná karta č
identifikačné číslo
identity card no
identity card number
národná identifikačná značka č
national number
nationalnumber#
nemzeti személyazonosító igazolvány
personalidnumber#
rč
rodne cislo
rodné číslo
social security number
ssn#
ssn
személyi igazolvány szám
személyi igazolvány száma
személyigazolvány szám
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="slovenia-drivers-license-number"></a>Slowenische Führerscheinnummer

### <a name="format"></a>Format

neun Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```
#### <a name="keywords_slovenia_eu_drivers_license_number"></a>Keywords\_slovenia\_eu\_driver's\_license\_number
```
vozniško dovoljenje
vozniška številka licence
vozniških dovoljenj
številka vozniškega dovoljenja
številke vozniških dovoljenj
```
## <a name="slovenia-passport-number"></a>Slowenische Reisepassnummer

### <a name="format"></a>Format

zwei Buchstaben, gefolgt von sieben Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster

zwei Buchstaben, gefolgt von sieben Ziffern:

- der Buchstabe P
- ein Großbuchstabe
- Sieben Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
- passport#
- passport #
- passportid
- passports
- passportno
- passport no
- passportnumber
- passport number
- passportnumbers
- passport numbers
```
#### <a name="keywords_slovenia_eu_passport_number"></a>Keywords\_slovenia\_eu\_passport\_number
```
- številka potnega lista
- potek veljavnosti
- potni list#
- datum rojstva
- potni list
- številke potnih listov
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
- date of issue
- date of expiry
```
## <a name="slovenia-tax-identification-number"></a>Slowenische Steueridentifikationsnummer

### <a name="format"></a>Format

acht Ziffern ohne Leerstellen oder Trennzeichen

### <a name="pattern"></a>Muster

- eine Ziffer von 1 bis 9
- Sechs Ziffern
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_slovenia_eu_tax_file_number"></a>Keywords\_slovenia\_eu\_tax\_file\_number
```
davčna številka
identifikacijska številka davka
številka davčne datoteke
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```

## <a name="slovenia-unique-master-citizen-number"></a>Slowenische eindeutige Personenidentifkationsnummer

### <a name="format"></a>Format

13 Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

13 Ziffern im angegebenen Muster:

- sieben Ziffern, die dem Geburtsdatum (TTMMLLL) entsprechen. Dabei steht LLL für die letzten drei Ziffern des Geburtsjahrs
- zwei Ziffern, die der Geburtsregion entsprechen (50)
- drei Ziffern, die einer Kombination aus Geschlecht und Seriennummer für Personen entsprechen, die am selben Tag geboren wurden. 000-499 für männliche und 500-999 für weibliche Personen.
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_slovenia_eu_national_id_card"></a>Keywords\_slovenia\_eu\_national\_id\_card
```
edinstvena številka glavnega državljana
emšo
enotna maticna številka obcana
id card
identification number
identifikacijska številka
identity card
nacionalna id
nacionalni potni list
national id
osebna izkaznica
osebni koda
osebni ne
osebni številka
personal code
personal number
personal numeric code
številka državljana
unique citizen number
unique id number
unique identity number
unique master citizen number
unique registration number
uniqueidentityno #
uniqueidentityno#
```

## <a name="south-africa-identification-number"></a>Südafrikanische ID-Nummer

### <a name="format"></a>Format

13 Ziffern, ggf. mit Leerstellen

### <a name="pattern"></a>Muster

13 Ziffern:

- Sechs Ziffern im Format JJMMTT als Geburtsdatum
- Vier Ziffern
- Eine Ziffer als Indikator für Staatsbürgerschaft
- Ziffer &quot;8&quot; oder &quot;9&quot;
- Eine Ziffer (Prüfsummenziffer)

### <a name="keywords"></a>Keywords

#### <a name="keyword_south_africa_identification_number"></a>Keyword\_south\_africa\_identification\_number

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Südkoreanische Einwohnerregistrierungsnummer (Resident Registration Number)

### <a name="format"></a>Format

13 Ziffern mit einem Bindestrich

### <a name="pattern"></a>Muster

13 Ziffern:

- Sechs Ziffern im Format JJMMTT als Geburtsdatum
- Ein Bindestrich
- Eine Ziffer basierend auf Jahrhundert und Geschlecht
- Vierstelliger Code für Geburtsregion
- Eine Ziffer zur Unterscheidung für Personen, bei denen die vorherigen Nummern identisch sind
- Eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keyword_south_korea_resident_number"></a>Keyword\_south\_korea\_resident\_number

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-dni"></a>Spanischer Personalausweis (DNI) 

### <a name="format"></a>Format

acht Ziffern, gefolgt von einem Zeichen

### <a name="pattern"></a>Muster

sieben Ziffern, gefolgt von einem Zeichen

- Acht Ziffern
- Optionale Leerstelle oder optionaler Bindestrich
- ein Prüfbuchstabe (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keywords_spain_eu_national_id_card"></a>Keywords\_spain\_eu\_national\_id\_card
```
carné de identidad
dni#
dni
dninúmero#
documento nacional de identidad
identidad único
identidadúnico#
insurance number
national identification number
national identity
nationalid#
nationalidno#
nie#
nie
nienúmero#
número de identificación
número nacional identidad
personal identification number
personal identity no
unique identity number
uniqueid#
```
## <a name="spain-drivers-license-number"></a>Spanische Führerscheinnummer 

### <a name="format"></a>Format

acht Ziffern, gefolgt von einem Zeichen

### <a name="pattern"></a>Muster

acht Ziffern, gefolgt von einem Zeichen:

- Acht Ziffern
- eine Ziffer oder ein Buchstabe (keine Beachtung der Groß-/Kleinschreibung)

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_spain_eu_drivers_license_number"></a>Keywords\_spain\_eu\_driver's\_license\_number
```
permiso de conducción
permiso conducción
licencia de conducir
licencia conducir
permiso conducir
permiso de conducir
permisos de conducir
permisos conducir
carnet conducir
carnet de conducir
licencia de manejo
licencia manejo
```
## <a name="spain-passport-number"></a>Spanische Reisepassnummer 

### <a name="format"></a>Format

eine Kombination aus Buchstaben und Zahlen ohne Leerstellen oder Trennzeichen mit acht oder neun Zeichen

### <a name="pattern"></a>Muster

eine Kombination aus Buchstaben und Zahlen mit acht oder neun Zeichen:

- zwei Ziffern oder Buchstaben
- eine Ziffer oder ein Buchstabe (optional)
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_passport_number"></a>Keywords\_eu\_passport\_number
```
passport#
passport #
passportid
passports
passportno
passport no
passportnumber
passport number
passportnumbers
passport numbers
```
#### <a name="keywords_spain_eu_passport_number"></a>Keywords\_spain\_eu\_passport\_number
```
libreta pasaporte
número pasaporte
españa pasaporte
números de pasaporte
número de pasaporte
números pasaporte
pasaporte no
Passeport n°
n° Passeport
pasaporte no.
pasaporte n°
spain passport
```
#### <a name="keywords_eu_passport_date"></a>Keywords\_eu\_passport\_date
```
date of issue
date of expiry
```
## <a name="spain-social-security-number-ssn"></a>Spanische Sozialversicherungsnummer (SSN)

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Sozialversicherungsnummer“ oder vergleichbaren vertraulichen Nummern enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Elf bis zwölf Ziffern

### <a name="pattern"></a>Muster

Elf bis zwölf Ziffern:

- Zwei Ziffern
- Ein Schrägstrich (optional)
- Sieben bis acht Ziffern
- Ein Schrägstrich (optional)
- Zwei Ziffern

### <a name="keywords"></a>Keywords

Keine

## <a name="spain-tax-identification-number"></a>Spanische Steueridentifikationsnummer 

### <a name="format"></a>Format

sieben oder acht Ziffern und ein oder zwei Buchstaben im angegebenen Muster

### <a name="pattern"></a>Muster

Spanische natürliche Personen mit spanischem nationalen Personalausweis:

- Acht Ziffern
- ein Großbuchstabe (Groß-/Kleinschreibung beachten)

Spanier mit Auslandswohnsitz und ohne spanischen nationalen Personalausweis

- ein Großbuchstabe L (Groß-/Kleinschreibung beachten)
- Sieben Ziffern
- ein Großbuchstabe (Groß-/Kleinschreibung beachten)

Spanier mit Wohnsitz in Spanien unter 14 Jahre ohne spanischen nationalen Personalausweis:

- ein Großbuchstabe K (Groß-/Kleinschreibung beachten)
- Sieben Ziffern
- ein Großbuchstabe (Groß-/Kleinschreibung beachten)

Ausländer mit Identifikationsnummer für Ausländer

- ein Großbuchstabe X, Y oder Z (Groß-/Kleinschreibung beachten)
- Sieben Ziffern
- ein Großbuchstabe (Groß-/Kleinschreibung beachten)

Ausländer ohne Identifikationsnummer für Ausländer

- ein Großbuchstabe M (Groß-/Kleinschreibung beachten)
- Sieben Ziffern
- ein Großbuchstabe (Groß-/Kleinschreibung beachten)

### <a name="keywords"></a>Keywords

#### <a name="keywords_spain_eu_tax_file_number"></a>Keywords\_spain\_eu\_tax\_file\_number
```
cif
cifid#
cifnúmero#
número de contribuyente
número de identificación fiscal
número de impuesto corporativo
spanishcifid#
spanishcifid
spanishcifno#
spanishcifno
tax file no
tax file number
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```
## <a name="sweden-drivers-license-number"></a>Schwedische Führerscheinnummer

### <a name="format"></a>Format

Zehn Ziffern mit einem Bindestrich

### <a name="pattern"></a>Muster

zehn Ziffern mit einem Bindestrich:

- Sechs Ziffern
- Ein Bindestrich
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_eu_drivers_license_number"></a>Keywords\_eu\_driver's\_license\_number
```
driverlic
driverlics
driverlicense
driverlicenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver licence
driver licences
driverslic
driverslics
driverslicence
driverslicences
driverslicense
driverslicenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers licence
drivers licences
driver'lic
driver'lics
driver'license
driver'licenses
driver'licence
driver'licences
driver' lic
driver' lics
driver' license
driver' licenses
driver' licence
driver' licences
driver'slic
driver'slics
driver'slicense
driver'slicenses
driver'slicence
driver'slicences
driver's lic
driver's lics
driver's license
driver's licenses
driver's licence
driver's licences
dl#
dls#
driverlic#
driverlics#
driverlicense#
driverlicenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licences#
driverslic#
driverslics#
driverslicense#
driverslicenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers licence#
drivers licences#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'licence#
driver'licences#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' licence#
driver' licences#
driver'slic#
driver'slics#
driver'slicense#
driver'slicenses#
driver'slicence#
driver'slicences#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's licence#
driver's licences#
driving licence 
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv licence
driv licences
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving licence
driving licences
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_sweden_eu_drivers_license_number"></a>Keywords\_sweden\_eu\_driver's\_license\_number
```
ajokortti
permis de conducere
ajokortin numero
kuljettajat lic.
drivere lic.
körkort
numărul permisului de conducere
שאָפער דערלויבעניש נומער
förare lic.
דריווערס דערלויבעניש
körkortsnummer
```

## <a name="sweden-national-id"></a>Nationale schwedische ID-Nummer

### <a name="format"></a>Format

Zehn oder zwölf Ziffern und ein optionales Trennzeichen

### <a name="pattern"></a>Muster

Zehn oder zwölf Ziffern und ein optionales Trennzeichen:

- Zwei Ziffern (optional)
- Sechs Ziffern als Datumsformat JJMMTT
- Trennzeichen &quot;-&quot; oder &quot;+&quot; (optional)
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keywords_swedish_national_identifier"></a>Keywords\_swedish\_national\_identifier

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Schwedische Reisepassnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Acht Ziffern

### <a name="pattern"></a>Muster

Acht aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_sweden_passport"></a>Keyword\_sweden\_passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="sweden-tax-identification-number"></a>Schwedische Steueridentifikationsnummer

### <a name="format"></a>Format

zehn Ziffern und ein Symbol im angegebenen Muster

### <a name="pattern"></a>Muster

zehn Ziffern und ein Symbol:

- sechs Ziffern, die dem Geburtsdatum (JJMMTT) entsprechen
- ein Plus- oder Minuszeichen
- drei Ziffern, die die ID eindeutig machen. Dabei gilt:
- für Nummern, die vor 1990 ausgegeben wurden, geben die siebente und achte Ziffer das Geburtsland von Personen an, die im Ausland geboren wurden
- die Ziffer an der neunten Position gibt das Geschlecht an. Ungerade Zahlen stehen für „männlich“, gerade für „weiblich“
- eine Prüfziffer

### <a name="keywords"></a>Keywords

#### <a name="keywords_sweden_eu_tax_file_number"></a>Keywords\_sweden\_eu\_tax\_file\_number
```
personal id number
personnummer
skatt id nummer
skatt identifikation
skattebetalarens identifikationsnummer
sverige tin
tax file
tax id
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
taxid#
taxidno#
taxidnumber#
taxno#
taxnumber#
taxnumber
tin id
tin no
tin#
```

## <a name="swift-code"></a>SWIFT-Code

### <a name="format"></a>Format

Vier Buchstaben, gefolgt von 5 bis 31 Buchstaben oder Ziffern

### <a name="pattern"></a>Muster

Vier Buchstaben, gefolgt von 5 bis 31 Buchstaben oder Ziffern:

- Vier Buchstaben als Bankcode (ohne Beachtung der Groß-/Kleinschreibung)
- Eine optionale Leerstelle
- 4 bis 28 Buchstaben oder Ziffern (einfache Bankkontonummer, BBAN)
- Eine optionale Leerstelle
- Ein bis drei Buchstaben oder Ziffern (Rest der BBAN)

### <a name="keywords"></a>Keywords

#### <a name="keyword_swift"></a>Keyword\_swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
\#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="switzerland-ssn-ahv-number"></a>Schweizer Sozialversicherungsnummer (AHV-Nummer)

### <a name="format"></a>Format

13-stellige Zahl

### <a name="pattern"></a>Muster

13-stellige Zahl:

- drei Ziffern – 756
- ein optionaler Punkt
- Vier Ziffern
- ein optionaler Punkt
- Vier Ziffern
- ein optionaler Punkt
- Zwei Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_swiss_ssn_ahv_number"></a>Keyword\_swiss\_ssn\_AHV\_number
```
ahv
ssn
pid
insurance number
personalidno#
social security number
personal id number
personal identification no.
insuranceno#
uniqueidno#
unique identification no.
avs number
personal identity no versicherungsnummer
identifikationsnummer
einzigartige identität nicht
sozialversicherungsnummer
identification personnelle id
numéro de sécurité sociale
```

## <a name="taiwan-national-identification-number"></a>Taiwanesische Personalausweisnummer

### <a name="format"></a>Format

Ein Buchstabe (Englisch), gefolgt von neun Ziffern

### <a name="pattern"></a>Muster

Ein Buchstabe (Englisch), gefolgt von neun Ziffern:

- Ein Buchstabe (Englisch, ohne Beachtung der Groß-/Kleinschreibung)
- Ziffer &quot;1&quot; oder &quot;2&quot;
- Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_taiwan_national_id"></a>Keyword\_taiwan\_national\_id

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Taiwanesische Reisepassnummer

### <a name="format"></a>Format

- Nummer des biometrischen Reisepasses: Neun Ziffern
- Nummer des nicht biometrischen Reisepasses: Neun Ziffern

### <a name="pattern"></a>Muster

Nummer des biometrischen Reisepasses:

- Zeichen &quot;3&quot;
- Acht Ziffern

Nummer des nicht biometrischen Reisepasses:

- Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_taiwan_passport"></a>Keyword\_taiwan\_passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Nummer des taiwanesischen Einwohnerzertifikats (ARC/TARC)

### <a name="format"></a>Format

Zehn Buchstaben und Ziffern

### <a name="pattern"></a>Muster

Zehn Buchstaben und Ziffern:

- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung)
- Acht Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_taiwan_resident_certificate"></a>Keyword\_taiwan\_resident\_certificate

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>Vereinigtes Königreich: Führerscheinnummer

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Führerscheinnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Kombination aus 18 Buchstaben und Ziffern im angegebenen Format

### <a name="pattern"></a>Muster

18 Buchstaben und Ziffern:

- Fünf Buchstaben (ohne Beachtung der Groß-/Kleinschreibung) oder die Ziffer &quot;9&quot; anstelle eines Buchstabens
- Eine Ziffer
- Fünf Ziffern als Datumsformat MMTTJ für das Geburtsdatum (Inkrementierung des siebten Zeichens mit 50 für weibliche Fahrer, also 51 bis 62 anstelle von 01 bis 12)
- Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung) oder die Ziffer &quot;9&quot; anstelle eines Buchstabens
- Fünf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_drivers_license"></a>Keyword\_uk\_drivers\_license

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>Vereinigtes Königreich: Wählerverzeichnisnummer

### <a name="format"></a>Format

Zwei Buchstaben, gefolgt von ein bis vier Ziffern

### <a name="pattern"></a>Muster

Zwei Buchstaben (ohne Beachtung der Groß-/Kleinschreibung), gefolgt von ein bis vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_electoral"></a>Keyword\_uk\_electoral

- council nomination
- nomination form
- electoral register
- electoral roll

## <a name="uk-national-health-service-number"></a>Vereinigtes Königreich: National Health Service-Nummer (NHS)

### <a name="format"></a>Format

10 bis 17 Ziffern, getrennt durch Leerstellen

### <a name="pattern"></a>Muster

10 bis 17 Ziffern:

- Entweder drei oder zehn Ziffern
- Eine Leerstelle
- Drei Ziffern
- Eine Leerstelle
- Vier Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_nhs_number"></a>Keyword\_uk\_nhs\_number

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Keyword\_uk\_nhs\_number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Keyword\_uk\_nhs\_number\_dob

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>Vereinigtes Königreich: Sozialversicherungsnummer (NINO)

Diese als vertraulich eingestufte Entität ist unter dem Vertraulichkeitsinformationstyp „EU-Personalausweisnummer“ enthalten und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Sieben Zeichen oder neun Zeichen, getrennt durch Leerstellen oder Bindestriche

### <a name="pattern"></a>Muster

Zwei mögliche Muster:

- Zwei Buchstaben (gültige Nummern enthalten in diesem Präfix nur bestimmte Zeichen, die bei diesem Muster überprüft werden; ohne Beachtung der Groß-/Kleinschreibung)
- Sechs Ziffern
- Entweder „A“, „B“, „C“ oder „D“ (wie beim Präfix sind auch im Suffix nur bestimmte Zeichen zulässig; ohne Beachtung der Groß-/Kleinschreibung).

oder

- Zwei Buchstaben
- Eine Leerstelle oder ein Bindestrich
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Entweder „A“, „B“, „C“ oder „D“


### <a name="keywords"></a>Keywords

#### <a name="keyword_uk_nino"></a>Keyword\_uk\_nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>Vereinigtes Königreich: Eindeutige Steuerzahlerreferenznummer

Diese als vertraulich eingestufte Nummer ist nur für folgende Zwecke verfügbar:

- Richtlinien zur Verhinderung von Datenverlust
- Richtlinien zur Konformität der Kommunikation
- Information Governance
- Datensatzverwaltung
- Microsoft Cloud App Security

### <a name="format"></a>Format

Zehn Ziffern ohne Leerstellen und Trennzeichen

### <a name="pattern"></a>Muster

10 Stellen

### <a name="keywords"></a>Keywords

#### <a name="keywords_uk_eu_tax_file_number"></a>Keywords\_uk\_eu\_tax\_file\_number

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="ukraine-passport-domestic"></a>Ukrainische Ausweisnummer (Inland)

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_ukraine_passport_domestic"></a>Keyword\_ukraine\_passport\_domestic
```
ukraine passport
passport number
passport no
паспорт України
номер паспорта
персональний
```
## <a name="ukraine-passport-international"></a>Ukrainische Reisepassnummer (international)

### <a name="format"></a>Format

alphanumerisches Muster mit acht Zeichen

#### <a name="pattern"></a>Muster

alphanumerisches Muster mit acht Zeichen:

- zwei Buchstaben oder Ziffern
- Sechs Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_ukraine_passport_international"></a>Keyword\_ukraine\_passport\_international
```
ukraine passport
passport number
passport no
паспорт України
номер паспорта
```
## <a name="us-bank-account-number"></a>US-Bankkontonummer

### <a name="format"></a>Format

6 bis 17 Ziffern

### <a name="pattern"></a>Muster

6 bis 17 aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_usa_bank_account"></a>Keyword\_usa\_Bank\_Account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>US-Führerscheinnummer

### <a name="format"></a>Format

Je nach Bundesstaat

### <a name="pattern"></a>Muster

Je nach Bundesstaat. Beispiel für New York:

- Neun Ziffern im Format ddd sind gültig.
- Neun Ziffern im Format „ddddddddd“ sind ungültig.

### <a name="keywords"></a>Keywords

#### <a name="keyword_us_drivers_license_abbreviations"></a>Keyword\_us\_drivers\_license\_abbreviations

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Keyword\_us\_drivers\_license

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Keyword\_[state\_name]\_drivers\_license\_name

- Abkürzung für Bundesstaat (z. B. &quot;NY&quot;)
- Name des Bundesstaats (z. B. &quot;New York&quot;)

## <a name="us-individual-taxpayer-identification-number-itin"></a>Persönliche Steuernummer in den USA (ITIN)

### <a name="format"></a>Format

Neun Ziffern, die mit einer &quot;9&quot; beginnen und eine &quot;7&quot; oder &quot;8&quot; als vierte Ziffer enthalten, optional mit Leerstellen oder Bindestrichen

### <a name="pattern"></a>Muster

Formatiert:

- Ziffer &quot;9&quot;
- Zwei Ziffern
- Eine Leerstelle oder ein Bindestrich
- Eine &quot;7&quot; oder &quot;8&quot;
- Eine Ziffer
- Eine Leerstelle oder ein Bindestrich
- Vier Ziffern

Unformatiert:

- Ziffer &quot;9&quot;
- Zwei Ziffern
- Eine &quot;7&quot; oder &quot;8&quot;
- Fünf Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_itin"></a>Keyword\_itin

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>US-Sozialversicherungsnummer (Social Security Number, SSN)

### <a name="format"></a>Format

Neun Ziffern (formatiertes oder unformatiertes Muster)

> [!Note]
> Bei Ausstellung vor Mitte 2011 verfügt eine SSN über eine feste Formatierung, bei der bestimmte Teile der Nummer in bestimmte Bereiche fallen müssen, damit die Nummer gültig ist (aber es ist keine Prüfsumme vorhanden).
>

### <a name="pattern"></a>Muster

Vier Funktionen suchen in vier unterschiedlichen Mustern nach SSNs:

- Mit „Func\_ssn“ werden SSNs mit fester Formatierung von vor 2011 ermittelt, die Bindestriche oder Leerstellen (ddd-dd-dddd ODER ddd dd dddd) enthalten.
- Mit „Func\_unformatted\_ssn“ werden SSNs mit fester Formatierung von vor 2011 ermittelt, die unformatiert sind und neun aufeinanderfolgende Ziffern (ddddddddd) enthalten.
- Mit „Func\_randomized\_formatted\_ssn“ werden nach 2011 ausgestellte SSNs ermittelt, die mit Bindestrichen oder Leerstellen (ddd-dd-dddd ODER ddd dd dddd) formatiert sind.
- Mit „Func\_randomized\_unformatted\_ssn“ werden nach 2011 ausgestellte SSNs ermittelt, die unformatiert sind und neun aufeinanderfolgende Ziffern (ddddddddd) enthalten.

### <a name="keywords"></a>Keywords

#### <a name="keyword_ssn"></a>Keyword\_ssn

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>USA/Vereinigtes Königreich: Reisepassnummer

Die als vertraulich eingestufte Reisepassnummer für das Vereinigte Königreich ist unter dem Vertraulichkeitsinformationstyp „EU-Reisepassnummer“ und als eigenständige Entität (vertraulich) verfügbar.

### <a name="format"></a>Format

Neun Ziffern

### <a name="pattern"></a>Muster

Neun aufeinanderfolgende Ziffern

### <a name="keywords"></a>Keywords

#### <a name="keyword_passport"></a>Keyword\_passport

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```
