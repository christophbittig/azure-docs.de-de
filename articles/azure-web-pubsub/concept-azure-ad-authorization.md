---
title: Autorisieren des Zugriffs mit Azure Active Directory für Azure Web PubSub
description: Dieser Artikel bietet Informationen zum Autorisieren des Zugriffs auf Azure Web PubSub-Dienstressourcen mit Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 386ef4d27a8fc2fbe4d9c3f209d2016fa1911761
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997455"
---
# <a name="authorize-access-to-web-pubsub-resources-using-azure-active-directory"></a>Autorisieren des Zugriffs auf Web PubSub-Ressourcen mit Azure Active Directory
Der Azure Web PubSub-Dienst unterstützt die Verwendung von Azure Active Directory (Azure AD) zum Autorisieren von Anforderungen an Web PubSub-Ressourcen. Azure AD ermöglicht Ihnen die Verwendung der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC), um einem Sicherheitsprinzipal<sup>[<a href="#security-principal">1</a>]</sup> Berechtigungen zu erteilen. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, und gibt seinerseits ein OAuth 2.0-Token zurück. Das Token kann anschließend zum Autorisieren einer Anforderung an die Web PubSub-Ressource verwendet werden.

Die Autorisierung von Anforderungen für Web PubSub mit Azure AD bietet mehr Sicherheit und Benutzerfreundlichkeit als die Autorisierung mit Zugriffsschlüsseln. Microsoft empfiehlt, nach Möglichkeit die Azure AD-Autorisierung mit Web PubSub-Ressourcen zu verwenden, um den Zugriff mit minimal erforderlichen Berechtigungen sicherzustellen.

<a id="security-principal"></a>
 *[1] Sicherheitsprinzipal: ein Benutzer/eine Ressourcengruppe, eine Anwendung oder ein Dienstprinzipal, z. B. systemseitig zugewiesene Identitäten und benutzerseitig zugewiesene Identitäten.*

## <a name="overview-of-azure-ad-for-web-pubsub"></a>Übersicht über Azure AD für Web PubSub

Wenn ein Sicherheitsprinzipal versucht, auf eine Web PubSub-Ressource zuzugreifen, muss die Anforderung autorisiert werden. Bei Azure AD sind für den Zugriff auf eine Ressource zwei Schritte erforderlich. 

1. Zunächst muss der Sicherheitsprinzipal von Azure authentifiziert werden, um anschließend selbst ein OAuth 2.0-Token zurückzugeben. 
2. Danach wird das Token als Teil einer Anforderung an Web PubSub-Ressource übergeben und vom Dienst verwendet, um den Zugriff auf die angegebene Ressource zu autorisieren.

### <a name="client-side-authentication-while-using-azure-ad"></a>Clientseitige Authentifizierung bei Verwendung von Azure AD

Bei Verwendung von Zugriffsschlüsseln wird der Schlüssel von Ihrem Aushandlungsserver (oder der Funktions-App) und der Web PubSub-Ressource gemeinsam verwendet. Das bedeutet, dass der Web PubSub-Dienst die Verbindungsanforderung des Clients mit dem gemeinsam genutzten Schlüssel authentifizieren kann. Es gibt jedoch keinen gemeinsamen Schlüssel, wenn Sie für die Autorisierung Azure AD verwenden. 

Zur Lösung dieses Problems steht Ihnen eine REST-API zum Generieren des Clienttokens zur Verfügung, das zum Herstellen einer Verbindung mit dem Azure Web PubSub-Dienst verwendet werden kann.

1. Zunächst benötigt der Aushandlungsserver ein **AAD-Token** von Azure, um sich selbst zu authentifizieren.
1. Anschließend ruft der Aushandlungsserver die Web PubSub-Authentifizierungs-API mit dem **AAD-Token** auf, um ein **Clienttoken** abzurufen und an den Client zurückzugeben.
1. Danach verwendet der Client das **Clienttoken**, um eine Verbindung mit dem Azure Web PubSub-Dienst herzustellen.

