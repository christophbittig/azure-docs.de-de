---
title: Überwachen der Integrität von App Service-Instanzen
description: Erfahren Sie, wie Sie die Integrität von App Service-Instanzen mithilfe der Integritätsprüfung überwachen.
keywords: Azure App Service, Web-App, Integritätsprüfung, Datenverkehr weiterleiten, fehlerfreie Instanzen, Pfad, Überwachung, fehlerhafte Instanzen entfernen, fehlerhafte Instanzen, Worker entfernen
author: msangapu-msft
ms.topic: article
ms.date: 07/19/2021
ms.author: msangapu
ms.custom: contperf-fy22q1
ms.openlocfilehash: 59e3d0f8e71de6333b3978dd9d40b51206a8a2a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462346"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Überwachen von App Service-Instanzen mit der Integritätsprüfung

In diesem Artikel wird die Integritätsprüfung im Azure-Portal verwendet, um App Service-Instanzen zu überwachen. Die Integritätsprüfung erhöht die Verfügbarkeit Ihrer Anwendung, indem Anforderungen von fehlerhaften Instanzen weg umgeleitet und Instanzen ersetzt werden, wenn sie fehlerhaft bleiben. Ihr [App Service-Plan](./overview-hosting-plans.md) sollte auf zwei oder mehr Instanzen skaliert sein, um die Integritätsprüfung in vollem Umfang nutzen zu können. Der Pfad der Integritätsüberprüfung sollte die kritischen Komponenten Ihrer Anwendung überprüfen. Wenn Ihre Anwendung z. B. von einer Datenbank und einem Messagingsystem abhängig ist, sollte der Endpunkt der Integritätsüberprüfung eine Verbindung mit diesen Komponenten herstellen. Wenn die Anwendung keine Verbindung mit einer kritischen Komponente herstellen kann, sollte der Pfad einen Antwortcode auf 500-Ebene zurückgeben, um damit anzugeben, dass die App fehlerhaft ist.

![Fehler bei der Integritätsprüfung][1]

## <a name="what-app-service-does-with-health-checks"></a>Verwendung von Integritätsprüfungen durch App Service

- Wenn Sie einen Pfad Ihrer App an die Integritätsprüfung übergeben, pingt diese den Pfad bei allen Instanzen Ihrer App Service-App in Intervallen von 1 Minute.
- Wenn eine Instanz nach zwei oder mehr Anforderungen nicht mit einem Statuscode zwischen 200 und 299 (inklusive) antwortet oder nicht auf das Pingsignal reagiert, bestimmt das System, dass sie fehlerhaft ist, und entfernt sie.
- Nach der Entfernung wird die fehlerhafte Instanz von der Integritätsprüfung weiterhin gepingt. Wenn die Instanz mit einem gesunden Statuscode (200-299) zu antworten beginnt, wird die Instanz an den Load Balancer zurückgegeben.
- Wenn eine Instanz für eine Stunde fehlerhaft bleibt, wird sie durch eine neue Instanz ersetzt.
- Beim zentralen Hoch- oder Herunterskalieren pingt App Service außerdem den Pfad der Integritätsüberprüfung, um sicherzustellen, dass neue Instanzen bereit sind.

> [!NOTE]
>- Die Integritätsprüfung folgt keinen 302-Umleitungen. Pro Stunde wird höchstens eine Instanz ersetzt, wobei der Maximalwert bei drei Instanzen pro Tag und App Service-Plan liegt.
>- Beachten Sie, dass bei Ausgabe des Status `Waiting for health check response` bei der Integritätsprüfung wahrscheinlich ein Fehler aufgrund eines HTTP-Statuscodes 307 vorliegt. Dies kann passieren, wenn Sie die HTTPS-Umleitung aktiviert, aber `HTTPS Only` deaktiviert haben.

## <a name="enable-health-check"></a>Aktivieren der Integritätsprüfung

![Navigation der Integritätsprüfung im Azure-Portal][3]

- Um die Integritätsprüfung zu aktivieren, wechseln Sie zum Azure-Portal, und wählen Sie Ihre App Service-App aus.
- Wählen Sie unter **Überwachung** die Option **Integritätsprüfung** aus.
- Wählen Sie **Aktivieren** aus, und geben Sie einen gültigen URL-Pfad für die Anwendung an, z. B. `/health` oder `/api/health`.
- Klicken Sie auf **Speichern**.

> [!CAUTION]
> Durch Änderungen an der Konfiguration der Integritätsprüfung wird Ihre App neu gestartet. Um die Auswirkungen auf Produktions-Apps zu minimieren, empfehlen wir, [Stagingslots zu konfigurieren](deploy-staging-slots.md) und diese in die Produktion zu tauschen.
>

### <a name="configuration"></a>Konfiguration

Zusätzlich zum Konfigurieren der Optionen der Integritätsprüfung können Sie auch die folgenden [App-Einstellungen](configure-common.md) konfigurieren:

