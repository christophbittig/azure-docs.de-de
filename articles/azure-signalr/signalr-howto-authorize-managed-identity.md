---
title: Autorisierung von Anfragen an SignalR-Ressourcen mit Azure AD aus verwalteten Identitäten
description: Dieser Artikel enthält Informationen zur Autorisierung von Anfragen an SignalR-Ressourcen mit Azure AD aus verwalteten Identitäten
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 1d332fd5d69b088717501771ccc03d8d24126478
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478928"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-managed-identities"></a>Autorisierung von Anfragen an SignalR-Ressourcen mit Azure AD aus verwalteten Identitäten
Azure SignalR Service unterstützt Azure Active Directory (Azure AD) und autorisiert Anfragen von [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

Dieser Artikel zeigt, wie Sie Ihre SignalR-Ressource und Codes konfigurieren, um die Anfrage an eine SignalR-Ressource von einer verwalteten Identität zu autorisieren.

## <a name="configure-managed-identities"></a>Konfigurieren von verwalteten Identitäten

Der erste Schritt besteht darin, verwaltete Identitäten zu konfigurieren.

Dies ist ein Beispiel für die Konfiguration von `System-assigned managed identity` auf einem `Virtual Machine` unter Verwendung des Azure-Portals.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/), suchen Sie nach einer virtuellen Maschine und wählen Sie sie aus.
1. Wählen Sie im Abschnitt **Einstellungen** die Option **Identität**.
1. Schalten Sie auf der Registerkarte **System zugewiesen** die Option **Status** auf **Ein**.
   ![Screenshot einer Anwendung](./media/authenticate/identity-virtual-machine.png)
1. Klicken Sie auf die Schaltfläche **Speichern**, um die Änderung zu bestätigen.


Klicken Sie auf die Schaltfläche Speichern, um die Änderung zu bestätigen.
- [Erstellen einer benutzerseitig zugewiesenen verwalteten Identität](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

Wenn Sie mehr über die Konfiguration von verwalteten Identitäten erfahren möchten, lesen Sie einen dieser Artikel:

- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer über das Azure-Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe der Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von Vorlagen](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Konfigurieren eines virtuellen Computers mit verwalteten Identitäten für Azure-Ressourcen mithilfe eines Azure SDK](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-app-service-and-azure-functions"></a>Für App-Dienst und Azure-Funktionen

Siehe [Verwenden verwalteter Identitäten für App Service und Azure Functions](../app-service/overview-managed-identity.md).

## <a name="add-role-assignments-on-azure-portal"></a>Rollenzuweisungen im Azure-Portal hinzufügen

Dieses Beispiel zeigt, wie man einer vom System zugewiesenen Identität über eine SignalR-Ressource eine `SignalR App Server`-Rolle zuweist. 

> [!Note]
> Eine Rolle kann einem beliebigen Bereich zugewiesen werden, einschließlich Verwaltungsgruppe, Abonnement, Ressourcengruppe oder einer einzelnen Ressource. Um mehr über den Geltungsbereich zu erfahren, siehe [Geltungsbereich für Azure RBAC verstehen](../role-based-access-control/scope-overview.md)

1. Öffnen Sie [Azure Portal](https://portal.azure.com/), navigieren Sie zu Ihrer SignalR-Ressource.

1. Klicken Sie auf **Zugriffskontrolle (IAM)** , um die Zugriffskontrolleinstellungen für das Azure SignalR anzuzeigen.

   Die folgende Abbildung zeigt ein Beispiel der Seite „Zugriffssteuerung (IAM)“ für eine Ressourcengruppe.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

   Der folgende Screenshot zeigt ein Beispiel für die Seite Zugriffskontrolle (IAM) für eine SignalR-Ressource.

   ![Screenshot der Zugangskontrolle](./media/authenticate/access-control.png)

1. Klicken Sie auf **Hinzufügen > Rollenzuweisung hinzufügen**.

1. Wählen Sie auf der Registerkarte **Rollen** die Option `SignalR App Server`.

1. Klicken Sie auf **Weiter**.

   ![Screenshot des Hinzufügens von Rollenzuweisungen](./media/authenticate/add-role-assignment.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** im Abschnitt **Zugriff zuweisen** die Option **Verwaltete Identität**.

1. Klicken Sie auf **Mitglieder auswählen**.

1. Wählen Sie im Fenster **Verwaltete Identitäten auswählen** die Option **Systemzugewiesene verwaltete Identität > Virtuelle Maschine**

1. Suchen Sie die virtuelle Maschine, der Sie die Rolle zuweisen möchten, und wählen Sie sie aus.

1. Klicken Sie auf **Auswählen**, um die Auswahl zu bestätigen.

2. Klicken Sie auf **Weiter**.

   ![Screenshot der Rollenzuweisung für verwaltete Identitäten](./media/authenticate/assign-role-to-managed-identities.png)

3. Klicken Sie auf **Überprüfen + Zuweisen**, um die Änderung zu bestätigen.

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann bis zu 30 Minuten dauern.

Weitere Informationen über die Zuweisung und Verwaltung von Azure-Rollenzuweisungen finden Sie in diesen Artikeln:
- [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md)
- [Zuweisen von Azure-Rollen mithilfe der REST-API](../role-based-access-control/role-assignments-rest.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Zuweisen von Azure-Rollen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-app"></a>Konfigurieren Ihrer App

### <a name="app-server"></a>App-Server

#### <a name="using-system-assigned-identity"></a>Verwendung der vom System zugewiesenen Identität

Sie können entweder [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) oder [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) zur Konfiguration Ihrer SignalR-Endpunkte verwenden.


Die beste Praxis ist jedoch die direkte Verwendung von `ManagedIdentityCredential`.

Die vom System zugewiesene verwaltete Identität wird standardmäßig verwendet, aber **bitte stellen Sie sicher, dass Sie keine Umgebungsvariablen** konfigurieren, die der [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) beibehalten würde, wenn Sie `DefaultAzureCredential` verwenden würden. Andernfalls wird auf die Verwendung von `EnvironmentCredential` für die Anfrage zurückgegriffen, was in den meisten Fällen zu einer `Unauthorized` Antwort führt.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential()),
    };
});
```

#### <a name="using-user-assigned-identity"></a>Verwendung der vom Benutzer zugewiesenen Identität

Geben Sie `ClientId` bei der Erstellung des `ManagedIdentityCredential` Objekts an.

> [!IMPORTANT]
> Verwenden Sie **Client Id**, nicht die Object (principal) ID, auch wenn beide GUIDs sind!

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        var clientId = "<your identity client id>";
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential(clientId)),
    };
```

