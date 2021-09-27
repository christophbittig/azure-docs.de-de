---
title: Ausführen von Azure Stream Analytics auf Azure Stack
description: Erstellen Sie einen Azure Stream Analytics-Edgeauftrag, und stellen Sie ihn über die Azure IoT Edge-Runtime auf dem Azure Stack Hub bereit.
ms.service: stream-analytics
author: an-emma
ms.author: raan
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 544abb1e39710330d8d2529a83349c8669deae40
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638252"
---
# <a name="run-azure-stream-analytics-on-azure-stack"></a>Ausführen von Azure Stream Analytics auf Azure Stack

Sie können Azure Stream Analytics auf Azure Stack Hub als Azure IoT Edge-Modul ausführen. Dem IoT Edge-Modul hinzugefügte Konfigurationen erlauben es ihm, mit Azure Blob Storage, Azure Event Hubs und Azure IoT Hubs zu interagieren, die in einem Azure Stack Hub-Abonnement ausgeführt werden, indem benutzerdefinierte URLs zugelassen werden, die in jeder Azure Stack Hub-Anwendung zu finden sind.

Mit Stream Analytics auf Azure Stack können Sie tatsächliche Hybridarchitekturen für die Streamverarbeitung in Ihrer eigenen privaten, autonomen Cloud erstellen. Ihre Cloud kann mit cloudnativen Apps über konsistente Azure-Dienste lokal verbunden oder getrennt werden. 

In diesem Artikel erfahren Sie, wie Sie in einem Azure Stack Hub-Abonnement Daten von einem IoT Hub oder Event Hub zu einem anderen Event Hub oder Blobspeicher streamen und mit Stream Analytics verarbeiten können.

## <a name="set-up-environments"></a>Einrichten von Umgebungen

Stream Analytics ist ein Hybriddienst auf Azure Stack Hub. Es handelt sich um ein IoT Edge-Modul, das in Azure konfiguriert ist, aber auf Azure Stack Hub ausgeführt werden kann.

Wenn Sie neu bei Azure Stack Hub oder IoT Edge sind, befolgen Sie die folgenden Anweisungen zum Einrichten von Umgebungen.

### <a name="prepare-the-azure-stack-hub-environment"></a>Vorbereiten der Azure Stack Hub-Umgebung

Erstellen Sie ein Azure Stack Hub-Abonnement. Weitere Informationen finden Sie im [Tutorial zum Erstellen eines Azure Stack Hub-Abonnements](/azure-stack/user/azure-stack-subscribe-services/).

Wenn Sie Azure Stack Hub auf Ihrem eigenen Server bewerten möchten, können Sie das Azure Stack Development Kit (ASDK) verwenden. Weitere Informationen zum ASDK finden Sie in der [ASDK-Übersicht](/azure-stack/asdk/).

### <a name="install-the-iot-edge-runtime"></a>Installieren der IoT Edge-Runtime

Um Stream Analytics auf Azure Stack Hub ausführen zu können, muss Ihr Gerät über die IoT Edge-Runtime verfügen und eine Netzwerkverbindung zum Azure Stack Hub aufweisen oder eine VM sein, die auf dem Azure Stack Hub ausgeführt wird. Die IoT Edge-Laufzeit ermöglicht die Integration von Stream Analytics Edge-Aufträgen in Azure Storage und Azure Event Hubs, die auf Ihrem Azure Stack Hub ausgeführt werden. Weitere Informationen finden Sie unter [Azure Stream Analytics auf IoT Edge](stream-analytics-edge.md). 

Zusätzlich zum Netzwerkzugriff auf die Azure Stack Hub-Ressourcen benötigt das IoT Edge-Gerät (oder die VM) Zugriff auf den IoT Hub in der öffentlichen Azure-Cloud, um das Stream Analytics-Modul zu konfigurieren. 

Die folgenden Artikel zeigen, wie Sie die IoT Edge Runtime auf Ihrem Gerät oder Ihrer VM einrichten:

* [Installieren der Azure IoT Edge-Runtime unter Windows](../iot-edge/how-to-install-iot-edge.md)
* [Installieren der Azure IoT Edge-Runtime auf Debian-basierten Linux-Systemen](../iot-edge/how-to-install-iot-edge.md)


