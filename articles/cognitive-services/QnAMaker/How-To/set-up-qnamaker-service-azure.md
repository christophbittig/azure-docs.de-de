---
title: Einrichten eines QnA Maker-Diensts – QnA Maker
description: Vor dem Erstellen von QnA Maker-Wissensdatenbanken müssen Sie zuerst einen QnA Maker-Dienst in Azure einrichten. Jeder mit der Berechtigung zum Erstellen neuer Ressourcen in einem Abonnement kann einen QnA Maker-Dienst einrichten.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/14/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ce152375289cd48681a87775ef534f5b9e6932de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071361"
---
# <a name="manage-qna-maker-resources"></a>QnA Maker-Ressourcen

Vor dem Erstellen von QnA Maker-Wissensdatenbanken müssen Sie zuerst einen QnA Maker-Dienst in Azure einrichten. Jeder mit der Berechtigung zum Erstellen neuer Ressourcen in einem Abonnement kann einen QnA Maker-Dienst einrichten. Wenn Sie das Feature „Benutzerdefinierte Fragen und Antworten“ ausprobieren, müssen Sie die Textanalyse-Ressource erstellen und das Feature „Benutzerdefinierte Fragen und Antworten“ hinzufügen.

[!INCLUDE [Custom question answering](../includes/new-version.md)]

Ein fundiertes Verständnis der folgenden Konzepte ist hilfreich, bevor Sie Ihre Ressource erstellen:

* [QnA Maker-Ressourcen](../Concepts/azure-resources.md)
* [Erstellungs- und Veröffentlichungsschlüssel](../Concepts/azure-resources.md#keys-in-qna-maker)

## <a name="create-a-new-qna-maker-service"></a>Erstellen eines neuen QnA Maker-Diensts

Mit diesem Verfahren werden die Azure-Ressourcen erstellt, die zum Verwalten des Inhalts der Wissensdatenbank benötigt werden. Nachdem Sie diese Schritte ausgeführt haben, finden Sie die _Abonnementschlüssel_ auf der Seite **Schlüssel** für die Ressource im Azure-Portal.

1. Melden Sie sich beim Azure-Portal an, und [erstellen Sie eine QnA Maker-Ressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker).

1. Nachdem Sie die Geschäftsbedingungen gelesen haben, wählen Sie **Erstellen** aus:

    ![Erstellen eines neuen QnA Maker-Diensts](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. Wählen Sie in **QnA Maker** die geeigneten Tarife und Regionen aus:

    ![Erstellen eines neuen QnA Maker-Diensts – Tarif und Regionen](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Geben Sie im Feld **Name** einen eindeutigen Namen zur Identifizierung dieses QnA Maker-Diensts ein. Dieser Name kennzeichnet auch den QnA Maker-Endpunkt, dem Ihre Wissensdatenbanken zugeordnet werden sollen.
    * Wählen Sie das **Abonnement** aus, unter dem die QnA Maker-Ressource bereitgestellt werden soll.
    * Wählen Sie den **Tarif** für die QnA Maker-Verwaltungsdienste (Portal und Verwaltungs-APIs) aus. Lesen Sie [weitere Informationen zu SKU-Preisen](https://aka.ms/qnamaker-pricing).
    * Erstellen Sie eine neue **Ressourcengruppe** (empfohlen), oder verwenden Sie eine vorhandene Ressourcengruppe, in der diese QnA Maker-Ressource bereitgestellt werden soll. QnA Maker erstellt verschiedene Azure-Ressourcen. Wenn Sie eine Ressourcengruppe für diese Ressourcen erstellen, können Sie die Ressourcen anhand des Ressourcengruppennamens leicht finden, verwalten und löschen.
    * Wählen Sie einen **Ressourcengruppenstandort** aus.
    * Wählen Sie den **Tarif für Suche** des Azure Cognitive Search-Diensts aus. Wenn die Option „Free-Tarif“ nicht verfügbar (abgeblendet) ist, bedeutet dies, dass Ihnen schon über Ihr Abonnement ein kostenloser Dienst bereitgestellt wird. In diesem Fall müssen Sie mit dem Basic-Tarif beginnen. Siehe [Azure Cognitive Search – Preise](https://azure.microsoft.com/pricing/details/search/).
    * Wählen Sie den **Suchspeicherort** aus, an dem die Azure Cognitive Search-Indizes bereitgestellt werden sollen. Einschränkungen zu den verpflichtenden Speicherorten für Kundendaten helfen bei der Bestimmung des Speicherorts, den Sie für Azure Cognitive Search wählen.
    * Geben Sie im Feld **App-Name** einen Namen für Ihre Azure App Service-Instanz ein.
    * Standardmäßig wird App Service nach dem Standardtarif (S1) abgerechnet. Sie können den Plan nach der Erstellung ändern. Erfahren Sie mehr über [App Service-Preise](https://azure.microsoft.com/pricing/details/app-service/).
    * Wählen Sie den **Website-Speicherort** aus, an dem App Service bereitgestellt werden soll.

        > [!NOTE]
        > Der **Suchspeicherort** kann vom **Website-Speicherort** abweichen.

    * Wählen Sie, ob Sie **Application Insights** aktivieren möchten. Wenn **Application Insights** aktiviert ist, sammelt QnA Maker Telemetriedaten zu Datenverkehr, Chatprotokollen und Fehlern.
    * Wählen Sie den **App Insights-Speicherort** aus, an dem die Application Insights-Ressource bereitgestellt werden soll.
    * Zur Kosteneinsparung können Sie einige, aber nicht alle Azure-Ressourcen, die für QnA Maker erstellt wurden, [freigeben](configure-QnA-Maker-resources.md#configure-qna-maker-to-use-different-cognitive-search-resource).

1. Nachdem Sie alle Felder überprüft haben, wählen Sie **Erstellen** aus. Der Vorgang kann einige Minuten dauern.

1. Nachdem die Bereitstellung abgeschlossen ist, werden die folgenden neu erstellten Ressourcen in Ihrem Abonnement angezeigt:

   ![Im neuen QnA Maker-Dienst erstellte Ressource](../media/qnamaker-how-to-setup-service/resources-created.png)

    Die Ressource mit dem _Cognitive Services_-Typ enthält Ihre _Abonnementschlüssel_.

## <a name="upgrade-azure-resources"></a>Durchführen von Upgrades für Azure-Ressourcen

### <a name="upgrade-qna-maker-sku"></a>Upgraden der QnA Maker-SKU

Wenn Ihre Wissensdatenbank mehr Fragen und Antworten enthalten soll, die über Ihren aktuellen Tarif hinausgehen, upgraden Sie den Tarif Ihres QnA Maker-Diensts.

So führen Sie ein Upgrade der QnA Maker Management-SKU aus:

1. Wechseln Sie im Azure-Portal zu Ihrer QnA Maker-Ressource, und wählen Sie **Tarif** aus.

    ![QnA Maker-Ressource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

1. Wählen Sie die passende SKU aus, und drücken Sie **Auswählen**.

    ![QnA Maker-Preise](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)
    
### <a name="upgrade-app-service"></a>Upgraden von App Service

Wenn Ihre Wissensdatenbank mehr Anforderungen aus Ihrer Client-App verarbeiten muss, upgraden Sie Ihren App Service-Tarif.

Sie können den App Service [hochskalieren](../../../app-service/manage-scale-up.md) oder aufskalieren.

Wechseln Sie im Azure-Portal zur App Service-Ressource, und wählen Sie nach Bedarf die Option **Hochskalieren** oder **Aufskalieren** aus.

![QnA Maker App Service-Staffelung](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

### <a name="upgrade-the-azure-cognitive-search-service"></a>Upgrade des Azure Cognitive Search-Diensts

Wenn Sie voraussichtlich eine Vielzahl von Wissensdatenbanken benötigen, upgraden Sie den Tarif Ihres Azure Cognitive Search-Diensts.

Sie können derzeit kein direktes Upgrade der Azure Search-SKU ausführen. Allerdings können Sie eine neue Azure Search-Ressource mit der gewünschten SKU erstellen, die Daten in der neuen Ressource wiederherstellen und anschließend eine Verknüpfung mit dem QnA Maker-Stapel herstellen. Gehen Sie dazu folgendermaßen vor:

1. Erstellen Sie im Azure-Portal eine neue Azure Search-Ressource, und wählen Sie die gewünschte SKU aus.

    ![QnA Maker-Azure Search-Ressource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

1. Stellen Sie die Indizes aus Ihrer ursprünglichen Azure Search-Ressource in der neuen wieder her. Sehen Sie dazu den [Beispielcode für die Wiederherstellung aus einer Sicherung](https://github.com/pchoudhari/QnAMakerBackupRestore).

1. Nachdem die Daten wiederhergestellt wurden, wechseln Sie zu Ihrer neuen Azure Search-Ressource, wählen Sie **Schlüssel** aus, und notieren Sie sich den **Namen** und den **Administratorschlüssel**:

    ![Schlüssel von QnA Maker-Azure Search](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

1. Um die neue Azure Search-Ressource mit dem QnA Maker-Stapel zu verknüpfen, wechseln Sie zur QnA Maker App Service-Instanz.

    ![QnA Maker App Service-Instanz](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

1. Wählen Sie **Anwendungseinstellungen** aus, und ändern Sie die Einstellungen in den Feldern **AzureSearchName** und **AzureSearchAdminKey** aus Schritt 3.

    ![QnA Maker App Service-Einstellung](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

1. Starten Sie die App Service-Instanz neu.

    ![Neustarten der QnA Maker App Service-Instanz](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
### <a name="inactivity-policy-for-free-search-resources"></a>Richtlinie zur Inaktivität für kostenlose Search-Ressourcen

Wenn Sie keine QnA Maker-Ressource verwenden, sollten Sie alle Ressourcen entfernen. Wenn Sie die nicht verwendeten Ressourcen nicht entfernen, wird Ihre Wissensdatenbank nicht mehr funktionieren, wenn Sie eine kostenlose Search-Ressource erstellt haben.

Kostenlose Search-Ressourcen werden nach 90 Tagen gelöscht, wenn kein API-Aufruf empfangen wurde.

## <a name="delete-azure-resources"></a>Löschen von Azure-Ressourcen

Wenn Sie Azure-Ressourcen löschen, die für Ihre Wissensdatenbanken verwendet werden, funktionieren diese Wissensdatenbanken nicht mehr. Stellen Sie vor dem Löschen von Ressourcen sicher, dass Sie Ihre Wissensdatenbanken auf der Seite **Einstellungen** exportieren.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über den [App-Dienst](../../../app-service/index.yml) und den [Suchdienst](../../../search/index.yml).

> [!div class="nextstepaction"]
> [Erstellen zusammen mit anderen Personen](../index.yml)
