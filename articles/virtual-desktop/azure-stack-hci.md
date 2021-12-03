---
title: 'Einrichten von Azure Virtual Desktop für Azure Stack HCI (Vorschauversion): Azure'
description: Es wird beschrieben, wie Sie Azure Virtual Desktop für Azure Stack HCI (Vorschauversion) einrichten.
author: Heidilohr
ms.topic: how-to
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: d91722247e44016695154912277a0e1e2370fed6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452127"
---
# <a name="set-up-azure-virtual-desktop-for-azure-stack-hci-preview"></a>Einrichten von Azure Virtual Desktop für Azure Stack HCI (Vorschauversion)

> [!IMPORTANT]
> Azure Virtual Desktop für Azure Stack HCI befindet sich derzeit in der Vorschauphase.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Mit Azure Virtual Desktop für Azure Stack HCI (Vorschauversion) können Sie Azure Virtual Desktop-Sitzungshosts in Ihrer lokalen Azure Stack HCI-Infrastruktur verwenden. Weitere Informationen finden Sie unter [Azure Virtual Desktop für Azure Stack HCI (Vorschau)](azure-stack-hci-overview.md).

## <a name="requirements"></a>Anforderungen

Sie benötigen Folgendes, um Azure Virtual Desktop für Azure Stack HCI verwenden zu können:

- Einen [Azure Stack HCI-Cluster, der bei Azure registriert ist](/azure-stack/hci/deploy/register-with-azure).

- Ein Azure-Abonnement für die Erstellung eines Azure Virtual Desktop-Sitzungshostpools mit allen erforderlichen Administratorberechtigungen.

- Eine [lokale AD-Instanz (Active Directory), die mit Azure Active Directory synchronisiert ist](/azure/architecture/reference-architectures/identity/azure-ad).

- Eine stabile Verbindung mit Azure aus Ihrem lokalen Netzwerk.

## <a name="configure-azure-virtual-desktop-for-azure-stack-hci"></a>Konfigurieren von Azure Virtual Desktop für Azure Stack HCI

Richten Sie Azure Virtual Desktop für Azure Stack HCI wie folgt ein:

