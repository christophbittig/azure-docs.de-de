---
title: Benutzerflows und benutzerdefinierte Richtlinien in Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Erfahren Sie mehr über integrierte Benutzerflows und das erweiterbare Richtlinienframework für benutzerdefinierte Richtlinien von Azure Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 73d1649881428047cf0c593335d66d9d3145ec2c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021864"
---
# <a name="user-flows-and-custom-policies-overview"></a>Übersicht über Benutzerflows und benutzerdefinierte Richtlinien

In Azure AD B2C können Sie die Geschäftslogik definieren, der Benutzer folgen müssen, um Zugriff auf Ihre Anwendung zu erhalten. Beispielsweise können Sie die Abfolge der Schritte bestimmen, die Benutzer ausführen müssen, wenn sie sich anmelden oder registrieren, ein Profil bearbeiten oder ein Kennwort zurücksetzen möchten. Am Ende der Abfolge erhält der Benutzer ein Token und damit Zugriff auf Ihre Anwendung. 

In Azure AD B2C gibt es zwei Möglichkeiten, Benutzeridentitäten bereitzustellen:

* **Benutzerflows** sind vordefinierte, integrierte und konfigurierbare Richtlinien, die zur Verfügung gestellt werden, damit Sie in wenigen Minuten Umgebungen zur Registrierung, Anmeldung und Profilbearbeitung erstellen können.

* **Benutzerdefinierte Richtlinien** ermöglichen es Ihnen, eigene User Journeys für komplexe Identitätsszenarios zu erstellen.

Der folgende Screenshot zeigt die Benutzeroberfläche für Benutzerfloweinstellungen im Vergleich zu benutzerdefinierten Richtlinienkonfigurationsdateien.

![Der Screenshot zeigt die Benutzeroberfläche für Benutzerfloweinstellungen im Vergleich zu benutzerdefinierten Richtlinienkonfigurationsdateien.](media/user-flow-overview/user-flow-vs-custom-policy.png)

In diesem Artikel erhalten Sie eine kurze Übersicht über Benutzerflows und benutzerdefinierte Richtlinien. Dies soll Ihnen bei der Entscheidung helfen, welche Methode für Ihre Geschäftsanforderungen am besten geeignet ist.

## <a name="user-flows"></a>Benutzerflows

Zum Einrichten der gängigsten Identitätsaufgaben enthält das Azure-Portal verschiedene vordefinierte und konfigurierbare Richtlinien, die als *Benutzerflows* bezeichnet werden.

Sie können Benutzerfloweinstellungen wie die folgenden konfigurieren, um das Verhalten von Identitäten in Ihren Anwendungen zu steuern:

* Kontotypen für die Anmeldung, z. B. Konten für soziale Netzwerke wie Facebook oder lokale Konten, die eine E-Mail-Adresse und ein Kennwort für die Anmeldung verwenden
* Attribute, die vom Consumer abgefragt werden, z. B. Vorname, Postleitzahl oder Land/Region des Wohnsitzes
* Azure AD Multi-Factor Authentication (MFA)
* Anpassung der Benutzeroberfläche
* Eine Reihe von Ansprüchen in einem Token, die Ihre Anwendung empfängt, nachdem der Benutzer den Benutzerflow abgeschlossen hat
* Sitzungsverwaltung
* ... und vieles mehr.

Die meisten gängigen Identitätsszenarien für Apps können mit Benutzerflows effektiv definiert und implementiert werden. Es wird empfohlen, die integrierten Benutzerflows zu verwenden, es sei denn, Sie benötigen für komplexe User Journey-Szenarien die hohe Flexibilität von benutzerdefinierten Richtlinien.

## <a name="custom-policies"></a>Benutzerdefinierte Richtlinien

Benutzerdefinierte Richtlinien sind Konfigurationsdateien, die das Verhalten der Benutzerumgebung Ihres Azure AD B2C-Mandanten definieren. Während Benutzerflows im Azure AD B2C-Portal für die gängigsten Identitätsaufgaben vordefiniert sind, können benutzerdefinierte Richtlinien von einem Identitätsentwickler vollständig bearbeitet werden, um die Ausführung vieler verschiedener Aufgaben zu ermöglichen.

