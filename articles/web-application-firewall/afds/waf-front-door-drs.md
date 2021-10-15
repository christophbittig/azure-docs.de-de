---
title: DRS-Regelgruppen und -Regeln von Azure Web Application Firewall für Azure Front Door
description: Dieser Artikel bietet Informationen zu DRS-Regelgruppen und -Regeln von Web Application Firewall.
ms.service: web-application-firewall
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: 95839937f2356f74d29499bb45e7cd0e1159b02c
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546347"
---
# <a name="web-application-firewall-drs-rule-groups-and-rules"></a>DRS-Regelgruppen und -Regeln von Web Application Firewall

Web Application Firewall (WAF) für Azure Front Door schützt Webanwendungen vor allgemeinen Sicherheitsrisiken und Exploits. Von Azure verwaltete Regelsätze bieten eine einfache Möglichkeit zum Bereitstellen von Schutz vor allgemeinen Sicherheitsbedrohungen. Da Azure diese Regelsätze verwaltet, werden die Regeln zum Schutz vor neuen Angriffssignaturen aktualisiert, wenn dies erforderlich ist.


## <a name="default-rule-sets"></a>Standardregelsätze

Der von Azure verwaltete Standardregelsatz enthält Regeln für die folgenden Bedrohungskategorien:

- Cross-Site-Scripting
- Java-Angriffe
- Local File Inclusion
- PHP Code-Injection
- Remotebefehlsausführung
- Remote File Inclusion
- Session Fixation
- Schutz vor Einschleusung von SQL-Befehlen
- Protokollangreifer

Die Versionsnummer des Standardregelsatzes wird erhöht, wenn dem Regelsatz neue Angriffssignaturen hinzugefügt werden.
Der Standardregelsatz wird standardmäßig im Erkennungsmodus in den WAF-Richtlinien aktiviert. Sie können einzelne Regeln im Standardregelsatz deaktivieren bzw. aktivieren, um die Anforderungen Ihrer Anwendungen zu erfüllen. Sie können pro Regel auch bestimmte Aktionen (ALLOW/BLOCK/REDIRECT/LOG) festlegen.

Manchmal ist es erforderlich, bestimmte Anforderungsattribute in einer WAF-Auswertung wegzulassen. Ein gängiges Beispiel sind von Active Directory eingefügte Token, die für die Authentifizierung verwendet werden. Sie können eine Ausschlussliste für eine verwaltete Regel, eine Regelgruppe oder für den gesamten Regelsatz konfigurieren.  

Die Standardaktion ist „BLOCK“ (Blockieren). Darüber hinaus können in der gleichen WAF-Richtlinie auch benutzerdefinierte Regeln konfiguriert werden, wenn Sie eine der vorkonfigurierten Regeln im Standardregelsatz umgehen möchten.

Benutzerdefinierte Regeln werden immer vor den Regeln im Standardregelsatz ausgewertet. Wenn eine Anforderung einer benutzerdefinierten Regel entspricht, wird die entsprechende Regelaktion angewendet. Die Anforderung wird entweder gesperrt oder an das Back-End weitergeleitet. Es werden keine weiteren benutzerdefinierten Regeln oder Regeln im Standardregelsatz verarbeitet. Der Standardregelsatz kann auch aus WAF-Richtlinien entfernt werden.

### <a name="microsoft-threat-intelligence-collection-rules"></a>Regeln der Microsoft Threat Intelligence-Sammlung

Die Regeln der Microsoft Threat Intelligence-Sammlung werden in Zusammenarbeit mit dem Microsoft Intelligence-Team verfasst, um eine bessere Abdeckung, Patches für bestimmte Sicherheitsrisiken und eine stärkere Reduzierung falsch positiver Ergebnisse zu bieten.

### <a name="anomaly-scoring-mode"></a>Anomaliebewertungsmodus

OWASP kann in zwei Modi entscheiden, ob Datenverkehr blockiert wird: herkömmlicher Modus und Anomaliebewertungsmodus.

Im herkömmlichen Modus wird Datenverkehr, der einer Regel entspricht, unabhängig davon berücksichtigt, ob er mit einer anderen Regel übereinstimmt. Dieser Modus ist leicht verständlich. Doch der Mangel an Informationen darüber, wie viele Regeln mit einer bestimmten Anforderung übereinstimmen, ist eine Einschränkung. Darum wurde der Anomaliebewertungsmodus eingeführt. Er ist die Standardeinstellung für OWASP 3.*x*.

Im Anomaliebewertungsmodus wird Datenverkehr, der einer beliebigen Regel entspricht, nicht sofort blockiert, wenn die Firewall sich im Schutzmodus befindet. Regeln haben einen bestimmten Schweregrad: *Kritisch*, *Fehler*, *Warnung* oder *Hinweis*. Dieser Schweregrad wirkt sich auf einen numerischen Wert für die Anforderung aus, der als „Anomaliebewertung“ bezeichnet wird. So trägt eine Übereinstimmung mit einer *Warnungsregel* mit 3 zum Ergebnis bei. Eine Übereinstimmung mit einer *kritischen* Regel trägt mit 5 zum Ergebnis bei.

|severity  |Wert  |
|---------|---------|
|Kritisch     |5|
|Fehler        |4|
|Warnung      |3|
|Hinweis       |2|

Ab einem Schwellenwert von 5 blockiert die Anomaliebewertung den Datenverkehr. Eine Übereinstimmung mit einer einzelnen *kritischen* Regel reicht also aus, damit WAF auch im Schutzmodus eine Anforderung blockiert. Aber eine Übereinstimmung mit einer *Warnungsregel* setzt die Anomaliebewertung nur um 3 herauf, was allein nicht ausreicht, um den Datenverkehr zu blockieren.

> [!NOTE]
> Textüberprüfung ist nur in DRS 2.0 verfügbar.

### <a name="drs-20"></a>DRS 2.0

DRS 2.0 umfasst 17 Regelgruppen, wie in der folgenden Tabelle gezeigt. Jede dieser Gruppen enthält mehrere Regeln, die deaktivierbar sind.

> [!NOTE]
> DRS 2.0 ist nur in Azure Front Door Premium verfügbar.

