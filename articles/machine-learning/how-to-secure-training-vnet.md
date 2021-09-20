---
title: Schützen von Trainingsumgebungen mit virtuellen Netzwerken
titleSuffix: Azure Machine Learning
description: Verwenden Sie eine isolierte Azure Virtual Network-Instanz, um Ihre Azure Machine Learning-Trainingsumgebung zu schützen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 08/04/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: b4b7f35173b4f1d6d83d9b7ffd937704750f5502
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122603968"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Schützen einer Azure Machine Learning-Trainingsumgebung mit virtuellen Netzwerken

In diesem Artikel erfahren Sie, wie Sie Trainingsumgebungen mit einem virtuellen Netzwerk in Azure Machine Learning schützen.

> [!TIP]
> Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:
>
> * [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
> * [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
> * [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
> * [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
> * [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
> * [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)
>
> Ein Tutorial zum Erstellen eines sicheren Arbeitsbereichs, eines Computeclusters und einer Compute-Instanz finden Sie unter [Tutorial: Erstellen eines sicheren Arbeitsbereichs](tutorial-create-secure-workspace.md).

In diesem Artikel erfahren Sie, wie Sie die folgenden Trainingscomputeressourcen in einem virtuellen Netzwerk schützen:
> [!div class="checklist"]
> - Azure Machine Learning-Computecluster
> - Azure Machine Learning-Computeinstanz
> - Azure Databricks
> - Virtual Machine
> - HDInsight-Cluster

## <a name="prerequisites"></a>Voraussetzungen

+ Im Artikel [Übersicht über die Netzwerksicherheit](how-to-network-security-overview.md) finden Sie Informationen zu gängigen Szenarien im Zusammenhang mit virtuellen Netzwerken sowie zur Gesamtarchitektur virtueller Netzwerke.

+ Ein vorhandenes virtuelles Netzwerk und Subnetz, die mit Ihren Computeressourcen verwendet werden können

+ Ihr Benutzerkonto muss über die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) zu den folgenden Aktionen berechtigt werden, um Ressourcen in einem virtuellen Netzwerk oder Subnetz bereitstellen zu können:

    - „Microsoft.Network/virtualNetworks/*/read“ für die VNET-Ressource Dies ist für ARM-Vorlagenbereitstellungen nicht erforderlich.
    - „Microsoft.Network/virtualNetworks/subnet/join/action“ auf der Subnetzressource

    Weitere Informationen zur rollenbasierten Zugriffssteuerung von Azure in Netzwerken finden Sie unter [Integrierte Netzwerkrollen](../role-based-access-control/built-in-roles.md#networking).

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning-Computecluster/-Instanz

* Das virtuelle Netzwerk muss sich im selben Abonnement befinden wie der Azure Machine Learning-Arbeitsbereich.
* Das für die Compute-Instanz oder den Computecluster verwendete Subnetz muss über ausreichend nicht zugewiesene IP-Adressen verfügen.

    * Ein Computecluster kann dynamisch skaliert werden. Wenn nicht ausreichend freie IP-Adressen vorhanden sind, wird der Cluster teilweise zugeordnet.
    * Eine Compute-Instanz erfordert nur eine IP-Adresse.

* Stellen Sie sicher, dass keine Sicherheitsrichtlinien oder Sperren vorhanden sind, die Berechtigungen zum Verwalten des virtuellen Netzwerks einschränken. Wenn Sie auf Richtlinien oder Sperren prüfen, betrachten Sie sowohl das Abonnement als auch die Ressourcengruppe für das virtuelle Netzwerk.
* Überprüfen Sie, ob Berechtigungen für die Verwaltung des virtuellen Netzwerks durch Ihre Sicherheitsrichtlinien oder -sperren für das Abonnement oder die Ressourcengruppe Ihres virtuellen Netzwerks eingeschränkt werden. 
* Wenn Sie das virtuelle Netzwerk durch Einschränkung des Datenverkehrs sichern möchten, lesen Sie den Abschnitt [Erforderlicher öffentlicher Internetzugriff](#required-public-internet-access).
* Das zum Bereitstellen des Computeclusters/der Compute-Instanz verwendete Subnetz darf nicht an einen anderen Dienst delegiert werden. Beispielsweise sollte es nicht an ACI delegiert werden.

### <a name="azure-databricks"></a>Azure Databricks

* Das virtuelle Netzwerk muss sich im selben Abonnement und in derselben Region befinden wie der Azure Machine Learning-Arbeitsbereich.
* Wenn die Azure Storage-Konten für den Arbeitsbereich ebenfalls in einem virtuellen Netzwerk geschützt sind, müssen sie sich im selben virtuellen Netzwerk befinden wie der Azure Databricks-Cluster.

## <a name="limitations"></a>Einschränkungen

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning-Computecluster/-Instanz

* Wenn Sie mehrere Compute-Instanzen oder Computecluster in einem einzelnen virtuellen Netzwerk platzieren möchten, müssen Sie möglicherweise eine Kontingenterhöhung für eine oder mehrere Ihrer Ressourcen anfordern. Die Machine Learning-Compute-Instanz bzw. der Cluster ordnet __in der Ressourcengruppe mit dem virtuellen Netzwerk__ automatisch zusätzliche Netzwerkressourcen zu. Für alle Compute-Instanzen und -cluster ordnet der Dienst folgende Ressourcen zu:

    * Eine Netzwerksicherheitsgruppe (NSG) Diese NSG enthält die folgenden Regeln, die spezifisch für Computecluster und Compute-Instanzen sind:

        * Zulassen des eingehenden TCP-Datenverkehrs an den Ports 29876-29877 vom Diensttag `BatchNodeManagement`.
        * Zulassen des eingehenden TCP-Datenverkehrs am Port 44224 vom Diensttag `AzureMachineLearning`.

        Im folgenden Screenshot sehen Sie ein Beispiel für diese Regeln:

        :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="Screenshot: NSG":::

    * Eine öffentliche IP-Adresse. Sollte die Erstellung einer öffentlichen IP-Adresse durch Azure-Richtlinienzuweisungen verhindert werden, wird die Cluster-/Instanzenbereitstellung fehlschlagen.
    * Ein Lastenausgleichsmodul

    Bei Computeclustern werden diese Ressourcen bei jedem Herunterskalieren des Clusters auf 0 Knoten gelöscht und beim Hochskalieren erstellt.

    Bei einer Compute-Instanz werden diese Ressourcen beibehalten, bis die Instanz gelöscht wird. Durch das Beenden der Instanz werden die Ressourcen nicht entfernt. 

    > [!IMPORTANT]
    > Diese Ressourcen werden durch die [Ressourcenkontingente](../azure-resource-manager/management/azure-subscription-service-limits.md) des Abonnements beschränkt. Ist die Ressourcengruppe des virtuellen Netzwerks gesperrt, kann der Computecluster/die Compute-Instanz nicht erfolgreich gelöscht werden. Der Lastenausgleich kann erst nach dem Löschen des Computeclusters/der Compute-Instanz gelöscht werden. Stellen Sie außerdem sicher, dass keine Azure-Richtlinienzuweisungen vorhanden sind, die die Erstellung von Netzwerksicherheitsgruppen verhindern.

* Wenn sich Azure Storage-Konten für den Arbeitsbereich auch im virtuellen Netzwerk befinden, verwenden Sie die folgende Anleitung zu Subnetzeinschränkungen:

    * Wenn Sie Azure Machine Learning __Studio__ zur Visualisierung von Daten oder zur Verwendung von Designer verwenden möchten, muss sich das Speicherkonto __im selben Subnetz wie die Compute-Instanz oder der Cluster befinden__.
    * Wenn Sie planen, das __SDK__ zu verwenden, kann sich das Speicherkonto in einem anderen Subnetz befinden.

    > [!NOTE]
    > Das Aktivieren des Kontrollkästchens „Hiermit erlauben Sie vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Konto.“ reicht nicht aus, um die ausgehende Kommunikation der Compute-Instanz zuzulassen.

* Wenn Ihr Arbeitsbereich einen privaten Endpunkt verwendet, ist der Zugriff auf die Compute-Instanz nur innerhalb des virtuellen Netzwerks möglich. Wenn Sie eine benutzerdefinierte DNS- oder Hostsdatei verwenden, fügen Sie einen Eintrag für `<instance-name>.<region>.instances.azureml.ms` hinzu. Ordnen Sie diesen Eintrag der privaten IP-Adresse des privaten Endpunkts des Arbeitsbereichs zu. Weitere Informationen finden Sie im Artikel [Benutzerdefiniertes DNS](./how-to-custom-dns.md).
* VNet-Dienstendpunktrichtlinien funktionieren für Systemspeicherkonten des Computeclusters/der Compute-Instanz nicht.
* Wenn sich Speicher- und Compute-Instanzen in unterschiedlichen Regionen befinden, können zeitweilig Timeouts auftreten.
* Wenn Sie Jupyter Notebooks auf einer Compute-Instanz verwenden möchten:

    * Deaktivieren Sie die WebSocket-Kommunikation nicht. Vergewissern Sie sich, dass Ihr Netzwerk die Websocket-Kommunikation mit `*.instances.azureml.net` und `*.instances.azureml.ms` gestattet.
    * Vergewissern Sie sich, dass Ihr Notebook auf einer Computeressource ausgeführt wird, die sich hinter demselben virtuellen Netzwerk und Subnetz wie Ihre Daten befindet. Verwenden Sie beim Erstellen der Compute-Instanz die Option **Erweiterte Einstellungen** > **Virtuelles Netzwerk konfigurieren**, um das Netzwerk und das Subnetz auszuwählen.

* __Computecluster__ können in einer anderen Region als der Ihres Arbeitsbereichs erstellt werden. Diese Funktionalität befindet sich in der __Vorschau__ und ist nur für __Computecluster__ und nicht für Compute-Instanzen verfügbar. Wenn Sie eine andere Region für den Cluster verwenden, gelten die folgenden Einschränkungen:

    * Wenn sich Ihre mit dem Arbeitsbereich verbundenen Ressourcen, z. B. Speicher, in einem anderen virtuellen Netzwerk befinden als der Cluster, richten Sie ein globales Peering virtueller Netzwerke zwischen den Netzwerken ein. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md).
    * Wenn Sie einen Arbeitsbereich mit privaten Endpunkten verwenden, wird das Erstellen des Clusters in einer anderen Region __nicht unterstützt__.
    * Es kann sein, dass sich die Netzwerklatenz und die Datenübertragungskosten erhöhen. Latenz und Kosten können beim Erstellen des Clusters und beim Anwenden von Aufträgen auf den Cluster anfallen.

    Anleitungen wie die Verwendung von NSG-Regeln, benutzerdefinierten Routen und Eingabe-/Ausgabeanforderungen werden ganz normal angewendet, wenn eine andere Region als für den Arbeitsbereich verwendet wird.

### <a name="azure-databricks"></a>Azure Databricks

* Zusätzlich zu den von Azure Databricks verwendeten Subnetzen __databricks-private__ und __databricks-public__ ist auch das für das virtuelle Netzwerk erstellte __standardmäßige__ Subnetz erforderlich.
* Azure Databricks verwendet keinen privaten Endpunkt zur Kommunikation mit dem virtuellen Netzwerk.

Weitere Informationen zur Verwendung von Azure Databricks mit einem virtuellen Netzwerk finden Sie unter [Bereitstellen von Azure Databricks in Ihrem Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

### <a name="azure-hdinsight-or-virtual-machine"></a>Azure HDInsight oder virtueller Computer

* Azure Machine Learning unterstützt nur virtuelle Computer, auf denen Ubuntu ausgeführt wird.

## <a name="required-public-internet-access"></a>Erforderlicher öffentlicher Internetzugriff

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

Informationen zur Verwendung einer Firewalllösung finden Sie unter [Verwenden einer Firewall mit Azure Machine Learning](how-to-access-azureml-behind-firewall.md).

## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Computecluster und -instanzen 

Verwenden Sie die folgenden Registerkarten, um auszuwählen, wie Sie einen Computecluster erstellen möchten:

# <a name="studio"></a>[Studio](#tab/azure-studio)

Verwenden Sie die folgenden Schritte, um einen Computecluster im Azure Machine Learning Studio zu erstellen:

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an, und wählen Sie dann Ihr Abonnement und den Arbeitsbereich aus.
1. Wählen Sie auf der linken Seite __Compute__, in der Mitte __Computecluster__ und dann __+ Neu__ aus.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster.png" alt-text="Screenshot: Erstellen eines Clusters":::

1. Wählen Sie im Dialogfeld __Computecluster erstellen__ die VM-Größe und -Konfiguration aus, die Sie benötigen, und wählen Sie dann __Weiter__ aus.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-vm.png" alt-text="Screenshot: Festlegen der VM-Konfiguration":::

1. Legen Sie im Abschnitt __Einstellungen konfigurieren__ den __Computenamen__, das __virtuelle Netzwerk__ und das __Subnetz__ fest.

    > [!TIP]
    > Wenn Ihr Arbeitsbereich einen privaten Endpunkt verwendet, um eine Verbindung mit dem virtuellen Netzwerk herzustellen, wird das Auswahlfeld __Virtuelles Netzwerk__ abgeblendet angezeigt.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-config.png" alt-text="Screenshot: Einstellungen des virtuellen Netzwerks":::

1. Wählen Sie __Erstellen__ aus, um den Computecluster zu erstellen.

# <a name="python"></a>[Python](#tab/python)

Der folgende Code erstellt einen neuen Machine Learning Compute-Cluster im Subnetz `default` eines virtuellen Netzwerks namens `mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           location="westus2",
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

---

Nach Abschluss des Erstellungsprozesses trainieren Sie Ihr Modell, indem Sie den Cluster in einem Experiment verwenden. Weitere Informationen finden Sie unter [Auswählen und Verwenden eines Computeziels für das Training](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="inbound-traffic"></a>Eingehender Datenverkehr

[!INCLUDE [udr info for computes](../../includes/machine-learning-compute-user-defined-routes.md)]

Weitere Informationen zu den Anforderungen an den Datenverkehr für Azure Machine Learning finden Sie unter [Verwenden eines Arbeitsbereichs hinter einer Firewall](how-to-access-azureml-behind-firewall.md).

## <a name="azure-databricks"></a>Azure Databricks

Weitere Informationen zur Verwendung von Azure Databricks mit einem virtuellen Netzwerk finden Sie unter [Bereitstellen von Azure Databricks in Ihrem Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtueller Computer oder HDInsight-Cluster

In diesem Abschnitt erfahren Sie, wie Sie einen virtuellen Computer oder einen Azure HDInsight-Cluster in einem virtuellen Netzwerk mit Ihrem Arbeitsbereich verwenden.

### <a name="create-the-vm-or-hdinsight-cluster"></a>Erstellen der VM oder des HDInsight-Clusters

Erstellen Sie einen virtuellen Computer oder einen HDInsight-Cluster mithilfe des Azure-Portals oder der Azure-Befehlszeilenschnittstelle, und platzieren Sie den Cluster in einem virtuellen Azure-Netzwerk. Weitere Informationen finden Sie in den folgenden Artikeln:
* [Erstellen und Verwalten virtueller Azure-Netzwerke für virtuelle Linux-Computer](../virtual-machines/linux/tutorial-virtual-network.md)

* [Erweitern von Azure HDInsight per Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Konfigurieren der Netzwerkports 

Damit Azure Machine Learning mit dem SSH-Port des virtuellen Computers oder des Clusters kommunizieren kann, konfigurieren Sie einen Quelleintrag für die Netzwerksicherheitsgruppe. Der SSH-Port ist in der Regel der Port 22. Gehen Sie wie folgt vor, um Datenverkehr aus dieser Quelle zuzulassen:

1. Wählen Sie in der Dropdownliste __Quelle__ den Eintrag __Diensttag__ aus.

1. Wählen Sie in der Dropdownliste __Quelldiensttag__ den Eintrag __AzureMachineLearning__ aus.

    ![Eingangsregeln für Experimente auf einem virtuellen Computer oder in einem HDInsight-Cluster innerhalb eines virtuellen Netzwerks](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. Wählen Sie in der Dropdownliste __Quellportbereiche__ den Eintrag __*__ aus.

1. Wählen in der Dropdownliste __Ziel__ die Option __Alle__ aus.

1. Wählen Sie in der Dropdownliste __Quellportbereiche__ den Eintrag __22__ aus.

1. Wählen Sie unter __Protokoll__ die Option __Alle__ aus.

1. Wählen Sie für __Aktion__ die Option __Zulassen__ aus.

Behalten Sie die Standardausgangsregeln für die Netzwerksicherheitsgruppe bei. Weitere Informationen finden Sie unter [Sicherheitsgruppen](../virtual-network/network-security-groups-overview.md#default-security-rules) bei den Standardsicherheitsregeln.

Wenn Sie die Standardausgangsregeln nicht verwenden und den ausgehenden Zugriff auf Ihr virtuelles Netzwerk beschränken möchten, lesen Sie den Abschnitt [Erforderlicher öffentlicher Internetzugriff](#required-public-internet-access).

### <a name="attach-the-vm-or-hdinsight-cluster"></a>Anfügen der VM oder des HDInsight-Clusters

Fügen Sie den virtuellen Computer oder den HDInsight-Cluster an Ihren Azure Machine Learning-Arbeitsbereich an. Weitere Informationen finden Sie unter [Einrichten von Computezielen für das Modelltraining](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel ist Teil einer Reihe zum Schützen eines Azure Machine Learning-Workflows. Sehen Sie sich auch die anderen Artikel in dieser Reihe an:

* [Virtuelle Netzwerke im Überblick](how-to-network-security-overview.md)
* [Schützen von Arbeitsbereichsressourcen](how-to-secure-workspace-vnet.md)
* [Schützen der Rückschlussumgebung](how-to-secure-inferencing-vnet.md)
* [Aktivieren von Studio-Funktionalität](how-to-enable-studio-virtual-network.md)
* [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
* [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)