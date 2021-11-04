---
title: Verwenden von Azure HPC Cache und Azure NetApp Files
description: Verwenden von Azure HPC Cache zum Verbessern des Zugriffs auf mit Azure NetApp Files gespeicherte Daten
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/05/2021
ms.author: femila
ms.openlocfilehash: 9a4e682b03ef93e0762dd8dc8117741329064caa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032922"
---
# <a name="use-azure-hpc-cache-with-azure-netapp-files"></a>Verwenden von Azure HPC Cache mit Azure NetApp Files

Sie können [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) als Speicherziel für Ihre Azure HPC Cache-Instanz verwenden. In diesem Artikel wird erläutert, wie diese beiden Dienste zusammenarbeiten, und Sie erhalten Tipp für die Einrichtung der Dienste.

Azure NetApp Files kombiniert das ONTAP-Betriebssystem mit der Skalierbarkeit und Geschwindigkeit von Microsoft Azure. Mit dieser Kombination können Benutzer bereits eingerichtete Workflows in die Cloud verschieben, ohne Code neu schreiben zu müssen.

Durch Hinzufügen einer Azure HPC Cache-Komponente lässt sich der Dateizugriff verbessern, da mehrere Azure NetApp Files-Volumes in einem aggregierten Namespace dargestellt werden. Damit lässt sich ein Edgecache für Volumes bereitstellen, die sich in einer anderen Dienstregion befinden. Auch die bedarfsbasierte Leistung kann für Volumes verbessert werden, die auf zur Kostenersparnis niedrigeren Dienstebenen erstellt werden.

## <a name="overview"></a>Übersicht

Um ein Azure NetApp Files-System als Back-End-Speicher mit Azure HPC Cache zu verwenden, führen Sie die folgenden Schritte aus.

