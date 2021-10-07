---
title: Hinzufügen oder Bearbeiten von Azure-Rollenzuweisungsbedingungen über das Azure-Portal (Vorschau) – Azure RBAC
description: Hier erfahren Sie, wie Sie Bedingungen der attributbasierten Zugriffssteuerung (Attribute-Based Access Control, ABAC) in Azure-Rollenzuweisungen über das Azure-Portal und unter Verwendung der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) hinzufügen, bearbeiten, anzeigen oder löschen.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: 7401b7369d766df5513100b30b7b65c324517fbe
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091983"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-the-azure-portal-preview"></a>Hinzufügen oder Bearbeiten von Azure-Rollenzuweisungsbedingungen über das Azure-Portal (Vorschau)

> [!IMPORTANT]
> Azure ABAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Eine [Azure-Rollenzuweisungsbedingung](conditions-overview.md) ist eine zusätzliche Überprüfung, die Sie Ihrer Rollenzuweisung optional hinzufügen können, um die Zugriffssteuerung präziser zu gestalten. Sie können z. B. eine Bedingung hinzufügen, mit der festgelegt wird, dass ein Objekt über ein bestimmtes Tag zum Lesen des Objekts verfügen muss. In diesem Artikel wird beschrieben, wie Sie über das Azure-Portal Bedingungen für Ihre Rollenzuweisungen hinzufügen, bearbeiten, anzeigen oder löschen.

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu den Voraussetzungen für das Hinzufügen oder Bearbeiten von Rollenzuweisungsbedingungen finden Sie unter [Voraussetzungen für Bedingungen](conditions-prerequisites.md).


## <a name="step-1-determine-the-condition-you-need"></a>Schritt 1: Bestimmen der erforderlichen Bedingung

Informationen zum Ermitteln der erforderlichen Bedingungen finden Sie in den [Beispielen für Azure-Rollenzuweisungsbedingungen](../storage/common/storage-auth-abac-examples.md).

Derzeit können Bedingungen zu integrierten oder benutzerdefinierten Rollenzuweisungen hinzugefügt werden, die über [Aktionen für Speicherblobdaten](../storage/common/storage-auth-abac-attributes.md) verfügen. Dies umfasst die folgenden integrierten Rollen:

- [Mitwirkender an Speicherblobdaten](built-in-roles.md#storage-blob-data-contributor)
- [Besitzer von Speicherblobdaten](built-in-roles.md#storage-blob-data-owner)
- [Leser von Speicherblobdaten](built-in-roles.md#storage-blob-data-reader)

## <a name="step-2-choose-how-to-add-condition"></a>Schritt 2: Auswählen der Methode zum Hinzufügen einer Bedingung

Es gibt zwei Möglichkeiten, um eine Bedingung hinzuzufügen. Sie können eine Bedingung beim Hinzufügen einer neuen Rollenzuweisung hinzufügen, oder Sie fügen einer vorhandenen Rollenzuweisung eine Bedingung hinzu.

### <a name="new-role-assignment"></a>Neue Rollenzuweisung

1. Befolgen Sie die Schritte zum [Zuweisen von Azure-Rollen über das Azure-Portal](role-assignments-portal.md).

1. Klicken Sie auf der Registerkarte **Conditions (optional)** (Bedingungen (optional)) auf **Bedingung hinzufügen**.

    Wenn die Registerkarte „Conditions (optional)“ (Bedingungen (optional)) nicht angezeigt wird, stellen Sie sicher, dass Sie eine Rolle ausgewählt haben, die Bedingungen unterstützt.

   ![Screenshot der Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Bedingung hinzufügen“ auf der Benutzeroberfläche der Vorschauversion.](./media/shared/condition.png)

    Die Seite „Bedingung für Rollenzuweisung hinzufügen“ wird angezeigt.

### <a name="existing-role-assignment"></a>Vorhandene Rollenzuweisung

1. Öffnen Sie im Azure-Portal die **Zugriffssteuerung (IAM)** im Bereich, in dem Sie eine Bedingung hinzufügen möchten. Beispielsweise können Sie ein Abonnement, eine Ressourcengruppe oder eine Ressource öffnen.

    Aktuell können Bedingungen für Verwaltungsgruppen nicht über das Azure-Portal hinzugefügt, angezeigt, bearbeitet oder gelöscht werden.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen für diesen Bereich anzuzeigen.

1. Suchen Sie nach einer Rollenzuweisung mit Speicherdatenaktionen, der Sie eine Bedingung hinzufügen möchten.

1. Klicken Sie in der Spalte **Bedingung** auf **Hinzufügen**.

    Wenn der Link „Hinzufügen“ nicht angezeigt wird, überprüfen Sie, ob der Bereich dem Bereich der Rollenzuweisung entspricht.

    ![Rollenzuweisungsliste mit der Spalte „Bedingung“.](./media/conditions-role-assignments-portal/condition-role-assignments-list.png)

    Die Seite „Bedingung für Rollenzuweisung hinzufügen“ wird angezeigt.

## <a name="step-3-review-basics"></a>Schritt 3: Überprüfen der grundlegenden Informationen

Nachdem die Seite zum Hinzufügen einer Rollenzuweisungsbedingung geöffnet wurde, können Sie die grundlegenden Informationen der Bedingung überprüfen. **Rolle** gibt die Rolle an, der die Bedingung hinzugefügt wird.

1. Übernehmen Sie für die Option **Editor-Typ** die Standardeinstellung **Visuelles Element**.

    Nachdem Sie eine Bedingung hinzugefügt haben, können Sie zwischen einem visuellen Element und Code wechseln.

1. (Optional) Wenn das Feld **Beschreibung** angezeigt wird, geben Sie eine Beschreibung ein.

    Abhängig von der gewählten Methode zum Hinzufügen einer Bedingung wird das Feld „Beschreibung“ möglicherweise nicht angezeigt. Eine Beschreibung ist nützlich, um mehr über eine Bedingung zu erfahren und sie sich besser einzuprägen.

    ![Seite zum Hinzufügen einer Rollenzuweisungsbedingung mit Anzeige des Editor-Typs und einer Beschreibung.](./media/conditions-role-assignments-portal/condition-basics.png)

## <a name="step-4-add-actions"></a>Schritt 4: Hinzufügen von Aktivitäten

1. Klicken Sie im Abschnitt **Aktion hinzufügen** auf **Aktion hinzufügen**.

    Die Seite „Aktion auswählen“ wird angezeigt. In diesem Bereich wird eine gefilterte Liste mit Datenaktionen angezeigt, die auf der Rollenzuweisung basiert, die als Ziel Ihrer Bedingung verwendet wird. Weitere Informationen finden Sie unter [Format und Syntax für Azure-Rollenzuweisungsbedingungen](conditions-format.md#actions).

    ![Wählen Sie einen Aktionsbereich für die Bedingung mit ausgewählter Aktion aus.](./media/conditions-role-assignments-portal/condition-actions-select.png)

1. Wählen Sie die Aktionen aus, die bei erfüllter Bedingung zugelassen werden sollen.

    Wenn Sie mehrere Aktionen für eine einzelne Bedingung auswählen, stehen für Ihre Bedingung möglicherweise weniger Attribute zur Auswahl, weil die Attribute für alle ausgewählten Aktionen verfügbar sein müssen.

1. Klicken Sie auf **Auswählen**.

    Die ausgewählten Aktionen werden in der Liste mit den Aktionen angezeigt.

## <a name="step-5-build-expressions"></a>Schritt 5: Erstellen von Ausdrücken

1. Klicken Sie im Abschnitt **Ausdruck erstellen** auf **Ausdruck hinzufügen**.

    Der Abschnitt „Ausdrücke“ wird erweitert.

1. Wählen Sie in der Liste „Attributquelle“ aus, wo sich das Attribut befindet.

    - **Ressource** gibt an, dass sich das Attribut in der Ressource (z. B. der Containername) befindet.
    - **Anforderung** gibt an, dass das Attribut Teil der Aktionsanforderung ist (z. B. das Festlegen des Blobindextags).

1. Wählen Sie in der Liste „Attribut“ ein Attribut für die linke Seite des Ausdrucks aus. Weitere Informationen finden Sie unter [Format und Syntax für Azure-Rollenzuweisungsbedingungen](conditions-format.md#attributes).

    Abhängig vom ausgewählten Attribut werden möglicherweise Felder hinzugefügt, um zusätzliche Attributdetails anzugeben.

1. Wählen Sie in der Liste „Operator“ einen Operator aus.

1. Geben Sie im Feld „Wert“ einen Wert für die rechte Seite des Ausdrucks ein.

    ![Abschnitt zum Erstellen eines Ausdrucks mit Werten für Blobindextags.](./media/shared/condition-expressions.png)

## <a name="step-6-review-and-add-condition"></a>Schritt 6: Überprüfen und Hinzufügen der Bedingung

1. Scrollen Sie nach oben zu **Editor-Typ**, und klicken Sie auf **Code**.

    Die Bedingung wird als Code angezeigt. In diesem Code-Editor können Sie Änderungen an der Bedingung vornehmen. Durch Klicken auf **Visuelles Element** können Sie zum visuellen Editor zurückkehren.

    ![Bedingung im Code-Editor mit ausgewählten Aktionen und hinzugefügtem Ausdruck.](./media/conditions-role-assignments-portal/condition-code.png)

1. Klicken Sie auf **Speichern**, um die Bedingung zur Rollenzuweisung hinzuzufügen.

## <a name="view-edit-or-delete-a-condition"></a>Anzeigen, Bearbeiten oder Löschen einer Bedingung

1. Öffnen Sie im Azure-Portal die **Zugriffssteuerung (IAM)** für die Rollenzuweisung mit der Bedingung, die Sie anzeigen, bearbeiten oder löschen möchten.

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um nach der Rollenzuweisung zu suchen.

1. Klicken Sie in der Spalte **Bedingung** auf **Anzeigen/Bearbeiten**.

    Wenn der Link zum Anzeigen/Bearbeiten nicht angezeigt wird, überprüfen Sie, ob der Bereich dem Bereich der Rollenzuweisung entspricht.

    ![Rollenzuweisungsliste mit dem Link zum Anzeigen/Bearbeiten für eine Bedingung.](./media/conditions-role-assignments-portal/condition-role-assignments-list-edit.png)

    Die Seite „Bedingung für Rollenzuweisung hinzufügen“ wird angezeigt.

1. Verwenden Sie den Editor, um die Bedingung anzuzeigen oder zu bearbeiten.

    ![Bedingung im Editor, nachdem Sie auf den Link zum Anzeigen/Bearbeiten geklickt haben.](./media/conditions-role-assignments-portal/condition-edit.png)

1. Klicken Sie abschließend auf **Speichern**. Zum Löschen der gesamten Bedingung klicken Sie auf **Bedingung löschen**. Durch das Löschen der Bedingung wird die Rollenzuweisung nicht entfernt.

## <a name="next-steps"></a>Nächste Schritte

- [Beispiele für Azure-Rollenzuweisungsbedingungen (Vorschau)](../storage/common/storage-auth-abac-examples.md)
- [Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs mit dem Azure-Portal (Vorschau)](../storage/common/storage-auth-abac-portal.md)
- [Problembehandlung: Bedingungen für die Azure-Rollenzuweisung (Vorschau)](conditions-troubleshoot.md)
