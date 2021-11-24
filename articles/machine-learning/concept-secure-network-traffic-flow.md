---
title: Sicherer Fluss des Netzwerkverkehrs
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie der Netzwerkverkehr zwischen den Komponenten fließt, wenn sich Ihr Azure Machine Learning-Arbeitsbereich in einem gesicherten virtuellen Netzwerk befindet.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2021
ms.openlocfilehash: d71d986ca975b9617af9b966c8795cd30d7db01d
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134947"
---
# <a name="network-traffic-flow-when-using-a-secured-workspace"></a>Fluss des Netzwerkverkehrs bei Verwendung eines gesicherten Arbeitsbereichs

Wenn Ihr Azure Machine Learning-Arbeitsbereich und die zugehörigen Ressourcen in einem Azure Virtual Network gesichert sind, ändert sich der Netzwerkverkehr zwischen den Ressourcen. Ohne ein virtuelles Netzwerk fließt der Netzwerkverkehr über das öffentliche Internet oder innerhalb eines Azure-Rechenzentrums. Sobald ein virtuelles Netzwerk (VNet) eingeführt ist, möchten Sie möglicherweise auch die Netzwerksicherheit erhöhen. So können Sie beispielsweise die ein- und ausgehende Kommunikation zwischen dem VNet und dem öffentlichen Internet blockieren. Azure Machine Learning erfordert jedoch den Zugriff auf einige Ressourcen im öffentlichen Internet. Zum Beispiel wird Azure Resource Management für Bereitstellungen und Verwaltungsvorgänge verwendet.

Dieser Artikel listet den erforderlichen Datenverkehr zum/vom öffentlichen Internet auf. Er erklärt auch, wie der Netzwerkverkehr zwischen Ihrer Client-Entwicklungsumgebung und einem gesicherten Azure Machine Learning-Arbeitsbereich in den folgenden Szenarien fließt:

* Verwendung von Azure Machine Learning __studio__ zur Arbeit mit:

    * Ihrem Arbeitsbereich
    * AutoML
    * Designer
    * Datasets und Datenspeicher

    > [!TIP]
    > Azure Machine Learning Studio ist eine webbasierte Benutzeroberfläche, die teilweise in Ihrem Webbrowser ausgeführt wird und Azure-Dienste aufruft, um Aufgaben wie das Trainieren eines Modells, die Verwendung von Designern oder die Anzeige von Datensätzen auszuführen. Einige dieser Aufrufe verwenden einen anderen Kommunikationsfluss, als wenn Sie das SDK, CLI, REST API oder VS Code verwenden.

* Verwenden von Azure Machine Learning __studio__, __SDK__, __CLI__ oder __REST API__ zum Arbeiten mit:

    * Compute-Instanzen und Clustern
    * Azure Kubernetes Service
    * Von Azure Machine Learning verwaltete Docker-Images

> [!TIP]
> Wenn ein Szenario oder eine Aufgabe hier nicht aufgeführt ist, sollte sie mit oder ohne einen gesicherten Arbeitsbereich genauso funktionieren.

## <a name="assumptions"></a>Annahmen

Dieser Artikel geht von der folgenden Konfiguration aus:

* Der Azure Machine Learning-Arbeitsbereich verwendet einen privaten Endpunkt für die Kommunikation mit dem VNet.
* Das Azure Storage Account, der Key Vault und die Container Registry, die vom Arbeitsbereich verwendet werden, verwenden ebenfalls einen privaten Endpunkt, um mit dem VNet zu kommunizieren.
* Ein VPN-Gateway oder eine Express Route wird von den Client-Workstations für den Zugriff auf das VNet verwendet.

## <a name="inbound-and-outbound-requirements"></a>Anforderungen für eingehende und ausgehende Verbindungen


