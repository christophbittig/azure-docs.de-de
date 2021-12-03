---
title: Aufgaben für Kontoadministratoren im Azure-Portal
description: Hier erfahren Sie, wie Sie Zahlungsvorgänge im Azure-Portal ausführen.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/22/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: 7fb94b8e714c62a92b0fe9411af97f69e0a50423
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229558"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Aufgaben für Kontoadministratoren im Azure-Portal

In diesem Artikel erfahren Sie, wie Sie folgende Aufgaben im Azure-Portal ausführen:
- Verwalten der Zahlungsmethoden Ihres Abonnements
- Entfernen des Ausgabenlimits Ihres Abonnements
- Hinzufügen von Guthaben zu Ihrem Azure in Open-Abonnement

Diese Aufgaben können nur vom Kontoadministrator ausgeführt werden.

## <a name="accounts-portal-is-retiring"></a>Kontenportal wird eingestellt

Das Kontenportal wird eingestellt, und Kunden werden ab 31. Dezember 2021 zum Azure-Portal umgeleitet. Die im Kontenportal unterstützten Features werden zum Azure-Portal migriert. In diesem Artikel erfahren Sie, wie Sie einige der gängigsten Vorgänge im Azure-Portal ausführen.


## <a name="navigate-to-your-subscriptions-payment-methods"></a>Navigieren zu den Zahlungsmethoden Ihres Abonnements

1. Melden Sie sich als Kontoadministrator beim Azure-Portal an.

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ ](./media/account-admin-tasks/search-bar.png)

