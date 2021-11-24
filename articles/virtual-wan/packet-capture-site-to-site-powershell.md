---
title: Konfigurieren Sie eine Paketerfassung für virtuelle WAN-Standort-zu-Standort-Verbindungen
description: Erfahren Sie, wie Sie mit PowerShell eine Paketerfassung auf einem Virtual WAN Site-to-Site VPN-Gateway durchführen.
services: virtual-wan
titleSuffix: Azure Virtual WAN
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/09/2021
ms.author: cherylmc
ms.openlocfilehash: a5b4427627b164c4f87d2c65203a69c1d2cc2699
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138824"
---
# <a name="configure-a-packet-capture-for-virtual-wan-site-to-site-vpn-powershell"></a>Konfigurieren einer Paketerfassung für Virtual WAN Site-to-Site VPN: PowerShell

Dieser Artikel hilft Ihnen bei der Erstellung einer Paketerfassung für ein Azure Virtual WAN Site-to-Site VPN-Gateway mit Azure PowerShell. Die Paketerfassung hilft Ihnen, das Problem auf bestimmte Teile des Netzes einzugrenzen. So können Sie feststellen, ob das Problem auf der lokalen Seite des Netzwerks, auf der Azure-Seite des Netzwerks oder irgendwo dazwischen liegt. Wenn Sie das Problem eingrenzen, können Sie es effizienter beheben und Abhilfemaßnahmen ergreifen.

Es gibt zwar einige allgemein verfügbare Tools für die Paketerfassung, aber die Erfassung relevanter Pakete mit diesen Tools kann mühsam sein, insbesondere in Szenarien mit hohem Verkehrsaufkommen. Die Filterfunktionen, die die Virtual WAN-Paketerfassung bietet, sind ein wichtiges Unterscheidungsmerkmal. Die virtuelle WAN-Paketaufzeichnung kann zusammen mit allgemein verfügbaren Paketaufzeichnungstools verwendet werden.

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass Sie die folgende Konfiguration bereits in Ihrer Umgebung eingerichtet haben:

* Ein virtuelles WAN und ein virtueller Hub.
* Ein Site-to-Site-VPN-Gateway, das im virtuellen Hub eingesetzt wird.
* Sie können auch Verbindungen haben, die VPN-Standorte mit Ihrem Site-to-Site-VPN-Gateway verbinden.

### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="set-up-the-environment"></a>Einrichten der Umgebung

Verwenden Sie den folgenden Befehl, um zu überprüfen, ob Sie das richtige Abonnement verwenden und als Benutzer angemeldet sind, der die Berechtigung hat, die Paketerfassung auf dem Site-to-Site-VPN-Gateway durchzuführen

```azurepowershell-interactive
$subid = “<insert Virtual WAN subscription ID here>”
Set-AzContext -SubscriptionId $subid
```

## <a name="create-a-storage-account-and-container"></a><a name="storage"></a> Erstellen Sie ein Speicherkonto und einen Container

Ein Speicherkonto wird verwendet, um die Ergebnisse von Paketaufzeichnungen zu speichern.

