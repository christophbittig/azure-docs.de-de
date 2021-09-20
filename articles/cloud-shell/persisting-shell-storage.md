---
title: Beibehalten von Dateien in Azure Cloud Shell | Microsoft-Dokumentation
description: Exemplarische Vorgehensweise für das Beibehalten von Dateien in Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: a554a58c8cb94fb4ec1f86b433050f440330918b
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252607"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Beibehalten von Dateien in Azure Cloud Shell
Cloud Shell nutzt Azure Files, um Dateien sitzungsübergreifend beizubehalten. Beim ersten Start fordert Cloud Shell Sie auf, eine neue oder vorhandene Dateifreigabe zu verknüpfen, um Dateien sitzungsübergreifend beizubehalten.

> [!NOTE]
> Für Bash und PowerShell wird dieselbe Dateifreigabe verwendet. In Cloud Shell kann der automatischen Bereitstellung nur eine Dateifreigabe zugeordnet werden.

> [!NOTE]
> Die Azure Storage-Firewall wird für Cloud Shell-Speicherkonten nicht unterstützt.

## <a name="create-new-storage"></a>Erstellen von neuem Speicher

Wenn Sie die grundlegenden Einstellungen verwenden und nur ein Abonnement auswählen, werden von Cloud Shell in der für Sie unterstützten nächstgelegenen Region drei Ressourcen erstellt:
* Ressourcengruppe: `cloud-shell-storage-<region>`
* Speicherkonto: `cs<uniqueGuid>`
* Dateifreigabe: `cs-<user>-<domain>-com-<uniqueGuid>`

![Einstellung „Abonnement“](media/persisting-shell-storage/basic-storage.png)

Die Dateifreigabe wird in Ihrem Verzeichnis `$Home` als `clouddrive` bereitgestellt. Dies ist eine einmalige Aktion, und die Dateifreigabe wird in den nachfolgenden Sitzungen automatisch bereitgestellt. 

Die Dateifreigabe enthält auch ein 5-GB-Image, das für Sie erstellt wird und mit dem Daten automatisch in Ihrem Verzeichnis `$Home` beibehalten werden. Dies gilt für Bash sowie für PowerShell.

## <a name="use-existing-resources"></a>Verwenden vorhandener Ressourcen

Sie können vorhandene Ressourcen zuordnen, indem Sie die erweiterte Option verwenden. Wenn Sie eine Cloud Shell-Region auswählen, müssen Sie ein unterstützendes Speicherkonto in derselben Region auswählen. Wenn die zugewiesene Region beispielsweise „USA, Westen“ lautet, müssen Sie eine Dateifreigabe zuordnen, die sich ebenfalls in „USA, Westen“ befindet.

Wählen Sie bei der Anzeige mit der Aufforderung zum Einrichten des Speichers die Option **Erweiterte Einstellungen anzeigen**, um weitere Optionen anzuzeigen. Die ausgefüllten Speicheroptionen werden nach Konten mit lokal redundantem Speicher (LRS), georedundantem Speicher (GRS) und zonenredundantem Speicher (ZRS) gefiltert. 

> [!NOTE]
> Für zusätzliche Resilienz wird die Verwendung von GRS- oder ZRS-Speicherkonten für Ihre unterstützende Dateifreigabe empfohlen. Die Art der Redundanz hängt von Ihren Zielen und Preisvorstellungen ab. [Erhalten Sie weitere Informationen zu Replikationsoptionen für Azure Storage-Konten](../storage/common/storage-redundancy.md).

![Einstellung „Ressourcengruppe“](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Schützen des Speicherzugriffs
Aus Sicherheitsgründen sollte jeder Benutzer ein eigenes Speicherkonto bereitstellen.  Für die rollenbasierte Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) benötigen Benutzer zumindest die Berechtigungen eines Mitwirkenden auf Speicherkontoebene.

In Cloud Shell wird eine Azure-Dateifreigabe in einem Speicherkonto innerhalb eines angegebenen Abonnements verwendet. Aufgrund von geerbten Berechtigungen können Benutzer mit ausreichenden Zugriffsrechten für das Abonnement auf alle Speicherkonten und Dateifreigaben zugreifen, die im Abonnement enthalten sind.

Benutzer sollten den Zugriff auf ihre Dateien sperren, indem sie die Berechtigungen auf der Speicherkonto- oder Abonnementebene festlegen.

Das Cloud Shell-Speicherkonto enthält Dateien, die vom Cloud Shell-Benutzer im Basisverzeichnis erstellt werden. Hierbei kann es sich auch um vertrauliche Informationen handeln, z. B. Zugriffstoken oder Anmeldeinformationen.

