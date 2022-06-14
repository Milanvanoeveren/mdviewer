Security (jaar 2, blok 8, 2021)

# Security

## Hoofstuk 1: inleiding

Cryptografie: maken en breken van codes.

Doelen:

- vertrouwelijkheid
- integriteit
- authenticatie
- onweerlegbaarheid

**Plain text**: leesbare tekst  
**Encrypten**: genereren van geheime tekst (cryptotekst / geheimtekst)  
**Decrypten**: teruggenereren van plain text uit cryptotekst  
**Sleutel**: speciale info benodigd voor en- of decrypten

## Hoofdstuk 2: cryptografische principes

Ieder cryptografisch algoritme moet aan de volgende principes voldoen:

- perfecte geheimhouding
  - alle denkbare plain text is even waarschijnlijk
  - type data niet af te leiden uit cryptotekst
  - er is niets over de structuur te zeggen
- geen 'security by obscurity'
  - **Kerckhoffs principe**: veiligheid van systeem mag niet afhangen van de kennis van een vijand daarover
  - **Security by obscurity**: geheimhouden van systeem om kraken te bemoeilijken > werkt niet!
- efficient te berekenen, hard te kraken
  - wat snel genoeg is hangt af van toepassing
  - brute force aanval moet weerstaan kunnen worden
- goede random getallen
  - normale computer gebruikt *pseudo random numbers*
  - voor cryptografie bepaalde wiskundige eisen > *cryptografisch veilig*
    - Windows: Cryptography API
    - Linux: `/dev/random` en `/dev/urandom`
- sleutelsterkte
  - is verwacht aantal pogingen benodigd voor brute force
  - kan afnemen door verbeterde kraaktechnieken
  - **entropie**: mate van willekeurigheid van data (in bits)
    - 32-bit entropie = `2^32` verschillende, even waarschijnlijke wachtwoorden
- symmetrische en asymmetrische algoritmen
  - zie volgende hoofdstukken

## Hoofdstuk 3: Symmetrische encryptie

- Dezelfde sleutel voor encrypten en decrypten
- Veilig zolang sleutel geheim is

**ROT-13**: verplaats letters van alfabet 13 plaatsen: `a` wordt `n`, `z` wordt `m`, cijfers niet vervangen.  
**Key exchange**: veilig uitwisselen van sleutel  
**Out of band**: key exchange over een ander kanaal dan de communicatie zelf

## Hoofdstuk 4: Asymmetrische encryptie

Synoniem: *public key encription*

- sleutels hebben speciale relatie tot elkaar (niet willekeurig)
- wat met private is gecrypt kan met public worden decrypt en andersom
- echtheid van publieke sleutel wordt gegarandeerd door certificaten.

**private key**: geheime sleutel  
**public key**: openbare sleutel  
**trapdoor**: functie die voor gegeven input snel de output berekent, maar andersom duurt lang > priemgetallen bijv.

Nadelen t.o.v. symmetrische encrypt:

- langzamer (ong. 1000x)
- kleinere sleutelsterkte bij gelijke sleutellengte
  - langere sleutel = gelijke veiligheid = nog langzamer
- genereert bij gelijke input langere cryptotekst

Verenigen voordelen:

1. assymetrische sleutelpaar
2. key exchange
3. symmetrisch data versleutelen met deze sleutels

## Hoofdstuk 5: Hashing

**checksum**: controlegetal dat door zender en ontvanger wordt vergeleken  
**error correcting codes**: detecteren en corrigeren van fouten (bijv. ECC-geheugen)  
**hash**: afbeelden van data op een vast formaat (dezelfde input = dezelfde output)  
**collision**: verschillende input met dezelfde output  
**cryptografisch veilige hashfunctie**: praktisch onmoogelijk om voor output (eenduidig) bijbehorende input te vinden > kan toevallige fouten en opzettelijke wijzigingen detecteren  
**rainbow table**: tabel met heel veel inputs en bijbehorende hashes

### 5.1: Wachtwoorden opslaan

- tegenwoordig niet gecrypt maar gehasht
- wens: hash van ene gebruiker geeft geen info over andere

**salt**: per gebruiker verschillende string die voor of achter het wachtwoord wordt toegevoegd > daarna hashen (salt wordt onversleuteld opgeslagen)

