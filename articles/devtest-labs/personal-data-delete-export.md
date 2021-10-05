---
title: Löschen und Exportieren personenbezogener Daten
description: Erfahren Sie, wie Sie personenbezogene Daten aus dem Azure DevTest Labs-Dienst löschen und exportieren, um Ihren Verpflichtungen im Rahmen der allgemeinen Datenschutz-Grundverordnung (DSGVO) nachzukommen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 6b9d96e21d01e5e2c91768e7162fcb0621c1e006
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600606"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportieren oder Löschen personenbezogener Daten aus Azure DevTest Labs
Dieser Artikel enthält Schritte zum Löschen und Exportieren personenbezogener Daten aus dem Azure DevTest Labs-Dienst. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Welche personenbezogenen Daten werden durch DevTest Labs erfasst?
DevTest Labs erfasst im Wesentlichen zwei Bestandteile der personenbezogenen Daten vom Benutzer: die E-Mail-Adresse des Benutzers und die Benutzerobjekt-ID. Diese Informationen sind wichtig, damit der Dienst den Labadministratoren und Labbenutzern die im Dienst enthaltenen Features bereitstellen kann.

### <a name="user-email-address"></a>E-Mail-Adresse des Benutzers
DevTest Labs verwendet die E-Mail-Adresse des Benutzers, um beim automatischen Herunterfahren E-Mail-Benachrichtigungen an Labbenutzer zu senden. In der E-Mail werden Benutzer darüber benachrichtigt, dass ihr Computer heruntergefahren wird. Die Benutzer können das Herunterfahren nach Bedarf entweder verschieben oder überspringen. Die E-Mail-Adresse wird auf Lab- oder auf VM-Ebene konfiguriert.

**Festlegen der E-Mail-Adresse auf Labebene:**

![Festlegen der E-Mail-Adresse auf Labebene](./media/personal-data-delete-export/lab-user-email.png)

**Festlegen der E-Mail-Adresse auf VM-Ebene:**

