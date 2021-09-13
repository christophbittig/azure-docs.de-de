---
title: Erneutes Verarbeiten von Zuweisungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung – Azure Active Directory
description: Hier erfahren Sie, wie Sie Zuweisungen für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung erneut verarbeiten.
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
ms.date: 06/25/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: b1b488f8c9331932b82ab0ab55db9c7dcb652add
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129332"
---
# <a name="reprocess-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Erneutes Verarbeiten von Zuweisungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

Als Zugriffspaket-Manager können Sie die ursprünglichen Zuweisungen von Benutzern in einem Zugriffspaket mithilfe der erneuten Verarbeitung automatisch neu auswerten und erzwingen. Durch die erneute Verarbeitung müssen Benutzer den Anforderungsprozess für Zugriffspakete nicht wiederholen, wenn ihr Zugriff auf Ressourcen durch Änderungen außerhalb der Berechtigungsverwaltung beeinträchtigt wurde.

Es kann beispielsweise vorkommen, dass ein Benutzer manuell aus einer Gruppe entfernt wurde und dadurch nicht mehr auf erforderliche Ressourcen zugreifen kann. 

Da externe Aktualisierungen der Zugriffspaketressourcen von der Berechtigungsverwaltung nicht blockiert werden, wird diese Änderung auf der Benutzeroberfläche der Berechtigungsverwaltung nicht korrekt angezeigt. Daher wird der Zuweisungsstatus des Benutzers als „Übermittelt“ angezeigt, auch wenn der Benutzer keinen Zugriff mehr auf die Ressourcen hat. Wenn die Zuweisung des Benutzers jedoch erneut verarbeitet wird, wird er wieder den Ressourcen des Zugriffspakets hinzugefügt. Durch die erneute Verarbeitung wird sichergestellt, dass die Zugriffspaketzuweisungen auf dem neuesten Stand sind, dass Benutzer Zugriff auf die erforderlichen Ressourcen haben und dass Zuweisungen auf der Benutzeroberfläche korrekt angegeben sind.

In diesem Artikel wird beschrieben, wie Zuweisungen in einem vorhandenen Zugriffspaket erneut verarbeitet werden.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Berechtigungsverwaltung zu verwenden und Benutzer zu Zugriffspaketen zuzuweisen, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-Lizenz

## <a name="open-an-existing-access-package-and-reprocess-user-assignments"></a>Öffnen eines vorhandenen Zugriffspakets und erneutes Verarbeiten von Benutzerzuweisungen

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaket-Manager oder Zugriffspaketzuweisungs-Manager

Wenn Sie über Benutzer verfügen, die sich im Zustand „Übermittelt“ befinden, aber keinen Zugriff auf Ressourcen haben, die Teil des Zugriffspakets sind, müssen die Zuweisungen wahrscheinlich erneut verarbeitet werden, um diese Benutzer wieder den Ressourcen des Zugriffspakets zuzuweisen. Führen Sie die folgenden Schritte aus, um Zuweisungen für ein vorhandenes Zugriffspaket erneut zu verarbeiten:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1.  Klicken Sie auf **Azure Active Directory** und dann auf **Identity Governance**.

1.  Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket mit der erneut zu verarbeitenden Benutzerzuweisung.

1.  Klicken Sie auf der linken Seite unter **Verwalten** auf **Zuweisungen**.

    ![Berechtigungsverwaltung im Azure-Portal](./media/entitlement-management-reprocess-access-package-assignments/reprocess-access-package-assignment.png)

1.  Wählen Sie alle Benutzer aus, deren Zuweisungen Sie erneut verarbeiten möchten.

1.  Klicken Sie auf **Erneut verarbeiten**.

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen, Hinzufügen und Entfernen von Zuweisungen für ein Zugriffspaket](entitlement-management-access-package-assignments.md)
- [Anzeigen von Berichten und Protokollen](entitlement-management-reports.md)
