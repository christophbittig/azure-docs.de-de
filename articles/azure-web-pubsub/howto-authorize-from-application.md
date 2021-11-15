---
title: Autorisieren von Anforderungen aus Azure-Anwendungen für Web PubSub-Ressourcen mit Azure AD
description: Dieser Artikel enthält Informationen zum Autorisieren von Anforderungen von Azure-Anwendungen an Web PubSub-Ressourcen mit Azure AD.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 9c6b0c520fbde3f028e933d7eec05d390cb209e8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997645"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-azure-applications"></a>Autorisieren von Anforderungen aus Azure-Anwendungen für Web PubSub-Ressourcen mit Azure AD

Der Azure Web PubSub-Dienst unterstützt die Azure AD-Authentifizierung (Azure Active Directory) von Anforderungen von [Azure-Anwendungen](../active-directory/develop/app-objects-and-service-principals.md). 

In diesem Artikel erfahren Sie, wie Sie Ihre Web PubSub-Ressource konfigurieren. Außerdem finden Sie hier Code, mit dem Sie Anforderungen an eine Web PubSub-Ressource von einer Azure-Anwendung autorisieren können.

## <a name="register-an-application"></a>Registrieren einer Anwendung

Der erste Schritt ist das Registrieren einer Azure-Anwendung.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach **Azure Active Directory**, und wählen Sie den Dienst aus.
2. Wählen Sie im Abschnitt **Verwalten** die Option **App-Registrierungen** aus.
3. Klicken Sie auf **Neue Registrierung**.

    ![Screenshot der Registrierung einer Anwendung](./media/aad-authorization/register-an-application.png)

4. Geben Sie einen **Anzeigenamen** für Ihre Anwendung ein.
5. Klicken Sie auf **Registrieren**, um die Registrierung zu bestätigen.

Nach dem Registrieren der Anwendung finden Sie die Werte **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** auf der Übersichtsseite. Diese GUIDs sind für die folgenden Schritte hilfreich.

![Screenshot einer Anwendung](./media/aad-authorization/application-overview.png)

Weitere Informationen zum Registrieren einer Anwendung finden Sie unter:
- [Schnellstart: Registrieren einer Anwendung bei der Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).

## <a name="add-credentials"></a>Hinzufügen von Anmeldeinformationen

Sie können Ihrer vertraulichen Client-App-Registrierung sowohl Zertifikate als auch geheime Clientschlüssel (Zeichenfolge) als Anmeldeinformationen hinzufügen.

### <a name="client-secret"></a>Geheimer Clientschlüssel

Die Anwendung benötigt zum Beweis ihrer Identität einen geheimen Clientschlüssel, wenn sie ein Token anfordert. Führen Sie die folgenden Schritte aus, um einen geheimen Clientschlüssel zu erstellen.

1. Wählen Sie im Abschnitt **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Klicken Sie auf der Registerkarte **Geheime Clientschlüssel** auf **Neuer geheimer Clientschlüssel**.
![Screenshot des Erstellens eines geheimen Clientschlüssels](./media/aad-authorization/new-client-secret.png)
1. Geben Sie eine **Beschreibung** für den geheimen Clientschlüssel ein, und wählen Sie die **Ablaufzeit** aus.
1. Kopieren Sie den Wert des **geheimen Clientschlüssels** an einen sicheren Speicherort. 
    > [!NOTE]
    > Das Geheimnis wird nur einmal angezeigt.
### <a name="certificate"></a>Zertifikat

Sie können auch eine Zertifizierung hochladen, anstatt einen geheimen Clientschlüssel zu erstellen.

![Screenshot des Hochladens einer Zertifizierung](./media/aad-authorization/upload-certificate.png)

Weitere Informationen zum Hinzufügen von Anmeldeinformationen finden Sie unter:

- [Hinzufügen von Anmeldeinformationen](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Hinzufügen von Rollenzuweisungen im Azure-Portal

In diesem Beispiel wird gezeigt, wie Sie einem Dienstprinzipal (Anwendung) über eine Web PubSub-Ressource die Rolle `Web PubSub Service Owner` zuweisen. 

> [!Note]
> Eine Rolle kann in jedem Bereich zugewiesen werden, einschließlich Verwaltungsgruppe, Abonnement, Ressourcengruppe oder einzelne Ressource. Weitere Informationen zum Geltungsbereich finden Sie unter [Grundlegendes zum Bereich für Azure RBAC](../role-based-access-control/scope-overview.md).
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Web PubSub-Ressource.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** , um Zugriffssteuerungseinstellungen für Azure Web PubSub anzuzeigen.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

   Der folgende Screenshot zeigt ein Beispiel für das Blatt „Zugriffssteuerung (IAM)“ für eine Web PubSub-Ressource.

   ![Screenshot der Zugriffssteuerung](./media/aad-authorization/access-control.png)

1. Klicken Sie auf **Hinzufügen > Rollenzuweisung hinzufügen**.

1. Wählen Sie auf der Registerkarte **Rollen** die Option `Web PubSub App Server`.

1. Klicken Sie auf **Weiter**.

   ![Screenshot des Hinzufügens einer Rollenzuweisung](./media/aad-authorization/add-role-assignment.png)

1. Wählen Sie unter **Zugriff zuweisen zu** auf der Registerkarte **Mitglieder** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.

1. Klicken Sie auf **Mitglieder auswählen**.

3. Suchen Sie nach der Anwendung, der Sie die Rolle zuweisen möchten, und wählen Sie sie aus.

1. Klicken Sie auf **Auswählen**, um die Auswahl zu bestätigen.

4. Klicken Sie auf **Weiter**.

   ![Screenshot des Zuweisens einer Rolle zu Dienstprinzipalen](./media/aad-authorization/assign-role-to-service-principals.png)

5. Klicken Sie auf **Überprüfen und zuweisen**, um die Änderung zu bestätigen.

> [!IMPORTANT]
> Die Verteilung von Azure-Rollenzuweisungen kann bis zu 30 Minuten dauern.
Weitere Informationen über die Zuweisung und Verwaltung von Azure-Rollenzuweisungen finden Sie in diesen Artikeln:
- [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md)
- [Zuweisen von Azure-Rollen mithilfe der REST-API](../role-based-access-control/role-assignments-rest.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Zuweisen von Azure-Rollen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-server"></a>Konfigurieren des Servers

Es wird empfohlen, Identität und Anmeldeinformationen in Ihren Umgebungsvariablen zu konfigurieren:

| Variable  | BESCHREIBUNG |
|------|------
| `AZURE_TENANT_ID` | Die ID des Azure Active Directory-Mandanten (Verzeichnis) |
| `AZURE_CLIENT_ID` | Die Client-ID (Anwendungs-ID) einer App-Registrierung im Mandanten |
| `AZURE_CLIENT_SECRET` | Ein geheimer Clientschlüssel, der für die App-Registrierung generiert wurde |
| `AZURE_CLIENT_CERTIFICATE_PATH` | Ein Pfad zum Zertifikat und zum privaten Schlüsselpaar im PEM- oder PFX-Format für die Authentifizierung der App-Registrierung |
| `AZURE_USERNAME`  | Der Benutzername, auch als UPN bezeichnet, eines Azure Active Directory-Benutzerkontos |
| `AZURE_PASSWORD`  | Das Kennwort des Azure Active Directory Benutzerkontos. Beachten Sie, dass Konten mit aktivierter MFA nicht unterstützt werden. |

Dabei können Sie entweder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) oder [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) verwenden, um Ihre Web PubSub-Endpunkte zu konfigurieren.

### <a name="sample-codes"></a>Codebeispiele

Im Folgenden finden Sie Beispielcode für C#. Informationen zu anderen unterstützten Programmiersprachen finden Sie unter JavaScript/Python/Java.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new DefaultAzureCredential());
```

Weitere Informationen zur Funktionsweise von `DefaultAzureCredential` finden Sie unter der [DefaultAzureCredential-Klasse](/dotnet/api/azure.identity.defaultazurecredential).

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new EnvironmentCredential());
```

Sie können [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) oder [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential) auch direkt verwenden, wenn Sie dies möchten.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```
```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var credential = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");
var client = new WebPubSubServiceClient(endpoint, "hub", credential);
```

Weitere Informationen zum Erstellen von `TokenCredential` für die Azure AD-Autorisierung finden Sie in den folgenden Artikeln:

- [DefaultAzureCredential-Klasse](/dotnet/api/azure.identity.defaultazurecredential)
- [ClientSecretCredential-Konstruktoren](/dotnet/api/azure.identity.clientsecretcredential.-ctor)
- [ClientCertificateCredential-Konstruktoren](/dotnet/api/azure.identity.clientcertificatecredential.-ctor)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden verwandten Artikeln:
- [Übersicht über Azure AD für Web PubSub](concept-azure-ad-authorization.md)
- [Autorisieren von Anforderungen von verwalteten Identitäten für Web PubSub-Ressourcen mit Azure AD](howto-authorize-from-managed-identity.md)