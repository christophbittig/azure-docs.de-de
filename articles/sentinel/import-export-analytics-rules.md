---
title: Importieren und Exportieren von Microsoft Sentinel-Analyseregeln | Microsoft Dokumentation
description: Exportieren und Importieren von Analyseregeln in und aus ARM-Vorlagen zur Unterstützung der Bereitstellung
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
ms.openlocfilehash: 97eb1145e8eebaed91019fbd68330c1399522002
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518544"
---
# <a name="export-and-import-analytics-rules-to-and-from-arm-templates"></a>Exportieren und Importieren von Analyseregeln in und aus ARM-Vorlagen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Die Funktion zum Exportieren und Importieren von Regeln befindet sich in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="introduction"></a>Einführung

Sie können jetzt Ihre Analyseregeln in Azure Resource Manager (ARM)-Vorlagendateien exportieren und Regeln aus diesen Dateien importieren, um Ihre Microsoft Sentinel-Bereitstellungen als Code zu verwalten und zu steuern. Die Exportaktion erstellt eine JSON-Datei (mit dem Namen *Azure_Sentinel_analytic_rule.json*) am Downloadspeicherort Ihres Browsers, die Sie dann umbenennen, verschieben und auch sonst wie jede andere Datei behandeln können.

Die exportierte JSON-Datei ist arbeitsbereichsunabhängig, sodass sie in andere Arbeitsbereiche und sogar andere Mandanten importiert werden kann. Als Code kann sie außerdem versionskontrolliert, aktualisiert und in einem verwalteten CI/CD-Framework bereitgestellt werden.

Die Datei enthält alle in der Analyseregel definierten Parameter. Daher enthält sie für **geplante** Regeln die zugrunde liegende Abfrage und die zugehörigen Zeitplanungseinstellungen, den Schweregrad, die Incidenterstellung, Ereignis- und Warnungsgruppierungseinstellungen, zugewiesene MITRE ATT&CK-Taktiken und vieles mehr. Alle Arten von Analyseregel – nicht nur **geplante Regeln** – können in eine JSON-Datei exportiert werden.

## <a name="export-rules"></a>Exportieren von Regeln

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Analytik**.

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

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel die Optionen **Analytik**.

1. Klicken Sie auf der Leiste am oberen Bildschirmrand auf **Importieren**. Navigieren Sie im daraufhin angezeigten Dialogfeld zu der JSON-Datei mit der Regel, die Sie importieren möchten, und wählen Sie **Öffnen** aus.

    :::image type="content" source="./media/import-export-analytics-rules/import-rule.png" alt-text="Importieren einer Analyseregel" lightbox="./media/import-export-analytics-rules/import-rule.png":::

    > [!NOTE]
    > Sie können **bis zu 50** Analyseregeln aus einer einzelnen ARM-Vorlagendatei importieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Analyseregeln in und aus ARM-Vorlagen exportieren und importieren.
- Erfahren Sie mehr über [Analyseregeln](detect-threats-built-in.md), einschließlich [benutzerdefinierter geplanter Regeln](detect-threats-custom.md).
- Weitere Informationen zu ARM-Vorlagen finden Sie [hier](../azure-resource-manager/templates/overview.md).