| Name der App-Einstellung | Zulässige Werte | BESCHREIBUNG |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2–10 | Die erforderliche Anzahl fehlerhafter Anforderungen, damit eine Instanz als fehlerhaft eingestuft und aus dem Lastenausgleich entfernt wird. Wenn die Einstellung beispielsweise auf `2` festgelegt wird, werden Ihre Instanzen nach `2` fehlgeschlagenen Pings entfernt. (Standardwert: `10`) |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | 0-100 | Standardmäßig wird nicht mehr als die Hälfte der Instanzen gleichzeitig aus dem Lastenausgleich ausgeschlossen, um zu vermeiden, dass die verbleibenden fehlerfreien Instanzen überlastet werden. Wenn z. B. ein App Service-Plan auf vier Instanzen skaliert ist und drei fehlerhaft sind, werden zwei ausgeschlossen. Die anderen beiden Instanzen (eine fehlerfreie und eine fehlerhafte) empfangen weiterhin Anforderungen. Im ungünstigsten Fall, in dem alle Instanzen fehlerhaft sind, wird keine ausgeschlossen. <br /> Um dieses Verhalten außer Kraft zu setzen, legen Sie die App-Einstellung auf einen Wert zwischen `0` und `100` fest. Ein höherer Wert führt dazu, dass mehr fehlerhafte Instanzen entfernt werden (Standardwert: `50`). |

#### <a name="authentication-and-security"></a>Authentifizierung und Sicherheit

Die Integritätsprüfung integriert sich in die [Authentifizierungs- und Autorisierungsfunktionen](overview-authentication-authorization.md) von App Service. Es sind keine zusätzlichen Einstellungen erforderlich, wenn diese Sicherheitsfeatures aktiviert sind.

