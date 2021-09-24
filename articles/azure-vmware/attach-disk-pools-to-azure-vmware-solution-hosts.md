---
title: Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)
description: Hier erfahren Sie, wie Sie einen Datenträgerpool anfügen, der über ein iSCSI-Ziel als VMware-Datenspeicher einer privaten Azure VMware Solution-Cloud verfügbar gemacht wird. Sobald der Datenspeicher konfiguriert ist, können Sie darin Volumes erstellen und an Ihre VMware-Instanzen anfügen.
ms.topic: how-to
ms.date: 08/20/2021
ms.openlocfilehash: 2487e26d887935f0d66f13d51ce7894edb2b2b6e
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769300"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)

[Azure-Datenträgerpools](../virtual-machines/disks-pools.md) bieten persistenten Blockspeicher für Anwendungen und Workloads, die von Azure Disks unterstützt werden. Sie können Datenträger als persistenten Speicher für Azure VMware Solution einsetzen, um sowohl die Kosten als auch die Leistung zu optimieren. Wenn Sie speicherintensive Workloads hosten, können Sie anstelle einer Clusterskalierung z. B. eine Hochskalierung mithilfe von Datenträgerpools vornehmen. Datenträger können zudem für die Replikation von Daten aus lokalen oder primären VMware-Umgebungen in Datenspeicher für den sekundären Standort verwendet werden. Wenn Speicher unabhängig von den Azure VMware Solution-Hosts skaliert werden soll, werden [Ultra-Datenträger](../virtual-machines/disks-types.md#ultra-disk) und [SSD Premium-Datenträger](../virtual-machines/disks-types.md#premium-ssd) als Datenspeicher unterstützt.  

>[!IMPORTANT]
>Azure-Datenträgerpools in Azure VMware Solution (Vorschau) sind derzeit als öffentliche Vorschauversion verfügbar.
>Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
>Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Verwaltete Azure-Datenträger werden an eine iSCSI-Controller-VM angefügt, die in der Azure VMware Solution-Ressourcengruppe bereitgestellt wird. Die Datenträger werden als Speicherziele für einen Datenträgerpool bereitgestellt, und jedes Speicherziel wird als iSCSI-LUN unterhalb des iSCSI-Ziels angezeigt. Sie können einen Datenträgerpool als iSCSI-Ziel verfügbar machen, das mit Azure VMware Solution-Hosts als Datenspeicher verbunden ist. Ein Datenträgerpool wird als einzelner Endpunkt für alle zugrunde liegenden Datenträger verfügbar gemacht, die als Speicherziele hinzugefügt werden. Jeder Datenträgerpool kann nur über jeweils einen iSCSI-Controller verfügen.

Die Abbildung zeigt, wie Datenträgerpools mit Azure VMware Solution-Hosts funktionieren. Jeder iSCSI-Controller greift über ein Azure-Standardprotokoll auf einen verwalteten Datenträger zu, und die Azure VMware Solution-Hosts können über iSCSI auf den iSCSI-Controller zugreifen.


:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="Darstellung der Funktionsweise von Datenträgerpools mit Azure VMware Solution-Hosts." border="false":::


## <a name="supported-regions"></a>Unterstützte Regionen

Sie können den Datenträgerpool ausschließlich mit einer privaten Azure VMware Solution-Cloud in derselben Region verbinden. Eine Liste der unterstützten Regionen finden Sie unter [Regionale Verfügbarkeit](../virtual-machines/disks-pools.md#regional-availability).  Wenn Ihre private Cloud in einer nicht unterstützten Region bereitgestellt wird, können Sie sie in einer unterstützten Region erneut bereitstellen. Durch die gemeinsame Platzierung der privaten Azure VMware Solution-Cloud und des Datenträgerpools in derselben Region wird eine optimale Leistung mit minimaler Netzwerklatenz erreicht.


## <a name="prerequisites"></a>Voraussetzungen

- Die Skalierbarkeits- und Leistungsanforderungen Ihrer Workloads wurden bestimmt. Einzelheiten finden Sie unter [Planen von Datenträgerpools](../virtual-machines/disks-pools-planning.md).

- Eine [private Azure VMware Solution-Cloud](deploy-azure-vmware-solution.md), die mit einem [konfigurierten virtuellen Netzwerk](deploy-azure-vmware-solution.md#connect-to-azure-virtual-network-with-expressroute) bereitgestellt wurde. Weitere Informationen finden Sie in der [Checkliste für die Netzwerkplanung](tutorial-network-checklist.md) und unter [Konfigurieren des Netzwerks für Ihre private VMware-Cloud in Azure](tutorial-configure-networking.md). 

   - Wenn Sie Ultra-Datenträger auswählen, verwenden Sie „Ultra Performance“ (Höchstleistung) für die private Azure VMware Solution-Cloud, und [aktivieren Sie ExpressRoute FastPath](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

   - Bei Auswahl von SSD Premium-Datenträgern verwenden Sie „Standard“ (1 GBit/s) für die private Azure VMware Solution-Cloud.  Zum Hosten von iSCSI muss „Standard\_DS##\_v3“ verwendet werden.  Bei Kontingentproblemen fordern Sie eine Erhöhung der [vCPU-Kontingentgrenzen](../azure-portal/supportability/per-vm-quota-requests.md) für jede Azure-VM-Serie für die Dsv3-Serie an.

- Ein Datenträgerpool als zugrunde liegender Speicher, der als iSCSI-Ziel bereitgestellt und verfügbar gemacht wird. Dabei wird jeder Datenträger als einzelne LUN angezeigt. Einzelheiten finden Sie unter [Deploy an Azure disk pool](../virtual-machines/disks-pools-deploy.md) (Bereitstellen eines Azure-Datenträgerpools).

   >[!IMPORTANT]
   > Der Datenträgerpool muss im gleichen Abonnement bereitgestellt werden wie der VMware-Cluster, und er muss an dasselbe VNet angefügt werden wie der VMware-Cluster.

## <a name="attach-a-disk-pool-to-your-private-cloud"></a>Anfügen eines Datenträgerpools an Ihre private Cloud
Sie fügen einen Datenträgerpool an, der über ein iSCSI-Ziel als VMware-Datenspeicher einer privaten Azure VMware Solution-Cloud verfügbar gemacht wird.

>[!IMPORTANT]
>In der **Vorschauphase** sollten Sie einen Datenträgerpool lediglich an einen Test- oder Nichtproduktionscluster anfügen.

1. Überprüfen Sie, ob das Abonnement bei `Microsoft.AVS` registriert ist.

   ```azurecli
   az provider show -n "Microsoft.AVS" --query registrationState
   ```

   Falls es nicht bereits registriert ist, registrieren Sie das Abonnement.

   ```azurecli
   az provider register -n "Microsoft.AVS"
   ```

2. Überprüfen Sie, ob das Abonnement bei `CloudSanExperience` AFEC in Microsoft.AVS registriert ist.

   ```azurecli
   az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
   ```

   - Falls es nicht bereits registriert ist, registrieren Sie das Abonnement.

      ```azurecli
      az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      ```

      Die Registrierung kann ca. 15 Minuten dauern. Sie können den aktuellen Status überprüfen.
      
      ```azurecli
      az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS" --query properties.state
      ```

      >[!TIP]
      >Wenn die Registrierung länger als 15 Minuten in einem Zwischenzustand verbleibt, müssen Sie die Registrierung aufheben und das Flag erneut registrieren.
      >
      >```azurecli
      >az feature unregister --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
      >```

3. Überprüfen Sie, ob die `vmware `-Erweiterung installiert ist. 

   ```azurecli
   az extension show --name vmware
   ```

   - Wenn die Erweiterung bereits installiert ist, überprüfen Sie, ob die Version **3.0.0** lautet. Wenn eine ältere Version installiert ist, aktualisieren Sie die Erweiterung.

      ```azurecli
      az extension update --name vmware
      ```

   - Falls die Erweiterung noch nicht installiert ist, installieren Sie sie.

      ```azurecli
      az extension add --name vmware
      ```

4. Erstellen Sie einen iSCSI-Datenspeicher und fügen Sie ihn unter Verwendung des von `Microsoft.StoragePool` bereitgestellten iSCSI-Ziels im privaten Azure VMware Solution-Cloudcluster an. Der Datenträgerpool wird über ein delegiertes Subnetz an ein VNet angefügt, was mit dem Ressourcenanbieter „Microsoft.StoragePool/diskPools“ erfolgt.  Wenn das Subnetz nicht delegiert ist, tritt bei der Bereitstellung ein Fehler auf.

   ```bash
   az vmware datastore disk-pool-volume create --name iSCSIDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name lun0
   ```

   >[!TIP]
   >Sie können die Hilfe zu den Datenspeichern anzeigen.
   >
   >   ```azurecli
   >   az vmware datastore -h
   >   ```
   

5. Zeigen Sie die Details eines iSCSI-Datenspeichers in einem privaten Cloudcluster an.
   
   ```azurecli
   az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
   ```

6. Listen Sie alle Datenspeicher in einem privaten Cloudcluster auf.

   ```azurecli
   az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="delete-an-iscsi-datastore-from-your-private-cloud"></a>Löschen eines iSCSI-Datenspeichers aus Ihrer privaten Cloud

Wenn Sie den Datenspeicher einer privaten Cloud löschen, werden die Ressourcen des Datenträgerpools nicht gelöscht. Für diesen Vorgang ist kein Wartungsfenster erforderlich.

1. Schalten Sie die VMs aus, und entfernen Sie alle Objekte, die den iSCSI-Datenspeichern zugeordnet sind. Dazu zählen:

   - VMs (aus dem Bestand entfernen)

   - Vorlagen

   - Momentaufnahmen

2. Löschen Sie den Datenspeicher der privaten Cloud.

   ```azurecli
   az vmware datastore delete --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
   ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen Datenträgerpool an Ihre Azure VMware Solution-Hosts angefügt haben, informieren Sie sich über die folgenden Themen:

- [Verwalten eines Azure-Datenträgerpools](../virtual-machines/disks-pools-manage.md).  Nachdem Sie einen Datenträgerpool bereitgestellt haben, stehen verschiedene Verwaltungsaktionen zur Verfügung. Sie können einen Datenträger zu einem Datenträgerpool hinzufügen oder daraus entfernen, die iSCSI-LUN-Zuordnung aktualisieren oder ACLs hinzufügen.

- [Löschen eines Datenträgerpools](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool). Wenn Sie einen Datenträgerpool löschen, werden alle Ressourcen in der verwalteten Ressourcengruppe ebenfalls gelöscht.

- [Deaktivieren der iSCSI-Unterstützung auf einem Datenträger](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support). Wenn Sie die iSCSI-Unterstützung für einen Datenträgerpool deaktivieren, können Sie den Datenträgerpool nicht mehr verwenden.

- [Verschieben eines Datenträgerpools in ein anderes Abonnement](../virtual-machines/disks-pools-move-resource.md). Verschieben Sie einen Azure-Datenträgerpool in ein anderes Abonnement. Dies umfasst das Verschieben des Datenträgerpools selbst, der enthaltenen Datenträger, der verwalteten Ressourcengruppe und aller Ressourcen. 

- [Problembehandlung bei Datenträgerpools](../virtual-machines/disks-pools-troubleshoot.md). Sehen Sie sich die allgemeinen Fehlercodes im Zusammenhang mit Azure-Datenträgerpools (Vorschauversion) an. Außerdem werden mögliche Lösungen und Informationen zu den Status von Datenträgerpools aufgeführt.
