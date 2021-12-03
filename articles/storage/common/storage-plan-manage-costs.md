---
title: Planen und Verwalten von Kosten für Azure Blob Storage
description: Hier erfahren Sie, wie Sie Kosten für Azure Blob Storage mithilfe der Kostenanalyse im Azure-Portal planen und verwalten.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: 8cb7f667f2898d1508df2f58a3aea2ced68582e1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131474797"
---
# <a name="plan-and-manage-costs-for-azure-blob-storage"></a>Planen und Verwalten von Kosten für Azure Blob Storage

Dieser Artikel unterstützt Sie beim Planen und Verwalten von Kosten für Azure Blob Storage. Schätzen Sie zuerst die Kosten mithilfe des Azure-Preisrechners. Nachdem Sie Ihr Speicherkonto erstellt haben, optimieren Sie es, damit Sie nur für Ressourcen bezahlen, die Sie benötigen. Verwenden Sie Kostenverwaltungsfunktionen zur Festlegung von Budgets und Überwachung von Kosten. Sie können auch vorhergesagte Kosten überprüfen und Ausgabentrends überwachen, um die Bereiche zu identifizieren, in denen ggf. Maßnahmen erforderlich sind.

Beachten Sie, dass Kosten für Blob Storage nur einen Teil der monatlichen Kosten in Ihrer Azure-Rechnung ausmachen. Zwar wird in diesem Artikel das Schätzen und Verwalten von Kosten für Blob Storage erläutert, doch werden Ihnen alle Azure-Dienste und -Ressourcen in Rechnung gestellt, die für Ihr Azure-Abonnement verwendet werden, einschließlich der Dienste von Drittanbietern. Nachdem Sie sich mit der Verwaltung der Kosten für Blob Storage vertraut gemacht haben, können Sie ähnliche Methoden zur Verwaltung der Kosten für alle Azure-Dienste anwenden, die unter Ihrem Abonnement genutzt werden.

## <a name="estimate-costs"></a>Schätzen der Kosten

Verwenden Sie den [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/), um die Kosten zu schätzen, bevor Sie ein Azure Storage-Konto erstellen und Daten dorthin übertragen.

1. Wählen Sie auf der Seite [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) die Kachel **Speicherkonten** aus.

2. Scrollen Sie auf der Seite nach unten, und suchen Sie den Abschnitt **Speicherkonten** in Ihrer Schätzung.

