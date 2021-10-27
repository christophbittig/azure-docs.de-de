---
title: Festlegen der technischen Konfiguration für den Plan für ein Azure Container-Angebot in Microsoft AppSource
description: Festlegen der technischen Konfiguration für den Plan für ein Azure Container-Angebot in Microsoft AppSource
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 04/21/2021
ms.openlocfilehash: f6145e8732252f7d7b407a2d258d2127e6911ddc
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072155"
---
# <a name="set-plan-technical-configuration-for-an-azure-container-offer"></a>Festlegen der technischen Konfiguration für den Plan für ein Azure Container-Angebot

Containerimages müssen in einer privaten [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)-Instanz gehostet werden. Verwenden Sie diese Seite, um Referenzinformationen für Ihr Containerimagerepository innerhalb der Azure Container Registry-Instanz bereitzustellen.

Nachdem Sie das Angebot übermittelt haben, wird Ihr Containerimage in Azure Marketplace in eine bestimmte öffentliche Containerregistrierung kopiert. Alle Anforderungen von Azure-Benutzern zur Verwendung Ihres Moduls werden von der öffentlichen Containerregistrierung von Azure Marketplace bedient, nicht von Ihrer privaten Containerregistrierung.

Mithilfe von Tags können Sie mehrere Plattformen anzielen und verschiedene Versionen des Containerimages Ihres Moduls bereitstellen. Weitere Informationen zu Tags und Versionsverwaltung finden Sie unter [Vorbereiten von technischen Ressourcen für Azure-Container](azure-container-technical-assets.md).

## <a name="image-repository-details"></a>Details des Imagerepositorys

Geben Sie die **Azure-Abonnement-ID** an, unter der die Ressourcennutzung gemeldet und Dienste für die Azure Container Registry-Instanz abgerechnet werden, die Ihr Containerimage enthält. Sie finden diese ID auf der Seite [Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) im Azure-Portal.

Geben Sie den [**Namen der Azure-Ressourcengruppe**](../azure-resource-manager/management/manage-resource-groups-portal.md) an, die die Azure Container Registry-Instanz mit Ihrem Containerimage enthält. Der Zugriff auf die Ressourcengruppe muss über die Abonnement-ID (oben) möglich sein. Sie finden den Namen im Azure-Portal auf der Seite [Ressourcengruppen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups).

Geben Sie den [**Namen der Azure Container Registry**](../container-registry/container-registry-intro.md) an, die Ihr Containerimage enthält. Die Containerregistrierung muss in der Azure-Ressourcengruppe vorhanden sein, die Sie zuvor angegeben haben. Geben Sie nur den Registrierungs Namen und nicht den vollständigen Anmelde Servernamen an. Lassen Sie **azurecr.io** beim Namen aus. Sie finden den Registrierungsnamen auf der Seite [Containerregistrierungen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) im Azure-Portal.

