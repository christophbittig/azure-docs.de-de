---
title: Erstellen von Unternehmens-Abonnements von Azure-Dev/Test
description: Erstellen Sie Unternehmens- und Organisationsabonnements von Azure Dev/Test für Teams und große Organisationen.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: 5897e60a2c3c5d46dac48973d78072364af27336
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095470"
---
# <a name="creating-enterprise-and-organization-azure-devtest-subscriptions"></a>Erstellen von Unternehmens- und Organisationsabonnements von Azure Dev/Test

Unternehmens-Abonnements von Dev/Test sind für die Teamentwicklung in großen Organisationen verfügbar.

- Für die Teamentwicklung in großen Unternehmen  
- Abrechnung für das gesamte Unternehmen – keine separaten Zahlungen  
- Erstellen eines Unternehmens-Angebotsabonnements von Dev/Test über das Azure-Unternehmens-Portal  

## <a name="prerequisites"></a>Voraussetzungen

Um ein Azure-Unternehmens-Abonnement von Dev/Test abzuschließen, müssen Sie über eine Rolle "Kontoinhaber" für ein Registrierungskonto verfügen, um ein Abonnement abschließen zu können. Es gibt zwei Möglichkeiten, auf diese Rolle zuzugreifen:  

- Der Unternehmensadministrator Ihrer Registrierung kann [Sie zum Kontoinhaber machen](../../cost-management-billing/manage/grant-access-to-create-subscription.md).  
- Ein vorhandener Besitzer des Registrierungskontos kann [Ihnen Zugriff gewähren](../../cost-management-billing/manage/grant-access-to-create-subscription.md).  

### <a name="important-information-before-you-add-account-owners"></a>Wichtige Informationen, bevor Sie Kontoinhaber hinzufügen

Ein EA-Kontoinhaber **kann nicht** das gleiche Anmeldekonto für das EA-Portal oder andere Azure-Angebote verwenden. Angenommen, Sie wurden dem EA-Portal als Kontoinhaber hinzugefügt und haben die gleichen Anmeldeinformationen verwendet, die Sie für Ihre individuellen Visual Studio Benefits verwendet haben. In diesem Fall wird dieses Visual-Studio-Abonnement in das EA Dev/Test-Angebot konvertiert.  

> [!Note]  
> Wenn Sie sich zum ersten Mal beim EA-Portal als Kontobesitzer anmelden, wird ein Popupfenster mit einer Warnung angezeigt. Es ist wichtig, dies zu lesen und zu verstehen. Ihre vorhandenen Abonnements werden in ein abrechenbares EA-Angebot konvertiert. Wenn Sie ein Visual-Studio-Abonnent sind, der als Kontoinhaber hinzugefügt wurde, verlieren Sie Ihr individuelles monatliches Azure-Guthaben, bis Sie weitere Maßnahmen ergreifen.

### <a name="to-recover-your-individual-visual-studio-azure-benefits"></a>So stellen Sie Ihre individuellen Visual-Studio-Azure-Vorteile wieder her.  

Nachdem Sie Ihre Rolle als EA-Kontoinhaber authentifiziert haben, haben Sie eine der beiden Möglichkeiten:  

- Entfernen oder Verschieben von Azure-Abonnements, deren Eigentümer Sie sind  
- Löschen Sie Ihre Rolle als Kontoinhaber im EA-Portal  
- Registrieren Sie sich anschließend erneut für individuelle Visual-Studio-Azure-Vorteile.  
- Oder löschen Sie den Abonnenten von der Visual-Studio-Administrator-Webseite im VLSC und weisen Sie das Abonnement mit einer anderen Anmeldung neu zu. Anschließend können Sie sich für individuelle Visual-Studio-Azure-Vorteile erneut registrieren.  

## <a name="create-your-subscription"></a>Aktivieren Ihres Abonnements

Um Ihre Unternehmens-Abonnements von Dev/Test zu erstellen, wählen Sie zunächst ein Registrierungskonto als Dev/Test-Konto aus.  

1. Um zu beginnen, wechseln Sie zu [Ea.azure.com](https://ea.azure.com). Im Azure-Unternehmens-Portal können Sie die Registrierungen Ihres Unternehmens verwalten.  
2. Wählen Sie auf der Registerkarte Verwalten Ihre Vertragsnummer aus.  

    > [!Note]
    > Die Registrierung kann sich basierend auf Ihrer Verlängerungsvereinbarung in Ihrem Unternehmensvertrag ändern.
    
    ![Screenshot der Verwaltungsportalseite des Unternehmensvertrags.](media/quickstart-create-enterprise-devtest-subscriptions/ea-manage-portal.png "Verwalten Sie die Registrierungen Ihres Unternehmens über das Azure Unternehmer-Portal.")
    
3. Nachdem Sie die entsprechende Registrierungsnummer ausgewählt haben, wählen Sie die Registerkarte "Konto" aus.  
4. Hier können Sie das Konto bzw. die Konten bearbeiten und das Entwicklungs-/Testflag auf "Ja" umschalten.  

So fügen Sie ein Konto hinzu:  

1. Wählen Sie im Azure Enterprise Portal im Navigationsbereich auf der linken Seite die Option Verwalten aus.  
2. Wählen Sie auf der Seite Konto die Registerkarte Konto und dann +Konto hinzufügen aus.  
3. Sie können einen Bereich auswählen oder leer lassen.
4. Wählen Sie den gewünschten Authentifizierungstyp aus.  
5. Geben Sie einen Anzeigenamen ein, damit Sie das Konto in Berichten identifizieren können.  
6. Geben Sie die E-Mail-Adresse des Kontoinhabers ein, die dem neuen Konto zugeordnet werden soll.  
7. Bestätigen Sie die E-Mail-Adresse, und klicken Sie auf Hinzufügen.  

![Screenshot der Verwaltungsseite des Azure Unternehmens-Portals mit ausgewählter Option "+Konto hinzufügen".](media/quickstart-create-enterprise-devtest-subscriptions/add-account.png "Klicken Sie anschließend auf Konto hinzufügen.")

## <a name="add-your-azure-enterprise-devtest-subscription"></a>Hinzufügen Ihres Azure Unternehmens-Abonnements Dev/Test

Nachdem Sie das Konto zum Erstellen eines Azure Unternehmens-Abonnements Dev/Test ausgewählt haben, fügen Sie das entsprechende Abonnementangebot hinzu. Es gibt zwei Methoden: Unabhängig davon, was Sie auswählen, ist die Benutzeroberfläche identisch.  

1. Die Registerkarte "Abonnement" in jedem Portal ist die Schaltfläche "+/Hinzufügen". 
    1. Sie gelangen zu einer Anmeldeseite.
    1. Melden Sie sich mit Ihren Microsoft-Azure-Zugangsdaten an.
    1. Nach der Anmeldung werden Sie zu einer Seite weitergeleitet, auf der die verfügbaren Angebote aufgeführt sind. Schalten Sie auf **ja** im EA-Portal, um Angebote für Unternehmen einzusehen.
    
1. Wählen Sie das Angebot Dev/Test für Unternehmer aus und schließen Sie Ihr Abonnement ab.
    1. Sie müssen über Zugriff und Berechtigungen verfügen, die Ihrer Identität zugeordnet sind.
    1. Sie müssen das Konto im Registrierungsportal als Dev/Test-Konto festlegen.

### <a name="next-steps"></a>Nächste Schritte  

- [Azure EA-Portalverwaltung](../../cost-management-billing/manage/ea-portal-administration.md)
- [Erste Schritte mit dem Azure Enterprise Portal](../../cost-management-billing/manage/ea-portal-get-started.md)