![Festlegen der E-Mail-Adresse auf VM-Ebene](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Benutzerobjekt-ID
DevTest Labs verwendet die Benutzerobjekt-ID, um Kostentrends im Vergleich zum Vormonat sowie Informationen zu Kosten nach Ressourcen für Labadministratoren anzuzeigen. So können diese Kosten verfolgen und Schwellenwerte für ihr Lab verwalten. 

**Geschätzter Kostentrend für den aktuellen Kalendermonat:** 
![Geschätzter Kostentrend für den aktuellen Kalendermonat](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Geschätzte monatliche Kosten bis heute nach Ressource:** 
![Geschätzte monatliche Kosten bis heute nach Ressource](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Warum brauchen wir diese personenbezogenen Daten?
Der DevTest Labs-Dienst verwendet die personenbezogenen Daten zu operativen Zwecken. Diese Daten sind entscheidend, damit der Dienst wesentliche Features bereitstellen kann. Wenn Sie für die Benutzer-E-Mail-Adresse eine Aufbewahrungsrichtlinie festlegen, erhalten Labbenutzer keine rechtzeitigen E-Mail-Benachrichtigungen zum automatischen Herunterfahren, nachdem ihre E-Mail-Adresse aus unserem System gelöscht wurde. Ebenso wenig kann der Labadministrator Kostentrends im Vergleich zum Vormonat oder Kosten nach Ressource für die Computer in seinen Labs anzeigen, wenn die Benutzerobjekt-IDs aufgrund einer Aufbewahrungsrichtlinie gelöscht werden. Daher müssen diese Daten aufbewahrt werden, solange die Ressource des Benutzers im Lab aktiv ist.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Wie kann ich meine personenbezogenen Daten aus dem System entfernen?
Wenn Sie als Labbenutzer wünschen, dass diese personenbezogenen Daten gelöscht werden, können Sie die entsprechende Ressource im Lab löschen. Der DevTest Labs-Dienst anonymisiert die gelöschten persönlichen Daten 30 Tage nachdem sie vom Benutzer gelöscht wurden.

Wenn Sie beispielsweise Ihren virtuellen Computer löschen oder Ihre E-Mail-Adresse entfernt haben, werden diese Daten 30 Tage nach dem Löschen der Ressource vom DevTest Labs-Dienst anonymisiert. Die 30-Tage-Aufbewahrungsrichtlinie nach dem Löschvorgang stellt sicher, dass wir dem Labadministrator eine genaue Kostenprognose im Vergleich zum Vormonat bereitstellen.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Wie kann ich einen Export meiner personenbezogenen Daten anfordern?
Sie können private und Labverwendungsdaten mit Azure-Portal oder PowerShell exportieren. Die Daten werden als zwei verschiedene CSV-Dateien exportiert:

- **disks.csv** enthält Informationen zu den Datenträgern, die von den verschiedenen VMs verwendet werden.
- **virtualmachines.csv** enthält Informationen zu den virtuellen Computern im Lab.

### <a name="azure-portal"></a>Azure-Portal
Als Labbenutzer können Sie einen Export Ihrer personenbezogenen Daten anfordern, die im DevTest Labs-Dienst gespeichert werden. Wechseln Sie zum Anfordern eines Exports in Ihrem Lab auf der Seite **Übersicht** zur Option **Personenbezogene Daten**. Durch Auswahl der Schaltfläche **Export anfordern** wird eine herunterladbare Excel-Datei im Speicherkonto Ihres Labadministrators erstellt. Sie können sich dann an Ihren Labadministrator wenden, um diese Daten einzusehen.

1. Wählen Sie im Menü links die Option **Personenbezogene Daten**. 

    ![Seite „Personenbezogene Daten“](./media/personal-data-delete-export/personal-data-page.png)
2. Wählen Sie die **Ressourcengruppe** aus, die das Lab enthält.

    ![Auswählen der Ressourcengruppe](./media/personal-data-delete-export/select-resource-group.png)
3. Wählen Sie das **Speicherkonto** in der Ressourcengruppe aus.
4. Klicken Sie auf der Seite **Speicherkonto** auf **Blobs**.

    ![Auswählen der Kachel „Blobs“](./media/personal-data-delete-export/select-blobs-tile.png)
5. Wählen Sie in der Liste der Container den Container mit dem Namen **labresourceusage** aus.

    ![Auswählen des Blobcontainers](./media/personal-data-delete-export/select-blob-container.png)
6. Wählen Sie den **Ordner** aus, der nach Ihrem Lab benannt ist. In diesem Ordner finden Sie **CSV**-Dateien für **Datenträger** und **virtuelle Computer** in Ihrem Lab. Sie können diese CSV-Dateien herunterladen und deren Inhalt für den Labbenutzer, der Zugriff angefordert hat, filtern und bereitstellen.

    ![Herunterladen der CSV-Datei](./media/personal-data-delete-export/download-csv-file.png)

### <a name="azure-powershell&quot;></a>Azure PowerShell

```powershell
Param (
    [Parameter (Mandatory=$true, HelpMessage=&quot;The storage account name where to store usage data")]
    [string] $storageAccountName,

    [Parameter (Mandatory=$true, HelpMessage="The storage account key")]
    [string] $storageKey,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab name to get usage data from")]
    [string] $labName,

    [Parameter (Mandatory=$true, HelpMessage="The DevTest Lab subscription")]
    [string] $labSubscription
    )

#Login
Login-AzureRmAccount

# Set the subscription for the lab
Get-AzureRmSubscription -SubscriptionId $labSubscription  | Select-AzureRmSubscription

# DTL will create this container in the storage when invoking the action, cannot be changed currently
$containerName = "labresourceusage"

# Get the storage context
$Ctx = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageKey 
$SasToken = New-AzureStorageAccountSASToken -Service Blob, File -ResourceType Container, Service, Object -Permission rwdlacup -Protocol HttpsOnly -Context $Ctx

# Generate the storage blob uri
$blobUri = $Ctx.BlobEndPoint + $SasToken

# blobStorageAbsoluteSasUri and usageStartDate are required
$actionParameters = @{
    'blobStorageAbsoluteSasUri' = $blobUri    
}

$startdate = (Get-Date).AddDays(-7)

$actionParameters.Add('usageStartDate', $startdate.Date.ToString())

# Get the lab resource group
$resourceGroupName = (Find-AzureRmResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $labName}).ResourceGroupName
    
# Create the lab resource id
$resourceId = "/subscriptions/" + $labSubscription + "/resourceGroups/" + $resourceGroupName + "/providers/Microsoft.DevTestLab/labs/" + $labName + "/"

# !!!!!!! this is the new resource action to get the usage data.
$result = Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
 
# Finish up cleanly
if ($result.Status -eq "Succeeded") {
    Write-Output "Telemetry successfully downloaded for " $labName
    return 0
}
else
{
    Write-Output "Failed to download lab: " + $labName
    Write-Error $result.toString()
    return -1
}
```

Die Schlüsselkomponenten im obigen Beispiel sind:

- Der Invoke-AzureRmResourceAction-Befehl.
   
    ```
    Invoke-AzureRmResourceAction -Action 'exportLabResourceUsage' -ResourceId $resourceId -Parameters $actionParameters -Force
    ```
- Zwei Aktionsparameter
    - **blobStorageAbsoluteSasUri**: Der Speicherkonto-URI mit dem Shared Access Signature-Token (SAS). Im PowerShell-Skript könnte dieser Wert anstelle des Speicherschlüssels übermittelt werden.
    - **usageStartDate**: Das Anfangsdatum zum Pullen von Daten, wobei das Enddatum das aktuelle Datum ist, an dem die Aktion ausgeführt wird. Die Granularität liegt auf Tagesebene. Uhrzeitinformationen, die Sie hinzufügen, werden also ignoriert.

### <a name="exported-data---a-closer-look"></a>Exportierte Daten – genauere Betrachtung
Wir betrachten die exportierten Daten nun genauer. Wie bereits erwähnt, liegen nach dem erfolgreichen Export der Daten zwei CSV-Dateien vor. 

Die Datei **virtualmachines.csv** enthält die folgenden Datenspalten:

| Spaltenname | BESCHREIBUNG |
| ----------- | ----------- | 
| SubscriptionId | Der Abonnementbezeichner, in dem das Lab vorhanden ist. |
| LabUId | Eindeutiger GUID-Bezeichner für das Lab. |
| LabName | Name des Labs |
| LabResourceId | Voll qualifizierte Labressourcen-ID. |
| ResourceGroupName | Name der Ressourcengruppe, die die VM enthält. | 
| resourceId | Vollqualifizierte Ressourcen-ID für die VM. |
| ResourceUId | GUID für die VM |
| Name | Name des virtuellen Computers. |
| CreatedTime | Angabe des Zeitpunkts, zu dem die VM erstellt wurde, in Datum und Uhrzeit. |
| DeletedDate | Angabe des Zeitpunkts, zu dem die VM erstellt wurde, in Datum und Uhrzeit. Wenn keine Angabe vorhanden ist, wurde die Löschung noch nicht durchgeführt. |
| ResourceOwner | Besitzer der VM. Wenn keine Angabe vorhanden ist, handelt es sich entweder um eine abrufbare VM, oder sie wird von einem Dienstprinzipal erstellt. |
| PricingTier | Preisstufe der VM |
| ResourceStatus | Verfügbarkeitsstatus der VM. „Active“, wenn noch vorhanden, oder „Inactive“, wenn der virtuelle Computer gelöscht wurde. |
| ComputeResourceId | Voll qualifizierter VM-Computeressourcenbezeichner. |
| Claimable | Auf „true“ gesetzt, wenn der virtuelle Computer eine abrufbare VM ist. | 
| EnvironmentId | Der Umgebungsressourcenbezeichner, in dem der virtuelle Computer erstellt wurde. Leer, wenn die VM nicht als Teil einer Umgebungsressource erstellt wurde. |
| ExpirationDate | Das Ablaufdatum für die VM. Wenn leer, wurde noch kein Ablaufdatum festgelegt.
| GalleryImageReferenceVersion |  Version des VM-Basisimages. |
| GalleryImageReferenceOffer | Angebot des VM-Basisimages. |
| GalleryImageReferencePublisher | Herausgeber des VM-Basisimages. |
| GalleryImageReferenceSku | SKU des VM-Basisimages. |
| GalleryImageReferenceOsType | Betriebssystemtyp des VM-Basisimages. |
| CustomImageId | Voll qualifizierte ID des benutzerdefinierten VM-Basisimages. |

Die in **disks.csv** enthaltenen Datenspalten sind unten aufgeführt:

| Spaltenname | BESCHREIBUNG | 
| ----------- | ----------- | 
| SubscriptionId | ID des Abonnements, das das Lab enthält. |
| LabUId | GUID für das Lab |
| LabName | Name des Labs | 
| LabResourceId | Vollqualifizierte Ressourcen-ID für das Lab. | 
| ResourceGroupName | Name der Ressourcengruppe, die das Lab enthält. | 
| resourceId | Vollqualifizierte Ressourcen-ID für die VM. |
| ResourceUId | GUID für die VM |
 |Name | Der Name des angefügten Datenträgers. |
| CreatedTime |Das Datum und die Uhrzeit der Datenträgererstellung. |
| DeletedDate | Das Datum und die Uhrzeit der Datenträgerlöschung. |
| ResourceStatus | Status der Ressource. „Active“, wenn die Ressource vorhanden ist. „Inactive“, wenn sie gelöscht wurde. |
| DiskBlobName | Blobname für den Datenträger. |
| DiskSizeGB | Die Größe des Datenträgers. |
| DiskType | Der Typ des Datenträgers. 0 für Standard, 1 für Premium. |
| LeasedByVmId | Die Ressourcen-ID des virtuellen Computers, dem der Datenträger angefügt wurde. |


> [!NOTE]
> Wenn Sie mit mehreren Labs arbeiten und allgemeine Informationen erhalten möchten, nutzen Sie die beiden Schlüsselspalten **LabUID** und **ResourceUId**, die die eindeutigen IDs für alle Abonnements sind.

Die exportierten Daten können mithilfe von Tools wie SQL Server, Power BI usw. manipuliert und visualisiert werden. Diese Funktion ist besonders nützlich, wenn Sie die Nutzung Ihres Labs an ihr Verwaltungsteam melden möchten, das möglicherweise nicht dasselbe Azure-Abonnement verwendet.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln: 

- [Festlegen von Richtlinien für ein Lab](devtest-lab-set-lab-policy.md)
- [Häufig gestellte Fragen](devtest-lab-faq.yml)
