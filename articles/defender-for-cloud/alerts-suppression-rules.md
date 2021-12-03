---
title: Verwenden von Unterdrückungsregeln für Warnungen, um falsch positive oder andere unerwünschte Sicherheitswarnungen in Microsoft Defender für Cloud zu unterdrücken.
description: In diesem Artikel wird erläutert, wie Sie mithilfe der Unterdrückungsregeln von Microsoft Defender für Cloud unerwünschte Sicherheitswarnungen ausblenden können.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 11/09/2021
ms.service: defender-for-cloud
ms.topic: how-to
ms.openlocfilehash: 38f10155de47db37b3ab73d81285b578c895e769
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526164"
---
# <a name="suppress-alerts-from-microsoft-defender-for-cloud"></a>Unterdrücken von Warnungen aus Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Auf dieser Seite wird erläutert, wie Sie mithilfe von Unterdrückungsregeln für Warnungen falsch positive oder andere unerwünschte Sicherheitswarnungen von Defender für Cloud unterdrücken können.

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Kostenlos<br>(Die meisten Sicherheitswarnungen sind nur mit [erweiterten Sicherheitsfeatures](enable-enhanced-security.md) verfügbar.)|
|Erforderliche Rollen und Berechtigungen:|**Sicherheitsadministrator** und **Besitzer** können Regeln erstellen und löschen.<br>**Sicherheitsleseberechtigter** und **Leser** können Regeln anzeigen.|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||


## <a name="what-are-suppression-rules"></a>Was sind Warnungsunterdrückungsregeln?

Die verschiedenen Microsoft Defender-Pläne erkennen Bedrohungen in einem bestimmten Bereich Ihrer Umgebung und generieren entsprechende Sicherheitswarnungen.

Wenn eine einzelne Warnung nicht interessant oder relevant ist, können Sie sie manuell verwerfen. Alternativ können Sie die Funktion „Unterdrückungsregeln“ verwenden, um ähnliche Warnungen zukünftig automatisch zu verwerfen. In der Regel verwenden Sie eine Unterdrückungsregel in folgenden Fällen:

- Unterdrücken von Warnungen, die Sie als falsch positiv identifiziert haben

- Unterdrücken von Warnungen, die zu oft ausgelöst werden und daher nicht nützlich sind

Mit Unterdrückungsregeln definieren Sie die Kriterien, nach denen Warnungen automatisch verworfen werden sollen.

> [!CAUTION]
> Durch das Unterdrücken von Sicherheitswarnungen wird die Effektivität des Bedrohungsschutzes von Microsoft Defender für die Cloud verringert. Sie sollten die potenziellen Auswirkungen der Unterdrückungsregeln sorgfältig überprüfen und diese über einen bestimmten Zeitraum überwachen.

:::image type="content" source="./media/alerts-suppression-rules/create-suppression-rule.gif" alt-text="Erstellen einer Warnungsunterdrückungsregel":::

## <a name="create-a-suppression-rule"></a>Erstellen einer Unterdrückungsregel

Es gibt einige Möglichkeiten, wie Sie Regeln zum Unterdrücken unerwünschter Sicherheitswarnungen erstellen können:

- Warnungen auf Verwaltungsgruppenebene können Sie mithilfe von Azure Policy unterdrücken.
- Um Warnungen auf Abonnementebene zu unterdrücken, können Sie wie im Folgenden erläutert das Azure-Portal oder die REST-API verwenden.

Mit Unterdrückungsregeln können nur Warnungen verworfen werden, die für die ausgewählten Abonnements bereits ausgelöst wurden.

So erstellen Sie eine Regel direkt im Azure-Portal

1. Auf der Seite mit den Sicherheitswarnungen von Microsoft Defender für Cloud:

    - Wählen Sie die Warnung aus, die nicht mehr angezeigt werden soll, und wählen Sie im Detailbereich **Aktion ausführen** aus.

    - Oder wählen Sie oben auf der Seite den Link **Unterdrückungsregeln** und dann auf der Seite der Unterdrückungsregeln die Option **Neue Unterdrückungsregel erstellen** aus:

        ![Neue Unterdrückungsregel erstellen** (Schaltfläche)](media/alerts-suppression-rules/create-new-suppression-rule.png)

