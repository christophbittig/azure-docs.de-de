---
title: Überwachen des Azure Stack Edge-Geräts | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie über das Azure-Portal und die lokale Webbenutzeroberfläche Ihr Azure Stack Edge-Gerät überwachen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/11/2021
ms.author: alkohli
ms.openlocfilehash: 84ffadea8e8b6980b7bf311db9d2b7a2a36748b6
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130047875"
---
# <a name="monitor-your-azure-stack-edge-device"></a>Überwachen Ihres Azure Stack Edge-Geräts

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

In diesem Artikel wird beschrieben, wie Sie Ihr Azure Stack Edge-Gerät überwachen. Zum Überwachen Ihres Geräts können Sie das Azure-Portal oder die lokale Webbenutzeroberfläche verwenden. Verwenden Sie das Azure-Portal, um Metriken sowie Geräteereignisse anzuzeigen und Warnungen zu konfigurieren und zu verwalten. Verwenden Sie die lokale Webbenutzeroberfläche auf Ihrem physischen Gerät, um den Hardwarestatus der verschiedenen Gerätekomponenten anzuzeigen.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
>
> * Anzeigen von Kapazitäts- und Transaktionsmetriken für Ihr Gerät
> * Anzeigen des Hardwarestatus von Gerätekomponenten


## <a name="view-metrics"></a>Anzeigen von Metriken

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metriken auf Ihrem Gerät

In diesem Abschnitt werden die Überwachungsmetriken auf Ihrem Gerät beschrieben. Folgende Metriken sind möglich:

* Kapazitätsmetriken. Die Kapazitätsmetriken beziehen sich auf die Kapazität des Geräts.

* Transaktionsmetriken. Die Transaktionsmetriken beziehen sich auf die Lese- und Schreibvorgänge in Azure Storage.

* Edgecomputingmetriken. Die Edgecomputingmetriken beziehen sich auf die Verwendung von Edgecomputing auf Ihrem Gerät.

Eine vollständige Liste der Metriken enthält die folgende Tabelle:

|Kapazitätsmetriken                     |BESCHREIBUNG  |
|-------------------------------------|-------------|
|**Verfügbare Kapazität**               | Bezieht sich auf die Menge der Daten, die auf das Gerät geschrieben werden können. Das bedeutet, dass diese Metrik die Kapazität ist, die auf dem Gerät zur Verfügung gestellt werden kann. <br></br>Sie können die Gerätekapazität freigeben, indem Sie die lokale Kopie von Dateien löschen, die sowohl auf dem Gerät als auch in der Cloud über eine Kopie verfügen.        |
|**Gesamtkapazität**                   | Bezieht sich auf die Gesamtanzahl der Bytes auf dem Gerät, in die Daten geschrieben werden sollen, was auch als Gesamtgröße des lokalen Caches bezeichnet wird. <br></br> Sie können jetzt die Kapazität eines vorhandenen virtuellen Geräts erhöhen, indem Sie einen Datenträger hinzufügen. Fügen Sie einen Datenträger über die Hypervisor-Verwaltung für den virtuellen Computer hinzu, und starten Sie den virtuellen Computer neu. Der lokale Speicherpool des Gatewaygeräts wird erweitert, um den neu hinzugefügten Datenträger aufzunehmen. <br></br>Weitere Informationen finden Sie unter [Hinzufügen einer Festplatte für virtuelle Hyper-V-Computer](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Transaktionsmetriken              | BESCHREIBUNG         |
|-------------------------------------|---------|
|**Hochgeladene Cloudbytes (Gerät)**    | Summe aller Bytes, die über alle Freigaben auf Ihrem Gerät hochgeladen wurden        |
|**Hochgeladene Cloudbytes (Freigabe)**     | Hochgeladene Bytes pro Freigabe Diese Metrik kann folgende Werte aufweisen: <br></br> Avg, d. h. (Summe aller hochgeladenen Bytes pro Freigabe/Anzahl der Freigaben)  <br></br>Max, d. h. die maximale Anzahl von Bytes, die von einer Freigabe hochgeladen wurden <br></br>Min, d. h. die minimale Anzahl von Bytes, die von einer Freigabe hochgeladen wurden      |
|**Clouddownloaddurchsatz (Freigabe)**| Heruntergeladene Bytes pro Freigabe Diese Metrik kann folgende Werte aufweisen: <br></br> Avg, d. h. (Summe aller von einer Freigabe gelesenen oder heruntergeladenen Bytes/Anzahl der Freigaben) <br></br> Max, d. h. die maximale Anzahl von Bytes, die von einer Freigabe heruntergeladen wurden<br></br> Min, d. h. die minimale Anzahl von Bytes, die von einer Freigabe heruntergeladen wurden  |
|**Cloudlesedurchsatz**            | Summe aller Bytes, die über alle Freigaben auf Ihrem Gerät aus der Cloud gelesen wurden     |
|**Clouduploaddurchsatz**          | Summe aller Bytes, die über alle Freigaben auf Ihrem Gerät in die Cloud geschrieben wurden     |
|**Clouduploaddurchsatz (Freigabe)**  | Summe aller Bytes, die von einer Freigabe in die Cloud geschrieben wurden/Anzahl der Freigaben ist durchschnittlich, maximal und minimal pro Freigabe      |
|**Lesedurchsatz (Netzwerk)**           | Enthält den Systemnetzwerkdurchsatz für alle aus der Cloud gelesenen Bytes Diese Ansicht kann Daten enthalten, die nicht auf Freigaben beschränkt sind. <br></br>Bei der Aufteilung wird der Datenverkehr über alle Netzwerkadapter auf dem Gerät angezeigt, einschließlich der Adapter, die nicht verbunden sind oder aktiviert wurden.      |
|**Schreibdurchsatz (Netzwerk)**       | Enthält den Systemnetzwerkdurchsatz für alle in die Cloud geschriebenen Bytes Diese Ansicht kann Daten enthalten, die nicht auf Freigaben beschränkt sind. <br></br>Bei der Aufteilung wird der Datenverkehr über alle Netzwerkadapter auf dem Gerät angezeigt, einschließlich der Adapter, die nicht verbunden sind oder aktiviert wurden.          |

| Edgecomputingmetriken              | BESCHREIBUNG         |
|-------------------------------------|---------|
|**Edgecomputing – Arbeitsspeichernutzung**      |           |
|**Edgecomputing – CPU in Prozent**    |         |


### <a name="view-device-events"></a>Anzeigen von Geräteereignissen

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]


## <a name="view-hardware-status"></a>Anzeigen des Hardwarestatus

Führen Sie die folgenden Schritte auf der lokalen Webbenutzeroberfläche aus, um den Hardwarestatus Ihrer Gerätekomponenten anzuzeigen.

1. Stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche Ihres Geräts her.
2. Navigieren Sie zu **Wartung > Hardwarestatus**. Sie können die Integrität der verschiedenen Gerätekomponenten anzeigen.

    ![Anzeigen des Hardwarestatus](media/azure-stack-edge-monitor/view-hardware-status.png)


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Bandbreite verwalten](azure-stack-edge-manage-bandwidth-schedules.md).
- Hier erfahren Sie, wie Sie [Warnungsbenachrichtigungen verwalten](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).