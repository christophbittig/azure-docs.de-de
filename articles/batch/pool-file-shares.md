---
title: Azure-Dateifreigabe für Azure Batch-Pools
description: Hier erfahren Sie, wie Sie eine Azure Files-Freigabe von Computeknoten in einen Linux- oder Windows-Pool in Azure Batch einbinden.
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: 2c5cbf1a3e5d74927ddc74c4838c31f68a348876
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772114"
---
# <a name="use-an-azure-file-share-with-a-batch-pool"></a>Verwenden einer Azure-Dateifreigabe mit einem Batch-Pool

[Azure Files](../storage/files/storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Protokoll Server Message Block (SMB) zugegriffen werden kann. Sie können eine Azure-Dateifreigabe in einen Batch-Pool mit Computeknoten einbinden und für diesen verwenden.

## <a name="considerations-for-use-with-batch"></a>Überlegungen für die Verwendung mit Batch

Wenn Sie über Pools verfügen, die eine verhältnismäßig geringe Anzahl von parallelen Tasks ausführen, sollten Sie bei Verwendung einer Nicht-Premium-Instanz von Azure Files den Einsatz einer Azure-Dateifreigabe in Erwägung ziehen. Sehen Sie sich die [Skalierbarkeits- und Leistungsziele](../storage/files/storage-files-scale-targets.md) an, um anhand der erwarteten Poolgröße und der erwarteten Anzahl von Ressourcendateien zu ermitteln, ob Sie Azure Files verwenden sollten (wofür ein Azure Storage-Konto benötigt wird).

Azure-Dateifreigaben sind [kosteneffizient](https://azure.microsoft.com/pricing/details/storage/files/) und können für die Datenreplikation in eine andere Region konfiguriert werden, sodass sie global redundant sind.

Sie können eine Azure-Dateifreigabe parallel von einem lokalen Computer aus einbinden. Stellen Sie jedoch sicher, dass Sie insbesondere bei Verwendung von REST-APIs die [Auswirkungen auf die Parallelität](../storage/blobs/concurrency-manage.md) verstehen.

Sehen Sie sich auch die allgemeinen [Planungsüberlegungen](../storage/files/storage-files-planning.md) für Azure-Dateifreigaben an.

## <a name="create-a-file-share"></a>Erstellen einer Dateifreigabe

Sie können eine Azure-Dateifreigabe in einem mit Ihrem Batch-Konto verknüpften Speicherkonto oder in einem separaten Speicherkonto erstellen. Weitere Informationen finden Sie unter [Erstellen einer Azure-Dateifreigabe](../storage/files/storage-how-to-create-file-share.md).

## <a name="mount-an-azure-file-share-on-a-batch-pool"></a>Einbinden einer Azure-Dateifreigabe in einen Batch-Pool

Ausführliche Informationen zum Einbinden einer Azure-Dateifreigabe in einen Pool finden Sie unter [Einbinden eines virtuellen Dateisystems in einen Batch-Pool](virtual-file-mount.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Optionen zum Lesen und Schreiben von Daten in Batch finden Sie unter [Persistente Aufträge und Aufgabenausgabe](batch-task-output.md).
- Erkunden Sie das [Batch Shipyard](https://github.com/Azure/batch-shipyard)-Toolkit, das [Shipyard-Rezepte](https://github.com/Azure/batch-shipyard/tree/master/recipes) zum Bereitstellen von Dateisystemen für Batch-Containerworkloads enthält.
