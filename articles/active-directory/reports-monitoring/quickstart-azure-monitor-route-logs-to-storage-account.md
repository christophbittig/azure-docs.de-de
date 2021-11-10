---
title: 'Tutorial: Archivieren von Verzeichnisprotokollen in einem Speicherkonto | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie die Azure-Diagnose einrichten, um Azure Active Directory-Protokolle per Pushvorgang in ein Speicherkonto zu übertragen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/05/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f170cd4f3530ea7391d2fbd039263c29540321f8
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995462"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Tutorial: Archivieren von Azure AD-Protokollen in einem Azure-Speicherkonto

In diesem Tutorial erfahren Sie, wie Sie Azure Monitor-Diagnoseeinstellungen einrichten, um Azure AD-Protokolle (Azure Active Directory) an ein Azure-Speicherkonto weiterzuleiten.

## <a name="prerequisites"></a>Voraussetzungen 

Sie benötigen Folgendes, um dieses Feature verwenden zu können:

* Ein Azure-Abonnement mit einem Azure-Speicherkonto. Falls Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für eine kostenlose Testversion registrieren](https://azure.microsoft.com/free/).
* Einen Azure AD-Mandanten.
* Einen Benutzer, der als *globaler Administrator* oder *Sicherheitsadministrator* für den Azure AD-Mandanten fungiert.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archivieren von Protokollen in einem Azure-Speicherkonto

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

2. Wählen Sie **Azure Active Directory** > **Überwachung** > **Überwachungsprotokolle** aus. 

3. Wählen Sie **Einstellungen für das Exportieren von Daten** aus. 

4. Führen Sie im Bereich **Diagnoseeinstellungen** einen der folgenden Schritte aus:
    1. Um die vorhandene Einstellung zu ändern, wählen Sie **Einstellung bearbeiten** neben der Diagnoseeinstellung, die Sie aktualisieren möchten.
    1. Klicken Sie zum Hinzufügen neuer Einstellungen auf **Diagnoseeinstellung hinzufügen**.  

    Sie können maximal drei Einstellungen verwenden.

     ![Exporteinstellungen](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Wenn Sie eine neue Einstellung erstellen, geben Sie im Bereich **Diagnoseeinstellung** einen Namen für die Einstellung ein, der ihren Zweck näher beschreibt (z. B. *An Azure-Speicherkonto senden*). Der Name einer vorhandenen Einstellung kann später nicht geändert werden.

6. Aktivieren Sie das Kontrollkästchen **In einem Speicherkonto archivieren** unter **Zieldetails**. 

7. Wählen Sie im Dropdownmenü **Abonnement** das Azure-Abonnement und im Dropdownmenü **Speicherkonto** das Speicherkonto aus, an das Sie die Protokolle weiterleiten möchten.

8. Wählen Sie unter **Kategoriedetails** alle relevanten Kategorien aus:

    Führen Sie einen oder beide der folgenden Schritte aus:
    1. Aktivieren Sie das Kontrollkästchen **AuditLogs**, um Überwachungsprotokolle an das Speicherkonto zu senden.
    
    1. Aktivieren Sie das Kontrollkästchen **SignInLogs**, um Anmeldeprotokolle an das Speicherkonto zu senden.

    ![Diagnoseeinstellungen](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

9. Nachdem die Kategorien ausgewählt wurden, geben Sie im Feld **Aufbewahrung in Tagen** die Anzahl der Tage ein, die Sie Ihre Protokolldaten aufbewahren müssen. Standardmäßig ist dieser Wert auf *0* festgelegt, d.h. die Protokolle werden im Speicherkonto unbegrenzt beibehalten. Wenn Sie einen anderen Wert festlegen, werden Ereignisse, die älter als die ausgewählte Anzahl von Tagen sind, automatisch bereinigt.
 
10. Klicken Sie auf **Speichern**, um die Einstellung zu speichern.

11. Schließen Sie das Fenster, um zum Bereich „Diagnoseeinstellungen“ zurückzukehren.

## <a name="next-steps"></a>Nächste Schritte

* [Interpret audit logs schema in Azure Monitor](./overview-reports.md) (Interpretieren des Überwachungsprotokollschemas in Azure Monitor)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretieren des Anmeldeprotokollschemas in Azure Monitor)
* [Häufig gestellte Fragen und bekannte Probleme](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
