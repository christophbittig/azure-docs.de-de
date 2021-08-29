---
title: Problembehandlung über das Azure-Portal – Azure IoT Edge | Microsoft-Dokumentation
description: Mithilfe der Seite zur Problembehandlung im Azure-Portal können Sie IoT Edge-Geräte und -Module überwachen.
author: kgremban
ms.author: kgremban
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e0761262463d69fe80f7dc15ed1a5944dcc235bb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345974"
---
# <a name="troubleshoot-iot-edge-devices-from-the-azure-portal"></a>Beheben von Problemen bei IoT Edge-Geräten über das Azure-Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge bietet eine optimierte Möglichkeit zur Überwachung von und Problembehandlung bei Modulen im Azure-Portal. Die Seite zur Problembehandlung ist ein Wrapper für direkte Methoden des IoT Edge-Agents, sodass Sie Protokolle aus bereitgestellten Modulen problemlos abrufen und remote neu starten können.

## <a name="prerequisites"></a>Voraussetzungen

Die vollständige Funktionalität dieses Problembehandlungsfeatures im Portal erfordert IoT Edge, Version 1.1.3 oder höher,, wenn Sie sich im Branch für langfristigen Support angemeldet haben, oder aber Version 1.2.1 oder höher, wenn Sie sich im neuesten stabilen Branch angemeldet haben. Sowohl die IoT Edge-Hostkomponente als auch das Modul „edgeAgent“ müssen in diesen Versionen enthalten sein.

## <a name="access-the-troubleshooting-page"></a>Zugriff auf die Seite zur Problembehandlung

Sie können auf die Seite zur Problembehandlung im Portal entweder über die Seite „IoT Edge Gerätedetails“ oder die Seite „IoT Edge Moduldetails“ zugreifen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrem IoT Hub.

1. Wählen Sie im linken Bereich aus dem Menü die Option **IoT Edge** aus.

1. Wählen Sie in der Liste mit Geräten das IoT Edge-Gerät aus, das Sie überwachen möchten.

1. Auf der Seite mit den Gerätedetails können Sie entweder im Menü die Option **Problembehandlung** auswählen oder den Laufzeitstatus eines bestimmten Moduls auswählen, den Sie überprüfen möchten.

   ![Wählen Sie auf der Seite „Gerätedetails“ die Option „Problembehandlung“ oder einen Modullaufzeitstatus aus.](./media/troubleshoot-in-portal/troubleshoot-from-device-details.png)

1. Auf der Seite mit den Gerätedetails können Sie auch den Namen eines Moduls auswählen, um die Seite mit den Moduldetails zu öffnen. Von dort aus können Sie im Menü die Option **Problembehandlung** auswählen.

   ![Wählen Sie auf der Seite mit den Moduldetails die Option „Problembehandlung“ aus.](./media/troubleshoot-in-portal/troubleshoot-from-module-details.png)

## <a name="view-module-logs-in-the-portal"></a>Anzeigen von Modulprotokollen im Portal

Auf der Seite **Problembehandlung** können Sie Protokolle von jedem der ausgeführten Module auf Ihrem IoT Edge-Gerät anzeigen und herunterladen.

Diese Seite hat einen maximalen Grenzwert von 1.500 Protokollzeilen, und Protokolle, die länger sind, werden abgeschnitten. Wenn die Protokolle zu groß sind, schlägt der Versuch des Abrufs von Modulprotokollen fehl. Versuchen Sie in diesem Fall, den Zeitbereichsfilter zu ändern, damit weniger Daten abgerufen werden, oder verwenden Sie direkte Methoden zum [Abrufen von Protokollen aus IoT Edge-Bereitstellungen](how-to-retrieve-iot-edge-logs.md), um größere Protokolldateien zu sammeln.

Wählen Sie im Dropdownmenü aus, welches Modul überprüft werden soll.

![Auswählen von Modulen im Dropdownmenü](./media/troubleshoot-in-portal/select-module.png)

Standardmäßig werden auf dieser Seite die letzten 15 Minuten aus Protokollen angezeigt. Wählen Sie den Filter **Zeitbereich** aus, um verschiedene Protokolle anzuzeigen. Mithilfe des Schiebereglers können Sie ein Zeitfenster innerhalb der letzten 60 Minuten auswählen, oder aktivieren Sie stattdessen **Enter time** (Zeit eingeben), um ein bestimmtes „datetime“-Fenster auszuwählen.

![Auswählen eines Zeitbereichs](./media/troubleshoot-in-portal/select-time-range.png)

Sobald Sie über die Protokolle aus dem Modul verfügen, bei dem Sie während des Zeitbereichs, den Sie überprüfen möchten, eine Problembehandlung durchführen möchten, können Sie mithilfe des Filters **Suchen** bestimmte Zeilen aus den Protokollen abrufen. Sie können entweder nach Warnungen oder Fehlern filtern oder aber einen bestimmten Begriff oder Ausdruck für die Suche angeben. Das Feature **Suche** unterstützt Klartextsuchen oder [reguläre .NET-Ausdrücke](/dotnet/standard/base-types/regular-expression-language-quick-reference) für komplexere Suchvorgänge.

Sie können die Modulprotokolle als Textdatei herunterladen. Die heruntergeladene Protokolldatei enthält alle aktiven Filter, die Sie auf die Protokolle angewendet haben.

>[!TIP]
>Bei der CPU-Auslastung auf einem Gerät kommt es vorübergehend zu Spitzen, während es Protokolle als Reaktion auf eine Anforderung aus dem Portal sammelt. Dieses Verhalten wird erwartet, und die Auslastung sollte sich nach Abschluss der Aufgabe stabilisieren.

## <a name="restart-modules"></a>Neustarten von Modulen

Die Seite **Problembehandlung** enthält ein Feature zum Neustarten eines Moduls. Wenn Sie diese Option auswählen, wird ein Befehl an den IoT Edge-Agent gesendet, um das ausgewählte Modul neu zu starten. Das Neustarten eines Moduls wirkt sich nicht auf Ihre Möglichkeit aus, Protokolle vor dem Neustart abzurufen.

![Neustarten eines Moduls über die Seite zur Problembehandlung](./media/troubleshoot-in-portal/restart-module.png)

## <a name="next-steps"></a>Nächste Schritte

Suchen Sie weitere Tipps zur [Problembehandlung bei Ihrem IoT Edge-Gerät](troubleshoot.md), oder informieren Sie sich über [Häufige Probleme und Lösungen](troubleshoot-common-errors.md). 

Wenn Sie weitere Fragen haben, erstellen Sie eine [Supportanfrage](https://portal.azure.com/#create/Microsoft.Support), um Hilfe zu erhalten.