|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[Allgemein](#general-20)**|Allgemeine Gruppe|
|**[METHOD-ENFORCEMENT](#drs911-20)**|Sperren von Methoden (PUT, PATCH)|
|**[PROTOCOL-ENFORCEMENT](#drs920-20)**|Schutz vor Protokoll- und Codierungsproblemen|
|**[PROTOCOL-ATTACK](#drs921-20)**|Schutz vor Header Injection, Request Smuggling und Response Splitting|
|**[APPLICATION-ATTACK-LFI](#drs930-20)**|Schutz vor Datei- und Pfadangriffen|
|**[APPLICATION-ATTACK-RFI](#drs931-20)**|Schutz vor RFI-Angriffen (Remote File Inclusion)|
|**[APPLICATION-ATTACK-RCE](#drs932-20)**|Schutz vor der Remoteausführung von Code|
|**[APPLICATION-ATTACK-PHP](#drs933-20)**|Schutz vor Angriffen mit Einschleusung von PHP-Befehlen|
|**[APPLICATION-ATTACK-NodeJS](#drs934-20)**|Schutz vor Node.js-Angriffen|
|**[APPLICATION-ATTACK-XSS](#drs941-20)**|Schutz vor Angriffen durch websiteübergreifendes Skripting|
|**[APPLICATION-ATTACK-SQLI](#drs942-20)**|Schutz vor Angriffen mit Einschleusung von SQL-Befehlen|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-20)**|Schutz vor Session Fixation-Angriffen|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-20)**|Schutz vor Java-Angriffen|
|**[MS-ThreatIntel-WebShells](#drs9905-20)**|Schutz vor Webshell-Angriffen|
|**[MS-ThreatIntel-AppSec](#drs9903-20)**|Schutz vor Angriffen auf die Anwendungssicherheit|
|**[MS-ThreatIntel-SQLI](#drs99031-20)**|Schutz vor Angriffen mit Einschleusung von SQL-Befehlen|
|**[MS-ThreatIntel-CVEs](#drs99001-20)**|Schutz vor CVE-Angriffen|

### <a name="drs-11"></a>DRS 1.1
|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-11)**|Schutz vor Header Injection, Request Smuggling und Response Splitting|
|**[APPLICATION-ATTACK-LFI](#drs930-11)**|Schutz vor Datei- und Pfadangriffen|
|**[APPLICATION-ATTACK-RFI](#drs931-11)**|Schutz vor Remote File Inclusion-Angriffen|
|**[APPLICATION-ATTACK-RCE](#drs932-11)**|Schutz vor Remotebefehlsausführung|
|**[APPLICATION-ATTACK-PHP](#drs933-11)**|Schutz vor Angriffen mit Einschleusung von PHP-Befehlen|
|**[APPLICATION-ATTACK-XSS](#drs941-11)**|Schutz vor Angriffen durch websiteübergreifendes Skripting|
|**[APPLICATION-ATTACK-SQLI](#drs942-11)**|Schutz vor Angriffen mit Einschleusung von SQL-Befehlen|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-11)**|Schutz vor Session Fixation-Angriffen|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-11)**|Schutz vor Java-Angriffen|
|**[MS-ThreatIntel-WebShells](#drs9905-11)**|Schutz vor Webshell-Angriffen|
|**[MS-ThreatIntel-AppSec](#drs9903-11)**|Schutz vor Angriffen auf die Anwendungssicherheit|
|**[MS-ThreatIntel-SQLI](#drs99031-11)**|Schutz vor Angriffen mit Einschleusung von SQL-Befehlen|
|**[MS-ThreatIntel-CVEs](#drs99001-11)**|Schutz vor CVE-Angriffen|

### <a name="drs-10"></a>DRS 1.0

|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[PROTOCOL-ATTACK](#drs921-10)**|Schutz vor Header Injection, Request Smuggling und Response Splitting|
|**[APPLICATION-ATTACK-LFI](#drs930-10)**|Schutz vor Datei- und Pfadangriffen|
|**[APPLICATION-ATTACK-RFI](#drs931-10)**|Schutz vor Remote File Inclusion-Angriffen|
|**[APPLICATION-ATTACK-RCE](#drs932-10)**|Schutz vor Remotebefehlsausführung|
|**[APPLICATION-ATTACK-PHP](#drs933-10)**|Schutz vor Angriffen mit Einschleusung von PHP-Befehlen|
|**[APPLICATION-ATTACK-SQLI](#drs942-10)**|Schutz vor Angriffen mit Einschleusung von SQL-Befehlen|
|**[APPLICATION-ATTACK-SESSION-FIXATION](#drs943-10)**|Schutz vor Session Fixation-Angriffen|
|**[APPLICATION-ATTACK-SESSION-JAVA](#drs944-10)**|Schutz vor Java-Angriffen|



### <a name="bot-rules"></a>Botregeln

|Regelgruppe|BESCHREIBUNG|
|---|---|
|**[BadBots](#bot100)**|Schutz vor bösartigen Bots|
|**[GoodBots](#bot200)**|Identifizieren von gültigen Bots|
|**[UnknownBots](#bot300)**|Identifizieren von unbekannten Bots|



Die folgenden Regelgruppen und Regeln sind bei Verwendung von Web Application Firewall für Azure Front Door verfügbar.

# <a name="drs-20"></a>[DRS 2.0](#tab/drs20)

## <a name="20-rule-sets"></a><a name="drs20"></a> 2.0-Regelsätze

### <a name="general"></a><a name="general-20"></a> Allgemein
|RuleId|BESCHREIBUNG|
|---|---|
|200002|Fehler beim Analysieren des Anforderungstexts|
|200003|Fehler bei der strengen Überprüfung des mehrteiligen Anforderungstexts|


### <a name="method-enforcement"></a><a name="drs911-20"></a> METHOD-ENFORCEMENT
|RuleId|BESCHREIBUNG|
|---|---|
|911100|Methode ist gemäß Richtlinie nicht zulässig|

### <a name="protocol-enforcement"></a><a name="drs920-20"></a> PROTOCOL-ENFORCEMENT
|RuleId|BESCHREIBUNG|
|---|---|
|920100|Ungültige HTTP-Anforderungszeile|
|920120|Versuchte „multipart/form-data“-Umgehung|
|920121|Versuchte „multipart/form-data“-Umgehung|
|920160|Content-Length-HTTP-Header ist nicht numerisch.|
|920170|GET- oder HEAD-Anforderung mit Textinhalt|
|920171|GET- oder HEAD-Anforderung mit Transfer-Encoding.|
|920180|POST-Anforderung mit fehlendem Content-Length-Header|
|920190|Bereich: Ungültiger Wert für letztes Byte|
|920200|Bereich: Zu viele Felder (mehr als 6)|
|920201|Bereich: Zu viele Felder für PDF-Anforderung (35 oder mehr)|
|920210|Mehrere bzw. in Konflikt stehende Verbindungsheaderdaten gefunden|
|920220|Versuchter Missbrauch der URL-Codierung|
|920230|Mehrere URL-Codierungen erkannt|
|920240|Versuchter Missbrauch der URL-Codierung|
|920260|Versuchter Missbrauch: Unicode (volle/halbe Breite)|
|920270|Ungültiges Zeichen in der Anforderung (Zeichen NULL)|
|920271|Ungültiges Zeichen in der Anforderung (nicht druckbare Zeichen)|
|920280|Fehlender Hostheader in Anforderung|
|920290|Leerer Hostheader|
|920300|Fehlender Accept-Header für Anforderung|
|920310|Anforderung hat einen leeren Accept-Header|
|920311|Anforderung hat einen leeren Accept-Header|
|920320|Benutzer-Agent-Header fehlt|
|920330|Leerer Benutzer-Agent-Header|
|920340|Anforderung enthält Inhalt, aber keinen Content-Type-Header|
|920341|Anforderung mit Inhalt erfordert Content-Type-Header|
|920350|Hostheader ist eine numerische IP-Adresse|
|920420|Anforderungsinhaltstyp ist gemäß Richtlinie nicht zulässig|
|920430|HTTP-Protokollversion ist gemäß Richtlinie nicht zulässig|
|920440|URL-Dateierweiterung wird durch Richtlinie eingeschränkt|
|920450|HTTP-Header ist durch Richtlinie eingeschränkt|
|920470|Illegaler Content-Type-Header|
|920480|Zeichensatz für Anforderungsinhaltstyp ist gemäß Richtlinie nicht zulässig|

### <a name="protocol-attack"></a><a name="drs921-20"></a> PROTOCOL-ATTACK

|RuleId|BESCHREIBUNG|
|---|---|
|921110|HTTP Request Smuggling-Angriff|
|921120|HTTP Response Splitting-Angriff|
|921130|HTTP Response Splitting-Angriff|
|921140|HTTP Header Injection-Angriff über Header|
|921150|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921151|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921160|HTTP Header Injection-Angriff über Nutzlast (CR/LF und Headername erkannt)|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-20"></a> LFI – Local File Inclusion
|RuleId|BESCHREIBUNG|
|---|---|
|930100|Path Traversal-Angriff (/../)|
|930110|Path Traversal-Angriff (/../)|
|930120|Zugriffsversuch auf Betriebssystemdatei|
|930130|Zugriffsversuch auf Datei mit eingeschränktem Zugriff|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-20"></a> RFI – Remote File Inclusion
|RuleId|BESCHREIBUNG|
|---|---|
|931100|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung von IP-Adresse für URL-Parameter|
|931110|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung eines häufigen und für RFI anfälligen Parameternamens mit URL-Nutzlast|
|931120|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung von URL-Nutzlast mit nachgestelltem Fragezeichen (?)|
|931130|Möglicher RFI-Angriff (Remote File Inclusion): Domänenexterner Verweis/Link|

### <a name="rce---remote-command-execution"></a><a name="drs932-20"></a> RCE – Remote Command Execution (Remotebefehlsausführung)
|RuleId|BESCHREIBUNG|
|---|---|
|932100|Remotebefehlsausführung: Einschleusung von Unix-Befehl|
|932105|Remotebefehlsausführung: Einschleusung von Unix-Befehl|
|932110|Remotebefehlsausführung: Einschleusung von Windows-Befehl|
|932115|Remotebefehlsausführung: Einschleusung von Windows-Befehl|
|932120|Remotebefehlsausführung: Windows PowerShell-Befehl gefunden|
|932130|Remotebefehlsausführung: Unix Shell-Ausdruck gefunden|
|932140|Remotebefehlsausführung: Windows-FOR/IF-Befehl gefunden|
|932150|Remotebefehlsausführung: Direkte Ausführung von UNIX-Befehlen|
|932160|Remotebefehlsausführung: Unix Shell-Code gefunden|
|932170|Remotebefehlsausführung: Shellshock (CVE-2014-6271)|
|932171|Remotebefehlsausführung: Shellshock (CVE-2014-6271)|
|932180|Eingeschränkter Dateiuploadversuch|

### <a name="php-attacks"></a><a name="drs933-20"></a> PHP-Angriffe
|RuleId|BESCHREIBUNG|
|---|---|
|933100|Angriff mit PHP-Einschleusung: Öffnendes/schließendes Tag gefunden|
|933110|Angriff mit PHP-Einschleusung: PHP-Skriptdateiupload gefunden|
|933120|PHP Injection-Angriff: Konfigurationsanweisung gefunden|
|933130|Angriff mit PHP-Einschleusung: Variable gefunden|
|933131|Angriff mit PHP-Einschleusung: Variable gefunden|
|933140|Angriff mit PHP-Einschleusung: E/A-Datenstrom gefunden|
|933150|Angriff mit PHP-Einschleusung: PHP-Funktionsname mit hohem Risikofaktor gefunden|
|933151|Angriff mit PHP-Einschleusung: PHP-Funktionsname mit mittlerem Risikofaktor gefunden|
|933160|Angriff mit PHP-Einschleusung: PHP-Funktionsaufruf mit hohem Risikofaktor gefunden|
|933161|Angriff mit PHP-Einschleusung: PHP-Funktionsaufruf mit niedrigem Wert gefunden|
|933170|Angriff mit PHP-Einschleusung: Einschleusung von serialisiertem Objekt|
|933180|Angriff mit PHP-Einschleusung: Aufruf einer Variablenfunktion gefunden|
|933200|Angriff mit PHP-Einschleusung: Wrapperschema erkannt|
|933210|PHP Injection-Angriff: Aufruf einer Variablenfunktion gefunden|

### <a name="node-js-attacks"></a><a name="drs934-20"></a> Node JS-Angriffe
|RuleId|BESCHREIBUNG|
|---|---|
|934100|Angriff mit Einschleusung von Node.js-Befehlen|

### <a name="xss---cross-site-scripting"></a><a name="drs941-20"></a> XSS – Cross-Site-Scripting
|RuleId|BESCHREIBUNG|
|---|---|
|941100|XSS-Angriff per libinjection erkannt|
|941101|XSS-Angriff per libinjection erkannt|
|941110|XSS-Filter – Kategorie 1: Skripttagvektor|
|941120|XSS-Filter – Kategorie 2: Ereignishandlervektor|
|941130|XSS-Filter – Kategorie 3: Attributvektor|
|941140|XSS-Filter – Kategorie 4: JavaScript-URI-Vektor|
|941150|XSS-Filter – Kategorie 5: Unzulässige HTML-Attribute|
|941160|NoScript XSS InjectionChecker: HTML-Einschleusung|
|941170|NoScript XSS InjectionChecker: Attributeinschleusung|
|941180|Schlüsselwörter von Sperrliste für Knotenvalidierung|
|941190|XSS mit Stylesheets|
|941200|XSS mit VML-Frames|
|941210|XSS mit verschleiertem JavaScript|
|941220|XSS mit verschleiertem VBScript|
|941230|XSS mit Tag 'embed'|
|941240|XSS mit Attribut 'import' oder 'implementation'|
|941250|IE-XSS-Filter – Angriff erkannt|
|941260|XSS mit Tag 'meta'|
|941270|XSS mit Href 'link'|
|941280|XSS mit Tag 'base'|
|941290|XSS mit Tag 'applet'|
|941300|XSS mit Tag 'object'|
|941310|Falsch formatierte US-ASCII-Codierung für XSS-Filter – Angriff erkannt|
|941320|Möglicher XSS-Angriff erkannt – HTML-Taghandler|
|941330|IE-XSS-Filter – Angriff erkannt|
|941340|IE-XSS-Filter – Angriff erkannt|
|941350|UTF-7-Codierung – IE XSS – Angriff erkannt|
|941360|JavaScript-Obfuskation erkannt|
|941370|Globale JavaScript-Variable gefunden|
|941380|Einschleusung clientseitiger AngularJS-Vorlagen erkannt|

>[!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Blacklist*, der von Microsoft nicht mehr verwendet wird. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.


### <a name="sqli---sql-injection"></a><a name="drs942-20"></a> SQLI – Einschleusung von SQL-Befehlen
|RuleId|BESCHREIBUNG|
|---|---|
|942100|SQL Injection-Angriff per libinjection erkannt|
|942110|Angriff mit Einschleusung von SQL-Befehlen: Allgemeine Tests auf Einschleusung von SQL-Befehlen erkannt|
|942120|Angriff mit Einschleusung von SQL-Befehlen: SQL-Operator ermittelt|
|942130|Angriff mit Einschleusung von SQL-Befehlen: SQL-Tautologie erkannt.|
|942140|Angriff mit Einschleusung von SQL-Befehlen: Häufige Datenbanknamen erkannt|
|942150|Angriff mit Einschleusung von SQL-Befehlen|
|942160|Erkennt Blind SQLI-Tests per sleep() oder benchmark().|
|942170|Erkennt SQL Benchmark- und Sleep Injection-Angriffsversuche, einschließlich bedingter Abfragen.|
|942180|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung 1/3|
|942190|Erkennt die Ausführung von MSSQL-Code und Versuche, Informationen auszulesen|
|942200|Erkennt durch MySQL-Kommentare oder -Leerzeichen verschleierte Einschleusungen und die Terminierung per Backtick|
|942210|Erkennt verkette Angriffsversuche SQL-Einschleusung 1/2|
|942220|Suche nach Ganzzahlüberlauf-Angriffen. Diese werden aus Skipfish übernommen, mit Ausnahme von 3.0.00738585072007e-308 (Absturz wegen ungültiger „magischer Zahl“).|
|942230|Erkennt Angriffsversuche mit bedingter SQL-Einschleusung.|
|942240|Erkennt MySQL-Zeichensatzwechsel und MSSQL-DoS-Angriffsversuche|
|942250|Erkennt MATCH AGAINST-, MERGE- und EXECUTE IMMEDIATE-Einschleusungen|
|942260|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 2/3|
|942270|Suche nach grundlegender SQL-Einschleusung. Häufig verwendete Angriffszeichenfolge für MySQL, Oracle und andere|
|942280|Erkennt Postgres pg_sleep-Einschleusung, WAITFOR DELAY-Angriffe und Versuche des Herunterfahrens von Datenbanken|
|942290|Ermittelt Angriffsversuche mit grundlegender MongoDB SQL-Einschleusung.|
|942300|Erkennt MySQL-Kommentare, Bedingungen und Einschleusungen von „ch(ar)“|
|942310|Erkennt verkette Angriffsversuche SQL-Einschleusung 2/2.|
|942320|Erkennt Einschleusungen von gespeicherten Prozeduren/Funktionen für MySQL und PostgreSQL.|
|942330|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen, 1/2|
|942340|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 3/3|
|942350|Erkennt MySQL-UDF-Einschleusung und andere Versuche der Manipulation von Daten bzw. der Struktur.|
|942360|Erkennt Versuche des Verkettens einfacher eingeschleuster SQL-Befehle und von SQL/LFI|
|942361|Erkennt grundlegende SQL-Einschleusung basierend auf Schlüsselwort „Alter“ oder „Union“|
|942370|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen, 2/2|
|942380|Angriff mit Einschleusung von SQL-Befehlen|
|942390|Angriff mit Einschleusung von SQL-Befehlen|
|942400|Angriff mit Einschleusung von SQL-Befehlen|
|942410|Angriff mit Einschleusung von SQL-Befehlen|
|942430|Eingeschränkte Anomalieerkennung für SQL-Zeichen (Argumente): Anzahl von Sonderzeichen überschritten (12)|
|942440|SQL-Kommentarsequenz erkannt|
|942450|Hexadezimale SQL-Codierung identifiziert|
|942460|Warnung: Erkennung einer Metazeichenanomalie – Wiederholte Non-Word-Zeichen|
|942470|Angriff mit Einschleusung von SQL-Befehlen|
|942480|Angriff mit Einschleusung von SQL-Befehlen|
|942500|MySQL-Inlinekommentar erkannt|
|942510|Versuch der Umgehung der Einschleusung von SQL-Befehlen durch Ticks und Backticks erkannt|


### <a name="session-fixation"></a><a name="drs943-20"></a> SESSION-FIXATION
|RuleId|BESCHREIBUNG|
|---|---|
|943100|Möglicher Session Fixation-Angriff: Festlegung von Cookiewerten in HTML|
|943110|Möglicher Session Fixation-Angriff: SessionID-Parametername mit domänenexternem Referrer|
|943120|Möglicher Session Fixation-Angriff: SessionID-Parametername ohne Referrer|

### <a name="java-attacks"></a><a name="drs944-20"></a> JAVA-Angriffe
|RuleId|BESCHREIBUNG|
|---|---|
|944100|Remotebefehlsausführung: Apache Struts, Oracle WebLogic|
|944110|Erkennt potenzielle Payloadausführung|
|944120|Mögliche Nutzlastausführung und Remotebefehlsausführung|
|944130|Verdächtige Java-Klassen|
|944200|Ausnutzung der Java-Deserialisierung (Apache Commons)|
|944210|Mögliche Verwendung von Java-Serialisierung|
|944240|Remotebefehlsausführung: Java-Serialisierung|
|944250|Remotebefehlsausführung: Verdächtige Java-Methode erkannt|

### <a name="ms-threatintel-webshells"></a><a name="drs9905-20"></a> MS-ThreatIntel-WebShells
|RuleId|BESCHREIBUNG|
|---|---|
|99005002|Webshell-Interaktionsversuch (POST)|
|99005003|Webshell-Uploadversuch (POST) – CHOPPER PHP|
|99005004|Webshell-Uploadversuch (POST) – CHOPPER ASPX|

### <a name="ms-threatintel-appsec"></a><a name="drs9903-20"></a> MS-ThreatIntel-AppSec
|RuleId|BESCHREIBUNG|
|---|---|
|99030001|Path Traversal-Umgehung in Headern (/.././../)|
|99030002|Path Traversal-Umgehung in Anforderungstext (/.././../)|

### <a name="ms-threatintel-sqli"></a><a name="drs99031-20"></a> MS-ThreatIntel-SQLI
|RuleId|BESCHREIBUNG|
|---|---|
|99031001|Angriff mit Einschleusung von SQL-Befehlen: Allgemeine Tests auf Einschleusung von SQL-Befehlen erkannt|
|99031002|SQL-Kommentarsequenz erkannt|

### <a name="ms-threatintel-cves"></a><a name="drs99001-20"></a> MS-ThreatIntel-CVEs
|RuleId|BESCHREIBUNG|
|---|---|
|99001001|Versuch der Ausnutzung der F5 TMUI-REST-API-Schwachstelle (CVE-2020-5902) mit bekannten Anmeldeinformationen|

# <a name="drs-11"></a>[DRS 1.1](#tab/drs11)

## <a name="11-rule-sets"></a><a name="drs11"></a> 1.1-Regelsätze

### <a name="protocol-attack"></a><a name="drs921-11"></a> PROTOCOL-ATTACK
|RuleId|BESCHREIBUNG|
|---|---|
|921110|HTTP Request Smuggling-Angriff|
|921120|HTTP Response Splitting-Angriff|
|921130|HTTP Response Splitting-Angriff|
|921140|HTTP Header Injection-Angriff über Header|
|921150|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921151|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921160|HTTP Header Injection-Angriff über Nutzlast (CR/LF und Headername erkannt)|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-11"></a> LFI – Local File Inclusion
|RuleId|BESCHREIBUNG|
|---|---|
|930100|Path Traversal-Angriff (/../)|
|930110|Path Traversal-Angriff (/../)|
|930120|Zugriffsversuch auf Betriebssystemdatei|
|930130|Zugriffsversuch auf Datei mit eingeschränktem Zugriff|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-11"></a> RFI – Remote File Inclusion
|RuleId|BESCHREIBUNG|
|---|---|
|931100|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung von IP-Adresse für URL-Parameter|
|931110|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung eines häufigen und für RFI anfälligen Parameternamens mit URL-Nutzlast|
|931120|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung von URL-Nutzlast mit nachgestelltem Fragezeichen (?)|
|931130|Möglicher RFI-Angriff (Remote File Inclusion): Domänenexterner Verweis/Link|

### <a name="rce---remote-command-execution"></a><a name="drs932-11"></a> RCE – Remote Command Execution (Remotebefehlsausführung)
|RuleId|BESCHREIBUNG|
|---|---|
|932100|Remotebefehlsausführung: Einschleusung von Unix-Befehl|
|932105|Remotebefehlsausführung: Einschleusung von Unix-Befehl|
|932110|Remotebefehlsausführung: Einschleusung von Windows-Befehl|
|932115|Remotebefehlsausführung: Einschleusung von Windows-Befehl|
|931120|Remotebefehlsausführung: Windows PowerShell-Befehl gefunden|
|932130|Remotebefehlsausführung: Unix Shell-Ausdruck gefunden|
|932140|Remotebefehlsausführung: Windows-FOR/IF-Befehl gefunden|
|932150|Remotebefehlsausführung: Direkte Ausführung von UNIX-Befehlen|
|932160|Remotebefehlsausführung: Shellshock (CVE-2014-6271)|
|932170|Remotebefehlsausführung: Shellshock (CVE-2014-6271)|
|932171|Remotebefehlsausführung: Shellshock (CVE-2014-6271)|
|932180|Eingeschränkter Dateiuploadversuch|

### <a name="php-attacks"></a><a name="drs933-11"></a> PHP-Angriffe
|RuleId|BESCHREIBUNG|
|---|---|
|933100|Angriff mit PHP-Einschleusung: öffnendes PHP-Tag gefunden|
|933110|Angriff mit PHP-Einschleusung: PHP-Skriptdateiupload gefunden|
|933120|PHP Injection-Angriff: Konfigurationsanweisung gefunden|
|933130|Angriff mit PHP-Einschleusung: Variable gefunden|
|933140|Angriff mit PHP-Einschleusung: E/A-Datenstrom gefunden|
|933150|Angriff mit PHP-Einschleusung: PHP-Funktionsname mit hohem Risikofaktor gefunden|
|933151|Angriff mit PHP-Einschleusung: PHP-Funktionsname mit mittlerem Risikofaktor gefunden|
|933160|Angriff mit PHP-Einschleusung: PHP-Funktionsaufruf mit hohem Risikofaktor gefunden|
|933170|Angriff mit PHP-Einschleusung: Einschleusung von serialisiertem Objekt|
|933180|PHP Injection-Angriff: Aufruf einer Variablenfunktion gefunden|

### <a name="xss---cross-site-scripting"></a><a name="drs941-11"></a> XSS – Cross-Site-Scripting
|RuleId|BESCHREIBUNG|
|---|---|
|941100|XSS-Angriff per libinjection erkannt|
|941101|XSS-Angriff per libinjection erkannt|
|941110|XSS-Filter – Kategorie 1: Skripttagvektor|
|941120|XSS-Filter – Kategorie 2: Ereignishandlervektor|
|941130|XSS-Filter – Kategorie 3: Attributvektor|
|941140|XSS-Filter – Kategorie 4: JavaScript-URI-Vektor|
|941150|XSS-Filter – Kategorie 5: Unzulässige HTML-Attribute|
|941160|NoScript XSS InjectionChecker: HTML-Einschleusung|
|941170|NoScript XSS InjectionChecker: Attributeinschleusung|
|941180|Schlüsselwörter von Sperrliste für Knotenvalidierung|
|941190|IE-XSS-Filter – Angriff erkannt|
|941200|IE-XSS-Filter – Angriff erkannt|
|941210|IE-XSS-Filter – Angriff erkannt|
|941220|IE-XSS-Filter – Angriff erkannt|
|941230|IE-XSS-Filter – Angriff erkannt|
|941240|IE-XSS-Filter – Angriff erkannt|
|941250|IE-XSS-Filter – Angriff erkannt|
|941260|IE-XSS-Filter – Angriff erkannt|
|941270|IE-XSS-Filter – Angriff erkannt|
|941280|IE-XSS-Filter – Angriff erkannt|
|941290|IE-XSS-Filter – Angriff erkannt|
|941300|IE-XSS-Filter – Angriff erkannt|
|941310|Falsch formatierte US-ASCII-Codierung für XSS-Filter – Angriff erkannt|
|941320|Möglicher XSS-Angriff erkannt – HTML-Taghandler|
|941330|IE-XSS-Filter – Angriff erkannt|
|941340|IE-XSS-Filter – Angriff erkannt|
|941350|UTF-7-Codierung – IE XSS – Angriff erkannt|

>[!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Blacklist*, der von Microsoft nicht mehr verwendet wird. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

### <a name="sqli---sql-injection"></a><a name="drs942-11"></a> SQLI – Einschleusung von SQL-Befehlen
|RuleId|BESCHREIBUNG|
|---|---|
|942100|SQL Injection-Angriff per libinjection erkannt|
|942110|Angriff mit Einschleusung von SQL-Befehlen: Allgemeine Tests auf Einschleusung von SQL-Befehlen erkannt|
|942120|Angriff mit Einschleusung von SQL-Befehlen: SQL-Operator ermittelt|
|942140|Angriff mit Einschleusung von SQL-Befehlen: Häufige Datenbanknamen erkannt|
|942150|Angriff mit Einschleusung von SQL-Befehlen|
|942160|Erkennt Blind SQLI-Tests per sleep() oder benchmark().|
|942170|Erkennt SQL Benchmark- und Sleep Injection-Angriffsversuche, einschließlich bedingter Abfragen.|
|942180|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung 1/3|
|942190|Erkennt die Ausführung von MSSQL-Code und Versuche, Informationen auszulesen|
|942200|Erkennt durch MySQL-Kommentare oder -Leerzeichen verschleierte Einschleusungen und die Terminierung per Backtick|
|942210|Erkennt verkette Angriffsversuche SQL-Einschleusung 1/2|
|942220|Suche nach Ganzzahlüberlauf-Angriffen. Diese werden aus Skipfish übernommen, mit Ausnahme von 3.0.00738585072007e-308 (Absturz wegen ungültiger „magischer Zahl“).|
|942230|Erkennt Angriffsversuche mit bedingter SQL-Einschleusung.|
|942240|Erkennt MySQL-Zeichensatzwechsel und MSSQL-DoS-Angriffsversuche|
|942250|Erkennt MATCH AGAINST-, MERGE- und EXECUTE IMMEDIATE-Einschleusungen|
|942260|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 2/3|
|942270|Suche nach grundlegender SQL-Einschleusung. Häufig verwendete Angriffszeichenfolge für MySQL, Oracle und andere|
|942280|Erkennt Postgres pg_sleep-Einschleusung, WAITFOR DELAY-Angriffe und Versuche des Herunterfahrens von Datenbanken|
|942290|Ermittelt Angriffsversuche mit grundlegender MongoDB SQL-Einschleusung.|
|942300|Erkennt MySQL-Kommentare, Bedingungen und Einschleusungen von „ch(ar)“|
|942310|Erkennt verkette Angriffsversuche SQL-Einschleusung 2/2.|
|942320|Erkennt Einschleusungen von gespeicherten Prozeduren/Funktionen für MySQL und PostgreSQL.|
|942330|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen 1/3|
|942340|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 3/3|
|942350|Erkennt MySQL-UDF-Einschleusung und andere Versuche der Manipulation von Daten bzw. der Struktur.|
|942360|Erkennt Versuche des Verkettens einfacher eingeschleuster SQL-Befehle und von SQL/LFI|
|942361|Erkennt grundlegende SQL-Einschleusung basierend auf Schlüsselwort „Alter“ oder „Union“|
|942370|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen 2/3|
|942380|Angriff mit Einschleusung von SQL-Befehlen|
|942390|Angriff mit Einschleusung von SQL-Befehlen|
|942400|Angriff mit Einschleusung von SQL-Befehlen|
|942410|Angriff mit Einschleusung von SQL-Befehlen|
|942430|Eingeschränkte Anomalieerkennung für SQL-Zeichen (Argumente): Anzahl von Sonderzeichen überschritten (12)|
|942440|SQL-Kommentarsequenz erkannt|
|942450|Hexadezimale SQL-Codierung identifiziert|
|942470|Angriff mit Einschleusung von SQL-Befehlen|
|942480|Angriff mit Einschleusung von SQL-Befehlen|

### <a name="session-fixation"></a><a name="drs943-11"></a> SESSION-FIXATION
|RuleId|BESCHREIBUNG|
|---|---|
|943100|Möglicher Session Fixation-Angriff: Festlegung von Cookiewerten in HTML|
|943110|Möglicher Session Fixation-Angriff: SessionID-Parametername mit domänenexternem Referrer|
|943120|Möglicher Session Fixation-Angriff: SessionID-Parametername ohne Referrer|

### <a name="java-attacks"></a><a name="drs944-11"></a> JAVA-Angriffe
|RuleId|BESCHREIBUNG|
|---|---|
|944100|Remotebefehlsausführung: Verdächtige Java-Klasse erkannt|
|944110|Möglicher Session Fixation-Angriff: Festlegung von Cookiewerten in HTML|
|944120|Remotebefehlsausführung: Java-Serialisierung (CVE-2015-5842)|
|944130|Verdächtige Java-Klasse erkannt|
|944200|Magische Bytes erkannt, wahrscheinlich wird Java-Serialisierung verwendet|
|944210|Magische Bytes erkannt, Base64-Codierung, wahrscheinlich wird Java-Serialisierung verwendet|
|944240|Remotebefehlsausführung: Java-Serialisierung (CVE-2015-5842)|
|944250|Remotebefehlsausführung: Verdächtige Java-Methode erkannt|

### <a name="ms-threatintel-webshells"></a><a name="drs9905-11"></a> MS-ThreatIntel-WebShells
|RuleId|BESCHREIBUNG|
|---|---|
|99005002|Webshell-Interaktionsversuch (POST)|
|99005003|Webshell-Uploadversuch (POST) – CHOPPER PHP|
|99005004|Webshell-Uploadversuch (POST) – CHOPPER ASPX|

### <a name="ms-threatintel-appsec"></a><a name="drs9903-11"></a> MS-ThreatIntel-AppSec
|RuleId|BESCHREIBUNG|
|---|---|
|99030001|Path Traversal-Umgehung in Headern (/.././../)|
|99030002|Path Traversal-Umgehung in Anforderungstext (/.././../)|

### <a name="ms-threatintel-sqli"></a><a name="drs99031-11"></a> MS-ThreatIntel-SQLI
|RuleId|BESCHREIBUNG|
|---|---|
|99031001|Angriff mit Einschleusung von SQL-Befehlen: Allgemeine Tests auf Einschleusung von SQL-Befehlen erkannt|
|99031002|SQL-Kommentarsequenz erkannt|

### <a name="ms-threatintel-cves"></a><a name="drs99001-11"></a> MS-ThreatIntel-CVEs
|RuleId|BESCHREIBUNG|
|---|---|
|99001001|Versuch der Ausnutzung der F5 TMUI-REST-API-Schwachstelle (CVE-2020-5902) mit bekannten Anmeldeinformationen|

# <a name="drs-10"></a>[DRS 1.0](#tab/drs10)

## <a name="10-rule-sets"></a><a name="drs10"></a> 1.0-Regelsätze

### <a name="protocol-attack"></a><a name="drs921-10"></a> PROTOCOL-ATTACK
|RuleId|BESCHREIBUNG|
|---|---|
|921110|HTTP Request Smuggling-Angriff|
|921120|HTTP Response Splitting-Angriff|
|921130|HTTP Response Splitting-Angriff|
|921140|HTTP Header Injection-Angriff über Header|
|921150|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921151|HTTP Header Injection-Angriff über Nutzlast (CR/LF erkannt)|
|921160|HTTP Header Injection-Angriff über Nutzlast (CR/LF und Headername erkannt)|

### <a name="lfi---local-file-inclusion"></a><a name="drs930-10"></a> LFI – Local File Inclusion
|RuleId|BESCHREIBUNG|
|---|---|
|930100|Path Traversal-Angriff (/../)|
|930110|Path Traversal-Angriff (/../)|
|930120|Zugriffsversuch auf Betriebssystemdatei|
|930130|Zugriffsversuch auf Datei mit eingeschränktem Zugriff|

### <a name="rfi---remote-file-inclusion"></a><a name="drs931-10"></a> RFI – Remote File Inclusion
|RuleId|BESCHREIBUNG|
|---|---|
|931100|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung von IP-Adresse für URL-Parameter|
|931110|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung eines häufigen und für RFI anfälligen Parameternamens mit URL-Nutzlast|
|931120|Möglicher RFI-Angriff (Remote File Inclusion): Verwendung von URL-Nutzlast mit nachgestelltem Fragezeichen (?)|
|931130|Möglicher RFI-Angriff (Remote File Inclusion): Domänenexterner Verweis/Link|

### <a name="rce---remote-command-execution"></a><a name="drs932-10"></a> RCE – Remote Command Execution (Remotebefehlsausführung)
|RuleId|BESCHREIBUNG|
|---|---|
|932100|Remotebefehlsausführung: Einschleusung von Unix-Befehl|
|932105|Remotebefehlsausführung: Einschleusung von Unix-Befehl|
|932110|Remotebefehlsausführung: Einschleusung von Windows-Befehl|
|932115|Remotebefehlsausführung: Einschleusung von Windows-Befehl|
|932120|Remotebefehlsausführung: Windows PowerShell-Befehl gefunden|
|932130|Remotebefehlsausführung: Unix Shell-Ausdruck gefunden|
|932140|Remotebefehlsausführung: Windows-FOR/IF-Befehl gefunden|
|932150|Remotebefehlsausführung: Direkte Ausführung von UNIX-Befehlen|
|932160|Remotebefehlsausführung: Unix Shell-Code gefunden|
|932170|Remotebefehlsausführung: Shellshock (CVE-2014-6271)|
|932171|Remotebefehlsausführung: Shellshock (CVE-2014-6271)|
|932180|Eingeschränkter Dateiuploadversuch|

### <a name="php-attacks"></a><a name="drs933-10"></a> PHP-Angriffe
|RuleId|BESCHREIBUNG|
|---|---|
|933100|Angriff mit PHP-Einschleusung: Öffnendes/schließendes Tag gefunden|
|933110|Angriff mit PHP-Einschleusung: PHP-Skriptdateiupload gefunden|
|933120|PHP Injection-Angriff: Konfigurationsanweisung gefunden|
|933130|Angriff mit PHP-Einschleusung: Variable gefunden|
|933131|Angriff mit PHP-Einschleusung: Variable gefunden|
|933140|Angriff mit PHP-Einschleusung: E/A-Datenstrom gefunden|
|933150|Angriff mit PHP-Einschleusung: PHP-Funktionsname mit hohem Risikofaktor gefunden|
|933151|Angriff mit PHP-Einschleusung: PHP-Funktionsname mit mittlerem Risikofaktor gefunden|
|933160|Angriff mit PHP-Einschleusung: PHP-Funktionsaufruf mit hohem Risikofaktor gefunden|
|933161|Angriff mit PHP-Einschleusung: PHP-Funktionsaufruf mit niedrigem Wert gefunden|
|933170|Angriff mit PHP-Einschleusung: Einschleusung von serialisiertem Objekt|
|933180|PHP Injection-Angriff: Aufruf einer Variablenfunktion gefunden|

### <a name="xss---cross-site-scripting"></a><a name="drs941-10"></a> XSS – Cross-Site-Scripting
|RuleId|BESCHREIBUNG|
|---|---|
|941100|XSS-Angriff per libinjection erkannt|
|941101|XSS-Angriff per libinjection erkannt|
|941110|XSS-Filter – Kategorie 1: Skripttagvektor|
|941120|XSS-Filter – Kategorie 2: Ereignishandlervektor|
|941130|XSS-Filter – Kategorie 3: Attributvektor|
|941140|XSS-Filter – Kategorie 4: JavaScript-URI-Vektor|
|941150|XSS-Filter – Kategorie 5: Unzulässige HTML-Attribute|
|941160|NoScript XSS InjectionChecker: HTML-Einschleusung|
|941170|NoScript XSS InjectionChecker: Attributeinschleusung|
|941180|Schlüsselwörter von Sperrliste für Knotenvalidierung|
|941190|XSS mit Stylesheets|
|941200|XSS mit VML-Frames|
|941210|XSS mit verschleiertem JavaScript|
|941220|XSS mit verschleiertem VBScript|
|941230|XSS mit Tag 'embed'|
|941240|XSS mit Attribut 'import' oder 'implementation'|
|941250|IE-XSS-Filter – Angriff erkannt|
|941260|XSS mit Tag 'meta'|
|941270|XSS mit Href 'link'|
|941280|XSS mit Tag 'base'|
|941290|XSS mit Tag 'applet'|
|941300|XSS mit Tag 'object'|
|941310|Falsch formatierte US-ASCII-Codierung für XSS-Filter – Angriff erkannt|
|941320|Möglicher XSS-Angriff erkannt – HTML-Taghandler|
|941330|IE-XSS-Filter – Angriff erkannt|
|941340|IE-XSS-Filter – Angriff erkannt|
|941350|UTF-7-Codierung – IE XSS – Angriff erkannt|

>[!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Blacklist*, der von Microsoft nicht mehr verwendet wird. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

### <a name="sqli---sql-injection"></a><a name="drs942-10"></a> SQLI – Einschleusung von SQL-Befehlen
|RuleId|BESCHREIBUNG|
|---|---|
|942100|SQL Injection-Angriff per libinjection erkannt|
|942110|Angriff mit Einschleusung von SQL-Befehlen: Allgemeine Tests auf Einschleusung von SQL-Befehlen erkannt|
|942120|Angriff mit Einschleusung von SQL-Befehlen: SQL-Operator ermittelt|
|942140|Angriff mit Einschleusung von SQL-Befehlen: Häufige Datenbanknamen erkannt|
|942150|Angriff mit Einschleusung von SQL-Befehlen|
|942160|Erkennt Blind SQLI-Tests per sleep() oder benchmark().|
|942170|Erkennt SQL Benchmark- und Sleep Injection-Angriffsversuche, einschließlich bedingter Abfragen.|
|942180|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung 1/3|
|942190|Erkennt die Ausführung von MSSQL-Code und Versuche, Informationen auszulesen|
|942200|Erkennt durch MySQL-Kommentare oder -Leerzeichen verschleierte Einschleusungen und die Terminierung per Backtick|
|942210|Erkennt verkette Angriffsversuche SQL-Einschleusung 1/2|
|942220|Suche nach Ganzzahlüberlauf-Angriffen. Diese werden aus Skipfish übernommen, mit Ausnahme von 3.0.00738585072007e-308 (Absturz wegen ungültiger „magischer Zahl“).|
|942230|Erkennt Angriffsversuche mit bedingter SQL-Einschleusung.|
|942240|Erkennt MySQL-Zeichensatzwechsel und MSSQL-DoS-Angriffsversuche|
|942250|Erkennt MATCH AGAINST-, MERGE- und EXECUTE IMMEDIATE-Einschleusungen|
|942260|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 2/3|
|942270|Suche nach grundlegender SQL-Einschleusung. Häufig verwendete Angriffszeichenfolge für MySQL, Oracle und andere|
|942280|Erkennt Postgres pg_sleep-Einschleusung, WAITFOR DELAY-Angriffe und Versuche des Herunterfahrens von Datenbanken|
|942290|Ermittelt Angriffsversuche mit grundlegender MongoDB SQL-Einschleusung.|
|942300|Erkennt MySQL-Kommentare, Bedingungen und Einschleusungen von „ch(ar)“|
|942310|Erkennt verkette Angriffsversuche SQL-Einschleusung 2/2.|
|942320|Erkennt Einschleusungen von gespeicherten Prozeduren/Funktionen für MySQL und PostgreSQL.|
|942330|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen, 1/2|
|942340|Erkennt Versuche der Umgehung der einfachen SQL-Authentifizierung, 3/3|
|942350|Erkennt MySQL-UDF-Einschleusung und andere Versuche der Manipulation von Daten bzw. der Struktur.|
|942360|Erkennt Versuche des Verkettens einfacher eingeschleuster SQL-Befehle und von SQL/LFI|
|942361|Erkennt grundlegende SQL-Einschleusung basierend auf Schlüsselwort „Alter“ oder „Union“|
|942370|Erkennt Probings von klassischen Einschleusungen von SQL-Befehlen, 2/2|
|942380|Angriff mit Einschleusung von SQL-Befehlen|
|942390|Angriff mit Einschleusung von SQL-Befehlen|
|942400|Angriff mit Einschleusung von SQL-Befehlen|
|942410|Angriff mit Einschleusung von SQL-Befehlen|
|942430|Eingeschränkte Anomalieerkennung für SQL-Zeichen (Argumente): Anzahl von Sonderzeichen überschritten (12)|
|942440|SQL-Kommentarsequenz erkannt|
|942450|Hexadezimale SQL-Codierung identifiziert|
|942470|Angriff mit Einschleusung von SQL-Befehlen|
|942480|Angriff mit Einschleusung von SQL-Befehlen|

### <a name="session-fixation"></a><a name="drs943-10"></a> SESSION-FIXATION
|RuleId|BESCHREIBUNG|
|---|---|
|943100|Möglicher Session Fixation-Angriff: Festlegung von Cookiewerten in HTML|
|943110|Möglicher Session Fixation-Angriff: SessionID-Parametername mit domänenexternem Referrer|
|943120|Möglicher Session Fixation-Angriff: SessionID-Parametername ohne Referrer|

### <a name="java-attacks"></a><a name="drs944-10"></a> JAVA-Angriffe
|RuleId|BESCHREIBUNG|
|---|---|
|944100|Remotebefehlsausführung: Apache Struts, Oracle WebLogic|
|944110|Erkennt potenzielle Payloadausführung|
|944120|Mögliche Nutzlastausführung und Remotebefehlsausführung|
|944130|Verdächtige Java-Klassen|
|944200|Ausnutzung der Java-Deserialisierung (Apache Commons)|
|944210|Mögliche Verwendung von Java-Serialisierung|
|944240|Remotebefehlsausführung: Java-Serialisierung|
|944250|Remotebefehlsausführung: Verdächtige Java-Methode erkannt|

# <a name="bot-rules"></a>[Botregeln](#tab/bot)

## <a name="bot-manager-rule-sets"></a><a name="bot"></a> Bot Manager-Regelsätze

### <a name="bad-bots"></a><a name="bot100"></a> Bösartige Bots
|RuleId|BESCHREIBUNG|
|---|---|
|Bot100100|Von Threat Intelligence erkannte schädliche Bots|
|Bot100200|Schädliche Bots mit gefälschter Identität|

### <a name="good-bots"></a><a name="bot200"></a> Gute Bots
|RuleId|BESCHREIBUNG|
|---|---|
|Bot200100|Suchmaschinencrawler|
|Bot200200|Nicht überprüfte Suchmaschinencrawler|

### <a name="unknown-bots"></a><a name="bot300"></a> Unbekannte Bots
|RuleId|BESCHREIBUNG|
|---|---|
|Bot300100|Nicht angegebene Identität|
|Bot300200|Tools und Frameworks für Webdurchforstung und Angriffe|
|Bot300300|Allgemeine HTTP-Clients und SDKs|
|Bot300400|Dienst-Agents|
|Bot300500|Dienste zur Überwachung der Standortintegrität|
|Bot300600|Von Threat Intelligence erkannte unbekannte Bots|
|Bot300700|Sonstige Bots|

---


## <a name="next-steps"></a>Nächste Schritte

- [Benutzerdefinierte Regeln für Web Application Firewall mit Azure Front Door](waf-front-door-custom-rules.md)
