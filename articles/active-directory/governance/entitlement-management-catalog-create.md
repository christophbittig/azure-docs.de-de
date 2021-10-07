---
title: Erstellen und Verwalten eines Ressourcenkatalogs in der Berechtigungsverwaltung – Azure AD
description: Erfahren Sie, wie Sie einen neuen Container für Ressourcen und Zugriffspakete in der Azure Active Directory-Berechtigungsverwaltung erstellen.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: ''
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 8/31/2021
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9540c207388dfc5b37e2dd03939b14753c3d8ec7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791781"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Erstellen und Verwalten eines Ressourcenkatalogs in der Azure AD-Berechtigungsverwaltung

In diesem Artikel erfahren Sie, wie Sie einen Katalog mit Ressourcen und Zugriffspaketen in der Berechtigungsverwaltung für Azure Active Directory (Azure AD) erstellen und verwalten.

## <a name="create-a-catalog"></a>Erstellen eines Katalogs

Ein Katalog ist ein Container für Ressourcen und Zugriffspakete. Sie erstellen einen Katalog, wenn Sie zugehörige Ressourcen und Zugriffspakete gruppieren möchten. Der Benutzer, der den Katalog erstellt, ist der erste Katalogbesitzer. Katalogbesitzer*innen können weitere Katalogbesitzer*innen hinzufügen.

**Erforderliche Rollen:** globale Administrator*innen, Identity Governance-Administrator*innen, Benutzeradministrator*innen oder Katalogersteller*in

> [!NOTE]
> Benutzer*innen mit der zugewiesenen Rolle „Benutzeradministrator*in“ können keine Kataloge mehr erstellen oder Zugriffspakete in einem Katalog verwalten, den sie nicht besitzen. Wenn Benutzer*innen Ihrer Organisation die Rolle „Benutzeradministrator*in“ zugewiesen wurde, um Kataloge, Zugriffspakete oder Richtlinien in der Berechtigungsverwaltung zu konfigurieren, sollten Sie diesen Benutzer*innen stattdessen die Rolle „Identity Governance-Administrator*in“ zuweisen.

Erstellen eines Katalogs:

1. Klicken Sie im Azure-Portal auf die Option **Azure Active Directory** und dann auf  > **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Kataloge**.

    ![Berechtigungsverwaltungskataloge im Azure-Portal](./media/entitlement-management-catalog-create/catalogs.png)

1. Klicken Sie auf **Neuer Katalog**.

1. Geben Sie einen eindeutigen Namen sowie eine Beschreibung für den Katalog ein.

    Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

1. Wenn Sie möchten, dass die Zugriffspakete in diesem Katalog unmittelbar nach der Erstellung von Benutzer*innen angefordert werden können, legen Sie **Aktiviert** auf **Ja** fest.

1. Wenn Sie zulassen möchten, dass Benutzer in ausgewählten externen Verzeichnissen Zugriffspakete in diesem Katalog anfordern können, legen Sie **Für externe Benutzer aktiviert** auf **Ja** fest.

    ![Neuer Katalog](./media/entitlement-management-shared/new-catalog.png)

1. Klicken Sie auf **Erstellen**, um den Katalog zu erstellen.

## <a name="create-a-catalog-programmatically"></a>Programmgesteuertes Erstellen eines Katalogs

Es gibt zwei Möglichkeiten, einen Katalog programmgesteuert zu erstellen.

### <a name="create-a-catalog-with-microsoft-graph"></a>Erstellen eines Katalogs mit Microsoft Graph

Sie können mithilfe von Microsoft Graph einen Katalog erstellen. Ein Benutzer in einer passenden Rolle mit einer Anwendung mit der delegierten Berechtigung `EntitlementManagement.ReadWrite.All` oder einer Anwendung mit dieser Anwendungsberechtigung kann die API aufrufen, um [eine accessPackageCatalog-Instanz zu erstellen](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true).

### <a name="create-a-catalog-with-powershell"></a>Erstellen eines Katalogs mit PowerShell

Sie können in PowerShell auch mit dem Cmdlet `New-MgEntitlementManagementAccessPackageCatalog`, das in den [PowerShell-Cmdlets für Identity Governance von Microsoft Graph ](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) (ab Modulversion 1.6.0) enthalten ist, einen Katalog erstellen.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>Hinzufügen von Ressourcen zu einem Katalog