Eine benutzerdefinierte Richtlinie ist vollständig konfigurierbar und richtliniengesteuert. Sie orchestriert die Vertrauensstellung zwischen Entitäten in Standardprotokollen, zum Beispiel OpenID Connect, OAuth, SAML und einige wenige nicht standardmäßige Protokolle wie der Austausch von Systemansprüchen auf REST API-Basis. Das Framework erstellt benutzerfreundliche Umgebungen mit Positivlisten.

Mit einer benutzerdefinierten Richtlinie haben Sie die Möglichkeit, User Journeys mit einer beliebigen Schrittkombination zu erstellen. Beispiel:

* Verbund mit anderen Identitätsanbietern
* Herausforderungen bei der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) von Erst- und Drittanbietern
* Beliebige Benutzereingaben erfassen
* Integration in externe Systeme mithilfe der REST-API-Kommunikation

Jede User Journey wird durch eine Richtlinie definiert. Sie können so viele oder so wenige Richtlinien erstellen, wie Sie benötigen, um die beste Benutzererfahrung für Ihre Organisation zu ermöglichen.

![Diagramm mit einem Beispiel für eine durch IEF ermöglichte komplexe User Journey](media/user-flow-overview/custom-policy-diagram.png)

Benutzerdefinierte Richtlinien werden durch mehrere Dateien im XML-Format definiert, die in einer hierarchischen Kette aufeinander verweisen. Die XML-Elemente definieren das Anspruchsschema, Anspruchstransformationen, Inhaltsdefinitionen, Anspruchsanbieter, technische Profile, Schritte zur Orchestrierung von User Journeys und weitere Aspekte der Identitätsverwaltung.

Die leistungsstarke Flexibilität von benutzerdefinierten Richtlinien ist am besten geeignet, wenn Sie komplexe Identitätsszenarien erstellen müssen. Entwickler, die benutzerdefinierte Richtlinien konfigurieren, müssen die vertrauenswürdigen Beziehungen im Detail definieren, um Metadatenendpunkte und genaue Anspruchsaustauschdefinitionen einzuschließen und bei Bedarf zudem Geheimnisse, Schlüssel und Zertifikate durch die einzelnen Identitätsanbieter zu konfigurieren.

