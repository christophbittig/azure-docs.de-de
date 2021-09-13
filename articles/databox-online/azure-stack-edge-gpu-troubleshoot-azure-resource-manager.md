---
title: Problembehandlung bei der Konfiguration von Azure Resource Manager auf Azure Stack Edge Pro GPU | Microsoft-Dokumentation
description: Beschreibt, wie Probleme mit der Azure Resource Manager-Konfiguration auf Azure Stack Edge-Geräten behoben werden können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/10/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: d8226d780c3caabb94f0780f3a4296765207f1b1
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987809"
---
# <a name="troubleshoot-azure-resource-manager-issues-on-an-azure-stack-edge-device"></a>Behebung von Problemen mit Azure Resource Manager auf einem Azure Stack Edge-Gerät 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In diesem Artikel wird beschrieben, wie Sie Probleme mit Azure Resource Manager beheben, die die Verwaltung von Ressourcen auf Ihrem Azure Stack Edge-Gerät beeinträchtigen können. Azure Resource Manager bietet eine Verwaltungsebene, die das Erstellen, Aktualisieren und Löschen von Ressourcen in Ihrem Azure-Konto ermöglicht.
 
## <a name="azure-resource-manager-configuration-errors"></a>Konfigurationsfehler bei Azure Resource Manager

Die folgenden Fehler können auf ein Problem mit Ihrer Azure Resource Manager-Konfiguration hinweisen. 

