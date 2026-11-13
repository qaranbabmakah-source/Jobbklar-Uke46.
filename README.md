# Skolearbeid-Jobbklar-Uke46.

Ukeplan – sikkerhet i Active Directory og Group Policy

Praktisk laboratorierapport | Mandag–fredag

Denne rapporten dokumenterer arbeid med identitet, tilgangsstyring, prinsippet om minste privilegium, Group Policy, sikkerhetsbaseline, passordsikkerhet, kontolåsing, auditing og systematisk feilsøking i et Windows-domene.

Miljø: firma.local · Domenekontroller: DC01 · Klient: CLIENT01 · Organisasjonsenheter: Bedrift/Brukere og Bedrift/Datamaskiner.

>

Om dokumentasjonen: Skjermbilder er plassert ved de relevante stegene. Der et bilde ikke er lagt ved, finnes en tydelig plassholder som kan erstattes med et eget skjermbilde. Eksempelnavn og resultater nedenfor beskriver laboratoriemiljøet.

Mandag – identitet og minste privilegium

Steg 1 – Domain Admins og privilegerte grupper

Hvorfor er Domain Admins viktig? Medlemmene i Domain Admins har svært høye rettigheter og kan administrere hele domenet. Vanlige brukere kan bruke ressurser de har fått tilgang til, mens domenadministratorer kan endre brukere, grupper og sikkerhetsinnstillinger. Derfor bør så få som mulig være medlemmer. Dette følger prinsippet om minste privilegium.

Skjermbilde – medlemmer av Domain Admins:

📷 Sett inn skjermbilde: Steg 1 – Domain Admins

Steg 4 – Vanlig konto og administratorkonto

Det er viktig å skille mellom en vanlig brukerkonto og en administratorkonto for å beskytte Active Directory og domenet.

En vanlig brukerkonto, for eksempel ola.nordmann, brukes til daglige oppgaver som å lese e-post, surfe på internett, skrive dokumenter og bruke vanlige programmer. En administratorkonto, for eksempel adm-ola, brukes bare til administrative oppgaver som å administrere servere, opprette brukere og konfigurere Group Policy.

Hvis en vanlig brukerkonto kompromitteres, skal det ikke automatisk gi tilgang til å administrere hele domenet. Separate kontoer reduserer konsekvensene av feil og skadevare og støtter minste privilegium.

Skjermbilde – vanlige og administrative kontoer:

📷 Sett inn skjermbilde: Steg 4 – separate kontoer

Steg 5 – Deaktiver en testkonto

Jeg opprettet testbrukeren gammel.bruker i Active Directory og deaktiverte kontoen. Deretter kontrollerte jeg statusen i PowerShell. Resultatet viste Enabled: False, som betyr at kontoen er deaktivert og ikke kan brukes til vanlig innlogging.

Det kan være bedre å deaktivere en konto før den slettes: Pålogging blir blokkert, samtidig som kontoinformasjon og gruppemedlemskap fortsatt kan undersøkes.

Skjermbilde – `Enabled: False`:

📷 Sett inn skjermbilde: Steg 5 – deaktivert konto

Mandagens grunnoppgave – spørsmål og svar

Hva betyr minste privilegium? En bruker skal bare ha rettighetene som er nødvendige for arbeidsoppgavene.

Hvorfor bør ikke alle være administratorer? Høye rettigheter øker risikoen ved feil og sikkerhetsbrudd.

Hva er en privilegert konto? En konto med ekstra rettigheter til å administrere systemer, brukere eller sikkerhetsinnstillinger.

Hvorfor bør Domain Admins ha få medlemmer? For å redusere risikoen for misbruk av omfattende domenetilganger.

Hvorfor deaktivere før sletting? Pålogging stoppes, men informasjon kan beholdes for kontroll og eventuell senere oppfølging.

Hvorfor bruke to kontoer? En vanlig konto brukes daglig; en administrativ konto brukes kun når ekstra rettigheter er nødvendige.

Videre utforskning – sikkerhetsrapport

