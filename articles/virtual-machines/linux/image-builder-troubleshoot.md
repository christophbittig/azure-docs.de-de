---
title: Problembehandlung für den Azure Image Builder-Dienst
description: Beheben von häufigen Problemen und Fehlern bei der Verwendung des Azure VM Image Builder-Diensts
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4117926fe8de79a295fa3c0a52c1ca54816496ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427986"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Problembehandlung für den Azure Image Builder-Dienst

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Dieser Artikel hilft Ihnen, häufige Probleme zu beheben, die beim Verwenden des Azure Image Builder-Diensts auftreten können.

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie beim Erstellen eines Builds sicher, dass Ihr Build die folgenden Voraussetzungen erfüllt:
    
- Der Image Builder-Dienst kommuniziert mit dem virtuellen Buildcomputer über WinRM oder SSH. Deaktivieren Sie diese Einstellungen NICHT als Teil des Builds.
- Image Builder erstellt Ressourcen als Teil des Builds. Stellen Sie sicher, dass Azure Policy AIB nicht daran hindert, erforderliche Ressourcen zu erstellen oder zu verwenden.
  - Erstellen der IT_-Ressourcengruppe
  - Erstellen eines Speicherkontos ohne Firewall
- Stellen Sie sicher, dass Azure Policy keine unbeabsichtigten Features wie Azure-Erweiterungen auf der Build-VM installiert.
-   Stellen Sie sicher, dass Image Builder über die richtigen Berechtigungen zum Lesen/Schreiben von Images und zum Herstellen einer Verbindung mit Azure Storage verfügt. Bitte lesen Sie die Dokumentation zu den Berechtigungen für [CLI](./image-builder-permissions-cli.md) oder [PowerShell](./image-builder-permissions-powershell.md).
- Image Builder kann den Build nicht erstellen, wenn bei den Skript-/Inlinebefehlen Fehler auftreten (Exitcodes ungleich 0). Testen und überprüfen Sie unbedingt, ob benutzerdefinierte Skripts fehlerfrei ausgeführt werden (Exitcode 0) oder Benutzereingaben erfordern. Weitere Informationen finden Sie in der folgenden [Dokumentation](../windows/image-builder-virtual-desktop.md#tips-for-building-windows-images).

AIB-Fehler können in zwei Bereichen auftreten:
- Übermittlung von Imagevorlagen
- Imageerstellung

## <a name="troubleshoot-image-template-submission-errors"></a>Problembehandlung bei der Übermittlung von Imagevorlagen

Fehler bei der Übermittlung von Imagevorlagen werden nur bei der Übermittlung zurückgegeben. Das heißt, es gibt kein Fehlerprotokoll für Fehler bei der Übermittlung von Imagevorlagen. Wenn bei der Übermittlung ein Fehler aufgetreten ist, können Sie den Fehler zurückgeben, indem Sie den Status der Vorlage überprüfen, insbesondere `ProvisioningState` und `ProvisioningErrorMessage`/`provisioningError`.

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> Für PowerShell müssen Sie die [PowerShell-Module von Azure Image Builder](../windows/image-builder-powershell.md#prerequisites) installieren.

In den folgenden Abschnitten finden Sie Anleitungen zur Problembehandlung für häufige Fehler bei der Übermittlung von Imagevorlagen.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Update/Upgrade von Imagevorlagen wird derzeit nicht unterstützt

#### <a name="error"></a>Fehler

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Ursache

Die Vorlage ist bereits vorhanden.

#### <a name="solution"></a>Lösung

Wenn Sie eine Imagekonfigurationsvorlage übermitteln und die Übermittlung fehlschlägt, gibt es immer noch ein fehlerhaftes Vorlagenartefakt. Löschen Sie die fehlerhafte Vorlage.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>Der Ressourcenvorgang wurde mit Bereitstellungsendzustand „Failed“ abgeschlossen.

#### <a name="error"></a>Fehler

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Ursache

In den meisten Fällen tritt der Fehler bei der Ressourcenbereitstellung aufgrund fehlender Berechtigungen auf.

#### <a name="solution"></a>Lösung

Abhängig von Ihrem Szenario benötigt der Azure Image Builder möglicherweise Berechtigungen für Folgendes:
- Quellimage oder Azure Compute Gallery (früher als Shared Image Gallery bezeichnet)
- Distributionsimage oder Azure Compute Gallery-Ressource
- Das Speicherkonto, der Container oder das Blob, auf das die Dateianpassung zugreift. 

Weitere Informationen zum Konfigurieren von Berechtigungen finden Sie unter [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI](image-builder-permissions-cli.md) oder [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe von PowerShell](image-builder-permissions-powershell.md).

### <a name="error-getting-managed-image"></a>Fehler beim Abrufen des verwalteten Images

#### <a name="error"></a>Fehler

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Ursache

Fehlende Berechtigungen.

#### <a name="solution"></a>Lösung

Abhängig von Ihrem Szenario benötigt der Azure Image Builder möglicherweise Berechtigungen für Folgendes:
* Quellimage oder Azure Compute Gallery-Ressourcengruppe
* Distributionsimage oder Azure Compute Gallery-Ressource
* Das Speicherkonto, der Container oder das Blob, auf das die Dateianpassung zugreift. 

Weitere Informationen zum Konfigurieren von Berechtigungen finden Sie unter [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI](image-builder-permissions-cli.md) oder [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe von PowerShell](image-builder-permissions-powershell.md).

### <a name="build--step-failed-for-image-version"></a>Fehler beim Buildschritt für die Imageversion

#### <a name="error"></a>Fehler
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Ursache

Azure Image Builder kann das Quellimage nicht finden.

#### <a name="solution"></a>Lösung

Stellen Sie sicher, dass das Quellimage korrekt ist und am Standort des Azure Image Builder-Diensts vorhanden ist.

### <a name="downloading-external-file-to-local-file"></a>Externe Datei wird in lokale Datei heruntergeladen

#### <a name="error"></a>Fehler

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Ursache

Der Dateiname oder Speicherort ist falsch, oder der Speicherort ist nicht erreichbar.

#### <a name="solution"></a>Lösung

Stellen Sie sicher, dass die Datei erreichbar ist. Vergewissern Sie sich, dass der Name und der Speicherort korrekt angegeben sind.

## <a name="troubleshoot-build-failures"></a>Beheben von Buildfehlern

Treten Fehler bei der Imageerstellung auf, können Sie den Fehler aus `lastrunstatus` abrufen und dann die Details in der Datei „customization.log“ überprüfen.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Anpassungsprotokoll

Sobald die Imageerstellung ausgeführt wird, werden Protokolle erstellt und in einem Speicherkonto gespeichert. Wenn Sie ein Imagevorlagenartefakt erstellen, wird das Speicherkonto von Azure Image Builder in der temporären Ressourcengruppe erstellt.

Für den Namen des Speicherkontos wird das folgende Muster verwendet: **IT_\<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Beispiel: *IT_aibmdi_helloImageTemplateLinux01*.

Sie können das Anpassungsprotokoll „customization.log“ im Speicherkonto in der Ressourcengruppe anzeigen, indem Sie **Speicherkonto** > **Blobs** > `packerlogs` auswählen.  Wählen Sie dann **Verzeichnis > customization.log** aus.


### <a name="understanding-the-customization-log"></a>Grundlegendes zum Anpassungsprotokoll

Das Protokoll ist sehr ausführlich. Es deckt die Imageerstellung einschließlich aller Probleme bei der Imageverteilung ab, wie z. B. die Azure Compute Gallery-Replikation. Diese Fehler werden in der Fehlermeldung des Status der Imagevorlage angezeigt.

Die Datei „customization.log“ umfasst die folgenden Stufen:

1. Stufe zur Bereitstellung der Build-VM und der Abhängigkeiten mithilfe von ARM-Vorlagen für die „IT_ staging“-Ressourcengruppe. Diese Stufe umfasst mehrere POSTs an den Azure Image Builder-Ressourcenanbieter:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Stufe zum Status der Bereitstellungen. Diese Stufe umfasst den Status der einzelnen Ressourcenbereitstellungen.
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Stufe zur Verbindung mit der Build-VM.

    Unter Windows stellt der Azure Image Builder-Dienst eine Verbindung mit WinRM her:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Unter Linux stellt der Azure Image Builder-Dienst eine Verbindung mit SSH her:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Stufe zur Ausführung von Anpassungen. Wenn Anpassungen ausgeführt werden, können Sie diese identifizieren, indem Sie die Datei „customization.log“ überprüfen. Suchen Sie nach *(telemetry)* .
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Stufe zur Aufhebung der Bereitstellung. Azure Image Builder fügt eine ausgeblendete Anpassung hinzu. Dieser Schritt zur Aufhebung der Bereitstellung ist für die Vorbereitung der VM zum Aufheben der Bereitstellung erforderlich. Dabei wird Windows Sysprep (unter Verwendung von c:\DeprovisioningScript.ps1) oder unter Linux die waagent-Aufhebung (unter Verwendung von /tmp/DeprovisioningScript.sh) ausgeführt. 

    Beispiel:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Stufe zur Bereinigung. Nachdem der Build abgeschlossen wurde, werden Azure Image Builder-Ressourcen gelöscht.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Tipps zur Problembehandlung bei der Skript-/Inlineanpassung
- Testen des Codes vor der Bereitstellung in Image Builder
- Sicherstellen, dass Azure Policy und Firewalls die Konnektivität zu Remoteressourcen ermöglichen
- Ausgabe von Kommentaren an die Konsole, z. B. mit `Write-Host` oder `echo` für die Suche in der Datei „customization.log“

## <a name="troubleshoot-common-build-errors"></a>Problembehandlung für häufige Buildfehler

### <a name="packer-build-command-failure"></a>Fehler beim Packer-Build-Befehl

#### <a name="error"></a>Fehler

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Ursache

Anpassungsfehler.

#### <a name="solution"></a>Lösung

Überprüfen Sie das Protokoll, um Fehler bei Anpassungen zu finden. Suchen Sie nach *(telemetry)* . 

Beispiel:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Timeout überschritten

#### <a name="error"></a>Fehler

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Ursache

Der Build hat das Buildtimeout überschritten. Dieser Fehler wird in „lastrunstatus“ angezeigt.

#### <a name="solution"></a>Lösung

1. Überprüfen Sie die Datei „customization.log“. Identifizieren Sie die Anpassung, die zuletzt ausgeführt werden sollte. Suchen Sie nach `(telemetry)`, beginnend am Ende des Protokolls. 

2. Überprüfen Sie die Skriptanpassungen. Die Anpassungen unterdrücken möglicherweise nicht die Benutzerinteraktion für Befehle, wie z. B. `quiet`-Optionen. `apt-get install -y` führt beispielsweise dazu, dass die Skriptausführung auf eine Benutzerinteraktion wartet.

3. Wenn Sie die `File`-Anpassung zum Herunterladen von Artefakten mit mehr als 20 MB verwenden, finden Sie weitere Informationen im Abschnitt mit den Problemumgehungen.

4. Überprüfen Sie Fehler und Abhängigkeiten in Skripts, die möglicherweise dazu führen, dass das Skript wartet.

5. Wenn Sie davon ausgehen, dass die Anpassungen mehr Zeit benötigen, erhöhen Sie [buildTimeoutInMinutes](image-builder-json.md). Der Standardwert ist vier Stunden.

6. Wenn Sie ressourcenintensive Aktionen durchführen, wie z. B. das Herunterladen mehrerer Gigabyte an Dateien, sollten Sie die zugrunde liegende Build-VM-Größe berücksichtigen. Der Dienst verwendet eine Standard_D1_v2-VM. Die VM verfügt über 1 vCPU und 3,5 GB Arbeitsspeicher. Wenn Sie 50 GB herunterladen, werden dadurch wahrscheinlich die VM-Ressourcen erschöpft und Kommunikationsfehler zwischen dem Azure Image Builder-Dienst und der Build-VM verursacht. Wiederholen Sie den Buildvorgang mit einer VM mit mehr Speicher, indem Sie den Parameter [VM_Size](image-builder-json.md#vmprofile) einstellen.

### <a name="long-file-download-time"></a>Lange Downloadzeit für Datei

#### <a name="error"></a>Fehler
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Ursache 

Für die Dateianpassung wird eine große Datei heruntergeladen.

#### <a name="solution"></a>Lösung

Die Dateianpassung ist nur für kleine Dateidownloads geeignet (< 20 MB). Verwenden Sie für größere Dateidownloads ein Skript oder einen Inlinebefehl. Unter Linux können Sie zum Beispiel `wget` oder `curl` verwenden. Unter Windows können Sie `Invoke-WebRequest` verwenden.

### <a name="error-waiting-on-azure-compute-gallery"></a>Fehler beim Warten auf Azure Compute Gallery

#### <a name="error"></a>Fehler

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to Azure Compute Gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to Azure Compute Gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on Azure Compute Gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Ursache

Timeout von Image Builder beim Warten auf das Hinzufügen und Replizieren des Images in die Azure Compute Gallery. Wenn das Image in die SIG eingefügt wird, kann davon ausgegangen werden, dass die Imageerstellung erfolgreich war. Der Gesamtprozess ist jedoch fehlgeschlagen, da Image Builder auf Azure Compute Gallery gewartet hat, um die Replikation abzuschließen. Obwohl der Build fehlgeschlagen ist, wird die Replikation fortgesetzt. Sie können die Eigenschaften der Imageversion abrufen, indem Sie die Distribution *runOutput* überprüfen.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2020-02-14
```

#### <a name="solution"></a>Lösung

Erhöhen Sie den Parameter **buildTimeoutInMinutes**.
 
### <a name="low-windows-resource-information-events"></a>Zu wenige Informationsereignisse zu Windows-Ressourcen

#### <a name="error"></a>Fehler

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Ursache

Ressourcenauslastung. Dieses Problem tritt häufig bei Windows Update auf, wenn es mit der Build-VM-Standardgröße D1_V2 ausgeführt wird.

#### <a name="solution"></a>Lösung

Erhöhen Sie die Build-VM-Größe.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Builds sind abgeschlossen, aber es wurden keine Artefakte erstellt.

#### <a name="error"></a>Fehler

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Ursache

Timeout beim Warten auf das Erstellen von erforderlichen Azure-Ressourcen.

#### <a name="solution"></a>Lösung

Führen Sie den Build erneut aus, um es noch mal zu versuchen.

### <a name="resource-not-found"></a>Ressource nicht gefunden

#### <a name="error"></a>Fehler

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Ursache

Fehlende Berechtigungen.

#### <a name="solution"></a>Lösung

Überprüfen Sie erneut, ob Azure Image Builder über alle erforderlichen Berechtigungen verfügt. 

Weitere Informationen zum Konfigurieren von Berechtigungen finden Sie unter [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe der Azure CLI](image-builder-permissions-cli.md) oder [Konfigurieren von Berechtigungen für den Azure VM Image Builder-Dienst mithilfe von PowerShell](image-builder-permissions-powershell.md).

### <a name="sysprep-timing"></a>Sysprep-Timing

#### <a name="error"></a>Fehler

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Ursache

Aufgrund der VM-Größe D1_V2 liegt möglicherweise ein Problem mit dem Timing vor. Wenn Anpassungen eingeschränkt sind und in weniger als drei Sekunden ausgeführt werden, werden die Sysprep-Befehle von Azure Image Builder ausgeführt, um die Bereitstellung aufzuheben. Wenn Azure Image Builder die Bereitstellung aufhebt, wird mit dem Sysprep-Befehl nach dem *WindowsAzureGuestAgent*-Element gesucht, welches möglicherweise nicht vollständig installiert ist und das Timingproblem verursacht. 

#### <a name="solution"></a>Lösung

Erhöhen Sie die VM-Größe. Alternativ können Sie eine Anpassung vornehmen, durch die PowerShell nach 60 Sekunden in den Standbymodus wechselt, um derartige Timingprobleme zu vermeiden.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Cancelling builder after context cancellation context canceled

#### <a name="error"></a>Fehler
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Ursache
Der Image Builder-Dienst verwendet Port 22 (Linux) oder Port 5986 (Windows), um eine Verbindung mit der Build-VM herzustellen. Dies geschieht, wenn die Verbindung des Diensts mit der Build-VM während einer Imageerstellung getrennt wird. Die Gründe für die Trennung der Verbindung können variieren, aber durch das Aktivieren oder Konfigurieren von Firewalls im Skript können die oben genannten Ports blockiert werden.

#### <a name="solution"></a>Lösung
Überprüfen Sie Ihre Skripts auf Firewalländerungen/-aktivierung oder Änderungen an SSH oder WinRM, und stellen Sie sicher, dass alle Änderungen eine konstante Konnektivität zwischen dem Dienst und der Build-VM an den oben genannten Ports zulassen. Weitere Informationen zu Image Builder-Netzwerken finden Sie im Artikel zu den [Anforderungen](./image-builder-networking.md).

### <a name="jwt-errors-in-log-early-in-the-build"></a>JWT-Fehler im Protokoll früh im Build

#### <a name="error"></a>Fehler
Früh im Buildprozess tritt ein Fehler auf, und das Protokoll weist auf einen JWT-Fehler hin:

```text
PACKER OUT Error: Failed to prepare build: "azure-arm"
PACKER ERR 
PACKER OUT 
PACKER ERR * client_jwt will expire within 5 minutes, please use a JWT that is valid for at least 5 minutes
PACKER OUT 1 error(s) occurred:
```

#### <a name="cause"></a>Ursache
Der `buildTimeoutInMinutes`-Wert in der Vorlage ist auf 1 bis 5 Minuten festgelegt.

#### <a name="solution"></a>Lösung
Wie unter [Erstellen einer Azure Image Builder-Vorlage](./image-builder-json.md) beschrieben, muss das Timeout auf 0 festgelegt werden, um den Standardwert zu verwenden, oder mehr als 5 Minuten, um den Standardwert zu überschreiben.  Ändern Sie das Timeout in Ihrer Vorlage in 0, um den Standardwert oder mindestens 6 Minuten zu verwenden.

## <a name="devops-task"></a>DevOps-Task 

### <a name="troubleshooting-the-task"></a>Problembehandlung für den Task
Der Task schlägt nur fehl, wenn während der Anpassung ein Fehler auftritt. In diesem Fall meldet der Task einen Fehler und verlässt die Stagingressourcengruppe mit den Protokollen, damit Sie das Problem identifizieren können. 

Sie müssen den Vorlagennamen kennen, um das Protokoll zu finden. Wechseln Sie zu „Pipeline“ und dem fehlgeschlagenen Build, und führen Sie einen Drilldown für den AIB-DevOps-Task aus, um dann das Protokoll und einen Vorlagennamen zu sehen:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Wechseln Sie zum Portal, suchen Sie in der Ressourcengruppe nach dem Vorlagennamen, und suchen Sie dann nach der Ressourcengruppe mit „IT_ *“.
Wechseln Sie zu „Speicherkonto > Blobs > Container > Protokolle“.

### <a name="troubleshooting-successful-builds"></a>Problembehandlung bei erfolgreichen Builds
Es kann vorkommen, dass Sie erfolgreiche Builds untersuchen müssen und sich das Protokoll ansehen möchten. Wie bereits erwähnt, wird die Stagingressourcengruppe, die die Protokolle enthält, bei der Bereinigung gelöscht, wenn die Imageerstellung erfolgreich ist. Sie können jedoch einen Standbymodus nach dem Inlinebefehl einführen und dann die Protokolle abrufen, wenn der Build angehalten ist. Gehen Sie dazu folgendermaßen vor:
 
1. Aktualisieren Sie den Inlinebefehl, und fügen Sie Folgendes hinzu: Write-Host / Echo „Sleep“ – dies ermöglicht Ihnen die Suche im Protokoll.
2. Fügen Sie mit [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) oder dem Linux-Befehl `Sleep` einen Standbymodus für mindestens 10 Minuten hinzu.
3. Verwenden Sie die obige Methode, um den Protokollspeicherort zu identifizieren, und überprüfen Sie dann das heruntergeladene Protokoll, bis in den Standbymodus gewechselt wird.


### <a name="operation-was-canceled"></a>Der Vorgang wurde abgebrochen.

#### <a name="error"></a>Fehler

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Ursache

Wenn der Build nicht von einem Benutzer abgebrochen wurde, wurde er vom Azure DevOps-Benutzer-Agent abgebrochen. Höchstwahrscheinlich ist das Timeout nach einer Stunde aufgrund der Azure DevOps-Funktionen aufgetreten. Wenn Sie ein privates Projekt und einen Agent verwenden, erhalten Sie eine Buildzeit von 60 Minuten. Wenn das Timeout für den Build überschritten wird, wird der laufende Task von DevOps abgebrochen.

Weitere Informationen zu Azure DevOps-Funktionen und -Einschränkungen finden Sie im Abschnitt zu den [von Microsoft gehosteten Agents](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations).
 
#### <a name="solution"></a>Lösung

Sie können Ihre eigenen DevOps-Agents hosten oder die Buildzeit verkürzen. Wenn Sie z. B. an Azure Compute Gallery verteilen, replizieren Sie in eine Region. für den Fall, dass Sie asynchron replizieren möchten. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Langsame Windows-Anmeldung: „Bitte warten Sie auf den Windows Modules Installer“

#### <a name="error"></a>Fehler
Nachdem Sie ein Windows 10-Image mit Image Builder und einen virtuellen Computer aus dem Image erstellt haben, stellen Sie eine Remotedesktopverbindung her. Jetzt müssen bei der ersten Anmeldung mehrere Minuten warten, bis ein blauer Bildschirm mit der folgenden Meldung angezeigt wird:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Lösung
Vergewissern Sie sich zunächst bei der Imageerstellung, dass keine Neustarts anstehen, indem Sie eine Windows-Neustartanpassung als letzte Anpassung hinzufügen, und dass die gesamte Softwareinstallation abgeschlossen wurde. Fügen Sie schließlich die Option [/mode:vm](/windows-hardware/manufacture/desktop/sysprep-command-line-options) zum Standard-Sysprep hinzu, das von AIB verwendet wird. Siehe weiter unten: „Aus AIB-Images erstellte VMs werden nicht erfolgreich erstellt“ > „Überschreiben der Befehle“.  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>Aus AIB-Images erstellte VMs werden nicht erfolgreich erstellt

Azure Image Builder führt standardmäßig Code zum *Aufheben der Bereitstellung* nach jeder Imageanpassungsphase aus, um das Image zu *generalisieren*. Generalisieren ist ein Prozess, bei dem das Image so eingerichtet ist, dass es wieder verwendet werden kann, um mehrere VMs zu erstellen. Sie können jedoch auch VM-Einstellungen wie Hostname, Benutzername usw. übergeben. Unter Windows führt Azure Image Builder *Sysprep* und unter Linux `waagent -deprovision` aus. 

Unter Windows verwendet Azure Image Builder einen generischen Sysprep-Befehl. Dies ist jedoch nicht für jede erfolgreiche Windows-Generalisierung geeignet. Mit Azure Image Builder können Sie den Sysprep-Befehl anpassen. Hinweis: Azure Image Builder ist ein Tool zur Imageautomatisierung. Es ist für die erfolgreiche Ausführung des Sysprep-Befehls verantwortlich. Möglicherweise müssen Sie jedoch andere Sysprep-Befehle verwenden, um Ihr Image wiederverwendbar zu machen. Unter Linux verwendet Azure Image Builder einen generischen `waagent -deprovision+user`-Befehl. Weitere Informationen finden Sie in der [Microsoft Azure-Dokumentation zu Linux-Agents](https://github.com/Azure/WALinuxAgent#command-line-options).

Wenn Sie eine vorhandene Anpassung migrieren und verschiedene Sysprep- oder waagent-Befehle verwenden, können Sie die allgemeinen Image Builder-Befehle nutzen. Und wenn die VM-Erstellung fehlschlägt, können Sie die vorherigen Sysprep- oder waagent-Befehle verwenden.

> [!NOTE]
> Wenn AIB ein benutzerdefiniertes Windows-Image erfolgreich erstellt und Sie daraus eine VM erstellen und dann feststellen, dass die VM nicht erfolgreich erstellt werden kann (z. B. der Befehl zur VM-Erstellung wird nicht abgeschlossen oder aufgrund eines Timeouts abgebrochen), sollten Sie die Dokumentation zur Windows Server-Systemvorbereitung lesen. Alternativ können Sie eine Supportanfrage an das Supportteam des Kundendiensts für die Windows Server-Systemvorbereitung richten, das Ihnen bei der Problembehandlung behilflich sein und Sie hinsichtlich des richtigen Sysprep-Befehls beraten kann.

### <a name="command-locations-and-filenames"></a>Befehlsspeicherorte und Dateinamen

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep-Befehl: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Befehl zum Aufheben der Bereitstellung: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Überschreiben der Befehle

Verwenden Sie zum Überschreiben der Befehle die Shell- oder PowerShell-Skriptbereitstellungen, um die Befehlsdateien mit dem gleichen Dateinamen zu erstellen, und fügen Sie sie in die zuvor aufgeführten Verzeichnisse ein: Azure Image Builder liest diese Befehle, und die Ausgabe wird in die Datei *customization.log* geschrieben.

## <a name="getting-support"></a>Support erhalten
Wenn Sie mithilfe des Leitfadens das Problem immer noch nicht beheben können, haben Sie die Möglichkeit, eine Supportanfrage zu öffnen. In diesem Fall wählen Sie das richtige Produkt und das passende Supportthema aus, damit Ihnen das Azure VM Image Builder-Supportteam weiterhelfen kann.

Auswahl des Produkts für die Anfrage:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über Azure Image Builder](../image-builder-overview.md).