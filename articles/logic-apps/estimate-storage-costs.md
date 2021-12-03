---
title: Abschätzen der Speicherkosten in Azure Logic Apps für nur einen Mandanten
description: Abschätzen der Speicherkosten für Ihre Workflows mithilfe des Azure Logic Apps-Speicherrechners.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: 995c620eeafc5a627a9f9d94733c80de47f2928d
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369396"
---
# <a name="estimate-storage-costs-for-workflows-in-single-tenant-azure-logic-apps"></a>Abschätzen der Speicherkosten für Workflows in Azure Logic Apps für nur einen Mandanten

Azure Logic Apps verwendet [Azure Storage](../storage/index.yml) für alle Speichervorgänge. In herkömmlichen Azure Logic Apps für *Multi-Mandanten* werden alle Speichernutzung und -kosten an die Azure Logic App gebunden. Jetzt können Sie in Azure Logic Apps mit *nur einem Mandanten* Ihr eigenes Speicherkonto verwenden. Diese Speicherkosten werden separat in Ihrer Azure-Abrechnung. Diese Funktionalität bietet Ihnen mehr Flexibilität und Kontrolle über Ihre Logik-App-Daten.

> [!NOTE]
> Dieser Artikel bezieht sich auf Workflows in der Umgebung von Azure Logic Apps mit nur einem Mandanten. Diese Workflows existieren in derselben Logik-App und in einem einzigen Mandanten, die sich denselben Speicherplatz teilen. Weitere Informationen finden Sie unter [Einzelmandant im Vergleich zu Multi-Mandanten und Integrationsserviceumgebung](single-tenant-overview-compare.md).

