---
title: Verwenden von dbForge Studio for MySQL zum Migrieren einer MySQL-Datenbank zu Azure Database for MySQL
description: Im Artikel wird gezeigt, wie Sie mithilfe von dbForge Studio for MySQL zu Azure Database for MySQL migrieren.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: d7ed106fb1922315ba181e2ca2c27dcc7d2b9cd3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639700"
---
# <a name="migrate-data-to-azure-database-for-mysql-with-dbforge-studio-for-mysql"></a>Migrieren von Daten zu Azure Database for MySQL mit dbForge Studio for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Möchten Sie Ihre MySQL-Datenbanken zu Azure Database for MySQL verschieben? Dann sollten Sie die Verwendung der Migrationstools in dbForge Studio for MySQL in Betracht ziehen. Damit kann eine Datenbankübertragung konfiguriert, gespeichert, bearbeitet, automatisiert und geplant werden.

Zum Abschließen der Beispiele in diesem Artikel müssen Sie [dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/) herunterladen und installieren.

## <a name="connect-to-azure-database-for-mysql"></a>Herstellen einer Verbindung mit Azure-Datenbank für MySQL

1. Wählen Sie in dbForge Studio for MySQL im Menü **Datenbank** die Option **Neue Verbindung** aus.

1. Geben Sie einen Hostnamen und Anmeldeinformationen an.

1. Wählen Sie **Verbindung testen** aus, um die Konfiguration zu überprüfen.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png" alt-text="Screenshot eines erfolgreichen Verbindungstests für Azure Database for MySQL" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/azure-connection-1.png":::

## <a name="migrate-with-the-backup-and-restore-functionality"></a>Migrieren mit der Funktionalität „Sichern und Wiederherstellen“

Sie können aus vielen Optionen wählen, wenn Sie Datenbanken mithilfe von dbForge Studio für MySQL zu Azure migrieren. Wenn Sie die gesamte Datenbank verschieben müssen, empfiehlt es sich, dazu die Funktionalität **Sichern und Wiederherstellen** zu verwenden.

In diesem Beispiel migrieren wir die Datenbank *sakila* von MySQL-Server zu Azure Database for MySQL. Wenn Sie dazu die Funktionalität **Sichern und Wiederherstellen** verwenden, können Sie eine Sicherung der MySQL-Datenbank erstellen und sie dann in Azure Database for MySQL wiederherstellen.

### <a name="back-up-the-database"></a>Sichern der Datenbank

1. Wählen Sie in dbForge Studio für MySQL im Menü **Sicherung und Wiederherstellung** die Option **Datenbank sichern** aus. Der **Assistent für das Sichern von Datenbanken** wird angezeigt.

1. Wählen Sie auf der Registerkarte **Backup content** (Sicherungsinhalt) des **Assistenten zum Sichern von Datenbanken** die Datenbankobjekte aus, die Sie sichern möchten.

1. Konfigurieren Sie auf der Registerkarte **Optionen** den Sicherungsprozess Ihren Anforderungen entsprechend.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png" alt-text="Screenshot des Optionsbereichs des Sicherungs-Assistenten" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/back-up-wizard-options.png":::

1. Wählen Sie **Weiter** aus, und geben Sie Fehlerverarbeitungsverhalten und Protokollierungsoptionen an.

1. Wählen Sie **Sichern** aus.

### <a name="restore-the-database"></a>Wiederherstellen der Datenbank