## <a name="create-an-azure-stream-analytics-edge-job"></a>Erstellen eines Azure Stream Analytics-Edge-Auftrags

Stream Analytics-Edgeaufträge werden in Containern ausgeführt, die auf IoT Edge-Geräten bereitgestellt sind. Sie bestehen aus zwei Teilen:
* Einem Cloudteil, der für die Auftragsdefinition zuständig ist: Benutzer definieren Eingaben, Ausgaben, Abfragen und andere Einstellungen (z. B. Ereignisse in falscher Reihenfolge) in der Cloud.
* Einem Modul, das auf Ihren IoT-Geräten ausgeführt wird. Dieses enthält die Stream Analytics-Engine und empfängt die Auftragsdefinition aus der Cloud.

### <a name="create-a-storage-account"></a>Erstellen eines Speicherkontos

Wenn Sie einen Stream Analytics-Auftrag zur Ausführung auf einem IoT Edge-Gerät erstellen, muss er so gespeichert werden, dass er vom Gerät aufgerufen werden kann. Sie können ein bereits vorhandenes Azure Storage-Konto verwenden oder ein neues erstellen.
1. Rufen Sie im Azure-Portal **Ressource erstellen** > **Speicher** > **Speicherkonto – Blob, Datei, Tabelle, Warteschlange** auf.
1. Geben Sie die folgenden Werte an, um Ihr Speicherkonto zu erstellen.

   | Feld | Wert |
   | --- | --- |
   | Name | Geben Sie einen eindeutigen Namen für Ihr Speicherkonto an. |
   | Standort | Wählen Sie einen Standort in Ihrer Nähe aus.|
   | Subscription | Wählen Sie das gleiche Abonnement wie für Ihren IoT Hub.|
   | Ressourcengruppe | Verwenden Sie dieselbe Ressourcengruppe für alle Testressourcen, die Sie während der [IoT Edge-Schnellstarts](../iot-edge/quickstart.md) und -Tutorials erstellen. Verwenden Sie beispielsweise **IoTEdgeResources**. |

1. Behalten Sie in den restlichen Feldern die Standardwerte bei, und wählen Sie **Erstellen**.


### <a name="create-a-new-job"></a>Erstellen eines neuen Auftrags

1. Wechseln Sie im Azure-Portal zu **Ressource erstellen** > **Internet der Dinge** > **Stream Analytics-Auftrag**.
1. Geben Sie die folgenden Werte an, um Ihr Speicherkonto zu erstellen.

   | Feld | value |
   | --- | --- |
   | Auftragsname | Geben Sie einen Namen für Ihren Auftrag an. Ein Beispiel ist **IoTEdgeJob**. |
   | Subscription | Wählen Sie das gleiche Abonnement wie für Ihren IoT Hub.|
   | Ressourcengruppe | Verwenden Sie dieselbe Ressourcengruppe für alle Testressourcen, die Sie während der [IoT Edge-Schnellstarts](../iot-edge/quickstart.md) und -Tutorials erstellen. Verwenden Sie beispielsweise **IoTEdgeResources**. |
   | Standort | Wählen Sie einen Standort in Ihrer Nähe aus. |
   | Hostumgebung | Wählen Sie **Edge** aus. |

1. Klicken Sie auf **Erstellen**.

### <a name="configure-your-job"></a>Konfigurieren des Auftrags

Nachdem Sie Ihren Stream Analytics-Auftrag im Azure-Portal erstellt haben, konfigurieren Sie ihn mit einer Eingabe, einer Ausgabe und einer Abfrage, die für die Daten ausgeführt wird, die er durchläuft. Sie können manuell Eingaben von einem IoT Hub oder einem Event Hub in einem Azure Stack Hub-Abonnement angeben.

1. Wechseln Sie im Azure-Portal zu Ihrem Stream Analytics-Auftrag.
1. Wählen Sie unter **Konfigurieren** die Option **Speicherkontoeinstellungen** aus, und wählen Sie dann das Speicherkonto aus, das Sie im vorherigen Schritt erstellt haben.

   :::image type="content" source="media/on-azure-stack/storage-account-settings.png" alt-text="Screenshot, der die Speicherkontoeinstellung „Auftrag“ zeigt." lightbox="media/on-azure-stack/storage-account-settings.png":::

