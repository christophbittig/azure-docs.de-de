---
title: 'Erneutes Verarbeiten von Anforderungen für ein Zugriffspaket in der Azure ADAD-Berechtigungsverwaltung: Azure Active Directory'
description: Erfahren Sie, wie Sie Anforderungen für ein Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung erneut verarbeiten.
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
ms.openlocfilehash: a85d796f8b66cca16d4d3c01ecbfc5f6c43c79f9
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129329"
---
# <a name="reprocess-requests-for-an-access-package-in-azure-ad-entitlement-management"></a>Erneutes Verarbeiten von Anforderungen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

Als Zugriffspaket-Manager können Sie die Anforderung eines Benutzers für den Zugriff auf ein Zugriffspaket jederzeit automatisch wiederholen, indem Sie die Funktion zur erneuten Verarbeitung nutzen. Durch die erneute Verarbeitung müssen Benutzer die Anforderung des Pakets nicht mehr wiederholen, wenn ihr Zugriff auf die Ressourcen nicht erfolgreich bereitgestellt wurde.

> [!NOTE]
> Sie können eine Anforderung bis zu 14 Tage nach dem Abschluss der ursprünglichen Anforderung erneut verarbeiten. Für Anforderungen, die vor mehr als 14 Tagen abgeschlossen wurden, müssen Benutzer den Vorgang abbrechen und in MyAccess neue Anforderungen stellen.

In diesem Artikel wird beschrieben, wie die Anforderungseinstellungen für ein vorhandenes Zugriffspaket erneut verarbeitet werden.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Berechtigungsverwaltung zu verwenden und Benutzer zu Zugriffspaketen zuzuweisen, benötigen Sie eine der folgenden Lizenzen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5-Lizenz

## <a name="open-an-existing-access-package-and-reprocess-user-requests"></a>Öffnen eines vorhandenen Zugriffspakets und erneutes Verarbeiten von Benutzeranforderungen

**Erforderliche Rolle**: Globaler Administrator, Identity Governance-Administrator, Benutzeradministrator, Katalogbesitzer, Zugriffspaket-Manager oder Zugriffspaketzuweisungs-Manager

Wenn es eine Gruppe von Benutzern gibt, deren Anforderungen den Status „Teilweise übermittelt“ oder „Fehler“ haben, müssen Sie möglicherweise einige dieser Anforderungen erneut verarbeiten. Führen Sie die folgenden Schritte aus, um Anforderungen für ein vorhandenes Zugriffspaket erneut zu verarbeiten:

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1.  Klicken Sie auf **Azure Active Directory** und dann auf **Identity Governance**.

1.  Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1.  Klicken Sie auf der linken Seite unter **Verwalten** auf **Anforderungen**.

1.  Wählen Sie alle Benutzer aus, deren Anforderungen Sie erneut verarbeiten möchten.

1.  Klicken Sie auf **Erneut verarbeiten**.

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen der Anforderungen für ein Zugriffspaket](entitlement-management-access-package-requests.md)
- [Genehmigen oder Ablehnen von Zugriffsanforderungen](entitlement-management-request-approve.md)