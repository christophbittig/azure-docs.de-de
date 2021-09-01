---
title: Protokollierung in Azure Front Door Standard/Premium (Vorschau)
description: In diesem Artikel wird die Funktionsweise der Protokollierung in Azure Front Door Standard/Premium erläutert.
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 08/26/2021
ms.author: duau
ms.openlocfilehash: 5446c240d1d07b5b99fe6f91a031617756a6018c
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967590"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Protokollierung in Azure Front Door Standard/Premium (Vorschau)

> [!Note]
> Diese Dokumentation ist für Azure Front Door Standard/Premium (Vorschau) gedacht. Suchen Sie nach Informationen zu Azure Front Door? Informationen finden Sie [hier](../front-door-overview.md).

Azure Front Door bietet eine andere Art der Protokollierung zum Nachverfolgen, Überwachen und Debuggen von Front Door. 

* Zugriffsprotokolle enthalten ausführliche Informationen zu jeder Anforderung, die von AFD empfangen wird, und helfen Ihnen beim Analysieren und Überwachen von Zugriffsmustern sowie bei der Behebung von Problemen. 
* Aktivitätsprotokolle geben Einblicke in die Vorgänge, die für Azure-Ressourcen ausgeführt wurden.  
* In Integritätstestprotokollen wird jeder fehlerhafte Test für den Ursprung protokolliert. 
* WAF-Protokolle (Web Application Firewall) enthalten ausführliche Informationen zu Anforderungen, die im Erkennungs- oder Schutzmodus eines Azure Front Door-Endpunkts protokolliert werden. Über diese Protokolle kann auch eine benutzerdefinierte Domäne, die mit WAF konfiguriert wird, angezeigt werden.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (Vorschau) befindet sich derzeit in der öffentlichen Vorschauversion.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zugriffsprotokolle, Integritätstestprotokolle und WAF-Protokolle sind standardmäßig nicht aktiviert. Führen Sie die folgenden Schritte aus, um die Protokollierung zu aktivieren. Aktivitätsprotokolleinträge werden standardmäßig gesammelt und können im Azure-Portal angezeigt werden. Protokolle können Verzögerungen von einigen Minuten aufweisen. 

Sie haben drei Möglichkeiten, um Ihre Protokolle zu speichern: 

* **Speicherkonto:** Speicherkonten eignen sich am besten für Szenarien, in denen Protokolle längere Zeit gespeichert und bei Bedarf überprüft werden. 
* **Event Hubs:** Event Hubs sind eine hervorragende Möglichkeit für die Integration in andere SIEM-Tools (Security Information and Event Management) oder andere externe Datenspeicher. Beispiel: Splunk/DataDog/Sumo. 
* **Azure Log Analytics:** Azure Log Analytics in Azure Monitor eignet sich am besten für die allgemeine Echtzeitüberwachung und -analyse der Leistung von Azure Front Door.

## <a name="configure-logs"></a>Konfigurieren von Protokollen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie nach „Azure Front Door Standard/Premium“, und wählen Sie das Azure Front Door-Profil aus.

1. Wechseln Sie im Profil zu **Überwachung**, und wählen Sie **Diagnoseeinstellungen** aus. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="Screenshot: Landing Page mit Diagnoseeinstellungen":::

1. Geben Sie unter **Diagnoseeinstellungen** in  **Name der Diagnoseeinstellungen** einen Namen ein.

1. Wählen Sie **FrontDoorAccessLog**, **FrontDoorHealthProbeLog** oder **FrontDoorWebApplicationFirewallLog** als  **Protokoll** aus.

