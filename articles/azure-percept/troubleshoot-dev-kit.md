---
title: Problembehandlung für das Azure Percept DK-Gerät
description: Tipps zur Fehlerbehebung für einige der häufigeren Probleme mit dem Azure Percept DK und IoT Edge
author: juniem
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: template-how-to
ms.openlocfilehash: 8db131bd39ae8ebe27720a7d725f6ab8082dfd83
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124780013"
---
# <a name="troubleshoot-the-azure-percept-dk-device"></a>Problembehandlung für das Azure Percept DK-Gerät

Der Zweck dieses Artikels zur Fehlerbehebung besteht darin, Azure Percept DK-Benutzern bei der schnellen Behebung von häufigen Problemen mit ihren Development Kits zu unterstützen. Außerdem erhalten Sie Anweisungen für das Sammeln von Protokollen, wenn ein zusätzlicher Support erforderlich ist.

## <a name="log-collection"></a>Protokollsammlung
In diesem Abschnitt erhalten Sie Anweisungen dazu, welche Protokolle auf welche Weise gesammelt werden sollen.

### <a name="how-to-collect-logs"></a>So werden Protokolle gesammelt
1. Stellen Sie eine [SSH-Verbindung](./how-to-ssh-into-percept-dk.md) mit Ihrem Development Kit her.
1. Führen Sie die erforderlichen Befehle im SSH-Terminalfenster aus. Die Befehlsliste für das Sammeln von Protokollen finden Sie im nächsten Abschnitt.
1. Verwenden Sie die folgende Syntax, um Ausgaben zur weiteren Analyse an eine TXT-Datei umzuleiten:
    ```console
    sudo [command] > [file name].txt
    ```
1. Ändern Sie die Berechtigungen der txt-Datei, damit Sie kopiert werden kann:
    ```console
    sudo chmod 666 [file name].txt
    ```
1. Kopieren Sie die Datei über SCP auf Ihren Host-PC:
    ```console
    scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
    ```

    ```[local host file path]``` bezieht sich auf den Speicherort auf dem Host-PC, an den Sie die TXT-Datei kopieren möchten. ```[remote username]``` ist der SSH-Benutzername, der im Rahmen des [Setups](./quickstart-percept-dk-set-up.md) ausgewählt wurde.

### <a name="log-types-and-commands"></a>Protokolltypen und Befehle

|Protokollzweck      |Zeitpunkt für die Sammlung         |Befehl                     |
|-----------------|---------------------------|----------------------------|
|*Support-Paket*: Stellt eine Reihe von Protokollen zur Verfügung, die für die meisten Kundensupportanfragen erforderlich sind.|Immer dann, wenn Support angefordert wird.|```sudo iotedge support-bundle --since 1h``` <br><br>*„--seit 1h“ kann in eine beliebige Zeitspanne geändert werden, z. B. „6h“ (6 Stunden), „6d“ (6 Tage) oder „6m“ (6 Minuten)*|
|*OOBE-Protokolle*: erfasst Details über die Setupfunktionalität.|Immer dann, wenn während des Setups Probleme auftreten.|```sudo journalctl -u oobe -b```|
|*edgeAgent-Protokolle*: Zeichnet die Versionsnummern aller Module auf, die auf Ihrem Gerät ausgeführt werden.|Immer dann, wenn mindestens ein Modul nicht funktioniert.|```sudo iotedge logs edgeAgent```|
|*Modulcontainerprotokolle*: Zeichnet Details zu bestimmten IoT Edge-Modulcontainern auf.|Immer dann, wenn Probleme mit einem Modul auftreten.|```sudo iotedge logs [container name]```|
|*Netzwerkprotokolle*: Eine Gruppe von Protokollen, die die WLAN-Dienste und den Netzwerkstapel abdecken.|Immer dann, wenn WLAN- oder Netzwerkprobleme auftreten.|```sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd > network_log.txt```<br><br>```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version && rpm -q ztpd > system_ver.txt```<br><br>Führen Sie beide Befehle aus. Mit jedem Befehl werden mehrere Protokolle gesammelt und in einer einzelnen Ausgabe abgelegt.|

> [!WARNING]
> Die Ausgabe des Befehls `support-bundle` kann Host-, Geräte- und Modulnamen, von ihren Modulen protokollierte Informationen usw. enthalten. Beachten Sie dies bitte, wenn Sie die Ausgabe in einem öffentlichen Forum freigeben.

## <a name="troubleshooting-commands"></a>Problembehandlung bei Befehlen
Im Folgenden finden Sie eine Reihe von Befehlen, die Sie zur Behebung von Problemen mit dem Development Kit verwenden können. Um diese Befehle auszuführen, müssen Sie zunächst eine [SSH](./how-to-ssh-into-percept-dk.md)-Verbindung mit Ihrem Development Kit herstellen. 

Weitere Informationen zu den Azure IoT Edge-Befehlen finden Sie in der [Problembehandlungsdokumentation für Azure IoT Edge-Geräte](../iot-edge/troubleshoot.md). 

