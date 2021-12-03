---
title: Referenz zum DNS-Normalisierungsschema von Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird das DNS-Normalisierungsschema von Microsoft Sentinel beschrieben.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3289f52d0f6925374aee5656afc797ed0713f962
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521470"
---
# <a name="microsoft-sentinel-dns-normalization-schema-reference-public-preview"></a>Referenz zum DNS-Normalisierungsschema von Microsoft Sentinel (Public Preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das DNS-Informationsmodell dient zur Beschreibung von Ereignissen, die von einem DNS-Server oder einem DNS-Sicherheitssystem gemeldet werden. Es wird von Microsoft Sentinel verwendet, um quellenagnostische Analysen zu ermöglichen.

Weitere Informationen finden Sie unter [Normalisierung und das erweiterte SIEM-Informationsmodell (ASIM)](normalization.md).

> [!IMPORTANT]
> Das DNS-Normalisierungsschema befindet sich derzeit in der VORSCHAUPHASE. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
>
> In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="schema-overview"></a>Schemaübersicht

Das ASIM-DNS-Schema stellt die DNS-Protokollaktivität dar, die entweder von einem DNS-Server oder von einem Gerät, das DNS-Anforderungen an einen DNS-Server sendet, protokolliert werden kann. Die DNS-Protokollaktivität umfasst DNS-Abfragen, DNS-Serverupdates und DNS-Massendatenübertragungen. Da das Schema die Protokollaktivität darstellt, wird es von RFCs und offiziell zugewiesenen Parameterlisten bestimmt, auf die in diesem Artikel gegebenenfalls verwiesen wird. Das DNS-Schema stellt keine DNS-Serverüberwachungsereignisse dar. 

Die wichtigste Aktivität, die von DNS-Servern gemeldet wird, ist eine DNS-Abfrage, bei der das Feld `EventType` auf `Query` festgelegt ist.   

Die wichtigsten Felder eines DNS-Ereignisses sind:

- [DnsQuery](#query), das den Domänennamen angibt, für den die Abfrage ausgegeben wurde.
- [SrcIpAddr](#srcipaddr) (mit dem Alias [IpAddr](#ipaddr)), das die IP-Adresse darstellt, von der die Anforderung generiert wurde. 
- [EventResultDetails](#eventresultdetails), das angibt, ob die Anforderung erfolgreich war, und falls nicht, warum.
- Falls verfügbar, [DnsResponseName](#responsename), das die Antwort enthält, die vom Server für die Abfrage bereitgestellt wurde. ASIM erfordert keine Analyse der Antwort und das Format variiert je nach Quelle. Um dieses Feld in quellenunabhängigen Inhalten zu verwenden, müssen Sie den Inhalt mithilfe der Operatoren `has` oder `contains` durchsuchen.

DNS-Ereignisse, die auf dem Clientgerät gesammelt werden, können auch [Benutzer-](#user) und [Prozessinformationen](#process) enthalten. 

## <a name="guidelines-for-collecting-dns-events"></a>Leitlinien zum Sammeln von DNS-Ereignissen

DNS ist insofern ein besonderes Protokoll, als es sich über eine große Anzahl von Computern erstrecken kann. Da DNS außerdem UDP verwendet, sind Anforderungen und Antworten voneinander entkoppelt und stehen nicht in direktem Zusammenhang.

Die folgende Abbildung zeigt einen vereinfachten DNS-Anforderungsflow, einschließlich vier Segmenten. Eine reale Anforderung kann komplexer sein, sobald mehr Segmente beteiligt sind.

:::image type="content" source="media/normalization/dns-request-flow.png" alt-text="Vereinfachter DNS-Anforderungsflow.":::

Da Anforderungs- und Antwortsegmente im DNS-Anforderungsflow nicht direkt miteinander verbunden sind, kann die vollständige Protokollierung zu erheblicher Duplizierung führen.

Das nützlichste zu protokollierende Segment ist die Antwort an den Client, die die Abfragen der Domänennamen, das Lookupergebnis und die IP-Adresse des Clients enthält. Zwar protokollieren viele DNS-Systeme nur dieses Segment, aber es ist sinnvoll, auch die anderen Teile zu protokollieren. Ein DNS-Cache-Poisoning-Angriff nutzt beispielsweise häufig gefälschte Antworten von einem vorgelagerten Server aus.

Wenn Ihre Datenquelle die vollständige DNS-Protokollierung unterstützt und Sie sich entschieden haben, mehrere Segmente zu protokollieren, müssen Sie Ihre Abfragen anpassen, um in Microsoft Sentinel Datenduplizierung zu vermeiden.

Beispielsweise können Sie Ihre Abfrage mit der folgenden Normalisierung ändern:

```kql
imDNS | where SrcIpAddr != "127.0.0.1" and EventSubType == "response"
```

## <a name="parsers"></a>Parser

### <a name="source-agnostic-parsers"></a>Quellenunabhängige Parser

Um die quellenunabhängigen Parser zu verwenden, die alle vorkonfigurierten Parser vereinheitlichen, und um sicherzustellen, dass Ihre Analyse über alle konfigurierten Quellen hinweg läuft, verwenden Sie die folgenden KQL-Funktionen als Tabellennamen in Ihrer Abfrage:

| Name | BESCHREIBUNG | Verwendungsanweisungen |
| --- | --- | --- |
| **imDNS** | Aggregativer Parser, der *union* verwendet, um normalisierte Ereignisse aus allen DNS-Quellen einzubeziehen. |Aktualisieren Sie diesen Parser, wenn Sie Quellen zu quellenagnostischen Analysen hinzufügen oder daraus entfernen möchten. <br><br>Verwenden Sie diese Funktion in Ihren quellenagnostischen Abfragen.|
| **ASimDNS** | Ähnlich wie die `imDns`Funktion, aber ohne Parameterunterstützung, und zwingt daher die Seitenauswahl **Logs** nicht, den Wert `custom` zu verwenden. |Aktualisieren Sie diesen Parser, wenn Sie Quellen zu quellenagnostischen Analysen hinzufügen oder daraus entfernen möchten.<br><br>– Verwenden Sie diese Funktion in Ihren quellenagnostischen Abfragen, wenn Sie nicht vorhaben, Parameter zu verwenden.|
| **vimDNS\<vendor\>\<product\>** | Quellspezifische Parser implementieren die Normalisierung für eine bestimmte Quelle, z. B. *imDNSWindowsOMS*. |Fügen Sie einen quellspezifischen Parser für eine Quelle hinzu, wenn kein vorkonfigurierter Normalisierungsparser vorhanden ist. Aktualisieren Sie den `im`aggregativen Q-Parser, um einen Verweis auf Ihren neuen Parser aufzunehmen. <br><br>Ändern Sie einen quellspezifischen Parser so, dass Analyse- und Normalisierungsprobleme behoben werden.<br><br>Verwenden Sie einen quellspezifischen Parser für quellspezifische Analysen.|
| **ASimDNS\<vendor\>\<product\>** | Quellenspezifische Parser implementieren die Normalisierung für eine bestimmte Quelle. Im Gegensatz zu den `vim*`-Funktionen unterstützen die `ASimDNS*`-Funktionen keine Parameter. |Fügen Sie einen quellspezifischen Parser für eine Quelle hinzu, wenn kein vorkonfigurierter Normalisierungsparser vorhanden ist. Ändern Sie den aggregativen Parser `ASim` so, dass er einen Verweis auf Ihren neuen Parser enthält.<br><br>Ändern Sie einen quellspezifischen Parser so, dass Analyse- und Normalisierungsprobleme behoben werden.<br><br>– Hinzufügen eines `ASim` quellenspezifischen Parsers für eine Quelle, wenn es keinen eingebauten normalisierenden Parser gibt.|
| | | |

Die Parser können über das [GitHub-Repository von Microsoft Sentinel](https://aka.ms/azsentinelDNS) bereitgestellt werden.

### <a name="out-of-the-box-source-specific-parsers"></a>Vorkonfigurierte, quellspezifische Parser

Microsoft Sentinel bietet die folgenden vorkonfigurierten, produktspezifischen DNS-Parser:

| **Name** | **Beschreibung** |
| --- | --- |
|**Microsoft DNS Server**  |   **Gesammelt mithilfe des DNS-Connectors und des Log Analytics-Agents**: <br> - `ASimDnsMicrosoftOMS` (regulär) <br>- `vimDnsMicrosoftOMS` (parametrisiert) <br><br>  **Gesammelt mithilfe von NXlog**: <br> - `ASimDnsMicrosoftNXlog` (regulär)<br>- `vimDnsMicrosoftNXlog` (parametrisiert) |
| **Azure Firewall** |- `ASimDnsAzureFirewall` (regulär)<br>- `vimDnsAzureFirewall` (parametrisiert) |
|**Sysmon für Windows** (Event 22) | **Gesammelt mithilfe des Log Analytics-Agents oder des Azure Monitor-Agents**; unterstützt sowohl die Tabelle `Event` als auch `WindowsEvent`: <br>- `ASimDnsMicrosoftSysmon` (regulär)<br>- `vimDnsMicrosoftSysmon` (parametrisiert)  |
|**Cisco Umbrella**  | - `ASimDnsCiscoUmbrella` (regulär)<br>- `vimDnsCiscoUmbrella` (parametrisiert)  |
|**Infoblox NIOS**  |- `ASimDnsInfobloxNIOS` (regulär)<br>- `vimDnsInfobloxNIOS` (parametrisiert) |
| **GCP DNS** |- `ASimDnsGcp` (regulär)<br>- `vimDnsGcp`  (parametrisiert) |
| **Corelight Zeek DNS-Ereignisse** | - `ASimDnsCorelightZeek` (regulär)<br>- `vimDnsCorelightZeek`  (parametrisiert) |
| **zScaler ZIA** |- `AsimDnszScalerZIA` (regulär)<br>- `vimDnszScalerZIA` (parametrisiert)  |
| | |

Die Parser können über das [GitHub-Repository von Microsoft Sentinel](https://aka.ms/azsentinelDNS) bereitgestellt werden.

### <a name="add-your-own-normalized-parsers"></a>Hinzufügen eigener normalisierter Parser

Wenn Sie benutzerdefinierte Parser für das DNS-Informationsmodell implementieren, benennen Sie Ihre KQL-Funktionen mit der folgenden Syntax:

- `vimDns<vendor><Product` für parametrisierte Parser
- `ASimDns<vendor><Product` für reguläre Parser

### <a name="filtering-parser-parameters"></a>Filtern von Parser-Parametern

Die Parser `im` und `vim*` unterstützen [Filterparameter](normalization-about-parsers.md#optimized-parsers). Indem diese Parser optional sind, können sie die Leistung Ihrer Abfrage verbessern.

Die folgenden Filterparameter sind verfügbar:

| Name     | type      | Beschreibung |
|----------|-----------|-------------|
| **StartTime** | datetime | Filtert nur DNS-Abfragen, die zu oder nach dieser Zeit ausgeführt wurden. |
| **EndTime** | datetime | Filtert nur DNS-Abfragen, deren Ausführung zu oder vor diesem Zeitpunkt beendet wurde. |
| **srcipaddr** | Zeichenfolge | Filtert nur DNS-Abfragen von dieser Quell-IP-Adresse. |
| **domain_has_any**| dynamisch | Filtert nur DNS-Abfragen, bei denen das `domain` (oder `query`) irgendeinen der aufgeführten Domänennamen enthält, auch als Teil der Ereignisdomäne.
| **responsecodename** | Zeichenfolge | Filtern nur DNS-Abfragen, bei denen der Name des Antwortcodes mit dem angegebenen Wert übereinstimmt. <br>Beispiel: `NXDOMAIN` |
| **response_has_ipv4** | Zeichenfolge | Filtert nur DNS-Abfragen, bei denen das Antwortfeld mit der angegebenen IP-Adresse oder dem IP-Adresspräfix anfängt. Verwenden Sie diesen Parameter, wenn Sie nach einer einzelnen IP-Adresse oder einem Präfix filtern möchten. <br><br>Für Quellen, die keine Antwort liefern, werden keine Ergebnisse zurückgegeben.|
| **Antwort_hat_beliebigen_präfix** | dynamisch| Filtert nur DNS-Abfragen, bei denen das Antwortfeld mit einer der aufgeführten IP-Adressen oder IP-Adress-Präfixe beginnt. <br><br>Verwenden Sie diesen Parameter, wenn Sie nach einer Liste von IP-Adressen oder Präfixen filtern möchten. <br><br>Für Quellen, die keine Antwort liefern, werden keine Ergebnisse zurückgegeben. |
| **EventType**| Zeichenfolge | Filtert nur DNS-Abfragen des angegebenen Typs. Wenn kein Wert angegeben wird, werden nur Lookup-Abfragen zurückgegeben. |
| | | |

Verwenden Sie zum Beispiel Folgendes, um nur DNS-Abfragen vom letzten Tag zu filtern, die den Domänennamen nicht auflösen konnten:

```kql
imDns (responsecodename = 'NXDOMAIN', starttime = ago(1d), endtime=now())
```

Verwenden Sie Folgendes, um nur DNS-Abfragen nach einer angegebenen Liste von Domänennamen zu filtern:

```kql
let torProxies=dynamic(["tor2web.org", "tor2web.com", "torlink.co",...]);
imDns (domain_has_any = torProxies)
```

## <a name="normalized-content"></a>Normalisierter Inhalt

Eine vollständige Liste der Analyseregeln, die normalisierte DNS-Ereignisse verwenden, finden Sie im Abschnitt [Sicherheitsinhalt von DNS-Abfragen](normalization-content.md#dns-query-security-content).

## <a name="schema-details"></a>Schemadetails

Das DNS-Informationsmodell orientiert sich am [OSSEM-DNS-Entitätsschema](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/dns.md).

Weitere Informationen finden Sie in der [Referenz zu den Domain Name System-Parametern der Internet Assigned Numbers Authority (IANA) ](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).

### <a name="common-fields"></a>Allgemeine Felder

Eine Beschreibung der Felder, die bei allen Schemas verwendet werden, finden Sie in der [ASIM-Schemaübersicht](normalization-about-schemas.md#common). Die folgenden Felder enthalten spezifische Richtlinien für DNS-Ereignisse:

| **Feld** | **Klasse** | **Typ**  | **Beschreibung** |
| --- | --- | --- | --- |
| **EventType** | Obligatorisch. | Enumerated | Gibt den vom Datensatz gemeldeten Vorgang an. <br><br> Bei DNS-Einträgen ist dieser Wert der [Code des DNS-Vorgangs](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Beispiel: `lookup`|
| **EventSubType** | Optional | Enumerated | Entweder **Anforderung** oder **Antwort**. <br><br>Bei den meisten Quellen werden [nur die Antworten protokolliert](#guidelines-for-collecting-dns-events), daher ist der Wert oft **Antwort**.  |
| <a name=eventresultdetails></a>**EventResultDetails** | Alias | | Grund oder Details für das im Feld **_EventResult_** gemeldete Ergebnis. <br><br> Versieht das Feld [ResponseCodeName](#responsecodename) mit einem Alias.|
| **EventSchemaVersion** | Obligatorisch. | String | Die Version des hier dokumentierten Schemas lautet **0.1.3**. |
| **EventSchema** | Obligatorisch. | String | Der Name des hier dokumentierten Schemas ist **Dns**. |
| **Dvc**-Felder| -      | -    | Gerätefelder für DNS-Ereignisse verweisen auf das System, das das DNS-Ereignis meldet. |
| | | | |


### <a name="dns-specific-fields"></a>DNS-spezifische Felder

Die nachfolgend aufgeführten Felder sind für DNS-Ereignisse spezifisch, obwohl viele den Feldern in anderen Schemas ähneln und demnach derselben Benennungskonvention folgen.

| **Feld** | **Klasse** | **Typ** | **Hinweise** |
| --- | --- | --- | --- |
| <a name="src"></a>**Src** | Empfohlen       | String     |    Der eindeutige Bezeichner des Quellgeräts. <br><br>Dieses Feld kann ein Alias für die Felder [SrcDvcId](#srcdvcid), [SrcHostname](#srchostname) oder [SrcIpAddr](#srcipaddr) sein. <br><br>Beispiel: `192.168.12.1`       |
| <a name="srcipaddr"></a>**SrcIpAddr** | Empfohlen | IP-Adresse | Die IP-Adresse des Clients, der die DNS-Anforderung sendet. Bei einer rekursiven DNS-Anforderung ist dieser Wert in der Regel das meldende Gerät und in den meisten Fällen auf `127.0.0.1` festgelegt. <br><br>Beispiel: `192.168.12.1` |
| **SrcPortNumber** | Optional | Integer | Quellport der DNS-Abfrage.<br><br>Beispiel: `54312` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias für [SrcIpAddr](#srcipaddr) |
| **SrcGeoCountry** | Optional | Land | Das Land/die Region, das bzw. die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `USA` |
| **SrcGeoRegion** | Optional | Region | Die Region innerhalb eines Landes, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `Vermont` |
| **SrcGeoCity** | Optional | City | Die Stadt, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `Burlington` |
| **SrcGeoLatitude** | Optional | Breitengrad | Der Breitengrad der geografischen Koordinate, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `44.475833` |
| **SrcGeoLongitude** | Optional | Längengrad | Der Längengrad der geografischen Koordinate, die der Quell-IP-Adresse zugeordnet ist.<br><br>Beispiel: `73.211944` |
| **SrcRiskLevel** | Optional | Integer | Die der Quelle zugeordnete Risikostufe. Der Wert sollte auf einen Bereich zwischen `0` und `100` angepasst werden, wobei `0` unbedenklich ist und `100` ein hohes Risiko darstellt.<br><br>Beispiel: `90` |
| <a name="srchostname"></a>**SrcHostname** | Empfohlen | String | Der Hostname des Quellgeräts ohne Domäneninformationen. Wenn kein Gerätename verfügbar ist, speichern Sie die entsprechende IP-Adresse in diesem Feld. Dieser Wert ist obligatorisch, wenn [SrcIpAddr](#srcipaddr) angegeben wird.<br><br>Beispiel: `DESKTOP-1282V4D` |
| **Hostname** | Alias | | Alias für [SrcHostname](#srchostname) |
|<a name="srcdomain"></a> **SrcDomain** | Empfohlen | String | Die Domäne des Quellgeräts.<br><br>Beispiel: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Empfohlen | Enumerated | Der Typ von [SrcDomain](#srcdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows` (Beispiel: `contoso`)<br>- `FQDN` (Beispiel: `microsoft.com`)<br><br>Erforderlich, wenn [SrcDomain](#srcdomain) verwendet wird. |
| **SrcFQDN** | Optional | String | Der Hostname des Quellgeräts ggf. mit Informationen zur Domäne. <br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. Das Feld [SrcDomainType](#srcdomaintype) spiegelt das verwendete Format wider. <br><br>Beispiel: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String | Die ID des Quellgeräts, wie im Datensatz angegeben.<br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | Enumerated | Der Typ von [SrcDvcId](#srcdvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der obigen Liste, und speichern Sie die anderen in den Feldern **SrcDvcAzureResourceId** bzw. **SrcDvcMDEid**.<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [SrcDvcId](#srcdvcid) verwendet wird. |
| **SrcDeviceType** | Optional | Enumerated | Der Typ des Quellgeräts. Mögliche Werte sind:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Optional | String | Eine maschinenlesbare, alphanumerische, eindeutige Darstellung des Quellbenutzers. Format und unterstützte Typen:<br>-  **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>-  **UID** (Linux): `4578`<br>-  **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>-  **OktaId**: `00urjk4znu3BcncfY0h7`<br>-  **AWSId**: `72643944673`<br><br>Speichern Sie den ID-Typ im Feld [SrcUserIdType](#srcuseridtype). <br><br>Wenn andere IDs verfügbar sind, wird empfohlen, die Feldnamen in **SrcUserSid**, **SrcUserUid**, **SrcUserAadId**, **SrcUserOktaId** bzw. **UserAwsId** zu normalisieren. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: S-1-12 |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Optional | Enumerated | Der Typ der ID, die im Feld [SrcUserId](#srcuserid) gespeichert ist. Unterstützte Werte: `SID`, `UIS`, `AADID`, `OktaId` und `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Optional | String | Der Quellbenutzername einschließlich Informationen zur Domäne, falls verfügbar. Verwenden Sie eines der folgenden Formate in der folgenden Reihenfolge nach Priorität:<br>- **UPN/E-Mail**: `johndow@contoso.com`<br>- **Windows:** `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Einfach**: `johndow`. Verwenden Sie dieses einfache Format nur, wenn keine Domäneninformationen verfügbar sind.<br><br>Speichern Sie den Benutzernamenstyp im Feld [SrcUsernameType](#srcusernametype). Wenn andere IDs verfügbar sind, empfiehlt es sich, die Feldnamen in **SrcUserUpn**, **SrcUserWindows** und **SrcUserDn** zu normalisieren.<br><br>Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `AlbertE` |
| <a name="user"></a>**User** | Alias | | Alias für [SrcUsername](#srcusername) |
| <a name="srcusernametype"></a>**SrcUsernameType** | Optional | Enumerated | Gibt den Typ des Benutzernamens an, der im Feld [SrcUsername](#srcusername) gespeichert ist. Unterstützte Werte: `UPN`, `Windows`, `DN` und `Simple`. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `Windows` |
| **SrcUserType** | Optional | Enumerated | Der Typ des Akteurs. Zulässige Werte sind:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Speichern Sie den ursprünglichen Wert im Feld [EventOriginalUserType](#eventoriginalusertype). |
| <a name="eventoriginalusertype"></a>**SrcOriginalUserType** | Optional | String | Der ursprüngliche Quellbenutzertyp, sofern von der Quelle bereitgestellt. |
| **SrcUserDomain** | Optional | String | Dieses Feld wird lediglich aus Gründen der Rückwärtskompatibilität beibehalten. ASIM erfordert, dass Domäneninformationen, sofern verfügbar, im Feld [SrcUsername](#srcusername) enthalten sind. |
| <a name="srcprocessname"></a>**SrcProcessName**              | Optional     | String     |   Der Dateiname des Prozesses, der die DNS-Anforderung initiiert. Dieser Name wird in der Regel als Prozessname betrachtet.  <br><br>Beispiel: `C:\Windows\explorer.exe`  |
| <a name="process"></a>**Process**        | Alias        |            | Alias für [SrcProcessName](#srcprocessname) <br><br>Beispiel: `C:\Windows\System32\rundll32.exe`|
| **SrcProcessId**| Obligatorisch.    | String        | Die Prozess-ID (PID) des Prozesses, der die DNS-Anforderung initiiert.<br><br>Beispiel: `48610176`           <br><br>**Hinweis**: Der Typ ist als *Zeichenfolge* definiert, um unterschiedliche Systeme zu unterstützen, aber unter Windows und Linux muss dieser Wert numerisch sein. <br><br>Wenn Sie einen Windows- oder Linux-Computer verwenden und einen anderen Typ verwendet haben, stellen Sie sicher, dass Sie die Werte konvertieren. Wenn Sie beispielsweise einen Hexadezimalwert verwendet haben, konvertieren Sie diesen in einen Dezimalwert.    |
| **SrcProcessGuid**              | Optional     | String     |  Ein generierter eindeutiger Bezeichner (GUID) für den Prozess, der die DNS-Anforderung initiiert.   <br><br> Beispiel: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| <a name="dst"></a>**Dst** | Empfohlen       | String     |    Ein eindeutiger Bezeichner des Servers, der die DNS-Anforderung empfängt. <br><br>Dieses Feld kann ein Alias für die Felder [DstDvcId](#dstdvcid), [DstHostname](#dsthostname) oder [DstIpAddr](#dstipaddr) sein. <br><br>Beispiel: `192.168.12.1`       |
| <a name="dstipaddr"></a>**DstIpAddr** | Optional | IP-Adresse | Die IP-Adresse des Servers, der die DNS-Anforderung empfängt. Bei einer herkömmlichen DNS-Anforderung ist dieser Wert in der Regel das meldende Gerät und in den meisten Fällen auf `127.0.0.1` festgelegt.<br><br>Beispiel: `127.0.0.1` |
| **DstGeoCountry** | Optional | Land | Das Land/die Region, das bzw. die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `USA` |
| **DstGeoRegion** | Optional | Region | Die der Ziel-IP-Adresse zugeordnete Region bzw. das zugeordnete Bundesland/der zugeordnete Kanton innerhalb eines Landes. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `Vermont` |
| **DstGeoCity** | Optional | City | Die Stadt, die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `Burlington` |
| **DstGeoLatitude** | Optional | Breitengrad | Der Breitengrad der geografischen Koordinate, die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `44.475833` |
| **DstGeoLongitude** | Optional | Längengrad | Der Längengrad der geografischen Koordinate, die der Ziel-IP-Adresse zugeordnet ist. Weitere Informationen finden Sie unter [Logische Typen](normalization-about-schemas.md#logical-types).<br><br>Beispiel: `73.211944` |
| **DstcRiskLevel** | Optional | Integer | Die dem Ziel zugeordnete Risikostufe. Der Wert sollte auf einen Bereich zwischen 0 und 100 angepasst werden, wobei 0 unbedenklich ist und 100 ein hohes Risiko darstellt.<br><br>Beispiel: `90` |
| **DstPortNumber** | Optional | Integer  | Zielportnummer.<br><br>Beispiel: `53` |
| <a name="dsthostname"></a>**DstHostname** | Optional | String | Der Hostname des Zielgeräts ohne Domäneninformationen. Wenn kein Gerätename verfügbar ist, speichern Sie die entsprechende IP-Adresse in diesem Feld.<br><br>Beispiel: `DESKTOP-1282V4D`<br><br>**Hinweis**: Dieser Wert ist obligatorisch, wenn [DstIpAddr](#dstipaddr) angegeben wird. |
| <a name="dstdomain"></a>**DstDomain** | Optional | String | Die Domäne des Zielgeräts.<br><br>Beispiel: `Contoso` |
| <a name="dstdomaintype"></a>**DstDomainType** | Optional | Enumerated | Der Typ von [DstDomain](#dstdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>Erforderlich, wenn [DstDomain](#dstdomain) verwendet wird. |
| **DstFQDN** | Optional | String | Der Hostname des Zielgeräts einschließlich Domäneninformationen, sofern verfügbar. <br><br>Beispiel: `Contoso\DESKTOP-1282V4D` <br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. [DstDomainType](#dstdomaintype) spiegelt das verwendete Format wider.   |
| <a name="dstdvcid"></a>**DstDvcId** | Optional | String | Die ID des Zielgeräts, wie im Datensatz angegeben.<br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DstDvcIdType** | Optional | Enumerated | Der Typ von [DstDvcId](#dstdvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEidIf`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der obigen Liste, und speichern Sie die anderen in den Feldern **DstDvcAzureResourceId** bzw. **DstDvcMDEid**.<br><br>Erforderlich, wenn **DstDeviceId** verwendet wird.|
| **DstDeviceType** | Optional | Enumerated | Der Typ des Zielgeräts. Mögliche Werte sind:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name=query></a>**DnsQuery** | Obligatorisch. | FQDN | Die aufzulösende Domäne. <br><br>**Hinweis**: Einige Quellen senden diese Abfrage in unterschiedlichen Formaten. Beispielsweise im DNS-Protokoll selbst enthält die Abfrage einen Punkt ( **.** ) am Ende, der entfernt werden muss.<br><br>Das DNS-Protokoll lässt zwar mehrere Abfragen in einer einzigen Anforderung zu, aber dieses Szenario ist, wenn überhaupt, nur selten anzutreffen. Wenn die Anforderung mehrere Abfragen enthält, speichern Sie die erste in diesem Feld und die übrigen optional im Feld [AdditionalFields](normalization-about-schemas.md#additionalfields).<br><br>Beispiel: `www.malicious.com` |
| **Domain** | Alias | | Alias für [Abfrage](#query). |
| **DnsQueryType** | Optional | Integer | Dieses Feld kann [Codes von DNS-Ressourcendatensatztypen](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml) enthalten. <br><br>Beispiel: `28`|
| **DnsQueryTypeName** | Empfohlen | Enumerated | Das Feld kann [Namen von DNS-Ressourcendatensatztypen](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml) enthalten. <br><br>**Hinweis**: IANA legt keine Groß-/Kleinschreibung für die Werte fest, sodass Groß-/Kleinschreibung für Analysen nach Bedarf normalisiert werden muss. Wenn die Quelle nur einen numerischen Code für den Abfragetyp und keinen Namen dafür angibt, muss der Parser zum Anreichern mit diesem Wert eine Nachschlagetabelle enthalten.<br><br>Beispiel: `AAAA`|
| <a name=responsename></a>**DnsResponseName** | Optional | String | Der Inhalt der Antwort, wie im Datensatz enthalten.<br> <br> Die DNS-Antwortdaten sind auf den meldenden Geräten inkonsistent, schwierig zu analysieren und von weniger Nutzen für quellagnostische Analysen. Daher muss das Informationsmodell nicht analysiert und normalisiert werden. Microsoft Sentinel stellt Antwortinformationen mit einer Hilfsfunktion bereit. Weitere Informationen finden Sie unter [Behandeln der DNS-Antwort](#handling-dns-response).|
| <a name=responsecodename></a>**DnsResponseCodeName** |  Obligatorisch. | Enumerated | Der [DNS-Antwortcode](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>**Hinweis**: IANA legt keine Groß-/Kleinschreibung für die Werte fest, sodass Groß-/Kleinschreibung für Analysen normalisiert werden muss. Wenn die Quelle nur einen numerischen Antwortcode und keinen Namen dafür angibt, muss der Parser zum Anreichern mit diesem Wert eine Nachschlagetabelle enthalten. <br><br> Wenn dieser Datensatz eine Anforderung und keine Antwort darstellt, legen Sie ihn auf **NA** fest. <br><br>Beispiel: `NXDOMAIN` |
| **DnsResponseCode** | Optional | Integer | Der [numerische DNS-Antwortcode](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml). <br><br>Beispiel: `3`|
| **TransactionIdHex** | Empfohlen | String | Die eindeutige hexadezimale DNS-Transaktions-ID. |
| **NetworkProtocol** | Optional | Enumerated | Das vom Netzwerkauflösungsereignis verwendete Transportprotokoll. Der Wert kann **UDP** oder **TCP** sein und wird für DNS in der Regel auf **UDP** festgelegt. <br><br>Beispiel: `UDP`|
| **DnsQueryClass** | Optional | Integer | Die [DNS-Klassen-ID](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>In der Praxis wird nur die **IN**-Klasse (ID 1) verwendet, wodurch dieses Feld weniger nützlich ist.|
| **DnsQueryClassName** | Optional | String | Der [DNS-Klassenname](https://www.iana.org/assignments/dns-parameters/dns-parameters.xhtml).<br> <br>In der Praxis wird nur die **IN**-Klasse (ID 1) verwendet, wodurch dieses Feld weniger nützlich ist. <br><br>Beispiel: `IN`|
| <a name=flags></a>**DnsFlags** | Optional | Liste von Zeichenfolgen | Das Feld mit Flags, wie vom meldenden Gerät bereitgestellt. Wenn Flaginformationen in mehreren Feldern bereitgestellt werden, verketten Sie sie mit Komma als Trennzeichen. <br><br>Da DNS-Flags schwierig zu analysieren sind und seltener in Analysen verwendet werden, sind Analyse und Normalisierung nicht erforderlich. Microsoft Sentinel stellt Informationen zu Flags mit einer Hilfsfunktion bereit. Weitere Informationen finden Sie unter [Behandeln der DNS-Antwort](#handling-dns-response). <br><br>Beispiel: `["DR"]`|
| <a name=UrlCategory></a>**UrlCategory** |  Optional | String | Eine DNS-Ereignisquelle kann auch die Kategorie der angeforderten Domänen nachschlagen. Das Feld heißt **_UrlCategory_**, um es am Microsoft Sentinel-Netzwerkschema zu orientieren. <br><br>**_DomainCategory_** wird als ein zu DNS passender Alias hinzugefügt. <br><br>Beispiel: `Educational \\ Phishing` |
| **DomainCategory** | Optional | Alias | Alias für [UrlCategory](#UrlCategory). |
| **ThreatCategory** | Optional | String | Wenn eine DNS-Ereignisquelle auch sicherheitsrelevante DNS-Informationen liefert, kann sie auch das DNS-Ereignis auswerten. So kann sie beispielsweise die IP-Adresse oder Domäne in einer Threat Intelligence-Datenbank suchen und der Domäne oder IP-Adresse eine Bedrohungskategorie zuweisen. |
| **EventSeverity** | Optional | String | Wenn eine DNS-Ereignisquelle auch sicherheitsrelevante DNS-Informationen liefert, kann sie das DNS-Ereignis auswerten. So kann sie beispielsweise in einer Threat-Intelligence-Datenbank nach der IP-Adresse oder Domäne suchen und auf der Grundlage der Auswertung einen Schweregrad zuweisen. <br><br>Beispiel: `Informational`|
| <a name="dnsnetworkduration"></a>**DnsNetworkDuration** | Optional | Integer | Die Zeitspanne in Millisekunden für den Abschluss der DNS-Anforderung.<br><br>Beispiel: `1500` |
| **Duration** | Alias | | Alias für [DnsNetworkDuration](#dnsnetworkduration) |
| **DnsFlagsAuthenticated** | Optional | Boolean | Das DNS Flag `AD`, das mit DNSSEC zusammenhängt, zeigt in einer Antwort an, dass alle in den Antwort- und Autoritätsabschnitten der Antwort enthaltenen Daten vom Server gemäß den Richtlinien dieses Servers überprüft wurden. Siehe [RFC 3655 Abschnitt 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) für weitere Informationen.    |
| **DnsFlagsAuthoritative** | Optional | Boolean | Das DNS Flag `AA` zeigt an, ob die Antwort des Servers autoritativ war    |
| **DnsFlagsCheckingDisabled** | Optional | Boolean | Das DNS Flag `CD`, das mit DNSSEC zusammenhängt, zeigt in einer Abfrage an, dass nicht verifizierte Daten für das System, das die Abfrage sendet, akzeptabel sind. Siehe [RFC 3655 Abschnitt 6.1](https://tools.ietf.org/html/rfc3655#section-6.1) für weitere Informationen.   |
| **DnsFlagsRecursionAvailable** | Optional | Boolean | Das DNS Flag `RA` zeigt in einer Antwort an, dass dieser Server rekursive Abfragen unterstützt.   |
| **DnsFlagsRecursionDesired** | Optional | Boolean | Das DNS Flag `RD` zeigt in einer Anforderung an, dass der Client möchte, dass der Server rekursive Abfragen verwendet.   |
| **DnsFlagsTruncates** | Optional | Boolean | Das DNS Flag `TC` zeigt an, dass eine Antwort abgeschnitten wurde, da sie die maximale Antwortgröße überschritten hat.  |
| **DnsFlags** | Optional | Boolean | Das DNS Flag `Z` ist ein veraltetes DNS-Flag, das möglicherweise von älteren DNS-Systemen gemeldet wird.  |
|<a name="dnssessionid"></a>**DnsSessionId** | Optional | Zeichenfolge | Der DNS-Sitzungsbezeichner, der vom meldenden Gerät gemeldet wird. <br><br>Beispiel: `EB4BFA28-2EAD-4EF7-BC8A-51DF4FDF5B55` |
| **SessionId** | Alias | String | Alias für [DnsSessionId](#dnssessionid) |
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

### <a name="schema-updates"></a>Schemaupdates

Dies sind die Änderungen in Version 0.1.2 des Schemas:
- Hinzufügung des Felds `EventSchema` – derzeit optional, wird aber am 1. Januar 2022 obligatorisch.
- Hinzufügung des dedizierten Flag-Felds, das das kombinierte **[Flags](#flags)** -Feld `DnsFlagsAuthoritative`, `DnsFlagsCheckingDisabled`, `DnsFlagsRecursionAvailable`, `DnsFlagsRecursionDesired`, `DnsFlagsTruncates` und `DnsFlagsZ` ergänzt.

Dies sind die Änderungen in Version 0.1.3 des Schemas:
- Das Schema dokumentiert jetzt explizit die Felder `Src*`, `Dst*`, `Process*` und `User*`.
- Es wurden zusätzliche `Dvc*`-Felder hinzugefügt, die mit der aktuellen Definition allgemeiner Felder übereinstimmen. 
- Hinzufügung von `Src` und `Dst` als Aliase zu einem führenden Bezeichner für das Quell- und das Zielsystem.
- Hinzufügung der optionalen Aliase `DnsNetworkDuration` und `Duration`.
- Hinzufügung der optionalen Felder „Geo Location“ und „Risk Level“.

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

- [Normalisierung in Microsoft Sentinel](normalization.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung der Authentifizierung (Public Preview)](authentication-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Datennormalisierung](normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Dateiereignissen (Public Preview)](file-event-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Prozessereignissen](process-events-normalization-schema.md)
- [Microsoft Sentinel: Referenz zum Schema zur Normalisierung von Registrierungsereignissen (Public Preview)](registry-event-normalization-schema.md)