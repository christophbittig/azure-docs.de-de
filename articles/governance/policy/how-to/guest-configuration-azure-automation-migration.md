---
title: Planen der Migration von Azure Automation State Configuration zu einer Gastkonfigurationen
description: Dieser Artikel enthält prozessbezogene und technische Anleitungen für Kunden, die von DSC Version 2 in Azure Automation zu Version 3 in Azure Policy wechseln möchten.
ms.date: 07/1/2021
ms.topic: how-to
ms.openlocfilehash: ae387e59abe5cbff335b43cdd78030f32f6e6f8c
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122773001"
---
# <a name="azure-automation-state-configuration-to-guest-configuration-migration-planning"></a>Planen der Migration von Azure Automation State Configuration zu einer Gastkonfiguration

Die Gastkonfiguration ist die neueste Implementierung der Funktionalität, die von Azure Automation State Configuration (auch als Azure Automation Desired State Configuration oder AADSC bezeichnet) bereitgestellt wurde.
Wenn möglich, sollten Sie planen, Ihre Inhalte und Computer in den neuen Dienst zu verschieben.
Dieser Artikel enthält Anleitungen zum Entwickeln einer Migrationsstrategie von Azure Automation zur Gastkonfiguration.

Neue Funktionen in der Gastkonfigurationsadresse: Die wichtigsten Fragen von Kunden:

- Höhere Größenbeschränkung für Konfigurationen ( 100 MB )
- Erweiterte Berichterstellung über Azure Resource Graph einschließlich Ressourcen-ID und Status
- Verwalten mehrerer Konfigurationen für denselben Computer
- Wenn Computer vom gewünschten Zustand abweichen, steuern Sie, wann die Wiederherstellung erfolgt
- Linux und Windows nutzen beide PowerShell-basierte DSC-Ressourcen

Bevor Sie beginnen empfiehlt es sich, die allgemeinen Informationen zur [Azure Policy-Gastkonfiguration](../concepts/guest-configuration.md) zu lesen.

## <a name="understand-migration"></a>Grundlegendes zur Migration

Der beste Ansatz für die Migration ist die erneute Bereitstellung von Inhalten und dann die Migration von Computern. Die erwarteten Schritte für die Migration werden beschrieben.

- Exportieren von Konfigurationen aus Azure Automation
- Ermitteln von Modulanforderungen und Laden in Ihrer Umgebung
- Kompilieren von Konfigurationen
- Erstellen und Veröffentlichen von Paketen für Gastkonfigurationen
- Testen von Paketen für Gastkonfigurationen
- Integrieren von Hybridcomputern in Azure Arc
- Aufheben der Registrierung von Servern bei Azure Automation State Configuration
- Zuweisen von Konfigurationen zu Servern mithilfe der Gastkonfiguration

Die Gastkonfiguration verwendet DSC Version 3 mit PowerShell Version 7. Die DSC Version 3 kann gemeinsam mit älteren DSC-Versionen in [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) und [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted) verwendet werden.
Die Implementierungen sind getrennt. Es gibt jedoch keine Konflikterkennung.

Die Gastkonfiguration erfordert nicht die Veröffentlichung von Modulen oder Konfigurationen in einem Dienst oder die Kompilierung in einem Dienst. Stattdessen werden Inhalte mithilfe von speziell entwickelten Tools entwickelt und getestet und überall dort veröffentlicht, wo der Computer über HTTPS erreichbar ist (in der Regel Azure Blob Storage).

Wenn Sie sich dafür entscheiden, dass der richtige Plan für Ihre Migration darin besteht, Maschinen für eine gewisse Zeit in beiden Diensten zu betreiben, kann das zwar verwirrend sein, aber es gibt keine technischen Hindernisse. Die beiden Optionen sind unabhängig voneinander.

## <a name="export-content-from-azure-automation"></a>Exportieren von Inhalten aus Azure Automation

Beginnen Sie mit dem Ermitteln und Exportieren von Inhalten aus Azure Automation State Configuration in eine Entwicklungsumgebung, in der Sie Inhaltspakete für die Gastkonfiguration erstellen, testen und veröffentlichen.

### <a name="configurations"></a>Konfigurationen

Nur Konfigurationsskripts können aus Azure Automation exportiert werden. Es ist nicht möglich, „Knotenkonfigurationen“ oder kompilierte MOF-Dateien zu exportieren.
Wenn Sie MOF-Dateien direkt im Automation-Konto veröffentlicht haben und keinen Zugriff mehr auf die ursprüngliche Datei haben, müssen Sie aus Ihren privaten Konfigurationsskripts neu kompilieren oder die Konfiguration möglicherweise neu erstellen, wenn das Original nicht gefunden werden kann.

