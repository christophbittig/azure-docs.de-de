---
title: Integrieren von Azure NetApp Files mit Azure VMware Solution
description: Verwenden Sie Azure NetApp Files mit Azure VMware Solution-VMs, um Daten zwischen lokalen Servern, Azure VMware Solution-VMs und Cloudinfrastrukturen zu migrieren und zu synchronisieren.
ms.topic: how-to
ms.date: 06/08/2021
ms.openlocfilehash: 7335434c30b88e54fbfd15b18f3604954cbdef0f
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226771"
---
# <a name="integrate-azure-netapp-files-with-azure-vmware-solution"></a>Integrieren von Azure NetApp Files mit Azure VMware Solution

[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) ist ein Azure-Dienst für die Migration und die Ausführung anspruchsvoller Unternehmensdateiworkloads in der Cloud: Datenbanken, SAP und High Performance Computing-Anwendungen, ohne dass Codeänderungen erforderlich sind. In diesem Artikel wird beschrieben, wie Sie mithilfe des NFS-Protokolls (Network File System) ein Azure NetApp Files-Volume einrichten, testen und überprüfen, das als Dateifreigabe für Azure VMware Solution-Workloads dient. Das Gastbetriebssystem wird auf virtuellen Computern (VMs) ausgeführt, die auf Azure NetApp Files-Volumes zugreifen. 

Azure NetApp Files und Azure VMware Solution werden in derselben Azure-Region erstellt. Azure NetApp Files ist in vielen [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=netapp,azure-vmware&regions=all) verfügbar und unterstützt die regionsübergreifende Replikation. Informationen zu den Methoden für die Azure NetApp Files-Konfiguration finden Sie unter [Speicherhierarchie von Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).

Dienste, mit denen Azure NetApp Files verwendet wird:

- **Active Directory-Verbindungen**: Azure NetApp Files unterstützt [Active Directory Domain Services und Azure Active Directory Domain Services](../azure-netapp-files/create-active-directory-connections.md#decide-which-domain-services-to-use).

- **Freigabeprotokoll**: Azure NetApp Files unterstützt SMB- (Server Message Block) und NFS-Protokolle (Network File System). Diese Unterstützung bedeutet, dass die Volumes auf dem Linux-Client bereitgestellt und auf dem Windows-Client zugeordnet werden können.

- **Azure VMware Solution**: Azure NetApp Files-Freigaben können über VMs bereitgestellt werden, die in der Azure VMware Solution-Umgebung erstellt werden.


Das Diagramm zeigt eine Verbindung über Azure ExpressRoute mit einer privaten Azure VMware Solution-Cloud. Die Azure VMware Solution-Umgebung greift auf die Azure NetApp Files-Freigabe zu, die auf Azure VMware Solution-VMs bereitgestellt ist.

:::image type="content" source="media/netapp-files/netapp-files-topology.png" alt-text="Diagramm: NetApp Files für Azure VMware Solution-Architektur" border="false":::


## <a name="prerequisites"></a>Voraussetzungen 

> [!div class="checklist"]
> * Azure-Abonnement mit Aktivierung von Azure NetApp Files
> * Subnetz für Azure NetApp Files
> * Linux-VM auf Azure VMware Solution
> * Windows-VMs unter Azure VMware Solution


## <a name="create-and-mount-azure-netapp-files-volumes"></a>Erstellen und Einbinden von Azure NetApp Files-Volumes

Sie erstellen Azure NetApp Files-Volumes und binden sie in Azure VMware Solution-VMs ein.

1. [Erstellen Sie ein NetApp-Konto](../azure-netapp-files/azure-netapp-files-create-netapp-account.md).

1. [Richten Sie einen Kapazitätspool ein](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).

1. [Erstellen Sie ein SMB-Volume für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes-smb.md).

1. [Erstellen Sie ein NFS-Volume für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-create-volumes.md).

1. [Delegieren Sie ein Subnetz an Azure NetApp Files](../azure-netapp-files/azure-netapp-files-delegate-subnet.md).


## <a name="verify-pre-configured-azure-netapp-files"></a>Überprüfen der vorkonfigurierten Azure NetApp Files-Instanz 

Sie überprüfen die in Azure erstellte vorkonfigurierte Azure NetApp Files-Instanz auf dem Servicelevel „Azure NetApp Files Premium“.

1. Wählen Sie im Azure-Portal unter **Speicher** die Option **Azure NetApp Files** aus. Eine Liste der konfigurierten Azure NetApp Files-Konten wird angezeigt. 

   :::image type="content" source="media/netapp-files/azure-netapp-files-list.png" alt-text="Screenshot: Liste mit vorkonfigurierten Azure NetApp Files-Instanzen"::: 

2. Wählen Sie ein konfiguriertes NetApp Files-Konto aus, um die zugehörigen Einstellungen anzuzeigen. Wählen Sie beispielsweise **Contoso-anf2** aus. 

3. Wählen Sie **Kapazitätspools** aus, um den konfigurierten Pool zu überprüfen. 

   :::image type="content" source="media/netapp-files/netapp-settings.png" alt-text="Screenshot: Optionen zum Anzeigen von Kapazitätspools und Volumes eines konfigurierten NetApp Files-Kontos":::

   Die Seite „Kapazitätspools“ wird geöffnet, und die Kapazität und der Servicelevel werden angezeigt. In diesem Beispiel wurde der Speicherpool mit einer Kapazität von 4 TiB und dem Servicelevel „Premium“ konfiguriert.

4. Wählen Sie die Option **Volumes** aus, um die im Kapazitätspool erstellten Volumes anzuzeigen. (Siehe obigen Screenshot.)

5. Wählen Sie ein Volume aus, um dessen Konfiguration anzuzeigen.  

   :::image type="content" source="media/netapp-files/azure-netapp-volumes.png" alt-text="Screenshot: Im Kapazitätspool erstellte Volumes":::

   Ein Fenster wird geöffnet, in dem die Konfigurationsdetails des Volumes angezeigt werden.

   :::image type="content" source="media/netapp-files/configuration-of-volume.png" alt-text="Screenshot: Konfigurationsdetails eines Volumes":::

   Sie können erkennen, dass das Volume ANFVOLUME eine Größe von 200 GiB hat und sich im Kapazitätspool ANFPOOL1 befindet. Es wird als NFS-Dateifreigabe über 10.22.3.4:/ANFVOLUME exportiert. Eine private IP-Adresse aus dem virtuellen Azure-Netzwerk (VNET) wurde für Azure NetApp Files und den NFS-Pfad für die Bereitstellung der VM erstellt.

   Informationen zur Leistung des Azure NetApp Files-Volumes relativ zur Größe (bzw. zum Kontingent) finden Sie unter [Überlegungen zur Leistung für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-performance-considerations.md). 

## <a name="verify-pre-configured-azure-vmware-solution-vm-share-mapping"></a>Überprüfen der Freigabezuordnung für die vorkonfigurierte Azure VMware Solution-VM

Damit Ihre Azure NetApp Files-Freigabe auf die Azure VMware Solution-VM zugreifen kann, müssen Sie die Zuordnung zwischen SMB- und NFS-Freigaben verstehen. Die SMB- oder NFS-Volumes können erst dann wie hier beschrieben eingebunden werden, nachdem sie konfiguriert wurden.

- **SMB-Freigabe**: Erstellen Sie eine Active Directory-Verbindung, bevor Sie ein SMB-Volume bereitstellen. Damit erfolgreich eine Verbindung hergestellt werden kann, muss das delegierte Subnetz von Azure NetApp Files auf die angegebenen Domänencontroller zugreifen können. Nachdem die Active Directory-Instanz im Azure NetApp Files-Konto konfiguriert wurde, wird sie beim Erstellen eines SMB-Volumes als auswählbares Element angezeigt.

- **NFS-Freigabe**: Azure NetApp Files trägt zur Erstellung der Volumes über das NFS-Protokoll oder beide Protokolle (NFS und SMB) bei. Der Kapazitätsverbrauch eines Volumes wird mit der bereitgestellten Kapazität des dazugehörigen Pools verrechnet. NFS kann auf dem Linux-Server bereitgestellt werden, indem die Befehlszeilen oder „/etc/fstab“-Einträge verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit der Integration von Azure NetApp Files und Ihrer Azure VMware Solution-Workloads beschäftigt haben, möchten Sie möglicherweise mehr über Folgendes erfahren:

- [Ressourceneinschränkungen für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-resource-limits.md#resource-limits)
- [Richtlinien für die Azure NetApp Files-Netzwerkplanung](../azure-netapp-files/azure-netapp-files-network-topologies.md)
- [Regionsübergreifende Replikation von Azure NetApp Files-Volumes](../azure-netapp-files/cross-region-replication-introduction.md) 
- [Azure NetApp Files NFS FAQ](../azure-netapp-files/faq-nfs.md)
- [Azure NetApp Files SMB FAQ](../azure-netapp-files/faq-smb.md)
