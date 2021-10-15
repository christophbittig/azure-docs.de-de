---
title: Testumgebungen, Drosselung und Diensteinschränkungen
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie mehr über die Drosselung und Diensteinschränkungen, die beim Bereitstellen einer Azure Active Directory-Testumgebung sowie beim Testen einer in die Microsoft Identity Platform integrierten App zu berücksichtigen sind.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2021
ms.author: ryanwi
ms.reviewer: arcrowe
ms.openlocfilehash: 35f7694dbdf3d7c4f3065f19dc89bac7d7b1915e
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129368073"
---
# <a name="throttling-and-service-limits-to-consider-for-testing"></a>Bei Tests zu berücksichtigende Drosselungsgrenzwerte und Diensteinschränkungen
Als Entwickler möchten Sie Ihre Anwendung testen, bevor Sie sie für die Produktion freigeben. Beim Testen von Anwendungen, die durch die Microsoft Identity Platform geschützt sind, sollten Sie eine Azure AD-Umgebung (Azure Active Directory) und einen Azure AD-Mandanten zur Verwendung für Tests einrichten.  

Für Anwendungen, die in die Microsoft Identity Platform integriert werden, müssen Verzeichnisobjekte (z. B. App-Registrierungen, Dienstprinzipale, Gruppen und Benutzer) in einem Azure AD-Mandanten erstellt und verwaltet werden.  Alle Einstellungen im Produktionsmandanten, die sich auf das Verhalten Ihrer App auswirken, sollten im Testmandanten repliziert werden. Füllen Sie den Testmandanten mit den erforderlichen Richtlinien für bedingten Zugriff, Berechtigungszuweisung, Anspruchszuordnung, Tokengültigkeitsdauer und Tokenausstellung auf. Ihre Anwendung kann auch Azure-Ressourcen wie Compute oder Speicher verwenden, die der Testumgebung hinzugefügt werden müssen. Je nach zu testender App kann Ihre Testumgebung eine Vielzahl von Ressourcen erfordern.

Damit eine zuverlässige Nutzung von Diensten durch alle Kunden gewährleistet ist, beschränken Azure AD und andere Dienste die Anzahl der Ressourcen, die pro Kunde und pro Mandant erstellt werden können. Beim Einrichten einer Testumgebung und Bereitstellen von Verzeichnisobjekten und Azure-Ressourcen können einige dieser Diensteinschränkungen und -kontingente erreicht werden.

Azure AD, Microsoft Graph und andere Azure-Dienste beschränken auch die Anzahl gleichzeitiger Aufrufe eines Diensts oder die Computelast pro Kunde, um eine übermäßige Nutzung von Ressourcen zu verhindern. Dies wird als Drosselung bezeichnet und stellt sicher, dass Azure-Dienste die Nutzung und eingehende Anforderungen ohne Dienstausfälle bewältigen können. Eine Drosselung kann auf Anwendungs-, Mandanten- oder der gesamten Dienstebene erfolgen. Eine Drosselung tritt häufig auf, wenn eine Anwendung über eine große Anzahl von Anforderungen innerhalb eines Mandanten oder mandantenübergreifend verfügt.  Zur Laufzeit kann Ihre Anwendung Azure AD-Verzeichnisobjekte über Microsoft Graph als Teil ihrer Geschäftslogik lesen oder aktualisieren. Dies umfasst beispielsweise das Lesen oder Festlegen von Benutzerattributen, Aktualisieren des Kalenders eines Benutzers oder Senden von E-Mails im Namen des Benutzers.  Während der Ausführung kann Ihre Anwendung auch Azure-Ressourcen bereitstellen, darauf zugreifen, sie aktualisieren und löschen. Während der Tests könnte Ihre Anwendung die Laufzeitdrosselungsgrenzen und die zuvor erwähnten Diensteinschränkungen beim Bereitstellen von Ressourcen oder Verzeichnisobjekten erreichen.

## <a name="azure-ad-service-limits-relevant-to-testing"></a>Für Tests relevante Azure AD-Diensteinschränkungen
Allgemeine Informationen zu Azure AD-Nutzungsbeschränkungen und Diensteinschränkungen finden Sie [hier](/azure/active-directory/enterprise-users/directory-service-limits-restrictions).  Allgemeine Informationen zu Einschränkungen für Azure-Abonnements und -Dienste, Kontingenten und Beschränkungen finden Sie [hier](/azure/azure-resource-manager/management/azure-subscription-service-limits).

