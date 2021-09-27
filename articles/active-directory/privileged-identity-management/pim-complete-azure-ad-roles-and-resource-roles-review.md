---
title: 'Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM: Azure AD | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie eine Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in Privileged Identity Management (PIM) in Azure Active Directory abschließen.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 9/3/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 614f45e4d83ff815bae0020b354b1b10d2812452
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452531"
---
# <a name="complete-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM

Nachdem eine [Zugriffsüberprüfung gestartet wurde](pim-create-azure-ad-roles-and-resource-roles-review.md), können Administratoren für privilegierte Rollen den privilegierten Zugriff überprüfen. Privileged Identity Management (PIM) in Azure Active Directory (Azure AD) sendet automatisch eine E-Mail, in der Benutzer zur Überprüfung ihres Zugriffs aufgefordert werden. Wenn ein Benutzer diese E-Mail nicht erhalten hat, können Sie ihm die Anweisungen unter [Ausführen einer Zugriffsüberprüfung](pim-perform-azure-ad-roles-and-resource-roles-review.md) zusenden.

Führen Sie nach dem Erstellen der Überprüfung die Schritte in diesem Artikel aus, um die Überprüfung abzuschließen und die Ergebnisse anzuzeigen.

## <a name="complete-access-reviews"></a>Durchführen von Zugriffsüberprüfungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an. Navigieren Sie unter **Azure-Ressourcen** zu **Privileged Identity Management**, und wählen Sie auf dem Dashboard unter **Verwalten** die Option **Azure-Ressourcen** aus. Wählen Sie auf dem gleichen Dashboard unter **Azure AD-Rollen** die Option **Azure AD-Rollen** aus.

2. Wählen Sie unter **Azure-Ressourcen** im Bereich **Azure-Ressourcen** Ihre Ressource und dann auf dem Dashboard die Option **Zugriffsüberprüfungen** aus. Navigieren Sie unter **Azure-Ressourcen** auf dem Dashboard direkt zu **Zugriffsüberprüfungen**.

3. Wählen Sie die Zugriffsüberprüfung aus, die Sie verwalten möchten. Nachfolgend sehen Sie einen Beispielscreenshot der Übersicht für **Zugriffsüberprüfungen** für **Azure-Ressourcen** und **Azure AD-Rollen**:

    :::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png" alt-text="Screenshot: Liste „Zugriffsüberprüfungen“ mit Rolle, Besitzer, Startdatum, Enddatum und Status" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-azure-ad-roles-home-list.png":::

Auf der Detailseite stehen die folgenden Optionen zum Verwalten der Überprüfung von **Azure-Ressourcen** und **Azure AD-Rollen** zur Verfügung:

![Screenshot: Optionen zum Verwalten einer Überprüfung unter „Azure-Ressourcen“: Beenden, Zurücksetzen, Anwenden, Löschen](media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-menu.png)

### <a name="stop-an-access-review"></a>Beenden einer Zugriffsüberprüfung

Alle Zugriffsüberprüfungen weisen ein Enddatum auf, Sie können jedoch die Schaltfläche **Beenden** verwenden, um sie zu einem frühen Zeitpunkt abzuschließen. Die Schaltfläche **Beenden** kann nur ausgewählt werden, wenn die Überprüfungsinstanz aktiv ist. Eine Überprüfung kann nicht neu gestartet werden, nachdem sie beendet wurde.

### <a name="reset-an-access-review"></a>Zurücksetzen einer Zugriffsüberprüfung

Wenn die Überprüfungsinstanz aktiv ist und mindestens eine Entscheidung von Prüfern getroffen wurde, können Sie die Zugriffsüberprüfung zurücksetzen, indem Sie die Schaltfläche **Zurücksetzen** auswählen, um alle dafür getroffenen Entscheidungen zu entfernen. Nachdem Sie eine Zugriffsüberprüfung zurückgesetzt haben, werden alle Benutzer als „Nicht überprüft“ gekennzeichnet.

### <a name="apply-an-access-review"></a>Anwenden einer Zugriffsüberprüfung

