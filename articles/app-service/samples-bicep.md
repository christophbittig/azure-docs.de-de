---
title: Bicep-Beispiele
description: Hier finden Sie Bicep-Beispiele für einige gängige App Service-Szenarien. Hier erfahren Sie, wie Sie Ihre Bereitstellungs- oder Verwaltungsaufgaben für App Service automatisieren.
author: seligj95
tags: azure-service-management
ms.topic: sample
ms.date: 8/26/2021
ms.author: jordanselig
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: c6423d3f77e85918f02bc64224dba86488b7323d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130224275"
---
# <a name="bicep-files-for-app-service"></a>Bicep-Dateien für App Service

In der folgenden Tabelle sind Links zu Bicep-Dateien für Azure App Service enthalten. Schnellstartanleitungen und weitere Informationen zu Bicep finden Sie in der [Bicep-Dokumentation](../azure-resource-manager/bicep/index.yml).

Weitere Informationen zur Bicep-Syntax und zu den Eigenschaften für App Services-Ressourcen finden Sie unter [Microsoft.Web resource types](/azure/templates/microsoft.web/allversions) (Microsoft.Web-Ressourcentypen).

| Bereitstellen einer App | BESCHREIBUNG |
|-|-|
| [App Service-Plan und einfache Linux-App](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-linux) | Stellt eine für Linux konfigurierte App Service-App bereit. |
| [App Service-Plan und einfache Windows-App](https://github.com/Azure/bicep/tree/main/docs/examples/101/web-app-windows) | Stellt eine für Windows konfigurierte App Service-App bereit. |
| [Website mit Container](https://github.com/Azure/bicep/tree/main/docs/examples/101/website-with-container) | Stellt eine Website aus einem für Linux konfigurierten Docker-Image bereit. |
| **Konfigurieren einer App** | **Beschreibung** |
| [App mit bedingter Protokollierung](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-conditional-log)| Stellt eine App Service-App mit einer Bedingung für die Aktivierung der Protokollierung bereit. |
| [App mit Log Analytics-Modul](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-loganalytics-mod)| Stellt eine App Service-App mit Log Analytics bereit. |
| [App mit regionaler VNET-Integration](https://github.com/Azure/bicep/tree/main/docs/examples/101/app-service-regional-vnet-integration)| Stellt eine App Service-App mit aktivierter regionaler VNET-Integration bereit. |
|**App mit verbundenen Ressourcen**| **Beschreibung** |
| [App mit CosmosDB](https://github.com/Azure/bicep/tree/main/docs/examples/101/cosmosdb-webapp)| Stellt eine App Service-App unter Linux mit CosmosDB bereit. |
| [App mit MySQL](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-managed-mysql)| Stellt eine App Service-App unter Windows mit Azure Database for MySQL bereit. |
| [App mit Datenbank in Azure SQL-Datenbank](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-sql-database)| Stellt eine App Service-App und eine Datenbank in Azure SQL-Datenbank auf der Dienstebene „Basic“ bereit. |
| [Mit einer Back-End-Web-App verbundene App](https://github.com/Azure/bicep/tree/main/docs/examples/101/webapp-privateendpoint-vnet-injection)| Stellt zwei Web-Apps (Front-End und Back-End) sicher miteinander verbunden mit VNET-Injektion und privatem Endpunkt bereit. |
| [App mit einer Datenbank, verwalteter Identität und Überwachung](https://github.com/Azure/bicep/tree/main/docs/examples/301/web-app-managed-identity-sql-db)| Stellt eine App Service-App mit einer Datenbank, verwalteter Identität und Überwachung bereit. |
|**App Service-Umgebung**| **Beschreibung** |
| [Erstellen einer App Service-Umgebung v2](https://github.com/Azure/bicep/tree/main/docs/examples/201/web-app-asev2-create) | Erstellt eine App Service-Umgebung v2 in Ihrem virtuellen Netzwerk. |
| | |