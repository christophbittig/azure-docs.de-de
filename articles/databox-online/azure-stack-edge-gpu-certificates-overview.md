---
title: Übersicht über die Zertifikate für Azure Stack Edge Pro GPU, Pro R und Mini R
description: Hier erhalten Sie einen Überblick über alle Zertifikate, die auf einem Azure Stack Edge Pro GPU-Gerät verwendet werden können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/01/2021
ms.author: alkohli
ms.openlocfilehash: c5ea156773335ed31fe6c6e728f496f91c6de634
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215631"
---
# <a name="what-are-certificates-on-azure-stack-edge-pro-gpu"></a>Was sind Zertifikate auf einem Azure Stack Edge Pro GPU-Gerät?

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel werden die Zertifikattypen beschrieben, die auf Ihrem Azure Stack Edge Pro GPU-Gerät installiert werden können. Der Artikel enthält auch die Details zu jedem einzelnen Zertifikattyp.  

## <a name="about-certificates"></a>Informationen zu Zertifikaten

Ein Zertifikat stellt eine Verbindung zwischen einem **öffentlichen Schlüssel** und einer Entität (z. B. Domänenname) her, die durch eine vertrauenswürdige dritte Instanz (z. B. eine  **Zertifizierungsstelle**) **signiert** (verifiziert) wurde.  Ein Zertifikat bietet eine bequeme Möglichkeit, vertrauenswürdige öffentliche Verschlüsselungsschlüssel zu verteilen. Zertifikate stellen damit sicher, dass Ihre Kommunikation vertrauenswürdig ist und dass Sie verschlüsselte Informationen an den richtigen Server senden. 

## <a name="deploying-certificates-on-device"></a>Bereitstellen von Zertifikaten auf einem Gerät

Sie können auf Ihrem Azure Stack Edge-Gerät die selbstsignierten Zertifikate verwenden oder eigene Zertifikate bereitstellen.

- **Vom Gerät generierte Zertifikate**: Bei der Erstkonfiguration Ihres Geräts werden automatisch selbstsignierte Zertifikate generiert. Bei Bedarf können Sie diese Zertifikate über die lokale Webbenutzeroberfläche neu generieren. Sobald die Zertifikate neu generiert sind, laden Sie sie herunter und importieren Sie die Zertifikate auf den Clients, die für den Zugriff auf Ihr Gerät verwendet werden.