Jeg undersøkte brukerkontoer i Active Directory med PowerShell: om kontoene var aktive eller deaktiverte, når passordene sist ble endret og tilgjengelig informasjon om siste innlogging. Resultatet ble eksportert til C:\Lab\brukersikkerhet.csv.

Jeg undersøkte også privilegerte grupper. I laboratoriet var Administrator medlem av både Domain Admins og Enterprise Admins, mens Gammel Bruker var deaktivert (Enabled = False). Dette viser hvorfor privilegerte gruppemedlemskap bør begrenses og kontrolleres.

Skjermbilde – eksport og privilegerte grupper:

📷 Sett inn skjermbilde: Mandag – PowerShell-sikkerhetsrapport

Tirsdag – modeller sikkerheten

Dagens mål

Jeg arbeidet med en konseptuell sikkerhetsmodell og sammenlignet den med det faktiske Active Directory-miljøet. Målet var å forstå sammenhengen mellom brukere, grupper, klienter, DNS, OU-er, GPO-er og tilgang.

Steg 1–2 – Sikkerhetsmodell

Modellen beskriver domenekontrolleren DC01, klienten CLIENT01, brukere og sikkerhetsgrupper. Brukerkontoer plasseres i hensiktsmessige OU-er, og tilgang gis gjennom grupper i stedet for å tildele unødvendige administratorrettigheter direkte til enkeltbrukere.

Skjermbilde/diagram – sikkerhetsmodell:

📷 Sett inn skjermbilde: Tirsdag – sikkerhetsmodell

Steg 3 – Finn risikoer og foreslå tiltak

  |   Risiko   |   Mulig sikkerhetstiltak   |  

  |  ---  |  ---  |  

  |   For mange brukere er Domain Admins   |   Begrens medlemskap og følg minste privilegium.   |  

  |   En gammel konto er fortsatt aktiv   |   Deaktiver kontoer som ikke lenger brukes.   |  

  |   Klienten bruker feil DNS-server   |   Konfigurer DNS til riktig domenetjeneste og test navneoppslag.   |  

  |   Alle får tilgang til en ressurs   |   Bruk sikkerhetsgrupper og nødvendige tilgangstillatelser.   |  

  |   Ingen sikkerhets-GPO brukes på klientene   |   Opprett, koble og verifiser en sikkerhets-GPO.   |  

  |   Ingen kontrollerer sikkerhetslogger   |   Aktiver relevant auditing og gjennomgå logger regelmessig.   |  

  |   Samme administratorkonto brukes til alt   |   Skill mellom vanlig konto og administratorkonto.   |  

Skjermbilde – risikovurdering:

📷 Sett inn skjermbilde: Tirsdag – risikoer og tiltak

Steg 4 – Sammenlign modell og virkelig miljø

Jeg sammenlignet sikkerhetsmodellen med det faktiske miljøet. Jeg kontrollerte at sikkerhetsgruppene GG-Support og GG-Administrasjon finnes, og at CLIENT01 ligger i firma.local/Bedrift/Datamaskiner. Jeg undersøkte også DNS-konfigurasjonen, GPO-koblingen og gruppemedlemskapet til Sara Ahmed i GG-Support.

Jeg brukte gpresult /scope computer /r på klienten for å se hvilke GPO-er som faktisk var brukt. Dokumentasjonen fra denne kontrollen viser en sikkerhets-GPO og Default Domain Policy. Merk: GPO-navnet i eldre deler av laboratoriet kan avvike fra navnet på sikkerhetsbaseline-GPO-en som ble opprettet senere.

Skjermbilde – IP-konfigurasjon / DNS-kontroll:



Tirsdag – ipconfig og nettverksinformasjon

Skjermbilde – anvendte GPO-er på CLIENT01:



Tirsdag – gpresult i kommandolinjen

Konklusjon: Sikkerhetsmodellen stemte med viktige deler av miljøet. Kontrollen hjalp meg å knytte teori om grupper, DNS og Group Policy til konkrete innstillinger.

Onsdag – sikkerhetsbaseline med Group Policy

Steg 1 – Sikkerhetsbaseline

Jeg opprettet GPO-Sikkerhetsbaseline-Client01 på DC01 og konfigurerte Windows Defender Firewall slik at Domain, Private og Public var aktivert. Jeg koblet GPO-en til Bedrift/Datamaskiner, der CLIENT01 ligger.

