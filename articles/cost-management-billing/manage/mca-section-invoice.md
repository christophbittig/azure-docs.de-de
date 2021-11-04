---
title: Organisieren Ihrer Rechnung basierend auf Ihren Anforderungen – Azure
description: Hier erfahren Sie, wie Sie die Kosten in Ihrer Rechnung organisieren können. Sie können Ihr Abrechnungskonto anpassen, indem Sie Abrechnungsprofile und Rechnungsabschnitte erstellen.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: 3243f877eb8f573cb06a6f600842763e6c67518b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131454198"
---
# <a name="organize-costs-by-customizing-your-billing-account"></a>Organisieren von Kosten durch Anpassen Ihres Abrechnungskontos

Ihr Abrechnungskonto für die Microsoft-Kundenvereinbarung bietet Ihnen die Flexibilität, Ihre Kosten basierend auf Ihren Anforderungen zu organisieren. Dies kann nach Abteilung oder Projekt ebenso wie nach Entwicklungsumgebung erfolgen.

In diesem Artikel wird beschrieben, wie Sie Ihre Kosten im Azure-Portal organisieren können. Er bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben.](#check-access-to-a-microsoft-customer-agreement)

Im Video zum [Organisieren von Kosten durch Anpassen Ihres Abrechnungskontos für die Microsoft-Kundenvereinbarung](https://www.youtube.com/watch?v=7RxTfShGHwU) erfahren Sie, wie Sie Kosten für Ihr Abrechnungskonto organisieren.

>[!VIDEO https://www.youtube.com/embed/7RxTfShGHwU]

## <a name="structure-your-account-with-billing-profiles-and-invoice-sections"></a>Strukturieren Ihres Kontos mit Abrechnungsprofilen und Rechnungsabschnitten

Im Abrechnungskonto für eine Microsoft-Kundenvereinbarung verwenden Sie Abrechnungsprofile und Rechnungsabschnitte, um Ihre Kosten zu organisieren.

![Screenshot der Abrechnungshierarchie für die Microsoft-Kundenvereinbarung](./media/mca-section-invoice/mca-hierarchy.png)

### <a name="billing-profile"></a>Abrechnungsprofil

Ein Abrechnungsprofil stellt eine Rechnung und die zugehörigen Abrechnungsinformationen wie Zahlungsmethoden und Abrechnungsadresse dar. Am Anfang jedes Kalendermonats wird in Ihrem Konto eine monatliche Rechnung für jedes Abrechnungsprofil erstellt. Die Rechnung enthält Gebühren für die Azure-Nutzung und andere Käufe aus dem Vormonat.

Ein Abrechnungsprofil wird automatisch zusammen mit Ihrem Abrechnungskonto erstellt, wenn Sie sich für Azure registrieren. Sie können zusätzliche Abrechnungsprofile erstellen, um Ihre Kosten in mehreren monatlichen Rechnungen zu organisieren.

> [!IMPORTANT]
>
> Das Erstellen zusätzlicher Abrechnungsprofile kann sich auf Ihre Gesamtkosten auswirken. Weitere Informationen finden Sie unter [Zu berücksichtigende Aspekte beim Hinzufügen neuer Abrechnungsprofile](#things-to-consider-when-adding-new-billing-profiles).

### <a name="invoice-section"></a>Rechnungsabschnitt

Ein Rechnungsabschnitt stellt eine Gruppierung von Kosten in Ihrer Rechnung dar. Für jedes Abrechnungsprofil in Ihrem Konto wird automatisch ein Rechnungsabschnitt erstellt. Sie können zusätzliche Abschnitte erstellen, um Ihre Kosten basierend auf Ihren Anforderungen zu organisieren. Jeder Rechnungsabschnitt wird auf der Rechnung mit den anfallenden Gebühren für diesen Monat angezeigt.

Die Abbildung unten zeigt eine Rechnung mit zwei Rechnungsabschnitten: Engineering und Marketing. Die Zusammenfassung und die Kostendetails für jeden Abschnitt werden in der Rechnung angezeigt. Die in der Abbildung aufgeführten Preise dienen nur zu Beispielzwecken und stellen nicht die tatsächlichen Preise der Azure-Dienste dar.

![Bild einer Rechnung mit Abschnitten](./media/mca-section-invoice/mca-invoice-with-sections.png)

## <a name="billing-account-structure-for-common-scenarios"></a>Abrechnungskontostruktur für gängige Szenarien

In diesem Abschnitt werden häufige Szenarien zum Organisieren von Kosten und die entsprechende Abrechnungskontostruktur beschrieben:

|Szenario  |Struktur  |
|---------|---------|
|Jack registriert sich bei Azure und benötigt eine einzelne monatliche Rechnung. | Ein Abrechnungsprofil und ein Rechnungsabschnitt. Diese Struktur wird automatisch für Jack eingerichtet, wenn er sich bei Azure registriert, und es sind keine weiteren Schritte erforderlich. |

![Infografik für ein Szenario mit einzelner Abrechnung](./media/mca-section-invoice/organize-billing-scenario1.png)

|Szenario  |Struktur  |
|---------|---------|
|Contoso ist eine kleine Organisation, die eine monatliche Rechnung benötigt, die Kosten jedoch nach ihren Abteilungen gruppiert: Marketing und Engineering.  | Ein Abrechnungsprofil für Contoso und je ein Rechnungsabschnitt für die Abteilungen Marketing und Engineering. |

![Infografik für ein Szenario mit mehreren Abrechnungen](./media/mca-section-invoice/organize-billing-scenario2.png)

|Szenario  |Struktur  |
|---------|---------|
|Fabrikam ist ein mittelständisches Unternehmen, das separate Rechnungen für seine Abteilungen Engineering und Marketing benötigt. Für die Engineeringabteilung sollen die Kosten nach Umgebungen gruppiert werden: Produktion und Entwicklung.  | Je ein Abrechnungsprofil für die Abteilungen Marketing und Engineering. Für die Engineeringabteilung je ein Rechnungsabschnitt für die Produktions- und die Entwicklungsumgebung. |

![Infografik für ein Szenario mit mehreren Abrechnungen, wobei für die Produktions- und Entwicklungsumgebung jeweils getrennt abgerechnet wird](./media/mca-section-invoice/organize-billing-scenario3.png)

## <a name="create-a-new-invoice-section"></a>Erstellen eines neuen Rechnungsabschnitts

Um einen Rechnungsabschnitt erstellen zu können, müssen Sie ein **Besitzer des Abrechnungsprofils** oder **Mitwirkender am Abrechnungsprofil** sein. Weitere Informationen finden Sie unter [Verwalten von Rechnungsabschnitten für das Abrechnungsprofil](understand-mca-roles.md#manage-invoice-sections-for-billing-profile).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot: Suche nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/mca-section-invoice/search-cmb.png)

3. Wählen Sie im linken Bereich die Option **Abrechnungsprofile** aus. Wählen Sie in der Liste ein Abrechnungsprofil aus. Der neue Abschnitt wird auf der Rechnung des ausgewählten Abrechnungsprofils angezeigt.

   [![Screenshot der Liste mit Abrechnungsprofilen](./media/mca-section-invoice/mca-select-profile.png)](./media/mca-section-invoice/mca-select-profile-zoomed-in.png#lightbox)

4. Wählen Sie im linken Bereich **Rechnungsabschnitte** und dann oben auf der Seite **Hinzufügen** aus.

   [![Screenshot des Hinzufügens von Rechnungsabschnitten](./media/mca-section-invoice/mca-list-invoice-sections.png)](./media/mca-section-invoice/mca-list-invoice-sections-zoomed-in.png#lightbox)

5. Geben Sie einen Namen für den Rechnungsabschnitt ein.

   [![Screenshot der Seite zum Erstellen eines Rechnungsabschnitts](./media/mca-section-invoice/mca-create-invoice-section.png)](./media/mca-section-invoice/mca-create-invoice-section-zoomed-in.png#lightbox)

6. Klicken Sie auf **Erstellen**.

## <a name="create-a-new-billing-profile"></a>Erstellen eines neuen Abrechnungsprofils

Um ein Abrechnungsprofil erstellen zu können, müssen Sie ein **Besitzer des Abrechnungskontos** oder **Mitwirkender am Abrechnungskonto** sein. Weitere Informationen finden Sie unter [Verwalten von Abrechnungsprofilen für ein Abrechnungskonto](understand-mca-roles.md#manage-billing-profiles-for-billing-account).

> [!IMPORTANT]
>
> Das Erstellen zusätzlicher Abrechnungsprofile kann sich auf Ihre Gesamtkosten auswirken. Weitere Informationen finden Sie unter [Zu berücksichtigende Aspekte beim Hinzufügen neuer Abrechnungsprofile](#things-to-consider-when-adding-new-billing-profiles).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot: Suche nach „Kostenverwaltung + Abrechnung“ im Azure-Portal](./media/mca-section-invoice/search-cmb.png)

3. Wählen Sie im linken Bereich **Abrechnungsprofile** und dann oben auf der Seite **Hinzufügen** aus.

   [![Screenshot der Liste mit Abrechnungsprofilen und ausgewählter Option „Hinzufügen“](./media/mca-section-invoice/mca-list-profiles.png)](./media/mca-section-invoice/mca-list-profiles-zoomed-in.png#lightbox)

    > [!Note]
    >
    > Wenn die Schaltfläche „Hinzufügen“ auf der Seite des Abrechnungsprofils nicht angezeigt wird, ist dieses Feature für Ihr Konto nicht verfügbar. Derzeit ist es nur für Konten verfügbar, die in Zusammenarbeit mit einem Microsoft-Vertreter eingerichtet wurden.

4. Füllen Sie das Formular aus, und klicken Sie auf **Erstellen**.

   [![Screenshot der Seite zum Erstellen eines Abrechnungsprofils](./media/mca-section-invoice/mca-add-profile.png)](./media/mca-section-invoice/mca-add-profile-zoomed-in.png#lightbox)

    |Feld  |Definition  |
    |---------|---------|
    |Name     | Ein Anzeigename, über den Sie das Abrechnungsprofil im Azure-Portal einfach identifizieren können.  |
    |Auftragsnummer    | Eine optionale Auftragsnummer. Die Auftragsnummer wird in den für das Abrechnungsprofil generierten Rechnungen angezeigt. |
    |Bill to  | Der Rechnungsempfänger wird auf den für das Abrechnungsprofil generierten Rechnungen angezeigt. |
    |Rechnung per E-Mail   | Aktivieren Sie das Feld „Rechnung per E-Mail“, um die Rechnungen für dieses Abrechnungsprofil per E-Mail zu erhalten. Wenn Sie sich nicht dafür anmelden, können Sie die Rechnungen im Azure-Portal anzeigen und herunterladen.|

5. Klicken Sie auf **Erstellen**.

## <a name="link-charges-to-invoice-sections-and-billing-profiles"></a>Verknüpfen von Gebühren mit Rechnungsabschnitten und Abrechnungsprofilen

Nachdem Sie Ihr Abrechnungskonto entsprechend Ihren Anforderungen angepasst haben, können Sie Abonnements und andere Produkte mit Ihrem gewünschten Rechnungsabschnitt und Abrechnungsprofil verknüpfen.

### <a name="link-a-new-subscription"></a>Verknüpfen eines neuen Abonnements

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Abonnements**.

   [![Screenshot der Suche im Azure-Portal nach einem Abonnement](./media/mca-section-invoice/search-subscriptions.png)](./media/mca-section-invoice/search-subscriptions.png#lightbox)

3. Wählen Sie oben auf der Seite die Option **Hinzufügen**.

   ![Screenshot der Schaltfläche „Hinzufügen“ in der Ansicht „Abonnements“ für ein neues Abonnement](./media/mca-section-invoice/subscription-add.png)

4. Wenn Sie Zugriff auf mehrere Abrechnungskonten haben, wählen Sie das Abrechnungskonto Ihrer Microsoft-Kundenvereinbarung aus.

   ![Screenshot des Bereichs „Abonnement erstellen“](./media/mca-section-invoice/mca-create-azure-subscription.png)

5. Wählen Sie das Abrechnungsprofil aus, dem die Abonnementnutzung berechnet wird. Die Gebühren für die Azure-Nutzung und andere Käufe für dieses Abonnement werden der Rechnung des ausgewählten Abrechnungsprofils angerechnet.

6. Wählen Sie den Rechnungsabschnitt aus, mit dem die Gebühren des Abonnements verknüpft werden sollen. Die Gebühren werden in diesem Abschnitt auf der Rechnung des Abrechnungsprofils angezeigt.

7. Wählen Sie einen Azure-Plan aus, und geben Sie einen Anzeigenamen für das Abonnement ein.

9. Klicken Sie auf **Erstellen**.  

### <a name="link-existing-subscriptions-and-products"></a>Verknüpfen vorhandener Abonnements und Produkte

Wenn Sie bereits über Azure-Abonnements oder andere Produkte wie Azure Marketplace- und AppSource-Ressourcen verfügen, können Sie diese aus ihrem bisherigen Rechnungsabschnitt in einen anderen verschieben, um Ihre Kosten neu zu organisieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot: Suche im Azure-Portal nach „Kostenverwaltung + Abrechnung“](./media/mca-section-invoice/search-cmb.png)

3. Wenn Sie ein Abonnement mit einem neuen Rechnungsabschnitt verknüpfen möchten, wählen Sie auf der linken Seite des Bildschirms **Azure-Abonnements** aus. Wählen Sie für andere Produkte wie Azure Marketplace- und AppSource-Ressourcen **Laufende Gebühren** aus.

   [![Screenshot der Option zum Ändern des Rechnungsabschnitts](./media/mca-section-invoice/mca-select-change-invoice-section.png)](./media/mca-section-invoice/mca-select-change-invoice-section-zoomed-in.png#lightbox)

4. Klicken Sie auf der Seite auf das Auslassungszeichen (drei Punkte) für das Abonnement oder Produkt, das Sie mit einem neuen Rechnungsabschnitt verknüpfen möchten. Wählen Sie **Rechnungsabschnitt ändern** aus.

5. Wählen Sie in der Dropdownliste das neue Abrechnungsprofil und den Rechnungsabschnitt aus.

    [![Screenshot der Auswahl eines neuen Rechnungsabschnitts](./media/mca-section-invoice/mca-select-new-invoice-section.png)](./media/mca-section-invoice/mca-select-new-invoice-section-zoomed-in.png#lightbox)

6. Wählen Sie **Speichern** aus.

## <a name="things-to-consider-when-adding-new-billing-profiles"></a>Zu berücksichtigende Aspekte beim Hinzufügen neuer Abrechnungsprofile

### <a name="azure-usage-charges-may-be-impacted"></a>Dies kann Auswirkungen auf die Azure-Nutzungsgebühren haben.

In Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung wird jeden Monat die Azure-Nutzung für jedes Abrechnungsprofil aggregiert. Die Preise für Azure-Ressourcen mit mehrstufigen Preisen werden basierend auf der Nutzung der einzelnen Abrechnungsprofile separat ermittelt. Die Nutzung wird bei der Preisberechnung nicht über Abrechnungsprofile hinweg aggregiert. Dies kann sich auf die Gesamtkosten der Azure-Nutzung für Konten mit mehreren Abrechnungsprofilen auswirken.

Hier ist ein Beispiel dafür, wie sich die Kosten für zwei Szenarien unterscheiden. Die in den Szenarien verwendeten Preise dienen nur zu Beispielzwecken und stellen nicht die tatsächlichen Preise der Azure-Dienste dar.

#### <a name="you-only-have-one-billing-profile"></a>Sie verfügen nur über ein Abrechnungsprofil.

Angenommen, Sie verwenden Azure Block Blob Storage mit einem Preis von 0,00184 USD pro GB für die ersten 50 TB (Terabyte) und dann 0,00177 USD pro GB für die nächsten 450 TB. Sie haben 100 TB in den Abonnements verbraucht, die dem Abrechnungsprofil zugeordnet und Ihnen in Rechnung gestellt werden.

|  Tarifpreis (USD) |Menge | Betrag (USD)|
|---------|---------|---------|
|1,84 pro TB für die ersten 50 TB/Monat    | 50 TB        | 92,0   |
|1,77 pro TB für die nächsten 450 TB/Monat    |  50 TB         | 88,5   |
|Gesamt     |     100 TB  | 180,5

Die Gesamtkosten für die Nutzung von 100 TB Daten in diesem Szenario betragen **180,5**.

#### <a name="you-have-multiple-billing-profiles"></a>Sie verfügen über mehrere Abrechnungsprofile.

Gehen Sie nun davon aus, dass Sie ein weiteres Abrechnungsprofil erstellt und 50 GB über Abonnements verwendet haben, die dem ersten Abrechnungsprofil angerechnet werden, und 50 GB über Abonnements, die dem zweiten Abrechnungsprofil angerechnet werden.

`Charges for the first billing profile`

|  Tarifpreis (USD) |Menge | Betrag (USD)|
|---------|---------|---------|
|1,84 pro TB für die ersten 50 TB/Monat    | 50 TB        | 92,0  |
|1,77 pro TB für die nächsten 450 TB/Monat    |  0 TB         | 0,0  |
|Gesamt     |     50 TB  | 92,0

`Charges for the second billing profile`

|  Tarifpreis (USD) |Menge | Betrag (USD)|
|---------|---------|---------|
|1,84 pro TB für die ersten 50 TB/Monat    | 50 TB        | 92,0  |
|1,77 pro TB für die nächsten 450 TB/Monat    |  0 TB         | 0,0  |
|Gesamt     |     50 TB  | 92,0

Die Gesamtkosten für die Nutzung von 100 TB Daten in diesem Szenario betragen **184,0** (92,0 × 2).

### <a name="azure-reservation-benefits-might-not-apply-to-all-subscriptions"></a>Azure-Reservierungsvorteile gelten möglicherweise nicht für alle Abonnements.

Azure-Reservierungen mit gemeinsamem Bereich werden auf Abonnements in einem einzelnen Abrechnungsprofil angewandt und nicht über Abrechnungsprofile hinweg gemeinsam genutzt.

![Infografik für Reservierungsanwendung für differenzierte Abrechnungskontostruktur](./media/mca-section-invoice/mca-reservations-benefits-by-bg.png)

In der obigen Abbildung verfügt Contoso über zwei Abonnements. Der Azure-Reservierungsvorteil wird unterschiedlich angewandt, je nachdem, wie das Abrechnungskonto strukturiert ist. Im linken Szenario wird der Reservierungsvorteil auf beide Abonnements angewandt, die dem Engineering-Abrechnungsprofil angerechnet werden. Im rechten Szenario wird der Reservierungsvorteil nur auf Abonnement 1 angewandt, da nur dieses Abonnement dem Engineering-Abrechnungsprofil angerechnet wird.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines zusätzlichen Azure-Abonnements für eine Microsoft-Kundenvereinbarung](create-subscription.md)
- [Verwalten von Abrechnungsrollen im Azure-Portal](understand-mca-roles.md#manage-billing-roles-in-the-azure-portal)
- [Anfordern des Abrechnungsbesitzes von Azure-Abonnements von Benutzern in anderen Abrechnungskonten](mca-request-billing-ownership.md)