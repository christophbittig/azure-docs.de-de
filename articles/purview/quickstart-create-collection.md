---
title: 'Schnellstart: Erstellen einer Sammlung'
description: Sammlungen werden für die Zugriffssteuerung und die Ressourcenorganisation in Azure Purview verwendet. In diesem Artikel wird beschrieben, wie Sie eine Sammlung erstellen und Berechtigungen hinzufügen, Quellen registrieren und Ressourcen für Sammlungen registrieren.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: template-quickstart
ms.openlocfilehash: 5dff616c6fa7ee8e72a932da281935f77db19010
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853698"
---
# <a name="quickstart-create-a-collection-and-assign-permissions-in-purview"></a>Schnellstart: Erstellen einer Sammlung und Zuweisen von Berechtigungen in Purview

Sammlungen sind das Tool von Azure Purview zum Verwalten des Besitzes und der Zugriffssteuerung für Ressourcen, Quellen und Informationen. Darüber hinaus werden damit Ihre Quellen und Ressourcen in benutzerdefinierten Kategorien strukturiert, um Ihre Verwaltungsoberfläche auf Ihre Daten abzustimmen. In diesem Leitfaden erfahren Sie, wie Sie Ihre erste Sammlung und einen Sammlungsadministrator einrichten, um die Purview-Umgebung für Ihre Organisation vorzubereiten.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Ihr eigener [Azure Active Directory-Mandant](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

## <a name="check-permissions"></a>Überprüfen Sie die Berechtigungen.

Zum Erstellen und Verwalten von Sammlungen in Purview muss Ihnen innerhalb von Purview die Rolle **Sammlungsadministrator** zugewiesen sein. Sie können diese Berechtigungen in [Purview Studio](use-purview-studio.md) überprüfen. Sie gelangen zu Purview Studio, indem Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Purview-Ressource wechseln und auf der Übersichtsseite die Kachel **Purview Studio öffnen** auswählen.

1. Wählen Sie im linken Bereich „Data Map“ > „Sammlungen“ aus, um die Seite für die Sammlungsverwaltung zu öffnen.

    :::image type="content" source="./media/quickstart-create-collection/find-collections.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und ausgewählter Registerkarte „Sammlungen“." border="true":::

1. Wählen Sie Ihre Stammsammlung aus. Dies ist die oberste Sammlung in Ihrer Sammlungsliste, die den gleichen Namen aufweist wie Ihre Purview-Ressource. In diesem Beispiel lautet der Name der Stammsammlung „Contoso Purview“.

    :::image type="content" source="./media/quickstart-create-collection/select-root-collection.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und hervorgehobener Stammsammlung" border="true":::

1. Wählen Sie im Sammlungsfenster „Rollenzuweisungen“ aus.

    :::image type="content" source="./media/quickstart-create-collection/role-assignments.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und hervorgehobener Registerkarte „Rollenzuweisungen“" border="true":::

1. Um eine Sammlung zu erstellen, müssen Sie in der Liste der Sammlungsadministratoren unterhalb von „Rollenzuweisungen“ enthalten sein. Wenn Sie die Purview-Ressource erstellt haben, sollten Sie bereits als Sammlungsadministrator in der Stammsammlung aufgeführt werden. Andernfalls müssen Sie sich an den Sammlungsadministrator wenden, damit Ihnen die entsprechende Berechtigung erteilt wird.

    :::image type="content" source="./media/quickstart-create-collection/collection-admins.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und hervorgehobenem Abschnitt „Sammlungsadministrator“" border="true":::

## <a name="create-a-collection-in-the-portal"></a>Erstellen einer Sammlung im Portal

Sie beginnen mit dem Erstellen der Sammlung in [Purview Studio](use-purview-studio.md). Sie gelangen zu Purview Studio, indem Sie im Azure-Portal zu Ihrer Purview-Ressource wechseln und auf der Übersichtsseite die Kachel **Purview Studio öffnen** auswählen.

1. Wählen Sie im linken Bereich „Data Map“ > „Sammlungen“ aus, um die Seite für die Sammlungsverwaltung zu öffnen.

    :::image type="content" source="./media/quickstart-create-collection/find-collections-2.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“ und ausgewählter Registerkarte „Sammlungen“" border="true":::

1. Wählen Sie **+ Sammlung hinzufügen** aus.

    :::image type="content" source="./media/quickstart-create-collection/select-add-collection.png" alt-text="Screenshot: Purview Studio mit geöffneter Option „Data Map“, ausgewählter Registerkarte „Sammlungen“ und Hervorhebung von „Sammlung hinzufügen“." border="true":::

1. Geben Sie im rechten Bereich den Sammlungsnamen und eine Beschreibung ein, und suchen Sie nach Benutzern, um sie als Sammlungsadministratoren hinzuzufügen.

    :::image type="content" source="./media/quickstart-create-collection/create-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster „Neue Sammlung“, mit ausgewähltem Anzeigenamen und Sammlungsadministrator und hervorgehobener Schaltfläche „Erstellen“" border="true":::

1. Klicken Sie auf **Erstellen**. Die Sammlungsinformationen werden auf der Seite widergespiegelt.

    :::image type="content" source="./media/quickstart-create-collection/created-collection.png" alt-text="Screenshot: Purview Studio mit dem Fenster für die neu erstellte Sammlung" border="true":::

## <a name="assign-permissions-to-collection"></a>Zuweisen von Berechtigungen zu einer Sammlung

Nachdem Sie nun über eine Sammlung verfügen, können Sie dieser Sammlung Berechtigungen zuweisen, um den Benutzerzugriff auf Purview zu verwalten.

### <a name="roles"></a>Rollen

Alle zugewiesenen Rollen gelten für Quellen, Ressourcen und andere Objekte innerhalb der Sammlung, auf die die Rolle angewendet wird.

* **Sammlungsadministratoren** können eine Sammlung und ihre Details bearbeiten, den Zugriff in der Sammlung verwalten und untergeordnete Sammlungen hinzufügen.
* **Datenquellenadministratoren** können Datenquellen und Datenüberprüfungen verwalten.
* **Datenkuratoren** können Aktionen für Katalogdatenobjekte erstellen, lesen, ändern und löschen.
* **Datenleser** können auf Katalogdatenobjekte zugreifen, diese aber nicht ändern.

### <a name="assign-permissions"></a>Zuweisen von Berechtigungen

1. Wählen Sie die Registerkarte **Rollenzuweisungen** aus, um alle Rollen in einer Sammlung anzuzeigen.

    :::image type="content" source="./media/quickstart-create-collection/select-role-assignments.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit hervorgehobener Registerkarte „Rollenzuweisungen“" border="true":::

1. Wählen Sie **Rollenzuweisungen bearbeiten** oder das Personensymbol aus, um die einzelnen Rollenmitglieder zu bearbeiten.

    :::image type="content" source="./media/quickstart-create-collection/edit-role-assignments.png" alt-text="Screenshot: Purview Studio-Sammlungsfenster mit ausgewählter Dropdownliste „Rollenzuweisungen bearbeiten“" border="true":::

1. Geben Sie einen Begriff in das Textfeld ein, um nach Benutzern zu suchen, die Sie als Rollenmitglied hinzufügen möchten. Wählen Sie **OK** aus, um die Änderung zu speichern.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun über eine Sammlung verfügen, können Sie mithilfe der folgenden Leitfäden Ressourcen hinzufügen sowie Ihre Sammlungen überprüfen und verwalten:

* [Registrieren einer Quelle für eine Sammlung](how-to-create-and-manage-collections.md#register-source-to-a-collection)
* [Hinzufügen von Rollen und Einschränken des Zugriffs über Sammlungen](how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)
