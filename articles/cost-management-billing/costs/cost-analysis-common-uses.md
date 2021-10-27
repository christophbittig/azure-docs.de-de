---
title: Häufige Anwendungsbereiche der Kostenanalyse in Azure Cost Management
description: In diesem Artikel wird erläutert, wie Sie in Cost Management Ergebnisse für gängige Kostenanalyseaufgaben erhalten.
author: bandersmsft
ms.author: banders
ms.date: 10/13/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 642c9ba475690b05723601f4e6ff429e382f29bb
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992489"
---
# <a name="common-cost-analysis-uses"></a>Häufige Anwendungsbereiche der Kostenanalyse

Benutzer von Cost Management benötigen häufig Antworten auf Fragen, die auch von vielen anderen gestellt werden. In diesem Artikel wird Schritt für Schritt erläutert, wie Sie in Azure Cost Management Ergebnisse für häufige Kostenanalyseaufgaben erhalten.

## <a name="view-forecasted-costs"></a>Anzeigen prognostizierter Kosten

Prognostizierte Kosten werden in Kostenanalysebereichen für Flächen- und gestapelte Säulenansichten angezeigt. Die Prognose beruht auf Ihrer bisherigen Ressourcenverwendung. Änderungen an der Ressourcenverwendung wirken sich auf die prognostizierten Kosten aus.

Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.

In der Standardansicht umfasst das obere Diagramm die Bereiche für tatsächliche/amortisierte Kosten und prognostizierte Kosten. Die Volltonfarbe des Diagramms zeigt die tatsächlichen/amortisierten Kosten an. Die schattierte Farbe zeigt die prognostizierten Kosten an.

