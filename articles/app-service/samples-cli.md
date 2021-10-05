---
title: Azure CLI-Beispiele für Azure App Service | Microsoft-Dokumentation
description: Hier finden Sie Azure CLI-Beispiele für einige gängige App Service-Szenarien. Hier erfahren Sie, wie Sie Ihre Bereitstellungs- oder Verwaltungsaufgaben für App Service automatisieren.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 09/17/2021
ms.custom: mvc, devx-track-azurecli, seo-azure-cli
keywords: Azure CLI-Beispiele, Azure CLI-Beispiele, Azure CLI-Codebeispiele
ms.openlocfilehash: 622c26488a3a92cfd63e744246014a7e5bdaf71f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676407"
---
# <a name="cli-samples-for-azure-app-service"></a>CLI-Beispiele für Azure App Service

Die folgende Tabelle enthält Links zu Bash-Skripts, die mithilfe der Azure CLI erstellt wurden.

| Skript | BESCHREIBUNG |
|-|-|
|**Erstellen einer App**||
| [Erstellen einer App und Bereitstellen von Dateien über FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellt eine App Service-App und stellt dafür eine Datei über FTP bereit. |
| [Erstellen einer App und Bereitstellen von Code über GitHub](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellt eine App Service-App und stellt Code aus einem öffentlichen GitHub-Repository bereit. |
| [Erstellen einer App mit Continuous Deployment über GitHub](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellt eine App Service-App mit kontinuierlicher Veröffentlichung aus einem GitHub-Repository, das sich in Ihrem Besitz befindet. |
| [Erstellen einer App und Bereitstellen von Code über ein lokales GitHub-Repository](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App und konfiguriert einen Codepush aus einem lokalen Git-Repository. |
| [Erstellen einer App und Bereitstellen von Code in einer Stagingumgebung](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App mit einem Bereitstellungsslot für Änderungen am Stagingcode. |
| [Erstellen einer ASP.NET Core-App in einem Docker-Container](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App unter Linux und lädt ein Docker-Image aus Docker Hub. |
| [Erstellen einer App und Verfügbarmachen der App mit einem privaten Endpunkt](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | Hier werden eine App Service-App und ein privater Endpunkt erstellt. |
|**Konfigurieren der App**||
| [Zuordnen einer benutzerdefinierten Domäne zu einer App](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellt eine App Service-App und ordnet ihr einen benutzerdefinierten Domänennamen zu. |
| [Binden eines benutzerdefinierten TLS-/SSL-Zertifikats an eine App](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellt eine App Service-App und bindet das TLS-/SSL-Zertifikat eines benutzerdefinierten Domänennamens an die App. |
|**Skalieren der App**||
| [Manuelles Skalieren einer App](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App und skaliert sie über zwei Instanzen. |
| [Weltweites Skalieren einer App mit einer Hochverfügbarkeitsarchitektur](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt zwei App Service-Apps in zwei verschiedenen geografischen Regionen und macht sie mithilfe von Azure Traffic Manager über einen zentralen Endpunkt verfügbar. |
|**Schützen einer App**||
| [Integrieren in Azure Application Gateway](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt mithilfe eines Dienstendpunkts und mithilfe von Zugriffsbeschränkungen eine App Service-App und integriert sie in Application Gateway. |
|**Verbinden der App mit Ressourcen**||
| [Verbinden einer App mit einer SQL-Datenbank](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellt eine App Service-App und eine Datenbank in Azure SQL-Datenbank und fügt dann die Datenbank-Verbindungszeichenfolge den App-Einstellungen hinzu. |
| [Verbinden einer App mit einem Speicherkonto](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Erstellt eine App Service-App und ein Speicherkonto und fügt dann die Speicherverbindungszeichenfolge den App-Einstellungen hinzu. |
| [Herstellen einer Verbindung zwischen einer App und einer Azure Cache for Redis-Instanz](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App und eine Azure Cache for Redis-Instanz und fügt den App-Einstellungen anschließend die Redis-Verbindungsdetails hinzu. |
| [Verbinden einer App mit Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App und eine Cosmos DB-Instanz und fügt die Cosmos DB-Verbindungsdetails dann den App-Einstellungen hinzu. |
|**Sichern und Wiederherstellen einer App**||
| [Sichern einer App](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App und eine einmalige Sicherung dafür. |
| [Erstellen einer geplanten Sicherung für eine App](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App und eine geplante Sicherung dafür. |
| [Wiederherstellen einer App aus einer Sicherung](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Stellt eine App Service-App aus einer Sicherung wieder her. |
|**Überwachen der App**||
| [Überwachen einer Web-App mit Webserverprotokollen](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Erstellt eine App Service-App, aktiviert die Protokollierung für die App und lädt die Protokolle auf Ihren lokalen Computer herunter. |
| | |
