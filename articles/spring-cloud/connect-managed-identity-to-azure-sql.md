---
title: Verwenden der verwalteten Identität zum Herstellen einer Verbindung zwischen Azure SQL und der Azure Spring Cloud-App
description: Richten Sie eine verwaltete Identität ein, um Azure SQL mit einer Azure Spring Cloud-App zu verbinden.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 03/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: e950667e1132c8eba350a5ffcada5e5d667a1288
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485638"
---
# <a name="use-a-managed-identity-to-connect-azure-sql-database-to-an-azure-spring-cloud-app"></a>Verwenden einer verwalteten Identität zum Herstellen einer Verbindung zwischen Azure SQL-Datenbank und der Azure Spring Cloud-App

**Dieser Artikel gilt für:** ✔️ Java

Dieser Artikel zeigt, wie Sie eine verwaltete Identität für eine Azure Spring Cloud-App erstellen und damit auf Azure SQL-Datenbank zugreifen können.

[Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) ist ein intelligenter, skalierbarer und relationaler Datenbankdienst, der für die Cloud entwickelt wurde. Der Dienst ist immer auf dem neuesten Stand und bietet KI-gestützte und automatisierte Features, die Leistung und Dauerhaftigkeit optimieren. Serverlose Compute- und Hyperscale-Speicheroptionen skalieren Ressourcen automatisch nach Bedarf, sodass Sie sich nicht mehr um Speichergrößen oder Ressourcenverwaltung kümmern müssen, sondern sich vollständig auf die Entwicklung neuer Anwendungen konzentrieren können.

## <a name="prerequisites"></a>Voraussetzungen

* Folgen Sie dem [Spring Data JPA-Tutorial](/azure/developer/java/spring-framework/configure-spring-data-jpa-with-azure-sql-server), um eine Azure SQL-Datenbank bereitzustellen und sie lokal mit einer Java-App verwenden zu können.
* Befolgen Sie das [Tutorial für systemzugeordnete verwaltete Identitäten in Azure Spring Cloud](./how-to-enable-system-assigned-managed-identity.md), um eine Azure Spring Cloud-App mit aktivierter verwalteter Identität bereitzustellen.

## <a name="grant-permission-to-the-managed-identity"></a>Erteilen der Berechtigung für die verwaltete Identität

Stellen Sie eine Verbindung mit Ihrem SQL-Server her, und führen Sie die folgende SQL-Abfrage aus:

```sql
CREATE USER [<MIName>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<MIName>];
ALTER ROLE db_datawriter ADD MEMBER [<MIName>];
ALTER ROLE db_ddladmin ADD MEMBER [<MIName>];
GO
```

Dieser `<MIName>` folgt der Regel: `<service instance name>/apps/<app name>`, z. B. `myspringcloud/apps/sqldemo`. Sie können „MIName“ auch mit der Azure CLI abfragen:

```azurecli
az ad sp show --id <identity object ID> --query displayName
```

## <a name="configure-your-java-app-to-use-managed-identity"></a>Konfigurieren Ihrer Java-App für die Verwendung der verwalteten Identität

Öffnen Sie die Datei *src/main/resources/application.properties*, und fügen Sie am Ende der folgenden Zeile `Authentication=ActiveDirectoryMSI;` hinzu. Achten Sie darauf, den richtigen Wert für die Variable $AZ_DATABASE_NAME zu verwenden.

```properties
spring.datasource.url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;Authentication=ActiveDirectoryMSI;
```

## <a name="build-and-deploy-the-app-to-azure-spring-cloud"></a>Erstellen und Bereitstellen der App in Azure Spring Cloud

Erstellen Sie die App neu, und stellen Sie sie in der Azure Spring Cloud-App bereit, die im zweiten Aufzählungspunkt unter „Voraussetzungen“ bereitgestellt wurde. Jetzt verfügen Sie über eine Spring Boot-Anwendung, die durch eine verwaltete Identität authentifiziert ist und JPA verwendet, um Daten aus einer Azure SQL-Datenbank in Azure Spring Cloud zu speichern und abzurufen.

## <a name="next-steps"></a>Nächste Schritte

* [Zugreifen auf das Speicherblob mit der verwalteten Identität in Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/managed-identity-storage-blob)
* [Aktivieren der systemseitig zugewiesenen verwalteten Identität für Anwendungen in Azure Spring Cloud](./how-to-enable-system-assigned-managed-identity.md)
* [Weitere Informationen zu verwalteten Identitäten für Azure-Ressourcen](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Authentifizieren von Azure Spring Cloud mit Key Vault in GitHub Actions](./github-actions-key-vault.md)