1. Wählen Sie in der Liste **Meine Abonnements** das Abonnement aus, dem Sie die Kreditkarte hinzufügen möchten.

   ![Screenshot der Seite „Kostenverwaltung + Abrechnung“, auf der Sie ein Abonnement auswählen können.](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Wenn einige Ihrer Abonnements hier nicht angezeigt werden, liegt dies möglicherweise daran, dass Sie das Abonnementverzeichnis zu irgendeinem Zeitpunkt geändert haben. Für diese Abonnements müssen Sie das Verzeichnis in das ursprüngliche Verzeichnis ändern (das Verzeichnis, in dem Sie sich anfänglich registriert haben). Wiederholen Sie anschließend Schritt 2.

1. Wählen Sie die Option **Zahlungsmethoden**.

    ![Screenshot der Seite „Zahlungsmethoden“, auf der Sie eine Zahlungsmethode hinzufügen können](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Hier können Sie eine neue Kreditkarte hinzufügen, die aktive Zahlungsmethode ändern, Kreditkartendetails bearbeiten und Kreditkarten löschen.

### <a name="change-active-payment-method"></a>Ändern der aktiven Zahlungsmethode

Sie können die aktive Zahlungsmethode ändern, indem Sie eine neue Kreditkarte hinzufügen oder eine bereits gespeicherte Kreditkarte auswählen. So legen Sie eine neue Kreditkarte als aktive Zahlungsmethode fest:

1. Wählen Sie links oben das Pluszeichen (+) aus, um eine Kreditkarte hinzuzufügen.

    ![Screenshot: Pluszeichen](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Geben Sie im Formular auf der rechten Seite Kreditkartendetails ein.

    ![Screenshot: Formular zum Hinzufügen einer Kreditkarte](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Aktivieren Sie oberhalb des Formulars das Kontrollkästchen **Diese Zahlungsmethode als aktive Methode festlegen**, um diese Karte als Ihre aktive Zahlungsmethode festzulegen. Diese Karte wird zum aktiven Zahlungsmittel für alle Abonnements, indem dieselbe Karte wie für das ausgewählte Abonnement verwendet wird.

    ![Screenshot: Kontrollkästchen, um die Karte als aktive Zahlungsmethode festzulegen](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Wählen Sie **Weiter** aus.

So legen Sie die aktive Zahlungsmethode auf eine bereits gespeicherte Kreditkarte fest:

1. Aktivieren Sie das Kontrollkästchen neben der Karte, die Sie als aktive Zahlungsmethode festlegen möchten.

    ![Screenshot: Aktiviertes Kontrollkästchen neben der Kreditkarte](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klicken Sie auf der Befehlsleiste auf **Als aktiv festlegen**.

    ![Screenshot: Schaltfläche „Als aktiv festlegen“](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Bearbeiten von Kreditkartendetails

Wenn Sie Kreditkartendetails wie das Ablaufdatum oder die Adresse bearbeiten möchten, klicken Sie auf die Kreditkarte, die Sie bearbeiten möchten. Rechts wird ein Kreditkartenformular angezeigt.

![Screenshot: Ausgewählte Kreditkarte](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Aktualisieren Sie die Kreditkartendetails, und klicken Sie anschließend auf **Speichern**.

### <a name="remove-a-credit-card-from-the-account"></a>Entfernen einer Kreditkarte aus dem Konto

1. Aktivieren Sie das Kontrollkästchen neben der Karte, die Sie löschen möchten.

    ![Screenshot: Aktiviertes Kontrollkästchen neben der Kreditkarte](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klicken Sie auf der Befehlsleiste auf **Löschen**.

    ![Screenshot: Schaltfläche „Löschen“](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Wenn Ihre Kreditkarte die aktive Zahlungsmethode für eines Ihrer Microsoft-Abonnements ist, können Sie sie nicht aus Ihrem Azure-Konto entfernen. Ändern Sie die aktive Zahlungsmethode für alle Abonnements, die mit dieser Kreditkarte verknüpft sind, und versuchen Sie es noch mal.

### <a name="switch-to-invoice-payment"></a>Umstellen auf Zahlung per Rechnung

Wenn Sie zur Zahlung per Rechnung (Scheck/Überweisung) berechtigt sind, können Sie Ihr Abonnement im Azure-Portal auf Zahlung per Rechnung (Scheck/Überweisung) umstellen.

1. Wählen Sie auf der Befehlsleiste die Option **Zahlung per Rechnung** aus.

    ![Screenshot der Seite „Zahlungsmethoden“ mit ausgewählter Option „Zahlung per Rechnung“](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Geben Sie die Adresse für die Zahlungsmethode „Rechnung“ ein.
1. Klicken Sie auf **Weiter**.

Informationen zum Beantragen der Zahlung per Rechnung finden Sie unter [Zahlen für Ihr Azure-Abonnement auf Rechnung](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Bearbeiten der Adresse für die Zahlung per Rechnung

Wenn Sie die Adresse für die Zahlungsmethode „Rechnung“ bearbeiten möchten, klicken Sie in der Liste mit den Zahlungsmethoden für Ihr Abonnement auf **Rechnung**. Daraufhin wird auf der rechten Seite das Adressformular geöffnet.

## <a name="remove-spending-limit"></a>Entfernen des Ausgabenlimits

Mit dem Ausgabenlimit in Azure wird verhindert, dass Sie Ihr Guthaben überschreiten. Wenn für Ihr Abonnement eine gültige Zahlungsmethode festgelegt wurde, können Sie das Ausgabenlimit jederzeit entfernen. Bei Abonnementtypen mit Guthaben für mehrere Monate (beispielsweise Visual Studio Enterprise und Visual Studio Professional) kann das Ausgabenlimit zu Beginn des nächsten Abrechnungszeitraums reaktiviert werden.

Für Abonnements mit Verpflichtungsplänen oder mit nutzungsbasierter Bezahlung ist kein Ausgabenlimit verfügbar.

1. Melden Sie sich als Kontoadministrator beim Azure-Portal an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ ](./media/account-admin-tasks/search-bar.png)

1. Wählen Sie in der Liste **Meine Abonnements** Ihr Visual Studio Enterprise-Abonnement aus.

   ![Screenshot des Bereichs „Meine Abonnements“, in dem Sie Ihr Visual Studio Enterprise-Abonnement auswählen können](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Wenn einige Ihrer Visual Studio-Abonnements hier nicht angezeigt werden, liegt dies möglicherweise daran, dass Sie ein Abonnementverzeichnis zu irgendeinem Zeitpunkt geändert haben. Für diese Abonnements müssen Sie das Verzeichnis in das ursprüngliche Verzeichnis ändern (das Verzeichnis, in dem Sie sich anfänglich registriert haben). Wiederholen Sie anschließend Schritt 2.

1. Klicken Sie in der Abonnementübersicht auf das orangefarbene Banner, um das Ausgabenlimit zu entfernen.

    ![Screenshot: Banner zum Entfernen des Ausgabenlimits](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Wählen Sie aus, ob Sie das Ausgabenlimit dauerhaft oder nur für den aktuellen Abrechnungszeitraum entfernen möchten.

   ![Screenshot: Blatt zum Entfernen des Ausgabenlimits](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Klicken Sie auf **Zahlungsmethode auswählen**, um eine Zahlungsmethode für Ihr Abonnement auszuwählen. Diese wird als aktive Zahlungsmethode für Ihr Abonnement verwendet.

1. Klicken Sie auf **Fertig stellen**.

## <a name="add-credits-to-azure-in-open-subscription"></a>Hinzufügen von Guthaben zu einem Azure in Open-Abonnement

Wenn Sie über ein Azure in Open License-Abonnement verfügen, können Sie Ihrem Abonnement im Azure-Portal Guthaben hinzufügen, indem Sie einen Product Key einlösen oder Guthaben mit einer Kreditkarte erwerben.

1. Melden Sie sich als Kontoadministrator beim Azure-Portal an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ ](./media/account-admin-tasks/search-bar.png)

1. Wählen Sie in der Liste **Meine Abonnements** Ihr Azure in Open-Abonnement aus.

    ![Screenshot des Bereichs „Meine Abonnements“, in dem Sie Ihr Azure in Open-Abonnement auswählen können](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Wenn Ihr Abonnement hier nicht angezeigt wird, liegt dies möglicherweise daran, dass Sie das zugehörige Verzeichnis zu irgendeinem Zeitpunkt geändert haben. Sie müssen das Verzeichnis dieses Abonnements in das ursprüngliche Verzeichnis ändern (das Verzeichnis, in dem Sie sich anfänglich registriert haben). Wiederholen Sie anschließend Schritt 2.

1. Wählen Sie **Bonitätsgeschichte** aus.

    ![Screenshot: Bonitätsgeschichte](./media/account-admin-tasks/aio-credit-history-blade.png)

1. Wählen Sie links oben das Pluszeichen (+) aus, um Guthaben hinzuzufügen.

    ![Screenshot: Schaltfläche zum Hinzufügen von Guthaben](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Wählen Sie im Dropdownfeld einen Zahlungsmethodentyp aus. Sie können entweder einen Product Key hinzufügen oder Guthaben mit einer Kreditkarte erwerben.

    ![Screenshot: Dropdownfeld für die Zahlungsmethode auf dem Blatt „Guthaben hinzufügen“](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Wenn Sie „Product Key“ ausgewählt haben, gehen Sie wie folgt vor:
    - Geben Sie den Product Key ein.
    - Klicken Sie auf **Überprüfen**.

1. Wenn Sie „Kreditkarte“ ausgewählt haben, gehen Sie wie folgt vor:
    - Klicken Sie auf **Zahlungsmethode auswählen**, um eine Kreditkarte hinzuzufügen oder eine vorhandene Kreditkarte auszuwählen.
    - Geben Sie an, wie viel Guthaben Sie hinzufügen möchten.

1. Klicken Sie auf **Anwenden**.

## <a name="usage-details-files-comparison"></a>Vergleich von Dateien mit Nutzungsdetails

Anhand der folgenden Informationen können Sie die Zuordnung zwischen den Feldern, die in den Versionen V1 und V2 der Dateien im Kontenportal verfügbar sind, und der aktuellen Version der Datei mit Nutzungsdetails im Azure-Portal ermitteln.

| V1 | V2 | Azure-Portal |
| --- | --- | --- |
| Zusätzliche Informationen | Zusätzliche Informationen | AdditionalInfo |
| Währung | Währung | BillingCurrency |
| Billing Period | Billing Period | BillingPeriodEndDate |
| Billing Period | Billing Period | BillingPeriodStartDate |
| Dienst | Consumed Service | ConsumedService |
| Wert | Wert | Kosten |
| Usage Date | Usage Date | Datum |
| Name | Meter Category | MeterCategory |
| ResourceGuid | ID der Verbrauchseinheit | MeterId |
| Region | Meter Region | MeterRegion |
| Resource | Meter Name | MeterName  |
| Typ | Unterkategorie für Verbrauchseinheit | MeterSubcategory |
| Consumed | Verbrauchte Menge | Menge |
| Komponente | Ressourcengruppe | ResourceGroup |
|   | Instanzen-ID | resourceId |
| Sub Region | Resource Location | ResourceLocation |
| Service Info 1 | Service Info 1 | ServiceInfo1 |
| Service Info 2 | Service Info 2 | ServiceInfo2 |
| Abonnement-ID | Abonnement-ID | SubscriptionId |
| Subscription Name | Subscription Name | SubscriptionName |
|   | `Tags` | `Tags` |
| Einheit | Einheit | UnitOfMeasure |
| | Rate | UnitPrice |

Weitere Informationen zu den Feldern, die in der aktuellen Datei mit Nutzungsdetails verfügbar sind, finden Sie unter [Grundlegendes zu den Bedingungen in der Datei für die Azure-Nutzung und -Gebühren](../understand/understand-usage.md).

Die folgenden Felder stammen aus den Versionen V1 und V2 der Dateien aus dem Kontenportal. Sie sind in der aktuellen Datei mit Nutzungsdetails nicht mehr verfügbar.

| V1 | V2 |
| --- | --- |
| Auftrags-ID | Auftrags-ID |
| BESCHREIBUNG | BESCHREIBUNG |
| Abrechnungsdatum (Jahrestag) | Abrechnungsdatum (Jahrestag) |
| Angebotsname | Angebotsname |
| Service Name | Service Name |
| Abonnementstatus | Abonnementstatus |
| Zusätzlicher Abonnementstatus | Zusätzlicher Abonnementstatus |
| Bereitstellungsstatus | Bereitstellungsstatus |
| SKU | SKU |
| Enthalten | Included Quantity |
| Billable | Overage Quantity |
| Within Commitment | Within Commitment |
| Commitment Rate | Commitment Rate |
| Überschreitung | Überschreitung |
| Komponente |  |

## <a name="troubleshooting"></a>Problembehandlung
Virtuelle Karten oder Prepaidkarten werden nicht unterstützt. Wenn beim Hinzufügen oder Aktualisieren einer gültigen Kreditkarte Fehler auftreten, versuchen Sie, den Browser im privaten Modus zu öffnen.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen über das [Analysieren unerwarteter Gebühren](../understand/analyze-unexpected-charges.md)
