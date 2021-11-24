---
title: Azure Virtual Desktop – RDP Shortpath für verwaltete Netzwerke
titleSuffix: Azure
description: Einrichten von RDP Shortpath für verwaltete Netzwerke für Azure Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: denisgun
ms.openlocfilehash: 43f4a8a1f467637f3a3703704361db525912f750
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179244"
---
# <a name="azure-virtual-desktop-rdp-shortpath-for-managed-networks"></a>Azure Virtual Desktop – RDP Shortpath für verwaltete Netzwerke

RDP Shortpath für verwaltete Netzwerke ist ein Feature von Azure Virtual Desktop und stellt eine direkte UDP-basierte Übertragung zwischen Remotedesktopclient und Sitzungshost her. RDP stellt mit diesem Transport Remotedesktop und RemoteApp bereit und sorgt gleichzeitig für mehr Zuverlässigkeit und konsistente Wartezeiten.

## <a name="key-benefits"></a>Hauptvorteile

* Der Transport von RDP Shortpath basiert auf dem äußerst effizienten [Universal Rate Control Protocol (URCP)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/). URCP ergänzt UDP um die aktive Überwachung der Netzwerkbedingungen und gewährleistet eine angemessene und vollständige Verbindungsnutzung. URCP arbeitet mit den für Remotedesktop erforderlichen niedrigen Verzögerungs- und Ausfallraten. Durch dynamisches Erlernen von Netzwerkparametern und Bereitstellen eines Protokolls mit Ratensteuerungsmechanismus kann URCP eine optimale Leistung erzielen.
* RDP Shortpath stellt die direkte Konnektivität zwischen Remotedesktopclient und Sitzungshost her. Eine direkte Konnektivität reduziert die Abhängigkeit von den Azure Virtual Desktop-Gateways, erhöht die Zuverlässigkeit der Verbindung und sorgt bei jeder Benutzersitzung für mehr verfügbare Bandbreite.
* Der Verzicht auf ein zusätzliches Relay verringert die Roundtripzeit und verbessert dadurch das Benutzererlebnis mit latenzempfindlichen Anwendungen und Eingabemethoden.
* RDP Shortpath unterstützt durch die Markierung mit DCSP (Differentiated Services Code Point) die [Konfiguration der Quality of Service (QoS)-Priorität](./rdp-quality-of-service-qos.md) für RDP-Verbindungen.
* Beim RDP Shortpath-Transport wird durch Angabe einer Drosselungsrate für jede Sitzung die [Beschränkung des ausgehenden Netzwerkdatenverkehrs](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) ermöglicht.

## <a name="connection-security"></a>Verbindungssicherheit

RDP Shortpath ergänzt die RDP-Mehrfachtransportfunktionen. Es soll den Reverse Connection-Transport nicht ersetzen, sondern ergänzen. Die Azure Virtual Desktop-Infrastruktur verwaltet das gesamte anfängliche Sitzungsbrokering.

Ihre Bereitstellung verwendet nur den vom Benutzer konfigurierten UDP-Port für eingehenden Shortpath-Datenverkehr, der über den Reverse Connection-Transport authentifiziert wird. Der RDP Shortpath-Listener ignoriert alle Verbindungsversuche, sofern sie nicht mit der Reverse Connection-Sitzung übereinstimmen.

