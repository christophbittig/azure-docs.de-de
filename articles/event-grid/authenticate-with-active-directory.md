---
title: Authentifizieren von Event Grid-Veröffentlichungsclients mit Azure Active Directory (Vorschau)
description: In diesem Artikel wird beschrieben, wie ein Azure Event Grid-Veröffentlichungsclient mithilfe von Azure Active Directory authentifiziert wird.
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: b15febf10316406489d3f5bad7fc1085624ee96a
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662254"
---
# <a name="authentication-and-authorization-with-azure-active-directory-preview"></a>Authentifizierung und Autorisierung mit Azure Active Directory (Vorschau)
In diesem Artikel wird beschrieben, wie Azure Event Grid-Veröffentlichungsclients mithilfe von Azure Active Directory (Azure AD) authentifiziert werden.

## <a name="overview"></a>Übersicht
Die [Microsoft Identity](../active-directory/develop/v2-overview.md)-Plattform bietet integrierte Authentifizierung und Zugriffssteuerungsverwaltung für Ressourcen und Anwendungen, die Azure Active Directory (Azure AD) als Identitätsanbieter verwenden. Verwenden Sie die Microsoft Identity-Plattform, um Authentifizierungs- und Autorisierungsunterstützung in Ihren Anwendungen bereitzustellen. Sie basiert auf offenen Standards wie OAuth 2.0 und OpenID Connect und bietet Tools und Open-Source-Bibliotheken, die viele Authentifizierungsszenarien unterstützen. Sie bietet erweiterte Features wie [bedingten Zugriff](../active-directory/conditional-access/overview.md), mit denen Sie Richtlinien festlegen können, die mehrstufige Authentifizierung erfordern oder z. B. Zugriff von bestimmten Standorten aus zulassen.

Ein Vorteil, der Ihre Sicherheitslage bei der Verwendung von Azure AD verbessert, besteht darin, dass Sie keine Anmeldeinformationen, wie z. B. Authentifizierungsschlüssel, im Code oder in Repositorys speichern müssen. Stattdessen verlassen Sie sich auf den Erhalt von OAuth 2.0-Zugriffstoken von der Microsoft Identity-Plattform, die Ihre Anwendung bei der Authentifizierung mit einer geschützten Ressource verwendet. Sie können Ihre Anwendung für die Ereignisveröffentlichung bei Azure AD registrieren und einen Dienstprinzipal abrufen, der Ihrer App zugeordnet ist, die Sie verwalten und verwenden. Stattdessen können Sie [verwaltete Identitäten](../active-directory/managed-identities-azure-resources/overview.md) (systemseitig oder vom Benutzer zugewiesen) für ein noch einfacheres Identitätsverwaltungsmodell verwenden, da einige Aspekte des Identitätslebenszyklus für Sie verwaltet werden. 

Mit [rollenbasiertes Zugriffssteuerung](../active-directory/develop/custom-rbac-for-developers.md) (RBAC) können Sie Autorisierung so konfigurieren, dass bestimmte Sicherheitsprinzipale (Identitäten für Benutzer, Gruppen oder Apps) über bestimmte Berechtigungen zum Ausführen von Vorgängen für Azure-Ressourcen verfügen. Auf diese Weise muss dem Sicherheitsprinzipal, der von einer Clientanwendung verwendet wird, die Ereignisse an Event Grid sendet, die RBAC-Rolle **EventGrid-Datensender** zugeordnet werden. 

### <a name="security-principals"></a>Sicherheitsprinzipale
Es gibt zwei allgemeine Kategorien von Sicherheitsprinzipalen, die bei Überlegungen zur Authentifizierung eines Event Grid-Veröffentlichungsclients Anwendung finden: 

