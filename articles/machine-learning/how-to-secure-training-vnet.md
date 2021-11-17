---
title: Schützen von Trainingsumgebungen mit virtuellen Netzwerken
titleSuffix: Azure Machine Learning
description: Verwenden Sie eine isolierte Azure Virtual Network-Instanz, um Ihre Azure Machine Learning-Trainingsumgebung zu schützen.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 11/05/2021
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1, references_regions
ms.openlocfilehash: ec3a5feefa41a1bd0bb0448321fc87f7e11a55d9
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132136294"
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

    - „Microsoft.Network/virtualNetworks/*/read“ für die VNET-Ressource Dies ist für Azure Resource Manager (ARM)-Vorlagenbereitstellungen nicht erforderlich.
    - „Microsoft.Network/virtualNetworks/subnet/join/action“ auf der Subnetzressource

    Weitere Informationen zur rollenbasierten Zugriffssteuerung von Azure in Netzwerken finden Sie unter [Integrierte Netzwerkrollen](../role-based-access-control/built-in-roles.md#networking).

### <a name="azure-machine-learning-compute-clusterinstance"></a>Azure Machine Learning-Computecluster/-Instanz

* Das virtuelle Netzwerk muss sich im selben Abonnement befinden wie der Azure Machine Learning-Arbeitsbereich.
* Das für die Compute-Instanz oder den Computecluster verwendete Subnetz muss über ausreichend nicht zugewiesene IP-Adressen verfügen.

    * Ein Computecluster kann dynamisch skaliert werden. Wenn nicht ausreichend freie IP-Adressen vorhanden sind, wird der Cluster teilweise zugeordnet.
    * Eine Compute-Instanz erfordert nur eine IP-Adresse.

* Um einen Computecluster oder eine Compute-Instanz [ohne öffentliche IP-Adresse](#no-public-ip) zu erstellen (eine Previewfunktion), muss Ihr Arbeitsbereich einen privaten Endpunkt für die Verbindung mit dem VNet verwenden. Weitere Informationen finden Sie unter [„Konfigurieren eines privaten Endpunkts für Azure Machine Learning Workspace“](how-to-configure-private-link.md).
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


        > [!TIP]
        > Wenn Ihr Computecluster oder Ihre Compute-Instanz keine öffentliche IP-Adresse verwendet (eine Previewfunktion), sind diese eingehenden NSG-Regeln nicht erforderlich. 
        
    * Bei Computeclustern oder Compute-Instanzen ist es jetzt möglich, die öffentliche IP-Adresse zu entfernen (eine Previewfunktion). Wenn Sie Azure-Richtlinienzuweisungen haben, die die Erstellung einer öffentlichen IP verbieten, wird die Bereitstellung des Computeclusters oder der Compute-Instanz erfolgreich sein.

    * Ein Lastenausgleichsmodul

    Bei Computeclustern werden diese Ressourcen bei jedem Herunterskalieren des Clusters auf 0 Knoten gelöscht und beim Hochskalieren erstellt.

    Bei einer Compute-Instanz werden diese Ressourcen beibehalten, bis die Instanz gelöscht wird. Durch das Beenden der Instanz werden die Ressourcen nicht entfernt. 

    > [!IMPORTANT]
    > Diese Ressourcen werden durch die [Ressourcenkontingente](../azure-resource-manager/management/azure-subscription-service-limits.md) des Abonnements beschränkt. Ist die Ressourcengruppe des virtuellen Netzwerks gesperrt, kann der Computecluster/die Compute-Instanz nicht erfolgreich gelöscht werden. Der Lastenausgleich kann erst nach dem Löschen des Computeclusters/der Compute-Instanz gelöscht werden. Stellen Sie außerdem sicher, dass keine Azure-Richtlinienzuweisungen vorhanden sind, die die Erstellung von Netzwerksicherheitsgruppen verhindern.

* Wenn sich Azure Storage-Konten für den Arbeitsbereich auch im virtuellen Netzwerk befinden, verwenden Sie die folgende Anleitung zu Subnetzeinschränkungen:

    * Wenn Sie Azure Machine Learning __Studio__ zur Visualisierung von Daten oder zur Verwendung von Designer verwenden möchten, muss sich das Speicherkonto __im selben Subnetz wie die Compute-Instanz oder der Cluster befinden__.
    * Wenn Sie planen, das __SDK__ zu verwenden, kann sich das Speicherkonto in einem anderen Subnetz befinden.

    > [!NOTE]
    > Das Hinzufügen einer Ressourceninstanz für Ihren Arbeitsbereich oder das Aktivieren des Kontrollkästchens „Hiermit erlauben Sie vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Konto.“ reicht nicht aus, um die ausgehende Kommunikation der Computeinstanz zuzulassen.

* Wenn Ihr Arbeitsbereich einen privaten Endpunkt verwendet, ist der Zugriff auf die Compute-Instanz nur innerhalb des virtuellen Netzwerks möglich. Wenn Sie eine benutzerdefinierte DNS- oder Hostsdatei verwenden, fügen Sie einen Eintrag für `<instance-name>.<region>.instances.azureml.ms` hinzu. Ordnen Sie diesen Eintrag der privaten IP-Adresse des privaten Endpunkts des Arbeitsbereichs zu. Weitere Informationen finden Sie im Artikel [Benutzerdefiniertes DNS](./how-to-custom-dns.md).
* VNet-Dienstendpunktrichtlinien funktionieren für Systemspeicherkonten des Computeclusters/der Compute-Instanz nicht.
* Wenn sich Speicher- und Compute-Instanzen in unterschiedlichen Regionen befinden, können zeitweilig Timeouts auftreten.
* Wenn die Azure Container Registry für Ihren Arbeitsbereich einen privaten Endpunkt zur Verbindung mit dem virtuellen Netzwerk verwendet, können Sie keine verwaltete Identität für die Compute-Instanz verwenden. Um eine verwaltete Identität mit der Compute-Instanz zu verwenden, darf die Container-Registrierung nicht in das VNet aufgenommen werden.
* Wenn Sie Jupyter Notebooks auf einer Compute-Instanz verwenden möchten:

    * Deaktivieren Sie die WebSocket-Kommunikation nicht. Vergewissern Sie sich, dass Ihr Netzwerk die Websocket-Kommunikation mit `*.instances.azureml.net` und `*.instances.azureml.ms` gestattet.
    * Vergewissern Sie sich, dass Ihr Notebook auf einer Computeressource ausgeführt wird, die sich hinter demselben virtuellen Netzwerk und Subnetz wie Ihre Daten befindet. Verwenden Sie beim Erstellen der Compute-Instanz die Option **Erweiterte Einstellungen** > **Virtuelles Netzwerk konfigurieren**, um das Netzwerk und das Subnetz auszuwählen.

* __Computecluster__ können in einer anderen Region als der Ihres Arbeitsbereichs erstellt werden. Diese Funktionalität befindet sich in der __Vorschau__ und ist nur für __Computecluster__ und nicht für Compute-Instanzen verfügbar. Wenn Sie eine andere Region für den Cluster verwenden, gelten die folgenden Einschränkungen:

    * Wenn sich Ihre mit dem Arbeitsbereich verbundenen Ressourcen, z. B. Speicher, in einem anderen virtuellen Netzwerk befinden als der Cluster, richten Sie ein globales Peering virtueller Netzwerke zwischen den Netzwerken ein. Weitere Informationen finden Sie unter [Peering in virtuellen Netzwerken](../virtual-network/virtual-network-peering-overview.md).
    * Es kann sein, dass sich die Netzwerklatenz und die Datenübertragungskosten erhöhen. Latenz und Kosten können beim Erstellen des Clusters und beim Anwenden von Aufträgen auf den Cluster anfallen.

    Anleitungen wie die Verwendung von NSG-Regeln, benutzerdefinierten Routen und Eingabe-/Ausgabeanforderungen werden ganz normal angewendet, wenn eine andere Region als für den Arbeitsbereich verwendet wird.

    > [!WARNING]
    > Wenn Sie einen privaten, __endpunktfähigen Arbeitsbereich__ verwenden, wird die Erstellung des Clusters in einer anderen Region __nicht unterstützt__.

### <a name="azure-databricks"></a>Azure Databricks

* Zusätzlich zu den von Azure Databricks verwendeten Subnetzen __databricks-private__ und __databricks-public__ ist auch das für das virtuelle Netzwerk erstellte __standardmäßige__ Subnetz erforderlich.
* Azure Databricks verwendet keinen privaten Endpunkt zur Kommunikation mit dem virtuellen Netzwerk.

Weitere Informationen zur Verwendung von Azure Databricks mit einem virtuellen Netzwerk finden Sie unter [Bereitstellen von Azure Databricks in Ihrem Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

### <a name="azure-hdinsight-or-virtual-machine"></a>Azure HDInsight oder virtueller Computer

* Azure Machine Learning unterstützt nur virtuelle Computer, auf denen Ubuntu ausgeführt wird.

## <a name="required-public-internet-access"></a>Erforderlicher öffentlicher Internetzugriff

[!INCLUDE [machine-learning-required-public-internet-access](../../includes/machine-learning-public-internet-access.md)]

Informationen zur Verwendung einer Firewalllösung finden Sie unter [Verwenden einer Firewall mit Azure Machine Learning](how-to-access-azureml-behind-firewall.md).

## <a name="compute-clusters"></a><a name="compute-cluster"></a>Compute-Cluster

Verwenden Sie die folgenden Registerkarten, um auszuwählen, wie Sie einen Computecluster erstellen möchten:

# <a name="studio"></a>[Studio](#tab/azure-studio)

Verwenden Sie die folgenden Schritte, um einen Computecluster im Azure Machine Learning Studio zu erstellen:

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com/) an, und wählen Sie dann Ihr Abonnement und den Arbeitsbereich aus.
1. Wählen Sie auf der linken Seite __Compute__, in der Mitte __Computecluster__ und dann __+ Neu__ aus.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster.png" alt-text="Screenshot: Erstellen eines Clusters":::

1. Wählen Sie im Dialogfeld __Computecluster erstellen__ die VM-Größe und -Konfiguration aus, die Sie benötigen, und wählen Sie dann __Weiter__ aus.

    :::image type="content" source="./media/how-to-enable-virtual-network/create-compute-cluster-vm.png" alt-text="Screenshot: Festlegen der VM-Konfiguration":::

1. Legen Sie im Abschnitt __Einstellungen konfigurieren__ den __Computenamen__, das __virtuelle Netzwerk__ und das __Subnetz__ fest.

    :::image type="content" source="media/how-to-enable-virtual-network/create-compute-cluster-config.png" alt-text="Der Screenshot zeigt die Einstellung des Compute-Namens, des virtuellen Netzwerks und des Subnetzes.":::

    > [!TIP]
    > Wenn Ihr Arbeitsbereich einen privaten Endpunkt verwendet, um eine Verbindung mit dem virtuellen Netzwerk herzustellen, wird das Auswahlfeld __Virtuelles Netzwerk__ abgeblendet angezeigt.
    > 

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

### <a name="no-public-ip-for-compute-clusters-preview"></a><a name="no-public-ip-amlcompute"></a>Keine öffentliche IP-Adresse für Computecluster (Vorschau)

Wenn Sie **keine öffentliche IP** aktivieren, verwendet Ihr Computecluster keine öffentliche IP für die Kommunikation mit anderen Abhängigkeiten. Stattdessen kommuniziert sie ausschließlich innerhalb des virtuellen Netzwerks über das Azure Private Link-Ökosystem sowie über Service-/Privat-Endpunkte, wodurch die Notwendigkeit einer öffentlichen IP vollständig entfällt. Keine öffentliche IP-Adresse verhindert den Zugang und die Auffindbarkeit von Computeclusterknoten aus dem Internet und eliminiert damit einen wichtigen Bedrohungsvektor. Cluster **ohne öffentliche IP-Adresse** helfen bei der Einhaltung von Richtlinien für öffentliche IP-Adressen, über die viele Unternehmen verfügen. 

Ein Computecluster, bei dem **keine öffentliche IP** aktiviert ist, hat im Vergleich zu einem Computecluster mit öffentlicher IP **keine Anforderungen an die eingehende Kommunikation** aus dem öffentlichen Internet. Insbesondere ist keine der beiden NSG-Regeln für den Eingang (`BatchNodeManagement`, `AzureMachineLearning`) erforderlich. Sie müssen den Eingang von der **VirtualNetwork**-Quelle und beliebigen Portquellen zum **VirtualNetwork**-Ziel und dem Zielport **29876, 29877** zulassen.

**Keine öffentliche IP**-Cluster sind von [Azure Private Link](how-to-configure-private-link.md) für Azure Machine Learning Workspace abhängig. Für einen Computecluster mit **keiner öffentlichen IP** müssen Sie außerdem private Endpunkt-Netzwerkrichtlinien und private Link-Service-Netzwerkrichtlinien deaktivieren. Diese Anforderungen stammen von Azure Private Link Service und Private Endpoints und sind nicht Azure Machine Learning-spezifisch. Befolgen Sie die Anweisungen unter [Deaktivieren der Netzwerkrichtlinien für den Private Link-Dienst](../private-link/disable-private-link-service-network-policy.md), um die Parameter `disable-private-endpoint-network-policies` und `disable-private-link-service-network-policies` für das Subnetz des virtuellen Netzwerks festzulegen.

Damit **ausgehende Verbindungen** funktionieren, müssen Sie eine Egress-Firewall wie die Azure-Firewall mit benutzerdefinierten Routen einrichten. Sie können beispielsweise eine Firewall mit [ein- und ausgehender Konfiguration](how-to-access-azureml-behind-firewall.md) verwenden und den Datenverkehr dorthin leiten, indem Sie eine Routentabelle in dem Teilnetz definieren, in dem der Computecluster bereitgestellt wird. Der Eintrag in der Routentabelle kann den nächsten Hop der privaten IP-Adresse der Firewall mit dem Adresspräfix 0.0.0.0/0 einrichten.

Sie können einen Dienstendpunkt oder privaten Endpunkt für Ihre Azure Container Registry und Azure Storage in dem Subnetz verwenden, in dem der Cluster bereitgestellt wird.

Um einen Computecluster ohne öffentliche IP-Adresse (eine Previewfunktion) in Studio zu erstellen, aktivieren Sie das Kontrollkästchen **Keine öffentliche IP** im Abschnitt „Virtuelles Netzwerk“.
Sie können einen Computecluster ohne öffentliche IP-Adresse auch über eine ARM-Vorlage erstellen. Setzen Sie in der ARM-Vorlage den Parameter „enableNodePublicIP“ auf „false“.

[!INCLUDE [no-public-ip-info](../../includes/machine-learning-no-public-ip-availibility.md)]

**Problembehandlung**

* Wenn Sie bei der Erstellung eines Clusters die Fehlermeldung „Im angegebenen Subnetz ist PrivateLinkServiceNetworkPolicies oder PrivateEndpointNetworkEndpoints aktiviert“ erhalten, befolgen Sie bitte die Anweisungen unter [Deaktivieren von Netzwerkrichtlinien für den Private Link-Dienst](../private-link/disable-private-link-service-network-policy.md) und [Deaktivieren von Netzwerkrichtlinien für den privaten Endpunkt](../private-link/disable-private-endpoint-network-policy.md).

* Wenn bei der Ausführung des Auftrags aufgrund von Verbindungsproblemen mit ACR oder Azure Storage ein Fehler auftritt, überprüfen Sie, ob der Kunde ACR- und Azure Storage-Dienstendpunkte/private Endpunkte zum Subnetz hinzugefügt hat und ob ACR/Azure Storage den Zugriff über das Subnetz zulässt.

* Um sicherzustellen, dass Sie einen Cluster ohne öffentliche IP-Adresse erstellt haben, sehen Sie in Studio bei den Clusterdetails, dass die Eigenschaft **Keine öffentliche IP** unter den Ressourceneigenschaften auf **true** festgelegt ist.

## <a name="compute-instance"></a>Compute-Instanz

Schritte zum Erstellen einer Compute-Instanz, die in einem virtuellen Netzwerk bereitgestellt wird, finden Sie unter [„Erstellen und Verwalten einer Azure Machine Learning-Recheninstanz“](how-to-create-manage-compute-instance.md).

### <a name="no-public-ip-for-compute-instances-preview"></a><a name="no-public-ip"></a>Keine öffentliche IP für Compute-Instanzen (Vorschau)

Wenn Sie **keine öffentliche IP** aktivieren, verwendet Ihre Compute-Instanz keine öffentliche IP für die Kommunikation mit anderen Abhängigkeiten. Stattdessen kommuniziert sie ausschließlich innerhalb des virtuellen Netzwerks über das Azure Private Link-Ökosystem sowie über Service-/Privat-Endpunkte, wodurch die Notwendigkeit einer öffentlichen IP vollständig entfällt. Keine öffentliche IP-Adresse verhindert den Zugang und die Auffindbarkeit des Knotenpunkts der Compute-Instanz aus dem Internet und eliminiert damit einen wichtigen Bedrohungsvektor. Compute-Instanzen führen auch eine Paketfilterung durch, um jeglichen Verkehr von außerhalb des virtuellen Netzwerks abzuweisen. **Keine öffentlichen IP-** Instanzen sind von [Azure Private Link](how-to-configure-private-link.md) für Azure Machine Learning Workspace abhängig. 

Damit **ausgehende Verbindungen** funktionieren, müssen Sie eine Egress-Firewall wie die Azure-Firewall mit benutzerdefinierten Routen einrichten. Sie können beispielsweise eine Firewall mit [ein- und ausgehender Konfiguration](how-to-access-azureml-behind-firewall.md) verwenden und den Datenverkehr dorthin leiten, indem Sie eine Routentabelle in dem Teilnetz definieren, in dem die Compute-Instanz bereitgestellt wird. Der Eintrag in der Routentabelle kann den nächsten Hop der privaten IP-Adresse der Firewall mit dem Adresspräfix 0.0.0.0/0 einrichten.

Eine Compute-Instanz, bei der **keine öffentliche IP** aktiviert ist, hat im Vergleich zu einer Compute-Instanz mit öffentlicher IP **keine Anforderungen an die eingehende Kommunikation** aus dem öffentlichen Internet. Insbesondere ist keine der beiden NSG-Regeln für den Eingang (`BatchNodeManagement`, `AzureMachineLearning`) erforderlich. Sie müssen den Eingang von der **VirtualNetwork**-Quelle, beliebigen Portquellen, vom **VirtualNetwork**-Ziel und von den Zielports **29876, 29877 und 44224** zulassen.

Für eine Compute-Instanz mit **keiner öffentlichen IP** müssen Sie außerdem private Endpunkt-Netzwerkrichtlinien und private Link-Service-Netzwerkrichtlinien deaktivieren. Diese Anforderungen stammen von Azure Private Link Service und Private Endpoints und sind nicht Azure Machine Learning-spezifisch. Befolgen Sie die Anweisungen unter [„Deaktivieren der Netzwerkrichtlinien für die Quell-IP des Private Link-Dienstes“](../private-link/disable-private-link-service-network-policy.md), um die Parameter`disable-private-endpoint-network-policies` und `disable-private-link-service-network-policies`das virtuelle Netzwerk-Subnetz festzulegen.

Um eine Compute-Instanz ohne öffentliche IP-Adresse (eine Vorschaufunktion) in Studio zu erstellen, aktivieren Sie das Kontrollkästchen **keine öffentliche IP** im Abschnitt „Virtuelles Netzwerk“.
Sie können auch keine öffentliche IP-Compute-Instanz über eine ARM-Vorlage erstellen. Setzen Sie in der ARM-Vorlage den Parameter „enableNodePublicIP“ auf „false“.

Nächste Schritte:
* [Verwenden von benutzerdefiniertem DNS](how-to-custom-dns.md)
* [Verwenden einer Firewall](how-to-access-azureml-behind-firewall.md)

[!INCLUDE [no-public-ip-info](../../includes/machine-learning-no-public-ip-availibility.md)]

## <a name="inbound-traffic"></a>Eingehender Datenverkehr

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