1. Erstellen Sie ein Speicherkonto. Weitere Schritte finden Sie unter [Speicherkonto erstellen](../storage/common/storage-account-create.md?tabs=azure-portal).
1. Erstellen Sie ein Container-Objekt in Ihrem Speicherkonto. Zu den Schritten siehe [Container erstellen](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

## <a name="generate-the-sas-url"></a><a name="URL"></a> Generieren Sie die SAS-URL

Wenn Sie eine Paketerfassung anhalten, müssen Sie die **SAS-URL** des von Ihnen erstellten Speichercontainers angeben. Die Ergebnisse Ihrer Paketerfassung werden über diese URL gespeichert. 

Führen Sie die folgenden Befehle aus, um eine SAS-URL (Shared Access Signature) zu erzeugen:

```azurepowershell-interactive
$rg = “<resource group name containing storage account>” 
$storeName = “<name of storage account> “
$containerName = “<name of container you want to store packet capture in>
$key = Get-AzStorageAccountKey -ResourceGroupName $rg -Name $storeName
$context = New-AzStorageContext -StorageAccountName  $storeName -StorageAccountKey $key[0].value
New-AzStorageContainer -Name $containerName -Context $context
$container = Get-AzStorageContainer -Name $containerName  -Context $context
$now = get-date
$sasurl = New-AzStorageContainerSASToken -Name $containerName -Context $context -Permission "rwd" -StartTime $now.AddHours(-1) -ExpiryTime $now.AddDays(1) -FullUri
```

## <a name="start-a-packet-capture"></a><a name="start"></a> Starten einer Paketerfassung

In diesem Abschnitt können Sie eine Paketerfassung für Ihr Site-to-Site-VPN-Gateway (alle Verbindungen) starten.

1. Um eine Paketerfassung durchzuführen, benötigen Sie den Wert **-Name** des Site-to-Site-VPN-Gateways. Um den Wert **-Name** zu finden, navigieren Sie im Azure-Portal zu Ihrem virtuellen Hub und klicken Sie unter **Connectivity** auf **VPN (Site-to-site)** .

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/vpn-gateway-name.png" alt-text="Abbildung: Name des Virtual WAN-Gateways" lightbox="./media//packet-capture-site-to-site-powershell/vpn-gateway-name.png":::

1. Um eine Paketaufzeichnung zu starten, führen Sie den folgenden Befehl aus:

   ```azurepowershell-interactive
   Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

## <a name="optional-specify-filters"></a><a name="filters"></a> Optional: Filter spezifizieren

Zur Vereinfachung Ihrer Paketerfassungen können Sie Filter für Ihre Paketerfassung angeben, um sich auf bestimmtes Verhalten zu konzentrieren.

>[!NOTE]
> Für „TracingFlags“ und „TCPFlags“ können mehrere Protokolle angegeben werden. Addieren Sie hierzu die numerischen Werte für die Protokolle, die Sie erfassen möchten (entspricht einem logischen ODER). Wenn Sie z. B. nur ESP- und OPVN-Pakete erfassen möchten, geben Sie einen TracingFlag-Wert von 8+1 = 9 an.  

| Parameter | BESCHREIBUNG | Standardwerte | Verfügbare Werte|
|--- |--- | --- | ---|
| TracingFlags | Ganze Zahl, die bestimmt, welche Pakettypen erfasst werden | 11 (ESP, IKE, OVPN) | ESP = 1, IKE = 2, OPVN = 8 |
| TCPFlags | Ganze Zahl, die bestimmt, welche Arten von TCP-Paketen erfasst werden | 0 (keine) | FIN = 1, SYN = 2, RST = 4, PSH = 8, ACK = 16, URG = 32, ECE = 64, CWR = 128| 
| MaxPacketBufferSize|Maximale Größe eines erfassten Pakets in Bytes. Pakete, deren Größe über den angegebenen Wert hinausgeht, werden abgeschnitten. |120|Any|
| MaxFileSize |Maximale Größe der Erfassungsdatei in MB. Da Erfassungen in einem Ringpuffer gespeichert werden, wird im Falle eines Überlaufs die FIFO-Methode angewendet. Das bedeutet, dass ältere Pakete zuerst entfernt werden.|100|Any|
|SourceSubnets|Pakete aus den angegebenen CIDR-Bereichen werden erfasst. Für die Angabe wird ein Array verwendet.|[] (alle IPv4-Adressen)|Array kommagetrennter IPv4-Subnetze|
| DestinationSubnets |Pakete, die für die angegebenen CIDR-Bereiche bestimmt sind, werden erfasst. Für die Angabe wird ein Array verwendet. |[] (alle IPv4-Adressen)|Array kommagetrennter IPv4-Subnetze|
|SourcePort |Pakete, deren Quelle sich in den angegebenen Bereichen befindet, werden erfasst. Für die Angabe wird ein Array verwendet.|[] (alle Ports)|Array kommagetrennter Ports|
|DestinationPort |Pakete, deren Ziel sich in den angegebenen Bereichen befindet, werden erfasst. Für die Angabe wird ein Array verwendet.|[] (alle Ports)|Array kommagetrennter Ports|
| CaptureSingleDirectionTrafficOnly |Bei „True“ wird in der Paketerfassung nur eine einzelne Richtung eines bidirektionalen Datenflusses angezeigt. Dadurch werden alle möglichen Kombinationen aus IP-Adresse und Ports erfasst.|True|True, False|
|Protokoll|Ein Array ganzer Zahlen, die IANA-Protokollen entsprechen. |[] (alle Protokolle)| Beliebige der [hier](https://www.iana.org/assignments/protocol-numbers/protocol-numbers.xhtml) angegebenen Protokolle |

Das folgende Beispiel zeigt eine Paketaufnahme unter Verwendung einer Filterzeichenfolge. Sie können die Parameter an Ihre Bedürfnisse anpassen.

```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -SasUrl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
```

## <a name="stop-a-packet-capture"></a><a name="stop"></a> Paketerfassung stoppen

Wir empfehlen, die Paketaufzeichnung mindestens 600 Sekunden lang laufen zu lassen, bevor Sie sie stoppen. Wenn Sie eine Paketerfassung stoppen, sind die Parameter ähnlich wie die Parameter im Abschnitt [Start einer Paketerfassung](#start). In dem Befehl wurde der SAS-URL-Wert im Abschnitt [Speicherkonto erstellen](#storage) generiert. Wenn der Parameter `SasUrl` nicht korrekt konfiguriert ist, kann die Erfassung mit Speicherfehlern fehlschlagen.

Wenn Sie bereit sind, die Paketaufzeichnung zu beenden, führen Sie den folgenden Befehl aus:

```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
```

## <a name="view-a-packet-capture"></a><a name="view"></a>Ansicht einer Paketaufnahme

In diesem Abschnitt können Sie die PCAP-Datei für die Paketerfassung herunterladen und anzeigen.

1. Navigieren Sie im Azure-Portal zu dem Speicherkonto, das Sie erstellt haben.
1. Klicken Sie auf **Containers**, um die Container für das Speicherkonto anzuzeigen.
1. Klicken Sie auf den Container, den Sie erstellt haben.
1. Navigieren Sie durch die Ordnerstruktur, um Ihre PCAP-Datei zu finden. Der Name und die Struktur des Ordners basieren auf dem Datum und der UTC-Zeit. Wenn Sie die PCAP-Datei gefunden haben, klicken Sie auf **Herunterladen**.

   :::image type="content" source="./media/packet-capture-site-to-site-powershell/download.png" alt-text="Grafik, die zeigt, wie man die Datei herunterlädt." lightbox="./media/packet-capture-site-to-site-powershell/download-expand.png":::
1. Bei der Paketerfassung werden Datendateien im PCAP-Format generiert. PCAP-Dateien können mit Wireshark oder mit einer anderen allgemein verfügbaren Anwendung geöffnet werden.

## <a name="next-steps"></a>Nächste Schritte

Um mehr über Virtual WAN zu erfahren, lesen Sie bitte die [Virtual WAN FAQ](virtual-wan-faq.md).
