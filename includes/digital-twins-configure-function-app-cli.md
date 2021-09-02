---
author: baanders
description: Include-Datei, die beschreibt, wie eine Azure-Funktion für die Zusammenarbeit mit Azure Digital Twins konfiguriert wird – CLI-Anweisungen
ms.service: digital-twins
ms.topic: include
ms.date: 7/20/2021
ms.author: baanders
ms.openlocfilehash: fffa878ca69b0704417c7db7492eb5523217e4e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801284"
---
Führen Sie die folgenden Befehle in [Azure Cloud Shell](https://shell.azure.com) oder einer [lokalen Installation der Azure CLI](/cli/azure/install-azure-cli) aus.

#### <a name="assign-an-access-role"></a>Zuweisen einer Zugriffsrolle

Die Azure-Funktion erfordert die Übergabe eines Bearertokens. Um sicherzustellen, dass das Bearertoken übergeben wird, erteilen Sie der Funktions-App die Rolle **Azure Digital Twins-Datenbesitzer** für Ihre Azure Digital Twins-Instanz, die der Funktions-App die Berechtigung zum Ausführen von Datenebenenaktivitäten für die Instanz erteilt.

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

1. Verwenden Sie den folgenden Befehl, um die Details der [systemseitig verwalteten Identität](../articles/active-directory/managed-identities-azure-resources/overview.md) für Ihre Funktion anzuzeigen. Beachten Sie in der Ausgabe das Feld `principalId`. Sie verwenden diese ID, um auf die Funktion zu verweisen, damit Sie ihr im nächsten Schritt Berechtigungen erteilen können.

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > Falls die Ergebnisanzeige leer ist und keine Details einer Identität angezeigt werden, sollten Sie mit diesem Befehl eine neue systemseitig verwaltete Identität erstellen:
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > In der Ausgabe werden Details zur Identität angezeigt, z. B. der für den nächsten Schritt benötigte Wert für `principalId`. 

1. Verwenden Sie den Wert `principalId` im folgenden Befehl, um der Funktion die Rolle **Azure Digital Twins-Datenbesitzer** für Ihre Azure Digital Twins-Instanz zuzuweisen.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

#### <a name="configure-application-settings"></a>Konfigurieren von Anwendungseinstellungen

Ermöglichen Sie Ihrer Funktion dann den Zugriff auf die URL Ihrer **Azure Digital Twins-Instanz**, indem Sie eine [Umgebungsvariable](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal#use-application-settings) für sie festlegen.

> [!TIP]
> Die URL der Azure Digital Twins-Instanz wird durch das Hinzufügen von *https://* am Anfang des Hostnamens der Instanz erstellt. Um den Hostnamen mit allen Eigenschaften der Instanz anzuzeigen, führen Sie Folgendes aus: `az dt show --dt-name <your-Azure-Digital-Twins-instance>`.

Der folgende Befehl legt eine Umgebungsvariable für die URL Ihrer Instanz fest, die Ihre Funktion immer dann verwendet, wenn sie auf die Instanz zugreifen muss.

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```