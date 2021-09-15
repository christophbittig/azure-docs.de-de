---
title: Validieren von XML für die B2B-Unternehmensintegration
description: Überprüfen von XML mithilfe von Schemas in Azure Logic Apps mit Enterprise Integration Packs.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 87650a1ab950f8e88fe08a1c4555c98652776730
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099284"
---
# <a name="validate-xml-for-workflows-in-azure-logic-apps"></a>XML-Überprüfung für Workflows in Azure Logic Apps

In B2B-Szenarien für die Unternehmensintegration müssen die beteiligten Parteien in der Regel zunächst sicherstellen, dass die ausgetauschten Nachrichten gültig sind, bevor eine Datenverarbeitung gestartet werden kann. Dokumente können anhand eines vordefinierten Schemas mithilfe der Aktion **XML-Überprüfung** in Azure Logic Apps überprüft werden.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Der Logik-App-Workflow (leer oder bereits vorhanden), in dem Sie die Aktion **XML-Überprüfung** verwenden möchten.

  Wenn Sie über einen leeren Workflow verfügen, verwenden Sie einen Trigger nach Wahl. In diesem Beispiel wird der Anforderungstrigger verwendet.

  Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen in der folgenden Dokumentation:

  * [Was ist Azure Logic Apps?](logic-apps-overview.md)

  * [Schnellstart: Erstellen Ihres ersten Logik-App-Workflows](quickstart-create-first-logic-app-workflow.md)

  * [Erstellen eines Logik-App-Workflows mit nur einem Mandanten](create-single-tenant-workflows-azure-portal.md)

  * [Modelle für Verbrauchsmessung, Abrechnung und Preise bei Azure Logic Apps](logic-apps-pricing.md)

