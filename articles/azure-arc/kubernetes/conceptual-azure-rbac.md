---
title: 'Azure RBAC: Kubernetes mit Azure Arc-Unterstützung'
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dieser Artikel bietet eine konzeptionelle Übersicht über die Azure RBAC-Funktionalität auf Kubernetes mit Azure Arc-Unterstützung.
ms.openlocfilehash: 126e85eb312b977eb90e27971e8e65180542b069
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112679960"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure RBAC auf Kubernetes mit Azure Arc-Unterstützung

Die Kubernetes-Objekttypen [ClusterRoleBinding und RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) bieten Unterstützung bei der nativen Definition der Autorisierung in Kubernetes. Mit Azure RBAC können Sie unter Verwendung von Azure Active Directory (Azure AD) und Rollenzuweisungen in Azure Autorisierungsüberprüfungen für den Cluster steuern.

Mit diesem Feature können alle Vorteile von Azure-Rollenzuweisungen wie Aktivitätsprotokolle, die alle Azure RBAC-Änderungen an einer Azure-Ressource veranschaulichen, jetzt für Ihren Kubernetes-Cluster mit Azure Arc-Unterstützung angewendet werden.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Architektur: Azure RBAC auf Kubernetes mit Azure Arc-Unterstützung

[ ![Azure RBAC-Architektur](./media/conceptual-azure-rbac.png) ](./media/conceptual-azure-rbac.png#lightbox)

Um alle Autorisierungszugriffsüberprüfungen an den Autorisierungsdienst in Azure weiterzuleiten, wird ein Webhookserver ([Wächter](https://github.com/appscode/guard)) im Cluster bereitgestellt.

Der `apiserver` des Clusters ist so konfiguriert, dass eine [Webhooktokenauthentifizierung](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) und eine [Webhookautorisierung](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) verwendet werden, sodass `TokenAccessReview`- und `SubjectAccessReview`-Anforderungen an den Wächterwebhookserver weitergeleitet werden. Die `TokenAccessReview`- und `SubjectAccessReview`-Anforderungen werden durch Anforderungen an Kubernetes-Ressourcen ausgelöst, die an den `apiserver` gesendet werden.

Der Wächter richtet dann einen Aufruf vom Typ `checkAccess` an den Autorisierungsdienst in Azure, um festzustellen, ob die anfordernde Azure AD-Entität Zugriff auf die betreffende Ressource hat. 

Wenn eine Rolle in der Zuweisung diesen Zugriff zulässt, wird vom Autorisierungsdienstwächter eine `allowed`-Antwort gesendet. Der Wächter sendet wiederum eine `allowed`-Antwort an den `apiserver`, sodass die aufrufende Entität auf die angeforderte Kubernetes-Ressource zugreifen kann.


Wenn in der Zuweisung keine Rolle vorhanden ist, die diesen Zugriff zulässt, wird eine `denied`-Antwort vom Autorisierungsdienst an den Wächter gesendet. Der Wächter sendet eine `denied`-Antwort an den `apiserver` und meldet der aufrufenden Entität einen „403 – Unzulässig“-Fehler für die angeforderte Ressource.

## <a name="next-steps"></a>Nächste Schritte

* Nutzen Sie den Schnellstart zum [Verbinden eines Kubernetes-Clusters mit Azure Arc](./quickstart-connect-cluster.md).
* [Richten Sie Azure RBAC](./azure-rbac.md) in Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung ein.