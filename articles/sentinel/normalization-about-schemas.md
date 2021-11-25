---
title: Schemas für das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) | Microsoft-Dokumentation
description: In diesem Artikel werden die Schemas für das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) beschrieben und erläutert, wie sie ASIM dabei unterstützen, Daten aus vielen verschiedenen Quellen in eine einheitliche Darstellung zu normalisieren.
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
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: ofshezaf
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5a315d02b29e113958c1bf2e9004e33b9021caa7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520311"
---
# <a name="advanced-siem-information-model-asim-schemas-public-preview"></a>Schemas für das erweiterte SIEM-Informationsmodell (Advanced SIEM Information Model, ASIM) (öffentliche Vorschau)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Ein [ASIM](normalization.md) Schema ist ein Satz von Feldern, die eine Aktivität darstellen. Die Verwendung der Felder eines normalisierten Schemas in einer Abfrage stellt sicher, dass die Abfrage mit jeder normalisierten Quelle verwendet werden kann.

Schemaverweise beschreiben die Felder, die jedes Schema umfasst. In ASIM sind derzeit die folgenden Schemas definiert:

 - [Netzwerksitzung](normalization-schema.md)
 - [DNS-Aktivität](dns-normalization-schema.md)
 - [DHCP-Aktivität](dhcp-normalization-schema.md)
 - [Prozessereignis](process-events-normalization-schema.md)
 - [Authentifizierungsereignis](authentication-normalization-schema.md)
 - [Registrierungsereignis](registry-event-normalization-schema.md)
 - [Dateiaktivität](file-event-normalization-schema.md)

> [!IMPORTANT]
> ASIM ist zurzeit als VORSCHAU verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="schema-concepts"></a>Schemakonzepte

Die folgenden Konzepte helfen Ihnen, die Schema-Verweisdokumente zu verstehen und erweitern das Schema auf normalisierte Weise, falls Ihre Daten Informationen enthalten, die das Schema nicht abdeckt.