- **Bereitstellen eigener Zertifikate**: Optional können Sie Ihre eigenen Zertifikate bereitstellen. Es gibt Richtlinien, die Sie befolgen müssen, wenn Sie Ihre eigenen Zertifikate verwenden möchten.

    - Lernen Sie zunächst in diesem Artikel die Zertifikattypen kennen, die mit Ihrem Azure Stack Edge-Gerät verwendet werden können.
    - Dann überprüfen Sie die [Zertifikatanforderungen für die einzelnen Zertifikattypen](azure-stack-edge-gpu-certificate-requirements.md).  
    - Anschließend können Sie Ihre [Zertifikate über Azure PowerShell](azure-stack-edge-gpu-create-certificates-powershell.md) oder [über das Readiness Checker-Tool](azure-stack-edge-gpu-create-certificates-tool.md) erstellen.
    - Zum Schluss [konvertieren Sie die Zertifikate in ein geeignetes Format](azure-stack-edge-gpu-prepare-certificates-device-upload.md), damit sie auf Ihr Gerät hochgeladen werden können.
    - [Laden Sie Ihre Zertifikate auf das Gerät hoch](azure-stack-edge-gpu-manage-certificates.md#upload-certificates-on-your-device).
    - [Importieren Sie die Zertifikate auf den Client](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device), der auf das Gerät zugreift.

## <a name="types-of-certificates"></a>Zertifikattypen

Die verschiedenen Zertifikattypen, die Sie für Ihre Gerät bereitstellen können, lauten wie folgt: 
- Signaturzertifikate
    - Stamm-CA
    - Fortgeschrittene Anfänger

- Knotenzertifikate

- Endpunktzertifikate   
   
    - Azure Resource Manager-Zertifikate
    - Blobspeicherzertifikate

- Lokale Benutzeroberflächenzertifikate
- IoT-Gerätezertifikate
    
- Kubernetes-Zertifikate

    - Zertifikat der Edgecontainerregistrierung
    - Zertifikat des Kubernetes-Dashboards
    
- WLAN-Zertifikate
- VPN-Zertifikate  

- Verschlüsselungszertifikate
    - Supportsitzungszertifikate

Jedes dieser Zertifikate wird in den folgenden Abschnitten ausführlich erläutert.

## <a name="signing-chain-certificates"></a>Signaturkettenzertifikate

Dies sind die Zertifikate für die Zertifizierungsstelle, die die Zertifikate signiert, oder die signierende Zertifizierungsstelle. 

### <a name="types"></a>Typen

Diese Zertifikate können Stammzertifikate oder die Zwischenzertifikate sein. Die Stammzertifikate sind immer selbstsigniert. Die Zwischenzertifikate sind nicht selbstsigniert und werden von der signierenden Zertifizierungsstelle unterzeichnet.

#### <a name="caveats"></a>Vorbehalte

- Die Stammzertifikate sollten Signaturkettenzertifikate sein.
- Die Stammzertifikate können in folgendem Format auf Ihr Gerät hochgeladen werden: 
    - **DER** – Diese sind als `.cer`-Dateierweiterung verfügbar.
    - **Base64-codiert** – Diese sind als `.cer`-Dateierweiterung verfügbar.
    - **P7b** – Dieses Format wird nur für Signaturkettenzertifikate verwendet, die die Stamm- und Zwischenzertifikate enthalten.
- Signaturkettenzertifikate werden immer hochgeladen, bevor Sie andere Zertifikate hochladen.


## <a name="node-certificates"></a>Knotenzertifikate

<!--Your  device could be a 1-node device or a 4-node device.--> Alle Knoten in Ihrem Gerät kommunizieren ständig miteinander und müssen daher über eine Vertrauensstellung verfügen. Knotenzertifikate bieten eine Möglichkeit, diese Vertrauensstellung herzustellen. Knotenzertifikate kommen auch ins Spiel, wenn Sie eine Remoteverbindung mit dem Geräteknoten über eine PowerShell-Remotesitzung über HTTPS herstellen.

#### <a name="caveats"></a>Vorbehalte

- Das Knotenzertifikat sollte im Format `.pfx` mit einem privaten Schlüssel, der exportiert werden kann, bereitgestellt werden. 
- Sie können ein Platzhalterknotenzertifikat oder vier einzelne Knotenzertifikate erstellen und hochladen. 
- Ein Knotenzertifikat muss geändert werden, wenn sich die DNS-Domäne, aber nicht der Gerätename ändert. Wenn Sie Ihr eigenes Knotenzertifikat bereitstellen, können Sie die Geräteseriennummer nicht ändern, sondern nur den Domänennamen.
- Verwenden Sie die folgende Tabelle als Leitfaden bei der Erstellung eines Knotenzertifikats.
   
    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Node|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
   

## <a name="endpoint-certificates"></a>Endpunktzertifikate

Für alle Endpunkte, die vom Gerät verfügbar gemacht werden, ist für eine vertrauenswürdige Kommunikation ein Zertifikat erforderlich. Die Endpunktzertifikate umfassen diejenigen, die für den Zugriff auf Azure Resource Manager und den Blobspeicher über die REST-APIs erforderlich sind. 

Wenn Sie ein eigenes signiertes Zertifikat verwenden, benötigen Sie auch die entsprechende Signaturkette des Zertifikats. Für die Signaturketten-, Azure Resource Manager- und Blobzertifikate auf dem Gerät benötigen Sie auch die entsprechenden Zertifikate auf dem Clientcomputer für die Authentifizierung und die Kommunikation mit dem Gerät.

#### <a name="caveats"></a>Vorbehalte

- Die Endpunktzertifikate müssen im Format `.pfx` mit einem privaten Schlüssel vorliegen. Die Signaturkette sollte im DER-Format (`.cer`-Dateierweiterung) vorliegen. 
- Wenn Sie Ihre eigenen Endpunktzertifikate bereitstellen, können diese als Einzelzertifikate oder als Zertifikate für mehrere Domänen vorliegen. 
- Wenn Sie eine Signaturkette bereitstellen, muss das Signaturkettenzertifikat hochgeladen werden, bevor Sie ein Endpunktzertifikat hochladen.
- Diese Zertifikate müssen geändert werden, wenn sich der Gerätename oder die DNS-Domänennamen ändern.
- Es kann ein Platzhalterzeichen-Endpunktzertifikat verwendet werden.
- Die Eigenschaften der Endpunktzertifikate ähneln denen eines typischen SSL-Zertifikats. 
- Verwenden Sie die folgende Tabelle beim Erstellen eines Endpunktzertifikats:

    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Einzelnes Zertifikat für mehrere alternative Antragstellernamen für beide Endpunkte|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |


## <a name="local-ui-certificates"></a>Lokale Benutzeroberflächenzertifikate

Sie können über einen Browser auf die lokale Webbenutzeroberfläche Ihres Geräts zugreifen. Um sicherzustellen, dass diese Kommunikation sicher ist, können Sie Ihr eigenes Zertifikat hochladen. 

#### <a name="caveats"></a>Vorbehalte

- Das lokale Benutzeroberflächenzertifikat wird ebenfalls in einem `.pfx`-Format mit einem privaten Schlüssel hochgeladen, der exportiert werden kann.
- Nachdem Sie das lokale Benutzeroberflächenzertifikat hochgeladen haben, müssen Sie den Browser neu starten und den Cache leeren. Weitere Informationen finden Sie in den speziellen Anweisungen für Ihren Browser.

    |type |Antragstellername  |Alternativer Antragstellername  |Beispiel für Antragstellername |
    |---------|---------|---------|---------|
    |Lokale Benutzeroberfläche| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
   

## <a name="iot-edge-device-certificates"></a>IoT Edge-Gerätezertifikate

Ihr Gerät ist ebenfalls ein IoT-Gerät, bei dem der Computevorgang durch ein angeschlossenes IoT Edge-Gerät aktiviert wird. Für jede sichere Kommunikation zwischen diesem IoT Edge-Gerät und den Downstreamgeräten, die sich mit ihm verbinden können, können Sie auch IoT Edge-Zertifikate hochladen. 

Das Gerät verfügt über selbstsignierte Zertifikate, die verwendet werden können, wenn Sie nur das Computeszenario mit dem Gerät verwenden möchten. Wenn das Gerät jedoch an Downstreamgeräte angeschlossen ist, dann müssen Sie Ihre eigenen Zertifikate bereitstellen.

Es gibt drei IoT Edge-Zertifikate, die Sie installieren müssen, um diese Vertrauensstellung zu aktivieren:

- **Stammzertifizierungsstelle oder die Zertifizierungsstelle des Besitzers.**
- **Zertifizierungsstelle für Gerätezertifikate** 
- **Zertifikat des Geräteschlüssels**


#### <a name="caveats"></a>Vorbehalte

- Die IoT Edge-Zertifikate werden im Format `.pem` hochgeladen. 

Weitere Informationen zu IoT Edge-Zertifikaten finden Sie unter [Details zu Azure IoT Edge-Zertifikaten](../iot-edge/iot-edge-certs.md#iot-edge-certificates) und [Erstellen von IoT Edge-Produktionszertifikaten](../iot-edge/how-to-manage-device-certificates.md?preserve-view=true&view=iotedge-2020-11#create-production-certificates).

## <a name="kubernetes-certificates"></a>Kubernetes-Zertifikate

Die folgenden Kubernetes-Zertifikate können mit Ihrem Azure Stack Edge-Gerät verwendet werden.

- **Zertifikat der Edgecontainerregistrierung:** Wenn Ihr Gerät über eine Edgecontainerregistrierung verfügt, benötigen Sie für diese ein Zertifikat für die sichere Kommunikation mit dem Client, der auf die Registrierung auf dem Gerät zugreift.
- **Zertifikat des Dashboardendpunkts:** Sie benötigen ein Zertifikat für den Dashboardendpunkt, um auf das Kubernetes-Dashboard auf Ihrem Gerät zuzugreifen.


#### <a name="caveats"></a>Vorbehalte

- Für das Zertifikat der Edgecontainerregistrierung muss Folgendes gelten: 
    - Es muss ein Zertifikat im PEM-Format sein.
    - Es muss entweder den alternativen Antragstellernamen (Subject Alternative Name, SAN) oder den CName (CN) vom Typ `*.<endpoint suffix>` oder `ecr.<endpoint suffix>` enthalten. Beispiel: `*.dbe-1d6phq2.microsoftdatabox.com OR ecr.dbe-1d6phq2.microsoftdatabox.com`


- Für das Dashboardzertifikat muss Folgendes gelten:
    - Es muss ein Zertifikat im PEM-Format sein.
    - Es muss entweder den alternativen Antragstellernamen (Subject Alternative Name, SAN) oder den CName (CN) vom Typ `*.<endpoint-suffix>` oder `kubernetes-dashboard.<endpoint-suffix>` enthalten. Beispiel: `*.dbe-1d6phq2.microsoftdatabox.com` oder `kubernetes-dashboard.dbe-1d6phq2.microsoftdatabox.com`. 


## <a name="vpn-certificates"></a>VPN-Zertifikate

Wenn VPN (Point-to-Site) auf Ihrem Gerät konfiguriert ist, können Sie Ihr eigenes VPN-Zertifikat bereitstellen, um zu gewährleisten, dass die Kommunikation vertrauenswürdig ist. Das Stammzertifikat wird in Azure VPN Gateway installiert, und die Clientzertifikate werden auf jedem Clientcomputer installiert, der eine Point-to-Site-Verbindung mit einem virtuellen Netzwerk herstellt.

#### <a name="caveats"></a>Vorbehalte

- Das VPN-Zertifikat muss im *PFX*-Format mit einem privaten Schlüssel hochgeladen werden.
- Das VPN-Zertifikat ist nicht vom Gerätenamen, der Geräteseriennummer oder der Gerätekonfiguration abhängig. Es erfordert nur den externen FQDN.
- Stellen Sie sicher, dass die Client-OID festgelegt ist.

Weitere Informationen finden Sie unter [Generieren und Exportieren von Zertifikaten für Point-to-Site-Verbindungen mithilfe von PowerShell](../vpn-gateway/vpn-gateway-certificates-point-to-site.md#generate-and-export-certificates-for-point-to-site-using-powershell).

## <a name="wi-fi-certificates"></a>WLAN-Zertifikate

Wenn Ihr Gerät für den Betrieb in einem WPA2-Enterprise-Drahtlosnetzwerk konfiguriert ist, benötigen Sie auch ein WLAN-Zertifikat für jegliche Kommunikation, die über das Drahtlosnetzwerk erfolgt. 

#### <a name="caveats"></a>Vorbehalte

- Das WLAN-Zertifikat muss im *PFX*-Format mit einem privaten Schlüssel hochgeladen werden.
- Stellen Sie sicher, dass die Client-OID festgelegt ist.

## <a name="support-session-certificates"></a>Supportsitzungszertifikate

Wenn bei Ihrem Gerät Probleme auftreten, kann zur Behebung dieser Probleme eine Remotesitzung des PowerShell-Supports auf dem Gerät geöffnet werden. Sie können ein Zertifikat hochladen, um eine sichere, verschlüsselte Kommunikation über diese Supportsitzung zu ermöglichen.

#### <a name="caveats"></a>Vorbehalte

- Stellen Sie sicher, dass das entsprechende `.pfx`-Zertifikat mit privatem Schlüssel mithilfe des Entschlüsselungstools auf dem Clientcomputer installiert ist.
- Stellen Sie sicher, dass das Feld **Schlüsselverwendung** für das Zertifikat nicht **Zertifikatssignierung** ist. Klicken Sie zur Überprüfung mit der rechten Maustaste auf das Zertifikat, wählen Sie **Öffnen** aus, und suchen Sie auf der Registerkarte **Details** nach **Schlüsselverwendung**. 

- Das Supportsitzungszertifikat muss im DER-Format mit der `.cer`-Erweiterung bereitgestellt werden.


## <a name="next-steps"></a>Nächste Schritte

[Lesen Sie die Zertifikatanforderungen](azure-stack-edge-gpu-certificate-requirements.md).