---
title: Übersicht über Azure Cloud Shell | Microsoft-Dokumentation
description: Übersicht über Azure Cloud Shell.
services: ''
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
ms.date: 06/4/2021
ms.author: damaerte
ms.openlocfilehash: f380db2502a34115b071c3aa3e70f18a67d2ca6b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355905"
---
# <a name="overview-of-azure-cloud-shell"></a>Übersicht über Azure Cloud Shell

Azure Cloud Shell ist eine interaktive, authentifizierte, über den Browser zugängliche Shell für die Verwaltung von Azure-Ressourcen. Sie bietet Ihnen die Flexibilität, die Shell-Umgebung auszuwählen, die sich am besten für Ihre Arbeitsweise eignet: Bash oder PowerShell.

Es gibt drei Möglichkeiten, auf Cloud Shell zuzugreifen:

- **Direkter Link:** Öffnen Sie die URL [https://shell.azure.com](https://shell.azure.com) in einem Browser.

- **Azure-Portal**: Klicken Sie im [Azure-Portal](https://portal.azure.com) auf das Symbol „Cloud Shell“:

    ![Symbol zum Starten von Cloud Shell über das Azure-Portal](media/overview/portal-launch-icon.png)

- **Codeausschnitte:** Klicken Sie unter [docs.microsoft.com]() und [Microsoft Learn](/learn/) auf die Schaltfläche **Try It** (Ausprobieren), die in den Codeausschnitten für die Azure CLI und Azure PowerShell angezeigt werden:

    ```azurecli-interactive
    az account show
    ```

    ```azurepowershell-interactive
    Get-AzSubscription
    ```

    Über die Schaltfläche **Try It** (Ausprobieren) wird Cloud Shell direkt einschließlich Dokumentation angezeigt. Dafür wird Bash (bei Codeausschnitten für die Azure CLI) oder PowerShell (bei Codeausschnitten für Azure PowerShell) verwendet.

    Verwenden Sie zum Ausführen des Codes im Codeausschnitt die Option **Kopieren**, drücken Sie **STRG**+**UMSCHALT**+**V** (Windows/Linux) oder **CMD**+**UMSCHALT**+**V** (macOS), um den Befehl einzufügen, und drücken Sie anschließend die **EINGABETASTE**.

## <a name="features"></a>Features

### <a name="browser-based-shell-experience"></a>Browserbasierte Shelloberfläche

Cloud Shell ermöglicht den Zugriff auf eine browserbasierte Befehlszeilenumgebung, die extra auf Azure-Verwaltungsaufgaben ausgerichtet ist. Nutzen Sie Cloud Shell, um unabhängig von einem lokalen Computer in einer Weise zu arbeiten, die Ihnen nur die Cloud bieten kann.

### <a name="choice-of-preferred-shell-experience"></a>Auswahl bevorzugter Shell-Benutzeroberfläche

Benutzer können zwischen Bash und PowerShell wählen.

1. Wählen Sie **Cloud Shell** aus.

    ![Cloud Shell-Symbol](media/overview/overview-cloudshell-icon.png)

2. Wählen Sie **Bash** oder **PowerShell** aus.

    ![Wählen Sie „Bash“ oder „PowerShell“ aus.](media/overview/overview-choices.png)

    Nach dem ersten Start können Sie das Dropdownsteuerelement für den Shelltyp verwenden, um zwischen Bash und PowerShell zu wechseln:

    ![Dropdownsteuerelement zum Auswählen von Bash oder PowerShell](media/overview/select-shell-drop-down.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Authentifizierte und konfigurierte Azure-Arbeitsstation

Cloud Shell wird von Microsoft verwaltet und verfügt somit über gängige Befehlszeilentools und Sprachunterstützung. Darüber hinaus führt Cloud Shell automatisch eine sichere Authentifizierung durch, sodass Sie über die Azure CLI oder über Azure PowerShell-Cmdlets sofort auf Ihre Ressourcen zugreifen können.

Zeigen Sie die vollständige [Liste der in Cloud Shell installierten Tools](features.md#tools) an.

### <a name="integrated-cloud-shell-editor"></a>Integrierter Cloud Shell-Editor

Cloud Shell bietet einen integrierten grafischen Text-Editor, der auf dem Open-Source-Monaco-Editor basiert. Erstellen und bearbeiten Sie Konfigurationsdateien einfach durch Ausführung von `code .` zur nahtlosen Bereitstellung über die Azure CLI oder Azure PowerShell.

[Erfahren Sie mehr über den Cloud Shell-Editor](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Mehrere Zugriffspunkte

Cloud Shell ist ein flexibles Tool mit verschiedenen Zugriffsmöglichkeiten:

* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-Dokumentation](/cli/azure)
* [Azure PowerShell-Dokumentation](/powershell/azure/)
* [Azure Mobile App](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Azure-Kontoerweiterung für Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Herstellen einer Verbindung mit dem Microsoft Azure Files-Speicher

Cloud Shell-Computer sind temporär, aber Ihre Dateien bleiben auf zwei Arten erhalten: über ein Datenträgerimage und über eine eingebundene Dateifreigabe mit dem Namen `clouddrive`. Beim ersten Start werden Sie von Cloud Shell darauf hingewiesen, dass für Sie eine Ressourcengruppe, ein Speicherkonto und eine Azure Files-Freigabe erstellt werden. Dieser Schritt ist nur einmal erforderlich. Diese Komponenten werden für alle Sitzungen automatisch angefügt. Es kann eine einzelne Dateifreigabe zugeordnet werden, die sowohl von Bash als auch von PowerShell in Cloud-Shell verwendet wird.

Erfahren Sie mehr darüber, wie Sie ein [neues oder vorhandenes Speicherkonto](persisting-shell-storage.md) einbinden oder welche [Persistenzmechanismen in Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works) verwendet werden.

> [!NOTE]
> Die Azure Storage-Firewall wird für Cloud Shell-Speicherkonten nicht unterstützt.

## <a name="concepts"></a>Konzepte

* Cloud Shell wird auf einem temporären Host ausgeführt, der pro Sitzung und pro Benutzer bereitgestellt wird.
* Nach 20 Minuten ohne interaktive Aktivitäten tritt für Cloud Shell ein Timeout ein.
* Für Cloud Shell muss eine Azure-Dateifreigabe bereitgestellt werden.
* Cloud Shell verwendet dieselbe Azure-Dateifreigabe für Bash und PowerShell.
* Cloud Shell wird ein Computer pro Benutzerkonto zugewiesen.
* Cloud Shell stellt $HOME dauerhaft unter Verwendung eines 5-GB-Images bereit, das sich in Ihrer Dateifreigabe befindet.
* Berechtigungen werden auf einen normalen Linux-Benutzer in Bash festgelegt.

Weitere Informationen erhalten Sie unter [Bash in Cloud Shell](features.md) und [PowerShell in Cloud Shell](./features.md).

## <a name="compliance"></a>Compliance
### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten
Die gesamte Cloud Shell-Infrastruktur ist standardmäßig mit der Mehrfachverschlüsselung ruhender Daten kompatibel. Auf Benutzerseite ist keine Aktion erforderlich.

## <a name="pricing"></a>Preise

Der Computer, auf dem Cloud Shell gehostet wird, ist kostenlos. Als Voraussetzung muss eine Azure Files-Freigabe eingebunden sein. Es gelten die üblichen Speicherkosten.

## <a name="next-steps"></a>Nächste Schritte

[Schnellstart für Bash in Azure Cloud Shell](quickstart.md) <br>
[Schnellstart für PowerShell in Cloud Shell](quickstart-powershell.md)