1. Erstellen Sie Azure NetApp Files-System- und -Volumes gemäß der Anleitung im Abschnitt [Planen Ihres Azure NetApp Files-Systems](#plan-your-azure-netapp-files-system) weiter unten.
1. Erstellen Sie die Azure HPC Cache-Instanz in der Region, in der Sie Dateizugriff benötigen. (Befolgen Sie die Anweisungen unter [Erstellen einer Azure HPC Cache-Instanz](hpc-cache-create.md).)
1. [Definieren Sie Speicherziele](#create-storage-targets-in-the-cache) im Cache, die auf die Azure NetApp Files-Volumes zeigen. Erstellen Sie ein Cachespeicherziel für jede eindeutige IP-Adresse, die für den Zugriff auf die Volumes verwendet wird.
1. Richten Sie Clients so ein, dass sie die [Azure HPC Cache-Instanz einbinden](#mount-storage-targets), anstatt Azure NetApp Files-Volumes direkt einzubinden.

## <a name="plan-your-azure-netapp-files-system"></a>Planen Ihres Azure NetApp Files-Systems

Wenn Sie Ihr Azure NetApp Files-System planen, berücksichtigen Sie die Elemente in diesem Abschnitt, um eine nahtlose Integration in Azure HPC Cache sicherzustellen.

Lesen Sie auch die [Dokumentation zu Azure NetApp Files](../azure-netapp-files/index.yml), bevor Sie Volumes zur Verwendung mit Azure HPC Cache erstellen.

### <a name="nfs-client-access-only"></a>Nur NFS-Clientzugriff

Azure HPC Cache unterstützt derzeit nur NFS-Zugriff. Der Dienst kann nicht mit Bitvolumes im SMB ACL- oder POSIX-Modus verwendet werden.

### <a name="exclusive-subnet-for-azure-netapp-files"></a>Exklusives Subnetz für Azure NetApp Files

Azure NetApp Files verwendet ein einzelnes delegiertes Subnetz für die Volumes. Dieses Subnetz kann von keinen anderen Ressourcen verwendet werden. Es kann auch nur ein Subnetz in einem virtuellen Netzwerk für Azure NetApp Files verwendet werden. Mehr dazu erfahren Sie in den [Richtlinien für die Azure NetApp Files-Netzwerkplanung](../azure-netapp-files/azure-netapp-files-network-topologies.md).

### <a name="delegated-subnet-size"></a>Größe des delegierten Subnetzes

Verwenden Sie beim Erstellen eines Azure NetApp Files-Systems zur Verwendung mit Azure HPC Cache die Mindestgröße für das delegierte Subnetz.

Die Mindestgröße, die mit der Netzmaske „/28“ angegeben wird, stellt 16 IP-Adressen bereit. In der Praxis verwendet Azure NetApp Files nur drei dieser verfügbaren IP-Adressen für den Volumezugriff. Das bedeutet, dass Sie in Ihrer Azure HPC Cache-Instanz nur drei Speicherziele erstellen müssen, um alle Volumes abzudecken.

Wenn das delegierte Subnetz zu groß ist, besteht die Möglichkeit, dass die Azure NetApp Files-Volumes mehr IP-Adressen verwenden, als eine einzelne Azure HPC Cache-Instanz verarbeiten kann. Ein einzelner Cache verfügt über ein [Limit von zehn Speicherzielen](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets) für die meisten Kombinationen aus Durchsatz und Speichergröße. Für die größten Konfigurationen liegt das Limit bei 20 Speicherzielen.

Das Schnellstartbeispiel in der Azure NetApp Files-Dokumentation verwendet 10.7.0.0/16 als delegiertes Subnetz. Dieses Subnetz ist zu groß.

### <a name="capacity-pool-service-level"></a>Dienstebene des Kapazitätspools

Wenn Sie die [Dienstebene](../azure-netapp-files/azure-netapp-files-service-levels.md) für Ihren Kapazitätspool auswählen, berücksichtigen Sie Ihren Workflow. Wenn häufig Daten in das Azure NetApp Files-Volume geschrieben werden und viel Zeit für das Rückschreiben benötigt wird, kann die Leistung des Caches eingeschränkt sein. Wählen Sie eine hohe Dienstebene für Volumes aus, in denen häufig Schreibvorgänge ausgeführt werden.

Bei Volumes mit niedrigen Dienstebenen können auch beim Starten eines Tasks Verzögerungen auftreten, während der Cache Inhalte vorab auffüllt. Wenn der Cache mit einem gut funktionierenden Satz Dateien eingerichtet ist und ausgeführt wird, sollte die Verzögerung unmerklich werden.

Es ist wichtig, die Dienstebene für den Kapazitätspool im Voraus zu planen, da diese nach der Erstellung nicht mehr geändert werden kann. Ein neues Volume müsste in einem anderen Kapazitätspool erstellt und die Daten müssten in diesen Pool kopiert werden.

Beachten Sie, dass Sie das Speicherkontingent eines Volumes und die Größe des Kapazitätspools ändern können, ohne den Zugriff zu unterbrechen.

## <a name="create-storage-targets-in-the-cache"></a>Erstellen von Speicherzielen im Cache

Nachdem Ihr Azure NetApp Files-System eingerichtet und die Azure HPC Cache-Instanz erstellt wurden, definieren Sie Speicherziele in dem Cache, der auf die Dateisystemvolumes zeigt.

Erstellen Sie ein Speicherziel pro IP-Adresse, die von Ihren Azure NetApp Files-Volumes verwendet wird. Die IP-Adresse wird auf der Seite mit den Einbindungsanweisungen für das Volume aufgeführt.

Wenn mehrere Volumes die gleiche IP-Adresse besitzen, können Sie ein Speicherziel für alle verwenden.  

Befolgen Sie die [Anweisungen zum Einbinden von Volumes in der Azure NetApp Files-Dokumentation](../azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md), um die zu verwendenden IP-Adressen zu finden.

Sie können die IP-Adressen auch mit der Azure CLI suchen:

```azurecli
az netappfiles volume list -g ${RESOURCE_GROUP} --account-name ${ANF_ACCOUNT} --pool-name ${POOL} --query "[].mountTargets[].ipAddress" | grep -Ee '[0-9]+[.][0-9]+[.][0-9]+[.][0-9]+' | tr -d '"' | tr -d , | sort | uniq
```

Exportnamen im Azure NetApp Files-System weisen nur eine Pfadkomponente auf. Versuchen Sie nicht, ein Speicherziel für den Stammexport ``/`` in Azure NetApp Files zu erstellen – dieser Export bietet keinen Dateizugriff.

Es gibt keine besonderen Einschränkungen in Bezug auf die Pfade der virtuellen Namespaces für diese Speicherziele.

## <a name="mount-storage-targets"></a>Einbinden von Speicherzielen

Clientcomputer sollten den Cache einbinden, anstatt die Azure NetApp Files-Volumes direkt einzubinden. Befolgen Sie die Anweisungen unter [Einbinden einer Azure HPC Cache-Instanz](hpc-cache-mount.md).

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das Einrichten und Verwenden von [Azure NetApp Files](../azure-netapp-files/index.yml).
* Um Unterstützung bei der Planung und Einrichtung Ihres Azure HPC Cache-Systems zur Verwendung von Azure NetApp Files zu erhalten, [wenden Sie sich an den Support](hpc-cache-support-ticket.md).
