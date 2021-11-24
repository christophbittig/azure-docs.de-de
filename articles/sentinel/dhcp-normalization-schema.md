---
title: Referenz zum DHCP-Normalisierungsschema von Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird das DHCP-Normalisierungsschema von Microsoft Sentinel beschrieben.
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
ms.openlocfilehash: f0287e6f116e8d63c0b20aa970974e48a31524c2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521451"
---
# <a name="microsoft-sentinel-dhcp-normalization-schema-reference-public-preview"></a>Referenz zum DHCP-Normalisierungsschema von Microsoft Sentinel (Public Preview)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Das DHCP-Informationsmodell dient zur Beschreibung von Ereignissen, die von einem DHCP-Server gemeldet werden, und wird von Microsoft Sentinel verwendet, um quellenagnostische Analysen zu ermöglichen.

Weitere Informationen finden Sie unter [Normalisierung und das erweiterte SIEM-Informationsmodell (ASIM)](normalization.md).

> [!IMPORTANT]
> Das DHCP-Normalisierungsschema befindet sich derzeit in der VORSCHAUPHASE. Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
>
> In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

## <a name="schema-overview"></a>Schemaübersicht

Das ASIM-DHCP-Schema stellt DHCP-Serveraktivitäten dar, einschließlich der Bereitstellung von Anforderungen für DHCP-IP-Adressen, die von Clientsystemen geleast werden, und der Aktualisierung eines DNS-Servers mit den gewährten Leases.

