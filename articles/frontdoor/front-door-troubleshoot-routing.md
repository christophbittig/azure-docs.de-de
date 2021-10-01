---
title: Behandeln von Problemen mit der Azure Front Door-Konfiguration
description: In diesem Tutorial erfahren Sie, wie Sie selbst einige häufige Probleme mit Ihrer Azure Front Door-Instanz behandeln können.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2021
ms.author: duau
ms.openlocfilehash: ed47d310f418936b84c505fcf254947a67f0eb6d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124824415"
---
# <a name="troubleshooting-common-routing-problems"></a>Behandeln von häufigen Routingproblemen

In diesem Artikel wird beschrieben, wie Sie häufige Routingprobleme in Ihrer Azure Front Door-Konfiguration beheben können.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>Antwort des Typs 503 von Azure Front Door nach einigen Sekunden

### <a name="symptom"></a>Symptom

* Reguläre Anforderungen, die ohne Azure Front Door an das Back-End gesendet werden, sind erfolgreich. Das Durchlaufen von Azure Front Door führt zu 503-Fehlermeldungen.
* Der Fehler von Azure Front Door wird in der Regel nach ungefähr 30 Sekunden angezeigt.
* Zeitweilige 503-Fehler mit dem Protokoll `ErrorInfo: OriginInvalidResponse`.

### <a name="cause"></a>Ursache

Für dieses Problem gibt es drei mögliche Ursachen:
 
* Ihr Back-End benötigt länger als das konfigurierte Timeout (Standardwert: 30 Sekunden), um die Anforderung von Azure Front Door zu erhalten.
* Die zum Beantworten der Anforderung von Azure Front Door erforderliche Zeit überschreitet den Timeoutwert.
* Der Client hat eine Bytebereichsanforderung mit `Accept-Encoding header` (Komprimierung aktiviert) gesendet.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

* Senden Sie die Anforderung direkt an Ihr Back-End (ohne Azure Front Door). Beobachten Sie, wie lange es normalerweise dauert, bis Ihr Back-End antwortet.
* Senden Sie die Anforderung über Azure Front Door, und prüfen Sie, ob Sie Antworten des Typs 503 erhalten. Falls nicht, ist das Timeout möglicherweise nicht das Problem. Wenden Sie sich an den Support.
* Wenn Anforderungen, die Azure Front Door durchlaufen, zu einem 503-Fehlerantwortcode führt, konfigurieren Sie die Einstellung **Timeout für Senden/Empfangen (in Sekunden)** für Azure Front Door. Sie können das Standardtimeout auf bis zu 4 Minuten (240 Sekunden) verlängern. Die Einstellung kann im *Front Door-Designer* über den Menüpunkt **Einstellungen** konfiguriert werden.

    :::image type="content" source=".\media\troubleshoot-route-issues\send-receive-timeout.png" alt-text="Screenshot des Felds „Timeout für Senden/Empfangen“ im Front Door-Designer.":::

* Wenn das Timeout das Problem nicht löst, verwenden Sie ein Tool wie Fiddler oder das Entwicklertool Ihres Browsers, um zu überprüfen, ob der Client Bytebereichsanforderungen mit Accept-Encoding-Headern sendet, was dazu führt, dass das Ursprungsobjekt mit unterschiedlichen Inhaltslängen antwortet. Wenn ja, können Sie entweder die Komprimierung für das Ursprungsobjekt bzw. Azure Front Door deaktivieren oder eine Regelsatzregel erstellen, um `accept-encoding` aus der Anforderung für Bytebereichsanforderungen zu entfernen.

    :::image type="content" source=".\media\troubleshoot-route-issues\remove-encoding-rule.png" alt-text="Screenshot der Accept-Encoding-Regel im Regelmodul.":::

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Für an die benutzerdefinierte Domäne gesendete Anforderungen wird der Statuscode 400 zurückgegeben

### <a name="symptom"></a>Symptom

* Sie haben eine Azure Front Door-Instanz erstellt, aber für eine Anforderung an die Domäne oder den Front-End-Host wird der HTTP-Statuscode 400 zurückgegeben.
* Sie haben eine DNS-Zuordnung für eine benutzerdefinierte Domäne zu dem von Ihnen konfigurierten Front-End-Host erstellt. Beim Senden einer Anforderung an den Hostnamen der benutzerdefinierten Domäne wird jedoch ein HTTP-Statuscode 400 zurückgegeben. Außerdem wird offenbar kein Routing zu dem von Ihnen konfigurierten Back-End durchgeführt.

