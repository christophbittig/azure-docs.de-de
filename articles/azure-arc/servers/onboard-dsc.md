---
title: Installieren des Connected Machine-Agent mithilfe von Windows PowerShell DSC
description: In diesem Artikel erfahren Sie, wie Sie Computer mithilfe von Azure Arc-fähigen Servern und Windows PowerShell DSC mit Azure verbinden.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: 357d89a989efbc3b8deded193808d47753b722d9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124789083"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Installieren des Connected Machine-Agent mithilfe von Windows PowerShell DSC

Mit [Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted) (DSC) können Sie die Softwareinstallation und -konfiguration für einen Windows-Computer automatisieren. In diesem Artikel wird beschrieben, wie Sie mit DSC den Connected Machine-Agent für Azure Arc-fähige Server auf Windows-Hybridcomputern installieren.

## <a name="requirements"></a>Requirements (Anforderungen)

- Windows PowerShell, Version 4.0 oder höher

- Das [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc)-DSC-Modul

- Ein Dienstprinzipal zum nicht interaktiven Herstellen einer Verbindung der Computer mit Azure Arc-fähigen Servern. Führen Sie die Schritte im Abschnitt [Erstellen eines Dienstprinzipals für flexibles Onboarding](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) aus, wenn Sie noch keinen Dienstprinzipal für Azure Arc-fähige Server erstellt haben.

## <a name="install-the-connectedmachine-dsc-module"></a>Installieren des ConnectedMachine-DSC-Moduls

1. Um das Modul manuell zu installieren, laden Sie den Quellcode herunter, und entpacken Sie den Inhalt des Projektverzeichnisses in `$env:ProgramFiles\WindowsPowerShell\Modules folder`. Führen Sie alternativ den folgenden Befehl aus, um mithilfe von PowerShellGet (in PowerShell 5.0) aus dem PowerShell-Katalog zu installieren:

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. Um die Installation zu bestätigen, führen Sie den folgenden Befehl aus, um sicherzustellen, dass die DSC-Ressourcen von Azure Connected Machine verfügbar sind.

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   Etwa folgende Ausgabe sollte angezeigt werden:

   ![Bestätigung des Installationsbeispiels für das DSC-Modul von Connected Machine](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>Installieren des Agents und Herstellen einer Verbindung mit Azure

Die Ressourcen in diesem Modul sind zum Verwalten der Konfiguration des Azure Connected Machine-Agent konzipiert. Außerdem ist ein PowerShell-Skript `AzureConnectedMachineAgent.ps1` enthalten, das sich im Ordner `AzureConnectedMachineDsc\examples` befindet. Es verwendet Communityressourcen, um den Download und die Installation zu automatisieren und eine Verbindung mit Azure Arc herzustellen. Dieses Skript führt ähnliche Schritte aus, wie im Artikel [Verbinden von Hybridcomputern mit Azure über das Azure-Portal](onboard-portal.md) beschrieben.

Wenn der Computer über einen Proxyserver mit dem Dienst kommunizieren muss, muss nach der Installation des Agent ein [hier](manage-agent.md#update-or-remove-proxy-settings) beschriebener Befehl ausgeführt werden. Dadurch wird die Systemumgebungsvariable `https_proxy` für den Proxyserver festgelegt. Anstatt den Befehl manuell auszuführen, können Sie diesen Schritt mit DSC ausführen, indem Sie das Modul [ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc) verwenden. Bei dieser Konfiguration kommuniziert der Agent mithilfe des HTTP-Protokolls über den Proxyserver.

>[!NOTE]
>Damit DSC ausgeführt werden kann, muss Windows für den Empfang von PowerShell-Remotebefehlen konfiguriert werden – selbst dann, wenn Sie eine Localhostkonfiguration ausführen. Um Ihre Umgebung auf einfache Weise ordnungsgemäß zu konfigurieren, rufen Sie einfach `Set-WsManQuickConfig -Force` in einem PowerShell-Terminal mit erhöhten Rechten aus.
>

Konfigurationsdokumente (MOF-Dateien) können mit dem Cmdlet `Start-DscConfiguration` auf den Computer angewendet werden.

Im Folgenden werden die Parameter angegeben, die Sie an das zu verwendende PowerShell-Skript übergeben.

- `TenantId`: Die eindeutige ID (GUID), die Ihre dedizierte Instanz von Azure AD darstellt.

- `SubscriptionId`: Die Abonnement-ID (GUID) Ihres Azure-Abonnements, in das Computer aufgenommen werden sollen.

- `ResourceGroup`: Der Name der Ressourcengruppe, zu der Ihre verbundenen Computer gehören sollen.

- `Location`: Siehe [Unterstützte Azure-Regionen](overview.md#supported-regions). Bei diesem Standort kann es sich um denselben Standort wie bei der Ressourcengruppe oder um einen anderen Standort handeln.

- `Tags`: Zeichenfolgenarray von Tags, die auf die verbundene Computerressource angewendet werden sollten.

- `Credential`: Ein PowerShell-Anmeldeinformationsobjekt mit **ApplicationId** und **Kennwort**, die zur flexiblen Registrierung von Computern mithilfe eines [Dienstprinzipals](onboard-service-principal.md) verwendet wird.

1. Navigieren Sie in einer PowerShell-Konsole zu dem Ordner, in dem Sie die `.ps1`-Datei gespeichert haben.

2. Führen Sie die folgenden PowerShell-Befehle zum Kompilieren des MOF-Dokuments aus (Informationen zum Kompilieren von DSC-Konfigurationen finden Sie unter [DSC-Konfigurationen](/powershell/scripting/dsc/configurations/configurations):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. Dadurch wird eine `localhost.mof file` in einem neuen Ordner namens `C:\dsc` erstellt.

Vergewissern Sie sich im Azure-Portal, dass die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Verbindungsherstellung mit Azure Arc-fähigen Servern konfiguriert haben. Zeigen Sie Ihre Computer im [Azure-Portal](https://aka.ms/hybridmachineportal) an.

## <a name="adding-to-existing-configurations"></a>Hinzufügen zu vorhandenen Konfigurationen

Diese Ressource kann vorhandenen DSC-Konfigurationen hinzugefügt werden, um eine End-to-End-Konfiguration für einen Computer darzustellen. Beispielsweise kann es vorkommen, dass Sie diese Ressource einer Konfiguration hinzufügen möchten, mit der sichere Einstellungen für das Betriebssystem festgelegt werden.

Das [CompositeResource](https://www.powershellgallery.com/packages/compositeresource)-Modul aus dem PowerShell-Katalog kann zum Erstellen einer [zusammengesetzten Ressource](/powershell/scripting/dsc/resources/authoringResourceComposite) der Beispielkonfiguration verwendet werden, um die Kombination von Konfigurationen weiter zu vereinfachen.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

* Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.

* Erfahren Sie, wie Sie Ihren Computer mithilfe von [Azure Policy](../../governance/policy/overview.md) verwalten, wie z. B. bei der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md), dem Überprüfen, ob der Computer dem erwarteten Log Analytics-Arbeitsbereich Bericht erstattet, beim Aktivieren der Überwachung mit [VM Insights](../../azure-monitor/vm/vminsights-enable-policy.md) und vieles mehr.