1.  Stellen Sie in dbForge Studio for MySQL eine Verbindung mit Azure Database for MySQL her. [Anleitungen dazu finden Sie unter: ](#connect-to-azure-database-for-mysql).

1. Wählen Sie im Menü **Sicherung und Wiederherstellung** die Option **Datenbank wiederherstellen** aus. Der **Assistent zum Wiederherstellen von Datenbanken** wird angezeigt.

1. Wählen Sie im **Assistenten zum Wiederherstellen von Datenbanken** eine Datei mit einer Datenbanksicherung aus.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png" alt-text="Screenshot des Schritts „Wiederherstellen“ des Assistenten zum Wiederherstellen von Datenbanken" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/restore-step-1.png":::

1. Wählen Sie **Wiederherstellen** aus.

1. Überprüfen Sie das Ergebnis.

## <a name="migrate-with-the-copy-databases-functionality"></a>Migrieren mit der Funktionalität „Copy Databases“ (Datenbanken kopieren)

Die Funktionalität **Copy Databases** in dbForge Studio for MySQL ähnelt **Sichern und Wiederherstellen** – mit der Ausnahme, dass zum Migrieren einer Datenbank keine zwei Schritte erforderlich sind. Außerdem können Sie zwei oder mehr Datenbanken gleichzeitig übertragen.

>[!NOTE]
> Die Funktionalität **Copy Databases** ist nur in der Enterprise Edition von dbForge Studio for MySQL verfügbar.

In diesem Beispiel migrieren wir die Datenbank *world_x* von MySQL-Server zu Azure Database for MySQL.

So migrieren Sie eine Datenbank mithilfe der Funktionalität „Copy Databases“ (Datenbanken kopieren):

1. Wählen Sie in dbForge Studio for MySQL im Menü **Datenbank** die Option **Copy Databases** (Datenbanken kopieren) aus. 

1. Geben Sie auf der Registerkarte **Copy Databases** (Datenbanken kopieren) die Quell- und Zielverbindung an. Wählen Sie außerdem die Datenbanken aus, die migriert werden sollen. 

   Wir geben die Azure MySQL-Verbindung ein und wählen die Datenbank *world_x* aus. Wählen Sie den grünen Pfeil aus, um den Vorgang starten.

1. Überprüfen Sie das Ergebnis.

Sie werden feststellen, dass die Datenbank *world_x* in Azure MySQL erfolgreich angezeigt wurde.

:::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png" alt-text="Screenshot mit den Ergebnissen der Funktion „Copy Databases“ (Datenbanken kopieren)" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/copy-databases-result.png":::

## <a name="migrate-a-database-with-schema-and-data-comparison"></a>Migrieren einer Datenbank mit Schema- und Datenvergleich

Sie können aus vielen Optionen wählen, wenn Sie Datenbanken, Schemas und/oder Daten mithilfe von dbForge Studio für MySQL zu Azure migrieren. Wenn Sie selektive Tabellen aus einer MySQL-Datenbank zu Azure verschieben müssen, sollten Sie dazu die Funktionen **Schemavergleich** und **Datenvergleich** verwenden.

In diesem Beispiel migrieren wir die Datenbank *world_x* von MySQL Server zu Azure Database for MySQL. 

Wenn Sie dazu die Funktionalität **Sichern und Wiederherstellen** verwenden, können Sie eine Sicherung der MySQL-Datenbank erstellen und sie dann in Azure Database for MySQL wiederherstellen.

Die Logik hinter diesem Ansatz besteht darin, eine leere Datenbank in Azure Database for MySQL zu erstellen und sie mit der MySQL-Quelldatenbank zu synchronisieren. Wir verwenden zuerst das Tool **Schemavergleich** und danach die Funktion **Datenvergleich**. Mit diesen Schritten wird sichergestellt, dass die MySQL-Schemas und -Daten korrekt zu Azure verschoben werden.

Zum Abschließen dieser Übung müssen Sie zuerst [eine Verbindung mit Azure Database for MySQL herstellen](#connect-to-azure-database-for-mysql) und eine leere Datenbank erstellen.

### <a name="schema-synchronization"></a>Schemasynchronisierung

1. Wählen Sie im Menü **Vergleich** die Option **Neuer Schemavergleich** aus. Der **Assistent „Neuer Schemavergleich“** wird angezeigt.

1. Wählen Sie Ihre Quelle und Ihr Ziel aus, und geben Sie dann die Schemavergleichsoptionen an. Wählen Sie **Vergleichen** aus.

1. Wählen Sie im angezeigten Raster der Vergleichsergebnisse Objekte aus, die synchronisiert werden sollen. Wählen Sie die grüne Pfeilschaltfläche aus, um den **Assistenten für die Schemasynchronisierung** zu öffnen.

1. Führen Sie die Schritte des Assistenten zum Konfigurieren der Synchronisierung aus. Wählen Sie **Synchronisieren** aus, um die Änderungen bereitzustellen.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png" alt-text="Screenshot: Schemasynchronisierungs-Assistent" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/schema-sync-wizard.png":::

### <a name="data-comparison"></a>Datenvergleich

1. Wählen Sie im Menü **Vergleich** die Option **Neuer Datenvergleich** aus. Der **Assistent „Neuer Datenvergleich“** wird angezeigt.

1. Wählen Sie Ihre Quelle und Ihr Ziel aus, und geben Sie die Datenvergleichsoptionen an. Ändern Sie ggf. Zuordnungen, und wählen Sie dann **Vergleichen** aus.

1. Wählen Sie im angezeigten Raster der Vergleichsergebnisse Objekte aus, die synchronisiert werden sollen. Wählen Sie die grüne Pfeilschaltfläche aus, um den **Assistenten für die Datensynchronisierung** zu öffnen.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png" alt-text="Screenshot mit den Ergebnissen des Datenvergleichs" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-comp-result.png":::

1. Führen Sie die Schritte des Assistenten zum Konfigurieren der Synchronisierung durch. Wählen Sie **Synchronisieren** aus, um die Änderungen bereitzustellen.

1. Überprüfen Sie das Ergebnis.

    :::image type="content" source="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png" alt-text="Screenshot mit den Ergebnissen des Assistenten für die Datensynchronisierung" lightbox="media/concepts-migrate-dbforge-studio-for-mysql/data-sync-result.png":::

## <a name="next-steps"></a>Nächste Schritte
- [Übersicht über MySQL](overview.md)
