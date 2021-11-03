---
title: Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)
description: Hier erfahren Sie, wie Sie einen Datenträgerpool anfügen, der über ein iSCSI-Ziel als VMware-Datenspeicher einer privaten Azure VMware Solution-Cloud verfügbar gemacht wird. Sobald der Datenspeicher konfiguriert ist, können Sie darin Volumes erstellen und an Ihre VMware-Instanzen anfügen.
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2719b135860f448732f5f36285ef2c33ff0115e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071629"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>Anfügen von Datenträgerpools an Azure VMware Solution-Hosts (Vorschau)

[Azure-Datenträgerpools](../virtual-machines/disks-pools.md) bieten persistenten Blockspeicher für Anwendungen und Workloads, die von Azure Disks unterstützt werden. Sie können Datenträger als persistenten Speicher für Azure VMware Solution einsetzen, um sowohl die Kosten als auch die Leistung zu optimieren. Wenn Sie speicherintensive Workloads hosten, können Sie anstelle einer Clusterskalierung z. B. eine Hochskalierung mithilfe von Datenträgerpools vornehmen. Datenträger können zudem für die Replikation von Daten aus lokalen oder primären VMware-Umgebungen in Datenspeicher für den sekundären Standort verwendet werden. Wenn Speicher unabhängig von den Azure VMware Solution-Hosts skaliert werden soll, werden [Ultra-Datenträger](../virtual-machines/disks-types.md#ultra-disks) und [SSD Premium](../virtual-machines/disks-types.md#premium-ssds)- und [SSD Standard](../virtual-machines/disks-types.md#standard-ssds)-Datenträger als Datenspeicher unterstützt.  

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

   - Wenn Sie Ultra-Datenträger auswählen, verwenden Sie ein ExpressRoute-VNet-Gateway mit Höchstleistung für die Netzwerkverbindung des Datenträgerpools mit Ihrer privaten Azure VMware Solution-Cloud und [aktivieren dann ExpressRoute FastPath](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

   - Wenn Sie Premium- oder Standard-SSD-Datenträger auswählen, verwenden Sie ein ExpressRoute-VNet-Gateway des Typs „Standard“ (1 GBit/s) oder „Hochleistung“ (2 GBit/s) für die Netzwerkverbindung des Datenträgerpools mit Ihrer privaten Azure VMware Solution-Cloud.  

- Zum Hosten von iSCSI muss „Standard\_DS##\_v3“ verwendet werden.  Bei Kontingentproblemen fordern Sie eine Erhöhung der [vCPU-Kontingentgrenzen](../azure-portal/supportability/per-vm-quota-requests.md) für jede Azure-VM-Serie für die Dsv3-Serie an.

- Ein Datenträgerpool als zugrunde liegender Speicher, der als iSCSI-Ziel bereitgestellt und verfügbar gemacht wird. Dabei wird jeder Datenträger als einzelne LUN angezeigt. Einzelheiten finden Sie unter [Deploy an Azure disk pool](../virtual-machines/disks-pools-deploy.md) (Bereitstellen eines Azure-Datenträgerpools).

   >[!IMPORTANT]
   > Der Datenträgerpool muss im gleichen Abonnement bereitgestellt werden wie der VMware-Cluster, und er muss an dasselbe VNet angefügt werden wie der VMware-Cluster.

## <a name="add-a-disk-pool-to-your-private-cloud"></a>Hinzufügen eines Datenträgerpools zu Ihrer privaten Cloud
Sie fügen einen Datenträgerpool an, der über ein iSCSI-Ziel als VMware-Datenspeicher einer privaten Azure VMware Solution-Cloud verfügbar gemacht wird.

>[!IMPORTANT]
>In der **Vorschauphase** sollten Sie einen Datenträgerpool lediglich an einen Test- oder Nichtproduktionscluster anfügen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Überprüfen Sie, ob das Abonnement bei `Microsoft.AVS` registriert ist.

```azurecli
az provider show -n "Microsoft.AVS" --query registrationState
```

Falls es nicht bereits registriert ist, registrieren Sie das Abonnement.

```azurecli
az provider register -n "Microsoft.AVS"
```

Überprüfen Sie, ob das Abonnement bei `CloudSanExperience` AFEC in Microsoft.AVS registriert ist.

```azurecli
az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

Falls es nicht bereits registriert ist, registrieren Sie das Abonnement.

```azurecli
az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

Die Registrierung kann ca. 15 Minuten dauern. Sie können den Status mit dem folgenden Befehl überprüfen:

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

Überprüfen Sie, ob die `vmware `-Erweiterung installiert ist. 

```azurecli
az extension show --name vmware
```

Wenn die Erweiterung bereits installiert ist, überprüfen Sie, ob die Version **3.0.0** lautet. Wenn eine ältere Version installiert ist, aktualisieren Sie die Erweiterung.

```azurecli
az extension update --name vmware
```

Falls die Erweiterung noch nicht installiert ist, installieren Sie sie.

```azurecli
az extension add --name vmware
```

### <a name="attach-the-iscsi-lun"></a>Anfügen der iSCSI-LUN

Erstellen Sie einen iSCSI-Datenspeicher und fügen Sie ihn unter Verwendung des von `Microsoft.StoragePool` bereitgestellten iSCSI-Ziels im privaten Azure VMware Solution-Cloudcluster an. Der Datenträgerpool wird über ein delegiertes Subnetz an ein VNet angefügt, was mit dem Ressourcenanbieter Microsoft.StoragePool/diskPools erfolgt.  Wenn das Subnetz nicht delegiert ist, tritt bei der Bereitstellung ein Fehler auf.

```bash
#Initialize input parameters
resourceGroupName='<yourRGName>'
name='<desiredDataStoreName>'
cluster='<desiredCluster>'
privateCloud='<privateCloud>'
lunName='<desiredLunName>'

az vmware datastore disk-pool-volume create --name $name --resource-group $resourceGroupName --cluster $cluster --private-cloud $privateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name $lunName
```

>[!TIP]
>Sie können die Hilfe zu den Datenspeichern anzeigen.
>
>   ```azurecli
>   az vmware datastore -h
>   ```


Um zu bestätigen, dass das Anfügen erfolgreich war, können Sie die folgenden Befehle verwenden:

Zeigen Sie die Details eines iSCSI-Datenspeichers in einem privaten Cloudcluster an.

```azurecli
az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
```

Listen Sie alle Datenspeicher in einem privaten Cloudcluster auf.

```azurecli
az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="preview-registration"></a>Vorschauregistrierung

Registrieren Sie zunächst Ihr Abonnement bei Microsoft.AVS und CloudSanExperience.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Suchen Sie nach **Abonnements**, und wählen Sie diese Option aus.
1. Wählen Sie das gewünschte Abonnement und dann unter **Einstellungen** die Option **Ressourcenanbieter** aus.
1. Suchen Sie nach **Microsoft.AVS**. Wählen Sie diese Option und dann **Registrieren** aus.
1. Wählen Sie unter **Einstellungen** die Option **Previewfunktionen** aus.
1. Suchen Sie nach **CloudSanExperience**, und registrieren Sie diese Option.

### <a name="connect-your-disk-pool"></a>Verbinden Ihres Datenträgerpools

Nachdem Ihr Abonnement ordnungsgemäß registriert wurde, können Sie Ihren Datenträgerpool mit Ihrem privaten Azure VMware Solution-Cloudcluster verbinden.

> [!IMPORTANT]
> Der Datenträgerpool wird über ein delegiertes Subnetz an ein VNet angefügt, was mit dem Ressourcenanbieter Microsoft.StoragePool erfolgt. Wenn das Subnetz nicht delegiert ist, tritt bei der Bereitstellung ein Fehler auf. Weitere Informationen finden Sie unter [Delegieren der Subnetzberechtigung](../virtual-machines/disks-pools-deploy.md#delegate-subnet-permission).

1. Navigieren Sie zu Ihrer Azure VMware Solution-Instanz.
1. Wählen Sie **Speicher (Vorschau)** unter **Verwalten** aus.
1. Wählen Sie **Datenträgerpool verbinden** aus.
1. Wählen Sie das gewünschte Abonnement aus.
1. Wählen Sie Ihren Datenträgerpool und den Clientcluster aus, mit dem Sie eine Verbindung herstellen möchten.
1. Aktivieren Sie Ihre LUNs (falls vorhanden). Geben Sie einen Datenspeichernamen an (standardmäßig wird die LUN verwendet), und wählen Sie **Verbinden** aus.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png" alt-text="Screenshot: Herstellen der Verbindung mit einem Datenträgerpool." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png":::

Nachdem die Verbindung erfolgreich hergestellt wurde, werden die vCenter hinzugefügten Datenspeicher gezeigt.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png" alt-text="Screenshot der vSphere-Benutzeroberfläche: Datenträgerpools wurden als Datenspeicher angefügt." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png":::

---

## <a name="disconnect-a-disk-pool-from-your-private-cloud"></a>Trennen eines Datenträgerpools von Ihrer privaten Cloud

Wenn Sie die Verbindung mit einem Datenträgerpool trennen, werden die Ressourcen des Datenträgerpools nicht gelöscht. Für diesen Vorgang ist kein Wartungsfenster erforderlich. Seien Sie jedoch vorsichtig, wenn Sie dies tun.

Schalten Sie zunächst die VMs aus, und entfernen Sie alle Objekte, die den Datenspeichern des Datenträgerpools zugeordnet sind. Dazu zählen u. a.:

   - VMs (aus dem Bestand entfernen)

   - Vorlagen

   - Momentaufnahmen

Löschen Sie dann den Datenspeicher der privaten Cloud.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure VMware Solution-Instanz.
1. Wählen Sie **Speicher** unter **Verwalten** aus.
1. Wählen Sie den Datenträgerpool, dessen Verbindung Sie trennen möchten, und danach **Trennen** aus.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png" alt-text="Screenshot der Seite mit dem Azure VMware Solution-Speicher und der Liste der angefügten Datenträgerpools mit Hervorhebung von „Trennen“." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen Datenträgerpool an Ihre Azure VMware Solution-Hosts angefügt haben, informieren Sie sich über die folgenden Themen:

- [Verwalten eines Azure-Datenträgerpools](../virtual-machines/disks-pools-manage.md).  Nachdem Sie einen Datenträgerpool bereitgestellt haben, stehen verschiedene Verwaltungsaktionen zur Verfügung. Sie können einen Datenträger zu einem Datenträgerpool hinzufügen oder daraus entfernen, die iSCSI-LUN-Zuordnung aktualisieren oder ACLs hinzufügen.

- [Löschen eines Datenträgerpools](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool). Wenn Sie einen Datenträgerpool löschen, werden alle Ressourcen in der verwalteten Ressourcengruppe ebenfalls gelöscht.

- [Deaktivieren der iSCSI-Unterstützung auf einem Datenträger](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support). Wenn Sie die iSCSI-Unterstützung für einen Datenträgerpool deaktivieren, können Sie den Datenträgerpool nicht mehr verwenden.

- [Verschieben eines Datenträgerpools in ein anderes Abonnement](../virtual-machines/disks-pools-move-resource.md). Verschieben Sie einen Azure-Datenträgerpool in ein anderes Abonnement. Dies umfasst das Verschieben des Datenträgerpools selbst, der enthaltenen Datenträger, der verwalteten Ressourcengruppe und aller Ressourcen. 

- [Problembehandlung bei Datenträgerpools](../virtual-machines/disks-pools-troubleshoot.md). Sehen Sie sich die allgemeinen Fehlercodes im Zusammenhang mit Azure-Datenträgerpools (Vorschauversion) an. Außerdem werden mögliche Lösungen und Informationen zu den Status von Datenträgerpools aufgeführt.
