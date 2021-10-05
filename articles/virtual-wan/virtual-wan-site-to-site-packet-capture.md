---
title: 'Tutorial: Durchführen einer Paketerfassung für Site-to-Site-Verbindungen von Azure Virtual WAN'
description: In diesem Tutorial erfahren Sie, wie Sie eine Paketerfassung im Site-to-Site-VPN-Gateway von Virtual WAN durchführen.
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 04/13/2021
ms.author: wellee
Customer intent: As someone with a networking background using Virtual WAN, I want to perform a packet capture on my Site-to-site VPN Gateway.
ms.openlocfilehash: 8970a275e295a54581876f13a2d4b0112c0116d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128581983"
---
# <a name="perform-packet-capture-on-the-azure-virtual-wan-site-to-site-vpn-gateway"></a>Durchführen einer Paketerfassung im Site-to-Site-VPN-Gateway von Azure Virtual WAN 

Konnektivitäts- und leistungsbezogene Probleme sind häufig komplex. Es kann viel Zeit und Mühe kosten, die Ursache des Problems einzugrenzen. Mithilfe der Paketerfassung können Sie den Umfang eines Problems auf bestimmte Teile des Netzwerks eingrenzen. Sie kann Ihnen dabei helfen, festzustellen, ob das Problem auf der Kundenseite des Netzwerks, auf der Azure-Seite des Netzwerks oder irgendwo dazwischen vorliegt. Nachdem Sie das Problem eingegrenzt haben, ist es effizienter, das Problem zu debuggen und Abhilfemaßnahmen zu ergreifen.

Im folgenden Artikel erfahren Sie, wie Sie eine Paketerfassung im Site-to-Site-VPN-Gateway von Azure Virtual WAN starten und beenden. Diese Funktion ist aktuell nur über PowerShell verfügbar.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Schritte in diesem Artikel ausführen können, müssen Sie die folgende Konfiguration bereits in Ihrer Umgebung eingerichtet haben:

* Eine Virtual WAN-Instanz, einen virtuellen Hub und ein im virtuellen Hub bereitgestelltes Site-to-Site-VPN-Gateway. Es können auch Verbindungen zwischen VPN-Standorten und Ihrem Site-to-Site-VPN-Gateway vorhanden sein.


### <a name="working-with-azure-powershell"></a>Arbeiten mit Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="set-up-the-environment"></a>Einrichten der Umgebung

Führen Sie das folgende Skript in PowerShell aus, um sich zu vergewissern, dass Sie sich im richtigen Abonnementkontext befinden. Dadurch wird sichergestellt, dass Sie als Benutzer mit Berechtigungen zum Durchführen der Paketerfassung für das Site-to-Site-VPN-Gateway angemeldet sind.

   ```azurepowershell-interactive
    $subid = “<insert Virtual WAN subscription ID here>”
    Set-AzContext -SubscriptionId $subid
   ```

## <a name="create-a-storage-account"></a><a name="createstorage"></a> Erstellen eines Speicherkontos

Sie müssen ein Speicherkonto unter Ihrem Azure-Abonnement erstellen, um die Ergebnisse Ihrer Paketerfassung zu speichern. Eine Anleitung zum Erstellen eines Speicherkontos finden Sie in [diesem Dokument](.././storage/common/storage-account-create.md).

Führen Sie nach dem Erstellen Ihres Kontos die folgenden Befehle aus, um eine SAS-URL (Shared Access Signature) zu generieren. Die Ergebnisse Ihrer Paketerfassung werden über diese URL gespeichert.
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

## <a name="start-the-packet-capture"></a>Starten der Paketerfassung

Beim Starten der Paketerfassung haben Sie zwei Optionen: Sie können Pakete über eine einzelne VPN-Verbindung oder für das gesamte Site-to-Site-VPN-Gateway erfassen. Erfassungen für VPN-Verbindungen können nur für sechs Verbindungen gleichzeitig durchgeführt werden.

### <a name="packet-capture-on-a-site-to-site-vpn-gateway-all-connections"></a>Paketerfassung für ein Site-to-Site-VPN-Gateway (alle Verbindungen)

Führen Sie die folgenden Befehle aus. Den Namen des Site-to-Site-VPN-Gateways finden Sie, indem Sie zu Ihrem virtuellen Hub navigieren und unter „Konnektivität“ auf „VPN (Site-to-Site)“ klicken.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png" alt-text="Abbildung: Name des Virtual WAN-Gateways" lightbox="./media/virtual-wan-pcap-screenshots/vpn-gateway-name.png":::

   ```azurepowershell-interactive
Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>"
   ```

### <a name="packet-capture-on-specific-site-to-site-vpn-connections"></a>Paketerfassung für bestimmte Site-to-Site-VPN-Verbindungen

>[!NOTE]
> Beachten Sie, dass eine Paketerfassung nur für fünf VPN-Verbindungen gleichzeitig durchgeführt werden kann.


Verwenden Sie den folgenden PowerShell-Befehl, um den Namen Ihrer VPN-Standortverbindung zu ermitteln.

   ```azurepowershell-interactive
$connections = Get-AzVpnConnection -ResourceGroupName $rg -ParentResourceName "<name of the Gateway"
$connections
$connections.name
   ```
Im obigen PowerShell-Block enthalten die lokalen *Variablenverbindungen* ein Array aller VPN-Standorte, die mit dem Site-to-Site-VPN Gateway verbunden sind. Suchen Sie den Eintrag, der dem VPN-Standort entspricht, an dem Sie eine Paketerfassung durchführen möchten. Kopieren Sie das Feld *name* und verwenden Sie es im folgenden Start-AzVpnConnectionPacketCapture Befehl als Parameter *-Name*. Alternativ können Sie die Namen aller Verbindungen anzeigen, indem Sie auf \*connections.name* zugreifen. 

