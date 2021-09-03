---
title: Authentifizieren mit verwalteten Identitäten für Azure Relay-Ressourcen (Vorschau)
description: In diesem Artikel wird beschrieben, wie Sie mit verwalteten Identitäten auf Azure Relay-Ressourcen zugreifen.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: fa82f41056f3a5a8617bab572b8b4f312861419f
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654181"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-relay-resources-preview"></a>Authentifizieren einer verwalteten Identität mit Azure Active Directory für den Zugriff auf Azure Relay-Ressourcen (Vorschau)
[Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md) ist ein Azure-übergreifendes Feature, mit dem Sie eine sichere Identität für die Bereitstellung erstellen können, in der Ihr Anwendungscode ausgeführt wird. Sie können dieser Identität dann Zugriffssteuerungsrollen zuordnen, um benutzerdefinierte Berechtigungen für den Zugriff auf bestimmte Azure-Ressourcen zu gewähren, die Ihre Anwendung benötigt.

Die Azure-Plattform verwaltet diese Laufzeitidentität mit verwalteten Identitäten. Sie müssen keine Zugriffsschlüssel in Ihrem Anwendungscode speichern und schützen – weder für die Identität selbst noch für die Ressourcen, auf die zugegriffen werden muss. Eine Relay-Client-App, die innerhalb einer Azure App Service-Anwendung oder in einem virtuellen Computer mit aktivierter Unterstützung für verwaltete Identitäten für Azure-Ressourcen ausgeführt wird, muss keine SAS-Regeln und -Schlüssel oder andere Zugriffstoken verarbeiten. Die Client-App benötigt nur die Endpunktadresse des Relay-Namespace. Wenn die App eine Verbindung herstellt, bindet Relay den Kontext der verwalteten Identität an den Client. Der entsprechende Vorgang wird weiter unten in diesem Artikel in einem Beispiel gezeigt. Nach der Zuordnung zu einer verwalteten Identität kann ein Relay-Client alle autorisierten Vorgänge ausführen. Die Autorisierung wird durch Zuordnung von Relay-Rollen zu einer verwalteten Identität gewährt.

[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="enable-managed-identity"></a>Aktivieren einer verwalteten Identität
Aktivieren Sie zunächst die verwaltete Identität für die Azure-Ressource, die auf Azure Relay-Entitäten (Hybridverbindungen oder WCF Relays) zugreifen muss. Wenn Ihre Relay-Clientanwendung beispielsweise auf einem virtuellen Azure-Computer ausgeführt wird, aktivieren Sie die verwaltete Identität für den virtuellen Computer gemäß den Anweisungen im Artikel [Konfigurieren der verwalteten Identität für einen virtuellen Azure-Computer](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). Nachdem Sie diese Einstellung aktiviert haben, wird eine neue verwaltete Dienstidentität in Ihrem Azure Active Directory (Azure AD) erstellt.

Eine Liste der Dienste, die verwaltete Identitäten unterstützen, finden Sie unter [Dienste, die verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

## <a name="assign-an-azure-relay-role-to-the-managed-identity"></a>Zuweisen einer Azure Relay-Rolle zur verwalteten Identität
Nachdem Sie die verwaltete Identität aktiviert haben, weisen Sie der Identität im entsprechenden Bereich eine der Azure Relay-Rollen (Azure Relay-Besitzer, Azure Relay-Listener oder Azure Relay-Absender) zu. Wenn die Azure-Rolle einer verwalteten Identität zugewiesen wurde, wird der verwalteten Identität der Zugriff auf Relay-Entitäten im entsprechenden Bereich erteilt.

Im folgenden Abschnitt wird eine einfache Anwendung verwendet, die unter einer verwalteten Identität auf einer Azure-VM-Instanz ausgeführt wird und auf Relay-Ressourcen zugreift.

## <a name="sample-app-on-vm-accessing-relay-entities"></a>Beispiel-App auf einem virtuellen Computer, der auf Relay-Entitäten zugreift

1. Laden Sie die [Beispielkonsolenanwendung für Hybridverbindungen](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol) von GitHub auf Ihren Compute herunter.
1. [Erstellen Sie eine Azure-VM](../virtual-machines/windows/quick-create-portal.md). Verwenden Sie für dieses Beispiel ein Windows 10-Image. 
1. Aktivieren Sie eine systemseitig oder eine benutzerseitig zugewiesenen verwaltete Identität für die Azure-VM. Anweisungen hierzu finden Sie unter [Aktivieren einer Identität für eine VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). 
1. Weisen Sie der verwalteten Dienstidentität im gewünschten Bereich (Relay-Entität, Relay-Namespace, Ressourcengruppe, Abonnement) eine der Relay-Rollen zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).
1. Erstellen Sie die Konsolen-App lokal auf Ihrem lokalen Computer gemäß den Anweisungen in dem [README-Dokument](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample). 
1. Kopieren Sie die ausführbare Datei aus dem Ordner „\<your local path\>\RoleBasedAccessControl\bin\Debug“ auf den virtuellen Computer. Sie können RDP verwenden, um eine Verbindung mit der Azure-VM herzustellen. Weitere Informationen hierzu finden Sie unter [Gewusst wie: Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](../virtual-machines/windows/connect-logon.md).
1. Führen Sie „RoleBasedAccessControl.exe“ auf dem virtuellen Azure-Computer aus, wie im [README-Dokument](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample) beschrieben. 

    > [!NOTE]
    > Führen Sie dieselben oben beschriebenen Schritte aus, um die [Konsolenanwendung für WCF Relays](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl) auszuführen.

#### <a name="highlighted-code-from-the-sample"></a>Hervorgehobener Code aus dem Beispiel
Hier sehen Sie den Code aus dem Beispiel, der zeigt, wie sie Azure AD-Authentifizierung verwenden, um eine Verbindung mit dem Azure Relay-Dienst herzustellen.  

1. Erstellen Sie ein [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider)-Objekt mithilfe der `TokenProvider.CreateManagedIdentityTokenProvider`-Methode. 
    
    - Wenn Sie eine **systemseitig zugewiesene verwaltete Identität** verwenden:
        ```csharp
        TokenProvider.CreateManagedIdentityTokenProvider();
        ```
    - Wenn Sie eine **benutzerseitig zugewiesene verwaltete Identität** verwenden, erhalten Sie die **Client-ID** für die benutzerseitig zugewiesene verwaltete Identität von der Seite **Verwaltete Identität** im Azure-Portal. Anleitungen finden Sie unter [Auflisten benutzerseitig zugewiesener verwalteter Identitäten](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azp#list-user-assigned-managed-identities).
        ```csharp
        var managedCredential = new ManagedIdentityCredential(clientId);
        tokenProvider = TokenProvider.CreateManagedIdentityTokenProvider(managedCredential);    
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
Weitere Informationen zu Azure Relay finden Sie in den folgenden Themen:
- [Was ist ein Relay?](relay-what-is-it.md)
- [Erste Schritte mit WebSockets von Azure Relay-Hybridverbindungen](relay-hybrid-connections-dotnet-get-started.md)
- [Erste Schritte mit HTTP-Anforderungen von Azure Relay-Hybridverbindungen](relay-hybrid-connections-http-requests-dotnet-get-started.md)



