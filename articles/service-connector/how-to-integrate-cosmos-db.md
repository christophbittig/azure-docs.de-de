---
title: Integrieren von Azure Cosmos DB mit Service Connector
description: Integrieren Sie Azure Cosmos DB mit Service Connector in Ihre Anwendung
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 11/11/2021
ms.openlocfilehash: 8bdd4a23ac0736f2ff4ddf3095f0d77b2af066ce
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373486"
---
# <a name="integrate-azure-cosmos-db-with-service-connector"></a>Integrieren von Azure Cosmos DB mit Service Connector

Diese Seite zeigt die unterstützten Authentifizierungstypen und Clienttypen von Azure Cosmos DB mit Service Connector. Möglicherweise können Sie auch in anderen Programmiersprachen eine Verbindung zu Azure Cosmos DB herstellen, ohne Service Connector zu verwenden. Diese Seite zeigt auch den Standardumgebungsvariablennamen und -wert (oder die Spring Boot-Konfiguration) an, die Sie beim Erstellen der Dienstverbindung erhalten. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .NET | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Java  | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Java - Spring Boot | | | ![Ja-Symbol](./media/green-check.png) | |
| Node.js | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |
| Go | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) | ![Ja-Symbol](./media/green-check.png) |


## <a name="default-environment-variable-names-or-application-properties"></a>Namen von Standard-Umgebungsvariablen oder Anwendungseigenschaften

### <a name="dotnet-java-nodejs-and-go"></a>Dotnet, Java, Nodejs und Go

**Geheimkennwort/Verbindungszeichenfolge**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_COSMOS_CONNECTIONSTRING | Mango DB in Cosmos DB-Verbindungszeichenfolge | `mongodb://{mango-db-admin-user}:{********}@{mango-db-server}.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&retrywrites=false&maxIdleTimeMS=120000&appName=@{mango-db-server}@` |

**Systemseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | Die URL zum Abrufen der Verbindungszeichenfolge | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Ihr verwalteter Identitätsumfang | `https://management.azure.com/.default` |
| AZURE_COSMOS_RESOURCEENDPOINT | Ihr Ressourcenendpunkt| `https://{your-database-server}.documents.azure.com:443/` |

**Benutzerseitig zugewiesene verwaltete Identität**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | Die URL zum Abrufen der Verbindungszeichenfolge | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Ihr verwalteter Identitätsumfang | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | Die ID Ihres geheimen Clientschlüssels | `{client-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | Ihre Abonnement-ID | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | Ihr Ressourcenendpunkt| `https://{your-database-server}.documents.azure.com:443/` |

**Dienstprinzipal**

| Standardname der Umgebungsvariablen | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_COSMOS_LISTCONNECTIONSTRINGURL | Die URL zum Abrufen der Verbindungszeichenfolge | `https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group-name}/providers/Microsoft.DocumentDB/databaseAccounts/{your-database-server}/listConnectionStrings?api-version=2021-04-15` |
| AZURE_COSMOS_SCOPE | Ihr verwalteter Identitätsumfang | `https://management.azure.com/.default` |
| AZURE_COSMOS_CLIENTID | Die ID Ihres geheimen Clientschlüssels | `{client-id}` |
| AZURE_COSMOS_CLIENTSECRET | Das Geheimnis Ihres geheimen Clientschlüssels | `{client-secret}` |
| AZURE_COSMOS_TENANTID | Ihre Mandanten-ID | `{tenant-id}` |
| AZURE_COSMOS_SUBSCRIPTIONID | Ihre Abonnement-ID | `{your-subscription-id}` |
| AZURE_COSMOS_RESOURCEENDPOINT | Ihr Ressourcenendpunkt| `https://{your-database-server}.documents.azure.com:443/` |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
