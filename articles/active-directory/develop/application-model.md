---
title: Anwendungsmodell | Azure
titleSuffix: Microsoft identity platform
description: Enthält eine Beschreibung des Prozesses zum Registrieren Ihrer Anwendung, damit diese mit der Microsoft Identity Platform integriert werden kann.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2021
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5b2f62b0adc040b5f20b341457789378f1aa983a
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129154164"
---
# <a name="application-model"></a>Anwendungsmodell

Anwendungen können Benutzer selbst anmelden oder die Anmeldung an einen Identitätsanbieter delegieren. In diesem Artikel werden die erforderlichen Schritte zum Registrieren einer Anwendung bei der Microsoft Identity Platform erläutert.

## <a name="register-an-application"></a>Registrieren einer Anwendung

Damit ein Identitätsanbieter weiß, dass ein Benutzer Zugriff auf eine bestimmte App hat, müssen sowohl der Benutzer als auch die Anwendung beim Identitätsanbieter registriert werden. Wenn Sie Ihre Anwendung bei Azure AD (Azure Active Directory) registrieren, stellen Sie eine Identitätskonfiguration für Ihre Anwendung bereit, die die Integration mit der Microsoft Identity Platform ermöglicht. Die Registrierung der App ermöglicht Ihnen außerdem Folgendes:

* Anpassen des Brandings Ihrer Anwendung im Anmeldedialogfeld. Dieses Branding ist wichtig, weil die Anmeldung der erste Eindruck ist, den ein Benutzer von Ihrer App erhält.
* Entscheiden, ob Sie die Anmeldung nur für Benutzer zulassen, die Ihrer Organisation angehören. Diese Architektur wird als „Einzelinstanzanwendung“ bezeichnet. Sie können Benutzern auch die Anmeldung mit einem beliebigen Geschäfts-, Schul- oder Unikonto ermöglichen. Dies wird als mehrinstanzenfähige Anwendung bezeichnet. Sie können auch persönliche Microsoft-Konten oder ein Social Media-Konto zulassen, z. B. von LinkedIn, Google usw.
* Anfordern von Bereichsberechtigungen. Sie können beispielsweise den Bereich „user.read“ anfordern, mit dem die Berechtigung zum Lesen des Profils des angemeldeten Benutzers gewährt wird.
* Definieren von Bereichen, mit denen der Zugriff auf Ihre Web-API definiert wird. Wenn eine App auf Ihre API zugreifen möchte, muss sie normalerweise Berechtigungen für die von Ihnen definierten Bereiche anfordern.
* Austauschen eines Geheimnisses mit der Microsoft Identity Platform, um die Identität der App nachzuweisen. Die Verwendung eines Geheimnisses ist relevant für den Fall, dass es sich bei der App um eine vertrauliche Clientanwendung handelt. In einer vertraulichen Clientanwendung können Anmeldeinformationen sicher aufbewahrt werden. Ein vertrauenswürdiger Back-End-Server wird zum Speichern der Anmeldeinformationen benötigt.

