---
title: Referenz zum Microsoft Sentinel-Normalisierungsschema für Netzwerksitzungen (Public Preview) | Microsoft-Dokumentation
description: Dieser Artikel behandelt das Microsoft Sentinel-Normalisierungsschema für Netzwerksitzungen.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: df994e2be047e2829ea56423b85eee32e63c0154
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520416"
---
# <a name="microsoft-sentinel-network-session-normalization-schema-reference-public-preview"></a>Referenz zum Microsoft Sentinel-Normalisierungsschema für Netzwerksitzungen (Public Preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das Normalisierungsschema für Netzwerksitzungen wird verwendet, um eine IP-Netzwerkaktivität zu beschreiben. Dies umfasst Netzwerkverbindungen und Netzwerksitzungen. Entsprechende Ereignisse werden beispielsweise von Betriebssystemen, Routern, Firewalls, Intrusion-Prevention-Systemen und Websicherheitsgateways gemeldet.

Weitere Informationen zu Normalisierung in Microsoft Sentinel finden Sie unter [Normalisierung und das Advanced SIEM Information Model (ASIM)](normalization.md).

> [!IMPORTANT]
> In diesem Artikel wird Version 0.2 des Netzwerknormalisierungsschemas beschrieben. [Version 0.1](normalization-schema-v1.md) wurde veröffentlicht, bevor ASIM verfügbar war, und stimmt an mehreren Stellen nicht mit ASIM überein. Weitere Informationen finden Sie unter [Unterschiede zwischen Versionen der Netzwerknormalisierungsschemas](normalization-schema-v1.md#changes). 
>

> [!IMPORTANT]
> Das Netzwerknormalisierungsschema befindet sich derzeit in der VORSCHAUPHASE. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
>
> In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="support-for-common-network-telemetry-sources"></a>Unterstützung für gängige Netzwerktelemetriequellen

Das Netzwerknormalisierungsschema kann jede IP-Netzwerksitzung darstellen, wurde jedoch speziell für die Unterstützung gängiger Quelltypen konzipiert:

- Netflow
- Firewalls
- Intrusion-Prevention-Systeme
- Webserver
- Websicherheitsgateways

Die folgenden Abschnitte enthalten Anleitungen zum Normalisieren und Verwenden des Schemas für die verschiedenen Quelltypen. Für jeden der Quelltypen gilt Folgendes:
- Er kann zusätzliche Felder aus den Hilfsfeldlisten unterstützen: [Zwischengerätefelder](#Intermediary), [HTTP-Sitzungsfelder](#http-session-fields) und [Inspektionsfelder](#inspection-fields). Einige Felder sind möglicherweise nur im Kontext des spezifischen Quelltyps Pflichtfelder.
- Er kann quelltypspezifische Werte für gängige Ereignisfelder wie `EventType` und `EventResult` zulassen.
- Er kann dem `imNetworkSession`-Parser auch den `imWebSession`- und/oder den `inNetworkNotable`-Parser unterstützen.

### <a name="netflow-log-sources"></a>Netflow-Protokollquellen

| Aufgabe | BESCHREIBUNG|
| --- | --- |
| **Normalisieren von Netflow-Ereignissen** | Um Netflow-Ereignisse zu normalisieren, ordnen Sie sie [Netzwerksitzungsfeldern](#network-session-fields) zu. Netflow-Telemetrie unterstützt Aggregation, und der Wert des Felds `EventCount` sollte dementsprechend auf den Netflow-Wert *Flows* festgelegt werden. |
| **Verwenden von Netflow-Ereignissen** | Netflow-Ereignisse treten im Rahmen des quellunabhängigen Parsers [imNetworkSession](#use-parsers) auf. Berücksichtigen Sie beim Erstellen einer aggregativen Abfrage den Feldwert `EventCount`, der möglicherweise nicht immer auf `1` festgelegt ist. |
| | |

### <a name="firewall-log-sources"></a>Firewallprotokollquellen

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **Normalisieren von Firewallereignissen** | Um Ereignisse von Firewalls zu normalisieren, ordnen Sie relevante Ereignisse den Feldern [Ereignis](#event-fields), [Netzwerksitzung](#network-session-fields) und [Sitzungsinspektion](#inspection-fields) zu. Filtern Sie diese Ereignisse, und fügen Sie sie dem quellunabhängigen Parser [inNetworkNotables](#use-parsers) hinzu. |
| **Verwenden von Firewallereignissen** | Firewallereignisse treten im Rahmen des quellunabhängigen Parsers [imNetworkSession](#use-parsers) auf. Relevante Ereignisse werden von den Firewallinspektions-Engines identifiziert und ebenfalls im Rahmen der quellunabhängigen [inNetworkNotables](#use-parsers)-Parser angezeigt. |
| | |

### <a name="intrusion-prevention-systems-ips-log-sources"></a>Protokollquellen von Intrusion-Prevention-Systemen (IPS)

| Aufgabe | BESCHREIBUNG |
| --- | --- |
| **Normalisieren von IPS-Ereignissen** | Um Ereignisse aus Intrusion-Prevention-Systemen zu normalisieren, ordnen Sie [Ereignisfelder](#event-fields), [Netzwerksitzungsfelder](#network-session-fields) und [Sitzungsinspektionsfelder](#inspection-fields) zu. Stellen Sie sicher, dass Sie Ihren quellspezifischen Parser in beiden quellunabhängigen Parsern, [imNetworkSession](#use-parsers) und [inNetworkNotables](#use-parsers), einschließen. |
| **Verwenden von IPS-Ereignissen** | IPS-Ereignisse treten im Rahmen der quellunabhängigen Parser [imNetworkSession](#use-parsers) und [inNetworkNotables](#use-parsers) auf. |
| | |

### <a name="web-servers"></a>Webserver

| Aufgabe | Beschreibung |
| --- | --- |
| **Normalisieren von Webserverereignissen** | Um Ereignisse von einem Webserver zu normalisieren, ordnen Sie [Ereignisfelder](#event-fields), [Netzwerksitzungsfelder](#network-session-fields) und [HTTP-Sitzungsfelder](#http-session-fields) zu. Legen Sie den `EventType`-Wert auf `HTTP Session` fest, und befolgen Sie die für HTTP-Sitzungen spezifischen Anweisungen für die Felder `EventResult` und `EventResultDetails`. <br><br>Stellen Sie sicher, dass Sie Ihren quellspezifischen Parser in beiden quellunabhängigen Parsern, [imNetworkSession](#use-parsers) und [imWebSession](#use-parsers), einschließen. |
| **Verwenden von Webserverereignissen** | Webserverereignisse treten im Rahmen des quellunabhängigen Parsers [imNetworkSession](#use-parsers) auf. Verwenden Sie jedoch den [imWebSession](#use-parsers)-Parser, um HTTP-spezifische Felder zu verwenden. |
| | |

### <a name="web-security-gateways"></a>Websicherheitsgateways

| Aufgabe | Beschreibung |
| --- | --- |
| **Normalisieren von Websicherheitsgateway-Ereignissen** | Um Ereignisse von einem Webservergateway zu normalisieren, ordnen Sie [Ereignisfelder](#event-fields), [Netzwerksitzungsfelder](#network-session-fields), [HTTP-Sitzungsfelder](#http-session-fields), [Sitzungsinspektionsfelder](#inspection-fields) und optional die Zwischenfelder zu. <br><br>Legen Sie den `EventType`-Wert auf `HTTP` fest, und befolgen Sie die für HTTP-Sitzungen spezifischen Anweisungen für die Felder `EventResult` und `EventResultDetails`. <br><br>Stellen Sie sicher, dass Sie Ihren quellspezifischen Parser in beiden quellunabhängigen Parsern, [imNetworkSession](#use-parsers) und [imWebSession](#use-parsers), einschließen. Filtern Sie außerdem von der Inspektions-Engine erkannte Ereignisse, und fügen Sie sie dem quellunabhängigen Parser [inNetworkNotables](#use-parsers) hinzu. |
| **Verwenden von Websicherheitsgateway-Ereignissen** | Webserverereignisse treten im Rahmen des quellunabhängigen Parsers [imNetworkSession](#use-parsers) auf. <br><br>– Um HTTP-spezifische Felder zu verwenden, setzen Sie den [imWebSession](#use-parsers)-Parser ein.<br>– Um erkannte Sitzungen zu analysieren, verwenden Sie den quellunabhängigen Parser [inNetworkNotables](#use-parsers). |
| | |


## <a name="use-parsers"></a>Verwenden von Parsern

Um einen quellunabhängigen Parser zu verwenden, der alle integrierten Parser vereinheitlicht, und um sicherzustellen, dass Ihre Analyse für alle konfigurierten Quellen ausgeführt wird, können Sie folgende Parser einsetzen:


- **imNetworkSession** für alle Netzwerksitzungen.
- **imWebSession** für HTTP-Sitzungen, die in der Regel von Webservern, Webproxys und Websicherheitsgateways gemeldet werden.
- **inNetworkNotables** für Sitzungen, die von einer Erkennungs-Engine (in der Regel als verdächtig) erkannt werden. Wichtige Ereignisse werden normalerweise von Intrusion-Prevention-Systemen, Firewalls und Websicherheitsgateways gemeldet.

Stellen Sie die [quellunabhängigen und quellspezifischen Parser](normalization-about-parsers.md) aus dem [Microsoft Sentinel GitHub-Repository](https://aka.ms/AzSentinelNetworkSession) bereit.

### <a name="built-in-source-specific-parsers"></a>Integrierte quellenspezifische Parser

Microsoft Sentinel bietet die folgenden integrierten, produktspezifischen Netzwerksitzungsparser:

- Quellspezifische Parser:
  - **Microsoft 365 Defender für Endpunkt**: vimNetworkSessionMicrosoft365Defender
  - **Microsoft Defender für IoT – Endpunkt (MD4IoT)** : vimNetworkSessionMD4IoT
  - **Microsoft Sysmon für Linux**: vimNetworkSessionSysmonLinux
  - **Windows Events-Firewall**: Windows-Firewallaktivität, die mithilfe von Windows Events 515x entweder mit dem Log Analytics-Agent oder dem Azure Monitor-Agent in der Tabelle „Event“ oder „WindowsEvent“ erfasst wird, vimNetworkSessionMicrosoftWindowsEventFirewall 

Die Parser können über das [GitHub-Repository von Microsoft Sentinel](https://aka.ms/AzSentinelNetworkSession) bereitgestellt werden.

### <a name="add-your-own-normalized-parsers"></a>Hinzufügen eigener normalisierter Parser

Wenn Sie benutzerdefinierte Parser für das Netzwerksitzungs-Informationsmodell implementieren, benennen Sie Ihre KQL-Funktionen anhand der folgenden Syntax: `imNetworkSession<vendor><Product>`. Diese Funktion sollte alle für die Quelle relevanten Felder zuordnen.

Fügen Sie ihre KQL-Funktion je nach Protokollquellen den relevanten quellunabhängigen Parsern hinzu. Weitere Informationen finden Sie unter

- [Netflow-Protokollquellen](#netflow-log-sources)
- [Firewallprotokollquellen](#firewall-log-sources)
- [Protokollquellen von Intrusion-Prevention-Systemen (IPS)](#intrusion-prevention-systems-ips-log-sources)
- [Webserver](#web-servers)
- [Websicherheitsgateways](#web-security-gateways)

## <a name="schema-details"></a>Schemadetails

Das Netzwerksitzungsinformationsmodell ist auf das [OSSEM-Netzwerkentitätsschema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/network.md) ausgerichtet.

Den bewährten Methoden der Branche entsprechend verwendet das Netzwerksitzungsschema die Deskriptoren **Src** und **Dst** zur Identifikation der Quell- und Zielgeräte der Netzwerksitzung, ohne das Token **Dvc** in den Feldnamen einzuschließen.

Der Hostname und die IP-Adresse des Quellgeräts heißen also beispielsweise **SrcHostname** bzw. **SrcIpAddr** und nicht **Src *Dvc* Hostname** und **Src *Dvc* IpAddr**. Das Präfix **Dvc** wird ggf. nur für das meldende Gerät oder das Zwischengerät verwendet.

Felder, die die den Quell- und Zielgeräten zugeordneten Benutzer und Anwendungen beschreiben, verwenden auch die Deskriptoren **Src** und **Dst**.

Andere ASIM-Schemas verwenden in der Regel **Target** anstelle von **Dst**.

### <a name="log-analytics-fields"></a>Log Analytics-Felder

Die folgenden Felder werden von Log Analytics für die einzelnen Datensätze generiert und können beim [Erstellen eines benutzerdefinierten Connectors](create-custom-connector.md) überschrieben werden.


| Feld | type | Diskussion (Discussion) |
|-------|------|------------|
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde. |
| **\_ResourceId** | guid | Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden. |
| **Type** | String | Die ursprüngliche Tabelle, aus der der Datensatz abgerufen wurde. Dieses Feld ist nützlich, wenn dasselbe Ereignis über mehrere Kanäle in unterschiedlichen Tabellen empfangen werden kann, aber dieselben `EventVendor`- und `EventProduct`-Werte aufweist. <br><br>Ein Sysmon-Ereignis kann z. B. entweder in der Tabelle **Event** oder in der Tabelle **SecurityEvent** erfasst werden. |
| | | |

> [!NOTE]
> Log Analytics fügt auch andere Felder hinzu, die für Sicherheitsanwendungsfälle weniger relevant sind. Weitere Informationen finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../azure-monitor/logs/log-standard-columns.md).
>

### <a name="event-fields"></a>Ereignisfelder

Ereignisfelder sind allen Schemas gemeinsam und beschreiben die Aktivität selbst und das meldende Gerät.

| Feld | Klasse | type | BESCHREIBUNG |
|-------|-------|------|-------------|
| **EventMessage** | Optional | String | Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert. |
| **EventCount** | Obligatorisch. | Integer | Die Anzahl der Ereignisse, die vom Datensatz beschrieben werden. <br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen. <br><br>**Hinweis**: Netflow-Quellen unterstützen Aggregation, und das Feld **EventCount** sollte auf den Wert des Netflow-Felds **FLOWS** festgelegt werden. Bei anderen Quellen wird der Wert in der Regel auf `1` festgelegt. |
| **EventStartTime** | Obligatorisch. | Datum/Uhrzeit | Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, gibt dieses Feld die Zeit an, zu der das erste Ereignis generiert wurde. Andernfalls wird in diesem Feld ein Alias für das Feld [TimeGenerated](#timegenerated) verwendet. |
| **EventEndTime** | Obligatorisch. | Alias | Alias für das Feld [TimeGenerated](#timegenerated). |
| **EventType** | Obligatorisch. | Enumerated | Beschreibt den vom Datensatz gemeldeten Vorgang.<br><br> Für Netzwerksitzungsdatensätze werden folgende Werte unterstützt:<br>- `NetworkConnection`<br>- `NetworkSession`<br>- `HTTPsession` |
| **EventSubType** | Optional | String | Zusätzliche Beschreibung des Ereignistyps, falls zutreffend. <br> Für Netzwerksitzungsdatensätze werden folgende Werte unterstützt:<br>- `Start`<br>- `End` |
| **EventResult** | Obligatorisch. | Enumerated | Beschreibt das Ereignisergebnis, das in einen der folgenden Werte normalisiert wird: <br> - `Success` <br> - `Partial` <br> - `Failure` <br> - `NA` (nicht zutreffend) <br><br>Für eine HTTP-Sitzung wird `Success` als Statuscode definiert, der niedriger als `400` ist, und `Failure` wird als Statuscode definiert, der höher als `400` ist. Eine Liste der HTTP-Statuscodes finden Sie unter [W3 Org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).<br><br>Die Quelle stellt möglicherweise nur einen Wert für das Feld [EventResultDetails](#eventresultdetails) bereit, das analysiert werden muss, um den **EventResult**-Wert zu erhalten. |
| <a name="eventresultdetails"></a>**EventResultDetails** | Optional | String | Bei HTTP-Sitzungen sollte der Wert dem HTTP-Statuscode entsprechen. <br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Der ursprüngliche Wert muss im Feld **EventOriginalResultDetails** gespeichert werden.|
| **EventOriginalResultDetails**    | Optional    | String     |  Der im ursprünglichen Datensatz bereitgestellte Wert für [EventResultDetails](#eventresultdetails), sofern er von der Quelle bereitgestellt wird.|
| **EventSeverity** | Obligatorisch. | Enumerated | Der Schweregrad des Ereignisses, wenn es eine erkannte Bedrohung oder eine Warnung darstellt. Mögliche Werte: `Informational`, `Low`, `Medium` und `High`. <br><br>Wenn das Ereignis keine Bedrohung darstellt, verwenden Sie den Wert `Informational`.<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Speichern Sie den ursprünglichen Wert im Feld [EventOriginalSeverity](#eventoriginalseverity). |
| <a name="eventoriginalseverity"></a>**EventOriginalSeverity** | Optional | String | Der ursprüngliche im Quelldatensatz bereitgestellte Schweregradwert. |
| **EventOriginalUid** | Optional | String | Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `69f37748-ddcd-4331-bf0f-b137f1ea83b` |
| **EventOriginalType** | Optional | String | Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt. <br><br>Beispiel: `5031` |
| <a name="eventproduct"></a>**EventProduct** | Obligatorisch. | String | Das Produkt, das das Ereignis erzeugt.<br><br>Beispiel: `Sysmon`<br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden. |
| **EventProductVersion** | Optional | String | Die Version des Produkts, das das Ereignis erzeugt.<br><br>Beispiel: `12.1` |
| **EventVendor** | Obligatorisch. | String | Der Hersteller des Produkts, das das Ereignis erzeugt.<br><br>Beispiel: `Microsoft`<br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden. |
| **EventSchema** | Obligatorisch. | String | Der Name des Schemas. Der Name des hier dokumentierten Schemas lautet `NetworkSession`. |
| **EventSchemaVersion** | Obligatorisch. | String | Die Version des Schemas. Die Version des hier dokumentierten Schemas lautet `0.2`. |
| **EventReportUrl** | Optional | String | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die zusätzliche Informationen zum Ereignis enthält. |
| **Dvc** | Alias | String | Ein eindeutiger Bezeichner des meldenden Geräts oder Zwischengeräts.<br><br>Beispiel: `ContosoDc.Contoso.Azure`<br><br>Dieses Feld kann ein Alias für die Felder [DvcFQDN](#dvcfqdn), [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die kein offensichtliches Gerät vorliegt, denselben Wert wie im Feld [Ereignisprodukt](#eventproduct). |
| <a name="dvcipaddr"></a>**DvcIpAddr** | Empfohlen | IP-Adresse | Die IP-Adresse des meldenden Geräts oder Zwischengeräts.<br><br>Beispiel: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Obligatorisch. | String | Der Hostname des meldenden Geräts oder Zwischengeräts ohne Domäneninformationen. Wenn kein Gerätename verfügbar ist, speichern Sie die entsprechende IP-Adresse in diesem Feld.<br><br>Beispiel: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | Empfohlen | String | Die Domäne des meldenden Geräts oder Zwischengeräts.<br><br>Beispiel: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Empfohlen | Enumerated | Der Typ von [DvcDomain](#dvcdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [DvcDomain](#dvcdomain) verwendet wird. |
| <a name="dvcfqdn"></a>**DvcFQDN** | Optional | String | Der Hostname des meldenden Geräts oder Zwischengeräts einschließlich Domäneninformationen, sofern verfügbar. <br><br> Beispiel: `Contoso\DESKTOP-1282V4D`<br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. Das Feld [DvcDomainType](#dvcdomaintype) spiegelt das verwendete Format wider.  |
| <a name="dvcid"></a>**DvcId** | Optional | String | Die ID des meldenden Geräts oder Zwischengeräts, wie im Datensatz angegeben.<br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | Optional | Enumerated | Der Typ von [DvcId](#dvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der Liste, und speichern Sie die anderen anhand der Feldnamen **DvcAzureResourceId** bzw. **DvcMDEid**.<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [DvcId](#dvcid) verwendet wird. |
| **AdditionalFields** | Optional | Dynamisch | Wenn Ihre Quelle zusätzliche Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie ihm die zusätzlichen Informationen als Schlüssel/Wert-Paare hinzu. |
| | | | |

### <a name="network-session-fields"></a>Netzwerksitzungsfelder

Die folgenden Felder gelten für die gesamte Protokollierung von Netzwerksitzungsaktivitäten:

| Feld | Klasse | type | Beschreibung |
|-------|-------|------|-------------|
|<a name="dstipaddr"></a> **DstIpAddr** | Empfohlen | IP-Adresse | Die IP-Adresse der Verbindung oder des Sitzungsziels. <br><br>Beispiel: `2001:db8::ff00:42:8329`<br><br>**Hinweis**: Dieser Wert ist obligatorisch, wenn [DstHostname](#dsthostname) angegeben wird. |
| <a name="dstportnumber"></a>**DstPortNumber** | Optional | Integer | Der Ziel-IP-Port<br><br>Beispiel: `443` |
| <a name="dsthostname"></a>**DstHostname** | Empfohlen | String | Der Hostname des Zielgeräts ohne Domäneninformationen. Wenn kein Gerätename verfügbar ist, speichern Sie die entsprechende IP-Adresse in diesem Feld.<br><br>Beispiel: `DESKTOP-1282V4D`<br><br>**Hinweis**: Dieser Wert ist obligatorisch, wenn [DstIpAddr](#dstipaddr) angegeben wird. |
| **Hostname** | Alias | | Alias für [DstHostname](#dsthostname) |
| <a name="dstdomain"></a>**DstDomain** | Empfohlen | String | Die Domäne des Zielgeräts.<br><br>Beispiel: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Empfohlen | Enumerated | Der Typ von [DstDomain](#dstdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>Erforderlich, wenn [DstDomain](#dstdomain) verwendet wird. |
| **DstFQDN** | Optional | String | Der Hostname des Zielgeräts einschließlich Domäneninformationen, sofern verfügbar. <br><br>Beispiel: `Contoso\DESKTOP-1282V4D` <br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. [DstDomainType](#dstdomaintype) spiegelt das verwendete Format wider.   |
| <a name="dstdvcid"></a>**DstDvcId** | Optional | String | Die ID des Zielgeräts, wie im Datensatz angegeben.<br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Optional | Enumerated | Der Typ von [DstDvcId](#dstdvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der obigen Liste, und speichern Sie die anderen in den Feldern **DstDvcAzureResourceId** bzw. **DstDvcMDEid**.<br><br>Erforderlich, wenn **DstDeviceId** verwendet wird.|
| **DstDeviceType** | Optional | Enumerated | Der Typ des Zielgeräts. Mögliche Werte sind:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="dstuserid"></a>**DstUserId** | Optional | String | Eine maschinenlesbare, alphanumerische, eindeutige Darstellung des Zielbenutzers. <br><br>Unterstützte Formate und Typen:<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Speichern Sie den ID-Typ im Feld [DstUserIdType](#dstuseridtype). Wenn andere IDs verfügbar sind, wird empfohlen, die Feldnamen in **DstUserSid**, **DstUserUid**, **DstUserAADID**, **DstUserOktaId** bzw. **UserAwsId** zu normalisieren. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `S-1-12` |
| <a name="dstuseridtype"></a>**DstUserIdType** | Optional | Enumerated | Der Typ der ID, die im Feld [DstUserId](#dstuserid) gespeichert ist. <br><br>Unterstützte Werte: `SID`, `UIS`, `AADID`, `OktaId` und `AWSId`. |
| <a name="dstusername"></a>**DstUsername** | Optional | String | Der Zielbenutzername einschließlich Domäneninformationen, sofern verfügbar. <br><br>Verwenden Sie eines der folgenden Formate in der folgenden Reihenfolge nach Priorität:<br>- **UPN/E-Mail**: `johndow@contoso.com`<br>- **Windows:** `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Einfach**: `johndow`. Verwenden Sie dieses einfache Format nur, wenn keine Domäneninformationen verfügbar sind.<br><br>Speichern Sie den Benutzernamenstyp im Feld [DstUsernameType](#dstusernametype). Wenn andere IDs verfügbar sind, wird empfohlen, die Feldnamen in **DstUserUpn**, **DstUserWindows** und **DstUserDn** zu normalisieren. Weitere Informationen finden Sie unter [Die Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `AlbertE` |
| **Benutzer** | Alias | | Alias für [DstUsername](#dstusername) |
| <a name="dstusernametype"></a>**DstUsernameType** | Optional | Enumerated | Gibt den Typ des Benutzernamens an, der im Feld [DstUsername](#dstusername) gespeichert ist. Unterstützte Werte: `UPN`, `Windows`, `DN` und `Simple`. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `Windows` |
| **DstUserType** | Optional | Enumerated | Der Typ des Akteurs. Unterstützte Werte sind:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Speichern Sie den ursprünglichen Wert im Feld [DstOriginalUserType](#dstoriginalusertype). |
| <a name="dstoriginalusertype"></a>**DstOriginalUserType** | Optional | String | Der ursprüngliche Zielbenutzertyp, sofern von der Quelle bereitgestellt. |
| **DstUserDomain** | Optional | String | Dieses Feld wird lediglich aus Gründen der Rückwärtskompatibilität beibehalten. ASIM erfordert, dass Domäneninformationen, sofern verfügbar, im Feld [DstUsername](#dstusername) enthalten sind. |
| <a name="dstappname"></a>**DstAppName** | Optional | String | Der Name der Zielanwendung.<br><br>Beispiel: `Facebook` |
| <a name="dstappid"></a>**DstAppId** | Optional | String | Die ID der Zielanwendung, wie vom meldenden Gerät angegeben.<br><br>Beispiel: `124` |
| **DstAppType** | Optional | String | Der Typ der Anwendung, die im Auftrag des Akteurs Autorisierungen durchführt. Unterstützte Werte sind:<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `URL`<br>- `SaaS application`<br>- `Other`<br><br>Dieses Feld ist obligatorisch, wenn [DstAppName](#dstappname) oder [DstAppId](#dstappid) verwendet wird. |
| **DstZone** | Optional | String | Die Netzwerkzone des Ziels, wie sie vom meldenden Gerät definiert wird.<br><br>Beispiel: `Dmz` |
| **DstInterfaceName** | Optional | String | Die Netzwerkschnittstelle, die vom Zielgerät für die Verbindung oder Sitzung verwendet wird.<br><br>Beispiel: `Microsoft Hyper-V Network Adapter` |
| **DstInterfaceGuid** | Optional | String | Die GUID der Netzwerkschnittstelle, die auf dem Zielgerät verwendet wird.<br><br>Beispiel:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **DstMacAddr** | Optional | String | Die MAC-Adresse der Netzwerkschnittstelle, die vom Zielgerät für die Verbindung oder Sitzung verwendet wird.<br><br>Beispiel: `06:10:9f:eb:8f:14` |
| **DstGeoCountry** | Optional | Land | Das Land/die Region, das bzw. die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `USA` |
| **DstGeoRegion** | Optional | Region | Die der Ziel-IP-Adresse zugeordnete Region bzw. das zugeordnete Bundesland/der zugeordnete Kanton innerhalb eines Landes. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `Vermont` |
| **DstGeoCity** | Optional | City | Die Stadt, die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `Burlington` |
| **DstGeoLatitude** | Optional | Breitengrad | Der Breitengrad der geografischen Koordinate, die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `44.475833` |
| **DstGeoLongitude** | Optional | Längengrad | Der Längengrad der geografischen Koordinate, die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `73.211944` |
| <a name="srcipaddr"></a>**SrcIpAddr** | Empfohlen | IP-Adresse | Die IP-Adresse, von der die Verbindung oder Sitzung stammt. Dieser Wert ist obligatorisch, wenn **SrcHostname** angegeben wird.<br><br>Beispiel: `77.138.103.108` |
| **IpAddr** | Alias | | Alias für [SrcIpAddr](#srcipaddr) |
| **SrcPortNumber** | Optional | Integer | Der IP-Port, von dem die Verbindung stammt. Ist möglicherweise für Sitzungen, die mehrere Verbindungen umfassen, nicht relevant.<br><br>Beispiel: `2335` |
| **SrcHostname** | Empfohlen | String | Der Hostname des Quellgeräts ohne Domäneninformationen. Wenn kein Gerätename verfügbar ist, speichern Sie die entsprechende IP-Adresse in diesem Feld. Dieser Wert ist obligatorisch, wenn [SrcIpAddr](#srcipaddr) angegeben wird.<br><br>Beispiel: `DESKTOP-1282V4D` |
|<a name="srcdomain"></a> **SrcDomain** | Empfohlen | String | Die Domäne des Quellgeräts.<br><br>Beispiel: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Empfohlen | Enumerated | Der Typ von [SrcDomain](#srcdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows` (Beispiel: `contoso`)<br>- `FQDN` (Beispiel: `microsoft.com`)<br><br>Erforderlich, wenn [SrcDomain](#srcdomain) verwendet wird. |
| **SrcFQDN** | Optional | String | Der Hostname des Quellgeräts ggf. mit Informationen zur Domäne. <br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. Das Feld [SrcDomainType](#srcdomaintype) spiegelt das verwendete Format wider. <br><br>Beispiel: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String | Die ID des Quellgeräts, wie im Datensatz angegeben.<br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | Enumerated | Der Typ von [SrcDvcId](#srcdvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der obigen Liste, und speichern Sie die anderen in den Feldern **SrcDvcAzureResourceId** bzw. **SrcDvcMDEid**.<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [SrcDvcId](#srcdvcid) verwendet wird. |
| **SrcDeviceType** | Optional | Enumerated | Der Typ des Quellgeräts. Mögliche Werte sind:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Optional | String | Eine maschinenlesbare, alphanumerische, eindeutige Darstellung des Quellbenutzers. Format und unterstützte Typen:<br>-  **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Speichern Sie den ID-Typ im Feld [SrcUserIdType](#srcuseridtype). Wenn andere IDs verfügbar sind, wird empfohlen, die Feldnamen in „SrcUserSid“, „SrcUserUid“, „SrcUserAadId“, „SrcUserOktaId“ bzw. „UserAwsId“ zu normalisieren. Weitere Informationen finden Sie unter „Die Benutzerentität“.<br><br>Beispiel: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Optional | Enumerated | Der Typ der ID, die im Feld [SrcUserId](#srcuserid) gespeichert ist. Unterstützte Werte: `SID`, `UIS`, `AADID`, `OktaId` und `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Optional | String | Der Quellbenutzername einschließlich Informationen zur Domäne, falls verfügbar. Verwenden Sie eines der folgenden Formate in der folgenden Reihenfolge nach Priorität:<br>- **UPN/E-Mail**: `johndow@contoso.com`<br>- **Windows:** `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Einfach**: `johndow`. Verwenden Sie dieses einfache Format nur, wenn keine Domäneninformationen verfügbar sind.<br><br>Speichern Sie den Benutzernamenstyp im Feld [SrcUsernameType](#srcusernametype). Wenn andere IDs verfügbar sind, empfiehlt es sich, die Feldnamen in **SrcUserUpn**, **SrcUserWindows** und **SrcUserDn** zu normalisieren.<br><br>Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `AlbertE` |
| <a name="srcusernametype"></a>**SrcUsernameType** | Optional | Enumerated | Gibt den Typ des Benutzernamens an, der im Feld [SrcUsername](#srcusername) gespeichert ist. Unterstützte Werte: `UPN`, `Windows`, `DN` und `Simple`. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `Windows` |
| **SrcUserType** | Optional | Enumerated | Der Typ des Akteurs. Zulässige Werte sind:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Speichern Sie den ursprünglichen Wert im Feld [EventOriginalSeverity](#eventoriginalseverity). |
| **SrcOriginalUserType** | | | Der ursprüngliche Quellbenutzertyp, sofern von der Quelle bereitgestellt. |
| **SrcUserDomain** | Optional | String | Dieses Feld wird lediglich aus Gründen der Rückwärtskompatibilität beibehalten. ASIM erfordert, dass Domäneninformationen, sofern verfügbar, im Feld [SrcUsername](#srcusername) enthalten sind. |
| <a name="srcappname"></a>**SrcAppName** | Optional | String | Der Name der Quellanwendung. <br><br>Beispiel: `filezilla.exe` |
| <a name="srcappid"></a>**SrcAppId** | Optional | String | Die ID der Zielanwendung, wie vom meldenden Gerät angegeben.<br><br>Beispiel: `124` |
| **SrcAppType** | Optional | String | Der Typ der Quellanwendung. Unterstützte Werte sind:<br>- `Process`<br>- `Service`<br>- `Resource`<br>- `Other`<br><br>Dieses Feld ist obligatorisch, wenn [SrcAppName](#srcappname) oder [SrcAppId](#srcappid) verwendet wird. |
| **SrcZone** | Optional | String | Die Netzwerkzone der Quelle, wie sie vom meldenden Gerät definiert wird.<br><br>Beispiel: `Internet` |
| **SrcIntefaceName** | Optional | String | Die Netzwerkschnittstelle, die vom Quellgerät für die Verbindung oder Sitzung verwendet wird. <br><br>Beispiel: `eth01` |
| **SrcInterfaceGuid** | Optional | String | Die GUID der Netzwerkschnittstelle, die auf dem Quellgerät verwendet wird.<br><br>Beispiel:<br>`46ad544b-eaf0-47ef-`<br>`827c-266030f545a6` |
| **SrcMacAddr** | Optional | String | Die MAC-Adresse der Netzwerkschnittstelle, von der aus die Verbindung oder Sitzung hergestellt wurde.<br><br>Beispiel: `06:10:9f:eb:8f:14` |
| **SrcGeoCountry** | Optional | Land | Das Land/die Region, das bzw. die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `USA` |
| **SrcGeoRegion** | Optional | Region | Die Region innerhalb eines Landes, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `Vermont` |
| **SrcGeoCity** | Optional | City | Die Stadt, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `Burlington` |
| **SrcGeoLatitude** | Optional | Breitengrad | Der Breitengrad der geografischen Koordinate, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `44.475833` |
| **SrcGeoLongitude** | Optional | Längengrad | Der Längengrad der geografischen Koordinate, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `73.211944` |
| **NetworkApplicationProtocol** | Optional | String | Das Protokoll der Anwendungsschicht, das von der Verbindung oder der Sitzung verwendet wird. Wenn der [DstPortNumber](#dstportnumber)-Wert angegeben wird, sollten Sie auch **NetworkApplicationProtocol** einschließen. Wenn der Wert nicht über die Quelle verfügbar ist, leiten Sie den Wert vom [DstPortNumber](#dstportnumber)-Wert ab.<br><br>Beispiel: `HTTP` |
| **NetworkProtocol** | Optional | Enumerated | Das IP-Protokoll, das von der Verbindung oder Sitzung verwendet wird, wie in der [IANA-Protokollzuweisung](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) aufgeführt. In der Regel `TCP`, `UDP` oder `ICMP`.<br><br>Beispiel: `TCP` |
| **NetworkDirection** | Optional | Enumerated | Die Richtung der Verbindung oder Sitzung, d. h. bei der Organisation eingehend oder ausgehend. Unterstützte Werte: `Inbound`, `Outbound` und `Listen`. `Listen` gibt an, dass ein Gerät mit der Annahme von Netzwerkverbindungen begonnen hat, aber nicht notwendigerweise verbunden ist.|
| <a name="networkduration"></a>**NetworkDuration** | Optional | Integer | Die Zeitspanne in Millisekunden für den Abschluss der Netzwerksitzung oder Verbindung.<br><br>Beispiel: `1500` |
| **Duration** | Alias | | Alias für [NetworkDuration](#networkduration) |
| **NetworkIcmpCode** | Optional | Integer | Bei einer ICMP-Nachricht der numerische Wert für den ICMP-Nachrichtentyp, wie in [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) für IPv4-Netzwerkverbindungen bzw. in [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) für IPv6-Netzwerkverbindungen beschrieben. Bei Angabe eines [NetworkIcmpType](#networkicmptype)-Werts ist dieses Feld obligatorisch. Wenn der Wert nicht über die Quelle verfügbar ist, leiten Sie den Wert stattdessen vom Feld [NetworkIcmpType](#networkicmptype) ab.<br><br>Beispiel: `34` |
|<a name="networkicmptype"></a> **NetworkIcmpType** | Optional | String | Bei einer ICMP-Nachricht die Textdarstellung für den ICMP-Nachrichtentyp, wie in [RFC 2780](https://datatracker.ietf.org/doc/html/rfc2780) für IPv4-Netzwerkverbindungen bzw. in [RFC 4443](https://datatracker.ietf.org/doc/html/rfc4443) für IPv6-Netzwerkverbindungen beschrieben.<br><br>Beispiel: `Destination Unreachable` |
| **DstBytes** | Empfohlen | Integer | Die Anzahl Bytes, die für die Verbindung oder Sitzung vom Ziel an die Quelle gesendet werden. Wenn das Ereignis aggregiert wird, sollte **DstBytes** der Summe aller aggregierten Sitzungen entsprechen.<br><br>Beispiel: `32455` |
| **SrcBytes** | Empfohlen | Integer | Die Anzahl Bytes, die für die Verbindung oder Sitzung von der Quelle an das Ziel gesendet werden. Wenn das Ereignis aggregiert wird, sollte **SrcBytes** der Summe aller aggregierten Sitzungen entsprechen.<br><br>Beispiel: `46536` |
| **NetworkBytes** | Optional | Integer | Die Anzahl Bytes, die in beide Richtungen gesendet werden. Wenn sowohl **BytesReceived** als auch **BytesSent** vorliegt, sollte **BytesTotal** ihrer Summe entsprechen. Wenn das Ereignis aggregiert wird, sollte **NetworkBytes** der Summe aller aggregierten Sitzungen entsprechen.<br><br>Beispiel: `78991` |
| **DstPackets** | Optional | Integer | Die Anzahl Pakete, die für die Verbindung oder Sitzung vom Ziel an die Quelle gesendet werden. Die Bedeutung eines Pakets wird vom meldenden Gerät definiert. Wenn das Ereignis aggregiert wird, sollte **DstPackets** der Summe aller aggregierten Sitzungen entsprechen.<br><br>Beispiel: `446` |
| **SrcPackets** | Optional | Integer | Die Anzahl Pakete, die für die Verbindung oder Sitzung von der Quelle ans Ziel gesendet werden. Die Bedeutung eines Pakets wird vom meldenden Gerät definiert. Wenn das Ereignis aggregiert wird, sollte **SrcPackets** der Summe aller aggregierten Sitzungen entsprechen.<br><br>Beispiel: `6478` |
| **NetworkPackets** | Optional | Integer | Die Anzahl von Paketen, die in beide Richtungen gesendet werden. Wenn sowohl **PacketsReceived** als auch **PacketsSent** vorliegt, sollte **BytesTotal** ihrer Summe entsprechen. Die Bedeutung eines Pakets wird vom meldenden Gerät definiert. Wenn das Ereignis aggregiert wird, sollte **NetworkPackets** der Summe aller aggregierten Sitzungen entsprechen.<br><br>Beispiel: `6924` |
|<a name="networksessionid"></a>**NetworkSessionId** | Optional | Zeichenfolge | Der Sitzungsbezeichner, der vom meldenden Gerät gemeldet wird. <br><br>Beispiel: `172\_12\_53\_32\_4322\_\_123\_64\_207\_1\_80` |
| **SessionId** | Alias | String | Alias für [NetworkSessionId](#networksessionid) |
| | | | |

### <a name="intermediary-device-fields"></a><a name="Intermediary"></a>Zwischengerätefelder

Die folgenden Felder sind nützlich, wenn der Datensatz Informationen zu einem Zwischengerät wie z. B. einer Firewall oder einem Proxy zum Vermitteln der Netzwerksitzung enthält.

| Feld | Klasse | type | BESCHREIBUNG |
| --- | --- | --- | --- |
| **DstNatIpAddr** | Optional | IP-Adresse | Bei Meldung von einem NAT-Zwischengerät die vom NAT-Gerät für die Kommunikation mit der Quelle verwendete IP-Adresse.<br><br>Beispiel: `2::1` |
| **DstNatPortNumber** | Optional | Integer | Bei Meldung von einem NAT-Zwischengerät der vom NAT-Gerät für die Kommunikation mit der Quelle verwendete Port.<br><br>Beispiel: `443` |
| **SrcNatIpAddr** | Optional | IP-Adresse | Bei Meldung von einem NAT-Zwischengerät die vom NAT-Gerät für die Kommunikation mit dem Ziel verwendete IP-Adresse.<br><br>Beispiel: `4.3.2.1` |
| **SrcNatPortNumber** | Optional | Integer | Bei Meldung von einem NAT-Zwischengerät der vom NAT-Gerät für die Kommunikation mit dem Ziel verwendete Port.<br><br>Beispiel: `345` |
| **DvcInboundInterface** | Optional | String | Bei Meldung von einem Zwischengerät die vom NAT-Gerät für die Verbindung mit dem Quellgerät verwendete Netzwerkschnittstelle.<br><br>Beispiel: `eth0` |
| **DvcOutboundInterface** | Optional | String | Bei Meldung von einem Zwischengerät die vom NAT-Gerät für die Verbindung mit dem Zielgerät verwendete Netzwerkschnittstelle.<br><br>Beispiel: `Ethernet adapter Ethernet 4e` |
| | | | |

### <a name="http-session-fields"></a><a name="http-session-fields"></a>HTTP-Sitzungsfelder

Eine HTTP-Sitzung ist eine Netzwerksitzung, die das HTTP-Protokoll verwendet. Derartige Sitzungen werden häufig von Webservern, Webproxys und Websicherheitsgateways gemeldet. Im Folgenden sind zusätzliche Felder aufgeführt, die für HTTP-Sitzungen spezifisch sind:

| Feld | Klasse | type | Beschreibung |
| --- | --- | --- | --- |
| **Url** | Empfohlen | String | Bei HTTP-/HTTPS-Netzwerksitzungen die vollständige HTTP-Anforderungs-URL, einschließlich Parametern. Dieses Feld ist obligatorisch, wenn das Ereignis eine HTTP-Sitzung darstellt.<br><br>Beispiel: `https://contoso.com/fo/?k=v&amp;q=u#f` |
| **UrlCategory** | Optional | String | Die definierte Gruppierung einer URL oder der Domänenteil der URL. Die Kategorie wird häufig von Websicherheitsgateways bereitgestellt und basiert auf dem Inhalt der Website, auf die die URL verweist.<br><br>Beispiel: Suchmaschinen, Erwachsenen-, Nachrichten-, Werbe- und geparkte Domänen. |
| **UrlOriginal** | Optional | String | Der ursprüngliche Wert der URL, wenn die URL vom meldenden Gerät geändert wurde und beide Werte angegeben werden. |
| **HttpVersion** | Optional | String | Die HTTP-Anforderungsversion für HTTP/HTTPS-Netzwerkverbindungen.<br><br>Beispiel: `2.0` |
| **HttpRequestMethod** | Empfohlen | Enumerated | Die HTTP-Methode für HTTP/HTTPS-Netzwerksitzungen. Die Werte entsprechen der Definition in [RFC 7231](https://datatracker.ietf.org/doc/html/rfc7231#section-4) und [RFC 5789](https://datatracker.ietf.org/doc/html/rfc5789#section-2)und umfassen `GET`, `HEAD`, `POST`, `PUT`, `DELETE`, `CONNECT`, `OPTIONS`, `TRACE` und `PATCH`.<br><br>Beispiel: `GET` |
| **HttpStatusCode** | Alias | | Der HTTP-Statuscode für HTTP/HTTPS-Netzwerksitzungen. Alias für [EventResultDetails](#eventresultdetails). |
| <a name="httpcontenttype"></a>**HttpContentType** | Optional | String | Der Content-Type-Header der HTTP-Antwort für HTTP/HTTPS-Netzwerksitzungen. <br><br>**Hinweis**: Das Feld **HttpContentType** kann sowohl das Inhaltsformat als auch zusätzliche Parameter enthalten, z. B. die Codierung, die zum Abrufen des tatsächlichen Formats verwendet wird.<br><br> Beispiel: `text/html; charset=ISO-8859-4` |
| **HttpContentFormat** | Optional | String | Der Inhaltsformatteil von [HttpContentType](#httpcontenttype) <br><br> Beispiel: `text/html` |
| **HttpReferrer** | Optional | String | Der HTTP-Referrer-Header für HTTP/HTTPS-Netzwerksitzungen.<br><br>**Hinweis**: ASIM verwendet die gemäß OSSEM richtige Schreibweise für *Referrer* und nicht die ursprüngliche HTTP-Headerschreibweise.<br><br>Beispiel: `https://developer.mozilla.org/docs` |
| <a name="httpuseragent"></a>**HttpUserAgent** | Optional | String | Der HTTP-Benutzer-Agent-Header für HTTP/HTTPS-Netzwerksitzungen.<br><br>Beispiel:<br> `Mozilla/5.0` (Windows NT 10.0; WOW64)<br>`AppleWebKit/537.36` (KHTML, wie Gecko)<br>`Chrome/83.0.4103.97 Safari/537.36` |
| **UserAgent** | Alias | | Alias für [HttpUserAgent](#httpuseragent) |
| **HttpRequestXff** | Optional | IP-Adresse | Der HTTP X-Forwarded-For-Header für HTTP/HTTPS-Netzwerksitzungen.<br><br>Beispiel: `120.12.41.1` |
| **HttpRequestTime** | Optional | Integer | Die Zeitspanne in Millisekunden, die zum Senden der Anforderung an den Server benötigt wurde, falls zutreffend.<br><br>Beispiel: `700` |
| **HttpResponseTime** | Optional | Integer | Die Zeitspanne in Millisekunden, die zum Empfangen einer Antwort auf dem Server benötigt wurde, falls zutreffend.<br><br>Beispiel: `800` |
| **FileName** | Optional | String | Bei HTTP-Uploads der Name der hochgeladenen Datei. |
| **FileMD5** | Optional | MD5 | Bei HTTP-Uploads der MD5-Hash der hochgeladenen Datei.<br><br>Beispiel: `75a599802f1fa166cdadb360960b1dd0` |
| **FileSHA1** | Optional | SHA1 | Bei HTTP-Uploads der SHA1-Hash der hochgeladenen Datei.<br><br>Beispiel:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
| **FileSHA256** | Optional | SHA256 | Bei HTTP-Uploads der SHA256-Hash der hochgeladenen Datei.<br><br>Beispiel:<br>`e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **FileSHA512** | Optional | SHA512 | Bei HTTP-Uploads der SHA512-Hash der hochgeladenen Datei. |
| **FileSize** | Optional | Integer | Bei HTTP-Uploads die Größe der hochgeladenen Datei in Bytes. |
| **FileContentType** | Optional | String | Bei HTTP-Uploads der Inhaltstyp der hochgeladenen Datei. |
| | | | |

Darüber hinaus gelten für die folgenden Standardfelder des Netzwerkschemas zusätzliche Richtlinien, wenn sie für eine HTTP-Sitzung verwendet werden:

- **EventType** muss &quot;HTTPSession&quot; lauten.
- **EventResultDetails** muss auf den HTTP-Statuscode festgelegt werden.
- **EventResult** muss für HTTP-Statuscodes, die niedriger als 400 sind, &quot;Success&quot; und andernfalls &quot;Failure&quot; lauten.

### <a name="inspection-fields"></a><a name="inspection-fields"></a>Inspektionsfelder

Die folgenden Felder werden zur Darstellung der Inspektion verwendet, die von einem Sicherheitsgerät wie einer Firewall, einem IPS oder einem Websicherheitsgateway durchgeführt wurde:

| Feld | Klasse | type | Beschreibung |
| --- | --- | --- | --- |
| **NetworkRuleName** | Optional | String | Der Name oder die ID der Regel, anhand derer die Entscheidung für [DvcAction](#dvcaction) getroffen wurde.<br><br> Beispiel: `AnyAnyDrop` |
| **NetworkRuleNumber** | Optional | Integer | Die Nummer der Regel, anhand derer die Entscheidung für [DvcAction](#dvcaction) getroffen wurde.<br><br>Beispiel: `23` |
| **Regel** | Obligatorisch. | String | Entweder `NetworkRuleName` oder `NetworkRuleNumber` |
| <a name="dvcaction"></a>**DvcAction** | Optional | Enumerated | Die für die Netzwerksitzung vorgenommene Aktion. Diese Werte werden unterstützt:<br>- `Allow`<br>- `Deny`<br>- `Drop`<br>- `Drop ICMP`<br>- `Reset`<br>- `Reset Source`<br>- `Reset Destination`<br>- `Encrypt`<br>- `Decrypt`<br>- `VPNroute`<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Der ursprüngliche Wert muss im Feld [DvcOriginalAction](#dvcoriginalaction) gespeichert werden.<br><br>Beispiel: `drop` |
| <a name="dvcoriginalaction"></a>**DvcOriginalAction** | Optional | String | Die ursprüngliche [DvcAction](#dvcaction), wie vom meldenden Gerät angegeben. |
| **ThreatId** | Optional | String | Die ID der in der Netzwerksitzung identifizierten Bedrohung oder Schadsoftware.<br><br>Beispiel: `Tr.124` |
| **ThreatName** | Optional | String | Der Name der in der Netzwerksitzung identifizierten Bedrohung oder Schadsoftware.<br><br>Beispiel: `EICAR Test File` |
| **ThreatCategory** | Optional | String | Die Kategorie der in der Netzwerksitzung identifizierten Bedrohung oder Schadsoftware.<br><br>Beispiel: `Trojan` |
| **ThreatRiskLevel** | Optional | Integer | Die der Sitzung zugeordnete Risikostufe. Hierbei muss es sich um eine Zahl zwischen **0** und **100** handeln.<br><br>**Hinweis**: Der Wert kann im Quelldatensatz in einer anderen Skala angegeben werden, die auf diese Skala normalisiert werden sollte. Der ursprüngliche Wert muss im Feld [ThreatRiskLevelOriginal](#threatriskleveloriginal) gespeichert werden. |
| <a name="threatriskleveloriginal"></a>**ThreatRiskLevelOriginal** | Optional | String | Die Risikostufe, wie vom meldenden Gerät gemeldet. |
| | | | |

### <a name="other-fields"></a>Weitere Felder

Wenn das Ereignis von einem der Endpunkte der Netzwerksitzung gemeldet wird, kann es Informationen über den Prozess enthalten, der die Sitzung initiiert oder beendet hat. In solchen Fällen wird das [ASIM-Prozessereignisschema](process-events-normalization-schema.md) zum Normalisieren dieser Informationen verwendet.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Normalisierung in Microsoft Sentinel](normalization.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung der Authentifizierung (Public Preview)](authentication-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Dateiereignissen (Public Preview)](file-event-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum DNS-Normalisierungsschema](dns-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Prozessereignissen](process-events-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Registrierungsereignissen (Public Preview)](registry-event-normalization-schema.md)
