---
title: Verwenden von kurzlebigen Betriebssystemdatenträger-Knoten für Azure Batch-Pools
description: Es wird beschrieben, wie Sie einen Batch-Pool erstellen, für den kurzlebige Betriebssystemdatenträger-Knoten verwendet werden, und warum dies erforderlich ist.
ms.topic: how-to
ms.date: 09/03/2021
ms.openlocfilehash: 760e2848917c6b1c502ac6ba9aae00d5ddd89c8c
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123544409"
---
# <a name="use-ephemeral-os-disk-nodes-for-azure-batch-pools"></a>Verwenden von kurzlebigen Betriebssystemdatenträger-Knoten für Azure Batch-Pools

Für einige Serien virtueller Azure-Computer (VMs) wird die Verwendung von [kurzlebigen Betriebssystemdatenträgern](../virtual-machines/ephemeral-os-disks.md) unterstützt, bei denen der Betriebssystemdatenträger im lokalen Speicher der VM eines Knotens erstellt wird. In der Standardkonfiguration für Batch-Pools werden [verwaltete Azure-Datenträger](../virtual-machines/managed-disks-overview.md) für den Betriebssystemdatenträger des Knotens verwendet. Hierbei verhält sich der verwaltete Datenträger wie ein physischer Datenträger, aber er ist im Azure Storage-Remotespeicher virtualisiert und dauerhaft angelegt.

Bei Batch-Workloads bestehen die Hauptvorteile der Verwendung von kurzlebigen Betriebssystemdatenträgern darin, dass für Pools geringere Kosten anfallen, kürzere Startzeiten für Knoten möglich sind und aufgrund einer höheren Leistung von Betriebssystemdatenträgern eine verbesserte Anwendungsleistung erzielt werden kann. Berücksichtigen Sie Folgendes beim Treffen der Auswahl, ob kurzlebige Betriebssystemdatenträger für Ihre Workload verwendet werden sollen:

- Bei kurzlebigen Betriebssystemdatenträgern ist die Lese-/Schreiblatenz geringer, und dies kann zu einer verbesserten Anwendungsleistung führen.
- Für kurzlebige Betriebssystemdatenträger fallen keine Speicherkosten an, wohingegen dies für jeden verwalteten Betriebssystemdatenträger der Fall ist.
- Ein Reimaging des Knotens – sofern dieser Vorgang von Batch unterstützt wird – kann für kurzlebige Datenträger schneller als für verwaltete Datenträger durchgeführt werden.
- Die Startzeit des Knotens ist ggf. etwas kürzer, wenn kurzlebige Betriebssystemdatenträger verwendet werden.
- Kurzlebige Betriebssystemdatenträger verfügen nicht über eine hohe Dauerhaftigkeit und Verfügbarkeit. Wenn eine VM aus irgendeinem Grund entfernt wird, geht der Betriebssystemdatenträger verloren. Da Batch-Workloads grundsätzlich zustandslos sind und es normalerweise nicht erforderlich ist, dass Änderungen am Betriebssystemdatenträger dauerhaft gespeichert werden, sind kurzlebige Betriebssystemdatenträger für die meisten Batch-Workloads geeignet.
- Die Verwendung eines kurzlebigen Betriebssystemdatenträgers wird derzeit nicht von allen Azure-VM-Serien unterstützt. Wenn ein kurzlebiger Betriebssystemdatenträger für eine bestimmte VM-Größe nicht unterstützt wird, muss ein verwalteter Betriebssystemdatenträger verwendet werden.

> [!NOTE]
> Die Konfiguration eines kurzlebigen Betriebssystemdatenträgers gilt nur für Pools vom Typ „virtualMachineConfiguration“ und wird von Pools vom Typ „cloudServiceConfiguration“ nicht unterstützt. Wir empfehlen Ihnen, „virtualMachineConfiguration“ für Ihre Batch-Pools zu verwenden, da für Pools vom Typ „cloudServiceConfiguration“ nicht alle Features unterstützt werden und keine neuen Funktionen geplant sind. Sie können [nach dem 29. Februar 2024](https://azure.microsoft.com/updates/azure-batch-cloudserviceconfiguration-pools-will-be-retired-on-29-february-2024/) weder neue cloudServiceConfiguration-Pools erstellen noch vorhandenen Pools neue Knoten hinzuzufügen. Weitere Informationen finden Sie unter [Migrieren der Batch-Poolkonfiguration von Cloud Services zu Virtual Machines](batch-pool-cloud-service-to-virtual-machine-configuration.md).

## <a name="vm-series-support"></a>Unterstützung bei VM-Serien

Informationen dazu, ob eine VM-Serie kurzlebige Betriebssystemdatenträger unterstützt, finden Sie in der Dokumentation zur jeweiligen VM-Instanz. Für die [Serien Ddv4 und Ddsv4](../virtual-machines/ddv4-ddsv4-series.md) werden kurzlebige Betriebssystemdatenträger beispielsweise unterstützt.

Alternativ können Sie die Funktion „EphemeralOSDiskSupported“ programmgesteuert abfragen. Ein Beispiel für ein PowerShell-Cmdlet zum Abfragen dieser Funktion finden Sie unter [Kurzlebige Betriebssystemdatenträger für virtuelle Azure-Computer: Häufig gestellte Fragen](../virtual-machines/ephemeral-os-disks.md#frequently-asked-questions).

## <a name="create-a-pool-that-uses-ephemeral-os-disks"></a>Erstellen eines Pools mit kurzlebigen Betriebssystemdatenträgern

Die Eigenschaft `EphemeralOSDiskSettings` ist standardmäßig nicht festgelegt. Sie müssen diese Eigenschaft festlegen, um die Verwendung von kurzlebigen Betriebssystemdatenträgern auf den Poolknoten konfigurieren zu können.

Im folgenden Beispiel wird veranschaulicht, wie Sie einen Batch-Pool erstellen, in dem die Knoten kurzlebige Betriebssystemdatenträger und keine verwalteten Datenträger verwenden.

### <a name="batch-net-api"></a>Batch .NET API

```csharp
VirtualMachineConfiguration virtualMachineConfiguration = new VirtualMachineConfiguration(
        imageReference: imageReference,
        nodeAgentSkuId: nodeAgentSku
        );
virtualMachineConfiguration.OSDisk = new OSDisk();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings = new DiffDiskSettings();
virtualMachineConfiguration.OSDisk.EphemeralOSDiskSettings.Placement = DiffDiskPlacement.CacheDisk;
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Informieren Sie sich über die [Kosten, die für Azure Batch-Workloads ggf. anfallen können](budget.md).
