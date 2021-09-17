---
title: 'Gewusst wie: Installieren des Moduls zum Erstellen der Gastkonfiguration'
description: Erfahren Sie, wie Sie das PowerShell-Modul zum Erstellen und Testen von Definitionen und Zuweisungen von Gastkonfigurationsrichtlinien installieren.
ms.date: 07/22/2021
ms.topic: how-to
ms.openlocfilehash: c32c405cffb71527e61b68f7fb532487d695743f
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772999"
---
# <a name="how-to-setup-a-guest-configuration-authoring-environment"></a>Gewusst wie: Einrichten einer Erstellungsumgebung für Gastkonfigurationen

Mit dem PowerShell-Modul `GuestConfiguration` wird die Erstellung von benutzerdefinierten Inhalten automatisiert, z. B.:

- Erstellen eines Inhaltsartefakts für die Gastkonfiguration (ZIP-Datei)
- Überprüfen, ob das Paket die Anforderungen erfüllt
- Lokales Installieren des Gastkonfigurations-Agents zu Testzwecken
- Überprüfen, ob das Paket verwendet werden kann, um Einstellungen auf einem Computer zu überwachen
- Überprüfen, ob das Paket verwendet werden kann, um Einstellungen auf einem Computer zu konfigurieren
- Veröffentlichen des Pakets in Azure Storage
- Erstellen einer Richtliniendefinition
- Veröffentlichen der Richtlinie

Die Unterstützung für das Anwenden von Konfigurationen über die Gastkonfiguration wurde in Version `3.4.2` eingeführt.

> [!IMPORTANT]
> Benutzerdefinierte Pakete, die den Zustand einer Umgebung überwachen, sind allgemein verfügbar. Pakete, die Konfigurationen anwenden, befinden sich hingegen **in der Vorschau**. **Es gelten die folgenden Einschränkungen:**
> 
> Zum Testen der Erstellung und Anwendung von Konfigurationen unter Linux ist das Modul `GuestConfiguration` nur unter Ubuntu 18 verfügbar, aber das vom Modul erstellte Paket und die Richtliniendefinitionen können für jede Linux-Distribution/-Version genutzt werden, die in Azure oder Arc unterstützt wird.
>
> Die Option zum Testen von Paketen unter macOS ist nicht verfügbar.

### <a name="base-requirements"></a>Basisanforderungen

Betriebssysteme, unter denen das Modul installiert werden kann:

- Ubuntu 18
- Windows

Das Modul kann auf einem Computer installiert werden, auf dem PowerShell 7 ausgeführt wird. Installieren Sie die unten aufgeführten Versionen von PowerShell.

| OS | PowerShell-Version |
|-|-|
|Windows|[PowerShell 7.1.3](https://github.com/PowerShell/PowerShell/releases/tag/v7.1.3)|
|Ubuntu 18|[PowerShell 7.2.0-preview.6](https://github.com/PowerShell/PowerShell/releases/tag/v7.2.0-preview.6)|

Das `GuestConfiguration`-Modul benötigt die folgende Software:

- Azure PowerShell 5.9.0 oder höher. Die erforderlichen Az-Module werden automatisch mit dem `GuestConfiguration`-Modul installiert, oder Sie können [diese Anweisungen](/powershell/azure/install-az-ps) befolgen.
  - Nur die Az-Module „Az.Accounts“, „Az.Resources“ und „Az.Storage“ sind erforderlich.
- `PSDesiredStateConfiguration`-Modul.

### <a name="install-the-module-from-the-powershell-gallery"></a>Installieren des Moduls aus dem PowerShell-Katalog

Führen Sie den folgenden Befehl in PowerShell 7 aus, um das `GuestConfiguration`-Modul unter Windows oder Linux zu installieren.

```powershell
# Install the guest configuration DSC resource module from PowerShell Gallery
Install-Module -Name GuestConfiguration
```

Überprüfen Sie, ob das Modul importiert wurde:

```powershell
# Get a list of commands for the imported GuestConfiguration module
Get-Command -Module 'GuestConfiguration'
```

## <a name="update-and-import-the-psdesiredstateconfiguration-module-on-linux"></a>Aktualisieren und Importieren des PSDesiredStateConfiguration-Moduls unter Linux

Ab PowerShell 7.2 Preview 6 wird DSC unabhängig von PowerShell als Modul im PowerShell-Katalog veröffentlicht. Führen Sie den folgenden Befehl aus, um DSC Version 3 in Ihrer PowerShell-Umgebung unter Linux zu installieren.

```powershell
# Install the DSC module before compiling using the Configuration keyword
Install-Module PSDesiredStateConfiguration -AllowPreRelease -Force
```

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie ein Paketartefakt](./guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](./guest-configuration-create-test.md) in Ihrer Entwicklungsumgebung.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy-Definition](./guest-configuration-create-definition.md) für die Verwaltung Ihrer Umgebung im großen Stil.
- [Weisen Sie Ihre benutzerdefinierten Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Informieren Sie sich, wie Sie die [Compliancedetails für die Richtlinienzuweisungen der Gastkonfiguration](./determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
