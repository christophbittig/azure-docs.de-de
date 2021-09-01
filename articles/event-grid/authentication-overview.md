---
title: Authentifizierung von Clients, die Ereignisse in benutzerdefinierten Event Grid-Themen, -Domänen oder -Partnernamespaces veröffentlichen.
description: Dieser Artikel beschreibt verschiedene Möglichkeiten zur Authentifizierung von Clients, die Ereignisse in benutzerdefinierten Event Grid-Themen, -Domänen oder -Partnernamespaces veröffentlichen.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 9e9718d6b39bd41cf91e610a01ce5f95bd0d5a18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339840"
---
# <a name="client-authentication-when-publishing-events-to-event-grid"></a>Clientauthentifizierung beim Veröffentlichen von Ereignissen in Event Grid
Authentifizierung für Clients, die Ereignisse in Event Grid veröffentlichen, wird mithilfe der folgenden Methoden unterstützt:

- Azure Active Directory (Azure AD)
- Zugriffsschlüssel oder Shared Access Signature (SAS)

## <a name="authenticate-using-azure-active-directory-preview"></a>Authentifizieren mit Azure Active Directory (Vorschau)
Die Azure AD-Integration für Event Grid-Ressourcen bietet rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure, mit der Sie den Zugriff eines Clients auf Ressourcen differenziert steuern können. Mit Azure RBAC können einem Sicherheitsprinzipal Berechtigungen erteilt werden. Bei einem Sicherheitsprinzipal kann es sich um einen Benutzer, um eine Gruppe oder um einen Anwendungsdienstprinzipal handeln. Der Sicherheitsprinzipal wird von Azure AD authentifiziert, um ein OAuth 2.0-Token zurückzugeben. Das Token kann verwendet werden, um eine Anforderung für den Zugriff auf Event Grid-Ressourcen (Themen, Domänen oder Partnernamespaces) zu autorisieren. Ausführliche Informationen finden Sie unter [Authentifizieren und Autorisieren mit Microsoft Identity Platform](authenticate-with-active-directory.md).


> [!IMPORTANT]
> Die Authentifizierung und Autorisierung von Benutzern oder Anwendungen mithilfe von Azure AD-Identitäten bietet höhere Sicherheit und Benutzerfreundlichkeit als schlüsselbasierte SAS-Authentifizierung (Shared Access Signatures). Mit Azure AD ist es nicht erforderlich, für Authentifizierung verwendete Geheimnisse in Ihrem Code zu speichern und potenzielle Sicherheitsrisiken einzugehen. Es wird dringend empfohlen, Azure AD mit Ihren Azure Event Grid-Ereignisveröffentlichungsanwendungen zu verwenden.

> [!NOTE]
> Azure AD-Authentifizierungsunterstützung durch Azure Event Grid wurde als Vorschauversion veröffentlicht. Azure Event Grid in Kubernetes unterstützt noch keine Azure AD-Authentifizierung. 

## <a name="authenticate-using-access-keys-and-shared-access-signatures"></a>Authentifizieren mit Zugriffsschlüsseln und SAS (Shared Access Signatures)
Sie können Clients, die Ereignisse in Azure Event Grid-Themen, -Domänen oder -Partnernamespaces veröffentlichen, mit einem **Zugriffsschlüssel** oder **SAS-Token (Shared Access Signature)** authentifizieren. Weitere Informationen finden Sie unter [Verwenden von Zugriffsschlüsseln oder SAS (Shared Access Signatures)](authenticate-with-access-keys-shared-access-signatures.md). 
   

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wird Authentifizierung beim **Veröffentlichen** von Ereignissen in Event Grid (Ereigniseingang) behandelt. Weitere Informationen zur Authentifizierung bei der **Übermittlung** von Ereignissen (Ereignisausgang) finden Sie unter [Authentifizieren der Ereignisübermittlung bei Ereignishandlern](security-authentication.md). 