1. Wählen Sie die  **Zieldetails** aus. Folgende Optionen für das Ziel sind verfügbar: 

    * **An Log Analytics senden**
        * Wählen Sie das *Abonnement* und den *Log Analytics-Arbeitsbereich* aus.
    * **In einem Speicherkonto archivieren**
        * Wählen Sie das *Abonnement* und das *Speicherkonto* aus. Legen Sie **Beibehaltung (Tage)** fest.
    * **An einen Event Hub streamen**
        * Wählen Sie unter  *Abonnement, Event Hub-Namespace, Event Hub-Name (optional)* und  *Event Hub-Richtlinienname* die entsprechenden Angaben aus. 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="Screenshot der Seite „Diagnoseeinstellungen“":::

1. Klicken Sie auf **Speichern**.

## <a name="access-log"></a>Zugriffsprotokoll

Azure Front Door bietet derzeit einzelne API-Anforderungen, wobei jeder Eintrag über das folgende Schema verfügt und wie unten dargestellt im JSON-Format protokolliert wird. 

| Eigenschaft | BESCHREIBUNG |
|----------|-------------| 
| TrackingReference | Die eindeutige Verweiszeichenfolge zur Identifizierung einer Anforderung, die von AFD verarbeitet wird. Diese wird auch als X-Azure-Ref-Header an den Client gesendet. Sie ist für die Suche nach Informationen in den Zugriffsprotokollen für eine bestimmte Anforderung erforderlich. |
| Zeit | Das Datum und die Uhrzeit, zu denen das AFD-Edge den angeforderten Inhalt an den Client gesendet hat (in UTC). |
| HttpMethod | Die von der Anforderung verwendete HTTP-Methode: DELETE, GET, HEAD, OPTIONS, PATCH, POST oder PUT. |
| HttpVersion | Die HTTP-Version, die der Viewer in der Anforderung angegeben hat. |
| RequestUri | URI der empfangenen Anforderung Dieses Feld ist ein vollständiger URI mit Schema, Port, Domäne, Pfad und Abfragezeichenfolge. |
| HostName | Der Hostname in der Anforderung vom Client. Wenn Sie benutzerdefinierte Domänen aktivieren und eine Platzhalterdomäne („*.contoso.com“) angegeben ist, lautet der Hostname „a.contoso.com“. Wenn Sie die Azure Front Door-Domäne („contoso.azurefd.net“) verwenden, lautet der Hostname „contoso.azurefd.net“. |
| RequestBytes | Die Größe der HTTP-Anforderungsnachricht in Byte, einschließlich Anforderungsheader und -text. Die Anzahl der Datenbytes, die der Viewer in die Anforderung eingeschlossen hat, einschließlich der Header. |
| ResponseBytes | Die vom Back-End-Server als Antwort gesendeten Bytes. |
| UserAgent | Der vom Client verwendete Browsertyp |
| ClientIp | Die IP-Adresse des Clients, der die ursprüngliche Anforderung gestellt hat. Wenn in der Anforderung ein X-Forwarded-For-Header vorhanden ist, wird die Client-IP daraus ausgewählt. |
| SocketIp | Die IP-Adresse der direkten Verbindung mit dem AFD-Edge. Wenn der Client einen HTTP-Proxy oder einen Lastenausgleich zum Senden der Anforderung verwendet hat, ist der Wert von „SocketIp“ die IP-Adresse des Proxys oder Lastenausgleichs. |
| timeTaken | Die Zeitspanne in Millisekunden zwischen dem Empfang einer Clientanforderung auf dem AFD-Edgeserver und dem Senden des letzten Bytes der Antwort an den Client durch AFD. In diesem Feld werden Netzwerklatenz und TCP-Pufferung nicht berücksichtigt. |
| RequestProtocol | Das Protokoll, das der Client in der Anforderung angegeben hat: HTTP oder HTTPS. |
| SecurityProtocol | Die TLS-/SSL-Protokollversion, die von der Anforderung verwendet wird, oder NULL, wenn keine Verschlüsselung verwendet wird. Mögliche Werte: SSLv3, TLSv1, TLSv1.1, TLSv1.2 |
| SecurityCipher | Wenn der Wert für „RequestProtocol“ HTTPS lautet, gibt dieses Feld das vom Client und der AFD ausgehandelte TLS/SSL-Verschlüsselungsverfahren an. |
| Endpunkt | Der Domänenname des AFD-Endpunkts, z. B. „contoso.Z01.azurefd.net“. |
| HttpStatusCode | Der von AFD zurückgegebene HTTP-Statuscode. |
| POP | Der Edge-POP, der auf die Benutzeranforderung geantwortet hat.  |
| Cachestatus | Gibt den Statuscode an, der die Behandlung der Anforderung durch den CDN-Dienst beim Zwischenspeichern angibt. Mögliche Werte: HIT: Die HTTP-Anforderung wurde vom AFD-Edge-POP-Cache bereitgestellt. <br> **MISS**: Die HTTP-Anforderung wurde vom Ursprung bereitgestellt. <br/> **PARTIAL_HIT**: Einige Bytes aus einer Anforderung wurden vom AFD-Edge-POP-Cache bereitgestellt, während andere Bytes vom Ursprung für Objektblockerstellungszenarien bereitgestellt wurden. <br> **CACHE_NOCONFIG**: Das Weiterleiten von Anforderungen ohne Cacheeinstellungen (einschließlich Umgehung). <br/> **PRIVATE_NOSTORE**: In den Cacheeinstellungen wurde vom Kunden kein Cache konfiguriert. <br> **REMOTE_HIT**: Die Anforderung wurde vom Cache des übergeordneten Knotens bereitgestellt. <br/> **N/A**:** Die Anforderung wurde von der signierten URL und dem Regelsatz abgelehnt. |
| MatchedRulesSetName | Die Namen der Regeln, die verarbeitet wurden. |
| RouteName | Der Name der Route, der die Anforderung entspricht. |
| ClientPort | Der IP-Port des Clients, der die Anforderung gestellt hat. |
| Referrer | Die URL der Website, von der die Anforderung stammt. |
| TimetoFirstByte | Die in Azure Front Door gemessene Zeitspanne in Millisekunden zwischen dem Zeitpunkt, zu dem AFD die Anforderung erhält, und dem Zeitpunkt, zu dem das erste Byte an den Client gesendet wird. Mit dieser Eigenschaft werden nicht die Clientdaten gemessen. |
| ErrorInfo | Dieses Feld enthält ausführliche Informationen zum Fehlertoken für jede Antwort. <br> **NoError**: Gibt an, dass kein Fehler gefunden wurde. <br> **CertificateError**: Generischer SSL-Zertifikatsfehler. <br> **CertificateNameCheckFailed**: Der Hostname im SSL-Zertifikat ist ungültig oder stimmt nicht überein. <br> **ClientDisconnected**: Anforderungsfehler aufgrund der Clientnetzwerkverbindung. <br> **ClientGeoBlocked**: Der Client wurde aufgrund des geografischen Standorts der IP-Adresse blockiert. <br> **UnspecifiedClientError**: Generischer Clientfehler. <br> **InvalidRequest**: Ungültige Anforderung. Dies kann aufgrund einer falschen Formatierung von Header, Text und URL auftreten. <br> **DNSFailure**: DNS-Fehler. <br> **DNSTimeout**: Die DNS-Abfrage zum Ermitteln des Back-End-Timeouts. <br> **DNSNameNotResolved**: Der Servername oder die Adresse konnte nicht aufgelöst werden. <br> **OriginConnectionAborted**: Die Verbindung mit dem Ursprung wurde nicht ordnungsgemäß getrennt. <br> **OriginConnectionError**: Generischer Fehler der Ursprungsverbindung. <br> **OriginConnectionRefused**: Die Verbindung mit dem Ursprung wurde nicht hergestellt. <br> **OriginError**: Generischer Ursprungsfehler. <br> **OriginInvalidRequest**: Es wurde eine ungültige Anforderung an den Ursprung gesendet. <br> **ResponseHeaderTooBig**: Der Ursprung hat einen zu großen Antwortheader zurückgegeben. <br> **OriginInvalidResponse**:** Der Ursprung hat eine ungültige oder unbekannte Antwort zurückgegeben. <br> **OriginTimeout**: Das Zeitlimit für die Ursprungsanforderung ist abgelaufen. <br> **ResponseHeaderTooBig**: Der Ursprung hat einen zu großen Antwortheader zurückgegeben. <br> **RestrictedIP**: Die Anforderung wurde aufgrund einer eingeschränkten IP-Adresse blockiert. <br> **SSLHandshakeError**: Aufgrund eines Fehlers beim SSL-Handshake kann keine Verbindung mit dem Ursprung hergestellt werden. <br> **SSLInvalidRootCA**: Die Stammzertifizierungsstelle war ungültig. <br> **SSLInvalidCipher**: Das Verschlüsselungsverfahren, für das die HTTPS-Verbindung hergestellt wurde, war ungültig. <br> **OriginConnectionAborted**: Die Verbindung mit dem Ursprung wurde nicht ordnungsgemäß getrennt. <br> **OriginConnectionRefused**: Die Verbindung mit dem Ursprung wurde nicht hergestellt. <br> **UnspecifiedError**: Es ist ein Fehler aufgetreten, der keinem Fehler in der Tabelle entspricht. |
| OriginURL | Die vollständige URL des Ursprungs, von dem Anforderungen gesendet werden. Besteht aus dem Schema, dem Hostheader, dem Port, dem Pfad und der Abfragezeichenfolge. <br> **URL Rewrite**: Wenn der Regelsatz eine URL-Rewrite-Regel enthält, ist der Pfad der umgeschriebene Pfad. <br> **Cachetreffer beim Edge-POP**: Wenn es sich um einen Cachetreffer beim Edge-POP handelt, lautet der Ursprung „N/A“. <br> **Large request** (Umfangreiche Anforderung): Wenn der angeforderte Inhalt groß ist und mehrere segmentierte Anforderungen zurück an den Ursprung gesendet werden, entspricht dieses Feld der ersten Anforderung an den Ursprung. Weitere Informationen finden Sie unter „Objektblockerstellung“. |
| OriginIP | Die Ursprungs-IP-Adresse, über die die Anforderung bereitgestellt wurde. <br> **Cachetreffer beim Edge-POP**: Wenn es sich um einen Cachetreffer beim Edge-POP handelt, lautet der Ursprung „N/A“. <br> **Large request** (Umfangreiche Anforderung): Wenn der angeforderte Inhalt groß ist und mehrere segmentierte Anforderungen zurück an den Ursprung gesendet werden, entspricht dieses Feld der ersten Anforderung an den Ursprung. Weitere Informationen finden Sie unter „Objektblockerstellung“. |
| OriginName| Der vollständige DNS-Name (Hostname in Ursprungs-URL) für den Ursprung. <br> **Cachetreffer beim Edge-POP**: Wenn es sich um einen Cachetreffer beim Edge-POP handelt, lautet der Ursprung „N/A“. <br> **Large request** (Umfangreiche Anforderung): Wenn der angeforderte Inhalt groß ist und mehrere segmentierte Anforderungen zurück an den Ursprung gesendet werden, entspricht dieses Feld der ersten Anforderung an den Ursprung. Weitere Informationen finden Sie unter „Objektblockerstellung“. |

