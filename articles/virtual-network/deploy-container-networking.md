---
title: Bereitstellen des Containernetzwerks mit Azure Virtual Network | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das CNI-Plug-In (Container Network Interface) von Azure Virtual Network für Kubernetes-Cluster bereitstellen.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 64aa913f6c9c5041356f193dcf0c9945deb852fb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257782"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>Bereitstellen des Container Network Interface-Plug-Ins von Azure Virtual Network

Das CNI-Plug-In (Container Network Interface) von Azure Virtual Network wird auf einem virtuellen Azure-Computer installiert und bietet so Virtual Network-Funktionen für Kubernetes-Pods und Docker-Container. Weitere Informationen zu dem Plug-In finden Sie unter [Verwenden von Azure Virtual Network-Funktionen für Container](container-networking-overview.md). Zudem kann das Plug-In mit Azure Kubernetes Service (AKS) verwendet werden, und zwar durch Auswählen der Option [Erweiterte Funktionen für Netzwerkbetrieb und Skalierung](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json), über die AKS-Container automatisch in einem virtuellen Netzwerk platziert werden.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Bereitstellen des Plug-Ins für AKS-Engine-Kubernetes-Cluster

Die AKS-Engine stellt einen Kubernetes-Cluster mit einer Azure Resource Manager-Vorlage bereit. Die Clusterkonfiguration wird in einer JSON-Datei angegeben, die beim Generieren der Vorlage an das Tool übergeben wird. Weitere Informationen zur gesamten Liste der unterstützten Clustereinstellungen und deren Beschreibungen finden Sie in der [Clusterdefinition für die Azure Container Service-Engine (AKS)](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md). Das Plug-In ist das standardmäßige Netzwerk-Plug-In für Cluster, die mithilfe der AKS-Engine erstellt werden. Folgende Netzwerkkonfigurationseinstellungen sind beim Konfigurieren des Plug-Ins zu beachten:

  | Einstellung                              | BESCHREIBUNG                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | Die IP-Adresse, die dem Masterknoten zugeordnet ist. Dies ist eine erforderliche Einstellung.                                     |
  | clusterSubnet unter kubernetesConfig | CIDR des Subnetzes des virtuellen Netzwerks, in dem der Cluster bereitgestellt wird und von dem IP-Adressen zu Pods zugeordnet werden.   |
  | vnetSubnetId unter masterProfile     | Gibt die ID der Azure Resource Manager-Ressource des Subnetzes an, in dem der Cluster bereitgestellt werden soll.                    |
  | vnetCidr                             | CIDR des virtuellen Netzwerks, in dem der Cluster bereitgestellt wird.                                                             |
  | max-Pods unter kubeletConfig         | Maximale Anzahl der Pods auf jedem virtuellen Agent-Computer. Für das Plug-In ist 30 als Standardwert festgelegt. Sie können bis zu 250 angeben.  |

### <a name="example-configuration"></a>Beispielkonfiguration

Das folgende JSON-Beispiel gilt für einen Cluster mit den folgenden Eigenschaften:
-   1 Masterknoten und 2 Agent-Knoten 
-   Wird im Subnetz *KubeClusterSubnet* (10.0.0.0/20) bereitgestellt, in dem sich der Masterknoten und die Agent-Knoten befinden.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Bereitstellen des Plug-Ins für einen Kubernetes-Cluster

Führen Sie die folgenden Schritte aus, um das Plug-In auf allen virtuellen Azure-Computern in einem Kubernetes-Cluster zu installieren:

1. [Laden Sie das Plug-In herunter, und installieren Sie es](#download-and-install-the-plug-in).
2. Ordnen Sie auf jedem virtuellen Computer, über den Pods IP-Adressen zugewiesen werden, vorab einen IP-Adressenpool des virtuellen Netzwerks zu. Alle virtuellen Azure-Computer umfassen eine private IP-Adresse des primären virtuellen Netzwerks für jede Netzwerkschnittstelle. Der Pool von IP-Adressen für Pods wird mithilfe einer der folgenden Optionen als sekundäre Adressen (*ipconfigs*) für die Netzwerkschnittstelle des virtuellen Computers hinzugefügt:

   - **CLI:**[Zuweisen von mehreren IP-Adressen über die Azure-Befehlszeilenschnittstelle](./ip-services/virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell:**[Zuweisen von mehreren IP-Adressen mithilfe von PowerShell](./ip-services/virtual-network-multiple-ip-addresses-powershell.md)
   - **Portal:**[Zuweisen von mehreren IP-Adressen über das Azure-Portal](./ip-services/virtual-network-multiple-ip-addresses-portal.md)
   - **Azure Resource Manager-Vorlage:**[Zuweisen von mehreren IP-Adressen mithilfe von Vorlagen](./template-samples.md)

   Stellen Sie sicher, dass Sie genügend IP-Adressen für alle Pods hinzufügen, die auf dem virtuellen Computer angezeigt werden sollen.

3. Wählen Sie das Plug-In für die Bereitstellung von Netzwerkfunktionen für den Cluster aus, indem Sie bei der Clustererstellung in Kubelet die Befehlszeilenoption `–network-plugin=cni` übergeben. In Kubernetes werden das Plug-In und die Konfigurationsdatei standardmäßig in den Verzeichnissen gesucht, in denen sie bereits installiert sind.
4. Wenn die Pods auf das Internet zugreifen können sollen, fügen Sie auf Ihren virtuellen Linux-Computern die folgende *iptables*-Regel hinzu, sodass eine Netzwerkadressenübersetzung für die Quelle des Internetdatenverkehrs durchgeführt wird. Im folgenden Beispiel wurde als IP-Adressbereich 10.0.0.0/8 angegeben.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   Mit den Regeln wird für Datenverkehr, der nicht für die angegebenen IP-Adressbereiche bestimmt ist, eine Netzwerkadressenübersetzung durchgeführt. Es wird davon ausgegangen, dass es sich bei dem gesamten Datenverkehr außerhalb der vorherigen Bereiche um Internetdatenverkehr handelt. Sie können die IP-Adressbereiche des virtuellen Netzwerks der virtuellen Computer, der mittels Peering verknüpften virtuellen Netzwerke oder der lokalen Netzwerke angeben.

   Virtuelle Windows-Computer führen automatisch eine Netzwerkadressenübersetzung für die Quelle des Datenverkehrs mit einem Ziel außerhalb des Subnetzes, in dem sich der virtuelle Computer befindet, durch. Es ist nicht möglich, benutzerdefinierte IP-Adressbereiche anzugeben.

Nach Abschluss der vorherigen Schritte werden den Pods, die auf den virtuellen Kubernetes-Agent-Computern angezeigt werden, automatisch private IP-Adressen aus dem virtuellen Netzwerk zugewiesen.

## <a name="deploy-plug-in-for-docker-containers"></a>Bereitstellen des Plug-Ins für Docker-Container

1. [Laden Sie das Plug-In herunter, und installieren Sie es](#download-and-install-the-plug-in).
2. Erstellen Sie mit dem folgenden Befehl Docker-Container:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Die Container erhalten automatisch IP-Adressen aus dem zugeordneten Pool. Wenn Sie einen Lastenausgleich für Datenverkehr in die Docker-Container durchführen möchten, müssen diese hinter einem Software-Load Balancer platziert werden, und Sie müssen einen Lastenausgleichstest konfigurieren. Dies erfolgt in gleicher Weise wie die Erstellung einer Richtlinie und von Tests für einen virtuellen Computer.

### <a name="cni-network-configuration-file"></a>CNI-Netzwerkkonfigurationsdatei

Die CNI-Netzwerkkonfigurationsdatei wird im JSON-Format beschrieben. Sie ist unter Linux standardmäßig in `/etc/cni/net.d` und für Windows in `c:\cni\netconf` vorhanden. Die Datei gibt die Konfiguration des Plug-Ins an und unterscheidet sich für Windows und Linux. Der folgende JSON-Code ist ein Beispiel für eine Linux-Konfigurationsdatei. Darauf folgt eine Erläuterung einiger wichtiger Einstellungen. An dieser Datei sind keine Änderungen erforderlich.

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Erläuterung der Einstellungen

- **cniVersion:** Das CNI-Plug-In von Azure Virtual Network unterstützt Version 0.3.0 und 0.3.1 der [CNI-Spezifikation](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name:** Name des Netzwerks. Diese Eigenschaft kann auf einen beliebigen eindeutigen Wert festgelegt werden.
- **Typ**: Name des Netzwerk-Plug-Ins. Legen Sie diese Eigenschaft auf *azure-vnet* fest.
- **mode**: Betriebsmodus. Dieses Feld ist optional. Es wird lediglich der Modus „bridge“ unterstützt. Weitere Informationen finden Sie unter [Operational Modes](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md) (Betriebsmodi).
- **bridge**: Name der Brücke, über die Container mit einem virtuellen Netzwerk verbunden werden. Dieses Feld ist optional. Wenn kein Wert angegeben ist, wählt das Plug-In basierend auf dem Index der Masterschnittstelle automatisch einen eindeutigen Namen aus.
- **ipam type**: Name des IPAM-Plug-Ins. Legen Sie diese Eigenschaft immer auf *azure-vnet-ipam* fest.

## <a name="download-and-install-the-plug-in"></a>Herunterladen und Installieren des Plug-Ins

Laden Sie das Plug-In von [GitHub](https://github.com/Azure/azure-container-networking/releases) herunter. Laden Sie die neueste Version für die verwendete Plattform herunter:

- **Linux**: [azure-vnet-cni-linux-amd64-\<version no.\>.tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [azure-vnet-cni-windows-amd64-\<version no.\>.zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Kopieren Sie das Installationsskript für [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) oder [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) auf Ihren Computer. Speichern Sie das Skript in einem Verzeichnis mit dem Namen `scripts` auf dem Computer, und geben Sie der Datei unter Linux den Namen `install-cni-plugin.sh` und unter Windows den Namen `install-cni-plugin.ps1`. Führen Sie zum Installieren des Plug-Ins das der verwendeten Plattform entsprechende Skript aus, und geben Sie dabei die Version des verwendeten Plug-Ins an. Sie können beispielsweise *v1.0.12-rc3* angeben:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

Das Skript installiert das Plug-In unter `/opt/cni/bin` (Linux) oder `c:\cni\bin` (Windows). Das installierte Plug-In umfasst eine einfache Netzwerkkonfigurationsdatei, die nach der Installation verwendet werden kann. Sie muss nicht aktualisiert werden. Weitere Informationen zu den Einstellungen in der Datei finden Sie unter [CNI-Netzwerkkonfigurationsdatei](#cni-network-configuration-file).