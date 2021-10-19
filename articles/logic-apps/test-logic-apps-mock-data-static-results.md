---
title: Modelltestworkflows
description: Richten Sie Modelldaten ein, um Workflows in Azure Logic Apps ohne Auswirkungen auf Produktionsumgebungen zu testen.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 10/08/2021
ms.openlocfilehash: 4167dcffe0a1b4db50f6d1580ad6284f2cf9321d
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129712622"
---
# <a name="test-workflows-with-mock-data-in-azure-logic-apps-preview"></a>Testen von Workflows mit Modelldaten in Azure Logic Apps (Vorschau)

> [!NOTE]
> Diese Funktion befindet sich in der Vorschauphase und unterliegt den [Zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um Ihre Workflows zu testen, ohne tatsächlich Live-Apps, Daten, Dienste oder Systeme auf aufrufen oder darauf zugreifen zu müssen, können Sie Modellwerte von Aktionen einrichten und zurückgeben. Beispielsweise können Sie verschiedene Aktionspfade basierend auf verschiedenen Bedingungen testen, Fehler erzwingen, bestimmten Nachrichtenantworttext bereitstellen oder sogar versuchen, einige Schritte zu überspringen. Wenn Modelldatentests für eine Aktion eingerichtet werden, wird diese nicht ausgeführt. Stattdessen werden Simulationsdaten zurückgegeben.

Wenn Sie beispielsweise Modelldaten für die Aktion Outlook 365-E-Mail senden einrichten, gibt Azure Logic Apps nur die von Ihnen bereitgestellten Modelldaten zurück, anstatt Outlook aufzurufen und eine E-Mail zu senden.

In diesem Artikel wird gezeigt, wie Sie Modelldaten für eine Aktion in einem Workflow für den [**Logik-App-Ressourcentyp (Verbrauch)** und den **Logik-App-Ressourcentyp (Standard)** einrichten](logic-apps-overview.md#resource-environment-differences). Sie finden vorherige Workflow-Ausführungen, die diese Modelldaten verwenden und vorhandene Aktionsausgaben als Modelldaten wiederverwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich <a href="https://azure.microsoft.com/free/?WT.mc_id=A261C142F" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Die Logik-App-Ressource und der Workflow, in der bzw. dem Sie Modelldaten einrichten möchten. In diesem Artikel werden ein **Wiederholungstrigger** und eine **HTTP-Aktion** als Beispielworkflow verwendet.

  Wenn Sie noch nicht mit Logik-Apps vertraut sind, lesen Sie [Was ist Azure Logic Apps?](logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten Logik-App-Workflows](quickstart-create-first-logic-app-workflow.md).

<a name="enable-mock-data"></a>

## <a name="enable-mock-data-output"></a>Aktivieren der Ausgabe von Modelldaten

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Gehen Sie bei der Aktion, bei der Sie Modelldaten zurückgeben möchten, wie folgt vor:

   1. Wählen Sie in der rechten oberen Ecke der Aktion die Schaltfläche mit den Auslassungspunkten ( *...* ) und dann **Tests** aus, z. B.:

      ![Der Screenshot zeigt das Azure-Portal, den Workflow-Designer, das Aktionskurzmenü und die Auswahl „Testen“.](./media/test-logic-apps-mock-data-static-results/select-testing.png)

   1. Wählen Sie im Bereich **Tests** die Option **Statisches Ergebnis aktivieren (Vorschau)** aus. Wenn die erforderlichen (*) Eigenschaften der Aktion angezeigt werden, geben Sie die Modellausgabewerte an, die Sie als Antwort der Aktion zurückgeben möchten.

      Die Eigenschaften unterscheiden sich je nach ausgewähltem Aktionstyp. Die HTTP-Aktion verfügt beispielsweise über die folgenden erforderlichen Eigenschaften:

      | Eigenschaft | BESCHREIBUNG |
      |----------|-------------|
      | **Status** | Der Status der Aktion, der zurückgegeben werden soll |
      | **Statuscode** | Der genaue Statuscode, der als Ausgabe zurückgeben werden soll |
      | **Headers** | Der Headerinhalt, der zurückgegeben werden soll |
      |||

      ![Screenshot, der den Bereich „Tests“ nach Auswahl von „Statisches Ergebnis aktivieren“ zeigt.](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      > [!TIP]
      > Wenn Sie die Werte im JSON-Format (JavaScript Object Notation) eingeben, wählen Sie **Zum JSON-Modus wechseln** (![Symbol für „Zum JSON-Modus wechseln“](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) aus.

   1. Optionale Eigenschaften finden Sie in der Liste **Optionale Felder auswählen**. Öffnen Sie diese, und wählen Sie die Eigenschaften aus, die simuliert werde sollen.

      ![Screenshot, der Bereich „Testen“ mit geöffneter Liste „Optionale Felder auswählen“ anzeigt.](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. Wählen Sie abschließend **Fertig** aus.

   In der rechten oberen Ecke der Aktion zeigt die Titelleiste jetzt ein Becherglassymbol (![Symbol für statisches Ergebnis](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), das angibt, dass die statischen Ergebnisse aktiviert sind.

   ![Screenshot, der eine Aktion mit dem Symbol für statische Ergebnisse zeigt.](./media/test-logic-apps-mock-data-static-results/static-result-enabled.png)

   Informationen dazu, wie Sie Workflow-Ausführungen finden, bei denen Simulationsdaten verwendet werden, erhalten Sie weiter unten in diesem Artikel im Abschnitt [Suchen von Ausführungen mit statischen Ergebnissen](#find-runs-mock-data).

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie im Designer die Aktion aus, an die Sie Modelldaten zurückgeben möchten, damit der Bereich mit den Aktionsdetails angezeigt wird.

1. Nachdem der Bereich mit den Aktionsdetails auf der rechten Seite geöffnet wurde, wählen Sie **Testen** aus.

   ![Der Screenshot zeigt das Azure-Portal, den Workflow-Designer, das Aktionsdetailbereich und die Auswahl „Testen“.](./media/test-logic-apps-mock-data-static-results/select-testing-standard.png)

1. Wählen Sie in der Registerkarte **Tests** die Option **Statisches Ergebnis aktivieren (Vorschau)** aus. Wenn die erforderlichen (*) Eigenschaften der Aktion angezeigt werden, geben Sie die Modellausgabewerte an, die Sie als Antwort der Aktion zurückgeben möchten.

   Die Eigenschaften unterscheiden sich je nach ausgewähltem Aktionstyp. Die HTTP-Aktion verfügt beispielsweise über die folgenden erforderlichen Eigenschaften:

   | Eigenschaft | BESCHREIBUNG |
   |----------|-------------|
   | **Status** | Der Status der Aktion, der zurückgegeben werden soll |
   | **Statuscode** | Der genaue Statuscode, der als Ausgabe zurückgeben werden soll |
   | **Headers** | Der Headerinhalt, der zurückgegeben werden soll |
   |||

   ![Screenshot, der die Registerkarte „Tests“ nach Auswahl von „Statisches Ergebnis aktivieren“ zeigt.](./media/test-logic-apps-mock-data-static-results/enable-static-result-standard.png)

   > [!TIP]
   > Wenn Sie die Werte im JSON-Format (JavaScript Object Notation) eingeben, wählen Sie **Zum JSON-Modus wechseln** (![Symbol für „Zum JSON-Modus wechseln“](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)) aus.

1. Optionale Eigenschaften finden Sie in der Liste **Optionale Felder auswählen**. Öffnen Sie diese, und wählen Sie die Eigenschaften aus, die simuliert werde sollen.

   ![Screenshot, der Bereich „Testen“ mit geöffneter Liste „Optionale Felder auswählen“ anzeigt.](./media/test-logic-apps-mock-data-static-results/optional-properties-standard.png)

1. Wählen Sie abschließend **Fertig** aus.

   In der unteren rechten Ecke der Aktion wird nun ein Bechersymbol für den Test angezeigt (![Symbol für statisches Ergebnis](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), das angibt, dass die statischen Ergebnisse aktiviert sind.

   ![Screenshot, der eine Aktion mit dem Symbol für statische Ergebnisse auf dem Designer zeigt.](./media/test-logic-apps-mock-data-static-results/static-result-enabled-standard.png)

   Informationen dazu, wie Sie Workflow-Ausführungen finden, bei denen Simulationsdaten verwendet werden, erhalten Sie weiter unten in diesem Artikel im Abschnitt [Suchen von Ausführungen mit statischen Ergebnissen](#find-runs-mock-data).

---

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-mock-data"></a>Ausführungen suchen, die Modelldaten verwenden

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

Um frühere Workflow-Ausführungen zu finden, bei denen die Aktionen Modelldaten verwenden, überprüfen Sie den Ausführungsverlauf dieses Workflows.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie im Ressourcenmenü Ihrer Logik-App die Option **Übersicht** aus.

1. Wählen Sie im Abschnitt **Essentials** die Option **Ausführungsverlauf** aus, sofern noch nicht ausgewählt.

1. Suchen Sie in der Tabelle **Ausführungsverlauf** die Spalte **Statische Ergebnisse**.

   Bei jeder Ausführung, die Aktionen mit Modelldatenausgabe umfasst, ist die Spalte **Statische Ergebnisse** auf **Aktiviert** festgelegt. Z. B.:

   ![Screenshot, der den Workflow-Ausführungsverlauf mit der Spalte „Statische Ergebnisse“ anzeigt.](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Wenn Sie diese Aktionen in einer Ausführung anzeigen möchten, die Modelldaten verwendet, wählen Sie die Ausführung aus, bei der die Spalte **Statische Ergebnisse** **aktiviert** ist.

   Aktionen, die statische Ergebnisse verwenden, zeigen das Bechersymbol für den Test (![Symbol für statisches Ergebnis](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)), zum Beispiel:

   ![Screenshot, der den  Workflow-Ausführungsverlauf mit Aktionen zeigt, die statische Ergebnisse verwenden.](./media/test-logic-apps-mock-data-static-results/static-result-enabled-run-details.png)

### <a name="standard"></a>[Standard](#tab/standard)

Um andere Workflow-Ausführungen zu finden, bei denen die Aktionen Modelldaten verwenden, müssen Sie jede Ausführung überprüfen.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie im Menü „Workflow“ die Option **Übersicht** aus.

1. Wählen Sie im Abschnitt **Essentials** die Option **Ausführungsverlauf** aus, sofern noch nicht ausgewählt.

1. Wählen Sie in der Tabelle **Ausführungsverlauf** die Ausführung, die Sie überprüfen möchten.

   ![Screenshot, der den Workflow-Ausführungsverlauf anzeigt.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. Überprüfen Sie im Bereich „Ausführungsdetails“, ob Aktionen das Bechersymbol für den Test (![Symbol für statisches Ergebnis](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)) anzeigen, zum Beispiel:

   ![Screenshot, der den  Workflow-Ausführungsverlauf mit Aktionen zeigt, die statische Ergebnisse verwenden.](./media/test-logic-apps-mock-data-static-results/run-history-static-result-standard.png)

---

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs-as-mock-data"></a>Wiederverwenden vorheriger Ausgaben als Modelldaten

Wenn Sie einen vorherigen Workflow mit Ausgaben ausgeführt haben, können Sie diese Ausgaben als Modelldaten wiederverwenden, indem Sie diese Ausgaben aus dieser Ausführung kopieren und einfügen.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie im Ressourcenmenü Ihrer Logik-App die Option **Übersicht** aus.

1. Wählen Sie im Abschnitt **Essentials** die Option **Ausführungsverlauf** aus, sofern noch nicht ausgewählt. Wählen Sie in der angezeigten Liste die gewünschte Workflow-Ausführung aus.

   ![Screenshot, der den Workflow-Ausführungsverlauf anzeigt.](./media/test-logic-apps-mock-data-static-results/select-run.png)

1. Nachdem der Bereich mit den Ausführungsdetails geöffnet wurde, erweitern Sie die Aktion mit den von Ihnen angezeigten Ausgaben.

1. Wählen Sie im Abschnitt **Ausgaben** die Option **unformatierte Ausgaben anzeigen** aus.

1. Kopieren Sie im Bereich **Ausgaben** entweder das ganze JSON-Objekt oder den genauen Unterabschnitt, den Sie verwenden möchten, z. B. den Abschnitt „Ausgaben“ oder sogar nur den Abschnitt „Header“.

1. Lesen Sie den früheren Abschnitt zum Einrichten [von Modelldaten](#enable-mock-data) für eine Aktion, und führen Sie die Schritte aus, um den Bereich **Tests** der Aktion zu öffnen.

1. Wählen Sie nach dem Öffnen des Bereichs **Tests** einen der beiden Schritte aus:

   * Klicken Sie zum Einfügen eines vollständigen JSON-Objekts neben der Bezeichnung **Tests** auf **In JSON-Modus wechseln**![ (Symbol für „In JSON-Modus wechseln“](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Screenshot, der das ausgewählte Symbol „In JSON-Modus wechseln“ anzeigt, um das vollständige JSON-Objekt einzufügen.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Wenn Sie nur einen JSON-Abschnitt, wie **Ausgabe** oder **Header**, einfügen möchten, wählen Sie **In JSON-Modus wechseln** aus, z. B.:

     ![Screenshot, der das ausgewählte Symbol „In JSON-Modus wechseln“ anzeigt, um einen Abschnitt aus einem JSON-Objekt einzufügen.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output.png)

1. Fügen Sie das zuvor kopierte JSON-Objekt in den JSON-Editor ein.

   ![Screenshot, der den im Editor gespeicherten JSON-Code zeigt.](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. Klicken Sie auf **Fertig**, wenn Sie fertig sind. Stattdessen können Sie auch **Editor-Modus wechseln** (![Symbol für „Editor-Modus wechseln“](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) auswählen, um zum Designer zurückzukehren.

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer.

1. Wählen Sie im Menü „Workflow“ die Option **Übersicht** aus.

1. Wählen Sie im Abschnitt **Essentials** die Option **Ausführungsverlauf** aus, sofern noch nicht ausgewählt.

1. Wählen Sie in der Tabelle **Ausführungsverlauf** die Ausführung, die Sie überprüfen möchten.

   ![Screenshot, der den Workflow-Ausführungsverlauf anzeigt.](./media/test-logic-apps-mock-data-static-results/select-run-standard.png)

1. Nachdem der Bereich mit den Ausführungsdetails geöffnet wurde, wählen Sie die Aktion mit den von Ihnen angezeigten Ausgaben.

1. Wählen Sie im Abschnitt **Ausgaben** die Option **unformatierte Ausgaben anzeigen** aus.

1. Kopieren Sie im Bereich **Ausgaben** entweder das ganze JSON-Objekt oder den genauen Unterabschnitt, den Sie verwenden möchten, z. B. den Abschnitt „Ausgaben“ oder sogar nur den Abschnitt „Header“.

1. Lesen Sie den früheren Abschnitt zum Einrichten [von Modelldaten](#enable-mock-data) für eine Aktion, und führen Sie die Schritte aus, um die Registerkarte **Tests** der Aktion zu öffnen.

1. Wählen Sie nach dem Öffnen der Registerkarte **Tests** einen der beiden Schritte aus:

   * Klicken Sie zum Einfügen eines vollständigen JSON-Objekts neben der Bezeichnung **Tests** auf **In JSON-Modus wechseln**![ (Symbol für „In JSON-Modus wechseln“](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Screenshot, der das ausgewählte Symbol „In JSON-Modus wechseln“ anzeigt, um das vollständige JSON-Objekt einzufügen.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete-standard.png)

   * Wenn Sie nur einen JSON-Abschnitt, wie **Ausgabe** oder **Header**, einfügen möchten, wählen Sie **In JSON-Modus wechseln** aus, z. B.:

     ![Screenshot, der das ausgewählte Symbol „In JSON-Modus wechseln“ anzeigt, um einen Abschnitt aus einem JSON-Objekt einzufügen.](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-output-standard.png)

1. Fügen Sie das zuvor kopierte JSON-Objekt in den JSON-Editor ein.

   ![Screenshot, der den im Editor gespeicherten JSON-Code zeigt.](./media/test-logic-apps-mock-data-static-results/json-editing-mode-standard.png)

1. Klicken Sie auf **Fertig**, wenn Sie fertig sind. Stattdessen können Sie auch **Editor-Modus wechseln** (![Symbol für „Editor-Modus wechseln“](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)) auswählen, um zum Designer zurückzukehren.

---

## <a name="disable-mock-data"></a>Deaktivieren von Modelldaten

Wenn Sie statische Ergebnisse für eine Aktion deaktivieren, werden die Werte nicht aus dem letzten Setup entfernt. Wenn Sie also das statische Ergebnis für dieselbe Aktion erneut aktivieren, können Sie ihre vorherigen Werte weiterhin verwenden.

### <a name="consumption"></a>[Verbrauch](#tab/consumption)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer. Suchen Sie die Aktion, für die Sie Modelldaten deaktivieren möchten.

1. Wählen Sie in der rechten oberen Ecke der Aktion das Becherglassymbol (![Symbol für statisches Ergebnis](./media/test-logic-apps-mock-data-static-results/static-result-test-beaker-icon.png)).

   ![Screenshot, der eine Aktion und das ausgewählte Bechersymbol für Tests zeigt.](./media/test-logic-apps-mock-data-static-results/disable-static-result.png)

1. Wählen Sie **Statisches Ergebnis deaktivieren** > **Fertig**.

   ![Screenshot, der die ausgewählte Option „Statisches Ergebnis deaktivieren“ zeigt.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button.png)

### <a name="standard"></a>[Standard](#tab/standard)

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) den Logik-App-Workflow im Designer. Wählen Sie die Aktion, für die Sie Modelldaten deaktivieren möchten.

1. Wählen Sie im Bereich „Aktionsdetails“ die Registerkarte **Tests** aus.

1. Wählen Sie **Statisches Ergebnis deaktivieren** > **Fertig**.

   ![Screenshot, der die ausgewählte Option „Statisches Ergebnis deaktivieren“ als Standard zeigt.](./media/test-logic-apps-mock-data-static-results/disable-static-result-button-standard.png)

---

## <a name="reference"></a>Verweis

Weitere Informationen zu dieser Einstellung in Ihren zugrunde liegenden Workflowdefinitionen finden Sie unter [Statische Ergebnisse – Schemareferenz für Workflowdefinitionssprache](logic-apps-workflow-definition-language.md#static-results) und [runtimeConfiguration.staticResult – Laufzeitkonfigurationseinstellungen](logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu [Azure Logic Apps](logic-apps-overview.md)