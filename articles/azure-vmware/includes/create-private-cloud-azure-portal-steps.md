---
title: Erstellen einer privaten Cloud von Azure VMware Solution
description: Hier erfahren Sie mehr über die Schritte zum Erstellen einer privaten Azure VMware Solution-Cloud über das Azure-Portal.
ms.topic: include
ms.date: 08/05/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: beae1ba1fe5cb37f9c96d411ca9ee0e8cddfe1d3
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122261693"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Sie können eine private Azure VMware Solution-Cloud über das Azure-Portal oder mithilfe der Azure-Befehlszeilenschnittstelle erstellen.


### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie **Neue Ressource erstellen** aus. 

1. Geben Sie im Textfeld **Marketplace durchsuchen** den Text `Azure VMware Solution` ein, und wählen Sie den Eintrag in der Liste aus. 

1. Wählen Sie im Fenster **Azure VMware Solution** die Option **Erstellen** aus.

1. Geben Sie auf der Registerkarte **Grundlagen** Werte für die Felder ein, und wählen Sie anschließend **Überprüfen und erstellen** aus. 

   >[!TIP]
   >Sie haben diese Informationen während der [Planungsphase](../plan-private-cloud-deployment.md) dieser Schnellstartanleitung gesammelt.

   | Feld   | Wert  |
   | ---| --- |
   | **Abonnement** | Wählen Sie das Abonnement aus, das Sie für die Bereitstellung verwenden möchten. Alle Ressourcen in einem Azure-Abonnement werden gemeinsam abgerechnet.|
   | **Ressourcengruppe** | Wählen Sie die Ressourcengruppe für Ihre private Cloud aus. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Alternativ können Sie eine neue Ressourcengruppe für Ihre private Cloud erstellen. |
   | **Ressourcenname** | Geben Sie den Namen Ihrer privaten Azure VMware Solution-Cloud an. |
   | **Location** | Wählen Sie einen Standort aus (beispielsweise **USA, Osten**). Dies ist die *Region*, die Sie während der Planungsphase definiert haben. |
   | **Größe des Hosts** | Wählen Sie **AV36** aus. |
   | **Anzahl von Hosts** | Die Anzahl von Hosts, die dem privaten Cloudcluster zugeordnet sind. Der Standardwert ist 3. Sie können den Wert nach der Bereitstellung erhöhen oder verringern.  |
   | **Adressblock für private Cloud** | Geben Sie einen IP-Adressblock für die private Cloud ein.  Die CIDR stellt das Verwaltungsnetzwerk der privaten Cloud dar und wird für die Clusterverwaltungsdienste wie vCenter Server und NSX-T Manager verwendet. Geben Sie einen /22-Adressraum an, beispielsweise 10.175.0.0/22.  Die Adresse sollte eindeutig sein und sich nicht mit anderen virtuellen Azure-Netzwerken und lokalen Netzwerken überschneiden. |
   

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Screenshot: Registerkarte „Grundlagen“ im Fenster „Private Cloud erstellen“." border="true":::

1. Überprüfen Sie die eingegebenen Informationen. Sind die Informationen korrekt, wählen Sie **Erstellen** aus.  

   > [!NOTE]
   > Dieser Schritt dauert ungefähr drei bis vier Stunden. Das Hinzufügen eines einzelnen Hosts in einem vorhandenen oder demselben Cluster dauert zwischen 30 und 45 Minuten.

1. Vergewissern Sie sich, dass die Bereitstellung erfolgreich war. Navigieren Sie zu der von Ihnen erstellten Ressourcengruppe, und wählen Sie Ihre private Cloud aus.  Wenn die Bereitstellung abgeschlossen ist, wird der Status **Erfolgreich** angezeigt. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Screenshot, der zeigt, dass die Bereitstellung erfolgreich war." border="true":::


### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
Zum Erstellen einer privaten Azure VMware Solution-Cloud können Sie anstelle des Azure-Portals auch die Azure CLI über Azure Cloud Shell verwenden. Eine Liste der Befehle, die Sie mit Azure VMware Solution verwenden können, finden Sie [hier](/cli/azure/ext/vmware/vmware).

So beginnen Sie mit der Verwendung der Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]


1. Erstellen Sie mithilfe des Befehls [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

   ```azurecli-interactive
   
   az group create --name myResourceGroup --location eastus
   ```

2. Geben Sie einen Namen für die Ressourcengruppe und für die private Cloud sowie einen Standort und die Größe des Clusters an.

   | Eigenschaft  | BESCHREIBUNG  |
   | --------- | ------------ |
   | **-g** (Ressourcengruppenname)     | Der Name der Ressourcengruppe für die Ressourcen Ihrer privaten Cloud        |
   | **-n** (Name der privaten Cloud)     | Der Name Ihrer privaten Azure VMware Solution-Cloud        |
   | **--location**     | Die für Ihre private Cloud verwendete Region.         |
   | **--cluster-size**     | Die Größe des Clusters. Der Mindestwert ist „3“.         |
   | **--network-block**     | Der für die private Cloud zu verwendende CIDR-IP-Adressenblock des Netzwerks. Der Adressblock darf sich nicht mit Adressblöcken überlappen, die in anderen virtuellen Netzwerken in Ihrem Abonnement und Ihren lokalen Netzwerken verwendet werden.        |
   | **--sku** | Der SKU-Wert AV36 |

   ```azurecli-interactive 
   az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
   ```