Weitere Informationen über benutzerdefinierte Richtlinien finden Sie unter [Benutzerdefinierte Richtlinien in Azure Active Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Vergleichen von Benutzerflows mit benutzerdefinierten Richtlinien

Die folgende Tabelle enthält einen detaillierten Vergleich der Szenarien, die Sie mit Azure AD B2C-Benutzerflows und benutzerdefinierten Richtlinien ermöglichen können.

| Kontext | Benutzerflows | Benutzerdefinierte Richtlinien |
|-|-------------------|-----------------|
| Zielbenutzer | Alle Anwendungsentwickler mit oder ohne Identitätskenntnissen | Identitätsexperten, Systemintegratoren, Berater und interne Identitätsteams. Sie sind mit OpenID Connect-Flows sowie Identitätsanbietern und anspruchsbasierten Authentifizierungen vertraut. |
| Konfigurationsmethode | Azure-Portal mit benutzerfreundlicher Benutzeroberfläche (User Interface, UI) | Direktes Bearbeiten von XML-Dateien und anschließendes Hochladen in das Azure-Portal |
| Anpassung der Benutzeroberfläche | [Vollständige Anpassung der Benutzeroberfläche](customize-ui-with-html.md) einschließlich HTML, CSS und [JavaScript](javascript-and-page-layout.md).<br><br>[Unterstützung mehrerer Sprachen](language-customization.md) mit benutzerdefinierten Zeichenfolgen. | Identisch mit Benutzerflows |
| Anpassung von Attributen | Standard- und benutzerdefinierte Attribute | Identisch mit Benutzerflows |
| Token- und Sitzungsverwaltung | [Anpassung von Token](configure-tokens.md) und des [Sitzungsverhaltens](session-behavior.md). | Identisch mit Benutzerflows |
| Identitätsanbieter | [Vordefinierte lokale Anbieter](identity-provider-local.md) oder [Social Media-Anbieter](add-identity-provider.md), z. B. Verbund mit Azure Active Directory-Mandanten. | Standardbasierte OIDC-, OAUTH- und SAML-Protokolle  Die Authentifizierung ist auch mithilfe der Integration von REST-APIs möglich. |
| Identitätsaufgaben | [Registrierung oder Anmeldung](add-sign-up-and-sign-in-policy.md) mit lokalen Konten oder vielen Social Media-Konten<br><br>[Self-Service-Kennwortzurücksetzung](add-password-reset-policy.md)<br><br>[Profilbearbeitung](add-profile-editing-policy.md)<br><br>Multi-Factor Authentication<br><br>Zugriffstoken-Flows | Führen Sie dieselben Aufgaben wie bei Benutzerflows mit benutzerdefinierten Identitätsanbietern aus, oder verwenden Sie benutzerdefinierte Bereiche.<br><br>Stellen Sie ein Benutzerkonto bei der Registrierung in einem anderen System bereit.<br><br>Senden Sie mit Ihrem eigenen E-Mail-Dienstanbieter eine Willkommens-E-Mail.<br><br>Verwenden Sie einen Benutzerspeicher außerhalb von Azure AD B2C.<br><br>Überprüfen Sie die von Benutzern bereitgestellten Informationen mit einem vertrauenswürdigen System über eine API. |

## <a name="application-integration"></a>Anwendungsintegration

Sie können viele Benutzerflows oder benutzerdefinierte Richtlinien verschiedener Art in Ihrem Mandanten erstellen und bei Bedarf in Ihren Anwendungen verwenden. Sowohl Benutzerflows als auch benutzerdefinierte Richtlinien können anwendungsübergreifend wiederverwendet werden. Durch diese Flexibilität können Sie Benutzeroberflächen im Zusammenhang mit der Identität mit minimalen oder ganz ohne Änderungen am Code definieren und ändern. 

Wenn sich ein Benutzer bei Ihrer Anwendung anmelden möchte, initiiert die Anwendung eine Autorisierungsanforderung an einen Benutzerflow-Endpunkt oder einen von der benutzerdefinierten Richtlinie bereitgestellten Endpunkt. Der Benutzerflow oder die benutzerdefinierte Richtlinie definiert und steuert die Funktionalität für die Benutzer. Wenn der Benutzer einen Benutzerflow ausführt, generiert Azure AD B2C ein Token und leitet den Benutzer anschließend wieder zurück zu Ihrer Anwendung.

![Mobile App mit Pfeilen, die den Flow zur Azure AD B2C-Anmeldeseite darstellen](media/user-flow-overview/app-integration.png)

Mehrere Anwendungen können den gleichen Benutzerflow oder die gleiche benutzerdefinierte Richtlinie verwenden. Jede Anwendung kann mehrere Benutzerflows oder benutzerdefinierte Richtlinien verwenden.

Beispielsweise kann die Anwendung den Benutzerflow *Registrieren oder anmelden* für die Anmeldung bei einer Anwendung verwenden. Nach der Anmeldung möchte der Benutzer möglicherweise sein Profil bearbeiten. Zum Bearbeiten des Profils initiiert die Anwendung eine weitere Autorisierungsanforderung, dieses Mal unter Verwendung des Benutzerflows *Profilbearbeitung*.

Ihre Anwendung löst mithilfe einer standardmäßigen HTTP-Authentifizierungsanforderung, die den Namen des Benutzerflows oder der benutzerdefinierten Richtlinie enthält, einen Benutzerflow aus. Als Antwort wird ein benutzerdefiniertes [Token](tokens-overview.md) empfangen.


## <a name="next-steps"></a>Nächste Schritte

- Befolgen Sie zum Erstellen der empfohlenen Benutzerflows die Anweisungen im [Tutorial: Erstellen eines Benutzerflows](tutorial-create-user-flows.md).
- Erfahren Sie mehr über [Benutzerflowversionen in Azure AD B2C](user-flow-versions.md).
- Erfahren Sie mehr über [Benutzerdefinierte Richtlinien in Azure AD B2C](custom-policy-overview.md).