Die Speicherkosten ändern sich basierend auf den Workflow-Inhalten. Verschiedene Auslöser, Vorgänge und Nutzlasten führen zu unterschiedlichen Speichervorgängen und Anforderungen. In diesem Artikel wird beschrieben, wie Sie Ihre Speicherkosten abschätzen können, wenn Sie Ihr eigenes Azure Storage-Konto mit Logik-Apps, die nur auf einem Mandanten basieren, verwenden. Zunächst können Sie [die Anzahl der Speichervorgänge schätzen](#estimate-storage-needs), die Sie mithilfe des Logic Apps-Speicherrechners ausführen. Anschließend können Sie [Ihre möglichen Speicherkosten](#estimate-storage-costs) mithilfe dieser Zahlen im Azure-Preisrechner abschätzen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein Logik-Apps-Workflow basierend auf nur einem Mandanten. Sie können einen Workflow [mithilfe des Microsoft Azure-Portals](create-single-tenant-workflows-azure-portal.md) oder [mit Visual Studio Code](create-single-tenant-workflows-visual-studio-code.md) erstellen. Wenn Sie noch keinen Workflow besitzen, können Sie die kleinen, mittleren und großen Beispielworkflows im Speicherrechner verwenden.

* Ein Azure-Speicherkonto, das Sie mit Ihrem Workflow verwenden möchten. Falls Sie kein Speicherkonto besitzen, [Erstellen ein Speicherkonto](../storage/common/storage-account-create.md)

## <a name="get-your-workflows-json-code"></a>Beziehen des JSON-Codes Ihres Workflows

Wenn Sie ein Workflow abschätzen möchten, beziehen Sie den JSON-Code für Ihren Workflow:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Wechseln Sie zum **Logic-Apps**-Dienst und wählen Sie Ihren Workflow aus.

1. Wählen Sie im Menü Ihrer Logic-App unter **Entwicklungstools** die Option **Logic-App-Codeansicht**.

1. Kopieren Sie den JSON-Code des Workflows.

## <a name="estimate-storage-needs"></a>Abschätzen des Speicherbedarfs

1. Wechseln Sie zum [Logic Apps-Speicherrechner](https://logicapps.azure.com/calculator).

   :::image type="content" source="./media/estimate-storage-costs/storage-calculator.png" alt-text="Screenshot des Logic Apps-Speicherrechners mit der Eingabe-Benutzeroberfläche" lightbox="./media/estimate-storage-costs/storage-calculator.png":::.

1. Eingabe, Upload oder Auswahl des JSON-Code für einen Logik-App-Workflow basierend auf einem Mandanten.

   * Wenn Sie den Code im vorherigen Abschnitt kopiert haben, fügen Sie ihn in das Feld **worflow.json einfügen oder hochladen** ein. 
   * Wenn Sie Ihre **workflow.json**-Datei lokal gespeichert haben, wählen Sie die Option **Dateien durchsuchen** unter **Workflow** aus. Wählen Sie Ihre Datei und dann die Option **Öffnen** aus.
   * Wenn Sie noch keinen Workflow besitzen, wählen Sie unter **Workflow auswählen** einen der Beispielworkflows aus.

1. Überprüfen Sie die Optionen unter **Erweiterte Optionen**. Diese Einstellungen sind abhängig von Ihrem Workflowtyp und können Folgendes umfassen:

   * Eine Option um die Anzahl der Durchläufe Ihrer Schleifen einzugeben.
   * Eine Option zum Auswählen aller Vorgänge mit Nutzlasten über 32 KB.

1. Geben Sie unter **Monatliche Ausführungen** ein, wie oft Sie Ihren Workflow pro Monat ausführen.

1. Wählen Sie **Berechnen** aus und warten Sie, bis die Berechnung ausgeführt wird.

1. Überprüfen Sie die Schätzung der **Vorgangsanzahl** unter **Aufschlüsselung des Speicherbetriebs und Berechnungsschritte**.

    In den beiden Tabellen können Sie die geschätzte Anzahl der Vorgänge nach Lauf und nach Monat anzeigen. Die folgenden Vorgänge werden angezeigt:

    * **Blob (lesen)** für Azure Blob Storage-Lesevorgänge.
    * **Blob (Schreiben)** für Azure Blob Storage-Schreibvorgänge.
    * **Warteschlange** für Azure Queues-Warteschlangenklasse 2 Vorgänge.
    * **Tabellen** für Azure Table Storage-Vorgänge.

    Jeder Vorgang verfügt über eine minimale, maximale und „best geschätzte“Anzahl. Wählen Sie die relevanteste Zahl aus, die Sie für die [Schätzung Ihrer Speicherbetriebskosten](#estimate-storage-costs) basierend auf Ihrem individuellen Szenario verwenden möchten. Als Empfehlung verwenden Sie die „Best Guess“-Anzahl für die Genauigkeit. Sie können jedoch auch die maximale Anzahl verwenden, um sicherzustellen, dass Ihre Kostenschätzung einen Puffer umfasst.

    :::image type="content" source="./media/estimate-storage-costs/storage-calculator-results.png" alt-text="Screenshot: Logic Apps-Speicherrechner mit Ausgabe der geschätzten Vorgängen" lightbox="./media/estimate-storage-costs/storage-calculator-results.png":::.

## <a name="estimate-storage-costs"></a>Abschätzen der Speicherkosten

Nach dem Berechnen des [Speicherbedarfs Ihres Logik-App-Workflows](#estimate-storage-needs), können Sie Ihre möglichen monatlichen Speicherkosten abschätzen. Sie können die Preise für die folgenden Speichervorgangstypen abschätzen:

* [Lese- und Schreibvorgänge für Blob Storage](#estimate-blob-storage-operations-costs)
* [Speichervorgänge für Warteschlangen](#estimate-queue-operations-costs)
* [Speichervorgänge für Tabellen](#estimate-table-operations-costs)

### <a name="estimate-blob-storage-operations-costs"></a>Einschätzen der Kosten für Blobspeichervorgänge

So schätzen Sie die monatlichen Kosten für die Blobspeichervorgänge Ihrer Logik-App:

1. Wechseln Sie zum [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

1. Wählen Sie auf der Registerkarte **Produkte** die Option **Speicher** &gt; **Speicherkonten** aus. Oder geben Sie in dem Suchfeld **Suchleiste** **Speicherkonten** ein, und wählen Sie die entsprechende Kachel aus.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Screenshot des Azure-Preisrechners mit der Kachel zum Hinzufügen der Speicherkontenansicht." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. Wählen Sie in der Benachrichtigung über **Hinzugefügte Speicherkonten** die Option **Ansicht** aus, um den Abschnitt **Speicherkonten** des Rechners anzuzeigen. Oder wechseln Sie manuell zum Abschnitt **Speicherkonten**.

1. Wählen Sie unter **Region** die Region für Ihre Logik-App aus.

1. Wählen Sie unter **Blockblob Speicher** den **Typ** aus.

1. Wählen Sie Ihre Leistungsstufe unter **Leistungsstufe** aus.

1. Wählen Sie Wählen Sie Ihre Redundanzstufe unter **Redundanz** aus.

1. Passen Sie alle anderen Einstellungen nach Bedarf an.

1. Eingabe der tatsächlichen Anzahl Ihrer **Blob-(Schreib-)** Vorgänge aus dem Logic Apps-Speicherrechner, *geteilt durch 10.000* unter **Schreibvorgänge**. Dieser Schritt ist erforderlich, da der Rechner in Transaktionseinheiten für Speichervorgänge arbeitet.

1. Eingabe der tatsächlichen Anzahl Ihrer **Blob-(Lese-)** Vorgänge aus dem Logic Apps-Speicherrechner, *geteilt durch 10.000* unter **Lesevorgänge**. Dieser Schritt ist erforderlich, da der Rechner in Transaktionseinheiten für Speichervorgänge arbeitet.

1. Überprüfen Sie die abgeschätzten Kosten für Blobspeichervorgänge.

### <a name="estimate-queue-operations-costs"></a>Abschätzen der Kosten für Warteschlangenvorgänge

Abschätzen der monatlichen Kosten für die Warteschlangenvorgänge Ihrer Logik-App :

1. Wechseln Sie zum [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

1. Wählen Sie auf der Registerkarte **Produkte** die Option **Speicher** &gt; **Speicherkonten** aus. Oder geben Sie in dem Suchfeld **Suchleiste** **Speicherkonten** ein, und wählen Sie die entsprechende Kachel aus.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Screenshot des Azure-Preisrechners mit der Kachel zum Hinzufügen der Speicherkontenansicht." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. Wählen Sie in der Benachrichtigung über **Hinzugefügte Speicherkonten** die Option **Ansicht** aus, um den Abschnitt **Speicherkonten** des Rechners anzuzeigen. Oder wechseln Sie manuell zum Abschnitt **Speicherkonten**.

1. Wählen Sie unter **Region** die Region für Ihre Logik-App aus.

1. Wählen Sie unter **Azure Queue Storage** den **Typ** aus.

1. Wählen Sie unter **Speicherkontotyp** Ihren Speicherkontotyp aus.

1. Wählen Sie Wählen Sie Ihre Redundanzstufe unter **Redundanz** aus.

1. Geben Sie die **Warteschlangen**-Vorgangsnummer aus dem Logic Apps Speicherrechner *dividiert durch 10.000* unter **Warteschlangenklasse 2-Vorgänge** ein. Dieser Schritt ist erforderlich, da der Rechner in Transaktionseinheiten für Warteschlangenvorgänge arbeitet.

1. Überprüfen der geschätzten Kosten für Warteschlangenvorgänge.

### <a name="estimate-table-operations-costs"></a>Abschätzen der Kosten für Tabellenvorgänge

Abschätzen der monatlichen Kosten für die Tabellenspeichervorgänge Ihrer Logik-App:

1. Wechseln Sie zum [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/).

1. Wählen Sie auf der Registerkarte **Produkte** die Option **Speicher** &gt; **Speicherkonten** aus. Oder geben Sie in dem Suchfeld **Suchleiste** **Speicherkonten** ein, und wählen Sie die entsprechende Kachel aus.

   :::image type="content" source="./media/estimate-storage-costs/pricing-calculator-storage-tile.png" alt-text="Screenshot des Azure-Preisrechners mit der Kachel zum Hinzufügen der Speicherkontenansicht." lightbox="./media/estimate-storage-costs/pricing-calculator-storage-tile.png":::

1. Wählen Sie in der Benachrichtigung über **Hinzugefügte Speicherkonten** die Option **Ansicht** aus, um den Abschnitt **Speicherkonten** des Rechners anzuzeigen. Oder wechseln Sie manuell zum Abschnitt **Speicherkonten**.

1. Wählen Sie unter **Region** die Region für Ihre Logik-App aus.

1. Wählen Sie unter **Table Storage** den **Typ** aus.

1. Wählen Sie unter **Ebene** Ihre Leistungsstufe aus.

1. Wählen Sie Wählen Sie Ihre Redundanzstufe unter **Redundanz** aus.

1. Geben Sie Ihre  **Tabellen**-Vorgangsnummer aus dem Logic Apps Speicherrechner *dividiert durch 10.000* unter **Speichertransaktionen** ein. Dieser Schritt ist erforderlich, da der Rechner in Transaktionseinheiten für Warteschlangenvorgänge arbeitet.

1. Überprüfen der abgeschätzten Kosten für Table Storage-Vorgänge.

## <a name="next-step"></a>Nächster Schritt

> [!div class="nextstepaction"]
> [Planen und Verwalten von Kosten für Logic Apps](plan-manage-costs.md)