- **Verwaltete Identitäten**. Eine verwaltete Identität kann vom System zugewiesen werden, die Sie für eine Azure-Ressource aktivieren. Sie wird nur dieser Ressource zugeordnet. Oder sie kann benutzerseitig zugewiesen werden, den Sie explizit erstellen und benennen. Benutzerseitig zugewiesene verwaltete Identitäten können mehreren Ressourcen zugeordnet werden.
- **Anwendungssicherheitsprinzipal**. Es handelt sich um einen Typ von Sicherheitsprinzipal, der eine Anwendung darstellt, die auf Ressourcen zugreift, die durch Azure AD geschützt sind. 

Unabhängig vom verwendeten Sicherheitsprinzipal, einer verwalteten Identität oder einem Anwendungssicherheitsprinzipal verwendet Ihr Client diese Identität, um sich bei Azure AD zu authentifizieren und ein [OAuth 2.0-Zugriffstoken](../active-directory/develop/access-tokens.md) abzurufen, das mit Anforderungen gesendet wird, wenn Ereignisse an Event Grid gesendet werden. Dieses Token wird kryptografisch signiert. Sobald es von Event Grid empfangen wird, wird das Token überprüft. Beispielsweise wird die Zielgruppe (der beabsichtigte Empfänger des Tokens) als Event Grid (`https://eventgrid.azure.net`) bestätigt. Das Token enthält Informationen zur Clientidentität. Event Grid überprüft anhand dieser Identität, ob dem Client die Rolle **EventGrid-Datensender** zugewiesen ist. Genauer gesagt überprüft Event Grid, ob die Identität über die ``Microsoft.EventGrid/events/send/action``-Berechtigung in einer RBAC-Rolle verfügt, die der Identität zugeordnet ist, bevor die Ereignisveröffentlichungsanforderung abgeschlossen werden kann. 
 
