---
title: Verwalten der Authentifizierung in Microsoft Azure Maps
titleSuffix: Azure Maps
description: Machen Sie sich mit der Authentifizierung von Azure Maps vertraut. Sehen Sie, welcher Ansatz in welchem Szenario am besten funktioniert. Erfahren Sie, wie Sie das Portal zum Anzeigen von Authentifizierungseinstellungen verwenden.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/10/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
custom.ms: subject-rbac-steps
ms.openlocfilehash: d087daf38c455fd4d87ea571ff5f3a0ab9ad0527
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454775"
---
# <a name="manage-authentication-in-azure-maps"></a>Verwalten der Authentifizierung in Azure Maps

Wenn Sie ein Azure Maps-Konto erstellen, werden Schlüssel und eine Client-ID generiert. Schlüssel und Client-ID werden verwendet, um die Azure Active Directory-Authentifizierung (Azure AD) und die Authentifizierung mit gemeinsam verwendeten Schlüsseln zu unterstützen.

## <a name="view-authentication-details"></a>Anzeigen von Authentifizierungsdetails

 >[!IMPORTANT]
 >Sie sollten den Primärschlüssel als Abonnementschlüssel verwenden, wenn Sie [Azure Maps mithilfe der Authentifizierung mit gemeinsam verwendetem Schlüssel aufrufen](./azure-maps-authentication.md#shared-key-authentication). Der Sekundärschlüssel kann in Szenarien wie Änderungen beim Schlüsselrollover verwendet werden. Weitere Informationen finden Sie unter [Authentifizierung in Azure Maps](./azure-maps-authentication.md).

So zeigen Sie Ihre Azure Maps-Authentifizierungsdetails an:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Navigieren Sie zum Menü des Azure-Portals. Wählen Sie **Alle Ressourcen** und dann Ihr Azure Maps-Konto aus.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/select-all-resources.png" alt-text="Auswählen des Azure Maps-Kontos.":::

3. Wählen Sie im linken Bereich unter **Einstellungen** die Option **Authentifizierung** aus.

      :::image type="content" border="true" source="./media/how-to-manage-authentication/view-authentication-keys.png" alt-text="Authentifizierungsdetails.":::

## <a name="choose-an-authentication-category"></a>Auswählen einer Authentifizierungskategorie

Abhängig von den Anwendungsanforderungen gibt es bestimmte Möglichkeiten der Anwendungssicherheit. Azure AD definiert Authentifizierungskategorien zur Unterstützung einer breiten Palette von Authentifizierungsflows. Informationen zum Auswählen der besten Kategorie für Ihre Anwendung finden Sie unter [Anwendungskategorien](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories).

> [!NOTE]
> Selbst wenn Sie die Authentifizierung mit gemeinsam verwendetem Schlüssel verwenden, hilft Ihnen das Verständnis von Kategorien und Szenarien dabei, die Anwendung zu schützen.

## <a name="choose-an-authentication-and-authorization-scenario"></a>Auswählen eines Authentifizierungs- und Autorisierungsszenarios

In der folgenden Tabelle werden gängige Authentifizierungs- und Autorisierungsszenarien in Azure Maps skizziert. Verwenden Sie die Links, um ausführliche Konfigurationsinformationen zu jedem Szenario zu erhalten.

> [!IMPORTANT]
> Für Produktionsanwendungen sollte Azure AD mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, Azure RBAC) implementiert werden.

| Szenario                                                                                    | Authentifizierung | Authorization | Entwicklungsaufwand | Betriebsaufwand |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Vertrauenswürdiger Daemon/nicht interaktive Clientanwendung](./how-to-secure-daemon-app.md)        | Gemeinsam verwendeter Schlüssel     | –           | Medium             | High               |
| [Vertrauenswürdiger Daemon/nicht interaktive Clientanwendung](./how-to-secure-daemon-app.md)        | Azure AD       | High          | Niedrig                | Medium             |
| [Schützen einer Single-Page-Webanwendung mit interaktiver einmaliger Anmeldung](./how-to-secure-spa-users.md) | Azure AD       | High          | Medium             | Medium             |
| [Single-Page-Webanwendung für mit nicht interaktiver Anmeldung](./how-to-secure-spa-app.md)      | Azure AD       | High          | Medium             | Medium             |
| [Webanwendung mit interaktiver einmaliger Anmeldung](./how-to-secure-webapp-users.md)          | Azure AD       | High          | High               | Medium             |
| [IoT-Gerät/Gerät mit eingeschränkter Eingabe](./how-to-secure-device-code.md)                     | Azure AD       | High          | Medium             | Medium             |

## <a name="view-built-in-azure-maps-role-definitions"></a>Anzeigen der integrierten Azure Maps-Rollendefinitionen

So zeigen Sie die integrierte Azure Maps-Rollendefinition an:

1. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.

2. Wählen Sie die Registerkarte **Rollen** aus.

3. Geben Sie im Suchfeld **Azure Maps** ein.

In den Ergebnissen werden die verfügbaren integrierten Rollendefinitionen für Azure Maps angezeigt.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-role-definitions.png" alt-text="Anzeigen der integrierten Azure Maps-Rollendefinitionen.":::