Um Konfigurationsskripts aus Azure Automation zu exportieren, identifizieren Sie zunächst das Azure Automation Konto, das die Konfigurationen und den Namen der Ressourcengruppe enthält, in der das Automation-Konto bereitgestellt wird.

Installieren Sie das PowerShell-Modul „Az.Automation“.

```powershell
Install-Module Az.Automation
```

Verwenden Sie als Nächstes den Befehl „Get-AzAutomationAccount“, um Ihre Automation-Konten und die Ressourcengruppe zu identifizieren, in der sie bereitgestellt werden.
Die Eigenschaften „ResourceGroupName“ und „AutomationAccountName“ sind für die nächsten Schritte wichtig.

```powershell
Get-AzAutomationAccount

SubscriptionId        : <your subscription id>
ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 :
Plan                  :
CreationTime          : 6/30/2021 11:56:17 AM -05:00
LastModifiedTime      : 6/30/2021 11:56:17 AM -05:00
LastModifiedBy        :
Tags                  : {}
```

Ermitteln Sie die Konfigurationen in Ihrem Automation-Konto. Die Ausgabe enthält einen Eintrag pro Konfiguration. Wenn Sie viele Einträge haben, speichern Sie die Informationen als Variable, damit sie einfacher zu verwenden sind.

```powershell
Get-AzAutomationDscConfiguration -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name>

ResourceGroupName     : <your resource group name>
AutomationAccountName : <your automation account name>
Location              : centralus
State                 : Published
Name                  : <your configuration name>
Tags                  : {}
CreationTime          : 6/30/2021 12:18:26 PM -05:00
LastModifiedTime      : 6/30/2021 12:18:26 PM -05:00
Description           :
Parameters            : {}
LogVerbose            : False
```

Exportieren Sie abschließend jede Konfiguration mit dem Befehl „Export-AzAutomationDscConfiguration“ in eine lokale Skriptdatei. Der resultierende Dateiname verwendet das Muster `\ConfigurationName.ps1`.

```powershell
Export-AzAutomationDscConfiguration -OutputFolder /<location on your machine> -ResourceGroupName <your resource group name> -AutomationAccountName <your automation account name> -name <your configuration name>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
```

#### <a name="export-configurations-using-the-powershell-pipeline"></a>Exportieren von Konfigurationen mithilfe der PowerShell-Pipeline

Nachdem Sie Ihre Konten und die Anzahl der Konfigurationen ermittelt haben, möchten Sie möglicherweise alle Konfigurationen in einen lokalen Ordner auf Ihrem Computer exportieren.
Um diesen Prozess zu automatisieren, leiten Sie die Ausgabe jedes der obigen Befehle an den nächsten weiter.

Im Beispiel werden fünf Konfigurationen exportiert. Das Ausgabemuster ist der einzige Hinweis auf den Erfolg.

```powershell
Get-AzAutomationAccount | Get-AzAutomationDscConfiguration | Export-AzAutomationDSCConfiguration -OutputFolder /<location on your machine>

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
                                               12/31/1600 18:09
```

#### <a name="consider-decomposing-complex-configuration-files"></a>Erwägen Sie, komplexe Konfigurationsdateien zu zerlegen.

Die Gastkonfiguration kann mehrere Konfigurationen pro Computer verwalten.
Bei vielen Konfigurationen, die für Azure Automation State Configuration geschrieben wurden, wurde die Einschränkung der Verwaltung einer einzelnen Konfiguration pro Computer angenommen. Um die erweiterten Funktionen der Gastkonfiguration zu nutzen, können große Konfigurationsdateien in viele kleinere Konfigurationen unterteilt werden, in denen jede ein bestimmtes Szenario verarbeitet.

Es gibt keine Orchestrierung in der Gastkonfiguration, um die Reihenfolge der Sortierung von Konfigurationen zu steuern. Führen Sie daher die Schritte in einer Konfiguration in einem Paket nacheinander aus, wenn sie sequenziell ausgeführt werden müssen.

### <a name="modules"></a>Module

Es ist nicht möglich, Module aus Azure Automation zu exportieren oder automatisch zu korrelieren, welche Konfigurationen welches Modul/welche Version benötigen. Sie benötigen die Module in Ihrer lokalen Umgebung, um ein neues Gastkonfigurationspaket zu erstellen. Um eine Liste der Module zu erstellen, die Sie für die Migration benötigen, verwenden Sie PowerShell, um Azure Automation nach dem Namen und der Version der Module abzufragen.