### <a name="cause"></a>Ursache

Das Problem tritt auf, wenn Sie keine Routingregel für die benutzerdefinierte Domäne konfiguriert haben, die als Front-End-Host hinzugefügt wurde. Für diesen Front-End-Host muss explizit eine Routingregel hinzugefügt werden. Selbst wenn bereits eine Routingregel für den Front-End-Host unter der Azure Front Door-Unterdomäne (*.azurefd.net) konfiguriert wurde, muss dies erfolgen.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Fügen Sie eine Routingregel für die benutzerdefinierte Domäne hinzu, um Datenverkehr an den ausgewählten Back-End-Pool weiterzuleiten.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door leitet HTTP oder HTTPS nicht weiter

### <a name="symptom"></a>Symptom

Für Azure Front Door ist eine Routingregel vorhanden, nach der HTTP zu HTTPS umgeleitet wird, doch beim Zugriff auf die Domäne wird HTTP als Protokoll beibehalten.

### <a name="cause"></a>Ursache

Dieses Verhalten kann auftreten, wenn Sie die Routingregeln für Azure Front Door nicht ordnungsgemäß konfiguriert haben. Im Grunde genommen ist Ihre aktuelle Konfiguration nicht spezifisch und kann miteinander in Konflikt stehende Regeln aufweisen.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Eine Anforderung an den Front-End-Hostnamen gibt den Statuscode 404 zurück

### <a name="symptom"></a>Symptom

Sie haben eine Azure Front Door-Instanz erstellt, indem Sie einen Front-End-Host, einen Back-End-Pool mit mindestens einem Back-End und eine Routingregel konfiguriert haben, die den Front-End-Host mit dem Back-End-Pool verbindet. Ihre Inhalte sind nicht verfügbar, wenn Sie eine Anforderung an den konfigurierten Front-End-Host senden. Folglich gibt die Anforderung den Statuscode HTTP 404 zurück.

### <a name="cause"></a>Ursache

Dieses Symptom kann verschiedene Ursachen haben:

* Das Back-End ist nicht öffentlich zugänglich und für Azure Front Door nicht sichtbar.
* Das Back-End ist falsch konfiguriert, was dazu führt, dass Azure Front Door falsche Anforderungen sendet. Das heißt, das Back-End akzeptiert nur HTTP, und Sie haben HTTPS zugelassen. Azure Front Door versucht also, HTTPS-Anforderungen weiterzuleiten.
* Das Back-End lehnt den Hostheader ab, der mit der Anforderung an das Back-End weitergeleitet wurde.
* Die Konfiguration für das Back-End wurde noch nicht vollständig bereitgestellt.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

* Überprüfen Sie die Bereitstellungszeit:
   * Warten Sie mindestens zehn Minuten, dass die Konfiguration bereitgestellt wird.

* Überprüfen Sie die Back-End-Einstellungen:
    * Wechseln Sie zum Back-End-Pool, an den die Anforderung weitergeleitet werden soll. (Dies hängt davon ab, wie Sie die Routingregel konfiguriert haben.) Überprüfen Sie, ob der Back-End-Hosttyp und der Back-End-Hostname richtig sind. Wenn das Back-End ein benutzerdefinierter Host ist, stellen Sie sicher, dass er richtig geschrieben wurde. 

    * Überprüfen Sie die HTTP- und HTTPS-Ports. In den meisten Fällen ist 80 bzw. 443 korrekt, und es sollten keine Änderungen erforderlich sein. Allerdings besteht die Möglichkeit, dass Ihr Back-End anders konfiguriert ist und an einem anderen Port lauscht.

    * Überprüfen Sie den Back-End-Hostheader, der für die Back-Ends konfiguriert wurde, an die der Front-End-Host weiterleiten soll. In den meisten Fällen sollte dieser Header mit dem Back-End-Hostnamen identisch sein. Ein falscher Wert kann jedoch zu verschiedenen 4xx-HTTP-Statuscodes führen, wenn das Back-End etwas anderes erwartet. Wenn Sie die IP-Adresse Ihres Back-Ends eingeben, müssen Sie möglicherweise den Back-End-Hostheader auf den Back-End-Hostnamen festlegen.

