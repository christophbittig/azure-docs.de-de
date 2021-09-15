---
title: 'Azure Application Gateway: Konfiguration der HTTP-Einstellungen'
description: In diesem Artikel wird das Konfigurieren der HTTP-Einstellungen von Azure Application Gateway beschrieben.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: f2fb9d2f6221928f093895914b8fc0082573a8b2
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866265"
---
# <a name="application-gateway-http-settings-configuration"></a>Application Gateway: Konfiguration der HTTP-Einstellungen

Das Application Gateway leitet Datenverkehr mithilfe der Konfiguration, die Sie hier festlegen, an die Back-End-Server weiter. Nachdem Sie eine HTTP-Einstellung erstellt haben, müssen Sie sie einer oder mehreren Regeln für das Routing von Anforderungen zuordnen.

## <a name="cookie-based-affinity"></a>Cookiebasierte Affinität

Azure Application Gateway verwendet vom Gateway verwaltete Cookies zum Beibehalten von Benutzersitzungen. Wenn ein Benutzer die erste Anforderung an Application Gateway sendet, setzt dieses in der Antwort ein Affinitätscookie mit einem Hashwert, der die Sitzungsdetails enthält, sodass die nachfolgenden Anfragen, die das Affinitätscookie enthalten, zum selben Back-End-Server geleitet werden, um die Bindung aufrechtzuerhalten.

Diese Funktion ist hilfreich, wenn eine Benutzersitzung auf demselben Server bleiben soll und der Sitzungszustand lokal auf dem Server für eine Benutzersitzung gespeichert wird. Wenn die Anwendung cookiebasierte Affinität nicht verarbeiten kann, können Sie diese Funktion nicht verwenden. Um sie zu verwenden, stellen Sie sicher, dass die Clients Cookies unterstützen.
> [!NOTE]
> Bei einigen Überprüfungen auf Sicherheitsrisiken wird möglicherweise das Application Gateway-Affinitätscookie gekennzeichnet, weil die Flags „Secure“ oder „HttpOnly“ nicht festgelegt sind. Bei diesen Überprüfungen wird nicht berücksichtigt, dass die Daten im Cookie mit einem unidirektionalen Hash generiert werden. Das Cookie enthält keine Benutzerinformationen und wird ausschließlich für das Routing verwendet. 