| __Szenario__ | __Erforderlich eingehend__ | __Erforderlich ausgehend__ | __Zusätzliche Konfiguration__ | 
| ----- | ----- | ----- | ----- |
| [Zugriff auf den Arbeitsbereich vom Studio](#scenario-access-workspace-from-studio) | Nicht verfügbar | <ul><li>Azure Active Directory</li><li>Azure Front Door</li><li>Azure Machine Learning Service</li></ul> | Möglicherweise müssen Sie einen benutzerdefinierten DNS-Server verwenden. Weitere Informationen finden Sie unter [Verwenden Sie Ihren Arbeitsbereich mit einem benutzerdefinierten DNS](how-to-custom-dns.md). | 
| [Verwenden Sie AutoML, Designer, Dataset und Datenspeicher von studio](#scenario-use-automl-designer-dataset-and-datastore-from-studio) | Nicht verfügbar | Nicht verfügbar | <ul><li>Konfiguration des Arbeitsbereichsdienstes Principal</li><li>Zugriff von vertrauenswürdigen Azure-Diensten zulassen</li></ul>Weitere Informationen finden Sie unter [Wie man einen Arbeitsbereich in einem virtuellen Netzwerk sichert](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts). | 
| [Benutzen Sie Recheninstanz und Rechencluster](#scenario-use-compute-instance-and-compute-cluster) | <ul><li>Azure Machine Learning-Dienst an Port 44224</li><li>Azure Batch Management Dienst auf den Ports 29876-29877</li></ul> | <ul><li>Azure Active Directory</li><li>Azure Resource Manager</li><li>Azure Machine Learning Service</li><li>Azure Storage-Konto</li><li>Azure-Schlüsseltresor</li></ul> | Wenn Sie eine Firewall verwenden, erstellen Sie benutzerdefinierte Routen. Weitere Informationen finden Sie unter [Konfiguration des ein- und ausgehenden Datenverkehrs](how-to-access-azureml-behind-firewall.md). | 
| [Verwenden von Azure Kubernetes Service](#scenario-use-azure-kubernetes-service) | Nicht verfügbar | Informationen zur ausgehenden Konfiguration für AKS finden Sie unter [Bereitstellung für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster). | Konfigurieren Sie den internen Load Balancer. Weitere Informationen finden Sie unter [Bereitstellung für Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster). | 
| [Verwendung von Docker-Images, die von Azure Machine Learning verwaltet werden](#scenario-use-docker-images-managed-by-azure-ml) | Nicht verfügbar | <ul><li>Microsoft Container Registry</li><li>`viennaglobal.azurecr.io` globale Container-Registrierung</li></ul> | Wenn die Azure Container Registry für Ihren Arbeitsbereich hinter dem VNet liegt, konfigurieren Sie den Arbeitsbereich so, dass er einen Compute-Cluster zum Erstellen von Images verwendet. Weitere Informationen finden Sie unter [Wie Sie einen Arbeitsbereich in einem virtuellen Netzwerk sichern](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr). | 

> [!IMPORTANT]
> Azure Machine Learning verwendet mehrere Speicherkonten. Jedes speichert unterschiedliche Daten und hat einen anderen Zweck:
>
> * __Ihr Speicher__: Das Azure Storage-Konto/die Azure Storage-Konten in Ihrem Azure-Abonnement, das zum Speichern Ihrer Daten und Artefakte wie Modelle, Trainingsdaten, Trainingsprotokolle und Python-Skripte verwendet wird. Das _Standard_ speicherkonto für Ihren Arbeitsbereich befindet sich beispielsweise in Ihrem Abonnement. Die Azure Machine Learning Compute-Instanz und Compute-Cluster greifen über die Ports 445 (SMB) und 443 (HTTPS) auf __Datei-__ und __Blob__-Daten in diesem Speicher zu.
> 
>    Wenn Sie eine Compute-Instanz oder einen Compute-Cluster verwenden, wird Ihr Speicherkonto mithilfe des SMB-Protokolls als Dateifreigabe eingebunden. Auf diese Weise greift die Compute-Instanz/der Compute-Cluster auf Ihre Daten zu.
>
> * __Microsoft-Speicher__: Die Azure Machine Learning Compute-Instanz und die Compute-Cluster basieren auf Azure Batch und müssen auf Speicher zugreifen, der sich in einem Microsoft-Abonnement befindet. Dieser Speicher wird nur zur Verwaltung der Compute-Instanz/Compute-Cluster verwendet. Keine Ihrer Daten wird hier gespeichert. Die Compute-Instanz und der Compute-Cluster greifen über Port 443 (HTTPS) auf die __Blob-,__ __Tabellen-__ und __Warteschlangendaten__ in diesem Speicher zu.
>
> Machine Learning speichert Metadaten in einer Azure Cosmos DB-Instanz. Standardmäßig wird diese Instanz in einem Microsoft-Abonnement gehostet und von Microsoft verwaltet. Sie können optional eine Azure Cosmos DB-Instanz in Ihrem Azure-Abonnement verwenden. Weitere Informationen finden Sie unter [Datenverschlüsselung mit Azure Machine Learning](concept-data-encryption.md#azure-cosmos-db).

## <a name="scenario-access-workspace-from-studio"></a>Szenario: Zugriff auf den Arbeitsbereich vom Studio aus

> [!NOTE]
> Die Informationen in diesem Abschnitt beziehen sich auf die Verwendung des Arbeitsbereichs über das Azure Machine Learning-Studio. Wenn Sie das Azure Machine Learning SDK, die REST API, CLI oder Visual Studio Code verwenden, gelten die Informationen in diesem Abschnitt nicht für Sie.

Wenn Sie von Studio aus auf Ihren Arbeitsbereich zugreifen, läuft der Netzwerkverkehr wie folgt ab:

* Für die Authentifizierung bei Ressourcen wird __Azure Active Directory__ verwendet.
* Für Verwaltungs- und Bereitstellungsvorgänge wird __Azure Resource Manager__ verwendet.
* Für Azure Machine Learning spezifische Aufgaben wird __Azure Machine Learning Service__ verwendet
* Für den Zugriff auf Azure Machine Learning Studio (https://ml.azure.com), __Azure FrontDoor__ wird verwendet.
* Für die meisten Speichervorgänge fließt der Datenverkehr über den privaten Endpunkt des Standardspeichers für Ihren Arbeitsbereich. Ausnahmen werden im Abschnitt [Verwendung von AutoML, Designer, Dataset und Datenspeicher](#scenario-use-automl-designer-dataset-and-datastore-from-studio) behandelt.
* Außerdem müssen Sie eine DNS-Lösung konfigurieren, mit der Sie die Namen der Ressourcen innerhalb des VNet auflösen können. Weitere Informationen finden Sie unter [Verwenden Sie Ihren Arbeitsbereich mit einem benutzerdefinierten DNS](how-to-custom-dns.md).

:::image type="content" source="./media/concept-secure-network-traffic-flow/workspace-traffic-studio.png" alt-text="Diagramm des Netzwerkverkehrs zwischen Client und Arbeitsbereich bei Verwendung von studio":::

## <a name="scenario-use-automl-designer-dataset-and-datastore-from-studio"></a>Szenario: Verwendung von AutoML, Designer, Datensatz und Datenspeicher von Studio

Die folgenden Funktionen von Azure Machine Learning Studio verwenden _Datenprofilierung_:

* Dataset: Erkunden Sie das Dataset vom Studio aus.
* Designer: Visualisieren Sie die Ausgabedaten des Moduls.
* AutoML: Zeigen Sie eine Datenvorschau/ein Profil an und wählen Sie eine Zielspalte.
* Bezeichnungen

Die Erstellung von Datenprofilen hängt davon ab, dass der verwaltete Dienst Azure Machine Learning auf das standardmäßige Azure-Storage-Konto für Ihren Arbeitsbereich zugreifen kann. Der verwaltete Dienst _ist nicht in Ihrem VNet_ vorhanden, kann also nicht direkt auf das Speicherkonto im VNet zugreifen. Stattdessen verwendet der Arbeitsbereich einen Dienstprinzipal für den Speicherzugriff.

> [!TIP]
> Sie können bei der Erstellung des Arbeitsbereichs einen Dienstprinzipal angeben. Wenn Sie dies nicht tun, wird einer für Sie erstellt und trägt den gleichen Namen wie Ihr Arbeitsbereich.

Um den Zugriff auf das Speicherkonto zu erlauben, konfigurieren Sie das Speicherkonto so, dass eine __Ressourceninstanz__ für Ihren Arbeitsbereich zugelassen wird, oder wählen Sie __Azure-Diensten auf der Liste vertrauenswürdiger Dienste den Zugriff auf dieses Speicherkonto gestatten__ aus. Diese Einstellung ermöglicht dem verwalteten Dienst den Zugriff auf den Speicher über das Azure-Rechenzentrumsnetzwerk. 

Als Nächstes fügen Sie den Dienstprinzipal für den Arbeitsbereich der Rolle __Reader__ zum privaten Endpunkt des Speicherkontos hinzu. Diese Rolle wird verwendet, um die Informationen über den Arbeitsbereich und das Speichersubnetz zu überprüfen. Wenn sie übereinstimmen, wird der Zugriff gestattet. Schließlich benötigt der Dienstprinzipal auch __Blobdatenmitwirkenden__ Zugriff auf das Speicherkonto.

Weitere Informationen finden Sie im Abschnitt Azure-Storage-Konto in [Wie man einen Arbeitsbereich in einem virtuellen Netzwerk sichert](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts).

:::image type="content" source="./media/concept-secure-network-traffic-flow/storage-traffic-studio.png" alt-text="Diagramm des Datenverkehrs zwischen Client, Datenprofilierung und Speicher":::

## <a name="scenario-use-compute-instance-and-compute-cluster"></a>Szenario: Recheninstanz und Rechencluster verwenden

Azure Machine Learning Compute Instance und Compute Cluster sind verwaltete Dienste, die von Microsoft gehostet werden. Sie bauen auf dem Azure Batch-Dienst auf. Während sie in einer von Microsoft verwalteten Umgebung existieren, werden sie auch in Ihr VNet injiziert.

Wenn Sie eine Recheninstanz oder einen Rechencluster erstellen, werden auch die folgenden Ressourcen in Ihrem VNet erstellt:

* Eine Netzwerksicherheitsgruppe mit erforderlichen Ausgangsregeln. Diese Regeln erlauben den __eingehenden__ Zugriff von Azure Machine Learning (TCP auf Port 44224) und Azure Batch Service (TCP auf Ports 29876-29877).

    > [!IMPORTANT]
    > Wenn Sie eine Firewall verwenden, um den Internetzugang zum VNet zu blockieren, müssen Sie die Firewall so konfigurieren, dass sie diesen Datenverkehr zulässt. Mit Azure Firewall können Sie zum Beispiel benutzerdefinierte Routen erstellen. Weitere Informationen finden Sie unter [Wie Sie Azure Machine Learning mit einer Firewall verwenden](how-to-access-azureml-behind-firewall.md#inbound-configuration).

* Ein Load Balancer mit einer öffentlichen IP-Adresse.

Erlauben Sie auch __ausgehenden__ Zugriff auf die folgenden Service-Tags. Ersetzen Sie für jedes Tag `region` durch die Azure-Region Ihrer Recheninstanz/ Ihres Clusters:

* `Storage.region` - Dieser ausgehende Zugriff wird verwendet, um eine Verbindung zum Azure-Storage-Konto innerhalb des von Azure Batch Service verwalteten VNet herzustellen.
* `Keyvault.region` - Dieser ausgehende Zugang wird verwendet, um sich mit dem Azure Key Vault-Konto innerhalb des vom Azure Batch Service verwalteten VNet zu verbinden.

Der Datenzugriff von Ihrer Compute-Instanz oder Ihrem Cluster erfolgt über den privaten Endpunkt des Speicherkontos für Ihr VNet.

Wenn Sie Visual Studio Code auf einer Compute-Instanz verwenden, müssen Sie anderen ausgehenden Datenverkehr zulassen. Weitere Informationen finden Sie unter [Wie Sie Azure Machine Learning mit einer Firewall verwenden](how-to-access-azureml-behind-firewall.md).

:::image type="content" source="./media/concept-secure-network-traffic-flow/compute-instance-and-cluster.png" alt-text="Diagramm des Datenverkehrsflusses bei Verwendung einer Recheninstanz oder eines Clusters":::

## <a name="scenario-use-azure-kubernetes-service"></a>Szenario: Verwendung von Azure Kubernetes Service

Informationen über die für Azure Kubernetes Service erforderliche ausgehende Konfiguration finden Sie im Abschnitt über die Konnektivitätsanforderungen in [Wie wird Azure Kubernetes Service bereitgestellt](how-to-deploy-azure-kubernetes-service.md#understand-connectivity-requirements-for-aks-inferencing-cluster).

> [!NOTE]
> Der Load Balancer von Azure Kubernetes Service ist nicht derselbe wie der Load Balancer, der von Azure Machine Learning erstellt wird. Wenn Sie Ihr Modell als gesicherte Anwendung hosten möchten, die nur im VNet verfügbar ist, verwenden Sie den von Azure Machine Learning erstellten internen Load Balancer. Wenn Sie den öffentlichen Zugriff zulassen möchten, verwenden Sie den von Azure Machine Learning erstellten öffentlichen Load Balancer.

Wenn Ihr Modell zusätzliche eingehende oder ausgehende Verbindungen benötigt, z. B. zu einer externen Datenquelle, verwenden Sie eine Netzwerksicherheitsgruppe oder Ihre Firewall, um den Datenverkehr zuzulassen.

## <a name="scenario-use-docker-images-managed-by-azure-ml"></a>Szenario: Verwendung von Docker-Images, die von Azure ML verwaltet werden

Azure Machine Learning bietet Docker-Images, die zum Trainieren von Modellen oder zur Durchführung von rückschließen verwendet werden können. Wenn Sie keine eigenen Images angeben, werden die von Azure Machine Learning bereitgestellten Images verwendet. Diese Bilder werden in der Microsoft Container Registry (MCR) gehostet. Sie werden auch in einer georeplizierten Azure Container Registry namens `viennaglobal.azurecr.io` gehostet.

Wenn Sie Ihre eigenen Docker-Images bereitstellen, z. B. auf einer von Ihnen bereitgestellten Azure Container Registry, benötigen Sie die ausgehende Kommunikation mit MCR oder `viennaglobal.azurecr.io` nicht.

> [!TIP]
> Wenn Ihre Azure Container Registry im VNet gesichert ist, kann sie nicht von Azure Machine Learning zum Erstellen von Docker-Images verwendet werden. Stattdessen müssen Sie einen Azure Machine Learning-Rechencluster für die Erstellung von Images bestimmen. Weitere Informationen finden Sie unter [Wie Sie einen Arbeitsbereich in einem virtuellen Netzwerk sichern](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

:::image type="content" source="./media/concept-secure-network-traffic-flow/azure-machine-learning-docker-images.png" alt-text="Diagramm des Verkehrsflusses bei Verwendung der bereitgestellten Docker-Images":::
## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun gelernt haben, wie der Netzwerkverkehr in einer gesicherten Konfiguration fließt, erfahren Sie mehr über die Sicherung von Azure ML in einem virtuellen Netzwerk, indem Sie den Artikel [Überblick über Isolierung und Datenschutz in virtuellen Netzwerken](how-to-network-security-overview.md) lesen.

Informationen zu Best Practices finden Sie im Artikel [Azure Machine Learning Best Practices für Unternehmenssicherheit](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security).