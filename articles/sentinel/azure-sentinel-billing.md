---
title: Planen und Verwalten von Kosten für Microsoft Sentinel
description: Erfahren Sie, wie Sie Kosten und Abrechnung für Microsoft Sentinel mithilfe der Kostenanalyse im Azure-Portal und anderer Methoden planen, verstehen und verwalten können.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.custom: subject-cost-optimization, ignite-fall-2021
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 8bd0b6aee85ffe7b5dcc2b646b90a0b1678e4dc6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524890"
---
# <a name="plan-and-manage-costs-for-microsoft-sentinel"></a>Planen und Verwalten von Kosten für Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel wird beschrieben, wie Sie die Kosten für Microsoft Sentinel planen und verwalten. Zunächst nutzen Sie den Azure-Preisrechner, um die Kosten für Microsoft Sentinel zu planen, bevor Sie dem Dienst Ressourcen hinzufügen. Überprüfen Sie dann beim Hinzufügen von Azure-Ressourcen die voraussichtlichen Kosten.

Nachdem Sie mit der Nutzung von Microsoft Sentinel-Ressourcen begonnen haben, können Sie mithilfe von Cost Management-Features Budgets festlegen und Kosten überwachen. Sie können auch die prognostizierten Kosten prüfen und Ausgabentrends erkennen, um Bereiche auszumachen, in denen Sie eventuell handeln sollten. In diesem Artikel werden verschiedene Möglichkeiten zur Verwaltung und Optimierung der Microsoft Sentinel-Kosten beschrieben.

Kosten für Microsoft Sentinel stellen nur einen Teil der monatlichen Kosten Ihrer Azure-Rechnung dar. Zwar wird in diesem Artikel das Planen und Verwalten der Kosten für Microsoft Sentinel erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die im Rahmen Ihres Azure-Abonnements verwendet werden, einschließlich Diensten von Partnern.

## <a name="prerequisites"></a>Voraussetzungen

- Um Kostendaten anzuzeigen und Kostenanalysen in Cost Management durchzuführen, müssen Sie über einen unterstützten Azure-Kontotyp verfügen, der mindestens Lesezugriff bietet.

    Die Kostenanalyse in Cost Management wird von den meisten, jedoch nicht allen Azure-Kontotypen unterstützt. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

    Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