Das [v80-Update](https://chromiumdash.appspot.com/schedule) des [Chromium-Browsers](https://www.chromium.org/Home) enthielt ein Mandat, bei dem HTTP-Cookies ohne [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7)-Attribut als „SameSite=Lax“ behandelt werden müssen. Im Falle von CORS-Anforderungen (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen), wenn das Cookie in einem Drittanbieterkontext gesendet werden muss, muss es *SameSite=None; Secure*-Attribute verwenden und sollte nur über HTTPS gesendet werden. Andernfalls sendet der Browser in einem reinen HTTTP-Szenario die Cookies nicht im Drittanbieterkontext. Das Ziel dieses Updates von Chrome besteht darin, die Sicherheit zu erhöhen und CSRF-Angriffe (Cross-Site Request Forgery, siteübergreifende Anforderungsfälschung) zu vermeiden. 

Zur Unterstützung dieser Änderung fügt Application Gateway (alle SKU-Typen) ab dem 17. Februar 2020 ein weiteres Cookie namens *ApplicationGatewayAffinityCORS* zusätzlich zum vorhandenen Cookie *ApplicationGatewayAffinity* hinzu. Dem *ApplicationGatewayAffinityCORS*-Cookie sind zwei weitere Attribute hinzugefügt ( *"SameSite = None; Secure"* ), damit persistente Sitzungen selbst für ursprungsübergreifende Anforderungen erhalten bleiben.

Beachten Sie, dass der Standardname des Affinitätscookies *ApplicationGatewayAffinity* lautet, was Sie aber ändern können. Für den Fall, dass Sie einen benutzerdefinierten Affinitätscookienamen verwenden, wird ein zusätzliches Cookie mit CORS als Suffix hinzugefügt. Beispielsweise *CustomCookieNameCORS*.

> [!NOTE]
> Wenn das Attribut *SameSite=None* festgelegt ist, ist es obligatorisch, dass das Cookie auch das Flag *Secure* enthält und über HTTPS gesendet werden muss.  Wenn Sitzungsaffinität über CORS erforderlich ist, müssen Sie Ihren Workload zu HTTPS migrieren. Weitere Informationen zur TLS-Abladung und End-to-End-TLS-Dokumentation für Application Gateway finden Sie unter: [Übersicht](ssl-overview.md), [Konfigurieren einer Application Gateway-Instanz mit TLS-Beendigung mithilfe des Azure-Portals](create-ssl-portal.md) und [Konfigurieren der End-to-End-TLS-Verschlüsselung mithilfe von Application Gateway und dem Portal](end-to-end-ssl-portal.md).

## <a name="connection-draining"></a>Verbindungsausgleich

Mit dem Verbindungsausgleich können Sie Elemente des Back-End-Pools bei geplanten Dienstupdates korrekt entfernen. Sie können diese Einstellung auf alle Elemente eines Back-End-Pools anwenden, indem Sie den Verbindungsausgleich für die HTTP-Einstellung aktivieren. Dadurch wird sichergestellt, dass alle Instanzen, deren Registrierung aufgehoben wird, bestehende Verbindungen aufrechterhalten, die Verarbeitung laufender Anforderungen für einen konfigurierbaren Zeitraum fortsetzen und keine neuen Anforderungen oder Verbindungen empfangen. Die einzige Ausnahme hierbei sind Anforderungen zur Aufhebung der Registrierung von Instanzen aufgrund der durch das Gateway verwalteten Sitzungsaffinität. Diese Anforderungen werden weiterhin an die Instanzen weitergeleitet, deren Registrierung aufgehoben wird. Verbindungsausgleich gilt für Back-End-Instanzen, die explizit aus dem Back-End-Pool entfernt werden.

## <a name="protocol"></a>Protocol

Application Gateway unterstützt sowohl HTTP als auch HTTPS für das Routing von Anforderungen an die Back-End-Server. Bei Auswahl von HTTP ist Datenverkehr an die Back-End-Server unverschlüsselt. Wenn unverschlüsselte Kommunikation nicht akzeptabel ist, wählen Sie HTTPS.

Diese Einstellung unterstützt zusammen mit HTTPS im Listener [End-to-End-TLS](ssl-overview.md). Damit können Sie vertrauliche Daten sicher verschlüsselt an das Back-End übertragen. Jeder Back-End-Server im Back-End-Pool mit aktiviertem End-to-End-TLS muss mit einem Zertifikat konfiguriert sein, um die sichere Kommunikation zu erlauben.

## <a name="port"></a>Port

Diese Einstellung gibt den Port an, auf dem die Back-End-Server auf Datenverkehr vom Application Gateway lauschen. Sie können Ports im Bereich von 1 bis 65535 konfigurieren.

## <a name="request-timeout"></a>Anforderungszeitlimit

Diese Einstellung ist die Anzahl der Sekunden, die das Application Gateway auf eine Antwort vom Back-End-Server wartet.

## <a name="override-back-end-path"></a>Back-End-Pfad außer Kraft setzen

Mit dieser Einstellung können Sie einen optionalen benutzerdefinierten Weiterleitungspfad konfigurieren, der für die Weiterleitung der Anforderung an das Back-End verwendet werden soll. Ein beliebiger Teil des Eingangspfads, der mit dem benutzerdefinierten Pfad im Feld **Back-End-Pfad außer Kraft setzen** übereinstimmt, wird in den weitergeleiteten Pfad kopiert. Die folgende Tabelle zeigt die Funktionsweise dieses Features:

- Wenn die HTTP-Einstellung einer einfachen Routingregel für Anforderungen zugeordnet ist:

  | Ursprüngliche Anforderung  | Back-End-Pfad außer Kraft setzen | An das Back-End weitergeleitete Anforderung |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Wenn die HTTP-Einstellung einer pfadbasierten Routingregel für Anforderungen zugeordnet ist:

  | Ursprüngliche Anforderung           | Pfadregel       | Back-End-Pfad außer Kraft setzen | An das Back-End weitergeleitete Anforderung |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

## <a name="use-for-app-service"></a>Für App Service verwenden

Dies ist eine reine Benutzeroberflächenverknüpfung, die die zwei erforderlichen Einstellungen für das Azure App Service-Back-End auswählt. Sie aktiviert **Hostnamen aus Back-End-Adresse auswählen** und erstellt einen neuen benutzerdefinierten Test, wenn Sie nicht bereits einen besitzen. (Weitere Informationen finden Sie im Abschnitt [Hostnamen aus Back-End-Adresse auswählen](#pick-host-name-from-back-end-address) dieses Artikels.) Es wird ein neuer Test erstellt, dessen Header aus der Adresse des Back-End-Elements ausgewählt wird.

## <a name="use-custom-probe"></a>Benutzerdefinierten Test verwenden

Diese Einstellung ordnet einen [benutzerdefinierten Test](application-gateway-probe-overview.md#custom-health-probe) einer HTTP-Einstellung zu. Einer HTTP-Einstellung kann nur ein benutzerdefinierter Test zugeordnet werden. Wenn Sie nicht explizit einen benutzerdefinierten Test zuordnen, wird der [Standardtest](application-gateway-probe-overview.md#default-health-probe-settings) zur Überwachung der Integrität des Back-Ends verwendet. Sie sollten einen benutzerdefinierten Test erstellen, um größere Kontrolle über die Integritätsüberwachung Ihrer Back-Ends zu haben.

> [!NOTE]
> Der benutzerdefinierte Test überwacht die Integrität des Back-End-Pools nicht, sofern die entsprechende HTTP-Einstellung nicht explizit einem Listener zugeordnet ist.

## <a name="pick-host-name-from-back-end-address"></a>Auswählen eines Hostnamens aus der Back-End-Adresse

Diese Funktion legt den *Host*-Header in der Anforderung dynamisch auf den Hostnamen des Back-End-Pools fest. Sie verwendet dazu eine IP-Adresse oder einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN ).

Dieses Feature ist nützlich, wenn der Domänenname des Back-Ends vom DNS-Namen des Application Gateways abweicht und sich das Back-End für die Auflösung zum richtigen Endpunkt auf einen bestimmten Hostheader stützt.

Ein Beispielfall sind mehrinstanzenfähige Dienste auf dem Back-End. Ein App Service ist ein mehrinstanzenfähiger Dienst, der einen gemeinsam genutzten Speicherplatz mit einer einzigen IP-Adresse verwendet. Daher kann der Zugriff auf einen App Service nur über die Hostnamen erfolgen, die in den Einstellungen der benutzerdefinierten Domäne konfiguriert werden.

Standardmäßig ist der benutzerdefinierte Domänenname *example.azurewebsites.net*. Überschreiben Sie den Hostnamen in der ursprünglichen Anforderung an den Hostnamen des App-Diensts, um mithilfe einer Application Gateway-Instanz über einen Hostnamen auf Ihren App-Dienst zuzugreifen, der nicht explizit im App-Dienst oder dem vollqualifizierten Domänennamen der Application Gateway-Instanz registriert ist. Aktivieren Sie zu diesem Zweck die Einstellung **Hostnamen aus Back-End-Adresse auswählen**.

Für eine benutzerdefinierte Domäne, deren bestehender benutzerdefinierter DNS-Name dem App Service zugeordnet ist, müssen Sie diese Einstellung nicht aktivieren.

> [!NOTE]
> Diese Einstellung ist für die App Service-Umgebung nicht erforderlich, da es sich dabei um eine dedizierte Bereitstellung handelt.

## <a name="host-name-override"></a>Hostnamen außer Kraft setzen

Diese Funktion ersetzt den *Host*-Header in der beim Application Gateway eingehenden Anforderung durch den Hostnamen, den Sie angeben.

Wenn beispielsweise *www.contoso.com* in der Einstellung **Hostname** angegeben wird, wird die ursprüngliche Anforderung *`https://appgw.eastus.cloudapp.azure.com/path1` beim Weiterleiten der Anforderung an den Back-End-Server in *`https://www.contoso.com/path1` geändert.

## <a name="next-steps"></a>Nächste Schritte

- [Informationen zum Back-End-Pool](configuration-overview.md#back-end-pool).
