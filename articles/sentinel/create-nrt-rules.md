---
title: Arbeiten mit Analyseregeln nahezu in Echtzeit (Near-Real-Time, NRT) in Microsoft Sentinel | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie Regeln zur Erkennungsanalyse nahezu in Echtzeit (NRT) in Microsoft Sentinel anzeigen und erstellen.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 69e639ad74388756217f06922d8cfcae62d28197
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521691"
---
# <a name="work-with-near-real-time-nrt-detection-analytics-rules-in-microsoft-sentinel"></a>Arbeiten mit Analyseregeln nahezu in Echtzeit (Near-Real-Time, NRT) in Microsoft Sentinel

> [!IMPORTANT]
>
> - NRT-Regeln (nahezu in Echtzeit) befinden sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Microsoft Sentinels [Nah-Echtzeit-Analyseregeln](near-real-time-rules.md) sorgen sofort nach der Installation für eine minutengenaue Erkennung von Bedrohungen. Diese Art von Regel wurde so konzipiert, dass sie extrem reaktionsschnell ist, indem sie ihre Abfrage in Abständen von nur einer Minute durchführt.

Im Moment haben diese Vorlagen nur eine begrenzte Anwendung, wie unten beschrieben, aber die Technologie entwickelt sich schnell weiter und wächst.

## <a name="view-near-real-time-nrt-rules"></a>Anzeigen von Regeln nahezu in Echtzeit (Near-Real-Time, NRT)

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Analytik**.

1. Filtern Sie auf der Registerkarte **Aktive Regeln** des Blatts **Analysen** die Liste nach **NRT**-Vorlagen:

    1. Klicken Sie auf den Filter **Regeltyp** und dann auf die unten angezeigte Dropdownliste.

    1. Heben Sie die Markierung **Alle auswählen** auf, und markieren Sie dann die Option **NRT**.

    1. Klicken Sie bei Bedarf oben auf die Dropdownliste, um sie zu reduzieren, und klicken Sie dann auf **OK**.

## <a name="create-nrt-rules"></a>Erstellen von NRT-Regeln

Sie erstellen NRT-Regeln auf die gleiche Weise wie reguläre [Regeln für die Analyse geplanter Abfragen](detect-threats-custom.md):

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Analytik**.

1. Wählen Sie in der Schaltflächenleiste die Option **Erstellen** und dann in der Dropdownliste die Option **NRT-Abfrageregel** aus.

    :::image type="content" source="media/create-nrt-rules/create-nrt-rule.png" alt-text="Erstellen Sie eine neue NRT-Regel.":::

1. Befolgen Sie die Anweisungen des [**Analyseregel-Assistenten**](detect-threats-custom.md).

    Die Konfiguration von NRT-Regeln ist in vielerlei Hinsicht mit der Konfiguration geplanter Analyseregeln identisch. 

    - Sie können in Ihrer Abfragelogik auf [**Watchlists**](watchlists.md) und [**Threat Intelligence-Feeds**](understand-threat-intelligence.md) verweisen.

    - Sie können alle Methoden zur Warnungsanreicherung verwenden: [**Entitätszuordnung**](map-data-fields-to-entities.md), [**benutzerdefinierte Details**](surface-custom-details-in-alerts.md) und [**Warnungsdetails**](customize-alert-details.md).

    - Sie können auswählen, wie Warnungen zu Vorfällen gruppiert werden sollen, und eine Abfrage unterdrücken, wenn ein bestimmtes Ergebnis generiert wurde.

    - Sie können die Reaktion auf Warnungen und Vorfälle automatisieren.

    Aufgrund der [**Natur und der Einschränkungen von NRT-Regeln**](near-real-time-rules.md#considerations) sind die folgenden Features von geplanten Analyseregeln im Assistenten jedoch *nicht verfügbar*:

    - Die **Abfrageplanung** kann nicht konfiguriert werden, da Abfragen automatisch einmal pro Minute mit einem Rückblickzeitraum von einer Minute ausgeführt werden. 
    - Der **Warnungsschwellenwert** ist irrelevant, da immer eine Warnung generiert wird.
    - Die Konfiguration der **Ereignisgruppierung** ist nicht verfügbar, da Ereignisse immer in die Warnung gruppiert werden, die von der Regel erstellt wird, die die Ereignisse erfasst. NRT-Regeln können nicht für jedes Ereignis eine Warnung erzeugen.

    Darüber hinaus gelten für die Abfrage selbst die folgenden Anforderungen:

    - Die Abfrage selbst kann nur auf eine Tabelle verweisen und darf keine Unions oder Joins enthalten.

    - Sie können die Abfrage nicht arbeitsbereichsübergreifend ausführen.

    - Aufgrund der Größenbeschränkungen der Warnungen sollte Ihre Abfrage `project`-Anweisungen verwenden, um nur die erforderlichen Felder aus der Tabelle einzubeziehen. Andernfalls werden die Informationen, die Sie präsentieren möchten, möglicherweise abgeschnitten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Analyseregeln nahezu in Echtzeit (NRT) in Microsoft Sentinel erstellen.

- Erfahren Sie mehr über [Analyseregeln nahezu in Echtzeit (NRT) in Microsoft Sentinel](near-real-time-rules.md).
- Informieren Sie sich über andere [Typen von Analyseregeln](detect-threats-built-in.md).
