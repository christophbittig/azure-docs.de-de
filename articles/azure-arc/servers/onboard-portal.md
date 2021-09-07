---
title: Verbinden von Hybridcomputern mit Azure über das Azure-Portal
description: In diesem Artikel erfahren Sie, wie Sie Azure Arc-fähige Server im Azure-Portal verwenden, um den Agent zu installieren und Computer mit Azure zu verbinden.
ms.date: 08/17/2021
ms.topic: conceptual
ms.openlocfilehash: bcccb9bbc4db14c2bc5553b1c88099f7b0d7f5d1
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122356586"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Verbinden von Hybridcomputern mit Azure über das Azure-Portal

Azure Arc-fähige Server können mithilfe einiger manueller Schritte für einen oder einige wenige Windows- oder Linux-Computer in Ihrer Umgebung aktiviert werden. Alternativ steht auch eine automatisierte Methode in Form eines uns bereitgestellten Vorlagenskripts zur Verfügung. Dieses Skript automatisiert das Herunterladen und Installieren beider Agents.

Für diese Methode müssen Sie auf dem Computer über Administratorberechtigungen verfügen, um den Agent installieren und konfigurieren zu können. Unter Linux muss hierfür das root-Konto verwendet werden. Unter Windows müssen Sie der Gruppe der lokalen Administratoren angehören.