### 5.1: Berichten beveiligen en ondertekenen

Drie ondertekeningsdoelen voor een bericht:

1. integriteit
2. authenticatie
3. onweerlegbaarheid

*Hoe meer doelen, hoe intensiever de operatie.*

**MAC**: message authentication codes; aanvaller kan zonder sleutel het bericht niet ongemerkt wijzigen > bericht is niet onweerlegbaar  
**HMAC**: methode voor maken van MAC met cryptografische hashfunctie (*Hash-and-MAC*)

| | Hash | MAC | Digitale ondertekening |
|-|------|-----|------------------------|
| Sleutel | - | symmetrisch | asymmetrisch |
| Integriteit | ja | ja | ja |
| Authenticatie | - | ja | ja |
| Onweerlegbaarheid | - | - | ja |

## Hoofdstuk 6: Veelvoorkomende algoritmen

| Naam | Soort | Veiligheid |
|------|-------|------------|
| SHA-0 | hashing | verouderd |
| SHA-1 | hashing | legacy |
| SHA-128 | hashing | legacy |
| SHA-256 | hashing | actueel |
| MD5 | hashing | verouderd |
| DES | symmetrisch | verouderd |
| TDES | symmetrisch | verouderd |
| 3TDEA | symmetrisch | legacy |
| Rijndael | symmetrisch | actueel |
| RC4 | symmetrisch | legacy |
| AES-128 | symmetrisch | actueel |
| Blowfish | symmetrisch | legacy |
| Twofish | symmetrisch | actueel |
| DSA | asymmetrisch | legacy |
| RSA-512 | asymmetrisch | legacy |
| RSA-2048 | asymmetrisch | actueel |
| ECDSA | asymmetrisch | actueel |
| Curve25519 | asymmetrisch | actueel |

## Hoofdstuk 7: Certificaten

**Certificaat**: digitaal document dat public key bevat met info over de eigenaar  
**X.509**: standaard formaat certificaten  
**Certificaat Authoriteit**: (CA) ondertekent certificaten (asymmetrisch)  
**Chain of trust**: CA is ondertekend door hogere CA, enz.  
**Root CA**: einde van keten ondertekeningen  
**Public key infrastructure**: verzamelnaam voor gehele infrastructuur

*Een certificaat verliest zijn geldigheid als het einde van de geldigheidsduur is bereikt, of als hij ingetrokken (**revoked**) wordt.*

Niveau's van vertrouwen:

1. DV (domain validated) > aanvrager is admin van site
2. OV (organization validated) > id/postadres aanvrager is gecontroleerd
3. EV (extended validation) > extra controles, bijv. KvK-check

*Extra controles zijn in de praktijk vooral duurder, maar niet per se veiliger.*

**Let's Encrypt**: biedt gratis DV-certificaten aan

### OpenSSL

**.csr**: certificate signing request > wordt ter ondertekening aangeboden aan CA  
**.pem**: containerformaat voor `.csr`, keys, chains, etc.  
**.crt**: alias voor `.pem`  
**.key**: sleutelpaar in `.pem`-formaat > bevat private key!  
**.pkcs12**: sleutelpaar in `PKCS#12`-formaat > vooral op Windows

## Hoofdstuk 8: Toepassingen

### TLS

- protocol wordt gebruikt voor versleutelde https-verbindingen
- opvolger van SSL (soms synoniem)

**TLS-handshake**: opzetten van versleutelde verbinding

1. client stuurt verzoek naar server (met lijst ondersteunde encryptiemethoden)
2. server kiest sterkste methode en stuurt terug met certificaat (bevat public key)
3. client stuurt willekeurige data, versleuteld met public key
4. deze data wordt door beide gebruikt voor genereren **sessiesleutel**
5. sessiesleutel wordt gebruikt om alle data symmetrisch te versleutelen

Dus veilige combinatie van asymmetrisch en symmetrisch: efficient en veilig

### SSH

- versleutelde verbinding met andere computer (command line)
- ouder dan TLS, ander protocol
- vergelijkbaar met TLS-handshake
- uitzondering: verificatie authenticiteit van server
  - server presenteert eigen public key
  - gebruiker verifieert hash (alleen eerste keer)
  - ssh client slaat public key op in known_hosts