3. Wählen Sie Optionen aus den Dropdownlisten aus.

   Wenn Sie Werte in diesen Dropdownlisten ändern, ändert sich auch die Kostenschätzung. Diese Schätzung wird in der oberen Ecke und am unteren Rand angezeigt.

   ![Screenshot Ihrer Schätzung](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   Wenn Sie den Wert in der Dropdownliste **Typ** ändern, ändern sich auch andere Optionen, die auf diesem Arbeitsblatt angezeigt werden. Verwenden Sie die Links im Abschnitt **Weitere Informationen**, um mehr darüber zu erfahren, was die einzelnen Optionen bedeuten und wie sich diese Optionen auf den Preis von speicherbezogenen Vorgängen auswirken.

4. Ändern Sie die übrigen Optionen, um die jeweilige Auswirkung auf die Schätzung anzuzeigen.

## <a name="understand-the-full-billing-model-for-azure-blob-storage"></a>Grundlegende Informationen zum vollständigen Abrechnungsmodell für Azure Blob Storage

Azure Blob Storage wird auf der Azure-Infrastruktur ausgeführt, für die Kosten anfallen, wenn Sie neue Ressourcen bereitstellen. Es ist wichtig zu verstehen, dass möglicherweise andere zusätzliche Infrastrukturkosten anfallen.

### <a name="how-youre-charged-for-azure-blob-storage"></a>Die Gebühren für Azure Blob Storage

Wenn Sie Blob Storage-Ressourcen erstellen oder verwenden, werden Ihnen die folgenden Verbrauchseinheiten in Rechnung gestellt:

| Zähler | Einheit |
|---|---|
| Datenspeicher | Pro GB/pro Monat|
| Operationen (Operations) | Pro Transaktion |
| Datenübertragung | Pro GB |
| Metadaten | Pro GB/pro Monat<sup>1 |
| Blobindextags | Pro Tag<sup>2  |
| Änderungsfeed | Pro protokollierter Änderung<sup>2 |
| Verschlüsselungsbereiche | Pro Monat<sup>2 |
| Abfragebeschleunigung | Pro GB geprüft & pro GB zurückgegeben |

<sup>1</sup> Gilt nur für Konten mit einem hierarchischen Namespace.<br />
<sup>2</sup> Gilt nur, wenn Sie das Feature aktivieren.<br />

Für den Datenverkehr können auch Netzwerkkosten anfallen. Weitere Informationen finden Sie in der [Preisübersicht für Bandbreite](https://azure.microsoft.com/pricing/details/data-transfers/).

Am Ende Ihres Abrechnungszeitraums werden die Gebühren für die einzelnen Verbrauchseinheiten summiert. Ihre Rechnung zeigt einen Abschnitt für alle Azure Blob Storage-Kosten an. Für jede Verbrauchseinheit besteht ein separates Zeilenelement.

Datenspeicher und Metadaten werden monatlich pro GB abgerechnet. Für Daten und Metadaten, die weniger als einen Monat lang gespeichert werden, können Sie die Auswirkung auf Ihre monatliche Rechnung abschätzen, indem Sie die Kosten der jeweiligen GB pro Tag berechnen. Sie können einen ähnlichen Ansatz verwenden, um die Kosten von Verschlüsselungsbereichen abzuschätzen, die weniger als einen Monat lang verwendet werden. Die Anzahl der Tage in einem bestimmten Monat ist nicht konstant. Um die beste Schätzung Ihrer Kosten in einem bestimmten Monat zu erhalten, sollten Sie daher die monatlichen Kosten durch die Anzahl der Tage im betreffenden Monat teilen.

### <a name="finding-the-unit-price-for-each-meter"></a>Ermitteln des Preises je Einheit für die jeweilige Verbrauchseinheit

Öffnen Sie die richtige Preisseite, um die Einheitspreise zu ermitteln. Wenn Sie das Feature für hierarchische Namespaces in Ihrem Konto aktiviert haben, finden Sie weitere Informationen auf der Seite mit den [Preisen für Azure Data Lake Storage Gen2](https://azure.microsoft.com/pricing/details/storage/data-lake/). Wenn Sie dieses Feature nicht aktiviert haben, finden Sie weitere Informationen auf der Seite mit den [Preisen für Blockblobs](https://azure.microsoft.com/pricing/details/storage/blobs/).

Wenden Sie auf der Preisseite die entsprechenden Filter für Redundanz, Region und Währung an. Die Preise für die einzelnen Verbrauchseinheiten werden in einer Tabelle angezeigt. Die Preise hängen von anderen Einstellungen in Ihrem Konto ab, z. B. Datenredundanzoptionen, Zugriffsebene und Leistungsstufe.

### <a name="flat-namespace-accounts-and-transaction-pricing"></a>Konten mit flachem Namespace und Transaktionspreise

Clients können eine Anforderung über den Blob Storage-Endpunkt oder den Data Lake Storage-Endpunkt Ihres Kontos senden. Weitere Informationen zu Endpunkten von Speicherkonten finden Sie im Artikel über [Endpunkte von Speicherkonten ](storage-account-overview.md#storage-account-endpoints).

Transaktionspreise, die auf der Seite für [Blockblobpreise](https://azure.microsoft.com/pricing/details/storage/blobs/) angezeigt werden, gelten nur für Anforderungen, die den Blob Storage-Endpunkt verwenden (z. B. `https://<storage-account>.blob.core.windows.net`). Die aufgeführten Preise gelten nicht für Anforderungen, die den Data Lake Storage Gen2-Endpunkt verwenden (z. B. `https://<storage-account>.dfs.core.windows.net`). Öffnen Sie die Seite [Azure Data Lake Storage Gen2 – Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/), und wählen Sie die Option **Flacher Namespace** aus, um die Transaktionspreise für diese Anforderungen zu ermitteln.

> [!div class="mx-imgBorder"]
> ![Option „Flacher Namespace“](media/storage-plan-manage-costs/select-flat-namespace.png)

Anforderungen an den Data Lake Storage Gen2-Endpunkt können von folgenden Quellen stammen:

- Workloads, die den Azure Blob File System-Treiber oder [ABFS-Treiber](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) verwenden.

- REST-Aufrufe, die die [Azure Data Lake Store REST-API](/rest/api/storageservices/data-lake-storage-gen2) verwenden.

- Anwendungen, die Data Lake Storage Gen2-APIs aus einer Azure Storage-Clientbibliothek verwenden.

### <a name="using-azure-prepayment-with-azure-blob-storage"></a>Verwenden der Azure-Vorauszahlung mit Azure Blob Storage

Sie können Azure Blob Storage-Gebühren mit der Azure-Vorauszahlungsgutschrift (früher als Mindestverbrauch bezeichnet) bezahlen. Allerdings können Sie mit der Azure-Vorauszahlungsgutschrift keine Gebühren für Produkte und Dienste von Drittanbietern, einschließlich derjenigen aus dem Azure Marketplace, bezahlen.

## <a name="optimize-costs"></a>Optimieren von Kosten

Erwägen Sie eine Verwendung dieser Optionen zur Kostensenkung.

- Reservieren von Speicherkapazität

- Organisieren von Daten in Zugriffsebenen

- Automatisches Verschieben von Daten zwischen Zugriffsebenen

In diesem Abschnitt werden die einzelnen Optionen ausführlicher behandelt.

#### <a name="reserve-storage-capacity"></a>Reservieren von Speicherkapazität

Mit reservierter Azure Storage-Kapazität Sie können Geld bei den Speicherkosten für Blobdaten sparen. Reservierte Azure Storage-Kapazität bietet einen Rabatt auf die Kapazität für Blockblobs und Azure Data Lake Storage Gen2-Daten in Standardspeicherkonten, wenn Sie eine Reservierung für ein Jahr oder drei Jahre vornehmen. Eine Reservierung stellt eine feste Speicherkapazität für den Zeitraum der Reservierung zur Verfügung. Reservierte Azure Storage-Kapazität kann die Kapazitätskosten für Blockblobs und Azure Data Lake Storage Gen2-Daten erheblich verringern.

Weitere Informationen finden Sie unter [Optimieren der Kosten für Blob Storage mit reservierter Kapazität](../blobs/storage-blob-reserved-capacity.md).

#### <a name="organize-data-into-access-tiers"></a>Organisieren von Daten in Zugriffsebenen

Sie können Kosten senken, indem Sie Blobdaten in den kostengünstigsten Zugriffsebenen ablegen. Wählen Sie aus drei Ebenen aus, die zur Optimierung ihrer Kosten für die Datennutzung konzipiert sind. Beispielsweise hat die *heiße Ebene* höhere Speicherkosten, aber geringere Zugriffskosten. Wenn Sie also häufig auf Daten zugreifen möchten, ist die heiße Ebene möglicherweise die kostengünstigste Wahl. Wenn Sie weniger häufig auf Daten zugreifen möchten, ist die *kalte Ebene* oder die *Archivebene* möglicherweise am sinnvollsten, weil dabei zwar höhere Kosten für den Datenzugriff entstehen, gleichzeitig aber die Kosten für das Speichern von Daten gesenkt werden.

Weitere Informationen finden Sie unter [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](../blobs/access-tiers-overview.md?tabs=azure-portal).

#### <a name="automatically-move-data-between-access-tiers"></a>Automatisches Verschieben von Daten zwischen Zugriffsebenen

Verwenden Sie Richtlinien für die Lebenszyklusverwaltung, um Daten in regelmäßigen Abständen zwischen Ebenen zu verschieben und so die meisten Kosten zu sparen. Anhand dieser Richtlinien können Daten mithilfe der von Ihnen angegebenen Regeln verschoben werden. Beispielsweise könnten Sie eine Regel erstellen, die Blobs in die Archivebene verschiebt, wenn dieses Blob innerhalb von 90 Tagen nicht geändert wurde. Durch das Erstellen von Richtlinien, mit denen die Zugriffsebene für Ihre Daten angepasst wird, können Sie die kostengünstigsten Speicheroptionen für Ihre Anforderungen entwerfen.

Weitere Informationen finden Sie unter [Verwalten des Azure Blob Storage-Lebenszyklus](../blobs/lifecycle-management-overview.md?tabs=azure-portal).

## <a name="create-budgets"></a>Erstellen von Budgets

Sie können [Budgets](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) erstellen, um Kosten zu verwalten, und Warnungen erstellen, die die Beteiligten automatisch über Ausgabenanomalien und Überschreitungsrisiken informieren. Warnungen basieren auf Ausgaben im Vergleich zum Budget und zu Kostenschwellenwerten. Budgets und Warnungen werden für Azure-Abonnements und -Ressourcengruppen erstellt und sind daher im Rahmen einer umfassenden Strategie zur Kostenüberwachung hilfreich. Unter Umständen verfügen sie aber über eine eingeschränkte Funktionalität für die Verwaltung der Kosten einzelner Azure-Dienste, z. B. die Kosten für Azure Storage, weil diese Funktionen für die Nachverfolgung von Kosten auf höherer Ebene ausgelegt sind.

## <a name="monitor-costs"></a>Überwachen der Kosten

Wenn Sie Azure-Ressourcen mit Azure Storage verwenden, fallen Kosten an. Die Kosten pro Ressourcennutzungseinheit variieren nach dem Zeitintervall (Sekunden, Minuten, Stunden und Tage) oder der Einheitennutzung (Bytes, Megabytes usw.) Kosten fallen an, sobald mit der Nutzung von Azure Storage begonnen wird. Die Kosten werden im Bereich [Kostenanalyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) im Azure-Portal angezeigt.

Bei Verwendung der Kostenanalyse können Sie Azure Storage-Kosten in Diagrammen und Tabellen für unterschiedliche Zeitintervalle anzeigen. Beispiele hierfür sind „Tag“, „Aktueller Monat“, „Vorheriger Monat“ und „Jahr“. Außerdem können Sie Kosten nach Budgets und vorhergesagten Kosten anzeigen. Wenn Sie im Laufe der Zeit zu längeren Zeiträumen wechseln, können Sie Ausgabentrends ermitteln und erkennen, wo es ggf. zu hohen Ausgaben gekommen ist. Wenn Sie Budgets erstellt haben, können Sie auch leicht feststellen, wo diese überschritten wurden.

> [!NOTE]
> Die Kostenanalyse unterstützt verschiedene Arten von Azure-Kontotypen. Die vollständige Liste der unterstützten Kontotypen finden Sie unter [Grundlegendes zu Cost Management-Daten](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Um Kostendaten anzeigen zu können, müssen Sie mindestens über Lesezugriff auf Ihr Azure-Konto verfügen. Informationen zum Zuweisen des Zugriffs auf Azure Cost Management-Daten finden Sie unter [Zuweisen des Zugriffs auf Cost Management-Daten](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

So zeigen Sie Azure Storage-Kosten in der Kostenanalyse an:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Öffnen Sie das Fenster **Kostenverwaltung + Abrechnung**, und wählen Sie im Menü die Option **Kostenverwaltung** und dann **Kostenanalyse** aus. Anschließend können Sie den Bereich für ein bestimmtes Abonnement in der Dropdownliste **Bereich** ändern.

   ![Screenshot des Bereichs](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Um nur die Kosten für Azure Storage anzuzeigen, wählen Sie **Filter hinzufügen** und dann **Dienstname** aus. Wählen Sie dann **Speicher** aus der Liste aus.

   In diesem Beispiel werden nur die Kosten für Azure Storage angezeigt:

   ![Screenshot der Filterung nach Speicher](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

Im obigen Beispiel sind die aktuellen Kosten für den Dienst angegeben. Die Kosten nach Azure-Regionen (Standorten) und nach Ressourcengruppe werden ebenfalls angezeigt. Sie können auch andere Filter hinzufügen (beispielsweise einen Filter zum Anzeigen von Kosten für bestimmte Speicherkonten).

## <a name="export-cost-data"></a>Exportieren von Kostendaten

Sie können [Ihre Kostendaten](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) auch in ein Speicherkonto exportieren. Dies ist hilfreich, wenn Sie oder andere Personen zusätzliche Datenanalysen für Kosten ausführen müssen. Beispielsweise können Finanzteams die Daten mithilfe von Excel oder Power BI analysieren. Sie können Ihre Kosten täglich, wöchentlich oder monatlich exportieren und einen benutzerdefinierten Datumsbereich festlegen. Exportieren von Kostendaten ist die empfohlene Abrufmethode für Kostendatasets.

## <a name="faq"></a>Häufig gestellte Fragen

**Werden die Kosten anteilig berechnet, wenn ich Azure Storage nur einige Tage im Monat verwende?**

Die Speicherkapazität wird in Einheiten der durchschnittlichen täglich gespeicherten Datenmenge in Gigabyte (GB) für einen monatlichen Zeitraum abgerechnet. Wenn Sie beispielsweise in der ersten Hälfte des Monats durchweg 10 GB Speicher und in der zweiten Hälfte des Monats keinen Speicher nutzen, wird Ihnen eine Nutzung von 5 GB Speicher für diesen Monat in Rechnung gestellt.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die Preisgestaltung für Azure Storage. Siehe [Übersicht über die Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).
- [Optimieren Sie die Kosten für Blobspeicher mit reservierter Kapazität](../blobs/storage-blob-reserved-capacity.md).
- Erfahren Sie, wie Sie Ihre [Cloudinvestitionen mit Azure Cost Management optimieren](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie mehr über die Verwaltung von Kosten mit der [Kostenanalyse](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Erfahren Sie, wie Sie [unerwartete Kosten vermeiden](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Nehmen Sie an dem angeleiteten Kurs [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) teil.
