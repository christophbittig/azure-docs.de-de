---
title: Authentifizierungsprotokolle in Azure Active Directory B2C
description: Hier erfahren Sie, wie Sie Apps direkt mit den von Azure Active Directory B2C unterstützten Protokollen erstellen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: c01602182972c375843fde5a1720dcff2cc6d341
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130035636"
---
# <a name="azure-ad-b2c-authentication-protocols"></a>Azure AD B2C: Authentifizierungsprotokolle
Azure Active Directory B2C (Azure AD B2C) bietet Identity-as-a-Service (IDaaS) für Ihre Apps durch die Unterstützung zweier gängiger Standardprotokolle: OpenID Connect und OAuth 2.0. Auch wenn der Dienst standardkonform ist, kann es feine Unterschiede zwischen zwei Implementierungen dieser Protokolle geben.

Die Informationen in diesem Leitfaden sind hilfreich, wenn Sie Code direkt durch Senden und Verarbeiten von HTTP-Anforderungen schreiben, anstatt eine unserer Open Source-Bibliotheken zu nutzen. Es wird empfohlen, diese Seite zu lesen, bevor Sie sich mit den Details der spezifischen Protokolle beschäftigen. Falls Sie bereits mit Azure AD B2C vertraut sind, können Sie direkt zu den [Protokollreferenzhandbüchern](#protocols) wechseln.

<!-- TODO: Need link to libraries above -->

## <a name="the-basics"></a>Grundlagen
Jede App, die Azure AD B2C verwendet, muss im B2C-Verzeichnis im [Azure-Portal](https://portal.azure.com)registriert werden. Beim App-Registrierungsprozess werden einige Werte gesammelt und der App zugewiesen:

* Eine **Anwendungs-ID** , die Ihre App eindeutig identifiziert.
* Einen **Umleitungs-URI** oder **Paketbezeichner**, der zum Umleiten von Antworten zurück an die App verwendet werden kann.
* Einige andere szenariospezifische Werte. Weitere Informationen finden Sie im [Abschnitt zum Registrieren Ihrer Anwendung](tutorial-register-applications.md).

Nach dem Registrieren Ihrer App kommuniziert diese mit Azure Active Directory (Azure AD), indem sie Anforderungen an den Endpunkt sendet:

```
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/authorize
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/oauth2/v2.0/token
```

In fast allen OAuth- und OpenID Connect-Abläufen gibt es beim Austausch vier Beteiligte:




:::image type="content" source="./media/protocols-overview/protocols_roles.png" alt-text="Diagramm mit den vier OAuth 2.0-Rollen":::

* Der **Autorisierungsserver** ist der Azure AD-Endpunkt. Er kümmert sich um die sichere Verwaltung rund um Benutzerinformationen und Zugriff. Zudem verarbeitet er die Vertrauensstellungen zwischen den Parteien in einem Ablauf. Er ist verantwortlich für das Überprüfen der Identität des Benutzers, das Erteilen und Widerrufen des Zugriffs auf Ressourcen und das Ausstellen von Token. Er wird auch als Identitätsanbieter bezeichnet.

* Beim **Ressourcenbesitzer** handelt es sich normalerweise um den Endbenutzer. Diese Person besitzt die Daten und hat die Möglichkeit, Dritten den Zugriff auf die Daten oder die Ressource zu gewähren.

* Der **OAuth-Client** ist Ihre App. Sie wird durch die Anwendungs-ID identifiziert. Sie ist normalerweise die Komponente, mit der die Endbenutzer interagieren. Sie fordert auch Token vom Autorisierungsserver an. Der Ressourcenbesitzer muss dem Client die Berechtigung für den Zugriff auf die Ressource erteilen.

* Der **Ressourcenserver** ist der Ort, an dem die Ressource oder die Daten abgelegt sind. Er vertraut auf die sichere Authentifizierung und Autorisierung des OAuth-Clients durch den Autorisierungsserver. Außerdem verwendet er Bearerzugriffstoken, um sicherzustellen, dass der Zugriff auf eine Ressource gewährt werden kann.

## <a name="policies-and-user-flows"></a>Richtlinien und Benutzerflows
Die Richtlinien in Azure AD B2C sind wohl das wichtigste Feature des Diensts. Azure AD B2C erweitert die Standardprotokolle OAuth 2.0 und OpenID Connect durch die Einführung von Richtlinien. Diese ermöglichen Azure AD B2C viel mehr als nur die einfache Authentifizierung und Autorisierung.

Um Ihnen die Einrichtung der gängigsten Identitätsaufgaben zu erleichtern, enthält das Azure AD B2C-Portal vordefinierte und konfigurierbare Richtlinien, die als **Benutzerflows** bezeichnet werden. Benutzerflows beschreiben Umgebungen in Verbindung mit der Endbenutzeridentität – einschließlich Registrierung, Anmeldung und Profilbearbeitung. Benutzerflows können in einer Administratorbenutzeroberfläche definiert werden. Sie können mithilfe eines speziellen Abfrageparameters in HTTP-Authentifizierungsanforderungen ausgeführt werden.

Richtlinien und Benutzerflows sind keine Standardfeatures von OAuth 2.0 und OpenID Connect. Sie sollten sich also die Zeit nehmen, sich damit vertraut zu machen. Weitere Informationen finden Sie in der [Referenzanleitung zu Azure AD B2C-Benutzerflows](user-flow-overview.md).

## <a name="tokens"></a>Token
Die Implementierung von OAuth 2.0 und OpenID Connect in Azure AD B2C macht ausgiebig Gebrauch von Bearertoken (auch in Form von JSON-Webtoken, JWTs). Ein Bearertoken ist ein einfaches Sicherheitstoken, das dem Inhaber, dem „Bearer“, den Zugriff auf eine geschützte Ressource ermöglicht.

Der Bearer ist jede beliebige Partei, die das Token vorweisen kann. Azure AD muss eine Partei zunächst authentifizieren, damit sie ein Bearertoken erhalten kann. Falls jedoch nicht die erforderlichen Schritte ausgeführt werden, um das Token bei der Übertragung und Speicherung zu schützen, kann das Token von einer nicht vorgesehenen Partei abgefangen und verwendet werden.

Einige Sicherheitstoken verfügen über integrierte Mechanismen, die eine Verwendung durch nicht autorisierte Parteien verhindern. Bearertoken besitzen diesen Mechanismus jedoch nicht. Sie müssen über einen sicheren Kanal (beispielsweise Transport Layer Security (HTTPS)) übertragen werden.

Wird ein Bearertoken außerhalb eines sicheren Kanals gesendet, kann eine böswillige Partei das Token mithilfe eines Man-in-the-Middle-Angriffs abrufen und damit unautorisiert auf eine geschützte Ressource zugreifen. Die gleichen Sicherheitsprinzipien gelten für die (Zwischen-)Speicherung von Bearertoken zur späteren Verwendung. Stellen Sie daher sicher, dass Ihre App Bearertoken stets auf sichere Weise überträgt und speichert.

Weitere Überlegungen zur Sicherheit von Bearertoken finden Sie unter [RFC 6750, Abschnitt 5](https://tools.ietf.org/html/rfc6750).

Weitere Informationen zu verschiedenen Tokentypen, die in Azure AD B2C verwendet werden, finden Sie in der [Referenz zu Azure AD-Token](tokens-overview.md).

## <a name="protocols"></a>Protokolle
Wenn Sie bereit sind, sich einige Beispielanforderungen anzusehen, können Sie mit einem der folgenden Tutorials beginnen. Jedes Tutorial entspricht einem bestimmten Authentifizierungsszenario. Wenn Sie Hilfe beim Ermitteln des richtigen Arbeitsablaufs benötigen, informieren Sie sich über die [App-Typen, die mit Azure AD B2C erstellt werden können](application-types.md).

* [Erstellen von mobilen und nativen Anwendungen mit OAuth 2.0](authorization-code-flow.md)
* [Erstellen von Web-Apps mithilfe von OpenID Connect](openid-connect.md)
* [Erstellen von Apps mit einer einzigen Seite mit dem impliziten OAuth 2.0-Fluss](implicit-flow-single-page-application.md)

