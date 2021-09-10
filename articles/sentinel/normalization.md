---
title: Normalisierung und das Azure Sentinel-Informationsmodell (ASIM) | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie in Azure Sentinel Daten unter Verwendung des Azure Sentinel-Informationsmodells (ASIM) aus verschiedenen Quellen normalisiert werden.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: c8bf2fd28a1b5adee4d028c3dc11b771d48ef295
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122356274"
---
# <a name="normalization-and-the-azure-sentinel-information-model-asim"></a>Normalisierung und das Azure Sentinel-Informationsmodell (ASIM)

In Azure Sentinel werden Daten aus vielen Quellen erfasst. Die gemeinsame Verwendung verschiedener Datentypen und -tabellen macht es notwendig, dass Sie mit den einzelnen Typen und Tabellen vertraut sind und eindeutige Sätze für Analyseregeln, Arbeitsmappen und Hunting-Abfragen für jeden Typ und jedes Schema schreiben und verwenden. Manchmal benötigen Sie separate Regeln, Arbeitsmappen und Abfragen, selbst bei in den Datentypen gemeinsam verwendeten Elementen, z. B. Firewallgeräten. Auch die Korrelation zwischen verschiedenen Datentypen bei einer Untersuchung und Hunting-Abfrage kann schwierig sein.

Das Azure Sentinel-Informationsmodell (ASIM) bietet durch folgende Funktionen eine nahtlose Methode für die Verarbeitung verschiedener Quellen in einheitlichen, normalisierten Ansichten:

- Quellenunabhängige Inhalte und Lösungen
- Vereinfachte analytische Verwendung der Daten in Azure Sentinel-Arbeitsbereichen
- Verwendung der Analyse zur Abfragezeit bei gleichzeitiger Minimierung der Leistungsbeeinträchtigung


In diesem Artikel wird das Azure Sentinel-Informationsmodell beschrieben und erläutert, wie Sie normalisierende Parser erstellen können, um nicht normalisierte Daten in das normalisierte Schema des Informationsmodells zu transformieren. Außerdem können Sie Inhalte zur Verwendung des normalisierten Schemas entwickeln und vorhandene Inhalte zur Verwendung des normalisierten Schemas konvertieren.

