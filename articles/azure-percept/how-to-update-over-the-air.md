---
title: Aktualisieren von Azure Percept DK per Funk (Over-the-Air-Update)
description: Hier erfahren Sie, wie Sie Over-the-Air-Updates (OTA) für Ihr Azure Percept DK-Gerät erhalten.
author: EthanChangAED
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: ae4daa5721d19fa1fa9a556781808cbca4b58b24
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006200"
---
# <a name="update-azure-percept-dk-over-the-air"></a>Aktualisieren von Azure Percept DK per Funk (Over-the-Air-Update)

In dieser Anleitung erfahren Sie, wie Sie das Betriebssystem und die Firmware der Trägerplatine Ihres Azure Percept DK-Geräts per Funk (Over The Air, OTA) mit Device Update für IoT Hub aktualisieren.

## <a name="prerequisites"></a>Voraussetzungen

- Azure Percept DK (DevKit)
- [Azure-Abonnement](https://azure.microsoft.com/free/)
- [Azure Percept DK-Setup:](./quickstart-percept-dk-set-up.md) Sie haben Ihr DevKit mit einem WLAN verbunden, eine IoT Hub-Instanz erstellt und das DevKit mit der IoT Hub-Instanz verbunden.
- [Device Update für IoT Hub wurde erfolgreich konfiguriert](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Importieren Ihrer Update- und Manifestdatei

> [!NOTE]
> Falls Sie das Update bereits importiert haben, können Sie direkt mit **Erstellen einer Geräteupdategruppe** fortfahren.

1. Bestimmen Sie, welches [Manifest und Updatepaket](./how-to-select-update-package.md) für Ihr Dev Kit geeignet ist.

1. Navigieren Sie zu der Azure IoT Hub-Instanz, die Sie für Ihr Azure Percept-Gerät verwenden. Wählen Sie im Menübereich auf der linken Seite unter **Automatische Geräteverwaltung** die Option **Geräteupdates** aus.

1. Im oberen Bereich des Bildschirms werden mehrere Registerkarten angezeigt. Wählen Sie die Registerkarte **Updates** aus.

1. Wählen Sie unter der Überschrift **Bereitstellung kann durchgeführt werden** die Option **+ Neues Update importieren** aus.

1. Klicken Sie auf die Felder unter **Manifestdatei für Import auswählen** und **Updatedateien auswählen**, um Ihre Manifestdatei (JSON-Datei) und Updatedatei (SWU-Datei) auszuwählen.

1. Wählen Sie unter **Speichercontainer auswählen** das Ordnersymbol oder das Textfeld und anschließend das passende Speicherkonto aus. Wenn Sie bereits einen Speichercontainer erstellt haben, können Sie ihn wiederverwenden. Wählen Sie andernfalls **+ Container** aus, um einen neuen Speichercontainer für OTA-Updates zu erstellen. Wählen Sie den gewünschten Container aus, und klicken Sie auf **Auswählen**.

1. Wählen Sie **Übermitteln** aus, um den Importvorgang zu starten. Aufgrund der Imagegröße kann der Übermittlungsvorgang bis zu fünf Minuten dauern.

    > [!NOTE]
    > Möglicherweise werden Sie aufgefordert, eine CORS-Regel (Cross-Origin Resource Sharing, Ressourcenfreigabe zwischen verschiedenen Ursprüngen) für den Zugriff auf den ausgewählten Speichercontainer hinzuzufügen. Wählen Sie **Add rule and retry** (Regel hinzufügen und wiederholen) aus, um den Vorgang fortzusetzen.

1. Wenn der Importvorgang beginnt, werden Sie zur Registerkarte **Importverlauf** der Seite **Geräteupdates** umgeleitet. Klicken Sie auf **Aktualisieren**, um den Fortschritt des Importvorgangs zu überwachen. Der Vorgang kann je nach Größe des Updates einige Minuten oder auch länger dauern (zu Spitzenzeiten kann mit einer Dauer von bis zu einer Stunde gerechnet werden).

1. Wenn in der Spalte **Status** angezeigt wird, dass der Import erfolgreich war, wählen Sie die Registerkarte **Bereitstellung kann durchgeführt werden** aus, und klicken Sie auf **Aktualisieren**. Ihr importiertes Update sollte nun in der Liste angezeigt werden.

## <a name="create-a-device-update-group"></a>Erstellen einer Geräteupdategruppe

Mit Device Update für IoT Hub können Sie ein Update auf bestimmte Gruppen von Azure Percept DK-Geräten ausrichten. Um eine Gruppe zu erstellen, müssen Sie Ihrer Gerätezielgruppe in Azure IoT Hub ein Tag hinzufügen.

> [!NOTE]
> Falls Sie bereits eine Gruppe erstellt haben, können Sie mit dem nächsten Abschnitt fortfahren.

Anforderungen für Gruppentags:

- Sie können Ihrem Tag einen beliebigen Wert hinzufügen (mit Ausnahme von „Uncategorized“ (Nicht kategorisiert), da es sich dabei um einen reservierten Wert handelt).
- Der Tagwert darf maximal 255 Zeichen enthalten.
- Der Tagwert darf nur folgende Sonderzeichen enthalten: „.“, „-“, „_“, „~“.
- Für Tag- und Gruppennamen wird Groß-/Kleinschreibung beachtet.
- Ein Gerät kann immer nur über ein einzelnes Tag verfügen. Wenn bereits ein Tag vorhanden ist und ein weiteres Tag hinzugefügt wird, wird das zuvor vorhandene Tag überschrieben.
- Ein Gerät kann nur einer einzelnen Gruppe angehören.

1. Fügen Sie Ihrem Gerät bzw. Ihren Geräten ein Tag hinzu:
    1. Suchen Sie im linken Navigationsbereich unter **IoT Edge** nach Ihrem Azure Percept DK-Gerät, und navigieren Sie zum zugehörigen **Gerätezwilling**.
    1. Fügen Sie wie im Anschluss gezeigt einen neuen Tagwert vom Typ **Device Update für IoT Hub** hinzu (```<CustomTagValue>``` verweist auf Ihren Tagwert/-namen, z. B. „AzurePerceptGroup1“). Weitere Informationen zu Tags im JSON-Dokument für Gerätezwillinge finden Sie [hier](../iot-hub/iot-hub-devguide-device-twins.md#device-twins).

        ```json
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Klicken Sie auf **Speichern**, und beheben Sie ggf. vorhandene Formatierungsprobleme.

1. Erstellen Sie eine Gruppe, indem Sie ein vorhandenes Azure IoT Hub-Tag auswählen:

    1. Kehren Sie zu Ihrer Azure IoT Hub-Seite zurück.
    1. Wählen Sie im Menübereich auf der linken Seite unter **Automatische Geräteverwaltung** die Option **Geräteupdates** aus.
    1. Wählen Sie die Registerkarte **Gruppen** . Auf dieser Seite wird die Anzahl nicht gruppierter Geräte angezeigt, die mit Device Update verbunden sind.
    1. Wählen Sie **+ Hinzufügen** aus, um eine neue Gruppe zu erstellen.
    1. Wählen Sie in der Liste ein IoT Hub-Tag aus, und klicken Sie auf **Übermitteln**.
    1. Wenn die Gruppe erstellt wurde, werden das Updatekonformitätsdiagramm und die Gruppenliste aktualisiert. Das Diagramm gibt Aufschluss über die Anzahl von Geräten mit verschiedenen Konformitätszuständen: **Mit neuestem Update**, **Neue Updates verfügbar**, **Updates werden ausgeführt** und **Not yet grouped** (Noch nicht gruppiert).

## <a name="deploy-an-update"></a>Bereitstellen eines Updates

1. Die neu erstellte Gruppe sollte mit einem neuen Update unter **Verfügbare Updates** aufgelistet werden. (Möglicherweise muss die Ansicht einmal aktualisiert werden.) Wählen Sie das Update aus.

1. Vergewissern Sie sich, dass als Zielgerätegruppe die richtige Gerätegruppe ausgewählt ist. Wählen Sie ein **Startdatum** und eine **Startzeit** für Ihre Bereitstellung aus, und klicken Sie anschließend auf **Bereitstellung erstellen**.

    > [!CAUTION]
    > Wenn Sie die Startzeit auf einen Zeitpunkt in der Vergangenheit festlegen, wird die Bereitstellung sofort ausgelöst.

1. Überprüfen Sie das Konformitätsdiagramm. Dort sollte zu sehen sein, dass das Update jetzt ausgeführt wird.

1. Nach Abschluss des Updates wird das Konformitätsdiagramm mit dem neuen Status Ihres Updates aktualisiert.

1. Wählen Sie oben auf der Seite **Geräteupdates** die Registerkarte **Bereitstellungen** aus.

1. Wählen Sie Ihre Bereitstellung aus, um die Bereitstellungsdetails anzuzeigen. Klicken Sie ggf. auf **Aktualisieren**, bis sich der **Status** in **Erfolgreich** ändert.

## <a name="next-steps"></a>Nächste Schritte

Ihr Development Kit wurde erfolgreich aktualisiert. Sie können die Entwicklung und den Betrieb mit Ihrem DevKit fortsetzen.
