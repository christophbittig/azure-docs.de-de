---
title: Konfigurieren von Überwachungsprotokollen – Azure-Portal – Azure Database for MySQL – Flexible Server
description: In diesem Artikel wird beschrieben, wie Sie die Überwachungsprotokolle in Azure Database for MySQL Flexible Server im Azure-Portal konfigurieren und aus dem Portal auf die Protokolle zugreifen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/29/2020
ms.openlocfilehash: 68ee7528eb7b8d91d4346b5cf7fa2f92841255b7
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639750"
---
# <a name="configure-and-access-audit-logs-for-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>Konfigurieren von Überwachungsprotokolle für Azure Database for MySQL – Flexible Server und Zugreifen auf diese Protokolle im Azure-Portal

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Sie können im Azure-Portal die Azure Database for MySQL Flexible Server-[Überwachungsprotokolle](concepts-audit-logs.md) und -Diagnoseeinstellungen konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Die Schritte in diesem Artikel erfordern [Flexible Server](quickstart-create-server-portal.md).

## <a name="configure-audit-logging"></a>Konfigurieren der Überwachungsprotokollierung

>[!IMPORTANT]
> Es wird empfohlen, nur die Ereignistypen und Benutzer zu protokollieren, die für Ihre Überwachungszwecke erforderlich sind, um sicherzustellen, dass die Leistung Ihres Servers nicht stark beeinträchtigt wird.

Aktivieren und konfigurieren Sie die Überwachungsprotokollierung.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie Ihre Flexible Server-Instanz aus.

1. Wählen Sie auf der Randleiste im Abschnitt **Einstellungen** den Eintrag **Serverparameter** aus.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/server-parameters.png" alt-text="Serverparameter":::

1. Ändern Sie den Parameter **audit_log_enabled** in ON.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-enabled.png" alt-text="Aktivieren von Überwachungsprotokollen":::

1. Wählen Sie die zu protokollierenden [Ereignistypen](concepts-audit-logs.md#configure-audit-logging) aus, indem Sie den Parameter **audit_log_events** aktualisieren.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-events.png" alt-text="Überwachungsprotokollereignisse":::

1. Fügen Sie durch Aktualisieren der Parameter **audit_log_exclude_users** und **audit_log_include_users** alle MySQL-Benutzer hinzu, die in die Protokollierung einbezogen bzw. von ihr ausgeschlossen werden sollen. Geben Sie Benutzer an, indem Sie ihre MySQL-Benutzernamen bereitstellen.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/audit-log-exclude-users.png" alt-text="Vom Überwachungsprotokoll auszuschließende Benutzer":::

1. Nachdem Sie die Parameter geändert haben, klicken Sie auf **Speichern**. Sie können Ihre Änderungen auch **verwerfen**.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-parameters.png" alt-text="Speichern":::

## <a name="set-up-diagnostics"></a>Einrichten der Diagnose

Überwachungsprotokolle sind in die Diagnoseeinstellungen von Azure Monitor integriert, sodass Sie Ihre Protokolle an Azure Monitor-Protokolle, Event Hubs oder Azure Storage weiterleiten können.

1. Wählen Sie auf der Randleiste im Abschnitt **Überwachung** die Option **Diagnoseeinstellungen** aus.

1. Klicken Sie auf „+ Diagnoseeinstellung hinzufügen“  :::image type="content" source="./media/how-to-configure-audit-logs-portal/add-diagnostic-setting.png" alt-text="Diagnoseeinstellung hinzufügen":::.

1. Geben Sie einen Namen für die Diagnoseeinstellung ein.

1. Geben Sie an, an welche Ziele die Überwachungsprotokolle gesendet werden sollen (Speicherkonto, Event Hub und/oder Log Analytics-Arbeitsbereich).

1. Wählen Sie als Protokolltyp **MySqlAuditLogs** aus.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/configure-diagnostic-setting.png" alt-text="Konfigurieren der Diagnoseeinstellung":::

1. Nachdem Sie die Datensenken für die Überwachungsprotokolle konfiguriert haben, können Sie auf **Speichern** klicken.
    :::image type="content" source="./media/how-to-configure-audit-logs-portal/save-diagnostic-setting.png" alt-text="Speichern der Diagnoseeinstellung":::

1. Greifen Sie auf die Überwachungsprotokolle zu, indem Sie sie in den von Ihnen konfigurierten Datensenken einsehen. Es kann bis zu 10 Minuten dauern, bis die Protokolle angezeigt werden.

Wenn Sie Ihre Überwachungsprotokolle über eine Pipeline an Azure Monitor-Protokolle (Log Analytics) weitergeleitet haben, beziehen Sie sich auf einige [Beispielabfragen](concepts-audit-logs.md#analyze-logs-in-azure-monitor-logs), die Sie zur Analyse verwenden können.  

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Überwachungsprotokolle](concepts-audit-logs.md).
- Erfahren Sie mehr über [Protokolle für langsame Abfragen](concepts-slow-query-logs.md).
<!-- - Learn how to configure audit logs in the [Azure CLI](howto-configure-audit-logs-cli.md)-->