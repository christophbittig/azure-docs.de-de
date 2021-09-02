---
title: Application Insights-Verfügbarkeitstests
description: Richten Sie wiederkehrende Webtests ein, um die Verfügbarkeit und Reaktionsfähigkeit Ihrer App oder Website zu überwachen.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2e87d9eca75cfd507549213999882dfe6ff3ffa6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346546"
---
# <a name="application-insights-availability-tests"></a>Application Insights-Verfügbarkeitstests

Nachdem Sie die Web-App oder Website bereitgestellt haben, können Sie regelmäßige Tests einrichten, um die Verfügbarkeit und Reaktionszeit zu überwachen. [Application Insights](./app-insights-overview.md) sendet regelmäßig Webanforderungen von verschiedenen Punkten auf der ganzen Welt an Ihre Anwendung. Außerdem kann dieser Dienst Sie warnen, wenn Ihre Anwendung nicht oder zu langsam reagieren sollte.

Sie können für jeden HTTP- oder HTTPS-Endpunkt, der über das öffentliche Internet zugänglich ist, Verfügbarkeitstests einrichten. Sie müssen keinerlei Änderungen an der Website vornehmen, die Sie testen. Sie müssen nicht einmal der Eigentümer der Website sein. Sie können die Verfügbarkeit einer REST-API testen, von der Ihr Dienst abhängig ist.

## <a name="types-of-tests"></a>Testtypen

Es gibt vier Arten von Verfügbarkeitstests:

* [URL-Pingtest (klassisch)](monitor-web-app-availability.md): Sie können diesen einfachen Test über das Portal erstellen, um zu überprüfen, ob ein Endpunkt antwortet, und die mit dieser Antwort verbundene Leistung zu messen. Sie können außerdem benutzerdefinierte Erfolgskriterien festlegen, die an erweiterte Features wie das Analysieren von abhängigen Anforderungen und das Zulassen von Wiederholungsversuchen gekoppelt sind.
* [Standardtest (Vorschau)](availability-standard-tests.md): Dieser Einzelanforderungstest ähnelt dem URL-Pingtest. Er umfasst die Gültigkeit des SSL-Zertifikats, die proaktive Überprüfung der Lebensdauer, das HTTP-Anforderungsverb (z. B. `GET`, `HEAD` oder `POST`), benutzerdefinierte Header und benutzerdefinierte Daten, die Ihrer HTTP-Anforderung zugeordnet sind.
* [Mehrstufiger Webtest (klassisch)](availability-multistep.md): Sie können diese Aufzeichnung einer Sequenz von Webanforderungen wiedergeben, um komplexere Szenarien zu testen. Mehrstufige Webtests werden in Visual Studio Enterprise erstellt und ins Portal hochgeladen, wo Sie sie ausführen können.
* [Benutzerdefinierter TrackAvailability-Test](availability-azure-functions.md): Wenn Sie eine benutzerdefinierte Anwendung zum Ausführen von Verfügbarkeitstests erstellen möchten, können Sie die [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)-Methode verwenden, um die Ergebnisse an Application Insights zu senden.

> [!IMPORTANT]
> Sowohl der [URL-Pingtest](monitor-web-app-availability.md) als auch der [mehrstufige Webtest](availability-multistep.md) nutzen die DNS-Infrastruktur des öffentlichen Internets, um die Domänennamen der getesteten Endpunkte aufzulösen. Wenn Sie privates DNS verwenden, müssen Sie sicherstellen, dass die öffentlichen Domänennamenserver jeden Domänennamen Ihres Tests entfernen können. Wenn dies nicht möglich ist, können Sie stattdessen [benutzerdefinierte TrackAvailability-Tests](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)´verwenden.

Sie können bis zu 100 Verfügbarkeitstests pro Application Insights-Ressource erstellen.

## <a name="troubleshooting"></a>Problembehandlung

Informationen finden Sie in dem dedizierten [Artikel zur Problembehandlung](troubleshoot-availability.md).

## <a name="next-steps"></a>Nächste Schritte

* [Verfügbarkeitswarnungen](availability-alerts.md)
* [Multi-step web tests (Mehrstufige Webtests)](availability-multistep.md)
* [URL-Tests](monitor-web-app-availability.md)
* [Erstellen und Ausführen von benutzerdefinierten Verfügbarkeitstests mit Azure Functions](availability-azure-functions.md)
* [Webtests: Azure Resource Manager-Vorlage](/azure/templates/microsoft.insights/webtests?tabs=json)