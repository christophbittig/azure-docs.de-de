---
title: Autorisieren von Anforderungen aus Azure-Anwendungen für Web SignalIR-Ressourcen mit Azure AD
description: Dieser Artikel enthält Informationen zum Autorisieren von Anforderungen von Azure-Anwendungen an SignalR-Ressourcen mit Azure AD.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 8297a715d66206bbad2721faaca89c3616744ce4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478869"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-azure-applications"></a>Autorisieren von Anforderungen aus Azure-Anwendungen für Web SignalR-Ressourcen mit Azure AD

Der Azure SignalR-Dienst unterstützt die Azure AD-Authentifizierung (Azure Active Directory) von Anforderungen von [Azure-Anwendungen](../active-directory/develop/app-objects-and-service-principals.md).

In diesem Artikel erfahren Sie, wie Sie Ihre SignalR-Ressource konfigurieren. Außerdem finden Sie hier Code, mit dem Sie Anforderungen an eine SignalR-Ressource von einer Azure-Anwendung autorisieren können.

## <a name="register-an-application"></a>Registrieren einer Anwendung

Der erste Schritt ist das Registrieren einer Azure-Anwendung.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach **Azure Active Directory**, und wählen Sie den Dienst aus.
2. Wählen Sie im Abschnitt **Verwalten** die Option **App-Registrierungen** aus.
3. Klicken Sie auf **Neue Registrierung**.

    ![Screenshot der Registrierung einer Anwendung](./media/authenticate/register-an-application.png)

4. Geben Sie einen **Anzeigenamen** für Ihre Anwendung ein.
5. Klicken Sie auf **Registrieren**, um die Registrierung zu bestätigen.

Nach dem Registrieren der Anwendung finden Sie die Werte **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** auf der Übersichtsseite. Diese GUIDs sind für die folgenden Schritte hilfreich.

![Screenshot einer Anwendung](./media/authenticate/application-overview.png)

Weitere Informationen zum Registrieren einer Anwendung finden Sie unter:
- [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).


## <a name="add-credentials"></a>Hinzufügen von Anmeldeinformationen

Sie können Ihrer vertraulichen Client-App-Registrierung sowohl Zertifikate als auch geheime Clientschlüssel (Zeichenfolge) als Anmeldeinformationen hinzufügen.

### <a name="client-secret"></a>Geheimer Clientschlüssel

Die Anwendung benötigt zum Beweis ihrer Identität einen geheimen Clientschlüssel, wenn sie ein Token anfordert. Führen Sie die folgenden Schritte aus, um einen geheimen Clientschlüssel zu erstellen.

1. Wählen Sie im Abschnitt **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Klicken Sie auf der Registerkarte **Geheime Clientschlüssel** auf **Neuer geheimer Clientschlüssel**.
![Screenshot des Erstellens eines geheimen Clientschlüssels](./media/authenticate/new-client-secret.png)
1. Geben Sie eine **Beschreibung** für den geheimen Clientschlüssel ein, und wählen Sie die **Ablaufzeit** aus.
1. Kopieren Sie den Wert des **geheimen Clientschlüssels** an einen sicheren Speicherort. 
    > [!NOTE]
    > Das Geheimnis wird nur einmal angezeigt.

### <a name="certificate"></a>Zertifikat

Sie können auch eine Zertifizierung hochladen, anstatt einen geheimen Clientschlüssel zu erstellen.

![Screenshot des Hochladens einer Zertifizierung](./media/authenticate/upload-certificate.png)

Weitere Informationen zum Hinzufügen von Anmeldeinformationen finden Sie unter:

- [Hinzufügen von Anmeldeinformationen](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Hinzufügen von Rollenzuweisungen im Azure-Portal

In diesem Beispiel wird gezeigt, wie Sie einem Dienstprinzipal (Anwendung) über eine SignalR-Ressource die Rolle `SignalR App Server` zuweisen. 

> [!Note]
> Eine Rolle kann in jedem Bereich zugewiesen werden, einschließlich Verwaltungsgruppe, Abonnement, Ressourcengruppe oder einzelne Ressource. Weitere Informationen zum Geltungsbereich finden Sie unter [Grundlegendes zum Bereich für Azure RBAC](../role-based-access-control/scope-overview.md).

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer SignalR-Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** , um Zugriffssteuerungseinstellungen für Azure SignalR anzuzeigen.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

   Der folgende Screenshot zeigt ein Beispiel für das Blatt „Zugriffssteuerung (IAM)“ für eine SignalR-Ressource.

   ![Screenshot: Zugriffssteuerung](./media/authenticate/access-control.png)

1. Klicken Sie auf **Hinzufügen > Rollenzuweisung hinzufügen**.

1. Wählen Sie auf der Registerkarte **Rollen** die Rolle `SignalR App Server` aus.

1. Klicken Sie auf **Weiter**.
    
   ![Screenshot des Hinzufügens einer Rollenzuweisung](./media/authenticate/add-role-assignment.png)

1. Wählen Sie unter **Zugriff zuweisen zu** auf der Registerkarte **Mitglieder** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.

1. Klicken Sie auf **Mitglieder auswählen**.

3. Suchen Sie nach der Anwendung, der Sie die Rolle zuweisen möchten, und wählen Sie sie aus.

1. Klicken Sie auf **Auswählen**, um die Auswahl zu bestätigen.

4. Klicken Sie auf **Weiter**.
    
   ![Screenshot des Zuweisens einer Rolle zu Dienstprinzipalen](./media/authenticate/assign-role-to-service-principals.png)

5. Klicken Sie auf **Überprüfen und zuweisen**, um die Änderung zu bestätigen.

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann bis zu 30 Minuten dauern.

Weitere Informationen zum Zuweisen und Verwalten von Azure-Rollenzuweisungen finden Sie in den folgenden Artikeln:
- [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md)
- [Zuweisen von Azure-Rollen mithilfe der REST-API](../role-based-access-control/role-assignments-rest.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Zuweisen von Azure-Rollen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

## <a name="configure-you-app"></a>Konfigurieren Ihrer App

### <a name="app-server"></a>App-Server

Die bewährte Methode besteht darin, Identität und Anmeldeinformationen in Ihren Umgebungsvariablen zu konfigurieren:

| Variable  | BESCHREIBUNG |
|------|------
| `AZURE_TENANT_ID` | Die ID des Azure Active Directory-Mandanten (Verzeichnis) |
| `AZURE_CLIENT_ID` | Die Client-ID (Anwendungs-ID) einer App-Registrierung im Mandanten |
| `AZURE_CLIENT_SECRET` | Ein geheimer Clientschlüssel, der für die App-Registrierung generiert wurde |
| `AZURE_CLIENT_CERTIFICATE_PATH` | Ein Pfad zum Zertifikat und zum privaten Schlüsselpaar im PEM- oder PFX-Format für die Authentifizierung der App-Registrierung |
| `AZURE_USERNAME`  | Der Benutzername, auch als UPN bezeichnet, eines Azure Active Directory-Benutzerkontos |
| `AZURE_PASSWORD`  | Das Kennwort des Azure Active Directory Benutzerkontos. Beachten Sie, dass Konten mit aktivierter MFA nicht unterstützt werden. |

Dabei können Sie entweder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) oder [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) verwenden, um Ihre SignalR-Endpunkte zu konfigurieren.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new DefaultAzureCredential())
    };
});
```

Sie können auch `EnvironmentalCredential` direkt verwenden.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new EnvironmentCredential())
    };
});
```

