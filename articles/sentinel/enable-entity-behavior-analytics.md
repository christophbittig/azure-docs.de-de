---
title: Verwenden von Entity Behavior Analytics zum Erkennen komplexer Bedrohungen | Microsoft-Dokumentation
description: Aktivieren von User and Entity Behavior Analytics in Microsoft Sentinel und Konfigurieren von Datenquellen
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e8541c6205f8cfd7503b16fa918ca531d30d4339
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521356"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-microsoft-sentinel"></a>Aktivieren von User and Entity Behavior Analytics (UEBA) in Microsoft Sentinel 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Die Features „UEBA“ und „Entitätsseiten“ sind nun in **_allen_** Microsoft Sentinel-Geografien und -Regionen **allgemein verfügbar**. 

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Voraussetzungen

So aktivieren oder deaktivieren Sie dieses Feature (diese Voraussetzungen müssen nicht erfüllt sein, um das Feature zu verwenden)

- Der Benutzer muss Mitglied des Azure Active Directory Ihrer Organisation und darf kein Gastbenutzer sein.

- Ihrem Benutzer muss die Rolle **Globaler Administrator** oder **Sicherheitsadministrator** in Azure AD zugewiesen sein.

- Ihrem Benutzer muss mindestens eine der folgenden **Azure-Rollen**  zugewiesen sein ([Weitere Informationen zu Azure RBAC](roles.md)):
    - **Microsoft Sentinel-Mitwirkender** auf Arbeitsbereichs -oder Ressourcengruppenebene.
    - **Log Analytics-Mitwirkender** auf Ressourcengruppen- oder Abonnementebene.

- In Ihrem Arbeitsbereich dürfen keine Azure-Ressourcensperren aktiviert sein. [Erfahren Sie mehr über Azure-Ressourcensperren](../azure-resource-manager/management/lock-resources.md).

> [!NOTE]
> Zum Hinzufügen von UEBA-Funktionalität zu Microsoft Sentinel ist keine spezielle Lizenz erforderlich, aber evtl. fallen **zusätzliche Gebühren** an.

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>Aktivieren von User and Entity Behavior Analytics

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel **Entity Behavior** aus.

1. Schalten Sie den Umschalter unter der Überschrift **Aktivieren** auf **Ein** um.

1. Klicken Sie auf die Schaltfläche **Datenquellen auswählen**.

1. Aktivieren Sie im Bereich **Datenquellenauswahl** die Kontrollkästchen neben den Datenquellen, für die Sie UEBA aktivieren möchten, und wählen Sie dann **Anwenden** aus.

    > [!NOTE]
    >
    > In der unteren Hälfte des Bereichs **Datenquellenauswahl** wird eine Liste der von UEBA unterstützten Datenquellen angezeigt, die Sie noch nicht aktiviert haben. 
    >
    > Nachdem Sie UEBA aktiviert haben, können Sie beim Herstellen einer Verbindung mit neuen Datenquellen diese im Bereich des Datenconnectors direkt für UEBA aktivieren (sofern sie UEBA-fähig sind).

1. Wählen Sie **Go to entity search** (Zur Entitätsssuche) aus. Dadurch gelangen Sie zum Bereich für die Entitätssuche, der in Zukunft angezeigt wird, wenn Sie im Hauptmenü von Microsoft Sentinel **Entity behavior** auswählen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie die User and Entity Behavior Analytics (UEBA) in Microsoft Sentinel aktivieren und konfigurieren. Weitere Informationen zu Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](detect-threats-built-in.md).