Steg 2 – Opprett en egen sikkerhets-GPO

En separat GPO gjør det enklere å administrere, teste og dokumentere sikkerhetsinnstillinger uten å blande dem med andre regler. Jeg kontrollerte at GPO-en var koblet til riktig OU og ble brukt på klienten.

Skjermbilde – GPO og OU-kobling:

📷 Sett inn skjermbilde: Onsdag – GPO-kobling

Steg 3 – Automatisk låsing av maskinen

I Group Policy Management Editor gikk jeg til:

Computer Configuration → Policies → Windows Settings → Security Settings → Local Policies → Security Options

Jeg konfigurerte Interactive logon: Machine inactivity limit til 600 sekunder (10 minutter). Deretter kjørte jeg gpupdate /force og undersøkte resultatet med gpresult. I laboratoriet ble automatisk låsing testet ved å la klienten stå ubrukt.

Skjermbilde – automatisk låsing:

📷 Sett inn skjermbilde: Onsdag – automatisk låsing

Steg 4 – Kontroller Windows Firewall

På CLIENT01 kjørte jeg:

```powershell

netsh advfirewall show allprofiles state

`

Resultatet viste at Domain Profile, Private Profile og Public Profile var ON. Brannmuren kontrollerer nettverkstrafikk og er et ekstra sikkerhetslag.

Skjermbilde – alle brannmurprofiler ON:

📷 Sett inn skjermbilde: Onsdag – Windows Firewall

Steg 5 – Oppdater CLIENT01

Jeg kjørte:

```powershell

gpupdate /force

gpresult /scope computer /r

`

Oppdateringen ble fullført, og gpresult viste at sikkerhets-GPO-en ble brukt på CLIENT01.

Skjermbilde – Group Policy-resultater:

📷 Sett inn skjermbilde: Onsdag – gpresult

Steg 6 – Lag en HTML-rapport

Jeg opprettet og åpnet en HTML-rapport:

```powershell

gpresult /scope computer /h C:\Temp\gpo.html

`

Rapporten viste at CLIENT01 tilhører firma.local og ligger i Bedrift/Datamaskiner. Det ble ikke vist feil ved behandlingen av datamaskinpolicyene. Rapporten viste også en advarsel om en treg kobling; dette er ikke det samme som en behandlingsfeil.

Skjermbilder – HTML-rapporten:



Onsdag – gpresult HTML-rapport



Onsdag – oversikt over gpresult HTML

Videre utforskning – Security Filtering

Jeg undersøkte Security Filtering for GPO-Sikkerhetsbaseline-Client01. GPO-en var koblet til Bedrift/Datamaskiner, koblingen var aktivert, og Authenticated Users var oppført under Security Filtering.

Security Filtering kan begrense hvilke brukere eller datamaskiner som får bruke en GPO. Det forutsetter at de aktuelle objektene har nødvendige rettigheter til å lese og bruke policyen. Jeg undersøkte innstillingene uten å endre dem.

Skjermbilde – Security Filtering:

📷 Sett inn skjermbilde: Onsdag – Security Filtering

Konklusjon: Jeg konfigurerte en sikkerhetsbaseline, verifiserte brannmurprofiler og undersøkte at klienten mottok de relevante gruppepolicyene.

Torsdag – passordsikkerhet, kontolåsing og logging

Steg 1 – Kontroller domenets kontopolicy

Jeg åpnet Default Domain Policy i Group Policy Management og undersøkte Password Policy og Account Lockout Policy.

  |   Innstilling   |   Verdi før endring   |  

  |  ---  |  ---  |  

  |   Minimum passordlengde   |   7 tegn   |  

  |   Maksimal passordalder   |   42 dager   |  

  |   Minimum passordalder   |   1 dag   |  

  |   Passordhistorikk   |   24 passord   |  

  |   Kompleksitetskrav   |   Aktivert   |  

  |   Account lockout threshold   |   0 ugyldige forsøk   |  