## <a name="health-probe-log"></a>Integritätstestprotokoll

In Integritätstestprotokollen erfolgt eine Protokollierung für jeden fehlgeschlagenen Test, um Ihnen die Diagnose des Ursprungs zu erleichtern.Die Protokolle enthalten Informationen, die Sie verwenden können, um den Ursprung wieder in Betrieb zu nehmen. Szenarien, in denen dieses Protokoll hilfreich sein kann:

* Sie haben bemerkt, dass Azure Front Door-Datenverkehr an einige der Ursprünge gesendet wurde. Beispielsweise wird nur an drei von vier Ursprüngen Datenverkehr empfangen. Sie möchten wissen, ob die Ursprünge Tests empfangen, und wenn dies nicht der Fall ist, die Fehlerursache ermitteln.  

* Sie haben festgestellt, dass der Prozentsatz der Ursprungsintegrität geringer als erwartet ist, und möchten wissen, bei welchem Ursprung ein Fehler aufgetreten ist und was die Ursache des Fehlers ist.

### <a name="health-probe-log-properties"></a>Eigenschaften im Integritätstestprotokoll

Jedes Integritätstestprotokoll weist das folgende Schema auf.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| HealthProbeId  | Eine eindeutige ID zur Identifizierung der Anforderung. |
| Zeit | Die Abschlusszeit des Tests. |
| HttpMethod | Die von der Integritätstestanforderung verwendete HTTP-Methode. Zu den Werten zählen GET und HEAD, abhängig von der Integritätstestkonfiguration. |
| Ergebnis | Der Status des Integritätstests für den Ursprung. Der Wert enthält die Erfolgsmeldung und ggf. Fehlertext. |
| HttpStatusCode  | Der HTTP-Statuscode, der vom Ursprung zurückgegeben wurde. |
| ProbeURL (target) | Die vollständige URL des Ursprungs, von dem Anforderungen gesendet werden. Besteht aus dem Schema, dem Hostheader, dem Pfad und der Abfragezeichenfolge. |
| OriginName  | Der Ursprung, von dem Anforderungen gesendet werden. Dieses Feld unterstützt die Suche nach Ursprüngen, wenn für den Ursprung FDQN konfiguriert ist. |
| POP | Der Edge-POP, über den die Testanforderung gesendet wurde. |
| Ursprungs-IP-Adresse | Die IP-Adresse des Ursprungs. Dieses Feld unterstützt die Suche nach Ursprüngen, wenn für den Ursprung FDQN konfiguriert ist. |
| TotolaLatency | Die Zeitspanne zwischen dem Senden der Anforderung durch das AFD-Edge an den Ursprung und dem Senden der letzten Antwort an das AFD-Edge. |
| ConnectionLatency| Die Dauer des Einrichtens der TCP-Verbindung zum Senden der HTTP-Testanforderung an den Ursprung. | 
| DNSResolutionLatency | Die Dauer der DNS-Auflösung, wenn der Ursprung als FDQN statt als IP-Adresse konfiguriert ist. „N/A“, wenn der Ursprung als IP-Adresse konfiguriert ist. |

