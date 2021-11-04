---
title: Verwalten von Azure-Abonnementrichtlinien
description: Hier erfahren Sie, wie Sie Azure-Abonnementrichtlinien verwalten, um das Verschieben von Azure-Abonnements aus Verzeichnissen und in Verzeichnisse zu steuern.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2021
ms.reviewer: andalmia
ms.author: banders
ms.openlocfilehash: 2530cdd36a2844c5b311e97d9747c8734d808206
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464925"
---
# <a name="manage-azure-subscription-policies"></a>Verwalten von Azure-Abonnementrichtlinien

Dieser Artikel hilft Ihnen beim Konfigurieren von Azure-Abonnementrichtlinien für Abonnementvorgänge, um das Verschieben von Azure-Abonnements aus Verzeichnissen und in Verzeichnisse zu steuern.

## <a name="prerequisites"></a>Voraussetzungen

- Abonnementrichtlinien können nur von [globalen Verzeichnisadministratoren](../../active-directory/roles/permissions-reference.md#global-administrator) bearbeitet werden. Vor dem Bearbeiten von Abonnementrichtlinien muss der globale Administrator die [Zugriffsrechte erhöhen, um alle Azure-Abonnements und Verwaltungsgruppen verwalten zu können](../../role-based-access-control/elevate-access-global-admin.md). Anschließend kann er Abonnementrichtlinien bearbeiten.
- Alle anderen Benutzer können nur die aktuelle Richtlinieneinstellung lesen.

## <a name="available-subscription-policy-settings"></a>Verfügbare Einstellungen für Abonnementrichtlinien

Verwenden Sie die folgenden Richtlinieneinstellungen, um das Verschieben von Azure-Abonnements aus Verzeichnissen und in Verzeichnisse zu steuern.

### <a name="subscriptions-leaving-aad-directory"></a>Abonnements, die das AAD-Verzeichnis verlassen

Die Richtlinie ermöglicht oder verhindert, dass Benutzer Abonnements aus dem aktuellen Verzeichnis verschieben. [Abonnementbesitzer](../../role-based-access-control/built-in-roles.md#owner) können [das Verzeichnis eines Azure-Abonnements in ein anderes Verzeichnis ändern](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md), dem sie angehören. Da dies mit Governanceherausforderungen verbunden ist, können globale Administratoren Verzeichnisänderungen für Verzeichnisbenutzer zulassen oder verweigern.

### <a name="subscriptions-entering-aad-directory"></a>Abonnements, die dem AAD-Verzeichnis hinzugefügt werden

Die Richtlinie ermöglicht oder verhindert, dass Benutzer aus anderen Verzeichnissen, die Zugriff auf das aktuelle Verzeichnis haben, Abonnements in das aktuelle Verzeichnis verschieben. [Abonnementbesitzer](../../role-based-access-control/built-in-roles.md#owner) können [das Verzeichnis eines Azure-Abonnements in ein anderes Verzeichnis ändern](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md), dem sie angehören. Da dies mit Governanceherausforderungen verbunden ist, können globale Administratoren Verzeichnisänderungen für Verzeichnisbenutzer zulassen oder verweigern.

### <a name="exempted-users"></a>Ausgenommene Benutzer

Aus Governancegründen können globale Administratoren alle Verschiebungen von Abonnementverzeichnissen in das aktuelle Verzeichnis oder aus dem aktuellen Verzeichnis blockieren. Möglicherweise möchten sie jedoch bestimmten Benutzern erlauben, diese Vorgänge auszuführen. In beiden Fällen können sie eine Liste mit ausgenommenen Benutzern konfigurieren, die es den Benutzern ermöglicht, die für alle anderen Benutzer geltende Richtlinieneinstellung zu umgehen.

## <a name="setting-subscription-policy"></a>Festlegen der Abonnementrichtlinie

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Navigieren Sie zu **Abonnements**. Auf der Befehlsleiste wird **Richtlinien verwalten** angezeigt.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Screenshot: „Richtlinien verwalten“ unter „Abonnements“" lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Wählen Sie **Richtlinien verwalten** aus, um Details zu den aktuellen Abonnementrichtlinien anzuzeigen, die für das Verzeichnis festgelegt sind. Ein globaler Administrator mit [erweiterten Berechtigungen](../../role-based-access-control/elevate-access-global-admin.md) kann die Einstellungen bearbeiten und ausgenommene Benutzer hinzufügen oder entfernen.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="Screenshot: Bestimmte Richtlinieneinstellungen und ausgenommene Benutzer" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. Wählen Sie unten **Änderungen speichern** aus, um die Änderungen zu speichern. Die Änderungen werden umgehend wirksam.

## <a name="read-subscription-policy"></a>Lesen der Abonnementrichtlinie

Benutzer ohne Berechtigungen eines globalen Administrators können im Bereich für Abonnementrichtlinien die Richtlinieneinstellungen des Verzeichnisses anzeigen. Sie können allerdings keine Änderungen vornehmen. Aus Datenschutzgründen wird die Liste der ausgenommenen Benutzer nicht angezeigt. Sie können ihre globalen Administratoren anzeigen, um Anforderungen für Richtlinienänderungen zu übermitteln, sofern die Verzeichniseinstellungen dies zulassen.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Screenshot: Verwalten von Richtlinien in Abonnements als Leser" lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Dokumentation zu Cost Management and Billing](../index.yml).