1. Wählen Sie unter **Auftragstopologie** die Option **Eingaben** > **Datenstromeingabe hinzufügen** aus.
1. Wählen Sie **IoT Hub**, **Event Hub** oder **Edge Hub** aus der Dropdownliste aus. 
1. Wenn es sich bei der Eingabe um einen Event Hub oder IoT Hub in einem Azure Stack Hub-Abonnement handelt, geben Sie die Informationen wie hier dargestellt manuell ein.

   #### <a name="event-hub"></a>Event Hub

   | Feld | Wert |
   | --- | --- |
   | Eingabealias | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
   | Service Bus-Namespace | Der Namespace ist ein Container für einen Satz mit Nachrichtenentitäten. Sie haben bei der Erstellung einer neuen Event Hub-Instanz auch den Namespace erstellt. z. B. `sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com`. |
   | Event Hub-Name | Der Name des Event Hubs, der als Eingabe verwendet wird. |
   | Event Hub-Richtlinienname | Die SAS-Richtlinie, die Zugriff auf den Event Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. Diese Option wird automatisch ausgefüllt, sofern Sie nicht die Option zum manuellen Festlegen der Event-Hub-Einstellungen wählen. |
   | Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Autorisierung des Zugriffs auf Event Hub verwendet wird. Diese Option wird automatisch ausgefüllt, sofern Sie nicht die Option zum manuellen Festlegen der Event-Hub-Einstellungen wählen. Sie finden sie in den Event Hub-Einstellungen. |
   | Event Hub-Consumergruppe (optional) | Verwenden Sie für jeden Stream Analytics-Auftrag eine eigene Consumergruppe. Diese Zeichenfolge identifiziert die Consumergruppe, die zum Erfassen von Daten aus dem Event Hub verwendet werden soll. Wenn keine Consumergruppe angegeben wird, verwendet der Stream Analytics-Auftrag die $Default-Consumergruppe. |
   | Partitionsanzahl | Die Partitionsanzahl ist die Anzahl der Partitionen in einem Event Hub. |

   :::image type="content" source="media/on-azure-stack/event-hub-input.png" alt-text="Screenshot, der Event Hub-Eingaben zeigt." lightbox="media/on-azure-stack/event-hub-input.png":::
   
   #### <a name="iot-hub"></a>IoT Hub

   | Feld | Wert |
   | --- | --- |
   | Eingabealias | Ein Anzeigename, der in der Auftragsabfrage verwendet wird, um auf diese Eingabe zu verweisen. |
   | IoT Hub | Der Name von IoT Hub, der als Eingabe verwendet wird. z. B. `<IoT Hub Name>.shanghai.azurestack.corp.microsoft.com`. |
   | Name der SAS-Richtlinie | Die SAS-Richtlinie, die Zugriff auf IoT Hub ermöglicht. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
   | Schlüssel für SAS-Richtlinie | Der Schlüssel für den gemeinsamen Zugriff, der für die Autorisierung des Zugriffs auf IoT Hub verwendet wird. Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der IoT Hub-Einstellungen. |
   | Consumergruppe (optional) | Verwenden Sie für jeden Stream Analytics-Auftrag eine andere Consumergruppe. Die Consumergruppe, die zum Erfassen von Daten aus IoT Hub verwendet werden soll. Stream Analytics verwendet die $Default-Consumergruppe, sofern nicht anders angegeben. |
   | Partitionsanzahl | Die Partitionsanzahl ist die Anzahl der Partitionen in einem Event Hub. |

   :::image type="content" source="media/on-azure-stack/iot-hub-input.png" alt-text="Screenshot, der IoT Hub-Eingaben zeigt." lightbox="media/on-azure-stack/iot-hub-input.png" :::