Für die unterstützten Programmiersprachen stehen dafür Hilfsfunktionen (z. B. GenerateClientAccessUri) bereit.

## <a name="assign-azure-roles-for-access-rights"></a>Zuweisen von Azure-Rollen für Zugriffsrechte

Azure Active Directory (Azure AD) autorisiert die Berechtigungen für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung von Azure](../role-based-access-control/overview.md). Azure Web PubSub definiert einige in Azure integrierte Rollen mit allgemeinen Berechtigungssätzen für den Zugriff auf Web PubSub-Ressourcen. Außerdem können Sie benutzerdefinierte Rollen für den Zugriff auf Web PubSub-Ressourcen definieren.

### <a name="resource-scope"></a>Ressourcenumfang

Bevor Sie einem Sicherheitsprinzipal eine Azure RBAC-Rolle zuweisen, müssen Sie möglicherweise den Umfang des Zugriffs festlegen, den der Sicherheitsprinzipal haben soll. Es wird empfohlen, nur den kleinstmöglichen Bereich zu gewähren. Azure RBAC-Rollen, die in einem umfassenderen Bereich definiert sind, werden von den darunterliegenden Ressourcen geerbt.

Sie können den Zugriff auf Azure SignalR-Ressourcen auf den folgenden Ebenen einschränken, beginnend mit dem kleinstmöglichen Bereich:

- **Eine individuelle Ressource.** 

  In diesem Bereich gilt eine Rollenzuweisung nur für die Zielressource.

- **Eine Ressourcengruppe.** 

  In diesem Bereich gilt eine Rollenzuweisung für alle Ressourcen in der Ressourcengruppe.

- **Ein Abonnement.**

  In diesem Bereich gilt eine Rollenzuweisung für alle Ressourcen in allen Ressourcengruppen im Abonnement.

- **Eine Verwaltungsgruppe**. 

  In diesem Bereich gilt eine Rollenzuweisung für alle Ressourcen in allen Ressourcengruppen in sämtlichen Abonnements der Verwaltungsgruppe.

## <a name="azure-built-in-roles-for-web-pubsub-resources"></a>Integrierte Azure-Rollen für Web PubSub-Ressourcen

- `Web PubSub Service Owner`

    Vollzugriff mit Datenebenenberechtigungen, einschließlich REST-APIs für das Lesen/Schreiben und Authentifizierungs-APIs.

    Dies ist die am häufigsten verwendete Rolle zum Erstellen eines Upstreamservers.

- `Web PubSub Service Reader`

    Dient dem Zuweisen von Berechtigungen für schreibgeschützte REST-APIs für Web PubSub-Ressourcen.

    Sie wird normalerweise verwendet, wenn Sie ein Überwachungstool schreiben möchten, das **NUR** **SCHREIBGESCHÜTZTE** REST-APIs auf der Datenebene von Web PubSub aufruft.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen einer Azure-Anwendung und zum Verwenden der AAD-Authentifizierung finden Sie unter:
- [Autorisieren von Anforderungen aus Azure-Anwendungen für Web PubSub-Ressourcen mit Azure AD](howto-authorize-from-application.md)

Informationen zum Konfigurieren einer verwalteten Identität und zum Verwenden der AAD-Authentifizierung finden Sie unter:
- [Autorisieren von Anforderungen von verwalteten Identitäten für Web PubSub-Ressourcen mit Azure AD](howto-authorize-from-managed-identity.md)

Weitere Informationen zu den Rollen und Rollenzuweisungen finden Sie unter: 
- [Was ist die rollenbasierte Zugriffssteuerung in Azure?](../role-based-access-control/overview.md)

Informationen zum Erstellen benutzerdefinierter Rollen finden Sie unter: 
- [Schritte zum Erstellen einer benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)