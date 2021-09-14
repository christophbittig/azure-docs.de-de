---
title: Verbinden von Hybridcomputern mit Azure mithilfe von PowerShell
description: In diesem Artikel wird erläutert, wie Sie mithilfe von Azure Arc-fähigen Servern den Agent installieren und einen Computer mit Azure verbinden. Hierfür können Sie PowerShell verwenden.
ms.date: 07/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1ca9528af5b529dd844e566905b6aa7f92429d2
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122343671"
---
# <a name="connect-hybrid-machines-to-azure-by-using-powershell"></a>Verbinden von Hybridcomputern mit Azure mithilfe von PowerShell

Azure Arc-fähige Server können Sie mithilfe manueller Schritte für einen oder mehrere Windows- oder Linux-Computer in Ihrer Umgebung aktivieren. Alternativ können Sie das PowerShell-Cmdlet [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) verwenden, um den Connected Machine-Agent herunterzuladen und zu installieren und den Computer bei Azure Arc zu registrieren. Mit dem Cmdlet können Sie das Paket für den Windows-Agent (Windows Installer) im Microsoft Download Center und das Paket für den Linux-Agent im Microsoft-Paketrepository herunterladen.

Für diese Methode müssen Sie auf dem Computer über Administratorberechtigungen verfügen, um den Agent installieren und konfigurieren zu können. Unter Linux muss hierfür das root-Konto verwendet werden. Unter Windows müssen Sie der Gruppe der lokalen Administratoren angehören. Sie können diesen Prozess mithilfe von [PowerShell-Remoting](/powershell/scripting/learn/ps101/08-powershell-remoting) interaktiv oder remote auf einem Windows-Server ausführen.

Bevor Sie beginnen, überprüfen Sie die [Voraussetzungen](agent-overview.md#prerequisites), und vergewissern Sie sich, dass Ihr Abonnement und Ihre Ressourcen den Anforderungen entsprechen. Informationen zu unterstützten Regionen und andere Überlegungen finden Sie unter [Unterstützte Azure-Regionen](overview.md#supported-regions).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Computer mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/).

Verwenden Sie PowerShell zum Verwalten von VM-Erweiterungen auf Ihren über Azure Arc-fähige Server verwalteten Hybridservern. Installieren Sie vor der Verwendung von PowerShell das Modul `Az.ConnectedMachine`. Führen Sie auf Ihrem Azure Arc-fähigen Server den folgenden Befehl aus:

```powershell
Install-Module -Name Az.ConnectedMachine
```

Nach Abschluss der Installation wird die folgende Meldung angezeigt:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installieren des Agents und Herstellen einer Verbindung mit Azure

1. Öffnen Sie eine PowerShell-Konsole mit erhöhten Rechten.

2. Melden Sie sich bei Azure an, indem Sie den Befehl `Connect-AzAccount` ausführen.

3. Verwenden Sie `Connect-AzConnectedMachine` mit den Parametern `-Name`, `-ResourceGroupName` und `-Location`, um den Connected Machine-Agent zu installieren. Verwenden Sie den Parameter `-SubscriptionId`, um das Standardabonnement als Ergebnis des Azure-Kontexts zu überschreiben, das nach der Anmeldung erstellt wurde. Führen Sie einen der folgenden Befehle aus:

    * Führen Sie den folgenden Befehl aus, um den Connected Machine-Agent auf dem Zielcomputer zu installieren, der direkt mit Azure kommunizieren kann:

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region>
        ```
    
    * Führen Sie den folgenden Befehl aus, um den Connected Machine-Agent auf dem Zielcomputer zu installieren, der über einen Proxyserver kommunizieren kann:
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -Proxy http://<proxyURL>:<proxyport>
        ```

      Bei dieser Konfiguration kommuniziert der Agent mithilfe des HTTP-Protokolls über den Proxyserver.

Sollte der Agent nach Abschluss des Setups nicht gestartet werden, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Überprüfen Sie unter Windows die Datei *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log*. Überprüfen Sie unter Linux die Datei */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-by-using-powershell-remoting"></a>Installieren und Verbinden mithilfe von PowerShell-Remoting

Gehen Sie wie folgt vor, um einen oder mehrere Windows-Server mit Azure Arc-fähigen Servern zu konfigurieren. Sie müssen PowerShell-Remoting auf dem Remotecomputer aktivieren. Verwenden Sie hierzu das Cmdlet `Enable-PSRemoting`.

1. Öffnen Sie eine PowerShell-Konsole als Administrator.

2. Melden Sie sich bei Azure an, indem Sie den Befehl `Connect-AzAccount` ausführen.

3. Verwenden Sie `Connect-AzConnectedMachine` mit den Parametern `-ResourceGroupName` und `-Location`, um den Connected Machine-Agent zu installieren. In den Namen der Azure-Ressourcen wird automatisch der Hostname jedes Servers verwendet. Verwenden Sie den Parameter `-SubscriptionId`, um das Standardabonnement als Ergebnis des Azure-Kontexts zu überschreiben, das nach der Anmeldung erstellt wurde.

    * Führen Sie den folgenden Befehl aus, um den Connected Machine-Agent auf dem Zielcomputer zu installieren, der direkt mit Azure kommunizieren kann:
    
        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```
    
    * Um den Connected Machine-Agent gleichzeitig auf mehreren Remotecomputern zu installieren, fügen Sie eine Liste mit den jeweils durch Kommas getrennten Namen der Remotecomputer hinzu.

        ```azurepowershell
        $sessions = New-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Location <region> -PSSession $sessions
        ```

    Im folgenden Beispiel sind die Ergebnisse des Befehls mit einem einzelnen Computer als Ziel aufgeführt:
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Überprüfen der Verbindung mit Azure Arc

Überprüfen Sie im Azure-Portal, ob die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Registrierung bei Azure Arc-fähigen Servern konfiguriert haben. Sehen Sie sich Ihre Computer im [Azure-Portal](https://portal.azure.com) an.

![Screenshot des Dashboards „Server“ mit einer erfolgreichen Serververbindung](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nächste Schritte

* Bei Bedarf finden Sie Informationen im [Leitfaden zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

* Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.

* Erfahren Sie, wie Sie Ihren Computer mit [Azure Policy](../../governance/policy/overview.md) verwalten. Sie können mithilfe der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md) überprüfen, ob der Computer Meldungen an den erwarteten Log Analytics-Arbeitsbereich zurückgibt, und die Überwachung mit [VM Insights](../../azure-monitor/vm/vminsights-enable-policy.md) aktivieren.