## <a name="view-role-assignments"></a>Anzeigen von Rollenzuweisungen

Um Benutzer und Apps anzuzeigen, denen Zugriff für Azure Maps gewährt wurde, navigieren Sie zu **Zugriffssteuerung (IAM)** . Wählen Sie dort die Option **Rollenzuweisungen** aus, und filtern Sie dann nach **Azure Maps**.

1. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.

2. Klicken Sie auf die Registerkarte **Rollenzuweisungen**.

3. Geben Sie im Suchfeld **Azure Maps** ein.

In den Ergebnissen werden die aktuellen Azure Maps-Rollenzuweisungen angezeigt.

:::image type="content" border="true" source="./media/how-to-manage-authentication/view-amrbac.png" alt-text="Anzeigen von integrierten Benutzern und Apps, denen Zugriff gewährt wurde.":::

## <a name="request-tokens-for-azure-maps"></a>Anfordern von Token für Azure Maps

Anfordern eines Tokens vom Azure AD-Tokenendpunkt. Verwenden Sie in Ihrer Azure AD-Anforderung die folgenden Details:

| Azure-Umgebung      | Azure AD-Tokenendpunkt             | Azure-Ressourcen-ID              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Öffentliche Azure-Cloud     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Azure Government-Cloud | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Weitere Informationen zum Anfordern von Zugriffstoken aus Azure AD für Benutzer und Dienstprinzipale finden Sie unter [Was ist Authentifizierung?](../active-directory/develop/authentication-vs-authorization.md).  Spezifische Szenarien finden Sie in der [Tabelle der Szenarien](./how-to-manage-authentication.md#choose-an-authentication-and-authorization-scenario).

## <a name="manage-and-rotate-shared-keys"></a>Verwalten und Rotieren von gemeinsam verwendeten Schlüsseln

Ihre Azure Maps-Abonnementschlüssel ähneln einem Stammkennwort für Ihr Azure Maps-Konto. Achten Sie darauf, die Abonnementschlüssel immer gut zu schützen. Verwenden Sie Azure Key Vault zum sicheren Verwalten und Rotieren Ihrer Schlüssel. Geben Sie Zugriffsschlüssel nicht an andere Benutzer weiter, vermeiden Sie das Hartcodieren, und speichern Sie die Schlüssel nicht als Klartext, auf den andere Benutzer Zugriff haben. Wenn Sie der Meinung sind, dass Ihre Schlüssel möglicherweise kompromittiert wurden, rotieren Sie sie.

> [!NOTE]
> Wenn möglich, sollten Sie Azure AD anstelle des gemeinsam genutzten Schlüssels verwenden, um Anforderungen zu autorisieren. Azure AD bietet eine bessere Sicherheit als ein gemeinsam verwendeter Schlüssel und ist einfacher zu verwenden.

### <a name="manually-rotate-subscription-keys"></a>Manuelles Rotieren von Abonnementschlüsseln

Um die Sicherheit Ihres Azure Maps-Kontos zu gewährleisten, sollten Sie Ihre Abonnementschlüssel regelmäßig rotieren. Verwenden Sie nach Möglichkeit Azure Key Vault zum Verwalten Ihrer Zugriffsschlüssel. Wenn Sie Key Vault nicht verwenden, müssen Sie die Schlüssel manuell rotieren.

Es werden zwei Abonnementschlüssel zugewiesen, sodass Sie Ihre Schlüssel rotieren können. Durch das Vorhandensein von zwei Schlüsseln ist sichergestellt, dass der Zugriff Ihrer Anwendung auf Azure Maps während des Prozesses erhalten bleibt.

So rotieren Sie Ihre Azure Maps-Abonnementschlüssel im Azure-Portal

1. Aktualisieren Sie Ihren Anwendungscode so, dass er auf den sekundären Schlüssel für das Azure Maps-Konto verweist, und stellen Sie ihn bereit.
2. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrem Azure Maps-Konto.
3. Wählen Sie unter **Einstellungen** die Option **Authentifizierung** aus.
4. Klicken Sie auf die Schaltfläche **Neu generieren** neben dem primären Schlüssel, um den primären Schlüssel für Ihr Azure Maps-Konto neu zu generieren.
5. Aktualisieren Sie Ihren Anwendungscode so, dass auf den neuen Primärschlüssel verwiesen wird, und stellen Sie ihn bereit.
6. Generieren Sie den sekundären Zugriffsschlüssel auf die gleiche Weise neu.

> [!WARNING]
> Sie sollten in allen Ihren Anwendungen nicht mehrere Schlüssel gleichzeitig verwenden. Wenn Sie „Key 1“ an einigen Stellen und „Key 2“ an anderen verwenden, können Sie die Verwendung der Schlüssel nicht wechseln, ohne dass einige Anwendungen den Zugriff verlieren.

## <a name="next-steps"></a>Nächste Schritte

Suchen der API-Nutzungsmetriken für Ihr Azure Maps-Konto:
> [!div class="nextstepaction"]
> [Anzeigen von Nutzungsmetriken](how-to-view-api-usage.md)

Beispiele für die Integration von Azure AD in Azure Maps:

> [!div class="nextstepaction"]
> [Beispiele für die Azure AD-Authentifizierung](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