1. Erstellen Sie einen neuen Hostpool ohne virtuelle Computer, indem Sie die Anleitung unter [Erste Schritte des Einrichtungsprozesses für den Hostpool](create-host-pools-azure-marketplace.md#begin-the-host-pool-setup-process) befolgen. Wechseln Sie am Ende dieses Abschnitts zurück zu diesem Artikel, und fahren Sie mit Schritt 2 fort.

2. Konfigurieren Sie den neu erstellten Hostpool als Überprüfungs-Hostpool, indem Sie die Schritte unter [Definieren Ihres Hostpools als Überprüfungs-Hostpool](create-validation-host-pool.md#define-your-host-pool-as-a-validation-host-pool) befolgen, um die Eigenschaft „Überprüfungsumgebung“ zu aktivieren.

3. Befolgen Sie die Anleitung unter [Informationen zum Arbeitsbereich](create-host-pools-azure-marketplace.md#workspace-information), um einen eigenen Arbeitsbereich zu erstellen.

4. Stellen Sie einen neuen virtuellen Computer in Ihrer Azure Stack HCI-Infrastruktur bereit, indem Sie die Anleitung unter [Erstellen eines neuen virtuellen Computers](/azure-stack/hci/manage/vm#create-a-new-vm) befolgen. Stellen Sie einen virtuellen Computer mit einem unterstützten Betriebssystem bereit, und binden Sie ihn in eine Domäne ein.

   >[!NOTE]
   >Installieren Sie die Rolle „Remotedesktop-Sitzungshost (RDSH)“, wenn auf dem virtuellen Computer ein Windows Server-Betriebssystem ausgeführt wird.

5. Ermöglichen Sie für Azure die Verwaltung des neuen virtuellen Computers über Azure Arc, indem Sie darauf den Connected Machine-Agent installieren. Befolgen Sie die Anleitung unter [Verbinden von Hybridcomputern mit Azure Arc-fähigen Servern](../azure-arc/servers/learn/quick-enable-hybrid-vm.md), um den Windows-Agent auf dem virtuellen Computer zu installieren.

6. Fügen Sie den virtuellen Computer dem Azure Virtual Desktop-Hostpool hinzu, den Sie zuvor erstellt haben, indem Sie den [Azure Virtual Desktop-Agent](agent-overview.md) installieren. Befolgen Sie anschließend die Anleitung unter [Registrieren der VMs beim Azure Virtual Desktop-Hostpool](create-host-pools-powershell.md#register-the-virtual-machines-to-the-azure-virtual-desktop-host-pool), um den virtuellen Computer beim Azure Virtual Desktop-Dienst zu registrieren.

7. Befolgen Sie die Anleitung unter [Erstellen von App-Gruppen und Verwalten von Benutzerzuweisungen](manage-app-groups.md), um eine App-Gruppe zu Testzwecken zu erstellen und ihr Benutzerzugriff zuzuweisen.

8. Navigieren Sie zum [Webclient](./user-documentation/connect-web.md), und gewähren Sie Ihren Benutzern Zugriff auf die neue Bereitstellung.

## <a name="optional-configurations"></a>Optionale Konfigurationen

Nachdem Sie Azure Virtual Desktop für Azure Stack HCI nun eingerichtet haben, können Sie einige zusätzliche Schritte ausführen (siehe unten). Dies richtet sich jeweils nach den Anforderungen Ihrer Bereitstellung.

### <a name="create-a-profile-container-using-a-file-share-on-azure-stack-hci"></a>Erstellen eines Profilcontainers mit einer Dateifreigabe in Azure Stack HCI

Erstellen Sie einen Profilcontainer mit einer Dateifreigabe wie folgt:

1. Stellen Sie für eine Einzel- oder Clusterbereitstellung einer Windows Server-VM eine Dateifreigabe bereit. Die Windows Server-VMs mit Dateiserverrolle können auch zusammen in demselben Cluster angeordnet werden, in dem die Sitzungshost-VMs bereitgestellt werden.

2. Stellen Sie eine Verbindung mit dem virtuellen Computer her, indem Sie die Anmeldeinformationen verwenden, die Sie beim Erstellen des virtuellen Computers angegeben haben.

3. Starten Sie auf dem virtuellen Computer die **Systemsteuerung**, und wählen Sie **System**.

4. Wählen Sie „Computername“, **Einstellungen ändern** und dann **Ändern…** aus.

5. Wählen Sie die Option **Domäne** aus, und geben Sie dann die Active Directory-Domäne im virtuellen Netzwerk ein.

6. Authentifizieren Sie sich mit einem Domänenkonto, das über Berechtigungen für in die Domäne eingebundene Computer verfügt.

7. Befolgen Sie die Anleitung unter [Vorbereiten des virtuellen Computers als Dateifreigabe](create-host-pools-user-profile.md#prepare-the-virtual-machine-to-act-as-a-file-share-for-user-profiles), um Ihren virtuellen Computer auf die Bereitstellung vorzubereiten.

8. Befolgen Sie die Anleitung unter [Konfigurieren des FSLogix-Profilcontainers](create-host-pools-user-profile.md#configure-the-fslogix-profile-container), um Ihren Profilcontainer für die Verwendung zu konfigurieren.

### <a name="download-supported-os-images-from-azure-marketplace"></a>Herunterladen unterstützter Betriebssystemimages aus Azure Marketplace

Sie können alle Betriebssystemimages ausführen, die sowohl von Azure Virtual Desktop als auch von Azure Stack HCI für Ihre Bereitstellung unterstützt werden. Informationen zu den von Azure Virtual Desktop unterstützten Betriebssystemen finden Sie im Abschnitt zu den [unterstützten VM-Betriebssystemimages](overview.md#supported-virtual-machine-os-images).

Sie haben zwei Möglichkeiten, ein Image herunterzuladen:

- Stellen Sie einen virtuellen Computer mit Ihrem bevorzugten Betriebssystemimage bereit, und befolgen Sie dann die Anleitung unter [Herunterladen einer Windows-VHD von Azure](../virtual-machines/windows/download-vhd.md).
- Laden Sie eine Windows-VHD (Virtual Hard Disk, virtuelle Festplatte) von Azure herunter, ohne einen virtuellen Computer bereitzustellen.

Zum Herunterladen einer Windows-VHD ohne Bereitstellung eines virtuellen Computers müssen einige zusätzliche Schritte ausgeführt werden. Um eine VHD von Azure herunterzuladen, ohne einen virtuellen Computer bereitzustellen, müssen Sie die Anleitungsschritte in den folgenden Abschnitten in der richtigen Reihenfolge ausführen.

### <a name="requirements-to-download-a-vhd-without-a-vm"></a>Anforderungen zum Herunterladen einer VHD ohne virtuellen Computer

Stellen Sie zunächst sicher, dass Sie mit Azure verbunden sind und [Azure Cloud Shell](../cloud-shell/quickstart.md) über eine Eingabeaufforderung oder in der Bash-Umgebung ausführen. Sie können CLI-Referenzbefehle auch über die Azure-Befehlszeilenschnittstelle (CLI) ausführen.

Führen Sie bei Verwendung einer lokalen Installation den Befehl [az login](/cli/azure/reference-index#az_login) aus, um sich bei Azure anzumelden.

Befolgen Sie anschließend alle weiteren angezeigten Aufforderungen, um den Anmeldevorgang abzuschließen. Weitere Anmeldeoptionen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli).

Gehen Sie wie folgt vor, falls Sie die Azure CLI zum ersten Mal verwenden: Installieren Sie alle erforderlichen Erweiterungen, indem Sie die Anleitung unter [Verwenden von Erweiterungen mit der Azure CLI](/cli/azure/azure-cli-extensions-overview) befolgen.

Führen Sie abschließend den Befehl [az version](/cli/azure/reference-index?#az_version) aus, um sicherzustellen, dass Ihr Client auf dem neuesten Stand ist. Falls nicht, führen Sie den Befehl [az upgrade](/cli/azure/reference-index?#az_upgrade) aus, um das Upgrade auf die aktuelle Version durchzuführen.

### <a name="search-azure-marketplace-for-azure-virtual-desktop-images"></a>Suchen nach Azure Virtual Desktop-Images auf dem Azure Marketplace

Sie finden das gewünschte Image, indem Sie in Azure Marketplace im Azure-Portal die **Suchfunktion** verwenden. Um nach spezifischen Images für Azure Virtual Desktop zu suchen, können Sie eine der folgenden Beispielabfragen ausführen.

Falls Sie nach „Windows 10 (mehrere Sitzungen)“ suchen, können Sie eine Suche mit den folgenden Kriterien durchführen:

```azure
az vm image list --all --publisher "microsoftwindowsdesktop" --offer "windows-10" --sku "21h1-evd-g2"
```

Mit diesem Befehl sollte der folgende URN zurückgegeben werden:

```azure
MicrosoftWindowsDesktop:Windows-10:21h1-evd-g2:latest
```

Wenn Sie nach Windows Server 2019 Datacenter suchen, können Sie in Ihrer Azure CLI eine Suche mit den folgenden Kriterien durchführen:

```azure
az vm image list --all --publisher "microsoftwindowsserver" --offer "WindowsServer" --sku "2019-Datacenter-gen2"
```

Mit diesem Befehl sollte der folgende URN zurückgegeben werden:

```azure
MicrosoftWindowsServer:windowsserver-gen2preview:2019-datacenter-gen2:latest
```

>[!IMPORTANT]
>Achten Sie darauf, dass Sie nur Images der 2. Generation („Gen2“) verwenden. Für Azure Virtual Desktop für Azure Stack HCI wird die Erstellung eines virtuellen Computers mit einem Image der 1. Generation („Gen1“) nicht unterstützt. Vermeiden Sie die Verwendung von SKUs mit dem Suffix „-g1“.

### <a name="create-a-new-azure-managed-disk-from-the-image"></a>Erstellen eines neuen verwalteten Azure-Datenträgers aus dem Image

Als Nächstes müssen Sie einen verwalteten Azure-Datenträger aus dem Image erstellen, das Sie vom Azure Marketplace heruntergeladen haben.

Erstellen Sie wie folgt einen verwalteten Azure-Datenträger:

1. Führen Sie die folgenden Befehle über die Azure-Befehlszeile aus, um die Parameter für Ihren verwalteten Datenträger festzulegen. Achten Sie darauf, dass Sie die Elemente in Klammern durch die relevanten Werte für Ihr Szenario ersetzen.

```azure
$urn = <URN of the Marketplace image> #Example: “MicrosoftWindowsServer:WindowsServer:2019-Datacenter:Latest”
$diskName = <disk name> #Name for new disk to be created
$diskRG = <resource group> #Resource group that contains the new disk
```

2. Führen Sie diese Befehle aus, um den Datenträger zu erstellen und eine SAS-Zugriffs-URL (Serial Attached SCSI) zu generieren.

```azure
az disk create -g $diskRG -n $diskName --image-reference $urn
$sas = az disk grant-access --duration-in-seconds 36000 --access-level Read --name $diskName --resource-group $diskRG
$diskAccessSAS = ($sas | ConvertFrom-Json)[0].accessSas
```

### <a name="export-a-vhd-from-the-managed-disk-to-azure-stack-hci-cluster"></a>Exportieren einer VHD vom verwalteten Datenträger in einen Azure Stack HCI-Cluster

Anschließend müssen Sie die VHD, die Sie aus dem verwalteten Datenträger erstellt haben, in Ihren Azure Stack HCI-Cluster exportieren, um neue VMs erstellen zu können. Sie können die folgende Methode in einem regulären Webbrowser oder Storage-Explorer verwenden.

Exportieren Sie die VHD wie folgt:

1. Öffnen Sie einen Browser, und navigieren Sie zur SAS-URL des verwalteten Datenträgers, den Sie unter [Erstellen eines neuen verwalteten Azure-Datenträgers aus dem Image](#create-a-new-azure-managed-disk-from-the-image) generiert haben. Sie können das VHD-Image für das Image, das Sie vom Azure Marketplace heruntergeladen haben, unter dieser URL herunterladen.

2. Laden Sie das VHD-Image herunter. Haben Sie etwas Geduld, da der Downloadvorgang einige Minuten dauern kann. Vergewissern Sie sich, dass das Image vollständig heruntergeladen wurde, bevor Sie mit dem nächsten Abschnitt fortfahren.

>[!NOTE]
>Wenn Sie azcopy ausführen, müssen Sie möglicherweise md5check überspringen, indem Sie den folgenden Befehl ausführen:
>
> ```azure
> azcopy copy “$sas" "destination_path_on_cluster" --check-md5 NoCheck
> ```

### <a name="clean-up-the-managed-disk"></a>Bereinigen des verwalteten Datenträgers

Wenn Sie Ihre VHD nicht mehr benötigen, sollten Sie Speicherplatz freigeben, indem Sie den verwalteten Datenträger löschen.

Führen Sie diese Befehle aus, um den von Ihnen erstellten verwalteten Datenträger zu löschen:

```azure
az disk revoke-access --name $diskName --resource-group $diskRG 
az disk delete --name $diskName --resource-group $diskRG --yes
```

Haben Sie etwas Geduld, da die Ausführung dieses Befehls einige Minuten dauern kann.

>[!NOTE]
>Optional können Sie die heruntergeladene VHD auch in eine dynamische VHDx konvertieren, indem Sie den folgenden Befehl ausführen:
>
> ```powershell
> Convert-VHD -Path " destination_path_on_cluster\file_name.vhd" -DestinationPath " destination_path_on_cluster\file_name.vhdx" -VHDType Dynamic
> ```

## <a name="next-steps"></a>Nächste Schritte

Falls Sie Ihr Wissen zu den Grundlagen oder zu den Preisen auffrischen möchten, hilft Ihnen der Artikel zu [Azure Virtual Desktop für Azure Stack HCI](azure-stack-hci-overview.md) weiter.

Nutzen Sie bei weiteren Fragen den [Artikel zu den häufig gestellten Fragen](azure-stack-hci-faq.yml).
