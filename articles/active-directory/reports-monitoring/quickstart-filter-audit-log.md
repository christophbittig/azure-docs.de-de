---
title: Filtern des Azure AD-Überwachungsprotokolls
description: In diesem Schnellstar erfahren Sie, wie Sie Einträge in Ihrem Azure AD-Überwachungsprotokoll filtern können.
services: active-directory
ms.service: active-directory
ms.subservice: report-monitor
ms.topic: quickstart
ms.date: 06/11/2021
ms.author: markvi
author: MarkusVi
manager: karenhoran
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51bc001c5f6984e855adb11b1bdb7c938d2d5783
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995500"
---
# <a name="quickstart-filter-your-azure-ad-audit-log"></a>Schnellstart: Filtern des Azure AD-Überwachungsprotokolls 

Durch die Informationen im Azure AD-Überwachungsprotokoll erhalten Sie Zugriff auf Datensätze mit Systemaktivitäten zu Compliancezwecken. In diesem Schnellstart erfahren Sie, wie Sie ein neu erstelltes Benutzerkonto in Ihrem Überwachungsprotokoll ermitteln können.


## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung des Szenarios im Rahmen dieses Schnellstarts benötigen Sie Folgendes:

- **Zugriff auf einen Azure AD-Mandanten**: Wenn Sie keinen Zugriff auf einen Azure AD-Mandanten besitzen, können Sie hier ein [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- **Ein Testkonto unter dem Namen Isabella Simonsen**: Wenn Sie nicht wissen, wie Sie ein Testkonto erstellen, lesen Sie [Hinzufügen von cloudbasierten Benutzern](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="find-the-new-user-account"></a>Ermitteln des neuen Benutzerkontos

Dieser Abschnitt enthält die Schritte, die Sie zum Filtern Ihres Überwachungsprotokolls ausführen müssen.


**So suchen Sie nach dem neuen Benutzer:**

1. Navigieren Sie zum [Überwachungsprotokoll](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Audit).

2. So listen Sie nur Datensätze für Isabella Simonsen auf:

    a. Klicken Sie auf der Symbolleiste auf **Filter hinzufügen**.
    
    ![Hinzufügen eines Benutzerfilters](./media/quickstart-analyze-sign-in/add-filters.png)   

    b. Wählen Sie in der Liste **Feld auswählen** die Option **Ziel** aus, und klicken Sie dann auf **Übernehmen**.

    c. Geben Sie im Textfeld **Ziel** den **Benutzerprinzipalnamen** **Isabella Simonsen** ein, und klicken Sie dann auf **Übernehmen**.

3. Klicken Sie auf das gefilterte Element.

    ![Gefilterte Elemente](./media/quickstart-filter-audit-log/audit-log-list.png)  

4.  Überprüfen Sie die **Details des Überwachungsprotokolls**.
 
    ![Überwachungsprotokolldetails](./media/quickstart-filter-audit-log/audit-log-details.png)  
 
  

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Testbenutzer nicht mehr benötigen, löschen Sie ihn. Wenn Sie nicht wissen, wie ein Azure AD-Benutzer gelöscht wird, lesen Sie [Löschen von Benutzern aus Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Was sind Azure Active Directory-Berichte?](overview-reports.md)
