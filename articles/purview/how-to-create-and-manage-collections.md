---
title: Erstellen und Verwalten von Sammlungen
description: In diesem Artikel wird erläutert, wie Sie Sammlungen in Azure Purview erstellen und verwalten.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: template-how-to
ms.openlocfilehash: e96a3e1e523c466cc3dc851725b648cba78dc8e8
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502115"
---
# <a name="create-and-manage-collections-in-azure-purview"></a>Erstellen und Verwalten von Sammlungen in Azure Purview

> [!NOTE]
> Zurzeit gilt dieser Leitfaden nur für Azure Purview-Instanzen, die **am oder nach dem 18. August 2021** erstellt wurden. Vor dem 18. August erstellte Instanzen können Sammlungen erstellen, aber keine Berechtigungen über diese Sammlungen verwalten. Informationen zum Erstellen einer Sammlung für Purview-Instanzen, die vor dem 18. August erstellt wurden, finden Sie im [**Leitfaden zu Legacysammlungen**](#legacy-collection-guide) am Ende dieses Artikels.
>
> Alle Legacykonten erhalten in den kommenden Wochen automatisch ein Upgrade. Sie erhalten eine E-Mail-Benachrichtigung, wenn Ihr Purview-Konto aktualisiert wird. Informationen darüber, was sich bei einem Upgrade Ihres Kontos ändert, finden Sie in unserem Leitfaden zum [Upgrade von Konten](concept-account-upgrade.md).

Sammlungen in Azure Purview können verwendet werden, um Assets und Quellen nach den Abläufen in Ihrem Unternehmen zu organisieren, aber sie sind auch das Werkzeug, um den Zugriff in Purview zu verwalten. Der vorliegende Leitfaden führt Sie durch die Erstellung und Verwaltung dieser Sammlungen und beschreibt die Schritte zum Registrieren von Quellen und zum Hinzufügen von Ressourcen zu Ihren Sammlungen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ihr eigener [Azure Active Directory-Mandant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

### <a name="check-permissions"></a>Überprüfen Sie die Berechtigungen.

Zum Erstellen und Verwalten von Sammlungen in Purview muss Ihnen innerhalb von Purview die Rolle **Sammlungsadministrator** zugewiesen sein. Wir können diese Berechtigungen in [Purview Studio](https://web.purview.azure.com/resource/) überprüfen. Sie gelangen zu Purview Studio, indem Sie zu Ihrer Purview-Ressource im [Azure-Portal](https://portal.azure.com) wechseln und auf der Übersichtsseite die Kachel „Purview Studio öffnen“ auswählen.

1. Wählen Sie im linken Bereich „Datenzuordnung“ > „Sammlungen“ aus, um die Seite für die Sammlungsverwaltung zu öffnen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und ausgewählter Registerkarte „Sammlungen“" border="true":::

1. Wählen Sie Ihre Stammsammlung aus. Dies ist die oberste Sammlung in Ihrer Sammlungsliste, die den gleichen Namen aufweist wie Ihre Purview-Ressource. In unserem Beispiel lautet der Name der Stammsammlung „Contoso Purview“. Wenn bereits Sammlungen vorhanden sind, können Sie alternativ eine beliebige Sammlung auswählen, für die Sie eine untergeordnete Sammlung erstellen möchten.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-root-collection.png" alt-text="Screenshot: Purview Studio mit geöffneter Datenzuordnung und hervorgehobener Stammsammlung" border="true":::

1. Wählen Sie im Sammlungsfenster **Rollenzuweisungen** aus.

    :::image type="content" source="./media/how-to-create-and-manage-collections/role-assignments.png" alt-text="Screenshot: Purview Studio mit geöffneter Datenzuordnung und hervorgehobener Registerkarte „Rollenzuweisungen“" border="true":::

1. Um eine Sammlung zu erstellen, müssen Sie in der Liste der Sammlungsadministratoren unterhalb von „Rollenzuweisungen“ enthalten sein. Wenn Sie die Purview-Ressource erstellt haben, sollten Sie bereits als Sammlungsadministrator in der Stammsammlung aufgeführt werden. Andernfalls müssen Sie sich an den Sammlungsadministrator wenden, damit Ihnen die entsprechende Berechtigung erteilt wird.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-admins.png" alt-text="Screenshot: Purview Studio mit geöffneter Datenzuordnung und hervorgehobenem Abschnitt „Sammlungsadministrator“" border="true":::

## <a name="collection-management"></a>Sammlungsverwaltung

### <a name="create-a-collection"></a>Erstellen einer Sammlung

Sie müssen als Sammlungsadministrator fungieren, um eine Sammlung erstellen zu können. Wenn Sie sich nicht sicher sind, überprüfen Sie mithilfe der [Anleitung oben](#check-permissions) Ihre Berechtigungen.

1. Wählen Sie im linken Bereich „Datenzuordnung“ > „Sammlungen“ aus, um die Seite für die Sammlungsverwaltung zu öffnen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/find-collections.png" alt-text="Screenshot: Purview Studio mit geöffneter Datenzuordnung und ausgewählter und geöffneter Registerkarte „Sammlungen“" border="true":::

1. Wählen Sie **+ Sammlung hinzufügen** aus. Beachten Sie auch hier, dass Sammlungen nur von [Sammlungsadministratoren](#check-permissions) verwaltet werden können.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-add-a-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster für neue Sammlungen und hervorgehobenen Schaltflächen zum Hinzufügen einer Sammlung" border="true":::

1. Geben Sie im rechten Bereich den Namen und eine Beschreibung für die Sammlung ein. Bei Bedarf können Sie der neuen Sammlung auch Benutzer oder Gruppen als Sammlungsadministratoren hinzufügen.
1. Klicken Sie auf **Erstellen**.

    :::image type="content" source="./media/how-to-create-and-manage-collections/create-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster „Neue Sammlung“, mit ausgewähltem Anzeigenamen und Sammlungsadministrator und hervorgehobener Schaltfläche „Erstellen“" border="true":::

1. Die Informationen zur neuen Sammlung werden auf der Seite angezeigt.

    :::image type="content" source="./media/how-to-create-and-manage-collections/created-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster für die neu erstellte Sammlung" border="true":::

### <a name="edit-a-collection"></a>Eine Sammlung bearbeiten

1. Wählen Sie entweder auf der Seite mit den Sammlungsdetails oder im Dropdownmenü der Sammlung die Option **Bearbeiten** aus.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-collection.png" alt-text="Screenshot: Purview Studio mit geöffnetem Sammlungsfenster, mit hervorgehobener Schaltfläche „Bearbeiten“ im Fenster der ausgewählten Sammlung und unterhalb der Schaltfläche mit den Auslassungspunkten neben dem Namen der Sammlung" border="true":::

1. Derzeit können Sammlungsbeschreibung und Sammlungsadministratoren bearbeitet werden. Nehmen Sie die gewünschten Änderungen vor, und wählen Sie dann **Speichern** aus, um Ihre Änderungen zu speichern.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-description.png" alt-text="Screenshot: Purview Studio mit geöffnetem Fenster zur Sammlungsbearbeitung, einer der Sammlung hinzugefügten Beschreibung und hervorgehobener Schaltfläche „Speichern“" border="true":::

### <a name="view-collections"></a>Anzeigen von Sammlungen

1. Wählen Sie das Dreieckssymbol neben dem Namen der Sammlung aus, um die Sammlungshierarchie zu erweitern oder zu reduzieren. Klicken Sie zur Navigation auf die Sammlungsnamen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/subcollection-menu.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit hervorgehobener Schaltfläche neben dem Namen der Sammlung" border="true":::

1. Geben Sie oben in der Liste einen Begriff in das Filterfeld ein, um Sammlungen zu filtern.

    :::image type="content" source="./media/how-to-create-and-manage-collections/filter-collections.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit hervorgehobener Filterfunktion oberhalb der Sammlungen" border="true":::

1. Klicken Sie im Kontextmenü der Stammsammlung auf **Aktualisieren**, um die Sammlungsliste neu zu laden.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-collections.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit ausgewählter Schaltfläche neben dem Ressourcennamen und hervorgehobener Schaltfläche „Aktualisieren“" border="true":::

1. Klicken Sie auf der Seite mit den Sammlungsdetails auf **Aktualisieren**, um die Sammlung neu zu laden.

    :::image type="content" source="./media/how-to-create-and-manage-collections/refresh-single-collection.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit hervorgehobener Schaltfläche „Aktualisieren“ im Sammlungsfenster" border="true":::

### <a name="delete-a-collection"></a>Löschen einer Sammlung

Sie müssen als Sammlungsadministrator fungieren, um eine Sammlung erstellen zu können. Wenn Sie sich nicht sicher sind, überprüfen Sie mithilfe der Anleitung oben Ihre Berechtigungen. Sammlungen können nur gelöscht werden, wenn ihr keine untergeordneten Sammlungen, Ressourcen, Datenquellen oder Scans zugeordnet sind. 

1. Wählen Sie auf der Seite mit den Sammlungsdetails die Option **Löschen** aus.
   
   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collections.png" alt-text="Screenshot des Purview Studio-Fensters zum Löschen einer Sammlung" border="true":::

2. Wählen Sie **Bestätigen**, wenn Sie dazu aufgefordert werden, **Sind Sie sicher, dass Sie diese Sammlung löschen möchten?**

   :::image type="content" source="./media/how-to-create-and-manage-collections/delete-collection-confirmation.png" alt-text="Screenshot des Fensters &quot;Purview Studio&quot; mit Bestätigungsmeldung zum Löschen einer Sammlung" border="true":::

3. Überprüfen Sie das Löschen der Sammlung aus Ihrer Purview-Data Map.

## <a name="add-roles-and-restrict-access-through-collections"></a>Hinzufügen von Rollen und Einschränken des Zugriffs über Sammlungen

Da Berechtigungen über Sammlungen in Purview verwaltet werden, ist es wichtig, die Rollen und die Berechtigungen zu verstehen, die sie Ihren Benutzern erteilen. Ein Benutzer, dem Berechtigungen für eine Sammlung gewährt wurden, hat Zugriff auf Quellen und Ressourcen, die dieser Sammlung zugeordnet sind. Darüber hinaus erbt der Benutzer Berechtigungen für untergeordnete Sammlungen. Die Vererbung [kann eingeschränkt werden](#restrict-inheritance), ist jedoch standardmäßig zugelassen.

Im nachstehenden Leitfaden werden die Rollen, deren Verwaltung und die Berechtigungsvererbung erläutert.

### <a name="roles"></a>Rollen

Alle zugewiesenen Rollen gelten für Quellen, Ressourcen und andere Objekte innerhalb der Sammlung, auf die die Rolle angewendet wird.

* **Sammlungsadministratoren**: Sie können die Sammlung und deren Details bearbeiten und untergeordnete Sammlungen hinzufügen. Sie können einem Sammlungsbereich außerdem Datenkuratoren, Datenleseberechtigte und weitere Purview-Rollen hinzufügen. Sammlungsadministratoren, die automatisch von einer übergeordneten Sammlung geerbt werden, können nicht entfernt werden.
* **Datenquellenadministratoren**: Sie können Datenquellen und Datenüberprüfungen verwalten.
* **Datenkuratoren**: Sie können Katalogdatenobjekte erstellen, lesen, ändern und löschen und Beziehungen zwischen Objekten einrichten.
* **Datenleseberechtigte**: Sie können auf Katalogdatenobjekte zugreifen, diese aber nicht ändern.

### <a name="add-role-assignments"></a>Hinzufügen von Rollenzuweisungen

1. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um alle Rollen in einer Sammlung anzuzeigen. Nur ein Sammlungsadministrator kann Rollenzuweisungen verwalten.

    :::image type="content" source="./media/how-to-create-and-manage-collections/select-role-assignments.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit hervorgehobener Registerkarte „Rollenzuweisungen“" border="true":::

1. Wählen Sie **Rollenzuweisungen bearbeiten** oder das Personensymbol aus, um die einzelnen Rollenmitglieder zu bearbeiten.

    :::image type="content" source="./media/how-to-create-and-manage-collections/edit-role-assignments.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit ausgewählter Dropdownliste „Rollenzuweisungen bearbeiten“" border="true":::

1. Geben Sie einen Begriff in das Textfeld ein, um nach Benutzern zu suchen, die Sie als Rollenmitglied hinzufügen möchten. Klicken Sie auf das **X**, um Mitglieder zu entfernen, die Sie nicht hinzufügen möchten.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-user-permissions.png" alt-text="Screenshot: Purview Studio-Sammlung mit dem Fenster für Sammlungsadministratoren und hervorgehobener Suchleiste" border="true":::

1. Klicken Sie auf **OK**, um Ihre Änderungen zu speichern. Die neuen Benutzer werden in der Liste der Rollenzuweisungen angezeigt.

### <a name="remove-role-assignments"></a>Entfernen von Rollenzuweisungen

1. Klicken Sie auf die Schaltfläche **X** neben einem Benutzernamen, um eine Rollenzuweisung zu entfernen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-role-assignment.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit ausgewählter Registerkarte „Rollenzuweisungen“ und hervorgehobener Schaltfläche X neben einem der Namen" border="true":::

1. Klicken Sie auf **Bestätigen**, wenn Sie sicher sind, dass der Benutzer entfernt werden soll.

    :::image type="content" source="./media/how-to-create-and-manage-collections/confirm-remove.png" alt-text="Screenshot: Popupfenster zur Bestätigung mit hervorgehobener Schaltfläche „Bestätigen“" border="true":::

### <a name="restrict-inheritance"></a>Einschränken der Vererbung

Sammlungsberechtigungen werden automatisch von der übergeordneten Sammlung geerbt. Beispielsweise werden sämtliche Berechtigungen für die Stammsammlung (die Sammlung ganz oben in der Liste, die den gleichen Namen aufweist wie Ihre Purview-Ressource) an die darunter enthaltenen Sammlungen vererbt. Sie können die Vererbung durch eine übergeordnete Sammlung jederzeit über die Option „Geerbte Berechtigungen einschränken“ einschränken.

Nachdem Sie die Vererbung eingeschränkt haben, müssen Sie der eingeschränkten Sammlung Benutzer direkt hinzufügen, um ihnen Zugriff zu gewähren.

1. Navigieren Sie zu der Sammlung, für die Sie die Vererbung einschränken möchten, und wählen Sie die Registerkarte **Rollenzuweisungen** aus.
1. Klicken Sie auf **Geerbte Berechtigungen einschränken**, und wählen Sie im Popupdialogfeld **Zugriff einschränken** aus, um geerbte Berechtigungen aus dieser Sammlung und allen untergeordneten Sammlungen zu entfernen. Beachten Sie, dass Berechtigungen von Sammlungsadministratoren hiervon unberührt bleiben.

    :::image type="content" source="./media/how-to-create-and-manage-collections/restrict-access-inheritance.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit ausgewählter Registerkarte „Rollenzuweisungen“ und hervorgehobener Umschaltfläche „Geerbte Berechtigungen einschränken“" border="true":::

1. Nach der Einschränkung werden geerbte Mitglieder aus den Rollen entfernt, mit Ausnahme des Sammlungsadministrators.
1. Klicken Sie erneut auf die Umschaltfläche **Geerbte Berechtigungen einschränken**, um die Auswahl rückgängig zu machen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/remove-restriction.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit ausgewählter Registerkarte „Rollenzuweisungen“ und hervorgehobener Umschaltfläche „Geerbte Berechtigungen einschränken“" border="true":::
    
## <a name="register-source-to-a-collection"></a>Registrieren einer Quelle für eine Sammlung

1. Klicken Sie auf die Option **Registrieren** oder auf das Registrierungssymbol für den Sammlungsknoten, um eine Datenquelle zu registrieren. Beachten Sie, dass nur Datenquellenadministratoren Quellen registrieren können.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-by-collection.png" alt-text="Screenshot: Purview Studio-Datenzuordnungsfenster mit hervorgehobener Schaltfläche „Registrieren“ sowohl oben auf der Seite als auch unterhalb einer Sammlung"border="true":::

1. Geben Sie den Namen der Datenquelle und weitere Quellinformationen ein.  Im unteren Bereich des Formulars werden alle Sammlungen aufgeführt, für die Sie über Berechtigungen zum Überprüfen verfügen. Sie können eine Sammlung auswählen. Alle Ressourcen unterhalb dieser Quelle gehören zur ausgewählten Sammlung.

    :::image type="content" source="./media/how-to-create-and-manage-collections/register-source.png" alt-text="Screenshot: Fensters für die Registrierung von Quellen"border="true":::

1. Die erstellte Datenquelle wird unterhalb der ausgewählten Sammlung platziert. Klicken Sie auf **Details anzeigen**, um die Datenquelle anzuzeigen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/see-registered-source.png" alt-text="Screenshot: Purview Studio-Datenzuordnungsfenster mit hervorgehobener Karte der neu hinzugefügten Quelle"border="true":::

1. Wählen Sie **Neue Überprüfung aus**, um eine Überprüfung unterhalb der Datenquelle zu erstellen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/new-scan.png" alt-text="Screenshot: Purview Studio-Quellenfenster mit hervorgehobener Schaltfläche „Neue Überprüfung“"border="true":::

1. Ebenso können Sie im unteren Bereich des Formulars eine Sammlung auswählen, wodurch alle überprüften Ressourcen in die Sammlung aufgenommen werden.
Beachten Sie, dass die hier aufgeführten Sammlungen auf untergeordnete Sammlungen der Datenquellensammlung beschränkt sind.

    :::image type="content" source="./media/how-to-create-and-manage-collections/scan-under-collection.png" alt-text="Screenshot: Neues Überprüfungsfenster mit hervorgehobener Dropdownliste für die Sammlung"border="true":::

1. Zurück im Sammlungsfenster werden die Datenquellen angezeigt, die mit der Sammlung auf der Quellenkarte verknüpft sind.

    :::image type="content" source="./media/how-to-create-and-manage-collections/source-under-collection.png" alt-text="Screenshot: Purview Studio-Datenzuordnungsfenster mit hervorgehobener Karte der neu hinzugefügten Quelle in der Zuordnung"border="true":::

## <a name="add-assets-to-collections"></a>Hinzufügen von Ressourcen zu Sammlungen

Ressourcen und Quellen sind ebenfalls Sammlungen zugeordnet. Wenn während einer Überprüfung eine Sammlung zugeordnet war, werden die Ressourcen automatisch dieser Sammlung hinzugefügt, sie können aber auch manuell zu beliebigen untergeordneten Sammlungen hinzugefügt werden.

1. Überprüfen Sie die Sammlungsinformationen in den Ressourcendetails. Sie finden die Sammlungsinformationen im Abschnitt **Sammlungspfad** im oberen rechten Bereich der Seite mit den Ressourcendetails.

    :::image type="content" source="./media/how-to-create-and-manage-collections/collection-path.png" alt-text="Screenshot: Purview Studio-Ressourcenfenster mit hervorgehobenem Sammlungspfad" border="true":::

1. Berechtigungen auf der Seite mit Ressourcendetails:
    1. Überprüfen Sie das sammlungsbasierte Berechtigungsmodell, indem Sie den Anweisungen unter [Hinzufügen von Rollen und Einschränken des Zugriffs über Sammlungen](#add-roles-and-restrict-access-through-collections) weiter oben folgen.
    1. Wenn Sie keine Leseberechtigung für eine Sammlung besitzen, werden die Ressourcen unterhalb dieser Sammlung nicht in den Suchergebnissen aufgeführt. Wenn Sie über eine Direkt-URL für eine Ressource verfügen und diese URL öffnen, wird die Seite „Kein Zugriff“ angezeigt. Wenden Sie sich in diesem Fall an Ihren Purview-Administrator, um Zugriff zu erhalten. Sie können auf die Schaltfläche **Aktualisieren** klicken, um die Berechtigung erneut zu überprüfen.

        :::image type="content" source="./media/how-to-create-and-manage-collections/no-access.png" alt-text="Screenshot: Purview Studio-Ressourcenfenster, in dem der Benutzer über keine Berechtigungen verfügt und keinen Zugriff auf Informationen oder Optionen hat" border="true":::

    1. Wenn Sie Leseberechtigungen, aber keine Schreibberechtigungen für eine Sammlung besitzen, können Sie die Seite mit den Ressourcendetails durchsuchen. Die folgenden Vorgänge sind jedoch deaktiviert:
        * Bearbeiten der Ressource. Die Schaltfläche **Bearbeiten** ist deaktiviert.
        * Löschen der Ressource. Die Schaltfläche **Löschen** ist deaktiviert.
        * Verschieben der Ressource in eine andere Sammlung. Die Schaltfläche mit den Auslassungspunkten in der rechten oberen Ecke des Abschnitts „Sammlungspfad“ wird ausgeblendet.
    1. Die Berechtigungen wirken sich ebenfalls auf Ressourcen im Abschnitt **Hierarchie** aus. Ressourcen ohne Leseberechtigung werden abgeblendet dargestellt.

        :::image type="content" source="./media/how-to-create-and-manage-collections/hierarchy-permissions.png" alt-text="Screenshot: Purview Studio-Hierarchiefenster, in dem der Benutzer nur über Leseberechtigungen verfügt und keinen Zugriff auf Optionen hat" border="true":::

### <a name="move-asset-to-another-collection"></a>Verschieben einer Ressource in eine andere Sammlung

1. Klicken Sie auf die Schaltfläche mit den Auslassungspunkten in der rechten oberen Ecke des Abschnitts „Sammlungspfad“.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-asset.png" alt-text="Screenshot: Purview Studio-Ressourcenfenster mit hervorgehobener Option „Sammlungspfad“ und ausgewählter Schaltfläche mit Auslassungspunkten neben „Sammlungspfad“" border="true":::

1. Klicken Sie auf die Schaltfläche **In andere Sammlung verschieben**.
1. Wählen Sie im rechten Bereich die Zielsammlung für die Verschiebung aus. Beachten Sie, dass nur die Sammlungen angezeigt werden, für die Sie über Schreibberechtigungen verfügen. Die Ressource kann auch nur den untergeordneten Sammlungen der Datenquellensammlung hinzugefügt werden.

    :::image type="content" source="./media/how-to-create-and-manage-collections/move-select-collection.png" alt-text="Screenshot: Purview Studio-Popupfenster mit hervorgehobener Dropdownliste zur Auswahl einer Sammlung" border="true":::

1. Klicken Sie im unteren Fensterbereich auf die Schaltfläche **Verschieben**, um die Ressource zu verschieben.

## <a name="search-and-browse-by-collections"></a>Suchen und Durchsuchen nach Sammlung

### <a name="search-by-collection"></a>Suchen nach Sammlung

1. In Azure Purview befindet sich die Suchleiste oben in der Purview Studio-Benutzeroberfläche.

   :::image type="content" source="./media/how-to-create-and-manage-collections/purview-search-bar.png" alt-text="Screenshot der Position der Suchleiste in Azure Purview" border="true":::

1. Wenn Sie in die Suchleiste klicken, werden der aktuelle Suchverlauf und die zuletzt verwendeten Ressourcen angezeigt. Klicken Sie auf **Alle anzeigen**, um alle zuletzt angezeigten Ressourcen anzuzeigen.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-no-keywords.png" alt-text="Screenshot der Suchleiste vor Eingabe eines Schlüsselworts" border="true":::

1. Geben Sie in die Suchleiste Schlüsselwörter zur Identifikation der gewünschten Ressource ein, wie z. B. Name, Datentyp, Klassifizierung und Glossarbegriffe. Während der Eingabe von Schlüsselwörtern zur gesuchten Ressource zeigt Azure Purview Vorschläge zu Suchbegriffen sowie mögliche Ressourcenübereinstimmungen an. Klicken Sie zum Starten der Suche auf **Suchergebnisse anzeigen**, oder drücken Sie die **EINGABETASTE**.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-keywords.png" alt-text="Screenshot der Suchleiste während der Eingabe von Schlüsselwörtern durch den Benutzer" border="true":::

1. Auf der Seite der Suchergebnisse wird in der Reihenfolge ihrer Relevanz eine Liste der Ressourcen angezeigt, die mit den Schlüsselwörtern übereinstimmen. Die Relevanzbewertung einer Ressource wird von unterschiedlichen Faktoren beeinflusst. Sie können die Liste weiter einschränken, indem Sie bestimmte Sammlungen, Datenspeicher, Klassifizierungen, Kontakte, Bezeichnungen und Glossarbegriffe auswählen, die auf die gesuchte Ressource zutreffen.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-results.png" alt-text="Screenshot der Suchergebnisse" border="true":::

1. Klicken Sie auf die gewünschte Ressource, um die Seite mit den Ressourcendetails anzuzeigen, darunter z. B. Eigenschaften wie Schema, Herkunft und Ressourcenbesitzer.

   :::image type="content" source="./media/how-to-create-and-manage-collections/search-by-collection.png" alt-text="Screenshot: Suchergebnisse mit Sammlungen" border="true":::

### <a name="browse-by-collection"></a>Durchsuchen nach Sammlung

1. Sie können Datenressourcen durchsuchen, indem Sie auf der Startseite die Option **Ressourcen durchsuchen** auswählen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/browse-by-collection.png" alt-text="Screenshot: Purview Studio-Katalogfenster mit hervorgehobener Schaltfläche „Ressourcen durchsuchen“" border="true":::

1. Wählen Sie auf der Seite „Ressourcen durchsuchen“ auf **Nach Sammlung**. Die Sammlungen werden in einer hierarchischer Tabellenansicht aufgelistet. Wählen Sie den entsprechenden Sammlungsnamen aus, um die Ressourcen in jeder Sammlung weiter zu erkunden.

    :::image type="content" source="./media/how-to-create-and-manage-collections/by-collection-view.png" alt-text="Screenshot: Purview Studio-Ressourcenfenster mit ausgewählter Registerkarte „Nach Sammlung“"border="true":::

1. Auf der nächsten Seite werden die Suchergebnisse für die Ressourcen unterhalb der ausgewählten Sammlung angezeigt. Sie können die Ergebnisse durch Auswahl der Facettenfilter weiter eingrenzen. Alternativ können Sie die Ressourcen unterhalb weiterer Sammlungen anzeigen, indem Sie Namen der untergeordneten/verknüpften Sammlungen auswählen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/search-results-by-collection.png" alt-text="Screenshot: Purview Studio-Katalogfenster mit ausgewählter Registerkarte „Nach Sammlung“"border="true":::

1. Um die Details einer Ressource anzuzeigen, wählen Sie den Ressourcennamen im Suchergebnis aus. Sie können die Ressourcen auch überprüfen und sie per Massenbearbeitung ändern.

    :::image type="content" source="./media/how-to-create-and-manage-collections/view-asset-details.png" alt-text="Screenshot: Purview Studio-Katalogfenster mit ausgewählter Registerkarte „Nach Sammlung“ und hervorgehobenen Kontrollkästchen für die Ressource"border="true":::

## <a name="legacy-collection-guide"></a>Leitfaden zu Legacysammlungen

> [!NOTE]
> Dieser Leitfaden für Legacysammlungen gilt nur für Purview-Instanzen, die vor dem 18. August 2021 erstellt wurden. Für Instanzen, die nach diesem Zeitpunkt erstellt wurden, sollten Sie dem obigen Leitfaden folgen.

Legacysammlungen ordnen lediglich Quellen in der Datenzuordnung an und verwalten keine Berechtigungen für diese Quellen.

### <a name="create-a-legacy-collection"></a>Erstellen einer Legacysammlung

1. Wählen Sie im linken Bereich „Data Map“ aus, um die Datenzuordnung zu öffnen. Mithilfe der Zuordnungsansicht können Sie Ihre Sammlungen und die darunter aufgeführten Quellen anzeigen.

    :::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-view.png" alt-text="Screenshot: Purview Studio-Fenster mit geöffneter Option „Data Map“" border="true":::

1. Wählen Sie **+ Neue Sammlung** aus.

    :::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-create.png" alt-text="Screenshot: Purview Studio-Fenster mit geöffneter Option „Data Map“ und hervorgehobener Option „+ Neue Sammlung“" border="true":::

1. Geben Sie Ihrer Sammlung einen Namen, und wählen Sie ein übergeordnetes Element oder „Keine“ aus. Klicken Sie auf **Erstellen**. Die Sammlungsinformationen werden in der Datenzuordnung widergespiegelt.

    :::image type="content" source="./media/how-to-create-and-manage-collections/legacy-collection-name.png" alt-text="Screenshot: Purview Studio mit dem Fenster für die neue Sammlung" border="true":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun über eine Sammlung verfügen, können Sie mithilfe der folgenden Leitfäden Ressourcen hinzufügen und überprüfen.

* [Datenquellen verwalten](manage-data-sources.md)

* [Unterstützte Datenquellen](purview-connector-overview.md)

* [Überprüfung und Erfassung](concept-scans-and-ingestion.md)
