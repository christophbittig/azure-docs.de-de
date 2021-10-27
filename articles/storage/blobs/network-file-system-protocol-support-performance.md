---
title: Überlegungen zur NFS 3.0-Leistung in Azure Blob Storage | Microsoft-Dokumentation
description: Optimieren Sie mit den Empfehlungen in diesem Artikel die Leistung Ihrer NFS 3.0-Speicheranforderungen (Network File System).
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 06/21/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 1268df1aaf095fd6a965b447d48a9ef4978325d7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130046884"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage"></a>Überlegungen zur NFS 3.0-Leistung (Network File System) in Azure Blob Storage

Blob Storage unterstützt jetzt das NFS 3.0-Protokoll (Network File System). Dieser Artikel enthält Empfehlungen, mit deren Hilfe Sie die Leistung Ihrer Speicheranforderungen optimieren können. Weitere Informationen zur Unterstützung von NFS 3.0 in Azure Blob Storage finden Sie unter [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage](network-file-system-protocol-support.md).

## <a name="add-clients-to-increase-throughput"></a>Hinzufügen von Clients zum Erhöhen des Durchsatzes

Azure Blob Storage wird linear skaliert, bis der Maximalwert für eingehende und ausgehende Daten für das Speicherkonto erreicht ist. Daher können Ihre Anwendungen durch Verwenden von mehr Clients einen höheren Durchsatz erzielen. Die Maximalwerte für eingehende und ausgehende Daten für das Speicherkonto finden Sie unter [Skalierbarkeits- und Leistungsziele für Standardspeicherkonten](../common/scalability-targets-standard-account.md).

Das folgende Diagramm zeigt, wie die Bandbreite zunimmt, wenn Sie mehr Clients hinzufügen. In diesem Diagramm handelt es sich bei einem Client um einen virtuellen Computer (VM) mit einem Speicherkonto vom Standardspeicher vom Typ „Standard, Universell V2“.

> [!div class="mx-imgBorder"]
> ![Standardleistung](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

Das folgende Diagramm zeigt denselben Effekt bei Anwendung auf ein Premium-Blockblobspeicherkonto.

> [!div class="mx-imgBorder"]
> ![Premium-Leistung](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-block-blob-storage-accounts-for-small-scale-applications"></a>Verwenden von Premium-Blockblobspeicherkonten für kleine Anwendungen

Nicht alle Anwendungen lassen sich durch Hinzufügen von mehr Clients hochskalieren. Für solche Anwendungen bietet das [Azure-Premium-Blockblob-Speicherkonto](../common/storage-account-create.md) konsistente hohe Transaktionsraten mit niedriger Latenz. Das Premium-Blockblob-Speicherkonto kann die maximale Bandbreite mit weniger Threads und Clients erreichen. Ein Beispiel: Mit einem einzelnen Client kann ein Premium-Blockblob-Speicherkonto **2,3-mal** so viel Bandbreite erreichen wie dasselbe Setup mit einem Speicherkonto vom Typ „Universell v2“ mit Standardleistung.

Jeder Balken im folgenden Diagramm zeigt den Unterschied in der erreichten Bandbreite zwischen Speicherkonten mit der Leistungsstufe „Premium“ und „Standard“. In dem Maß, in dem die Anzahl von Clients steigt, verringert sich der Unterschied.

> [!div class="mx-imgBorder"]
> ![Relative Leistung](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="improve-read-ahead-size-to-increase-large-file-read-throughput"></a>Optimieren der Vorauslesegröße zur Verbesserung des Lesedurchsatzes bei großen Dateien

Mit dem Kernelparameter „read_ahead_kb“ wird die Menge der zusätzlichen Daten angegeben, die nach Erfüllung einer bestimmten Leseanforderung gelesen werden sollen. Sie können den Wert für diesen Parameter auf 16 MB erhöhen, um den Lesedurchsatz bei großen Dateien zu verbessern.

```
export AZMNT=/your/container/mountpoint

echo 15728640 > /sys/class/bdi/0:$(stat -c "%d" $AZMNT)/read_ahead_kb
```

## <a name="avoid-frequent-overwrites-on-data"></a>Vermeiden von häufigem Überschreiben der Daten

Ein Überschreibungsvorgang dauert länger als ein neuer Schreibvorgang. Das liegt daran, dass ein NFS-Überschreibungsvorgang – insbesondere eine direkte Dateiteilbearbeitung – eine Kombination aus mehreren zugrunde liegenden Blobvorgängen ist: ein Lesevorgang, ein Änderungsvorgang und ein Schreibvorgang. Aus diesem Grund ist eine Anwendung, die häufige direkte Bearbeitungen erfordert, für NFS-fähige Blobspeicherkonten nicht geeignet.

## <a name="deploy-azure-hpc-cache-for-latency-sensitive-applications"></a>Bereitstellen von Azure HPC Cache für Anwendungen, bei denen die Latenz eine wichtige Rolle spielt

Einige Anwendungen erfordern möglicherweise zusätzlich zu hohem Durchsatz eine niedrige Latenz. Sie können [Azure HPC Cache](../../hpc-cache/nfs-blob-considerations.md) bereitstellen, um die Latenz erheblich zu verbessern. Weitere Informationen finden Sie unter [Latenz im Blobspeicher](storage-blobs-latency.md).

## <a name="other-best-practice-recommendations"></a>Weitere Empfehlungen zu Best Practices

- Verwenden Sie VMs mit ausreichender Netzwerkbandbreite.

- Verwenden Sie mehrere Bereitstellungspunkte, wenn Ihre Workloads dies zulassen.

- Verwenden Sie so viele Threads wie möglich.

- Verwenden Sie große Blöcke.

- Führen Sie Speicheranforderungen von einem Client aus, der sich in derselben Region befindet wie das Speicherkonto. Damit lässt sich die Netzwerklatenz verbessern.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Unterstützung von NFS 3.0 in Azure Blob Storage finden Sie unter [Unterstützung für Network File System 3.0 (NFS) in Azure Blob Storage](network-file-system-protocol-support.md).

- Informationen zu den ersten Schritten finden Sie unter [Einbinden von Blob-Speicher mithilfe des NFS 3.0-Protokolls (Network File System)](network-file-system-protocol-support-how-to.md).
