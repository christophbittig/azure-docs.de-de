---
title: Referenz zum DNS-Normalisierungsschema von Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird das DNS-Normalisierungsschema von Azure Sentinel beschrieben.
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
ms.topic: reference
ms.date: 06/15/2021
ms.author: bagol
ms.openlocfilehash: 21775c8d6e9743b65a791abb946c571862b68156
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617562"
---
# <a name="azure-sentinel-dns-normalization-schema-reference-public-preview"></a>Referenz zum DNS-Normalisierungsschema von Azure Sentinel (Public Preview)

Das DNS-Informationsmodell dient zur Beschreibung von Ereignissen, die von einem DNS-Server oder einem DNS-Sicherheitssystem gemeldet werden. Es wird von Azure Sentinel verwendet, um quellenagnostische Analysen zu ermöglichen.

Weitere Informationen finden Sie unter [Normalisierung und das Azure Sentinel-Informationsmodell (ASIM)](normalization.md).

> [!IMPORTANT]
> Das DNS-Normalisierungsschema befindet sich derzeit in der VORSCHAUPHASE. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
>
> In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="guidelines-for-collecting-dns-events"></a>Leitlinien zum Sammeln von DNS-Ereignissen

DNS ist insofern ein besonderes Protokoll, als es sich über eine große Anzahl von Computern erstrecken kann. Da DNS außerdem UDP verwendet, sind Anforderungen und Antworten voneinander entkoppelt und stehen nicht in direktem Zusammenhang.

Die folgende Abbildung zeigt einen vereinfachten DNS-Anforderungsflow, einschließlich vier Segmenten. Eine reale Anforderung kann komplexer sein, sobald mehr Segmente beteiligt sind.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Vereinfachter DNS-Anforderungsflow.":::

Da Anforderungs- und Antwortsegmente im DNS-Anforderungsflow nicht direkt miteinander verbunden sind, kann die vollständige Protokollierung zu erheblicher Duplizierung führen.

Das nützlichste zu protokollierende Segment ist die Antwort an den Client, die die Abfragen der Domänennamen, das Lookupergebnis und die IP-Adresse des Clients enthält. Zwar protokollieren viele DNS-Systeme nur dieses Segment, aber es ist sinnvoll, auch die anderen Teile zu protokollieren. Ein DNS-Cache-Poisoning-Angriff nutzt beispielsweise häufig gefälschte Antworten von einem vorgelagerten Server aus.

Wenn Ihre Datenquelle die vollständige DNS-Protokollierung unterstützt und Sie sich entschieden haben, mehrere Segmente zu protokollieren, müssen Sie Ihre Abfragen anpassen, um in Azure Sentinel Datenduplizierung zu vermeiden.

Beispielsweise können Sie Ihre Abfrage mit der folgenden Normalisierung ändern:

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>Parser

### <a name="source-agnostic-parsers"></a>Quellenunabhängige Parser

Um die quellenunabhängigen Parser zu verwenden, die alle integrierten Parser vereinheitlichen, und um sicherzustellen, dass Ihre Analyse über alle konfigurierten Quellen hinweg läuft, verwenden Sie die folgenden KQL-Funktionen als Tabellennamen in Ihrer Abfrage:

| Name | BESCHREIBUNG | Verwendungsanweisungen |
| --- | --- | --- |
| **imDNS** | Aggregativer Parser, der *union* verwendet, um normalisierte Ereignisse aus allen DNS-Quellen einzubeziehen. |Aktualisieren Sie diesen Parser, wenn Sie Quellen zu quellenagnostischen Analysen hinzufügen oder daraus entfernen möchten. <br><br>Verwenden Sie diese Funktion in Ihren quellenagnostischen Abfragen.|
| **ASimDNS** | Ähnlich wie die `imDns`Funktion, aber ohne Parameterunterstützung, und zwingt daher die Seitenauswahl **Logs** nicht, den Wert `custom` zu verwenden. |Aktualisieren Sie diesen Parser, wenn Sie Quellen zu quellenagnostischen Analysen hinzufügen oder daraus entfernen möchten.<br><br>– Verwenden Sie diese Funktion in Ihren quellenagnostischen Abfragen, wenn Sie nicht vorhaben, Parameter zu verwenden.|
| **vimDNS\<vendor\>\<product\>** | Quellspezifische Parser implementieren die Normalisierung für eine bestimmte Quelle, z. B. *imDNSWindowsOMS*. |Fügen Sie einen quellspezifischen Parser für eine Quelle hinzu, wenn kein integrierter Normalisierungsparser vorhanden ist. Aktualisieren Sie den `im`aggregativen Q-Parser, um einen Verweis auf Ihren neuen Parser aufzunehmen. <br><br>Ändern Sie einen quellspezifischen Parser so, dass Analyse- und Normalisierungsprobleme behoben werden.<br><br>Verwenden Sie einen quellspezifischen Parser für quellspezifische Analysen.|
| **ASimDNS\<vendor\>\<product\>** | Quellenspezifische Parser implementieren die Normalisierung für eine bestimmte Quelle. Im Gegensatz zu den `vim*`-Funktionen unterstützen die `ASimDNS*`-Funktionen keine Parameter. |Fügen Sie einen quellspezifischen Parser für eine Quelle hinzu, wenn kein integrierter Normalisierungsparser vorhanden ist. Ändern Sie den aggregativen Parser `ASim` so, dass er einen Verweis auf Ihren neuen Parser enthält.<br><br>Ändern Sie einen quellspezifischen Parser so, dass Analyse- und Normalisierungsprobleme behoben werden.<br><br>– Hinzufügen eines `ASim` quellenspezifischen Parsers für eine Quelle, wenn es keinen eingebauten normalisierenden Parser gibt.|
| | | |

