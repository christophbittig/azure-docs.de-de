---
title: Anzeigen des monatlichen geschätzten Labkostentrends
description: Dieser Artikel enthält Informationen zum Nachverfolgen der Kosten Ihres Labs (monatliches geschätztes Kostentrenddiagramm) in Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f0e99ae3da38ef723b211075c9f32dc1bbdfda70
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286467"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Verfolgen von Kosten eines Labs in Azure DevTest Labs
Dieser Artikel enthält Informationen dazu, wie Sie die Kosten Ihres Labs nachverfolgen. Sie erfahren darin, wie Sie den geschätzten Kostentrend des Labs für den aktuellen Kalendermonat anzeigen. Außerdem erfahren Sie, wie Sie pro Ressource im Lab die Kosten für den bisherigen Kalendermonat anzeigen.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Anzeigen des monatlichen geschätzten Labkostentrends 
In diesem Abschnitt lernen Sie, wie Sie das Diagramm **Monatlicher geschätzter Kostentrend** verwenden, um die bisherigen geschätzten Kosten für den aktuellen Kalendermonat sowie die veranschlagten Kosten am Monatsende für den aktuellen Kalendermonat anzuzeigen. Außerdem lernen Sie, wie Sie die Labkosten verwalten, indem Sie Ausgabenziele und Schwellenwerte festlegen, bei deren Erreichen das Melden der Ergebnisse durch DevTest Labs ausgelöst wird.

