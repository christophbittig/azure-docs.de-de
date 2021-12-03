---
title: Serververtrauensgruppe
titleSuffix: Azure SQL Managed Instance
description: Erfahren Sie, wie Sie die Vertrauensstellung zwischen Instanzen mithilfe einer Serververtrauensgruppe in Azure SQL Managed Instance verwalten.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: service-overview
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 857af87fac233ff15bad924746c87e0856f90b1f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131431045"
---
# <a name="set-up-trust-between-instances-with-server-trust-group-azure-sql-managed-instance"></a>Einrichten der Vertrauensstellung zwischen Instanzen mithilfe einer Serververtrauensgruppe (Azure SQL Managed Instance)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Eine Serververtrauensgruppe (auch SQL-Vertrauensgruppe genannt) ist ein Konzept, das für die Verwaltung der Vertrauensstellung zwischen Instanzen in Azure SQL Managed Instance verwendet wird. Durch das Erstellen einer Gruppe wird eine zertifikatbasierte Vertrauensstellung zwischen den Mitgliedern eingerichtet. Diese Vertrauensstellung kann für verschiedene instanzübergreifende Szenarien verwendet werden. Durch das Entfernen von Servern aus der Gruppe oder das Löschen der Gruppe wird die Vertrauensstellung zwischen den Servern aufgehoben. Benutzer, die eine Serververtrauensgruppe erstellen oder löschen möchten, müssen über Schreibberechtigungen für die verwaltete Instanz verfügen.
Die [Serververtrauensgruppe](/azure/templates/microsoft.sql/allversions) ist ein Azure Resource Manager-Objekt, das im Azure-Portal als **SQL-Vertrauensgruppe** bezeichnet wird.


## <a name="set-up-group"></a>Einrichten der Gruppe

Die Serververtrauensgruppe kann über [Azure PowerShell](/powershell/module/az.sql/new-azsqlservertrustgroup) oder [Azure CLI](/cli/azure/sql/stg) eingerichtet werden. 

Führen Sie die folgenden Schritte aus, um eine Serververtrauensgruppe im Azure-Portal zu erstellen: 

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/).

2. Navigieren Sie zu der Instanz von Azure SQL Managed Instance, die Sie einer Serververtrauensgruppe hinzufügen möchten.

3. Wählen Sie in den Einstellungen für die **Sicherheit** die Registerkarte **SQL-Vertrauensgruppen** aus.

   :::image type="content" source="./media/server-trust-group-overview/sql-trust-groups.png" alt-text="SQL-Vertrauensgruppen":::

4. Wählen Sie auf der Konfigurationsseite der **SQL-Vertrauensgruppen** das Symbol **Neue Gruppe** aus.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group-button.png" alt-text="Neue Gruppe":::

5. Legen Sie auf dem Blatt zum Erstellen der **SQL-Vertrauensgruppe** den **Gruppennamen** fest. Dieser muss im Abonnement, in der Ressourcengruppe und in der Region der Gruppe eindeutig sein. Der **Vertrauensbereich** legt den Typ des instanzübergreifenden Szenarios fest, das mit der Serververtrauensgruppe ermöglicht wird. Der Vertrauensbereich ist festgesetzt. Alle verfügbaren Funktionen sind vorab ausgewählt und können nicht geändert werden. Wählen Sie das **Abonnement** und die **Ressourcengruppe** aus, um die verwalteten Instanzen auszuwählen, die Mitglieder der Gruppe sein werden.

   :::image type="content" source="./media/server-trust-group-overview/new-sql-trust-group.png" alt-text="Blatt zum Erstellen einer SQL-Vertrauensgruppe":::

6. Nachdem alle erforderlichen Felder ausgefüllt wurden, klicken Sie auf **Speichern**.

## <a name="edit-group"></a>Gruppe bearbeiten 

Führen Sie die folgenden Schritte aus, um eine Serververtrauensgruppe zu bearbeiten: 

1. Navigieren Sie zum Azure-Portal.
1. Navigieren Sie zu einer verwalteten Instanz, die der Vertrauensgruppe angehört.
1. Wählen Sie in den Einstellungen für die **Sicherheit** die Registerkarte **SQL-Vertrauensgruppen** aus.
1. Wählen Sie die Vertrauensgruppe aus, die Sie bearbeiten möchten.
1. Klicken Sie auf **Gruppe konfigurieren**.

   :::image type="content" source="./media/server-trust-group-overview/configure-sql-trust-group.png" alt-text="Konfigurieren der SQL-Vertrauensgruppe":::

1. Fügen Sie verwaltete Instanzen zu der Gruppe hinzu oder entfernen Sie diese.
1. Klicken Sie auf **Speichern**, um die Auswahl zu bestätigen, oder auf **Abbrechen**, um Änderungen zu verwerfen.

## <a name="delete-group"></a>Gruppe löschen

Führen Sie die folgenden Schritte aus, um eine Serververtrauensgruppe zu löschen: 

1. Öffnen Sie das Azure-Portal.
1. Navigieren Sie zu einer verwalteten Instanz, die der SQL-Vertrauensgruppe angehört.
1. Wählen Sie in den Einstellungen für die **Sicherheit** die Registerkarte **SQL-Vertrauensgruppen** aus.
1. Wählen Sie die Vertrauensgruppe aus, die Sie löschen möchten.

   :::image type="content" source="./media/server-trust-group-overview/select-delete-sql-trust-group.png" alt-text="Auswählen der SQL-Vertrauensgruppe":::

1. Wählen Sie nun **Gruppe löschen** aus.

   :::image type="content" source="./media/server-trust-group-overview/delete-sql-trust-group.png" alt-text="Löschen der SQL-Vertrauensgruppe"::: 

1. Geben Sie den Namen der SQL-Vertrauensgruppe ein, um den Löschvorgang zu bestätigen, und klicken Sie dann auf **Löschen**.

   :::image type="content" source="./media/server-trust-group-overview/confirm-delete-sql-trust-group-2.png" alt-text="Bestätigen des Löschvorgangs der SQL-Vertrauensgruppe":::

> [!NOTE]
> Durch das Löschen der SQL-Vertrauensgruppe wird die Vertrauensstellung zwischen den beiden verwalteten Instanzen möglicherweise nicht sofort aufgehoben. Sie können das Aufheben von Vertrauensstellungen erzwingen, indem Sie ein [Failover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) der verwalteten Instanzen auslösen. Aktuelle Informationen hierzu finden Sie unter [Bekannte Probleme](../managed-instance/doc-changes-updates-known-issues.md).

## <a name="limitations"></a>Einschränkungen

Folgende Einschränkungen gelten für Serververtrauensgruppen: 

 * Die Gruppe darf nur Instanzen von Azure SQL Managed Instance enthalten.
 * Der Vertrauensbereich kann nicht geändert werden, wenn eine Gruppe erstellt oder bearbeitet wird.
 * Der Name der Serververtrauensgruppe muss für das Abonnement, die Ressourcengruppe und die Region eindeutig sein.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu verteilten Transaktionen in Azure SQL Managed Instance finden Sie unter [Verteilte Transaktionen](../database/elastic-transactions-overview.md).
* Informationen zu Releaseupdates und bekannten Problemen finden Sie unter [Neuigkeiten](doc-changes-updates-release-notes-whats-new.md).
* Wenn Sie weitere Features benötigen, können Sie diese im [Forum für Managed Instance](https://feedback.azure.com/d365community/forum/a99f7006-3425-ec11-b6e6-000d3a4f0f84) veröffentlichen.
