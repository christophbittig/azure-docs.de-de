---
title: Übersicht über die Azure Functions-Diagnose
description: Erfahren Sie, wie Sie Probleme mit Ihrer Funktions-App mit der Azure Functions-Diagnose beheben können.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 81d8da21d5da91c79fa40077832dd6543fd69499
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432223"
---
# <a name="azure-functions-diagnostics-overview"></a>Übersicht über die Azure Functions-Diagnose

Wenn Sie eine Funktions-App ausführen, möchten Sie auf alle auftretenden Probleme vorbereitet sein, von 4xx-Fehlern bis hin zu Triggerfehlern. Die Azure Functions-Diagnose ist eine intelligente und interaktive Komponente, mit der Sie Probleme bei Ihrer Funktions-App beheben können, ohne dass eine Konfiguration erforderlich ist oder zusätzliche Kosten anfallen. Wenn Probleme mit Ihrer Funktions-App auftreten, weist die Azure Functions-Diagnose darauf hin, was fehlerhaft ist. Sie führt Sie zu den richtigen Informationen, um das Problem leichter und schneller behandeln und beheben zu können. Dieser Artikel zeigt Ihnen die Grundlagen, wie Sie die Azure Functions-Diagnose verwenden können, um häufige Probleme mit Funktions-Apps schneller zu diagnostizieren und zu lösen.

## <a name="start-azure-functions-diagnostics"></a>Starten der Azure Functions-Diagnose

So starten Sie die Azure Functions-Diagnose

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Funktions-App.
1. Wählen Sie **Diagnose und Problembehandlung** aus, um die Azure Functions-Diagnose zu öffnen.
1. Wählen Sie mithilfe der Schlüsselwörter auf der Kachel „Startseite“ eine Kategorie aus, die das Problem ihrer Funktions-App am besten beschreibt. Sie können auch ein Schlüsselwort in die Suchleiste eingeben, das Ihr Problem am besten beschreibt. Beispielsweise können Sie `execution` eingeben, um eine Liste der Diagnoseberichte anzuzeigen, die sich auf die Ausführung ihrer Funktions-App beziehen, und diese direkt über die Startseite öffnen.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Suchen nach der Azure Functions-Diagnose." border="true":::

## <a name="use-the-interactive-interface"></a>Verwenden der interaktiven Benutzeroberfläche

Nachdem Sie auf der Startseite eine Kategorie ausgewählt haben, die am ehesten auf das bei der Funktions-App aufgetretene Problem zutrifft, können Sie über die interaktive Benutzeroberfläche namens „Genie“ der Azure Functions-Diagnose durch die Diagnose und Problembehebung Ihrer App geleitet werden. Sie können die in Genie angegebenen Kachelverknüpfungen verwenden, um den vollständigen Diagnosebericht der jeweiligen Problemkategorie anzuzeigen. Über die Kachelverknüpfungen haben Sie direkt die Möglichkeit, auf die Diagnosemetriken zuzugreifen.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Genie ist die Benutzeroberfläche der Azure Functions-Diagnose." border="false":::

Nachdem Sie eine Kachel ausgewählt haben, wird eine Liste mit Themen angezeigt, die sich auf das in der jeweiligen Kachel beschriebene Problem beziehen. Diese Themen enthalten Auszüge mit relevanten Informationen aus dem vollständigen Bericht. Wählen Sie jedes einzelne dieser Themen aus, um die Probleme weiter zu untersuchen. Zudem können Sie **Vollständigen Bericht anzeigen** auswählen, um alle Themen auf einer Seite zu untersuchen.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Vorschau des Diagnoseberichts" border="false":::

## <a name="view-a-diagnostic-report"></a>Anzeigen eines Diagnoseberichts

Nachdem Sie ein Thema ausgewählt haben, wird ein spezifischer Diagnosebericht für Ihre Funktions-App angezeigt. In Diagnoseberichten werden Statussymbole verwendet, um anzugeben, ob bestimmte Probleme mit der App vorliegen. Sie sehen eine detaillierte Beschreibung des Problems, empfohlene Aktionen, verwandte Metriken und hilfreiche Dokumente. Angepasste Diagnoseberichte werden aus einer Reihe von Überprüfungen generiert, die für Ihre Funktions-App ausgeführt werden. Diagnoseberichte können ein nützliches Tool zum Ermitteln von Problemen in ihrer Funktions-App und zur Behebung des Problems sein.

## <a name="find-the-problem-code"></a>Ermitteln des Problemcodes

Für skriptbasierte Funktionen können Sie **Function Execution and Errors** (Funktionsausführung und Fehler) unter **Function App Down or Reporting Errors** („Funktions-App ausgefallen oder mit Fehlermeldungen“) verwenden, um die Codezeile einzugrenzen, die Ausnahmen oder Fehler verursacht. Sie können dieses Tool nutzen, um die Grundursache zu ermitteln und Probleme aus einer bestimmten Codezeile zu beheben. Diese Option ist für vorkompilierte C#- und Java-Funktionen nicht verfügbar.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Diagnosebericht zu Funktionsausführungsfehlern" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Ansicht der Ausnahmedetails." border="false":::

## <a name="next-steps"></a>Nächste Schritte

Unter [UserVoice](https://feedback.azure.com/d365community/forum/9df02822-f224-ec11-b6e6-000d3a4f0da0) können Sie Fragen stellen oder Feedback zur Azure Functions-Diagnose bereitstellen. Nehmen Sie `[Diag]` in den Titel Ihres Feedbacks auf.

> [!div class="nextstepaction"]
> [Überwachen Ihrer Funktions-Apps](functions-monitoring.md)
