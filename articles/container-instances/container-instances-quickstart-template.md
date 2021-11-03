---
title: 'Schnellstart: Erstellen einer Containerinstanz – Azure Resource Manager-Vorlage'
description: In diesem Schnellstart verwenden Sie eine Azure Resource Manager-Vorlage, um schnell eine Container-Web-App bereitzustellen, die in einer isolierten Azure-Containerinstanz ausgeführt wird.
services: azure-resource-manager
ms.date: 04/30/2020
ms.topic: quickstart
ms.service: azure-resource-manager
ms.custom: subject-armqs, devx-track-js, mode-arm
ms.openlocfilehash: 0b67348f5b824b79e3ef9e5f2ee2375b9e9b1a34
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131045215"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-an-arm-template"></a>Schnellstart: Bereitstellen einer Containerinstanz in Azure mithilfe einer ARM-Vorlage

Führen Sie mithilfe von Azure Container Instances serverlose Docker-Container schnell und einfach in Azure aus. Sie stellen eine Anwendung bedarfsgesteuert in einer Containerinstanz bereit, wenn Sie keine vollständige Containerorchestrierungsplattform wie Azure Kubernetes Service benötigen. In dieser Schnellstartanleitung stellen Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) einen isolierten Docker-Container bereit und machen seine Webanwendung mit einer öffentlichen IP-Adresse verfügbar.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.containerinstance%2Faci-linuxcontainer-public-ip%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/aci-linuxcontainer-public-ip/).

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.containerinstance/aci-linuxcontainer-public-ip/azuredeploy.json":::

In der Vorlage ist die folgende Ressource definiert:

* **[Microsoft.ContainerInstance/containerGroups](/azure/templates/microsoft.containerinstance/containergroups)** : Erstellen einer Azure-Containergruppe. Diese Vorlage definiert eine Gruppe, die aus einer einzelnen Containerinstanz besteht.

Weitere Azure Container Instances-Vorlagenbeispiele finden Sie im [Schnellstartvorlagenkatalog](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

 1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt eine Registrierung und ein Replikat an einem anderen Standort.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.containerinstance%2Faci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    * **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    * **Ressourcengruppe**: Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und wählen Sie dann **OK** aus.
    * **Standort**: Wählen Sie einen Standort für die Ressourcengruppe aus. Beispiel: **USA, Mitte**
    * **Name**: Übernehmen Sie den generierten Namen für die Instanz, oder geben Sie einen Namen ein.
    * **Image**: Übernehmen Sie den Standardnamen für das Image. Dieses Linux-Beispielimage verpackt eine kleine in Node.js geschriebene Web-App, die eine statische HTML-Seite bedient. 

    Übernehmen Sie für die übrigen Eigenschaften die Standardwerte.

    Sehen Sie sich die Geschäftsbedingungen an. Wenn Sie einverstanden sind, aktivieren Sie **Ich stimme den oben genannten Geschäftsbedingungen zu**.

    ![Vorlageneigenschaften](media/container-instances-quickstart-template/template-properties.png)

 3. Nach der erfolgreichen Erstellung der Instanz erhalten Sie eine Benachrichtigung:

    ![Benachrichtigung im Portal](media/container-instances-quickstart-template/deployment-notification.png)

 Zum Bereitstellen der Vorlage wird das Azure-Portal verwendet. Neben dem Azure-Portal können Sie Azure PowerShell, die Azure-Befehlszeilenschnittstelle (Azure CLI) und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Verwenden Sie das Azure-Portal oder ein Tool wie die [Azure CLI](container-instances-quickstart.md), um die Eigenschaften der Containerinstanz zu überprüfen.

1. Suchen Sie im Portal nach „Containerinstanzen“, und wählen Sie die von Ihnen erstellte Containerinstanz aus.

1. Sehen Sie sich auf der Seite **Übersicht** den **Status** der Instanz sowie den Wert unter **IP-Adresse** an.

    ![Übersicht über Instanzen](media/container-instances-quickstart-template/aci-overview.png)

2. Sobald der Status *Ausgeführt* lautet, navigieren Sie in Ihrem Browser zur IP-Adresse. 

    ![Mit Azure Container Instances bereitgestellte App im Browser](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Containerprotokolle anzeigen

Die Protokolle für eine Containerinstanz sind hilfreich, wenn Sie Probleme mit dem Container oder mit der ausgeführten Anwendung behandeln.

Wählen Sie zum Anzeigen der Protokolle des Containers unter **Einstellungen** die Optionen **Container** > **Protokolle** aus. Daraufhin sollte die HTTP GET-Anforderung angezeigt werden, die beim Betrachten der Anwendung in Ihrem Browser generiert wurde.

![Containerprotokolle im Azure-Portal](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Container nicht mehr benötigen, wählen Sie auf der Seite **Übersicht** für die Containerinstanz die Option **Löschen** aus. Bestätigen Sie den Löschvorgang, wenn Sie dazu aufgefordert werden.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Azure-Containerinstanz aus einem öffentlichen Microsoft-Image erstellt. Fahren Sie mit dem Azure Container Instances-Tutorial fort, wenn Sie ein Containerimage erstellen und über eine private Azure-Containerregistrierung bereitstellen möchten.

> [!div class="nextstepaction"]
> [Tutorial: Erstellen eines Containerimages für die Bereitstellung in Azure Container Instances](./container-instances-tutorial-prepare-app.md)

Ein Schritt-für-Schritt-Tutorial mit Anleitungen zum Erstellen einer Vorlage finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