Nachdem eine Überprüfung abgeschlossen wurde, weil das Enddatum erreicht ist oder weil sie manuell beendet wurde, wird mit der Schaltfläche **Anwenden** der Zugriff auf die Rolle des verweigerten Benutzers entfernt. Wenn bei der Überprüfung der Zugriff eines Benutzers verweigert wurde, wird mit diesem Schritt die Rollenzuweisung entfernt. Wird beim Erstellen der Überprüfung die Einstellung **Automatisch anwenden** konfiguriert, ist diese Schaltfläche immer deaktiviert, da die Überprüfung automatisch und nicht manuell angewendet wird.

### <a name="delete-an-access-review"></a>Löschen einer Zugriffsüberprüfung

Wenn Sie an einer Überprüfung nicht weiter interessiert sind, löschen Sie sie. Wenn Sie die Zugriffsüberprüfung aus dem Privileged Identity Management-Dienst entfernen möchten, wählen Sie die Schaltfläche **Löschen** aus.

> [!IMPORTANT]
> Sie müssen diese destruktive Änderung nicht bestätigen, also überprüfen Sie, ob Sie diese Überprüfung löschen möchten.

## <a name="results"></a>Ergebnisse

Auf der Seite **Ergebnisse** können Sie eine Liste mit Ihren Ergebnissen der Überprüfung anzeigen und herunterladen.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png" alt-text="Screenshot: Seite „Ergebnisse“ mit „Benutzer“, „Ergebnis“, „Grund“, „Überprüft von“, „Angewendet von“ und „Ergebnis anwenden“ für Azure AD-Rollen" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-results.png":::

> [!Note]
> **Azure AD-Rollen** verfügen über ein Konzept von Gruppen, denen Rollen zugewiesen werden können, wobei der Rolle eine Gruppe zugewiesen werden kann. In diesem Fall wird die Gruppe in der Überprüfung angezeigt, und es werden nicht die Mitglieder der Gruppe erweitert. Ein Prüfer genehmigt oder verweigert die gesamte Gruppe.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png" alt-text="Screenshot: Seite „Ergebnisse“ mit „Benutzer“, „Ergebnis“, „Grund“, „Überprüft von“, „Angewendet von“ und „Ergebnis anwenden“ für Azure-Ressourcenrollen" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-results.png":::

> [!Note]
>Wird eine Gruppe **Azure-Ressourcenrollen** zugewiesen, wird dem Prüfer der Azure-Ressourcenrolle die erweiterte Liste der Benutzer in einer geschachtelten Gruppe angezeigt. Wenn ein Prüfer ein Mitglied einer geschachtelten Gruppe verweigert, wird dieses Verweigerungsergebnis nicht erfolgreich angewendet, da der Benutzer nicht aus der geschachtelten Gruppe entfernt wird.

## <a name="reviewers"></a>Reviewer

Auf der Seite **Prüfer** können Sie Prüfer für Ihre vorhandene Zugriffsüberprüfung anzeigen und hinzufügen. Sie können hier Prüfer auch daran erinnern, ihre Überprüfungen abzuschließen.

> [!Note]
> Wenn der ausgewählte Prüfertyp ein Benutzer oder eine Gruppe ist, können Sie jederzeit weitere Benutzer oder Gruppen als primäre Prüfer hinzufügen. Sie können primäre Prüfer auch jederzeit entfernen. Wenn der Prüfertyp „Vorgesetzter“ lautet, können Sie Benutzer oder Gruppen als Fallbackprüfer hinzufügen, um Überprüfungen für Benutzer ohne Vorgesetzte durchzuführen. Fallbackprüfer können nicht entfernt werden.

:::image type="content" source="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png" alt-text="Screenshot: Seite „Prüfer“ mit dem Namen und dem Benutzerprinzipalname für Azure-Ressourcenrollen" lightbox="media/pim-complete-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-resource-reviewers.png":::

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [Durchführen einer Zugriffsüberprüfung für Azure-Ressourcenrollen und Azure AD-Rollen in PIM](pim-perform-azure-ad-roles-and-resource-roles-review.md)
