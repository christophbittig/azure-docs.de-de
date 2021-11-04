---
title: Abrechnungsrollen für Microsoft-Kundenvereinbarungen – Azure
description: Hier erhalten Sie Informationen zu Abrechnungsrollen für Abrechnungskonten für Microsoft-Kundenvereinbarungen in Azure.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: ebf9a4127a98593c714f7de86c40a8c79f145cb8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443089"
---
# <a name="understand-microsoft-customer-agreement-administrative-roles-in-azure"></a>Grundlegendes zu Verwaltungsrollen für Microsoft-Kundenvereinbarungen in Azure

Zum Verwalten Ihres Abrechnungskontos für eine Microsoft-Kundenvereinbarung verwenden Sie die in den folgenden Abschnitten beschriebenen Rollen. Diese Rollen sind zusätzlich zu den integrierten Rollen verfügbar, die in Azure zum Steuern des Zugriffs auf Ressourcen vorhanden sind. Weitere Informationen finden Sie unter [Integrierte Azure-Rollen](../../role-based-access-control/built-in-roles.md).

Dieser Artikel bezieht sich auf ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung. [Überprüfen Sie, ob Sie Zugriff auf eine Microsoft-Kundenvereinbarung haben.](#check-access-to-a-microsoft-customer-agreement)

Sehen Sie sich das [Video zur Verwaltung des Zugriffs auf Ihr MCA-Abrechnungskonto](https://www.youtube.com/watch?v=9sqglBlKkho) an, um zu erfahren, wie Sie den Zugriff auf Ihr MCA-Abrechnungskonto (Microsoft Customer Agreement, Microsoft-Kundenvereinbarung) steuern können.

>[!VIDEO https://www.youtube.com/embed/9sqglBlKkho]

## <a name="billing-role-definitions"></a>Definitionen von Abrechnungsrollen

In der folgenden Tabelle werden die Abrechnungsrollen beschrieben, die Sie zum Verwalten Ihres Abrechnungskontos, Ihrer Abrechnungsprofile und Rechnungsabschnitte verwenden.

|Role|BESCHREIBUNG|
|---|---|
|Besitzer des Abrechnungskontos|Verwalten aller Einstellungen für ein Abrechnungskonto|
|Mitwirkender am Abrechnungskonto|Verwalten aller Einstellungen außer den Berechtigungen für das Abrechnungskonto|
|Benutzer mit Leseberechtigung für Abrechnungskonto|Schreibgeschützte Ansicht aller Einstellungen für das Abrechnungskonto|
|Besitzer des Abrechnungsprofils|Verwalten aller Einstellungen für das Abrechnungsprofil|
|Mitwirkender am Abrechnungsprofil|Verwalten aller Einstellungen außer den Berechtigungen für das Abrechnungsprofil|
|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Schreibgeschützte Ansicht aller Einstellungen für das Abrechnungsprofil|
|Rechnungs-Manager|Anzeigen und Bezahlen von Rechnungen für das Abrechnungsprofil|
|Rechnungsabschnitt (Besitzer)|Verwalten aller Einstellungen für den Rechnungsabschnitt|
|Rechnungsabschnitt (Mitwirkender)|Verwalten aller Einstellungen außer den Berechtigungen für den Rechnungsabschnitt|
|Rechnungsabschnitt (Leser)|Schreibgeschützte Ansicht aller Einstellungen für den Rechnungsabschnitt|
|Azure-Abonnementersteller|Erstellen von Azure-Abonnements|

## <a name="billing-account-roles-and-tasks"></a>Rollen und Aufgaben für ein Abrechnungskonto

Ein Abrechnungskonto wird erstellt, wenn Sie sich für die Nutzung von Azure registrieren. Sie verwenden Ihr Abrechnungskonto zum Verwalten Ihrer Rechnungen und Zahlungen sowie zum Nachverfolgen von Kosten. Die Rollen für das Abrechnungskonto verfügen über die höchste Berechtigungsstufe, und Benutzer mit diesen Rollen erhalten Einblick in die Kosten- und Abrechnungsinformationen für Ihr gesamtes Konto. Weisen Sie diese Rollen nur Benutzern zu, die Rechnungen anzeigen und Kosten für Ihr gesamtes Konto nachverfolgen müssen (also beispielsweise Mitgliedern der Finanz- und Buchhaltungsteams). Weitere Informationen finden Sie unter [Grundlegendes zum Abrechnungskonto](../understand/mca-overview.md#your-billing-account).

In den folgenden Tabellen wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit dem Abrechnungskonto benötigen.

### <a name="manage-billing-account-permissions-and-properties"></a>Verwalten von Berechtigungen und Eigenschaften für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen von Rollenzuweisungen für das Abrechnungskonto|✔|✔|✔|
|Zuweisen von Berechtigungen zum Anzeigen und Verwalten des Abrechnungskontos für andere Benutzer|✔|✘|✘|
|Anzeigen von Eigenschaften des Abrechnungskontos wie Adresse, Vereinbarungen und Ähnliches|✔|✔|✔|
|Aktualisieren von Eigenschaften des Abrechnungskontos wie Käufer, Anzeigename und Ähnliches|✔|✔|✘|

### <a name="manage-billing-profiles-for-billing-account"></a>Verwalten von Abrechnungsprofilen für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller Abrechnungsprofile für das Konto|✔|✔|✔|
|Erstellen neuer Abrechnungsprofile|✔|✔|✘|

### <a name="manage-invoices-for-billing-account"></a>Verwalten von Rechnungen für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller Rechnungen für das Konto|✔|✔|✔|
|Bezahlen von Rechnungen per Kreditkarte|✔|✔|✘|
|Herunterladen von Rechnungen, Azure-Nutzungsdateien, Preisblättern und Steuerdokumenten|✔|✔|✔|

### <a name="manage-products-for-billing-account"></a>Verwalten von Produkten für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller erworbenen Produkte für das Konto|✔|✔|✔|
|Verwalten der Abrechnung für Produkte (beispielsweise Stornieren, Deaktivieren der automatischen Verlängerung und Ähnliches)|✔|✔|✘|

### <a name="manage-subscriptions-for-billing-account"></a>Verwalten von Abonnements für das Abrechnungskonto

|Aufgabe|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto|
|---|---|---|---|
|Anzeigen aller für das Abrechnungskonto erstellten Azure-Abonnements|✔|✔|✔|
|Erstellen neuer Azure-Abonnements|✔|✔|✘|
|Kündigen von Azure-Abonnements|✘|✘|✘|

## <a name="billing-profile-roles-and-tasks"></a>Rollen und Aufgaben für ein Abrechnungsprofil

Jedes Abrechnungskonto verfügt über mindestens ein Abrechnungsprofil. Ihr erstes Abrechnungsprofil wird eingerichtet, wenn Sie sich für die Verwendung von Azure registrieren. Für das Abrechnungsprofil wird eine monatliche Rechnung generiert, die alle zugehörigen Gebühren aus dem Vormonat enthält. Bei Bedarf können weitere Abrechnungsprofile eingerichtet werden. Benutzer mit Rollen in einem Abrechnungsprofil können Kosten anzeigen, ein Budget festlegen sowie Rechnungen verwalten und bezahlen. Weisen Sie diese Rollen Benutzern zu, die für die Budgetverwaltung und die Zahlung von Rechnungen für das Abrechnungsprofil zuständig sind – also beispielsweise Mitgliedern der Geschäftsverwaltung in Ihrer Organisation. Weitere Informationen finden Sie unter [Grundlegendes zu Abrechnungsprofilen](../understand/mca-overview.md#billing-profiles).

In den folgenden Tabellen wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit dem Abrechnungsprofil benötigen.

### <a name="manage-billing-profile-permissions-properties-and-policies"></a>Verwalten von Berechtigungen, Eigenschaften und Richtlinien für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen von Rollenzuweisungen für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Zuweisen von Berechtigungen zum Anzeigen und Verwalten des Abrechnungsprofils für andere Benutzer|✔|✘|✘|✘|✔|✘|✘|
|Anzeigen von Eigenschaften des Abrechnungsprofils wie Auftragsnummer, Rechnungsempfänger und Ähnliches|✔|✔|✔|✔|✔|✔|✔|
|Aktualisieren der Eigenschaften des Abrechnungsprofils |✔|✔|✘|✘|✔|✔|✘|
|Anzeigen der auf das Abrechnungsprofil angewendeten Richtlinien wie Azure-Reservierungseinkäufe, Azure Marketplace-Einkäufe und Ähnliches|✔|✔|✔|✔|✔|✔|✔|
|Anwenden von Richtlinien auf das Abrechnungsprofil |✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-invoices-for-billing-profile"></a>Verwalten von Rechnungen für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Rechnungen für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Bezahlen von Rechnungen per Kreditkarte|✔|✔|✘|✔|✔|✘|✘|
|Herunterladen von Rechnungen, Dateien zu Azure-Nutzung und -Gebühren, Preisblättern und Steuerdokumenten für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-invoice-sections-for-billing-profile"></a>Verwalten von Rechnungsabschnitten für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Rechnungsabschnitte für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Erstellen eines neuen Rechnungsabschnitts für das Abrechnungsprofil|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-billing-profile"></a>Verwalten von Produkten für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller erworbenen Produkte für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Verwalten der Abrechnung für Produkte (beispielsweise Stornieren, Deaktivieren der automatischen Verlängerung und Ähnliches)|✔|✔|✘|✘|✔|✔|✘|
|Ändern des Abrechnungsprofils für die Produkte|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-payment-methods-for-billing-profile"></a>Verwalten von Zahlungsmethoden für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen von Zahlungsmethoden für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Verwalten von Zahlungsmethoden wie etwa Ersetzen oder Trennen einer Kreditkarte und Ähnliches|✔|✔|✘|✘|✔|✔|✘|
|Nachverfolgen des Azure-Guthabens für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|

### <a name="manage-subscriptions-for-billing-profile"></a>Verwalten von Abonnements für das Abrechnungsprofil

|Aufgabe|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil|Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto
|---|---|---|---|---|---|---|---|
|Anzeigen aller Azure-Abonnements für das Abrechnungsprofil|✔|✔|✔|✔|✔|✔|✔|
|Erstellen neuer Azure-Abonnements|✔|✔|✘|✘|✔|✔|✘|
|Kündigen von Azure-Abonnements|✘|✘|✘|✘|✘|✘|✘|
|Ändern des Abrechnungsprofils für die Azure-Abonnements|✔|✔|✘|✘|✔|✔|✘|

## <a name="invoice-section-roles-and-tasks"></a>Rollen und Aufgaben für einen Rechnungsabschnitt

Jedes Abrechnungsprofil enthält standardmäßig einen Rechnungsabschnitt. Sie können weitere Rechnungsabschnitte erstellen, um die Kosten in der Rechnung des Abrechnungsprofils zu gruppieren.  Benutzer mit Rollen für einen Rechnungsabschnitt können steuern, wer Azure-Abonnements erstellen und andere Käufe tätigen kann. Weisen Sie diese Rollen Benutzern zu, die Ihre Azure-Umgebung für Teams in der Organisation einrichten. Bei diesen Benutzern handelt es sich beispielsweise um technische Leiter und technische Architekten. Weitere Informationen finden Sie unter [Grundlegendes zu Rechnungsabschnitten](../understand/mca-overview.md#invoice-sections).

In den folgenden Tabellen wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit Rechnungsabschnitten benötigen.

### <a name="manage-invoice-section-permissions-and-properties"></a>Verwalten von Berechtigungen und Eigenschaften für einen Rechnungsabschnitt

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil |Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Anzeigen der Rollenzuweisungen für den Rechnungsabschnitt|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Zuweisen von Berechtigungen zum Anzeigen und Verwalten des Rechnungsabschnitts für andere Benutzer|✔|✘|✘|✘|✔|✘|✘|✘|✔|✘|✘|
|Anzeigen der Eigenschaften des Rechnungsabschnitts|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Aktualisieren der Eigenschaften des Rechnungsabschnitts|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-products-for-invoice-section"></a>Verwalten von Produkten für einen Rechnungsabschnitt

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil |Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Anzeigen aller erworbenen Produkte für den Rechnungsabschnitt|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Verwalten der Abrechnung für Produkte (beispielsweise Stornieren, Deaktivieren der automatischen Verlängerung und Ähnliches)|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Ändern des Rechnungsabschnitts für die Produkte|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

### <a name="manage-subscriptions-for-invoice-section"></a>Verwalten von Abonnements für den Rechnungsabschnitt

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil |Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Anzeigen aller Azure-Abonnements für den Rechnungsabschnitt|✔|✔|✔|✘|✔|✔|✔|✔|✔|✔|✔|
|Erstellen von Azure-Abonnements|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Kündigen von Azure-Abonnements|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|
|Ändern des Rechnungsabschnitts für das Azure-Abonnement|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Anfordern des Abrechnungsbesitzes von Abonnements von Benutzern in anderen Abrechnungskonten|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|

## <a name="subscription-billing-roles-and-tasks"></a>Rollen und Aufgaben für die Abonnementabrechnung

In der folgenden Tabelle wird aufgezeigt, welche Rolle Sie zum Ausführen von Aufgaben im Zusammenhang mit Abonnements benötigen.

|Aufgaben|Rechnungsabschnitt (Besitzer)|Rechnungsabschnitt (Mitwirkender)|Rechnungsabschnitt (Leser)|Azure-Abonnementersteller|Besitzer des Abrechnungsprofils|Mitwirkender am Abrechnungsprofil|Benutzer mit Leseberechtigung für das Abrechnungsprofil |Rechnungs-Manager|Besitzer des Abrechnungskontos|Mitwirkender am Abrechnungskonto|Benutzer mit Leseberechtigung für Abrechnungskonto 
|---|---|---|---|---|---|---|---|---|---|---|---|
|Erstellen von Abonnements|✔|✔|✘|✔|✔|✔|✘|✘|✔|✔|✘|
|Aktualisieren der Kostenstelle für das Abonnement|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Ändern des Rechnungsabschnitts für das Abonnement|✔|✔|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Ändern des Abrechnungsprofils für das Abonnement|✘|✘|✘|✘|✔|✔|✘|✘|✔|✔|✘|
|Kündigen von Azure-Abonnements|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|✘|

## <a name="manage-billing-roles-in-the-azure-portal"></a>Verwalten von Abrechnungsrollen im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

   ![Screenshot, der die Suche im Azure-Portal zeigt](./media/understand-mca-roles/billing-search-cost-management-billing.png)

3. Wählen Sie **Zugriffssteuerung (IAM)** für einen Bereich (z. B. Abrechnungskonto, Abrechnungsprofil oder Rechnungsabschnitt) aus, für den Sie Zugriff erteilen möchten.

4. Auf der Seite „Zugriffssteuerung (IAM)“ werden die jeder Rolle für diesen Bereich zugewiesenen Benutzer und Gruppen aufgelistet.

   ![Screenshot der Liste der Administratoren für ein Abrechnungskonto](./media/understand-mca-roles/billing-list-admins.png)

5. Um einem Benutzer Zugriff zu erteilen, wählen Sie oben auf der Seite die Option **Hinzufügen** aus. Wählen Sie in der Dropdownliste „Rolle“ eine Rolle aus. Geben Sie die E-Mail-Adresse des Benutzers ein, dem Sie Zugriff erteilen möchten. Wählen Sie **Speichern** aus, um die Rolle zuzuweisen.

   ![Screenshot, der das Hinzufügen eines Administrators zu einem Abrechnungskonto zeigt](./media/understand-mca-roles/billing-add-admin.png)

6. Um den Zugriff für einen Benutzer zu entfernen, wählen Sie den Benutzer mit der Rollenzuweisung aus, den Sie entfernen möchten. Wählen Sie „Entfernen“ aus.

   ![Screenshot, der das Entfernen eines Administrators aus einem Abrechnungskonto zeigt](./media/understand-mca-roles/billing-remove-admin.png)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren
[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um Informationen zu einem Abrechnungskonto zu erhalten:

- [Erste Schritte mit Ihrem Abrechnungskonto für eine Microsoft-Kundenvereinbarung](../understand/mca-overview.md)
- [Erstellen eines Azure-Abonnements für Ihr Abrechnungskonto für eine Microsoft-Kundenvereinbarung](create-subscription.md)