En terskel på 0 betyr at kontoen ikke automatisk låses basert på antall feil passordforsøk. Før endringen var varighet og nullstilling ikke definert i den undersøkte GPO-en.

Skjermbilde – opprinnelig kontolåsepolicy:



Torsdag – Account Lockout Policy før endring

Steg 2 – Konfigurer Account Lockout Policy

Jeg konfigurerte følgende verdier i laboratoriet:

  |   Innstilling   |   Ny verdi   |  

  |  ---  |  ---  |  

  |   Account lockout threshold   |   5 feil påloggingsforsøk   |  

  |   Account lockout duration   |   10 minutter   |  

  |   Reset account lockout counter after   |   10 minutter   |  

Etterpå kjørte jeg:

```powershell

gpupdate /force

net accounts /domain

`

net accounts /domain bekreftet at terskelen var 5 og låsevarigheten 10 minutter. Kontolåsing reduserer risikoen ved gjentatte passordforsøk, men innstillingene må balanseres mot brukervennlighet.

Skjermbilde – nye verdier / `net accounts /domain`:

📷 Sett inn skjermbilde: Torsdag – konfigurert kontolåsing

Steg 3 – Test kontolåsing med testbruker

Jeg opprettet testbruker1 i firma.local → Bedrift → Brukere. På CLIENT01 forsøkte jeg å logge inn med feil passord flere ganger. Etter gjentatte feil viste Windows meldingen om at kontoen var sperret og ikke kunne logge på.

På DC01 kontrollerte jeg statusen:

```powershell

Get-ADUser testbruker1 -Properties LockedOut

`

Resultatet viste Enabled: True og LockedOut: True. Kontoen var altså aktiv, men låst. Testkontoen ble brukt for å unngå å låse en administratorkonto.

Skjermbilde – sperret konto på CLIENT01:



Torsdag – testbruker låst på CLIENT01

Skjermbilde – `LockedOut: True`:

📷 Sett inn skjermbilde: Torsdag – LockedOut True

Steg 4 – Lås opp testbrukeren

Jeg åpnet PowerShell på DC01 og brukte:

```powershell

Unlock-ADAccount testbruker1

Get-ADUser testbruker1 -Properties LockedOut

`

Resultatet viste LockedOut: False, som bekreftet at kontoen ikke lenger var sperret. Jeg prøvde først et feil kommandonavn, Unlock-ADUser, men korrigerte dette til den riktige cmdleten Unlock-ADAccount.

Skjermbilde – opplåsing og bekreftelse:



Torsdag – Unlock-ADAccount og LockedOut False

Steg 5 – Undersøk sikkerhetsloggen

Jeg åpnet Event Viewer → Windows Logs → Security på DC01. Først undersøkte jeg Event ID 4625 (mislykket pålogging), men de viste hendelsene gjaldt ikke nødvendigvis testbrukeren.

Deretter fant jeg Event ID `4740`, som viste at FIRMA\testbruker1 var blitt låst. Hendelsen oppga CLIENT01 som kildedatamaskin, DC01.firma.local som domenekontroller og tidspunkt 12.11.2026 kl. 13:52:27.

Dette stemte med PowerShell-kontrollen LockedOut: True.

Skjermbilde – Event ID 4740:

📷 Sett inn skjermbilde: Torsdag – Event ID 4740

Steg 6 – Auditing og PowerShell

Jeg undersøkte mislykkede pålogginger med:

```powershell

Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 10

`

Kommandoen viste fem hendelser med Event ID 4625. Jeg kunne se tidspunkt, hendelses-ID og melding. Disse hendelsene var fra tidligere datoer og dokumenterer ikke nødvendigvis innloggingsforsøkene med testbruker1.

Auditing og PowerShell gjør det enklere å finne, filtrere og undersøke relevante sikkerhetshendelser.

Skjermbilde – PowerShell, Event ID 4625:

📷 Sett inn skjermbilde: Torsdag – PowerShell auditing