Die Parser können über das [GitHub Repository von Azure Sentinel](https://aka.ms/azsentinelDNS) bereitgestellt werden.

### <a name="built-in-source-specific-parsers"></a>Integrierte quellenspezifische Parser

Azure Sentinel bietet die folgenden integrierten, produktspezifischen DNS-Parser:

  - **Microsoft DNS Server**, gesammelt mit dem Log Analytics Agent – ASimDnsMicrosoftOMS (regulär), vimDnsMicrosoftOMS (parametrisiert)
  - **Cisco Umbrella** – ASimDnsCiscoUmbrella (regulär), vimDnsCiscoUmbrella (parametrisiert)
  - **Infoblox NIOS** – ASimDnsInfobloxNIOS (regulär), vimDnsInfobloxNIOS (parametrisiert)
  - **GCPDNS** – ASimDnsGcp (regulär), vimDnsGcp (parametrisiert)
  - **Corelight Zeek DNS-Ereignisse** – ASimDnsCorelightZeek (regulär), vimDnsCorelightZeek (parametrisiert)
  - **Sysmon für Windows** (Ereignis 22), das entweder mit dem Log Analytics Agent oder dem Azure Monitor Agent gesammelt wird und sowohl die Event- als auch die WindowsEvent-Tabelle unterstützt, ASimDnsMicrosoftSysmon (regulär), vimDnsMicrosoftSysmon (parametrisiert)

Die Parser können über das [GitHub Repository von Azure Sentinel](https://aka.ms/azsentinelDNS) bereitgestellt werden.

### <a name="add-your-own-normalized-parsers"></a>Hinzufügen eigener normalisierter Parser

Wenn Sie benutzerdefinierte Parser für das Dns-Informationsmodell implementieren, benennen Sie Ihre KQL-Funktionen mit der folgenden Syntax: `vimDns<vendor><Product` für parametrisierte Parser und `ASimDns<vendor><Product` für reguläre Parser.

### <a name="filtering-parser-parameters"></a>Filtern von Parser-Parametern

Die Parser `im` und `vim*` unterstützen [Filterparameter](normalization-about-parsers.md#optimized-parsers). Indem diese Parser optional sind, können sie die Leistung Ihrer Abfrage verbessern.

Die folgenden Filterparameter sind verfügbar:

| Name     | type      | Beschreibung |
|----------|-----------|-------------|
| **StartTime** | datetime | Filtert nur DNS-Abfragen, die zu oder nach dieser Zeit ausgeführt wurden. |
| **EndTime** | datetime | Filtert nur DNS-Abfragen, deren Ausführung zu oder vor diesem Zeitpunkt beendet wurde. |
| **srcipaddr** | Zeichenfolge | Filtert nur DNS-Abfragen von dieser Quell-IP-Adresse. |
| **domain_has_any**| dynamisch | Filtert nur DNS-Abfragen, bei denen das `domain` (oder `query`) irgendeinen der aufgeführten Domänennamen enthält, auch als Teil der Ereignisdomäne.
| **responsecodename** | Zeichenfolge | Filtern nur DNS-Abfragen, bei denen der Name des Antwortcodes mit dem angegebenen Wert übereinstimmt. Zum Beispiel: NXDOMAIN |
| **response_has_ipv4** | Zeichenfolge | Filtert nur DNS-Abfragen, bei denen das Antwortfeld mit der angegebenen IP-Adresse oder dem IP-Adresspräfix anfängt. Verwenden Sie diesen Parameter, wenn Sie nach einer einzelnen IP-Adresse oder einem Präfix filtern möchten. Für Quellen, die keine Antwort liefern, werden keine Ergebnisse zurückgegeben.|
| **Antwort_hat_beliebigen_präfix** | dynamisch| Filtert nur DNS-Abfragen, bei denen das Antwortfeld mit einer der aufgeführten IP-Adressen oder IP-Adress-Präfixe beginnt. Verwenden Sie diesen Parameter, wenn Sie nach einer Liste von IP-Adressen oder Präfixen filtern möchten. Für Quellen, die keine Antwort liefern, werden keine Ergebnisse zurückgegeben. |
| **EventType**| Zeichenfolge | Filtert nur DNS-Abfragen des angegebenen Typs. Wenn kein Wert angegeben wird, werden nur Lookup-Abfragen zurückgegeben. |
||||

Um Ergebnisse mit Hilfe eines Parameters zu filtern, müssen Sie den Parameter in Ihrem Parser angeben. 

## <a name="normalized-content"></a>Normalisierter Inhalt

In der Unterstützung für das DNS-ASIM-Schema ist auch die Unterstützung für die folgenden integrierten Analyseregeln mit normalisierten DNS-Parsern enthalten. Im Folgenden finden Sie Links zum Azure Sentinel GitHub-Repository. Sie können diese Regeln aber auch in dem [Azure Sentinel Analytics-Regelkatalog](detect-threats-built-in.md) finden. Verwenden Sie die verlinkten GitHub-Seiten, um alle relevanten Hunting-Abfragen für die aufgeführten Regeln zu kopieren.

Die folgenden integrierten Analyseregeln funktionieren jetzt mit normalisierten DNS-Parsern:
 - (Vorschau) Tl map Domain-Entität zu Dns-Ereignis (normalisierter DNS)
 - (Vorschau) Tl map IP entity to DnsEvents (Normalized DNS)
 - [Potenzielle DGA erkannt (ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
 - [Übermäßige NXDOMAIN-DNS-Abfragen (normalisiertes DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [DNS-Ereignisse im Zusammenhang mit Miningpools (normalisiertes DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [DNS-Ereignisse im Zusammenhang mit ToR-Proxys (normalisiertes DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [Bekannte Barium-Domänen](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [Bekannte Barium-IP-Adressen](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Exchange Server: im März 2021 offengelegte Sicherheitsrisiken – IoC-Übereinstimmung](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [Known GALLIUM domains and hashes (Bekannte GALLIUM-Domänen und -Hashes)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [Known IRIDIUM IP (Bekannte IRIDIUM-IP-Adresse)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - Domain and IP IOCs - March 2021 (NOBELIUM – Domänen- und IP-IOCs – März 2021)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [Known Phosphorus group domains/IP (Bekannte Phosphorus-Gruppendomänen/IP-Adressen)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [Bekannte STRONTIUM-Gruppendomänen – Juli 2019](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Solorigate Network Beacon (Solorigate-Netzwerkbeacon)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [THALLIUM domains included in DCU takedown (Im DCU-Takedown enthaltene THALLIUM-Domänen)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [Bekannte ZINC Comebacker- und Klackring-Schadsoftwarehashes](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)


## <a name="schema-details"></a>Schemadetails

Das DNS-Informationsmodell orientiert sich am [OSSEM-DNS-Entitätsschema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

Weitere Informationen finden Sie in der [Referenz zu den Domain Name System-Parametern der Internet Assigned Numbers Authority (IANA) ](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### <a name="log-analytics-fields"></a>Log Analytics-Felder

Die folgenden Felder werden von Log Analytics für jeden Datensatz generiert und können beim [Erstellen eines benutzerdefinierten Connectors](create-custom-connector.md) überschrieben werden.

| **Feld** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | Datum/Uhrzeit | Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde. |
| **\_ResourceId** | guid | Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden. |
| **Type** | String | Die ursprüngliche Tabelle, aus der der Datensatz abgerufen wurde. Dieses Feld ist nützlich, wenn ein und dasselbe Ereignis über mehrere Kanäle in verschiedenen Tabellen empfangen werden kann und die gleichen EventVendor- und EventProduct-Werte hat.<br><br>Ein Sysmon-Ereignis kann z. B. entweder in der Tabelle Event oder in der Tabelle WindowsEvent gesammelt werden. |
| | | |

> [!NOTE]
> Weitere Log Analytics-Felder, die weniger sicherheitsrelevant sind, werden mit [Azure Monitor](../azure-monitor/logs/log-standard-columns.md) dokumentiert.
> 

### <a name="event-fields"></a>Ereignisfelder

Ereignisfelder sind allen Schemas gemeinsam und beschreiben die Aktivität selbst sowie das meldende Gerät.

| **Feld** | **Klasse** | **Typ**  | **Diskussion (Discussion)** |
| --- | --- | --- | --- |
| **EventMessage** | Optional | String | Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert. |
| **EventCount** | Obligatorisch. | Integer | Die Anzahl der Ereignisse, die vom Datensatz beschrieben werden. <br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen. <br><br>Für andere Quellen muss dieses Feld auf **1** festgelegt werden. <br><br>Beispiel:`1`|
| **EventStartTime** | Obligatorisch. | Datum/Uhrzeit | Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, geben Sie mithilfe dieses Felds die Zeit an, zu der das erste Ereignis generiert wurde. <br><br>Versehen Sie in anderen Fällen das Feld [TimeGenerated](#timegenerated) mit einem Alias. |
| **EventEndTime** | Alias || Alias für das Feld [TimeGenerated](#timegenerated). |
| **EventType** | Obligatorisch. | Enumerated | Gibt den vom Datensatz gemeldeten Vorgang an. <br><Br> Bei DNS-Einträgen ist dieser Wert der [Code des DNS-Vorgangs](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Beispiel: `lookup`|
| **EventSubType** | Optional | Enumerated | Entweder **Anforderung** oder **Antwort**. Bei den meisten Quellen werden [nur die Antworten protokolliert](#guidelines-for-collecting-dns-events), daher ist der Wert oft **Antwort**.  |
| **EventResult** | Obligatorisch. | Enumerated | Einer der folgenden Werte: **Success**, **Partial**, **Failure**, **NA** (Not Applicable/nicht zutreffend).<br> <br>Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die in diese Werte normalisiert werden sollten. Alternativ kann die Quelle auch nur das Feld [EventResultDetails](#eventresultdetails) angeben, das analysiert werden muss, um den EventResult-Wert abzuleiten.<br> <br>Wenn dieser Datensatz eine Anforderung und keine Antwort darstellt, legen Sie ihn auf **NA** fest. <br><br>Beispiel: `Success`|
| <a name=eventresultdetails></a>**EventResultDetails** | Alias | | Grund oder Details für das im Feld **_EventResult_** gemeldete Ergebnis. Versieht das Feld [ResponseCodeName](#responsecodename) mit einem Alias.|
| **EventOriginalUid** | Optional | String | Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt. |
| **EventOriginalType**   | Optional    | String  |  Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `lookup` |
| <a name ="eventproduct"></a>**EventProduct** | Obligatorisch. | String | Das Produkt, das das Ereignis erzeugt. Dieses Feld ist möglicherweise im Quelldatensatz nicht vorhanden. In diesem Fall muss es vom Parser festgelegt werden. <br><br>Beispiel: `DNS Server` |
| **EventProductVersion** | Optional | String | Die Version des Produkts, das das Ereignis erzeugt. Dieses Feld ist möglicherweise im Quelldatensatz nicht vorhanden. In diesem Fall muss es vom Parser festgelegt werden. <br><br>Beispiel: `12.1` |
| **EventVendor** | Obligatorisch. | String | Der Hersteller des Produkts, das das Ereignis erzeugt. Dieses Feld ist möglicherweise im Quelldatensatz nicht vorhanden. In diesem Fall muss es vom Parser festgelegt werden.<br><br>Beispiel: `Microsoft`|
| **EventSchemaVersion** | Obligatorisch. | String | Die Version des hier dokumentierten Schemas ist **0.1.2**. |
| **EventSchema** | Obligatorisch. | String | Der Name des hier dokumentierten Schemas ist **Dns**. |
| **EventReportUrl** | Optional | String | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die weitere Informationen zum Ereignis enthält. |
| <a name="dvc"></a>**Dvc** | Obligatorisch.       | String     |    Ein eindeutiger Bezeichner des Geräts, auf dem das Ereignis aufgetreten ist. <br><br>Dieses Feld kann ein Alias für die Felder [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die es kein offensichtliches Gerät gibt, den gleichen Wert wie das Feld [Ereignisprodukt](#eventproduct). <br><br>Beispiel: `ContosoDc.Contoso.Azure`       |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | Empfohlen | IP-Adresse |  Die IP-Adresse des Geräts, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `45.21.42.12` |
| <a name ="dvchostname"></a>**DvcHostname**         | Empfohlen | Hostname   | Der Hostname des Geräts, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `ContosoDc.Contoso.Azure`                |
| <a name ="dvcid"></a>**DvcId**               | Optional    | String     | Die eindeutige ID des Geräts, auf dem das Prozessereignis aufgetreten ist. <br><br>Beispiel: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| <a name=additionalfields></a>**AdditionalFields** | Optional | Dynamisch | Wenn Ihre Quelle andere Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie ihm die zusätzlichen Informationen als Schlüssel-Wert-Paare hinzu. |
| | | | |

### <a name="dns-specific-fields"></a>DNS-spezifische Felder

Die folgenden Felder gelten speziell für DNS-Ereignisse. Viele von ihnen haben jedoch Ähnlichkeiten in anderen Schemas und befolgen daher die gleiche Benennungskonvention.

| **Feld** | **Klasse** | **Typ** | **Hinweise** |
| --- | --- | --- | --- |
| **SrcIpAddr** | Obligatorisch. | IP-Adresse | Die IP-Adresse des Clients, der die DNS-Anforderung sendet. Bei einer rekursiven DNS-Anforderung ist dieser Wert in der Regel das meldende Gerät und in den meisten Fällen auf `127.0.0.1` festgelegt.<br><br>Beispiel: `192.168.12.1` |
| **SrcPortNumber** | Optional | Integer | Quellport der DNS-Abfrage.<br><br>Beispiel: `54312` |
| **DstIpAddr** | Optional | IP-Adresse | Die IP-Adresse des Servers, der die DNS-Anforderung empfängt. Bei einer herkömmlichen DNS-Anforderung ist dieser Wert in der Regel das meldende Gerät und in den meisten Fällen auf `127.0.0.1` festgelegt.<br><br>Beispiel: `127.0.0.1` |
| **DstPortNumber** | Optional | Integer  | Zielportnummer.<br><br>Beispiel: `53` |
| **IpAddr** | Alias | | Alias für SrcIpAddr |
| <a name=query></a>**DnsQuery** | Obligatorisch. | FQDN | Die aufzulösende Domäne. <br><br>**Hinweis**: Einige Quellen senden diese Abfrage in unterschiedlichen Formaten. Beispielsweise im DNS-Protokoll selbst enthält die Abfrage einen Punkt ( **.** ) am Ende, der entfernt werden muss.<br><br>Das DNS-Protokoll lässt zwar mehrere Abfragen in einer einzigen Anforderung zu, aber dieses Szenario ist, wenn überhaupt, nur selten anzutreffen. Wenn die Anforderung mehrere Abfragen enthält, speichern Sie die erste in diesem Feld und die übrigen optional im Feld [AdditionalFields](#additionalfields).<br><br>Beispiel: `www.malicious.com` |
| **Domain** | Alias | | Alias für [Abfrage](#query). |
| **DnsQueryType** | Optional | Integer | Dieses Feld kann [Codes von DNS-Ressourcendatensatztypen](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml) enthalten. <br><br>Beispiel: `28`|
| **DnsQueryTypeName** | Obligatorisch. | Enumerated | Das Feld kann [Namen von DNS-Ressourcendatensatztypen](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml) enthalten. <br><br>**Hinweis**: IANA legt keine Groß-/Kleinschreibung für die Werte fest, sodass Groß-/Kleinschreibung für Analysen nach Bedarf normalisiert werden muss. Wenn die Quelle nur einen numerischen Code für den Abfragetyp und keinen Namen dafür angibt, muss der Parser zum Anreichern mit diesem Wert eine Nachschlagetabelle enthalten.<br><br>Beispiel: `AAAA`|
| <a name=responsename></a>**DnsResponseName** | Optional | String | Der Inhalt der Antwort, wie im Datensatz enthalten.<br> <br> Die DNS-Antwortdaten sind auf den meldenden Geräten inkonsistent, schwierig zu analysieren und von weniger Nutzen für quellagnostische Analysen. Daher muss das Informationsmodell nicht analysiert und normalisiert werden. Azure Sentinel stellt Antwortinformationen mit einer Hilfsfunktion bereit. Weitere Informationen finden Sie unter [Behandeln der DNS-Antwort](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Obligatorisch. | Enumerated | Der [DNS-Antwortcode](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Hinweis**: IANA legt keine Groß-/Kleinschreibung für die Werte fest, sodass Groß-/Kleinschreibung für Analysen normalisiert werden muss. Wenn die Quelle nur einen numerischen Antwortcode und keinen Namen dafür angibt, muss der Parser zum Anreichern mit diesem Wert eine Nachschlagetabelle enthalten. <br><br> Wenn dieser Datensatz eine Anforderung und keine Antwort darstellt, legen Sie ihn auf **NA** fest. <br><br>Beispiel: `NXDOMAIN` |
| **DnsResponseCode** | Optional | Integer | Der [numerische DNS-Antwortcode](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Beispiel: `3`|
| **TransactionIdHex** | Empfohlen | String | Die eindeutige hexadezimale DNS-Transaktions-ID. |
| **NetworkProtocol** | Optional | Enumerated | Das vom Netzwerkauflösungsereignis verwendete Transportprotokoll. Der Wert kann **UDP** oder **TCP** sein und wird für DNS in der Regel auf **UDP** festgelegt. <br><br>Beispiel: `UDP`|
| **DnsQueryClass** | Optional | Integer | Die [DNS-Klassen-ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>In der Praxis wird nur die **IN**-Klasse (ID 1) verwendet, wodurch dieses Feld weniger nützlich ist.|
| **DnsQueryClassName** | Optional | String | Der [DNS-Klassenname](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>In der Praxis wird nur die **IN**-Klasse (ID 1) verwendet, wodurch dieses Feld weniger nützlich ist. <br><br>Beispiel: `IN`|
| <a name=flags></a>**DnsFlags** | Optional | Liste von Zeichenfolgen | Das Feld mit Flags, wie vom meldenden Gerät bereitgestellt. Wenn Flaginformationen in mehreren Feldern bereitgestellt werden, verketten Sie sie mit Komma als Trennzeichen. <br><br>Da DNS-Flags schwierig zu analysieren sind und seltener in Analysen verwendet werden, sind Analyse und Normalisierung nicht erforderlich. Azure Sentinel stellt Informationen zu Flags mit einer Hilfsfunktion bereit. Weitere Informationen finden Sie unter [Behandeln der DNS-Antwort](#handling-dns-response). <br><br>Beispiel: `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  Optional | String | Eine DNS-Ereignisquelle kann auch die Kategorie der angeforderten Domänen nachschlagen. Das Feld heißt **_UrlCategory_**, um es am Azure Sentinel-Netzwerkschema zu orientieren. <br><br>**_DomainCategory_** wird als ein zu DNS passender Alias hinzugefügt. <br><br>Beispiel: `Educational \\ Phishing` |
| **DomainCategory** | Optional | Alias | Alias für [UrlCategory](#UrlCategory). |
| **ThreatCategory** | Optional | String | Wenn eine DNS-Ereignisquelle auch sicherheitsrelevante DNS-Informationen liefert, kann sie auch das DNS-Ereignis auswerten. So kann sie beispielsweise die IP-Adresse oder Domäne in einer Threat Intelligence-Datenbank suchen und der Domäne oder IP-Adresse eine Bedrohungskategorie zuweisen. |
| **EventSeverity** | Optional | String | Wenn eine DNS-Ereignisquelle auch sicherheitsrelevante DNS-Informationen liefert, kann sie das DNS-Ereignis auswerten. So kann sie beispielsweise in einer Threat-Intelligence-Datenbank nach der IP-Adresse oder Domäne suchen und auf der Grundlage der Auswertung einen Schweregrad zuweisen. <br><br>Beispiel: `Informational`|
| **DvcAction** | Optional | String | Wenn eine DNS-Ereignisquelle auch sicherheitsrelevante DNS-Informationen liefert, kann sie eine Aktion auf die Anforderung anwenden, sie z. B. blockieren. <br><br>Beispiel: `Blocked` |
| **DnsFlagsAuthenticated** | Optional | Boolean | Das DNS Flag `AD`, das mit DNSSEC zusammenhängt, zeigt in einer Antwort an, dass alle in den Antwort- und Autoritätsabschnitten der Antwort enthaltenen Daten vom Server gemäß den Richtlinien dieses Servers überprüft wurden. Siehe [RFC 3655 Abschnitt 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) für weitere Informationen.    |
| **DnsFlagsAuthoritative** | Optional | Boolean | Das DNS Flag `AA` zeigt an, ob die Antwort des Servers autoritativ war    |
| **DnsFlagsCheckingDisabled** | Optional | Boolean | Das DNS Flag `CD`, das mit DNSSEC zusammenhängt, zeigt in einer Abfrage an, dass nicht verifizierte Daten für das System, das die Abfrage sendet, akzeptabel sind. Siehe [RFC 3655 Abschnitt 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) für weitere Informationen.   |
| **DnsFlagsRecursionAvailable** | Optional | Boolean | Das DNS Flag `RA` zeigt in einer Antwort an, dass dieser Server rekursive Abfragen unterstützt.   |
| **DnsFlagsRecursionDesired** | Optional | Boolean | Das DNS Flag `RD` zeigt in einer Anforderung an, dass der Client möchte, dass der Server rekursive Abfragen verwendet.   |
| **DnsFlagsTruncates** | Optional | Boolean | Das DNS Flag `TC` zeigt an, dass eine Antwort abgeschnitten wurde, da sie die maximale Antwortgröße überschritten hat.  |
| **DnsFlags** | Optional | Boolean | Das DNS Flag `Z` ist ein veraltetes DNS-Flag, das möglicherweise von älteren DNS-Systemen gemeldet wird.  |
| | | | |

### <a name="deprecated-aliases"></a>Veraltete Aliase

Bei den folgenden Feldern handelt es sich um Aliasnamen, die zwar derzeit veraltet sind, aber aus Gründen der Abwärtskompatibilität beibehalten werden. Sie werden am 31. Dezember 2021 aus dem Schema entfernt.

- Query (Alias für DnsQuery)
- QueryType (Alias für DnsQueryType)
- QueryTypeName (Alias für DnsQueryTypeName)
- ResponseName (Alias für DnsReasponseName)
- ResponseCodeName (Alias für DnsResponseCodeName)
- ResponseCode (Alias für DnsResponseCode)
- QueryClass (Alias für DnsQueryClass)
- QueryClassName (Alias für DnsQueryClassName)
- Flags (Alias für DnsFlags)

### <a name="added-fields"></a>Hinzugefügte Felder

Die folgenden Felder wurden der Version 0.1.2 des Schemas hinzugefügt:
- Ereignisschema – derzeit optional, wird aber am 1. Januar 2022 obligatorisch.
- Dediziertes Flag-Feld, das das kombinierte **[Flags](#flags)** -Feld `DnsFlagsAuthoritative`, `DnsFlagsCheckingDisabled`, `DnsFlagsRecursionAvailable`, `DnsFlagsRecursionDesired`, `DnsFlagsTruncates` und `DnsFlagsZ` ergänzt.

### <a name="additional-entities"></a>Zusätzliche Entitäten

Ereignisse ergeben sich im Zusammenhang mit Entitäten wie Benutzern, Hosts, Prozessen oder Dateien. Zur Beschreibung jeder Entität können mehrere Felder erforderlich sein. Zum Beispiel:

- Ein Host kann sowohl einen Namen als auch eine IP-Adresse aufweisen.
- Ein einzelner Datensatz kann mehrere Entitäten desselben Typs enthalten, z. B. einen Quell- und Zielhost.

Das in diesem Artikel dokumentierte DNS-Schema enthält Felder, die Entitäten beschreiben. Wenn Ihre Quelle weitere Informationen zur Beschreibung von Entitäten enthält, fügen Sie weitere Felder auf Grundlage der in der nachstehenden Tabelle aufgeführten Entitäten hinzu, um diese Informationen zu erfassen.

Weitere Informationen hierzu finden Sie unter [Normalisierung in Azure Sentinel](normalization.md).


| **Entität** | **Fields** | **Typ** | **Pflichtfelder** | **Hinweise** |
| --- | --- | --- | --- | --- |
| **Actor** (Schauspieler) | Actor\* | Benutzer |  | Die meisten DNS-Ereignisquellen liefern keine Benutzerinformationen, die normalerweise kein Bestandteil des DNS-Protokolls sind. <br><br>In einigen Fällen stellt das meldende Gerät die Benutzerinformationen zur Verfügung, in der Regel durch Auflösen der Quell-IP-Adresse in Benutzerinformationen. Stellen Sie in solchen Fällen den Benutzer entsprechend der Beschreibung in der Dokumentation der Entitäten des Schemas dar. Verwenden Sie **Actor** als Deskriptor, da die Informationen auf der Quell-IP-Adresse basieren. <br><br>**Hinweis**: Wenn **Actor** als Entitätsdeskriptor verwendet wird, muss das Feld **User** nicht angefügt werden. |
| **Quellgerät** | Src\* | Gerät | `SrcIpAddr` | DNS-Ereignisquellen melden in der Regel die IP-Adresse der Anforderungsquelle, weshalb **SrcIpAddr** obligatorisch ist. <br><br>Wenn das meldende Gerät weitere Informationen zur Anforderungsquelle liefert oder Sie die Daten anreichern, befolgen Sie die Entitätsrichtlinien des Geräts zur Normalisierung mit **Src** als Feldpräfix. |
| **Zielgerät** | Dst\* | Gerät |  | DNS-Ereignisquellen melden in der Regel keine Informationen zum Ziel der Anforderung. Wenn das meldende Gerät Informationen zum Anforderungsziel liefert oder Sie die Daten anreichern, befolgen Sie die Entitätsrichtlinien des Geräts zur Normalisierung mit **Dst** als Präfix. |
| **Meldendes Gerät** | Dvc\* | Gerät | `Dvc` | Die meisten Ereignisse enthalten Informationen zum meldenden Gerät. Verwenden Sie für diese Felder das Präfix „Dvc“.|
| **Prozessinformationen** | Prozess\* | Prozess |  | Informationen zum Prozess, der die DNS-Abfrage auf dem Clientgerät generiert hat. |
| | | | | |

## <a name="handling-dns-response"></a>Behandeln der DNS-Antwort

In den meisten Fällen enthalten die protokollierten DNS-Ereignisse keine Antwortinformationen, die umfangreich und detailliert sein können. Wenn Ihr Datensatz weitere Antwortinformationen enthält, speichern Sie diese im Feld [ResponseName](#responsename), so wie sie im Datensatz angezeigt werden.

Sie können auch eine zusätzliche KQL-Funktion mit dem Namen `_imDNS<vendor>Response_` bereitstellen, die die nicht analysierte Antwort als Eingabe verwendet und einen dynamischen Wert mit der folgenden Struktur zurückgibt:

```kql
[
    {
        "part": "answer"
        "query": "yahoo.com."
        "TTL": 1782
        "Class": "IN"
        "Type": "A"
        "Response": "74.6.231.21"
    }
    {
        "part": "authority"
        "query": "yahoo.com."
        "TTL": 113066
        "Class": "IN"
        "Type": "NS"
        "Response": "ns5.yahoo.com"
    }
    ...
]
```

Die Felder in den einzelnen Wörterbüchern im dynamischen Wert entsprechen den Feldern in jeder DNS-Antwort. Der Eintrag `part` sollte entweder `answer`, `authority` oder `additional` enthalten, um den Teil der Antwort wiederzugeben, zu dem das Wörterbuch gehört.

> [!TIP]
> Um eine optimale Leistung zu gewährleisten, rufen Sie die Funktion `imDNS<vendor>Response` nur bei Bedarf und nur nach anfänglicher Filterung auf, um eine bessere Leistung zu gewährleisten.
>

## <a name="handling-dns-flags"></a>Behandeln von DNS-Flags

Analyse und Normalisierung sind für Flagdaten nicht erforderlich. Speichern Sie stattdessen die vom meldenden Gerät bereitgestellten Flagdaten im Feld [Flags](#flags). Wenn die Bestimmung des Wertes einzelner Flags unkompliziert ist, können Sie auch die dedizierten Flag-Felder verwenden. 

Sie können auch eine zusätzliche KQL-Funktion namens `_imDNS<vendor>Flags_` bereitstellen, die die nicht analysierte Antwort oder dedizierten Flag-Felder als Eingabe annimmt und eine dynamische Liste mit booleschen Werten zurückgibt, die jedes Flag in der folgenden Reihenfolge repräsentieren:

- Authentifiziert (AD)
- Autoritativ (AA)
- Überprüfung deaktiviert (CD)
- Rekursion verfügbar (RA)
- Rekursion gewünscht (RD)
- Abgeschnitten (TC)
- Z

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Normalisierung in Azure Sentinel](normalization.md)
- [Azure Sentinel: Referenz zum Schema zur Normalisierung der Authentifizierung (Public Preview)](authentication-normalization-schema.md)
- [Azure Sentinel: Schemareferenz zur Datennormalisierung](normalization-schema.md)
- [Azure Sentinel: Referenz zum Schema zur Normalisierung von Dateiereignissen (Public Preview)](file-event-normalization-schema.md)
- [Azure Sentinel: Referenz zum Schema zur Normalisierung von Prozessereignissen](process-events-normalization-schema.md)
- [Azure Sentinel: Referenz zum Registrierungsereignis-Normalisierungsschema (Öffentliche Vorschau)](registry-event-normalization-schema.md)