| **Problem/Fehler** |  **Lösung** | 
|------------|-----------------|
|Allgemeine Probleme|<li>[Vergewissern Sie sich, dass das Gerät ordnungsgemäß konfiguriert ist.](#verify-the-device-is-configured-properly)<li> [Vergewissern Sie sich, dass der Client ordnungsgemäß konfiguriert ist.](#verify-the-client-is-configured-properly)|
|Add-AzureRmEnvironment: Fehler beim Senden der Anforderung.<br>Zeile:1 Zeichen:1<br>+ Add-AzureRmEnvironment -Name Az3 -ARMEndpoint "https://management.dbe ...|Ihr Gerät ist nicht erreichbar oder nicht ordnungsgemäß konfiguriert. Vergewissern Sie sich, dass Gerät und Client ordnungsgemäß konfiguriert sind. Eine Anleitung finden Sie in der Zeile **Allgemeine Probleme** dieser Tabelle.|
|Dienst hat einen Fehler zurückgegeben. Weitere Details finden Sie in der InnerException. Die zugrunde liegende Verbindung wurde geschlossen: Es konnte keine Vertrauensstellung für den sicheren SSL/TLS-Kanal eingerichtet werden. |  Bei der Erstellung und Installation des Zertifikats auf Ihrem Gerät ist ein Fehler aufgetreten. Weitere Informationen finden Sie unter [Erstellen und Installieren von Zertifikaten](azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates). |
|Operation returned an invalid status code 'BadRequest'. (Der Vorgang hat den ungültigen Statuscode „ServiceUnavailable“ zurückgegeben.) <br> Der Antwortstatuscode gibt keinen Erfolg an: 503 (Dienst nicht verfügbar). | Dieser Fehler kann auf eine der folgenden Bedingungen zurückzuführen sein:<li>ArmStsPool befindet sich im beendeten Zustand.</li><li>Entweder Azure Resource Manager oder die Website für den Sicherheitstokendienst ist nicht verfügbar.</li><li>Die Azure Resource Manager-Clusterressource ist nicht verfügbar.</li><br>Durch einen Neustart des Geräts kann das Problem behoben werden. Zum weiteren Debuggen [stellen Sie ein Supportpaket zusammen](azure-stack-edge-gpu-troubleshoot.md#collect-support-package).|
|AADSTS50126: Ungültiger Benutzername oder ungültiges Kennwort.<br>Ablaufverfolgungs-ID: 29317da9-52fc-4ba0-9778-446ae5625e5a<br>Korrelations-ID: 1b9752c4-8cbf-4304-a714-8a16527410f4<br>Timestamp: 2019-11-15 09:21:57Z: The remote server returned an error: (400) Bad Request (400 (Ungültige Anforderung)).<br>Zeile:1 Zeichen:1 |Dieser Fehler kann auf eine der folgenden Bedingungen zurückzuführen sein:<li>Stellen Sie bei einem ungültigen Benutzernamen und Kennwort sicher, dass Sie das [Azure Storage Manager-Kennwort](./azure-stack-edge-gpu-set-azure-resource-manager-password.md) im Azure-Portal zurückgesetzt haben, und verwenden Sie dann das richtige Kennwort.<li>Stellen Sie bei einer ungültigen Mandanten-ID sicher, dass die Mandanten-ID auf `c0257de7-538f-415c-993a-1b87a031879d` festgelegt ist.</li>|
|connect-AzureRmAccount: AADSTS90056: Die Ressource ist deaktiviert oder nicht vorhanden. Überprüfen Sie den Code Ihrer App, um sicherzustellen, dass Sie die genaue Ressourcen-URL für die Ressource angegeben haben, auf die Sie zugreifen möchten.<br>Ablaufverfolgungs-ID: e19bdbc9-5dc8-4a74-85c3-ac6abdfda115<br>Korrelations-ID: 75c8ef5a-830e-48b5-b039-595a96488ff9 Zeitstempel: 2019-11-18 07:00:51Z: The remote server returned an error: (400) Fehlerhaft |Die im Befehl `Add-AzureRmEnvironment` verwendeten Azure Resource Manager-Endpunkte sind falsch.<br>Die Azure Resource Manager-Endpunkte können Sie auf der lokalen Webbenutzeroberfläche Ihres Geräts auf der Seite **Gerät** unter **Geräteendpunkte** ermitteln.<br>PowerShell-Anweisungen finden Sie unter [Festlegen der Azure Resource Manager-Umgebung](azure-stack-edge-gpu-connect-resource-manager.md#step-7-set-azure-resource-manager-environment). |
|Endpunkte können nicht aus der Cloud abgerufen werden.<br>Stellen Sie sicher, dass Sie über eine Netzwerkverbindung verfügen. Fehlerdetail: HTTPSConnectionPool(host='management.dbg-of4k6suvm.microsoftdatabox.com', port=30005): Maximale Anzahl von Wiederholungen überschritten mit URL: /metadata/endpoints?api-version=2015-01-01 (Caused by SSLError(SSLError("bad handshake: Error([('SSL routines', 'tls_process_server_certificate', 'certificate verify failed')],)",),)) |Dieser Fehler tritt meist in einer Mac- oder Linux-Umgebung auf. Der Fehler tritt auf, weil dem Python-Zertifikatspeicher kein Zertifikat im PEM-Format hinzugefügt wurde. |


## <a name="troubleshoot-general-issues-with-azure-resource-manager"></a>Behandeln von allgemeinen Problemen bei Azure Resource Manager

Stellen Sie bei allgemeinen Problemen mit Azure Resource Manager sicher, dass Ihr Gerät und der Client ordnungsgemäß konfiguriert sind. Informationen zu End-to-End-Prozeduren finden Sie unter [Herstellen einer Verbindung mit Azure Resource Manager auf Ihrem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-gpu-connect-resource-manager.md).


### <a name="verify-the-device-is-configured-properly"></a>Überprüfen, ob das Gerät ordnungsgemäß konfiguriert ist

1. Überprüfen Sie auf der lokalen Benutzeroberfläche, ob das Gerätenetzwerk ordnungsgemäß konfiguriert ist.

2. [Vergewissern Sie sich, dass die Zertifikate für alle Endpunkte aktualisiert werden.](./azure-stack-edge-gpu-connect-resource-manager.md#step-2-create-and-install-certificates)

3. Rufen Sie den Verwaltungs- und Anmeldeendpunkt für Azure Resource Manager auf der Seite **Gerät** der lokalen Benutzeroberfläche ab.

4. Vergewissern Sie sich, dass das Gerät in Azure aktiviert und registriert ist.


### <a name="verify-the-client-is-configured-properly"></a>Überprüfen, ob der Client ordnungsgemäß konfiguriert ist

1. [Überprüfen Sie, ob die richtige PowerShell-Version installiert ist.](./azure-stack-edge-gpu-connect-resource-manager.md#step-3-install-powershell-on-the-client)

2. [Überprüfen Sie, ob die richtigen PowerShell-Module installiert sind.](./azure-stack-edge-gpu-connect-resource-manager.md#step-4-set-up-azure-powershell-on-the-client)

3. Überprüfen Sie, ob die Azure Resource Manager- und Anmeldeendpunkte erreichbar sind. Sie können versuchen, die Endpunkte zu pingen. Beispiel:

   `ping management.28bmdw2-bb9.microsoftdatabox.com`
   `ping login.28bmdw2-bb9.microsoftdatabox.com`
   
   Wenn sie nicht erreichbar sind, [fügen Sie DNS-/Hostdateieinträge hinzu](./azure-stack-edge-gpu-connect-resource-manager.md#step-5-modify-host-file-for-endpoint-name-resolution).
   
4. [Überprüfen Sie, ob Clientzertifikate installiert sind.](./azure-stack-edge-gpu-connect-resource-manager.md#import-certificates-on-the-client-running-azure-powershell)

5. Wenn Sie PowerShell verwenden, aktivieren Sie die Debugvoreinstellung, um detaillierte Meldungen anzuzeigen, indem Sie diesen PowerShell-Befehl ausführen. 

    `$debugpreference = "continue"`


## <a name="next-steps"></a>Nächste Schritte

- [Behandeln von Problemen bei der Geräteaktivierung](azure-stack-edge-gpu-troubleshoot-activation.md).
- [Beheben von Geräteproblemen](azure-stack-edge-gpu-troubleshoot.md)