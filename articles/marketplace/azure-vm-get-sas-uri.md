---
title: Generieren eines SAS-URI für ein VM-Image
description: Generieren Sie im Azure Marketplace einen SAS-URI (Shared Access Signature) für eine virtuelle Festplatte (VHDs).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: krsh
ms.date: 06/23/2021
ms.openlocfilehash: 4bebe0ea34f9f9cc62b0ed4e96fd6fb1c7615635
ms.sourcegitcommit: fd83264abadd9c737ab4fe85abdbc5a216467d8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112913453"
---
# <a name="generate-a-sas-uri-for-a-vm-image"></a>Generieren eines SAS-URI für ein VM-Image

> [!NOTE]
> Für das Veröffentlichen einer VM ist kein SAS-URI erforderlich. Sie können Images einfach in Partner Center freigeben. Sehen Sie sich hierzu die Anweisungen unter [Erstellen eines virtuellen Computers mit einer genehmigten Basis](azure-vm-create-using-approved-base.md) oder [Erstellen eines virtuellen Computers mit einem eigenen Image](azure-vm-create-using-own-image.md) an.

Für das Generieren von SAS-URIs für Ihre VHDs gelten folgende Anforderungen:

- Es sind nur Auflistungs- und Leseberechtigungen erforderlich. Gewähren Sie keinen Schreib- oder Löschzugriff.
- Die Zugriffsdauer (Ablaufdatum) sollte mindestens drei Wochen betragen, gerechnet ab dem Erstellungsdatum des SAS-URI.
- Um Fehler aufgrund von UTC-Zeitabweichungen zu vermeiden, sollten Sie das Startdatum auf einen Tag vor dem aktuellen Datum festlegen. Wählen Sie beispielsweise den 15.6.2020 aus, wenn das aktuelle Datum der 16. Juni 2020 ist.

## <a name="extract-vhd-from-a-vm"></a>Extrahieren einer VHD aus einer VM

> [!NOTE]
> Wenn Sie bereits eine VHD in ein Storage-Konto hochgeladen haben, können Sie diesen Schritt überspringen.

Zum Extrahieren der VHD aus Ihrer VM müssen Sie eine Momentaufnahme Ihres VM-Datenträgers erstellen und die VHD aus dieser Momentaufnahme extrahieren.

Erstellen Sie zunächst eine Momentaufnahme des VM-Datenträgers:

1. Melden Sie sich beim Azure-Portal an.
2. Wählen Sie zunächst oben links Ressource erstellen aus, suchen Sie Momentaufnahme, und wählen Sie die Option aus.
3. Klicken Sie auf dem Blatt „Momentaufnahme“ auf „Erstellen“.
4. Geben Sie einen Namen für die Momentaufnahme ein.
5. Wählen Sie eine vorhandene Ressourcengruppe aus, oder geben Sie den Namen einer neuen Ressourcengruppe ein.
6. Wählen Sie für Quelldatenträger den verwalteten Datenträger aus, für den eine Momentaufnahme erstellt werden soll.
7. Wählen Sie den Kontotyp aus, der zum Speichern der Momentaufnahme verwendet werden soll. Wir empfehlen Standard-Festplattenlaufwerke, es sei denn, Sie benötigen eine leistungsstarke SSD.
8. Klicken Sie auf „Erstellen“.

### <a name="extract-the-vhd"></a>Extrahieren der VHD

Verwenden Sie das folgende Skript, um die Momentaufnahme in eine VHD in Ihrem Speicherkonto zu exportieren.

