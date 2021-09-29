---
title: Verwaltungsbibliotheken – Azure Event Hubs | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu der Bibliothek, die Sie zum Verwalten von Azure Event Hubs-Namespaces und -Entitäten über .NET verwenden können.
ms.topic: article
ms.date: 09/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 0898ec33e0acb09791ba78a1ac62b2bc7a3cac0b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207701"
---
# <a name="event-hubs-management-libraries"></a>Event Hubs-Verwaltungsbibliotheken

Mit den Event Hubs-Verwaltungsbibliotheken können Sie dynamisch Event Hubs-Namespaces und -Entitäten bereitstellen. Diese Dynamik ermöglicht komplexe Bereitstellungen und Messagingszenarien sowie eine programmgesteuerte Bestimmung der bereitzustellenden Entitäten. Die Bibliotheken sind derzeit für .NET verfügbar.

## <a name="supported-functionality"></a>Unterstützte Funktionen

* Erstellen, Aktualisieren und Löschen von Namespaces
* Erstellen, Aktualisieren und Löschen von Event Hubs
* Erstellen, Aktualisieren und Löschen von Consumergruppen

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung von Event Hubs-Verwaltungsbibliotheken müssen Sie zunächst eine Authentifizierung mit Azure Active Directory (AAD) durchführen. AAD erfordert, dass Sie sich als Dienstprinzipal authentifizieren. Dadurch erhalten Sie Zugriff auf Ihre Azure-Ressourcen. Informationen zum Erstellen eines Dienstprinzipals finden Sie in einem der folgenden Artikel:  

* [Erstellen einer Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md)
* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli)

In diesen Tutorials erhalten Sie Werte für `AppId` (Client-ID), `TenantId` und `ClientSecret` (Authentifizierungsschlüssel). Diese werden von den Verwaltungsbibliotheken für die Authentifizierung verwendet. Für die Ressourcengruppe, für die die Ausführung erfolgen soll, müssen Sie über **Besitzer**-Berechtigungen verfügen.

## <a name="programming-pattern"></a>Muster für die Programmierung

Das Muster zum Bearbeiten einer beliebigen Event Hubs-Ressource folgt einem gemeinsamen Protokoll:

1. Abrufen eines Tokens aus AAD mithilfe der `Microsoft.IdentityModel.Clients.ActiveDirectory`-Bibliothek.
    ```csharp
    var context = new AuthenticationContext($"https://login.microsoftonline.com/{tenantId}");

    var result = await context.AcquireTokenAsync(
        "https://management.core.windows.net/",
        new ClientCredential(clientId, clientSecret)
    );
    ```

1. Erstellen Sie das Objekt `EventHubManagementClient`.
    ```csharp
    var creds = new TokenCredentials(token);
    var ehClient = new EventHubManagementClient(creds)
    {
        SubscriptionId = SettingsCache["SubscriptionId"]
    };
    ```

1. Legen Sie die `CreateOrUpdate`-Parameter auf Ihre spezifischen Werte fest.
    ```csharp
    var ehParams = new EventHubCreateOrUpdateParameters()
    {
        Location = SettingsCache["DataCenterLocation"]
    };
    ```

1. Führen Sie den Aufruf aus.
    ```csharp
    await ehClient.EventHubs.CreateOrUpdateAsync(resourceGroupName, namespaceName, EventHubName, ehParams);
    ```

## <a name="next-steps"></a>Nächste Schritte
* [.NET-Verwaltungsbeispiel](https://github.com/Azure-Samples/event-hubs-dotnet-management/)
* [Microsoft.Azure.Management.EventHub-Referenz](/dotnet/api/Microsoft.Azure.Management.EventHub) 