|Funktion         |Verwendung                    |Befehl                 |
|------------------|----------------------------|---------------------------|
|Überprüft die Softwareversion im Development Kit|Immer dann, wenn Sie überprüfen müssen, welche Softwareversion in Ihrem Development Kit enthalten ist.|```cat /etc/os-release && cat /etc/os-subrelease && cat /etc/adu-version```|
|Überprüft die Temperatur des Development Kits|Immer dann, wenn Sie der Meinung sind, dass das Development Kit überhitzen könnte.|```cat /sys/class/thermal/thermal_zone0/temp```|
|Überprüft die Telemetrie-ID des Development Kits|Immer dann, wenn Sie die eindeutige Telemetrie-ID des Development Kits benötigen.|```sudo azure-device-health-id```|
|Überprüft den Status von IoT Edge|Immer dann, wenn Probleme mit IoT Edge-Modulen beim Herstellen einer Verbindung mit der Cloud auftreten.|```sudo iotedge check```|
|Startet den Azure IoT Edge Security Daemon erneut|Immer dann, wenn IoT Edge nicht reagiert oder nicht ordnungsgemäß funktioniert.|```sudo systemctl restart iotedge``` |
|Listet die bereitgestellten Azure IoT Edge-Module auf|Immer dann, wenn Sie alle Module sehen müssen, die im Development Kit bereitgestellt werden.|```sudo iotedge list``` |
|Zeigt den verfügbaren/gesamten Speicherplatz in dem/den angegebenen Dateisystem(en) an|Immer dann, wenn Sie Informationen über den verfügbaren Speicher im Development Kit benötigen.|```df [option] [file]```|
|Zeigt die IP- und Schnittstelleninformationen des Development Kits an|Immer dann, wenn Sie die IP-Adresse des Development Kits benötigen.|`ip route get 1.1.1.1`        | 
|Zeigt nur die IP-Adresse des Development Kits an|Immer dann, wenn Sie nur die IP-Adresse des Development Kits, aber keine anderen Schnittstelleninformationen benötigen.|<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |

## <a name="usb-update-errors"></a>USB-Updatefehler

|Error:                                    |Lösung:                                               |
|------------------------------------------|--------------------------------------------------------|
|„LIBUSB_ERROR_XXX“ beim Ausführen eines USB-Flashs per UUU |Dieser Fehler ist auf einen USB-Verbindungsfehler während der Aktualisierung per UUU zurückzuführen. Wenn das USB-Kabel nicht ordnungsgemäß mit den USB-Anschlüssen des PC oder der Percept DK Trägerplatine verbunden ist, tritt ein Fehler dieser Art auf. Versuchen Sie, beide Enden des USB-Kabels zu trennen und wieder anzuschließen und rütteln Sie am Kabel, um eine sichere Verbindung zu gewährleisten.|

## <a name="clearing-hard-drive-space-on-the-azure-percept-dk"></a>Freigeben von Festplattenspeicherplatz im Azure Percept DK
Es gibt zwei Komponenten, die den Festplattenspeicherplatz im Azure Percept DK in Anspruch nehmen, die Docker-Containerprotokolle und die Docker-Container selbst. Um sicherzustellen, dass die Containerprotokolle nicht den gesamten Speicherplatz in Anspruch nehmen, verfügt das Azure Percept DK über eine integrierte Protokollrotation, bei der alle alten Protokolle entfernt werden, sobald neue Protokolle generiert werden.

Wenn die Anzahl der Docker-Container Probleme mit dem Festplattenspeicher verursacht, können Sie nicht genutzte Container löschen, indem Sie die folgenden Schritte ausführen:
1. [Stellen Sie eine SSH-Verbindung mit dem Development Kit her.](./how-to-ssh-into-percept-dk.md)
1. Führen Sie diesen Befehl aus: `docker system prune`

Dadurch werden alle nicht genutzten Container, Netzwerke, Images und optional auch Volumes entfernt. Details dazu finden Sie auf [dieser Seite](https://docs.docker.com/engine/reference/commandline/system_prune/).

## <a name="azure-percept-dk-carrier-board-led-states"></a>LED-Statusanzeigen der Azure Percept DK-Trägerplatine

An der Oberseite des Gehäuses der Trägerplatine befinden sich drei kleine LEDs. Die LED 1 ist mit einem Wolkensymbol, die LED 2 mit einem WLAN-Symbol und die LED 3 mit einem Ausrufezeichen gekennzeichnet. Die folgende Tabelle enthält Informationen zur jeweiligen LED-Statusanzeige:

|LED             |State      |BESCHREIBUNG                      |
|----------------|-----------|---------------------------------|
|LED 1 (IoT Hub) |An (leuchtet) |Das Gerät ist mit einer IoT Hub-Instanz verbunden. |
|LED 2 (WLAN)   |Langsames Blinken |Das Gerät ist bereit, von Wi-Fi Easy Connect konfiguriert zu werden und kündigt sich bei einem Konfigurator an. |
|LED 2 (WLAN)   |Schnelles Blinken |Die Authentifizierung war erfolgreich, und das Gerät wird zugeordnet. |
|LED 2 (WLAN)   |An (leuchtet) |Authentifizierung und Zuordnung waren erfolgreich. Das Gerät ist mit einem WLAN verbunden. |
|LED 3           |Nicht verfügbar         |Die LED wird nicht verwendet. |