## <a name="supported-storage-regions"></a>Unterstützte Speicherregionen
Um die aktuelle Region zu suchen, können Sie `env` in Bash ausführen und die Variable `ACC_LOCATION` suchen oder in PowerShell `$env:ACC_LOCATION` ausführen. Dateifreigaben erhalten ein für Sie erstelltes 5-GB-Image zum Beibehalten Ihres Verzeichnisses `$Home`.

Cloud Shell-Computer sind in folgenden Regionen vorhanden:

|Bereich|Region|
|---|---|
|Amerika|USA, Osten; USA, Süden-Mitte; USA, Westen|
|Europa|„Europa, Norden“, „Europa, Westen“|
|Asien-Pazifik|Indien, Mitte; Asien, Südosten|

Kunden sollten eine primäre Region auswählen, es sei denn, es gilt die Anforderung, dass ihre ruhenden Daten in einer bestimmten Region gespeichert werden müssen. Wenn eine solche Anforderung besteht, sollte eine sekundäre Speicherregion verwendet werden.

### <a name="secondary-storage-regions"></a>Sekundäre Speicherregionen
Wenn eine sekundäre Speicherregion verwendet wird, befindet sich das zugehörige Azure Storage-Konto in einer anderen Region als der Cloud Shell-Computer, auf dem Sie es einbinden. Beispielsweise kann Jane festlegen, dass sich ihr Speicherkonto in „Kanada, Osten“ befindet, einer sekundären Region, aber sich der Computer, in den es eingebunden ist, weiterhin in einer primären Region befindet. Ihre ruhenden Daten befinden sich in Kanada, werden jedoch in den USA verarbeitet.

> [!NOTE]
> Wenn eine sekundäre Region verwendet wird, können der Dateizugriff und die Startzeit für Cloud Shell verlangsamt sein.

Ein Benutzer kann `(Get-CloudDrive | Get-AzStorageAccount).Location` in PowerShell ausführen, um den Speicherort seiner Dateifreigabe anzuzeigen.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Beschränken der Ressourcenerstellung mit einer Azure-Ressourcenrichtlinie
Speicherkonten, die Sie in Cloud Shell erstellen, erhalten das Tag `ms-resource-usage:azure-cloud-shell`. Wenn Sie nicht möchten, dass Benutzer Speicherkonten in Cloud Shell erstellen, können Sie eine [Azure-Ressourcenrichtlinie für Tags](../governance/policy/samples/index.md) erstellen, die durch das jeweilige Tag ausgelöst werden.

## <a name="how-cloud-shell-storage-works"></a>Funktionsweise von Cloud Shell-Speicher 
Cloud Shell nutzt die beiden folgenden Methoden zum Beibehalten von Dateien: 
* Es wird ein Datenträgerimage Ihres Verzeichnisses `$Home` erstellt, um alle Inhalte im Verzeichnis beizubehalten. Das Datenträgerimage wird in der von Ihnen angegebenen Dateifreigabe als `acc_<User>.img` unter `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` gespeichert, und die Änderungen werden automatisch synchronisiert. 
* Ihre angegebene Dateifreigabe wird zur direkten Interaktion mit der Freigabe als `clouddrive` in Ihrem Verzeichnis `$Home` eingebunden. `/Home/<User>/clouddrive` wird `fileshare.storage.windows.net/fileshare` zugeordnet.
 
> [!NOTE]
> Alle Dateien in Ihrem Verzeichnis `$Home`, z.B. SSH-Schlüssel, werden dauerhaft in Ihrem Benutzerdatenträgerimage beibehalten, das in der eingebundenen Dateifreigabe gespeichert ist. Nutzen Sie beim dauerhaften Speichern von Informationen in Ihrem Verzeichnis `$Home` und auf der eingebundenen Dateifreigabe die bewährten Methoden.

## <a name="clouddrive-commands"></a>clouddrive-Befehle

### <a name="use-the-clouddrive-command"></a>Verwenden des Befehls `clouddrive`
Mit Cloud Shell können Sie den Befehl `clouddrive` ausführen, mit dem Sie die in Cloud Shell eingebundene Dateifreigabe manuell aktivieren können.

