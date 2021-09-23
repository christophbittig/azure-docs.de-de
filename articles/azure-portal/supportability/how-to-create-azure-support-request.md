---
title: 'Gewusst wie: Erstellen einer Azure-Supportanfrage'
description: Kunden, die Unterstützung benötigen, können das Azure-Portal verwenden, um Self-Service-Lösungen zu finden und Supportanfragen zu erstellen und zu verwalten.
ms.topic: how-to
ms.custom: support-help-page
ms.date: 09/01/2021
ms.openlocfilehash: 768c778926024288b0d331b5cb6b60c8e9c16a57
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429459"
---
# <a name="create-an-azure-support-request"></a>Erstellen einer Azure-Supportanfrage

Azure ermöglicht das Erstellen und Verwalten von Supportanfragen (auch Supporttickets genannt). In diesem Artikel werden die Erstellung und Verwaltung von Anfragen über das [Azure-Portal](https://portal.azure.com) behandelt. Anfragen können aber auch programmgesteuert mithilfe der [Azure-Supportticket-REST-API](/rest/api/support) oder der [Azure CLI](/cli/azure/azure-cli-support-request) erstellt und verwaltet werden.

> [!NOTE]
> Die Azure-Portal-URL ist für die Azure-Cloud spezifisch, in der Ihre Organisation bereitgestellt ist.
>
>* Azure-Portal zur kommerziellen Verwendung: [https://portal.azure.com](https://portal.azure.com)
>* Azure-Portal für Deutschland: [https://portal.microsoftazure.de](https://portal.microsoftazure.de).
>* Azure-Portal für die US-Regierung: [https://portal.azure.us](https://portal.azure.us).

In Azure wird unbegrenzter Support für die Abonnementverwaltung bereitgestellt, einschließlich Abrechnung, Kontingentanpassungen und Kontenübertragungen. Für technischen Support benötigen Sie einen Supportplan. Weitere Informationen finden Sie unter [Supportpläne vergleichen](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Erste Schritte

Zu **Hilfe und Support** gelangen Sie im Azure-Portal. Die Option steht über das Menü im Azure-Portal, die globale Kopfzeile oder das Ressourcenmenü für einen Dienst zur Verfügung. Bevor Sie eine Supportanfrage einreichen können, müssen Sie über die entsprechenden Berechtigungen verfügen.

### <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure

Um eine Supportanfrage erstellen zu können, müssen Sie ein [Besitzer](../../role-based-access-control/built-in-roles.md#owner) oder [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) sein, oder Ihnen muss die Rolle [Mitwirkender für Supportanfragen](../../role-based-access-control/built-in-roles.md#support-request-contributor) auf der Abonnementebene zugewiesen sein. Zum Erstellen einer Supportanfrage ohne Abonnement, z. B. in einem Azure Active Directory-Szenario, müssen Sie ein [Administrator](../../active-directory/roles/permissions-reference.md) sein.

### <a name="go-to-help--support-from-the-global-header"></a>Über die globale Kopfzeile zu „Hilfe + Support“ wechseln

So starten Sie von einer beliebigen Stelle im Azure-Portal aus eine Supportanfrage

1. Wählen Sie das **?** im globalen Header aus, und klicken Sie dann auf **Hilfe und Support**.

   :::image type="content" source="media/how-to-create-azure-support-request/helpandsupportnewlower.png" alt-text="Screenshot des Hilfemenüs im Azure-Portal":::

1. Klicken Sie auf **Supportanfrage erstellen**. Befolgen Sie die Anweisungen, Informationen zu Ihrem Problem zur Verfügung zu stellen. Wir schlagen einige mögliche Lösungen vor, sammeln Details zum Problem und helfen Ihnen, die Supportanfrage zu übermitteln und zu verfolgen.

   :::image type="content" source="media/how-to-create-azure-support-request/newsupportrequest2lower.png" alt-text="Screenshot der Seite „Hilfe und Support“ mit dem Link „Supportanfrage erstellen“":::

### <a name="go-to-help--support-from-a-resource-menu"></a>Aus einem Ressourcenmenü zu „Hilfe + Support“ wechseln

So starten Sie eine Supportanfrage im Kontext der Ressource, mit der Sie gerade arbeiten:

1. Wählen Sie im Ressourcenmenü im Abschnitt **Support + Problembehandlung** die Option **Neue Supportanfrage** aus.

   :::image type="content" source="media/how-to-create-azure-support-request/incontext2lower.png" alt-text="Screenshot der Option „Neue Supportanfrage“ im Ressourcenbereich":::

1. Befolgen Sie die Anweisungen, um uns Informationen zu Ihrem Problem zu geben. Wenn Sie den Supportanfrageprozess von einer Ressource aus starten, sind einige Optionen vorab für Sie ausgewählt.

## <a name="create-a-support-request"></a>Erstellen einer Supportanfrage

Wir führen Sie durch einige Schritte, um Informationen zu Ihrem Problem zu sammeln und Ihnen bei dessen Behebung zu helfen. Jeder dieser Schritte wird in den folgenden Abschnitten beschrieben.

### <a name="problem-description"></a>Problembeschreibung

Der erste Schritt des Supportanfrageprozesses besteht darin, einen Problemtyp auszuwählen. Sie werden dann zur Angabe weiterer Informationen aufgefordert, die je nach ausgewählter Art des Problems variieren können. In den meisten Fällen müssen Sie ein Abonnement angeben, Ihr Problem kurz beschreiben und einen Problemtyp auswählen. Wenn Sie **Technisch** auswählen, müssen Sie den Dienst angeben, auf den sich Ihr Problem bezieht. Je nach Dienst werden zusätzliche Optionen für **Problemtyp** und **Problemuntertyp** angezeigt.

:::image type="content" source="media/how-to-create-azure-support-request/basics2lower.png" alt-text="Screenshot des Schritts „Problembeschreibung“ des Supportanfrageprozesses":::

Nachdem Sie all diese Details angegeben haben, klicken Sie auf **Weiter**.

### <a name="recommended-solution"></a>Empfohlene Lösung

Basierend auf den von Ihnen bereitgestellten Informationen zeigen wir Ihnen empfohlene Lösungen an, die Sie zum Beheben des Problems verwenden können. In einigen Fällen können wir sogar eventuell eine schnelle Diagnose ausführen. Lösungen werden von Azure-Experten geschrieben und unterstützen Sie beim Beheben der am häufigsten auftretenden Probleme.

Wenn Sie das Problem noch immer nicht beheben können, fahren Sie mit dem Erstellen Ihrer Supportanfrage fort, indem Sie auf **Weiter** klicken.

### <a name="additional-details"></a>Zusätzliche Details

Als Nächstes sammeln wir zusätzliche Details über das Problem. Wenn Sie in diesem Schritt ausführliche und detaillierte Informationen bereitstellen, können wir Ihre Supportanfrage besser an den richtigen Techniker weiterleiten.

1. Füllen Sie die **Problemdetails** aus, um uns weitere Informationen zu Ihrem Problem bereitzustellen. Teilen Sie uns wenn möglich mit, wann das Problem begonnen hat und durch welche Schritte es hervorgerufen wird. Sie können eine Datei hochladen, z. B. eine Protokolldatei oder eine Ausgabe der Diagnose. Weitere Informationen zu Dateiuploads finden Sie unter [Richtlinien für den Dateiupload](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. Wählen Sie im Abschnitt **Diagnoseinformationen freigeben** die Option **Ja** oder **Nein** aus. Wenn Sie **Ja** auswählen, kann der Azure-Support [Diagnoseinformationen](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) aus Ihren Azure-Ressourcen erfassen. Wenn Sie diese Informationen nicht freigeben möchten, wählen Sie **Nein** aus. In einigen Fällen stehen zusätzliche Optionen zur Auswahl, z. B. ob der Zugriff auf den Arbeitsspeicher eines virtuellen Computers zugelassen werden soll.

1. Wählen Sie im Abschnitt **Supportmethode** den Schweregrad der Auswirkungen aus. Der maximale Schweregrad richtet sich nach Ihrem [Supportplan](https://azure.microsoft.com/support/plans).

1. Geben Sie Ihre bevorzugte Kontaktmethode, Ihre Verfügbarkeit und Ihre bevorzugte Supportsprache an.

1. Füllen Sie als Nächstes den Abschnitt **Kontaktinformationen** aus, damit wir wissen, wie wir Sie kontaktieren können.

Klicken Sie auf **Weiter**, wenn Sie alle erforderlichen Informationen angegeben haben.

### <a name="review--create"></a>Überprüfen + erstellen

Bevor Sie Ihre Anfrage erstellen, überprüfen Sie alle Details, die Sie an den Support senden. Sie können auf **Zurück** klicken, um zu einer beliebigen Registerkarte zurückzukehren und Änderungen vorzunehmen. Wenn Sie damit zufrieden sind und die Supportanfrage vollständig ist, wählen Sie **Erstellen** aus.

Ein Supporttechniker setzt sich mit Ihnen mithilfe der von Ihnen angeführten Methode in Verbindung. Informationen zur anfänglichen Reaktionszeit finden Sie unter [Supportumfang und Reaktionszeiten](https://azure.microsoft.com/support/plans/response/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Optionen für die Selbsthilfeunterstützung in Azure finden Sie in diesem Video:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Weitere Informationen finden Sie unter folgenden Links:

* [Verwalten einer Azure-Supportanfrage](how-to-manage-azure-support-request.md)
* [Azure-Supportticket-REST-API](/rest/api/support)
* Kontaktaufnahme mit uns auf [Twitter](https://twitter.com/azuresupport)
* Hilfe von Kollegen über die [Microsoft Q&A-Frageseite](/answers/products/azure)
* Weitere Informationen in den [Häufig gestellten Fragen zum Azure-Support](https://azure.microsoft.com/support/faq)