Steg 7 – Sikkerhetsdetektiv

  |   Del   |   Dokumentasjon   |  

  |  ---  |  ---  |  

  |   **Symptom**   |   `testbruker1` kunne ikke logge inn på `CLIENT01`.   |  

  |   **Hypotese**   |   Kontoen var låst etter feil passordforsøk.   |  

  |   **Test**   |   `Get-ADUser testbruker1 -Properties LockedOut` og Event Viewer.   |  

  |   **Funn**   |   `LockedOut: True` og Event ID `4740`.   |  

  |   **Løsning**   |   `Unlock-ADAccount testbruker1`, deretter `LockedOut: False`.   |  

Konklusjon: Jeg fant årsaken ved systematisk feilsøking og bekreftet løsningen.

Torsdagens grunnoppgave – spørsmål og svar

Hva er en passordpolicy? Regler for blant annet passordlengde, kompleksitet, historikk og alder.

Hva er en kontolåsepolicy? Regler for når en konto låses etter feil påloggingsforsøk, og hvor lenge låsingen varer.

Hvorfor bruke kontolåsing? For å begrense gjentatte forsøk på å gjette passord.

Hvorfor ikke teste med Administrator? Kontoen har høye rettigheter og er viktig for administrasjonen; en vanlig testkonto er tryggere.

Forebygging kontra logging? Forebygging stopper eller begrenser uønsket aktivitet; logging registrerer det som skjedde.

Hva betyr auditing? Registrering og kontroll av sikkerhetsrelevante hendelser.

Hvorfor er Event Viewer viktig? Administratoren kan undersøke hendelser, tidspunkt, kontoer og feilmeldinger.

Hva er ulempen med for streng policy? Brukere kan bli låst ute unødvendig ofte, og supportbehovet kan øke.

Videre utforskning – Advanced Audit Policy

Jeg undersøkte Advanced Audit Policy Configuration i Default Domain Policy, blant annet kategoriene Account Logon og Logon/Logoff. De undersøkte innstillingene stod som Ikke konfigurert i denne GPO-en. Det betyr ikke nødvendigvis at auditing er deaktivert i miljøet. Jeg gjorde ingen endringer.

Skjermbilde – Advanced Audit Policy:

📷 Sett inn skjermbilde: Torsdag – Advanced Audit Policy

Videre utforskning – Sammenlign Event ID 4624 og 4625

Jeg kjørte:

```powershell

Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4624} -MaxEvents 5

Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625} -MaxEvents 5

`

4624: Vellykket pålogging.

4625: Mislykket pålogging.

Begge kommandoene viste fem hendelser. Hendelsene med ID 4625 var eldre enn de viste hendelsene med ID 4624, og de kan derfor ikke uten videre knyttes til testen med testbruker1.

Skjermbilde – sammenligning av 4624 og 4625:

📷 Sett inn skjermbilde: Torsdag – Event ID 4624 og 4625

Torsdagens konklusjon: Jeg konfigurerte og testet kontolåsing, låste opp en testkonto, undersøkte sikkerhetslogger og brukte PowerShell til å filtrere hendelser. Dette viser sammenhengen mellom forebygging, testing og logging.

Fredag – oppsummering og systematisk feilsøking

Oppsummering – du er administrator

Situasjon: En bruker sier: «Jeg får ikke logget inn på CLIENT01.»

Jeg vil undersøke problemet systematisk før jeg endrer innstillinger:

Nettverk og DNS: Kjør ipconfig /all på klienten. Feil IP- eller DNS-konfigurasjon kan hindre kontakt med domenekontrolleren.

Brukerkonto: Kontroller i Active Directory at kontoen finnes og er aktivert.

Kontolåsing: Kjør Get-ADUser testbruker1 -Properties LockedOut på DC01 for å undersøke om kontoen er låst.

Gruppemedlemskap og Group Policy: Kontroller relevante grupper, GPO-koblinger og gpresult /r.

Sikkerhetslogg: Undersøk relevante hendelser i Event Viewer, for eksempel 4625 og 4740.

Hvorfor denne rekkefølgen? Den hjelper administratoren med å utelukke grunnleggende feil før mer avansert feilsøking, i stedet for å endre tilfeldige innstillinger.

Skjermbilde – feilsøkingsrekkefølge:

📷 Sett inn skjermbilde: Fredag – feilsøking

Sikkerhetskjeden

