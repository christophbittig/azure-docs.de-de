---
title: Erstellen eines Datencontrollers
description: Erstellen Sie einen Azure Arc-Datencontroller in einem typischen Kubernetes-Cluster mit mehreren Knoten, den Sie bereits bereitgestellt haben.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 74c41ed89a3980967532955e46d4dade9eaacdb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346063"
---
# <a name="create-the-azure-arc-data-controller"></a>Erstellen des Azure Arc-Datencontrollers


## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Übersicht über die Erstellung des Azure Arc-Datencontrollers

Azure Arc-fähige Datendienste können in vielen verschiedenen Typen von Kubernetes-Clustern und Managed Kubernetes-Diensten unter Verwendung unterschiedlicher Ansätze erstellt werden.

Derzeit werden die folgenden Kubernetes-Dienste und -Distributionen unterstützt:

- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service in Azure Stack HCI
- Azure RedHat OpenShift (ARO)
- OpenShift Container Platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes Engine (GKE)
- Open-Source-Upstreamversion von Kubernetes – in der Regel mit kubeadm bereitgestellt

> [!IMPORTANT]
> * Die unterstützte Mindestversion von Kubernetes ist v1.17. Weitere Informationen finden Sie unter [Bekannte Probleme](./release-notes.md#known-issues). 
> * Die unterstützte Mindestversion von OCP ist 4.5.
> * Unter [Konnektivitätsanforderungen](connectivity.md) erfahren Sie, welche Konnektivität zwischen Ihrer Umgebung und Azure erforderlich ist.
> * Informationen zum Konfigurieren Ihres beständigen Speichers finden Sie in der [Anleitung zur Speicherkonfiguration](storage-configuration.md).
> * Wenn Sie Azure Kubernetes Service verwenden, muss die Größe der Workerknoten-VM Ihres Clusters mindestens **Standard_D8s_v3** betragen, und der Cluster muss **Premium-Datenträger** nutzen. Der Cluster sollte sich nicht über mehrere Verfügbarkeitszonen hinweg ausdehnen. 
> * Wenn Sie eine andere Kubernetes-Distribution oder einen anderen Kubernetes-Dienst verwenden, sollten Sie sicherstellen, dass der Knoten eine Mindestgröße von 8 GB RAM und 4 Kerne umfasst und dass auf allen Kubernetes-Knoten eine Gesamtkapazität von 32 GB RAM zur Verfügung steht. Beispielsweise können Sie über einen Knoten mit 32 GB RAM und 4 Kernen oder über zwei Knoten mit jeweils 16 GB RAM und vier Kernen verfügen.

> [!NOTE]
> Bei Verwendung von Red Hat OpenShift Container Platform in Azure empfiehlt es sich, die neueste verfügbare Version zu verwenden.

Abhängig von der gewählten Option sind bestimmte Tools _erforderlich_. Es wird jedoch empfohlen, [alle Clienttools zu installieren](./install-client-tools.md), bevor Sie mit der Erstellung des Azure Arc-Datencontrollers beginnen.

Unabhängig von der gewählten Option müssen Sie während der Erstellung die folgenden Informationen angeben:

- **Data controller name** (Name des Datencontrollers): Ein beschreibender Name für Ihren Datencontroller, z. B. „Produktionsdatencontroller“, „Datencontroller Seattle“.
- **Data controller username** (Benutzername für Datencontroller): Ein Benutzername für den Datencontrolleradministrator.
- **Data controller password** (Kennwort für Datencontroller): Ein Kennwort für den Datencontrolleradministrator.
- **Name of your Kubernetes namespace** (Name des Kubernetes-Namespace): Der Name des Kubernetes-Namespace, in dem Sie den Datencontroller erstellen möchten.
- **Connectivity mode** (Konnektivitätsmodus): Der Konnektivitätsmodus bestimmt den Grad der Konnektivität zwischen Ihrer Azure Arc-fähigen Datendienstumgebung und Azure. Der indirekte Konnektivitätsmodus ist allgemein verfügbar. Der direkte Konnektivitätsmodus befindet sich in der Vorschauphase.  Weitere Informationen finden Sie unter [Konnektivitätsmodi und -anforderungen](./connectivity.md). 
- **Azure subscription ID** (Azure-Abonnement-ID): Die Azure-Abonnement-GUID für den Ort, an dem die Datencontrollerressource in Azure erstellt werden soll.
- **Azure resource group name** (Azure-Ressourcengruppenname): Der Name der Ressourcengruppe, in der die Datencontrollerressource in Azure erstellt werden soll.
- **Azure Location** (Azure-Standort): Der Azure-Standort, an dem die Metadaten der Datencontrollerressource in Azure gespeichert werden. Eine Liste der verfügbaren Regionen finden Sie unter [Globale Azure-Infrastruktur/Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc). Die Metadaten und Abrechnungsinformationen zu den Azure-Ressourcen, die vom bereitgestellten Datencontroller verwaltet werden, werden nur an dem Speicherort in Azure gespeichert, den Sie als Location-Parameter angeben. Wenn Sie die Bereitstellung im Direktverbindungsmodus durchführen, entspricht der Location-Parameter für den Datencontroller dem Speicherort der benutzerdefinierten Standortressource, die Sie als Ziel verwenden.

## <a name="next-steps"></a>Nächste Schritte

Es gibt mehrere Optionen zum Erstellen des Azure Arc-Datencontrollers:

> **Möchten Sie es selbst ausprobieren?**  
> Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte unternehmen.
> 
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über die CLI](create-data-controller-indirect-cli.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus über das Azure-Portal mithilfe eines Jupyter Notebooks in Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Erstellen eines Datencontrollers im indirekten Konnektivitätsmodus mit Kubernetes-Tools wie kubectl oder oc](create-data-controller-using-kubernetes-native-tools.md)
- [Erstellen eines Datencontrollers im direkten Konnektivitätsmodus](create-data-controller-direct-prerequisites.md)
- [Erstellen eines Datencontrollers mit dem Azure Arc-Schnelleinstieg für eine beschleunigte Testbereitstellung](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
