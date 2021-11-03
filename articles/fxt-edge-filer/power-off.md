---
title: Herunterfahren einer Microsoft Azure FXT Edge Filer-Einheit
description: Lernen Sie die Prozeduren für den Start und das sichere Herunterfahren eines Azure FXT Edge Filer-Knotens mithilfe der Systemsteuerungssoftware des Clusters kennen.
author: femila
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: femila
ms.openlocfilehash: c3b9f9c317de7ff1bdf2801d1348769882cb777b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024237"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Sicheres Ausschalten von Azure FXT Edge Filer-Hardware

Sie können zwar den physischen Netzschalter verwenden, um einen einzelnen Knoten einzuschalten, aber Sie sollten ihn im Normalfall nicht nutzen, um die Einheit herunterzufahren.

Sobald ein Azure FXT Edge Filer-Knoten als Teil eines Clusters verwendet wird, sollte die Hardware über die Systemsteuerungssoftware des Clusters heruntergefahren werden.

> [!NOTE]
> Verwenden Sie zur Vermeidung von Datenverlust oder -beschädigungen immer die Systemsteuerungssoftware, um einen Azure FXT Edge Filer herunterzufahren. Nutzen Sie den physischen Netzschalter nur dann zum Herunterfahren, wenn Sie vom Microsoft-Kundendienst und -Support (CSS) dazu aufgefordert werden.
>
> Ziehen Sie in einem elektrischen Notfall die Netzkabel ab, oder verwenden Sie den Mechanismus, der in Ihrem Rechenzentrum für das Schalten in den stromlosen Zustand vorgesehen ist.

## <a name="shut-down-a-node-from-the-control-panel"></a>Herunterfahren eines Knotens über die Systemsteuerung

Befolgen Sie diese Anleitung, um einen Azure FXT Edge Filer-Knoten sicher auszuschalten:

1. Melden Sie sich an der Systemsteuerung des Clusters an. (Anleitung unter [Öffnen der Einstellungsseiten](cluster-create.md#open-the-settings-pages))
1. Klicken Sie auf die Registerkarte **Einstellungen**, und laden Sie anschließend die Seite **Cluster** > **FXT Nodes** (FXT-Knoten).
1. Suchen Sie in der Liste mit den Clusterknoten nach dem Knoten, den Sie herunterfahren möchten. Klicken Sie in der zugehörigen Spalte **Aktionen** auf die Schaltfläche **Ausschalten**.
1. Warten Sie einen Moment. Der Knoten wird heruntergefahren und schaltet sich selbst aus.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den Status-LEDs und anderen Anzeigen finden Sie unter [Überwachen des Azure FXT Edge Filer-Hardwarestatus](monitor.md).
* Informieren Sie sich unter [Anschließen der Netzkabel](network-power.md#connect-power-cables) weiter über die Stromversorgung für Azure FXT Edge Filer.
