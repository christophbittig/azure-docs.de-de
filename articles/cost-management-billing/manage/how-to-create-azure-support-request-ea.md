---
title: Erstellen einer Azure-Supportanfrage für ein Enterprise Agreement-Problem
description: Enterprise Agreement-Kunden, die Unterstützung benötigen, können im Azure-Portal Self-Service-Lösungen finden sowie Supportanfragen erstellen und verwalten.
ms.topic: troubleshooting
ms.date: 10/07/2021
ms.author: banders
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: billing
ms.openlocfilehash: 7826b2c19a77ef3762f39ade9a80f26615f704bd
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129660189"
---
# <a name="create-an-azure-support-request-for-an-enterprise-agreement-issue"></a>Erstellen einer Azure-Supportanfrage für ein Enterprise Agreement-Problem

Azure ermöglicht Ihnen das Erstellen und Verwalten von Supportanfragen (auch Supporttickets genannt) für Enterprise Agreements. In diesem Artikel werden die Erstellung und Verwaltung von Anfragen über das [Azure-Portal](https://portal.azure.com) behandelt. Anfragen können aber auch programmgesteuert mithilfe der [Azure-Supportticket-REST-API](/rest/api/support) oder der [Azure CLI](/cli/azure/azure-cli-support-request) erstellt und verwaltet werden.

> [!NOTE]
> Die Azure-Portal-URL ist für die Azure-Cloud spezifisch, in der Ihre Organisation bereitgestellt ist.
>
>- Azure-Portal zur kommerziellen Verwendung: [https://portal.azure.com](https://portal.azure.com)
>- Azure-Portal für Deutschland: [https://portal.microsoftazure.de](https://portal.microsoftazure.de).
>- Azure-Portal für die US-Regierung: [https://portal.azure.us](https://portal.azure.us).

In Azure wird unbegrenzter Support für die Abonnementverwaltung bereitgestellt, einschließlich Abrechnung, Kontingentanpassungen und Kontenübertragungen. Sie benötigen einen Supportplan für technischen Support. Weitere Informationen finden Sie unter [Supportpläne vergleichen](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Erste Schritte

Zu **Hilfe und Support** gelangen Sie im Azure-Portal. Die Option steht über das Menü im Azure-Portal, die globale Kopfzeile oder das Ressourcenmenü für einen Dienst zur Verfügung. Bevor Sie eine Supportanfrage einreichen können, müssen Sie über die entsprechenden Berechtigungen verfügen.

### <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Um eine Supportanfrage für ein Enterprise Agreement zu erstellen, müssen Sie ein Unternehmensadministrator oder Partneradministrator sein, der einer Unternehmensregistrierung zugeordnet ist. 

### <a name="go-to-help--support-from-the-global-header"></a>Über die globale Kopfzeile zu „Hilfe + Support“ wechseln

So starten Sie von einer beliebigen Stelle im Azure-Portal aus eine Supportanfrage

1. Wählen Sie das Fragezeichensymbol in der globalen Kopfzeile und dann **Hilfe und Support** aus.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/help-support-new-lower.png" alt-text="Screenshot des Hilfemenüs im Azure-Portal":::

1. Klicken Sie auf **Supportanfrage erstellen**. Befolgen Sie die Anweisungen, Informationen zu Ihrem Problem zur Verfügung zu stellen. Wir schlagen einige mögliche Lösungen vor, sammeln Details zum Problem und helfen Ihnen, die Supportanfrage zu übermitteln und zu verfolgen.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/new-support-request-2-lower.png" alt-text="Screenshot der Seite „Hilfe und Support“ mit dem Link „Supportanfrage erstellen“":::

### <a name="go-to-help--support-from-a-resource-menu"></a>Aus einem Ressourcenmenü zu „Hilfe + Support“ wechseln

So starten Sie eine Supportanfrage

1. Wählen Sie im Ressourcenmenü im Abschnitt **Support + Problembehandlung** die Option **Neue Supportanfrage** aus.

   :::image type="content" source="media/how-to-create-azure-support-request-ea/in-context-2-lower.png" alt-text="Screenshot der Option „Neue Supportanfrage“ im Ressourcenbereich":::

1. Befolgen Sie die Anweisungen, um uns Informationen zu Ihrem Problem zu geben. Wenn Sie den Supportanfrageprozess von einer Ressource aus starten, sind einige Optionen vorab für Sie ausgewählt.

## <a name="create-a-support-request"></a>Erstellen einer Supportanfrage

Wir führen Sie durch einige Schritte, um Informationen zu Ihrem Problem zu sammeln und Ihnen bei dessen Behebung zu helfen. Jeder dieser Schritte wird in den folgenden Abschnitten beschrieben.

### <a name="problem-description"></a>Problembeschreibung

1. Geben Sie eine Zusammenfassung Ihres Problems ein, und wählen Sie dann **Problemtyp** aus. 
1. Wählen Sie in der Liste **Problemtyp** die Option **Registrierungsverwaltung** für Probleme im Zusammenhang mit dem EA-Portal aus.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" alt-text="Screenshot: Registrierungsverwaltung auswählen" lightbox="./media/how-to-create-azure-support-request-ea/select-issue-type-enrollment-administration.png" :::
1. Wählen Sie unter **Registrierungsnummer** die Registrierungsnummer aus. 
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-enrollment.png" alt-text="Screenshot: Registrierungsnummer auswählen" :::
1. Wählen Sie unter **Problemtyp** die Problemkategorie aus, die den Problemtyp am besten beschreibt.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/select-problem-type.png" alt-text="Screenshot: Problemtyp auswählen" :::
1. Wählen Sie unter **Problemuntertyp** eine Unterkategorie für das Problem aus. 

Nachdem Sie all diese Details angegeben haben, klicken Sie auf **Weiter: Lösungen**.

### <a name="recommended-solution"></a>Empfohlene Lösung

Basierend auf den von Ihnen bereitgestellten Informationen zeigen wir Ihnen empfohlene Lösungen, die Sie zum Beheben des Problems verwenden können. In einigen Fällen können wir sogar eventuell eine schnelle Diagnose ausführen. Lösungen werden von Azure-Experten geschrieben und unterstützen Sie beim Beheben der am häufigsten auftretenden Probleme.

Wenn Sie das Problem noch immer nicht beheben können, fahren Sie mit dem Erstellen Ihrer Supportanfrage fort, indem Sie auf **Weiter: Details** klicken.

### <a name="additional-details"></a>Zusätzliche Details

Als Nächstes sammeln wir weitere Details über das Problem. Wenn Sie in diesem Schritt ausführliche und detaillierte Informationen bereitstellen, können wir Ihre Supportanfrage besser an den richtigen Techniker weiterleiten.

1. Füllen Sie auf der Registerkarte „Details“ den Abschnitt **Problemdetails** aus, um uns weitere Informationen zu Ihrem Problem bereitzustellen. Teilen Sie uns wenn möglich mit, wann das Problem begonnen hat und durch welche Schritte es hervorgerufen wird. Sie können eine Datei hochladen, z. B. eine Protokolldatei oder eine Ausgabe der Diagnose. Weitere Informationen zu Dateiuploads finden Sie unter [Richtlinien für den Dateiupload](../../azure-portal/supportability/how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Wählen Sie im Abschnitt **Diagnoseinformationen freigeben** die Option **Ja** oder **Nein** aus. Wenn Sie **Ja** auswählen, kann der Azure-Support [Diagnoseinformationen](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) aus Ihren Azure-Ressourcen erfassen. Wenn Sie diese Informationen nicht freigeben möchten, wählen Sie **Nein** aus. In einigen Fällen stehen weitere Optionen zur Auswahl.

1. Wählen Sie im Abschnitt **Supportmethode** den Schweregrad des Problems aus. Der maximale Schweregrad richtet sich nach Ihrem [Supportplan](https://azure.microsoft.com/support/plans).

1. Geben Sie Ihre bevorzugte Kontaktmethode, Ihre Verfügbarkeit und Ihre bevorzugte Supportsprache an.

1. Füllen Sie als Nächstes den Abschnitt **Kontaktinformationen** aus, damit wir wissen, wie wir Sie kontaktieren können.  
    :::image type="content" source="./media/how-to-create-azure-support-request-ea/details-tab.png" alt-text="Screenshot: Registerkarte „Details“" lightbox="./media/how-to-create-azure-support-request-ea/details-tab.png" :::

Klicken Sie auf **Weiter: Überprüfen und erstellen**, wenn Sie alle erforderlichen Informationen angegeben haben.

### <a name="review--create"></a>Überprüfen + erstellen

Bevor Sie Ihre Anfrage erstellen, überprüfen Sie alle Details, die Sie an den Support senden. Sie können auf **Zurück** klicken, um zu einer beliebigen Registerkarte zurückzukehren und Änderungen vorzunehmen. Wenn Sie damit zufrieden sind und die Supportanfrage vollständig ist, wählen Sie **Erstellen** aus.

Ein Supporttechniker setzt sich mit Ihnen mithilfe der von Ihnen angeführten Methode in Verbindung. Informationen zur anfänglichen Reaktionszeit finden Sie unter [Supportumfang und Reaktionszeiten](https://azure.microsoft.com/support/plans/response/).

## <a name="cant-create-request-with-microsoft-account"></a>Erstellen der Anfrage mit Microsoft-Konto nicht möglich

Wenn Sie über ein Microsoft-Konto (MSA) verfügen, können Sie kein Azure-Supportticket erstellen. Microsoft-Konten werden für Dienste wie Outlook, Windows Live und Hotmail erstellt.

Zum Erstellen eines Azure-Supporttickets muss ein *Organisationskonto* über die EA-Administratorrolle oder die Partneradministratorrolle verfügen.

Wenn Sie über ein MSA verfügen, lassen Sie einen Administrator ein Organisationskonto für Sie erstellen. Ein Unternehmensadministrator oder Partneradministrator muss dann Ihr Organisationskonto als Unternehmensadministrator oder Partneradministrator hinzufügen. Anschließend können Sie Ihr Organisationskonto verwenden, um eine Supportanfrage zu stellen.

- Informationen zum Hinzufügen eines Unternehmensadministrators finden Sie unter [Erstellen eines weiteren Unternehmensadministrators](ea-portal-administration.md#create-another-enterprise-administrator).
- Informationen zum Hinzufügen eines Partneradministrators finden Sie unter [Verwalten von Partneradministratoren](ea-partner-portal-administration.md#manage-partner-administrators).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter folgenden Links:

* [Verwalten einer Azure-Supportanfrage]()../../azure-portal/supportability/how-to-manage-azure-support-request.md)
* [Azure-Supportticket-REST-API](/rest/api/support)
* Kontaktaufnahme mit uns auf [Twitter](https://twitter.com/azuresupport)
* Hilfe von Kollegen über die [Microsoft Q&A-Frageseite](/answers/products/azure)
* Weitere Informationen in den [Häufig gestellten Fragen zum Azure-Support](https://azure.microsoft.com/support/faq)