In der folgenden Tabelle sind Azure AD-Diensteinschränkungen aufgeführt, die beim Einrichten einer Testumgebung oder beim Ausführen von Tests zu berücksichtigen sind. 

| Category         | Begrenzung   |
|-------------------|----------------|
| Mandanten | Ein einzelner Benutzer kann maximal 200 Verzeichnisse erstellen.|
| Ressourcen | <ul><li>Bei der kostenlosen Edition von Azure Active Directory können Benutzer standardmäßig in einem einzelnen Mandanten maximal 50.000 Azure AD-Ressourcen erstellen. Wenn Sie mindestens eine verifizierte Domäne haben, wird das Standardkontingent für den Azure AD-Dienst für Ihre Organisation auf 300.000 Azure AD-Ressourcen erweitert. Das Kontingent für den Azure AD-Dienst für durch die Self-Service-Registrierung erstellte Organisationen beträgt weiterhin 50.000 Azure AD-Ressourcen, auch wenn eine interne Administratorübernahme stattgefunden hat. Die Organisation wird in einen verwalteten Mandanten mit mindestens einer überprüften Domäne konvertiert. Dieses Dienstlimit steht nicht im Zusammenhang mit dem Tariflimit von 500.000 Ressourcen auf der Azure AD-Preisseite. Um das Standardkontingent zu überschreiten, müssen Sie sich an den Microsoft-Support wenden.</li><li>Ein Benutzer ohne Administratorrechte kann maximal 250 Azure AD-Ressourcen erstellen. Zu diesem Kontingent zählen sowohl aktive Ressourcen als auch gelöschte Ressourcen, die zum Wiederherstellen verfügbar sind. Nur gelöschte Azure AD-Ressourcen, die vor weniger als 30 Tagen gelöscht wurden, stehen für die Wiederherstellung bereit. Gelöschte Azure AD-Ressourcen, die nicht mehr für die Wiederherstellung verfügbar sind, zählen für 30 Tage mit einem Viertelwert zu diesem Kontingent. Wenn Sie Entwickler haben, die dieses Kontingent wahrscheinlich wiederholt im Rahmen ihrer regulären Aufgaben überschreiten, können Sie eine benutzerdefinierte Rolle erstellen und zuweisen, die die Berechtigung hat, eine unbegrenzte Anzahl von App-Registrierungen zu erstellen.</li></ul>|
| Anwendungen| <ul><li>Für einen Benutzer, eine Gruppe oder einen Dienstprinzipal können maximal 1.500 App-Rollenzuweisungen festgelegt werden.</li><li>Ein Benutzer kann maximal nur 48 Apps verwenden, für die Benutzername und Kennwortanmeldeinformationen konfiguriert sind.</li></ul>|
| Anwendungsmanifest| Im Anwendungsmanifest können maximal 1200 Einträge hinzugefügt werden. |
| Gruppen |  <ul><li>Ein Benutzer ohne Administratorrechte kann in einer Azure AD-Organisation maximal 250 Gruppen erstellen. Alle Azure AD-Administratoren, die Gruppen in der Organisation verwalten können, können auch eine unbegrenzte Anzahl von Gruppen erstellen (bis zum Grenzwert für Azure AD-Objekte). Wenn Sie eine Rolle zuweisen, um den Grenzwert für einen Benutzer zu entfernen, weisen Sie ihm eine integrierte Rolle mit weniger Berechtigungen zu, etwa die Rolle „Benutzeradministrator“ oder „Gruppenadministrator“.</li><li>Eine Azure AD-Organisation kann maximal 5.000 dynamische Gruppen enthalten.</li><li>In einer einzigen Azure AD-Organisation (Mandant) können maximal 300 Gruppen erstellt werden, denen Rollen zugewiesen werden können.</li><li>Eine beliebige Anzahl von Azure AD-Ressourcen kann einer einzelnen Gruppe angehören.</li><li>Ein Benutzer kann ein Mitglied einer beliebigen Anzahl von Gruppen sein.</li></ul>|
| Azure AD-Rollen und -Berechtigungen | <ul><li>In einer Azure AD-Organisation können maximal 30 benutzerdefinierte Azure AD-Rollen erstellt werden.</li><li>Maximal 100 benutzerdefinierte Azure AD-Rollenzuweisungen für einen einzelnen Prinzipal im Mandantenbereich.</li><li>Maximal 100 integrierte Azure AD-Rollenzuweisungen für einen einzelnen Prinzipal im Nichtmandantenbereich (z. B. Verwaltungseinheit oder Azure AD-Objekt). Im Mandantenbereich können beliebig viele integrierte Azure AD-Rollenzuweisungen verwendet werden.</li></ul>|


