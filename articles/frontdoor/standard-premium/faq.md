---
title: 'Azure Front Door: Häufig gestellte Fragen'
description: Diese Seite enthält Antworten auf häufig gestellte Fragen zu Azure Front Door Standard/Premium.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/18/2021
ms.author: duau
ms.openlocfilehash: 2be4e0b08ca31cabbd0ffbadf37760d289da7eb9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331260"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Häufig gestellte Fragen zu Azure Front Door Standard/Premium (Vorschauversion)

In diesem Artikel werden häufige Fragen zu den Features und der Funktionalität von Azure Front Door beantwortet.

## <a name="general"></a>Allgemein

### <a name="what-is-azure-front-door"></a>Was ist Azure Front Door?

Bei Azure Front Door handelt es sich um ein schnelles, zuverlässiges und sicheres CDN in der modernen Cloud, das über einen intelligenten Schutz vor Bedrohungen verfügt. Der Dienst ermöglicht die Beschleunigung statischer und dynamischer Inhalte, globalen Lastenausgleich und erweiterte Sicherheitsmaßnahmen für Ihre globalen Anwendungen mit Hyperskalierung, APIs, Websites und Clouddienste mit intelligentem Schutz vor Bedrohungen.

### <a name="what-features-does-azure-front-door-support"></a>Welche Features werden von Azure Front Door unterstützt?

Azure Front Door unterstützt Folgendes:

* Beschleunigung von statischen Inhalten und dynamischen Anwendungen
* TLS/SSL-Abladung und End-to-End-TLS
* Web Application Firewall
* Cookiebasierte Sitzungsaffinität
* Routing auf Basis von URL-Pfaden
* Kostenlose Zertifikate und Verwaltung von mehreren Domänen