```text

Bruker

↓

Autentisering

↓

Active Directory

↓

Gruppemedlemskap

↓

Autorisasjon

↓

Group Policy

↓

Logging

`

Bruker: Personen som ønsker tilgang til en datamaskin eller ressurs.

Autentisering: Systemet kontrollerer brukerens identitet, for eksempel ved hjelp av passord.

Active Directory: Katalogtjenesten lagrer og organiserer brukere, datamaskiner og grupper.

Gruppemedlemskap: Grupper gjør det enklere å organisere og styre tilgang.

Autorisasjon: Systemet avgjør hva en autentisert bruker har lov til å gjøre.

Group Policy: Administratoren administrerer felles regler og sikkerhetsinnstillinger.

Logging: Relevante hendelser registreres når riktig auditing er aktivert, slik at de kan undersøkes senere.

Defense in depth betyr sikkerhet i flere lag. Passordpolicy, kontolåsing, grupper, tilgangskontroll, brannmur, GPO og logging beskytter ulike deler av miljøet. Ett tiltak er ikke tilstrekkelig alene.

Når noe ikke virker – fast undersøkelsesrekkefølge

  |   Rekkefølge   |   Kontroll   |   Eksempel   |  

  |  ---  |  ---  |  ---  |  

  |   1   |   Nettverk   |   `ipconfig /all` – IP, nettverksmaske og gateway   |  

  |   2   |   DNS   |   Bruker klienten riktig DNS-server?   |  

  |   3   |   Domenekontakt   |   `nslookup firma.local` og kontroll av kontakt med `DC01`   |  

  |   4   |   Bruker/datamaskin   |   Finnes objektene, er de aktive, og er brukeren låst?   |  

  |   5   |   OU   |   Ligger objektene i riktig OU?   |  

  |   6   |   Gruppe   |   Har brukeren riktig gruppemedlemskap?   |  

  |   7   |   GPO   |   Er policyen koblet til riktig OU, og tillater filtreringen bruk?   |  

  |   8   |   Resultat   |   Hva viser `gpresult /r`?   |  

  |   9   |   Logger   |   Hva viser Event Viewer?   |  

  |   10   |   Dokumentasjon   |   Symptom → hypotese → test → funn → løsning   |  

Eksempel fra laboratoriet: testbruker1 fikk ikke logget inn. Jeg mistenkte kontolåsing, kontrollerte LockedOut, fant True og hendelse 4740, låste opp kontoen med Unlock-ADAccount og bekreftet LockedOut: False.

Etter uka – se sammenhengen

```text

Identitet → Autentisering → Katalogtjeneste → Grupper

→ Minste privilegium → Group Policy

→ Sikkerhetsbaseline → Sikkerhetsinnstillinger

→ Testing → Auditing og logging → Feilsøking

`

Identitet gir brukeren en egen konto. Autentisering bekrefter identiteten. Active Directory organiserer kontoer, datamaskiner og grupper. Grupper og minste privilegium begrenser tilgang. Group Policy og en sikkerhetsbaseline gir felles innstillinger, for eksempel brannmur og automatisk låsing. Testing bekrefter at innstillingene virker. Auditing og logging gjør det mulig å undersøke hendelser. Feilsøking hjelper administratoren med å finne årsaker og dokumentere løsninger.

Skjermbilde – ukas oppsummering:

📷 Sett inn skjermbilde: Fredag – oppsummering

Sluttrefleksjon

Gjennom denne uka har jeg lært at god IT-sikkerhet ikke handler om én enkelt innstilling, men om flere mekanismer som samarbeider.

Jeg har arbeidet med Active Directory, brukere og grupper, minste privilegium, Group Policy, Windows Defender Firewall, Password Policy, Account Lockout Policy, PowerShell og Event Viewer. Jeg har også lært å kontrollere om sikkerhetsinnstillinger fungerer, og å undersøke sikkerhetshendelser på en systematisk måte.

Det viktigste er å forstå hva vi forsøker å beskytte, hvorfor sikkerhetstiltakene brukes, hvordan vi tester dem, og hvordan vi finner årsaken når noe ikke fungerer.
