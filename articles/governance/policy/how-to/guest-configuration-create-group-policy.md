---
title: Erstellen einer Gastkonfigurationsrichtlinie aus einer Gruppenrichtlinie
description: Lernen eine Gruppenrichtlinie in eine Richtliniendefinition zu konvertieren.
ms.date: 03/31/2021
ms.topic: how-to
ms.openlocfilehash: 12bd1c905c254f16da170cde4a4426a2aa0cb263
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772294"
---
# <a name="how-to-create-a-guest-configuration-policy-from-group-policy"></a>Erstellen einer Gastkonfigurationsrichtlinie aus einer Gruppenrichtlinie

Bevor Sie beginnen, sollten Sie die Übersichtsseite zur [Gastkonfiguration](../concepts/guest-configuration.md) und die Details zu den Auswirkungen der Gastkonfigurationsrichtlinien auf die [Konfiguration von Abhilfemaßnahmen für die Gastkonfiguration](../concepts/guest-configuration-policy-effects.md) lesen.

> [!IMPORTANT]
> Konvertieren der Gruppenrichtlinie in die Gastkonfiguration befindet sich **in der Vorschau**. Nicht für alle Arten von Gruppenrichtlinieneinstellungen sind korrespondierende DSC-Ressourcen für PowerShell 7 verfügbar.
>
> Alle Befehle auf dieser Seite müssen in **Windows PowerShell 5.1** ausgeführt werden.
> Die resultierenden MOF-Dateien sollten dann mit dem `GuestConfiguration`Modul in PowerShell 7.1.3 oder höher verpackt werden.
> 
> Benutzerdefinierte Gastkonfigurationsrichtliniendefinitionen, die **AuditlfNotExists** verwenden, sind allgemein verfügbar, aber Definitionen, die **HDeploylfNotExists** mit Gastkonfiguration verwenden, sind **in der Vorschau**.
> 
> Die Gastkonfigurationserweiterung ist für virtuelle Computer in Azure erforderlich. Um die Erweiterung in großem Umfang auf allen Computern bereitzustellen, weisen Sie die folgende Richtlinieninitiative zu: `Deploy prerequisites to enable guest configuration policies on
> virtual machines`
>
> Verwenden Sie keine geheimen oder vertraulichen Informationen in benutzerdefinierten Inhaltspaketen.

