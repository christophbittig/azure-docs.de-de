---
title: Zuweisen eines Benutzers als Administrator eines Azure-Abonnements – Azure RBAC
description: Erfahren Sie, wie Sie einen Benutzer über das Azure-Portal und die rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) als Administrator für ein Azure-Abonnement festlegen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/25/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: af88edbb788afa3576d3317d4e1cd54dea6f4deb
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988065"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Zuweisen von Administratorzugriff für ein Azure-Abonnement

Um einen Benutzer zum Administrator eines Azure-Abonnements zu machen, weisen Sie ihm die Rolle [Besitzer](built-in-roles.md#owner) im Abonnementbereich zu. Durch die Rolle „Besitzer“ erhält der Benutzer Vollzugriff auf alle Ressourcen im Abonnement, einschließlich der Berechtigung, anderen Personen den Zugriff zu gewähren. Diese Schritte sind identisch mit allen anderen Rollenzuweisungen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>Schritt 1: Öffnen des Abonnements

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Suchen Sie im oberen Suchfeld nach Abonnements.

1. Klicken Sie auf das Abonnement, das Sie verwenden möchten.

    Die folgende Abbildung zeigt ein Beispielabonnement.

    ![Screenshot: Abonnementübersicht](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-page"></a>Schritt 2: Öffnen der Seite „Rollenzuweisung hinzufügen“

Die Zuweisung von Rollen zum Gewähren von Zugriff auf Azure-Ressourcen erfolgt in der Regel über die Seite **Zugriffssteuerung (IAM)** . Diese wird auch als Identity & Access Management (IAM) bezeichnet und wird an mehreren Stellen im Azure-Portal angezeigt.

1. Klicken Sie auf **Zugriffssteuerung (IAM)**.

    Die folgende Abbildung zeigt ein Beispiel der Seite „Zugriffssteuerung (IAM)“ für ein Abonnement:

    ![Screenshot: Seite „Zugriffssteuerung (IAM)“ für ein Abonnement](./media/shared/sub-access-control.png)

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** .

   Wenn Sie keine Berechtigungen zum Zuweisen von Rollen haben, ist die Option „Rollenzuweisung hinzufügen“ deaktiviert.

    ![Screenshot: Menü „Hinzufügen > Rollenzuweisung hinzufügen“ in der Benutzeroberfläche der Vorschauversion.](./media/shared/add-role-assignment-menu-preview.png)

    Die Seite „Rollenzuweisung hinzufügen“ wird geöffnet.

## <a name="step-3-select-the-owner-role"></a>Schritt 3: Auswählen der Rolle „Besitzer“

Über die Rolle [Besitzer](built-in-roles.md#owner) wird Vollzugriff zum Verwalten aller Ressourcen gewährt, einschließlich der Möglichkeit, Rollen in Azure RBAC zuzuweisen. Sie sollten höchstens 3 Abonnementbesitzer haben, um die Möglichkeit einer Sicherheitsverletzung durch einen kompromittierten Besitzer zu verringern.

1. Wählen Sie auf der Registerkarte **Rollen** die Rolle **Besitzer** aus.

    Sie können nach einer Rolle anhand des Namens oder der Beschreibung suchen. Sie können Rollen auch nach Typ und Kategorie filtern.

   ![Screenshot: Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Rollen“ auf der Benutzeroberfläche der Vorschauversion](./media/shared/roles.png)

1. Klicken Sie auf **Weiter**.

## <a name="step-4-select-who-needs-access"></a>Schritt 4: Auswahl zugriffsberechtigter Benutzer

1. Wählen Sie auf der Registerkarte **Mitglieder** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.

   ![Screenshot: Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Mitglieder hinzufügen“ in der Benutzeroberfläche der Vorschauversion.](./media/shared/members.png)

1. Klicken Sie auf **Mitglieder auswählen**.

1. Suchen Sie den Benutzer, und wählen Sie ihn aus.

    Sie können im Feld **Auswählen** einen Begriff eingeben, um das Verzeichnis nach Anzeigename oder E-Mail-Adresse zu durchsuchen.

   ![Screenshot: Bereich „Mitglieder auswählen“ auf der Benutzeroberfläche der Vorschauversion](./media/shared/select-members.png)

1. Klicken Sie auf **Speichern**, um den Benutzer der Liste „Mitglieder“ hinzuzufügen.

1. Geben Sie im Feld **Beschreibung** eine optionale Beschreibung für diese Rollenzuweisung ein.

    Sie können diese Beschreibung später in der Liste der Rollenzuweisungen anzeigen.

1. Klicken Sie auf **Weiter**.

## <a name="step-5-assign-role"></a>Schritt 5: Zuweisen einer Rolle

1. Überprüfen Sie auf der Registerkarte **Überprüfen und zuweisen** die Einstellungen für die Rollenzuweisung.

1. Klicken Sie auf **Überprüfen und zuweisen**, um die Rolle zuzuweisen.

   Kurz darauf wird dem Benutzer die Rolle „Besitzer“ für das Abonnement zugewiesen.

    ![Screenshot: Liste der Rollenzuweisungen nach dem Zuweisen einer Rolle in der Benutzeroberfläche der Vorschauversion.](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure-Rollen über das Azure-Portal](role-assignments-portal.md)
- [Auflisten von Azure-Rollenzuweisungen über das Azure-Portal](role-assignments-list-portal.md)
- [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md)
