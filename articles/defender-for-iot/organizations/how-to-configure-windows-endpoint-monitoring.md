---
title: Konfigurieren der Windows-Endpunktüberwachung
description: Anreichern von Daten, die auf Geräten durch die Windows-Endpunktüberwachung (Windows Endpoint Monitoring, WMI) aufgelöst werden.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 7b59c0b5dcb14b57ee5e4e12c2dc3e925e30d37d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281833"
---
# <a name="configure-windows-endpoint-monitoring-wmi"></a>Konfigurieren der Windows-Endpunktüberwachung (WMI)

Mit der Funktion „Windows-Endpunktüberwachung“ können Sie in Microsoft Defender für IoT selektive Stichproben in Windows-Systemen konfigurieren. Dadurch erhalten Sie stärker fokussierte und genauere Informationen über Ihre Geräte, etwa über die Service Pack-Stufen.

Sie können die Erfassung von Stichproben mit bestimmten Bereichen und Hosts und nur im gewünschten Umfang konfigurieren. Sie erreichen die selektive Erfassung von Stichproben mithilfe der Windows-Verwaltungsinstrumentation (WMI), der Standard-Skriptsprache von Microsoft zur Verwaltung von Windows-Systemen.

> [!NOTE]
> - Es kann jederzeit nur jeweils eine Überprüfung durchgeführt werden.
> - Optimale Ergebnisse erhalten Sie mit Benutzern, die Domänenberechtigungen oder lokale Administratorrechte besitzen.
> - Bevor Sie mit der WMI-Konfiguration beginnen, konfigurieren Sie eine Firewallregel, die ausgehenden Datenverkehr vom Sensor in das überprüfte Subnetz über den UDP-Port 135 und alle TCP-Ports oberhalb von 1024 zulässt.

Wenn die Stichprobe abgeschlossen ist, steht in der Option zum Protokollexport eine Protokolldatei mit allen unternommen Versuchen zur Stichprobenerfassung bereit. Das Protokoll enthält alle IP-Adressen, für die eine Stichprobe erfolgt ist. Für jede IP-Adresse weist das Protokoll Erfolgs- und Fehlerinformationen aus. Ferner gibt es einen Fehlercode, bei dem es sich um eine freie Zeichenfolge handelt, die aus der Ausnahme abgeleitet wurde. Im System wird nur die Überprüfung des letzten Protokolls aufbewahrt.

Sie können die Überprüfungen nach einem Zeitplan oder manuell ausführen. Wenn eine Überprüfung abgeschlossen wurde, können Sie die Ergebnisse in einer CSV-Datei anzeigen.

**Voraussetzungen**

Konfigurieren Sie eine Firewallregel, die ausgehenden Datenverkehr vom Sensor in das überprüfte Subnetz über den UDP-Port 135 und alle TCP-Ports oberhalb von 1024 zulässt.

## <a name="perform-an-automatic-scan"></a>Durchführen einer automatischen Überprüfung

In diesem Abschnitt wird beschrieben, wie sie eine automatische Überprüfung durchführen können.

**So führen Sie eine automatische Überprüfung durch:**

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie **Windows Endpoint Monitoring** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: (Windows-Endpunktüberwachung) aus.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot der Auswahl von Windows Endpoint Monitoring (Windows-Endpunktüberwachung).":::

3. Konfigurieren Sie die Optionen im Bereich **Scan Schedule** (Zeitplan für die Überprüfung) wie folgt:

      - **By fixed intervals (in hours)** (Nach festen Intervallen (in Stunden)): Legen Sie den Überprüfungszeitplan nach Intervallen in Stunden fest.

      - **By specific times** (Nach bestimmter Uhrzeit): Legen Sie den Überprüfungszeitplan nach bestimmten Uhrzeiten fest, und wählen Sie **Save Scan** (Überprüfung speichern) aus.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Screenshot der Schaltfläche „Save Scan“"::: (Überprüfung speichern)

4. Zum Definieren des Überprüfungsbereichs wählen Sie **Set scan ranges** (Überprüfungsbereiche festlegen) aus.

5. Legen Sie den IP-Adressbereich fest, und fügen Sie Ihren Benutzer und das Kennwort hinzu.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Screenshot: Hinzufügen von Benutzer und Kennwort":::

6. Wenn Sie einen IP-Adressbereich aus einer Überprüfung ausschließen möchten, wählen Sie neben dem Bereich **Disable** (Deaktivieren) aus.

7. Zum Entfernen eines Bereichs wählen Sie :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: neben dem Bereich aus.

8. Wählen Sie **Speichern** aus. Das Dialogfeld **Edit Scan Ranges Configuration** (Konfiguration der Überprüfungsbereiche bearbeiten) wird geschlossen, und die Anzahl der Bereiche wird im Bereich **Scan Ranges** (Überprüfungsbereiche) angezeigt.

## <a name="perform-a-manual-scan"></a>Durchführen einer manuellen Überprüfung

**So führen Sie eine manuelle Überprüfung aus:**

1. Wählen Sie im Menü an der Seite **System Settings** (Systemeinstellungen) aus.

2. Wählen Sie **Windows Endpoint Monitoring** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: (Windows-Endpunktüberwachung) aus.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Screenshot des Setupbildschirms für Windows Endpoint Monitoring (Windows-Endpunktüberwachung).":::

3. Wählen Sie im Bereich **Actions** (Aktionen) **Start scan** (Überprüfung starten) aus. Eine Statusleiste wird im Bereich **Actions** (Aktionen) angezeigt, die den Fortschritt des Überprüfungsvorgangs darstellt.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Screenshot der Schaltfläche „Start scan“ (Überprüfung starten).":::

## <a name="view-scan-results"></a>Anzeigen der Überprüfungsergebnisse

**So zeigen Sie die Überprüfungsergebnisse an:**

1. Wenn die Überprüfung abgeschlossen ist, wählen Sie im Bereich **Actions** (Aktionen) **View Scan Results** (Überprüfungsergebnisse anzeigen) aus. Die CSV-Datei mit den Scanergebnissen wird auf Ihren Computer heruntergeladen.