Von der DSC-Community wurde das [BaselineManagement-Modul](https://github.com/microsoft/BaselineManagement) veröffentlicht, mit dem exportierte [Gruppenrichtlinien](/support/windows-server/group-policy/group-policy-overview)vorlagen in das PowerShell DSC konvertiert werden können. Zusammen mit dem `GuestConfiguration`Modul können Sie ein Gastkonfigurationspaket für Windows aus exportierten Gruppenrichtlinie erstellen. Das Gastkonfigurationspaket kann dann zur Überprüfung oder Konfiguration von Servern unter Verwendung lokaler Richtlinien verwendet werden, selbst wenn diese nicht in eine Domäne eingebunden sind.

In dieser Anleitung wird der Prozess zur Erstellung eines Azure Policy-Gastkonfigurationspakets aus einem Gruppenrichtlinienobjekt (GPO) erläutert.

## <a name="download-required-powershell-modules"></a>Herunterladen der erforderlichen PowerShell-Module

So installieren Sie alle erforderlichen Module in PowerShell:

```powershell
Install-Module guestconfiguration
Install-Module baselinemanagement
```

Um Gruppenrichtlinienobjekte (GPOs) aus einer Active Directory-Umgebung zu sichern, benötigen Sie die im Remote Server Administration Toolkit (RSAT) verfügbaren PowerShell-Befehle.

So aktivieren Sie RSAT für die Gruppenrichtlinien-Verwaltungskonsole unter Windows 10:

```powerShell
Add-WindowsCapability -Online -Name 'Rsat.GroupPolicy.Management.Tools~~~~0.0.1.0'
Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools~~~~0.0.1.0'
```

## <a name="export-and-convert-group-policy-to-guest-configuration"></a>Exportieren und Konvertieren einer Gruppenrichtlinie in eine Gastkonfiguration

Es gibt drei Möglichkeiten, Gruppenrichtliniendateien zu exportieren und sie in DSC zu konvertieren, um sie in der Gastkonfiguration zu verwenden.

- Exportieren von einem einzelnen Gruppenrichtlinienobjekt
- Exportieren von zusammengeführten Gruppenrichtlinienobjekte für eine OU
- Exportieren Sie die zusammengefassten Gruppenrichtlinienobjekte von einem Computer aus

### <a name="single-group-policy-object"></a>Einzelne Gruppenrichtlinienobjekte

Identifizieren Sie die GUID des zu exportierenden Gruppenrichtlinienobjekts, indem Sie die Befehle im `Group Policy` Modul verwenden. In einer großen Umgebung sollten Sie erwägen, die Ausgabe nach `where-object` zu leiten und nach Namen zu filtern.

Führen Sie jeden der folgenden Schritte in einer **Windows PowerShell 5.1**-Umgebung auf einem Windows-Computer mit **Domänenanschluss** aus:

```powershell
# List all Group Policy Objects
Get-GPO -all
```

Sichern Sie die Gruppenrichtlinie in Dateien. Der Befehl akzeptiert auch einen "Name"-Parameter, aber die Verwendung der GUID der Richtlinie ist weniger fehleranfällig.

```powershell
Backup-GPO -Guid 'f0cf623e-ae29-4768-9bb4-406cce1f3cff' -Path C:\gpobackup\
```

```

The output of the command returns the details of the files.

ConfigurationScript                   Configuration                   Name
-------------------                   -------------                   ----
C:\convertfromgpo\myCustomPolicy1.ps1 C:\convertfromgpo\localhost.mof myCustomPolicy1
```

Überprüfen Sie das exportierte PowerShell-Skript, um sicherzustellen, dass alle Einstellungen ausgefüllt wurden und keine Fehlermeldungen geschrieben wurden. Erstellen Sie mithilfe der MOF-Datei ein neues Konfigurationspaket, indem Sie die Anleitung auf der Seite [Erstellen benutzerdefinierter Gastkonfigurationspaketartefakte](./guest-configuration-create.md) verwenden.
Die Schritte zum Erstellen und Ausprobieren des Gastkonfigurationspakets sollten in einer PowerShell 7-Umgebung ausgeführt werden.

### <a name="merged-group-policy-objects-for-an-ou"></a>Zusammengeführte Gruppenrichtlinienobjekte für eine OU

Exportieren Sie die zusammengefasste Kombination von Gruppenrichtlinienobjekten (ähnlich einem resultierenden Richtliniensatz) für eine bestimmte Organisationseinheit. Der Zusammenführungsvorgang berücksichtigt den Verknüpfungsstatus, die Durchsetzung und den Zugriff, jedoch keine WMI-Filter.

```powershell
Merge-GPOsFromOU -Path C:\mergedfromou\ -OUDistinguishedName 'OU=mySubOU,OU=myOU,DC=mydomain,DC=local' -OutputConfigurationScript
```

Die Ausgabe des Befehls gibt die Details der Dateien zurück.

```powershell
Configuration                                Name    ConfigurationScript
-------------                                ----    -------------------
C:\mergedfromou\mySubOU\output\localhost.mof mySubOU C:\mergedfromou\mySubOU\output\mySubOU.ps1
```

### <a name="merged-group-policy-objects-from-within-a-machine"></a>Zusammengeführte Gruppenrichtlinienobjekte auf einem Computer

Sie können die Richtlinien, die auf einen bestimmten Computer angewendet werden, auch zusammenführen, indem Sie den `Merge-GPOs`Befehl in Windows PowerShell ausführen. WMI-Filter werden nur ausgewertet, wenn Sie die Zusammenführung auf einem Computer vornehmen.

```powershell
Merge-GPOs -OutputConfigurationScript -Path c:\mergedgpo
```

Die Ausgabe des Befehls gibt die Details der Dateien zurück.

```powershell
Configuration              Name                  ConfigurationScript                    PolicyDetails
-------------              ----                  -------------------                    -------------
C:\mergedgpo\localhost.mof MergedGroupPolicy_ws1 C:\mergedgpo\MergedGroupPolicy_ws1.ps1 {@{Name=myEnforcedPolicy; Ap...
```

## <a name="optional-download-sample-group-policy-files-for-testing"></a>OPTIONAL: Herunterladen von Beispielen von Gruppenrichtliniendateien zum Ausprobieren

Wenn Sie nicht soweit sind, Gruppenrichtliniedateien aus einer Active Directory-Umgebung zu exportieren, können Sie die Windows Server-Sicherheitsbaseline aus dem Windows-Sicherheit-Toolkit herunterladen.

Erstellen Sie ein Verzeichnis für die Windows Server 2019-Sicherheitsbaseline, und laden Sie diese aus dem Windows Security Compliance-Toolkit herunter.

```azurepowershell-interactive
# Download the 2019 Baseline files from https://docs.microsoft.com/windows/security/threat-protection/security-compliance-toolkit-10
New-Item -Path 'C:\git\policyfiles\downloads' -Type Directory
Invoke-WebRequest -Uri 'https://download.microsoft.com/download/8/5/C/85C25433-A1B0-4FFA-9429-7E023E7DA8D8/Windows%2010%20Version%201909%20and%20Windows%20Server%20Version%201909%20Security%20Baseline.zip' -Out C:\git\policyfiles\downloads\Server2019Baseline.zip
```

Entsperren und erweitern Sie die heruntergeladene Server 2019-Baseline.

```azurepowershell-interactive
Unblock-File C:\git\policyfiles\downloads\Server2019Baseline.zip
Expand-Archive -Path C:\git\policyfiles\downloads\Server2019Baseline.zip -DestinationPath C:\git\policyfiles\downloads\
```

Überprüfen Sie den Inhalt der Server 2019-Baseline mithilfe von **MapGuidsToGpoNames.ps1**.

```azurepowershell-interactive
# Show content details of downloaded GPOs
C:\git\policyfiles\downloads\Scripts\Tools\MapGuidsToGpoNames.ps1 -rootdir C:\git\policyfiles\downloads\GPOs\ -Verbose
```

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie ein Paketartefakt](./guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](./guest-configuration-create-test.md) aus Ihrer Entwicklungsumgebung.
- [Veröffentlichen Sie das Paketartefakt](./guest-configuration-create-publish.md), damit es für Ihre Computer zugänglich ist.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy Definition](./guest-configuration-create-definition.md) für die skalierungsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie die [Konformitätsdetails für die Zuweisung von Gastkonfigurationsrichtlinien](./determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