[![Prognostizierte Kosten](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Anzeigen der prognostizierten Kosten gruppiert nach Dienst

In der Standardansicht werden die prognostizierten Kostengruppen nicht nach Dienst angezeigt. Daher müssen Sie eine Auswahl für „Gruppieren nach“ hinzufügen.

Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.

Wählen Sie **Gruppieren nach** > **Dienstname** aus.

In der Ansicht werden die für jeden Dienst gruppierten Kosten angezeigt. Die prognostizierten Kosten werden nicht für jeden Dienst berechnet. Stattdessen werden die Kosten für die **Gesamtheit** Ihrer Dienste projiziert.

[![Gruppierte prognostizierte Kosten](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Anzeigen der prognostizierten Kosten für einen Dienst

Sie können die nur auf einen einzelnen Dienst beschränkten prognostizierten Kosten anzeigen. Beispielsweise können Sie nur die prognostizierten Kosten für virtuelle Computer anzeigen.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Wählen Sie **Filter hinzufügen** und dann **Dienstname** aus.
1. Wählen Sie in der Liste **Auswählen** einen Dienst aus. Wählen Sie beispielsweise **Virtual Machines** aus.

Überprüfen Sie die tatsächlichen Kosten und die prognostizierten Kosten für die Auswahl.

Sie können der Ansicht weitere Anpassungen hinzufügen.

1. Fügen Sie einen zweiten Filter für die **Verbrauchseinheit** hinzu, und wählen Sie einen zu filternden Wert für einen einzelnen Verbrauchseinheitstyp unter dem ausgewählten Dienstnamen aus.
1. Gruppieren Sie nach **Ressource**, um die spezifischen Ressourcen anzuzeigen, für die Kosten anfallen. Die prognostizierten Kosten werden nicht für jeden Dienst berechnet. Stattdessen werden die Kosten für die **Gesamtheit** Ihrer Ressourcen projiziert.

[![Prognostizierte Kosten für einen Dienst](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Anzeigen der Azure-Kosten zusammen mit den AWS-Kosten  

Um Azure- und AWS-Kosten zusammen anzuzeigen, verwenden Sie Verwaltungsgruppenbereiche in Azure.

1. Erstellen Sie eine Verwaltungsgruppe, oder wählen Sie eine vorhandene Gruppe aus.
1. Weisen Sie der Verwaltungsgruppe die gewünschten vorhandenen Azure-Abonnements zu.
1. Weisen Sie die *gleiche* Verwaltungsgruppe dem verknüpften Konto des Connectors zu.
1. Navigieren Sie zu „Kostenanalyse“, und wählen Sie **Kumulierte Kosten** aus.
1. Wählen Sie **Gruppieren nach** - **Anbieter** aus.

## <a name="view-new-commerce-license-and-consumption-costs"></a>Anzeigen der Lizenz- und Verbrauchskosten im neuen E-Commerce-Verfahren

Sie können Ihre Lizenz- und Verbrauchsprodukte im neuen E-Commerce-Verfahren zusammen mit Ihren Azure-Gebühren in der Kostenanalyse anzeigen. Wählen Sie die Filterliste und dann **Anbieter** aus. Anschließend wählen Sie eine Option aus der Liste aus, z. B. Microsoft 365 und Dynamics 365.

:::image type="content" source="./media/cost-analysis-common-uses/filter-select-provider.png" alt-text="Screenshot der Filterauswahl für „Anbieter“" lightbox="./media/cost-analysis-common-uses/filter-select-provider.png" :::

Mit dem Filter **Gebührentyp** und den Werten **Kauf** oder **Nutzung** können Sie die Anzeige auf bestimmte arbeitsplatzbasierte oder Verbrauchsgebühren beschränken.

:::image type="content" source="./media/cost-analysis-common-uses/filter-charge-type.png" alt-text="Screenshot der Filterauswahl für „Gebührentyp“" lightbox="./media/cost-analysis-common-uses/filter-charge-type.png" :::

Derzeit ist der Kauf von Produkten im neuen E-Commerce-Verfahren nur für Partner verfügbar.

## <a name="view-cost-breakdown-by-azure-service"></a>Anzeigen der Kostenaufschlüsselung nach Azure-Dienst

Die Kostenanzeige nach Azure-Dienst kann Ihnen helfen, die Teile Ihrer Infrastruktur besser zu verstehen, die die höchsten Kosten verursachen. Beispielsweise können die VM-Computekosten niedrig sein. Doch können aufgrund der Informationsmenge, die von den virtuellen Computern ausgegeben wird, erhebliche Netzwerkkosten entstehen. Es ist von entscheidender Bedeutung, die primären Kostenverursacher bei Ihren Azure-Diensten zu kennen, damit Sie bei Bedarf die Dienstnutzung anpassen können.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Wählen Sie **Kosten nach Dienst** aus, und gruppieren Sie dann nach **Dienstebene**.
1. Ändern Sie die Ansicht in **Tabelle**.

[![Kostenaufschlüsselung nach Azure-Dienst](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Überprüfen der berechneten Gebühren in der Kostenanalyse

Navigieren Sie zum Anzeigen der Rechnungsdetails im Azure-Portal zur Kostenanalyse für den Bereich, der der von Ihnen analysierten Rechnung zugeordnet ist. Wählen Sie die Ansicht **Rechnungsdetails** aus. In den Rechnungsdetails werden die Gebühren wie auf der Rechnung angezeigt.

[![Beispiel: Anzeigen der Rechnungsdetails](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

Wenn Sie die Rechnungsdetails anzeigen, können Sie den Dienst identifizieren, der unerwartete Kosten verursacht, und ermitteln, welche Ressourcen in der Kostenanalyse direkt mit der Ressource verknüpft sind. Wenn Sie beispielsweise die Gebühren für den Virtual Machines-Dienst analysieren möchten, navigieren Sie zur Ansicht **Kumulierte Kosten**. Legen Sie dann die Granularität auf **Täglich** fest, filtern Sie nach Gebühren für **Dienstname: Virtual Machines**, und gruppieren Sie die Gebühren nach **Ressource**.

[![Beispiel für die akkumulierten Kosten für Virtual Machines](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Anzeigen der Kostenaufschlüsselung nach Azure-Ressource

Ihre Dienste werden mit Azure-Ressourcen erstellt. Die Überprüfung der Kosten nach Ressourcen kann Ihnen helfen, schnell die Hauptkostenverursacher zu identifizieren. Wenn ein Dienst Ressourcen nutzt, die zu teuer sind, sollten Sie in Erwägung ziehen, zur Kostensenkung Änderungen vorzunehmen.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Wählen Sie **Kosten nach Ressource** aus.
1. Ändern Sie die Ansicht in **Tabelle**.

[![Anzeigen der Kostenaufschlüsselung nach Azure-Ressource](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Anzeigen der Kostenaufschlüsselung nach ausgewählten Dimensionen

Mithilfe von Dimensionen können Sie Ihre Kosten anhand verschiedener Metadatenwerte organisieren, die in ihren Gebühren angezeigt werden. Beispielsweise könnten Sie Ihre Kosten nach Standort gruppieren.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Wählen Sie den Filter **Gruppieren nach** aus.  
    [![Auswählen einer Gruppe nach Position](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. Optional können Sie die Ansicht zur späteren Verwendung speichern.
1. Klicken Sie unterhalb des Graphen auf ein Kreisdiagramm, um ausführlichere Daten anzuzeigen.  
    [![Anzeigen der Kostenaufschlüsselung nach ausgewählten Dimensionen](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Anzeigen der Kosten pro Tag oder Monat

Durch die Betrachtung der täglichen und monatlichen Kosten können Sie besser verstehen, ob es eine Woche oder ein Jahr gibt, in der bzw. dem Ihre Kosten höher liegen. Verursacht ein eventuell höherer Datenverkehr von Kunden an Feiertagen einen entsprechenden Anstieg ihrer Azure-Kosten? Ist der Freitag ein kostenträchtigerer Tag als Montag?

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Legen Sie die **Granularität** auf **Monatlich** oder **Täglich** fest.

[![Anzeigen der Kosten pro Tag](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>Anzeigen der Gebühren für Spot-VMs

Spot-VMs ermöglichen umfangreiche Einsparungen für Workloads, die Unterbrechungen verarbeiten können. Zur Ausführung von Workloads wird ungenutzte Azure-Kapazität verwendet. Für Spot-VMs erhalten Sie einen erheblichen Rabatt, da sie jederzeit entfernt werden können. Führen Sie die folgenden Schritte aus, um die Gebühren für Spot-VMs anzuzeigen:

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**
2. Fügen Sie einen Filter für **Preismodell: Spot** hinzu.

![Beispiel für einen Spot-VM-Filter](./media/cost-analysis-common-uses/spot-vm-filter.png)

Die Dimension „Preismodell“ wird auch zum Anzeigen von bedarfsgesteuerten Gebühren sowie von Reservierungsgebühren verwendet.

## <a name="view-your-reservation-charges"></a>Anzeigen Ihrer Reservierungsgebühren

Mit reservierten Instanzen können Sie bei Azure Geld sparen. Bei Reservierungen geben Sie vorab Geld für eine bestimmte Anzahl von Ressourcen im Zeitverlauf aus. In der Kostenanalyse werden die Gebühren so angezeigt, wie sie auf Ihrer Rechnung erscheinen. Die Gebühren werden als tatsächliche Kosten oder als amortisierte Kosten im Laufe Ihres Reservierungszeitraums angezeigt.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**
1. Fügen Sie einen Filter für **Preismodell: Reservierung** hinzu.
1. Klicken sie unter **Bereich** neben den angezeigten Kosten auf den Pfeil nach unten, und wählen Sie entweder die Metrik **Tatsächliche Kosten** oder die Metrik **Amortisierte Kosten** aus.

![Auswählen einer Kostenmetrik](./media/cost-analysis-common-uses/metric-cost.png)

Jede Metrik wirkt sich darauf aus, wie Daten für Ihre Reservierungsgebühren angezeigt werden.

**Tatsächliche Kosten**: Zeigt den Kauf an, wie er auf Ihrer Rechnung erscheint. Wenn Sie beispielsweise im Januar für 1.200 USD eine Reservierung für ein Jahr gekauft haben, enthält die Kostenanalyse für den Monat Januar Kosten in Höhe von 1.200 USD für die Reservierung. Für andere Monate des Jahres werden keine Reservierungskosten angezeigt. Wenn Sie die tatsächlichen Kosten nach VM gruppieren, fallen für eine VM, auf die der Reservierungsvorteil für einen bestimmten Monat angewendet wird, für diesen Monat keine Kosten an.

**Amortisierte Kosten**: Zeigt einen Reservierungskauf als amortisierte Kosten an (aufgeteilt auf die Dauer des Reservierungszeitraums). Im gleichen Beispiel wie oben würden in der Kostenanalyse Kosten in Höhe von 100 USD monatlich für das ganze Jahr angezeigt, wenn Sie im Januar für 1.200 USD eine Reservierung für ein Jahr erwerben würden. Wenn Sie in diesem Beispiel die Kosten nach VM gruppieren, werden Ihnen die Kosten für jeden virtuellen Computer angezeigt, auf den der Reservierungsvorteil angewendet wurde.

## <a name="view-your-reservation-utilization"></a>Anzeigen der Reservierungsnutzung

Nach dem Kauf einer Reservierung ist es wichtig, die Nutzung nachzuverfolgen, damit Sie erhalten, wofür Sie bezahlt haben. Beispiel: Wenn Sie zehn VMs für ein Jahr erworben haben, aber nur fünf davon nutzen, ist praktisch die Hälfte des Kaufs verschwendet. Es gibt zwei verschiedene Möglichkeiten, die Nutzung zu bewerten:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Anzeigen von nicht genutzten RI-Kosten in der Kostenanalyse

Führen Sie die folgenden Schritte aus, um zu ermitteln, welche Kosten derzeit monatlich für den Reservierungskauf verschwendet werden.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für den Bereich, in dem Ihre Reservierung angewendet wird. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**
1. Fügen Sie einen Filter für **Preismodell: Reservierung** hinzu.
1. Wählen Sie die Ansicht **Amortisierte Kosten** aus.
1. Legen Sie die Granularität auf **Monatlich** fest.
1. Legen Sie den Zeitraum auf das aktuelle Jahr oder Ihren Reservierungszeitraum fest.
1. Legen Sie den Diagrammtyp auf **Säule (gestapelt)** fest.
1. Gruppieren Sie Gebühren nach **Gebührentyp**.
1. Überprüfen Sie die Ergebnisse für Werte vom Typ `unusedreservation`.

[![Beispiel für die Reservierungsnutzung](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Anzeigen der Nutzung in Reservierungen

Ausführliche Anweisungen finden Sie unter [Optimieren der Reservierungsnutzung](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use).

## <a name="view-costs-for-a-specific-tag"></a>Anzeigen der Kosten für ein bestimmtes Tag

Viele Azure-Benutzer wenden Tags auf Ihre Ressourcen an, z.B. für eine Kostenstelle oder Entwicklungsumgebung (Produktion und Test), um Kosten besser zu kategorisieren. Tags werden in der Kostenanalyse als Dimension angezeigt. Mit der Dimension können Sie Einblicke in Ihre Kategorisierung mit benutzerdefinierten Tags erhalten.

Die Tag-Unterstützung betrifft nur die Nutzung, die gemeldet wird, *nachdem* das Tag auf die Ressource angewendet wurde. Tags werden nicht rückwirkend für die Kostenkalkulation angewendet.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Wählen Sie **Gruppieren nach** für das Tag aus.

[![Anzeigen der Kosten für ein bestimmtes Tag](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Herunterladen Ihrer Nutzungsdetails

Die Berichtsdatei mit den Nutzungsdetails im CSV-Format enthält eine Aufschlüsselung aller Gebühren, die für eine Rechnung anfallen. Sie können den Bericht mit Ihrer Rechnung vergleichen, die dadurch besser verständlich wird. Jede in Rechnung gestellte Gebühr entspricht den im Nutzungsbericht aufgeschlüsselten Kosten.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Nutzung und Gebühren** für ein Abrechnungskonto oder Abonnement. Beispiel: **Kostenverwaltung + Abrechnungs** > **Abrechnung** > **Nutzung + Gebühren**.
1. Markieren Sie die Position, die Sie herunterladen möchten, und klicken Sie dann auf das Symbol zum Herunterladen.  
    [![Herunterladen von Nutzungsdaten und Gebühren](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Wählen Sie die herunterzuladende Nutzungsdatei aus.  
    ![Auswählen der herunterzuladenden Nutzungsdatei](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Anzeigen der Aufschlüsselung der EA-Kosten pro Monat

Durch die Registrierung Ihres Enterprise Agreements (EA) entstehen Kosten für Ihre gesamte Organisation. Wenn Sie verstehen, wie Kosten anfallen und im Laufe der Zeit in Rechnung gestellt werden, können Sie die entsprechenden Beteiligten besser einbeziehen, um sicherzustellen, dass die Kosten verantwortungsbewusst verwaltet werden.

Es werden nur die Kosten für Ihre aktive Registrierung angezeigt. Wenn Sie eine Registrierung (inaktiv) in eine neue Registrierung (aktiv) übertragen haben, werden die Kosten für die vorherige Registrierung in Cost Management nicht angezeigt.


1. Navigieren Sie im Azure-Portal zu **Kostenverwaltung + Abrechnung** > **Übersicht**.
1. Klicken Sie für den aktuellen Monat auf **Aufschlüsselung**, und zeigen Sie den Verbrauch der Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) an.  
    [![Übersicht über EA-Kosten – Aufschlüsselung in der Übersicht](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Klicken Sie auf die Registerkarte **Nutzung + Gebühren**, und zeigen Sie die Aufschlüsselung des Vormonats im ausgewählten Zeitraum an.  
    [![Registerkarte „Nutzung + Gebühren“](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Anzeigen der monatlichen Kosten der Registrierung nach Laufzeit

Verwenden Sie eine grafische Ansicht der monatlichen Kosten für Ihre Registrierung, um die Kostentrends und die in Rechnung gestellten Beträge für einen bestimmten Zeitraum zu verstehen.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Wählen Sie Ihre Registrierung aus, und legen Sie die Laufzeit der Registrierung fest.
1. Legen Sie die Granularität auf „Monatlich“ und dann die Ansicht auf **Säule (gestapelt)** fest.

Zugunsten einer detaillierteren Analyse können Sie Ihre Daten gruppieren und filtern.

[![Monatliche Registrierungskosten nach Laufzeit](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Anzeigen der kumulierten Kosten der EA-Registrierung

Zeigen Sie die im Laufe der Zeit angefallenen Gebühren als kumulierten Nettobetrag an, um die Gesamtausgaben für Ihre Organisation für einen bestimmten Zeitraum zu verstehen.

1. Navigieren Sie im Azure-Portal zur Kostenanalyse für Ihren Bereich. Beispiel: **Kostenverwaltung + Abrechnung** > **Kostenverwaltung** > **Kostenanalyse**.
1. Wählen Sie Ihre Registrierung aus, und zeigen Sie dann die aktuellen kumulierten Kosten an.

[![Kumulierte Kosten der Registrierung](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte
- Falls Sie den ersten Schnellstart für Cost Management noch nicht abgeschlossen haben, lesen Sie ihn unter [Erste Schritte in der Analyse von Kosten](quick-acm-cost-analysis.md).
- Lesen Sie die [Dokumentation zur Kostenverwaltung](../index.yml).
