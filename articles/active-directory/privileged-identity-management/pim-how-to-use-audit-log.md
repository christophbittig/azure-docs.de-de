---
title: Anzeigen des Überwachungsprotokollberichts für Azure AD-Rollen in Azure AD PIM | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Überwachungsprotokollverlauf für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) anzeigen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 10/07/2021
ms.author: curtand
ms.reviewer: shaunliu
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 408ae958cb82557d6dabd92cd92e3967b9a8b40e
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129667970"
---
# <a name="view-audit-history-for-azure-ad-roles-in-privileged-identity-management"></a>Anzeigen des Überwachungsverlaufs für Azure AD-Rollen in Privileged Identity Management

Im Überwachungsverlauf von Privileged Identity Management (PIM) können Sie alle Rollenzuweisungen und -aktivierungen für alle privilegierten Rollen in den letzten 30 Tagen anzeigen. Wenn Sie Überwachungsdaten länger als den Standardaufbewahrungszeitraum beibehalten möchten, können Sie Azure Monitor verwenden, um sie an ein Azure-Speicherkonto weiterzuleiten. Weitere Informationen finden Sie unter [Archivieren von Azure AD-Protokollen in einem Azure Storage-Konto](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md). Wenn Sie den vollständigen Überwachungsverlauf von Aktivitäten in Ihrer Azure Active Directory-Organisation (Azure AD-Organisation) einschließlich der Aktivitäten von Administratoren, Endbenutzern und Synchronisierungen anzeigen möchten, können Sie die [Azure Active Directory-Sicherheits- und Aktivitätsberichte](../reports-monitoring/overview-reports.md) verwenden.

Führen Sie die folgenden Schritte aus, um den Überwachungsverlauf für Azure AD-Rollen anzuzeigen.

## <a name="view-resource-audit-history"></a>Anzeigen des Ressourcenüberwachungsverlaufs

Mit der Ressourcenüberwachung erhalten Sie eine Übersicht über alle Aktivitäten, die Ihren Azure AD Rollen zugeordnet sind.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie **Ressourcenüberwachung** aus.

1. Filtern Sie den Verlauf nach einem vordefinierten Datum oder nach einem benutzerdefinierten Bereich.

    ![Azure AD-Rollenüberwachungsliste mit Filtern](media/azure-pim-resource-rbac/rbac-resource-audit.png)

## <a name="view-my-audit"></a>Anzeigen der eigenen Überwachung

In der eigenen Überwachung können Sie Ihre persönliche Rollenaktivität anzeigen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Wählen Sie **Azure AD-Rollen** aus.

1. Wählen Sie die Ressource aus, für die Sie den Überwachungsverlauf anzeigen möchten.

1. Wählen Sie **Meine Überwachung** aus.

1. Filtern Sie den Verlauf nach einem vordefinierten Datum oder nach einem benutzerdefinierten Bereich.

    ![Überwachungsliste für den aktuellen Benutzer](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen von Aktivitäten und des Überwachungsverlaufs für Azure-Ressourcenrollen in Privileged Identity Management](azure-pim-resource-rbac.md)
