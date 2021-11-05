---
title: Migrieren von Java-Apps zu Azure App Service
description: Hier werden die in Azure App Service verfügbaren Java-Migrationsressourcen vorgestellt.
author: msangapu-msft
ms.topic: article
ms.date: 03/29/2021
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f876445673d0237af33f10e3e5b599032ba28bd7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095537"
---
# <a name="java-migration-resources-for-azure-app-service"></a>Java-Migrationsressourcen für Azure App Service

Azure App Service bietet Tools zum Ermitteln von Web-Apps, die auf lokalen Webservern bereitgestellt werden. Sie können die Bereitschaft dieser Apps überprüfen und sie dann zu App Service migrieren. Sowohl der Web-App-Inhalt als auch die unterstützte Konfiguration können zu App Service migriert werden. Diese Tools wurden entwickelt, um eine Vielzahl von Szenarien zu unterstützen, insbesondere die Ermittlung, Bewertung und Migration.

## <a name="java-tomcat-migration-linux"></a>Java-Tomcat-Migration (Linux)

[Laden Sie den Assistenten herunter](https://azure.microsoft.com/services/app-service/migration-assistant/), um eine Java-App zu migrieren, die auf einem Apache Tomcat-Webserver ausgeführt wird. Sie können auch Azure Container Registry verwenden, um lokale Linux-Docker-Container zu App Service zu migrieren.

| Ressourcen |
|-----------|
| **Gewusst wie** |
| [Wiki: Java App-Service-Migration-Assistant](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information) |
| [Wiki: Azure/App-Service-Migration-Assistant](https://github.com/Azure/App-Service-Migration-Assistant/wiki/Linux-Notes) |
| **Videos** |
|[Verweisen von Java-Apps auf Azure App Service und Migrieren dieser Apps mithilfe des Migrationssystems](https://www.youtube.com/watch?v=Mpxa0KE0X9k) |

## <a name="standalone-tomcat-web-app-migration-windows-os"></a>Eigenständige Tomcat-Web-App-Migration (Windows-Betriebssystem)

Laden Sie dieses [Vorschautool](https://azure.microsoft.com/services/app-service/migration-assistant/) herunter, um eine Java-Web-App in Apache Tomcat zu App Service unter Windows zu migrieren. Weitere Informationen finden Sie in [diesem Video](https://channel9.msdn.com/Shows/The-Launch-Space/Updates-on-Migrating-to-Azure-App-Service) und in [dieser Anleitung](https://github.com/Azure/App-Service-Migration-Assistant/wiki/TOMCAT-Java-Information).

## <a name="containerize-standalone-tomcat-web-app"></a>Containerisieren einer eigenständigen Tomcat-Web-App

Die Anwendungscontainerisierung bietet einen einfachen Ansatz zum Neupaketieren von Anwendungen als Container mit minimalen Codeänderungen. Das Tool unterstützt derzeit die Containerisierung von ASP.NET- und Apache Tomcat-Java-Webanwendungen. Weitere Informationen finden Sie in [diesem Blog](https://azure.microsoft.com/blog/accelerate-application-modernization-with-azure-migrate-app-containerization/) und in [dieser Anleitung](../migrate/tutorial-app-containerization-java-app-service.md).

## <a name="next-steps"></a>Nächste Schritte

[Migrieren von Tomcat-Anwendungen zu Tomcat unter Azure App Service](/azure/developer/java/migration/migrate-tomcat-to-tomcat-app-service)
