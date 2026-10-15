![Home Assistant](https://img.shields.io/badge/Home%20Assistant-2026%2B-blue.svg)
![Script](https://img.shields.io/badge/Home%20Assistant-Script-success.svg)
![License](https://img.shields.io/badge/License-Community-green.svg)



# Er-is-post-in-de-brievenbus  📬
Home Assistant automations en scripts voor het smart maken van je brievenbus.
<br>
<br>
<br>
<br>
Ik wil een klein projectje delen dat ik gemaakt heb: een slimme brievenbus die mij en mijn vrouw een melding op onze telefoon geeft zodra er nieuwe post is.

Zo maak je van een gewone, 'domme' brievenbus in een handomdraai een slimme brievenbus met Home Assistant.

Het mooie is dat ik voor dit project maar twee sensoren nodig had. Die had ik nog liggen, dus ik kon meteen aan de slag!

## Hoe werkt het?

De opstelling hangt een beetje af van het type brievenbus dat je hebt. Onze brievenbus heeft een klep aan de bovenkant waar de post doorheen valt en een deurtje aan de voorkant om de post eruit te halen.

Ik had daarom twee sensoren nodig: één om te detecteren wanneer er post wordt bezorgd en één om bij te houden wanneer de brievenbus wordt geopend.

Onze brievenbus is van metaal. In eerste instantie vroeg ik me af of dat problemen zou opleveren voor het Zigbee-signaal, maar dat blijkt gelukkig geen probleem te zijn.

## Wat heb je nodig?

Voor dit project heb ik twee sensoren en vijf helpers aangemaakt.

### Benodigdheden

- 1 sensor voor de beweging van de klep.
- 1 sensor om het openen en sluiten van het deurtje te detecteren.
- Home Assistant met de benodigde automations, scripts en helpers.
- Een telefoonn meldingssysteem dat acties ondersteunt, zoals de Home Assistant Companion-app.

### De sensoren

Ik gebruik de volgende entities:

- `binary_sensor.brievenbus_klep` — detecteert wanneer de klep beweegt en er mogelijk post wordt bezorgd.
- `binary_sensor.brievenbus_deur` — detecteert wanneer de brievenbus wordt geopend.

De namen hierboven zijn die van mijn eigen installatie. Je zult ze dus moeten aanpassen aan de sensoren die je zelf gebruikt.

### De vijf helpers

**1. Teller voor de bezorgde post**

Houdt bij hoe vaak er post is bezorgd.

```yaml
counter:
  brievenbus_post_vandaag:
    name: Brievenbus post vandaag
    icon: mdi:mailbox
    step: 1
```

**2. Laatste postdetectie**

Onthoudt wanneer de laatste post is gedetecteerd. Dit helpt onder andere om dubbele detecties te voorkomen.

```yaml
input_datetime:
  brievenbus_laatste_detectie:
    name: Brievenbus laatste detectie
    has_date: true
    has_time: true
```

**3. Laatste deuropening**

Onthoudt wanneer het deurtje voor het laatst is geopend. Hiermee kan het systeem rekening houden met bewegingen die ontstaan tijdens het openen van de brievenbus.

```yaml
input_datetime:
  brievenbus_laatste_deuropening:
    name: Brievenbus laatste deuropening
    has_date: true
    has_time: true
```

**4. Brievenbus afgehandeld**

Houdt bij of de post al is opgehaald en afgehandeld.

```yaml
input_boolean:
  brievenbus_afgehandeld:
    name: Brievenbus afgehandeld
    icon: mdi:mailbox-open
```

**5. Brievenbus onderweg**

Houdt bij of iemand heeft aangegeven de brievenbus te gaan legen.

```yaml
input_boolean:
  brievenbus_onderweg:
    name: Brievenbus onderweg
    icon: mdi:walk
```

> **Let op:** bovenstaande YAML-fragmenten laten de configuratie van de helpers zien. Plaats ze op de juiste plek in je eigen Home Assistant-configuratie. Gebruik je de YAML-configuratie voor helpers, let dan op de bestaande structuur van je configuratiebestand. Maak je de helpers via de gebruikersinterface aan, dan hoef je deze YAML niet over te nemen.
<br>
<br>

```
** Een mogelijke mappenstructuur **

home-assistant-slimme-brievenbus/
├── README.md
├── automations/
│   ├── brievenbus_post_gedetecteerd.yaml
│   └── ...
├── scripts/
│   ├── brievenbus_afhandelen.yaml
│   └── ...
└── images/
    └── brievenbus-opstelling.jpg
```
    
## Een melding zodra er post is!

Zodra er post in de brievenbus valt, krijgen mijn vrouw en ik allebei een melding op onze telefoon.

In die melding kunnen we aangeven of we de brievenbus gaan legen. Drukt één van ons op **Ja**, dan weet de ander dat de brievenbus wordt geleegd. Zo voorkomen we dat we allebei naar buiten lopen voor dezelfde stapel post.

Zodra iemand de brievenbus opent, gaat Home Assistant ervan uit dat de post is opgehaald. De meldingen worden opgeruimd, de teller wordt weer op nul gezet en de brievenbus wordt als afgehandeld gemarkeerd.

En dat werkt ook als niemand op Ja heeft gedrukt, maar iemand gewoon zelf naar buiten loopt om de post te pakken.

## Geen post vergeten

Soms druk je op Ja, maar komt er iets tussen. Daarom volgt er na 15 minuten een herinnering als de brievenbus nog niet is geopend.

Ligt er om 18.00 uur nog post in de brievenbus? Dan krijgen we allebei nog een herinnering.

Wordt er 's avonds of 's nachts post bezorgd, dan krijgen we niet midden in de nacht een melding. In plaats daarvan ontvangen we de volgende ochtend om 07.00 uur een bericht.

Ook aan het deurtje heb ik gedacht. Blijft dat langer dan vijf minuten openstaan, dan krijgen we allebei een waarschuwing. Wordt het deurtje eerder gesloten, dan gebeurt er verder niets.

## En als je op Nee drukt?

Niet iedereen heeft altijd tijd om direct de brievenbus te legen. Daarom kun je ook op **Nee** drukken.

Er gebeurt dan verder niets: de post blijft geregistreerd en de gebruikelijke herinneringen blijven gewoon werken. Zodra iemand de brievenbus opent, wordt alles alsnog netjes afgehandeld.

## Voorkomen dat het systeem in de war raakt

Een bewegende klep betekent niet altijd dat er nieuwe post is. Het kan bijvoorbeeld ook gebeuren dat je de brievenbus opent en daarbij de klep beweegt.

Daarom probeert het systeem zulke situaties te herkennen en onterechte meldingen te voorkomen. Ook wordt bijgehouden wanneer de klep voor het laatst beweging heeft gedetecteerd, zodat dezelfde post niet meerdere keren wordt geregistreerd.

Komt er opnieuw post binnen terwijl er nog post ligt, dan begint het proces opnieuw. De teller loopt verder op en de nieuwe melding vervangt de lopende herinneringsronde.

## Waarom losse automations en scripts?

In Home Assistant kun je zoiets op verschillende manieren bouwen. Je kunt bijvoorbeeld een kant-en-klare blueprint gebruiken, maar je kunt ook zelf de verschillende onderdelen maken.

Ik heb gekozen voor losse automations en scripts. Dat heeft voor mij drie voordelen:

- **Geen black box:** ik kan precies zien wat er gebeurt en waarom.
- **Veel vrijheid:** ik kan het systeem aanpassen aan mijn eigen wensen.
- **Leren door te doen:** je leert Home Assistant beter begrijpen door zelf iets te bouwen.

Het nadeel is dat je wat meer kennis van YAML nodig hebt. Daarom heb ik de onderdelen voorzien van uitleg en commentaar, zodat ook iemand die nog niet zo lang met Home Assistant werkt ermee aan de slag kan.

Het complete systeem bestaat uit negen automations en twee scripts.

Ik ga er wel van uit dat je de basis van Home Assistant en YAML kent en weet waar je de configuratie moet plaatsen.

## Testen, testen en nog eens testen

Voordat ik het project wilde delen, heb ik de werking gecontroleerd aan de hand van verschillende scenario's. Ik wilde niet alleen testen of er een melding komt wanneer er post wordt bezorgd, maar ook of het systeem goed omgaat met situaties die in het dagelijks gebruik kunnen voorkomen.

### Scenario 1 – Post ontvangen en ophalen via de melding

- Er wordt post bezorgd en de teller gaat met één omhoog.
- Beide telefoons ontvangen een melding.
- Eén van ons drukt op Ja.
- Het systeem houdt bij dat iemand onderweg is.
- Zodra de brievenbus wordt geopend, wordt de actie afgehandeld.
- De teller wordt gereset en de meldingen verdwijnen.
- Het deurtje wordt weer gesloten.

### Scenario 2 – Post ophalen zonder op Ja te drukken

- Er wordt post bezorgd en beide telefoons ontvangen een melding.
- Niemand drukt op Ja of Nee.
- Eén van ons loopt zelf naar de brievenbus.
- Zodra de deur wordt geopend, wordt de post als opgehaald beschouwd.
- De teller wordt gereset en de meldingen verdwijnen.

### Scenario 3 – Wel op Ja drukken, maar de post niet ophalen

- Er wordt post bezorgd en we ontvangen allebei een melding.
- Eén van ons drukt op Ja.
- De brievenbus wordt de eerste 15 minuten niet geopend.
- Er volgt een herinnering dat de brievenbus nog niet is geleegd.

### Scenario 4 – Nieuwe post terwijl er nog post ligt

- Om 09.00 uur wordt post bezorgd.
- Om 09.01 uur drukt één van ons op Ja.
- Om 09.05 uur wordt opnieuw post bezorgd.
- De teller loopt op naar twee.
- De lopende herinneringsronde wordt gestopt.
- Er start een nieuwe ronde met nieuwe meldingen.

### Scenario 5 – Post in de avond of nacht

- Om 23.00 uur wordt post bezorgd.
- Er volgt op dat moment geen melding.
- Om 07.00 uur de volgende ochtend ontvangen we allebei een melding.

### Scenario 6 – Het deurtje blijft openstaan

- Er wordt post bezorgd en we ontvangen een melding.
- De brievenbus wordt geopend en de post wordt als opgehaald beschouwd.
- De teller wordt gereset en de meldingen verdwijnen.
- Het deurtje blijft openstaan.
- Na vijf minuten ontvangen we allebei een waarschuwing.

### Scenario 7 – Het deurtje wordt op tijd gesloten

- Er wordt post bezorgd en we ontvangen een melding.
- De brievenbus wordt geopend en de post wordt als opgehaald beschouwd.
- Het deurtje wordt binnen vijf minuten gesloten.
- Er volgt geen waarschuwing.

### Scenario 8 – Op Nee drukken

- Er wordt post bezorgd en we ontvangen allebei een melding.
- Eén van ons drukt op Nee.
- De post blijft geregistreerd en de teller verandert niet.
- De status 'onderweg' blijft uitgeschakeld.
- De gebruikelijke herinneringen en de detectie van het openen van de brievenbus blijven werken.

### Scenario 9 – De brievenbus openen zonder dat er post ligt

- Eén van ons opent de brievenbus.
- Er wordt geen nieuwe post geregistreerd.
- Er volgt geen melding en het systeem blijft normaal werken.

## De configuratie

De bijbehorende automations en scripts zijn bedoeld om samen één geheel te vormen. Neem daarom niet zomaar één onderdeel over zonder te controleren welke helpers, entities en andere onderdelen ervan afhankelijk zijn.

Pas de entity-ID's, meldingsacties en eventuele verwijzingen naar scripts aan je eigen installatie aan.

## Samengevat

Een brievenbussysteem dat rekening houdt met allerlei situaties uit het dagelijks leven. Geen onnodige meldingen, geen dubbele meldingen en hopelijk ook geen vergeten post meer!

Heb je zelf al eens iets vergelijkbaars gebouwd, of heb je ideeën om dit systeem nog slimmer te maken? Dan hoor ik het graag.