Wenn Sie das Event Grid-SDK verwenden, müssen Sie sich keine Gedanken darüber machen, wie Sie den Abruf von Zugriffstoken implementieren und in jede Anforderung an Event Grid integrieren, da dies von den [Event Grid-Datenebenen-SDKs](#publish-events-using-event-grids-client-sdks) für Sie erledigt wird. 

### <a name="high-level-steps"></a>Allgemeine Schritte
Führen Sie die folgenden Schritte aus, um Ihren Client für die Verwendung von Azure AD-Authentifizierung beim Senden von Ereignissen an ein Thema, eine Domäne oder einen Partnernamespace vorzubereiten.

1. Erstellen oder verwenden Sie einen Sicherheitsprinzipal, den Sie für die Authentifizierung verwenden möchten. Sie können eine verwaltete [Identität](#authenticate-using-a-managed-identity) oder einen [Anwendungssicherheitsprinzipal](#authenticate-using-a-security-principal-of-a-client-application) verwenden.
2. [Erteilen Sie einem Sicherheitsprinzipal die Berechtigung zum Veröffentlichen von Ereignissen](#assign-permission-to-a-security-principal-to-publish-events), indem Sie dem Sicherheitsprinzipal die Rolle **EventGrid-Datensender** zuweisen.
3. Verwenden Sie das Event Grid-SDK, um Ereignisse in einer Event Grid-Instanz zu veröffentlichen.

## <a name="authenticate-using-a-managed-identity"></a>Authentifizieren mit einer verwalteten Identität

Verwaltete Identitäten sind Identitäten, die Azure-Ressourcen zugeordnet sind. Verwaltete Identitäten stellen eine Identität bereit, die Anwendungen verwenden, wenn sie Azure-Ressourcen nutzen, die Azure AD-Authentifizierung unterstützen. Anwendungen können die verwaltete Identität der Hostingressource (z. B. eines virtuellen Computers oder eines Azure-App-Diensts) verwenden, um Azure AD-Token abzurufen, die bei der Veröffentlichung von Ereignissen in Event Grid mit der Anforderung übermittelt werden. Wenn die App eine Verbindung herstellt, bindet Event Grid den Kontext der verwalteten Entität an den Client. Nach der Zuordnung zu einer verwalteten Identität können vom Event Grid-Veröffentlichungsclient sämtliche autorisierten Vorgänge ausgeführt werden. Die Autorisierung wird durch Zuordnen einer verwalteten Entität zu einer RBAC-Rolle von Event Grid gewährt.

Die verwaltete Identität stellt für Azure-Dienste eine automatisch verwaltete Identität in Azure AD bereit. Im Gegensatz zu anderen Authentifizierungsmethoden müssen Sie weder Zugriffsschlüssel noch Shared Access Signatures (SAS) in Ihrem Anwendungscode oder ihrer Konfiguration speichern und schützen, weder für die Identität selbst noch für die Ressourcen, auf die Sie zugreifen müssen.

Um Ihren Ereignisveröffentlichungsclient mit verwalteten Identitäten zu authentifizieren, entscheiden Sie sich zuerst für den Azure-Hostingdienst für Ihre Clientanwendung und aktivieren dann system- oder benutzerseitig zugewiesene verwaltete Identitäten für diese Azure-Dienstinstanz. Beispielsweise können Sie verwaltete Identitäten für eine [VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md), eine Azure App Service-Instanz oder [Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet) aktivieren. 

Nachdem Sie eine verwaltete Identität in einem Hostingdienst konfiguriert haben, [weisen Sie dieser Identität die Berechtigung zum Veröffentlichen von Ereignissen zu](#assign-permission-to-a-security-principal-to-publish-events).

## <a name="authenticate-using-a-security-principal-of-a-client-application"></a>Authentifizieren mithilfe eines Sicherheitsprinzipals einer Clientanwendung

Neben verwalteten Identitäten besteht eine weitere Identitätsoption in der Erstellung eines Sicherheitsprinzipals für Ihre Clientanwendung. Zu diesem Zweck müssen Sie Ihre Anwendung bei Azure AD registrieren. Die Registrierung Ihrer Anwendung ist eine Geste, mit der Sie die Identitäts- und Zugriffsverwaltungssteuerung an Azure AD delegieren. Führen Sie die Schritte im Abschnitt [Registrieren einer Anwendung](../active-directory/develop/quickstart-register-app.md#register-an-application) und im Abschnitt [Hinzufügen eines Clientgeheimnisses](../active-directory/develop/quickstart-register-app.md#add-a-client-secret) aus. Überprüfen Sie unbedingt die [Voraussetzungen](../active-directory/develop/quickstart-register-app.md#prerequisites), bevor Sie beginnen.

Sobald Sie über einen Anwendungssicherheitsprinzipal verfügen und die oben genannten Schritte befolgt haben, [weisen Sie die Berechtigung zum Veröffentlichen von Ereignissen für diese Identität zu](#assign-permission-to-a-security-principal-to-publish-events).

> [!NOTE]
> Wenn Sie eine Anwendung im Portal registrieren, werden automatisch ein [Anwendungsobjekt](../active-directory/develop/app-objects-and-service-principals.md#application-object) und ein [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) in Ihrem Basismandanten erstellt. Alternativ können Sie Microsot-Graph verwenden, um Ihre Anwendung zu registrieren. Wenn Sie eine Anwendung mit den Microsoft Graph-APIs registrieren oder erstellen, wird das Dienstprinzipalobjekt jedoch in einem separaten Schritt erstellt. 

## <a name="assign-permission-to-a-security-principal-to-publish-events"></a>Zuweisen der Berechtigung zu einem Sicherheitsprinzipal zum Veröffentlichen von Ereignissen

Die Identität, die zum Veröffentlichen von Ereignissen in Event Grid verwendet wird, muss über die Berechtigung ``Microsoft.EventGrid/events/send/action`` verfügen, die es ihr ermöglicht, Ereignisse an Event Grid zu senden. Diese Berechtigung ist in der integrierten RBAC-Rolle [EventGrid-Datensender](../role-based-access-control/built-in-roles.md#eventgrid-data-sender) enthalten. Diese Rolle kann einem [Sicherheitsprinzipal](../role-based-access-control/overview.md#security-principal) für einen bestimmten [Bereich](../role-based-access-control/overview.md#scope) zugewiesen werden. Dies kann eine Verwaltungsgruppe, ein Azure-Abonnement, eine Ressourcengruppe oder ein bestimmtes Event Grid-Thema, eine Domäne oder ein Partnernamespace sein. Führen Sie die Schritte unter [Zuweisen von Azure-Rollen](../role-based-access-control/role-assignments-portal.md?tabs=current) aus, um einem Sicherheitsprinzipal die Rolle **EventGrid-Datensender** zuzuweisen und auf diese Weise einer Anwendung mithilfe dieses Sicherheitsprinzipals Zugriff zum Senden von Ereignissen zu gewähren. Alternativ können Sie eine [benutzerdefinierte Rolle](../role-based-access-control/custom-roles.md) definieren, die die Berechtigung ``Microsoft.EventGrid/events/send/action`` enthält, und diese benutzerdefinierte Rolle Ihrem Sicherheitsprinzipal zuweisen.

Da nun die RBAC-Privilegien geregelt sind, können Sie Ihre [Clientanwendung erstellen, um Ereignisse an Event Grid zu senden](#publish-events-using-event-grids-client-sdks).

> [!NOTE]
> Event Grid unterstützt weitere RBAC-Rollen für Zwecke, die über das Senden von Ereignissen hinausgehen. Weitere Informationen finden Sie unter [Integrierte Event Grid-Rollen](security-authorization.md#built-in-roles).


## <a name="publish-events-using-event-grids-client-sdks"></a>Veröffentlichen von Ereignissen mit Client-SDKs von Event Grid

Verwenden Sie das [Datenebenen-SDK von Event Grid](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/), um Ereignisse in Event Grid zu veröffentlichen. Event Grid-SDKs unterstützen alle Authentifizierungsmethoden, einschließlich Azure AD-Authentifizierung. 

### <a name="prerequisites"></a>Voraussetzungen

Im Folgenden finden Sie die Voraussetzungen für die Authentifizierung bei Event Grid.

- Installieren Sie das SDK für Ihre Anwendung.
   - [Java](/java/api/overview/azure/messaging-eventgrid-readme#include-the-package)
   - [.NET](/dotnet/api/overview/azure/messaging.eventgrid-readme-pre#install-the-package)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventgrid/eventgrid#install-the-azureeventgrid-package)
   - [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/eventgrid/azure-eventgrid#install-the-package)
- Installieren Sie die Azure Identity-Clientbibliothek. Das Event Grid-SDK ist für Authentifizierung von der Azure Identity-Clientbibliothek abhängig. 
   - [Azure Identity-Clientbibliothek für Java](/java/api/overview/azure/identity-readme)
   - [Azure Identity-Clientbibliothek für .NET](/dotnet/api/overview/azure/identity-readme)
   - [Azure Identity-Clientbibliothek für JavaScript](/javascript/api/overview/azure/identity-readme)
   - [Azure Identity-Clientbibliothek für Python](/python/api/overview/azure/identity-readme)
- Ein Thema, eine Domäne oder ein Partnernamespace, an das/die/den Ihre Anwendung Ereignisse sendet.

### <a name="publish-events-using-azure-ad-authentication"></a>Veröffentlichen von Ereignissen mit Azure AD-Authentifizierung

Um Ereignisse an ein Thema, eine Domäne oder einen Partnernamespace zu senden, können Sie den Client auf folgende Weise erstellen. Die API-Version, die zuerst Unterstützung für Azure AD-Authentifizierung bereitgestellt hat, ist ``2021-06-01-preview``. Verwenden Sie diese API-Version oder eine neuere Version in Ihrer Anwendung.

```java 
        DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
        EventGridPublisherClient cloudEventClient = new EventGridPublisherClientBuilder()
                .endpoint("<your-event-grid-topic-domain-or-partner-namespace-endpoint>?api-version=2021-06-01-preview")
                .credential(credential)
                .buildCloudEventPublisherClient();
```
Wenn Sie einen Sicherheitsprinzipal verwenden, der einer Clientveröffentlichungsanwendung zugeordnet ist, müssen Sie Umgebungsvariablen konfigurieren, wie im Artikel zur [Java SDK-Infodatei](/java/api/overview/azure/identity-readme#environment-variables) gezeigt. `DefaultCredentialBuilder` liest diese Umgebungsvariablen, um die richtige Identität zu verwenden. Weitere Informationen finden Sie unter [Java-API: Übersicht](/java/api/overview/azure/identity-readme#defaultazurecredential).


Weitere Informationen finden Sie in den folgenden Artikeln:

- [Azure Event Grid-Clientbibliothek für Java](/java/api/overview/azure/messaging-eventgrid-readme)
- [Azure Event Grid-Clientbibliothek für .NET](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/eventgrid/Azure.Messaging.EventGrid#authenticate-using-azure-active-directory)
- [Azure Event Grid-Clientbibliothek für JavaScript](/javascript/api/overview/azure/eventgrid-readme)
- [Azure Event Grid-Clientbibliothek für Python](/python/api/overview/azure/eventgrid-readme)

## <a name="disable-key-and-shared-access-signature-authentication"></a>Deaktivieren von Zugriffsschlüssel- und SAS-Authentifizierung (Shared Access Signature)

Azure AD-Authentifizierung bietet eine bessere Authentifizierungsunterstützung als Authentifizierung mit Zugriffsschlüsseln oder SAS-Token (Shared Access Signature). Bei Azure AD-Authentifizierung wird die Identität anhand des Azure AD-Identitätsanbieters überprüft. Als Entwickler müssen Sie keine Schlüssel in Ihrem Code verwalten, wenn Sie Azure AD-Authentifizierung verwenden. Sie profitieren auch von allen Sicherheitsfeatures, die in die Microsoft Identity-Plattform integriert sind, z. B. von [bedingtem Zugriff](../active-directory/conditional-access/overview.md). Diese Features können Ihnen helfen, die Sicherheit Ihrer Anwendung zu verbessern. 

Sobald Sie sich für die Verwendung von Azure AD-Authentifizierung entschieden haben, können Sie Authentifizierung auf Grundlage von Zugriffsschlüsseln oder SAS-Tokens deaktivieren. 

> [!NOTE]
> Authentifizierung mit Zugriffsschlüsseln oder SAS-Token ist eine Form der **lokalen Authentifizierung**. Wenn es um diese Kategorie von Authentifizierungsmechanismen geht, die sich nicht auf Azure AD stützen, wird manchmal von „lokaler Authentifizierung“ gesprochen. Der API-Parameter, der zum Deaktivieren der lokalen Authentifizierung verwendet wird, wird entsprechend ``disableLocalAuth`` genannt.

Der folgende CLI-Befehl zeigt, wie Sie ein benutzerdefiniertes Thema mit deaktivierter lokaler Authentifizierung erstellen. Das Feature zum Deaktivieren lokaler Authentifizierung ist derzeit als Vorschau verfügbar, und Sie müssen API-Version ``2021-06-01-preview`` verwenden.

```cli
az resource create --subscription <subscriptionId> --resource-group <resourceGroup> --resource-type Microsoft.EventGrid/topics --api-version 2021-06-01-preview --name <topicName> --location <location> --properties "{ \"disableLocalAuth\": true}"
```

Als Referenz werden die folgenden Ressourcentypwerte angegeben, die Sie gemäß dem Thema verwenden können, das Sie erstellen oder aktualisieren.

| Thementyp        | Ressourcentyp                        |
| ------------------| :------------------------------------|
| Domänen           | Microsoft.EventGrid/domains          |
| Partnernamespace | Microsoft.EventGrid/partnerNamespaces|
| Benutzerdefiniertes Thema      | Microsoft.EventGrid/topics           |

Wenn Sie PowerShell verwenden, verwenden Sie die folgenden Cmdlets, um ein benutzerdefiniertes Thema mit deaktivierter lokaler Authentifizierung zu erstellen. 

```PowerShell

Set-AzContext -SubscriptionId <SubscriptionId>

New-AzResource -ResourceGroupName <ResourceGroupName> -ResourceType Microsoft.EventGrid/topics -ApiVersion 2021-06-01-preview -ResourceName <TopicName> -Location <Location> -Properties @{disableLocalAuth=$true}
```

> [!NOTE]
> - Informationen zur Verwendung von Zugriffsschlüssel- oder SAS-Authentifizierung finden Sie unter [Authentifizieren von Veröffentlichungsclients mit Schlüsseln oder SAS-Token](security-authenticate-publishing-clients.md).
> - In diesem Artikel wird Authentifizierung beim Veröffentlichen von Ereignissen in Event Grid (Ereigniseingang) behandelt. Authentifizierung von Event Grid bei der Übermittlung von Ereignissen (Ereigniseingang) ist das Thema des Artikels [Authentifizieren der Ereignisübermittlung an Ereignishandler](security-authentication.md). 

## <a name="resources"></a>Ressourcen
- SDKs für Datenebenen
    - Java-SDK: [github](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid) | [samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventgrid/azure-messaging-eventgrid/src/samples/java/com/azure/messaging/eventgrid) | [migration guide from previous SDK version](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventgrid/azure-messaging-eventgrid/migration-guide.md)
    - .NET-SDK: [github](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid) | [samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventgrid/Azure.Messaging.EventGrid/samples) | [migration guide from previous SDK version](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventgrid/Azure.Messaging.EventGrid/MigrationGuide.md)
    - Python-SDK: [github](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid) | [samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventgrid/azure-eventgrid/samples) | [migration guide from previous SDK version](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventgrid/azure-eventgrid/migration_guide.md)
    - JavaScript-SDK: [github](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/) | [samples](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventgrid/eventgrid/samples) | [migration guide from previous SDK version](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventgrid/eventgrid/MIGRATION.md)
- [Blog zum Event Grid-SDK](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)
- Azure Identity-Clientbibliothek
   - [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/identity/azure-identity/README.md)
   - [.NET](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/identity/Azure.Identity/README.md)  
   - [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/identity/azure-identity/README.md)
   - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md)
- Weitere Informationen zu [verwalteten Identitäten](../active-directory/managed-identities-azure-resources/overview.md)
- Weitere Informationen zum [Verwenden verwalteter Identitäten für App Service und Azure Functions](../app-service/overview-managed-identity.md?tabs=dotnet)
- Weitere Informationen zu [Anwendungen und Dienstprinzipalen](../active-directory/develop/app-objects-and-service-principals.md)
- Weitere Informationen zum[Registrieren einer Anwendung bei der Microsoft Identity-Plattform](../active-directory/develop/quickstart-register-app.md).
- Weitere Informationen zur Funktionsweise der [Autorisierung](../role-based-access-control/overview.md) (RBAC-Zugriffssteuerung).
- Weitere Informationen zu integrierten RBAC-Rollen on Event Grid, einschließlich der [EventGrid-Rolle „Datensender“](../role-based-access-control/built-in-roles.md#eventgrid-data-sender). [Rollenliste von Event Grid](security-authorization.md#built-in-roles).
- Weitere Informationen zum [Zuweisen von RBAC-Rollen](../role-based-access-control/role-assignments-portal.md?tabs=current) zu Identitäten.
- Weitere Informationen zum [Definieren benutzerdefinierter RBAC-Rollen](../role-based-access-control/custom-roles.md).
- Weitere Informationen zu [Anwendungs- und Dienstprinzipalobjekten in Azure AD](../active-directory/develop/app-objects-and-service-principals.md).
- Weitere Informationen zu [Zugriffstoken der Microsoft Identity-Plattform](../active-directory/develop/access-tokens.md).
- Weitere Informationen zum [OAuth 2.0-Authentifizierungscodefluss und der Microsoft Identity-Plattform](../active-directory/develop/v2-oauth2-auth-code-flow.md).
