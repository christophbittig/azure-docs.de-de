---
title: Private Verfügbarkeitstests – Azure Monitor Application Insights
description: Erfahren Sie, wie Sie private Verfügbarkeitstests auf internen Servern verwenden, die hinter einer Firewall ausgeführt werden.
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 713a57db66042c562e212d65c6dd265c2aab47cd
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858441"
---
# <a name="private-testing"></a>Private Tests

Wenn Sie Verfügbarkeitstests auf internen Servern verwenden möchten, die hinter einer Firewall ausgeführt werden, gibt es zwei mögliche Lösungen: Aktivierung öffentlicher Pingtests und getrenntes Szenario/Szenario ohne Zugriff.

## <a name="public-ping-test-enablement"></a>Aktivierung öffentlicher Pingtests

> [!NOTE]
> Wenn Sie keinen Zugriff auf Ihre Umgebung zulassen möchten, verwenden Sie die Methode im Abschnitt [Getrenntes Szenario oder Szenario ohne Zugriff](#disconnected-or-no-ingress-scenarios).

 Stellen Sie sicher, dass Sie über einen öffentlichen DNS-Eintrag für Ihre interne Website verfügen. Der Test ist nicht möglich, wenn das DNS nicht aufgelöst werden kann. [Erstellen Sie einen benutzerdefinierten Domänennamen für die interne Anwendung.](../../cloud-services/cloud-services-custom-domain-name-portal.md#add-an-a-record-for-your-custom-domain)

Konfigurieren Sie die Firewall so, dass eingehende Anforderungen von unserem Dienst zugelassen werden.

- [Diensttags](../../virtual-network/service-tags-overview.md) sind eine einfache Möglichkeit, Azure-Dienste zu aktivieren, ohne einzelne IP-Adressen autorisieren oder eine aktuelle Liste verwalten zu müssen. Diensttags können übergreifend für die Azure Firewall und Netzwerksicherheitsgruppen verwendet werden, um den Dienstzugriff zu ermöglichen. **ApplicationInsightsAvailability** ist das Diensttag für des Pingtestdienst.
    1. Wenn Sie [Azure-Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md) verwenden, wechseln Sie zu Ihrer Ressource für Netzwerksicherheitsgruppen, und wählen Sie unter *Einstellungen* die Option **Eingangssicherheitsregeln** und dann **Hinzufügen** aus.

         :::image type="content" source="media/availability-private-test/add.png" alt-text="Screenshot der Registerkarte „Eingangssicherheitsregeln“ der Ressource für Netzwerksicherheitsgruppen":::

    1. Wählen Sie dann *Diensttag* als Quelle und *ApplicationInsightsAvailability* als Quelldiensttag aus. Verwenden Sie die offenen Ports 80 (HTTP) und 443 (HTTPS) für eingehenden Datenverkehr vom Diensttag.

        :::image type="content" source="media/availability-private-test/service-tag.png" alt-text="Screenshot der Registerkarte „Eingangssicherheitsregel hinzufügen“ mit einem Diensttag als Quelle":::

- Wenn Ihre Endpunkte außerhalb von Azure gehostet werden oder Diensttags für Ihr Szenario nicht verfügbar sind, müssen Sie die [IP-Adressen unserer Webtest-Agents](ip-addresses.md) einzeln zur Positivliste hinzufügen. Sie können die IP-Adressbereiche direkt über PowerShell, die Azure CLI oder einen REST-Aufruf mithilfe der [Diensttag-API](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) abfragen. Sie können auch eine [JSON-Datei](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) herunterladen, um eine Liste der aktuellen Diensttags mit IP-Adressendetails zu erhalten.
    1. Wählen Sie in Ihrer Ressource für Netzwerksicherheitsgruppen unter *Einstellungen* die Option **Eingangssicherheitsregeln** und dann **Hinzufügen** aus.
    1. Wählen Sie dann *IP-Adressen* als Quelle aus, und fügen Sie anschließend Ihre IP-Adressen in einer durch Kommas getrennten Liste unter „Quell-IP-Adress-/CIRD-Bereiche“ hinzu.

         :::image type="content" source="media/availability-private-test/ip-addresses.png" alt-text="Screenshot der Registerkarte „Eingangssicherheitsregel hinzufügen“ mit IP-Adressen als Quelle":::

## <a name="disconnected-or-no-ingress-scenarios"></a>Getrenntes Szenario oder Szenario ohne Zugriff

Ihr Testserver muss Zugriff in ausgehender Richtung auf den Application Insights-Erfassungsendpunkt haben. Dies ist ein deutlich geringeres Sicherheitsrisiko als bei der Alternativlösung, bei der eingehende Anforderungen zugelassen werden. Die Ergebnisse werden auf der Registerkarte „Verfügbarkeitswebtests“ mit einer vereinfachten Benutzeroberfläche angezeigt, die für Tests verfügbar ist, die über das Azure-Portal erstellt wurden. Benutzerdefinierte Verfügbarkeitstests werden auch als Verfügbarkeitsergebnisse in Analysen, Suchen und Metriken angezeigt.

1. Verbinden Sie Ihre Application Insights-Ressource und getrennte Umgebung mithilfe von [Azure Private Link](../logs/private-link-security.md).
1. Schreiben Sie benutzerdefinierten Code zum regelmäßigen Testen Ihres internen Servers oder der Endpunkte. Sie können den Code mit [Azure Functions](availability-azure-functions.md) oder einem Hintergrundprozess auf einem Testserver hinter Ihrer Firewall ausführen. Die Ergebnisse des Testvorgangs können an Application Insights gesendet werden, indem die `TrackAvailability()`-API im Core SDK-Paket verwendet wird.

## <a name="troubleshooting"></a>Problembehandlung

[Artikel zur Problembehandlung](troubleshoot-availability.md) (Dedicated)

## <a name="next-steps"></a>Nächste Schritte

* [Azure Private Link](../logs/private-link-security.md)
* [Availability Alerts (Verfügbarkeitswarnungen)](availability-alerts.md)
* [URL-Tests](monitor-web-app-availability.md)
* [Erstellen und Ausführen von benutzerdefinierten Verfügbarkeitstests mit Azure Functions](availability-azure-functions.md)
