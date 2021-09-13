---
title: 'Ändern der Anforderungseinstellungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung: Azure Active Directory'
description: Erfahren Sie, wie Sie die Anforderungseinstellungen für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung ändern.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 07/01/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fcd11f88cb25ef3afb199b147856d0a0140e44c
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113491981"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändern der Anforderungseinstellungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

Als Zugriffspaket-Manager können Sie jederzeit die Benutzer ändern, die ein Zugriffspaket anfordern können, indem Sie die Richtlinie bearbeiten oder eine neue Richtlinie hinzufügen. In diesem Artikel wird beschrieben, wie die Anforderungseinstellungen für ein vorhandenes Zugriffspaket geändert werden.

## <a name="choose-between-one-or-multiple-policies"></a>Wählen zwischen einer oder mehreren Richtlinien

Mit einer Richtlinie legen Sie fest, wer ein Zugriffspaket anfordern kann. Bevor Sie eine neue Richtlinie erstellen oder eine bestehende Richtlinie in einem Zugriffspaket bearbeiten, müssen Sie feststellen, wie viele Richtlinien das Zugriffspaket benötigt. 

Beim Erstellen eines Zugriffspakets geben Sie die Anforderungseinstellung an, die eine Richtlinie erstellt. Die meisten Zugriffspakete enthalten eine einzige Richtlinie, aber ein einzelnes Zugriffspaket kann über mehrere Richtlinien verfügen. Sie erstellen mehrere Richtlinien für ein Zugriffspaket, wenn Sie unterschiedlichen Benutzergruppen Zuweisungen mit unterschiedlichen Anforderungs- und Genehmigungseinstellungen erteilen möchten. 

Beispielsweise ist es mit einer einzelnen Richtlinie nicht möglich, einem Zugriffspaket interne und externe Benutzer zuzuweisen. Erstellen Sie hierfür in einem Zugriffspaket zwei separate Richtlinien für interne bzw. externe Benutzer. Gelten für einen Benutzer mehrere Richtlinien, wird er beim Anfordern dazu aufgefordert, die Richtlinie auszuwählen, der er zugewiesen werden möchte. Im folgenden Diagramm ist ein Zugriffspaket mit zwei Richtlinien dargestellt.