Wenn Sie Module verwenden, die benutzerdefiniert erstellt wurden und nur in Ihrer privaten Entwicklungsumgebung vorhanden sind, ist es nicht möglich, sie aus Azure Automation zu exportieren.

Wenn ein benutzerdefiniertes Modul für eine Konfiguration erforderlich ist und sich im Konto befindet, Sie es jedoch nicht in Ihrer Umgebung finden können, können Sie die Konfiguration nicht kompilieren, was bedeutet, dass Sie die Konfiguration nicht migrieren können.

#### <a name="list-modules-imported-in-azure-automation"></a>Auflisten der in Azure Automation importierten Module

Verwenden Sie den Befehl `Get-AzAutomationModule`, um eine Liste aller Module abzurufen, die in Ihrem Automation-Konto installiert sind. Die Eigenschaft „IsGlobal“ gibt an, ob das Modul für Azure Automation immer integriert ist oder ob es im Konto veröffentlicht wurde.

Beispielsweise, um eine Liste aller Module zu erstellen, die in einem Ihrer Konten veröffentlicht wurden.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false
```

Sie können auch die PowerShell-Katalog verwenden, um Informationen zu Modulen zu finden, die öffentlich verfügbar sind. Beispielsweise wird die Liste der Module, die in neue Automation-Konten integriert sind und DSC-Ressourcen enthalten, im folgenden Beispiel erstellt.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $true | Find-Module -erroraction silentlycontinue | ? {'' -ne $_.Includes.DscResource} | Select Name, Version -Unique | format-table -AutoSize

Name                       Version
----                       -------
AuditPolicyDsc             1.4.0
ComputerManagementDsc      8.4.0
PSDscResources             2.12.0
SecurityPolicyDsc          2.10.0
xDSCDomainjoin             1.2.23
xPowerShellExecutionPolicy 3.1.0.0
xRemoteDesktopAdmin        1.1.0.0
```

#### <a name="download-modules-from-powershell-gallery-or-powershellget-repository"></a>Herunterladen von Modulen aus PowerShell-Katalog oder PowerShellGet-Repository

Wenn die Module aus dem PowerShell-Katalog importiert wurden, können Sie die Ausgabe von `Find-Module` direkt in `Install-Module` weiterleiten. Das befehlsübergreifende Piping der Ausgabe bietet eine Lösung zum Laden einer Entwicklerumgebung mit allen Modulen, die sich derzeit in einem Automation-Konto befinden und im PowerShell-Katalog öffentlich verfügbar sind.

Der gleiche Ansatz kann verwendet werden, um Module aus einem benutzerdefinierten NuGet-Feed zu pullen, wenn der Feed in Ihrer lokalen Umgebung als [PowerShellGet-Repository](/powershell/scripting/gallery/how-to/working-with-local-psrepositories) registriert ist.

Der Befehl `Find-Module` im Beispiel unterdrückt keine Fehler, d. h., alle Module, die nicht im Katalog gefunden wurden, geben eine Fehlermeldung zurück.

```powershell
Get-AzAutomationAccount | Get-AzAutomationModule | ? IsGlobal -eq $false | Find-Module | ? {'' -ne $_.Includes.DscResource} | Install-Module

  Installing package xWebAdministration'

    [                                                                                        ]
```

#### <a name="inspecting-configuration-scripts-for-module-requirements"></a>Überprüfen von Konfigurationsskripts auf Modulanforderungen

Wenn Sie Konfigurationsskripts aus Azure Automation exportiert haben, können Sie auch den Inhalt überprüfen, um Details darüber zu erhalten, welche Module zum Kompilieren der einzelnen Konfigurationen in eine MOF-Datei erforderlich sind. Dieser Ansatz ist nur erforderlich, wenn Sie Konfigurationen in Ihren Automation-Konten finden, in denen die Module entfernt wurden.
Die Konfigurationen sind für Computer nicht mehr nützlich, befinden sich aber möglicherweise noch im Konto.

Suchen Sie oben in jeder Datei nach einer Zeile, die „Import-DscResource“ enthält.
Dieser Befehl gilt nur innerhalb einer Konfiguration und wird zum Laden von Modulen zum Zeitpunkt der Kompilierung verwendet.

Die Konfiguration „WindowsIISServerConfig“ im PowerShell-Katalog enthält beispielsweise die Zeilen in diesem Beispiel.

```powershell
configuration WindowsIISServerConfig
{

Import-DscResource -ModuleName @{ModuleName = 'xWebAdministration';ModuleVersion = '1.19.0.0'}
Import-DscResource -ModuleName 'PSDesiredStateConfiguration'
```

