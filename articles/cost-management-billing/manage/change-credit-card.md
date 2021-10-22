---
title: Ändern Ihrer Kreditkarte für Azure
description: Es wird beschrieben, wie Sie die für die Zahlung eines Azure-Abonnements verwendete Kreditkarte ändern.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 07/27/2021
ms.author: banders
ms.openlocfilehash: 18d8a363a21e8086b3ebcd01c0b32b0ae119a483
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000421"
---
# <a name="add-or-update-a-credit-card-for-azure"></a>Hinzufügen oder Aktualisieren einer Kreditkarte für Azure

Dieses Dokument gilt für Kunden, die sich online mit einer Kreditkarte für Azure registriert haben.

Im Azure-Portal können Sie Ihre Standardzahlungsmethode auf eine neue Kreditkarte festlegen und Ihre Kreditkartendetails aktualisieren. Diese Änderungen können nur von einem [Kontoadministrator](add-change-subscription-administrator.md#whoisaa) oder von einem Benutzer mit den entsprechenden [MCA-Berechtigungen](understand-mca-roles.md) vorgenommen werden.

Wenn Sie eine Kreditkarte löschen möchten, finden Sie weitere Informationen hierzu unter [Löschen einer Zahlungsmethode für die Azure-Abrechnung](delete-azure-payment-method.md).

Für Microsoft Azure werden Kreditkarten und Scheck/Überweisung als Zahlungsmethode unterstützt. Informationen zum Beantragen der Zahlung per Scheck/Überweisung finden Sie unter [Zahlen für Ihr Azure-Abonnement auf Rechnung](pay-by-invoice.md).

Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, sind Ihre Zahlungsmethoden mit Abrechnungsprofilen verknüpft. Informationen zum Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung finden Sie [hier](#check-the-type-of-your-account). Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, fahren Sie mit [Verwalten von Kreditkarten für eine Microsoft-Kundenvereinbarung](#manage-credit-cards-for-a-microsoft-customer-agreement) fort.

>[!NOTE]
> Wenn Sie ein neues Abonnement erstellen, können Sie eine neue Kreditkarte angeben. In diesem Fall werden der neuen Kreditkarte keine anderen Abonnements zugeordnet. Wenn Sie jedoch später eine der folgenden Änderungen vornehmen, verwenden *alle Abonnements* die von Ihnen ausgewählte Zahlungsmethode.
  >- Aktivieren einer Zahlungsmethode mit der Option **Als aktiv festlegen**
  >- Verwenden der Zahlungsoption **Ersetzen** für ein beliebiges Abonnement
  >- Ändern der Standardzahlungsmethode

<a id="addcard"></a>

## <a name="manage-credit-cards-for-an-azure-subscription"></a>Verwalten von Kreditkarten für ein Azure-Abonnement

Die folgenden Abschnitte gelten für Kunden, die über ein Abrechnungskonto für das Microsoft Online Services-Programm verfügen. Informationen zum Überprüfen des Abrechnungskontotyps finden Sie [hier](#check-the-type-of-your-account). Wenn es sich bei Ihrem Abrechnungskonto um ein Konto vom Typ „Microsoft Online Services-Programm“ handelt, sind Zahlungsmethoden mit einzelnen Azure-Abonnements verknüpft. Wenn nach dem Hinzufügen der Kreditkarte eine Fehlermeldung ausgegeben wird, befolgen Sie die Anleitung unter [Ablehnung der Kreditkarte bei der Azure-Registrierung](./troubleshoot-declined-card.md).

### <a name="change-credit-card-for-all-subscriptions-by-adding-a-new-credit-card"></a>Ändern der Kreditkarte für alle Abonnements durch Hinzufügen einer neuen Kreditkarte

Sie können die Standardkreditkarte Ihres Azure-Abonnements im Azure-Portal auf eine neue oder zuvor gespeicherte Kreditkarte festlegen. Sie müssen der Kontoadministrator sein, um die Kreditkarte ändern zu können. 

Falls mehrere Abonnements über die gleiche aktive Zahlungsmethode verfügen, wird durch die Änderung der Standardzahlungsmethode für eines der Abonnements auch die aktive Zahlungsmethode für die anderen Abonnements aktualisiert.

Wenn Sie die Standardkreditkarte Ihres Abonnements auf eine neue Kreditkarte festlegen möchten, gehen Sie wie folgt vor:

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Screenshot der Suche" lightbox="./media/change-credit-card/search.png" :::
1. Wählen Sie das Abonnement aus, dem Sie die Kreditkarte hinzufügen möchten.
1. Wählen Sie die Option **Zahlungsmethoden**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Screenshot der ausgewählten Option „Zahlungsmethoden verwalten“" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. Wählen Sie oben links **+ Hinzufügen** aus, um eine Karte hinzuzufügen. Auf der rechten Seite wird ein Kreditkartenformular angezeigt.
1. Geben Sie die Details der Kreditkarte ein.  
    :::image type="content" source="./media/change-credit-card/sub-add-new-default.png" alt-text="Screenshot des Hinzufügens einer neuen Karte" lightbox="./media/change-credit-card/sub-add-new-default.png" :::
1. Um diese Karte als Standardzahlungsmethode festzulegen, wählen Sie über dem Formular die Option **Als Standardzahlungsmethode festlegen** aus. Diese Karte wird zum aktiven Zahlungsmittel für alle Abonnements, indem dieselbe Karte wie für das ausgewählte Abonnement verwendet wird.
1. Klicken Sie auf **Weiter**.

### <a name="replace-credit-card-for-a-subscription-to-a-previously-saved-credit-card"></a>Ersetzen der Kreditkarte für ein Abonnement durch eine zuvor gespeicherte Kreditkarte

Die Standardkreditkarte eines Abonnements kann anhand der folgenden Schritte auch durch eine Kreditkarte ersetzt werden, die bereits in Ihrem Konto gespeichert ist. Mit diesem Verfahren wird die Kreditkarte für alle anderen Abonnements geändert.

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.  
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Screenshot der Suche nach „Kostenverwaltung + Abrechnung“" lightbox="./media/change-credit-card/search.png" :::
1. Wählen Sie das Abonnement aus, dem Sie die Kreditkarte hinzufügen möchten.
1. Wählen Sie die Option **Zahlungsmethoden**.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Screenshot der Option „Zahlungsmethoden verwalten“" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. Wählen Sie **Ersetzen** aus, um die aktuelle Kreditkarte in eine ausgewählte Kreditkarte zu ändern.
    :::image type="content" source="./media/change-credit-card/replace-credit-card.png" alt-text="Screenshot der Option „Ersetzen“" lightbox="./media/change-credit-card/replace-credit-card.png" :::
1. Wählen Sie unter **Standardzahlungsmethode ersetzen** eine andere Kreditkarte aus, um die Standardkreditkarte zu ersetzen, und wählen Sie dann **Weiter** aus.
    :::image type="content" source="./media/change-credit-card/replace-default-payment-method.png" alt-text="Screenshot des Bereichs „Standardzahlungsmethode ersetzen“" lightbox="./media/change-credit-card/replace-default-payment-method.png" :::
1. Nach einigen Augenblicken sehen Sie eine Bestätigung, dass Ihre Zahlungsmethode geändert wurde.

### <a name="edit-credit-card-details"></a>Bearbeiten von Kreditkartendetails

Wenn Ihre Kreditkarte verlängert wird und die Kreditkartennummer gleich bleibt, können Sie die bereits vorhandenen Kreditkartendetails (z. B. das Ablaufdatum) aktualisieren. Wenn sich die Kreditkartennummer aufgrund von Verlust, Diebstahl oder Ablauf ändert, führen Sie die Schritte im Abschnitt [Hinzufügen einer Kreditkarte als Zahlungsmethode](#addcard) aus. Die Kartenprüfnummer müssen Sie nicht aktualisieren.

1. Melden Sie sich als Kontoadministrator beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
    :::image type="content" source="./media/change-credit-card/search.png" alt-text="Screenshot der Suche" lightbox="./media/change-credit-card/search.png" :::
1. Wählen Sie die Option **Zahlungsmethoden**.
    :::image type="content" source="./media/change-credit-card/payment-methods-blade-x.png" alt-text="Screenshot der Option „Zahlungsmethoden verwalten“" lightbox="./media/change-credit-card/payment-methods-blade-x.png" :::
1. Wählen Sie die Kreditkarte aus, die Sie bearbeiten möchten. Rechts wird ein Kreditkartenformular angezeigt.
    :::image type="content" source="./media/change-credit-card/edit-card-x.png" alt-text="Screenshot des Bereichs „Zahlungsmethode bearbeiten“" lightbox="./media/change-credit-card/edit-card-x.png" :::
1. Aktualisieren Sie die Kreditkartendetails.
1. Klicken Sie auf **Weiter**.

## <a name="manage-credit-cards-for-a-microsoft-customer-agreement"></a>Verwalten von Kreditkarten für eine Microsoft-Kundenvereinbarung

Die folgenden Abschnitte gelten für Kunden, die über eine Microsoft-Kundenvereinbarung verfügen und sich online mit einer Kreditkarte für Azure registriert haben, sowie für Kunden, die über die entsprechenden [MCA-Berechtigungen](understand-mca-roles.md) verfügen. Informationen zum Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung finden Sie [hier](#check-the-type-of-your-account).

### <a name="change-default-credit-card"></a>Ändern der Standardkreditkarte

Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, ist Ihre Zahlungsmethoden mit einem Abrechnungsprofil verknüpft. Die Zahlungsmethode für ein Abrechnungsprofil kann nur von der Person geändert werden, die sich für Azure registriert und das Abrechnungskonto erstellt hat, oder von einer Person, die über die entsprechenden [MCA-Berechtigungen](understand-mca-roles.md) verfügt.

Wenn Sie die Standardzahlungsmethode Ihres Abrechnungsprofils in „Scheck/Überweisung“ ändern möchten, lesen Sie [Zahlen für Ihr Azure-Abonnement auf Rechnung](pay-by-invoice.md).

Führen Sie zum Ändern Ihrer Kreditkarte die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie im Menü auf der linken Seite **Abrechnungsprofile** aus.
1. Wählen Sie ein Abrechnungsprofil aus.
1. Wählen Sie im Menü links die Option **Zahlungsmethoden**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="Screenshot der Option „Zahlungsmethoden“ im Menü" lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. Wählen Sie im Abschnitt **Standardzahlungsmethode** die Option **Ersetzen** aus.  
    :::image type="content" source="./media/change-credit-card/change-payment-method-mca.png" alt-text="Screenshot der Option „Ersetzen“" lightbox="./media/change-credit-card/change-payment-method-mca.png" :::
1. Wählen Sie in dem neuen Bereich auf der rechten Seite entweder eine bereits vorhandene Karte aus der Dropdownliste aus, oder wählen Sie den blauen Link **Neue Zahlungsmethode hinzufügen** aus, um eine neue Karte hinzuzufügen.

### <a name="edit-a-credit-card"></a>Bearbeiten einer Kreditkarte

Sie können Kreditkartendetails im Azure-Portal bearbeiten, um beispielsweise das Ablaufdatum zu aktualisieren. 

Gehen Sie zum Bearbeiten einer Kreditkarte wie folgt vor:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie im Menü auf der linken Seite **Abrechnungsprofile** aus.
1. Wählen Sie ein Abrechnungsprofil aus.
1. Wählen Sie im Menü links die Option **Zahlungsmethoden**.  
    :::image type="content" source="./media/change-credit-card/payment-methods-tab-mca.png" alt-text="Screenshot der Option „Zahlungsmethoden“ im Menü" lightbox="./media/change-credit-card/payment-methods-tab-mca.png" :::
1. Suchen Sie im Abschnitt **Ihre Kreditkarten** nach der Kreditkarte, die Sie bearbeiten möchten.
1. Wählen Sie die Auslassungspunkte (`...`) am Ende der Zeile aus.  
    :::image type="content" source="./media/change-credit-card/edit-delete-credit-card-mca.png" alt-text="Screenshot der Auslassungspunkte" lightbox="./media/change-credit-card/edit-delete-credit-card-mca.png" :::
1. Wenn Sie Ihre Kreditkartendetails bearbeiten möchten, wählen Sie im Kontextmenü **Bearbeiten** aus.

## <a name="troubleshooting"></a>Problembehandlung

Virtuelle Karten oder Prepaidkarten werden von Azure nicht unterstützt. Wenn beim Hinzufügen oder Aktualisieren einer gültigen Kreditkarte Fehler auftreten, versuchen Sie, den Browser im privaten Modus zu öffnen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

In den folgenden Abschnitten werden häufig gestellte Fragen zum Ändern der Kreditkarteninformationen beantwortet.

### <a name="why-do-i-keep-getting-your-login-session-has-expired-please-click-here-to-log-back-in"></a>Warum wird beständig die Meldung „Ihre Anmeldesitzung ist abgelaufen. Klicken Sie hier, um sich wieder anzumelden.“ angezeigt?

Wenn diese Fehlermeldung weiterhin angezeigt wird, selbst wenn Sie sich bereits abgemeldet und dann erneut angemeldet haben, versuchen Sie es erneut mit einer privaten Browsersitzung.

### <a name="how-do-i-use-a-different-card-for-each-subscription"></a>Wie kann ich für jedes Abonnement eine andere Karte verwenden?

Wie bereits erwähnt, können Sie beim Erstellen eines neuen Abonnements eine neue Kreditkarte angeben. In diesem Fall werden der neuen Kreditkarte keine anderen Abonnements zugeordnet. Sie können mehrere neue Abonnements mit jeweils einer eigenen Kreditkarte hinzufügen. Wenn Sie jedoch später eine der folgenden Änderungen vornehmen, verwenden *alle Abonnements* die von Ihnen ausgewählte Zahlungsmethode.

- Aktivieren einer Zahlungsmethode mit der Option **Als aktiv festlegen**
- Verwenden der Zahlungsoption **Ersetzen** für ein beliebiges Abonnement
- Ändern der Standardzahlungsmethode

### <a name="how-do-i-make-payments"></a>Wie tätige ich Zahlungen?

Wenn Sie eine Kreditkarte als Zahlungsmethode eingerichtet haben, wird diese Karte nach jedem Abrechnungszeitraum automatisch belastet. Sie müssen nichts weiter tun.

Wenn Sie [auf Rechnung zahlen](pay-by-invoice.md), senden Sie Ihre Zahlung an den Empfänger, der unten auf der Rechnung angegeben ist.

### <a name="how-do-i-change-the-tax-id"></a>Wie ändere ich die Steuernummer?

Aktualisieren Sie zum Hinzufügen oder Aktualisieren der Steuernummer Ihr Profil im [Azure-Portal](https://portal.azure.com), und wählen Sie anschließend **Steuereintrag** aus. Diese Steuernummer wird auf Ihrer Rechnung angezeigt und für die Berechnung von Steuerbefreiungen verwendet.

## <a name="check-the-type-of-your-account"></a>Überprüfen Sie den Typ Ihres Abrechnungskontos.

[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Azure-Reservierungen](../reservations/save-compute-costs-reservations.md), um zu ermitteln, ob Sie damit Kosten sparen können.
- Wenn Sie eine Kreditkarte löschen möchten, finden Sie weitere Informationen hierzu unter [Löschen einer Zahlungsmethode für die Azure-Abrechnung](delete-azure-payment-method.md).