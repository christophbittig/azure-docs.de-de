---
title: Richten Sie die Bicep-Entwicklungs- und Einsatzumgebungen ein
description: Konfigurieren von Bicep-Entwicklungs- und -Bereitstellungsumgebungen
ms.topic: conceptual
ms.date: 10/20/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 7f590c0d0954ca4e3ccc3f4d894f55892b01a6ae
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443507"
---
# <a name="install-bicep-tools"></a>Installieren von Bicep-Tools

Stellen Sie sicher, dass Ihre Umgebung für die Entwicklung und Bereitstellung von Bicep-Dateien eingerichtet ist.

## <a name="vs-code-and-bicep-extension"></a>VS Code und Bicep-Erweiterung

Zum Erstellen von Bicep-Dateien benötigen Sie einen guten Bicep-Editor. Es wird Folgendes empfohlen:

- **Visual Studio Code:** Wenn Sie Visual Studio Code noch nicht installiert haben, holen Sie die [Installation](https://code.visualstudio.com/) jetzt nach.
- **Bicep-Erweiterung für Visual Studio Code:**  Visual Studio Code mit der Bicep-Erweiterung bietet Sprachunterstützung und automatische Vervollständigung für Ressourcen. Mit der Erweiterung können Sie Bicep-Dateien erstellen und überprüfen.

  Suchen Sie zum Installieren der Erweiterung auf der Registerkarte **Erweiterungen** oder im [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) nach *bicep*.

  Wählen Sie **Installieren** aus.

  :::image type="content" source="./media/install/install-extension.png" alt-text="Installieren der Bicep-Erweiterung":::

Um zu überprüfen, ob Sie die Erweiterung installiert haben, öffnen Sie eine beliebige Datei mit der Dateierweiterung `.bicep`. In der unteren rechten Ecke sollte der Sprachmodus in **Bicep** geändert werden.

:::image type="content" source="./media/install/language-mode.png" alt-text="Bicep-Sprachmodus":::

### <a name="troubleshoot"></a>Problembehandlung

Wenn Sie die Bicep-Erweiterung für Visual Studio Code installieren, werden möglicherweise die folgenden Fehlermeldungen angezeigt:

```error
Failed to install .NET runtime v5.0
```

```error
Failed to download .NET 5.0.x ....... Error!
```

Um das Problem zu beheben, können Sie .NET manuell über die [.NET-Website](https://aka.ms/dotnet-core-download)installieren und dann Visual Studio Code konfigurieren, um eine vorhandene Installation von .NET wiederzuverwenden. mit den folgenden Einstellungen:

**Windows**

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "C:\\Program Files\\dotnet\\dotnet.exe"
  }
]

```

**macOS**

```json
"dotnetAcquisitionExtension.existingDotnetPath": [
  {
    "extensionId": "ms-azuretools.vscode-bicep",
    "path": "/usr/local/share/dotnet/dotnet"
  }
]
```

Informationen zum Konfigurieren der Visual Studio Code-Einstellungen finden Sie unter [Benutzer- und Workspace-Einstellungen](https://code.visualstudio.com/docs/getstarted/settings).

## <a name="deployment-environment"></a>Bereitstellungsumgebung

Nachdem Sie Ihre Entwicklungsumgebung eingerichtet haben, müssen Sie die Tools für Ihre Bereitstellungsumgebung installieren. Installieren Sie zum Einrichten einer lokalen Bereitstellungsumgebung die Bicep-CLI mit der [Azure CLI](#azure-cli) oder mit [Azure PowerShell](#azure-powershell). Diese Schritte werden in den nächsten Abschnitten beschrieben.

Informationen zum Bereitstellen von Bicep-Dateien aus Azure Pipelines finden Sie unter [Integrieren von Bicep in Azure Pipelines](add-template-to-azure-pipelines.md). Informationen zur Bereitstellung von Bicep-Dateien über GitHub Actions finden Sie unter [Bereitstellen von Bicep-Dateien mithilfe von GitHub Actions](deploy-github-actions.md).

## <a name="azure-cli"></a>Azure CLI

Sie müssen **mindestens Version 2.20.0** der Azure CLI installiert haben. Informationen zum Installieren oder Aktualisieren der Azure-Befehlszeilenschnittstelle:

- [Installieren der Azure CLI unter Windows](/cli/azure/install-azure-cli-windows)
- [Installieren der Azure CLI unter Linux](/cli/azure/install-azure-cli-linux)
- [Installieren der Azure CLI unter macOS](/cli/azure/install-azure-cli-macos)

Führen Sie zum Überprüfen Ihrer aktuellen Version Folgendes aus:

```azurecli
az --version
```

Sie verfügen nun über alles, was Sie zum [Bereitstellen](deploy-cli.md) und [Dekompilieren](decompile.md) von Bicep-Dateien benötigen. Sie verfügen über alle erforderlichen Komponenten, da von der Azure CLI automatisch die Bicep-Befehlszeilenschnittstelle installiert wird, wenn diese für die Ausführung eines Befehls benötigt wird.

Verwenden Sie zum manuellen Starten der Installation der Bicep-Befehlszeilenschnittstelle Folgendes:

```azurecli
az bicep install
```

Führen Sie zum Aktualisieren auf die aktuelle Version den folgenden Befehl aus:

```azurecli
az bicep upgrade
```

Verwenden Sie Folgendes, um die Installation zu überprüfen:

```azurecli
az bicep version
```

Weitere Befehle finden Sie unter [Befehle der Bicep-Befehlszeilenschnittstelle](bicep-cli.md).

> [!IMPORTANT]
> Die Azure CLI installiert eine eigenständige Instanz der Bicep-Befehlszeilenschnittstelle. Bei dieser Instanz tritt kein Konflikt mit Versionen auf, die Sie möglicherweise manuell installiert haben. Von der Azure CLI wird Ihrem Pfad (PATH) keine Bicep-Befehlszeilenschnittstelle hinzugefügt.

## <a name="azure-powershell"></a>Azure PowerShell

Sie müssen **mindestens Azure PowerShell-Version 5.6.0** installiert haben. Informationen zur Aktualisierung oder Installation finden Sie unter [Installieren des Azure Az PowerShell-Moduls](/powershell/azure/install-az-ps).

Von Azure PowerShell wird die Bicep-Befehlszeilenschnittstelle nicht automatisch installiert. Stattdessen müssen Sie die [Bicep-Befehlszeilenschnittstelle manuell installieren](#install-manually).

> [!IMPORTANT]
> Die von der Azure CLI installierte eigenständige Instanz der Bicep-Befehlszeilenschnittstelle ist für PowerShell-Befehle nicht verfügbar. Azure PowerShell-Bereitstellungen sind nicht erfolgreich, wenn Sie die Bicep-Befehlszeilenschnittstelle nicht manuell installiert haben.

Wenn Sie die Bicep-Befehlszeilenschnittstelle manuell installieren, führen Sie die Bicep-Befehle mit der `bicep`-Syntax anstelle der `az bicep`-Syntax für die Azure CLI aus.

Führen Sie zum Überprüfen der Version der Bicep-Befehlszeilenschnittstelle Folgendes aus:

```cmd
bicep --version
```

## <a name="install-manually"></a>Manuelle Installation

Mit den folgenden Methoden wird die Bicep-Befehlszeilenschnittstelle installiert und Ihrem Pfad hinzugefügt. Für eine andere Verwendung als mit der Azure CLI ist eine manuelle Installation erforderlich.

- [Linux](#linux)
- [macOS](#macos)
- [Windows](#windows)

### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!
```