## <a name="throttling-limits-relevant-to-testing"></a>Für Tests relevante Drosselungsgrenzwerte

Es gelten die folgenden globalen Microsoft Graph-Drosselungsgrenzwerte:

| Anforderungstyp | Pro App mandantenübergreifend |
|-------------------|----------------|
| Anforderungstyp | Pro App mandantenübergreifend |
| Any | 2000 Anforderungen pro Sekunde| 

In der folgenden Tabelle sind Azure AD-Drosselungsgrenzwerte aufgeführt, die beim Ausführen von Tests berücksichtigt werden müssen. Die Drosselung basiert auf einem Tokenbucketalgorithmus, bei dem die einzelnen Anforderungskosten addiert werden. Die Summe der Anforderungskosten wird dann mit den vorab festgelegten Grenzwerten verglichen. Nur die Anforderungen, die diese Grenzwerte überschreiten, werden gedrosselt. Ausführlichere Informationen zu Anforderungskosten finden Sie unter den [Diensteinschränkungen für Identität und Zugriff](/graph/throttling#pattern). Weitere dienstspezifische Beschränkungen für Microsoft Graph finden Sie [hier](/graph/throttling#service-specific-limits).

| Grenzwerttyp | Ressourceneinheitenkontingent | Schreibkontingent |
|-------------------|----------------|----------------|
| Anwendungs- und Mandantenpaar | S: 3500, M: 5000, L: 8000 pro 10 Sekunden | 3000 pro 2 Minuten und 30 Sekunden |
| application | 150.000 pro 20 Sekunden | 70.000 pro 5 Minuten |
| tenant | Nicht zutreffend | 18.000 pro 5 Minuten |

Der Grenzwert für Anwendungs- und Mandantenpaare hängt von der Anzahl der Benutzer in dem Mandanten ab, für den Anforderungen ausgeführt werden. Die Mandantengrößen sind wie folgt definiert: S – weniger als 50 Benutzer, M – zwischen 50 und 500 Benutzer, L – mehr als 500 Benutzer.

## <a name="what-happens-when-a-throttling-limit-is-exceeded"></a>Was geschieht bei Überschreitung eines Drosselungsgrenzwerts? 
Das Drosselungsverhalten kann von Art und Anzahl der Anforderungen abhängen. So werden beispielsweise bei einer großen Anzahl von Anforderungen sämtliche Arten von Anforderungen gedrosselt. Schwellenwerte variieren je nach Anforderungstyp. Das kann zu Szenarien führen, in denen Schreibvorgänge gedrosselt, aber Lesevorgänge weiterhin zulässig sind.

Wenn Sie einen Drosselungsgrenzwert überschreiten, erhalten Sie den HTTP-Statuscode `429 Too many requests`, und Ihre Anforderung schlägt fehl. Die Antwort enthält einen `Retry-After`-Headerwert, der die Anzahl von Sekunden angibt, die Ihre Anwendung warten muss (oder im Ruhezustand verbleibt), bevor die nächste Anforderung gesendet wird. Wiederholen Sie die Anforderung.  Wenn Sie eine Anforderung senden, bevor der Retry-Wert verstrichen ist, wird Ihre Anforderung nicht verarbeitet, und es wird ein neuer Retry-Wert zurückgegeben. Wenn die Anforderung erneut den Fehlercode 429 erhält, ist die Drosselung noch aktiv. Wiederholen Sie die Anforderung nach dem in `Retry-After` empfohlenen Zeitraum so lange, bis sie erfolgreich ist.

## <a name="next-steps"></a>Nächste Schritte
Hier erfahren Sie, wie Sie eine [Testumgebung einrichten](test-setup-environment.md).