Für die Konfiguration müssen Sie über die Modulversion „xWebAdministration“ „1.19.0.0“ und das Modul „PSDesiredStateConfiguration“ verfügen.

### <a name="test-content-in-azure-guest-configuration"></a>Testen von Inhalten in der Azure-Gastkonfiguration

Die beste Möglichkeit, um zu bewerten, ob Ihre Inhalte aus Azure Automation State Configuration mit der Gastkonfiguration verwendet werden können, besteht darin, das Schritt-für-Schritt-Tutorial auf der Seite [Erstellen von benutzerdefinierten Gastkonfigurationspaketartefakten zu befolgen](./guest-configuration-create.md).

Wenn Sie den Schritt [Konfiguration erstellen](./guest-configuration-create.md#author-a-configuration) erreichen, sollte das Konfigurationsskript, das eine MOF-Datei generiert, eines der Skripts sein, die Sie aus Azure Automation State Configuration exportiert haben. Sie müssen die erforderlichen PowerShell-Module in Ihrer Umgebung installiert haben, bevor Sie die Konfiguration in eine MOF-Datei kompilieren und ein Gastkonfigurationspaket erstellen können.

#### <a name="what-if-a-module-does-not-work-with-guest-configuration"></a>Was geschieht, wenn ein Modul nicht mit der Gastkonfiguration funktioniert?

Bei einigen Modulen können Kompatibilitätsprobleme mit der Gastkonfiguration auftreten. Die häufigsten Probleme beziehen sich auf .NET Framework und .NET Core. Ausführliche technische Informationen finden Sie auf der Seite [Unterschiede zwischen Windows PowerShell 7 und PowerShell (Core) 7.x](/powershell/scripting/whats-new/differences-from-windows-powershell)

Eine Möglichkeit zum Beheben von Kompatibilitätsproblemen ist das Ausführen von Befehlen in Windows PowerShell aus einem Modul, das in PowerShell 7 importiert wird, indem `powershell.exe` ausgeführt wird.
Sie können ein Beispielmodul überprüfen, das dieses Verfahren im Azure-Policy Repository verwendet, in dem es zum Überwachen des Zustands [von Windows DSC-Konfiguration](https://github.com/Azure/azure-policy/blob/bbfc60104c2c5b7fa6dd5b784b5d4713ddd55218/samples/GuestConfiguration/package-samples/resource-modules/WindowsDscConfiguration/DscResources/WindowsDscConfiguration/WindowsDscConfiguration.psm1#L97) verwendet wird.

Das Beispiel veranschaulicht auch einen kleinen Proof of Concept.

```powershell
# example function that could be loaded from module
function New-TaskResolvedInPWSH7 {
  # runs the fictitious command 'Get-myNotCompatibleCommand' in Windows PowerShell
  $compatObject = & powershell.exe -noprofile -NonInteractive -command { Get-myNotCompatibleCommand }
  # resulting object can be used in PowerShell 7
  return $compatObject
}
```

#### <a name="will-i-have-to-add-reasons-property-to-get-targetresource-in-all-modules-i-migrate"></a>Muss ich Get-TargetResource in allen Modulen, die ich migrieren möchte, die Eigenschaft „Reasons“ hinzufügen?

Die Implementierung der [Eigenschaft „Reasons"](../concepts/guest-configuration-custom.md#special-requirements-for-get) bietet eine bessere Erfahrung beim Anzeigen der Ergebnisse einer Konfigurationszuweisung im Azure-Portal. Wenn die Methode `Get` in einem Modul nicht „Reasons“ enthält, wird die generische Ausgabe mit Details aus den Eigenschaften zurückgegeben, die von der Methode `Get` zurückgegeben werden. Daher ist es für die Migration optional.

## <a name="machines"></a>Machines

Nachdem Sie die Tests von Inhalten aus Azure Automation State Configuration in der Gastkonfiguration abgeschlossen haben, entwickeln Sie einen Plan für die Migration von Computern.

Azure Automation State Configuration ist sowohl für virtuelle Computer in Azure als auch für Hybridcomputer außerhalb von Azure verfügbar. Sie müssen jedes dieser Szenarien mit unterschiedlichen Schritten planen.

### <a name="azure-vms"></a>Virtuelle Azure-Computer

Virtuelle Azure-Computer verfügen bereits über eine [Ressource](../../../azure-resource-manager/management/overview.md#terminology) in Azure. Das bedeutet, dass sie für Gastkonfigurationszuweisungen bereit sind, die sie einer Konfiguration zuordnen. Die übergeordneten Aufgaben für die Migration virtueller Azure-Computer bestehen darin, sie aus Azure Automation State Configuration zu entfernen und dann Konfigurationen mithilfe der Gastkonfiguration zuzuweisen.

Um einen Computer aus Azure Automation State Configuration zu entfernen, führen Sie die Schritte auf der Seite [Entfernen einer Konfiguration und eines Knotens aus Automation State Configuration](../../../automation/state-configuration/remove-node-and-configuration-package.md) aus.

Führen Sie zum Zuweisen von Konfigurationen mithilfe der Gastkonfiguration die Schritte in den Azure Policy Schnellstarts aus, z. B. [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen](../assign-policy-portal.md).
Wählen Sie in Schritt 6 beim Auswählen einer Richtliniendefinition die Definition aus, die eine Konfiguration anwendet, die Sie aus Azure Automation State Configuration migriert haben.

### <a name="hybrid-machines"></a>Hybridcomputer

Computer außerhalb von Azure [können für Azure Automation State Configuration registriert werden](../../../automation/automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines), verfügen jedoch nicht über eine Computerressource in Azure. Die Verbindung mit Azure Automation wird vom lokalen Konfigurations-Manager-Dienst auf dem Computer verarbeitet, und der Datensatz des Knotens wird als Ressource im Anbietertyp Azure Automation verwaltet.

Bevor Sie einen Computer aus Azure Automation State Configuration entfernen, integrieren Sie jeden Knoten als [Azure Arc-fähigen Server](../../../azure-arc/servers/overview.md).
Das Onboarding in Azure Arc erstellt eine Computerressource in Azure, damit der Computer von Azure Policy verwaltet werden kann. Der Computer kann jederzeit in Azure Arc integriert werden, aber Sie können Azure Automation State Configuration verwenden, um den Prozess zu automatisieren.

Sie können einen Computer mithilfe von PowerShell DSC bei Azure Arc aktivierten Servern registrieren.
Weitere Informationen finden Sie unter [Installieren des Connected Machine-Agents mithilfe von Windows PowerShell DSC](../../../azure-arc/servers/onboard-dsc.md).
Denken Sie jedoch daran, dass Azure Automation State Configuration nur eine Konfiguration pro Computer pro Automation-Konto verwalten kann. Dies bedeutet, dass Sie die Möglichkeit haben, Ihre Inhalte für die Gastkonfiguration zu exportieren, zu testen und vorzubereiten, und dann die Knotenkonfiguration in Azure Automation zu wechseln, um sie in Azure Arc zu integrieren. Im letzten Schritt entfernen Sie die Knotenregistrierung aus Azure Automation State Configuration und verwalten den Computerstatus nur über die Gastkonfiguration.

## <a name="troubleshooting-issues-when-exporting-content"></a>Problembehandlung beim Exportieren von Inhalten

Details zu bekannten Problemen werden bereitgestellt.

### <a name="exporting-configurations-results-in--character-in-file-name"></a>Das Exportieren von Konfigurationen bewirkt die Hinzufügung des Zeichens „\\“ im Dateinamen.

Wenn Sie PowerShell unter MacOS/Linux verwenden, treten Probleme im Zusammenhang mit den Dateinamen auf, die von `Export-AzAutomationDSCConfiguration` ausgegeben werden.

Als Problemumgehung wurde ein Modul im PowerShell-Katalog mit dem Namen [AADSCConfigContent](https://www.powershellgallery.com/packages/AADSCConfigContent/) veröffentlicht.
Das Modul verfügt nur über einen Befehl, der den Inhalt einer in Azure Automation gespeicherten Konfiguration exportiert, indem eine REST-Anforderung an den Dienst gestellt wird.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie ein Paketartefakt](./guest-configuration-create.md) für Gastkonfigurationen.
- [Testen Sie das Paketartefakt](./guest-configuration-create-test.md) aus Ihrer Entwicklungsumgebung.
- [Veröffentlichen Sie das Paketartefakt](./guest-configuration-create-publish.md), damit es für Ihre Computer zugänglich ist.
- Verwenden Sie das Modul `GuestConfiguration` zum [Erstellen einer Azure Policy Definition](./guest-configuration-create-definition.md) für die skalierungsorientierte Verwaltung Ihrer Umgebung.
- [Weisen Sie Ihre Richtliniendefinition](../assign-policy-portal.md) mithilfe des Azure-Portals zu.
- Erfahren Sie, wie Sie die [Konformitätsdetails für die Zuweisung von Gastkonfigurationsrichtlinien](./determine-non-compliance.md#compliance-details-for-guest-configuration) anzeigen.
