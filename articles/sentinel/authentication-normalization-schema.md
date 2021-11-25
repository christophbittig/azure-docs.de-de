---
title: Referenz zum Normalisierungsschema für die Authentifizierung in Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird das Normalisierungsschema für die Authentifizierung in Microsoft Sentinel beschrieben.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 62bf35c4638433cc4e9820f8d0bb7e2592bcebf5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517575"
---
# <a name="microsoft-sentinel-authentication-normalization-schema-reference-public-preview"></a>Referenz zum Normalisierungsschema für die Authentifizierung in Microsoft Sentinel (Public Preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das Authentifizierungsinformationsmodell wird zum Beschreiben von Ereignissen im Zusammenhang mit der Authentifizierung und der An- und Abmeldung von Benutzern verwendet. Authentifizierungsereignisse werden von vielen meldenden Geräten meist im Rahmen des Ereignisdatenstroms zusammen mit anderen Ereignissen gesendet.

Von Windows werden beispielsweise verschiedene Authentifizierungsereignisse zusammen mit anderen Ereignissen im Zusammenhang mit Betriebssystemaktivitäten gesendet. Daher werden die Authentifizierungsereignisse in den meisten Fällen in verschiedenen Microsoft Sentinel-Tabellen gespeichert und mithilfe einer KQL-Funktion normalisiert, die auch nur die relevanten Authentifizierungsereignisse herausfiltert.

Zu den Authentifizierungsereignissen gehören sowohl Ereignisse von Systemen, die sich auf die Authentifizierung konzentrieren, wie etwa VPN-Gateways oder Domänencontroller, als auch die direkte Authentifizierung bei einem Endsystem, wie etwa bei einem Computer oder einer Firewall.

Weitere Informationen zur Normalisierung in Microsoft Sentinel finden Sie unter [Normalisierung und erweitertes SIEM-Informationsmodell (ADVANCED SIEM Information Model, ASIM).](normalization.md)

> [!IMPORTANT]
> Das Authentifizierungsnormalisierungsschema befindet sich derzeit in der VORSCHAUPHASE. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
>
> In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="parsers"></a>Parser

Microsoft Sentinel bietet die folgenden integrierten produktspezifischen Authentifizierungsereignisparser: 

- **Windows-Anmeldungen**, die als sicherheitsrelevantes Ereignisse (4624, 4625, 4634 und 4647) gemeldet und mithilfe des Log Analytics-Agents oder des Azure Monitor-Agents erfasst werden.
- **Windows-Anmeldungen**,die von Microsoft 365 Defender für Endpunkt gemeldet und mit dem Microsoft 365 Defender-Connector erfasst werden.
- **Azure Active Directory-Anmeldungen**, die mit dem Azure Active Directory-Connector erfasst werden. Für reguläre, nicht interaktive, verwaltete Identitäten- und Dienstprinzipalanmeldungen werden eigene Parser bereitgestellt.
- **AWS-Anmeldungen**, die mit dem AWS CloudTrail-Connector erfasst werden.
- **Okta-Authentifizierung**, die mit dem Okta-Connector erfasst wird.

Damit der quellunabhängige Parser verwendet wird, der alle aufgelisteten Parser vereinheitlicht und so sicherstellt, dass Sie Daten aus allen konfigurierten Quellen analysieren, verwenden Sie **imAuthentication** als Tabellenname in Ihrer Abfrage.