Geben Sie den [**Administratorbenutzernamen für die Azure Container Registry**](../container-registry/container-registry-authentication.md#admin-account) an, der der Azure Container Registry-Instanz mit Ihrem Containerimage zugeordnet ist. Benutzername und Kennwort (nächster Schritt) sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Um den Administratorbenutzernamen und das zugeordnete Kennwort abzurufen, legen Sie die Eigenschaft **admin-enabled** (Von Administrator aktiviert) an der Azure-Befehlszeilenschnittstelle auf **True** fest. Optional können Sie im Azure-Portal **Administratorbenutzer** auf **Aktivieren** festlegen.

:::image type="content" source="media/azure-container/azure-create-12-update-container-registry-edit.png" alt-text="Darstellung des Dialogfelds „Containerregistrierung aktualisieren“.":::

Geben Sie das **Administratorkennwort für die Azure Container Registry** für den Administratorbenutzernamen an, der der Azure Container Registry-Instanz mit Ihrem Containerimage zugeordnet ist. Benutzername und Kennwort sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Sie können das Kennwort im Azure-Portal unter **Containerregistrierung** > **Zugriffsschlüssel** oder in der Azure-Befehlszeilenschnittstelle mit dem [Befehl „show“](/cli/azure/acr/credential#az-acr-credential-show) abrufen.

:::image type="content" source="media/azure-container/azure-create-13-access-keys.png" alt-text="Darstellung des Zugriffsschlüssel-Bildschirms im Azure-Portal.":::

Geben Sie den **Repositorynamen innerhalb der Azure Container Registry** an, die Ihr Image enthält. Sie geben den Namen des Repositorys an, wenn Sie das Image per Push an die Registrierung übertragen. Sie finden den Namen des Repositorys auf der Seite [Container Registry](https://azure.microsoft.com/services/container-registry/) > **Repositorys**. Weitere Informationen finden Sie unter [Anzeigen von Azure Container Registry-Repositorys im Azure-Portal](../container-registry/container-registry-repositories.md). Nachdem der Name festgelegt wurde, kann er nicht mehr geändert werden. Verwenden Sie für jedes Angebot in Ihrem Konto einen eindeutigen Namen.

## <a name="image-versions"></a>Imageversionen

Kunden müssen imstande sein, Updates beim Azure Marketplace automatisch zu erhalten, wenn Sie ein Update veröffentlichen. Wenn sie kein Update ausführen möchten, müssen sie die Möglichkeit haben, bei einer bestimmten Version Ihres Images zu bleiben. Sie können dies erreichen, indem Sie für jedes Update des Images neue Imagetags hinzufügen.

Wählen Sie **Imageversion hinzufügen** aus, um ein **Imagetag** hinzuzufügen, das auf die neueste Version Ihres Images auf allen unterstützten Plattformen verweist. Es muss außerdem ein Versionstag enthalten (beispielsweise beginnend mit xx.xx.xx, wobei xx eine Zahl ist). Kunden sollten [Manifesttags](https://github.com/estesp/manifest-tool) verwenden, um mehrere Zielplattformen anzugeben. Zudem müssen alle Tags, auf die von einem Manifesttag verwiesen wird, hinzugefügt werden, damit wir sie hochladen können. Alle Manifesttags (mit Ausnahme des aktuellsten Tags) müssen entweder mit X.Y- oder X.Y.Z- beginnen, wobei X, Y und Z ganze Zahlen sind. Wenn ein aktuelles Tag beispielsweise auf `1.0.1-linux-x64`, `1.0.1-linux-arm32` und `1.0.1-windows-arm32` verweist, müssen diese sechs Tags zum Feld hinzugefügt werden. Ausführliche Informationen zu Tags und Versionsverwaltung finden Sie unter [Vorbereiten Ihrer technischen Ressourcen für Azure-Container](azure-container-technical-assets.md).

> [!TIP]
> Fügen Sie Ihrem Image ein Testtag hinzu, damit Sie das Image beim Testen erkennen können.

<!-- possible future restore

## Samples

These examples show how the plan listing fields appear in different views.

These are the fields in Azure Marketplace when viewing plan details:

:::image type="content" source="media/azure-container/azure-create-10-plan-details-mtplc.png" alt-text="Illustrates the fields you see when viewing plan details in Azure Marketplace.":::

These are plan details on the Azure portal:

:::image type="content" source="media/azure-container/azure-create-11-plan-details-portal.png" alt-text="Illustrates plan details on the Azure portal.":::

Select **Save draft**, then **← Plan overview**  in the left-nav menu to return to the plan overview page.
-->
## <a name="next-steps"></a>Nächste Schritte

- Für das **Co-Selling mit Microsoft** (optional) wählen Sie die entsprechende Option im linken Navigationsmenü aus. Ausführliche Informationen finden Sie unter [Partner-Engagement für Co-Selling](./co-sell-overview.md).
- Für den **Verkauf über CSPs** (Cloud Solution Partners, ebenfalls optional) wählen Sie die entsprechende Option im linken Navigationsmenü aus. Ausführliche Informationen finden Sie unter [Verkaufen über CSP-Partner](cloud-solution-providers.md).
- Wenn Sie keine dieser Optionen einrichten oder fertig sind, ist es an der Zeit, [Ihr Angebot zu überprüfen und zu veröffentlichen](review-publish-offer.md).