```azurecli
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. Currently, only general purpose v1 storage is supported.
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

$sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

### <a name="script-explanation"></a>Erläuterung des Skripts
Dieses Skript verwendet die folgenden Befehle zum Generieren des SAS-URIs für eine Momentaufnahme und zum Kopieren der zugrunde liegenden VHD in ein Speicherkonto mithilfe des SAS-URIs. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.


|Get-Help  |Notizen  |
|---------|---------|
| az disk grant-access    |     Generiert eine schreibgeschützte Shared Access Signature (SAS), die verwendet wird, um die zugrunde liegende VHD-Datei in ein Speicherkonto zu kopieren oder lokal herunterzuladen    |
|  az storage blob copy start   |    Kopiert ein Blob asynchron aus einem Speicherkonto in ein anderes. Verwenden Sie az storage blob show, um den Status des neuen Blobs zu überprüfen.     |
|

## <a name="generate-the-sas-address"></a>Generieren der SAS-Adresse

Es gibt zwei Tools, die häufig zum Erstellen einer SAS-Adresse (URL) verwendet werden:

1. **Azure Storage Explorer:** Im Azure-Portal verfügbar
2. **Azure CLI:** Empfohlen für andere Betriebssysteme als Windows und für automatisierte Umgebungen oder Continuous Integration-Umgebungen.

### <a name="using-tool-1-azure-storage-explorer"></a>Tool 1: Azure Storage-Explorer

1. Wechseln Sie zum **Speicherkonto**.
1. Öffnen Sie **Storage-Explorer**.

    :::image type="content" source="media/create-vm/storge-account-explorer.png" alt-text="Fenster „Speicherkonto“":::

3. Klicken Sie unter **Container** mit der rechten Maustaste auf die VHD-Datei, und wählen Sie **Share Access Signature abrufen** aus.
4. Füllen Sie im Dialogfeld **Shared Access Signature** die folgenden Felder aus:

    1. Startzeit: Das Startdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte. Geben Sie ein Datum an, das einen Tag vor dem aktuellen Datum liegt.
    2. Ablaufzeit: Das Ablaufdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte. Geben Sie ein Datum an, das mindestens drei Wochen nach dem aktuellen Datum liegt.
    3. Berechtigungen: Wählen Sie die Berechtigungen für das Lesen und Auflisten aus.
    4. Containerebene: Aktivieren Sie das Kontrollkästchen URI für Shared Access Signature auf Containerebene generieren.

    ![Dialogfeld „Shared Access Signature“](media/vm/create-sas-uri-storage-explorer.png)

5. Wählen Sie **Erstellen** aus, um den zugehörigen SAS-URI für diese VHD zu erstellen.
6. Kopieren Sie den URI, und speichern Sie ihn in einer Textdatei an einem sicheren Ort. Damit wurde der SAS-URI für den Zugriff auf Containerebene generiert. Bearbeiten Sie die Textdatei, und fügen Sie den Namen der VHD hinzu, um dies spezifisch anzugeben.
7. Fügen Sie den Namen Ihrer virtuellen Festplatte nach der Zeichenfolge „vhds“ in den SAS-URI ein (einschließlich Schrägstrich). Der endgültige SAS-URI sollte wie folgt aussehen:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

8. Wiederholen Sie diese Schritte für jede VHD in den Plänen, die Sie veröffentlichen möchten.

### <a name="using-tool-2-azure-cli"></a>Tool 2: Azure CLI

1. Laden Sie die [Microsoft Azure CLI](/cli/azure/install-azure-cli) herunter, und installieren Sie diese. Es stehen Versionen für Windows, macOS und verschiedene Linux-Distributionen zur Verfügung.
2. Erstellen Sie eine PowerShell-Datei (Dateierweiterung PS1), kopieren Sie den folgenden Code in die Datei, und speichern Sie sie lokal.

    ```azurecli-interactive
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net' --name <container-name> --permissions rl --start '<start-date>' --expiry '<expiry-date>'
    ```

3. Bearbeiten Sie die Datei, um die folgenden Parameterwerte zu verwenden. Verwenden Sie für Datumsangaben das UTC-Format für Datum und Uhrzeit, z. B. 2020-04-01T00:00:00Z.

    - account-name: Der Name Ihres Azure Storage-Kontos
    - account-key: Der Schlüssel Ihres Azure Storage-Kontos
    - start-date: Das Startdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte. Geben Sie ein Datum an, das einen Tag vor dem aktuellen Datum liegt.
    - expiry-date: Das Ablaufdatum der Berechtigung für den Zugriff auf die virtuelle Festplatte. Geben Sie ein Datum an, das mindestens drei Wochen nach dem aktuellen Datum liegt.

    Dieses Beispiel enthält richtige Parameterwerte (zum Zeitpunkt der Veröffentlichung dieses Dokuments):

    ```azurecli-interactive
    az storage container generate-sas --connection-string 'DefaultEndpointsProtocol=https;AccountName=st00009;AccountKey=6L7OWFrlabs7Jn23OaR3rvY5RykpLCNHJhxsbn9ON c+bkCq9z/VNUPNYZRKoEV1FXSrvhqq3aMIDI7N3bSSvPg==;EndpointSuffix=core.windows.net' --name <container-name> -- permissions rl --start '2020-04-01T00:00:00Z' --expiry '2021-04-01T00:00:00Z'
    ```

1. Speichern Sie die Änderungen.
2. Führen Sie dieses Skript mit einem der folgenden Verfahren mit Administratorrechten aus, um eine SAS-Verbindungszeichenfolge für den Zugriff auf Containerebene zu erstellen:

    - Führen Sie das Skript an der Konsole aus. Klicken Sie unter Windows mit der rechten Maustaste auf das Skript, und wählen Sie **Als Administrator ausführen** aus.
    - Führen Sie das Skript mit einem PowerShell-Skript-Editor aus, z. B. [Windows PowerShell ISE](/powershell/scripting/components/ise/introducing-the-windows-powershell-ise). Auf diesem Bildschirm wird die Erstellung einer SAS-Verbindungszeichenfolge in diesem Editor angezeigt:

    [![Erstellung einer SAS-Verbindungszeichenfolge im PowerShell-Editor](media/vm/create-sas-uri-power-shell-ise.png)](media/vm/create-sas-uri-power-shell-ise.png#lightbox)

6. Kopieren Sie die SAS-Verbindungszeichenfolge, und speichern Sie sie in einer Textdatei an einem sicheren Ort. Bearbeiten Sie diese Zeichenfolge, und fügen Sie die Informationen zum Speicherort der virtuellen Festplatte hinzu, um den endgültigen SAS-URI zu erstellen.
7. Navigieren Sie im Azure-Portal zu dem Blobspeicher, der die VHD mit dem neuen URI enthält.
8. Kopieren Sie die URL des Blobdienstendpunkts:

    ![Die URL des Blob-Dienstendpunkts wird kopiert.](media/vm/create-sas-uri-blob-endpoint.png)

9. Bearbeiten Sie die Textdatei mit der SAS-Verbindungszeichenfolge aus Schritt 6. Erstellen Sie den gesamten SAS-URI im folgenden Format:

    `<blob-service-endpoint-url> + /vhds/ + <vhd-name>? + <sas-connection-string>`

## <a name="verify-the-sas-uri"></a>Überprüfen des SAS-URI

Überprüfen Sie den SAS-URI, bevor Sie ihn im Partner Center veröffentlichen, um Probleme im Zusammenhang mit der SAS-URI-Übermittlung der Anforderung zu vermeiden. Dieser Schritt ist optional, wird aber empfohlen.

- Der URI enthält den Namen Ihrer VHD-Imagedatei, einschließlich der Dateierweiterung `.vhd`.
- `Sp=rl` wird ungefähr in der Mitte Ihres URI angezeigt. Mit dieser Zeichenfolge wird angegeben, dass Zugriffsberechtigungen zum Lesen und Auflisten gewährt werden.
- `sr=c` steht für Zugriff auf Containerebene.
- Kopieren Sie den URI, und fügen Sie ihn in einen Browser ein, um das Blob testweise herunterzuladen. (Sie können den Vorgang vor Abschluss des Downloads abbrechen.)

## <a name="next-steps"></a>Nächste Schritte

- Bei Problemen lesen Sie die Informationen unter [SAS-Fehlermeldungen auf VMs](azure-vm-sas-failure-messages.md).
- [Anmelden bei Partner Center](https://go.microsoft.com/fwlink/?linkid=2165935)
- [Erstellen eines VM-Angebots im Azure Marketplace](azure-vm-create.md)