> [!NOTE]
> Das Azure Sentinel-Informationsmodell ist am Common Information Model [Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) ausgerichtet und ermöglicht daher die vorhersagbare Korrelation von Entitäten über normalisierte Tabellen hinweg. OSSEM ist ein von der Community betriebenes Projekt, das hauptsächlich auf die Dokumentation und Standardisierung von Sicherheitsereignisprotokollen aus unterschiedlichen Datenquellen und Betriebssystemen ausgerichtet ist. Außerdem stellt das Projekt ein Common Information Model (CIM) bereit, das während der Datennormalisierungsprozeduren von Datentechnikern verwendet werden kann, damit Sicherheitsanalytiker Daten über verschiedene Datenquellen hinweg abfragen und analysieren können.
>
> Weitere Informationen finden Sie in der [OSSEM-Referenzdokumentation](https://ossemproject.com/cdm/guidelines/entity_structure.html).
>
## <a name="azure-sentinel-information-model-components"></a>Komponenten des Azure Sentinel-Informationsmodells

Das Azure Sentinel-Informationsmodell umfasst die folgenden Komponenten:

|Komponente  |BESCHREIBUNG  |
|---------|---------|
|**Normalisierte Schemas**     |   Standardsätze von vorhersagbaren Ereignistypen, die Sie beim Erstellen einheitlicher Funktionen verwenden können. <br><br>Jedes Schema definiert die Felder, die ein Ereignis, eine Namenskonvention für normalisierte Spalten und ein Standardformat für die Feldwerte darstellen. <br><br> In ASIM sind derzeit die folgenden Schemas definiert:<br> - [Netzwerksitzung](normalization-schema.md)<br> - [DNS-Aktivität](dns-normalization-schema.md)<br> - [Prozessereignis](process-events-normalization-schema.md)<br> - [Authentifizierungsereignis](authentication-normalization-schema.md)<br> - [Registrierungsereignis](registry-event-normalization-schema.md)<br> - [Dateiaktivität](file-event-normalization-schema.md) |
|**Parser**     |  Zuordnung vorhandener Daten unter Verwendung von [KQL-Funktionen](/azure/data-explorer/kusto/query/functions/user-defined-functions) zu normalisierten Schemas. <br><br>Die von Microsoft entwickelten normalisierten Parser können über den GitHub-Ordner [Azure-Sentinel/Parsers](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers) bereitgestellt werden. Normalisierte Parser befinden sich in den Unterordnern, die mit **ASim** beginnen.       |
|**Inhalte für jedes normalisierte Schema**     |    Enthält Analyseregeln, Arbeitsmappen, Hunting-Abfragen und mehr. Die Inhalte für jedes normalisierte Schema können für alle normalisierten Daten verwendet werden, ohne dass quellenspezifische Inhalte erstellt werden müssen.     |

<br>

Die folgende Abbildung zeigt, wie nicht normalisierte Daten in normalisierte Inhalte umgewandelt und in Azure Sentinel verwendet werden können. Beispielsweise können Sie eine benutzerdefinierte, produktspezifische, nicht normalisierte Tabelle mithilfe eines Parsers und eines Normalisierungsschemas in normalisierte Daten konvertieren. Die normalisierten Daten können Sie in von Microsoft definierten sowie in benutzerdefinierten Analysen, Regeln, Arbeitsmappen, Abfragen usw. verwenden.

 :::image type="content" source="media/normalization/sentinel-information-model-components.png" alt-text="Umwandlung von nicht normalisierten in normalisierte Daten und Verwendung in Azure Sentinel":::

### <a name="azure-sentinel-information-model-terminology"></a>Terminologie zum Azure Sentinel-Informationsmodell

Im Azure Sentinel-Informationsmodell werden die folgenden Begriffe verwendet:

|Begriff  |BESCHREIBUNG  |
|---------|---------|
|**Meldendes Gerät**     |   Das System, das die Datensätze an Azure Sentinel sendet. Möglicherweise ist dieses System nicht das Zielsystem für den gesendeten Datensatz.      |
|**Datensatz**     |Eine Dateneinheit, die vom meldenden Gerät gesendet wird. Ein Datensatz wird häufig als `log`, `event` oder `alert` angegeben, kann sich aber auch auf andere Datentypen beziehen.         |
|**Inhalt** oder **Inhaltselement**     |Die verschiedenen, anpassbaren oder vom Benutzer erstellten Artefakte, die mit Azure Sentinel verwendet werden können. Zu diesen Artefakten gehören beispielsweise Analyseregeln, Hunting-Abfragen und Arbeitsmappen. Ein Inhaltselement ist ein solches Artefakt.|

<br>

## <a name="normalized-schemas"></a>Normalisierte Schemas

### <a name="schema-concepts"></a>Schemakonzepte

Ein Schema ist ein Satz von Feldern, die eine Aktivität darstellen. Die Verwendung der Felder eines normalisierten Schemas in einer Abfrage stellt sicher, dass die Abfrage mit jeder normalisierten Quelle verwendet werden kann.

Schemaverweise beschreiben die Felder, die jedes Schema umfasst. Die folgenden Konzepte dienen dem Verständnis der Schemaverweisdokumente und der normalisierten Erweiterung eines Schemas, wenn die Quelle Informationen enthält, die vom Schema nicht abgedeckt werden.


|Konzept  |BESCHREIBUNG  |
|---------|---------|
|**Feldnamen**     |   Der Kern jedes Schemas sind die Feldnamen. Feldnamen lassen sich in folgende Gruppen einordnen: <br><br>- Felder, die allen Schemas gemein sind <br>- Felder, die für ein Schema spezifisch sind <br>- Felder, die Entitäten darstellen, z. B. Benutzer, die am Schema mitwirken. Felder, die Entitäten darstellen, [sind in allen Schemas ähnlich](#entities). <br><br>Wenn Quellen Felder enthalten, die nicht im dokumentierten Schema angegeben sind, werden sie zur Wahrung der Konsistenz normalisiert. Wenn die zusätzlichen Felder eine Entität darstellen, werden sie basierend auf den Leitfäden für Entitätsfelder normalisiert. Andernfalls wird in den Schemas versucht, die Konsistenz über alle Schemas hinweg zu wahren.<br><br> Beispielsweise enthalten die Aktivitätsprotokolle von DNS-Servern keine Benutzerinformationen, während die DNS-Aktivitätsprotokolle eines Endpunkts Benutzerinformationen enthalten können, die entsprechend den Leitfäden für die Benutzerentität normalisiert werden können.      |
|**Feldtypen**     |  Jedem Schemafeld ist ein Typ zugeordnet. Der Log Analytics-Arbeitsbereich verfügt über eine begrenzte Anzahl von Datentypen. Daher wird in Azure Sentinel für viele Schemafelder ein logischer Typ verwendet, der in Log Analytics nicht erzwungen wird, jedoch für die Schemakompatibilität erforderlich ist. Durch logische Feldtypen wird sichergestellt, dass Werte sowie Feldnamen in allen Quellen konsistent sind.  <br><br>Weitere Informationen finden Sie unter [Logische Typen](#logical-types).     |
|**Feldklasse**     |Felder können mehrere Klassen beinhalten, die definieren, wann die Felder durch einen Parser implementiert werden sollen: <br><br>-    **Erforderliche Felder** müssen in jedem Parser angezeigt werden. Wenn eine Quelle keine Informationen für diesen Wert enthält oder die Daten nicht anderweitig hinzugefügt werden können, werden die meisten Inhaltselemente, die auf das normalisierte Schema verweisen, nicht unterstützt.<br>-  **Empfohlene** Felder sollten normalisiert werden, falls sie verfügbar sind. Es kann jedoch sein, dass sie nicht in jeder Quelle verfügbar sind, sodass bei jedem Inhaltselement, das auf dieses normalisierte Schema verweist, die Verfügbarkeit berücksichtigt werden muss. <br>-  **Optionale** Felder können, falls vorhanden, normalisiert oder in ihrer ursprünglichen Form belassen werden. Normalerweise werden sie aus Leistungsgründen durch einen sehr einfachen Parser nicht normalisiert.    |
|**Entitäten**     | Ereignisse ergeben sich im Zusammenhang mit Entitäten wie Benutzern, Hosts, Prozessen oder Dateien. Zur Beschreibung jeder Entität können mehrere Felder erforderlich sein. Ein Host kann beispielsweise einen Namen und eine IP-Adresse haben. <br><br>Ein einzelner Datensatz kann mehrere Entitäten desselben Typs enthalten, z. B. einen Quell- und Zielhost. <br><br>Das Azure Sentinel-Informationsmodell definiert, wie Entitäten konsistent zu beschreiben sind. Mit Entitäten lassen sich die Schemas erweitern. <br><br>Während das Netzwerksitzungsschema beispielsweise keine Prozessinformationen enthält, enthalten einige Ereignisquellen Prozessinformationen, die hinzugefügt werden können. Weitere Informationen finden Sie unter [Entitäten](#entities). |
|**Aliase**     |  In einigen Fällen erwarten verschiedene Benutzer, dass ein Feld unterschiedliche Namen hat. In der DNS-Terminologie wird z. B. ein Feld mit dem Namen `query` erwartet, während es in allgemeineren Anwendungsfällen einen Domänennamen enthält. Mit Aliasen lässt sich dieses Problem der Mehrdeutigkeit lösen, dadurch dass mehrere Namen für einen angegebenen Wert zugelassen werden. Die Aliasklasse entspricht der des Felds, für das sie als Alias verwendet wird.       |

<br>

### <a name="logical-types"></a>Logische Typen

Jedem Schemafeld ist ein Typ zugeordnet. Einige Felder verfügen über integrierte Azure Log Analytics-Typen wie `string`, `int`, `datetime` oder `dynamic`. Andere Felder verfügen über einen logischen Typ, der angibt, wie die Feldwerte normalisiert werden sollen.

|Datentyp  |Physischer Typ  |Format und Wert  |
|---------|---------|---------|
|**Boolean**     |   Bool      |    Verwenden Sie den nativen booleschen KQL-Datentyp anstelle einer numerischen oder Zeichenfolgendarstellung boolescher Werte.     |
|**Enumeration**     |  String       |   Eine Liste von Werten, die explizit für das Feld definiert sind. In der Schemadefinition sind die akzeptierten Werte aufgeführt.      |
|**Datum/Uhrzeit**     |  Verwenden Sie je nach Erfassungsmethode eine der folgenden physischen Darstellungen in absteigender Priorität: <br><br>- Integrierter datetime-Typ von Log Analytics <br>- Integerfeld, das die numerische datetime-Darstellung von Log Analytics verwendet <br>- Zeichenfolgenfeld, das die numerische datetime-Darstellung von Log Analytics verwendet <br>- Zeichenfolgenfeld, in dem ein unterstütztes [datetime-Format von Log Analytics](/azure/data-explorer/kusto/query/scalar-data-types/datetime) gespeichert wird       |  Die [Darstellung von Datums- und Uhrzeitwerten in Log Analytics](/azure/kusto/query/scalar-data-types/datetime) ähnelt der UNIX-Darstellung, weist aber Unterschiede auf. Weitere Informationen finden Sie in den [Konvertierungsleitfäden](/azure/kusto/query/datetime-timespan-arithmetic). <br><br>**Hinweis:** Die Zeitwerte sollten gegebenenfalls an die Zeitzone angepasst sein. |
|**MAC-Adresse**     |  String       | Hexadezimalnotation mit Doppelpunkt        |
|**IP-Adresse**     |String         |    Azure Sentinel-Schemas enthalten keine separaten IPv4- und IPv6-Adressen. Jedes IP-Adressfeld kann wie folgt entweder eine IPv4-Adresse oder eine IPv6-Adresse enthalten: <br><br>z. B. - **IPv4** in einer Punkt-Dezimal-Notation  <br>- **IPv6** in 8-Hextett-Notation, die die Kurzform ermöglicht<br><br>Zum Beispiel:<br>`192.168.10.10` (IPv4)<br>`FEDC:BA98:7654:3210:FEDC:BA98:7654:3210` (IPv6)<br>`1080::8:800:200C:417A` (IPv6-Kurzform)     |
|**FQDN**        |   Zeichenfolge      |    Ein vollqualifizierter Domänenname mit Punktnotation, z. B. `docs.microsoft.com` |
|**Country**     |   String      |    Eine Zeichenfolge unter Verwendung von [ISO 3166-1](https://www.iso.org/iso-3166-country-codes.html), entsprechend der folgenden Priorität: <br><br> - Alpha-2-Codes, z. B. `US` für die USA <br> - Alpha-3-Codes, z. B. `USA` für die USA <br>- Kurzname<br><br>Die Liste der Codes finden Sie auf der [Website der International Standards Organization (ISO)](https://www.iso.org/obp/ui/#search).|
|**Region**     | String        |   Der Name der Untereinheiten von Ländern nach ISO 3166-2<br><br>Die Liste der Codes finden Sie auf der [Website der International Standards Organization (ISO)](https://www.iso.org/obp/ui/#search).|
|**City (Ort)**     |  String       |         |
|**Longitude** (Längengrad)     | Double        |  ISO 6709-Koordinatendarstellung (Dezimalwert mit Vorzeichen)       |
|**Latitude** (Breitengrad)     | Double        |    ISO 6709-Koordinatendarstellung (Dezimalwert mit Vorzeichen)     |
|**MD5**     |    String     |  32-Hexadezimalzeichen       |
|**SHA1**     |   String      | 40-Hexadezimalzeichen        |
|**SHA256**     | String        |  64-Hexadezimalzeichen       |
|**SHA512**     |   String      |  128-Hexadezimalzeichen       |

<br>

### <a name="common-fields"></a>Allgemeine Felder

Die folgenden Felder sind in allen ASIM-Schemas enthalten. Die allgemeinen Felder sind hier und für jedes Schema aufgeführt, für die Fälle, in denen sich die Details je nach Schema unterscheiden. Beispielsweise können die Werte für das Feld **EventType** und für das Feld **EventSchemaVersion** je nach Schema variieren. 

| Feld               | Klasse       | type       |  BESCHREIBUNG        |
|---------------------|-------------|------------|--------------------|
| <a name="timegenerated"></a>**TimeGenerated** | Integriert | datetime | Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde.|
| **_ResourceId**   | Integriert |  guid     | Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden. |
| **EventMessage**        | Optional    | String     |     Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert.   |
| **EventCount**          | Obligatorisch.   | Integer    |     Die Anzahl der Ereignisse, die im Datensatz beschrieben werden. <br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen. <br><br>Legen Sie den Wert für andere Quellen auf `1` fest.   |
| **EventStartTime**      | Obligatorisch.   | Datum/Uhrzeit  |      Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, gibt dieses Feld die Zeit an, zu der das erste Ereignis generiert wurde. <br><br>Andernfalls wird in diesem Feld ein Alias für das Feld [TimeGenerated](#timegenerated) verwendet. |
| **EventEndTime**        | Obligatorisch.   | Alias      |      Alias für das Feld [TimeGenerated](#timegenerated).    |
|  <a name=eventtype></a>**EventType**           | Obligatorisch.   | Enumerated |    Beschreibt den vom Datensatz gemeldeten Vorgang. Jedes Schema definiert die Liste der für dieses Feld gültigen Werte. |
| **EventSubType** | Optional | Enumerated | Beschreibt eine Untereinheit des Vorgangs, der im Feld [EventType](#eventtype) gemeldet wird. Jedes Schema definiert die Liste der für dieses Feld gültigen Werte. |
| <a name="eventresult"></a>**EventResult** | Obligatorisch. | Enumerated | Einer der folgenden Werte: **Success**, **Partial**, **Failure**, **NA** (Not Applicable, nicht anwendbar).<br> <br>Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Alternativ kann die Quelle auch nur das Feld [EventResultDetails](#eventresultdetails) angeben, das analysiert werden sollte, um den EventResult-Wert abzuleiten.<br><br>Beispiel: `Success`|
| <a name=eventresultdetails></a>**EventResultDetails** | Obligatorisch. | Alias | Grund oder Details für das im Feld [**EventResult**](#eventresult) gemeldete Ergebnis. Jedes Schema definiert die Liste der für dieses Feld gültigen Werte.<br><br>Beispiel: `NXDOMAIN`|
| **EventOriginalUid**    | Optional    | String     |   Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | Optional    | String     |   Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt. Dieses Feld wird beispielsweise verwendet, um die ursprüngliche Windows-Ereignis-ID zu speichern.<br><br>Beispiel: `4624`|
| <a name ="eventproduct"></a>**EventProduct**        | Obligatorisch.   | String     |             Das Produkt, das das Ereignis erzeugt. <br><br>Beispiel: `Sysmon`<br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.           |
| **EventProductVersion** | Optional    | String     | Die Version des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `12.1`      |
| **EventVendor**         | Obligatorisch.   | String     |           Der Hersteller des Produkts, das das Ereignis erzeugt. <br><br>Beispiel: `Microsoft`  <br><br>**Hinweis**: Dieses Feld ist möglicherweise nicht im Quelldatensatz verfügbar. In solchen Fällen muss dieses Feld vom Parser festgelegt werden.  |
| **EventSchemaVersion**  | Obligatorisch.   | String     |    Die Version des Schemas. In jedem Schema ist die aktuelle Version dokumentiert.         |
| **EventReportUrl**      | Optional    | String     | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die zusätzliche Informationen zum Ereignis enthält.|
| **Dvc** | Obligatorisch.       | String     |               Ein eindeutiger Bezeichner des Geräts, auf dem das Ereignis eingetreten ist. <br><br>Dieses Feld kann ein Alias für die Felder [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die es kein offensichtliches Gerät gibt, den gleichen Wert wie das Feld [EventProduct](#eventproduct).           |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Empfohlen | IP-Adresse |         Die IP-Adresse des Geräts, auf dem das Ereignis eingetreten ist.  <br><br>Beispiel: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | Empfohlen | Hostname   |               Der Hostname des Geräts, auf dem das Ereignis eingetreten ist. <br><br>Beispiel: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | Optional    | String     |  Die eindeutige ID des Geräts, auf dem das Ereignis eingetreten ist. <br><br>Beispiel: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | Optional    | MAC        |   Die MAC-Adresse des Geräts, auf dem das Ereignis eingetreten ist.  <br><br>Beispiel: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | Optional    | String     |         Das Betriebssystem, das auf dem Gerät ausgeführt wird, auf dem das Ereignis eingetreten ist.    <br><br>Beispiel: `Windows`    |
| **DvcOsVersion**        | Optional    | String     |   Die Version des Betriebssystems auf dem Gerät, auf dem das Ereignis eingetreten ist. <br><br>Beispiel: `10` |
| **AdditionalFields**    | Optional    | Dynamisch    | Wenn Ihre Quelle zusätzliche Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie ihm die zusätzlichen Informationen als Schlüssel-Wert-Paare hinzu.    |

> [!NOTE]
> Log Analytics fügt auch andere Felder hinzu, die für Sicherheitsanwendungsfälle weniger relevant sind. Weitere Informationen finden Sie unter [Standardspalten in Azure Monitor-Protokollen](../azure-monitor/logs/log-standard-columns.md).
>


### <a name="entities"></a>Entitäten

Ereignisse entwickeln sich im Zusammenhang mit Entitäten wie Benutzern, Hosts, Prozessen oder Dateien. Die Entitätsdarstellung ermöglicht, dass mehrere Entitäten desselben Typs Teil eines einzelnen Datensatzes sind, und sie unterstützt mehrere Attribute für dieselben Entitäten. 

Zur Ermöglichung von Entitätsfunktionen gelten für die Entitätsdarstellung die folgenden Richtlinien:

|Vorgabe  |BESCHREIBUNG  |
|---------|---------|
|**Deskriptoren und Aliase**     | Da ein einzelnes Ereignis häufig mehrere Entitäten desselben Typs enthält, z. B. Quell- und Zielhosts, werden *Deskriptoren* als Präfix verwendet, um alle Felder zu identifizieren, die einer bestimmten Entität zugeordnet sind. <br><br>Zur Aufrechterhaltung der Normalisierung wird im Azure Sentinel-Informationsmodell eine kleine Gruppe von Standarddeskriptoren verwendet, die für die spezifische Rolle der Entitäten am besten geeignet sind.  <br><br>Wenn eine einzelne Entität eines Typs für ein Ereignis relevant ist, muss kein Deskriptor verwendet werden. Außerdem wird ein Satz von Feldern ohne Deskriptor als Alias für die am häufigsten verwendete Entität für den Typ verwendet.  |
|**Bezeichner und Typen**     | Ein normalisiertes Schema ermöglicht mehrere Bezeichner für jede Entität, die voraussichtlich in Ereignissen gleichzeitig vorhanden sind. Wenn das Quellereignis andere Entitätsbezeichner enthält, die nicht dem normalisierten Schema zugeordnet werden können, behalten Sie sie im Quellformat bei, oder verwenden Sie das dynamische Feld `AdditionalFields`. <br><br>Zum Verwalten der Typinformationen für die Bezeichner speichern Sie den Typ ggf. in einem Feld mit dem gleichen Namen und dem Suffix `Type`. Beispiel: `UserIdType`.         |
|**Attribute**     |   Entitäten umfassen häufig andere Attribute, die nicht als Bezeichner dienen und auch mit einem Deskriptor qualifiziert werden können. Wenn der Quellbenutzer z. B. Domäneninformationen enthält, lautet das normalisierte Feld `SrcUserDomain`.      |

<br>

Mit jedem Schema werden explizit die zentralen Entitäten und Entitätsfelder definiert. Anhand der nachfolgenden Leitfäden wird Folgendes veranschaulicht:

- Zentrale Schemafelder
- Normalisierte Erweiterung von Schemas unter Verwendung anderer Entitäten oder Entitätsfelder, die in einem Schema nicht explizit definiert sind

#### <a name="the-user-entity"></a>Benutzerentität

Für einen Benutzer werden wie in den folgenden Szenarien beschrieben die Deskriptoren **Akteur**, **Zielbenutzer** und **Aktualisierter Benutzer** verwendet:

|Aktivität  |Vollständiges Szenario  |Szenario mit einzelner Entität, das für Aliasing verwendet wird  |
|---------|---------|---------|
|**Benutzer erstellen**     |  Ein **Akteur** hat einen **Zielbenutzer** erstellt oder geändert.       |  Der (Ziel-)**Benutzer** wurde erstellt.       |
|**Ändern eines Benutzers**     |   Ein **Akteur** hat einen **Zielbenutzer** in einen **aktualisierten Benutzer** umbenannt. Der **aktualisierte Benutzer** verfügt normalerweise nicht über alle Informationen, die einem Benutzer zugeordnet sind, und weist einige Überschneidungen mit dem **Zielbenutzer** auf.      |         |
|**Netzwerkverbindung**     |    Ein Prozess, der als **Akteur** auf dem Quellhost ausgeführt wird, kommuniziert mit einem Prozess, der als **Zielbenutzer** auf dem Zielhost ausgeführt wird.     |         |
|**DNS-Anforderung**     | Ein **Akteur** hat eine DNS-Abfrage initiiert.        |         |
|**Anmelden**     |    Ein **Akteur** hat sich als **Zielbenutzer** bei einem System angemeldet.     |Ein (Ziel-)Benutzer hat sich angemeldet.         |
|**Prozesserstellung**     |   Ein **Akteur** (der dem initiierenden Prozess zugeordnete Benutzer) hat die Prozesserstellung initiiert. Der erstellte Prozess wird unter den Anmeldeinformationen eines **Zielbenutzers** (der dem Zielprozess zugeordnete Benutzer) ausgeführt.      |  Der erstellte Prozess wird unter den Anmeldeinformationen eines (Ziel-)**Benutzers** ausgeführt.       |
|**E-Mail**     |     Ein **Akteur** sendet eine E-Mail an einen **Zielbenutzer**.    |         |

<br>

In der folgenden Tabelle werden die unterstützten Bezeichner für einen Benutzer beschrieben:

|Normalisiertes Feld  |type  |Format und unterstützte Typen  |
|---------|---------|---------|
|**UserId**     |    String     |   Eine maschinenlesbare, alphanumerische, eindeutige Darstellung eines Benutzers in einem System. <br><br>Format und unterstützte Typen:<br>    - **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>    -  **UID** (Linux): `4578`<br>    -    **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>    - **OktaId:** `00urjk4znu3BcncfY0h7`<br>    - **AWSId:** `72643944673`<br><br>    Speichern Sie den ID-Typ im Feld `UserIdType`. Wenn andere IDs verfügbar sind, empfiehlt es sich, die Feldnamen auf `UserSid`, `UserUid`, `UserAADID`, `UserOktaId` bzw. `UserAwsId` zu normalisieren.       |
|**Benutzername**     |  String       |   Ein Benutzername, ggf. einschließlich der Domäneninformationen, in einem der folgenden Formate und in der folgenden Prioritätsreihenfolge: <br> -   **UPN/E-Mail:** `johndow@contoso.com` <br>  -    **Windows:** `Contoso\johndow` <br> -   **DN:** `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM` <br>  - **Einfach:** `johndow`. Verwenden Sie dieses Format nur, wenn keine Domäneninformationen verfügbar sind. <br><br> Speichern Sie den Benutzernamenstyp im Feld `UsernameType`.    |

<br>

#### <a name="the-process-entity"></a>Prozessentität

Für einen Prozess werden wie in den folgenden Szenarien beschrieben die Deskriptoren `Acting Process`, `Target Process` und `Parent Process` verwendet:

- **Netzwerkverbindung:** Ein **aktiver Prozess** hat eine Netzwerkverbindung zur Kommunikation mit einem **Zielprozess** auf einem Remotesystem initiiert.
- **DNS-Anforderung:**  Ein **aktiver Prozess** hat eine DNS-Abfrage initiiert.
- **Anmeldung:**  Ein **aktiver Prozess** hat eine Anmeldung bei einem Remotesystem initiiert, das einen **Zielprozess** in seinem Namen ausgeführt hat.
- **Prozesserstellung:** Ein **aktiver Prozess** hat die Erstellung eines **Zielprozesses** initiiert. Ein **übergeordneter Prozess** ist dem aktiven Prozess übergeordnet.

In der folgenden Tabelle werden die unterstützten Bezeichner für Prozesse beschrieben:

|Normalisiertes Feld  |type  |Format und unterstützte Typen  |
|---------|---------|---------|
|**Id**     |    String     |   Die vom Betriebssystem zugewiesene Prozess-ID      |
|**Guid**     |  String       |   Die vom Betriebssystem zugewiesene Prozess-GUID. Die GUID ist im Allgemeinen bei Systemneustarts eindeutig, wohingegen die ID häufig wiederverwendet wird.   |
|**Path**     |    String     |   Der vollständige Pfadname des Prozesses, einschließlich Verzeichnis- und Dateiname       |
|**Name**     |  Alias       |  Der Prozessname ist ein Alias für den Pfad.   |

<br>

Weitere Informationen finden Sie unter [Azure Sentinel: Referenz zum Prozessereignis-Normalisierungsschema (öffentliche Vorschau)](process-events-normalization-schema.md).

#### <a name="the-device-entity"></a>Geräteentität

Mit den Normalisierungsschemas wird versucht, der Intuition des Benutzers möglichst umfassend zu folgen. Daher werden Geräte je nach Szenario auf unterschiedliche Weise verarbeitet:

- Wenn der Ereigniskontext ein Quell- und ein Zielgerät impliziert, werden die Deskriptoren `Src` und `Target` verwendet. In diesen Fällen wird der Deskriptor `Dvc` für das meldende Gerät verwendet.

- Für Ereignisse einzelner Geräte, z. B. Ereignisse des lokalen Betriebssystems, wird der Deskriptor `Dvc` verwendet.

- Wenn im Ereignis auf ein anderes Gatewaygerät verwiesen wird und sich der Wert vom meldenden Gerät unterscheidet, wird der Deskriptor `Gateway` verwendet.

Die Leitfäden zur Verarbeitung von Geräten werden wie folgt näher erläutert:

- **Netzwerkverbindung:** Eine Verbindung zwischen einem **Quellgerät** (`Src`) und einem **Zielgerät** (`Target`) wurde hergestellt. Die Verbindung wurde von einem (meldenden) **Gerät** (`Dvc`) gemeldet.
- **Netzwerkverbindung mit Proxy:** Eine Verbindung zwischen einem **Quellgerät** (`Src`) und einem **Zielgerät** (`Target`) wurde über ein **Gatewaygerät** (`Gateway`) hergestellt. Die Verbindung wurde von einem (meldenden) **Gerät** gemeldet.
- **DNS-Anforderung:**  Eine DNS-Abfrage wurde über ein **Quellgerät** (`Src`) initiiert.
- **Anmeldung:** Eine Anmeldung wurde über ein **Quellgerät** (`Src`) bei einem Remotesystem auf einem **Zielgerät** (`Target`) initiiert.
- **Prozess:** Ein Prozess wurde auf einem **Gerät** (`Dvc`) initiiert.

In der folgenden Tabelle werden die unterstützten Bezeichner für Geräte beschrieben:

|Normalisiertes Feld  |type  |Format und unterstützte Typen  |
|---------|---------|---------|
|**Hostname**     |    String     |        |
|**FQDN**     |  String       |   Vollqualifizierter Domänenname   |
|**IpAddr**     |    IP-Adresse     |   Während Geräte mehrere IP-Adressen haben können, weisen Ereignisse normalerweise eine einzelne identifizierende IP-Adresse auf. Eine Ausnahme bildet ein Gatewaygerät, das über zwei relevante IP-Adressen verfügen kann. Verwenden Sie `UpstreamIpAddr` und `DownstreamIpAddr` für ein Gatewaygerät.      |
|**HostId**     |  String       |     |

<br>

> [!NOTE]
> `Domain` ist ein typisches Attribut eines Geräts, aber kein vollständiger Bezeichner.
>

Weitere Informationen finden Sie unter [Azure Sentinel: Referenz zum Authentifizierungsnormalisierungsschema (öffentliche Vorschau)](authentication-normalization-schema.md).

#### <a name="entity-mapping-example"></a>Beispiel für eine Entitätszuordnung

In diesem Abschnitt wird am Beispiel des [Windows-Ereignisses 4624](/windows/security/threat-protection/auditing/event-4624) beschrieben, wie die Ereignisdaten für Azure Sentinel normalisiert werden.

Dieses Ereignis umfasst die folgenden Entitäten:

|Microsoft-Terminologie  |Präfix des ursprünglichen Ereignisfelds |Präfix des ASIM-Felds  |BESCHREIBUNG  |
|---------|---------|---------|---------|
|**Subject**     | `Subject`        |   `Actor`      |  Der Benutzer, der Informationen zu einer erfolgreichen Anmeldung gemeldet hat      |
|**Neue Anmeldung**     |    `Target`     |  `TargetUser`       |  Der Benutzer, für den die Anmeldung durchgeführt wurde       |
|**Process**     |    -     |     `ActingProcess`    |   Der Prozess, über den die Anmeldung durchgeführt wurde      |
|**Netzwerkinformationen**     |   -      |   `Src`      |     Der Computer, über den ein Anmeldeversuch durchgeführt wurde    |

<br>

Basierend auf diesen Entitäten wird das [Windows-Ereignis 4624](/windows/security/threat-protection/auditing/event-4624) wie folgt normalisiert (einige Felder sind optional):

|Normalisiertes Feld  |Ursprüngliches Feld  |Wert im Beispiel  |Notizen  |
|---------|---------|---------|---------|
|**ActorUserId**     |  SubjectUserSid       |  S-1-5-18        |        |
|**ActorUserIdType**     |  -       | SID        |         |
|**ActorUserName**     |   SubjectDomainName\SubjectUserName      |  WORKGROUP\WIN-GG82ULGC9GO$       |  Erstellt durch Verkettung der beiden Felder       |
|**ActorUserNameType**     |   -      |   Windows      |         |
|**ActorSessionId**     | SubjectLogonId        |  0x3e7       |         |
|**TargetUserId**     |   TargetUserSid      |    S-1-5-21-1377283216-344919071-3415362939-500     |         |
|**UserId**     |    TargetUserSid     |   alias      |         |
|**TargetUserIdType**     |   -      |    SID     |         |
|**TargetUserName**     | TargetDomainName\TargerUserName        |   Administrator\WIN-GG82ULGC9GO$      |   Erstellt durch Verkettung der beiden Felder      |
|**Benutzername**     |  TargetDomainName\TargerUserName       |  alias       |         |
|**TargetUserNameType**     | -        |  Windows       |         |
|**TargetSessionId**     |   TargetLogonId      |  0x8dcdc       |         |
|**ActingProcessName**     |  ProcessName       |  C:\\Windows\\System32\\svchost.exe       |         |
|**ActingProcessId**     |    ProcessId     |     0x44c    |         |
|**SrcHostname**     |    WorkstationName     | Windows        |         |
|**SrcIpAddr**     |  IpAddress       |     127.0.0.1    |         |
|**SrcPortNumber**     |  IpPort       |  0       |         |
|**TargetHostname**     |   Computer      |  WIN-GG82ULGC9GO           |         |
|**Hostname**     |     Computer    |     Alias    |         |

<br>

## <a name="parsers"></a>Parser

In Azure Sentinel erfolgt die Analyse zur Abfragezeit. Parser werden als [benutzerdefinierte KQL-Funktionen](/azure/data-explorer/kusto/query/functions/user-defined-functions) erstellt, die Daten in vorhandenen Tabellen (wie etwa **CommonSecurityLog**, benutzerdefinierte Protokolltabellen, Syslog) in das normalisierte Schema transformieren. Nachdem der Parser als Arbeitsbereichsfunktion gespeichert wurde, kann er wie jede andere Azure Sentinel-Tabelle verwendet werden.

Es gibt zwei Ebenen von Parsern: **quellenunabhängige** und **quellenspezifische** Parser, wie oben in der Abbildung [Komponenten des Azure Sentinel-Informationsmodells](#azure-sentinel-information-model-components) dargestellt.

- Ein **quellenunabhängiger Parser** kombiniert alle nach demselben Schema normalisierten Quellen und kann zur Abfrage aller Quellen anhand normalisierter Felder verwendet werden. Der Name eines quellenspezifischen Parsers entspricht `im<schema>`, wobei `<schema>` für das spezifische Schema steht, für das er verwendet wird.

    In der folgenden Abfrage wird beispielsweise der quellenunabhängige DNS-Parser verwendet, um DNS-Ereignisse anhand der normalisierten Felder `ResponseCodeName`, `SrcIpAddr` und `TimeGenerated` abzufragen:

    ```kusto
    imDns
      | where isnotempty(ResponseCodeName)
      | where ResponseCodeName =~ "NXDOMAIN"
      | summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
    ```

    Ein quellenunabhängiger Parser kann mithilfe des KQL-Operators `union` mehrere quellenspezifische normalisierte Parser kombinieren. Der Name eines quellenspezifischen normalisierten Parsers entspricht `vim<schema><vendor><product>`. Daher sieht der Parser `imDns` wie folgt aus:

    ```kusto
    union isfuzzy=true
    vimDnsEmpty,
    vimDnsCiscoUmbrella,
    vimDnsInfobloxNIOS,
    vimDnsMicrosoftOMS
    ```

- Durch Hinzufügen **quellenspezifischer** normalisierter Parser zu dem quellenunabhängigen Parser können Sie benutzerdefinierte Quellen in integrierten Abfragen einschließen, bei denen der quellenunabhängige Parser verwendet wird.

    Mit quellenspezifischen Parsern können Sie sofortigen Nutzen aus den integrierten Inhalten ziehen, z. B. Analysen, Arbeitsmappen, Erkenntnisse für Ihre benutzerdefinierten Daten.

    Die quellenspezifischen Parser können auch unabhängig voneinander verwendet werden. In einer Infoblox-spezifischen Arbeitsmappe können Sie beispielsweise den Parser `vimDnsInfobloxNIOS` verwenden.
### <a name="writing-parsers"></a>Schreiben von Parsern

Ein Parser ist eine KQL-Abfrage, die als Arbeitsbereichsfunktion gespeichert wird. Nach dem Speichern kann er wie integrierte Tabellen verwendet werden. Die Parserabfrage umfasst folgende Teile:

**Filterung** > **Analyse** > **Vorbereitung von Feldern**

#### <a name="filtering"></a>Filterung

In vielen Fällen enthält eine Tabelle mehrere Ereignistypen. Zum Beispiel:
* Die Syslog-Tabelle enthält Daten aus mehreren Quellen.
* Benutzerdefinierte Tabellen können Informationen aus einer einzelnen Quelle enthalten, die mehrere Ereignistypen umfasst und für verschiedene Schemas geeignet ist.

Daher sollte ein Parser zunächst nur die Datensätze filtern, die für das Zielschema relevant sind.

Die Filterung in KQL erfolgt mit dem Operator `where`. Beispiel: Das **Sysmon-Ereignis 1** meldet die Prozesserstellung und sollte mit dem Schema **ProcessEvent** normalisiert werden. Das **Sysmon-Ereignis 1** ist in der Tabelle `Event` enthalten. Folgender Filter sollte verwendet werden:

```kusto
Event | where Source == "Microsoft-Windows-Sysmon" and EventID == 1
```

Beachten Sie die folgenden Empfehlungen für die Filterung, um die Leistungsfähigkeit des Parsers zu gewährleisten:

-   Führen Sie die Filterung immer nach integrierten statt nach analysierten Feldern durch. Auch wenn es manchmal einfacher ist, anhand von analysierten Feldern zu filtern, hat dies doch erhebliche Auswirkungen auf die Leistung.
-   Verwenden Sie Operatoren, die optimale Leistung ermöglichen, insbesondere `==`, `has` und `startswith`. Auch die Verwendung von Operatoren wie `contains` oder `matches regex` wirkt sich erheblich auf die Leistung aus.

Die Empfehlungen für die Filterung zur Leistungsoptimierung lassen sich nicht immer einfach umsetzen. Beispielsweise ist die Verwendung von `has` weniger genau als die von `contains`. In anderen Fällen ist der Abgleich des integrierten Felds, z. B. `SyslogMessage`, weniger genau als der Vergleich eines extrahierten Felds, z. B. `DvcAction`. In diesen Fällen empfiehlt es sich, für ein integriertes Feld dennoch eine Vorabfilterung mithilfe eines leistungsoptimierenden Operators durchzuführen und die Filterung nach der Analyse mit genaueren Bedingungen erneut durchzuführen.

Ein Beispiel dazu sehen Sie im nachfolgenden Parserausschnitt [Infoblox DNS](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox). Der Parser überprüft zunächst, ob das SyslogMessage-Feld den Begriff `client` enthält (`has`). Der Begriff kann jedoch auch an einer anderen Stelle der Meldung verwendet werden. Daher überprüft der Parser nach dem Analysieren des Felds `Log_Type` erneut, ob der Begriff `client` tatsächlich der Wert des Felds war.

```kusto
Syslog | where ProcessName == "named" and SyslogMessage has "client"
…
      | extend Log_Type = tostring(Parser[1]),
      | where Log_Type == "client"
```

> [!NOTE]
> Parser sollten nicht nach Zeit filtern, da die Abfrage, bei der ein Parser verwendet wird, nach Zeit filtert.
> 

#### <a name="parsing"></a>Analyse

Nachdem bei der Abfrage die relevanten Datensätze ausgewählt wurden, müssen sie möglicherweise analysiert werden. Normalerweise ist eine Analyse erforderlich, wenn ein Großteil der Ereignisinformationen in einem einzelnen Textfeld übermittelt wird.

Die KQL-Operatoren, mit denen die Analyse durchgeführt wird, sind nachfolgend nach ihrer Leistungsoptimierung sortiert aufgeführt. Der erste Operator bietet die am besten optimierte Leistung, der letzte die am wenigsten optimierte Leistung.

|Operator  |BESCHREIBUNG  |
|---------|---------|
|[split](/azure/data-explorer/kusto/query/splitfunction)     |    Analysiert eine Zeichenfolge von Werten, die durch ein Trennzeichen getrennt sind     |
|[parse_csv](/azure/data-explorer/kusto/query/parsecsvfunction)     |     Analysiert eine Zeichenfolge von Werten, die als CSV-Zeile (mit durch Trennzeichen getrennten Werten) formatiert sind    |
|[parse](/azure/data-explorer/kusto/query/parseoperator)     |    Analysiert mehrere Werte aus einer beliebigen Zeichenfolge anhand eines Musters, das ein vereinfachtes Muster mit besserer Leistung oder ein regulärer Ausdruck sein kann     |
|[extract_all](/azure/data-explorer/kusto/query/extractallfunction)     | Analysiert einzelne Werte aus einer beliebigen Zeichenfolge anhand eines regulären Ausdrucks. `extract_all` hat eine ähnliche Leistung wie `parse`, wenn bei letzterem ein regulärer Ausdruck verwendet wird.        |
|[extract](/azure/data-explorer/kusto/query/extractfunction)     |    Extrahiert einen einzelnen Wert aus einer beliebigen Zeichenfolge anhand eines regulären Ausdrucks. <br><br>Die Verwendung von `extract` ermöglicht eine bessere Leistung als die von `parse` oder `extract_all`, wenn ein einzelner Wert abgefragt werden soll. Die Verwendung mehrerer Aktivierungen von `extract` für dieselbe Quellzeichenfolge ist jedoch deutlich weniger effizient als eine einzige Aktivierung von `parse` oder `extract_all` und sollte daher vermieden werden.      |
|[parse_json](/azure/data-explorer/kusto/query/parsejsonfunction)  | Analysiert die Werte in einer Zeichenfolge, die als JSON-Code formatiert ist. Wenn nur wenige Werte aus dem JSON-Code benötigt werden, bietet die Verwendung von `parse`, `extract` oder `extract_all` eine bessere Leistung.        |
|[parse_xml](/azure/data-explorer/kusto/query/parse-xmlfunction)     |    Analysiert die Werte in einer Zeichenfolge, die als XML-Code formatiert ist. Wenn nur wenige Werte aus dem XML-Code benötigt werden, bietet die Verwendung von `parse`, `extract` oder `extract_all` eine bessere Leistung.     |

<br>

Neben der Analyse der Zeichenfolge kann in der Analysephase eine weitere Verarbeitung der ursprünglichen Werte erforderlich sein, beispielsweise:

- **Formatierung und Typkonvertierung:** Das Quellfeld muss nach der Extraktion möglicherweise so formatiert werden, dass es dem Zielschemafeld entspricht. Beispielsweise müssen Sie möglicherweise eine Zeichenfolge, die Datum und Uhrzeit darstellt, in ein datetime-Feld konvertieren.     In diesen Fällen sind Funktionen wie `todatetime` und `tohex` hilfreich.

- **Wertsuche:** Der Wert des Quellfelds muss nach der Extraktion möglicherweise der für das Zielschemafeld angegebenen Gruppe von Werten zugeordnet werden. Einige Quellen melden z. B. numerische DNS-Antwortcodes, während das Schema die allgemeineren Textantwortcodes vorschreibt. Für die Zuordnung einer kleinen Anzahl von Werten können die Funktionen `iff` und `case` nützlich sein.

    Beispielsweise weist der Microsoft DNS-Parser das Feld `EventResult` basierend auf der Ereignis-ID und dem Antwortcode mithilfe einer `iff`-Anweisung wie folgt zu:

    ```kusto
    extend EventResult = iff(EventId==257 and ResponseCode==0 ,'Success','Failure')
    ```

    Verwenden Sie für mehrere Werte `datatable` und `lookup`, wie im gleichen DNS-Parser gezeigt:

    ```kusto
    let RCodeTable = datatable(ResponseCode:int,ResponseCodeName:string) [ 0, 'NOERROR', 1, 'FORMERR'....];
    ...
     | lookup RCodeTable on ResponseCode
     | extend EventResultDetails = case (
         isnotempty(ResponseCodeName), ResponseCodeName, 
         ResponseCode between (3841 .. 4095), 'Reserved for Private Use', 
         'Unassigned')
    ```

> [!NOTE]
> Bei der Transformation ist nicht die alleinige Verwendung von `lookup` zulässig, da mehrere Werte zu `Reserved for Private Use` oder `Unassigned` zugeordnet werden. Daher werden bei der Abfrage Such- und Fallabfragen verwendet. Dennoch ist die Abfrage immer noch wesentlich effizienter als die Verwendung von `case` für alle Werte.
>

#### <a name="prepare-fields-in-the-result-set"></a>Vorbereiten von Feldern im Resultset

Der Parser muss die Felder im Resultset vorbereiten, um sicherzustellen, dass die normalisierten Felder verwendet werden. Als Leitfaden gilt, dass ursprüngliche Felder, die nicht normalisiert sind, nicht aus dem Resultset entfernt werden sollten, es sei denn, es gibt einen zwingenden Grund dafür, z. B. wenn sie zu Unklarheiten führen.

Zur Vorbereitung von Feldern werden die folgenden KQL-Operatoren verwendet:

|Operator  | BESCHREIBUNG  | Verwendung in einem Parser  |
|---------|---------|---------|
|**extend**     | Erstellt berechnete Felder und fügt sie dem Datensatz hinzu.        |  `Extend` wird verwendet, wenn die normalisierten Felder aus den ursprünglichen Daten analysiert oder transformiert werden. Weitere Informationen finden Sie im Beispiel im Abschnitt [Analyse](#parsing) weiter oben.     |
|**project-rename**     | Benennt Felder um.        |     Verwenden Sie `project-rename`, wenn ein Feld im ursprünglichen Ereignis vorhanden ist und nur umbenannt werden muss. <br><br>Das umbenannte Feld verhält sich weiterhin wie ein integriertes Feld, und Vorgänge für das Feld weisen eine wesentlich bessere Leistung auf.   |
|**project-away**     |      Entfernt Felder.   |Verwenden Sie `project-away` für spezifische Felder, die aus dem Resultset entfernt werden sollen.         |
|**project**     |  Wählt Felder aus, die bereits vorhanden waren oder als Teil der Anweisung erstellt wurden. Entfernt alle anderen Felder.       | Wird nicht für die Verwendung in einem Parser empfohlen, da der Parser keine anderen nicht normalisierten Felder entfernen soll. <br><br>Wenn Sie spezifische Felder entfernen möchten, z. B. temporäre bei der Analyse verwendete Werte, verwenden Sie `project-away`, um sie aus den Ergebnissen zu entfernen.      |

<br>

### <a name="handle-parsing-variants"></a>Verarbeiten von Analysevarianten

In vielen Fällen enthalten Ereignisse in einem Ereignisstream Varianten, die eine andere Analyselogik erfordern. 

Es erscheint häufig sinnvoll, einen Parser aus verschiedenen Unterparsern zu erstellen, die jeweils eine andere Variante der Ereignisse verarbeiten, für die eine andere Analyselogik erforderlich ist. Diese Unterparser, die jeweils eine Abfrage sind, werden dann mit dem Operator `union` vereinheitlicht. Dieser Ansatz ist zwar praktisch, wird jedoch *nicht* empfohlen, da die Leistung des Parsers erheblich beeinträchtigt wird.

Beachten Sie bei der Verarbeitung von Varianten folgende Leitfäden:

|Szenario  |Verarbeitung  |
|---------|---------|
|Die verschiedenen Varianten stellen *verschiedene* Ereignistypen dar, die häufig verschiedenen Schemas zugeordnet sind.     |  Verwenden Sie separate Parser.       |
|Die verschiedenen Varianten stellen den *gleichen* Ereignistyp dar, sind jedoch unterschiedlich strukturiert.     |   Wenn die Varianten bekannt sind, z. B. wenn eine Methode zur Unterscheidung der Ereignisse vor der Analyse vorhanden ist, verwenden Sie den Operator `case`, um den richtigen Operator `extract_all` für die Ausführung und Feldzuordnung auszuwählen, wie im Parser [Infoblox DNS](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM/Infoblox) veranschaulicht.      |
|Wenn `union` unvermeidbar ist     |  Wenn die Verwendung von `union` unvermeidbar ist, sind folgende Leitfäden zu beachten:<br><br>- Führen Sie mithilfe integrierter Felder eine Vorfilterung in jeder der Unterabfragen durch. <br>- Stellen Sie sicher, dass sich die Filter gegenseitig ausschließen. <br>- Analysieren Sie weniger wichtige Informationen ggf. nicht, um so die Anzahl der Unterabfragen zu reduzieren.       |

<br>

### <a name="deploy-parsers"></a>Bereitstellen von Parsern

Sie können Parser manuell bereitstellen, indem Sie sie auf die Azure Monitor-Seite **Protokolle** kopieren und die Änderung speichern. Diese Methode empfiehlt sich bei Tests. Weitere Informationen finden Sie unter [Erstellen einer Funktion](../azure-monitor/logs/functions.md).

Um jedoch eine große Anzahl von Parsern bereitzustellen, wird die Verwendung einer ARM-Vorlage empfohlen. Beispielsweise können Sie eine ARM-Vorlage verwenden, wenn Sie eine vollständige Normalisierungslösung bereitstellen, die einen quellenunabhängigen Parser und mehrere quellenspezifische Parser enthält, oder wenn Sie mehrere Parser für verschiedene Schemas für eine Quelle bereitstellen.

Weitere Informationen finden Sie in der [ARM-Vorlage des generischen Parsers](https://github.com/Azure/Azure-Sentinel/tree/master/Tools/ARM-Templates/ParserQuery). Verwenden Sie diese Vorlage als Ausgangspunkt, und stellen Sie Ihren Parser bereit, indem Sie ihn während der Bereitstellung der Vorlage an der relevanten Stelle einfügen. Ein Beispiel finden Sie in der [ARM-Vorlage für DNS-Parser](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/ASimDns/ARM).

> [!TIP]
> ARM-Vorlagen können verschiedene Ressourcen enthalten, sodass Ihre Parser zusammen mit Connectors, Analyseregeln oder Watchlists bereitgestellt werden können, um nur einige nützliche Optionen zu nennen. Zum Beispiel kann Ihr Parser auf eine Watchlist verweisen, die parallel bereitgestellt wird.
>

### <a name="use-parsers-in-normalized-content"></a>Verwenden von Parsern in normalisierten Inhalten

Azure Sentinel-Inhalte umfassen Analyseregeln, Hunting-Abfragen und Arbeitsmappen, die mit quellenunabhängigen Normalisierungsparsern verwendet werden können.

- Suchen Sie normalisierte, integrierte Inhalte in Azure Sentinel-Katalogen und Azure Sentinel-[Lösungen](sentinel-solutions-catalog.md).

- Erstellen Sie normalisierte Inhalte selbst, oder ändern Sie vorhandene Inhalte so, dass normalisierte Daten verwendet werden.

####  <a name="identify-built-in-normalized-content"></a>Identifizieren integrierter normalisierter Inhalte

Die Dokumentation für die einzelnen Schemas enthält jeweils eine Liste der Inhaltselemente, die mit jedem normalisierten Schema verwendet werden können. Die Schemainhalte werden regelmäßig aktualisiert und orientieren sich an den folgenden Leitfäden:

-   **Inhaltselemente, die sich auf ein normalisiertes Schema beziehen**, enthalten das Schema als Teil des Namens. Beispielsweise haben die Namen von Analyseregeln, die sich auf das [normalisierte DNS-Schema](dns-normalization-schema.md) beziehen, das Suffix `(Normalized DNS)`.

-   **Inhaltselemente, bei denen das normalisierte Schema neben anderen Datentypen berücksichtigt wird**, werden nicht durch ein Suffix gekennzeichnet. Suchen Sie in diesem Fall auf GitHub nach dem Namen des Parsers für das normalisierte Schema, um alle Inhaltselemente zu identifizieren.

#### <a name="modifying-your-content-to-use-normalized-data"></a>Ändern von Inhalten zur Verwendung normalisierter Daten

So aktivieren Sie benutzerdefinierte Inhalte zur Verwendung der Normalisierung

- Ändern Sie Ihre Abfragen so, dass die für die Abfrage relevanten quellenunabhängigen Parser verwendet werden.
- Ändern Sie die Feldnamen in der Abfrage so, dass die Namen der normalisierten Schemafelder verwendet werden.
- Ändern Sie ggf. die Bedingungen so, dass die normalisierten Werte der Felder in der Abfrage verwendet werden.

Als Beispiel dient die DNS-Analyseregel **Rare client observed with high reverse DNS lookup count**, die auf DNS-Ereignisse angewandt wird, die von Infoblox-DNS-Servern gesendet werden:

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

Bei der folgenden Version handelt es sich um die quellenunabhängige Version, bei der durch Normalisierung für jede Quelle, die DNS-Abfrageereignisse enthält, die gleiche Erkennung erfolgt:

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

Die normalisierte, quellenunabhängige Version weist die folgenden Unterschiede auf:

- Anstelle des Infoblox-Parsers wird der normalisierte Parser `imDns` verwendet.

- `imDns` ruft nur DNS-Abfrageereignisse ab, sodass eine Überprüfung des Ereignistyps, wie sie in der Infoblox-Version über `where ProcessName =~ "named" and Log_Type =~ "client"` erfolgt, nicht erforderlich ist.

- Die Felder `ResponseCodeName` und `SrcIpAddr` werden anstelle von `ResponseCode` bzw. `Client_IP` verwendet.

#### <a name="enable-normalized-content-to-use-your-custom-data"></a>Aktivieren normalisierter Inhalte zur Verwendung von benutzerdefinierten Daten

Durch die Normalisierung können Sie eigene Inhalte und integrierte Inhalte mit Ihren benutzerdefinierten Daten verwenden.

Bei einem benutzerdefinierten Connector, der Aktivitätsprotokolle von DNS-Abfragen empfängt, können Sie sicherstellen, dass in den Aktivitätsprotokollen der DNS-Abfragen alle normalisierten DNS-Inhalte wie folgt genutzt werden:

-   [Erstellen eines normalisierten Parsers](#parsers) für Ihren benutzerdefinierten Connector. Wenn der Parser für das Produkt `Xxx` des Herstellers `Yyy` gilt, hat er den Namen `vimDnsYyyXxx`.

-   Ändern des quellenunabhängigen Parsers `imDns`, sodass er auch Ihren Parser enthält, indem er in der Liste der Parser in der Anweisung `union` hinzugefügt wird. Beispiel:

    ```kusto
    union isfuzzy=true 
    vimDnsEmpty, 
    vimDnsCiscoUmbrella, 
    vimDnsInfobloxNIOS, 
    vimDnsMicrosoftOMS,
    vimDnsYyyXxx
    ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wird die Normalisierung in Azure Sentinel und das Azure Sentinel-Informationsmodell beschrieben.

Weitere Informationen finden Sie unter:

- [Azure Sentinel: Referenz zum Netzwerknormalisierungsschema](normalization-schema.md)
- [Azure Sentinel: Referenz zum DNS-Normalisierungsschema](dns-normalization-schema.md)
- [Azure Sentinel: Referenz zum Dateiereignis-Normalisierungsschema (öffentliche Vorschau)](file-event-normalization-schema.md)
- [Azure Sentinel: Referenz zum Prozessereignis-Normalisierungsschema](process-events-normalization-schema.md)
- [Azure Sentinel: Referenz zum Authentifizierungsnormalisierungsschema (öffentliche Vorschau)](authentication-normalization-schema.md)