Wenn Sie ein eigenes Authentifizierungssystem verwenden, muss der Pfad der Integritätsüberprüfung anonymen Zugriff zulassen. Um den Endpunkt der Integritätsprüfung zu sichern, sollten Sie zunächst Features wie [IP-Einschränkungen](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [Clientzertifikate](app-service-ip-restrictions.md#set-an-ip-address-based-rule) oder ein virtuelles Netzwerk verwenden, um den Anwendungszugriff einzuschränken. Sie können den Endpunkt der Integritätsprüfung sichern, indem Sie anfordern, dass der `User-Agent` der eingehenden Anforderung `HealthCheck/1.0` entspricht. Der Benutzer-Agent kann nicht manipuliert werden, da die Anforderung bereits durch die vorherigen Sicherheitsfeatures gesichert wäre.

## <a name="monitoring"></a>Überwachung

Nachdem Sie den Pfad der Integritätsüberprüfung der Anwendung angegeben haben, können Sie die Integrität Ihres Standorts mithilfe von Azure Monitor überwachen. Klicken Sie im Portal auf dem Blatt **Integrität überprüfen** auf der oberen Symbolleiste auf **Metriken**. Daraufhin wird ein neues Blatt geöffnet, auf dem Sie den Verlauf des Integritätsstatus der Site anzeigen und eine neue Warnungsregel erstellen können. Weitere Informationen zum Überwachen Ihrer Standorte finden Sie im [Handbuch zu Azure Monitor](web-sites-monitor.md).

## <a name="limitations"></a>Einschränkungen

- Die Integritätsprüfung sollte auf Premium Functions-Websites nicht aktiviert werden. Aufgrund der schnellen Skalierung von Premium Functions können Anforderungen zur Integritätsprüfung unnötige Schwankungen im HTTP-Datenverkehr verursachen. Premium Functions verfügt über eigene interne Integritätstests, die für Skalierungsentscheidungen verwendet werden.
- Die Integritätsprüfung kann für **Free** (kostenlos) und **Shared** (freigegeben) App Service-Pläne aktiviert werden, sodass Sie über Metriken zur Integrität des Standorts verfügen und Warnungen einrichten können. Da **Free** und **Shared** Standorte aber nicht aufskaliert werden können, werden keine fehlerhaften Instanzen ersetzt. Sie sollten auf den **Basic**-Tarif oder höher hochskalieren, damit Sie auf zwei oder mehr Instanzen aufskalieren und den vollen Vorteil der Integritätsprüfung nutzen können. Dies wird für Produktionsanwendungen empfohlen, da dies die Verfügbarkeit und Leistung Ihrer App erhöht.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-happens-if-my-app-is-running-on-a-single-instance"></a>Was geschieht, wenn meine App auf einer einzelnen Instanz ausgeführt wird?

Wenn Ihre App nur auf eine Instanz skaliert ist und fehlerhaft wird, wird sie nicht aus dem Lastenausgleich entfernt, da dadurch Ihre Anwendung vollständig ausfallen würde. Skalieren Sie auf zwei oder mehr Instanzen auf, um den Umleitungsvorteil der Integritätsprüfung zu erhalten. Wenn Ihre App auf einer einzelnen Instanz ausgeführt wird, können Sie weiterhin das [Überwachungsfeature](#monitoring) der Integritätsprüfung verwenden, um die Integrität Ihrer Anwendung nachzuverfolgen.
 
### <a name="why-are-the-health-check-request-not-showing-in-my-frontend-logs"></a>Warum werden die Integritätsprüfungsanforderungen nicht in meinen Front-End-Protokollen angezeigt?

Die Integritätsprüfungsanforderungen werden intern an Ihren Standort gesendet, sodass die Anforderung in [den Front-End-Protokollen](troubleshoot-diagnostic-logs.md#enable-web-server-logging) nicht angezeigt wird. Dies bedeutet außerdem, dass die Anforderung den Ursprung `127.0.0.1` hat, da sie intern gesendet wird. Sie können Ihrem Integritätsprüfungscode Protokollanweisungen hinzufügen, um Protokolle darüber zu führen, wenn ihr Integritätsprüfungspfad gepingt wird.

### <a name="are-the-health-check-requests-sent-over-http-or-https"></a>Werden die Integritätsprüfungsanforderungen über HTTP oder HTTPS gesendet?

Bei Windows App Service werden die Health Check-Anfragen über HTTPS gesendet, wenn [Nur HTTPS](configure-ssl-bindings.md#enforce-https) auf der Site aktiviert ist. Andernfalls werden sie über HTTP gesendet. Unter Linux App Service werden die Health Check-Anfragen nur über HTTP gesendet und können derzeit nicht über HTTP **S** gesendet werden.

### <a name="what-if-i-have-multiple-apps-on-the-same-app-service-plan"></a>Was geschieht, wenn ich mehrere Apps im selben App Service-Plan habe?

Fehlerhafte Instanzen werden immer aus der Lastenausgleichsrotation entfernt, unabhängig von anderen Apps im App Service-Plan (bis zu dem in [`WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT`](#configuration) angegebenen Prozentsatz). Wenn eine App auf einer Instanz länger als eine Stunde fehlerhaft bleibt, wird die Instanz nur ersetzt, wenn alle anderen Apps mit aktivierter Integritätsprüfung ebenfalls fehlerhaft sind. Apps, für die die Integritätsprüfung nicht aktiviert ist, werden nicht berücksichtigt. 

#### <a name="example"></a>Beispiel 

Angenommen, Sie verfügen über zwei Anwendungen (oder eine App mit einem Slot) mit aktivierter Integritätsprüfung namens „App A“ und „App B“. Sie befinden sich im selben App Service-Plan, und der Plan ist auf vier Instanzen aufskaliert. Wenn App A auf zwei Instanzen fehlerhaft wird, hört der Lastenausgleich auf diesen beiden Instanzen auf, Anforderungen an App A zu senden. Anforderungen werden weiterhin an App B auf diesen Instanzen weitergeleitet, vorausgesetzt, App B ist fehlerfrei. Wenn App A auf diesen beiden Instanzen länger als eine Stunde fehlerhaft bleibt, werden diese Instanzen nur ersetzt, wenn App B auf diesen Instanzen **ebenfalls** fehlerhaft ist. Wenn App B fehlerfrei ist, wird die Instanz nicht ersetzt.

![Visuelles Diagramm, in dem das obige Beispielszenario veranschaulicht wird.][2]

> [!NOTE]
> Wenn ein anderer Standort oder Slot im Plan (Standort C) ohne aktivierte Integritätsprüfung vorhanden wäre, würde dieser beim Ersetzen von Instanzen nicht berücksichtigt.

### <a name="what-if-all-my-instances-are-unhealthy"></a>Was ist, wenn alle meine Instanzen fehlerhaft sind?

In dem Szenario, in dem alle Instanzen Ihrer Anwendung fehlerhaft sind, entfernt App Service Instanzen aus dem Lastenausgleich bis zu dem in `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` angegebenen Prozentsatz. In diesem Szenario würde das Entfernen aller fehlerhaften App-Instanzen aus der Lastenausgleichsrotation effektiv zu einem Ausfall Ihrer Anwendung führen.

### <a name="does-health-check-work-on-app-service-environments"></a>Funktioniert die Integritätsprüfung in App Service-Umgebungen?

Ja, in App Service-Umgebungen (ASEs) pingt die Plattform Ihre Instanzen auf dem angegebenen Pfad und entfernt alle fehlerhaften Instanzen aus dem Lastenausgleich, sodass Anforderungen nicht an sie umgeleitet werden. Derzeit werden diese fehlerhaften Instanzen jedoch nicht durch neue Instanzen ersetzt, wenn sie 1 Stunde lang fehlerhaft bleiben.

## <a name="next-steps"></a>Nächste Schritte
- [Erstellen einer Aktivitätsprotokollwarnung, um alle Vorgänge der Engine für die automatische Skalierung für Ihr Abonnement zu überwachen](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-alert)
- [Erstellen Sie eine Aktivitätsprotokollwarnung, um alle Autoskalierungs-Vorgänge zum horizontalen Herunterskalieren und horizontalen Hochskalieren in Ihrem Abonnement, bei denen Fehler aufgetreten sind, zu überwachen.](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/monitor-autoscale-failed-alert)
- [Referenz zu Umgebungsvariablen und App-Einstellungen](reference-app-settings.md)

[1]: ./media/app-service-monitor-instances-health-check/health-check-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-multi-app-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