|Konzept  |BESCHREIBUNG  |
|---------|---------|
|**Feldnamen**     |   Der Kern jedes Schemas sind die Feldnamen. Feldnamen lassen sich in folgende Gruppen einordnen: <br><br>- Felder, die allen Schemas gemein sind <br>- Felder, die für ein Schema spezifisch sind <br>- Felder, die Entitäten darstellen, z. B. Benutzer, die am Schema mitwirken. Felder, die Entitäten darstellen, [sind in allen Schemas ähnlich](#entities). <br><br>Wenn Quellen Felder enthalten, die nicht im dokumentierten Schema angegeben sind, werden sie zur Wahrung der Konsistenz normalisiert. Wenn die zusätzlichen Felder eine Entität darstellen, werden sie basierend auf den Leitfäden für Entitätsfelder normalisiert. Andernfalls wird in den Schemas versucht, die Konsistenz über alle Schemas hinweg zu wahren.<br><br> Beispielsweise enthalten die Aktivitätsprotokolle von DNS-Servern keine Benutzerinformationen, während die DNS-Aktivitätsprotokolle eines Endpunkts Benutzerinformationen enthalten können, die entsprechend den Leitfäden für die Benutzerentität normalisiert werden können.      |
|**Feldtypen**     |  Jedem Schemafeld ist ein Typ zugeordnet. Der Log Analytics-Arbeitsbereich verfügt über eine begrenzte Anzahl von Datentypen. Daher wird in Microsoft Sentinel für viele Schemafelder ein logischer Typ verwendet, der in Log Analytics nicht erzwungen wird, jedoch für die Schemakompatibilität erforderlich ist. Durch logische Feldtypen wird sichergestellt, dass Werte sowie Feldnamen in allen Quellen konsistent sind.  <br><br>Weitere Informationen finden Sie unter [Logische Typen](#logical-types).     |
|**Feldklasse**     |Felder können mehrere Klassen beinhalten, die definieren, wann die Felder durch einen Parser implementiert werden sollen: <br><br>-    **Erforderliche Felder** müssen in jedem Parser angezeigt werden. Wenn eine Quelle keine Informationen für diesen Wert enthält oder die Daten nicht anderweitig hinzugefügt werden können, werden die meisten Inhaltselemente, die auf das normalisierte Schema verweisen, nicht unterstützt.<br>-  **Empfohlene** Felder sollten normalisiert werden, falls sie verfügbar sind. Es kann jedoch sein, dass sie nicht in jeder Quelle verfügbar sind, sodass bei jedem Inhaltselement, das auf dieses normalisierte Schema verweist, die Verfügbarkeit berücksichtigt werden muss. <br>-  **Optionale** Felder können, falls vorhanden, normalisiert oder in ihrer ursprünglichen Form belassen werden. Normalerweise werden sie aus Leistungsgründen durch einen sehr einfachen Parser nicht normalisiert.    |
|**Entitäten**     | Ereignisse ergeben sich im Zusammenhang mit Entitäten wie Benutzern, Hosts, Prozessen oder Dateien. Zur Beschreibung jeder Entität können mehrere Felder erforderlich sein. Ein Host kann beispielsweise einen Namen und eine IP-Adresse haben. <br><br>Ein einzelner Datensatz kann mehrere Entitäten desselben Typs enthalten, z. B. einen Quell- und Zielhost. <br><br>Das erweiterte SIEM-Informationsmodell definiert, wie Entitäten konsistent zu beschreiben sind. Mit Entitäten lassen sich die Schemas erweitern. <br><br>Während das Netzwerksitzungsschema beispielsweise keine Prozessinformationen enthält, enthalten einige Ereignisquellen Prozessinformationen, die hinzugefügt werden können. Weitere Informationen finden Sie unter [Entitäten](#entities). |
|**Aliase**     |  In einigen Fällen erwarten verschiedene Benutzer, dass ein Feld unterschiedliche Namen hat. In der DNS-Terminologie wird z. B. ein Feld mit dem Namen `query` erwartet, während es in allgemeineren Anwendungsfällen einen Domänennamen enthält. Mit Aliasen lässt sich dieses Problem der Mehrdeutigkeit lösen, dadurch dass mehrere Namen für einen angegebenen Wert zugelassen werden. Die Aliasklasse entspricht der des Felds, für das sie als Alias verwendet wird.<br><br>Beachten Sie, dass Aliasing in Log Analytics nicht unterstützt wird. Um Aliase zu implementieren, erstellen Parser über den `extend`-Operator eine Kopie des ursprünglichen Werts.        |
| | |

## <a name="logical-types"></a>Logische Typen

Jedem Schemafeld ist ein Typ zugeordnet. Einige Felder verfügen über integrierte Azure Log Analytics-Typen wie `string`, `int`, `datetime` oder `dynamic`. Andere Felder verfügen über einen logischen Typ, der angibt, wie die Feldwerte normalisiert werden sollen.

|Datentyp  |Physischer Typ  |Format und Wert  |
|---------|---------|---------|
|**Boolean**     |   Bool      |    Verwenden Sie den integrierten KQL-Datentyp `bool` anstelle einer numerischen oder Zeichenfolgendarstellung boolescher Werte.     |
|**Enumeration**     |  String       |   Eine Liste von Werten, die explizit für das Feld definiert sind. In der Schemadefinition sind die akzeptierten Werte aufgeführt.      |
|**Datum/Uhrzeit**     |  Verwenden Sie je nach Erfassungsmethode eine der folgenden physischen Darstellungen in absteigender Priorität: <br><br>- Integrierter datetime-Typ von Log Analytics <br>- Integerfeld, das die numerische datetime-Darstellung von Log Analytics verwendet. <br>- Zeichenfolgenfeld, das die numerische datetime-Darstellung von Log Analytics verwendet <br>- Zeichenfolgenfeld, in dem ein unterstütztes [datetime-Format von Log Analytics](/azure/data-explorer/kusto/query/scalar-data-types/datetime) gespeichert wird.       |  Die [Darstellung von Datums- und Uhrzeitwerten in Log Analytics](/azure/kusto/query/scalar-data-types/datetime) ähnelt der UNIX-Darstellung, weist aber Unterschiede auf. Weitere Informationen finden Sie in den [Konvertierungsleitfäden](/azure/kusto/query/datetime-timespan-arithmetic). <br><br>**Hinweis:** Die Zeitwerte sollten gegebenenfalls an die Zeitzone angepasst sein. |
|**MAC-Adresse**    |  String       | Hexadezimalnotation mit Doppelpunkt        |
|**IP-Adresse**     |String         |    Microsoft Sentinel-Schemas enthalten keine separaten IPv4- und IPv6-Adressen. Jedes IP-Adressfeld kann wie folgt entweder eine IPv4-Adresse oder eine IPv6-Adresse enthalten: <br><br>- **IPv4** in einer Punkt-Dezimal-Notation<br>- **IPv6** in 8-Hextett-Notation, die die Kurzform ermöglicht<br><br>Zum Beispiel:<br>- **IPv4**: `192.168.10.10` <br>- **IPv6**: `FEDC:BA98:7654:3210:FEDC:BA98:7654:3210`<br>- **IPv6-Kurzform**: `1080::8:800:200C:417A`     |
|**FQDN**        |   Zeichenfolge      |    Ein vollqualifizierter Domänenname mit Punktnotation, z. B. `docs.microsoft.com` |
|**Country**     |   String      |    Eine Zeichenfolge unter Verwendung von [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html), entsprechend der folgenden Priorität: <br><br> - Alpha-2-Codes, z. B. `US` für die USA <br> - Alpha-3-Codes, z. B. `USA` für die USA) <br>- Kurzname<br><br>Die Liste der Codes finden Sie auf der [Website der International Standards Organization (ISO)](https://www.iso.org/obp/ui/#search)|
|**Region**     | String        |   Der Name der Untereinheiten von Ländern nach ISO 3166-2<br><br>Die Liste der Codes finden Sie auf der [Website der International Standards Organization (ISO)](https://www.iso.org/obp/ui/#search)|
|**City (Ort)**     |  String       |         |
|**Longitude** (Längengrad)     | Double        |  ISO 6709-Koordinatendarstellung (Dezimalwert mit Vorzeichen)       |
|**Latitude** (Breitengrad)     | Double        |    ISO 6709-Koordinatendarstellung (Dezimalwert mit Vorzeichen)     |
|**MD5**     |    String     |  32-Hexadezimalzeichen       |
|**SHA1**     |   String      | 40-Hexadezimalzeichen        |
|**SHA256**     | String        |  64-Hexadezimalzeichen       |
|**SHA512**     |   String      |  128-Hexadezimalzeichen       |
| | | |

## <a name="common-fields"></a><a name="common"></a>Allgemeine Felder

Einige Felder sind in allen ASIM-Schemas enthalten. Beachten Sie, dass jedes Schema Richtlinien für die Verwendung einiger der allgemeinen Felder im Kontext des jeweiligen Schemas hinzufügen kann. Beispielsweise können die zulässigen Werte für das Feld **EventType** und für das Feld **EventSchemaVersion** je nach Schema variieren.

Die folgenden Felder werden von Log Analytics für die einzelnen Datensätze generiert und können beim [Erstellen eines benutzerdefinierten Connectors](create-custom-connector.md) überschrieben werden.

| Feld         | type     | Diskussion (Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | datetime | Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde.|
| **_ResourceId**   | guid     | Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden. |
| **Type** | String | Die ursprüngliche Tabelle, aus der der Datensatz abgerufen wurde. Dieses Feld ist nützlich, wenn dasselbe Ereignis über mehrere Kanäle in unterschiedlichen Tabellen empfangen werden kann und die gleichen [EventVendor](#eventvendor)- und [EventProduct](#eventproduct)-Werte vorliegen.<br><br>Ein Sysmon-Ereignis kann z. B. entweder in der Tabelle `Event` oder in der Tabelle `WindowsEvent` gesammelt werden. |
| | | |

> [!NOTE]
> Log Analytics fügt auch andere Felder hinzu, die für Sicherheitsanwendungsfälle weniger relevant sind. Weitere Informationen finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../azure-monitor/logs/log-standard-columns.md).
>


Die folgenden Felder werden von ASIM für alle Schemas definiert:

| Feld               | Klasse       | type       |  BESCHREIBUNG        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | Optional    | String     |     Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert.   |
| **EventCount**          | Obligatorisch.   | Integer    |     Die Anzahl der Ereignisse, die vom Datensatz beschrieben werden. <br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen. <br><br>Legen Sie den Wert für andere Quellen auf `1` fest.   |
| **EventStartTime**      | Obligatorisch.   | Datum/Uhrzeit  |      Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, gibt dieses Feld die Zeit an, zu der das erste Ereignis generiert wurde. <br><br>Andernfalls wird in diesem Feld ein Alias für das Feld [TimeGenerated](#timegenerated) verwendet. |
| **EventEndTime**        | Obligatorisch.   | Alias      |      Alias für das Feld [TimeGenerated](#timegenerated).    |
|  <a name="eventtype"></a>**EventType**           | Obligatorisch.   | Enumerated |    Beschreibt den vom Datensatz gemeldeten Vorgang. Jedes Schema definiert die Liste der für dieses Feld gültigen Werte. |
| **EventSubType** | Optional | Enumerated | Beschreibt eine Untereinheit des Vorgangs, der im Feld [EventType](#eventtype) gemeldet wird. Jedes Schema definiert die Liste der für dieses Feld gültigen Werte. |
| <a name="eventresult"></a>**EventResult** | Obligatorisch. | Enumerated | Einer der folgenden Werte: **Success**, **Partial**, **Failure**, **NA** (Not Applicable/nicht zutreffend).<br> <br>Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die in diese Werte normalisiert werden sollten. Alternativ kann die Quelle auch nur das Feld [EventResultDetails](#eventresultdetails) angeben, das analysiert werden sollte, um den EventResult-Wert abzuleiten.<br><br>Beispiel: `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | Obligatorisch. | Alias | Grund oder Details für das im Feld [**EventResult**](#eventresult) gemeldete Ergebnis. Jedes Schema definiert die Liste der für dieses Feld gültigen Werte.<br><br>Beispiel: `NXDOMAIN`|
| **EventOriginalUid**    | Optional    | String     |   Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Optional    | String     |   Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt. Dieses Feld wird beispielsweise verwendet, um die ursprüngliche Windows-Ereignis-ID zu speichern.<br><br>Beispiel: `4624`|
| <a name="eventproduct"></a>**EventProduct**        | Obligatorisch.   | String     |             Das Produkt, das das Ereignis erzeugt. <br><br>Beispiel: `Sysmon`<br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.           |
| **EventProductVersion** | Optional    | String     | Die Version des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `12.1`      |
| <a name="eventvendor"></a>**EventVendor**         | Obligatorisch.   | String     |           Der Hersteller des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `Microsoft`  <br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.  |
| **EventSchemaVersion**  | Obligatorisch.   | String     | Die Version des Schemas. In jedem Schema ist die aktuelle Version dokumentiert.         |
| **EventReportUrl**      | Optional    | String     | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die zusätzliche Informationen zum Ereignis enthält.|
| <a name="dvc"></a>**Dvc** | Obligatorisch.       | String     | Je nach Schema ist dies ein eindeutiger Bezeichner des Geräts, auf dem das Ereignis eingetreten ist oder das das Ereignis gemeldet hat. <br><br>Dieses Feld kann ein Alias für die Felder [DvcFQDN](#dvcfqdn), [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die kein offensichtliches Gerät vorliegt, denselben Wert wie im Feld [Ereignisprodukt](#eventproduct).            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Empfohlen | IP-Adresse | Je nach Schema ist dies die IP-Adresse des Geräts, auf dem das Ereignis eingetreten ist oder das das Ereignis gemeldet hat. <br><br>Beispiel: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Empfohlen | Hostname   | Je nach Schema ist dies der Hostname des Geräts, auf dem das Ereignis eingetreten ist oder das das Ereignis gemeldet hat. <br><br>Beispiel: `ContosoDc.Contoso.Azure`               |
| <a name="dvcdomain"></a>**DvcDomain** | Empfohlen | String | Je nach Schema ist dies die Domäne des Geräts, auf dem das Ereignis eingetreten ist oder das das Ereignis gemeldet hat.<br><br>Beispiel: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Empfohlen | Enumerated | Der Typ von [DvcDomain](#dvcdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows`, z. B. für `contoso\mypc`<br>- `FQDN`, z. B. für `docs.microsoft.com`<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [DvcDomain](#dvcdomain) verwendet wird. |
| <a name="dvcfqdn"></a>**DvcFQDN** | Optional | String | Je nach Schema ist dies der Hostname des Geräts, auf dem das Ereignis eingetreten ist oder das das Ereignis gemeldet hat. <br><br> Beispiel: `Contoso\DESKTOP-1282V4D`<br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. Das Feld [DvcDomainType](#dvcdomaintype) spiegelt das verwendete Format wider.  |
| <a name ="dvcid"></a>**DvcId**               | Optional    | String     | Je nach Schema ist dies die eindeutige ID des Geräts, auf dem das Ereignis eingetreten ist oder das das Ereignis gemeldet hat. <br><br>Beispiel: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcIdType** | Optional | Enumerated | Der Typ von [DvcId](#dvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der Liste, und speichern Sie die anderen anhand der Feldnamen **DvcAzureResourceId** bzw. **DvcMDEid**.<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [DvcId](#dvcid) verwendet wird. |
| **DvcMacAddr**          | Optional    | MAC        |   Die MAC-Adresse des Geräts, auf dem das Ereignis eingetreten ist.  <br><br>Beispiel: `00:1B:44:11:3A:B7`       |
| **DvcZone** | Optional | String | Je nach Schema ist dies das Netzwerk, in dem das Ereignis eingetreten ist oder das das Ereignis gemeldet hat. Die Zone wird vom meldenden Gerät definiert.<br><br>Beispiel: `Dmz` |
| **DvcOs**               | Optional    | String     |         Das Betriebssystem, das auf dem Gerät ausgeführt wird, auf dem das Ereignis eingetreten ist.    <br><br>Beispiel: `Windows`    |
| **DvcOsVersion**        | Optional    | String     |   Die Version des Betriebssystems auf dem Gerät, auf dem das Ereignis eingetreten ist. <br><br>Beispiel: `10` |
| **DvcAction** | Optional | String | Bei meldenden Sicherheitssystemen ist dies die vom System ausgeführte Aktion (falls zutreffend). <br><br>Beispiel: `Blocked` |
| <a name="additionalfields"></a>**AdditionalFields**    | Optional    | Dynamisch    | Wenn Ihre Quelle zusätzliche Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie ihm die zusätzlichen Informationen als Schlüssel-Wert-Paare hinzu.    |
| | | | |

> [!NOTE]
> Log Analytics fügt auch andere Felder hinzu, die für Sicherheitsanwendungsfälle weniger relevant sind. Weitere Informationen finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../azure-monitor/logs/log-standard-columns.md).
>


## <a name="entities"></a>Entitäten

Ereignisse entwickeln sich im Zusammenhang mit Entitäten wie Benutzern, Hosts, Prozessen oder Dateien. Die Entitätsdarstellung ermöglicht, dass mehrere Entitäten desselben Typs Teil eines einzelnen Datensatzes sind, und sie unterstützt mehrere Attribute für dieselben Entitäten.

Zur Ermöglichung von Entitätsfunktionen gelten für die Entitätsdarstellung die folgenden Richtlinien:

|Vorgabe  |BESCHREIBUNG  |
|---------|---------|
|**Deskriptoren und Aliase**     | Da ein einzelnes Ereignis häufig mehrere Entitäten desselben Typs enthält, z. B. Quell- und Zielhosts, werden *Deskriptoren* als Präfix verwendet, um alle Felder zu identifizieren, die einer bestimmten Entität zugeordnet sind. <br><br>Zur Aufrechterhaltung der Normalisierung wird im erweiterten SIEM-Informationsmodell eine kleine Gruppe von Standarddeskriptoren verwendet, die für die spezifische Rolle der Entitäten am besten geeignet sind.  <br><br>Wenn eine einzelne Entität eines Typs für ein Ereignis relevant ist, muss kein Deskriptor verwendet werden. Außerdem wird ein Satz von Feldern ohne Deskriptor als Alias für die am häufigsten verwendete Entität für jeden Typ verwendet.  |
|**Bezeichner und Typen**     | Ein normalisiertes Schema ermöglicht mehrere Bezeichner für jede Entität, die voraussichtlich in Ereignissen gleichzeitig vorhanden sind. Wenn das Quellereignis andere Entitätsbezeichner enthält, die nicht dem normalisierten Schema zugeordnet werden können, behalten Sie sie im Quellformat bei, oder verwenden Sie das dynamische Feld `AdditionalFields`. <br><br>Zum Verwalten der Typinformationen für die Bezeichner speichern Sie den Typ ggf. in einem Feld mit dem gleichen Namen und dem Suffix `Type`. Beispiel: `UserIdType`.         |
|**Attribute**     |   Entitäten umfassen häufig andere Attribute, die nicht als Bezeichner dienen und auch mit einem Deskriptor qualifiziert werden können. Wenn der Quellbenutzer z. B. Domäneninformationen enthält, lautet das normalisierte Feld `SrcUserDomain`.      |
| | |

Mit jedem Schema werden explizit die zentralen Entitäten und Entitätsfelder definiert. Die folgenden Richtlinien helfen Ihnen, die zentralen Schemafelder zu verstehen, und zeigen Ihnen, wie Sie Schemas auf normalisierte Weise mit anderen Entitäten oder Entitätsfeldern erweitern können, die nicht explizit im Schema definiert sind.

### <a name="the-user-entity"></a>Die Benutzerentität

Für einen Benutzer werden wie in den folgenden Szenarien beschrieben die Deskriptoren **Akteur**, **Zielbenutzer** und **Aktualisierter Benutzer** verwendet:

|Aktivität  |Vollständiges Szenario  |Szenario mit einzelner Entität, das für Aliasing verwendet wird  |
|---------|---------|---------|
|**Benutzer erstellen**     |  Ein **Akteur** hat einen **Zielbenutzer** erstellt oder geändert       |  Der (Ziel-)**Benutzer** wurde erstellt.       |
|**Ändern eines Benutzers**     |   Ein **Akteur** hat einen **Zielbenutzer** in einen **aktualisierten Benutzer** umbenannt. Der **aktualisierte Benutzer** verfügt normalerweise nicht über alle Informationen, die einem Benutzer zugeordnet sind, und weist einige Überschneidungen mit dem **Zielbenutzer** auf.      |         |
|**Netzwerkverbindung**     |    Ein Prozess, der als **Akteur** auf dem Quellhost ausgeführt wird, kommuniziert mit einem Prozess, der als **Zielbenutzer** auf dem Zielhost ausgeführt wird     |         |
|**DNS-Anforderung**     | Ein **Akteur** hat eine DNS-Abfrage initiiert        |         |
|**Anmelden**     |    Ein **Akteur** hat sich als **Zielbenutzer** bei einem System angemeldet.     |Ein (Ziel-)Benutzer hat sich angemeldet         |
|**Prozesserstellung**     |   Ein **Akteur** (der dem initiierenden Prozess zugeordnete Benutzer) hat die Prozesserstellung initiiert. Der erstellte Prozess wird unter den Anmeldeinformationen eines **Zielbenutzers** (der dem Zielprozess zugeordnete Benutzer) ausgeführt.      |  Der erstellte Prozess wird unter den Anmeldeinformationen eines (Ziel-)**Benutzers** ausgeführt.       |
|**E-Mail**     |     Ein **Akteur** sendet eine E-Mail an einen **Zielbenutzer**    |         |
| | | |

In der folgenden Tabelle werden die unterstützten Bezeichner für einen Benutzer beschrieben:

|Normalisiertes Feld  |type  |Format und unterstützte Typen  |
|---------|---------|---------|
|**UserId**     |    String     |   Eine maschinenlesbare, alphanumerische, eindeutige Darstellung eines Benutzers in einem System. <br><br>Format und unterstützte Typen:<br>    - **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID** (Linux): `4578`<br>    -    **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId**: `00urjk4znu3BcncfY0h7`<br>    - **AWSId**: `72643944673`<br><br>    Speichern Sie den ID-Typ im Feld `UserIdType`. Wenn andere IDs verfügbar sind, empfiehlt es sich, die Feldnamen auf `UserSid`, `UserUid`, `UserAADID`, `UserOktaId` bzw. `UserAwsId` zu normalisieren.       |
|**Benutzername**     |  String       |   Ein Benutzername, ggf. einschließlich der Domäneninformationen, in einem der folgenden Formate und in der folgenden Prioritätsreihenfolge: <br> -   **UPN/E-Mail**: `johndow@contoso.com` <br>  -    **Windows:** `Contoso\johndow` <br> -   **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **Einfach**: `johndow`. Verwenden Sie dieses Format nur, wenn keine Domäneninformationen verfügbar sind. <br><br> Speichern Sie den Benutzernamenstyp im Feld `UsernameType`.    |
| | | |


### <a name="the-process-entity"></a>Prozessentität

Für einen Prozess werden wie in den folgenden Szenarien beschrieben die Deskriptoren `Acting Process`, `Target Process` und `Parent Process` verwendet:

- **Netzwerkverbindung**. Ein **aktiver Prozess** hat eine Netzwerkverbindung zur Kommunikation mit einem **Zielprozess** auf einem Remotesystem initiiert.
- **DNS-Anforderung**.  Ein **aktiver Prozess** hat eine DNS-Abfrage initiiert
- **Anmeldung**.  Ein **aktiver Prozess** hat eine Anmeldung bei einem Remotesystem initiiert, das einen **Zielprozess** in seinem Namen ausgeführt hat.
- **Prozesserstellung**. Ein **aktiver Prozess** hat die Erstellung eines **Zielprozesses** initiiert. Ein **übergeordneter Prozess** ist dem aktiven Prozess übergeordnet.

In der folgenden Tabelle werden die unterstützten Bezeichner für Prozesse beschrieben:

|Normalisiertes Feld  |type  |Format und unterstützte Typen  |
|---------|---------|---------|
|**Id**     |    String     |   Die vom Betriebssystem zugewiesene Prozess-ID.      |
|**Guid**     |  String       |   Die vom Betriebssystem zugewiesene Prozess-GUID. Die GUID ist im Allgemeinen bei Systemneustarts eindeutig, wohingegen die ID häufig wiederverwendet wird.   |
|**Path**     |    String     |   Der vollständige Pfadname des Prozesses, einschließlich Verzeichnis- und Dateiname.       |
|**Name**     |  Alias       |  Der Prozessname ist ein Alias für den Pfad.   |
| | | |


Weitere Informationen finden Sie unter [Microsoft Sentinel: Referenz zum Prozessereignis-Normalisierungsschema (öffentliche Vorschau)](process-events-normalization-schema.md).

### <a name="the-device-entity"></a>Geräteentität

Mit den Normalisierungsschemas wird versucht, der Intuition des Benutzers möglichst umfassend zu folgen. Daher werden Geräte je nach Szenario auf unterschiedliche Weise behandelt:

- Wenn der Ereigniskontext ein Quell- und ein Zielgerät impliziert, werden die Deskriptoren `Src` und `Target` verwendet. In diesen Fällen wird der Deskriptor `Dvc` für das meldende Gerät verwendet.

- Für Ereignisse einzelner Geräte, z. B. Ereignisse des lokalen Betriebssystems, wird der Deskriptor `Dvc` verwendet.

- Wenn im Ereignis auf ein anderes Gatewaygerät verwiesen wird und sich der Wert vom meldenden Gerät unterscheidet, wird der Deskriptor `Gateway` verwendet.

Die Leitfäden zur Verarbeitung von Geräten werden wie folgt näher erläutert:

- **Netzwerkverbindung**. Eine Verbindung zwischen einem **Quellengerät** (`Src`) und einem **Zielgerät** (`Target`) wurde hergestellt. Die Verbindung wurde von einem (meldenden) **Gerät** (`Dvc`) gemeldet.
- **Netzwerkverbindung mit Proxy:** . Eine Verbindung zwischen einem **Quellengerät** (`Src`) und einem **Zielgerät** (`Target`) wurde über ein **Gatewaygerät** (`Gateway`) hergestellt. Die Verbindung wurde von einem (meldenden) **Gerät** gemeldet.
- **DNS-Anforderung**.  Eine DNS-Abfrage wurde über ein **Quellengerät** (`Src`) initiiert.
- **Anmeldung:** Eine Anmeldung wurde über ein **Quellengerät** (`Src`) bei einem Remotesystem auf einem **Zielgerät** (`Target`) initiiert.
- **Prozess:** Ein Prozess wurde auf einem **Gerät** (`Dvc`) initiiert.

In der folgenden Tabelle werden die unterstützten Bezeichner für Geräte beschrieben:

|Normalisiertes Feld  |type  |Format und unterstützte Typen  |
|---------|---------|---------|
|**Hostname**     |    String     |        |
|**FQDN**     |  String       |   Ein Vollqualifizierter Domänenname   |
|**IpAddr**     |    IP-Adresse     |   Während Geräte mehrere IP-Adressen haben können, weisen Ereignisse normalerweise eine einzelne identifizierende IP-Adresse auf. Eine Ausnahme bildet ein Gatewaygerät, das über zwei relevante IP-Adressen verfügen kann. Verwenden Sie `UpstreamIpAddr` und `DownstreamIpAddr` für ein Gatewaygerät.      |
|**HostId**     |  String       |     |
| | | |


> [!NOTE]
> `Domain` ist ein typisches Attribut eines Geräts, aber kein vollständiger Bezeichner.
>

Weitere Informationen finden Sie unter [Microsoft Sentinel: Referenz zum Authentifizierungsnormalisierungsschema (öffentliche Vorschau)](authentication-normalization-schema.md).

### <a name="sample-entity-mapping"></a>Beispiel von Entitätszuordnung

In diesem Abschnitt wird am Beispiel des [Windows-Ereignisses 4624](/windows/security/threat-protection/auditing/event-4624) beschrieben, wie die Ereignisdaten für Microsoft Sentinel normalisiert werden.

Dieses Ereignis umfasst die folgenden Entitäten:

|Microsoft-Terminologie  |Präfix des ursprünglichen Ereignisfelds |Präfix des ASIM-Felds  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|**Subject**     | `Subject`        |   `Actor`      |  Der Benutzer, der Informationen zu einer erfolgreichen Anmeldung gemeldet hat.      |
|**Neue Anmeldung**     |    `Target`     |  `TargetUser`       |  Der Benutzer, für den die Anmeldung durchgeführt wurde.       |
|**Process**     |    -     |     `ActingProcess`    |   Der Prozess, über den die Anmeldung durchgeführt wurde.      |
|**Netzwerkinformationen**     |   -      |   `Src`      |     Der Computer, über den ein Anmeldeversuch durchgeführt wurde.    |
| | | | |


Basierend auf diesen Entitäten wird das [Windows-Ereignis 4624](/windows/security/threat-protection/auditing/event-4624) wie folgt normalisiert (einige Felder sind optional):

|Normalisiertes Feld  |Ursprüngliches Feld  |Wert im Beispiel  |Notizen  |
|---------|---------|---------|---------|
|**ActorUserId**     |  SubjectUserSid       |  S-1-5-18        |        |
|**ActorUserIdType**     |  -       | SID        |         |
|**ActorUserName**     |   SubjectDomainName\ SubjectUserName      |  WORKGROUP\WIN-GG82ULGC9GO$       |  Erstellt durch Verkettung der beiden Felder       |
|**ActorUserNameType**     |   -      |   Windows      |         |
|**ActorSessionId**     | SubjectLogonId        |  0x3e7       |         |
|**TargetUserId**     |   TargetUserSid      |    S-1-5-21-1377283216-344919071-3415362939-500     |         |
|**UserId**     |    TargetUserSid     |   alias      |         |
|**TargetUserIdType**     |   -      |    SID     |         |
|**TargetUserName**     | TargetDomainName\ TargerUserName        |   Administrator\WIN-GG82ULGC9GO$      |   Erstellt durch Verkettung der beiden Felder      |
|**Benutzername**     |  TargetDomainName\ TargerUserName       |  alias       |         |
|**TargetUserNameType**     | -        |  Windows       |         |
|**TargetSessionId**     |   TargetLogonId      |  0x8dcdc       |         |
|**ActingProcessName**     |  ProcessName       |  C:\\Windows\\System32\\svchost.exe       |         |
|**ActingProcessId**     |    ProcessId     |     0x44c    |         |
|**SrcHostname**     |    WorkstationName     | Windows        |         |
|**SrcIpAddr**     |  IpAddress       |     127.0.0.1    |         |
|**SrcPortNumber**     |  IpPort       |  0       |         |
|**TargetHostname**     |   Computer      |  WIN-GG82ULGC9GO           |         |
|**Hostname**     |     Computer    |     Alias    |         |
| | | | |


## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel gibt einen Überblick über die Normalisierung in Microsoft Sentinel und das erweiterte SIEM-Informationsmodell.

Weitere Informationen finden Sie unter
- [Übersicht über das erweiterte SIEM-Informationsmodell](normalization.md)
- [Parser des erweiterten SIEM-Informationsmodells](normalization-about-parsers.md)
- [Inhalte des erweiterten SIEM-Informationsmodells](normalization-content.md)