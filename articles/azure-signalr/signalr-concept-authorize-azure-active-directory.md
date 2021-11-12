---
title: Autorisieren des Zugriffs mit Azure Active Directory für Azure SignalR Service
description: Dieser Artikel bietet Informationen zum Autorisieren des Zugriffs auf Azure SignalR Service-Ressourcen mit Azure Active Directory.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 0e644f792e47243f3c6924cd8546efb5661a9193
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478751"
---
# <a name="authorize-access-with-azure-active-directory-for-azure-signalr-service"></a>Autorisieren des Zugriffs mit Azure Active Directory für Azure SignalR Service

Azure SignalR Service unterstützt die Verwendung von Azure Active Directory (Azure AD), um Anforderungen an Azure SignalR Service-Ressourcen zu autorisieren. Azure AD ermöglicht Ihnen die Verwendung der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC), um einem Sicherheitsprinzipal<sup>[<a href="#security-principal">1</a>]</sup> Berechtigungen zu erteilen. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, das ein OAuth 2.0-Token übermittelt. Das Token kann dann verwendet werden, um eine Anforderungan die SignalR-Ressource zu autorisieren.

Die Autorisierung von Anfragen gegen SignalR mit Azure AD bietet eine höhere Sicherheit und Benutzerfreundlichkeit als die Autorisierung mit Access Key. Microsoft empfiehlt die Verwendung von Azure AD-Autorisierung mit Ihren SignalR-Ressourcen, wenn dies möglich ist, um den Zugriff mit den geringsten erforderlichen Berechtigungen sicherzustellen.

<a id="security-principal"></a>
 *[1] Sicherheitsprinzipal: ein Benutzer/eine Ressourcengruppe, eine Anwendung oder ein Dienstprinzipal, z. B. systemseitig zugewiesene Identitäten und benutzerseitig zugewiesene Identitäten.*

## <a name="overview-of-azure-ad-for-signalr"></a>Übersicht über Azure AD für SignalR

Wenn ein Sicherheitsprinzipal versucht, auf eine SignalR-Ressource zuzugreifen, muss die Anforderung autorisiert werden. Bei Azure AD sind für den Zugriff auf eine Ressource 2 Schritte erforderlich. 

1. Zunächst muss der Sicherheitsprinzipal von Azure authentifiziert werden, von wo anschließend ein OAuth 2.0-Token übermittelt wird. 
2. Anschließend wird das Token als Teil einer Anforderung an den Tabellendienst übergeben und von diesem verwendet, um den Zugriff auf die angegebene Ressource zu autorisieren.

### <a name="client-side-authentication-while-using-azure-ad"></a>Clientseitige Authentifizierung mit Azure AD

Bei der Verwendung von Access Key wird der Schlüssel zwischen Ihrem App-Server (oder Function App) und der SignalR-Ressource geteilt, was dazu führt, dass der SignalR-Dienst die Verbindungsanforderung des Clients mit dem geteilten Schlüssel authentifizieren kann. Es gibt jedoch keinen gemeinsamen Schlüssel, wenn Sie für die Autorisierung Azure AD verwenden. 

Um dieses Problem zu lösen, haben wir einen **temporären Zugriffsschlüssel** eingeführt, der zum Signieren von Token für Clientverbindungen verwendet werden kann. Der Workflow enthält vier Schritte.

1. Zunächst benötigt der Sicherheitsprinzipal ein OAuth 2.0-Token von Azure, um sich selbst zu authentifizieren.
2. Zweitens ruft der Sicherheitsprinzipal die SignalR-Authentifizierungs-API auf, um einen **temporären Zugriffsschlüssel** abzurufen.
3. Als nächstes signiert der Sicherheitsprinzipal ein Client-Token mit dem **temporären Zugangsschlüssel** für Client-Verbindungen während des Dialogs.
4. Schließlich verwendet der Client das Clienttoken, um eine Verbindung mit Azure SignalR-Ressourcen herzustellen.

Der **temporäre Zugriffsschlüssel** läuft in 90 Minuten ab, weshalb es sich empfiehlt, einen neuen Schlüssel zu erhalten und den alten einmal pro Stunde zu rotieren. 

Der Workflow ist in unserem [Server SDK](https://github.com/Azure/azure-signalr)integriert.

## <a name="assign-azure-roles-for-access-rights"></a>Zuweisen von Azure-Rollen für Zugriffsrechte

Azure Active Directory (Azure AD) autorisiert die Berechtigungen für den Zugriff auf geschützte Ressourcen über die [rollenbasierte Zugriffssteuerung von Azure](../role-based-access-control/overview.md). Azure SignalR definiert eine Reihe von in Azure integrierten Rollen, die gemeinsame Sätze von Berechtigungen für den Zugriff auf SignalR-Ressourcen umfassen. Sie können auch eigene Rollen für den Zugriff auf SignalR-Ressourcen definieren.

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

## <a name="azure-built-in-roles-for-signalr-resources"></a>Integrierte Azure-Rollen für SignalR-Ressourcen

- [SignalR-App-Server](../role-based-access-control/built-in-roles.md#signalr-app-server-preview)

    Zugriff auf die Websocket-Verbindungserstellungs-API und Authentifizierungs-APIs.
    
    Dies ist die am häufigsten verwendete Rolle für einen App-Server.

- [SignalR Service-Besitzer](../role-based-access-control/built-in-roles.md#signalr-service-owner)

    Vollständiger Zugriff auf alle Datenebenen-APIs, einschließlich aller REST-APIs, WebSocket-Verbindungsaufbau-API und Auth-APIs.

    Der **Serverlose Modus** sollte diese Rolle verwenden, um die Autorisierung mit Azure AD zu unterstützen, da er sowohl REST-API-Berechtigungen als auch Auth-API-Berechtigungen erfordert.

- [SignalR-REST-API-Besitzer](../role-based-access-control/built-in-roles.md#signalr-rest-api-owner)

    Vollzugriff auf REST-APIs auf Datenebene.

    Er wird normalerweise verwendet, wenn Sie ein Verwaltungswerkzeug schreiben möchten, das Verbindungen und Gruppen verwaltet, jedoch **NICHT** Verbindungen herstellt oder Auth APIs aufruft.

- [SignalR-REST-API-Leser](../role-based-access-control/built-in-roles.md#signalr-rest-api-reader)

    Schreibgeschützter Zugriff auf REST-APIs auf Datenebene.

    Er wird normalerweise verwendet, wenn man ein Überwachungswerkzeug schreiben möchte, das **NUR** **SCHREIBGESCHÜTZE** REST-APIs auf der SignalR Datenebene aufruft.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen einer Azure-Anwendung und zum Verwenden der Azure AD-Authentifizierung finden Sie unter
- [Autorisieren von Anforderungen aus Azure-Anwendungen für SignalR-Ressourcen mit Azure AD](signalr-howto-authorize-application.md)

Informationen zum Konfigurieren einer verwalteten Identität und zum Verwenden der Azure AD-Authentifizierung finden Sie unter
- [Autorisieren von Anforderungen von verwalteten Identitäten für SignalR-Ressourcen mit Azure AD](signalr-howto-authorize-managed-identity.md)

Weitere Informationen zu den Rollen und Rollenzuweisungen finden Sie unter 
- [Worum handelt es sich bei der Azure rollenbasierten Zugriffskontrolle (Azure RBAC)?](../role-based-access-control/overview.md).

Informationen zum Erstellen benutzerdefinierter Rollen finden Sie unter 
- [Schritte zum Erstellen einer benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)