### <a name="health-probe-log-sample-in-json"></a>Beispiel für ein Integritätstestprotokoll in JSON

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>Aktivitätsprotokolle

Aktivitätsprotokolle bieten Informationen zu den in Azure Front Door Standard/Premium durchgeführten Vorgängen. Die Protokolle enthalten Informationen darüber, von wem und wann in Azure Front Door Schreibvorgänge durchgeführt wurden. 

> [!NOTE]
> Aktivitätsprotokolle enthalten keine GET-Vorgänge. Sie enthalten auch keine Vorgänge, die Sie entweder mithilfe des Azure-Portals oder der ursprünglichen Verwaltungs-API ausführen. 

Greifen Sie auf Aktivitätsprotokolle in Ihrer Front Door-Instanz oder alle Protokolle Ihrer Azure-Ressourcen in Azure Monitor zu.

So zeigen Sie Aktivitätsprotokolle an:

1. Wählen Sie das Front Door-Profil aus.

1. Wählen Sie **Aktivitätsprotokoll** aus.

1. Wählen Sie einen Filterbereich und dann **Übernehmen** aus.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Berichte in Azure Front Door Standard/Premium (Vorschau)](how-to-reports.md).
- Informieren Sie sich über die [Echtzeitüberwachungsmetriken in Azure Front Door Standard/Premium (Vorschau)](how-to-monitor-metrics.md).