Führen Sie die folgenden Schritte aus, um den Namen der Links zu finden. Navigieren Sie zu Ihrem virtuellen Hub, und klicken Sie unter Konnektivität auf VPN (Site-to-Site). Navigieren Sie anschließend zu dem VPN-Standort, für den Sie die Paketerfassung durchführen möchten, und klicken Sie auf der rechten Seite auf die drei Punkte. Klicken Sie im daraufhin angezeigten Menü auf **VPN-Verbindung bearbeiten**.

:::image type="content" source="./media/virtual-wan-pcap-screenshots/sample-connection.png" alt-text="Abbildung: Ermitteln der Namen der VPN-Verbindungen" lightbox="./media/virtual-wan-pcap-screenshots/sample-connection.png":::

Den Namen der Links, die mit einem bestimmten VPN-Standort verbunden sind, finden Sie, indem Sie auf den VPN-Standort aus dem vorherigen Abschnitt klicken und sich die Tabelle **Links** ansehen. 

:::image type="content" source="./media/virtual-wan-pcap-screenshots/link-name-sample.png" alt-text="Abbildung: Ermitteln des VPN-Linknamens" lightbox="./media/virtual-wan-pcap-screenshots/link-name-sample.png":::

   ```azurepowershell-interactive
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links eg. "link1,link2">”
   ```

## <a name="optional-specifying-filters"></a>Optional: Angeben von Filtern

Für die Paketerfassung stehen verschiedene gängige Tools zur Verfügung. Eine relevante Paketerfassung kann mit diesen Tools umständlich sein, insbesondere in Szenarien mit hohem Datenaufkommen. Zur Vereinfachung Ihrer Paketerfassungen können Sie Filter für Ihre Paketerfassung angeben, um sich auf bestimmtes Verhalten zu konzentrieren. Folgende Parameter stehen zur Verfügung:

>[!NOTE]
> Für „TracingFlags“ und „TCPFlags“ können mehrere Protokolle angegeben werden. Addieren Sie hierzu die numerischen Werte für die Protokolle, die Sie erfassen möchten (entspricht einem logischen ODER). Wenn Sie also beispielsweise nur ESP- und OPVN-Pakete erfassen möchten, geben Sie den TracingFlag-Wert „9“ (8 + 1) an.  

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

Im Anschluss finden Sie ein Beispiel für eine Paketerfassung mit einer Filterzeichenfolge. Sie können die Parameter an Ihre Anforderungen anpassen. Orientieren Sie sich dabei an der obigen Tabelle.  

   ```azurepowershell-interactive
$filter="{`"TracingFlags`":11,`"MaxPacketBufferSize`":120,`"MaxFileSize`":500,`"Filters`":[{`"SourceSubnets`":[`"10.19.0.4/32`",`"10.20.0.4/32`"],`"DestinationSubnets`":[`"10.20.0.4/32`",`"10.19.0.4/32`"],`"TcpFlags`":9,`"CaptureSingleDirectionTrafficOnly`":true}]}"
Start-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name "<name of the VPN connection>" -ParentResourceName “<name of the Gateway>” -LinkConnection “<comma separated list of links>” -Sasurl $sasurl -FilterData $filter

Start-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name "<name of the Gateway>" -FilterData $filter
   ```

## <a name="stopping-the-packet-capture"></a>Beenden der Paketerfassung
Es empfiehlt sich, die Paketerfassung mindestens 600 Sekunden lang laufen zu lassen. Aufgrund von Synchronisierungsproblemen zwischen mehreren Komponenten im Pfad stellen kürzere Paketerfassungen möglicherweise keine vollständigen Daten bereit. Wenn Sie die Paketerfassung beenden möchten, führen Sie den folgenden Befehl aus:

Die Parameter ähneln denen aus dem Abschnitt zum Starten einer Paketerfassung. Die SAS-URL wurde im Abschnitt [Erstellen eines Speicherkontos](#createstorage) generiert. Wenn der `SASurl`-Parameter nicht ordnungsgemäß konfiguriert ist, treten bei der Ablaufverfolgung möglicherweise Speicherfehler auf.

### <a name="gateway-level-packet-capture"></a>Paketerfassung auf Gatewayebene

   ```azurepowershell-interactive
Stop-AzVpnGatewayPacketCapture -ResourceGroupName $rg -Name <GatewayName> -SasUrl $sasurl
   ```

### <a name="connection-level-packet-captures"></a>Paketerfassungen auf Verbindungsebene

   ```azurepowershell-interactive
Stop-AzVpnConnectionPacketCapture -ResourceGroupName $rg -Name <name of the VPN connection> -ParentResourceName "<name of VPN Gateway>" -LinkConnectionName <comma separated list of links e.g. "link1,link2">-SasUrl $sasurl
   ```

## <a name="viewing-your-packet-capture"></a>Anzeigen Ihrer Paketerfassung

Navigieren Sie im Azure-Portal zu dem Speicherkonto, das im Abschnitt „Erstellen eines Speicherkontos“ erstellt wurde. Klicken Sie anschließend auf Ihren Container, und laden Sie die Datei herunter. Bei der Paketerfassung werden Datendateien im PCAP-Format generiert. PCAP-Dateien können mit Wireshark oder mit einer anderen allgemein verfügbaren Anwendung geöffnet werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Virtual WAN finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> * [Virtual WAN – Häufig gestellte Fragen](virtual-wan-faq.md)