* Wenn Sie den Ressourcentyp **Logik-App (Verbrauch)** verwenden, benötigen Sie ein [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) das die folgenden Anforderungen erfüllt:

  * Das Konto ist demselben Azure-Abonnement zugeordnet wie Ihre Logik-App-Ressource.

  * Das Konto befindet sich am gleichen Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource, in der Sie die Aktion **XML-Überprüfung** verwenden möchten.

  * Das Konto ist mit der Logik-App-Ressource [verknüpft](logic-apps-enterprise-integration-create-integration-account.md#link-account).

  * Es umfasst das [Schema](logic-apps-enterprise-integration-schemas.md), das zum Validieren des XML-Inhalts verwendet werden soll.

  Wenn Sie den Ressourcentyp **Logik-App (Standard)** verwenden, müssen Sie kein Integrationskonto verknüpfen. Sie müssen aber dennoch das [Schema](logic-apps-enterprise-integration-schemas.md) hinzufügen, das zum Überprüfen von XML-Inhalten für Ihre Logik-App-Ressource verwendet werden soll. Sie können diese Aufgabe im Menü Ihrer Logik-App-Ressource im Abschnitt **Schemas** unter **Einstellungen** ausführen.

## <a name="add-xml-validation-action"></a>Hinzufügen der XML-Überprüfungsaktion

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihre Logik-App und den Workflow in der Designeransicht.

1. Wenn Sie über eine leere Logik-App ohne Trigger verfügen, fügen Sie einen Trigger nach Wahl hinzu. In diesem Beispiel wird der Anforderungstrigger verwendet. Fahren Sie andernfalls mit dem nächsten Schritt fort.

   Um den Anforderungstrigger hinzuzufügen, geben Sie im Suchfeld des Designers `HTTP request` ein, und wählen Sie den Anforderungstrigger mit dem Namen **Beim Empfang einer HTTP-Anforderung** aus.

1. Wählen Sie unter dem Schritt in Ihrem Workflow, in dem Sie die Aktion **XML-Überprüfung** einfügen möchten, einen der folgenden Schritte aus:

   Wählen Sie für eine verbrauchsbasierte App oder eine auf dem ISE-Plan basierende Logik-App einen Schritt aus:

   * Wenn Sie die Aktion **XML-Überprüfung** am Ende des Workflows hinzufügen möchten, wählen Sie **Neuer Schritt** aus.

   * Wenn Sie die Aktion **XML-Überprüfung** zwischen vorhandenen Schritten einfügen möchten, zeigen Sie auf den Pfeil, der die beiden Schritte verbindet. Daraufhin wird ein Pluszeichen ( **+** ) angezeigt. Wählen Sie dieses Pluszeichen und anschließend die Option **Aktion hinzufügen** aus.

   Wählen Sie für eine auf dem Standard-Plan basierende Logik-App einen Schritt aus:

   * Um die Aktion **XML-Überprüfung** am Ende Ihres Workflows einzufügen, wählen Sie das Pluszeichen ( **+** ) und dann **Aktion hinzufügen** aus.

   * Um die Aktion **XML-Überprüfung** zwischen vorhandenen Schritten einzufügen, wählen Sie das Pluszeichen ( **+** ) zwischen diesen Schritten und dann **Aktion hinzufügen** aus.

1. Wählen Sie unter **Vorgang auswählen** die Option **Integriert** aus. Geben Sie im Suchfeld `xml validation`ein. Wählen Sie in der Liste mit den Aktionen die Aktion **XML-Überprüfung** aus.

1. Klicken Sie zum Angeben des zu überprüfenden XML-Inhalts in das Feld **Inhalt**, um die dynamische Inhaltsliste anzuzeigen.

   Die Liste mit den dynamischen Inhalten enthält Eigenschaftstoken, die die Ausgaben der vorherigen Schritte im Workflow darstellen. Falls eine erwartete Eigenschaft nicht in der Liste enthalten ist, überprüfen Sie in der Trigger- oder Aktionsüberschrift der Liste, ob Sie **Mehr anzeigen** auswählen können.

   Für eine verbrauchsbasierte App oder eine auf dem ISE-Plan basierende Logik-App sieht der Designer wie im folgenden Beispiel aus:

   ![Screenshot: Designer mit mehreren Mandanten, mit Cursor im Feld „Inhalt“ und geöffneter dynamischer Inhaltsliste](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-multi-tenant.png)

   Für eine auf dem Standard-Plan basierende Logik-App sieht der Designer wie im folgenden Beispiel aus:

   ![Screenshot: Designer mit einem Mandanten, mit Cursor im Feld „Inhalt“ und geöffneter dynamischer Inhaltsliste](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list-single-tenant.png)

1. Wählen Sie in der dynamischen Inhaltsliste das Eigenschaftentoken für den zu überprüfenden Inhalt aus.

   In diesem Beispiel wird das Token **Text** für den Trigger ausgewählt.

1. Um das Schema anzugeben, das für die Validierung verwendet werden soll, öffnen Sie die Liste **Schemaname** und wählen das Schema aus, das Sie zuvor hinzugefügt haben.

1. Speichern Sie Ihren Logik-App-Workflow, wenn Sie fertig sind.

   Sie haben die Einrichtung Ihrer Aktion **XML-Überprüfung** abgeschlossen. In einer echten App empfiehlt es sich ggf., die überprüften Daten in einer branchenspezifischen App wie Salesforce zu speichern. Um die überprüfte Ausgabe an Salesforce zu senden, fügen Sie eine Salesforce-Aktion hinzu.

1. Um Ihre Überprüfungsaktion zu testen, lösen Sie die Ausführung Ihres Workflows aus. Senden Sie beispielsweise für den Anforderungstrigger eine Anforderung an die Endpunkt-URL des Triggers.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Schemas für die XML-Überprüfung in Azure Logic Apps](logic-apps-enterprise-integration-schemas.md)
* [XML-Transformation für Workflows in Azure Logic Apps](logic-apps-enterprise-integration-transform.md)