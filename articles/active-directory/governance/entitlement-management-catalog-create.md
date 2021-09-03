---
title: Erstellen und Verwalten eines Ressourcenkatalogs in der Berechtigungsverwaltung – Azure AD
description: Erfahren Sie, wie Sie einen neuen Container für Ressourcen und Zugriffspakete in der Azure Active Directory-Berechtigungsverwaltung erstellen.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32b848f6a34fbd25322c53cd35dc0db600743c88
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/28/2021
ms.locfileid: "114730207"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Erstellen und Verwalten eines Ressourcenkatalogs in der Azure AD-Berechtigungsverwaltung

## <a name="create-a-catalog"></a>Erstellen eines Katalogs

Ein Katalog ist ein Container für Ressourcen und Zugriffspakete. Sie erstellen einen Katalog, wenn Sie zugehörige Ressourcen und Zugriffspakete gruppieren möchten. Der Benutzer, der den Katalog erstellt, ist der erste Katalogbesitzer. Ein Katalogbesitzer kann weitere Katalogbesitzer hinzufügen.

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator oder Katalogersteller

> [!NOTE]
> Benutzer mit zugewiesener Rolle „Benutzeradministrator“ können keine Kataloge mehr erstellen oder Zugriffspakete in einem Katalog verwalten, dessen Besitzer sie nicht sind. Wenn Benutzern in Ihrer Organisation die Rolle „Benutzeradministrator“ zugewiesen wurde, um in „Berechtigungsverwaltung“ Kataloge, Zugriffspakete oder Richtlinien zu konfigurieren, sollten Sie diesen Benutzern stattdessen die Rolle **Identity Governance-Administrator** zuweisen.

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**.

    ![Berechtigungsverwaltungskataloge im Azure-Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicken Sie auf **Neuer Katalog**.

1. Geben Sie einen eindeutigen Namen sowie eine Beschreibung für den Katalog ein.

    Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

1. Wenn Sie möchten, dass die Zugriffspakete in diesem Katalog unmittelbar nach der Erstellung von Benutzern angefordert werden können, legen Sie **Aktiviert** auf **Ja** fest.

1. Wenn Sie zulassen möchten, dass Benutzer in ausgewählten externen Verzeichnissen Zugriffspakete in diesem Katalog anfordern können, legen Sie **Für externe Benutzer aktiviert** auf **Ja** fest.

    ![Bereich „Neuer Katalog“](./media/entitlement-management-shared/new-catalog.png)

1. Klicken Sie auf **Erstellen**, um den Katalog zu erstellen.

## <a name="create-a-catalog-programmatically"></a>Programmgesteuertes Erstellen eines Katalogs
### <a name="create-a-catalog-with-microsoft-graph"></a>Erstellen eines Katalogs mit Microsoft Graph

Sie können auch mithilfe von Microsoft Graph einen Katalog erstellen.  Ein Benutzer in einer passenden Rolle mit einer Anwendung mit der delegierten Berechtigung `EntitlementManagement.ReadWrite.All` oder einer Anwendung mit dieser Anwendungsberechtigung kann die API aufrufen, um [eine accessPackageCatalog-Instanz zu erstellen](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

### <a name="create-a-catalog--with-powershell"></a>Erstellen eines Katalogs mit PowerShell

Sie können einen Katalog in PowerShell mit dem Cmdlet `New-MgEntitlementManagementAccessPackageCatalog` erstellen, das in den [PowerShell-Cmdlets für Identity Governance von Microsoft Graph](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) (ab Modulversion 1.6.0) enthalten ist.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>Hinzufügen von Ressourcen zu einem Katalog

Um Ressourcen in ein Zugriffspaket einschließen zu können, müssen die Ressourcen in einem Katalog vorhanden sein. Bei den Typen von Ressourcen, die Sie hinzufügen können, handelt es sich um Gruppen, Anwendungen und SharePoint Online-Websites.

* Die Gruppen können in der Cloud erstellte Microsoft 365-Gruppen oder in der Cloud erstellte Azure AD-Sicherheitsgruppen sein.  Gruppen, die aus einer lokalen Active Directory-Instanz stammen, können nicht als Ressourcen zugewiesen werden, weil ihre Besitzer- oder Mitgliedsattribute in Azure AD nicht geändert werden können.   Gruppen, die ursprünglich als Verteilergruppen in Exchange Online vorliegen, können in Azure AD ebenfalls nicht geändert werden.
* Die Anwendungen können Azure AD-Unternehmensanwendungen sein, einschließlich SaaS-Anwendungen und Ihrer eigenen in Azure AD integrierten Anwendungen. Weitere Informationen zum Auswählen geeigneter Ressourcen für Anwendungen mit mehreren Rollen finden Sie unter [Hinzufügen von Ressourcenrollen](entitlement-management-access-package-resources.md#add-resource-roles).
* Die Websites können SharePoint Online-Websites oder SharePoint Online-Websitesammlungen sein.

**Erforderliche Rolle:** Siehe [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Ressourcen hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Ressourcen**.

1. Klicken Sie auf **Ressourcen hinzufügen**.

1. Klicken Sie auf einen Ressourcentyp: **Gruppen und Teams**, **Anwendungen** oder **SharePoint-Websites**.

    Wenn Sie eine Ressource, die Sie hinzufügen möchten, nicht sehen, oder wenn Sie eine Ressource nicht hinzufügen können, stellen Sie sicher, dass Sie die erforderliche Azure AD-Verzeichnisrolle und -Berechtigungsverwaltungsrolle haben. Möglicherweise müssen Sie jemanden mit den erforderlichen Rollen beauftragen, die Ressource Ihrem Katalog hinzuzufügen. Weitere Informationen finden Sie unter [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Wählen Sie eine oder mehrere Ressourcen des Typs aus, die Sie dem Katalog hinzufügen möchten.

    ![Hinzufügen von Ressourcen zu einem Katalog](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Klicken Sie abschließend auf **Hinzufügen**.

    Diese Ressourcen können jetzt in Zugriffspakete im Katalog aufgenommen werden.

### <a name="add-a-multi-geo-sharepoint-site"></a>Hinzufügen einer SharePoint-Website mit mehreren geografischen Regionen

1. Wenn für SharePoint [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) aktiviert ist, wählen Sie die Umgebung aus, aus der Sie Websites auswählen möchten.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="Zugriffspaket: Hinzufügen von Ressourcenrollen: Auswählen von SharePoint-Websites: Multi-Geo-Websites":::

1. Wählen Sie dann die Websites aus, die Sie dem Katalog hinzufügen möchten. 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>Programmgesteuertes Hinzufügen einer Ressource zu einem Katalog

Sie können eine Ressource auch mithilfe von Microsoft Graph einem Katalog hinzufügen.  Ein Benutzer in einer passenden Rolle oder ein Katalog- und Ressourcenbesitzer mit einer Anwendung, die über die delegierte `EntitlementManagement.ReadWrite.All`-Berechtigung verfügt, kann die API aufrufen, um [eine accessPackageResourceRequest zu erstellen](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true).  Eine Anwendung mit Anwendungsberechtigungen kann jedoch noch nicht programmgesteuert eine Ressource ohne Benutzerkontext zum Zeitpunkt der Anforderung hinzufügen.

## <a name="remove-resources-from-a-catalog"></a>Entfernen von Ressourcen aus einem Katalog

Sie können Ressourcen aus einem Katalog entfernen. Eine Ressource kann nur aus einem Katalog entfernt werden, wenn sie nicht in einem der Zugriffspakete des Katalogs verwendet wird.

**Erforderliche Rolle:** Siehe [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, aus dem Sie Ressourcen entfernen möchten.

1. Klicken Sie im linken Menü auf **Ressourcen**.

1. Wählen Sie die Ressourcen aus, die Sie entfernen möchten.

1. Klicken Sie auf **Entfernen**. Oder klicken Sie auf die Auslassungspunkte ( **...** ), und klicken Sie dann auf **Ressource entfernen**.


## <a name="add-additional-catalog-owners"></a>Hinzufügen weiterer Katalogbesitzer

Der Benutzer, der einen Katalog erstellt hat, ist der erste Katalogbesitzer. Wenn Sie die Verwaltung eines Katalogs delegieren möchten, fügen Sie der Rolle „Katalogbesitzer“ Benutzer hinzu. Dadurch können die Aufgaben der Katalogverwaltung gemeinsam wahrgenommen werden. 

Führen Sie die folgenden Schritte aus, um einem Benutzer die Rolle „Katalogbesitzer“ zuzuweisen:

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Administratoren hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Rollen und Administratoren**.

    ![Katalogrollen und -administratoren](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicken Sie auf **Besitzer hinzufügen**, um die Mitglieder für diese Rollen auszuwählen.

1. Klicken Sie auf **Auswählen**, um diese Mitglieder hinzuzufügen.

## <a name="edit-a-catalog"></a>Bearbeiten eines Katalogs

Sie können den Namen und die Beschreibung eines Katalogs bearbeiten. Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie bearbeiten möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Bearbeiten**.

1. Bearbeiten Sie den Namen, die Beschreibung oder die aktivierten Einstellungen des Katalogs.

    ![Bearbeiten von Katalogeinstellungen](./media/entitlement-management-shared/catalog-edit.png)

1. Klicken Sie auf **Speichern**.

## <a name="delete-a-catalog"></a>Löschen eines Katalogs

Sie können einen Katalog nur löschen, wenn er keine Zugriffspakete enthält.

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie löschen möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Löschen**.

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

### <a name="deleting-a-catalog-programmatically"></a>Programmgesteuertes Löschen eines Katalogs

Sie können auch mithilfe von Microsoft Graph einen Katalog löschen.  Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte `EntitlementManagement.ReadWrite.All`-Berechtigung verfügt, kann die API aufrufen, um [einen accessPackageCatalog zu löschen](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte

- [Delegieren der Zugriffssteuerung an Zugriffspaket-Manager](entitlement-management-delegate-managers.md)