Weitere Informationen zur Funktionsweise von `DefaultAzureCredential` finden Sie unter der [DefaultAzureCredential-Klasse](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

#### <a name="use-different-credentials-while-using-multiple-endpoints"></a>Verwenden Sie unterschiedliche Anmeldeinformationen, während Sie mehrere Endpunkte verwenden.

Aus irgendeinem Grund könnten Sie sich dazu entscheiden, unterschiedliche Anmeldeinformationen für verschiedene Endpunkte zu verwenden.

In diesem Szenario können Sie [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) oder [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential) verwenden.

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    var credential1 = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
    var credential2 = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");

    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), credential1),
        new ServiceEndpoint(new Uri("https://<resource2>.service.signalr.net"), credential2),
    };
});
```

### <a name="azure-functions-signalr-bindings"></a>Azure Functions SignalR-Bindungen

> [!WARNING]
> Die SignalR-Triggerbindung unterstützt noch keine identitätsbasierte Verbindung, und Verbindungszeichenfolgen sind weiterhin erforderlich.

Azure Functions SignalR-Bindungen verwenden [Anwendungseinstellungen](/azure/azure-functions/functions-how-to-use-azure-function-app-settings) im Portal oder [`local.settings.json`](/azure/azure-functions/functions-develop-local#local-settings-file)lokal, um Azure-Anwendungsidentitäten für den Zugriff auf Ihre SignalR-Ressourcen zu konfigurieren.

Zunächst müssen Sie den Dienst-URI des SignalR Service angeben, deren Schlüssel mit `serviceUri`einem **Verbindungsnamenpräfix** (standardmäßig `AzureSignalRConnectionString`) und einem Trennzeichen (`__` auf Azure-Portal und `:` in der Datei local.settings.json) beginnt. Der Verbindungsname kann mit der Bindungseigenschaft angepasst [`ConnectionStringSetting`](/azure/azure-functions/functions-bindings-signalr-service) werden. Lesen Sie weiter, um das Beispiel zu finden.

Anschließend konfigurieren Sie Ihre Azure-Anwendungsidentität in [vordefinierten Umgebungsvariablen](#configure-identity-in-pre-defined-environment-variables) oder [in SignalR-spezifizierten Variablen](#configure-identity-in-signalr-specified-variables).

#### <a name="configure-identity-in-pre-defined-environment-variables"></a>Konfigurieren der Identität in vordefinierten Umgebungsvariablen

Eine Liste der vordefinierten Umgebungsvariablen finden Sie unter [Umgebungsvariablen](/dotnet/api/overview/azure/identity-readme#environment-variables). Es wird empfohlen, dass, wenn Sie über mehrere Dienste verfügen, die SignalR von derselben Azure-Anwendungsidentität abhängig sind, damit Sie die Identität nicht für jeden Dienst konfigurieren müssen. Diese Umgebungsvariablen können auch von anderen Diensten gemäß den Einstellungen anderer Dienste verwendet werden.

Wenn Sie z. B. Anmeldeinformationen für geheime Clientinformationen verwenden möchten, konfigurieren Sie wie folgt in der `local.settings.json` Datei.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "AZURE_CLIENT_ID":"...",
    "AZURE_CLIENT_SECRET":"...",
    "AZURE_TENANT_ID":"..."
  }
}
```
Fügen Sie im Azure-Portal wie folgt Einstellungen hinzu:
```
 <CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
AZURE_CLIENT_ID = ...
AZURE_TENANT_ID = ...
AZURE_CLIENT_SECRET = ...
 ```

#### <a name="configure-identity-in-signalr-specified-variables"></a>Konfigurieren der Identität in von SignalR angegebenen Variablen

Die von SignalR angegebenen Variablen verwenden das gleiche Schlüsselpräfix mit dem `serviceUri`-Schlüssel. Hier ist die Liste der Variablen, die Sie evtl. verwenden:
* clientId
* clientSecret
* tenantId

Hier sind die Beispiele für die Verwendung geheimer Clientanmeldeinformationen:

In der Datei `local.settings.json`:
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "<CONNECTION_NAME_PREFIX>:clientId": "...",
    "<CONNECTION_NAME_PREFIX>:clientSecret": "...",
    "<CONNECTION_NAME_PREFIX>:tenantId": "..."
  }
}
```

Fügen Sie im Azure-Portal wie folgt Einstellungen hinzu:
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__clientId = ...
<CONNECTION_NAME_PREFIX>__clientSecret = ...
<CONNECTION_NAME_PREFIX>__tenantId = ...
```
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:
- [Übersicht über Azure AD für SignalR](signalr-concept-authorize-azure-active-directory.md)
- [Autorisieren von Anforderungen von verwalteten Identitäten für SignalR-Ressourcen mit Azure AD](signalr-howto-authorize-managed-identity.md)