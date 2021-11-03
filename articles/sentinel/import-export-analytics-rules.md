---
title: Importieren und Exportieren von Azure Sentinel-Analyseregeln | Microsoft-Dokumentation
description: Exportieren und Importieren von Analyseregeln in und aus ARM-Vorlagen zur Unterstützung der Bereitstellung
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 905455a371bd479d83418eaf68fbc5bf327ce4e4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009394"
---
# <a name="export-and-import-analytics-rules-to-and-from-arm-templates"></a>Exportieren und Importieren von Analyseregeln in und aus ARM-Vorlagen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Die Funktion zum Exportieren und Importieren von Regeln befindet sich in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="introduction"></a>Einführung

Sie können jetzt Ihre Analyseregeln in ARM-Vorlagendateien (Azure Resource Manager) exportieren und Regeln aus diesen Dateien importieren, um Ihre Azure Sentinel-Bereitstellungen in Form von Code zu verwalten und zu steuern. Die Exportaktion erstellt eine JSON-Datei (mit dem Namen *Azure_Sentinel_analytic_rule.json*) am Downloadspeicherort Ihres Browsers, die Sie dann umbenennen, verschieben und auch sonst wie jede andere Datei behandeln können.

Die exportierte JSON-Datei ist arbeitsbereichsunabhängig, sodass sie in andere Arbeitsbereiche und sogar andere Mandanten importiert werden kann. Als Code kann sie außerdem versionskontrolliert, aktualisiert und in einem verwalteten CI/CD-Framework bereitgestellt werden.

Die Datei enthält alle in der Analyseregel definierten Parameter. Daher enthält sie für **geplante** Regeln die zugrunde liegende Abfrage und die zugehörigen Zeitplanungseinstellungen, den Schweregrad, die Incidenterstellung, Ereignis- und Warnungsgruppierungseinstellungen, zugewiesene MITRE ATT&CK-Taktiken und vieles mehr. Alle Arten von Analyseregel – nicht nur **geplante Regeln** – können in eine JSON-Datei exportiert werden.

## <a name="export-rules"></a>Exportieren von Regeln

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Analysen** aus.

1. Wählen Sie die Regel aus, die Sie exportieren möchten, und klicken Sie auf der Leiste am oberen Bildschirmrand auf **Exportieren**.

    :::image type="content" source="./media/import-export-analytics-rules/export-rule.png" alt-text="Exportieren einer Analyseregel" lightbox="./media/import-export-analytics-rules/export-rule.png":::

    > [!NOTE]
    > - Sie können mehrere Analyseregeln gleichzeitig für den Export auswählen, indem Sie die Kontrollkästchen neben den Regeln markieren und am Ende auf **Exportieren** klicken.
    >
    > - Sie können alle Regeln auf einer einzelnen Seite des Anzeigerasters gleichzeitig exportieren, indem Sie das Kontrollkästchen in der Kopfzeile (neben **SCHWEREGRAD**) markieren, bevor Sie auf **Exportieren** klicken. Das gleichzeitige Exportieren von Regeln auf verschiedenen Seiten ist jedoch nicht möglich.
    >
    > - Beachten Sie, dass in diesem Szenario eine einzelne Datei (mit dem Namen *Azure_Sentinel_analytic_ **rules**.json*) erstellt wird, die JSON-Code für alle exportierten Regeln enthält.

## <a name="import-rules"></a>Importieren von Regeln

1. Bereiten Sie eine ARM-Vorlagen-JSON-Datei für die Analyseregel vor.

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Analysen** aus.

1. Klicken Sie auf der Leiste am oberen Bildschirmrand auf **Importieren**. Navigieren Sie im daraufhin angezeigten Dialogfeld zu der JSON-Datei mit der Regel, die Sie importieren möchten, und wählen Sie **Öffnen** aus.

    :::image type="content" source="./media/import-export-analytics-rules/import-rule.png" alt-text="Importieren einer Analyseregel" lightbox="./media/import-export-analytics-rules/import-rule.png":::

    > [!NOTE]
    > Sie können **bis zu 50** Analyseregeln aus einer einzelnen ARM-Vorlagendatei importieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Analyseregeln in und aus ARM-Vorlagen exportieren und importieren.
- Erfahren Sie mehr über [Analyseregeln](detect-threats-built-in.md), einschließlich [benutzerdefinierter geplanter Regeln](detect-threats-custom.md).
- Weitere Informationen zu ARM-Vorlagen finden Sie [hier](../azure-resource-manager/templates/overview.md).