Bevor Sie beginnen, sollten Sie die [Voraussetzungen](agent-overview.md#prerequisites) überprüfen und sicherstellen, dass Ihr Abonnement und Ihre Ressourcen auch diese Anforderungen erfüllen. Informationen zu unterstützten Regionen und andere Überlegungen finden Sie unter [Unterstützte Azure-Regionen](overview.md#supported-regions).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Generieren des Installationsskripts über das Azure-Portal

Das Skript zum Automatisieren des Download- und Installationsvorgangs sowie zum Herstellen der Verbindung mit Azure Arc ist über das Azure-Portal verfügbar. Führen Sie die folgenden Schritte aus, um den Prozess abzuschließen:

1. Navigieren Sie in Ihrem Browser zum [Azure-Portal](https://portal.azure.com).

1. Wählen Sie auf der Seite **Server - Azure Arc** oben links **Hinzufügen** aus.

1. Wählen Sie auf der Seite **Methode auswählen** die Kachel **Server über ein interaktives Skript hinzufügen** und dann **Skript generieren** aus.

1. Wählen Sie auf der Seite **Skript generieren** das Abonnement und die Ressourcengruppe für die Verwaltung des Computers in Azure aus. Wählen Sie einen Azure-Standort zum Speichern der Computermetadaten aus. Bei diesem Standort kann es sich um denselben Standort wie bei der Ressourcengruppe oder um einen anderen Standort handeln.

1. Lesen Sie sich die Informationen auf der Seite **Voraussetzungen** durch, und wählen Sie dann **Weiter: Ressourcendetails** aus.

1. Geben Sie auf der Seite **Ressourcendetails** Folgendes an:

    1. Wählen Sie in der Dropdownliste **Ressourcengruppe** die Ressourcengruppe aus, über die der Computer verwaltet wird.
    1. Wählen Sie in der Dropdownliste **Regionen** die Azure-Region aus, in der die Metadaten des Servers gespeichert werden sollen.
    1. Wählen Sie in der Dropdownliste **Betriebssystem** das Betriebssystem aus, für das das Skript konfiguriert ist.
    1. Wenn der Computer über einen Proxyserver kommuniziert, um eine Verbindung mit dem Internet herzustellen, geben Sie die IP-Adresse des Proxyservers oder den Namen und die Portnummer an, die der Computer für die Kommunikation mit dem Proxyserver verwenden wird. Geben Sie den Wert im Format `http://<proxyURL>:<proxyport>` ein.
    1. Klicken Sie auf **Weiter: Tags**.

1. Überprüfen Sie auf der Seite **Tags** die vorgeschlagenen standardmäßigen **physischen Speicherorttags**, und geben Sie einen Wert ein, oder geben Sie mindestens ein **benutzerdefiniertes Tag** an, um Ihre Standards zu unterstützen.

1. Klicken Sie auf **Weiter: Skript herunterladen und ausführen**.

1. Überprüfen Sie die auf der Seite **Skript herunterladen und ausführen** die Zusammenfassungsinformationen, und wählen Sie dann **Herunterladen** aus. Sollten noch Änderungen erforderlich sein, wählen Sie **Zurück** aus.

## <a name="install-and-validate-the-agent-on-windows"></a>Installieren und Überprüfen des Agents unter Windows

### <a name="install-manually"></a>Manuelle Installation

Sie können den Azure Connected Machine-Agent manuell installieren, indem Sie das Windows Installer-Paket *AzureConnectedMachineAgent.msi* ausführen. Sie können die neueste Version des [Windows Installer-Pakets für den Windows-Agent](https://aka.ms/AzureConnectedMachineAgent) vom Microsoft Download Center herunterladen.

>[!NOTE]
>* Zum Installieren und Deinstallieren des Agents sind *Administratorberechtigungen* erforderlich.
>* Laden Sie zunächst das Installer-Paket herunter, und kopieren Sie es in einen Ordner auf dem Zielserver, oder verwenden Sie einen freigegebenen Netzwerkordner. Wenn Sie das Installer-Paket ohne Optionen ausführen, wird ein Setup-Assistent zur interaktiven Installation des Agents gestartet.

Wenn der Computer über einen Proxyserver mit dem Dienst kommunizieren muss, muss nach der Installation des Agents ein weiter unten in den Schritten beschriebener Befehl ausgeführt werden. Mit diesem Befehl wird die Systemumgebungsvariable `https_proxy` für den Proxyserver festgelegt. Bei dieser Konfiguration kommuniziert der Agent mithilfe des HTTP-Protokolls über den Proxyserver.

Wenn Sie nicht mit den Befehlszeilenoptionen für Windows Installer-Pakete vertraut sind, lesen Sie die Artikel [Standardmäßige Installer-Befehlszeilenoptionen](/windows/win32/msi/standard-installer-command-line-options) und [Befehlszeilenoptionen](/windows/win32/msi/command-line-options).

Führen Sie das Installationsprogramm z. B. mit dem Parameter `/?` aus, um die Hilfe- und Kurzübersichtsoption anzuzeigen.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Führen Sie den folgenden Befehl aus, um den Agent im Hintergrund zu installieren und eine Setupprotokolldatei im vorhandenen Ordner `C:\Support\Logs` zu erstellen.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Sollte der Agent nach Abschluss des Setups nicht gestartet werden, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Das Protokollverzeichnis lautet *%ProgramData%\AzureConnectedMachineAgent\log*.

2. Wenn der Computer über einen Proxyserver kommunizieren muss, führen Sie den folgenden Befehl aus, um die Umgebungsvariable für den Proxyserver festzulegen:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >Das Festlegen der Proxyauthentifizierung wird vom Agent nicht unterstützt.
    >

3. Nachdem Sie den Agent installiert haben, müssen Sie ihn für die Kommunikation mit dem Azure Arc-Dienst konfigurieren, indem Sie den folgenden Befehl ausführen:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Installieren mit der Skriptmethode

1. Melden Sie sich beim Server an.

1. Öffnen Sie eine PowerShell-Eingabeaufforderung mit erhöhten Rechten.

    >[!NOTE]
    >Das Skript unterstützt nur die Ausführung in einer 64-Bit-Version von Windows PowerShell.
    >

1. Navigieren Sie zu dem Ordner bzw. zu der Freigabe, in den bzw. in die Sie das Skript kopiert haben, und führen Sie es auf dem Server aus, indem Sie das Skript `./OnboardingScript.ps1` ausführen.

Sollte der Agent nach Abschluss des Setups nicht gestartet werden, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Das Protokollverzeichnis lautet *%ProgramData%\AzureConnectedMachineAgent\log*.

## <a name="install-and-validate-the-agent-on-linux"></a>Installieren und Überprüfen des Agents unter Linux

Der Linux-Agent für verbundene Computer wird im bevorzugten Paketformat für die Distribution (RPM oder DEB) bereitgestellt, die im [Paketrepository](https://packages.microsoft.com/) von Microsoft gehostet wird. Durch das [Shell-Skriptpaket `Install_linux_azcmagent.sh`](https://aka.ms/azcmagent) werden folgende Aktionen ausgeführt:

* Konfigurieren des Hostcomputers für das Herunterladen des Agent-Pakets von „packages.microsoft.com“

* Installieren des Hybridressourcenanbieter-Pakets

Durch Einschließen des Parameters `--proxy "{proxy-url}:{proxy-port}"` kann der Agent optional mit Ihren Proxyinformationen konfiguriert werden. Bei dieser Konfiguration kommuniziert der Agent mithilfe des HTTP-Protokolls über den Proxyserver.

Das Skript enthält auch Logik zum Identifizieren der unterstützten und nicht unterstützten Distributionen, und es überprüft die Berechtigungen, die zum Ausführen der Installation erforderlich sind.

Im folgenden Beispiel wird der Agent heruntergeladen und installiert:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. Um den Agent herunterzuladen und zu installieren, führen sie die folgenden Befehle aus. Wenn Ihr Computer über einen Proxyserver kommunizieren muss, um eine Verbindung mit dem Internet herzustellen, schließen Sie den Parameter `--proxy` ein. 

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. Nachdem Sie den Agent installiert haben, müssen Sie ihn für die Kommunikation mit dem Azure Arc-Dienst konfigurieren, indem Sie den folgenden Befehl ausführen:

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>Installieren mit der Skriptmethode

1. Melden Sie sich beim Server mit einem Konto an, das über root-Zugriff verfügt.

1. Navigieren Sie zu dem Ordner bzw. zu der Freigabe, in den bzw. in die Sie das Skript kopiert haben, und führen Sie es auf dem Server aus, indem Sie das Skript `./OnboardingScript.sh` ausführen.

Sollte der Agent nach Abschluss des Setups nicht gestartet werden, suchen Sie in den Protokollen nach ausführlichen Fehlerinformationen. Das Protokollverzeichnis ist *var/opt/azcmagent/log*.

## <a name="verify-the-connection-with-azure-arc"></a>Überprüfen der Verbindung mit Azure Arc

Vergewissern Sie sich im Azure-Portal, dass die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Verbindungsherstellung mit Azure Arc-fähigen Servern konfiguriert haben. Zeigen Sie Ihre Computer im [Azure-Portal](https://aka.ms/hybridmachineportal) an.

![Erfolgreiche Serververbindung](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

- Lesen Sie den [Planungs- und Bereitstellungsleitfaden](plan-at-scale-deployment.md), um die Bereitstellung von Servern mit Azure Arc-Unterstützung in beliebiger Größenordnung zu planen und eine zentrale Verwaltung und Überwachung zu implementieren.

- Erfahren Sie, wie Sie Ihren Computer mithilfe von [Azure Policy](../../governance/policy/overview.md) verwalten, wie z. B. bei der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md), dem Überprüfen, ob der Computer dem erwarteten Log Analytics-Arbeitsbereich Bericht erstattet, beim Aktivieren der Überwachung mit [VM Insights](../../azure-monitor/vm/vminsights-enable-policy.md) und vieles mehr.