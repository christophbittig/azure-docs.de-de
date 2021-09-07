---
title: Azure Relay-Authentifizierung und -Autorisierung | Microsoft-Dokumentation
description: Dieser Artikel bietet eine Übersicht über die SAS-Authentifizierung (Shared Access Signature) beim Azure Relay-Dienst.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: dbabee7f49e4f905c34a91dcb6095e7eab3faa0f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355573"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay-Authentifizierung und -Autorisierung
Es gibt zwei Möglichkeiten, den Zugriff auf Azure Relay-Ressourcen zu authentifizieren und zu autorisieren: Azure Active Directory (Azure AD) und Shared Access Signatures (SAS). In diesem Artikel erhalten Sie Informationen zur Verwendung dieser beiden Arten von Sicherheitsmechanismen.

## <a name="azure-active-directory-preview"></a>Azure Active Directory (Vorschau)
Die Azure AD-Integration für Azure Relay-Ressourcen bietet rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC), mit der Sie den Zugriff eines Clients auf Ressourcen differenziert steuern können. Mit Azure RBAC können einem Sicherheitsprinzipal Berechtigungen erteilt werden. Bei einem Sicherheitsprinzipal kann es sich um einen Benutzer, um eine Gruppe oder um einen Anwendungsdienstprinzipal handeln. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, um ein OAuth 2.0-Token zurückzugeben. Das Token kann zum Autorisieren einer Anforderung für den Zugriff auf eine Azure Relay-Ressource verwendet werden.

Weitere Informationen zur Authentifizierung mit Azure AD finden Sie in den folgenden Artikeln:
- [Authentifizieren mit verwalteten Dienstidentitäten](authenticate-managed-identity.md)
- [Authentifizieren aus einer Azure Active Directory-Anwendung](authenticate-application.md)

> [!IMPORTANT]
> Das Autorisieren von Benutzern oder Anwendungen mithilfe eines von Azure AD zurückgegebenen OAuth 2.0-Tokens bietet mehr Sicherheit und Benutzerfreundlichkeit als die Autorisierung per SAS (Shared Access Signature). Mit Azure AD ist es nicht erforderlich, Token in Ihrem Code zu speichern und potenzielle Sicherheitsrisiken einzugehen. Es wird empfohlen, Azure AD nach Möglichkeit mit Ihren Azure Relay-Anwendungen zu verwenden.

### <a name="built-in-roles"></a>Integrierte Rollen
Bei Azure Relay ist die Verwaltung der Namespaces und aller zugehörigen Ressourcen über das Azure-Portal und die Azure-Ressourcenverwaltungs-API bereits durch das Azure RBAC-Modell geschützt. Azure stellt die folgenden integrierten Azure-Rollen zum Autorisieren des Zugriffs auf einen Relay-Namespace bereit:

| Rolle | BESCHREIBUNG | 
| ---- | ----------- | 
| [Azure Relay-Besitzer](../role-based-access-control/built-in-roles.md#azure-relay-owner) | Mit dieser Rolle können Sie **Voll** zugriff auf Azure Relay-Ressourcen gewähren. |
| [Azure Relay-Listener](../role-based-access-control/built-in-roles.md#azure-relay-listener) | Mit dieser Rolle können Sie **Lausch- und Entitätslese**-zugriff auf Azure Relay-Ressourcen gewähren. |
| [Azure Relay-Absender](../role-based-access-control/built-in-roles.md#azure-relay-sender) | Mit dieser Rolle können Sie **Sende- und Entitätslese-** zugriff auf Azure Relay-Ressourcen gewähren. | 


## <a name="shared-access-signature"></a>Shared Access Signature (SAS)
Anwendungen können mithilfe der SAS-Authentifizierung (Shared Access Signature) bei Azure Relay authentifiziert werden. Die SAS-Authentifizierung ermöglicht Anwendungen die Authentifizierung beim Azure Relay-Dienst mithilfe eines Zugriffsschlüssels, der für den Namespace „Relay“ konfiguriert wird. Sie können diesen Schlüssel zum Generieren eines SAS-Tokens verwenden, das Clients für die Authentifizierung beim Relaydienst verwenden können.

Mit der [SAS-Authentifizierung](../service-bus-messaging/service-bus-sas.md) können Sie einem Benutzer Zugriff auf Azure Relay-Ressourcen mit spezifischen Rechten gewähren. Die SAS-Authentifizierung umfasst die Konfiguration eines kryptografischen Schlüssels mit den zugehörigen Rechten für eine Ressource. Clients können Zugriff auf diese Ressource erlangen, indem sie ein SAS-Token bereitstellen. Dieses setzt sich aus dem Ressourcen-URI, auf den zugegriffen wird, und einer Ablaufangabe zusammen, die mit dem konfigurierten Schlüssel signiert wird.

Sie können Schlüssel für SAS für einen Relaynamespace konfigurieren. Im Gegensatz zum Service Bus-Messaging unterstützen [Relay Hybrid Connections](relay-hybrid-connections-protocol.md) nicht autorisierte oder anonyme Sender. Wie in dem folgenden Screenshot des Portals gezeigt wird, können Sie den anonymen Zugriff auf die Entität bei der Erstellung aktivieren:

![Ein Dialogfeld mit der Bezeichnung „Hybridverbindung erstellen“ enthält das Textfeld „Name“ und das aktivierte Kontrollkästchen „Requires Client Authentication“ (Clientauthentifizierung erforderlich).][0]

Wenn Sie SAS verwenden möchten, können Sie ein [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)-Objekt für einen Relaynamespace konfigurieren, das aus den folgenden Eigenschaften besteht:

* *KeyName* , der die Regel identifiziert.
* *PrimaryKey* – einem kryptografischen Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *SecondaryKey* – einem kryptografischen Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *Rechten* , die die Auflistung der erteilten Lausch-, Sende- oder Verwaltungsrechte (Listen, Send, Manage) darstellen.

Autorisierungsregeln, die auf Namespace-Ebene konfiguriert werden, können Zugriff auf alle Relayverbindungen in einem Namespace für Clients mit Token erteilen, die mithilfe des entsprechenden Schlüssels signiert wurden. Bis zu zwölf solcher Autorisierungsregeln können für einen Relaynamespace konfiguriert werden. Standardmäßig wird für jeden Namespace bei der ersten Bereitstellung ein [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) -Objekt mit allen Rechten konfiguriert.

Für den Zugriff auf eine Entität erfordert der Client ein SAS-Token, das mithilfe einer bestimmten [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) generiert wurde. Das SAS-Token wird anhand des HMAC-SHA256-Codes einer Ressourcenzeichenfolge generiert. Diese  besteht aus dem Ressourcen-URI, auf den der Zugriff beansprucht wird, sowie aus einer Ablaufangabe mit einem kryptografischen Schlüssel, der der Autorisierungsregel zugeordnet ist.

Die Unterstützung der SAS-Authentifizierung für Azure Relay ist im Azure .NET SDK, Version 2.0 oder höher, inbegriffen. SAS umfasst Unterstützung für eine [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle APIs, die eine Verbindungszeichenfolge als Parameter akzeptieren, enthalten Unterstützung für SAS-Verbindungszeichenfolgen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu SAS finden Sie unter [SAS-Authentifizierung (Shared Access Signature) bei Service Bus](../service-bus-messaging/service-bus-sas.md) .
- Ausführliche Informationen zur Hybrid Connections-Funktion finden Sie im [Azure Relay Hybrid Connections-Protokollhandbuch](relay-hybrid-connections-protocol.md).
- Die entsprechenden Informationen zur Authentifizierung und Autorisierung über das Service Bus-Messaging finden Sie unter [Service Bus-Authentifizierung und -Autorisierung](../service-bus-messaging/service-bus-authentication-and-authorization.md). 

[0]: ./media/relay-authentication-and-authorization/hcanon.png