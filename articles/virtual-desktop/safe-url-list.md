---
title: Liste der erforderlichen URLs für Azure Virtual Desktop – Azure
description: Eine Liste der URLs, die Sie entsperren müssen, um sicherzustellen, dass Ihre Azure Virtual Desktop-Bereitstellung wie vorgesehen funktioniert.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: ea5cd9dffeb8efdeb9dc29fa9d38696973285e47
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549234"
---
# <a name="required-url-list"></a>Erforderliche URL-Liste

Damit Sie Azure Virtual Desktop bereitstellen und verwenden können, müssen Sie bestimmte URLs freigeben, damit Ihre VMs jederzeit darauf zugreifen können. In diesem Artikel werden die erforderlichen URLs aufgelistet, die Sie für die ordnungsgemäße Funktion von Azure Virtual Desktop entsperren müssen. 

>[!IMPORTANT]
>Azure Virtual Desktop unterstützt keine Bereitstellungen, die die in diesem Artikel aufgeführten URLs blockieren.

## <a name="required-url-check-tool"></a>Erforderliches URL-Überprüfungstool

Das erforderliche URL-Überprüfungstool überprüft URLs und zeigt an, ob auf die URLs zugegriffen werden kann, die der virtuelle Computer für seine Funktionen benötigt. Falls nicht, listet das Tool die URLs auf, auf die nicht zugegriffen werden kann, damit Sie die Blockierung bei Bedarf entsperren können.

Dabei sollten Sie unbedingt folgendes berücksichtigen:

- Sie können das erforderliche URL-Überprüfungstool nur für Bereitstellungen in kommerziellen Clouds verwenden.
- Das erforderliche URL-Überprüfungstool kann URLs nicht mit Platzhaltern überprüfen. Stellen Sie daher sicher, dass Sie die Blockierung dieser URLs zuerst entsperren.

### <a name="requirements"></a>Anforderungen

Sie benötigen Folgendes, um das erforderliche URL-Überprüfungstool zu verwenden:

- Ihr virtueller Computer muss über ein .NET 4.6.2-Framework verfügen
- RDAgent-Version 1.0.2944.400 oder höher
- Die Datei WVDAgentUrlTool.exe muss sich im gleichen Ordner wie die Datei WVDAgentUrlTool.config befinden

### <a name="how-to-use-the-required-url-check-tool"></a>Wie das erforderliche URL-Überprüfungstool verwendet wird

Wie das erforderliche URL-Überprüfungstool verwendet wird:

1. Öffnen Sie als Administrator eine Eingabeaufforderung auf ihrem VM.
2. Führen Sie den folgenden Befehl aus, um das Verzeichnis in denselben Ordner wie den aktuelle Build-Agent zu ändern (in diesem Beispiel RDAgent_1.0.2944.1200):

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Führen Sie den folgenden Befehl aus:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. Nachdem Sie die Datei ausgeführt haben, wird eine Liste der URLs angezeigt, auf die zugegriffen werden kann und auf die nicht zugegriffen werden kann.

    Der folgende Screenshot zeigt beispielsweise ein Szenario, in dem Sie die Blockierung von zwei erforderlichen URLs ohne Platzhalter entsperren müssen:

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Ausgabe nicht zugänglicher URLs.](media/noaccess.png)
    
    Die Ausgabe sollte so aussehen, nachdem Sie die Blockierung aller erforderlichen URLs aufgehoben haben, die keine Platzhalter-URLs sind:

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Ausgabe nicht zugänglicher URLs.](media/access.png)

## <a name="virtual-machines"></a>Virtuelle Computer

Die Azure-VMs, die Sie für Azure Virtual Desktop erstellen, müssen über Zugriff auf die folgenden URLs in der kommerziellen Azure-Cloud verfügen:

|Adresse|Ausgehender TCP-Port|Zweck|Diensttag|
|---|---|---|---|
|*.wvd.microsoft.com|443|Dienstdatenverkehr|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Agent-Datenverkehr|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Agent-Datenverkehr|AzureCloud|
|*xt.blob.core.windows.net|443|Agent-Datenverkehr|AzureCloud|
|*eh.servicebus.windows.net|443|Agent-Datenverkehr|AzureCloud|
|*xt.table.core.windows.net|443|Agent-Datenverkehr|AzureCloud|
|*xt.queue.core.windows.net|443|Agent-Datenverkehr|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Aktivierung von Windows|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Agent- und SXS-Stapelupdates|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Unterstützung des Azure-Portals|AzureCloud|
| 169.254.169.254 | 80 | [Azure-Instanzmetadatendienst-Endpunkt](../virtual-machines/windows/instance-metadata-service.md) | – |
| 168.63.129.16 | 80 | [Sitzungshost-Systemüberwachung](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | – |

>[!IMPORTANT]
>Azure Virtual Desktop unterstützt jetzt das FQDN-Tag. Weitere Informationen finden Sie unter [Verwenden von Azure Firewall zum Schutz von Windows Virtual Desktop-Bereitstellungen](../firewall/protect-azure-virtual-desktop.md).
>
>Wir empfehlen Ihnen, FQDN- oder Diensttags anstelle von URLs zu verwenden, um Dienstprobleme zu verhindern. Die aufgeführten URLs und Tags beziehen sich nur auf Azure Virtual Desktop-Websites und -Ressourcen. Sie enthalten keine URLs für andere Dienste, z. B. Azure Active Directory.

Die Azure-VMs, die Sie für Azure Virtual Desktop erstellen, müssen über Zugriff auf die folgenden URLs in der Azure Government-Cloud verfügen:

|Adresse|Ausgehender TCP-Port|Zweck|Diensttag|
|---|---|---|---|
|*.wvd.azure.us|443|Dienstdatenverkehr|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Agent-Datenverkehr|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Agent-Datenverkehr|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Agent-Datenverkehr|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Agent-Datenverkehr|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Agent-Datenverkehr|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Agent-Datenverkehr|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Aktivierung von Windows|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Agent- und SXS-Stapelupdates|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Unterstützung des Azure-Portals|AzureCloud|
| 169.254.169.254 | 80 | [Azure-Instanzmetadatendienst-Endpunkt](../virtual-machines/windows/instance-metadata-service.md) | – |
| 168.63.129.16 | 80 | [Sitzungshost-Systemüberwachung](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | – |

In der folgenden Tabelle sind optionale URLs aufgeführt, auf die Ihre virtuellen Azure-Computer Zugriff haben können:

|Adresse|Ausgehender TCP-Port|Zweck|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Authentifizierung bei Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Telemetriedienst|Keine|
|www.msftconnecttest.com|443|Ermittelt, ob das Betriebssystem mit dem Internet verbunden ist.|Keine|
|*.prod.do.dsp.mp.microsoft.com|443|Windows-Update|Keine|
|login.windows.net|443|Anmelden bei Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Updates für die OneDrive-Clientsoftware|oneclient.sfx.ms|
|*.digicert.com|443|Überprüfung der Zertifikatsperre|Keine|
|*.azure-dns.com|443|Azure DNS-Auflösung|Keine|
|*.azure-dns.net|443|Azure DNS-Auflösung|Keine|

>[!NOTE]
>Azure Virtual Desktop verfügt derzeit über keine Liste mit IP-Adressbereichen, die Sie entsperren können, um Netzwerkdatenverkehr zuzulassen. Momentan wird nur das Freigeben von spezifischen URLs unterstützt.
>
>Wenn Sie eine Firewall der nächsten Generation (Next Generation Firewall, NGFW) verwenden, müssen Sie eine dynamische Liste verwenden, die speziell für Azure-IP-Adressen erstellt wurde, um sicherzustellen, dass Sie eine Verbindung herstellen können.
>
>Eine Liste der sicheren URLs für Office, einschließlich der erforderlichen URLs für Azure Active Directory, finden Sie unter [Office 365-URLs und -IP-Adressbereiche](/office365/enterprise/urls-and-ip-address-ranges).
>
>Sie müssen das Platzhalterzeichen (*) für URLs für Dienstdatenverkehr verwenden. Wenn Sie kein Platzhalterzeichen (*) für Agent-Datenverkehr verwenden möchten, ermitteln Sie wie folgt die URLs ohne Platzhalter:
>
>1. Registrieren Sie Ihre VMs beim Azure Virtual Desktop-Hostpool.
>2. Öffnen Sie die **Ereignisanzeige**, navigieren Sie dann zu **Windows-Protokolle** > **Anwendung** > **WVD-Agent**, und suchen Sie nach der Ereignis-ID 3701.
>3. Heben Sie die Blockierung von URLs auf, die Sie unter der Ereignis-ID 3701 finden. Die URLs unter der Ereignis-ID 3701 sind regionsspezifisch. Sie müssen den Freigabeprozess mit den relevanten URLs für jede Region wiederholen, in der Sie Ihre virtuellen Computer bereitstellen möchten.

## <a name="remote-desktop-clients"></a>Remotedesktopclients

Alle von Ihnen verwendeten Remotedesktopclients müssen auf die folgenden URLs zugreifen können:

|Adresse|Ausgehender TCP-Port|Zweck|Client(s)|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Dienstdatenverkehr|All|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Problembehandlung für Daten|All|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|All|Keine|
|aka.ms|443|Microsoft-URL-Verkürzung|All|Keine|
|docs.microsoft.com|443|Dokumentation|All|Keine|
|privacy.microsoft.com|443|Datenschutzbestimmungen|All|Keine|
|query.prod.cms.rt.microsoft.com|443|Clientupdates|Windows Desktop|Keine|

>[!IMPORTANT]
>Das Öffnen dieser URLs ist für einen zuverlässigen Clientbetrieb von entscheidender Bedeutung. Der Zugriff auf diese URLs darf nicht blockiert werden; andernfalls wird die Dienstfunktionalität beeinträchtigt.
>
>Diese URLs gelten lediglich für die Clientstandorte und -ressourcen. Diese Liste enthält keine URLs für andere Dienste wie Azure Active Directory. Azure Active Directory-URLs finden Sie unter ID 56, 59 und 125 in [URLs und IP-Adressbereiche von Office 365](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).