### Code signing

- applicaties kunnen ook ondertekend worden
- jar: `MANIFEST.INF` bevat hash voor elk bestand
- niet alle bestanden hoeven ondertekend te zijn
- bestanden kunnen door verschillende instanties ondertekend zijn
- complexiteit is geldigheidsduur van een certificaat > meestal een jaar

## Hoofdstuk 9: Authenticatie en Authorisatie

- beide vaak afgekort met *auth*
- authenticatie: vaststellen van identiteit
- authorisatie: toekennen van rechten > meestal na authenticatie

## Hoofdstuk 10: Authenticatie

- meestal gebruikersnaam/wachtwoord
- ook andere manieren

### Wachtwoorden

Manieren om te kraken:

- veelvoorkomende wachtwoorden proberen
- wachtwoorden van gebruiker op andere sites proberen
- alle mogelijkheden proberen (*brute force*)
- zoeken in *rainbow table*

Een goed wachtwoord:

- niet standaard / veel voorkomend
- op slechts 1 plek gebruiken
- hoge *entropie*

Systeembeheerder moet:

- wachtwoorden veilig opslaan
- brute force aanvallen afslaan (na x inlogpogingen)
- regelmatig rainbow tables controleren

### Public key authenticatie in SSH

- public key / private key combinatie is ook mogelijk i.p.v. gebruikersnaam/wachtwoord
- keys kunnen worden beveiligd met wachtwoord

### Multifactor authenticatie

**Factor**: aspect waarover alleen de rechtmatige eigenaar mag beschikken

Drie klassen:

1. dingen die je bezit > bankpas, telefoon, etc.
2. dingen die je weet > wachtwoord, pin, antwoord op vraag, etc.
3. dingen die je bent > vingerafdruk, iris-scan, etc.

*Factoren die in meerdere klassen vallen zijn onafhankelijk.*

**MFA / 2FA**: twee of meer onafhankelijke factoren combineren  
**Challenge - response**: server vraagt > client geeft juiste antwoord (bijv. TAN-codes)

### Eenmalige wachtwoorden (OTP)

- tijdelijk
- voorkomt uitlekken en kraken
- bezitfactor
- vaak met tokens op PC of mobiele telefoon

*Hardware tokens zijn veiliger dan software tokens.*

**HOTP**: OTP gebaseerd op HMAC > genereert codes met oplopende counter  
**TOTP**: OTP gebaseerd op tijd > vervangt counter door tijd

### Single sign on (SSO)

- eenmaal aanmelden voor meerdere systemen
- binnen organisatie bijv. met LDAP

**SAML**: (Security Assertion Markup Language) - XML-formaat waarmee uitspraken over iemands identiteit gedaan kunnen worden.

## Hoofdstuk 11: Authorisatie

**OAuth**: gebruiker authoriseert derde partij voor gebruik HTTP-service zonder volledige toegang tot account

- geen authenticatieprotocol (OpenID uitbreiding)
- twee versies (1.0 en 2.0) > niet compatibel, 1 is verouderd, 2 is eenvoudiger maar minder veilig
- framework > implementaties verschillen

*Bekendste manier om protocol te doorlopen is **authorization code grant**.*

- resource owner (gebruiker)
- client (applicatie)
- authorization endpoint (URL voor authorisatie > voor gebruiker)
- token endpoint (URL voor token > voor client)
- web API (applicatie die client gaat gebruiken)
- client id (identificeert client, niet geheim)
- authorzation code (aanbieden aan token endpoint, beperkte geldigheidsduur)
- access token (geheim, beperkte geldigheidsduur)
- refresh token (ververst access token zonder authorization code, geheim)

**Proof Key for Code Exchange**: (PKCE) - verbetert robuustheid *authorization grant flow*  
**Implicit flow**: verouderd, onveilig

1. client genereert **code_verifier** en bewaart deze
2. client genereert hash hiervan: **code_challenge** (standaard met SHA-256)
3. client stuurt code_challenge met hashmethode door naar authorzation endpoint
4. authorization endpoint genereert authorization code en bewaart code_challenge
5. client stuurt request naar token endpoint en stuurt code_verifier mee
6. server controleert code_verifier met code_challenge
7. server stuurt token
