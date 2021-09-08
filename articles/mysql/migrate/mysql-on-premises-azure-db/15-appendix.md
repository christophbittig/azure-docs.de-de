---
title: 'Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Beispielanwendungen'
description: Laden Sie die zusätzliche Dokumentation herunter, die für dieses Migrationshandbuch erstellt wurde, und erfahren Sie Näheres zur Konfiguration.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 27f4ded8e1378ccceeb11409e9c393fbc46c92aa
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084550"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-sample-applications"></a>Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL: Beispielanwendungen

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>Übersicht

Dieser Artikel enthält Informationen zum Bereitstellen einer Beispielanwendung mit einem End-to-End-MySQL-Migrationsleitfaden und zum Überprüfen der verfügbaren Serverparameter.

## <a name="environment-setup"></a>Einrichten der Umgebung

[Laden Sie weitere Dokumentation herunter](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20A.pdf), die für diesen Migrationsleitfaden erstellt wurde, und erfahren Sie, wie Sie eine Umgebung konfigurieren, um die Migrationsschritte des Leitfadens für das [Konferenzdemo](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/sample-app)-Anwendungsbeispiel auszuführen.

## <a name="azure-resource-manager-arm-templates"></a>Azure Resource Manager-Vorlagen

### <a name="secure"></a>Sicher

Die ARM-Vorlage stellt alle Ressourcen mit privaten Endpunkten bereit. Die ARM-Vorlage entfernt effektiv jeglichen Zugriff auf die PaaS-Dienste über das Internet.

[Sichere ARM-Vorlage](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>Nicht sicher

Die ARM-Vorlage stellt Ressourcen mithilfe der Standardbereitstellung bereit, bei der alle Ressourcen über das Internet verfügbar sind.

[Nicht sichere ARM-Vorlage](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)

## <a name="default-server-parameters-mysql-55-and-azure-database-for-mysql"></a>Standardserverparameter in MySQL 5.5 und Azure Database for MySQL

Die [vollständige Liste der Standardserverparameter von MySQL 5.5 und Azure Database for MySQL](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20C.pdf) finden Sie im GitHub-Repository.