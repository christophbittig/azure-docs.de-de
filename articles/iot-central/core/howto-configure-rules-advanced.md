---
title: Integrieren Ihrer Azure IoT Central-Anwendung in andere Clouddienste mithilfe von Workflows | Microsoft-Dokumentation
description: In diesem Anleitungsartikel für Entwickler erfahren Sie, wie Sie Regeln und Aktionen für die Integration Ihrer Azure IoT Central-Anwendung in andere Clouddienste konfigurieren. Für die Erstellung einer erweiterten Regel wird ein IoT Central-Connector in Power Automate oder Azure Logic Apps verwendet.
author: dominicbetts
ms.author: dobett
ms.date: 08/26/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1068bd5f5a97c4eeab702add9c305b38a8084464
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079650"
---
# <a name="use-workflows-to-integrate-your-azure-iot-central-application-with-other-cloud-services"></a>Integrieren Ihrer Azure IoT Central-Anwendung in andere Clouddienste mithilfe von Workflows

Sie können Regeln in IoT Central erstellen, um als Reaktion auf telemetriebasierte Bedingungen (etwa eine Gerätetemperatur, die einen Schwellenwert übersteigt) Aktionen wie etwa das Senden einer E-Mail auszulösen.

Der Azure IoT Central V3-Connector für Power Automate und Azure Logic Apps ermöglicht die Erstellung komplexerer Regeln, um Vorgänge in IoT Central zu automatisieren:

- Wenn eine Regel in Ihrer Azure IoT Central-App ausgelöst wird, kann ein Workflow in Power Automate oder Azure Logic Apps ausgelöst werden. Durch diese Workflows können Aktionen in anderen Clouddiensten (beispielsweise Microsoft 365) oder in einem Drittanbieterdienst ausgeführt werden.
- Durch ein Ereignis in einem anderen Clouddienst (beispielsweise Microsoft 365) kann ein Workflow in Power Automate oder Azure Logic Apps ausgelöst werden. Durch diese Workflows können Aktionen ausgeführt oder Daten aus Ihrer IoT Central-Anwendung abgerufen werden.
- Der Azure IoT Central V3-Connector ist auf die allgemein verfügbare [REST-API-Oberfläche 1.0](/rest/api/iotcentral/) abgestimmt. Alle Connectoraktionen unterstützen das [DTDLv2-Format](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md), und die Unterstützung für DTDLv1-basierte Modelle ist veraltet. Die neuesten Informationen und Details zu den letzten Aktualisierungen finden Sie in den [Versionshinweisen](/connectors/azureiotcentral/#release-notes) für die aktuelle Connectorversion.

## <a name="prerequisites"></a>Voraussetzungen

Zum Ausführen der Schritte in dieser Anleitung ist Folgendes erforderlich:

[!INCLUDE [iot-central-prerequisites-basic](../../../includes/iot-central-prerequisites-basic.md)]

> [!NOTE]
> Wenn Sie eine IoT Central-Anwendung der Version 2 verwenden, lesen Sie [Erstellen von Workflows mit dem IoT Central-Connector in Azure Logic Apps](/previous-versions/azure/iot-central/core/howto-build-azure-logic-apps) auf der Dokumentationswebsite der vorherigen Version, und verwenden Sie den Azure IoT Central V2-Connector

## <a name="trigger-a-workflow-from-a-rule"></a>Auslösen eines Workflows über eine Regel

Um einen Workflow in Power Automate oder Azure Logic Apps auslösen zu können, benötigen Sie zunächst eine Regel in Ihrer IoT Central-Anwendung. Weitere Informationen finden Sie unter [Konfigurieren von Regeln](./howto-configure-rules.md).

So fügen Sie den **Azure IoT Central V3-** Connector als Auslöser in Power Automate hinzu:

1. Wählen Sie in Power Automate **+ Erstellen** und anschließend die Registerkarte **Benutzerdefiniert** aus.
1. Suchen Sie nach *IoT Central*, und wählen Sie den **Azure IoT Central V3-** Connector aus.
1. Wählen Sie in der Liste mit den Triggern die Option **When a rule is fired (preview)** (Beim Auslösen einer Regel (Vorschauversion)) aus.
1. Wählen Sie im Schritt **When a rule is fired** (Beim Auslösen einer Regel) Ihre IoT Central-Anwendung und die verwendete Regel aus.

So fügen Sie den **Azure IoT Central V3-** Connector als Auslöser in Azure Logic Apps hinzu:

1. Wählen Sie im **Designer für Logik-Apps** die Vorlage **Leere Logik-App** aus.
1. Suchen Sie im Designer nach *IoT Central* und wählen Sie den **Azure IoT Central V3-** Connector aus.
1. Wählen Sie in der Liste mit den Triggern die Option **When a rule is fired (preview)** (Beim Auslösen einer Regel (Vorschauversion)) aus.
1. Wählen Sie im Schritt **When a rule is fired** (Beim Auslösen einer Regel) Ihre IoT Central-Anwendung und die verwendete Regel aus.

:::image type="content" source="./media/howto-configure-rules-advanced/triggers.png" alt-text="Suchen nach dem Connector „Azure IoT Central - preview“ (Azure IoT Central (Vorschauversion)) und Auswählen des Triggers":::

Nun können Sie Ihrem Workflow weitere Schritte hinzufügen, um Ihr Integrationsszenario zu erstellen.

## <a name="run-an-action"></a>Ausführen einer Aktion

Sie können Aktionen in einer IoT Central-Anwendung über Power Automate- und Azure Logic Apps-Workflows ausführen. Erstellen Sie zunächst Ihren Workflow, und verwenden Sie einen Connector, um einen Trigger zum Starten des Workflows zu definieren. Verwenden Sie dann den **Azure IoT Central V3-** Connector als Aktion.

So fügen Sie den **Azure IoT Central V3-** Connector als Aktion in Power Automate hinzu:

1. Wählen Sie in Power Automate im Bereich **Aktion auswählen** die Registerkarte **Benutzerdefiniert** aus.
1. Suchen Sie nach *IoT Central* und wählen Sie den **Azure IoT Central V3-** Connector.
1. Wählen Sie in der Liste mit den Aktionen die gewünschte IoT Central-Aktion aus.
1. Schließen Sie im Aktionsschritt die Konfiguration für die ausgewählte Aktion ab. Klicken Sie dann auf **Speichern**.

Gehen Sie wie folgt vor, um den **Azure IoT Central V3 – Vorschau**-Connector in Azure Logic Apps als Aktion hinzuzufügen:

1. Wählen Sie im **Designer für Logik-Apps** im Bereich **Aktion auswählen** die Registerkarte **Benutzerdefiniert** aus.
1. Suchen Sie nach *IoT Central*, und wählen Sie den **Azure IoT Central V3 – Vorschau**-Connector aus.
1. Wählen Sie in der Liste mit den Aktionen die gewünschte IoT Central-Aktion aus.
1. Schließen Sie im Aktionsschritt die Konfiguration für die ausgewählte Aktion ab. Klicken Sie dann auf **Speichern**.

:::image type="content" source="./media/howto-configure-rules-advanced/actions.png" alt-text="Suchen nach dem Azure IoT Central V3-Connector, und wählen Sie eine Aktion aus":::

## <a name="list-of-actions"></a>Liste mit Aktionen

Eine vollständige Liste der von dem Konnektor unterstützten Aktionen finden Sie unter [Aktionen](/connectors/azureiotcentral/#actions).


### <a name="create-or-update-a-device"></a>„Create or update a device“ (Gerät erstellen oder aktualisieren)

Verwenden Sie diese Aktion, um ein Gerät in Ihrer IoT Central-Anwendung zu erstellen oder zu aktualisieren.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des Geräts, das erstellt oder aktualisiert werden soll. |
| Genehmigt | Wählen Sie aus, ob für das Gerät die Verbindungsherstellung mit IoT Central genehmigt wurde. |
| Gerätebeschreibung | Eine detaillierte Beschreibung des Geräts. |
| Gerätename | Der Anzeigename des Geräts. |
| Gerätevorlage | Wählen Sie eine Vorlage aus der Liste mit Gerätevorlagen in Ihrer IoT Central-Anwendung aus. |
| Simuliert | Wählen Sie aus, ob es sich um ein simuliertes Gerät handelt. |

### <a name="delete-a-device"></a>Löschen eines Mediums

Verwenden Sie diese Aktion, um ein Gerät aus Ihrer IoT Central-Anwendung zu löschen.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |

### <a name="execute-a-device-command"></a>„Execute a device command“ (Gerätebefehl ausführen)

Verwenden Sie diese Aktion, um einen Befehl auszuführen, der in einer der Schnittstellen des Geräts definiert ist.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |
| „Device Component“ (Gerätekomponente) | Die Schnittstelle in der Gerätevorlage, die den Befehl enthält. |
| Gerätebefehl | Wählen Sie einen der Befehle für die ausgewählte Schnittstelle aus. |
| Gerätevorlage | Wählen Sie eine Vorlage aus der Liste mit Gerätevorlagen in Ihrer IoT Central-Anwendung aus. |
| „Device Command Request Payload“ (Anforderungsnutzlast für Gerätebefehl) | Sollte für den Befehl eine Anforderungsnutzlast erforderlich sein, fügen Sie sie hier hinzu.  |

> [!NOTE]
> Eine Gerätekomponente kann erst ausgewählt werden, nachdem eine Gerätevorlage ausgewählt wurde.

### <a name="get-a-device-by-id"></a>„Get a device by ID“ (Gerät nach ID abrufen)

Verwenden Sie diese Aktion, um die Details des Geräts abzurufen.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |

Die zurückgegebenen Details können in den dynamischen Ausdrücken in anderen Aktionen verwendet werden. Folgende Gerätedetails werden zurückgegeben: **Genehmigt**, **Text**, **Gerätebeschreibung**, **Gerätename**, **Gerätevorlage**, **Bereitgestellt** und **Simuliert**.

### <a name="get-device-cloud-properties"></a>„Get device cloud properties“ (Cloudeigenschaften des Geräts abrufen)

Verwenden Sie diese Aktion, um die Cloudeigenschaftswerte für ein bestimmtes Gerät abzurufen.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |
| Gerätevorlage | Wählen Sie eine Vorlage aus der Liste mit Gerätevorlagen in Ihrer IoT Central-Anwendung aus. |

Die zurückgegebenen Cloudeigenschaftswerte können in den dynamischen Ausdrücken in anderen Aktionen verwendet werden.

### <a name="get-device-properties"></a>„Get device properties“ (Geräteeigenschaften abrufen)

Verwenden Sie diese Aktion, um die Eigenschaftswerte für ein bestimmtes Gerät abzurufen.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |
| Gerätevorlage | Wählen Sie eine Vorlage aus der Liste mit Gerätevorlagen in Ihrer IoT Central-Anwendung aus. |

Die zurückgegebenen Eigenschaftswerte können in den dynamischen Ausdrücken in anderen Aktionen verwendet werden.

### <a name="get-device-telemetry-value"></a>„Get device telemetry value“ (Gerätetelemetriewert abrufen)

Verwenden Sie diese Aktion, um die Telemetriewerte für ein bestimmtes Gerät abzurufen.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |
| Gerätevorlage | Wählen Sie eine Vorlage aus der Liste mit Gerätevorlagen in Ihrer IoT Central-Anwendung aus. |

Die zurückgegebenen Telemetriewerte können in den dynamischen Ausdrücken in anderen Aktionen verwendet werden.

### <a name="update-device-cloud-properties"></a>„Update device cloud properties“ (Cloudeigenschaften des Geräts aktualisieren)

Verwenden Sie diese Aktion, um die Cloudeigenschaftswerte für ein bestimmtes Gerät zu aktualisieren.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |
| Gerätevorlage | Wählen Sie eine Vorlage aus der Liste mit Gerätevorlagen in Ihrer IoT Central-Anwendung aus. |
| Cloudeigenschaften | Nach dem Auswählen einer Gerätevorlage wird ein Feld für jede Cloudeigenschaft hinzugefügt, die in der Vorlage definiert ist. |

### <a name="update-device-properties"></a>„Update device properties“ (Geräteeigenschaften aktualisieren)

Verwenden Sie diese Aktion, um die beschreibbaren Eigenschaftswerte für ein bestimmtes Gerät zu aktualisieren.

| Feld | BESCHREIBUNG |
| ----- | ----------- |
| Anwendung | Wählen Sie eine Anwendung aus der Liste mit IoT Central-Anwendungen aus. |
| Gerät | Die eindeutige ID des zu löschenden Geräts. |
| Gerätevorlage | Wählen Sie eine Vorlage aus der Liste mit Gerätevorlagen in Ihrer IoT Central-Anwendung aus. |
| Schreibbare Eigenschaften | Nach dem Auswählen einer Gerätevorlage wird ein Feld für jede beschreibbare Eigenschaft hinzugefügt, die in der Vorlage definiert ist. |

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun wissen, wie Sie eine erweiterte Regel in Ihrer Azure IoT Central-Anwendung erstellen, können Sie sich als Nächstes mit dem [Analysieren von Gerätedaten mithilfe von Analytics](howto-create-analytics.md) beschäftigen.