Führen Sie folgende Schritte aus, um das Diagramm „Monatlicher geschätzter Kostentrend“ anzuzeigen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste mit den Labs Ihr Lab aus.  
4. Wählen Sie im linken Menü die Option **Konfiguration und Richtlinien** aus.  
4. Wählen Sie im linken Menü im Abschnitt **Cost tracking** (Kostennachverfolgung) die Option **Cost trend** (Kostentrend) aus. Der folgende Screenshot zeigt ein Beispiel eines Kostendiagramms. 
   
    ![Screenshot: Kostendiagramm](./media/devtest-lab-configure-cost-management/graph.png)

    Der Wert für **Estimated cost** (Geschätzte Kosten) entspricht den geschätzten Kosten im aktuellen Kalendermonat bis zum heutigen Tag. Der Wert für **Projected cost** (Prognostizierte Kosten) entspricht den geschätzten Kosten für den gesamten aktuellen Kalendermonat, berechnet anhand der Labkosten für die vergangenen fünf Tage.

    Die Kostenbeträge werden auf die nächste ganze Zahl aufgerundet. Beispiel: 

   * 5.01 wird auf 6 aufgerundet. 
   * 5.50 wird auf 6 aufgerundet.
   * 5.99 wird auf 6 aufgerundet.

     Oberhalb des Diagramms wird darauf hingewiesen, dass es sich bei den in diesem Diagramm standardmäßig angezeigten Kosten um *geschätzte* Kosten handelt, die anhand der geltenden Preise für die [nutzungsbasierte Bezahlung](https://azure.microsoft.com/offers/ms-azr-0003p/) kalkuliert werden. Sie können auch Ihre eigenen Ausgabenziele festlegen, die in den Diagrammen angezeigt werden, indem Sie die [Kostenziele für Ihr Lab verwalten](#managing-cost-targets-for-your-lab).

     Bei der Kostenkalkulation werden folgende Kosten *nicht* berücksichtigt:

   * CSP- und Dreamspark-Abonnements werden derzeit nicht unterstützt. Azure DevTest Labs verwendet die Azure-Abrechnungs-APIs, um die Labkosten zu berechnen, die keine CSP- oder Dreamspark-Abonnements unterstützen.
   * Sonderpreise. Zurzeit können Sie nicht von Sonderpreisen (wie in Ihrem Abonnement angezeigt) profitieren, die Sie mit Microsoft oder Microsoft-Partnern vereinbart haben. Es werden nur Preise für die nutzungsbasierte Bezahlung verwendet.
   * Steuern.
   * Rabatte.
   * Rechnungswährung. Zurzeit werden die Labkosten nur in USD angezeigt.

### <a name="managing-cost-targets-for-your-lab"></a>Verwalten von Kostenzielen für Ihr Lab
Mit DevTest Labs können Sie Ihre Labkosten verwalten, indem Sie ein Ausgabenziel festlegen, das Sie im Diagramm Monatlicher geschätzter Kostentrend anzeigen können. DevTest Labs kann Ihnen eine Benachrichtigung senden, wenn die Ausgaben den angegebenen Zielschwellenwert erreichen. 

1. Wählen Sie auf der Seite **Cost trend** (Kostentrend) die Option **Manage target** (Ziel verwalten) aus.

    ![Screenshot der Schaltfläche „Verwalten“ (Manage)](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Geben Sie auf der Seite **Manage target** (Ziel verwalten) ein Ausgabenziel und Schwellenwerte an. Sie können auch festlegen, ob die einzelnen ausgewählten Schwellenwerte im Kostentrenddiagramm oder über eine Webhookbenachrichtigung gemeldet werden.

    ![Screenshot: Bereich "Ziel verwalten"](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Wählen Sie einen Zeitraum, für den die Kostenziele nachverfolgt werden sollen.
      - **Monatlich**: Die Kostenziele werden pro Monat nachverfolgt.
      - **Fest:** Die Kostenziele werden für den Datumsbereich nachverfolgt, den Sie als Start- und Enddatum angeben. Diese Werte geben in der Regel an, wie lange die Ausführung Ihres Projekts geplant ist.
   - Geben Sie **Zielkosten** an. Zum Beispiel den Betrag, den Sie für dieses Lab im definierten Zeitraum aufwenden möchten.
   - Aktivieren bzw. deaktivieren Sie die Schwellenwerte, die gemeldet werden sollen – in Inkrementen von 25% bis zum Höchstwert von 125% der angegebenen **Zielkosten**.
      - **Benachrichtigen**: Wenn die Ergebnisse diesen Schwellenwert erreichen, werden Sie von einer von Ihnen angegebenen Webhook-URL benachrichtigt.
      - **Plot-Diagramm**: Wenn die Ergebnisse diesen Schwellenwert erreichen, wird das Ergebnis in einem Kostentrenddiagramm dargestellt, das Sie anzeigen können.
   - Wenn Sie für die Erreichung des Schwellenwerts die Option **Benachrichtigen** wählen, müssen Sie eine Webhook-URL angeben. Wählen Sie im Bereich „Kostenintegrationen“ die Option **Klicken Sie hier, um eine Integration hinzuzufügen**. Geben Sie im Bereich „Benachrichtigung konfigurieren“ eine **Webhook-URL** ein, und wählen Sie **OK** aus.

       ![Screenshot: Bereich "Benachrichtigung konfigurieren"](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - Wenn Sie **Benachrichtigen** angeben, müssen Sie eine Webhook-URL definieren.
     - Entsprechend müssen Sie beim Definieren einer Webhook-URL im Bereich „Kostenschwellenwert“ die Option **Benachrichtigung** auf **Ein** festlegen.
     - Erstellen Sie den Webhook, bevor Sie ihn hier eingeben.  

       Weitere Informationen zu Webhooks finden Sie unter [Erstellen eines Webhooks oder einer API-Azure-Funktion](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Anzeigen von Kosten nach Ressource 
Mit der Funktion für den monatlichen Kostentrend in Labs können Sie sehen, wie viel Sie schon im aktuellen Kalendermonat ausgegeben haben. Außerdem wird eine Prognose Ihrer Ausgaben bis zum Monatsende angezeigt, basierend auf Ihren Ausgaben der letzten sieben Tage. Sie können die Funktion **Kosten nach Ressource** verwenden, die Ihnen die Kosten für den bisherigen Kalendermonat **pro Ressource** in einer Tabelle anzeigt, um besser zu verstehen, warum die Ausgaben im Lab früh die Schwellenwerte erreichen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie im linken Menü die Option **Konfiguration und Richtlinien** aus.
5. Wählen Sie im linken Menü im Abschnitt **Cost tracking** (Kostennachverfolgung) die Option **Kosten nach Ressource** aus. Daraufhin werden die Kosten für jede Ressource einem Lab zugeordnet. 

    ![Screenshot: Kosten nach Ressource](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Mit dieser Funktion können Sie leicht die Ressourcen identifizieren, die die höchsten Kosten verursachen, um Maßnahmen zur Reduzierung der Ausgaben für das Lab zu ergreifen. Beispielsweise sind die Kosten für eine VM von der Größe der VM abhängig. Je größer der virtuelle Computer ist, desto höher sind die Kosten. Sie können die Größe eines virtuellen Computers und des Besitzers ermitteln und mit dem Besitzer darüber sprechen, warum er diese Größe für den virtuellen Computer benötigt und ob er die Größe nicht verringern kann.

Die [Richtlinie zum automatischen Herunterfahren](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) hilft Ihnen dabei, die Kosten zu senken, indem virtuelle Lab-Computer zu einer bestimmten Tageszeit heruntergefahren werden. Der Benutzer eines Labs kann diese Richtlinie jedoch deaktivieren, wodurch sich die Kosten für die Ausführung der VM erhöhen. Sie können einen virtuellen Computer in der Tabelle auswählen, um zu sehen, ob dafür die Richtlinie zum automatischen Herunterfahren deaktiviert wurde. Sprechen Sie mit dem Besitzer des virtuellen Computers, um herauszufinden, warum er sich abgemeldet hat, und prüfen Sie, ob er sich wieder anmelden kann.
 
## <a name="next-steps"></a>Nächste Schritte
Diese Schritte könnten Sie als Nächstes ausführen:

* [Definieren von Labrichtlinien](devtest-lab-set-lab-policy.md) Hier erfahren Sie, wie Sie die verschiedenen Richtlinien festlegen, mit denen Ihr Lab und die zugehörigen virtuellen Computer verwendet werden. 
* [Erstellen eines benutzerdefinierten Images](devtest-lab-create-template.md) Wenn Sie einen virtuellen Computer erstellen, geben Sie eine Basis an. Die Basis kann entweder ein benutzerdefiniertes Image oder ein Marktplatz-Image sein. In diesem Abschnitt erfahren Sie, wie Sie ein benutzerdefiniertes Image aus einer Datei einer virtuellen Festplatte erstellen.
* [Konfigurieren von Marktplatz-Images](devtest-lab-configure-marketplace-images.md) DevTest Labs unterstützt die Erstellung virtueller Computer auf der Basis von Azure Marktplatz-Images. In diesem Artikel wird veranschaulicht, wie Sie Azure Marktplatz-Images angeben, die Sie beim Erstellen von virtuellen Computern in einem Lab verwenden können.
* [Löschen eines virtuellen Computers in einem Lab](devtest-lab-add-vm.md). In diesem Abschnitt wird veranschaulicht, wie Sie einen virtuellen Computer aus einem benutzerdefinierten oder Marktplatz-Basis-Image erstellen und mit Artefakten auf dem virtuellen Computer arbeiten.
