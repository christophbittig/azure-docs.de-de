---
title: Erstellen einer Azure Cognitive Services-Ressource mit Preisen für Mindestabnahme
description: Hier erfahren Sie, wie Sie sich für Preise für Mindestabnahme registrieren. Diese unterscheiden sich von Preisen für die nutzungsbasierte Bezahlung.
author: aahill
ms.author: aahi
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/04/2021
ms.openlocfilehash: 94bd9d22876f264ffd438d579c48aa037ed87c3f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486619"
---
# <a name="quickstart-purchase-commitment-tier-pricing"></a>Schnellstart: Kaufen zu Preisen für Mindestabnahme

Cognitive Services bietet Preise für Mindestabnahme. Hierbei handelt es sich jeweils um einen rabattierten Tarif im Vergleich zum Preismodell mit nutzungsbasierter Bezahlung. Mit Preisen für Mindestabnahme können Sie die folgenden Cognitive Services-Features gegen eine feste Gebühr nutzen, sodass Sie die Gesamtkosten auf der Grundlage der Anforderungen Ihrer Workload zuverlässig planen können:

* Sprache-in-Text (Standard)
* Text-zu-Sprache (neuronal)
* Textübersetzung (Standard)
* Language Understanding, Standard (Textanforderungen)
* Azure Cognitive Service for Language
    * Standpunktanalyse
    * Schlüsselwortextraktion
    * Spracherkennung
* Maschinelles Sehen: Lesen

Preise für Mindestabnahme sind auch für den folgenden Applied AI-Dienst verfügbar:
* Formularerkennung: Benutzerdefiniert/Rechnung

