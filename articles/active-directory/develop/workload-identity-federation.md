---
title: Verbund Workload Identity (Arbeitslast-Identität)
titleSuffix: Microsoft identity platform
description: Verwenden Sie den Verbund Workload Identity, um Workloads, die außerhalb von Azure ausgeführt werden, Zugriff auf die von Azure geschützten AD-Ressourcen ohne Verwendung von Geheimnissen oder Zertifikaten zu gewähren. Dadurch müssen Entwickler langlebige Geheimnisse oder Zertifikate außerhalb von Azure nicht mehr speichern und verwalten.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: ryanwi
ms.reviewer: keyam, udayh, vakarand
ms.custom: aaddev
ms.openlocfilehash: ff1161e41965c92b469372038c4ba0cd602ee074
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095321"
---
# <a name="workload-identity-federation-preview"></a>Verbund Workload Identity (Vorschau)
Dieser Abschnitt gewährt eine Übersicht über den Verbund Workload Identity für Software-Workloads. Mithilfe des Verbunds Workload Identity können Sie auf vom Verzeichnisdienst Azure Active Directory (Azure AD) geschützte Ressourcen zugreifen, ohne Geheimnisse verwalten zu müssen (für unterstützte Szenarien).

Sie können den Verbund Workload Identity in solchen Fällen wie GitHub-Aktionen verwenden.

## <a name="why-use-workload-identity-federation"></a>Warum sollten Sie den Verbund Workload Identity (Arbeitslast-Identität) nutzen?

In der Regel benötigt eine Software-Arbeitslast (Workload) (z. B. eine Anwendung, ein Dienst, ein Skript oder eine containerbasierte Anwendung) eine Identität, um Ressourcen zu authentifizieren und darauf zu zugreifen oder mit anderen Diensten zu kommunizieren.  Wenn diese Workloads in Azure ausgeführt werden, können Sie verwaltete Identitäten verwenden und die Azure-Plattform verwaltet die Anmeldeinformationen für Sie.  Für eine Softwareworkload, die außerhalb von Azure ausgeführt wird, müssen Sie Anwendungsanmeldeinformationen (ein Geheimnis oder Zertifikat) für den Zugriff auf Azure-AD-geschützte Ressourcen (z. B. Azure, Microsoft Graph, Microsoft 365 oder Ressourcen von Drittanbietern) verwenden.  Diese Anmeldeinformationen stellen ein Sicherheitsrisiko dar und müssen sicher gespeichert und regelmäßig durchgewechselt werden. Außerdem besteht das Risiko einer Dienstausfallzeit, wenn die Anmeldeinformationen ablaufen.

Sie verwenden den Verbund Workload Identity, um eine Azure-AD-App-Registrierung zu konfigurieren, um Token von einem externen Identitätsanbieter (IdP) zu vertrauen, z. B. GitHub.  Nachdem diese Vertrauensstellung erstellt wurde, kann Ihre Softwareworkload vertrauenswürdige Token aus dem externen IdP gegen Zugriffstoken von der Microsoft Identity Platform austauschen.  Ihre Softwareworkload verwendet dann dieses Zugriffstoken für den Zugriff auf von Azure AD geschützte Ressourcen, auf die der Workload Zugriff gewährt wurde. Dadurch entfällt der Wartungsaufwand der manuellen Verwaltung von Anmeldeinformationen und das Risiko, Geheimnisse preiszugeben oder Zertifikate ablaufen zu lassen, wird ausgeschaltet.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

Die folgenden Szenarien werden für den Zugriff auf von Azure AD geschützte Ressourcen mithilfe eines Verbunds Workload Identity unterstützt:

- GitHub-Aktionen. [Konfigurieren Sie zunächst eine Vertrauensstellung](workload-identity-federation-create-trust-github.md) zwischen Ihrer App in Azure AD und einem GitHub-Repository im Azure-Portal oder mithilfe von Microsoft Graph. [Konfigurieren Sie einen GitHub-Aktionen-Arbeitsablauf (Actions worklfow)](/azure/developer/github/connect-from-azure), um ein Zugriffstoken vom Microsoft-Identitäts-Provide abzurufen und auf Azure-Ressourcen zuzugreifen.
- Workloads, die auf Compute-Plattformen außerhalb von Azure ausgeführt werden. [Konfigurieren Sie eine Vertrauensstellung](workload-identity-federation-create-trust.md) zwischen ihrer Azure-AD-Anwendungsregistrierung und dem externen IdP für Ihre Compute-Plattform. Sie können von dieser Plattform ausgestellte Token verwenden, um sich mit der Microsoft Identity Platform zu authentifizieren und APIs im Microsoft-Ökosystem aufzurufen. Verwenden Sie den [Client-Anmeldeinformationen-Flow](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential), um einen Zugriffstoken von der Microsoft Identity Platform zu bekommen. Übergeben Sie dabei das JWT des Identitäts-Providers, anstatt selbst ein Zugriffstoken mithilfe eines gespeicherten Zertifikats zu erstellen.

## <a name="how-it-works"></a>Funktionsweise
Erstellen Sie eine Vertrauensstellung zwischen dem externen IdP und einer App in Azure AD, indem Sie die [Anmeldeinformationen einer föderierten Identität](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true) konfigurieren. Die Anmeldeinformationen für die föderierte Identität werden verwendet, um anzugeben, welches Token vom externen Identitätsanbieter von Ihrer Anwendung als vertrauenswürdig eingestuft werden soll. Sie konfigurieren die Anmeldeinformationen für die föderierte Identität in Ihrer App-Registrierung im Azure-Portal oder über Microsoft Graph.  Die Schritte zum Konfigurieren der Vertrauensstellung unterscheiden sich je nach Szenario und externem IdP.

Der Arbeitsablauf (Workflow) zum Austauschen eines externen Tokens gegen ein Zugriffstoken ist jedoch in allen Fällen identisch. Das folgende Diagramm zeigt den allgemeinen Arbeitsablauf einer Workload, die ein externes Token gegen ein Zugriffstoken austauscht und dann auf von Azure AD geschützte Ressourcen zugreift.

:::image type="content" source="media/workload-identity-federation/workflow.svg" alt-text="Zeigt ein externes Token, das gegen ein Zugriffstoken ausgetauscht wurde und auf Azure zugreift." border="false":::

1. Die externe Workload (z. B. ein GitHub-Aktions-Arbeitsablauf) fordert ein Token vom externen IdP an (z. B. GitHub).
1. Der externe IdP stellt ein Token für die externe Workload aus.
1. Die externe Workload (z. B. die Anmeldeaktion in einem GitHub-Workflow) [sendet das Token an die Microsoft Identity Platform](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) und fordert ein Zugriffstoken an.
1. Die Microsoft Identity Platform überprüft die [Vertrauensstellung](workload-identity-federation-create-trust.md) bei der App-Registrierung und überprüft das externe Token anhand der Aussteller-URL von Open ID Connect (OIDC) für den externen IdP.
1. Wenn die Überprüfungen zufriedenstellend ausgeführt worden sind, gibt die Microsoft Identity Platform einen Zugriffstoken für die externe Workload aus.
1. Die externe Workload greift auf von Azure AD geschützte Ressourcen mithilfe des Zugriffstokens der Microsoft Identity Platform zu. Ein GitHub-Aktions-Workflow verwendet z. B. das Zugriffstoken, um eine Web-App in einem Azure-App-Service zu veröffentlichen.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über die Funktionsweise des Verbunds Workload Identity:
- Wie nutzt Azure AD den [OAuth-2.0-Grant Client-Anmeldedaten (client credentials)](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) und eine Client-Assertion, die von einem anderen IdP ausgegeben wurde, um ein Token abzurufen.
- Hier erfahren Sie, wie Sie [Anmeldeinformationen einer föderierten Identität](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true) für eine App-Registrierung mithilfe von Microsoft Graph erstellen, löschen, einholen oder aktualisieren.
- Lesen Sie die [Dokumentation über GitHub-Aktionen](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure), um mehr über das Konfigurieren Ihres GitHub-Actions-Workflows für das Abrufen eines Zugriffstokens vom Microsoft-Identitäts-Provider und den Zugriff auf Azure-Ressourcen zu erfahren.
