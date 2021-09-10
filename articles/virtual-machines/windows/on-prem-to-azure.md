---
title: Migrieren von AWS und anderen Plattformen zu verwalteten Datenträgern in Azure
description: Erstellen Sie VMs in Azure mithilfe von VHDs, die aus anderen Clouds wie AWS oder von anderen Virtualisierungsplattformen hochgeladen werden, und verwenden Sie verwaltete Azure-Datenträger.
author: roygara
manager: twooley
ms.service: storage
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68aaf58230ebadd7283e62baf232b84743bba535
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692414"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migrieren von Amazon Web Services (AWS) und anderen Plattformen zu verwalteten Datenträgern in Azure

**Gilt für**: :heavy_check_mark: Windows VMs 

Sie können VHD-Dateien aus AWS oder lokalen Virtualisierungslösungen in Azure hochladen, um virtuelle Computer (VMs) zu erstellen, die verwaltete Datenträger nutzen. Dank verwalteter Azure-Datenträger ist es nicht mehr erforderlich, Speicherkonten für Azure-IaaS-VMs zu verwalten. Sie geben einfach den Typ und die Größe des erforderlichen Datenträgers an, und Azure erstellt und verwaltet den Datenträger für Sie. 

Sie können sowohl generalisierte als auch spezialisierte VHDs hochladen. 
- **Generalisierte VHD**: es wurden alle Ihre persönlichen Kontoinformationen mit Sysprep entfernt. 
- **Spezialisierte VHD**: Behält die Benutzerkonten, Anwendungen und andere Zustandsdaten Ihrer ursprünglichen VM bei. 

> [!IMPORTANT]
> Bevor Sie eine VHD in Azure hochladen, befolgen Sie die Anweisungen unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](prepare-for-upload-vhd-image.md).
>
>


| Szenario                                                                                                                         | Dokumentation                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Sie verfügen über AWS EC2-Instanzen, die Sie mithilfe von verwalteten Datenträgern zu Azure-VMs migrieren möchten.                              | [Verschieben eines VM aus Amazon Web Services (AWS) zu Azure](aws-to-azure.md)                           |
| Sie verfügen über eine VM von einer anderen Virtualisierungsplattform, die Sie als Image zum Erstellen weiterer Azure-VMs nutzen möchten. | [Hochladen einer generalisierten VHD und Verwendung dieser VHD zum Erstellen einer neuen VM in Azure](upload-generalized-managed.md) |
| Sie habe eine besonders angepasste VM, die Sie in Azure neu erstellen möchten.                                                      | [Hochladen einer spezialisierten VHD in Azure und Erstellen einer neuen VM](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Übersicht über verwaltete Datenträger

Verwaltete Azure-Datenträger vereinfachen die VM-Verwaltung dadurch, dass die Verwaltung von Speicherkonten entfällt. Verwaltete Datenträger profitieren auch von der höheren Zuverlässigkeit von VMs in einer Verfügbarkeitsgruppe. Es wird sichergestellt, dass die Datenträger verschiedener VMs in einer Verfügbarkeitsgruppe ausreichend voneinander isoliert sind, um einen Single Point of Failure zu vermeiden. Datenträger verschiedener VMs werden automatisch in einer Verfügbarkeitsgruppe in unterschiedlichen Skalierungseinheiten von Speicher (sog. „Stamps“) platziert. Dadurch werden die Auswirkungen des Ausfalls einzelner Speicherskalierungseinheiten eingedämmt, die von Hardware- und Softwarefehlern verursacht werden.
Je nach Anforderungen stehen vier Typen von Speicheroptionen zur Wahl. Weitere Informationen zu den verfügbaren Datenträgertypen finden Sie in unserem Artikel [Auswählen eines Datenträgertyps](../disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Planen der Migration zu verwalteten Datenträgern

Dieser Abschnitt hilft Ihnen, hinsichtlich VM- und Datenträgertypen die beste Entscheidung zu treffen.

Wenn Sie eine Migration von nicht verwalteten Datenträgern zu verwalteten Datenträgern planen, müssen Sie beachten, dass Benutzer mit der Rolle [Mitwirkender von virtuellen Computern](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) danach die Größe des virtuellen Computers nicht mehr ändern können (was vor dem Wechsel möglich war). Der Grund dafür ist, dass virtuelle Computer mit verwalteten Datenträgern erfordern, dass der Benutzer über die Berechtigung „Microsoft.Compute/disks/write“ für die Betriebssystemdatenträger verfügt.

### <a name="location"></a>Standort

Wählen Sie einen Standort aus, an dem verwaltete Azure-Datenträger verfügbar sind. Wenn Sie zu SSD Premium-Datenträgern migrieren, stellen Sie außerdem sicher, dass Storage Premium in der Region verfügbar ist, in die die Migration erfolgen soll. Aktuelle Informationen zu verfügbaren Standorten finden Sie unter [Azure-Dienste nach Region](https://azure.microsoft.com/regions/#services).

### <a name="vm-sizes"></a>VM-Größen

Wenn Sie zu SSD Premium-Datenträgern migrieren, müssen Sie die Größe der VM in eine Storage Premium-fähige Größe ändern, die in der Region mit dem Standort der VM verfügbar ist. Überprüfen Sie die Storage Premium-fähigen VM-Größen. Die Größenspezifikationen der Azure-VM sind unter [Größen für virtuelle Computer](../sizes.md)aufgelistet.
Sehen Sie sich die Leistungsmerkmale von virtuellen Computern für Storage Premium an, und wählen Sie die am besten für Ihre Workload geeignete VM-Größe aus. Stellen Sie sicher, dass auf Ihrem virtuellen Computer ausreichend Bandbreite zum Steuern des Datenverkehrs des Datenträgers verfügbar ist.

### <a name="disk-sizes"></a>Datenträgergrößen

Informationen zu verfügbaren Datenträgertypen und -größen finden Sie unter [Welche Datenträgertypen stehen in Azure zur Verfügung?](../disks-types.md).

### <a name="disk-caching-policy"></a>Zwischenspeicherungsrichtlinie für Datenträger 

**Premium Managed Disks**

Standardmäßig ist die Richtlinie für das Zwischenspeichern für alle Premium-Datenträger *Schreibgeschützt* und für die Premium-Betriebssystem-Datenträger, die an den virtuellen Computer angeschlossen sind, *Lesen/Schreiben*. Diese Konfigurationseinstellung wird empfohlen, um die optimale E/A-Leistung für Ihre Anwendung zu erreichen. Für Datenträger mit hohem oder ausschließlichem Schreibzugriff (z. B. SQL Server-Protokolldateien) deaktivieren Sie das Zwischenspeichern, sodass Sie eine bessere Anwendungsleistung erzielen können.

### <a name="pricing"></a>Preise

Überprüfen Sie die [Preise für verwaltete Datenträger](https://azure.microsoft.com/pricing/details/managed-disks/).


## <a name="next-steps"></a>Nächste Schritte

- Bevor Sie eine VHD in Azure hochladen, befolgen Sie die Anweisungen unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](prepare-for-upload-vhd-image.md).