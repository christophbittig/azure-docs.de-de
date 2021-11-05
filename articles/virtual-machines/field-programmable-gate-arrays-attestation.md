---
title: 'Azure-Nachweisdienst: FPGA'
description: Enthält eine Beschreibung des Nachweisdiensts für VMs der NP-Serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 110021730ecc91281f2c187a5ef6f1f989fbaf87
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018468"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA-Nachweis für Azure-VMs der NP-Serie (Vorschau)

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Beim FPGA-Nachweisdienst wird eine Reihe von Überprüfungen einer Entwurfsprüfpunkt-Datei (als „netlist“ bezeichnet) durchgeführt, die mit dem Xilinx-Toolset generiert wird. Darüber hinaus wird eine Datei mit dem überprüften Image (als „Bitstream“ bezeichnet) erstellt, die auf einer VM der NP-Serie auf die Xilinx U250 FPGA-Karte geladen werden kann.  

## <a name="prerequisites"></a>Voraussetzungen  

Sie benötigen ein Azure-Abonnement und ein Azure Storage-Konto. Über das Abonnement haben Sie Zugriff auf Azure, und das Speicherkonto wird verwendet, um Ihre netlist-Datei und die Ausgabedateien des Nachweisdiensts zu speichern.  

Wir stellen PowerShell- und Bash-Skripts zum Übermitteln von Nachweisanforderungen bereit.   Für die Skripts wird die Azure CLI verwendet, die unter Windows und Linux ausgeführt werden kann. PowerShell kann unter Windows, Linux und macOS ausgeführt werden.  

[Azure CLI-Download (erforderlich)](/cli/azure/install-azure-cli)

[PowerShell-Download für Windows, Linux und macOS (nur für PowerShell-Skripts)](/powershell/scripting/install/installing-powershell)

Sie müssen Ihre Mandanten- und Abonnement-ID für die Übermittlung an den Nachweisdienst autorisieren lassen. Sie können den Zugriff unter [https://aka.ms/AzureFPGAAttestationPreview](https://aka.ms/AzureFPGAAttestationPreview) anfordern. 

## <a name="building-your-design-for-attestation"></a>Erstellen eines Entwurfs für den Nachweis  

Das bevorzugte Xilinx-Toolset zum Entwickeln von Entwürfen ist Vitis 2020.2. Die Verwendung von netlist-Dateien, die mit einer früheren Version des Toolsets erstellt wurden und mit 2020.2 kompatibel sind, ist möglich. Vergewissern Sie sich, dass Sie die richtige Shell für die Entwicklung geladen haben. Die zurzeit unterstützte Version ist `xilinx_u250_gen3x16_xdma_2_1_202010_1`. Die unterstützenden Dateien können aus der Xilinx Alveo Lounge heruntergeladen werden.

Sie müssen das folgende Argument in Vitis (v++-Befehlszeile) einschließen, um eine `xclbin`-Datei zu erstellen, die anstelle einer Bitstream-Datei eine Entwurfsprüfpunkt-Datei enthält.

`--advanced.param compiler.acceleratorBinaryContent=dcp`

## <a name="logging-into-azure"></a>Protokollieren in Azure  

Bevor Sie Vorgänge mit Azure durchführen können, müssen Sie sich bei Azure anmelden und das Abonnement festlegen, das zum Aufrufen des Diensts autorisiert ist. Verwenden Sie hierfür die Befehle `az login` und `az account set –s <Sub ID or Name>`. Weitere Informationen zu diesem Prozess finden Sie hier: [Anmelden mit Azure CLI](/cli/azure/authenticate-azure-cli). Verwenden Sie in der Befehlszeile entweder die Option zum **interaktiven Anmelden** oder zum **Anmelden mit Anmeldeinformationen**.  

## <a name="creating-a-storage-account-and-blob-container"></a>Erstellen eines Speicherkontos und Blobcontainers  

Ihre netlist-Datei muss in einen Azure Storage-Blobcontainer hochgeladen werden, damit der Nachweisdienst darauf zugreifen kann.  

Auf der folgenden Seite finden Sie weitere Informationen zum Erstellen des Kontos und eines Containers und zum Hochladen Ihrer netlist-Datei als Blob in diesen Container: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](../storage/blobs/storage-quickstart-blobs-cli.md).  

Sie können hierfür auch das Azure-Portal verwenden.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Hochladen Ihrer netlist-Datei in Azure-Blobspeicher  

Es gibt mehrere Möglichkeiten, die Datei zu kopieren. Unten ist ein Beispiel mit Verwendung des Cmdlets „az storage upload“ angegeben. Die az-Befehle können unter Linux und unter Windows ausgeführt werden. Sie können einen beliebigen Namen als Blobnamen wählen, aber Sie müssen die Erweiterung `xclbin` beibehalten.

`az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>`

## <a name="download-the-attestation-scripts"></a>Herunterladen der Nachweisskripts  

Die Überprüfungsskripts können aus dem folgenden Azure Storage-Blobcontainer heruntergeladen werden:  

[https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip](https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip)

Die ZIP-Datei enthält zwei PowerShell-Skripts: eins für die Übermittlung und das andere für die Überwachung. Bei der dritten Datei handelt es sich um ein Bash-Skript, mit dem beide Funktionen ausgeführt werden.  

## <a name="running-the-attestation-scripts"></a>Ausführen der Nachweisskripts  