RDP Shortpath verwendet eine TLS-Verbindung zwischen Client und Sitzungshost und nutzt dafür die Zertifikate des Sitzungshosts. Standardmäßig wird das Zertifikat, das für die RDP-Verschlüsselung verwendet wird, vom Betriebssystem bei der Bereitstellung selbst generiert. Bei Bedarf können Kunden zentral verwaltete Zertifikate bereitstellen, die von der Unternehmenszertifizierungsstelle ausgestellt werden. Weitere Informationen zur Konfiguration von Zertifikaten finden Sie in der [Windows Server-Dokumentation](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>RDP Shortpath-Verbindungssequenz

Nach der Einrichtung des [Reverse Connection-Transports](./network-connectivity.md) starten Client und Sitzungshost die RDP-Verbindung und handeln die Mehrfachtransportfunktionen aus.

Der Sitzungshost handelt die Mehrfachtransportfunktionen folgendermaßen aus:

1. Der Sitzungshost sendet die Liste der privaten und öffentlichen IPv4- und IPv6-Adressen an den Client.
2. Der Client startet den Hintergrundthread, um einen parallelen UDP-basierten Transport direkt zu einer der IP-Adressen des Hosts einzurichten.
3. Während der Client die angegebenen IP-Adressen prüft, setzt er die anfängliche Verbindungsherstellung über den Reverse Connection-Transport fort, um jegliche Verzögerung in der Benutzerverbindung zu vermeiden.
4. Wenn der Client über eine direkte Sichtverbindung verfügt, stellt der Client eine sichere TLS-Verbindung mit dem Sitzungshost her.
5. Nach dem Einrichten des Shortpath-Transports verschiebt RDP alle dynamischen virtuellen Kanäle (Dynamic Virtual Channels, DVCs), einschließlich Remotegrafik, Eingabe und Geräteumleitung, zum neuen Transport.
6. Wenn eine Firewall oder die Netzwerktopologie den Client daran hindert, eine direkte UDP-Verbindung herzustellen, setzt RDP den Vorgang mit einem Reverse Connection-Transport fort.

Das folgende Diagramm bietet eine allgemeine Übersicht über die RDP Shortpath-Netzwerkverbindung.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagramm der RDP Shortpath-Netzwerkverbindungen" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Requirements (Anforderungen)

Zur Unterstützung von RDP Shortpath braucht der Azure Virtual Desktop-Client eine direkte Sichtverbindung zum Sitzungshost. Eine direkte Sichtverbindung können Sie über eine der folgenden Technologien herstellen:

- Stellen Sie sicher, dass auf den Remoteclientcomputern entweder Windows 10 oder Windows 7 ausgeführt wird und dass der [Windows Desktop-Client](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) installiert ist. Clients ohne Windows werden derzeit nicht unterstützt.
- Verwenden Sie ein [privates ExpressRoute-Peering](../expressroute/expressroute-circuit-peerings.md).
- Verwenden Sie ein [Site-to-Site-VPN (IPsec-basiert)](../vpn-gateway/tutorial-site-to-site-portal.md).
- Verwenden Sie ein [Point-to-Site-VPN (IPsec-basiert)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md).
- Verwenden Sie eine [Zuweisung einer öffentlichen IP-Adresse](../virtual-network/ip-services/virtual-network-public-ip-address.md).

Wenn Sie andere VPN-Typen verwenden, um eine Verbindung mit Azure herzustellen, empfehlen wir die Verwendung eines UDP-basierten VPN (User Datagram Protocol). Wenngleich die meisten TCP-basierten VPN-Lösungen (Transmission Control Protocol) geschachteltes UDP unterstützen, führen sie zu einem Mehraufwand durch die TCP-Überlastungssteuerung, was die RDP-Leistung herabsetzt.

Eine direkte Sichtverbindung bedeutet, dass der Client eine direkte Verbindung mit dem Sitzungshost herstellen kann, ohne durch Firewalls blockiert zu werden.

## <a name="configure-rdp-shortpath-for-managed-networks"></a>Konfigurieren von RDP Shortpath für verwaltete Netzwerke

Wenn Sie RDP Shortpath für verwaltete Netzwerke nutzen möchten, müssen Sie den RDP Shortpath-Listener auf dem Sitzungshost aktivieren. Sie können RDP Shortpath für eine beliebige Anzahl von Sitzungshosts aktivieren, die in Ihrer Umgebung verwendet werden. Es ist jedoch nicht erforderlich, RDP Shortpath auf allen Hosts in Ihrem Hostpool zu aktivieren.

So aktivieren Sie den RDP Shortpath-Listener

1. Zunächst müssen Sie administrative Vorlagen installieren, durch die Regeln und Einstellungen für Azure Virtual Desktop hinzugefügt werden. Laden Sie die [Azure Virtual Desktop-Richtlinienvorlagendatei](https://aka.ms/avdgpo) (AVDGPTemplate.cab) herunter und extrahieren Sie den Inhalt der CAB-Datei und des ZIP-Archivs.
2. Kopieren Sie die Datei **terminalserver-avd.admx**, und fügen Sie sie in den Ordner **%windir%\PolicyDefinitions** ein.
3. Kopieren Sie die Datei **en-us\terminalserver-avd.adml**, und fügen Sie sie in den Ordner **%windir%\PolicyDefinitions\en-us** ein.
4. Bestätigen Sie nun, dass die Dateien ordnungsgemäß kopiert wurden. Öffnen Sie hierzu den **Gruppenrichtlinien-Editor**, und navigieren Sie zu **Computerrichtlinien** > **Administrative Vorlagen** > **Windowskomponenten** > **Remotedesktopdienste** > **Remotedesktop-Sitzungshost** > **Azure Virtual Desktop**.
5. Ihnen sollten eine oder mehrere Azure Virtual Desktop-Richtlinien angezeigt werden, wie im folgenden Screenshot dargestellt.

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="Screenshot des Gruppenrichtlinien-Editors" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!NOTE]
   > Sie können auch administrative Vorlagen für die Gruppenrichtlinie „Zentraler Speicher in Ihrer Active Directory-Domäne“ installieren.
   > Weitere Informationen über den zentralen Speicher für administrative Vorlagedateien für Gruppenrichtlinien finden Sie unter [Erstellen und Verwalten des zentralen Speichers für administrative Vorlagendateien für Gruppenrichtlinien in Windows](/troubleshoot/windows-client/group-policy/create-and-manage-central-store).

6. Öffnen Sie die Richtlinie **RDP Shortpath für verwaltete Netzwerke aktivieren**, und legen Sie sie auf **Aktiviert** fest. Wenn Sie diese Richtlinieneinstellung aktivieren, können Sie auch die Portnummer konfigurieren, die der Azure Virtual Desktop-Sitzungshost zum Lauschen auf eingehende Verbindungen verwendet. Der Standardport lautet 3390.
7. Starten Sie Ihren Sitzungshost neu, um die Änderungen zu übernehmen.

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Konfigurieren von Windows Defender Firewall mit erweiterter Sicherheit

Um eingehenden Netzwerkdatenverkehr für RDP Shortpath zuzulassen, erstellen Sie mithilfe von Windows Defender Firewall und dem Knoten „Erweiterte Sicherheit“ im MMC-Snap-In der Gruppenrichtlinienverwaltung die Firewallregeln.

1. Öffnen Sie die Gruppenrichtlinien-Verwaltungskonsole für [Windows Defender Firewall mit erweiterter Sicherheit](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. Wählen Sie im Navigationsbereich **Regeln für eingehenden Datenverkehr** aus.
3. Wählen Sie **Aktion** und dann **Neue Regel** aus.
4. Wählen Sie im Assistenten für neue Regeln für eingehenden Datenverkehr auf der Seite **Regeltyp** die Option **Benutzerdefiniert** aus, und wählen Sie dann **Weiter** aus.
5. Wählen Sie auf der Seite **Programm** die Option **Dieser Programmpfad** aus, geben Sie „%SystemRoot%\system32\svchost.exe“ ein, und wählen Sie dann **Weiter** aus.
6. Wählen Sie auf der Seite **Protokoll und Ports** „UDP“ als Protokolltyp aus. Wählen Sie unter **Lokaler Port** die Option „Bestimmte Ports“ aus, und geben Sie den konfigurierten UDP-Port ein. Wenn Sie die Standardeinstellungen übernommen haben, lautet die Portnummer 3390.
7. Auf der Seite **Bereich** können Sie angeben, dass die Regel nur für Netzwerkdatenverkehr zu und von den auf dieser Seite eingegebenen IP-Adressen gelten soll. Nehmen Sie die Konfiguration entsprechend Ihrem Design vor, und wählen Sie dann **Weiter** aus.
8. Wählen Sie auf der Seite **Aktion** die Option **Verbindung zulassen** aus, und wählen Sie dann **Weiter** aus.
9. Wählen Sie auf der Seite **Profil** die Netzwerkadresstypen aus, für die diese Regel gilt, und wählen Sie dann **Weiter** aus.
10. Geben Sie auf der Seite **Name** einen Namen und eine Beschreibung für Ihre Regel ein, und wählen Sie dann **Fertig stellen** aus.

Überprüfen Sie anschließend, ob die neue Regel dem Format im folgenden Screenshot entspricht.
:::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="Screenshot der Registerkarte „Allgemein“ zur Firewallkonfiguration für RDP Shortpath-Netzwerkverbindungen mit ausgewählter Option „Verbindung zulassen“" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Screenshot der Registerkarte „Programme und Dienste“ zur Firewallkonfiguration für RDP Shortpath-Netzwerkverbindungen mit ausgewählter Option „Remotedesktopdienste“" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Screenshot der Registerkarte „Protokolle und Ports“ zur Firewallkonfiguration für RDP Shortpath-Netzwerkverbindungen mit konfiguriertem UDP-Port 3390" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Sie können die Windows-Firewall auch mit PowerShell konfigurieren:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Verwenden von PowerShell zum Konfigurieren von Windows Defender Firewall

Sie können die Gruppenrichtlinie auch mithilfe von PowerShell konfigurieren, indem Sie das folgende Cmdlet ausführen.

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Konfigurieren einer Azure-Netzwerksicherheitsgruppe

Wenn Sie den Zugriff auf den RDP Shortpath-Listener über Netzwerksicherheitsgrenzen hinweg zulassen möchten, müssen Sie die Azure-Netzwerksicherheitsgruppe konfigurieren und UDP-Port 3390 für den eingehenden Datenverkehr zulassen.
Beachten Sie die [Dokumentation zu Netzwerksicherheitsgruppen](../virtual-machines/windows/nsg-quickstart-portal.md), um mit den folgenden Parametern eine Sicherheitsregel für den eingehenden Datenverkehr zu erstellen, die den Datenverkehr erlaubt:

* **Quelle** - **Beliebig** oder der IP-Adressbereich, in dem sich die Clients befinden
* **Quellportbereiche** -  **\***
* **Ziel**  -  **Beliebig**
* **Zielportbereiche** - **3390**
* **Protokoll** - **UDP**
* **Aktion** - **Zulassen**
* Optional können Sie die **Priorität** ändern. Die Priorität wirkt sich auf die Reihenfolge aus, in der Regeln angewendet werden: je niedriger der numerische Wert, desto früher wird die Regel angewendet.
* **Name** – **RDP Shortpath**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Deaktivieren von RDP Shortpath für ein bestimmtes Subnetz

Wenn bestimmte Subnetze für die Verwendung des RDP Shortpath-Transports gesperrt werden müssen, können Sie eine weitere Netzwerksicherheitsgruppe zur Angabe der richtigen Quell-IP-Adressbereiche konfigurieren.

## <a name="verify-your-network-connectivity"></a>Überprüfen Sie die Konnektivität Ihres Netzwerks.

Als Nächstes müssen Sie sicherstellen, dass Ihr Netzwerk RDP Shortpath verwendet. Hierzu können Sie entweder das Dialogfeld „Verbindungsinformationen“ oder Log Analytics verwenden.

### <a name="connection-information-dialog"></a>Dialogfeld „Verbindungsinformationen“

Wenn Sie überprüfen möchten, ob die Verbindungen RDP Shortpath verwenden, öffnen Sie das Dialogfeld „Verbindungsinformationen“, indem Sie auf der **Verbindungssymbolleiste** im oberen Bildschirmbereich auf das Antennensymbol klicken, wie im folgenden Screenshot abgebildet.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Abbildung der Remotedesktop-Verbindungssymbolleiste":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Abbildung des Remotedesktop-Dialogfelds „Verbindungsinformationen“":::

### <a name="using-event-logs"></a>Mit Ereignisprotokollen

So stellen Sie sicher, dass die Sitzung RDP Shortpath-Transport verwendet

1. Verwenden Sie den Azure Virtual Desktop-Client Ihrer Wahl, um eine Verbindung mit Ihrem VM-Desktop herzustellen.
2. Öffnen Sie die **Ereignisanzeige**, und wechseln Sie dann zu **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**.
3. Wenn die Ereignis-ID 131 angezeigt wird, verwendet Ihr Netzwerk den RDP Shortpath-Transport.

### <a name="use-log-analytics"></a>Verwenden von Log Analytics

Wenn Sie [Azure Log Analytics](./diagnostics-log-analytics.md) verwenden, können Sie durch Abfragen der [WVDConnections-Tabelle](/azure/azure-monitor/reference/tables/wvdconnections) Verbindungen überwachen. Die Spalte „UdpUse“ gibt an, ob der Azure Virtual Desktop-RDP-Stapel das UDP-Protokoll für die aktuelle Benutzerverbindung verwendet.
Mögliche Werte sind:

* **0**: Benutzerverbindung verwendet RDP Shortpath nicht
- **1**: Die Benutzerverbindung verwendet RDP Shortpath für verwaltete Netzwerke.
  
Mit der folgenden Abfrageliste können Sie Verbindungsinformationen überprüfen. Sie können diese Abfrage im [Log Analytics-Abfrage-Editor](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query) ausführen. Ersetzen Sie für jede Abfrage `userupn` durch den UPN des Benutzers, den Sie suchen möchten.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="verify-shortpath-listener"></a>Überprüfen des Shortpath-Listeners

Mit dem folgenden PowerShell-Befehl auf dem Sitzungshost können Sie überprüfen, ob der UDP-Listener aktiviert ist:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Wenn er aktiviert ist, sollte eine Ausgabe wie die folgende angezeigt werden:

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Bei einem Konflikt können Sie den Prozess identifizieren, der den Port blockiert, indem Sie den folgenden Befehl ausführen:

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Deaktivieren von RDP Shortpath

In einigen Fällen müssen Sie möglicherweise den RDP Shortpath-Transport deaktivieren. RDP Shortpath wird mithilfe der Gruppenrichtlinie deaktiviert.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Deaktivieren von RDP Shortpath auf dem Client

Um RDP Shortpath für einen bestimmten Client zu deaktivieren, können Sie die folgende Gruppenrichtlinie verwenden und die UDP-Unterstützung deaktivieren:

1. Führen Sie auf dem Client **gpedit.msc** aus.
2. Navigieren Sie zu **Computerkonfiguration** > **Administrative Vorlagen** > **Windows-Komponenten** > **Remotedesktopdienste** > **Remotedesktopverbindungs-Client**.
3. Legen Sie die Einstellung **UDP auf Client deaktivieren** auf **Aktiviert** fest.

### <a name="disable-rdp-shortpath-on-the-session-host"></a>Deaktivieren von RDP Shortpath auf dem Sitzungshost

Um RDP Shortpath für einen bestimmten Sitzungshost zu deaktivieren, können Sie die folgende Gruppenrichtlinie verwenden und die UDP-Unterstützung deaktivieren:

1. Führen Sie auf dem Sitzungshost **gpedit.msc** aus.
2. Wechseln Sie zu **Computerkonfiguration > Administrative Vorlagen > Windows-Komponenten > Remotedesktopdienste > Remotedesktop-Verbindungshost > Verbindungen**.
3. Legen Sie die Einstellung **RDP-Transportprotokolle auswählen** auf **Nur TCP** fest.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zur Netzwerkkonnektivität von Azure Virtual Desktop finden Sie unter [Grundlegendes zur Azure Virtual Desktop-Netzwerkkonnektivität](network-connectivity.md).
* Informationen zu den ersten Schritten mit Quality of Service (QoS) für Azure Virtual Desktop finden Sie unter [Implementieren von Quality of Service (QoS) für Azure Virtual Desktop](rdp-quality-of-service-qos.md).
