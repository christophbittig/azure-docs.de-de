---
title: Azure Service Bus-Authentifizierung und -Autorisierung | Microsoft-Dokumentation
description: Authentifizieren Sie Service Bus-Apps mit Shared Access Signature-Authentifizierung (SAS).
ms.topic: article
ms.date: 09/15/2021
ms.openlocfilehash: 1b1cc20df861f657ed9fff4704862e9ee36965a2
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130002072"
---
# <a name="service-bus-authentication-and-authorization"></a>Service Bus-Authentifizierung und -Autorisierung
Es gibt zwei Möglichkeiten, den Zugriff auf Azure Service Bus-Ressourcen zu authentifizieren und zu autorisieren: Azure Active Directory (Azure AD) und Shared Access Signatures (SAS). In diesem Artikel erhalten Sie Informationen zur Verwendung dieser beiden Arten von Sicherheitsmechanismen. 

## <a name="azure-active-directory"></a>Azure Active Directory
Die Azure AD-Integration für Service Bus-Ressourcen bietet rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure, mit der Sie den Zugriff eines Clients auf Ressourcen differenziert steuern können. Mit Azure RBAC können einem Sicherheitsprinzipal Berechtigungen erteilt werden. Bei einem Sicherheitsprinzipal kann es sich um einen Benutzer, um eine Gruppe oder um einen Anwendungsdienstprinzipal handeln. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, um ein OAuth 2.0-Token zurückzugeben. Das Token kann zum Autorisieren einer Anforderung für den Zugriff auf eine Service Bus-Ressource (Warteschlange, Thema usw.) verwendet werden.

Weitere Informationen zur Authentifizierung mit Azure AD finden Sie in den folgenden Artikeln:

- [Authentifizieren mit verwalteten Dienstidentitäten](service-bus-managed-service-identity.md)
- [Authentifizieren über eine Anwendung](authenticate-application.md)

> [!NOTE]
> Die [Service Bus REST-API](/rest/api/servicebus/) unterstützt die OAuth-Authentifizierung mit Azure AD.

> [!IMPORTANT]
> Das Autorisieren von Benutzern oder Anwendungen mithilfe eines von Azure AD zurückgegebenen OAuth 2.0-Tokens bietet mehr Sicherheit und Benutzerfreundlichkeit als die Autorisierung per SAS (Shared Access Signature). Mit Azure AD ist es nicht erforderlich, Token in Ihrem Code zu speichern und potenzielle Sicherheitsrisiken einzugehen. Es wird empfohlen, Azure AD nach Möglichkeit mit Ihren Azure Service Bus-Anwendungen zu verwenden. 

## <a name="shared-access-signature"></a>Shared Access Signature (SAS)
Mit der [SAS-Authentifizierung](service-bus-sas.md) können Sie einem Benutzer Zugriff auf Service Bus-Ressourcen mit spezifischen Rechten gewähren. Die SAS-Authentifizierung in Service Bus umfasst die Konfiguration eines kryptografischen Schlüssels mit den zugehörigen Rechten für eine Service Bus-Ressource. Clients können Zugriff auf diese Ressource erlangen, indem sie ein SAS-Token bereitstellen. Dieses setzt sich aus dem Ressourcen-URI, auf den zugegriffen wird, und einer Ablaufangabe zusammen, die mit dem konfigurierten Schlüssel signiert wird.

Sie können Schlüssel für SAS für einen Service Bus-Namespace konfigurieren. Der betreffende Schlüssel gilt für alle Messagingentitäten innerhalb des jeweiligen Namespace. Sie können auch Schlüssel für Service Bus-Warteschlangen und -Themen konfigurieren. SAS wird in [Azure Relay](../azure-relay/relay-authentication-and-authorization.md) ebenfalls unterstützt.

Wenn Sie SAS verwenden möchten, können Sie eine Autorisierungsregel für gemeinsamen Zugriff für einen Namespace, eine Warteschlange oder ein Thema konfigurieren. Diese Regel besteht aus den folgenden Elementen:

* *KeyName*: identifiziert die Regel.
* *PrimaryKey*: ein kryptografischer Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *SecondaryKey*: ein kryptografischer Schlüssel, der zum Signieren/Überprüfen von SAS-Token verwendet wird.
* *Rechte*: stellt die Auflistung der erteilten Lausch-, Sende- oder Verwaltungsrechte (**Listen**, **Send**, **Manage**) dar.

Autorisierungsregeln, die auf Namespace-Ebene konfiguriert werden, können Zugriff auf alle Entitäten in einem Namespace für Clients mit Token erteilen, die mithilfe des entsprechenden Schlüssels signiert wurden. Sie können bis zu zwölf solcher Autorisierungsregeln für einen Service Bus-Namespace, eine Service Bus-Warteschlange oder ein Service Bus-Thema konfigurieren. Standardmäßig wird für jeden Namespace bei der ersten Bereitstellung eine Autorisierungsregel für gemeinsamen Zugriff mit allen Rechten konfiguriert.

Für den Zugriff auf eine Entität erfordert der Client ein SAS-Token, das mithilfe einer bestimmten Autorisierungsregel für gemeinsamen Zugriff generiert wurde. Das SAS-Token wird anhand des HMAC-SHA256-Codes einer Ressourcenzeichenfolge generiert. Diese  besteht aus dem Ressourcen-URI, auf den der Zugriff beansprucht wird, sowie aus einer Ablaufangabe mit einem kryptografischen Schlüssel, der der Autorisierungsregel zugeordnet ist.

Die Unterstützung der SAS-Authentifizierung für Service Bus ist im Azure .NET SDK, Version 2.0 oder höher, enthalten. SAS beinhaltet die Unterstützung einer Autorisierungsregel für gemeinsamen Zugriff. Alle APIs, die eine Verbindungszeichenfolge als Parameter akzeptieren, enthalten Unterstützung für SAS-Verbindungszeichenfolgen.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Authentifizierung mit Azure AD finden Sie in den folgenden Artikeln:

- [Authentifizierung mit verwalteten Identitäten](service-bus-managed-service-identity.md)
- [Authentifizierung aus einer Anwendung](authenticate-application.md)

Weitere Informationen zur Authentifizierung mit SAS finden Sie in den folgenden Artikeln:

- [Authentifizierung mit SAS](service-bus-sas.md)
