---
title: Systemanforderungen für Microsoft Azure Stack Edge Mini R | Microsoft-Dokumentation
description: Informationen zu den Software- und Netzwerkanforderungen für Ihre Azure Stack Edge Mini R-Instanz
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: alkohli
ms.openlocfilehash: 31183be893a9216f33adc4f5e25a55cfb3545db8
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2021
ms.locfileid: "110099566"
---
# <a name="azure-stack-edge-mini-r-system-requirements"></a>Azure Stack Edge Mini R: Systemanforderungen

In diesem Artikel werden die wichtigen Systemanforderungen für Ihre Microsoft Azure Stack Edge Mini R-Lösung und die Clients beschrieben, die eine Verbindung mit Azure Stack Edge Mini R herstellen. Es wird empfohlen, die Informationen sorgfältig anzusehen, bevor Sie Ihr Azure Stack Edge Mini R-Gerät bereitstellen. Sie können diese Informationen ggf. während der Bereitstellung und des nachfolgenden Betriebs als Referenz nutzen.

Die Systemanforderungen für Azure Stack Edge Mini R umfassen Folgendes:

- **Softwareanforderungen für Hosts:** beschreibt die unterstützten Plattformen, Browser für die lokale Konfigurationsbenutzeroberfläche, SMB-Clients und alle weiteren Anforderungen an Clients, die auf das Gerät zugreifen.
- **Netzwerkanforderungen für das Gerät:** enthält Informationen zu den Netzwerkanforderungen für den Betrieb des physischen Geräts.

## <a name="supported-os-for-clients-connected-to-device"></a>Unterstütztes Betriebssystem für Clients, die mit dem Gerät verbunden sind

