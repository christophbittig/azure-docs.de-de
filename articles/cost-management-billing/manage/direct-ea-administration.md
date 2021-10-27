---
title: Azure-Portal-Verwaltung für direkte Enterprise Agreements
description: In diesem Artikel werden die häufigsten Aufgaben erläutert, die direkte Unternehmensadministratoren im Azure-Portal durchführen.
author: bandersmsft
ms.author: banders
ms.date: 10/11/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: d958ea620e16dbf7ab4e9181d05508246b832907
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007280"
---
# <a name="azure-portal-administration-for-direct-enterprise-agreements"></a>Azure-Portal-Verwaltung für direkte Enterprise Agreements

In diesem Artikel werden die häufigsten Aufgaben erläutert, die direkte Enterprise Agreement-Administratoren (EA) im [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) durchführen. Ein direktes Enterprise Agreement wird zwischen Microsoft und einem Enterprise Agreement-Kunden abgeschlossen.

Umgekehrt wird ein indirektes EA zwischen einem Kunden und einem Microsoft-Partner abgeschlossen. Indirekte EAs werden über das [Azure Enterprise-Portal](https://ea.azure.com/) verwaltet. Weitere Informationen zum Verwalten indirekter EAs finden Sie unter [Verwaltung des Azure EA-Portals](ea-portal-administration.md).

## <a name="manage-your-enrollment"></a>Verwalten Ihrer Registrierung

Um den Dienst zu verwalten, öffnet der erste Unternehmensadministrator das  [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) und meldet sich mit der E-Mail-Adresse aus der Einladungs-E-Mail an.

Wenn Sie als Unternehmensadministrator eingerichtet wurden, wechseln Sie zum Azure-Portal, und melden Sie sich mit Ihrer Geschäfts-, Schul- oder Microsoft-Konto-E-Mail-Adresse und ihrem Kennwort an.

Wenn Sie über mehrere Abrechnungskonten verfügen, wählen Sie im Abrechnungsbereichsmenü ein Abrechnungskonto aus. Sie können die Eigenschaften und Richtlinien Ihres Abrechnungskontos im linken Menü anzeigen.

## <a name="select-a-billing-scope"></a>Auswählen eines Abrechnungsbereichs

Enterprise Agreements und die Kunden, die auf die Agreements zugreifen, können über mehrere Registrierungen verfügen. Ein Benutzer kann Zugriff auf mehrere Registrierungsbereiche (Abrechnungskontobereiche) haben. Alle Informationen und Aktivitäten im Azure-Portal befinden sich im Kontext eines Abrechnungskontobereichs. Es ist wichtig, dass der Unternehmensadministrator zuerst einen Abrechnungsbereich auswählt und dann Verwaltungsaufgaben übernimmt.

### <a name="to-select-a-billing-scope"></a>Auswählen eines Abrechnungsbereichs

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**, und wählen Sie diese Option aus.  
    :::image type="content" source="./media/direct-ea-administration/search-cost-management.png" alt-text="Screenshot der Suche nach „Kostenverwaltung + Abrechnung“." lightbox="./media/direct-ea-administration/search-cost-management.png" :::
1. Wählen Sie im Navigationsmenü die Option **Abrechnungsbereiche** und dann das Abrechnungskonto aus, mit dem Sie arbeiten möchten.  
    :::image type="content" source="./media/direct-ea-administration/select-billing-scope.png" alt-text="Screenshot: Auswählen eines Abrechnungskontos." lightbox="./media/direct-ea-administration/select-billing-scope.png" :::

## <a name="view-enrollment-details"></a>Anzeigen von Registrierungsdetails

Ein Azure-Unternehmensadministrator (EA-Administrator) kann Registrierungseigenschaften und -richtlinien anzeigen und verwalten, um sicherzustellen, dass die Registrierungseinstellungen ordnungsgemäß konfiguriert sind.

### <a name="to-view-enrollment-properties"></a>So zeigen Sie Registrierungseigenschaften an

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im Navigationsmenü unter **Einstellungen** die Option **Eigenschaften** aus.
1. Rufen Sie Informationen zum Abrechnungskonto ab.  
    :::image type="content" source="./media/direct-ea-administration/billing-account-properties.png" alt-text="Screenshot der Abrechnungskontoeigenschaften." lightbox="./media/direct-ea-administration/billing-account-properties.png" :::

### <a name="view-and-manage-enrollment-policies"></a>Anzeigen und Verwalten von Registrierungsrichtlinien

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im Navigationsmenü **Richtlinien** aus.
1. Aktivieren oder deaktivieren Sie Richtlinien, indem Sie **Ein** oder **Aus** auswählen.  
    :::image type="content" source="./media/direct-ea-administration/enrollment-policies.png" alt-text="Screenshot: EA-Registrierungsrichtlinien." lightbox="./media/direct-ea-administration/enrollment-policies.png" :::

Weitere Informationen zum Abteilungsadministrator (Department Admin, DA) und zum Kontobesitzer (Account Owner, AO) finden Sie unter [Preise für verschiedene Benutzerrollen](understand-ea-roles.md#see-pricing-for-different-user-roles).

## <a name="add-another-enterprise-administrator"></a>Hinzufügen eines weiteren Unternehmensadministrators

Nur bestehende EA-Administratoren können andere Unternehmensadministratoren erstellen. Wählen Sie basierend auf Ihrer Situation eine der folgenden Optionen aus:

### <a name="if-youre-already-an-enterprise-administrator"></a>Sie sind bereits Unternehmensadministrator:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie im oberen Menü **+ Hinzufügen** und dann **Unternehmensadministrator** aus.  
    :::image type="content" source="./media/direct-ea-administration/add-enterprise-admin-navigate.png" alt-text="Screenshot: Navigation zum Unternehmensadministrator." lightbox="./media/direct-ea-administration/add-enterprise-admin-navigate.png" :::
1. Füllen Sie das Formular „Rollenzuweisung hinzufügen“ aus, und wählen Sie dann **Hinzufügen** aus.

Stellen Sie sicher, dass Sie über die E-Mail-Adresse und die bevorzugte Authentifizierungsmethode des Benutzers verfügen, z. B. ein Geschäfts-, Schul-, Uni- oder Microsoft-Konto.

Ein EA-Administrator kann den Zugriff für bestehende Unternehmensadministratoren verwalten, indem er das Auslassungszeichen ( **...** ) rechts neben dem jeweiligen Benutzer auswählt. Sie können vorhandene Benutzer **Bearbeiten** und **Löschen**.

### <a name="if-youre-not-an-enterprise-administrator"></a>Sie sind kein Unternehmensadministrator:

Wenn Sie kein EA-Administrator sind, wenden Sie sich an Ihren EA-Administrator, um anzufordern, dass er Sie einer Registrierung hinzufügt. Der EA-Administrator führt die vorherigen Schritte aus, um Sie als Unternehmensadministrator hinzuzufügen. Nachdem Sie einer Registrierung hinzugefügt wurden, erhalten Sie eine Aktivierungs-E-Mail.

### <a name="if-your-enterprise-administrator-cant-help-you"></a>Ihr Unternehmensadministrator kann Ihnen nicht helfen:

Wenn Ihr Unternehmensadministrator Ihnen nicht helfen kann, erstellen Sie eine  [Azure-Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Geben Sie die folgenden Informationen ein:

- Registrierungsnummer
- Hinzuzufügende E-Mail-Adresse und Authentifizierungstyp (Geschäfts-, Schul-, Uni- oder Microsoft-Konto)
- E-Mail-Genehmigung von einem vorhandenen Unternehmensadministrator

Wenn der vorhandene Unternehmensadministrator nicht verfügbar ist, bitten Sie Ihren Partner oder Softwareberater, die Kontaktinformationen über das VLSC-Tool (Volume Licensing Service Center) zu ändern.

## <a name="create-an-azure-enterprise-department"></a>Erstellen einer Azure-Unternehmensabteilung

EA-Administratoren und Abteilungsadministratoren verwenden Abteilungen zum Organisieren und Erstellen von Berichten zu Azure-Diensten für Unternehmen und zur Nutzung nach Abteilung und Kostenstelle. Der Unternehmensadministrator kann folgende Aktionen ausführen:

- Hinzufügen oder Entfernen von Abteilungen
- Zuordnen eines Kontos zu einer Abteilung
- Erstellen von Abteilungsadministratoren
- Zulassen, dass Abteilungsadministratoren Preise und Kosten anzeigen

Ein Abteilungsadministrator kann seinen Abteilungen neue Konten hinzufügen. Er kann auch Konten aus den Abteilungen entfernen, nicht jedoch aus der Registrierung.

### <a name="to-create-a-department"></a>So erstellen Sie eine Abteilung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im Navigationsmenü auf der linken Seite **Abteilungen** aus.
1. Klicken Sie auf **+ Hinzufügen**.  
    :::image type="content" source="./media/direct-ea-administration/add-department.png" alt-text="Screenshot: Navigation zum Hinzufügen einer Abteilung." lightbox="./media/direct-ea-administration/add-department.png" :::
1. Geben Sie unter „Neue Abteilung hinzufügen“ Ihre Informationen ein. Der Abteilungsname ist das einzige Pflichtfeld. Er muss mindestens drei Zeichen lang sein.
1. Klicken Sie zum Abschluss auf **Speichern**.

## <a name="add-a-department-administrator"></a>Hinzufügen eines Abteilungsadministrators

Nachdem eine Abteilung erstellt wurde, kann der EA-Administrator Abteilungsadministratoren hinzufügen und diese jeweils einer Abteilung zuordnen. Abteilungsadministratoren können für ihre Abteilungen folgende Aktionen ausführen:

- Erstellen anderer Abteilungsadministratoren
- Anzeigen und Bearbeiten von Abteilungseigenschaften wie dem Namen oder der Kostenstelle
- Hinzufügen von Konten
- Entfernen von Konten
- Herunterladen von Nutzungsdetails
- Anzeigen der monatlichen Nutzung und Gebühren <sup>1</sup>

 <sup>1</sup> Ein EA-Administrator muss die Berechtigungen erteilen.

### <a name="to-add-a-department-administrator"></a>So fügen Sie einen Abteilungsadministrator hinzu

Als Unternehmensadministrator:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im Navigationsmenü auf der linken Seite **Abteilungen** aus.
1. Wählen Sie die Abteilung aus, der Sie einen Administrator hinzufügen möchten.
1. Wählen Sie in der Abteilungsansicht **Zugriffssteuerung (IAM)** aus.
1. Wählen Sie **+ Hinzufügen** und dann **Abteilungsadministrator** aus.
1. Geben Sie die E-Mail-Adresse und andere erforderliche Informationen ein.
1. Für einen schreibgeschützten Zugriff setzen Sie die Option **Schreibgeschützt** auf **Ja** und wählen dann **Hinzufügen** aus.  
    :::image type="content" source="./media/direct-ea-administration/add-department-admin.png" alt-text="Screenshot: Navigation zum Abteilungsadministrator." lightbox="./media/direct-ea-administration/add-department-admin.png" :::

### <a name="to-set-read-only-access"></a>So legen Sie Lesezugriff fest

EA-Administratoren können Abteilungsadministratoren Lesezugriff gewähren. Wenn Sie einen neuen Abteilungsadministrator erstellen, legen Sie die Option „Schreibgeschützt“ auf **Ja** fest.

So bearbeiten Sie einen vorhandenen Abteilungsadministrator:

1. Wählen Sie rechts neben einer Zeile das Auslassungszeichen ( **...** ) aus.
1. Legen Sie die Option „Schreibgeschützt“ auf   **Ja** fest, und wählen Sie dann  **Anwenden** aus.

Unternehmensadministratoren erhalten automatisch Abteilungsadministratorberechtigungen.

## <a name="add-an-account-and-account-owner"></a>Hinzufügen eines Kontos und Kontobesitzers

Die Struktur von Konten und Abonnements wirkt sich darauf aus, wie diese verwaltet und auf Rechnungen und Berichten angezeigt werden. Zu den Beispielen für typische Organisationsstrukturen zählt die Strukturierung nach Geschäftsbereichen, funktionsbezogenen Teams und geografischen Standorten.

Nachdem der Registrierung ein neues Konto hinzugefügt wurde, wird dem Kontobesitzer eine Kontobesitz-E-Mail gesendet, die zum Bestätigen des Besitzes verwendet wird.

### <a name="to-add-an-account-and-account-owner"></a>So fügen Sie ein Konto und einen Kontobesitzer hinzu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü die Option **Konten** aus.
1. Klicken Sie auf **+ Hinzufügen**.
1. Geben Sie auf der Seite „Konto hinzufügen“ einen Anzeigenamen ein, um das Konto zu identifizieren, das für die Berichterstellung verwendet wird.
1. Geben Sie die **E-Mail-Adresse des Kontobesitzers** ein, die dem neuen Konto zugeordnet werden soll.
1. Wählen Sie eine Abteilung aus, oder treffen Sie keine Auswahl.
1. Wählen Sie zum Abschluss **Hinzufügen** aus.

### <a name="to-confirm-account-ownership"></a>So bestätigen Sie den Kontobesitz

Nachdem der Kontobesitzer eine E-Mail zum Kontobesitz erhalten hat, muss er seinen Besitz bestätigen.

1. Melden Sie sich bei dem E-Mail-Konto an, das dem Geschäfts-, Schul- oder Microsoft-Konto zugeordnet ist, das als Kontobesitzer festgelegt wurde.
1. Öffnen Sie die E-Mail-Benachrichtigung mit dem Titel _Einladung zur Aktivierung Ihres Kontos im Microsoft Azure-Dienst über die Microsoft-Volumenlizenzierung_.
1. Wählen Sie in der Einladung den Link **Konto aktivieren** aus.
1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie auf der Seite „Konto aktivieren“ die Option **Ja, ich möchte fortfahren** aus, um den Kontobesitz zu bestätigen.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="Screenshot: Seite „Konto aktivieren“." lightbox="./media/direct-ea-administration/activate-account.png" :::

Nachdem der Kontobesitz bestätigt wurde, können Sie Abonnements erstellen und Ressourcen mit den Abonnements erwerben.

## <a name="change-azure-subscription-or-account-ownership"></a>Ändern des Besitzes von Azure-Abonnements oder -Konten

EA-Administratoren können das Azure-Portal verwenden, um den Kontobesitz bestimmter oder aller Abonnements in einer Registrierung zu übertragen. Wenn Sie eine Besitzübertragung für ein Abonnement oder Konto durchführen, aktualisiert Microsoft den Kontobesitzer.

Bevor Sie mit der Besitzübertragung beginnen, machen Sie sich mit den folgenden Azure-Richtlinien für die rollenbasierte Zugriffssteuerung (Role-Based Access Control, Azure RBAC) vertraut:

- Bei der Übertragung eines Abonnements oder Kontobesitzes zwischen zwei Organisations-IDs innerhalb desselben Mandanten werden die folgenden Elemente beibehalten:
    - Azure RBAC-Richtlinien
    - Vorhandener Dienstadministrator
    - Co-Administratorrollen
- Bei mandantenübergreifenden Besitzübertragungen für Abonnements oder Konten gehen Ihre Azure RBAC-Richtlinien und Rollenzuweisungen verloren.
- Richtlinien und Administratorrollen werden nicht über verschiedene Verzeichnisse hinweg übertragen. Dienstadministratoren werden auf den Besitzer des Zielkontos aktualisiert.
- Wenn Sie den Verlust der Azure RBAC-Richtlinien und Rollenzuweisungen bei der Abonnementübertragung zwischen Mandanten vermeiden möchten, achten Sie darauf, dass das Kontrollkästchen **Abonnements in den Azure AD-Mandanten des Empfängers verschieben** deaktiviert ist. Durch diese Auswahl bleiben die Dienste, Azure-Rollen und Richtlinien im aktuellen Azure AD-Mandanten erhalten, und es wird lediglich der Abrechnungsbesitz für das Konto übertragen.

Führen Sie vor dem Ändern des Kontobesitzers die folgenden Aktionen durch:

1. Zeigen Sie die Registerkarte **Konto** an, und identifizieren Sie das Quellkonto. Das Quellkonto muss aktiv sein.
1. Identifizieren Sie das Zielkonto, und stellen Sie sicher, dass es aktiv ist.

So übertragen Sie den Kontobesitz für alle Abonnements:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü die Option **Konten** aus.
1. Wählen Sie ein **Konto** und dann rechts neben der Zeile das Auslassungszeichen ( **...** ) aus.
1. Wählen Sie **Abonnements übertragen** aus.  
    :::image type="content" source="./media/direct-ea-administration/transfer-subscriptions-01.png" alt-text="Screenshot, der zeigt, wohin Abonnements übertragen werden." lightbox="./media/direct-ea-administration/transfer-subscriptions-01.png" :::
1. Wählen Sie auf der Seite „Abonnements übertragen“ das Zielkonto für die Übertragung und dann **Weiter** aus.
1. Wenn Sie den Kontobesitz an einen anderen Azure AD-Mandanten übertragen möchten, wählen Sie die Bestätigung **Ja, ich möchte auch die Abonnements in den Azure AD-Mandanten des neuen Kontos verschieben** aus.
1. Bestätigen Sie die Übertragung, und wählen Sie  **Senden** aus.  
    :::image type="content" source="./media/direct-ea-administration/transfer-account-confirmation.png" alt-text="Screenshot: Bestätigung der Abonnementübertragung." lightbox="./media/direct-ea-administration/transfer-account-confirmation.png" :::

So übertragen Sie den Kontobesitz für ein einzelnes Abonnement:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü **Azure-Abonnements** aus.
1. Wählen Sie auf der Seite „Azure-Abonnements“ rechts von einem Abonnement das Auslassungszeichen ( **...** ) aus.
1. Klicken Sie auf **Abonnement übertragen**.
1. Wählen Sie auf der Seite „Abonnements übertragen“ das Zielkonto für die Übertragung des Abonnements und dann   **Weiter** aus.
1. Wenn Sie den Abonnementbesitz an einen anderen Azure AD-Mandanten übertragen möchten, wählen Sie die Option **Ja, ich möchte auch die Abonnements in den Azure AD-Mandanten des neuen Kontos verschieben** aus.
1. Bestätigen Sie die Übertragung, und klicken Sie auf **Senden**.

## <a name="associate-an-account-to-a-department"></a>Zuordnen eines Kontos zu einer Abteilung

EA-Administratoren können vorhandene Konten Abteilungen in der Registrierung zuordnen.

### <a name="to-associate-an-account-to-a-department"></a>So ordnen Sie ein Konto einer Abteilung zu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü die Option **Konten** aus.
1. Wählen Sie rechts neben einem Konto das Auslassungszeichen ( **...** ) und dann **Bearbeiten** aus.
1. Wählen Sie auf der Seite „Konto bearbeiten“ eine **Abteilung** in der Liste aus.
1. Wählen Sie **Speichern** aus.

## <a name="associate-an-account-with-a-pay-as-you-go-subscription"></a>Zuordnen eines Kontos zu einem Abonnement mit nutzungsbasierter Bezahlung

Wenn Sie bereits über ein Schul-, Geschäfts-, oder Microsoft-Konto für ein Azure-Abonnement mit nutzungsbasierter Bezahlung verfügen, können Sie es Ihrer Enterprise Agreement-Registrierung zuordnen.

Um ein Konto zuordnen zu können, müssen Sie über die Rolle EA-Administrator oder Abteilungsadministrator verfügen.

### <a name="to-associate-an-existing-account"></a>So ordnen Sie ein vorhandenes Konto zu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü die Option **Konten** aus.
1. Klicken Sie auf **+ Hinzufügen**.
1. Geben Sie auf der Seite „Konto hinzufügen“ einen Namen ein, den Sie zum Identifizieren des Kontos für Berichtszwecke verwenden möchten.
1. Wählen Sie das **Microsoft-** oder **Geschäfts-, Schul- oder Unikonto** aus, das dem vorhandenen Azure-Konto zugeordnet ist.
1. Geben Sie die E-Mail-Adresse des Kontobesitzers ein, und bestätigen Sie sie.
1. Wählen Sie **Hinzufügen**.

Es kann bis zu acht Stunden dauern, bis das Konto im Azure-Portal angezeigt wird.

### <a name="to-confirm-account-ownership"></a>So bestätigen Sie den Kontobesitz

1. Melden Sie sich bei dem E-Mail-Konto an, das dem im vorherigen Verfahren angegebenen Geschäfts-, Schul-, Uni- oder Microsoft-Konto zugeordnet ist.
1. Öffnen Sie die E-Mail-Benachrichtigung mit dem Titel _Einladung zur Aktivierung Ihres Kontos im Microsoft Azure-Dienst über die Microsoft-Volumenlizenzierung_.
1. Wählen Sie in der Einladung den Link **Konto aktivieren** aus.
1. Melden Sie sich beim Azure-Portal an.
1. Wählen Sie auf der Seite „Konto aktivieren“ die Option **Ja, ich möchte fortfahren** aus, um den Kontobesitz zu bestätigen.  
    :::image type="content" source="./media/direct-ea-administration/activate-account.png" alt-text="Screenshot: Seite „Konto aktivieren“." lightbox="./media/direct-ea-administration/activate-account.png" :::

## <a name="enable-azure-marketplace-purchases"></a>Aktivieren von Azure Marketplace-Einkäufen

Die meisten _Abonnements_ mit nutzungsbasierter Bezahlung können zwar einem Azure Enterprise Agreement zugeordnet werden, zuvor erworbene Azure Marketplace-_Dienste_ jedoch nicht. Um eine einzelne Ansicht aller Abonnements und Gebühren zu erhalten, empfehlen wir Ihnen, Azure Marketplace-Käufe zu aktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im Menü links **Richtlinien** aus.
1. Legen Sie unter **Azure Marketplace** die Richtlinie auf **Ein** fest.  
   :::image type="content" source="./media/direct-ea-administration/azure-marketplace.png" alt-text="Screenshot der Azure Marketplace-Richtlinieneinstellung." lightbox="./media/direct-ea-administration/azure-marketplace.png" :::

Der Kontobesitzer kann nun Azure Marketplace-Dienste erneut erwerben, die zuvor im Rahmen der Abonnements mit nutzungsbasierter Bezahlung besessen wurden.

Die Einstellung gilt für alle Kontobesitzer in der Registrierung. Sie ermöglicht ihnen, Azure Marketplace-Käufe zu tätigen.

Nachdem die Abonnements unter Ihrer Azure EA-Registrierung aktiviert wurden, kündigen Sie die Azure Marketplace-Dienste, die mit dem Abonnement mit nutzungsbasierter Bezahlung erstellt wurden. Dieser Schritt ist wichtig für den Fall, dass Ihr nutzungsbasiertes Zahlungsmittel abläuft.

## <a name="msdn-subscription-transfer"></a>MSDN-Abonnementübertragung

Wenn Sie ein MSDN-Abonnement an eine EA-Registrierung übertragen, wird es automatisch in ein MSDN Dev/Test-Abonnement konvertiert. Nach der Konvertierung geht für das Abonnement jedes vorhandene Guthaben verloren. Daher sollten Sie ihr gesamtes Guthaben verwenden, bevor Sie es an Ihr Enterprise Agreement übertragen.

## <a name="azure-in-open-subscription-transfer"></a>Azure in Open-Abonnementübertragung

Wenn Sie ein Azure in Open-Abonnement an ein Enterprise Agreement übertragen, verlieren Sie jegliches ungenutztes Azure in Open-Guthaben. Sie sollten Ihr gesamtes Guthaben für das Azure in Open-Abonnement verwenden, bevor Sie es an Ihr Enterprise Agreement übertragen.

## <a name="subscription-transfers-with-support-plans"></a>Abonnementübertragungen mit Supportplänen

Wenn Ihr Enterprise Agreement keinen Supportplan hat und Sie versuchen, ein vorhandenes MOSA-Abonnement (Microsoft Online Support Agreement) zu übertragen, das über einen Supportplan verfügt, wird das Abonnement nicht automatisch übertragen. Sie müssen einen Supportplan für Ihre EA-Registrierung während der Toleranzperiode, die am Ende des folgenden Monats endet, erneut erwerben.

## <a name="manage-department-and-account-spending-with-budgets"></a>Verwalten von Abteilungs- und Kontoausgaben mit Budgets

EA-Kunden können für jede Abteilung und jedes Konto unter einer Registrierung Budgets festlegen oder ändern. Budgets in Cost Management helfen Ihnen, die organisatorische Verantwortlichkeit zu planen und zu steigern. Sie unterstützen Sie dabei, andere über ihre Ausgaben zu informieren, um die Kosten proaktiv zu steuern und die Entwicklung der Ausgaben im Laufe der Zeit zu überwachen. Sie können Warnungen basierend auf den tatsächlichen oder prognostizierten Kosten konfigurieren, um sicherzustellen, dass Ihre Ausgaben innerhalb des Ausgabenlimits Ihrer Organisation liegen. Wenn die erstellte Budgetschwellenwerte überschritten werden, werden nur Benachrichtigungen ausgelöst. Keines Ihrer Ressourcen wird beeinträchtigt, und die Nutzung wird nicht beendet. Sie können Budgets verwenden, um Ausgaben bei der Kostenanalyse zu vergleichen und zu verfolgen. Weitere Informationen zum Erstellen von Budgets finden Sie unter [Tutorial: Erstellen und Verwalten von Azure-Budgets](../costs/tutorial-acm-create-budgets.md).

## <a name="enterprise-agreement-user-roles"></a>Enterprise Agreement-Benutzerrollen

Das Azure-Portal unterstützt Sie bei der Verwaltung Ihrer Azure EA-Kosten und der Nutzung. Es gibt drei wesentliche EA-Rollen:

- Enterprise Agreement-Administrator (EA)
- Abteilungsadministrator
- Kontobesitzer

Jede Rolle verfügt über eine andere Zugriffs- und Autorisierungsebene. Weitere Informationen zu Benutzerrollen finden Sie unter  [Unternehmensbenutzerrollen](understand-ea-roles.md#enterprise-user-roles).

## <a name="add-an-azure-ea-account"></a>Hinzufügen eines Azure EA-Kontos

Ein Azure EA-Konto ist eine Organisationseinheit im Azure-Portal. Im Azure-Portal wird es als _Konto_ bezeichnet. Sie wird zur Abonnementverwaltung und auch für die Berichterstellung verwendet. Für den Zugriff auf Azure-Dienste und ihre Verwendung müssen Sie ein Konto erstellen oder erstellen lassen. Weitere Informationen zu Konten finden Sie unter [Hinzufügen eines Kontos](#add-an-account-and-account-owner).

## <a name="enable-the-enterprise-devtest-offer"></a>Aktivieren des Enterprise Dev/Test-Angebots

Als EA-Administrator können Sie Kontobesitzern in Ihrer Organisation die Erstellung von Abonnements auf Grundlage des EA Dev/Test-Angebots ermöglichen. Wählen Sie hierzu in den Kontoeigenschaften die Option **Dev/Test** aus. Informieren Sie den Kontobesitzer nach Auswahl der Option „Dev/Test“ darüber, dass er nun die EA-Dev/Test-Abonnements erstellen kann, die er für seine Teams von Dev/Test-Abonnenten benötigt. Dank dieses Angebots können aktive Visual Studio-Abonnenten Entwicklungs- und Testworkloads in Azure zu speziellen Dev/Test-Tarifen ausführen. Es besteht Zugriff auf den vollständigen Katalog mit Dev/Test-Images, einschließlich Windows 8.1 und Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>So richten Sie das Enterprise-Dev/Test-Angebot ein

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü die Option **Konten** aus.
1. Wählen Sie das Konto aus, unter dem Sie den Dev/Test-Zugriff aktivieren möchten.
1. Wählen Sie auf der Seite des Registrierungskontos die Option **Bearbeiten** aus.
1. Wählen Sie auf der Seite „Konto bearbeiten“ die Option **Dev/Test** und dann **Speichern** aus.

Wenn ein Benutzer als Kontobesitzer hinzugefügt wird, werden alle dem Benutzer zugeordneten Azure-Abonnements, die entweder auf dem Dev/Test-Angebot mit nutzungsbasierter Zahlung oder auf den Angeboten mit monatlichem Guthaben für Visual Studio-Abonnenten basieren, in das EA-Dev/Test-Angebot konvertiert. Abonnements, die auf anderen Angebotstypen (beispielsweise nutzungsbasierte Zahlung) basieren und dem Kontobesitzer zugeordnet sind, werden in Microsoft Azure Enterprise-Angebote konvertiert.

Das Dev/Test-Angebot gilt nicht für Azure Government-Kunden.

## <a name="create-a-subscription"></a>Erstellen eines Abonnements

Kontobesitzer können Abonnements anzeigen und verwalten. Sie können Abonnements verwenden, um Teams in Ihrer Organisation Zugriff auf Entwicklungsumgebungen und Projekte zu ermöglichen. Zum Beispiel: 

- Test
- Bereitstellung
- Entwicklung
- Staging

Wenn Sie verschiedene Abonnements für jede Anwendungsumgebung erstellen, trägt dies zur Sicherheit der Umgebungen bei.

- Es ist auch möglich, dass Sie jedem Abonnement ein anderes Dienstadministratorkonto zuweisen.
- Sie können Abonnements beliebig viele Dienste zuordnen.
- Der Kontobesitzer erstellt Abonnements und weist jedem Abonnement in seinem Konto ein Dienstadministratorkonto zu.

## <a name="add-a-subscription"></a>Hinzufügen eines Abonnements

Kontobesitzer erstellen Abonnements innerhalb ihres Registrierungskontos. Wenn Sie Ihrem Konto zum ersten Mal ein Abonnement hinzufügen, werden Sie aufgefordert, den Microsoft Online-Abonnementvertrag (Microsoft Online Subscription Agreement, MOSA) und eine Gebührenübersicht zu akzeptieren. Auch wenn sie bei Enterprise Agreement-Kunden keine Anwendung finden, sind MOSA und Gebührenübersicht zum Erstellen des Abonnements erforderlich. Ihre Microsoft Azure Enterprise Agreement-Registrierungszusatzvereinbarung hat Vorrang vor diesen Punkten, und Ihre Vertragsbeziehung ändert sich dadurch nicht. Wenn Sie dazu aufgefordert werden, wählen Sie die Option aus, die angibt, dass Sie die Bedingungen akzeptieren.

_Microsoft Azure Enterprise_ ist der Standardname, der beim Erstellen eines Abonnements verwendet wird. Sie können den Namen ändern, um dieses Abonnement von den anderen Abonnements in Ihrer Registrierung zu unterscheiden und sicherzustellen, dass es in Berichten auf Unternehmensebene identifizierbar ist.

### <a name="to-add-a-subscription"></a>So fügen Sie ein Abonnement hinzu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich für die Registrierung aus.
1. Wählen Sie im linken Menü die Option **Konten** aus.
1. Wählen Sie in der Liste der Registrierungskonten das Konto aus, für das Sie ein Abonnement erstellen möchten.
1. Wählen Sie auf der Seite des Registrierungskontos die Option **+ Neues Abonnement** aus.  
    :::image type="content" source="./media/direct-ea-administration/new-subscription.png" alt-text="Screenshot: Option „Neues Abonnement“." lightbox="./media/direct-ea-administration/new-subscription.png" :::
1. Geben Sie auf der Seite „Abonnement erstellen“ einen Namen für das Abonnement ein, und wählen Sie anschließend **Erstellen** aus.

Der Abonnementname wird in Berichten angezeigt. Dies ist der Name des Projekts, das dem Abonnement im Entwicklungsportal zugeordnet ist.

Es kann bis zu 24 Stunden dauern, bis neue Abonnements in der Liste der Abonnements angezeigt werden. Nachdem Sie ein Abonnement erstellt haben, können Sie folgende Aktionen ausführen:

- Abonnementdetails bearbeiten
- Verwalten von Abonnementdiensten

Sie können Abonnements auch erstellen, indem Sie zur Seite „Azure-Abonnements“ navigieren und **+ Hinzufügen** auswählen.

## <a name="cancel-a-subscription"></a>Kündigen eines Abonnements

Nur Kontobesitzer können ihre eigenen Abonnements kündigen.

Löschen Sie ein Abonnement, für das Sie als Kontobesitzer fungieren, wie folgt:

1. Melden Sie sich mit den Anmeldeinformationen Ihres Kontos beim Azure-Portal an.
1. Wählen Sie im Navigationsmenü **Abonnements** aus.
1. Wählen Sie ein Abonnement aus.
1. Klicken Sie auf der Seite mit den Abonnementdetails in der oberen linken Ecke auf **Abonnement kündigen**.
1. Geben Sie den Abonnementnamen ein, und wählen Sie einen Kündigungsgrund und die Schaltfläche **Kündigen** aus.

Weitere Informationen finden Sie unter  [Was geschieht, nachdem ich mein Abonnement gekündigt habe?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

## <a name="delete-an-enrollment-account"></a>Löschen eines Registrierungskontos

Sie können ein Registrierungskonto nur löschen, wenn keine aktiven Abonnements verfügbar sind.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_GTM/ModernBillingMenuBlade/AllBillingScopes) an.
1. Navigieren Sie zu **Kostenverwaltung und Abrechnung**.
1. Wählen Sie im linken Menü **Abrechnungsbereiche** und dann einen Abrechnungskontobereich aus.
1. Wählen Sie im linken Menü die Option **Konten** aus.
1. Suchen Sie in der Liste „Konten“ nach dem Konto, das Sie löschen möchten.
1. Wählen Sie in der Kontozeile, die Sie löschen möchten, das Auslassungszeichen ( **…** ) und die Option **Löschen** aus.
1. Wählen Sie auf der Seite „Konto löschen“ die Bestätigung **Ja, ich möchte dieses Konto löschen** und dann **Löschen** aus.

## <a name="azure-sponsorship-offer"></a>Azure Sponsorship-Angebot

Das Azure Sponsorship-Angebot ist ein eingeschränktes gesponsertes Microsoft Azure-Konto. Es ist nur per E-Mail-Einladung für bestimmte Kunden verfügbar, die von Microsoft ausgewählt wurden. Wenn Sie zur Nutzung des Microsoft Azure Sponsorship-Angebots berechtigt sind, erhalten Sie eine E-Mail-Einladung für Ihre Konto-ID.

Sollten Sie weitere Informationen benötigen, erstellen Sie eine  [Supportanfrage für die Sponsorship-Aktivierung](https://aka.ms/azrsponsorship).

## <a name="convert-to-work-or-school-account-authentication"></a>Umstellung auf die Authentifizierung per Geschäfts-, Schul- oder Unikonto

Azure-Unternehmensbenutzer können von der Authentifizierung per Microsoft-Konto (MSA oder Live-ID) zur Authentifizierung per Geschäfts-, Schul- oder Unikonto wechseln. Ein Geschäfts-, Schul- oder Unikonto verwendet den Azure Active Directory-Authentifizierungstyp.

### <a name="to-begin"></a>Vorbereitung

1. Fügen Sie dem Azure-Portal das Geschäfts-, Schul- oder Unikonto in den benötigten Rollen hinzu.
1. Sollten Fehler auftreten, ist das Konto möglicherweise nicht in Active Directory gültig. Azure verwendet den Benutzerprinzipalnamen (User Principal Name, UPN), und dieser ist nicht immer mit der E-Mail-Adresse identisch.
1. Authentifizieren Sie sich beim Azure-Portal mit dem Geschäfts-, Schul- oder Unikonto.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>So stellen Sie Abonnements von Microsoft-Konten auf Geschäfts-, Schul- oder Unikonten um

1. Melden Sie sich mit dem Microsoft-Konto, zu dem die Abonnements gehören, beim Azure-Portal an.
1. Verwenden Sie eine Übertragung des Kontobesitzes, um zu dem neuen Konto zu wechseln.
1. Für das Microsoft-Konto sollten keine aktiven Abonnements mehr vorhanden sein, sodass es gelöscht werden kann.
1. Alle gelöschten Konten können aus historischen Abrechnungsgründen im Azure-Portal mit inaktivem Status angezeigt werden. Durch Auswahl von **Nur aktive Konten anzeigen** können Sie sie aus der Ansicht herausfiltern.

## <a name="azure-ea-term-glossary"></a>Glossar für Azure EA-Begriffe

**Konto**<br>
Eine Organisationseinheit. Sie wird zur Abonnementverwaltung und Berichterstellung verwendet.

**Kontobesitzer**<br>
Die Person, die Abonnements und Dienstadministratoren in Azure verwaltet. Kontobesitzer können Nutzungsdaten für dieses Konto und die zugehörigen Abonnements anzeigen.

**Zusatzabonnement**<br>
Ein Jahres- oder Anschlussabonnement unter der Registrierungszusatzvereinbarung.

**Vorauszahlung**<br>
Vorauszahlung in Form eines jährlichen Geldbetrags für Azure-Dienste zu einem rabattierten Vorauszahlungssatz für die Nutzung im Rahmen dieser Vorauszahlung.

**Abteilungsadministrator**<br>
Die Person, die Abteilungen verwalten, neue Konten und Kontobesitzer erstellen, Nutzungsdetails für die von ihr verwalteten Abteilungen einsehen sowie bei entsprechender Berechtigung Kosten anzeigen kann.

**Registrierungsnummer**<br>
Ein eindeutiger, von Microsoft bereitgestellter Bezeichner zur Identifizierung der spezifischen Registrierung, die einem Enterprise Agreement zugeordnet ist.

**Unternehmensadministrator**<br>
Die Person, die Abteilungen, Abteilungsbesitzer, Konten und Kontobesitzer in Azure verwaltet. Diese Person kann Unternehmensadministratoren verwalten und Nutzungsdaten, in Rechnung gestellte Mengen sowie nicht in Rechnung gestellte Gebühren für alle Konten und Abonnements anzeigen, die der Unternehmensregistrierung zugeordnet sind.

**Enterprise Agreement**<br>
Ein Microsoft-Lizenzvertrag für Kunden mit zentralisiertem Einkauf, die ihre gesamte Organisation auf der Grundlage von Microsoft-Technologie standardisieren und eine auf Microsoft-Software basierende IT-Infrastruktur nutzen möchten.

**Enterprise Agreement-Registrierung**<br>
Eine Registrierung im Enterprise Agreement-Programm für die Volumenbereitstellung von Microsoft-Produkten zu vergünstigten Preisen.

**Microsoft-Konto**<br>
Ein webbasierter Dienst, der beteiligten Websites die Authentifizierung eines Benutzers mit einem einzelnen Satz von Anmeldeinformationen ermöglicht.

**Microsoft Azure Enterprise Enrollment Amendment (Registrierungszusatzvereinbarung)**<br>
Eine von einem Unternehmen unterzeichnete Zusatzvereinbarung, durch die das Unternehmen im Rahmen seiner Registrierung Zugang zu Azure erhält.

**Verbrauchte Ressourcenmenge**<br>
Die Menge eines einzelnen Azure-Diensts, die innerhalb eines Monats genutzt wurde.

**Dienstadministrator**<br>
Die Person, die auf Abonnements und Entwicklungsprojekte zugreift und diese verwaltet.

**Abonnement**<br>
Stellt ein Azure-EA-Abonnement dar und ist ein Container mit Azure-Diensten, die von demselben Dienstadministrator verwaltet werden.

**Geschäfts- oder Schulkonto**<br>
Konto für Organisationen, die Azure Active Directory mit Cloudverbund eingerichtet haben und bei denen sich alle Konten in einem einzigen Mandanten befinden

## <a name="enrollment-status"></a>Anmeldungsstatus

**Neu**<br>
Dieser Status wird einer Registrierung zugewiesen, die innerhalb von 24 Stunden erstellt wurde. Er wird innerhalb von 24 Stunden auf den Status „Ausstehend“ aktualisiert.

**Ausstehend**<br>
Der Registrierungsadministrator muss sich beim Azure-Portal anmelden. Nach der Anmeldung wechselt die Registrierung den Status „Aktiv“.

**Aktiv**<br>
Die Registrierung ist aktiv, und Konten und Abonnements können im EA-Portal erstellt werden. Die Registrierung bleibt bis zum Enddatum des Enterprise Agreements aktiv.

**Unbestimmte Laufzeitverlängerung**<br>
Eine unbestimmte Laufzeitverlängerung tritt ein, wenn das Enddatum des Enterprise Agreements überschritten wurde. Mit diesem Status können Azure EA-Kunden, die sich für die Laufzeitverlängerung entschieden haben, Azure-Dienste am Ende ihres Enterprise Agreements unbefristet weiterverwenden.

Bevor die Azure EA-Registrierung das Enddatum des Enterprise Agreements erreicht, muss der Registrierungsadministrator sich für eine der folgenden Optionen entscheiden:

- Verlängern der Registrierung durch Hinzufügen einer zusätzlichen Azure-Vorauszahlung.
- Übertragen auf eine neue Registrierung
- Migrieren zum Microsoft Online Subscription-Programm (MOSP)
- Bestätigen der Deaktivierung aller Dienste, die der Registrierung zugeordnet sind

**Abgelaufen**<br>
Der Azure EA-Kunde wurde aus der Laufzeitverlängerung ausgeschlossen, und die Azure EA-Registrierung hat das Enddatum des Enterprise Agreements erreicht. Die Registrierung läuft ab, und alle zugeordneten Dienste werden deaktiviert.

**Übertragen**<br>
Status für Registrierungen, bei denen alle zugeordneten Konten und Dienste in eine neue Registrierung übertragen wurden.
 > [!NOTE]
 > Registrierungen werden nicht automatisch übertragen, wenn während der Verlängerung eine neue Registrierungsnummer generiert wird. Sie müssen die vorherige Registrierungsnummer in Ihren Verlängerungsdokumenten angeben, um eine automatische Übertragung zu ermöglichen.

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie eine Azure-Supportanfrage für Ihre EA-Registrierung erstellen müssen, finden Sie weitere Informationen unter [Erstellen einer Azure-Supportanfrage für ein Enterprise Agreement-Problem](how-to-create-azure-support-request-ea.md).
- In den [häufig gestellten Fragen zu Cost Management + Billing](../cost-management-billing-faq.yml) finden Sie Fragen zum Besitz von EA-Abonnements.