Weitere Informationen finden Sie unter [Azure Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="request-approval-to-purchase-a-commitment-plan"></a>Anfordern einer Genehmigung für den Erwerb eines Mindestabnahmeplans

Vor dem Erwerb eines Mindestabnahmeplans müssen Sie einen [Onlineantrag stellen](https://aka.ms/csgatecommitment). Wenn Ihr Antrag genehmigt wird, können Sie über das Portal eine Mindestabnahme erwerben, und zwar sowohl für neue als auch für bereits vorhandene Azure-Ressourcen. 

* In dem Formular muss eine geschäftliche E-Mail-Adresse angegeben werden, die einer Azure-Abonnement-ID zugeordnet ist.

* Überprüfen Sie Ihr E-Mail-Postfach (Posteingang und Junk-Ordner) auf Updates von `csgate@microsoft.com` zum Status Ihres Antrags.

Nach erfolgter Genehmigung können Sie entweder eine neue Ressource erstellen, um einen Mindestabnahmeplan zu verwenden, oder eine vorhandene Ressource aktualisieren. 

## <a name="create-a-new-resource"></a>Neue Ressource erstellen

> [!NOTE]
> Wenn Sie einen Mindestabnahmeplan erwerben und verwenden möchten, muss Ihre Ressource über den Standard-Tarif verfügen. Sie können keinen Mindestabnahmeplan für eine Ressource mit Free-Tarif erwerben. (Die entsprechende Option wird in diesem Fall nicht angezeigt.)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wählen Sie für einen der oben aufgeführten Cognitive Services- oder Applied AI-Dienste die Option **Neue Ressource erstellen** aus. 

2. Geben Sie die entsprechenden Informationen ein, um Ihre Ressource zu erstellen. Achten Sie darauf, den Standard-Tarif auszuwählen.

    :::image type="content" source="media/commitment-tier/create-resource.png" alt-text="Screenshot: Ressourcenerstellung im Azure-Portal" lightbox="media/commitment-tier/create-resource.png":::

3. Nach der Erstellung Ihrer Ressource können Sie von der nutzungsbasierten Bezahlung zu einem Mindestabnahmeplan wechseln.  

## <a name="purchase-a-commitment-plan-by-updating-your-azure-resource"></a>Erwerben eines Mindestabnahmeplans durch Aktualisieren Ihrer Azure-Ressource

1. Melden Sie sich mit dem genehmigten Azure-Abonnement beim [Azure-Portal](https://portal.azure.com/) an. 
2. Wählen Sie in Ihrer Azure-Ressource für eines der oben aufgeführten anwendbaren Features die Option **Preise für Mindestabnahme** aus.

    > [!NOTE]
    > Die Option zum Erwerben eines Mindestabnahmeplans wird nur angezeigt, wenn die folgenden Voraussetzungen erfüllt sind:
    > * Die Ressource verwendet den Standard-Tarif.
    > * Der Erwerb zu Preisen für Mindestabnahme wurde genehmigt. 
 
1. Wählen Sie **Ändern** aus, um die verfügbaren Mindestabnahmen für die gehostete API und Containernutzung anzuzeigen. 

    :::image type="content" source="media/commitment-tier/commitment-tier-pricing.png" alt-text="Screenshot: Seite „Preise für Mindestabnahme“ im Azure-Portal" lightbox="media/commitment-tier/commitment-tier-pricing.png":::

4. Wählen Sie im angezeigten Fenster sowohl einen **Tarif** als auch die Option **Automatische Verlängerung** aus.

    * **Mindestabnahme**: Die Mindestabnahme für das Feature. Die Mindestabnahme wird direkt nach dem Klicken auf **Kaufen** aktiviert, und der Betrag für die Mindestabnahme wird Ihnen anteilig in Rechnung gestellt.
    
    * **Automatische Verlängerung**: Wählen Sie aus, wie der aktuelle Mindestabnahmeplan ab dem nächsten Abrechnungszyklus verlängert, geändert oder gekündigt werden soll. Wenn Sie sich für die automatische Verlängerung entscheiden, ist das **Datum der automatischen Verlängerung** das Datum (in Ihrer lokalen Zeitzone), an dem Ihnen der nächste Abrechnungszyklus in Rechnung gestellt wird. Dieses Datum entspricht dem Anfang des Kalendermonats.
    

    > [!CAUTION]
    > Nach dem Klicken auf **Kaufen** wird Ihnen der ausgewählte Tarif in Rechnung gestellt. Der erworbene Mindestabnahmeplan ist nicht erstattungsfähig.
    > 
    > Mindestabnahmepläne werden monatlich abgerechnet – außer im Monat des Kaufs, in dem die Kosten und das Kontingent anteilig auf der Grundlage der verbleibenden Anzahl von Tagen in diesem Monat berechnet werden. Für die Folgemonate wird die Gebühr jeweils am ersten Tag des Monats fällig.

    :::image type="content" source="media/commitment-tier/enable-commitment-plan.png" alt-text="Screenshot: Preise für Mindestabnahme sowie Verlängerungsdetails im Azure-Portal" lightbox="media/commitment-tier/enable-commitment-plan-large.png":::


## <a name="overage-pricing"></a>Preise für Überschreitungen

Wenn Ihre Ressourcennutzung das bereitgestellte Kontingent übersteigt, wird Ihnen die zusätzliche Nutzung zu dem im Prepaidtarif angegebenen Überschreitungsbetrag in Rechnung gestellt.

## <a name="purchase-a-different-commitment-plan"></a>Erwerben eines anderen Mindestabnahmeplans 

Die Mindestabnahmepläne basieren auf Kalendermonaten. Sie können jederzeit vom standardmäßigen Modell mit nutzungsbasierter Bezahlung zu einem Mindestabnahmeplan wechseln. Wenn Sie einen Plan erwerben, wird Ihnen ein anteiliger Preis für den verbleibenden Monat in Rechnung gestellt. Während des Verpflichtungszeitraums kann der Mindestabnahmeplan für den aktuellen Monat nicht geändert werden. Sie können jedoch einen anderen Mindestabnahmeplan für den nächsten Kalendermonat auswählen. Die Abrechnung für den nächsten Monat erfolgt dann am ersten Tag des nächsten Monats.

Sollten Sie einen Mindestabnahmeplan benötigen, der über die angebotenen Tarife hinausgeht, wenden Sie sich an `csgate@microsoft.com`.

## <a name="end-a-commitment-plan"></a>Beenden eines Mindestabnahmeplans

Wenn Sie einen Mindestabnahmeplan nicht mehr benötigen, können Sie die automatische Verlängerung für Ihre Ressource auf **Nicht automatisch verlängern** festlegen. Der Mindestabnahmeplan läuft dann zum angezeigten Enddatum ab. Nach diesem Datum wird Ihnen der Mindestabnahmeplan nicht mehr in Rechnung gestellt. Sie können die Azure-Ressource weiterhin für API-Aufrufe verwenden, und Ihnen werden die Preise für die nutzungsbasierte Bezahlung in Rechnung gestellt. Sie haben bis Mitternacht (UTC) am letzten Tag eines Monats Zeit, einen Mindestabnahmeplan zu beenden, und Ihnen werden keine Kosten für den folgenden Monat in Rechnung gestellt. 

## <a name="see-also"></a>Weitere Informationen

* [Azure Cognitive Services – Preise](https://azure.microsoft.com/pricing/details/cognitive-services/).
* [Antrag auf Erwerb zu Preisen für Mindestabnahme](https://aka.ms/csgatecommitment)
