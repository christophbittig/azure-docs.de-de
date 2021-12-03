---
title: Zuweisen von Vertraulichkeitsbezeichnungen zu Gruppen – Azure AD | Microsoft-Dokumentation
description: Erfahren Sie, wie Gruppen Vertraulichkeitsbezeichnungen zugewiesen werden. Lesen Sie die Informationen zur Problembehandlung, und sehen Sie sich weitere verfügbare Ressourcen an.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/28/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b116a4b6a100f8b93d453b95b7c181cde97b572
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401400"
---
# <a name="assign-sensitivity-labels-to-microsoft-365-groups-in-azure-active-directory"></a>Zuweisen von Vertraulichkeitsbezeichnungen zu Microsoft 365-Gruppen in Azure Active Directory

Azure Active Directory (Azure AD) unterstützt das Anwenden von Vertraulichkeitsbezeichnungen, die vom [Microsoft 365 Compliance Center](https://sip.protection.office.com/homepage) für Microsoft 365-Gruppen veröffentlicht werden. Vertraulichkeitsbezeichnungen gelten für Gruppen über Dienste wie Outlook, Microsoft Teams und SharePoint hinweg. Weitere Informationen zur Unterstützung von Microsoft 365-Apps finden Sie unter [Microsoft 365-Unterstützung für Vertraulichkeitsbezeichnungen](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#support-for-the-sensitivity-labels).

> [!IMPORTANT]
> Um dieses Feature zu konfigurieren, muss mindestens eine aktive Azure Active Directory Premium P1-Lizenz in Ihrer Azure AD-Organisation vorhanden sein.

## <a name="enable-sensitivity-label-support-in-powershell"></a>Aktivieren der Unterstützung von Vertraulichkeitsbezeichnungen in PowerShell

Damit veröffentlichte Bezeichnungen auf Gruppen angewendet werden können, müssen Sie zunächst das Feature aktivieren. Diese Schritte aktivieren das Feature in Azure AD.

1. Öffnen Sie ein Windows PowerShell-Fenster auf Ihrem Computer. Sie können es ohne erhöhte Rechte öffnen.
1. Führen Sie die folgenden Befehle aus, um die Ausführung der Cmdlets vorzubereiten.

    ```powershell
    Install-Module AzureADPreview
    Import-Module AzureADPreview
    Connect-AzureAD
    ```

    Geben Sie auf der Seite **Bei Ihrem Konto anmelden** Ihr Administratorkonto und das zugehörige Kennwort ein, um eine Verbindung mit dem Dienst herzustellen, und wählen Sie **Anmelden** aus.
1. Rufen Sie die aktuellen Gruppeneinstellungen für die Azure AD-Organisation ab.

    ```powershell
    $grpUnifiedSetting = (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ)
    $template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
    $setting = $template.CreateDirectorySetting()
    ```

    > [!NOTE]
    > Wenn keine Gruppeneinstellungen für diese Azure AD-Organisation erstellt wurden, erhalten Sie eine Fehlermeldung mit dem Wortlaut " Das Argument kann nicht an den Parameter 'Id' gebunden werden, da dieser null ist". In diesem Fall müssen Sie zuerst die Einstellungen erstellen. Um Gruppeneinstellungen für diese Azure AD-Organisation zu erstellen, führen Sie die Schritte in [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../enterprise-users/groups-settings-cmdlets.md) aus.

1. Zeigen Sie als nächstes die aktuellen Gruppeneinstellungen an.

    ```powershell
    $Setting.Values
    ```

1. Aktivieren Sie das Feature:

    ```powershell
    $Setting["EnableMIPLabels"] = "True"
    ```
 
1. Überprüfen Sie den neuen angewendeten Wert:

    ```powershell
    $Setting.Values
    ```
    
1. Speichern Sie die Änderungen, und übernehmen Sie die Einstellungen:

    ```powershell
    Set-AzureADDirectorySetting -Id $grpUnifiedSetting.Id -DirectorySetting $setting
    ```

Außerdem müssen Sie Ihre Vertraulichkeitsbezeichnungen mit Azure AD synchronisieren. Entsprechende Anweisungen finden Sie unter [Aktivieren von Vertraulichkeitsbezeichnungen für Container und Synchronisieren von Bezeichnungen](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#how-to-enable-sensitivity-labels-for-containers-and-synchronize-labels).

## <a name="assign-a-label-to-a-new-group-in-azure-portal"></a>Zuweisen einer Bezeichnung zu einer neuen Gruppe im Azure-Portal

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) an.
1. Wählen Sie **Gruppen** und anschließend **Neue Gruppe** aus.
1. Wählen Sie auf der Seite **Neue Gruppe** die Option **Office 365** aus, füllen Sie dann die erforderlichen Informationen für die neue Gruppe aus, und wählen Sie anschließend eine Vertraulichkeitsbezeichnung aus der Liste aus.

   ![Zuweisen einer Vertraulichkeitsbezeichnung auf der Seite „Neue Gruppen“](./media/groups-assign-sensitivity-labels/new-group-page.png)

1. Speichern Sie die Änderungen, und wählen Sie **Erstellen** aus.

Ihre Gruppe wird erstellt, und die mit der ausgewählten Bezeichnung verbundenen Standort- und Gruppeneinstellungen werden automatisch durchgesetzt.

## <a name="assign-a-label-to-an-existing-group-in-azure-portal"></a>Zuweisen einer Bezeichnung zu einer vorhandenen Gruppe im Azure-Portal

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem Gruppenadministratorkonto oder als Gruppenbesitzer an.
1. Wählen Sie **Gruppen** aus.
1. Wählen Sie auf der Seite **Alle Gruppen** die Gruppe aus, die Sie bezeichnen möchten.
1. Wählen Sie auf der Seite der ausgewählten Gruppe **Eigenschaften** und anschließend eine Vertraulichkeitsbezeichnung aus der Liste aus.

   ![Zuweisen einer Vertraulichkeitsbezeichnung auf der Übersichtsseite für eine Gruppe](./media/groups-assign-sensitivity-labels/assign-to-existing.png)

1. Wählen Sie **Speichern**, um Ihre Änderungen zu speichern.

## <a name="remove-a-label-from-an-existing-group-in-azure-portal"></a>Entfernen einer Bezeichnung von einer vorhandenen Gruppe im Azure-Portal

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem globalen Administrator- oder Gruppenadministratorkonto oder als Gruppenbesitzer an.
1. Wählen Sie **Gruppen** aus.
1. Wählen Sie auf der Seite **Alle Gruppen** die Gruppe aus, deren Bezeichnung Sie entfernen möchten.
1. Wählen Sie auf der Seite **Gruppe** die Option **Eigenschaften** aus.
1. Wählen Sie **Entfernen**.
1. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

## <a name="using-classic-azure-ad-classifications"></a>Verwenden klassischer Azure AD-Klassifizierungen

Nachdem Sie diese Funktion aktiviert haben, werden die „klassischen“ Klassifizierungen für Gruppen nur in vorhandenen Gruppen und Standorten angezeigt. Sie sollten sie nur dann für neue Gruppen verwenden, wenn Sie Gruppen in Apps erstellen, die keine Vertraulichkeitsbezeichnung unterstützen. Ihr Administrator kann sie bei Bedarf später in Vertraulichkeitsbezeichnungen umwandeln. Klassische Klassifizierungen sind die alten Klassifizierungen, die Sie durch Definieren von Werten für die `ClassificationList`-Einstellung in Azure AD PowerShell eingerichtet haben. Wenn dieses Feature aktiviert ist, werden diese Klassifizierungen nicht auf Gruppen angewendet.

## <a name="troubleshooting-issues"></a>Fragen zur Problembehandlung

### <a name="sensitivity-labels-are-not-available-for-assignment-on-a-group"></a>Vertraulichkeitsbezeichnungen sind nicht für die Zuweisung zu einer Gruppe verfügbar.

Die Option „Vertraulichkeitsbezeichnung“ wird nur für Gruppen angezeigt, wenn alle der folgenden Bedingungen erfüllt sind:

1. Für diese Azure AD-Organisation werden die Bezeichnungen im Microsoft 365 Compliance Center veröffentlicht.
1. Das Feature ist aktiviert, „EnableMIPLabels“ ist im Azure AD PowerShell-Modul auf „True“ festgelegt.
1. Die Bezeichnungen werden im PowerShell-Modul „Security & Compliance“ mit dem Cmdlet „Execute-AzureAdLabelSync“ mit Azure AD synchronisiert.
1. Bei der Gruppe handelt es sich um eine Microsoft 365-Gruppe.
1. Die Organisation verfügt über eine Active Azure Active Directory Premium P1-Lizenz.
1. Der aktuell angemeldete Benutzer verfügt über ausreichende Berechtigungen, um Bezeichnungen zuzuweisen. Der Benutzer muss entweder ein globaler Administrator, ein Gruppenadministrator oder der Gruppenbesitzer sein.

Stellen Sie sicher, dass alle Bedingungen erfüllt sind, damit Sie einer Gruppe Bezeichnungen zuweisen können.

### <a name="the-label-i-want-to-assign-is-not-in-the-list"></a>Die Bezeichnung, die ich zuweisen möchte, ist nicht in der Liste enthalten.

Wenn die gesuchte Bezeichnung nicht in der Liste enthalten ist, kann dies einen der folgenden Gründe haben:

- Die Bezeichnung wird möglicherweise nicht im Microsoft 365 Compliance Center veröffentlicht. Dies könnte auch für Bezeichnungen gelten, die nicht mehr veröffentlicht werden. Weitere Informationen erhalten Sie von Ihrem Administrator.
- Die Bezeichnung kann veröffentlicht werden, ist aber für den angemeldeten Benutzer nicht verfügbar. Weitere Informationen zum Zugriff auf die Bezeichnung erhalten Sie von Ihrem Administrator.

### <a name="how-to-change-the-label-on-a-group"></a>Ändern der Bezeichnung einer Gruppe

Bezeichnungen können jederzeit mit denselben folgenden Schritten wie bei der Zuweisung eines Bezeichners zu einer bestehenden Gruppe ausgetauscht werden:

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem globalen Administrator- oder Gruppenadministratorkonto oder als Gruppenbesitzer an.
1. Wählen Sie **Gruppen** aus.
1. Wählen Sie auf der Seite **Alle Gruppen** die Gruppe aus, die Sie bezeichnen möchten.
1. Wählen Sie auf der Seite der ausgewählten Gruppe **Eigenschaften** und anschließend eine neue Vertraulichkeitsbezeichnung aus der Liste aus.
1. Wählen Sie **Speichern** aus.

### <a name="group-setting-changes-to-published-labels-arent-updated-on-the-groups"></a>Änderungen der Gruppeneinstellungen an veröffentlichten Bezeichnungen werden in den Gruppen nicht aktualisiert.

Wenn Sie Änderungen an den Gruppeneinstellungen für eine veröffentlichte Bezeichnung im [Microsoft 365 Compliance Center](https://sip.protection.office.com/homepage) vornehmen, werden diese Richtlinienänderungen nicht automatisch auf die bezeichneten Gruppen angewendet. Microsoft empfiehlt, die Gruppeneinstellungen für die Bezeichnung nicht im Microsoft 365 Compliance Center zu ändern, nachdem die Vertraulichkeitsbezeichnung veröffentlicht und auf Gruppen angewendet wurde.

Wenn Sie eine Änderung vornehmen müssen, verwenden Sie ein [Azure AD PowerShell-Skript](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1), um Updates manuell auf die betroffenen Gruppen anzuwenden. Diese Methode stellt sicher, dass alle vorhandenen Gruppen die neue Einstellung erzwingen.

## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Vertraulichkeitsbezeichnungen bei Microsoft Teams, Microsoft 365-Gruppen und SharePoint-Websites](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites)
- [Manuelles Aktualisieren von Gruppen nach der Änderung der Bezeichnungsrichtlinie mit einem Azure AD PowerShell-Skript](https://github.com/microsoftgraph/powershell-aad-samples/blob/master/ReassignSensitivityLabelToO365Groups.ps1)
- [Bearbeiten Ihrer Gruppeneinstellungen](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Verwalten von Gruppen mithilfe von PowerShell-Befehlen](../enterprise-users/groups-settings-v2-cmdlets.md)
