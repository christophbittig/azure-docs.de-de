---
title: Cloud Shell in einem Azure Virtual Network
description: Bereitstellen von Cloud Shell in einem Azure Virtual Network
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ce108b7f36b7f7d4caf5133aafe99eb837df1ce
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346895"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Bereitstellen von Cloud Shell in einem Azure Virtual Network

Eine reguläre Cloud Shell-Sitzung wird in einem Container in einem Microsoft-Netzwerk separat von Ihren Ressourcen ausgeführt. Dies bedeutet, dass Befehle, die innerhalb des Containers ausgeführt werden, nicht auf Ressourcen zugreifen können, auf die nur von einem spezifischen virtuellen Netzwerk aus zugegriffen werden kann. Beispielsweise können Sie SSH nicht verwenden, um eine Verbindung von Cloud Shell aus mit einem virtuellen Computer herzustellen, der nur über eine private IP-Adresse verfügt, oder Sie können nicht mithilfe von kubectl eine Verbindung mit einem Kubernetes-Cluster herstellen, der den Zugriff gesperrt hat. 

Diese optionale Funktion behebt diese Einschränkungen und ermöglicht es Ihnen, Cloud Shell in einem virtuellen Azure-Netzwerk bereitzustellen, das Sie kontrollieren. Von dort aus kann der Container mit Ressourcen innerhalb des virtuellen Netzwerks interagieren, das Sie auswählen.  

Im Folgenden sehen Sie die Ressourcenarchitektur, die in diesem Szenario bereitgestellt und verwendet wird.

![Veranschaulicht die von der Cloud Shell isolierte VNET-Architektur.](media/private-vnet/data-diagram.png)

Bevor Sie Cloud Shell in Ihrem eigenen Azure Virtual Network verwenden können, müssen Sie mehrere Ressourcen erstellen, um diese Funktionalität zu unterstützen. In diesem Artikel wird gezeigt, wie die erforderlichen Ressourcen mithilfe einer ARM-Vorlage eingerichtet werden.

> [!NOTE]
> Diese Ressourcen müssen nur einmal für das virtuelle Netzwerk eingerichtet werden. Sie können dann von allen Administratoren mit Zugriff auf das virtuelle Netzwerk gemeinsam genutzt werden.

## <a name="required-network-resources"></a>Erforderliche Netzwerkressourcen

### <a name="virtual-network"></a>Virtuelles Netzwerk
Ein virtuelles Netzwerk definiert den Adressraum, in dem mindestens ein Subnetz erstellt wird.

Das gewünschte virtuelle Netzwerk, das für die Cloud Shell verwendet werden soll, muss identifiziert werden. Dabei handelt es sich in der Regel um ein vorhandenes virtuelles Netzwerk, das Ressourcen enthält, die Sie verwalten möchten, oder um ein Netzwerk, das sich mit Netzwerken koppelt, die Ihre Ressourcen enthalten.

### <a name="subnet"></a>Subnet
In dem ausgewählten virtuellen Netzwerk muss ein dediziertes Subnetz für Cloud Shell-Container verwendet werden. Dieses Subnetz wird an den Azure Container Instances-Dienst (ACI) delegiert.  Wenn ein Benutzer einen Cloud Shell-Container in einem virtuellen Netzwerk anfordert, verwendet Cloud Shell ACI, um einen Container zu erstellen, der sich in diesem delegierten Subnetz befindet.  In diesem Subnetz können keine anderen Ressourcen erstellt werden.

### <a name="network-profile"></a>Netzwerkprofil
Ein Netzwerkprofil ist eine Netzwerkkonfigurationsvorlage für Azure-Ressourcen, die bestimmte Netzwerkeigenschaften für die Ressource angibt.

### <a name="azure-relay"></a>Azure Relay
Ein [Azure Relay](../azure-relay/relay-what-is-it.md) ermöglicht zwei Endpunkten, die nicht direkt erreichbar sind, die Kommunikation. In diesem Fall wird es verwendet, um dem Browser des Administrators die Kommunikation mit dem Container in dem privaten Netzwerk zu gestatten.

Die für Cloud Shell verwendete Azure Relay-Instanz kann so konfiguriert werden, dass sie kontrolliert, welche Netzwerke auf Containerressourcen zugreifen können: 
- Zugänglich aus dem öffentlichen Internet: In dieser Konfiguration bietet Cloud Shell eine Möglichkeit, andernfalls interne Ressourcen von außerhalb zu erreichen. 
- Zugänglich aus angegebenen Netzwerken: In dieser Konfiguration müssen Administratoren von einem Computer aus auf das Azure-Portal zugreifen, der im entsprechenden Netzwerk ausgeführt wird, um Cloud Shell verwenden zu können.

## <a name="storage-requirements"></a>Speicheranforderungen
Wie bei der standardmäßigen Cloud Shell ist ein Speicherkonto erforderlich, wenn Cloud Shell in einem virtuellen Netzwerk verwendet wird. Jeder Administrator benötigt eine Dateifreigabe, um seine Dateien zu speichern.  Das Speicherkonto muss aus dem virtuellen Netzwerk zugänglich sein, das von Cloud Shell verwendet wird. 