* Überprüfen Sie die Einstellungen der Routingregel:
    * Navigieren Sie zur Routingregel, die vom fraglichen Front-End-Hostnamen an einen Back-End-Pool weiterleiten soll. Überprüfen Sie, ob die akzeptierten Protokolle ordnungsgemäß konfiguriert sind, wenn die Anforderung weitergeleitet wird. Das Feld **Zulässige Protokolle** bestimmt, welche Anforderungen von Azure Front Door akzeptiert werden sollten. Das Weiterleitungsprotokoll bestimmt, welches Protokoll Azure Front Door verwenden soll, um die Anforderung an das Back-End weiterzuleiten.
      
      Wenn das Back-End beispielsweise nur HTTP-Anforderungen akzeptiert, wären die folgenden Konfigurationen gültig:
            
      * Zulässige Protokolle sind HTTP und HTTPS. Das Weiterleitungsprotokoll ist HTTP. Eine Übereinstimmungsanforderung funktioniert nicht, da es sich bei HTTPS um ein zulässiges Protokoll handelt. Bei einer HTTPS-Anforderung würde Azure Front Door versuchen, sie mithilfe von HTTPS weiterzuleiten.

      * Das zulässige Protokoll ist HTTP. Das Weiterleitungsprotokoll ist entweder eine Übereinstimmungsanforderung oder HTTP.
    - **URL-Rewrite** ist standardmäßig deaktiviert. Verwenden Sie dieses Feld nur, wenn Sie den Umfang der auf dem Back-End gehosteten Ressourcen, die verfügbar sein sollen, einschränken möchten. Wenn das Feld deaktiviert ist, leitet Azure Front Door den gleichen Anforderungspfad weiter, der eingegangen ist. Es ist möglich, dieses Feld fehlerhaft zu konfigurieren. Wenn Azure Front Door also eine Ressource aus dem Back-End anfordert, die nicht verfügbar ist, wird ein HTTP-Statuscode 404 zurückgegeben.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Eine Anforderung an den Front-End-Hostnamen gibt den Statuscode 411 zurück

### <a name="symptom"></a>Symptom

Sie haben eine Azure Front Door-Instanz erstellt und einen Front-End-Host, einen Back-End-Pool mit mindestens einem Back-End und eine Routingregel konfiguriert, die den Front-End-Host mit dem Back-End-Pool verbindet. Ihre Inhalte sind offenbar nicht verfügbar, wenn Sie eine Anforderung an den konfigurierten Front-End-Host senden, da der HTTP-Statuscode 411 zurückgegeben wird.

Antworten auf diese Anforderungen können auch eine HTML-Fehlerseite mit einer Erläuterung im Antworttext enthalten. Beispiel: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Ursache

Dieses Symptom kann verschiedene Ursachen haben. Grundsätzlich liegt es daran, dass Ihre HTTP-Anforderung nicht vollständig RFC-konform ist. 

Ein Beispiel für Nichtkonformität ist eine `POST`-Anforderung, die ohne einen `Content-Length`- oder `Transfer-Encoding`-Header gesendet wird (z. B. mit `curl -X POST https://example-front-door.domain.com`). Diese Anforderung erfüllt nicht die Voraussetzungen, die in [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)festgelegt sind. Azure Front Door würde sie mit einer HTTP 411-Antwort blockieren.

Dieses Verhalten ist unabhängig von der WAF-Funktionalität (Web Application Firewall) von Azure Front Door. Derzeit gibt es keine Möglichkeit, dieses Verhalten zu deaktivieren. Alle HTTP-Anforderungen müssen die Anforderungen erfüllen, selbst wenn die WAF-Funktion nicht verwendet wird.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

- Vergewissern Sie sich, dass Ihre Anforderungen die Vorgaben in den entsprechenden RFCs erfüllen.

- Notieren Sie sich den HTML-Nachrichtentext, der als Antwort auf Ihre Anforderung zurückgegeben wird. Ein Nachrichtentext erläutert häufig genau, *in welcher Hinsicht* Ihre Anforderung nicht kompatibel ist.