Eine vollständige Liste der unterstützten Features finden Sie unter [Übersicht über Azure Front Door](overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Was ist der Unterschied zwischen Azure Front Door und Azure Application Gateway?

Es wird zwar sowohl für Front Door als auch für Application Gateway ein Layer 7-Lastenausgleich (HTTP/HTTPS) durchgeführt, aber der Hauptunterschied besteht darin, dass Front Door ein globaler Dienst ist. Application Gateway ist dagegen ein regionaler Dienst. Während Front Door den Lastenausgleich zwischen den verschiedenen Skalierungseinheiten, Clustern und Stempeleinheiten über Regionen hinweg ermöglicht, gestattet Application Gateway den Lastenausgleich zwischen VMs/Containern, die sich innerhalb der Skalierungseinheit befinden.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Wann sollte ein Application Gateway hinter Front Door bereitgestellt werden?

Wichtige Szenarien, in denen Application Gateway hinter Front Door verwendet werden sollte:

* Front Door kann einen pfadbasierten Lastenausgleich nur auf globaler Ebene durchführen, aber wenn der Datenverkehr im virtuellen Netzwerk (VNET) noch weiter ausgeglichen werden soll, ist die Nutzung von Application Gateway ratsam.
* Da Front Door auf VM-/Containerebene nicht funktioniert, kann kein Verbindungsausgleich durchgeführt werden. Mit Application Gateway können Sie jedoch den Verbindungsausgleich durchführen. 
* Mit einem Application Gateway hinter Azure Front Door Service können Sie eine TLS/SSL-Abladung von 100 % erreichen und nur HTTP-Anforderungen innerhalb ihres virtuellen Netzwerks (VNET) weiterleiten.
* Sowohl Front Door als auch Application Gateway unterstützen die Sitzungsaffinität. Front Door kann den anfallenden Datenverkehr aus einer Benutzersitzung an denselben Cluster oder ein Back-End in einer bestimmten Region weiterleiten. Mit Application Gateway kann der Datenverkehr direkt demselben Server im Cluster zugeordnet werden.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Kann Azure Load Balancer hinter Front Door bereitgestellt werden?

Azure Front Door benötigt einen öffentlichen VIP oder einen öffentlich verfügbaren DNS-Namen, um den Datenverkehr zu weiterzuleiten. Die Bereitstellung von Azure Load Balancer hinter Front Door ist ein häufiger Anwendungsfall.

### <a name="what-protocols-does-azure-front-door-support"></a>Welche Protokolle werden von Azure Front Door unterstützt?

Azure Front Door unterstützt HTTP, HTTPS und HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Wie unterstützt Azure Front Door HTTP/2?

Die Unterstützung des HTTP/2-Protokolls ist nur für Clients verfügbar, die mit Azure Front Door verbunden sind. Die Kommunikation mit den Back-Ends im Back-End-Pool erfolgt über HTTP/1.1. Die HTTP/2-Unterstützung ist standardmäßig aktiviert.

### <a name="what-resources-are-supported-today-as-part-of-an-origin-group"></a>Welche Ressourcen werden derzeit als Teil einer Ursprungsgruppe unterstützt?

Ursprungsgruppen können sich aus zwei Arten von Ursprüngen zusammensetzen:

- Öffentliche Ursprünge umfassen Speicherkonten, App Service-Apps, Kubernetes-Instanzen sowie jeden anderen benutzerdefinierten Hostnamen mit öffentlicher Konnektivität. Diese Ursprünge müssen entweder über eine öffentliche IP-Adresse oder einen öffentlich auflösbaren DNS-Hostnamen definiert sein. Mitglieder von Ursprungsgruppen können über Verfügbarkeitszonen und Regionen hinweg und sogar außerhalb von Azure bereitgestellt werden, sofern sie über öffentliche Konnektivität verfügen. Öffentliche Ursprünge werden für die Standard- und Premium-Tarife von Azure Front Door unterstützt.
- [Private Link-Ursprünge](concept-private-link.md) sind verfügbar, wenn Sie Azure Front Door (Premium) nutzen.

### <a name="what-regions-is-the-service-available-in"></a>In welchen Regionen ist der Dienst verfügbar?

Azure Front Door ist ein globaler und nicht an eine bestimmte Azure-Region gebundener Dienst. Der einzige Speicherort, den Sie beim Erstellen einer Front Door-Instanz angeben müssen, ist der Speicherort für die Ressourcengruppe. Mit diesem Speicherort wird im Wesentlichen angegeben, wo die Metadaten für die Ressourcengruppe gespeichert werden. Die Front Door-Ressource selbst wird als globale Ressource erstellt und die Konfiguration wird global für alle Edgestandorte bereitgestellt. 

### <a name="where-are-the-edge-locations-for-azure-front-door"></a>Welche Edgestandorte sind für Azure Front Door verfügbar?

Eine vollständige Liste der Azure Front Door Edgestandorte finden Sie unter [Azure Front Door Edgestandorte](edge-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Ist Azure Front Door eine dedizierte Bereitstellung für meine Anwendung, oder wird es zur gemeinsamen Nutzung für Kunden freigegeben?

Azure Front Door ist ein global verteilter, mehrinstanzenfähiger Dienst. Die Infrastruktur für Front Door wird von allen Kunden gemeinsam genutzt. Durch die Erstellung eines Front Door-Profils definieren Sie aber die spezifische Konfiguration, die für Ihre Anwendung erforderlich ist. Änderungen, die an Ihrer Front Door-Instanz vorgenommen werden, wirken sich nicht auf andere Front Door-Konfigurationen aus.

### <a name="is-http-https-redirection-supported"></a>Wird die Umleitung von HTTP zu HTTPS unterstützt?

Ja. Für Azure Front Door werden Umleitungen per Host, Pfad und Abfragezeichenfolge sowie teilweise URL-Umleitungen unterstützt. Weitere Informationen zur [URL-Umleitung](concept-rule-set-url-redirect-and-rewrite.md). 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Wie kann ich den Zugriff auf mein Back-End nur auf Azure Front Door beschränken?

Die beste Möglichkeit, Ihre Anwendung so zu sperren, dass Datenverkehr nur von Ihrer jeweiligen Front Door-Instanz akzeptiert wird, ist die Veröffentlichung Ihrer Anwendung über einen privaten Endpunkt. Der Netzwerkdatenverkehr zwischen Front Door und der Anwendung wird über das VNET und eine private Verbindung im Microsoft-Backbonenetzwerk geleitet, sodass die Daten nicht ins öffentliche Internet gelangen.

Lesen Sie die weiteren Informationen zum [Schützen des Ursprungs für Front Door mit Private Link](concept-private-link.md).  

Eine Alternative zum Sperren Ihrer Anwendung, damit Datenverkehr nur von Ihrer spezifischen Front Door-Instanz akzeptiert wird, besteht darin, IP-Zugriffssteuerungslisten für Ihr Back-End einzurichten. Beschränken Sie den Datenverkehr Ihres Back-Ends dann auf den spezifischen Wert des Headers „X-Azure-FDID“, der von Front Door gesendet wird. Diese Schritte werden wie folgt beschrieben:

* Konfigurieren Sie IP-ACLs für Ihre Back-Ends so, dass sie nur Datenverkehr aus dem Back-End-IP-Adressraum von Azure Front Door und den Infrastrukturdiensten von Azure akzeptieren. Lesen Sie sich die folgenden Informationen zum Einrichten einer Zugriffssteuerungsliste für Ihr Back-End durch:
 
    * Sehen Sie sich den Abschnitt *AzureFrontDoor.Backend* unter [IP-Bereiche und Diensttags in Azure](https://www.microsoft.com/download/details.aspx?id=56519) für den Back-End-IP-Adressbereich von Front Door an. Sie können auch das Diensttag *AzureFrontDoor.Backend* in Ihren [Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md#security-rules) verwenden.
    * [Grundlegende Infrastrukturdienste](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations) von Azure über virtualisierte IP-Hostadressen: `168.63.129.16` und `169.254.169.254`.

    > [!WARNING]
    > Der Back-End-IP-Adressbereich von Front Door kann später geändert werden, aber wir werden sicherstellen, dass zuvor eine Integration in [Azure-IP-Adressbereiche und Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) erfolgt ist. Es wird empfohlen, dass Sie [Azure-IP-Adressbereiche und Diensttags](https://www.microsoft.com/download/details.aspx?id=56519) für alle Änderungen oder Updates abonnieren.

* Führen Sie einen GET-Vorgang in Front Door mit der API-Version `2020-01-01` oder höher durch. Suchen Sie im API-Befehl nach dem `frontdoorID`-Feld. Filtern Sie nach dem von Front Door an Ihr Back-End gesendeten eingehenden Header „**X-Azure-FDID**“ mit dem Wert des Felds `frontdoorID`. Sie finden den `Front Door ID`-Wert auch im Abschnitt „Übersicht“ über die Front Door-Portalseite. 

* Wenden Sie Regelfilterung in Ihrem Back-End-Webserver an, um den Datenverkehr basierend auf dem resultierenden „X-Azure-FDID“-Headerwert einzuschränken.

  Hier folgt ein Beispiel für [Microsoft-Internetinformationsdienste (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

* Azure Front Door unterstützt auch das Diensttag *AzureFrontDoor.Frontend*, das die Liste der IP-Adressen angibt, die Clients beim Herstellen einer Verbindung mit Front Door verwenden. Sie können das Diensttag *AzureFrontDoor.Frontend* verwenden, wenn Sie den ausgehenden Datenverkehr steuern, der zugelassen werden sollte, um eine Verbindung mit den hinter Azure Front Door bereitgestellten Diensten herzustellen. Azure Front Door unterstützt zudem das zusätzliche Diensttag *AzureFrontDoor.FirstParty* zum internen Integrieren mit anderen Azure-Diensten. Weitere Informationen zu Anwendungsfällen für Azure Front Door-Diensttags finden Sie unter [Verfügbare Diensttags](../../virtual-network/service-tags-overview.md#available-service-tags).

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>Kann sich die Anycast-IP-Adresse während der Lebensdauer meiner Front Door-Instanz ändern?

Die Front-End-Anycast-IP-Adresse für Ihre Front Door-Instanz sollte sich in der Regel nicht ändern und kann während der Front Door-Lebensdauer statisch bleiben. Es gibt hierfür jedoch **keine Garantien**. Wir raten Ihnen, keine direkten Abhängigkeiten von der IP-Adresse herzustellen.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Unterstützt Azure Front Door statische oder dedizierte IP-Adressen?

Azure Front Door unterstützt derzeit keine statischen oder dedizierten Front-End-Anycast-IP-Adressen. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Unterstützt Azure Front Door X-Forwarded-For-Header?

Ja. Azure Front Door unterstützt die Header „X-Forwarded-For“, „X-Forwarded-Host“ und „X-Forwarded-Proto“. Wenn der Header für „X-Forwarded-For“ bereits vorhanden war, fügt Front Door die Client-Socket-IP-Adresse an diesen an. Andernfalls wird der Header mit der Client-Socket-IP-Adresse als Wert hinzugefügt. Für X-Forwarded-Host und X-Forwarded-Proto wird der Wert außer Kraft gesetzt.  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Wie lange dauert die Bereitstellung von Azure Front Door? Funktioniert meine Front Door-Instanz auch bei einem Update weiterhin?

Die meisten Konfigurationsupdates der Regel-Engine werden in weniger als 20 Minuten abgeschlossen. Sie können erwarten, dass die Regel wirksam wird, sobald das Update abgeschlossen ist. 

 > [!Note]  
  > Bei den meisten benutzerdefinierten TLS/SSL-Zertifikatupdates dauert die globale Bereitstellung einige Minuten bis hin zu einer Stunde.

Alle Aktualisierungen von Routen oder Back-End-Pools erfolgen nahtlos und verursachen keine Ausfallzeiten (wenn die neue Konfiguration korrekt ist). Durch Zertifikatupdates kommt es nicht zu Ausfällen, sofern Sie nicht von „Verwalteter Azure Front Door-Dienst“ zu Ihrem eigenen Zertifikat (oder umgekehrt) wechseln.


## <a name="configuration"></a>Konfiguration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Kann Azure Front Door für den Datenverkehr in einem virtuellen Netzwerk einen Lastenausgleich vornehmen oder den Datenverkehr weiterleiten?

Azure Front Door (Standard) erfordert eine öffentliche IP-Adresse oder einen öffentlich auflösbaren DNS-Namen, um Datenverkehr weiterzuleiten. Für Azure Front Door ist keine direkte Weiterleitung an Ressourcen in einem virtuellen Netzwerk möglich. Sie können eine Application Gateway- oder Azure Load Balancer-Instanz mit einer öffentlichen IP-Adresse verwenden, um dieses Problem zu beheben.

Azure Front Door (Premium) unterstützt das Weiterleiten von Datenverkehr an [Private Link-Ursprünge](concept-private-link.md).

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Welche Timeouts und Grenzwerte gelten für Azure Front Door?

Weitere Informationen zu allen dokumentierten [Timeouts und Grenzwerten für Azure Front Door](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Wie lange dauert es, bis eine Regel wirksam wird, nachdem sie zur Front Door-Regel-Engine hinzugefügt wurde?

Die meisten Konfigurationsupdates der Regel-Engine werden in weniger als 20 Minuten abgeschlossen. Sie können erwarten, dass die Regel wirksam wird, sobald das Update abgeschlossen ist. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Kann ich Azure CDN hinter meinem Front Door-Profil oder Front Door hinter meinem Azure CDN konfigurieren?

Azure Front Door und Azure CDN können nicht zusammen konfiguriert werden, da beide Dienste bei der Antwort auf Anforderungen die gleichen Azure Edge-Sites nutzen. 

## <a name="performance"></a>Leistung

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Wie unterstützt Azure Front Door die Hochverfügbarkeit und Skalierbarkeit?

Azure Front Door ist eine global verteilte mehrinstanzenfähige Plattform mit einer sehr großen Menge an Kapazität, um die Skalierungsanforderungen Ihrer Anwendung zu erfüllen. Front Door wird vom Rand des globalen Netzwerks von Microsoft bereitgestellt und bietet globale Funktionen für den Lastenausgleich, mit denen Sie für Ihre gesamte Anwendung oder sogar für einzelne Microservices über Regionen oder verschiedene Clouds hinweg einen Failover ausführen können.

## <a name="tls-configuration"></a>TLS-Konfiguration

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Welche TLS-Versionen werden vom Azure Front Door unterstützt?

Alle Front Door-Profile, die nach September 2019 erstellt wurden, verwenden standardmäßig TLS 1.2 als Mindestvoraussetzung.

Front Door unterstützt die TLS-Versionen 1.0, 1.1 und 1.2. TLS 1.3 wird noch nicht unterstützt. Ausführlichere Informationen finden Sie unter [End-to-End-TLS mit Azure Front Door](../concept-end-to-end-tls.md).

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Warum schlägt der HTTPS-Datenverkehr an das Back-End fehl?

Es kann zwei Gründe geben, warum der HTTPS-Datenverkehr für eine erfolgreiche HTTPS-Verbindung mit Ihrem Back-End – ob für Integritätstests oder Weiterleitungsanforderungen – fehlschlägt:

* **Zertifikatantragsteller-Namenskonflikt**: Bei HTTPS-Verbindungen erwartet Front Door, dass Ihr Back-End ein Zertifikat von einer gültigen Zertifizierungsstelle vorlegt, und dass die Antragstellernamen mit dem Back-End-Hostnamen übereinstimmen. Beispiel: Ihr Back-End-Hostname ist auf `myapp-centralus.contosonews.net` festgelegt, und das Zertifikat, das von Ihrem Back-End während des TLS-Handshakes angegeben wird, enthält weder `myapp-centralus.contosonews.net` noch `*myapp-centralus*.contosonews.net` im Antragstellernamen. In diesem Fall wird die Verbindungsherstellung von Front Door abgelehnt und ein Fehler angezeigt. 
    * **Lösung**: Aus Konformitätssicht ist dies zwar nicht zu empfehlen, aber Sie können diesen Fehler umgehen, indem Sie die Prüfung des Zertifikatantragsteller-Namens für Ihre Front Door-Instanz deaktivieren. Sie finden diese Option im Azure-Portal unter „Einstellungen“ und unter „BackendPoolsSettings“ in der API.
* **Back-End-Hostingzertifikat von einer ungültigen Zertifizierungsstelle**: In Front Door können nur Zertifikate von [gültigen Zertifizierungsstellen](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT) auf dem Back-End verwendet werden. Zertifikate von internen Zertifizierungsstellen oder selbstsignierte Zertifikate sind nicht zulässig.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Kann ich bei Azure Front Door die Clientauthentizierung/gegenseitige Authentifizierung verwenden?

Nein. Obwohl Azure Front Door TLS 1.2 unterstützt, bei dem die Clientauthentizierung/gegenseitige Authentifizierung in [RFC 5246](https://tools.ietf.org/html/rfc5246) eingeführt wurde, unterstützt Azure Front Door diese Authentifizierungsart zurzeit nicht.

## <a name="diagnostics-and-logging"></a>Diagnose und Protokollierung

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Welche Arten von Metriken und Protokollen sind mit Azure Front Door verfügbar?

Informationen zu Protokollen und anderen Diagnosefunktionen finden Sie unter Überwachen von Metriken und Protokollen für Front Door.

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Welche Aufbewahrungsrichtlinie gilt für die Diagnoseprotokolle?

Diagnoseprotokolle gelangen in die Speicherkonten von Kunden, und Kunden können die Aufbewahrungsrichtlinie je nach Präferenz festlegen. Diagnoseprotokolle können auch an einen Event Hub oder Azure Monitor-Protokolle gesendet werden. Weitere Informationen finden Sie unter [Azure Front Door Standard/Premium (Vorschauversion): Protokollierung](how-to-logs.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Wie erhalte ich Überwachungsprotokolle für Azure Front Door?

Überwachungsprotokolle sind für Azure Front Door verfügbar. Wählen Sie im Portal auf der Menüseite Ihrer Front Door-Instanz die Option **Aktivitätsprotokoll** aus, um auf das Überwachungsprotokoll zuzugreifen. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Kann ich mit Azure Front Door Warnungen festlegen?

Ja. Azure Front Door unterstützt Warnungen. Warnungen werden für Metriken konfiguriert. 

## <a name="billing"></a>Abrechnung

### <a name="will-i-be-billed-for-the-azure-front-door-resources-that-are-disabled"></a>Werden mir die Azure Front Door-Ressourcen in Rechnung gestellt, die deaktiviert sind?

Azure Front Door-Ressourcen, z. B. Front Door-Profile, werden nicht berechnet.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Erstellen einer Instanz von Front Door Standard/Premium](create-front-door-portal.md).
