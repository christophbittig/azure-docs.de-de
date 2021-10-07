---
title: Glossarbericht zu Ihren Daten mithilfe von Purview-Erkenntnissen
description: In dieser Schrittanleitung wird beschrieben, wie Sie die Glossarberichterstellung mit Purview-Erkenntnissen für Ihre Daten anzeigen und verwenden können.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 315cfdc60de77ab4d6345c5d579351049c5129a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218936"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Glossarerkenntnisse über Ihre Daten in Azure Purview

In dieser Schrittanleitung wird beschrieben, wie Sie auf Purview-Glossar-Erkenntnisberichte für Ihre Daten zugreifen, sie anzeigen und filtern können.

> [!IMPORTANT]
> Azure Purview-Erkenntnisse befinden sich derzeit in der VORSCHAUPHASE. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Schrittanleitung lernen Sie Folgendes:

> [!div class="checklist"]
> - Wechseln zu Erkenntnissen aus Ihrem Purview-Konto
> - Erhalten eines Überblicks über Ihre Daten

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie vor dem Beginn mit Purview--Erkenntnissen sicher, dass Sie die folgenden Schritte ausgeführt haben:

- Einrichten Ihrer Azure-Ressourcen und Auffüllen des Kontos mit Daten

- Einrichten und Ausführen eines Scans für den Quelltyp

- Einrichten eines Glossars und Anfügen von Ressourcen an Glossarbegriffe

Weitere Informationen finden Sie unter [Verwalten von Datenquellen in Azure Purview (Vorschau)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Verwenden von Purview-Glossarerkenntnissen

In Azure Purview können Sie Glossarbegriffe erstellen und diese Ressourcen anfügen. Später können Sie die Glossarverteilung in „Glossarerkenntnisse“ anzeigen. So erfahren Sie den Status Ihres Glossars gemäß der Begriffe, die Ressourcen angefügt sind. Außerdem werden Begriffe nach Status und Verteilung der Rollen nach Anzahl der Benutzer aufgeführt.

**So zeigen Sie „Glossarerkenntnisse“ an:**

1. Wechseln Sie zum **Azure Purview**-[Instanzbildschirm im Azure-Portal](https://aka.ms/purviewportal), und wählen Sie Ihr Purview-Konto aus.

1. Klicken Sie auf der Seite **Übersicht** im Abschnitt **Erste Schritte** auf die Kontokachel **Open Purview Studio** (Purview Studio öffnen).

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Starten von Purview im Azure-Portal":::

1. Klicken Sie auf der Purview-Seite **Start** im linken Menü auf **Erkenntnisse**.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Anzeigen Ihrer Erkenntnisse im Azure-Portal":::

1. Wählen Sie im Bereich **Erkenntnisse** die Option **Glossar** aus, um den **Glossarerkenntnisse**-Bericht von Purview anzuzeigen.

**Glossarerkenntnisse** bieten Geschäftskunden wertvolle Informationen, um ein genau definiertes Glossar für ihre Organisation zu verwalten.

1. Der Bericht beginnt mit **Allgemeine KPIs**. Dort werden die **_Total terms_ *(Begriffe insgesamt) in Ihrem Purview-Konto,* _Genehmigte Begriffe ohne Ressourcen_*und*_Abgelaufene Begriffe mit Ressourcen_** angezeigt. Mit jedem dieser Werte können Sie die Integrität Ihres Glossars ermitteln.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Anzeige der KPIs in den Glossarerkenntnissen "::: 


2. Im Abschnitt **Momentaufnahme von Begriffen** (darüber angezeigt) werden für Begriffe mit Ressourcen und für Begriffe ohne Ressourcen die Begriffsstatus **_Entwurf_ *,* _Genehmigt_ *,* _Warnung_*und*_Abgelaufen_** angezeigt.

3. Klicken Sie auf **Mehr anzeigen**, um die Begriffsnamen mit verschiedenen Status und weitere Details zu **_Inhaltsverantwortlichen_ *_ und _* _Experten_** anzuzeigen. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Momentaufnahme von Begriffen mit und ohne Ressourcen":::  

4. Wenn Sie für ***Approved terms with assets** _ (Genehmigte Begriffe mit Ressourcen) auf „Mehr anzeigen“ klicken, können Sie zur Detailseite für Begriffe im _ *Glossar** und von dort zur Liste der Ressourcen mit den zugehörigen Begriffen navigieren. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Einblicke in das Glossar"::: 

4. Zeigen Sie auf der Seite „Glossarerkenntnisse“ die Verteilung **Unvollständiger Begriffe** nach dem Typ der fehlenden Informationen an. Im Diagramm wird in den Feldern **_Missing Definition_ *(Fehlende Definition),* _Missing Expert_ *(Fehlender Experte),* _Missing Steward_ *(Fehlender Inhaltsverantwortlicher) und*_Missing multiple_**  (Mehrere fehlend) die Anzahl der Begriffe angezeigt, für die die entsprechenden Informationen fehlen.

1. Klicken Sie unter _*„Unvollständige Begriffe“** auf ***Mehr anzeigen** _, um die Begriffe anzuzeigen, für die Informationen fehlen. Sie können zur Detailseite für Glossarbegriffe navigieren, um die fehlenden Informationen für einen Begriff einzugeben und sicherzustellen, dass der Glossarbegriff vollständig ist.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Erstellen eines [Glossarbegriffs](./how-to-create-import-export-glossary.md).
