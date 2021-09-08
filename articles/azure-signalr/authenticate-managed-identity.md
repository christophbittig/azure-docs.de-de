---
title: Authentifizieren einer verwalteten Identität mit Azure Active Directory
description: Dieser Artikel enthält Informationen zur Authentifizierung einer verwalteten Identität mit Azure Active Directory, um auf Azure SignalR Service zuzugreifen.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: e25fa98e2ca43b6697de6290ad122b6585b6a072
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186027"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Azure SignalR Service-Ressourcen
Azure SignalR Service unterstützt die Azure AD-Authentifizierung (Azure Active Directory) mit [verwalteten Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md). Sie können verwaltete Identitäten für Azure-Ressourcen verwenden, um den Zugriff auf Azure SignalR Service-Ressourcen mithilfe von Azure AD-Anmeldeinformationen über Anwendungen zu autorisieren, die auf virtuellen Azure-Computern, in Funktions-Apps, in VM-Skalierungsgruppen und anderen Diensten ausgeführt werden. Durch Verwendung von verwalteten Identitäten für Azure-Ressourcen zusammen mit der Azure AD-Authentifizierung können Sie vermeiden, dass Anmeldeinformationen mit den in der Cloud ausgeführten Anwendungen gespeichert werden.

In diesem Artikel wird die Autorisierung des Zugriffs auf Azure SignalR Service mithilfe einer verwalteten Identität erläutert.

## <a name="enable-managed-identities"></a>Aktivieren von verwalteten Identitäten
Damit Sie verwaltete Identitäten für Azure-Ressourcen zum Autorisieren des Zugriffs auf Azure SignalR Service-Ressourcen verwenden können, müssen Sie zunächst verwaltete Identitäten für Azure-Ressourcen aktivieren. 
### <a name="for-azure-resources-on-a-vm"></a>Für Azure-Ressourcen auf einem virtuellen Computer
Informationen zum Aktivieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Computer finden Sie in diesen Artikeln:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
  1. Wechseln Sie zu **Einstellungen** im Azure-Portal, und wählen Sie **Identität** aus. 
  2. Legen Sie den **Status** auf **Ein** fest. 
  3. Klicken Sie auf **Speichern**, um die Einstellung zu speichern. 

      ![Verwaltete Identität für einen virtuellen Computer](./media/authenticate/identity-virtual-machine.png)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure-Befehlszeilenschnittstelle](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-Vorlage](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager-Clientbibliotheken](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-azure-functions"></a>Für Azure Functions

Siehe [Verwenden verwalteter Identitäten für App Service und Azure Functions](../app-service/overview-managed-identity.md).

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Erteilen von Berechtigungen für eine verwaltete Identität in Azure AD
Damit Sie eine Anforderung an Azure SignalR Service über eine verwaltete Identität in Ihrer Anwendung autorisieren können, müssen Sie zuerst die Einstellungen der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) für diese verwaltete Identität konfigurieren. Azure SignalR Service definiert RBAC-Rollen, die Berechtigungen für das Abrufen von `AccessKey` oder `ClientToken` beinhalten. Wenn die RBAC-Rolle einer verwalteten Identität zugewiesen wird, wird der verwalteten Identität der Zugriff auf Azure SignalR Service im entsprechenden Umfang erteilt.

Befolgen Sie diese Anweisungen, um Rollenzuweisungen zu verwalten:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer SignalR-Ressource.
1. Wählen Sie **Zugriffssteuerung (IAM)** aus, um Zugriffssteuerungseinstellungen für Azure SignalR Service anzuzeigen. 
1. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um die Liste mit den Rollenzuweisungen anzuzeigen. Wählen Sie die Schaltfläche **Hinzufügen** auf der Symbolleiste und dann **Rollenzuweisung hinzufügen** aus. 

    ![Schaltfläche „Hinzufügen“ auf der Symbolleiste](./media/authenticate/role-assignments-add-button.png)

1. Führen Sie auf der Seite **Rollenzuweisung hinzufügen** die folgenden Schritte aus:
    1. Wählen Sie **SignalR Service-Besitzer** als Rolle aus.
    1. Suchen Sie nach dem **Sicherheitsprinzipal** (Benutzer, Gruppe, Dienstprinzipal), dem Sie die Rolle zuweisen möchten.
    1. Klicken Sie auf **Speichern**, um die Rollenzuweisung zu speichern. 

        ![Zuweisen einer Rolle zu einer Anwendung](./media/authenticate/assign-role-to-application.png)

    1. Die Identität, der Sie die Rolle zugewiesen haben, wird unter dieser Rolle angezeigt. Die folgende Abbildung zeigt z. B., dass die Anwendungen `signalr-dev` und `signalr-service` die Rolle „SignalR-App-Server“ besitzen. 
        
        ![Rollenzuweisungsliste](./media/authenticate/role-assignment-list.png)

Sie können ähnliche Schritte ausführen, um eine Rolle zuzuweisen, die für eine Ressourcengruppe oder ein Abonnement gilt. Nachdem Sie die Rolle und ihren Bereich definiert haben, können Sie dieses Verhalten mit den Beispielen [an diesem GitHub-Speicherort](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) testen.

Weitere Informationen zum Zuweisen von RBAC-Rollen finden Sie unter [Autorisieren des Zugriffs auf Azure SignalR Service mit Azure Active Directory](authorize-access-azure-active-directory.md).

Weitere Informationen zum Verwalten des Zugriffs auf Azure-Ressourcen mithilfe der Azure RBAC und des Azure-Portals finden Sie in [diesem Artikel](..//role-based-access-control/role-assignments-portal.md). 

## <a name="configure-your-app"></a>Konfigurieren Ihrer App
### <a name="app-server"></a>App-Server

Fügen Sie bei Verwendung von `AddAzureSignalR` folgende Optionen hinzu:

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;";
});
```

### <a name="azure-functions-app"></a>Azure Functions-App

Fügen Sie im Azure-Portal eine Anwendungseinstellung mit dem Namen `AzureSignalRConnectionString` und dem Wert `Endpoint=https://<name>.signalr.net;AuthType=aad;` hinzu.

Fügen Sie lokal in Ihrer Datei `local.appsettings.json` im Abschnitt `Values` Folgendes hinzu:
```json
{
    "Values": {
        "AzureSignalRConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur rollenbasierten Zugriffssteuerung finden Sie in folgendem Artikel: [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md)
- Informationen zum Zuweisen und Verwalten von Azure-Rollenzuweisungen mit Azure PowerShell, der Azure-Befehlszeilenschnittstelle oder der REST-API finden Sie in diesen Artikeln:
    - [Verwalten der rollenbasierten Zugriffssteuerung mit Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Verwalten der rollenbasierten Zugriffssteuerung mit der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
    - [Verwalten der rollenbasierten Zugriffssteuerung mit der REST-API](../role-based-access-control/role-assignments-rest.md)
    - [Verwalten der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) mit Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:
- [Authentifizieren einer Anwendung mit Azure Active Directory für den Zugriff auf Azure SignalR Service](authenticate-application.md)
- [Autorisieren des Zugriffs auf Azure SignalR Service mit Azure Active Directory](authorize-access-azure-active-directory.md)
