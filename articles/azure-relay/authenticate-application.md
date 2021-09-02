---
title: Authentifizieren über eine Anwendung – Azure Relay (Vorschau)
description: Dieser Artikel enthält Informationen zur Authentifizierung einer Anwendung mit Azure Active Directory, um auf Azure Relay-Ressourcen zuzugreifen.
ms.topic: article
ms.date: 07/02/2021
ms.openlocfilehash: 2304dd58332cb95c40e7492451cf010ee46d76be
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654171"
---
# <a name="authenticate-and-authorize-an-application-with-azure-active-directory-to-access-azure-relay-entities-preview"></a>Authentifizieren und Autorisieren einer Anwendung mit Azure Active Directory für den Zugriff auf Azure Relay-Entitäten (Vorschau)
Azure Relay unterstützt die Verwendung Azure Active Directory (Azure AD) zum Autorisieren von Anforderungen an Azure Relay-Entitäten (Hybridverbindungen, WCF Relays). Mit Azure AD können Sie die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zum Gewähren von Berechtigungen für einen Sicherheitsprinzipal verwenden, bei dem es sich um einen Benutzer, eine Gruppe oder einen Anwendungsdienstprinzipal handeln kann. Weitere Informationen zu Rollen und Rollenzuweisungen finden Sie unter [Grundlegendes zu den verschiedenen Rollen](../role-based-access-control/overview.md).   


