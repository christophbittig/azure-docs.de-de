---
title: Autorisieren von Anforderungen von verwalteten Identitäten für Web PubSub-Ressourcen mit Azure AD
description: Dieser Artikel enthält Informationen zum Autorisieren von Anforderungen von verwalteten Identitäten an Web PubSub-Ressourcen mit Azure AD.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 7f0b5cff34b74953970672d0d48c4f027f4811b0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997740"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-managed-identities"></a>Autorisieren von Anforderungen von verwalteten Identitäten für Web PubSub-Ressourcen mit Azure AD
Der Azure Web PubSub-Dienst unterstützt die Azure AD-Authentifizierung (Azure Active Directory) von Anforderungen von [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md). 

In diesem Artikel erfahren Sie, wie Sie Ihre Web PubSub-Ressource konfigurieren. Außerdem finden Sie hier Code, mit dem Sie Anforderungen an eine Web PubSub-Ressource von einer verwalteten Identität autorisieren können.

## <a name="configure-managed-identities"></a>Konfigurieren von verwalteten Identitäten

Der erste Schritt besteht darin, verwaltete Identitäten zu konfigurieren.

Dies ist ein Beispiel für die Konfiguration von `System-assigned managed identity` auf einem `Virtual Machine` unter Verwendung des Azure-Portals.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), suchen Sie nach einer virtuellen Maschine und wählen Sie sie aus.
1. Wählen Sie im Abschnitt **Einstellungen** die Option **Identität**.
1. Legen Sie auf der Registerkarte **Systemseitig zugewiesen** den **Status** auf **Ein** fest.
   ![Screenshot einer VM-Identität](./media/aad-authorization/identity-virtual-machine.png)
1. Klicken Sie auf die Schaltfläche **Speichern**, um die Änderungen zu bestätigen.

### <a name="how-to-create-user-assigned-managed-identities"></a>Erstellen benutzerseitig zugewiesener verwalteter Identitäten
- [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

### <a name="how-to-configure-managed-identities-on-other-platforms"></a>Konfigurieren von verwalteten Identitäten auf anderen Plattformen

- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von Vorlagen](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Konfigurieren eines virtuellen Computers mit verwalteten Identitäten für Azure-Ressourcen mithilfe eines Azure SDK](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="how-to-configure-managed-identities-for-app-service-and-azure-functions"></a>Konfigurieren verwalteter Identitäten für App Service und Azure Functions

- [Verwenden verwalteter Identitäten für App Service und Azure Functions](../app-service/overview-managed-identity.md)

## <a name="add-role-assignments-on-azure-portal"></a>Hinzufügen von Rollenzuweisungen im Azure-Portal  

In diesem Beispiel wird gezeigt, wie Sie einer systemseitig zugewiesenen Identität über eine Web PubSub-Ressource die Rolle `Web PubSub Service Owner` zuweisen. 

> [!Note]
> Eine Rolle kann in jedem Bereich zugewiesen werden, einschließlich Verwaltungsgruppe, Abonnement, Ressourcengruppe oder einzelne Ressource. Weitere Informationen zum Geltungsbereich finden Sie unter [Grundlegendes zum Bereich für Azure RBAC](../role-based-access-control/scope-overview.md).
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Web PubSub-Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** , um Zugriffssteuerungseinstellungen für Azure Web PubSub anzuzeigen.

   Die folgende Abbildung zeigt ein Beispiel der Seite „Zugriffssteuerung (IAM)“ für eine Ressourcengruppe.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

   Der folgende Screenshot zeigt ein Beispiel für das Blatt „Zugriffssteuerung (IAM)“ für eine Web PubSub-Ressource.

   ![Screenshot der Zugriffssteuerung](./media/aad-authorization/access-control.png)

1. Klicken Sie auf **Hinzufügen > Rollenzuweisung hinzufügen**.

1. Wählen Sie auf der Registerkarte **Rollen** die Option `Web PubSub Service Owner`.

1. Klicken Sie auf **Weiter**.

   ![Screenshot des Hinzufügens von Rollenzuweisungen](./media/aad-authorization/add-role-assignment.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** im Abschnitt **Zugriff zuweisen** die Option **Verwaltete Identität**.

1. Klicken Sie auf **Mitglieder auswählen**.

1. Wählen Sie im Fenster **Verwaltete Identitäten auswählen** die Option **Systemzugewiesene verwaltete Identität > Virtuelle Maschine**

1. Suchen Sie die virtuelle Maschine, der Sie die Rolle zuweisen möchten, und wählen Sie sie aus.

1. Klicken Sie auf **Auswählen**, um die Auswahl zu bestätigen.

2. Klicken Sie auf **Weiter**.

   ![Screenshot der Rollenzuweisung für verwaltete Identitäten](./media/aad-authorization/assign-role-to-managed-identities.png)

3. Klicken Sie auf **Überprüfen + Zuweisen**, um die Änderung zu bestätigen.

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann bis zu 30 Minuten dauern.
Weitere Informationen über die Zuweisung und Verwaltung von Azure-Rollenzuweisungen finden Sie in diesen Artikeln:
- [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md)
- [Zuweisen von Azure-Rollen mithilfe der REST-API](../role-based-access-control/role-assignments-rest.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Zuweisen von Azure-Rollen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

## <a name="sample-codes-while-configuring-your-server"></a>Codebeispiele für das Konfigurieren des Servers

### <a name="using-system-assigned-identity"></a>Verwenden einer systemseitig zugewiesenen Identität

Sie können entweder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) oder [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) verwenden, um Ihre Web PubSub-Endpunkte unter Verwendung einer systemseitig zugewiesenen Identität zu konfigurieren.

Als allgemeine bewährte Methode wird jedoch die direkte Verwendung von `ManagedIdentityCredential` empfohlen.

Die systemseitig zugewiesene verwaltete Identität wird standardmäßig verwendet. **Stellen Sie jedoch sicher, dass Sie keine Umgebungsvariablen konfiguriert haben**, die von [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) beibehalten werden, wenn Sie `DefaultAzureCredential` verwenden. Andernfalls wird für die Anforderung ein Fallback auf `EnvironmentCredential` verwendet, das in den meisten Fällen die Antwort `401 Unauthorized` zur Folge hat.

Im Folgenden finden Sie Beispielcode für C#.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential());
```

Es gibt auch Beispiele für andere unterstützte Programmiersprachen: [Java](), [JavaScript]() und [Python]().

### <a name="using-user-assigned-identity"></a>Benutzerseitig zugewiesene Identität

Geben Sie bei der Erstellung des `ManagedIdentityCredential`-Objekts einfach die `ClientId` an.

> [!IMPORTANT]
> Verwenden Sie die **Client-ID**, nicht die Objekt-ID (Prinzipal-ID), auch wenn sie ähnlich aussehen!

Im Folgenden finden Sie Beispielcode für C#.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var clientId = "<your user-assigned identity client id>";
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential(clientId));
```

Es gibt auch Beispiele für andere unterstützte Programmiersprachen: [Java](), [JavaScript]() und [Python]().

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:
- [Übersicht über Azure AD für Web PubSub](concept-azure-ad-authorization.md)
- [Autorisieren von Anforderungen aus Azure-Anwendungen für Web PubSub-Ressourcen mit Azure AD](howto-authorize-from-application.md)