Stellen Sie die [quellenunabhängigen und quellenspezifischen Parser](normalization-about-parsers.md) aus dem [Microsoft Sentinel GitHub-Repository](https://aka.ms/AzSentinelAuth) bereit.



## <a name="normalized-content"></a>Normalisierter Inhalt

Zur Unterstützung für das Authentifizierungsschema ASIM gehört auch die Unterstützung für die folgenden integrierten Analyseregeln mit normalisierten Authentifizierungsparsern. Links zum Microsoft Sentinel-GitHub-Repository sind weiter unten als Referenz angegeben. Sie finden diese Regeln jedoch auch im [Microsoft Sentinel Analytics-Regelkatalog.](detect-threats-built-in.md) Verwenden Sie die verlinkten GitHub-Seiten, um alle relevanten Hunting-Abfragen für die aufgeführten Regeln zu kopieren.

- [Potenzieller Kennwortspray-Angriff (Verwendung der Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [Brute-Force-Angriff auf Anmeldeinformationen von Benutzern (Verwendung der Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [Benutzeranmeldung aus verschiedenen Ländern innerhalb von 3 Stunden (Verwendung der Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [Anmeldungen von IPs, die versuchen, sich bei deaktivierten Konten anzumelden (verwendet die Authentifizierungsnormalisierung)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


Regeln für die Analyse einer normalisierten Authentifizierung sind speziell, da damit Angriffe von verschiedenen Quellen erkannt werden. Wenn sich beispielsweise ein Benutzer oder eine Benutzerin von verschiedenen Ländern aus bei verschiedenen, nicht miteinander verbundenen Systemen anmeldet, wird diese Bedrohung jetzt von Microsoft Sentinel erkannt.

## <a name="schema-details"></a>Schemadetails

Das Authentifizierungsinformationsmodell orientiert sich am [OSSEM-Anmeldeentitätsschema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/logon.md).

In den folgenden Tabellen ist mit *Typ* ein logischer Typ gemeint. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).

### <a name="log-analytics-fields"></a>Log Analytics-Felder

Die folgenden Felder werden von Log Analytics für jeden Datensatz generiert und können beim Erstellen eines benutzerdefinierten Connectors überschrieben werden.

|Feld  |type  |BESCHREIBUNG  |
|---------|---------|---------|
|<a name ="timegenerated"></a>**TimeGenerated**     |  datetime       |Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde.         |
|**_ResourceId**     | guid        |  Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden.       |
| **Type** | String | Die ursprüngliche Tabelle, aus der der Datensatz abgerufen wurde. Dieses Feld ist nützlich, wenn ein und dasselbe Ereignis über mehrere Kanäle in verschiedenen Tabellen empfangen werden kann und die gleichen EventVendor- und EventProduct-Werte hat.<br><br>Ein Sysmon-Ereignis kann z. B. entweder in der Tabelle Event oder in der Tabelle WindowsEvent gesammelt werden. |
|     |         |         |

> [!NOTE]
> Log Analytics fügt auch andere Felder hinzu, die für Sicherheitsanwendungsfälle weniger relevant sind. Weitere Informationen finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Ereignisfelder

Ereignisfelder sind allen Schemas gemeinsam und beschreiben die Aktivität selbst und das meldende Gerät.

| Feld               | Klasse       | type       |  BESCHREIBUNG        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Optional    | String     |     Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert.   |
| **EventCount**          | Obligatorisch.   | Integer    |     Die Anzahl der Ereignisse, die vom Datensatz beschrieben werden. <br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen. <br><br>Legen Sie den Wert für andere Quellen auf `1` fest. <br><br>**Hinweis**: Dieses Feld ist aus Gründen der Konsistenz enthalten, wird jedoch in der Regel nicht für Authentifizierungsereignisse verwendet.  |
| **EventStartTime**      | Obligatorisch.   | Datum/Uhrzeit  |      Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, gibt dieses Feld die Zeit an, zu der das erste Ereignis generiert wurde. Andernfalls wird in diesem Feld ein Alias für das Feld [TimeGenerated](#timegenerated) verwendet.<br><br>**Hinweis**: Dieses Feld ist aus Gründen der Konsistenz enthalten, wird jedoch in der Regel nicht für Authentifizierungsereignisse verwendet.  |
| **EventEndTime**        | Obligatorisch.   | Alias      |      Alias für das Feld [TimeGenerated](#timegenerated).    |
| **EventType**           | Obligatorisch.   | Enumerated |    Beschreibt den vom Datensatz gemeldeten Vorgang. <br><br>Für Authentifizierungsdatensätze werden folgende Werte unterstützt: <br>- `Logon` <br>- `Logoff`<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Der ursprüngliche Wert muss im Feld [EventOriginalType](#eventoriginaltype) gespeichert werden.|
| <a name ="eventoriginaltype"></a>**EventOriginalType**           | Optional   | String |    Ereignistyp oder -ID, wie im Quelldatensatz angegeben. <br><br>Beispiel: `4625`|
| **EventResult**         | Obligatorisch.   | Enumerated |  Beschreibt das Ergebnis des Ereignisses, das auf einen der folgenden unterstützten Werte normalisiert wird: <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA` (nicht zutreffend) <br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. <br><br>Die Quelle stellt möglicherweise ebenfalls nur einen Wert für das Feld [EventResultDetails](#eventresultdetails) bereit, das analysiert werden muss, um den Wert **EventResult** zu erhalten. |
| <a name ="eventresultdetails"></a>**EventResultDetails**         | Optional   | String |  Einer der folgenden Werte: <br><br>-  `No such user or password`. Dieser Wert sollte auch dann verwendet werden, wenn mit dem ursprünglichen Ereignis ohne Verweis auf ein Kennwort gemeldet wird, dass kein solcher Benutzer vorhanden ist. <br>-   `Incorrect password`<br>-   `Account expired`<br>-  `Password expired`<br>- `User locked`<br>-  `User disabled`<br>-    `Logon violates policy`. Dieser Wert sollte verwendet werden, wenn mit dem ursprünglichen Ereignis beispielsweise Folgendes gemeldet wird: MFA erforderlich, Anmeldung außerhalb der Arbeitszeiten, Beschränkungen durch bedingten Zugriff oder zu viele Versuche.<br>-  `Session expired`<br>-  `Other`<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten.|
| **EventSubType**    | Optional    | String     |   Der Anmeldetyp, der in der Regel für Windows-Anmeldetypen verwendet wird. <br><br>Beispiel: `Interactive`|
| **EventOriginalResultDetails**    | Optional    | String     |  Der im ursprünglichen Datensatz bereitgestellte Wert für [EventResultDetails](#eventresultdetails), sofern er von der Quelle bereitgestellt wird.|
| **EventOriginalUid**    | Optional    | String     |   Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt.|
| **EventOriginalType**   | Optional    | String     |   Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | Obligatorisch.   | String     |             Das Produkt, das das Ereignis erzeugt. <br><br>Beispiel: `Windows`<br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.           |
| **EventProductVersion** | Optional    | String     | Die Version des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `10` <br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.     |
| **EventVendor**         | Obligatorisch.   | String     |           Der Hersteller des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `Microsoft`  <br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.  |
| **EventSchemaVersion**  | Obligatorisch.   | String     |    Die Version des Schemas. Die hier dokumentierte Version des Schemas ist `0.1`.         |
| **EventReportUrl**      | Optional    | String     | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die zusätzliche Informationen zum Ereignis enthält.|
| <a name ="dvc"></a>**Dvc** | Obligatorisch.       | String     |              Ein eindeutiger Bezeichner des Geräts, auf dem das Ereignis aufgetreten ist. <br><br>Dieses Feld kann ein Alias für die Felder [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die es kein offensichtliches Gerät gibt, den gleichen Wert wie das Feld [Ereignisprodukt](#eventproduct).             |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Empfohlen | IP-Adresse |         Die IP-Adresse des Geräts, auf dem das Prozessereignis aufgetreten ist.  <br><br>Beispiel: `45.21.42.12`  <br><br>**Hinweis**: Verwenden Sie dieses Feld nicht, wenn ein Bezeichner verfügbar, der Typ jedoch unbekannt ist. Weitere Informationen finden Sie unter [Dvc](#dvc).  |
| <a name ="dvchostname"></a>**DvcHostname**         | Empfohlen | Hostname   |               Der Hostname des Geräts, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `ContosoDc.Contoso.Azure`      <br><br>**Hinweis**: Verwenden Sie dieses Feld nicht, wenn ein Bezeichner verfügbar, der Typ jedoch unbekannt ist. Weitere Informationen finden Sie unter [Dvc](#dvc).          |
| <a name ="dvcid"></a>**DvcId**               | Optional    | String     |  Die eindeutige ID des Geräts, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **AdditionalFields**    | Optional    | Dynamisch    | Wenn Ihre Quelle zusätzliche Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie ihm die zusätzlichen Informationen als Schlüssel-Wert-Paare hinzu.    |
| | | | |


## <a name="authentication-specific-fields"></a>Authentifizierungsspezifische Felder

Die in der folgenden Tabelle aufgeführten Felder sind spezifisch für Authentifizierungsereignisse. Sie ähneln jedoch Feldern in anderen Schemas und folgen ähnlichen Benennungskonventionen.

Authentifizierungsereignisse beziehen sich auf die folgenden Entitäten:

- **Actor** (Schauspieler)
- **ActingApp**
- **SrcDvc**
- **TargetUser**
- **TargetApp**
- **TargetDvc**

Die Beziehung zwischen diesen Entitäten lässt sich am besten wie folgt darstellen:

Ein **Akteur**, der eine *agierende Anwendung* (**ActingApp**) auf einem *Quellgerät* (**SrcDvc**) ausführt, authentifiziert einen **Zielbenutzer** (TargetUser) bei einer *Zielanwendung* (**TargetApp**) auf einem *Zielgerät* (**TargetDvc**).

| Feld          | Klasse        | type       | BESCHREIBUNG   |
|---------------|--------------|------------|-----------------|
|**LogonMethod** |Optional |String |Die zum Durchführen der Authentifizierung verwendete Methode. <br><br>Beispiel: `Username & Password` |
|**LogonProtocol** |Optional |String |Das zum Durchführen der Authentifizierung verwendete Protokoll. <br><br>Beispiel: `NTLM` |
| <a name="actoruserid"></a>**ActorUserId**    | Optional  | UserId     |   Eine maschinenlesbare, alphanumerische, eindeutige Darstellung des Akteurs. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).  <br><br>Beispiel: `S-1-12-1-4141952679-1282074057-627758481-2916039507`    |
| **ActorUserIdType**| Optional  | UserIdType     |  Der Typ der ID, die im Feld [ActorUserId](#actoruserid) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).         |
| <a name="actorusername"></a>**ActorUsername**  | Optional    | Username     | Der Benutzername des Akteurs ggf. mit Informationen zur Domäne. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `AlbertE`     |
| **ActorUsernameType**              | Optional    | UsernameType |   Gibt den Typ des Benutzernamens an, der im Feld [ActorUsername](#actorusername) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity). <br><br>Beispiel: `Windows`       |
| **ActorUserType** | Optional | String | Der Typ des Akteurs. <br><br>Beispiel: `Guest` |
| **ActorSessionId** | Optional     | String     |   Die eindeutige ID der Anmeldesitzung des Akteurs.  <br><br>Beispiel: `102pTUgC3p8RIqHvzxLCHnFlg`  |
| **ActingAppId** | Optional | String | Die ID der Anwendung, die im Auftrag des Akteurs Autorisierungen durchführt. Dabei kann es sich um einen Prozess, einen Browser oder einen Dienst handeln. <br><br>Beispiel: `0x12ae8` |
| **ActiveAppName** | Optional | String | Der Name der Anwendung, die im Auftrag des Akteurs Autorisierungen durchführt. Dabei kann es sich um einen Prozess, einen Browser oder einen Dienst handeln. <br><br>Beispiel: `C:\Windows\System32\svchost.exe` |
| **ActingAppType** | Optional | Enumerated | Der Typ der agierenden Anwendung. Unterstützte Werte sind: <br> <br>- `Process` <br>- `Browser` <br>- `Resource` <br>- `Other` |
| **HttpUserAgent** |   Optional    | String |  Wenn die Authentifizierung über HTTP oder HTTPS erfolgt, entspricht der Wert dieses Felds dem HTTP-Header „user_agent“, der von der agierenden Anwendung beim Durchführen der Authentifizierung bereitgestellt wird.<br><br>Beispiel: `Mozilla/5.0 (iPhone; CPU iPhone OS 12_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1` |
|<a name="targetuserid"></a> **TargetUserId**   | Optional | UserId     | Eine maschinenlesbare, alphanumerische, eindeutige Darstellung des Zielbenutzers. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).            <br><br> Beispiel: `00urjk4znu3BcncfY0h7`    |
| **TargetUserIdType**               | Optional | UserIdType     | Der Typ der Benutzer-ID, die im Feld [TargetUserId](#targetuserid) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).            <br><br> Beispiel: `SID`  |
| <a name="targetusername"></a>**TargetUsername** | Optional | Username     | Der Benutzername des Zielbenutzers ggf. mit Informationen zur Domäne. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).  <br><br>Beispiel: `MarieC`      |
| **TargetUsernameType**             |Optional  | UsernameType | Gibt den Typ des Benutzernamens an, der im Feld [TargetUsername](#targetusername) gespeichert ist. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).          |
| **TargetUserType** | Optional | String | Der Typ des Zielbenutzers. <br><br>Beispiel: `Member` |
| **TargetSessionId** | Optional | String | Der Bezeichner für die Anmeldesitzung des Zielbenutzers auf dem Quellgerät. |
| **Benutzer**           | Alias        |     String       | Alias für den [TargetUsername](#targetusername) oder die [TargetUserId](#targetuserid), wenn [TargetUsername](#targetusername) nicht definiert ist. <br><br>Beispiel: `CONTOSO\dadmin`     |
|**SrcDvcId** |Optional |String |Die ID des Quellgeräts, wie im Datensatz angegeben. <br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| <a name="srcdvchostname"></a>**SrcDvcHostname** |Optional | Hostname| Der Hostname des Quellgeräts ggf. mit Informationen zur Domäne. Weitere Informationen finden Sie unter [Geräteentität](normalization-about-schemas.md#the-device-entity). <br><br>Beispiel: `Constoso\DESKTOP-1282V4D`|
| **SrcDvcHostnameType**|Optional |HostnameType |Der Typ von [SrcDvcHostname](#srcdvchostname), sofern bekannt. Weitere Informationen finden Sie unter [Geräteentität](normalization-about-schemas.md#the-device-entity). |
|**SrcDvcType** |Optional |Enumerated |Der Typ des Quellgeräts. Mögliche Werte sind: <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|**SrcDvcIpAddr**|Empfohlen |IP-Adresse |Die IP-Adresse des Quellgeräts. <br><br>Beispiel: `185.175.35.214` |
| **SrcDvcOs**|Optional |String |Das Betriebssystem des Quellgeräts. <br><br>Beispiel: `Windows 10` |
|**SrcIsp** | Optional|String |Der Internetdienstanbieter (Internet Service Provider, ISP), der vom Quellgerät zum Herstellen einer Internetverbindung verwendet wird. <br><br>Beispiel: `corpconnect` |
| **SrcGeoCountry**|Optional |Land |Beispiel: `Canada` <br><br>Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types). |
| **SrcGeoCity**|Optional |City |Beispiel: `Montreal` <br><br>Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types). |
|**SrcGeoRegion** | Optional|Region | Beispiel: `Quebec` <br><br>Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).|
| **SrcGeoLongtitude**|Optional |Längengrad  | Beispiel: `-73.614830` <br><br>Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).|
| **SrcGeoLatitude**|Optional |Breitengrad |Beispiel: `45.505918` <br><br>Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types). |
|**TargetAppId** |Optional | String| Die ID der Anwendung, für die die Autorisierung erforderlich ist und die häufig vom meldenden Gerät zugewiesen wird. <br><br>Beispiel: `89162` |
|<a name="targetappname"></a>**TargetAppName** |Optional |String |Der Name der Anwendung, für die die Autorisierung erforderlich ist. Dabei kann es sich um einen Dienst, eine URL oder eine SaaS-Anwendung handeln. <br><br>Beispiel: `Saleforce` |
| **TargetAppType**|Optional |String |Der Typ der Anwendung, die im Auftrag des Akteurs Autorisierungen durchführt. Unterstützte Werte sind:  <br><br>- `Process` <br>- `Service` <br>- `Resource` <br>- `URL` <br>- `SaaS application` <br>- `Other`|
|**TargetUrl** |Optional |String |Die der Zielanwendung zugeordnete URL. <br><br>Beispiel: `https://console.aws.amazon.com/console/home?fromtb=true&hashArgs=%23&isauthcode=true&nc2=h_ct&src=header-signin&state=hashArgsFromTB_us-east-1_7596bc16c83d260b` |
|**LogonTarget**| Alias| |Alias für [TargetAppName](#targetappname), *URL* oder [TargetDvcHostname](#targetdvchostname), je nachdem, mit welchem Feld das Authentifizierungsziel am besten beschrieben wird. |
|**TargetDvcId** |Optional | String|Die ID des Zielgeräts, wie im Datensatz angegeben. <br><br> Beispiel: `2739` |
|<a name="targetdvchostname"></a>**TargetDvcHostname** | Empfohlen| String|Der Hostname des Zielgeräts ggf. mit Informationen zur Domäne. Weitere Informationen finden Sie unter [Geräteentität](normalization-about-schemas.md#the-device-entity). |
| **TargetDvcHostnameType**|Empfohlen | String|Der Typ von [TargetDvcHostname](#targetdvchostname). Weitere Informationen finden Sie unter [Geräteentität](normalization-about-schemas.md#the-device-entity). |
|**TargetDvcType** |Optional | Enumerated|Der Typ des Zielgeräts. Unterstützte Werte sind: <br><br>- `Computer`<br>- `Mobile Device` <br>- `IOT Device` <br>- `Other` |
|<a name="targetdvcipaddr"></a>**TargetDvcIpAddr** |Optional | IP-Adresse|Die IP-Adresse des Zielgeräts. <br><br>Beispiel: `2.2.2.2` |
|**TargetDvc** |Alias | |   Der eindeutige Bezeichner des Zielgeräts. <br><br>Wählen Sie als Alias den am besten geeigneten Wert für die jeweilige Quelle: [TargetDvcHostname](#targetdvchostname), [TargetDvcIpAddr](#targetdvcipaddr) oder eine andere ID, falls diese besser geeignet ist. |
| **TargetDvcOs**| Optional| String| Das Betriebssystem des Zielgeräts. <br><br>Beispiel: `Windows 10`|
| **TargetPortNumber**|Optional |Integer |Der Port des Zielgeräts.|
| | | | |




## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Normalisierung in Microsoft Sentinel](normalization.md)
- [Referenz zum DNS-Normalisierungsschema von Microsoft Sentinel](dns-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Dateiereignissen (Public Preview)](file-event-normalization-schema.md)
- [Referenz zum Microsoft Sentinel-Netzwerk-Normalisierungsschema](./network-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Prozessereignis-Normalisierungsschema (Öffentliche Vorschau)](process-events-normalization-schema.md)