Zum Ausführen der Skripts müssen Sie den Namen Ihres Speicherkontos, den Namen des Blobcontainers, in dem die netlist-Datei gespeichert ist, und den Namen der netlist-Datei angeben. Darüber hinaus müssen Sie eine Shared Access Signature (SAS) für den Dienst erstellen, mit der Ihrem Container (nicht der netlist) Lese- und Schreibzugriff gewährt wird. Diese SAS wird vom Nachweisdienst genutzt, um eine lokale Kopie Ihrer netlist-Datei zu erstellen und die sich ergebenden Ausgabedateien des Überprüfungsprozesses in Ihren Container zurückzuschreiben.  

Eine Übersicht über Shared Access Signatures finden Sie hier und spezifische Informationen zur Dienst-SAS finden Sie hier. Die Seite zur Dienst-SAS enthält einen wichtigen Warnhinweis, in dem es um den Schutz der generierten SAS geht.  Lesen Sie sich den Warnhinweis durch, um sich darüber zu informieren, warum es erforderlich ist, die SAS vor böswilliger oder unbeabsichtigter Nutzung zu schützen.  

Sie können eine SAS für Ihren Container generieren, indem Sie das Cmdlet „az storage container generate-sas“ verwenden. Geben Sie einen Ablaufzeitpunkt im UTC-Format an, der mindestens einige Stunden nach dem Zeitpunkt der Übermittlung liegt. Sechs Stunden sollten in diesem Fall mehr als ausreichend sein.  

Falls Sie virtuelle Verzeichnisse verwenden möchten, müssen Sie die Verzeichnishierarchie in das Containerargument einbinden. Wenn Sie beispielsweise über einen Container mit dem Namen „netlists“ und ein virtuelles Verzeichnis mit dem Namen „image1“ verfügen, in dem das netlist-Blob enthalten ist, geben Sie „netlists/image1“ als Containernamen an. Fügen Sie ggf. weitere Verzeichnisnamen an, um eine Hierarchie mit mehreren Ebenen anzugeben. 

### <a name="powershell"></a>PowerShell   

```powershell
$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)

.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>
```

### <a name="bash"></a>Bash  

```bash
sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  

validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas
``` 

## <a name="checking-on-the-status-of-your-submission"></a>Überprüfen des Status Ihrer Übermittlung  

Der Nachweisdienst gibt die Orchestrierungs-ID Ihrer Übermittlung zurück. Die Übermittlungsskripts beginnen automatisch mit der Überwachung der Übermittlung, indem die Informationen zum Abschluss abgerufen werden. Die Orchestrierungs-ID ist das wichtigste Mittel, mit dem wir überprüfen können, was mit Ihrer Übermittlung passiert ist. Bewahren Sie die ID daher für den Fall, dass ein Problem auftritt, gut auf. Richtwerte für die Dauer des Nachweisvorgangs sind ca. 30 Minuten für eine kleine netlist-Datei (300 MB) und eine Stunde für eine Datei mit einer Größe von 1,6 GB. 

Sie können jederzeit das Skript „Monitor-Validation.ps1“ aufrufen, um den Status und die Ergebnisse des Nachweisvorgangs zu erhalten. Geben Sie hierzu die Orchestrierungs-ID als Argument an:  

`.\Monitor-Validation.ps1 -OrchestrationId <orchestration ID>`

Alternativ können Sie die HTTP POST-Anforderung auch an den Endpunkt des Nachweisdiensts übermitteln:  

`https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus`

Der Anforderungstext sollte die Abonnement-ID, Mandanten-ID und Orchestrierungs-ID Ihrer Nachweisanforderung enthalten:  

```json
{  
  "OrchestrationId": "<orchestration ID>",  
  "ClientSubscriptionId": "<your subscription ID>",  
  "ClientTenantId": "<your tenant ID>"
}
```

## <a name="post-validation-steps"></a>Schritte nach der Überprüfung

Der Dienst schreibt seine Ausgabe zurück in Ihren Container. Wenn der Überprüfungsvorgang erfolgreich ist, enthält Ihr Container die ursprüngliche netlist-Datei (abc.xclbin), eine Datei mit dem Bitstream (abc.bit.xclbin), eine Datei mit der Angabe des privaten Speicherorts Ihres gespeicherten Bitstreams (abc.azure.xclbin) sowie vier Protokolldateien: eine für den Startprozess (abc-log.txt) und jeweils eine für die drei parallelen Phasen, in denen die Überprüfung erfolgt. Diese haben den Namen „*logPhaseX.txt“, wobei „X“ für die Nummer der Phase steht. Die Datei „azure.xclbin“ wird auf Ihrer VM verwendet, um das Hochladen Ihres überprüften Images auf die U250 zu signalisieren. 

Falls die Überprüfung nicht erfolgreich war, wird die Datei „error-*.txt“ geschrieben. Sie enthält den Hinweis, in welchem Schritt ein Fehler aufgetreten ist. Überprüfen Sie auch die Protokolldateien, falls im Fehlerprotokoll angezeigt wird, dass der Nachweisvorgang nicht erfolgreich war. Achten Sie bei einer Kontaktaufnahme mit unserem Support darauf, dass Sie alle diese Dateien sowie die Orchestrierungs-ID in die Supportanfrage einfügen.  

Sie können das Azure-Portal nutzen, um Ihren Container zu erstellen, und Sie können Ihre netlist hoch- und die Bitstream- und Protokolldateien herunterladen. Die Übermittlung einer Nachweisanforderung und die Überwachung des Status über das Portal werden derzeit nicht unterstützt. Hierfür müssen Sie die oben beschriebenen Skripts verwenden.
