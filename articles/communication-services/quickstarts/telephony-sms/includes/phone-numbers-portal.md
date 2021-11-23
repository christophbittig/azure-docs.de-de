---
ms.openlocfilehash: 35394fc33316518dac760363d2b69fdaa0727d6a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132530004"
---

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Eine aktive Communication Services-Ressource.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Beschaffen einer Telefonnummer

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Communication Services-Ressource, um mit der Bereitstellung von Telefonnummern zu beginnen.

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Screenshot: Hauptseite einer Communication Services-Ressource":::

### <a name="search-for-available-phone-numbers"></a>Suchen nach verfügbaren Telefonnummern

Navigieren Sie im Ressourcenmenü zum Blatt **Telefonnummern**.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Screenshot: Telefonnummernseite einer Communication Services-Ressource":::

Klicken Sie auf die Schaltfläche **Abrufen**, um den Assistenten zu starten. Im Assistenten werden auf dem Blatt **Telefonnummern** einige Fragen gestellt, um die Telefonnummer zu ermitteln, die für Ihr Szenario am besten geeignet ist. 

Zuerst müssen Sie eine Auswahl für **Land/Region** treffen, um anzugeben, wo die Telefonnummer bereitgestellt werden soll. Nach der Auswahl des Lands bzw. der Region müssen Sie den **Anwendungsfall** auswählen, der Ihre Anforderungen am besten erfüllt. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Screenshot: Ansicht „Telefonnummer erhalten“":::

### <a name="select-your-phone-number-features"></a>Auswählen der Telefonnummernfeatures

Das Konfigurieren Ihrer Telefonnummer ist in zwei Schritte unterteilt: 

1. Auswahl des [Nummerntyps](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Auswahl der [Nummernfunktionen](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

Sie haben die Wahl zwischen zwei Telefonnummerntypen: **lokale** und **gebührenfreie** Telefonnummern. Nach der Wahl eines Nummerntyps können Sie das Feature auswählen.

In diesem Beispiel wurde der Nummerntyp **Gebührenfrei** mit den Features **Anrufe tätigen** und **Send and receive SMS** (SMS senden und empfangen) ausgewählt.

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Screenshot: Ansicht für die Featureauswahl":::

Klicken Sie hier auf die Schaltfläche **Weiter: Nummern** (unten auf der Seite), um die Telefonnummern anzupassen, die Sie bereitstellen möchten.

### <a name="customizing-phone-numbers"></a>Anpassen von Telefonnummern

Auf der Seite **Nummern** können Sie die Telefonnummern anpassen, die Sie bereitstellen möchten.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Screenshot: Seite für die Nummernauswahl":::

> [!NOTE]
> In dieser Schnellstartanleitung wird der Anpassungsvorgang für den Nummerntyp **Gebührenfrei** gezeigt. Der Vorgang weicht etwas ab, wenn Sie den Nummerntyp **Lokal** ausgewählt haben, aber das Endergebnis ist identisch.

Treffen Sie in der Liste mit den verfügbaren Ortsvorwahlen eine Auswahl für **Ortsvorwahl**, und geben Sie die Menge an, die Sie bereitstellen möchten. Klicken Sie anschließend auf **Suchen**, um nach Nummern zu suchen, die die ausgewählten Anforderungen erfüllen. Die Telefonnummern, die Ihre Anforderungen erfüllen, werden zusammen mit den monatlichen Kosten angezeigt.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Screenshot: Seite für die Nummernauswahl mit reservierten Nummern":::

> [!NOTE]
> Die Verfügbarkeit hängt davon ab, welche Optionen Sie für Nummerntyp, Standort und Features ausgewählt haben.
> Die Nummern werden im Zeitraum bis zum Ablauf der Transaktion für kurze Zeit reserviert. Wenn die Transaktion abläuft, müssen Sie die Nummern erneut auswählen.

Klicken Sie am unteren Rand der Seite auf die Schaltfläche **Weiter: Zusammenfassung**, um eine Zusammenfassung des Kaufs anzuzeigen und Ihre Bestellung aufzugeben.

### <a name="purchase-phone-numbers"></a>Kaufen von Telefonnummern

Auf der Seite mit der Zusammenfassung werden noch einmal der Nummerntyp, die Features, die Telefonnummern und die monatlichen Gesamtkosten für die Bereitstellung der Telefonnummern angezeigt.

> [!NOTE]
> Bei den angezeigten Preisen handelt es sich um die **monatlichen Gebühren**. Dies sind die Kosten für das Leasen der ausgewählten Telefonnummer an Sie. Nicht in dieser Anzeige enthalten sind die **Kosten für die nutzungsbasierte Bezahlung**, die beim Tätigen bzw. Empfangen von Anrufen anfallen. Die Preislisten finden Sie [hier](../../../concepts/pricing.md). Diese Kosten richten sich nach dem Nummerntyp und den angerufenen Zielen. Die Preise pro Minute für einen Anruf von einer Nummer in der Region Seattle bei einer Nummer in der Region New York und für einen Anruf von derselben Nummer bei einer Mobiltelefonnummer im Vereinigten Königreich können sich beispielsweise unterscheiden.

Klicken Sie abschließend unten auf der Seite auf **Bestellen**, um die Bestellung zu bestätigen.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Screenshot: Seite mit der Zusammenfassung zu Nummerntyp, Features, Telefonnummern und monatlichen Gesamtkosten":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Suchen nach Ihren Telefonnummern im Azure-Portal

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Azure Communication Services-Ressource:

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Screenshot: Hauptseite einer Communication Services-Ressource":::

Wählen Sie im Menü das Blatt „Telefonnummern“ aus, um Ihre Telefonnummern zu verwalten.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Screenshot: Telefonnummernseite einer Communication Services-Ressource":::

> [!NOTE]
> Es kann einige Minuten dauern, bis die bereitgestellten Nummern auf dieser Seite angezeigt werden.


### <a name="update-phone-number-capabilities"></a>Funktionen für Telefonnummern

Auf der Seite **Telefonnummern** können Sie eine Telefonnummer auswählen, um sie zu konfigurieren.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Screenshot: Seite zum Aktualisieren von Features":::

Wählen Sie aus den verfügbaren Optionen die gewünschten Features aus, und klicken Sie anschließend auf **Speichern**, um Ihre Auswahl anzuwenden.

### <a name="release-phone-number"></a>Freigeben einer Telefonnummer

Auf der Seite **Nummern** können Sie Telefonnummern freigeben.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Screenshot: Seite zum Freigeben von Telefonnummern.":::

Wählen Sie die Telefonnummer aus, die Sie freigeben möchten, und klicken Sie dann auf die Schaltfläche **Freigeben**.