1. Behalten Sie für die übrigen Felder die Standardwerte bei, und klicken Sie auf **Speichern**.
1. Öffnen Sie unter **Auftragstopologie** die Option **Ausgaben**, und wählen Sie dann **Hinzufügen** aus.
1. Wählen Sie **Blob Storage**, **Event Hub** oder **Edge Hub** aus der Dropdownliste aus.
1. Wenn es sich bei der Ausgabe um Event Hub oder Blob Storage in einem Azure Stack Hub-Abonnement handelt, geben Sie die Informationen wie hier dargestellt manuell ein.

   #### <a name="event-hub"></a>Event Hub

   | Feld | value |
   | --- | --- |
   | Ausgabealias | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Event Hub weiterzuleiten. |
   | Service Bus-Namespace | Ein Container für einen Satz von Nachrichtenentitäten. Sie haben bei der Erstellung eines neuen Event Hubs auch einen Service Bus-Namespace erstellt. z. B. `sb://<Event Hub Name>.eventhub.shanghai.azurestack.corp.microsoft.com`. |
   | Event Hub-Name | Der Name Ihrer Event Hub-Ausgabe. |
   | Event Hub-Richtlinienname | Die Richtlinie für den gemeinsamen Zugriff, die Sie auf der Registerkarte **Konfigurieren** des Event Hubs erstellen können. Jede SAS-Richtlinie umfasst einen Namen, die von Ihnen festgelegten Berechtigungen und Zugriffsschlüssel. |
   | Event Hub-Richtlinienschlüssel | Der Schlüssel für den gemeinsamen Zugriff, der für die Authentifizierung des Zugriffs auf den Event Hub-Namespace verwendet wird. |

   :::image type="content" source="media/on-azure-stack/event-hub-output.png" lightbox="media/on-azure-stack/event-hub-output.png" alt-text="Screenshot, der Event Hub-Ausgaben zeigt.":::
   #### <a name="blob-storage"></a>Blobspeicher 

   | Feld | value |
   | --- | --- |
   | Ausgabealias | Ein Anzeigename, der in Abfragen verwendet wird, um die Abfrageausgabe an diesen Blobspeicher weiterzuleiten. |
   | Speicherkonto | Der Name des Speicherkontos, an das Sie die Ausgabe senden. z. B. `<Storage Account Name>.blob.shanghai.azurestack.corp.microsoft.com`. |
   | Speicherkontoschlüssel | Der geheime Schlüssel, der dem Speicherkonto zugeordnet ist. Diese Option wird automatisch ausgefüllt, es sei denn, Sie wählen die Option zum manuellen Festlegen der Blob Storage-Einstellungen. |

> [!NOTE]
> Das Parquet-Format wird für Edgeaufträge auf Azure Stack Hub nicht unterstützt. Für **Minimale** Zeilen und **Maximale** Zeit verwenden Sie **0**, oder lassen Sie sie leer.


## <a name="deploy-stream-analytics-on-a-vm-or-device-connected-to-azure-stack"></a>Bereitstellen von Stream Analytics auf einer VM oder einem Gerät, das mit Azure Stack verbunden ist

1. Öffnen Sie IoT Hub im Azure-Portal. Wechseln Sie zu **IoT Edge**, und klicken Sie auf das Gerät oder die VM, auf das/die Sie diese Bereitstellung ausrichten möchten.
1. Wählen Sie **Module festlegen** >  **+ Hinzufügen** und anschließend **Azure Stream Analytics-Modul** aus. 
1. Wählen Sie das Abonnement und den erstellten Steam Analytics Edge-Auftrag aus. Wählen Sie **Speichern** und anschließend **Weiter: Routen** aus.

   :::image type="content" source="media/on-azure-stack/edge-modules.png" lightbox="media/on-azure-stack/edge-modules.png" alt-text="Screenshot des Hinzufügens eines Moduls.":::

1. Wählen Sie **Überprüfen + erstellen >** aus.
1. Wählen Sie im Schritt **Überprüfen + erstellen** die Option **Erstellen** aus.

   :::image type="content" source="media/on-azure-stack/module-content.png" lightbox="media/on-azure-stack/module-content.png" alt-text="Screenshot, der das Manifest zeigt.":::

1. Vergewissern Sie sich, dass das Modul zur Liste hinzugefügt wurde.

   :::image type="content" source="media/on-azure-stack/edge-deployment.png" lightbox="media/on-azure-stack/edge-deployment.png" alt-text="Screenshot, der die Bereitstellungsseite zeigt.":::
## <a name="next-steps"></a>Nächste Schritte
- [Azure Stream Analytics auf IoT Edge](./stream-analytics-edge.md)
- [Entwickeln von Stream Analytics Edge-Aufträgen](/stream-analytics-query/stream-analytics-query-language-reference)
