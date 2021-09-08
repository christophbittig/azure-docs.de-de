---
title: 'Tutorial: Bestellen von Azure Data Box Heavy | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie mehr über die Hybridlösung Azure Data Box Heavy, mit der Sie lokale Daten in Azure importieren können. Darüber hinaus wird erläutert, wie Sie Data Box Heavy bestellen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: alkohli
ms.localizationpriority: high
ms.custom: contperf-fy22q1
ms.openlocfilehash: d5334314626d29dc9e3047bc382d41fcfa318a1d
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469516"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Tutorial: Bestellen von Azure Data Box Heavy


Azure Data Box Heavy ist eine Hybridlösung, mit der Sie Ihre lokalen Daten auf schnelle, einfache und zuverlässige Weise in Azure importieren können. Sie übertragen Ihre Daten auf ein von Microsoft bereitgestelltes Speichergerät mit einer nutzbaren Kapazität von etwa 770 TB und senden es anschließend an Microsoft zurück. Diese Daten werden dann in Azure hochgeladen.

In diesem Tutorial erfahren Sie, wie Sie ein Azure Data Box Heavy-Gerät bestellen. In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
> * Voraussetzungen für Data Box Heavy
> * Bestellen eines Data Box Heavy-Geräts
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie das Gerät bereitstellen, müssen die folgenden Konfigurationsvoraussetzungen für den Data Box-Dienst und das Data Box-Gerät erfüllt sein.

### <a name="for-installation-site"></a>Aufstellungsort

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Gerät passt durch normale Türen und Zugänge. Vergewissern Sie sich jedoch, dass das Gerät durch alle Ihre Zugänge passt. Abmessungen des Geräts: Breite: 66,04 cm, Länge: 121,92 cm, Höhe: 71,12 cm.
- Falls sich der Aufstellungsort des Geräts nicht im Erdgeschoss befindet, muss das gewünschte Stockwerk über einen Aufzug oder über eine Rampe erreichbar sein. Das Gerät wiegt rund 227 kg,
- In Ihrem Datencenter muss ein ebener Aufstellungsort mit einer Netzwerkverbindung verfügbar sein, der genügend Platz für ein Gerät dieser Größe bietet.

### <a name="for-service"></a>Für den Dienst

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>Für das Gerät

Stellen Sie Folgendes sicher, bevor Sie beginnen:
- Das Gerät ist ausgepackt.
- Sie verfügen über einen Hostcomputer, der mit dem Netzwerk des Datencenters verbunden ist. Die Data Box Heavy kopiert die Daten von diesem Computer. Auf dem Hostcomputer muss ein unterstütztes Betriebssystem ausgeführt werden (siehe [Systemanforderungen für Azure Data Box](data-box-system-requirements.md)).
- Sie benötigen einen Laptop und ein RJ45-Kabel, um eine Verbindung mit der lokalen Benutzeroberfläche herstellen und das Gerät konfigurieren zu können. Mithilfe des Laptops muss jeder Knoten des Geräts einmalig konfiguriert werden.
- Ihr Datencenter verfügt über ein Hochgeschwindigkeitsnetzwerk. Mindestens eine 10-GbE-Verbindung wird dringend empfohlen.
- Für jeden Geräteknoten wird ein 40-GBit/s- oder 10-GBit/s-Kabel benötigt. Die Kabel müssen mit der Netzwerkschnittstelle [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) kompatibel sein:

    - Bei 40-GBit/s-Kabeln muss ein Kabelende vom Typ QSFP+ verwendet werden.
    - Bei 10-GBit/s-Kabeln benötigen Sie ein SFP+-Kabel, das auf der einen Seite mit einem 10-G-Switch verbunden ist und an dem Ende, das mit dem Gerät verbunden wird, über einen QSFP+-auf-SFP+-Adapter (oder über den QSA-Adapter) verfügt.
    - Die Netzkabel sind im Lieferumfang des Geräts enthalten.

## <a name="order-data-box-heavy"></a>Bestellen von Data Box Heavy

[!INCLUDE [order-data-box-via-portal](../../includes/data-box-order-portal.md)]

## <a name="track-the-order"></a>Nachverfolgen der Bestellung

Nachdem Sie die Bestellung aufgegeben haben, können Sie ihren Status im Azure-Portal nachverfolgen. Navigieren Sie zu Ihrer Data Box Heavy-Bestellung und anschließend zu **Übersicht**, um den Status anzuzeigen. Die Bestellung wird im Portal mit dem Status **Bestellt** angezeigt.

Sollte das Gerät nicht verfügbar sein, erhalten Sie eine entsprechende Benachrichtigung. Wenn das Gerät verfügbar ist, identifiziert Microsoft das Gerät für den Versand und bereitet den Versand vor. Während der Vorbereitung des Geräts werden folgende Aktionen ausgeführt:

- Für jedes mit dem Gerät verknüpfte Speicherkonto werden SMB-Freigaben erstellt.
- Für jede Freigabe werden Anmeldeinformationen wie Benutzername und Kennwort generiert.
- Ein Gerätekennwort zum Entsperren des Geräts wird ebenfalls generiert.
- Data Box Heavy wird gesperrt, um nicht autorisierte Zugriffe auf das Gerät zu verhindern.

Wenn die Vorbereitung des Geräts abgeschlossen ist, wird die Bestellung im Portal mit dem Status **Verarbeitet** angezeigt.

![Verarbeitete Data Box Heavy-Bestellung](media/data-box-overview/data-box-order-status-processed.png)

Microsoft bereitet dann Ihr Gerät vor und versendet es über einen regionalen Kurierdienst. Sobald das Gerät versandt wurde, erhalten Sie eine Nachverfolgungsnummer. Das Portal zeigt die Bestellung im Zustand **Versandt** an.

![Versendete Data Box Heavy-Bestellung](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Stornieren der Bestellung

Um die Bestellung zu stornieren, navigieren Sie im Azure-Portal zu **Übersicht**, und klicken Sie dann auf der Befehlsleiste auf **Stornieren**.

Solange eine Bestellung noch nicht mit dem Status „Verarbeitet“ angezeigt wird, können Sie sie jederzeit stornieren.
 
Um eine stornierte Bestellung zu löschen, navigieren Sie zu **Übersicht** und klicken dann auf der Befehlsleiste auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Informationen zu Azure Data Box Heavy vermittelt:

> [!div class="checklist"]
> * Voraussetzungen
> * Bestellen von Data Box Heavy
> * Nachverfolgen der Bestellung
> * Stornieren der Bestellung

Im nächsten Tutorial erfahren Sie, wie Sie Data Box Heavy einrichten.

> [!div class="nextstepaction"]
> [Verkabeln und Herstellen der Verbindung mit Azure Data Box Heavy (Vorschauversion)](./data-box-heavy-deploy-set-up.md)