- Sie müssen über Details zu Ihren Datenquellen verfügen. Microsoft Sentinel ermöglicht Ihnen das Einlesen von Daten aus einer oder mehreren Datenquellen. Einige dieser Datenquellen sind kostenlos, für andere werden Gebühren berechnet. Weitere Informationen finden Sie unter [Kostenlose Datenquellen](#free-data-sources).

## <a name="estimate-costs-before-using-microsoft-sentinel"></a>Schätzen der Kosten vor Nutzung von Microsoft Sentinel

Wenn Sie Microsoft Sentinel noch nicht einsetzen, können Sie mithilfe des [Microsoft Sentinel-Preisrechners](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel) die potenziellen Kosten schätzen. Geben Sie *Microsoft Sentinel* in das Feld „Suchen“ ein, und wählen Sie die resultierende Kachel „Microsoft Sentinel“ aus. Mit dem Preisrechner können Sie Ihre wahrscheinlichen Kosten auf Grundlage Ihres erwarteten Datenerfassungs- und -aufbewahrungsaufkommens schätzen.

Beispielsweise können Sie das tägliche Datenaufkommen (in GB), das Sie in Microsoft Sentinel erfassen möchten, und die Region für Ihren Arbeitsbereich eingeben. Der Rechner gibt die aggregierten monatlichen Kosten für diese Komponenten aus:

- Log Analytics-Datenerfassung
- Microsoft Sentinel-Datenanalyse
- Log Analytics-Datenaufbewahrung

> [!NOTE]
> Die in diesem Bild gezeigten Kosten dienen nur als Beispiel. Sie sollen keine tatsächlichen Kosten widerspiegeln.

:::image type="content" source="media/billing/pricing-calculator.png" alt-text="Screenshot eines Beispiels der geschätzten Kosten im Azure-Preisrechner." lightbox="media/billing/pricing-calculator.png" :::

## <a name="understand-the-full-billing-model-for-microsoft-sentinel"></a>Grundlegendes zum vollständigen Abrechnungsmodell für Microsoft Sentinel

Microsoft Sentinel bietet ein flexibles und berechenbares Preismodell. Weitere Informationen finden Sie auf der Seite [Microsoft Sentinel – Preise](https://azure.microsoft.com/pricing/details/azure-sentinel/). Die zugehörigen Log Analytics-Gebühren finden Sie unter [Azure Monitor Log Analytics – Preise](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft Sentinel wird in Azure-Infrastruktur ausgeführt, für die Kosten anfallen, sobald Sie neue Ressourcen bereitstellen. Es ist wichtig zu verstehen, dass möglicherweise andere zusätzliche Infrastrukturkosten anfallen.

### <a name="how-youre-charged-for-microsoft-sentinel"></a>Rechnungsstellung für Microsoft Sentinel

Für den Dienst Microsoft Sentinel gibt es zwei Zahlungsoptionen: **Nutzungsbasierte Zahlung** und **Mindestabnahme**.

- **Nutzungsbasierte Zahlung** ist das Standardmodell, das auf dem tatsächlich gespeicherten Datenvolumen basiert und optional eine Datenaufbewahrung über 90 Tage hinaus vorsieht. Das Datenvolumen wird in GB (10^9 Bytes) gemessen.

- Log Analytics und Microsoft Sentinel bieten auch den Tarif **Mindestabnahme** (zuvor als „Kapazitätsreservierungen“ bezeichnet), der besser kalkulierbar ist und im Vergleich zu „Nutzungsbasierte Zahlung“ eine Ersparnis bis zu 65 % bringt.

    Mit dem Tarif „Mindestabnahme“ können Sie Kapazität ab 100 GB/Tag buchen. Sämtliche Nutzung, die die gebuchte Kapazität überschreitet, wird mit dem von Ihnen für die Mindestabnahme gewählten Satz in Rechnung gestellt. Bei einer Mindestabnahme von 100 GB/Tag wird beispielsweise das zugesagte Datenvolumen von 100 GB/Tag plus jedes weitere GB/Tag zum ermäßigten Satz für diesen Tarif in Rechnung gestellt.

    Sie können Ihre Mindestabnahme jederzeit erhöhen und alle 31 Tage verringern, um die Kosten zu optimieren, sobald Ihr Datenvolumen zu- oder abnimmt. Um Ihren aktuellen Microsoft Sentinel-Tarif einzusehen, wählen Sie im linken Navigationsbereich von Microsoft Sentinel **Einstellungen** und dann die Registerkarte **Preise** aus. Ihr aktueller Tarif ist als **Aktueller Tarif** markiert.

    Informationen zum Festlegen und Ändern Ihrer Mindestabnahme finden Sie unter [Festlegen oder Ändern des Tarifs](#set-or-change-pricing-tier).

### <a name="understand-your-microsoft-sentinel-bill"></a>Grundlegendes zur Microsoft Sentinel-Rechnung

Abrechenbare Verbrauchseinheiten sind die einzelnen Komponenten Ihres Diensts, die auf Ihrer Rechnung und auch in der Kostenanalyse unter Ihrem Dienst ausgewiesen werden. Am Ende Ihres Abrechnungszeitraums werden die Gebühren für die einzelnen Verbrauchseinheiten summiert. Ihre Rechnung enthält einen Abschnitt für alle Microsoft Sentinel-Kosten an. Für jede Verbrauchseinheit besteht ein separates Zeilenelement.

Um Ihre Azure-Rechnung einzusehen, wählen Sie im linken Navigationsbereich von **Cost Management + Billing** die Option **Kostenanalyse** aus. Wählen Sie auf dem Bildschirm **Kostenanalyse** den Dropdownpfeil im Feld **Ansicht** und dann **Rechnungsdetails** aus.

> [!NOTE]
> Die in diesem Bild gezeigten Kosten dienen nur als Beispiel. Sie sollen keine tatsächlichen Kosten widerspiegeln.

![Screenshot des Microsoft Sentinel-Abschnitts mit einer Azure-Beispielrechnung](media/billing/sample-bill.png)

Microsoft Sentinel- und Log Analytics-Gebühren werden auf Ihrer Azure-Rechnung basierend auf Ihrem ausgewählten Tarif als separate Posten ausgewiesen. Wenn Sie die Mindestabnahme für Ihren Arbeitsbereich in einem bestimmten Monat überschreiten, weist die Azure-Rechnung einen Einzelposten für die Mindestabnahme mit den damit verbundenen Fixkosten und einen separaten Einzelposten für die über die Mindestabnahme hinausgehende Erfassung aus, die zum gleichen Mindestabnahmesatz abgerechnet wird.

Die folgende Tabelle zeigt, wie Microsoft Sentinel- und Log Analytics-Kosten in den Spalten **Dienstname** und **Verbrauchseinheit** Ihrer Azure-Rechnung ausgewiesen werden:

| Cost | Dienstname | Zähler |
|--|--|--|
| Microsoft Sentinel-Mindestabnahme | `sentinel` | **`n` GB Mindestabnahme** |
| Log Analytics-Mindestabnahme | `azure monitor` | **`n` GB Mindestabnahme** |
| Microsoft Sentinel-Überschreitung der Mindestabnahme oder Nutzungsbasierte Zahlung| `sentinel` |**Analyse**|
| Log Analytics-Überschreitung der Mindestabnahme oder Nutzungsbasierte Zahlung| `log analytics` |**Datenerfassung**|

Weitere Informationen zum Anzeigen und Herunterladen Ihrer Azure-Rechnung finden Sie unter [Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren](../cost-management-billing/understand/download-azure-daily-usage.md).

### <a name="costs-for-other-services"></a>Kosten anderer Dienste

Microsoft Sentinel kann in viele andere Azure-Dienste integriert werden, um erweiterte Funktionen bereitzustellen. Zu diesen Diensten gehören Azure Logic Apps, Azure Notebooks und BYOML-Modelle (Bring Your Own Machine Learning). Für einige dieser Dienste fallen möglicherweise zusätzliche Gebühren an. Einige der Datenconnectors und -lösungen von Microsoft Sentinel verwenden für die Datenerfassung Azure Functions, wodurch ebenfalls gesonderte Kosten anfallen.

Preisdetails für diese Dienste finden Sie unter:

- [Azure Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/)
- [Azure Machine Learning – Preise](https://azure.microsoft.com/pricing/details/machine-learning/)
- [Machine Learning Studio (Classic) – Preise](https://azure.microsoft.com/pricing/details/machine-learning-studio/)
- [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/)

Für alle anderen Dienste, die Sie nutzen, können Kosten anfallen.

### <a name="data-retention-costs"></a>Datenaufbewahrungskosten

Nachdem Sie Microsoft Sentinel in einem Log Analytics-Arbeitsbereich aktiviert haben, können Sie alle im Arbeitsbereich erfassten Daten in den ersten 90 Tagen kostenlos aufbewahren. Eine Aufbewahrung über 90 Tage hinaus wird gemäß den standardmäßigen [Log Analytics-Aufbewahrungspreisen](https://azure.microsoft.com/pricing/details/monitor/) in Rechnung gestellt.

Sie können für einzelne Datentypen unterschiedliche Aufbewahrungseinstellungen angeben. Weitere Informationen finden Sie unter [Aufbewahrung nach Datentyp](../azure-monitor/logs/manage-cost-storage.md#retention-by-data-type).

### <a name="other-cef-ingestion-costs"></a>Andere CEF-Erfassungskosten

CEF ist ein unterstütztes Syslog-Ereignisformat in Microsoft Sentinel. Sie können CEF verwenden, um wertvolle Sicherheitsinformationen aus verschiedenen Quellen in Ihren Microsoft Sentinel-Arbeitsbereich einzubringen. CEF-Protokolle werden in die Tabelle „CommonSecurityLog“ in Microsoft Sentinel gesammelt, die alle standardmäßigen aktuellen CEF-Felder enthält.

Viele Geräte und Datenquellen lassen Protokollierungsfelder zu, die über das CEF-Standardschema hinausgehen. Diese zusätzlichen Felder werden in der Tabelle AdditionalExtensions angezeigt. Diese Felder können höhere Erfassungsvolumen als die CEF-Standardfelder haben, da der Ereignisinhalt in diesen Feldern variabel sein kann.

### <a name="costs-that-might-accrue-after-resource-deletion"></a>Nach dem Löschen von Ressourcen möglicherweise anfallende Kosten

Durch das Entfernen von Microsoft Sentinel werden weder der Log Analytics-Arbeitsbereich, in dem Microsoft Sentinel bereitgestellt wurde, noch etwaige separate Gebühren entfernt, die für diesen Arbeitsbereich anfallen könnten.

### <a name="free-trial"></a>Kostenlose Testversion

Testen Sie Microsoft Sentinel die ersten 31 Tage kostenlos. Microsoft Sentinel kann für einen Log Analytics-Arbeitsbereich in Azure Monitor ohne zusätzliche Kosten aktiviert werden. Dies gilt gemäß den unten angegebenen Grenzwerten:

- **Neue Log Analytics-Arbeitsbereiche** können für die ersten 31 Tage bis zu 10 GB pro Tag ohne Kosten nutzen. Neue Arbeitsbereiche umfassen Arbeitsbereiche, die weniger als drei Tage alt sind.

   Sowohl die Log Analytics-Datenerfassung als auch Microsoft Sentinel Gebühren entfallen während des 31-tägigen Testzeitraums. Diese kostenlose Testversion unterliegt einem Grenzwert von maximal 20 Arbeitsbereichen pro Azure-Mandant.

- **Vorhandene Log Analytics-Arbeitsbereiche** können Microsoft Sentinel ohne zusätzliche Kosten aktivieren. Vorhandene Arbeitsbereiche umfassen alle Arbeitsbereiche, die vor mehr als drei Tagen erstellt wurden.

   Nur die Microsoft Sentinel Gebühren entfallen während des 31-tägigen Testzeitraums.

Nutzungen, die diese Grenzwerte überschreiten, werden gemäß den Preisen berechnet, die auf der Seite [Microsoft Sentinel – Preise](https://azure.microsoft.com/pricing/details/azure-sentinel) aufgeführt sind. Gebühren im Zusammenhang mit Azure Monitor Log Analytics für die Datenerfassung und zusätzliche Funktionen für die Automatisierung sowie ‘Bringen Sie Ihr eigenes Maschinen-Lernen mit‘ gelten auch während des kostenlosen Testversionszeitraums weiter.

> [!TIP]
> Während Ihrer kostenlosen Testversion finden Sie Ressourcen für Kostenverwaltung, Schulungen und vieles mehr auf der Registerkarte  **Neues und Leitfäden> Kostenlose Testversion** in Microsoft Sentinel.
>
> Auf dieser Registerkarte werden auch Details zu den Datumsangaben Ihrer kostenlosen Testversion und zu der Anzahl der Tage angezeigt, die Sie bis zum Ablauf der Testversion noch haben.
>
### <a name="free-data-sources"></a>Kostenlose Datenquellen

Die folgenden Datenquellen lassen sich kostenlos mit Microsoft Sentinel nutzen:

- Azure-Aktivitätsprotokolle.
- Office 365-Überwachungsprotokolle, einschließlich aller SharePoint-Aktivitäten, Exchange-Administratoraktivitäten und Teams.
- Sicherheitswarnungen, einschließlich Warnungen von Microsoft Defender für Cloud, Microsoft 365 Defender, Microsoft Defender für Office 365, Microsoft Defender for Identity und Microsoft Defender für Endpunkt.
- Warnungen von Microsoft Defender für Cloud und Microsoft Defender for Cloud Apps. Rohprotokolle für einige Datentypen von Microsoft 365 Defender, Microsoft Defender for Cloud Apps, Azure Active Directory (Azure AD) und Azure Information Protection (AIP) sind jedoch kostenpflichtig.

In der folgenden Tabelle sind die kostenlosen Datenquellen aufgeführt, die Sie in Microsoft Sentinel aktivieren können. Einige der Datenconnectors, z. B. Microsoft 365 Defender und Defender for Cloud Apps, enthalten sowohl kostenlose als auch kostenpflichtige Datentypen.

| Microsoft Sentinel-Datenconnector   | Datentyp | Kostenlos oder kostenpflichtig |
|-------------------------------------|--------------------------------|------------------|
| **Azure-Aktivitätsprotokolle**         | AzureActivity                  | Kostenlos             |
| **Azure AD Identity Protection**         | SecurityAlert (IPC)                  | Kostenlos             |
| **Office 365**                     | OfficeActivity (SharePoint)    | Kostenlos|
|| OfficeActivity (Exchange)|Kostenlos|
|| OfficeActivity (Teams)          | Kostenlos|
| **Microsoft Defender für Cloud**                  | SecurityAlert (Defender für Cloud)             | Kostenlos             |
| **Microsoft Defender für IoT**          | SecurityAlert (Defender für IoT)     | Kostenlos             |
| **Microsoft 365 Defender**          | SecurityIncident | Kostenlos|
||SecurityAlert| Kostenlos|
||DeviceEvents                    | Kostenpflichtig|
||DeviceFileEvents                | Kostenpflichtig|
||DeviceImageLoadEvents           | Kostenpflichtig|
||DeviceInfo                      | Kostenpflichtig|
||DeviceLogonEvents               | Kostenpflichtig|
||DeviceNetworkEvents             | Kostenpflichtig|
||DeviceNetworkInfo               | Kostenpflichtig|
||DeviceProcessEvents             | Kostenpflichtig|
||DeviceRegistryEvents            | Kostenpflichtig|
||DeviceFileCertificateInfo       | Kostenpflichtig|
| **Microsoft Defender für den Endpunkt** | SecurityAlert (MDATP)          | Kostenlos             |
| **Microsoft Defender for Identity** | SecurityAlert (AATP)           | Kostenlos             |
| **Microsoft Defender für Cloud-Apps**   | SecurityAlert (Defender for Cloud Apps)           | Kostenlos             |
||MCASShadowITReporting           | Kostenpflichtig|

Für Datenconnectors, die sowohl kostenlose als auch kostenpflichtige Datentypen enthalten, können Sie auswählen, welche Datentypen Sie aktivieren möchten.

![Screenshot der Seite „Datenconnector“ für Defender for Cloud Apps, auf der die kostenlosen Sicherheitswarnungen ausgewählt sind und die kostenpflichtige Option „MCASShadowITReporting“ nicht ausgewählt ist](media/billing/data-types.png)

Weitere Informationen über kostenlose und kostenpflichtige Datenquellen und Connectors finden Sie unter [Verbinden von Datenquellen](connect-data-sources.md).

> [!NOTE]
> In Public Preview aufgeführte Datenconnectors verursachen keine Kosten. Datenconnectors verursachen erst Kosten, sobald sie allgemein verfügbar sind.
>

## <a name="estimate-microsoft-sentinel-costs"></a>Schätzen der Microsoft Sentinel-Kosten

Wenn Sie Microsoft Sentinel noch nicht einsetzen, können Sie mithilfe des [Microsoft Sentinel-Preisrechners](https://azure.microsoft.com/pricing/calculator/?service=azure-sentinel) die potenziellen Kosten der Nutzung von Microsoft Sentinel schätzen. Geben Sie *Microsoft Sentinel* in das Feld „Suchen“ ein, und wählen Sie die resultierende Kachel „Microsoft Sentinel“ aus. Mit dem Preisrechner können Sie Ihre wahrscheinlichen Kosten auf Grundlage Ihres erwarteten Datenerfassungs- und -aufbewahrungsaufkommens schätzen.

Beispielsweise können Sie das tägliche Datenaufkommen (in GB), das Sie in Microsoft Sentinel erfassen möchten, und die Region für Ihren Arbeitsbereich eingeben. Der Rechner gibt die aggregierten monatlichen Kosten für diese Komponenten aus:

- Log Analytics-Datenerfassung
- Microsoft Sentinel-Datenanalyse
- Log Analytics-Datenaufbewahrung

## <a name="manage-and-monitor-microsoft-sentinel-costs"></a>Verwalten und Überwachen der Microsoft Sentinel-Kosten

Sobald Sie Azure-Ressourcen mit Microsoft Sentinel verwenden, fallen Kosten an. Die Kosten pro Azure-Ressourcennutzungseinheit variieren je nach Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder Einheitennutzung (Bytes und Megabytes). Sobald mit der Microsoft Sentinel-Nutzung begonnen wird, fallen Kosten an, die Sie in der [Kostenanalyse](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) einsehen können.

Bei der Kostenanalyse können Sie Microsoft Sentinel-Kosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Sie können Kosten auch im Vergleich mit Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln. Außerdem können Sie erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten werden.

Der Hub [Azure Cost Management + Billing](../cost-management-billing/costs/quick-acm-cost-analysis.md) bietet nützliche Funktionen. Nachdem Sie im Azure-Portal **Cost Management + Billing** geöffnet haben, wählen Sie im linken Navigationsbereich **Cost Management** aus. Wählen Sie dann den [Bereich](..//cost-management-billing/costs/understand-work-scopes.md) oder die Gruppe der zu untersuchenden Ressourcen aus, z. B. ein Azure-Abonnement oder eine Ressourcengruppe.

Auf dem Bildschirm **Kostenanalyse** werden detaillierte Ansichten Ihrer Azure-Nutzung und -Kosten gezeigt, auf die Sie eine Vielzahl von Steuerelementen und Filtern anwenden können.

So können Sie z. B. Diagramme Ihrer täglichen Kosten für einen bestimmten Zeitrahmen einsehen:

1. Wählen Sie im Feld **Ansicht** den Dropdownpfeil und dann **Akkumulierte Kosten** oder **Tägliche Kosten** aus.
1. Wählen Sie den Dropdownpfeil im Datumsfeld und dann einen Datumsbereich aus.
1. Wählen Sie den Dropdownpfeil neben **Granularität** und dann **Täglich** aus.

> [!NOTE]
> Datenerfassungsvolumen von Microsoft Sentinel werden in einigen Nutzungsdiagrammen im Portal unter **Einblicke in die Sicherheit** angezeigt.

In den Microsoft Sentinel-Tarifen sind Log Analytics-Gebühren nicht inbegriffen. Informationen zum Ändern Ihres gebuchten Tarifs für Log Analytics finden Sie unter [Ändern des Tarifs](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier).

Weitere Informationen finden Sie unter [Erstellen von Budgets](#create-budgets) und [Weitere Möglichkeiten zum Verwalten und Reduzieren von Microsoft Sentinel-Kosten](#other-ways-to-manage-and-reduce-microsoft-sentinel-costs).

### <a name="using-azure-prepayment-with-microsoft-sentinel"></a>Nutzen der Azure-Vorauszahlung mit Microsoft Sentinel

Sie können Microsoft Sentinel-Gebühren mit Azure-Vorauszahlungsguthaben begleichen. Sie können Azure-Vorauszahlungsguthaben jedoch nicht dazu nutzen, um Rechnungen an Drittorganisationen für deren Produkte und Dienste oder für Produkte aus Azure Marketplace zu begleichen.

### <a name="run-queries-to-understand-your-data-ingestion"></a>Ausführen von Abfragen zum Verstehen Ihrer Datenerfassung

Microsoft Sentinel nutzt eine umfangreiche Abfragesprache zum Analysieren, Interagieren mit und Ableiten von Erkenntnissen aus großen Mengen operativer Daten in Sekundenschnelle. Es folgen einige Kusto-Abfragen, mit deren Hilfe Sie das Datenerfassungsvolumen verstehen können.

Führen Sie die folgende Abfrage aus, um das Datenerfassungsvolumen nach Lösung anzuzeigen:

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| render columnchart
```

Führen Sie die folgende Abfrage aus, um das Datenerfassungsvolumen nach Datentyp anzuzeigen:

```kusto
Usage
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType
| render columnchart
```

Führen Sie die folgende Abfrage aus, um das Datenerfassungsvolumen nach Lösung und Datentyp anzuzeigen:

```kusto
Usage
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| extend Solution = iif(Solution == "SecurityInsights", "AzureSentinel", Solution)
| sort by Solution asc, DataType asc
```

### <a name="deploy-a-workbook-to-visualize-data-ingestion"></a>Bereitstellen einer Arbeitsmappe zum Visualisieren der Datenerfassung

Die **Arbeitsmappe „Bericht zur Arbeitsbereichsnutzung“** stellt Datennutzungs-, Kosten- und Nutzungsstatistiken für Ihren Arbeitsbereich bereit. Die Arbeitsmappe gibt den Status der Datenerfassung im Arbeitsbereich und die Menge der kostenlosen und abrechenbaren Daten an. Sie können mithilfe der Arbeitsmappenlogik Datenerfassung und Kosten überwachen sowie benutzerdefinierte Ansichten und regelbasierte Warnungen erstellen.

Diese Arbeitsmappe bietet auch detaillierte Erfassungsdetails. In der Arbeitsmappe sind die Daten in Ihrem Arbeitsbereich nach Datentabellen aufgeschlüsselt, und es werden Volumen pro Tabelle und Eintrag angegeben, damit Sie Ihre Erfassungsmuster besser verstehen können.

So aktivieren Sie die Arbeitsmappe „Bericht zur Arbeitsbereichsnutzung“

1. Wählen Sie in Microsoft Sentinel im linken Navigationsbereich **Bedrohungsmanagement** > **Arbeitsmappen** aus.
1. Geben Sie in der Suchleiste *Arbeitsbereichsnutzung* ein, und wählen Sie dann **Bericht zur Arbeitsbereichsnutzung** aus.
1. Wählen Sie **Vorlage anzeigen** aus, um die Arbeitsmappe unverändert zu verwenden, oder wählen Sie **Speichern** aus, um eine bearbeitbare Kopie der Arbeitsmappe zu erstellen. Wenn Sie eine Kopie speichern, wählen Sie **Gespeicherte Arbeitsmappe anzeigen** aus.
1. Wählen Sie in der Arbeitsmappe das **Abonnement** und den **Arbeitsbereich** aus, den Sie anzeigen möchten, und legen Sie dann **TimeRange** auf den Zeitrahmen fest, den Sie einsehen möchten. Sie können den Umschalter **Hilfe anzeigen** auf **Ja** festlegen, um in der Arbeitsmappe Erklärungen anzuzeigen.

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen weitere Datenanalysen im Hinblick Kosten ausführen müssen. Beispielsweise kann ein Finanzteam die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und [Warnungen](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich.

Sie können Budgets mit Filtern für bestimmte Ressourcen oder Dienste in Azure erstellen, wenn Sie mehr Detailgenauigkeit bei Ihrer Überwachung wünschen. Mit Filtern stellen Sie sicher, dass Sie nicht versehentlich neue Ressourcen erstellen, die Ihnen zusätzliche Kosten bereiten. Weitere Informationen zu den beim Erstellen eines Budgets verfügbaren Filteroptionen finden Sie unter [Gruppen- und Filteroptionen](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

### <a name="use-a-playbook-for-cost-management-alerts"></a>Verwenden eines Playbooks für Kostenverwaltungswarnungen

Sie können ein Playbook zur Kostenverwaltung erstellen, um Ihr Microsoft Sentinel-Budget zu steuern. Das Playbook sendet Ihnen eine Warnung, wenn der Microsoft Sentinel-Arbeitsbereich innerhalb eines bestimmten Zeitraums ein von Ihnen definiertes Budget überschreitet.

Die GitHub-Community für Microsoft Sentinel stellt das Playbook [`Send-IngestionCostAlert`](https://github.com/iwafula025/Azure-Sentinel/tree/master/Playbooks/Send-IngestionCostAlert) zur Kostenverwaltung auf GitHub zur Verfügung. Dieses Playbook wird durch einen Wiederholungstrigger aktiviert und bietet ein hohes Maß an Flexibilität. Sie können die Ausführungshäufigkeit, das Erfassungsvolumen und die auszulösende Meldung basierend auf Ihren Anforderungen steuern.

### <a name="define-a-data-volume-cap-in-log-analytics"></a>Festlegen einer Obergrenze für das Datenvolumen in Log Analytics

In Log Analytics können Sie eine tägliche Volumenobergrenze aktivieren, die die tägliche Erfassung für Ihren Arbeitsbereich einschränkt. Die tägliche Obergrenze kann Ihnen helfen, unerwartete Anstiege des Datenvolumens zu bewältigen, ihr Budget einzuhalten und ungeplante Gebühren zu begrenzen.

Um eine tägliche Volumenobergrenze festzulegen, wählen Sie im linken Navigationsbereich Ihres Log Analytics-Arbeitsbereichs **Nutzungs- und geschätzte Kosten** und dann Tägliche **Obergrenze** aus. Wählen Sie **Ein** aus, geben Sie eine tägliche Volumenobergrenze ein, und wählen Sie dann **OK** aus.

![Screenshot: Bildschirm „Nutzungs- und geschätzte Kosten“ und Fenster „Tägliche Obergrenze“.](media/billing/daily-cap.png)

Auf dem Bildschirm **Nutzung und geschätzte Kosten** sind auch der Trend des erfassten Datenvolumens in den letzten 31 Tagen und das gesamte gespeicherte Datenvolumen zu sehen.

> [!IMPORTANT]
> Die tägliche Obergrenze begrenzt nicht die Sammlung aller Datentypen. Sicherheitsdaten sind von der Obergrenze ausgenommen. Weitere Informationen zum Verwalten der täglichen Obergrenze in Log Analytics finden Sie unter [Verwalten des maximalen täglichen Datenvolumens](../azure-monitor/logs/manage-cost-storage.md#manage-your-maximum-daily-data-volume).

## <a name="other-ways-to-manage-and-reduce-microsoft-sentinel-costs"></a>Weitere Optionen zum Verwalten und Senken von Microsoft Sentinel-Kosten

So verwalten Sie Datenerfassung und -aufbewahrung

- [Wählen des Tarifs „Mindestabnahme“, um die Kosten basierend auf Ihrem Datenerfassungsvolumen zu optimieren](#set-or-change-pricing-tier)
- [Ablegen nicht sicherheitsrelevanter Daten in einem anderen Arbeitsbereich](#separate-non-security-data-in-a-different-workspace)
- [Optimieren der Log Analytics-Kosten mithilfe dedizierter Cluster](#optimize-log-analytics-costs-with-dedicated-clusters)
- [Senken langfristiger Datenaufbewahrungskosten mit Azure Data Explorer](#reduce-long-term-data-retention-costs-with-adx)
- [Verwenden von Datensammlungsregeln für sicherheitsrelevante Windows-Ereignisse](#use-data-collection-rules-for-your-windows-security-events)

### <a name="set-or-change-pricing-tier"></a>Festlegen oder Ändern des Tarifs

Zum Erzielen der höchsten Einsparungen überwachen Sie Ihr Erfassungsvolumen, um sicherzustellen, dass Sie die Mindestabnahme gebucht haben, die am ehesten mit Ihren Erfassungsvolumenmustern übereinstimmt. Sie können Ihre Mindestabnahme erhöhen oder verringern, um sie an sich ändernde Datenvolumen anzupassen.

Sie können Ihre Mindestabnahme jederzeit erhöhen, wodurch der zugehörige Zeitraum von 31 Tagen neu gestartet wird. Wenn Sie jedoch zur nutzungsbasierten Zahlung oder zu einer niedrigeren Mindestabnahme zurückkehren möchten, müssen Sie warten, bis der 31-tägige Mindestabnahmezeitraum abgelaufen ist. Die Abrechnung für Mindestabnahmen erfolgt täglich.

Um Ihren aktuellen Microsoft Sentinel-Tarif einzusehen, wählen Sie im linken Navigationsbereich von Microsoft Sentinel **Einstellungen** und dann die Registerkarte **Preise** aus. Ihr aktueller Tarif ist als **Aktueller Tarif** markiert.

Um die gebuchte Mindestabnahme zu ändern, wählen Sie auf der Preisseite einen der anderen Tarife und dann **Übernehmen** aus. Sie müssen in Microsoft Sentinel die Rolle **Mitwirkender** oder **Besitzer** haben, um den Tarif zu ändern.

![Screenshot der Seite „Preise“ in den Microsoft Sentinel-Einstellungen, wobei „Nutzungsbasierte Zahlung“ als aktueller Tarif angegeben ist](media/billing/pricing.png)

> [!NOTE]
> Datenerfassungsvolumen von Microsoft Sentinel werden in einigen Nutzungsdiagrammen im Portal unter **Einblicke in die Sicherheit** angezeigt.

In den Microsoft Sentinel-Tarifen sind Log Analytics-Gebühren nicht inbegriffen. Informationen zum Ändern Ihres gebuchten Tarifs für Log Analytics finden Sie unter [Ändern des Tarifs](../azure-monitor/logs/manage-cost-storage.md#changing-pricing-tier).

### <a name="separate-non-security-data-in-a-different-workspace"></a>Ablegen nicht sicherheitsrelevanter Daten in einem anderen Arbeitsbereich

Microsoft Sentinel analysiert alle Daten, die in für Microsoft Sentinel aktivierten Log Analytics-Arbeitsbereichen erfasst werden. Es empfiehlt sich ein separater Arbeitsbereich für nicht sicherheitsrelevante Betriebsdaten, um sicherzustellen, dass für diese keine Microsoft Sentinel-Kosten anfallen.

Wenn Sie Bedrohungen in Microsoft Sentinel suchen oder untersuchen, müssen Sie möglicherweise auf operative Daten zugreifen, die in diesen eigenständigen Azure Log Analytics-Arbeitsbereichen gespeichert sind. Sie können auf diese Daten zugreifen, indem Sie bei der Protokolluntersuchung und in Arbeitsmappen arbeitsbereichsübergreifende Abfragen verwenden. Sie können allerdings nur dann arbeitsbereichsübergreifende Analyseregeln und Hunting-Abfragen nutzen, wenn Microsoft Sentinel für alle Arbeitsbereiche aktiviert ist.

### <a name="optimize-log-analytics-costs-with-dedicated-clusters"></a>Optimieren der Log Analytics-Kosten mithilfe dedizierter Cluster

Wenn Sie in Ihrem Microsoft Sentinel-Arbeitsbereich bzw. in Arbeitsbereichen in derselben Region mindestens 500 GB pro Tag beanspruchen, sollten Sie zur Kostensenkung einen Umstieg auf einen dedizierten Log Analytics-Cluster in Betracht ziehen. Eine Mindestabnahme für einen dedizierten Log Analytics-Cluster aggregiert das Datenvolumen mehrerer Arbeitsbereiche, die zusammen mindestens 500 GB pro Tag erfassen.

Für dedizierte Log Analytics-Cluster gelten nicht die Microsoft Sentinel-Mindestabnahmen. Microsoft Sentinel-Kosten fallen im dedizierten Cluster weiter arbeitsbereichsbezogen an.

Sie können einem dedizierten Log Analytics-Cluster mehrere Microsoft Sentinel-Arbeitsbereiche hinzufügen. Der Einsatz eines dedizierten Log Analytics-Clusters für Microsoft Sentinel bietet verschiedene Vorteile:

- Arbeitsbereichsübergreifende Abfragen erfolgen schneller, wenn sich alle an der Abfrage beteiligten Arbeitsbereiche im dedizierten Cluster befinden. Es empfiehlt sich nach wie vor, so wenig Arbeitsbereiche wie möglich in Ihrer Umgebung zu betreiben, und für einen dedizierten Cluster gilt unverändert die [Obergrenze von 100 Arbeitsbereichen](../azure-monitor/logs/cross-workspace-query.md) für die Einbeziehung in eine einzelne arbeitsbereichsübergreifende Abfrage.

- Alle Arbeitsbereiche im dedizierten Cluster können die für den Cluster festgelegten Log Analytics-Mindestabnahme gemeinsam nutzen. Dadurch, dass keine separaten Log Analytics-Mindestabnahmen für jeden Arbeitsbereich verpflichtend sind, lassen sich Kosten einsparen und mehr Effizienz erzielen. Durch Aktivieren eines dedizierten Clusters verpflichten Sie sich zu einer Mindestabnahme für Log Analytics von 500 TB pro Tag.

Im Folgenden finden Sie einige weitere Überlegungen zum Umstieg auf einen dedizierten Cluster zur Kostenoptimierung:

- Die maximale Anzahl aktiver Cluster pro Region und Abonnement ist 2.
- Alle mit einem Cluster verknüpften Arbeitsbereiche müssen sich in der gleichen Region befinden.
- Mit einem Cluster können maximal 1000 Arbeitsbereiche verknüpft sein.
- Sie können die Verknüpfung eines Arbeitsbereichs mit Ihrem Cluster aufheben. Die Anzahl der Verknüpfungsvorgänge in einem bestimmten Arbeitsbereich ist innerhalb eines Zeitraums von 30 Tagen auf 2 begrenzt.
- Sie können einen vorhandenen Arbeitsbereich nicht in einen Cluster mit kundenseitig verwaltetem Schlüssel verschieben. Sie müssen den Arbeitsbereich im Cluster erstellen.
- Das Verschieben eines Clusters in eine andere Ressourcengruppe oder ein anderes Abonnement wird derzeit nicht unterstützt.
- Eine Verknüpfung eines Arbeitsbereichs mit einem Cluster schlägt fehl, wenn der Arbeitsbereich mit einem anderen Cluster verknüpft ist.

Weitere Informationen zu dedizierten Clustern finden Sie unter [Dedizierte Log Analytics-Cluster](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters).

### <a name="reduce-long-term-data-retention-costs-with-adx"></a>Senken langfristiger Datenaufbewahrungskosten mit Azure Data Explorer

Die Microsoft Sentinel-Datenaufbewahrung ist in den ersten 90 Tagen kostenlos. Um den Datenaufbewahrungszeitraum in Log Analytics zu ändern, wählen Sie im linken Navigationsbereich **Nutzungs- und geschätzte Kosten** und dann **Datenaufbewahrung** aus. Passen Sie anschließend den Schieberegler an.

Microsoft Sentinel-Sicherheitsdaten verlieren nach einigen Monaten möglicherweise einen Teil ihres Nutzens. SOC-Benutzer (Security Operations Center) müssen möglicherweise nicht so häufig auf ältere Daten zugreifen wie auf neuere, benötigen aber möglicherweise dennoch den Zugriff auf die Daten für sporadische Untersuchungen oder zu Prüfzwecken. Um Microsoft Sentinel-Kosten für Datenaufbewahrung zu senken, können Sie Azure Data Explorer für die langfristige Datenaufbewahrung zu geringeren Kosten verwenden. Azure Data Explorer bietet das richtige Gleichgewicht von Kosten und Nutzen älterer Daten, für die die Sicherheitsfunktionen von Microsoft Sentinel nicht mehr erforderlich sind.

Mit Azure Data Explorer können Sie Daten zu niedrigeren Kosten speichern, die Daten aber dennoch mit den gleichen KQL-Abfragen (Kusto Query Language) wie in Microsoft Sentinel erkunden. Sie können mit dem Proxyfeature von Azure Data Explorer auch plattformübergreifende Abfragen durchführen. Diese Abfragen aggregieren und korrelieren Daten, die auf Azure Data Explorer, Application Insights, Microsoft Sentinel und Log Analytics verteilt sind.

Weitere Informationen finden Sie unter [Integrieren von Azure Data Explorer für die langfristige Protokollaufbewahrung](store-logs-in-azure-data-explorer.md).

### <a name="use-data-collection-rules-for-your-windows-security-events"></a>Verwenden von Datensammlungsregeln für sicherheitsrelevante Windows-Ereignisse

Mit dem Connector [Windows-Sicherheitsereignisse](connect-windows-security-events.md?tabs=LAA) können Sie sicherheitsrelevante Ereignisse von jedem Computer mit Windows Server streamen, der mit Ihrem Microsoft Sentinel-Arbeitsbereich verbunden ist, einschließlich physischer, virtueller oder lokaler Server bzw. in jeder Cloud. Dieser Connector unterstützt den Azure Monitor-Agent, der mithilfe von Datensammlungsregeln die von den einzelnen Agents zu sammelnden Daten definiert.

Datensammlungsregeln ermöglichen Ihnen das Verwalten von Sammlungseinstellungen nach Maß, während Sie gleichzeitig eindeutige und begrenzte Konfigurationen für Untergruppen von Computern zulassen. Weitere Informationen finden Sie unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md).

Neben den vordefinierten Ereignismengen, die Sie für die Erfassung auswählen können, wie z. B. „Alle Ereignisse“, „Minimal“ oder „Allgemein“, können Sie mithilfe von Datenerfassungsregeln benutzerdefinierte Filter erstellen und bestimmte zu erfassende Ereignisse auswählen. Der Azure Monitor-Agent filtert anhand dieser Regeln die Daten aus der Quelle und erfasst nur die von Ihnen ausgewählten Ereignisse, während alles andere unberücksichtigt bleibt. Wenn Sie bestimmte zu erfassende Ereignisse auswählen, können Sie Kosten optimieren und sparen.

> [!NOTE]
> Die in diesem Bild gezeigten Kosten dienen nur als Beispiel. Sie sollen keine tatsächlichen Kosten widerspiegeln.

![Screenshot des Bildschirms „Kostenanalyse“ in „Cost Management + Billing“.](media/billing/cost-management.png)

Sie können auch weitere Steuerelemente anwenden. Um beispielsweise nur die mit Microsoft Sentinel verbundenen Kosten anzuzeigen, wählen Sie **Filter hinzufügen**, anschließend **Dienstname** und dann die Dienstnamen **Sentinel**, **Log Analytics** und **Azure Monitor** aus.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.
- Weitere Tipps zum Reduzieren des Log Analytics-Datenvolumens finden Sie unter [Tipps zum Reduzieren der Datenmenge](../azure-monitor/logs/manage-cost-storage.md#tips-for-reducing-data-volume).
