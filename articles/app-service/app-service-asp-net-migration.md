---
title: Migrieren von .NET-Apps zu Azure App Service
description: Hier werden die .NET-Migrationsressourcen vorgestellt, die in Azure App Service verfügbar sind.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8918c32e03c6e2c27621dd5c05b9abe976ded511
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095490"
---
# <a name="net-migration-cases-for-azure-app-service"></a>.NET-Migrationsfälle für Azure App Service

Azure App Service bietet benutzerfreundliche Tools, mit denen Sie schnell lokale .NET-Web-Apps ermitteln, die Bereitschaft bewerten und die Inhalte und unterstützten Konfigurationen zu App Service migrieren können.

Diese Tools wurden entwickelt, um verschiedene Szenarios zu unterstützen, insbesondere die Ermittlung, Bewertung und Migration. Im Folgenden werden die .NET-Migrationstools und Anwendungsfälle aufgeführt.

## <a name="migrate-from-multiple-servers-at-scale-preview"></a>Migrieren von mehreren Servern im großen Stil (Vorschauversion)

<!-- Intent: discover how to assess and migrate at scale. -->

Für Azure Migrate wurde vor Kurzem die Ermittlung und Bewertung von ASP.NET-Web-Apps ohne Agent und im großen Stil angekündigt. Sie können jetzt ganz einfach ASP.NET-Web-Apps ermitteln, die auf IIS-Servern in einer VMware-Umgebung ausgeführt werden, und deren Migrationsbereitschaft zu Azure App Service bewerten. Mithilfe der Bewertung können Sie neben der Migrationsbereitschaft auch Hindernisse, Anleitungen zu Problembehebungen, SKU-Empfehlungen und Hostingkosten ermitteln. Im Folgenden finden Sie Ressourcen für die Migration im großen Stil.

### <a name="at-scale-migration-resources"></a>Ressourcen für die Migration im großen Stil

| Gewusst wie |
|----------------|
| [Ermitteln von Web-Apps und SQL Server-Instanzen](../migrate/how-to-discover-sql-existing-project.md)                              |
| [Erstellen einer Azure App Service-Bewertung](../migrate/how-to-create-azure-app-service-assessment.md)                            |
| [Tutorial: Bewerten von Web-Apps für die Migration zu Azure App Service](../migrate/tutorial-assess-webapps.md)                       |
| [Ermitteln von Softwareinventar auf lokalen Servern mit Azure Migrate](../migrate/how-to-discover-applications.md)           |
| **Blog** |
| [Ermitteln und Bewerten von ASP.NET-Apps im großen Stil mit Azure Migrate](https://azure.microsoft.com/blog/discover-and-assess-aspnet-apps-atscale-with-azure-migrate/) |
| **Häufig gestellte Fragen** |
| [Azure App Service-Bewertungen in Azure Migrate Discovery und Bewertungstool](../migrate/concepts-azure-webapps-assessment-calculation.md) |
| **bewährten Methoden** |
| [Bewährte Bewertungsmethoden des Azure Migrate-Tools für Ermittlung und Bewertung](../migrate/best-practices-assessment.md) |
| **Video** |
| [Ermittlung und Bewertung im großen Stil für die Migration von ASP.NET-Apps mit Azure Migrate](https://channel9.msdn.com/Shows/Inside-Azure-for-IT/At-scale-discovery-and-assessment-for-ASPNET-app-migration-with-Azure-Migrate) |

## <a name="migrate-from-an-iis-server"></a>Migrieren von einem IIS-Server

<!-- Intent: discover how to assess and migrate from a single IIS server  -->

Sie können ASP.NET-Web-Apps mithilfe von [PowerShell-Skripts](https://github.com/Azure/App-Service-Migration-Assistant/wiki/PowerShell-Scripts) [(herunterladen)](https://appmigration.microsoft.com/api/download/psscriptpreview/AppServiceMigrationScripts.zip) von einem IIS-Server migrieren, der mit dem entsprechenden Azure Migrate-Feature ermittelt wurde. Sehen Sie sich das Video [Updates für die Migration zu Azure App Service](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service) an.

## <a name="aspnet-web-app-migration"></a>Migration von ASP.NET-Web-Apps
<!-- Intent: migrate a single web app -->

Mit dem App Service Migration Assistant können Sie [eigenständige lokale ASP.NET-Web-Apps zu Azure App Service migrieren](https://www.youtube.com/watch?v=9LBUmkUhmXU). App Service Migration Assistant wurde entwickelt, um Ihre Reise in die Cloud durch eine kostenlose, einfache und schnelle Lösung für die Migration von Anwendungen von On-Premises in die Cloud zu vereinfachen. Weitere Informationen zum Migration Assistant-Tool finden Sie in den [häufig gestellten Fragen](https://github.com/Azure/App-Service-Migration-Assistant/wiki).

## <a name="containerize-an-aspnet-web-app"></a>Containerisieren von ASP.NET-Web-Apps

Einige .NET Framework-Webanwendungen verfügen möglicherweise über Abhängigkeiten von Bibliotheken und anderen Funktionen, die in Azure App Service nicht verfügbar sind. Diese Apps können von anderen Komponenten im globalen Assemblycache abhängig sein. Bisher konnten Sie diese Anwendungen nur auf virtuellen Computern ausführen. Jetzt können Sie sie jedoch in Azure App Service-Windows-Containern ausführen.

Das [App-Containerisierungstool](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) kann Anwendungen mit minimalen Änderungen als Container neu paketieren. Das Tool unterstützt derzeit die Containerisierung von ASP.NET-Anwendungen und Apache Tomcat Java-Anwendungen. Weitere Informationen zur Containerisierung und Migration finden Sie in dieser [Anleitung](../migrate/tutorial-app-containerization-aspnet-app-service.md).

## <a name="next-steps"></a>Nächste Schritte

[Migrieren einer lokalen Web-App zu Azure App Service](/learn/modules/migrate-app-service-migration-assistant/)