1. Geben Sie im Bereich „Neue Unterdrückungsregel (Vorschau)“ Details zur neuen Regel ein.
    - Mit der Regel kann die Warnung für **alle Ressourcen** verworfen werden, sodass zukünftig keine derartige Regel mehr angezeigt wird.     
    - Mit der Regel kann die Warnung **nach bestimmten Kriterien** verworfen werden, z. B. wenn die Warnung sich auf eine bestimmte IP-Adresse, einen Prozessnamen, ein Benutzerkonto, eine Azure-Ressource oder einen Standort bezieht.

    > [!TIP]
    > Wenn Sie die Seite für die neue Regel über eine bestimmte Warnung geöffnet haben, werden die Warnung und das Abonnement automatisch in der neuen Regel konfiguriert. Wenn Sie den Link **Neue Unterdrückungsregel erstellen** verwendet haben, entsprechen die ausgewählten Abonnements dem aktuellen Filter im Portal.

    [![Bereich zum Erstellen von Unterdrückungsregeln](media/alerts-suppression-rules/new-suppression-rule-pane.png)](media/alerts-suppression-rules/new-suppression-rule-pane.png#lightbox)
1. Geben Sie Details zur Regel ein:
    - **Name:** ein Name für die Regel. Regelnamen müssen mit einem Buchstaben oder einer Ziffer beginnen, müssen zwischen 2 und 50 Zeichen lang sein und dürfen keine anderen Symbole als Bindestriche (-) oder Unterstriche (_) enthalten. 
    - **Status:** „Aktiviert“ oder „Deaktiviert“.
    - **Grund:** Wählen Sie einen der integrierten Gründe oder „Sonstiger“ aus, wenn diese Ihren Anforderungen nicht entsprechen.
    - **Ablaufdatum:** ein Ablaufdatum und eine Ablaufzeit für die Regel. Regeln können bis zu sechs Monate lang gelten.
1. Optional können Sie die Regel über die Schaltfläche **Simulate** (Simulieren) testen, um zu sehen, wie viele Warnungen verworfen werden, wenn die Regel aktiv ist.
1. Speichern Sie die Regel. 


## <a name="edit-a-suppression-rule"></a>Bearbeiten einer Unterdrückungsregel

Die erstellten Regeln können Sie auf der Seite der Unterdrückungsregeln bearbeiten.

1. Wählen Sie oben auf der Seite mit den Sicherheitswarnungen von Defender für Cloud den Link **Unterdrückungsregeln** aus.
1. Die Seite der Unterdrückungsregeln wird mit allen Regeln für das ausgewählte Abonnement geöffnet.

    [![Liste der Unterdrückungsregeln](media/alerts-suppression-rules/suppression-rules-page.png)](media/alerts-suppression-rules/suppression-rules-page.png#lightbox)

1. Um eine einzelne Regel zu bearbeiten, öffnen Sie das Menü mit den Auslassungspunkten (...) für die Regel, und wählen Sie **Bearbeiten** aus.
1. Nehmen Sie die gewünschten Änderungen vor, und wählen Sie dann **Anwenden** aus. 

## <a name="delete-a-suppression-rule"></a>Löschen einer Unterdrückungsregel

Wenn Sie eine oder mehrere der erstellten Regeln löschen möchten, verwenden Sie die Seite „Unterdrückungsregeln“.

1. Wählen Sie oben auf der Seite mit den Sicherheitswarnungen von Defender für Cloud den Link **Unterdrückungsregeln** aus.
1. Die Seite der Unterdrückungsregeln wird mit allen Regeln für das ausgewählte Abonnement geöffnet.
1. Um eine einzelne Regel zu löschen, öffnen Sie das Menü mit den Auslassungspunkten (...) für die Regel, und wählen Sie **Löschen** aus.
1. Wenn Sie mehrere Regeln löschen möchten, aktivieren Sie die Kontrollkästchen für die zu löschenden Regeln, und wählen Sie **Löschen** aus.
    ![Löschen einzelner oder mehrerer Unterdrückungsregeln](media/alerts-suppression-rules/delete-multiple-alerts.png)

## <a name="create-and-manage-suppression-rules-with-the-api"></a>Erstellen und Verwalten von Unterdrückungsregeln über die API

Sie können Unterdrückungsregeln für Warnungen über die REST-API von Defender für Cloud erstellen, anzeigen oder löschen. 

Die relevanten HTTP-Methoden für Unterdrückungsregeln in der REST-API lauten wie folgt:

- **PUT**: Erstellen oder Aktualisieren einer Unterdrückungsregel in einem angegebenen Abonnement

- **GET**:

    - Auflisten aller Regeln, die für ein bestimmtes Abonnement konfiguriert sind. Diese Methode gibt ein Array der anwendbaren Regeln zurück.

    - Abrufen der Details einer spezifischen Regel für ein bestimmtes Abonnement. Diese Methode gibt eine Unterdrückungsregel zurück.

    - Simulieren der Auswirkung einer Unterdrückungsregel in der Entwurfsphase. Mit diesem Aufruf ermitteln Sie, welche der vorhandenen Warnungen verworfen werden, wenn die Regel aktiv ist.

- **DELETE**: Löschen einer vorhandenen Regel (der Status der durch die Regel bereits verworfenen Warnungen wird jedoch nicht geändert).

Ausführliche Informationen und Verwendungsbeispiele finden Sie in der [API-Dokumentation](/rest/api/securitycenter/). 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden Unterdrückungsregeln in Microsoft Defender für Cloud beschrieben, mit denen unerwünschte Warnungen automatisch verworfen werden.

Weitere Informationen zu Sicherheitswarnungen finden Sie auf den folgenden Seiten:

- [Sicherheitswarnungen und Kill Chain für Absichten:](alerts-reference.md) Eine Referenzanleitung zu den Sicherheitswarnungen, die Sie möglicherweise von Defender für Cloud erhalten.
