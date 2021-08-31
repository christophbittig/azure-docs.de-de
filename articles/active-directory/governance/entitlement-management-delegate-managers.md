---
title: Delegieren der Zugriffssteuerung an Zugriffspaket-Manager in der Azure AD-Berechtigungsverwaltung – Azure Active Directory
description: Erfahren Sie, wie Sie die Zugriffssteuerung von IT-Administratoren an Zugriffspaket-Manager und Projektmanager delegieren, damit diese den Zugriff selbst verwalten können.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c875c1a4ded86686120bb4de67904ef69a53c6
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286560"
---
# <a name="delegate-access-governance-to-access-package-managers-in-azure-ad-entitlement-management"></a>Delegieren der Zugriffssteuerung an Zugriffspaket-Manager in der Azure AD-Berechtigungsverwaltung

Um die Erstellung und Verwaltung von Zugriffspaketen in einem Katalog zu delegieren, fügen Sie der Rolle Zugriffspaket-Manager Benutzer hinzu. Zugriffspaket-Manager müssen mit der Notwendigkeit vertraut sein, dass Benutzer Zugriff auf Ressourcen in einem Katalog anfordern müssen. Wenn z. B. für ein Projekt ein Katalog verwendet wird, kann ein Projektleiter ein Zugriffspaket-Manager für diesen Katalog sein.  Zugriffspaket-Manager können einem Katalog keine Ressourcen hinzufügen, aber sie können die Zugriffspakete und -richtlinien in einem Katalog verwalten.  Bei der Delegierung an einen Zugriffspaket-Manager kann diese Person dann für Folgendes verantwortlich sein:

- Welche Rollen ein Benutzer für die Ressourcen in einem Katalog hat
- Wer Zugriff benötigt
- Wer die Zugriffsanforderungen genehmigen soll
- Wie lange das Projekt dauert

Dieses Video bietet eine Übersicht darüber, wie Sie die Zugriffssteuerung vom Katalogbesitzer an den Zugriffspaket-Manager delegieren.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq08]

Zusätzlich zu den Rollen „Katalogbesitzer“ und „Zugriffspaket-Manager“ können Sie auch Benutzer zur Rolle „Katalogleser“, die schreibgeschützten Zugriff auf den Katalog bietet, oder zur Rolle „Zugriffspaketzuweisungs-Manager“ hinzufügen, die Benutzern die Möglichkeit bietet, Zuweisungen zu ändern, aber keinen Zugriff auf Pakete oder Richtlinien ermöglicht.

## <a name="as-a-catalog-owner-delegate-to-an-access-package-manager"></a>Delegieren an den Zugriffspaket-Manager als Katalogbesitzer

Um der Rolle Zugriffspaket-Manager einen Benutzer zuzuweisen, gehen Sie wie folgt vor:

**Erforderliche Rolle:** Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Administratoren hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Rollen und Administratoren**.

    ![Katalogrollen und -administratoren](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. Klicken Sie auf **Zugriffspaket-Manager hinzufügen**, um die Mitglieder für diese Rollen auszuwählen.

1. Klicken Sie auf **Auswählen**, um diese Mitglieder hinzuzufügen.

## <a name="remove-an-access-package-manager"></a>Entfernen eines Zugriffspaket-Managers

Um Benutzer aus der Rolle Zugriffspaket-Manager zu entfernen, gehen Sie wie folgt vor:

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator oder Katalogbesitzer

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Kataloge**, und öffnen Sie dann den Katalog, dem Sie Administratoren hinzufügen möchten.

1. Klicken Sie im linken Menü auf **Rollen und Administratoren**.

1. Fügen Sie neben einem zu entfernenden Zugriffspaket-Manager ein Häkchen hinzu.

1. Klicken Sie auf **Entfernen**.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines neuen Zugriffspakets](entitlement-management-access-package-create.md)