![Mehrere Richtlinien in einem Zugriffspaket](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Wie viele Richtlinien werden benötigt?

| Szenario | Anzahl von Richtlinien |
| --- | --- |
| Für alle Benutzer in meinem Verzeichnis sollen die gleichen Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket gelten. | Eine |
| Alle Benutzer in bestimmten verbundenen Organisationen sollen ein Zugriffspaket anfordern können. | Eine |
| Sowohl die Benutzer in meinem Verzeichnis als auch Benutzer außerhalb meines Verzeichnisses sollen ein Zugriffspaket anfordern können. | Mehrere |
| Ich möchte für einige Benutzer andere Genehmigungseinstellungen angeben. | Mehrere |
| Ich möchte, dass die Zugriffspaketzuweisungen einiger Benutzer ablaufen, während andere Benutzer ihren Zugriff verlängern können. | Mehrere |

Informationen zur Prioritätslogik, die beim Anwenden mehrerer Richtlinien verwendet wird, finden Sie unter [Mehrere Richtlinien](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Öffnen eines vorhandenen Zugriffspaket und Hinzufügen einer neue Richtlinie für Anforderungseinstellungen

Wenn Sie über eine Gruppe von Benutzern verfügen, für die unterschiedliche Anforderungs- und Genehmigungseinstellungen gelten sollen, müssen Sie wahrscheinlich eine neue Richtlinie erstellen. Führen Sie die folgenden Schritte aus, um einem vorhandenen Zugriffspaket eine neue Richtlinie hinzuzufügen:

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Richtlinien** und dann auf **Richtlinie hinzufügen**.

1. Sie beginnen auf der Registerkarte **Grundlagen**. Geben Sie einen Namen und eine Beschreibung für die Richtlinie ein.

    ![Erstellen der Richtlinie mit Name und Beschreibung](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Klicken Sie auf **Weiter**, um die Registerkarte **Anforderungen** zu öffnen.

1. Ändern Sie die Einstellung **Benutzer, die Zugriff anfordern können**. Befolgen Sie die Schritte in den folgenden Abschnitten, um die Einstellung in eine der folgenden Optionen zu ändern: 
    - [Für Benutzer in Ihrem Verzeichnis](#for-users-in-your-directory) 
    - [Für Benutzer, die sich nicht in Ihrem Verzeichnis befinden](#for-users-not-in-your-directory)
    - [Keine (nur direkte Administratorzuweisungen)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>Für Benutzer in Ihrem Verzeichnis

Gehen Sie folgendermaßen vor, wenn Sie möchten, dass Benutzer in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. Beim Definieren der Anforderungsrichtlinie können Sie einzelne Benutzer oder Gruppen von Benutzern angeben. Beispielsweise verfügt Ihre Organisation möglicherweise bereits über eine Gruppe wie **Alle Mitarbeiter**.  Wenn diese Gruppe in die Richtlinie für Benutzer eingefügt wird, die Zugriff anfordern können, können alle Mitglieder dieser Gruppe Zugriff anfordern.

1. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für in Ihrem Verzeichnis befindliche Benutzer**.

    Wenn Sie diese Option auswählen, werden neue Optionen angezeigt, um weiter zu verfeinern, wer in Ihrem Verzeichnis dieses Zugriffspaket anfordern kann.

    ![Zugriffspaket – Anforderungen – Für in Ihrem Verzeichnis befindliche Benutzer](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Wählen Sie eine der folgenden Optionen aus:

    |  | BESCHREIBUNG |
    | --- | --- |
    | **Bestimmte Benutzer und Gruppen** | Wählen Sie diese Option aus, wenn Sie möchten, dass nur die von Ihnen angegebenen Benutzer und Gruppen in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. |
    | **Alle Mitglieder (keine Gäste)** | Wählen Sie diese Option aus, wenn Sie möchten, dass alle Mitgliedsbenutzer in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. Diese Option umfasst keine Gastbenutzer, die Sie möglicherweise in Ihr Verzeichnis eingeladen haben. |
    | **Alle Benutzer (einschließlich Gästen)** | Wählen Sie diese Option aus, wenn Sie möchten, dass alle Mitgliedsbenutzer und Gastbenutzer in Ihrem Verzeichnis dieses Zugriffspaket anfordern können sollen. |

    Gastbenutzer verweisen auf externe Benutzer, die mit [Azure AD B2B-](../external-identities/what-is-b2b.md) in Ihr Verzeichnis eingeladen wurden. Weitere Informationen zu den Unterschieden zwischen Mitglieds- und Gastbenutzern finden Sie unter [Welche Standardbenutzerberechtigungen gibt es in Azure Active Directory?](../fundamentals/users-default-permissions.md).

1. Wenn Sie **Bestimmte Benutzer und Gruppen** ausgewählt haben, klicken Sie auf **Benutzer und Gruppen hinzufügen**.

1. Wählen Sie im Bereich „Benutzer und Gruppen auswählen“ die Benutzer und Gruppen aus, die Sie hinzufügen möchten.

    ![Zugriffspaket – Anforderungen – Benutzer und Gruppen auswählen](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Klicken Sie auf **Auswählen**, um die Benutzer und Gruppen hinzuzufügen.

1. Wenn Sie eine Genehmigung anfordern möchten, folgen Sie den Schritten unter [Ändern der Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung](entitlement-management-access-package-approval-policy.md), um die Genehmigungseinstellungen zu konfigurieren.

1. Wechseln Sie zum Abschnitt [Anforderungen aktivieren](#enable-requests).
 
## <a name="for-users-not-in-your-directory"></a>Für Benutzer, die sich nicht in Ihrem Verzeichnis befinden

 **Benutzer, die sich nicht in Ihrem Verzeichnis befinden** bezieht sich auf Benutzer in einem anderen Azure AD-Verzeichnis bzw. einer anderen Domäne. Diese Benutzer wurden möglicherweise noch nicht zu Ihrem Verzeichnis eingeladen. Azure AD-Verzeichnisse müssen in den **Einschränkungen bei der Zusammenarbeit** so konfiguriert werden, dass sie Einladungen zulassen. Weitere Informationen finden Sie unter [Aktivieren der externen B2B-Zusammenarbeit und Steuern, wer Gäste einladen kann](../external-identities/delegate-invitations.md).

> [!NOTE]
> Für einen sich noch nicht in Ihrem Verzeichnis befindenden Benutzer, dessen Anforderung genehmigt oder automatisch genehmigt wird, wird ein Gastbenutzerkonto erstellt. Der Gast wird eingeladen, erhält jedoch keine Einladungs-E-Mail. Stattdessen erhält er eine E-Mail, wenn seine Zugriffspaketzuweisung bereitgestellt wird. Wenn dieser Gastbenutzer zu einem späteren Zeitpunkt keine Zugriffspaketzuweisungen mehr besitzt, weil die letzte Zuweisung abgelaufen ist oder abgebrochen wurde, wird das Gastbenutzerkonto standardmäßig für die Anmeldung blockiert und anschließend gelöscht. Wenn Gastbenutzer dauerhaft in Ihrem Verzeichnis bleiben sollen, auch wenn sie keine Zugriffspaketzuweisungen haben, können Sie die Einstellungen für Ihre Berechtigungsverwaltungskonfiguration ändern. Weitere Informationen zum Gastbenutzerobjekt finden Sie unter [Eigenschaften eines Azure Active Directory B2B-Zusammenarbeitsbenutzers](../external-identities/user-properties.md).

Gehen Sie folgendermaßen vor, wenn Sie Benutzern, die sich nicht in Ihrem Verzeichnis befinden, die Möglichkeit geben möchten, dieses Zugriffspaket anzufordern:

1. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Für nicht in Ihrem Verzeichnis befindliche Benutzer**.

    Wenn Sie diese Option auswählen, werden neue Optionen angezeigt.

    ![Zugriffspaket – Anforderungen – Für nicht in Ihrem Verzeichnis befindliche Benutzer](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Wählen Sie eine der folgenden Optionen aus:

    |  | BESCHREIBUNG |
    | --- | --- |
    | **Bestimmte verbundene Organisationen** | Wählen Sie diese Option aus, wenn Sie aus einer Liste mit Organisationen auswählen möchten, die Ihr Administrator zuvor hinzugefügt hat. Alle Benutzer aus den ausgewählten Organisationen können dieses Zugriffspaket anfordern. |
    | **Alle konfigurierten verbundenen Organisationen** | Wählen Sie diese Option aus, wenn alle Benutzer aus allen Ihren konfigurierten verbundenen Organisationen dieses Zugriffspaket anfordern können. Nur Benutzer aus konfigurierten verbundenen Organisationen können Zugriffspakete anfordern, die Benutzern aus allen konfigurierten Organisationen angezeigt werden. |
    | **Alle Benutzer (alle verbundenen Organisationen und alle neuen externen Benutzer)** | Wählen Sie diese Option aus, wenn beliebige Benutzer im Internet die Möglichkeit haben sollen, dieses Zugriffspaket anzufordern.  Falls sie nicht zu einer verbundenen Organisation in Ihrem Verzeichnis gehören, wird für sie automatisch eine verbundene Organisation erstellt, wenn Sie das Paket anfordern. Die automatisch erstellte verbundene Organisation befindet sich im Zustand **Vorgeschlagen**. Weitere Informationen zum Zustand „Vorgeschlagen“ finden Sie unter [Zustandseigenschaften verbundener Organisationen](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    Eine verbundene Organisation ist ein externes Azure AD-Verzeichnis bzw. eine externe Domäne, mit der eine Beziehung besteht.

1. Wenn Sie **Bestimmte verbundene Organisationen**  ausgewählt haben, klicken Sie auf **Verzeichnisse hinzufügen**, um aus einer Liste der verbundenen Organisationen auszuwählen, die Ihr Administrator zuvor hinzugefügt hat.

1. Geben Sie den Namen oder Domänennamen ein, um nach einer zuvor verbundenen Organisation zu suchen.

    ![Zugriffspaket – Anforderungen – Verzeichnisse auswählen](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Wenn die Organisation, mit der Sie zusammenarbeiten möchten, nicht in der Liste enthalten ist, können Sie Ihren Administrator bitten, sie als verbundene Organisation hinzuzufügen. Weitere Informationen finden Sie unter [Hinzufügen einer verbundenen Organisation](entitlement-management-organization.md).

1. Nachdem Sie alle Ihre verbundenen Organisationen ausgewählt haben, klicken Sie auf **Auswählen**.

    > [!NOTE]
    > Alle Benutzer aus den ausgewählten verbundenen Organisationen werden dieses Zugriffspaket anfordern können. Dies schließt in Azure AD auch Benutzer aller Unterdomänen ein, die der Organisation zugeordnet sind, sofern diese Domänen nicht über die Positiv- oder Sperrliste von Azure B2B blockiert werden. Weitere Informationen finden Sie unter [Zulassen oder Blockieren von Einladungen für B2B-Benutzer von bestimmten Organisationen](../external-identities/allow-deny-list.md).

1. Wenn Sie eine Genehmigung anfordern möchten, folgen Sie den Schritten unter [Ändern der Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung](entitlement-management-access-package-approval-policy.md), um die Genehmigungseinstellungen zu konfigurieren.
 
1. Wechseln Sie zum Abschnitt [Anforderungen aktivieren](#enable-requests).

## <a name="none-administrator-direct-assignments-only"></a>Keine (nur direkte Administratorzuweisungen)

Gehen Sie folgendermaßen vor, wenn Sie Zugriffsanforderungen umgehen und Administratoren ermöglichen soll, bestimmte Benutzer direkt diesem Zugriffspaket zuzuweisen. Benutzer müssen das Zugriffspaket nicht anfordern. Sie können weiterhin Lebenszykluseinstellungen festlegen, aber es gibt keine Anforderungseinstellungen.

1. Klicken Sie im Abschnitt **Benutzer, die Zugriff anfordern können** auf **Keine (nur direkte Zuweisungen eines Administrators)** .

    ![Zugriffspaket – Anforderungen – Keine (nur direkte Administratorzuweisungen)](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    Nach der Erstellung des Zugriffspakets können Sie direkt bestimmte interne und externe Benutzer dem Zugriffspaket zuweisen. Wenn Sie einen externen Benutzer angeben, wird ein Gastbenutzerkonto in Ihrem Verzeichnis erstellt. Weitere Informationen zum direkten Zuweisen eines Benutzers finden Sie unter [Anzeigen, Hinzufügen und Entfernen von Zuweisungen für ein Zugriffspaket ](entitlement-management-access-package-assignments.md).

1. Fahren Sie mit dem Abschnitt [Anforderungen aktivieren](#enable-requests) fort.


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Öffnen und Bearbeiten einer vorhandenen Richtlinie mit Anforderungseinstellungen

Wenn Sie die Anforderungs- und Genehmigungseinstellungen für ein Zugriffspaket ändern möchten, müssen Sie die entsprechende Richtlinie öffnen. Führen Sie die folgenden Schritte aus, um die Anforderungseinstellungen für ein Zugriffspaket zu öffnen und zu bearbeiten:

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie auf **Richtlinien** und anschließend auf die Richtline, die Sie bearbeiten möchten.

    Der Bereich „Richtliniendetails“ wird unten auf der Seite geöffnet.

    ![Zugriffspaket: Bereich „Richtliniendetails“](./media/entitlement-management-shared/policy-details.png)

1. Klicken Sie auf **Bearbeiten**, um die Richtlinie zu bearbeiten.

    ![Zugriffspaket: Richtlinie bearbeiten](./media/entitlement-management-shared/policy-edit.png)

1. Klicken Sie auf die Registerkarte **Anforderungen**, um die Anforderungseinstellungen zu öffnen.

1. Führen Sie die Schritte in den vorherigen Abschnitten aus, um die Anforderungseinstellungen nach Bedarf zu ändern.

1. Wechseln Sie zum Abschnitt [Anforderungen aktivieren](#enable-requests).

## <a name="enable-requests"></a>Ermöglichen von Anforderungen

1. Wenn das Zugriffspaket Benutzern in der Anforderungsrichtlinie sofort zum Anfordern zur Verfügung gestellt werden soll, legen Sie den Umschalter auf **Ja** fest.

    Sie können sie in der Zukunft immer aktivieren, nachdem Sie das Erstellen des Zugriffspakets abgeschlossen haben.

    Wenn Sie **Keine (nur direkte Administratorzuweisungen)** ausgewählt haben und Sie die Aktivierung auf **Nein** festlegen, können Administratoren dieses Zugriffspaket nicht direkt zuweisen.

    ![Zugriffspaket – Richtlinie – Einstellung „Richtlinie aktivieren“](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Klicken Sie auf **Weiter**.

1. Wenn Anforderer beim Anfordern des Zugriffs auf ein Zugriffspaket weitere Informationen bereitstellen sollen, verwenden Sie die unter [Ändern Sie die Einstellungen zu Genehmigungs- und Anfordererinformationen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung.](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval) beschriebenen Schritte, um die Anfordererinformationen zu konfigurieren.

1. Konfigurieren Sie Lebenszykluseinstellungen.

1. Wenn Sie eine Richtlinie bearbeiten, klicken Sie auf **Aktualisieren**. Wenn Sie eine neue Richtlinie hinzufügen, klicken Sie auf **Erstellen**.

## <a name="prevent-requests-from-users-with-incompatible-access-preview"></a>Verhindern von Anforderungen von Benutzern mit inkompatiblem Zugriff (Vorschau)

Zusätzlich zu den Richtlinienüberprüfungen, die steuern, welche Benutzer Anforderungen übermitteln können, können Sie den Zugriff weiter einschränken, um zu verhindern, dass ein Benutzer, der bereits über gewisse Zugriffsberechtigungen (über eine Gruppe oder ein anderes Zugriffspaket) verfügt, übermäßigen Zugriff erhält.

Wenn Sie möchten, dass ein Benutzer kein Zugriffspaket anfordern kann, wenn er bereits einem anderen Zugriffspaket zugewiesen ist oder einer Gruppe angehört, verwenden Sie die unter [Konfigurieren von Aufgabentrennungsprüfungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung (Vorschau)](entitlement-management-access-package-incompatible.md) beschriebenen Schritte.

## <a name="next-steps"></a>Nächste Schritte

- [Ändern der Genehmigungseinstellungen für ein Zugriffspaket](entitlement-management-access-package-approval-policy.md)
- [Ändern der Lebenszykluseinstellungen für ein Zugriffspaket](entitlement-management-access-package-lifecycle-policy.md)
- [Anzeigen der Anforderungen für ein Zugriffspaket](entitlement-management-access-package-requests.md)