### <a name="azure-functions-signalr-bindings"></a>Azure Functions SignalR-Bindungen

> [!WARNING]
> Die SignalR-Trigger-Bindung unterstützt noch keine identitätsbasierte Verbindung, und es sind noch Verbindungszeichenfolgen erforderlich.

Azure Functions SignalR Bindings verwenden [Anwendungseinstellungen](/azure/azure-functions/functions-how-to-use-azure-function-app-settings) auf dem Portal oder [`local.settings.json`](/azure/azure-functions/functions-develop-local#local-settings-file) auf der lokalen Ebene, um die Managed-Identity für den Zugriff auf Ihre SignalR-Ressourcen zu konfigurieren.

Sie benötigen möglicherweise eine Gruppe von Schlüssel-Wert-Paaren, um eine Identität zu konfigurieren. Die Schlüssel aller Schlüssel-Wert-Paare müssen mit einem **Verbindungsnamenpräfix** (Standardwert ist `AzureSignalRConnectionString`) und einem Trennzeichen (`__` bei Portal und `:` bei lokal) beginnen. Das Präfix kann mit der Bindungseigenschaft [`ConnectionStringSetting`](/azure/azure-functions/functions-bindings-signalr-service) angepasst werden.

#### <a name="using-system-assigned-identity"></a>Verwendung der vom System zugewiesenen Identität

Wenn Sie nur den Dienst-URI konfigurieren, wird die `DefaultAzureCredential` verwendet. Dies ist nützlich, wenn Sie dieselbe Konfiguration auf Azure und in der lokalen Entwicklungsumgebung verwenden möchten. Um zu erfahren, wie `DefaultAzureCredential` funktioniert, siehe [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

Stellen Sie im Azure-Portal wie folgt ein, um einen `DefaultAzureCredential` zu konfigurieren. Wenn Sie sicherstellen, dass Sie keine der hier aufgeführten [Umgebungsvariablen konfigurieren](/dotnet/api/overview/azure/identity-readme#environment-variables), wird die vom System zugewiesene Identität zur Authentifizierung verwendet.
```
<CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
```

Hier ist ein Beispiel für die Konfiguration von `DefaultAzureCredential` in der Datei `local.settings.json`. Beachten Sie, dass es bei lokal keine verwaltete Identität gibt und die Authentifizierung über Visual Studio, Azure CLI und Azure PowerShell-Konten der Reihe nach versucht wird.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net"
  }
}
```

Wenn Sie die vom System zugewiesene Identität unabhängig und ohne den Einfluss von [anderen Umgebungsvariablen](/dotnet/api/overview/azure/identity-readme#environment-variables) verwenden wollen, sollten Sie den `credential` Schlüssel mit Verbindungsnamenpräfix auf `managedidentity` setzen. Hier ist ein Beispiel für die Anwendungseinstellungen:

```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
```

#### <a name="using-user-assigned-identity"></a>Verwendung der vom Benutzer zugewiesenen Identität

Wenn Sie die benutzerzugeordnete Identität verwenden möchten, müssen Sie im Vergleich zur systemzugeordneten Identität einen weiteren `clientId`-Schlüssel mit dem Präfix für den Verbindungsnamen zuweisen. Hier ist ein Beispiel für die Anwendungseinstellungen:
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
<CONNECTION_NAME_PREFIX>__clientId = <CLIENT_ID>
```
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:
- [Übersicht über Azure AD für SignalR](signalr-concept-authorize-azure-active-directory.md)
- [Anfrage auf SignalR-Ressourcen mit Azure AD aus Azure-Anwendungen autorisieren](signalr-howto-authorize-application.md)