[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="authenticate-from-an-app"></a>Authentifizieren über eine App
Ein wesentlicher Vorteil der Verwendung von Azure AD mit Azure Relay besteht darin, dass Ihre Anmeldeinformationen nicht mehr im Code gespeichert werden müssen. Stattdessen können Sie ein OAuth 2.0-Zugriffstoken von Microsoft Identity Platform anfordern. Azure AD übernimmt die Authentifizierung des Sicherheitsprinzipals (Benutzer, Gruppe oder Dienstprinzipal), der die Anwendung ausführt. Wenn die Authentifizierung erfolgreich ist, gibt Azure AD das Zugriffstoken an die Anwendung zurück, und die Anwendung kann dann das Zugriffstoken zum Autorisieren von Anforderungen an Azure Relay verwenden.

In den folgenden Abschnitten wird gezeigt, wie Sie Ihre Konsolenanwendung für die Authentifizierung mit Microsoft Identity Platform 2.0 konfigurieren. Weitere Informationen finden Sie in der [Übersicht über Microsoft Identity Platform (v2.0)](../active-directory/develop/v2-overview.md).

Eine Übersicht über den Datenfluss für OAuth 2.0-Codeberechtigungen finden Sie unter [Autorisieren des Zugriffs auf Azure Active Directory-Webanwendungen mit dem Flow zum Erteilen des OAuth 2.0-Codes](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrieren der Anwendung bei einem Azure AD-Mandanten
Der erste Schritt bei der Verwendung von Azure AD zum Autorisieren von Azure Relay-Entitäten ist die Registrierung Ihrer Clientanwendung mit einem Azure AD-Mandanten über das Azure-Portal. Wenn Sie Ihre Clientanwendung registrieren, stellen Sie Azure AD Informationen zu Ihrer Anwendung bereit. Azure AD stellt dann eine Client-ID (auch als Anwendungs-ID bezeichnet) bereit, mit der Sie Ihre Anwendung zur Laufzeit Azure AD zuordnen können. 

Eine Schritt-für-Schritt-Anleitung zum Registrieren Ihrer Anwendung bei Azure AD finden Sie unter [Schnellstart: Registrieren einer Anwendung bei Azure AD](../active-directory/develop/quickstart-register-app.md#register-an-application).

> [!IMPORTANT]
> Notieren Sie sich die Werte von **Verzeichnis-ID (Mandant)** und **Anwendungs-ID (Client)** . Sie benötigen diese Werte, um die Beispielanwendung auszuführen.

### <a name="create-a-client-secret"></a>Erstellen eines Clientgeheimnisses   
Die Anwendung benötigt zum Beweis ihrer Identität einen geheimen Clientschlüssel, wenn sie ein Token anfordert. Im selben oben verlinkten Artikel finden Sie Informationen zum Erstellen eines geheimen Clientschlüssels im Abschnitt [Hinzufügen eines geheimen Clientschlüssels](../active-directory/develop/quickstart-register-app.md#add-a-client-secret). 

> [!IMPORTANT]
> Notieren Sie sich den **Geheimen Clientschlüssel**. Sie benötigen ihn, um die Beispielanwendung auszuführen.

## <a name="assign-azure-roles-using-the-azure-portal"></a>Zuweisen von Azure-Rollen über das Azure-Portal
Weisen Sie eine der Azure Relay-Rollen dem Dienstprinzipal der Anwendung im gewünschten Bereich (Relayentität, Namespace, Ressourcengruppe, Abonnement) zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="run-the-sample"></a>Ausführen des Beispiels

1. Laden Sie das Konsolenanwendungsbeispiel von [GitHub](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol) herunter.
1. Führen Sie die Anwendung gemäß den Anweisungen im [README-Artikel](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample) lokal auf Ihrem Computer aus.

    > [!NOTE]
    > Führen Sie dieselben oben beschriebenen Schritte aus, um die [Beispielkonsolenanwendung für WCF Relay](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl) auszuführen. 

#### <a name="highlighted-code-from-the-sample"></a>Hervorgehobener Code aus dem Beispiel
Hier sehen Sie den Code aus dem Beispiel, der zeigt, wie sie Azure AD-Authentifizierung verwenden, um eine Verbindung mit dem Azure Relay-Dienst herzustellen.  

1. Erstellen Sie ein [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider)-Objekt mithilfe der `TokenProvider.CreateAzureActiveDirectoryTokenProvider`-Methode. 

    Wenn Sie noch keine App-Registrierung erstellt haben, finden Sie Informationen zum Erstellen im Abschnitt [Registrieren Ihrer Anwendung bei Azure AD](#register-your-application-with-an-azure-ad-tenant), und erstellen Sie dann einen geheimen Clientschlüssel, wie im Abschnitt [Erstellen eines geheimen Clientschlüssels](#create-a-client-secret) beschrieben.

    Wenn Sie eine vorhandene App-Registrierung verwenden möchten, befolgen Sie diese Anweisungen, um die **Anwendungs-ID (Client)** und die **Verzeichnis-ID (Mandant)** abzurufen. 

    1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
    1. Suchen Sie über die Suchleiste oben nach **Azure Active Directory**, und wählen Sie es aus.
    1. Wählen Sie auf der Seite **Azure Active Directory** im linken Menü im Abschnitt **Verwalten** die Option **App-Registrierungen** aus. 
    1. Wählen Sie Ihre App-Registrierung aus. 
    1. Auf der Seite für Ihre App-Registrierung werden die Werte für **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** angezeigt. 
    
    Gehen Sie wie folgt vor, um den **geheimen Clientschlüssel** abzurufen:
    1. Wählen Sie auf der Seite Ihrer App-Registrierung im linken Menü **Zertifikate und Geheimnisse** aus. 
    1. Verwenden Sie die Schaltfläche „Kopieren“ in der Spalte **Wert** für das Geheimnis im Abschnitt **Geheime Clientschlüssel**. 

    
    ```csharp
    static TokenProvider GetAadTokenProvider(string clientId, string tenantId, string clientSecret)
    {
        return TokenProvider.CreateAzureActiveDirectoryTokenProvider(
            async (audience, authority, state) =>
            {
                IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
                    .WithAuthority(authority)
                    .WithClientSecret(clientSecret)
                    .Build();

                var authResult = await app.AcquireTokenForClient(new [] { $"{audience}/.default" }).ExecuteAsync();
                return authResult.AccessToken;
            },
            $"https://login.microsoftonline.com/{tenantId}");
    }
    ```
1. Erstellen Sie ein [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_)- oder [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider))-Objekt, indem Sie den Hybridverbindungs-URI und den Tokenanbieter übergeben, den Sie im vorherigen Schritt erstellt haben.

    **Listener:**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **Absender:**
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zur Azure RBAC finden Sie im Artikel [Was ist die rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)?](../role-based-access-control/overview.md).
- Informationen zum Zuweisen und Verwalten von Azure-Rollenzuweisungen mit Azure PowerShell, der Azure-Befehlszeilenschnittstelle oder der REST-API finden Sie in diesen Artikeln:
    - [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe von Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md)
    - [Hinzufügen oder Entfernen von Rollenzuweisungen mithilfe von Azure RBAC und der REST-API](../role-based-access-control/role-assignments-rest.md)
    - [Hinzufügen von Azure-Rollenzuweisungen mithilfe von Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)

Weitere Informationen zu Azure Relay finden Sie in den folgenden Themen:
- [Was ist ein Relay?](relay-what-is-it.md)
- [Erste Schritte mit WebSockets von Azure Relay-Hybridverbindungen](relay-hybrid-connections-dotnet-get-started.md)
- [Erste Schritte mit HTTP-Anforderungen von Azure Relay-Hybridverbindungen](relay-hybrid-connections-http-requests-dotnet-get-started.md)








