---
title: 'Tutorial: Manuelles Failover einer Azure IoT Hub-Instanz | Microsoft-Dokumentation'
description: 'Tutorial: Hier wird beschrieben, wie Sie ein manuelles Failover für Ihren IoT-Hub auf eine andere Region ausführen und die Funktionsweise überprüfen und anschließend die Verlagerung zurück in die ursprüngliche Region und eine erneute Überprüfung durchführen.'
author: eross-msft
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 08/10/2021
ms.author: lizross
ms.custom:
- mvc
- mqtt
ms.openlocfilehash: 2687316551f7f1a7f11cd035130823c19d10b01a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550437"
---
# <a name="tutorial-perform-manual-failover-for-an-iot-hub"></a>Tutorial: Ausführen eines manuellen Failovers für eine IoT Hub-Instanz

Das manuelle Failover ist ein Feature des IoT Hub-Diensts, der Kunden die Ausführung eines [Failovers](https://en.wikipedia.org/wiki/Failover) für die Hubvorgänge einer primären Region in der entsprechenden geografisch gekoppelten Azure-Region ermöglicht. Das manuelle Failover kann im Falle eines regionalen Notfalls oder eines längeren Dienstausfalls ausgeführt werden. Sie können auch ein geplantes Failover zum Testen der Notfallwiederherstellungsfunktionen ausführen. Es wird jedoch empfohlen, einen IoT-Testhub anstelle einer in der Produktion ausgeführten Instanz zu verwenden. Das Feature für das manuelle Failover steht Kunden ohne zusätzliche Kosten für IoT-Hubs zur Verfügung, die nach dem 18. Mai 2017 erstellt wurden.

In diesem Tutorial führen Sie die folgenden Aufgaben aus:

> [!div class="checklist"]
> * Erstellen eines IoT-Hubs über das Azure-Portal 
> * Ausführen eines Failovers. 
> * Anzeigen der Hubausführung am sekundären Standort
> * Ausführen eines Failbacks, um die Vorgänge des IoT-Hubs am primären Standort wiederherzustellen 
> * Überprüfen, ob der Hub ordnungsgemäß am richtigen Standort ausgeführt wird

Weitere Informationen zum manuellen Failover und einem von Microsoft initiierten Failover mit IoT Hub finden Sie unter [Regionenübergreifende Notfallwiederherstellung](iot-hub-ha-dr.md#cross-region-dr).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Stellen Sie sicher, dass Port 8883 in Ihrer Firewall geöffnet ist. Für das Beispielgerät in diesem Tutorial wird das MQTT-Protokoll verwendet, das über Port 8883 kommuniziert. In einigen Netzwerkumgebungen von Unternehmen oder Bildungseinrichtungen ist dieser Port unter Umständen blockiert. Weitere Informationen und Problemumgehungen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Erstellen eines IoT-Hubs

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="perform-a-manual-failover"></a>Ausführen eines manuellen Failovers

> [!NOTE]
> Bei einem IoT-Hub können pro Tag maximal zwei Failover und zwei Failbacks ausgeführt werden.

1. Klicken Sie auf **Ressourcengruppen**, und wählen Sie dann Ihre Ressourcengruppe aus. Klicken Sie in der Liste der Ressourcen auf Ihren Hub.

1. Klicken Sie im Bereich „IoT Hub“ unter **Hubeinstellungen** auf **Failover**.

:::image type="content" source="./media/tutorial-manual-failover/trigger-failover-01.png" alt-text="Screenshot mit dem Bereich „Eigenschaften“ der IoT Hub-Instanz":::

1. Im Bereich „Manuelles Failover“ werden die Optionen **Aktueller Standort** und **Failover location** (Failoverstandort) angezeigt. Der aktuelle Standort gibt immer den Standort an, an dem der Hub derzeit aktiv ist. Der Failoverstandort ist die standardmäßige [geografisch gekoppelte Azure-Region](../best-practices-availability-paired-regions.md), die mit dem aktuellen Standort gekoppelt ist. Die Standortwerte können nicht geändert werden. In diesem Tutorial ist der aktuelle Standort `West US 2` und der Failoverstandort `West Central US`.

   ![Screenshot mit dem Bereich „Manuelles Failover“](./media/tutorial-manual-failover/trigger-failover-02.png)

1. Klicken Sie oben im Bereich „Manuelles Failover“ auf **Failover starten**. 

1. Geben Sie im Bereich für die Bestätigung den Namen Ihres IoT-Hubs ein, um zu bestätigen, dass für ihn das Failover ausgeführt werden soll. Klicken Sie anschließend zum Initiieren des Failovers auf **Failover**.

   Die Dauer für die Ausführung des manuellen Failovers ist proportional zur Anzahl von Geräten, die für Ihren Hub registriert sind. Wenn Sie beispielsweise 100.000 Geräte besitzen, dauert die Ausführung vielleicht 15 Minuten. Bei fünf Millionen Geräten kann sie jedoch eine Stunde oder länger dauern.

   ![Screenshot mit dem Bestätigungsbereich „Manuelles Failover“](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Während des manuellen Failovers wird ein Banner mit dem Hinweis angezeigt, dass ein manuelles Failover ausgeführt wird. 

   ![Screenshot mit dem Hinweis, dass gerade ein manuelles Failover ausgeführt wird](./media/tutorial-manual-failover/trigger-failover-04-in-progress.png)

   Wenn Sie den IoT Hub-Bereich schließen und wieder öffnen, indem Sie im Bereich „Ressourcengruppe“ auf den Hub klicken, wird ein Banner mit dem Hinweis angezeigt, dass für den Hub gerade ein manuelles Failover ausgeführt wird. 

   ![Screenshot mit dem Hinweis, dass gerade ein IoT Hub-Failover ausgeführt wird](./media/tutorial-manual-failover/trigger-failover-05-hub-inactive.png)

   Nach Abschluss der Ausführung werden die aktuelle Region und die Failoverregion auf der Seite „Manuelles Failover“ getauscht, und der Hub ist wieder aktiv. In diesem Beispiel ist jetzt der aktuelle Standort `WestCentralUS` und der Failoverstandort `West US 2`. 

   ![Screenshot mit abgeschlossenem Failover](./media/tutorial-manual-failover/trigger-failover-06-finished.png)

   Auf der Übersichtsseite wird auch ein Banner mit dem Hinweis angezeigt, dass das Failover abgeschlossen ist und die IoT Hub-Instanz in `West Central US` ausgeführt wird.

   ![Screenshot mit abgeschlossenem Failover auf der Übersichtsseite](./media/tutorial-manual-failover/trigger-failover-06-finished-overview.png)


## <a name="perform-a-failback"></a>Ein Failback durchführen 

Nach der Ausführung eines manuellen Failovers können Sie die Vorgänge des Hubs wieder in der ursprünglichen primären Region auszuführen: Dieser Schritt wird Failback genannt. Falls Sie soeben ein Failover ausgeführt haben, müssen Sie bis zur Anforderung eines Failbacks eine Stunde warten. Wenn Sie vorher versuchen, das Failback auszuführen, wird eine Fehlermeldung angezeigt.

Ein Failback wird genau wie ein manuelles Failover ausgeführt. Hier sind die Schritte aufgeführt: 

1. Kehren Sie zum Ausführen eines Failbacks zum Iot Hub-Bereich für Ihren IoT-Hub zurück.

2. Klicken Sie im Bereich „IoT Hub“ unter **Einstellungen** auf **Failover**. 

3. Klicken Sie oben im Bereich „Manuelles Failover“ auf **Failover starten**. 

4. Geben Sie im Bereich für die Bestätigung den Namen Ihres IoT-Hubs ein, um zu bestätigen, dass für ihn das Failback ausgeführt werden soll. Klicken Sie dann auf **OK**, um das Failback zu initiieren. 

   ![Screenshot der Anforderung für ein manuelles Failback](./media/tutorial-manual-failover/trigger-failover-03-confirm.png)

   Die Banner werden wie im Abschnitt „Ausführen eines Failovers“ angezeigt. Nach Abschluss des Failbacks wird wie ursprünglich festgelegt erneut `West US 2` als aktueller Standort und `West Central US` als Failoverstandort angezeigt.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen 

Wenn Sie die für dieses Tutorial erstellten Ressourcen entfernen möchten, löschen Sie die Ressourcengruppe. Diese Aktion löscht alle in der Gruppe enthaltenen Ressourcen. In diesem Fall werden der IoT-Hub und die Ressourcengruppe selbst entfernt. 

1. Klicken Sie auf **Ressourcengruppen**. 

2. Suchen Sie nach der Ressourcengruppe **ManlFailRG**, und wählen Sie sie aus. Klicken Sie darauf, um es zu öffnen. 

3. Klicken Sie auf **Ressourcengruppe löschen**. Geben Sie bei entsprechender Aufforderung den Namen der Ressourcengruppe ein, und klicken Sie zum Bestätigen auf **Löschen**. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie ein manuelles Failover konfigurieren und ausführen und wie Sie durch Ausführen der folgenden Aufgaben ein Failback anfordern:

> [!div class="checklist"]
> * Erstellen eines IoT-Hubs über das Azure-Portal 
> * Ausführen eines Failovers. 
> * Anzeigen der Hubausführung am sekundären Standort
> * Ausführen eines Failbacks, um die Vorgänge des IoT-Hubs am primären Standort wiederherzustellen 
> * Überprüfen, ob der Hub ordnungsgemäß am richtigen Standort ausgeführt wird

Im nächsten Tutorial erfahren Sie, wie Sie Ihr Gerät über einen Back-End-Dienst konfigurieren können. 

> [!div class="nextstepaction"]
> [Konfigurieren Ihrer Geräte](tutorial-device-twins.md)