> [!NOTE]
> Verwenden Sie für einfache Linux-Distributionen wie [Alpine](https://alpinelinux.org/) im vorherigen Skript **bicep-linux-musl-x64** anstelle von **bicep-linux-x64**.

### <a name="macos"></a>macOS

#### <a name="via-homebrew"></a>Über Homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep

# Install the tool
brew install bicep
```

#### <a name="via-bash"></a>Über BASH

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

### <a name="windows"></a>Windows

#### <a name="windows-installer"></a>Windows Installer

Laden Sie das [neueste Windows-Installationsprogramm](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe) herunter, und führen Sie es aus. Für das Installationsprogramm sind keine Administratorrechte erforderlich. Nach der Installation wird die Bicep-Befehlszeilenschnittstelle dem Benutzerpfad hinzugefügt. Schließen Sie alle geöffneten Befehlsshellfenster, und öffnen Sie sie erneut, damit die Änderung am Pfad wirksam wird.

#### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

#### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

#### <a name="manual-with-powershell"></a>Manuell mit PowerShell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-on-air-gapped-cloud"></a>Installieren in einer Cloud mit Air Gap

Zum Installieren der Bicep-CLI in einer Umgebung mit Air Gap müssen Sie die ausführbare Datei für die Bicep-CLI manuell herunterladen und an einem bestimmten Speicherort speichern.

- **Linux**

    1. Laden Sie **bicep-linux-x64** von der [Bicep-Releaseseite](https://github.com/Azure/bicep/releases/latest/) in eine Umgebung ohne Air Gap herunter.
    1. Kopieren Sie die ausführbare Datei auf einem Computer mit Air Gap in das Verzeichnis **$HOME/.azure/bin**.

- **macOS**

    1. Laden Sie **bicep-osx-x64** von der [Bicep-Releaseseite](https://github.com/Azure/bicep/releases/latest/) in eine Umgebung ohne Air Gap herunter.
    1. Kopieren Sie die ausführbare Datei auf einem Computer mit Air Gap in das Verzeichnis **$HOME/.azure/bin**.

- **Windows**

    1. Laden Sie **bicep-win-x64.exe** von der [Bicep-Releaseseite](https://github.com/Azure/bicep/releases/latest/) in eine Umgebung ohne Air Gap herunter.
    1. Kopieren Sie die ausführbare Datei auf einem Computer mit Air Gap in das Verzeichnis **%UserProfile%/.azure/bin**.

Beachten Sie, dass die Befehle `bicep install` und `bicep upgrade` in einer Umgebung mit Air Gap nicht funktionieren.

## <a name="install-the-nightly-builds"></a>Installieren der nächtlichen Builds

Wenn Sie die neuesten Vorabversionen von Bicep testen möchten, bevor sie veröffentlicht werden, finden Sie unter [Installieren von nächtlichen Builds](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md) weitere Informationen.

> [!WARNING]
> Diese Vorabversionen weisen sehr wahrscheinlich bekannte oder unbekannte Fehler auf.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Verwendung von Visual Studio Code und der BICEP-Erweiterung finden Sie unter [Schnellstart: Erstellen von BICEP-Dateien mit Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