Nach der Registrierung der App erhält diese einen eindeutigen Bezeichner, der beim Anfordern von Token für die Microsoft Identity Platform bereitgestellt wird. Wenn es sich bei der App um eine [vertrauliche Clientanwendung](developer-glossary.md#client-application) handelt, wird auch das Geheimnis oder der öffentliche Schlüssel ausgetauscht. Dies hängt davon ab, ob Zertifikate oder Geheimnisse verwendet werden.

Für die Microsoft Identity Platform werden Anwendungen mit einem Modell dargestellt, das zwei Hauptfunktionen erfüllt:

* Identifizieren der App anhand der unterstützten Authentifizierungsprotokolle
* Bereitstellen aller Bezeichner, URLs, Geheimnisse und zugehörigen Informationen, die für die Authentifizierung erforderlich sind

Für die Microsoft Identity Platform gilt Folgendes:

* Enthält alle Daten, die zur Unterstützung der Authentifizierung zur Laufzeit erforderlich sind.
* Enthält alle Daten zum Treffen der Entscheidung, auf welche Ressourcen eine App ggf. zugreifen muss und unter welchen Umständen eine bestimmte Anforderung erfüllt werden soll.
* Stellt Infrastruktur für die Implementierung der App-Bereitstellung innerhalb des Mandanten des App-Entwicklers und für andere Azure AD-Mandanten bereit.
* Verarbeitet die Benutzereinwilligung während der Tokenanforderung und vereinfacht die dynamische mandantenübergreifende Bereitstellung von Apps.

Die *Einwilligung* ist der Prozess, bei dem ein Ressourcenbesitzer einer Clientanwendung durch spezifische Berechtigungen die Autorisierung für den Zugriff auf geschützte Ressourcen im Auftrag des Ressourcenbesitzers gewährt. Die Microsoft Identity Platform ermöglicht Folgendes:

* Benutzer und Administratoren können der App dynamisch die Einwilligung für den Zugriff auf Ressourcen in ihrem Namen gewähren oder verweigern.
* Administratoren können letztendlich entscheiden, welche Aktionen Apps ausführen und welche Benutzer bestimmte Apps verwenden dürfen und wie auf die Verzeichnisressourcen zugegriffen wird.

## <a name="multi-tenant-apps"></a>Mehrinstanzenfähige Apps

In Microsoft Identity Platform beschreibt ein [Anwendungsobjekt](developer-glossary.md#application-object) eine Anwendung. Zum Zeitpunkt der Bereitstellung verwendet die Microsoft Identity Platform das Anwendungsobjekt als Blaupause zum Erstellen eines [Dienstprinzipals](developer-glossary.md#service-principal-object), der eine konkrete Instanz einer Anwendung in einem Verzeichnis oder Mandanten darstellt. Mit dem Dienstprinzipal wird definiert, welche Aktionen die App in einem bestimmten Zielverzeichnis ausführen darf, wer sie verwenden kann, auf welche Ressourcen sie Zugriff besitzt usw. Die Microsoft Identity Platform erstellt mittels [Einwilligung](developer-glossary.md#consent) aus einem Anwendungsobjekt einen Dienstprinzipal.

Die folgende Abbildung zeigt einen vereinfachten Microsoft Identity Platform-Bereitstellungsablauf, der durch die Einwilligung gesteuert wird. Sie enthält zwei Mandanten: *A* und *B*.

* *Mandant A* ist der Besitzer der Anwendung.
* *Mandant B* instanziiert die Anwendung über einen Dienstprinzipal.

![Diagramm: Vereinfachter Bereitstellungsablauf, der auf Einwilligung basiert](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

In diesem Bereitstellungsablauf geschieht Folgendes:

1. Ein Benutzer von Mandant B versucht, sich mit der App anzumelden. Der Autorisierungsendpunkt fordert ein Token für die Anwendung an.
1. Die Anmeldeinformationen des Benutzers werden abgerufen und für die Authentifizierung überprüft.
1. Der Benutzer wird aufgefordert, dem Zugriff auf den Mandanten B für die App zuzustimmen.
1. Die Microsoft Identity Platform verwendet das Anwendungsobjekt in Mandant A als Blaupause für die Erstellung eines Dienstprinzipals in Mandant B.
1. Der Benutzer erhält das angeforderte Token.

Sie können diesen Prozess für weitere Mandanten wiederholen. Der Mandant A enthält die Blaupause für die App (Anwendungsobjekt). Benutzer und Administratoren aller anderen Mandanten, in denen der App die Zustimmung erteilt wird, behalten durch das entsprechende Dienstprinzipalobjekt in jedem Mandanten die Kontrolle darüber, welche Aktionen die Anwendung ausführen darf. Weitere Informationen finden Sie unter [Anwendungs- und Dienstprinzipalobjekte in der Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Authentifizierung und Autorisierung in der Microsoft Identity Platform finden Sie in den folgenden Artikeln:

* Informationen zu den grundlegenden Konzepten für die Authentifizierung und Autorisierung finden Sie unter [Authentifizierung im Vergleich zu Autorisierung](authentication-vs-authorization.md).
* Unter [Sicherheitstoken](security-tokens.md) erfahren Sie, wie Zugriffstoken, Aktualisierungstoken und ID-Token bei der Authentifizierung und Autorisierung verwendet werden.
* Informationen zum Anmeldeablauf für Web-, Desktop- und mobile Apps finden Sie unter [App-Anmeldeflow mit Microsoft Identity Platform](app-sign-in-flow.md).

Weitere Informationen zum Anwendungsmodell finden Sie in den folgenden Artikeln:

* Weitere Informationen zu Anwendungsobjekten und Dienstprinzipalen der Microsoft Identity Platform finden Sie unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](active-directory-how-applications-are-added.md).
* Unter [Mandanten in Azure Active Directory](single-and-multi-tenant-apps.md) erhalten Sie weitere Informationen zu Einzelinstanz-Apps und mehrinstanzenfähigen Apps.
* In der [Azure Active Directory B2C-Dokumentation](../../active-directory-b2c/index.yml) erfahren Sie, wie von Azure AD auch Azure Active Directory B2C bereitgestellt wird, damit Organisationen für die Anmeldung von Benutzern (meist Kunden) Identitäten sozialer Netzwerke, z. B. ein Google-Konto, verwenden können.