[!INCLUDE [Supported OS for clients connected to device](../../includes/azure-stack-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Unterstützte Protokolle für Clients, die auf das Gerät zugreifen

[!INCLUDE [Supported protocols for clients accessing device](../../includes/azure-stack-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Unterstützte Speicherkonten

[!INCLUDE [Supported storage accounts](../../includes/azure-stack-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-edge-storage-accounts"></a>Unterstützte Edge-Speicherkonten

Die folgenden Edge-Speicherkonten werden für die REST-Schnittstelle des Geräts unterstützt. Die Edge-Speicherkonten werden auf dem Gerät erstellt. Weitere Informationen finden Sie unter [Edge-Speicherkonten](azure-stack-edge-gpu-manage-storage-accounts.md#about-edge-storage-accounts).

|type  |Speicherkonto  |Kommentare  |
|---------|---------|---------|
|Standard     |GPv1: Blockblob         |         |


*Seitenblobs und Azure Files werden derzeit nicht unterstützt.

## <a name="supported-local-azure-resource-manager-storage-accounts"></a>Unterstützte lokale Azure Resource Manager-Speicherkonten

Diese Speicherkonten werden über die lokalen Geräte-APIs erstellt, wenn Sie mit der lokalen Azure Resource Manager-Instanz verbunden sind. Die folgenden Speicherkonten werden unterstützt:

|type  |Speicherkonto  |Kommentare  |
|---------|---------|---------|
|Standard     |GPv1: Blockblob, Seitenblob         | Der SKU-Typ ist Standard_LRS.        |
|Premium   |GPv1: Blockblob, Seitenblob         |Der SKU-Typ ist Premium_LRS.         |


## <a name="supported-storage-types"></a>Unterstützte Speichertypen

[!INCLUDE [Supported storage types](../../includes/azure-stack-edge-gateway-supported-storage-types.md)]


## <a name="supported-browsers-for-local-web-ui"></a>Unterstützte Browser für die lokale Webbenutzeroberfläche

[!INCLUDE [Supported browsers for local web UI](../../includes/azure-stack-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Anforderungen für den Netzwerkport

### <a name="port-requirements-for-azure-stack-edge-mini-r"></a>Portanforderungen für Azure Stack Edge Mini R

In der folgenden Tabelle sind die Ports aufgeführt, die in der Firewall für SMB-, Cloud- oder Verwaltungsdatenverkehr geöffnet werden müssen. In dieser Tabelle bezieht sich *ein* oder *eingehend* auf die Richtung, aus der eingehende Clientanforderungen auf das Gerät zugreifen. Entsprechend bezieht sich *aus* oder *ausgehend* auf die Richtung, in der Ihr Azure Stack Edge Mini R-Gerät Daten über die Bereitstellung hinaus an externe Ziele sendet, z. B. ausgehende Verbindungen mit dem Internet.

[!INCLUDE [Port configuration for device](../../includes/azure-stack-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Portanforderungen für IoT Edge

Azure IoT Edge ermöglicht die ausgehende Kommunikation von einem lokalen Edgegerät mit der Azure-Cloud über unterstützte IoT Hub-Protokolle. Die eingehende Kommunikation ist nur für bestimmte Szenarien erforderlich, in denen Azure IoT Hub Nachrichten an das Azure IoT Edge-Gerät (z.B. C2D-Nachrichten) pushen muss.

Die folgende Tabelle enthält die Portkonfiguration für Server, die die Azure IoT Edge-Runtime hosten:

| Port-Nr. | ein oder aus | Portbereich | Erforderlich | Anleitungen |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| aus       | WAN        | Ja      | Ausgehend offen für die IoT Edge-Bereitstellung. Diese Konfiguration ist bei Verwendung manueller Skripts oder des Azure IoT Device Provisioning-Diensts (Device Provisioning Service, DPS) erforderlich.|

Ausführliche Informationen finden Sie unter [Firewall- und Portkonfigurationsregeln für die IoT Edge-Bereitstellung](../iot-edge/troubleshoot.md).

## <a name="url-patterns-for-firewall-rules"></a>URL-Muster für Firewallregeln

Netzwerkadministratoren können häufig erweiterte, auf den URL-Mustern basierende Firewallregeln konfigurieren, die zum Filtern des eingehenden und ausgehenden Verkehrs verwendet werden. Ihr Azure Stack Edge Mini R-Gerät und der Dienst sind abhängig von anderen Microsoft-Anwendungen wie Azure Service Bus, Azure Active Directory Access Control, Speicherkonten und Microsoft Update-Servern. Die URL-Muster, die diesen Anwendungen zugeordnet sind, können verwendet werden, um Firewallregeln zu konfigurieren. Es ist wichtig, zu verstehen, dass sich diese den Anwendungen zugeordneten URL-Muster ändern können. Diese Änderungen erfordern, dass der Netzwerkadministrator die Firewallregeln für Ihre Azure Stack Edge Mini R-Instanz nach Bedarf überwacht und aktualisiert.

Es wird empfohlen, dass Sie Ihre Firewallregeln für den ausgehenden Datenverkehr basierend auf den festen IP-Adressen für Azure Stack Edge Mini R in den meisten Fällen recht locker festlegen. Sie können jedoch die folgenden Informationen verwenden, um erweiterte Firewallregeln festzulegen, die erforderlich sind, um sichere Umgebungen zu erstellen.

> [!NOTE]
> - Die Geräte-IPs (Quell-IPs) sollten immer für alle cloudaktivierten Netzwerkschnittstellen eingerichtet sein.
> - Die Ziel-IPs sollten auf die [IP-Bereiche des Azure-Datencenters](https://www.microsoft.com/download/confirmation.aspx?id=41653) festgelegt werden.

### <a name="url-patterns-for-gateway-feature"></a>URL-Muster für Gateway-Feature

[!INCLUDE [URL patterns for firewall](../../includes/azure-stack-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>URL-Muster für Compute-Feature

| URL-Muster                      | Komponente oder Funktion                     |   
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Microsoft-Containerregistrierung (erforderlich)               |
| https://\*.azurecr.io                     | Persönliche Containerregistrierungen und Containerregistrierungen von Drittanbietern (optional) | 
| https://\*.azure-devices.net              | IoT Hub-Zugriff (erforderlich)                             | 
| https://\*.docker.com              | StorageClass (erforderlich)                             |

### <a name="url-patterns-for-gateway-for-azure-government"></a>URL-Muster für das Gateway für Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/azure-stack-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>URL-Muster für Compute für Azure Government

| URL-Muster                      | Komponente oder Funktion                     |  
|----------------------------------|---------------------------------------------|
| https:\//mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Microsoft-Containerregistrierung (erforderlich)               |
| https://\*.azure-devices.us              | IoT Hub-Zugriff (erforderlich)           |
| https://\*.azurecr.us                    | Persönliche Containerregistrierungen und Containerregistrierungen von Drittanbietern (optional) | 

## <a name="internet-bandwidth"></a>Internetbandbreite

[!INCLUDE [Internet bandwidth](../../includes/azure-stack-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Berechnen von Überlegungen zur Größenanpassung

Nutzen Sie Ihre Erfahrung beim Entwickeln und Testen Ihrer Lösung, um sicherzustellen, dass auf Ihrem Azure Stack Edge Mini R-Gerät ausreichend Kapazität vorhanden ist und Sie die optimale Geräteleistung erzielen.

Zu den zu berücksichtigenden Faktoren gehören:

- **Containerdetails**: Bedenken Sie Folgendes.

    - Wie hoch ist Ihr Containerspeicherbedarf? Wie viel Arbeitsspeicher, Speicherplatz und CPU belegt Ihr Container?
    - Wie viele Container umfasst Ihre Workload? Sie könnten eine Vielzahl von leichten Containern anstelle von einigen ressourcenintensiven Containern verwenden.
    - Welche Ressourcen sind diesen Containern zugeteilt im Vergleich zu den von ihnen belegten Ressourcen (der Speicherbedarf)?
    - Wie viele Ebenen werden von den Containern gemeinsam genutzt? Containerimages sind ein Bündel von Dateien, die in einem Stapel von Ebenen angeordnet sind. Bestimmen Sie für Ihr Containerimage die Anzahl der Ebenen und deren jeweilige Größe, um den Ressourcenbedarf zu berechnen.
    - Gibt es nicht verwendete Container? Ein angehaltener Container belegt weiterhin Speicherplatz.
    - In welcher Sprache sind Ihre Container geschrieben?
- **Umfang der verarbeiteten Daten**: Wie viele Daten werden Ihre Container verarbeiten? Werden diese Daten Speicherplatz verbrauchen oder werden die Daten im Arbeitsspeicher verarbeitet?
- **Erwartete Leistung**: Was sind die gewünschten Leistungsmerkmale Ihrer Lösung? 

Um die Leistung Ihrer Lösung zu verstehen und zu optimieren, können Sie Folgendes verwenden:

- Die im Azure-Portal verfügbaren Computemetriken. Wechseln Sie zu Ihrer Azure Stack Edge-Ressource und dann zu **Überwachung > Metriken**. Betrachten Sie **Edgecomputing – Arbeitsspeichernutzung** und **Edgecomputing – CPU in Prozent**, um die verfügbaren Ressourcen zu verstehen und wie die Ressourcen verbraucht werden.
- Die über die PowerShell-Schnittstelle des Geräts verfügbaren Überwachungsbefehle wie:

    - `dkr`: Statistiken zum Abrufen eines Livestreams der Ressourcennutzungsstatistiken für Container. Der Befehl unterstützt Metriken zu CPU, Arbeitsspeicherauslastung, Arbeitsspeicherlimit und Netzwerk-E/A.
    - `dkr system df`: zum Abrufen von Informationen bezüglich des belegten Speicherplatzes. 
    - `dkr image [prune]`: zum Löschen nicht verwendeter Images und Freigeben von Speicherplatz.
    - `dkr ps --size`: zum Anzeigen der ungefähren Größe eines aktiven Containers. 

    Weitere Informationen zu den verfügbaren Befehlen finden Sie unter [ Debuggen von Kubernetes-Problemen](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge).

Stellen Sie abschließend sicher, dass Sie Ihre Lösung vor der Bereitstellung in der Produktionsumgebung für Ihr Dataset überprüfen und die Leistung der Azure Stack Edge Mini R-Instanz quantifizieren.

## <a name="next-step"></a>Nächster Schritt

- [Bereitstellen Ihrer Azure Stack Edge Mini R-Instanz](azure-stack-edge-placeholder.md)