![Ausführen des Befehls „clouddrive“](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Auflisten von `clouddrive`
Führen Sie den `df`-Befehl aus, um zu ermitteln, welche Dateifreigabe als `clouddrive` bereitgestellt wird. 

Der Dateipfad zu „clouddrive“ zeigt den Namen Ihres Speicherkontos und die Dateifreigabe in der URL. Zum Beispiel, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Einbinden eines neuen clouddrive-Verzeichnisses

#### <a name="prerequisites-for-manual-mounting"></a>Voraussetzungen für die manuelle Bereitstellung
Sie können die Dateifreigabe aktualisieren, die Cloud Shell zugeordnet ist, indem Sie den Befehl `clouddrive mount` verwenden.

Wenn Sie eine vorhandene Dateifreigabe einbinden, müssen sich die Speicherkonten in der ausgewählten Cloud Shell-Region befinden. Sie rufen den Standort durch Ausführen von `env` und Überprüfen der `ACC_LOCATION` ab.

#### <a name="the-clouddrive-mount-command"></a>Befehl `clouddrive mount`

> [!NOTE]
> Wenn Sie eine neue Dateifreigabe einbinden, wird für Ihr `$Home`-Verzeichnis ein neues Benutzerimage erstellt. Ihre vorheriges `$Home`-Image wird in Ihrer vorherigen Dateifreigabe gespeichert.

Führen Sie den Befehl `clouddrive mount` mit den folgenden Parametern aus:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Führen Sie `clouddrive mount -h` wie hier gezeigt aus, um weitere Details anzuzeigen:

![Ausführen des Befehls „clouddrive mount“](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>Aufheben der Einbindung von clouddrive
Sie können die Bereitstellung einer Dateifreigabe in Cloud Shell jederzeit aufheben. Cloud Shell erfordert jedoch eine eingebundene Dateifreigabe, daher werden Sie aufgefordert, in der nächsten Sitzung eine andere Dateifreigabe zu erstellen und einzubinden.

1. Führen Sie `clouddrive unmount` aus.
2. Lesen und bestätigen Sie die Eingabeaufforderungen.

Ihre Dateifreigabe ist weiterhin vorhanden, sofern Sie sie nicht manuell löschen. Cloud Shell sucht in nachfolgenden Sitzungen nicht mehr nach dieser Dateifreigabe. Führen Sie `clouddrive unmount -h` wie hier gezeigt aus, um weitere Details anzuzeigen:

![Ausführen des Befehls „clouddrive unmount“](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Durch Ausführung dieses Befehls werden keine Ressourcen gelöscht. Wenn Sie aber eine Ressourcengruppe, ein Speicherkonto oder eine Dateifreigabe mit Zuordnung zu Cloud Shell manuell löschen, werden dadurch sowohl Ihr Datenträgerimage im Verzeichnis `$Home` als auch alle Dateien in Ihrer Dateifreigabe gelöscht. Diese Aktion kann nicht rückgängig gemacht werden.
## <a name="powershell-specific-commands"></a>PowerShell-spezifische Befehle

### <a name="list-clouddrive-azure-file-shares"></a>Auflisten von `clouddrive`-Azure-Dateifreigaben
Das Cmdlet `Get-CloudDrive` ruft Informationen zu den derzeit von `clouddrive` in Cloud Shell eingebundenen Azure-Dateifreigaben ab. <br>
![Get-CloudDrive ausführen](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Aufheben der Bereitstellung von `clouddrive`
Sie können die Einbindung einer Azure-Dateifreigabe in Cloud Shell jederzeit aufheben. Wenn die Azure-Dateifreigabe entfernt wurde, werden Sie in der nächsten Sitzung aufgefordert, eine neue Azure-Dateifreigabe zu erstellen und einzubinden.

Das Cmdlet `Dismount-CloudDrive` hebt die Einbindung einer Azure-Dateifreigabe für das aktuelle Speicherkonto auf. Durch das Aufheben der Einbindung von `clouddrive` wird die aktuelle Sitzung beendet. Der Benutzer wird in der nächsten Sitzung aufgefordert, eine neue Azure-Dateifreigabe zu erstellen und einzubinden.
![Dismount-CloudDrive ausführen](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Hinweis: Wenn Sie eine Funktion in einer Datei definieren und diese über die PowerShell-Cmdlets aufrufen müssen, muss der Punktoperator enthalten sein. Beispiel: . .\MeineFunktionen.ps1

## <a name="next-steps"></a>Nächste Schritte
[Cloud Shell-Schnellstart](quickstart.md) <br>
[Einführung in Microsoft Azure Storage](../storage/files/storage-files-introduction.md) <br>
[Weitere Informationen zu Speichertags](../azure-resource-manager/management/tag-resources.md) <br>