Um Ressourcen in ein Zugriffspaket einschließen zu können, müssen die Ressourcen in einem Katalog vorhanden sein. Bei den Typen von Ressourcen, die Sie hinzufügen können, handelt es sich um Gruppen, Anwendungen und SharePoint Online-Websites. Beispiel:

* Die Gruppen können in der Cloud erstellte Microsoft 365-Gruppen oder in der Cloud erstellte Azure AD-Sicherheitsgruppen sein. Gruppen, die aus einer lokalen Active Directory-Instanz stammen, können nicht als Ressourcen zugewiesen werden, weil ihre Besitzer- oder Mitgliedsattribute in Azure AD nicht geändert werden können. Gruppen, die ursprünglich als Verteilergruppen in Exchange Online vorliegen, können in Azure AD ebenfalls nicht geändert werden.
* Apps können Azure AD-Unternehmens-Apps sein, die sowohl Software-as-a-Service-Apps (SaaS-Apps) und Ihre eigenen, in Azure AD integrierten Apps beinhalten. Weitere Informationen zum Auswählen geeigneter Ressourcen für Apps mit mehreren Rollen finden Sie unter [Ressourcenrollen hinzufügen](entitlement-management-access-package-resources.md#add-resource-roles).
* Websites können SharePoint Online-Websites oder SharePoint Online-Websitesammlungen sein.

**Erforderliche Rollen:** Weitere Informationen finden Sie unter [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

Ressourcen zu einem Katalog hinzufügen:

1. Klicken Sie im Azure-Portal auf die Option **Azure Active Directory** und dann auf  > **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Ressourcen hinzufügen möchten.

1. Klicken Sie im linken Menü auf die Option **Ressourcen**.

1. Wählen Sie **Ressourcen hinzufügen** aus.

1. Klicken Sie auf den Ressourcentyp **Gruppen und Teams**, **Apps** oder **SharePoint-Websites**.

    Wenn Sie eine Ressource, die Sie hinzufügen möchten, nicht sehen, oder wenn Sie eine Ressource nicht hinzufügen können, stellen Sie sicher, dass Sie die erforderliche Azure AD-Verzeichnisrolle und -Berechtigungsverwaltungsrolle haben. Möglicherweise müssen Sie jemanden mit den erforderlichen Rollen beauftragen, die Ressource Ihrem Katalog hinzuzufügen. Weitere Informationen finden Sie unter [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

1. Wählen Sie eine oder mehrere Ressourcen des Typs aus, die Sie dem Katalog hinzufügen möchten.

    ![Ressourcen zu Katalog hinzufügen](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. Wenn Sie fertig sind, klicken Sie auf **OK**.

    Diese Ressourcen können jetzt in Zugriffspakete im Katalog aufgenommen werden.

### <a name="add-resource-attributes-preview-in-the-catalog"></a>Hinzufügen von Ressourcenattributen (Vorschau) im Katalog

Attribute sind Pflichtfelder, die anfordernde Personen beantworten müssen, bevor sie ihre Zugriffsanforderung übermitteln. Die Antworten zu diesen Attribute werden genehmigenden Personen angezeigt und auch dem Benutzerobjekt in Azure AD hinzugefügt. 

> [!NOTE]
>Alle für eine Ressource eingerichteten Attribute müssen beantwortet werden, bevor eine Anforderung für ein Zugriffspaket mit dieser Ressource übermittelt werden kann. Wenn anfordernde Personen nicht antworten, wird ihre Anforderung nicht verarbeitet.

Attribute für Zugriffsanforderungen anfordern:

1. Klicken Sie im linken Menü auf **Ressourcen**, und eine Liste der Ressourcen im Katalog wird angezeigt. 

1. Klicken Sie auf die Auslassungspunkte neben der Ressource, der Sie Attribute hinzufügen möchten, und klicken Sie dann auf **Attribute anfordern (Vorschau)** . 

    ![Attribute anfordern (Vorschau)](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  Auswählen des Attributtyps:

    1. **Integrierte** Attribute beinhalten Azure AD-Benutzerprofilattribute.
    1. Mit der **Directory-Schemaerweiterung** können mehr Daten für Benutzerobjekte und andere Verzeichnisobjekte in Azure AD gespeichert werden. Dazu gehören Gruppen, Mandantendetails und Dienstprinzipale. Nur Erweiterungsattribute für Benutzerobjekte können zum Senden von Ansprüchen an Apps verwendet werden.
1. Wenn Sie **Integrierte** Attribute ausgewählt haben, wählen Sie ein Attribut aus der Dropdownliste aus. Wenn Sie **Directory-Schemaerweiterung** ausgewählt haben, geben Sie den Attributnamen in das Textfeld ein.

    > [!NOTE]
    > Das Attribut „User.mobilePhone“ kann nur für Benutzer*innen ohne Administratorrechte aktualisiert werden. Weitere Informationen finden Sie auf [dieser Website](/graph/permissions-reference#remarks-5).

1.  Wählen Sie das Antwortformat aus, das anfordernde Personen verwenden sollen. Mögliche Antwortformate: **kurzer Text**, **Mehrfachauswahl** und **langer Text**.

1.  Wenn Sie Mehrfachauswahl auswählen, klicken Sie auf die Schaltfläche **Bearbeiten und lokalisieren**, um die Antwortoptionen zu konfigurieren. 
    1. Geben Sie im Bereich **Frage anzeigen/bearbeiten** die Antwortoptionen ein, die sie den anfordernden Personen geben möchten, wenn sie die Frage in den Feldern **Antwortwerte** beantworten.
    1. Wählen Sie die Sprache für die Antwortoption aus. Sie können Antwortoptionen lokalisieren, wenn Sie mehrere Sprachen auswählen.
    1. Geben Sie beliebig viele Antworten ein, und klicken Sie dann auf **Speichern**.

1. Wenn Sie möchten, dass der Attributwert bei direkten Zuweisungen und Self-Service-Anforderungen bearbeitet werden kann, klicken Sie auf **Ja**.

    > [!NOTE]
    > ![Attribute bearbeiten](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - Wenn Sie im Feld **Attribute value is editable** (Attributwert kann bearbeitet werden) **Nein** auswählen und der Attributwert *leer ist*, können Benutzer*innen einen Attributwert eingeben. Der Wert kann nach dem Speichern nicht mehr bearbeitet werden. 
    > - Wenn Sie im Feld **Attribute value is editable** (Attributwert kann bearbeitet werden) **Nein** auswählen und der Attributwert *nicht leer ist*, können Benutzer*innen den bereits vorhandenen Wert bei direkten Zuweisungen und Self-Service-Anforderungen nicht bearbeiten.
 
    ![Lokalisierungen hinzufügen](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  Wenn Sie Lokalisierungen hinzufügen möchten, klicken Sie auf **Add localization** (Lokalisierung hinzufügen).

    1. Wählen Sie im Bereich **Add localizations for question** (Lokalisierungen für Frage hinzufügen) den Sprachcode für die Sprache aus, in die Sie die Frage lokalisieren.
    1. Geben Sie die Frage in der von Ihnen konfigurierten Sprache im Feld **Localized Text** (Lokalisierter Text) ein.
    1. Wenn Sie alle Lokalisierungen hinzugefügt haben, klicken Sie auf **Speichern**.

       ![Lokalisierungen speichern](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  Wenn alle Attributinformationen auf der Seite **Attribute anfordern (Vorschau)** fertig sind, klicken Sie auf **Speichern**.

### <a name="add-a-multi-geo-sharepoint-site"></a>Hinzufügen einer SharePoint-Website mit mehreren geografischen Regionen

1. Wenn für SharePoint [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) aktiviert ist, wählen Sie die Umgebung aus, aus der Sie Websites beziehen möchten.
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="SharePoint Online-Websites auswählen":::

1. Wählen Sie dann die Websites aus, die Sie dem Katalog hinzufügen möchten.

### <a name="add-a-resource-to-a-catalog-programmatically"></a>Programmgesteuertes Hinzufügen einer Ressource zu einem Katalog

Sie können eine Ressource auch mithilfe von Microsoft Graph einem Katalog hinzufügen. Ein Benutzer in einer passenden Rolle oder ein Katalog- und Ressourcenbesitzer mit einer Anwendung, die über die delegierte `EntitlementManagement.ReadWrite.All`-Berechtigung verfügt, kann die API aufrufen, um [eine accessPackageResourceRequest zu erstellen](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true). Eine App mit App-Berechtigungen kann zum Zeitpunkt der Anforderung jedoch noch nicht ohne Benutzerkontext programmgesteuert eine Ressource hinzufügen.

## <a name="remove-resources-from-a-catalog"></a>Entfernen von Ressourcen aus einem Katalog

Sie können Ressourcen aus einem Katalog entfernen. Eine Ressource kann nur aus einem Katalog entfernt werden, wenn sie nicht in einem der Zugriffspakete des Katalogs verwendet wird.

**Erforderliche Rollen:** Weitere Informationen finden Sie unter [Erforderliche Rollen, um einem Katalog Ressourcen hinzuzufügen](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog).

Ressourcen aus einem Katalog entfernen:

1. Klicken Sie im Azure-Portal auf die Option **Azure Active Directory** und dann auf  > **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, aus dem Sie Ressourcen entfernen möchten.

1. Klicken Sie im linken Menü auf die Option **Ressourcen**.

1. Wählen Sie die Ressourcen aus, die Sie entfernen möchten.

1. Wählen Sie **Entfernen**. Klicken Sie optional auf die Auslassungspunkte ( **...** ) und dann auf **Ressource entfernen**.

## <a name="add-more-catalog-owners"></a>Katalogbesitzer*innen hinzufügen

Wenn Sie einen Katalog erstellen, werden Sie der oder die erste Katalogbesitzer*in. Wenn Sie die Verwaltung eines Katalogs delegieren möchten, fügen Sie der Rolle „Katalogbesitzer*in“ Benutzer*innen hinzu. Das Hinzufügen weiterer Katalogbesitzer*innen trägt dazu bei, die Zuständigkeiten der Katalogverwaltung zu teilen.

**Erforderliche Rollen:** globale Administrator*innen, Identity Governance-Administrator*innen, Benutzeradministrator*innen oder Katalogbesitzer*innen

Benutzer*innen der Rolle „Katalogbesitzer*in“ zuweisen:

1. Klicken Sie im Azure-Portal auf die Option **Azure Active Directory** und dann auf  > **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Administrator*innen hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Rollen und Administratoren**.

    ![Katalogrollen und Administrator*innen](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicken Sie auf **Besitzer hinzufügen**, um die Mitglieder für diese Rollen auszuwählen.

1. Klicken Sie auf **Auswählen**, um diese Mitglieder hinzuzufügen.

## <a name="edit-a-catalog"></a>Bearbeiten eines Katalogs

Sie können den Namen und die Beschreibung eines Katalogs bearbeiten. Benutzern werden diese Informationen in den Details eines Zugriffspakets angezeigt.

**Erforderliche Rollen:** globale Administrator*innen, Identity Governance-Administrator*innen, Benutzeradministrator*innen oder Katalogbesitzer*innen

Katalog bearbeiten:

1. Klicken Sie im Azure-Portal auf die Option **Azure Active Directory** und dann auf  > **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie bearbeiten möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Bearbeiten**.

1. Bearbeiten Sie den Namen, die Beschreibung oder die aktivierten Einstellungen des Katalogs.

    ![Katalogeinstellungen bearbeiten](./media/entitlement-management-shared/catalog-edit.png)

1. Wählen Sie **Speichern** aus.

## <a name="delete-a-catalog"></a>Löschen eines Katalogs

Sie können einen Katalog nur löschen, wenn er keine Zugriffspakete enthält.

**Erforderliche Rollen:** globale Administrator*innen, Identity Governance-Administrator*innen, Benutzeradministrator*innen oder Katalogbesitzer*innen

Katalog löschen:

1. Klicken Sie im Azure-Portal auf die Option **Azure Active Directory** und dann auf  > **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, den Sie löschen möchten.

1. Klicken Sie auf der Seite **Übersicht** des Katalogs auf **Löschen**.

1. Klicken Sie im angezeigten Meldungsfeld auf **Ja**.

### <a name="delete-a-catalog-programmatically"></a>Programmgesteuertes Löschen eines Katalogs

Sie können einen Katalog auch mithilfe von Microsoft Graph löschen. Ein Benutzer in einer passenden Rolle mit einer Anwendung, die über die delegierte `EntitlementManagement.ReadWrite.All`-Berechtigung verfügt, kann die API aufrufen, um [einen accessPackageCatalog zu löschen](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true).

## <a name="next-steps"></a>Nächste Schritte

[Delegieren der Zugriffssteuerung an Zugriffspaket-Manager](entitlement-management-delegate-managers.md)