Die wichtigsten Felder in einem DHCP-Ereignis sind [SrcIpAddr](#srcipaddr) und [SrcHostname](#srchostname), die der DHCP-Server durch Gewähren der Lease bindet und die von den Feldern [IpAddr](#ipaddr) bzw. [Hostname](#hostname) als Alias verwendet werden. Das Feld [SrcMacAddr](#srcmacaddr) ist ebenfalls wichtig, da es den verwendeten Clientcomputer darstellt, wenn eine IP-Adresse nicht geleast wird.  

Ein DHCP-Server kann einen Client entweder aufgrund von Sicherheitsbedenken oder aufgrund einer Überlastung des Netzwerks ablehnen. Er kann einen Client auch unter Quarantäne stellen, indem er ihm eine IP-Adresse least, die ihn mit einem eingeschränkten Netzwerk verbindet. Die Felder [EventResult](#eventresult), [EventResultDetails](#eventresultdetails) und [DvcAction](#dvcaction) enthalten Informationen zur Antwort und Aktion des DHCP-Servers.

Die Dauer einer Lease wird im Feld [DhcpLeaseDuration](#dhcpleaseduration) gespeichert.

## <a name="schema-details"></a>Schemadetails

ASIM ist auf das Projekt [Open Source Security Events Metadata (OSSEM)](https://github.com/OTRF/OSSEM) ausgerichtet.

OSSEM verfügt über kein DHCP-Schema, das mit dem ASIM-DHCP-Schema vergleichbar ist.

### <a name="log-analytics-fields"></a>Log Analytics-Felder

Die folgenden Felder werden von Log Analytics für jeden Datensatz generiert und können beim [Erstellen eines benutzerdefinierten Connectors](create-custom-connector.md) überschrieben werden.

| **Feld** | **Typ** | **Beschreibung** |
| --- | --- | --- |
| <a name=timegenerated></a>**TimeGenerated** | Datum/Uhrzeit | Der Zeitpunkt, zu dem das Ereignis vom meldenden Gerät generiert wurde. |
| **\_ResourceId** | guid | Die Azure-Ressourcen-ID des meldenden Geräts oder Diensts oder die Ressourcen-ID des Protokollforwarders für Ereignisse, die mit Syslog, CEF oder WEF weitergeleitet werden. |
| **Type** | String | Die ursprüngliche Tabelle, aus der der Datensatz abgerufen wurde. Dieses Feld ist nützlich, wenn dasselbe Ereignis über mehrere Kanäle in unterschiedlichen Tabellen empfangen werden kann und die gleichen [EventVendor](#eventvendor)- und [EventProduct](#eventproduct)-Werte vorliegen.<br><br>Ein Sysmon-Ereignis kann z. B. entweder in der Tabelle Event oder in der Tabelle WindowsEvent gesammelt werden. |
| | | |

> [!NOTE]
> Weitere Log Analytics-Felder, die weniger sicherheitsrelevant sind, werden mit [Azure Monitor](../azure-monitor/logs/log-standard-columns.md) dokumentiert.
> 

### <a name="event-fields"></a>Ereignisfelder

Ereignisfelder sind allen Schemas gemeinsam und beschreiben die Aktivität selbst sowie das meldende Gerät.

| **Feld** | **Klasse** | **Typ**  | **Diskussion (Discussion)** |
| --- | --- | --- | --- |
| **EventMessage** | Optional | String | Eine allgemeine Nachricht oder Beschreibung, entweder im Datensatz enthalten oder aus ihm generiert. |
| **EventCount** | Obligatorisch. | Integer | Die Anzahl der Ereignisse, die vom Datensatz beschrieben werden.<br><br>Dieser Wert wird verwendet, wenn die Quelle Aggregation unterstützt. Ein einzelner Datensatz kann mehrere Ereignisse darstellen.<br><br>Für andere Quellen muss dieses Feld auf `1` festgelegt werden.<br><br>Beispiel: `1`|
| **EventStartTime** | Obligatorisch. | Datum/Uhrzeit | Wenn die Quelle Aggregation unterstützt und der Datensatz mehrere Ereignisse darstellt, geben Sie mithilfe dieses Felds die Zeit an, zu der das erste Ereignis generiert wurde. <br><br>Versehen Sie in anderen Fällen das Feld [TimeGenerated](#timegenerated) mit einem Alias. |
| **EventEndTime** | Alias || Alias für das Feld [TimeGenerated](#timegenerated). |
| **EventType** | Obligatorisch. | Enumerated | Gibt den vom Datensatz gemeldeten Vorgang an. <br><Br> Mögliche Werte sind `Assign`, `Renew`, `Release` und `DNS Update`. <br><br>Beispiel: `Assign`| 
| <a name="eventresult"></a>**EventResult** | Obligatorisch. | Enumerated | Einer der folgenden Werte: `Success`, `Partial`, `Failure`, `NA` (Not Applicable/nicht zutreffend).<br> <br>Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die in diese Werte normalisiert werden sollten. Alternativ kann die Quelle auch nur das Feld [EventResultDetails](#eventresultdetails) angeben, das analysiert werden muss, um den **EventResult**-Wert abzuleiten. Wenn ein Client vom DHCP-Server unter Quarantäne gestellt wird, sollte dieses Feld auf `Partial` festgelegt werden.<br><br>Beispiel: `Success`|
| <a name="eventresultdetails"></a>**EventResultDetails** | Alias | | Grund oder Details für das im Feld „EventResult](#eventresult)“ gemeldete Ergebnis. <br><Br> Mögliche Werte sind `Exhausted`, `Quarantined` und `Denied`. <br><br>Beispiel: `Exhausted` |
| **EventOriginalResultDetails**    | Optional    | String     |  Der im ursprünglichen Datensatz bereitgestellte Wert für [EventResultDetails](#eventresultdetails), sofern er von der Quelle bereitgestellt wird. Für Windows DHCP-Serverprotokolle speichern Sie hier den Wert des Felds „QResult“. |
| **EventOriginalUid** | Optional | String | Eine eindeutige ID des ursprünglichen Datensatzes, sofern von der Quelle bereitgestellt. |
| **EventOriginalType**   | Optional    | String  |  Der ursprüngliche Ereignistyp oder die ursprüngliche Ereignis-ID, sofern von der Quelle bereitgestellt.<br><br>Beispiel: `DNS Assign Failed` |
| <a name ="eventproduct"></a>**EventProduct** | Obligatorisch. | String | Das Produkt, das das Ereignis erzeugt. Dieses Feld ist möglicherweise im Quelldatensatz nicht vorhanden. In diesem Fall muss es vom Parser festgelegt werden. <br><br>Beispiel: `DHCP Server` |
| **EventProductVersion** | Optional | String | Die Version des Produkts, das das Ereignis erzeugt. Dieses Feld ist möglicherweise im Quelldatensatz nicht vorhanden. In diesem Fall muss es vom Parser festgelegt werden. <br><br>Beispiel: `12.1` |
| <a name="eventvendor"></a>**EventVendor** | Obligatorisch. | String | Der Hersteller des Produkts, das das Ereignis erzeugt. Dieses Feld ist möglicherweise im Quelldatensatz nicht vorhanden. In diesem Fall muss es vom Parser festgelegt werden.<br><br>Beispiel: `Microsoft`|
| **EventSchemaVersion** | Obligatorisch. | String | Die Version des hier dokumentierten Schemas lautet **0.1.0**. |
| **EventSchema** | Obligatorisch. | String | Der Name des hier dokumentierten Schemas lautet **Dhcp**. |
| **EventReportUrl** | Optional | String | Eine URL, die im Ereignis für eine Ressource bereitgestellt wird, die weitere Informationen zum Ereignis enthält. |
| <a name="dvc"></a>**Dvc** | Alias | String | Ein eindeutiger Bezeichner des DHCP-Servers.<br><br>Beispiel: `ContosoDc.Contoso.Azure`<br><br>Dieses Feld kann ein Alias für die Felder [DvcFQDN](#dvcfqdn), [DvcId](#dvcid), [DvcHostname](#dvchostname) oder [DvcIpAddr](#dvcipaddr) sein. Verwenden Sie für Cloudquellen, für die kein offensichtliches Gerät vorliegt, denselben Wert wie im Feld [Ereignisprodukt](#eventproduct). |
| <a name="dvcipaddr"></a>**DvcIpAddr** | Empfohlen | IP-Adresse | Die IP-Adresse des DHCP-Servers.<br><br>Beispiel: `2001:db8::ff00:42:8329` |
| <a name="dvchostname"></a>**DvcHostname** | Obligatorisch. | String | Der Hostname des DHCP-Servers ohne Domäneninformationen. Wenn kein Gerätename verfügbar ist, speichern Sie die entsprechende IP-Adresse in diesem Feld.<br><br>Beispiel: `DESKTOP-1282V4D` |
| <a name="dvcdomain"></a>**DvcDomain** | Empfohlen | String | Die Domäne des DHCP-Servers.<br><br>Beispiel: `Contoso` |
| <a name="dvcdomaintype"></a>**DvcDomainType** | Empfohlen | Enumerated | Der Typ von [DvcDomain](#dvcdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows (contoso\mypc)`<br>- `FQDN (docs.microsoft.com)`<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [DvcDomain](#dvcdomain) verwendet wird. |
| <a name="dvcfqdn"></a>**DvcFQDN** | Optional | String | Der Hostname des DHCP-Servers, einschließlich Domäneninformationen (sofern verfügbar). <br><br> Beispiel: `Contoso\DESKTOP-1282V4D`<br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. Das Feld [DvcDomainType](#dvcdomaintype) spiegelt das verwendete Format wider.  |
| <a name="dvcid"></a>**DvcId** | Optional | String | Die ID des DHCP-Servers, wie im Datensatz angegeben.<br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **DvcIdType** | Optional | Enumerated | Der Typ von [DvcId](#dvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der Liste, und speichern Sie die anderen anhand der Feldnamen **DvcAzureResourceId** bzw. **DvcMDEid**.<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [DvcId](#dvcid) verwendet wird. |
| **EventSeverity** | Optional | Enumerated | Legen Sie `Low` fest, wenn der DHCP-Server den Client unter Quarantäne gestellt hat, und `Medium`, wenn der Server den Client blockiert hat. Andernfalls legen Sie `Informational` fest. <br><br>Beispiel: `Informational`|
| <a name="dvcaction"></a>**DvcAction** | Optional | Enumerated | Die vom DHCP-Server ausgeführte Aktion. Mögliche Werte sind `Allow`, `Deny` und `Quarantine`.<br><br>Beispiel: `Deny` |
| <a name="additionalfields"></a>**AdditionalFields** | Optional | Dynamisch | Wenn Ihre Quelle andere Informationen enthält, die erhalten bleiben sollten, behalten Sie sie entweder mit den ursprünglichen Feldnamen bei, oder erstellen Sie das dynamische Feld **AdditionalFields**, und fügen Sie ihm die zusätzlichen Informationen als Schlüssel-Wert-Paare hinzu. |
| | | | |

### <a name="dhcp-specific-fields"></a>DHCP-spezifische Felder

Die nachfolgend aufgeführten Felder sind für DHCP-Ereignisse spezifisch, doch ähneln viele den Feldern in anderen Schemas und folgen derselben Benennungskonvention.

| **Feld** | **Klasse** | **Typ** | **Hinweise** |
| --- | --- | --- | --- |
| <a name="srcipaddr"></a>**SrcIpAddr** | Obligatorisch. | IP-Adresse | Die IP-Adresse, die dem Client vom DHCP-Server zugewiesen wird.<br><br>Beispiel: `192.168.12.1` |
| <a name="ipaddr"></a>**IpAddr** | Alias | | Alias für [SrcIpAddr](#srcipaddr). |
| <a name="requestedipaddr"></a>**RequestedIpAddr** | Optional | IP-Adresse | Die vom DHCP-Client angeforderte IP-Adresse (sofern verfügbar).<br><br>Beispiel: `192.168.12.3` |
| <a name="srchostname"></a>**SrcHostname** | Obligatorisch. | String | Der Hostname des Geräts, das die DHCP-Lease anfordert. Wenn kein Gerätename verfügbar ist, speichern Sie die entsprechende IP-Adresse in diesem Feld.<br><br>Beispiel: `DESKTOP-1282V4D` |
| <a name="hostname"></a>**Hostname** | Alias | | Alias für [SrcHostname](#srchostname). |
| <a name="srcdomain"></a> **SrcDomain** | Empfohlen | String | Die Domäne des Quellgeräts.<br><br>Beispiel: `Contoso` |
| <a name="srcdomaintype"></a>**SrcDomainType** | Empfohlen | Enumerated | Der Typ von [SrcDomain](#srcdomain), sofern bekannt. Mögliche Werte sind:<br>- `Windows` (Beispiel: `contoso`)<br>- `FQDN` (Beispiel: `microsoft.com`)<br><br>Erforderlich, wenn [SrcDomain](#srcdomain) verwendet wird. |
| **SrcFQDN** | Optional | String | Der Hostname des Quellgeräts ggf. mit Informationen zur Domäne. <br><br>**Hinweis**: Dieses Feld unterstützt sowohl das herkömmliche FQDN-Format als auch das Windows-Format „Domäne\Hostname“. Das Feld [SrcDomainType](#srcdomaintype) spiegelt das verwendete Format wider. <br><br>Beispiel: `Contoso\DESKTOP-1282V4D` |
| <a name="srcdvcid"></a>**SrcDvcId** | Optional | String | Die ID des Quellgeräts, wie im Datensatz angegeben.<br><br>Beispiel: `ac7e9755-8eae-4ffc-8a02-50ed7a2216c3` |
| **SrcDvcIdType** | Optional | Enumerated | Der Typ von [SrcDvcId](#srcdvcid), sofern bekannt. Mögliche Werte sind:<br> - `AzureResourceId`<br>- `MDEid`<br><br>Wenn mehrere IDs verfügbar sind, verwenden Sie die erste aus der obigen Liste, und speichern Sie die anderen in den Feldern **SrcDvcAzureResourceId** bzw. **SrcDvcMDEid**.<br><br>**Hinweis**: Dieses Feld ist erforderlich, wenn das Feld [SrcDvcId](#srcdvcid) verwendet wird. |
| **SrcDeviceType** | Optional | Enumerated | Der Typ des Quellgeräts. Mögliche Werte sind:<br>- `Computer`<br>- `Mobile Device`<br>- `IOT Device`<br>- `Other` |
| <a name="srcuserid"></a>**SrcUserId** | Optional | String | Eine maschinenlesbare, alphanumerische, eindeutige Darstellung des Quellbenutzers. Format und unterstützte Typen:<br>- **SID** (Windows): `S-1-5-21-1377283216-344919071-3415362939-500`<br>- **UID** (Linux): `4578`<br>- **AADID** (Azure Active Directory): `9267d02c-5f76-40a9-a9eb-b686f3ca47aa`<br>- **OktaId**: `00urjk4znu3BcncfY0h7`<br>- **AWSId**: `72643944673`<br><br>Speichern Sie den ID-Typ im Feld [SrcUserIdType](#srcuseridtype). Wenn andere IDs verfügbar sind, wird empfohlen, die Feldnamen in „SrcUserSid“, „SrcUserUid“, „SrcUserAadId“, „SrcUserOktaId“ bzw. „UserAwsId“ zu normalisieren.<br><br>Beispiel: `S-1-12` |
| <a name="srcuseridtype"></a>**SrcUserIdType** | Optional | Enumerated | Der Typ der ID, die im Feld [SrcUserId](#srcuserid) gespeichert ist. Unterstützte Werte: `SID`, `UIS`, `AADID`, `OktaId` und `AWSId`. |
| <a name="srcusername"></a>**SrcUsername** | Optional | String | Der Quellbenutzername einschließlich Informationen zur Domäne, falls verfügbar. Verwenden Sie eines der folgenden Formate in der folgenden Reihenfolge nach Priorität:<br>- **UPN/E-Mail**: `johndow@contoso.com`<br>- **Windows:** `Contoso\johndow`<br>- **DN**: `CN=Jeff Smith,OU=Sales,DC=Fabrikam,DC=COM`<br>- **Einfach**: `johndow`. Verwenden Sie dieses einfache Format nur, wenn keine Domäneninformationen verfügbar sind.<br><br>Speichern Sie den Benutzernamenstyp im Feld [SrcUsernameType](#srcusernametype). Wenn andere IDs verfügbar sind, empfiehlt es sich, die Feldnamen in **SrcUserUpn**, **SrcUserWindows** und **SrcUserDn** zu normalisieren.<br><br>Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `AlbertE` |
| **Benutzername** | Alias | | Alias für [SrcUsername](#srcusername). |
| <a name="srcusernametype"></a>**SrcUsernameType** | Optional | Enumerated | Gibt den Typ des Benutzernamens an, der im Feld [SrcUsername](#srcusername) gespeichert ist. Unterstützte Werte: `UPN`, `Windows`, `DN` und `Simple`. Weitere Informationen finden Sie unter [Benutzerentität](normalization-about-schemas.md#the-user-entity).<br><br>Beispiel: `Windows` |
| **SrcUserType** | Optional | Enumerated | Der Typ des Akteurs. Zulässige Werte sind:<br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other`<br><br>**Hinweis**: Der Wert kann im Quelldatensatz mit anderen Begriffen angegeben werden, die auf diese Werte normalisiert werden sollten. Speichern Sie den ursprünglichen Wert im Feld [EventOriginalUserType](#srcoriginalusertype). |
| <a name="srcoriginalusertype"></a>**SrcOriginalUserType** | | | Der ursprüngliche Quellbenutzertyp, sofern von der Quelle bereitgestellt. |
| <a name="srcmacaddr"></a>**SrcMacAddr** | Obligatorisch. | MAC-Adresse | Die MAC-Adresse des Clients, der eine DHCP-Lease anfordert. <br><br>**Hinweis**: Der Windows DHCP-Server protokolliert die MAC-Adresse auf nicht standardmäßige Weise, wobei die Doppelpunkte ausgelassen werden, die vom Parser eingefügt werden sollten.<br><br>Beispiel: `06:10:9f:eb:8f:14` |
| <a name="dhcpleaseduration"></a>**DhcpLeaseDuration** | Optional | Integer | Die Länge der einem Client gewährten Lease in Sekunden. |  
|<a name="dhcpsessionid"></a>**DhcpSessionId** | Optional | Zeichenfolge | Der Sitzungsbezeichner, der vom meldenden Gerät gemeldet wird. Für den Windows DHCP-Server legen Sie diesen auf das Feld „TransactionID“ fest. <br><br>Beispiel: `2099570186` |
| **SessionId** | Alias | String | Alias für [DhcpkSessionId](#dhcpsessionid). |
| <a name="dhcpsessionduration"></a>**DhcpSessionDuration** | Optional | Integer | Die Zeitspanne in Millisekunden für den Abschluss der DHCP-Sitzung.<br><br>Beispiel: `1500` |
| **Duration** | Alias | | Alias für [DhcpSessionDuration](#dhcpsessionduration). |
| **DhcpSrcDHCId** | Optional | String | Die DHCP-Client-ID gemäß [RFC4701](https://datatracker.ietf.org/doc/html/rfc4701). |
| **DhcpCircuitId** | Optional | String | Die DHCP-Leitungs-ID gemäß [RFC3046](https://datatracker.ietf.org/doc/html/rfc3046). |
| **DhcpSubscriberId** | Optional | String | Die DHCP-Abonnenten-ID gemäß [RFC3993](https://datatracker.ietf.org/doc/html/rfc3993). |
| **DhcpVendorClassId**  | Optional | String | Die DHCP-Herstellerklassen-ID gemäß [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925). |
| **DhcpVendorClass**  | Optional | String | Die DHCP-Herstellerklasse gemäß [RFC3925](https://datatracker.ietf.org/doc/html/rfc3925).|
| **DhcpUserClassId**  | Optional | String | Die DHCP-Benutzerklassen-ID gemäß [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004).|
| **DhcpUserClass** | Optional | String | Die DHCP-Benutzerklasse gemäß [RFC3004](https://datatracker.ietf.org/doc/html/rfc3004).|
| | | | |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- Sehen Sie sich das [ASIM-Webinar](https://www.youtube.com/watch?v=WoGD-JeC7ng) an, oder befassen Sie sich mit den [Folien](https://1drv.ms/b/s!AnEPjr8tHcNmjDY1cro08Fk3KUj-?e=murYHG).
- [Schemas des erweiterten SIEM-Informationsmodells](normalization-about-schemas.md)
- [Parser des erweiterten SIEM-Informationsmodells](normalization-about-parsers.md)
- [Inhalte des erweiterten SIEM-Informationsmodells](normalization-content.md)
