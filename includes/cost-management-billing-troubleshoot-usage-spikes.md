---
title: Datei einfügen
description: Datei einfügen
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: include
ms.date: 02/09/2021
ms.author: banders
ms.reviewer: isvargas
ms.custom: include file
ms.openlocfilehash: efd08ec98533bd78d6a7cc606e11cbde1b4e33e3
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "122338820"
---
## <a name="troubleshoot-usage-spikes"></a>Problembehandlung bei Nutzungsspitzen

In diesem Abschnitt erfahren Sie, wie eine Nutzungsspitze in Ihrer Azure-Nutzungsdatei angezeigt wird, wie Sie Nutzungsspitzen verhindern, wie Sie Ihre Ressourcen überwachen und wann Sie sich an den Azure-Support wenden sollten. Er richtet sich an Kunden mit einem Enterprise Agreement (EA) oder einer Microsoft-Kundenvereinbarung. Sie müssen über die Rolle „EA-Administrator“ oder „Abrechnungsadministrator“ verfügen. Weitere Informationen zu Berechtigungen finden Sie unter [Herunterladen oder Anzeigen Ihrer Azure-Rechnungen und täglichen Nutzungsdaten](../articles/cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Ein sprunghafter Anstieg Ihrer Azure-Nutzung oder unerwartete Gebühren für einen bestimmten Dienst oder eine bestimmte Ressource werden häufig durch einen Incident oder einen unbeabsichtigten Missbrauch verursacht.

In beiden Fällen müssen Sie die betroffenen Dienste und Ressourcen eingrenzen, bevor Sie sich an den Support wenden, damit Sie den richtigen Supportbereich auswählen können.

Sie müssen verstehen, dass es unwahrscheinlich ist, dass Microsoft die zugrunde liegende Ursache für eine erhöhte Nutzung und die damit verbundenen Gebühren ermitteln kann. Daher können Kunden ihre eigenen detaillierten Nutzungsdaten im Azure-Portal herunterladen.

Microsoft führt aufgrund von Sicherheitsbedenken und aus Datenschutzgründen keine Überwachung Ihrer bereitgestellten Azure-Ressourcen wie virtuelle Computer, Netzwerke oder Datenübertragungen durch. Microsoft versucht jedoch, Sie darüber zu informieren, wie Sie Ihre Azure-Nutzung überwachen können. Letztendlich liegt es in Ihrer Verantwortung, Ihre eigene Nutzung zu überwachen.

### <a name="what-a-spike-looks-like-in-the-usage-file"></a>Erscheinungsbild einer Spitze in der Nutzungsdatei

Nachdem Sie die in den vorherigen Abschnitten beschriebenen Filter angewendet haben, können Sie nach Anomalien suchen. Sie können beispielsweise eine Problembehandlung für eine Spitze bei der **Bandbreite** (Kategorie der Verbrauchseinheit) durchführen.

Fügen Sie **Produkt** und **Instanz-ID** (Ressourcen-ID bei einer Microsoft-Kundenvereinbarung) im Abschnitt „Zeilen“ des PivotTable-Tools ein. Fügen Sie dann **Kosten** unter „Werte“, **Abonnement-ID** unter „Filter“ und **Datum** unter „Spalten“ hinzu. Anschließend führen Sie einen Filtervorgang aus, damit nur Daten für eine Abonnement-ID angezeigt werden. Beispiel: `111111111111-1111-1111-111111111111`.

Die folgende Abbildung zeigt, wie eine Spitze bei der Bandbreite (Datenübertragungen) aussieht.

:::image type="content" source="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" alt-text="Screenshot von Excel mit einer Nutzungsspitze" lightbox="./media/cost-management-billing-troubleshoot-usage-spikes/data-usage-spike.png" :::

Die Spitze betrifft eine bestimmte Ressource. In diesem Fall zeigt Zeile 7 in der Excel-Datei die Kostenwerte für das Storage-Konto **storageaccountnameazurefile1**. Am 1. Oktober 2020 wiesen die Kosten einen Wert nahe null (0) USD auf (2.23043E-06, was 0,000002230431449 entspricht). Am 2. und 3. Oktober 2020 ist ein großer Anstieg zu beobachten. Hier stiegen die Kosten auf 10.000 und 28.000 USD. Am 4. Oktober 2020 kehrten die Kosten wieder zu einem Normalwert (9.29E-07) zurück.

In diesem Beispiel haben Sie die Ressource, die eine hohe Bandbreitengebühr verursachte, das Datum, an dem diese auftrat, und das spezifische Produkt (Regionsübergreifend – Ausgehende Datenübertragung – Europa) identifiziert. Stellen Sie fest, ob die Spitze durch eine große Datenübertragung verursacht wurde. Verwenden Sie die Informationen in den vorherigen Abschnitten, um die betroffene Ressource zu überprüfen.

Wenn Sie feststellen, dass an den genannten Tagen keine Übertragungen von der Ressource erfolgten, wenden Sie sich an ein Azure-Technikteam. Das Team kann dabei helfen festzustellen, ob das Problem durch einen Fehler oder Incident verursacht wurde. In diesem Beispiel ist die betroffene Ressource ein Storage Konto. Daher wenden Sie sich an das Azure Storage-Technikteam. Wenn die Spitze einen virtuellen Computer betrifft, wenden Sie sich entsprechend an das Azure Virtual Machines-Technikteam, um festzustellen, ob ein laufender Incident den Dienst für virtuelle Computer beeinträchtigt.

Liegt ein laufender Incident vor, stimmt sich das Azure-Technikteam mit dem Azure-Abrechnungsteam ab, um einen Kostenerstattungsantrag zu prüfen.

### <a name="tools-to-monitor-azure-usage"></a>Tools zum Überwachen der Azure-Nutzung

Sie können Ihre Kosten jederzeit mit Azure Cost Management verwalten und Budgets erstellen. Weitere Informationen finden Sie unter:

- [Verwalten von Kosten mit Azure-Budgets](../articles/cost-management-billing/manage/cost-management-budget-scenario.md)
- [Tutorial: Erstellen und Verwalten von Azure-Budgets](../articles/cost-management-billing/costs/tutorial-acm-create-budgets.md)

Für die Storage Nutzung wird empfohlen, das Tool Storage Analytics zu verwenden. Es ermöglicht Ihnen die Protokollierung pro Transaktion. Die Protokolle sind detailliert, aber Sie können auch selbst eine umfassende Ablaufverfolgung und ein Debugging durchführen. Weitere Informationen finden Sie unter:

- [Speicheranalyse](../articles/storage/common/storage-analytics.md)
- [Protokollformat der Speicheranalyse](/rest/api/storageservices/Storage-Analytics-Log-Format)
- [Aktivieren und Verwalten von Azure Storage Analytics-Protokollen (klassisch)](../articles/storage/common/manage-storage-analytics-logs.md)

Für die netzwerkbezogene Nutzung können Sie Netzwerkerfassungstools wie den [Netzwerkmonitor](https://www.microsoft.com/download/details.aspx?id=4865) oder Fiddler verwenden.

Bei Problemen im Zusammenhang mit virtuellen Computern mit einem Image des Windows-Betriebssystems können Sie das [Windows-Ereignisprotokoll](/windows/win32/wes/windows-event-log) verwenden.

Bei PaaS-Bereitstellungen (Platform-as-a-Service) [aktivieren Sie die Azure-Diagnose](../articles/cloud-services/cloud-services-dotnet-diagnostics.md) in der Anwendung.

Bei IaaS-Bereitstellungen (Infrastructure-as-a-Service) aktivieren Sie die [Ablaufverfolgung mit Windows Communication Foundation](/dotnet/framework/wcf/diagnostics/tracing/configuring-tracing).

Aktivieren Sie die [erweiterte Protokollierung für IIS 8.5](/iis/get-started/whats-new-in-iis-85/enhanced-logging-for-iis85).

[Aktivieren Sie die Diagnoseprotokollierung für Web-Apps in Azure App Service](../articles/app-service/troubleshoot-diagnostic-logs.md).

Wenn Sie weitere Details und Ratschläge für Ihre Situation benötigen, wenden Sie sich an Ihren Microsoft Customer Success Account Manager, um Unterstützung von einem Cloud Solution Architect zu erhalten.