## <a name="virtual-network-deployment-limitations"></a>Einschränkungen für die Bereitstellung in einem virtuellen Netzwerk
* Aufgrund der beteiligten zusätzlichen Netzwerkressourcen ist das Starten von Cloud Shell in einem virtuellen Netzwerk in der Regel langsamer als eine Cloud Shell-Standardsitzung.

* Alle Cloud Shell-Regionen außer „Indien, Mitte“ werden zurzeit unterstützt. 

* [Azure Relay](../azure-relay/relay-what-is-it.md) ist kein kostenloser Dienst. Sehen Sie sich daher die zugehörigen [Preis](https://azure.microsoft.com/pricing/details/service-bus/) an. Im Cloud Shell-Szenario wird für jeden Administrator eine Hybridverbindung verwendet, während sie Cloud Shell verwenden. Die Verbindung wird automatisch heruntergefahren, nachdem die Cloud Shell-Sitzung beendet wurde.

## <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

Der Ressourcenanbieter „Microsoft.ContainerInstances“ muss in dem Abonnement registriert sein, das das virtuelle Netzwerk enthält, das Sie verwenden möchten. Wählen Sie das entsprechende Abonnement mit `Set-AzContext -Subscription {subscriptionName}` aus, und führen Sie dann Folgendes aus:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

Wenn **RegistrationState** gleich `Registered` ist, ist keine Aktion erforderlich. Ist der Zustand `NotRegistered`, führen Sie `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` aus. 

## <a name="deploy-network-resources"></a>Bereitstellen von Netzwerkressourcen
 
### <a name="create-a-resource-group-and-virtual-network"></a>Erstellen einer Ressourcengruppe und eines virtuellen Netzwerks
Wenn Sie bereits über ein gewünschtes VNET verfügen, das Sie verbinden möchten, fahren Sie mit dem nächsten Abschnitt fort.

Erstellen Sie im Azure-Portal oder mithilfe von Azure CLI, Azure PowerShell usw. eine Ressourcengruppe und ein virtuelles Netzwerk in der neuen Ressourcengruppe, wobei sich **die Ressourcengruppe und das virtuelle Netzwerk in derselben Region befinden müssen**.

### <a name="arm-templates"></a>ARM-Vorlagen
Nutzen Sie die [Azure-Schnellstartvorlage](https://aka.ms/cloudshell/docs/vnet/template) zum Erstellen von Cloud Shell-Ressourcen in einem virtuellen Netzwerk und die [Azure-Schnellstartvorlage](https://azure.microsoft.com/resources/templates/cloud-shell-vnet-storage/), um den erforderlichen Speicher zu erstellen. Notieren Sie sich Ihre Ressourcennamen, insbesondere den Namen Ihrer Dateifreigabe.

### <a name="open-relay-firewall"></a>Öffnen der Relayfirewall
Navigieren Sie zu dem Relay, das Sie mithilfe der obigen Vorlage erstellt haben, wählen Sie in den Einstellungen „Netzwerk“ aus, und lassen Sie den Zugriff von Ihrem Browsernetzwerk auf das Relay zu. Standardmäßig ist das Relay nur über das virtuelle Netzwerk zugänglich, in dem es erstellt wurde. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Konfigurieren der Cloud Shell für die Verwendung eines virtuellen Netzwerks
> [!NOTE]
> Dieser Schritt muss für jeden Administrator durchgeführt werden, der Cloud Shell verwenden wird.

Nachdem Sie die obigen Schritte abgeschlossen haben, navigieren Sie im Azure-Portal oder auf https://shell.azure.com zu Cloud Shell. Eine dieser Umgebungen muss jedes Mal verwendet werden, wenn Sie eine Verbindung mit einer isolierten Cloud Shell-Umgebung herstellen möchten.

> [!NOTE]
> Wenn Cloud Shell in der Vergangenheit verwendet wurde, muss die Einbindung des vorhandenen clouddrive aufgehoben werden. Führen Sie hierzu aus einer aktiven Cloud Shell-Sitzung `clouddrive unmount` aus, und aktualisieren Sie Ihre Seite.

Stellen Sie eine Verbindung mit Cloud Shell her, und Ihnen wird die First Run Experience (Eindruck beim ersten Ausführen) präsentiert. Wählen Sie Ihre bevorzugte Shell aus, wählen Sie „Erweiterte Einstellungen anzeigen“ aus, und aktivieren Sie das Kontrollkästchen „VNET-Isolationseinstellungen anzeigen“. Füllen Sie die Felder im Popup aus.  Die meisten Felder werden automatisch mit den verfügbaren Ressourcen ausgefüllt, die Cloud Shell in einem virtuellen Netzwerk zugeordnet werden können.  Der Name der Dateifreigabe muss vom Benutzer eingetragen werden.


![Veranschaulicht die Einstellungen für die von der Cloud Shell isolierte Ersterfahrung des VNETs.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie mehr über Azure Virtual Networks](../virtual-network/virtual-networks-overview.md)
