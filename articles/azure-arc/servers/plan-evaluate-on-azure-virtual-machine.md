---
title: Auswerten von Servern mit Azure Arc-Unterstützung mit einem virtuellen Azure-Computer
description: Erfahren Sie, wie Sie Server mit Azure Arc-Unterstützung mithilfe eines virtuellen Azure-Computers auswerten.
ms.date: 07/16/2021
ms.topic: conceptual
ms.openlocfilehash: 2c981bad00d286860c3759e8266011c6685ff994
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114392814"
---
# <a name="evaluate-arc-enabled-servers-on-an-azure-virtual-machine"></a>Auswerten von Servern mit Azure Arc-Unterstützung auf einem virtuellen Azure-Computer

Server mit Azure Arc-Unterstützung sollen Ihnen helfen, Server, die lokal oder in anderen Clouds ausgeführt werden, mit Azure zu verbinden. Normalerweise würden Sie keine Server mit Azure Arc-Unterstützung auf einem virtuellen Azure-Computer verwenden, da all dieselben Funktionen nativ für diese VMs verfügbar sind, einschließlich einer Darstellung des virtuellen Computers in Azure Resource Manager, VM-Erweiterungen, verwalteten Identitäten und Azure Policy. Wenn Sie versuchen, Server mit Azure Arc-Unterstützung auf einem virtuellen Azure-Computer zu installieren, erhalten Sie die Fehlermeldung, dass dies nicht unterstützt und die Agent-Installation abgebrochen wird.

Sie können keine Server mit Azure Arc-Unterstützung auf einem virtuellen Azure-Computer für Produktionsszenarien installieren, es ist jedoch möglich, Server mit Azure Arc-Unterstützung so zu konfigurieren, dass sie *nur zu Auswertungs- und Testzwecken* auf einem virtuellen Azure-Computer ausgeführt werden. Dieser Artikel hilft Ihnen beim Einrichten eines virtuellen Azure-Computers, bevor Sie Server mit Azure Arc-Unterstützung darauf aktivieren können.

## <a name="prerequisites"></a>Voraussetzungen

* Ihr Konto ist der Rolle [Mitwirkender von virtuellen Computern](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) zugewiesen.
* Auf dem virtuellen Azure-Computer wird ein [Betriebssystem ausgeführt, das von Servern mit Azure Arc-Unterstützung](agent-overview.md#supported-operating-systems) unterstützt wird. Wenn Sie über keinen virtuellen Azure-Computer verfügen, können Sie eine [einfache Windows-VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json) oder eine [einfache Ubuntu Linux 18.04 LTS-VM](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2fquickstarts%2fmicrosoft.compute%2fvm-simple-windows%2fazuredeploy.json) bereitstellen.
* Ihre Azure-VM verfügt über eine ausgehende Kommunikation, um das Azure Connected Machine-Agent-Paket für Windows aus dem [Microsoft Download Center](https://aka.ms/AzureConnectedMachineAgent) und für Linux aus dem Microsoft-[Paketrepository](https://packages.microsoft.com/) herunterzuladen. Wenn ausgehende Verbindungen mit dem Internet nach Ihrer IT-Sicherheitsrichtlinie eingeschränkt sind, müssen Sie das Agent-Paket manuell herunterladen und in einen Ordner auf dem virtuellen Azure-Computer kopieren.
* Ein Konto mit erhöhten Rechten (d. h. Administrator oder Root) auf dem virtuellen Computer und RDP- oder SSH-Zugriff auf den virtuellen Computer.
* Zum Registrieren und Verwalten des virtuellen Azure-Computers mit Servern mit Azure Arc-Unterstützung sind Sie Mitglied der Rolle [Ressourcenadministrator für Azure Connected Machine](../../role-based-access-control/built-in-roles.md#azure-connected-machine-resource-administrator) oder [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) in der Ressourcengruppe.

## <a name="plan"></a>Planen

Um Ihre Azure-VM als Server mit Azure Arc-Unterstützung zu verwalten, müssen Sie die folgenden Änderungen an der Azure-VM vornehmen, bevor Sie Server mit Azure Arc-Unterstützung installieren und konfigurieren können.

1. Entfernen Sie alle VM-Erweiterungen, die auf dem virtuellen Azure-Computer bereitgestellt wurden, z. B. den Log Analytics-Agent. Während Server mit Azure Arc-Unterstützung viele der gleichen Erweiterungen wie virtuelle Azure-Computer unterstützen, kann der Agent des Servers mit Azure Arc-Unterstützung entsprechende VM-Erweiterungen, die bereits auf dem virtuellen Computer bereitgestellt wurden, nicht verwalten.

2. Deaktivieren Sie den Azure Windows- oder Linux-Gast-Agent. Der Gast-Agent des virtuellen Azure-Computers dient einem ähnlichen Zweck wie der Connected Machine-Agent des Servers mit Azure Arc-Unterstützung. Um Konflikte zwischen den beiden zu vermeiden, muss der Azure-VM-Agent deaktiviert werden. Sobald er deaktiviert ist, können Sie keine VM-Erweiterungen oder bestimmte Azure-Dienste verwenden.

3. Erstellen Sie eine Sicherheitsregel, um den Zugriff auf Azure Instance Metadata Service (IMDS) zu verweigern. IMDS ist eine REST-API, die Anwendungen aufrufen können, um Informationen zur Darstellung des virtuellen Computers in Azure abzurufen, einschließlich Ressourcen-ID und Standort. IMDS bietet auch Zugriff auf alle verwalteten Identitäten, die dem Computer zugewiesen sind. Server mit Azure Arc-Unterstützung bieten eine eigene IMDS-Implementierung und geben Informationen über die Azure Arc-Darstellung des virtuellen Computers zurück. Um Situationen zu vermeiden, in denen beide IMDS-Endpunkte verfügbar sind und Apps zwischen den beiden Endpunkten wählen müssen, blockieren Sie den Zugriff auf die Azure-VM-IMDS, sodass nur die IMDS-Implementierung für Server mit Azure Arc-Unterstützung verfügbar ist.

Nachdem Sie diese Änderungen vorgenommen haben, verhält sich Ihr virtueller Azure-Computer wie jeder andere Computer oder Server außerhalb von Azure und befindet sich am erforderlichen Startpunkt, um Server mit Azure Arc-Unterstützung zu installieren und auszuwerten.

Wenn Server mit Azure Arc-Unterstützung auf dem virtuellen Computer konfiguriert sind, werden zwei Darstellungen von ihm in Azure angezeigt. Eine ist die Azure-VM-Ressource mit einem `Microsoft.Compute/virtualMachines`-Ressourcentyp, die andere eine Azure Arc-Ressource mit einem `Microsoft.HybridCompute/machines`-Ressourcentyp. Da die Verwaltung des Gastbetriebssystems vom freigegebenen physischen Hostserver verhindert wird, ist die beste Möglichkeit, sich die beiden Ressourcen vorzustellen, dass die Azure-VM-Ressource die virtuelle Hardware für Ihren virtuellen Computer ist. Nun können Sie den Energiezustand steuern und Informationen zu seiner SKU, dem Netzwerk und den Speicherkonfigurationen anzeigen. Die Azure Arc-Ressource verwaltet das Gastbetriebssystem auf diesem virtuellen Computer und kann verwendet werden, um Erweiterungen zu installieren, Konformitätsdaten für Azure Policy anzuzeigen und alle anderen von Servern mit Azure Arc-Unterstützung unterstützten Aufgaben auszuführen.

## <a name="reconfigure-azure-vm"></a>Neukonfigurieren eines virtuellen Azure-Computers

1. Entfernen Sie alle VM-Erweiterungen auf dem virtuellen Azure-Computer.

   Navigieren Sie im Azure-Portal zu Ihrer Azure-VM-Ressource, und wählen Sie im linken Bereich **Erweiterungen** aus. Wenn Erweiterungen auf dem virtuellen Computer installiert sind, wählen Sie jede Erweiterung einzeln aus, und wählen Sie dann **Deinstallieren** aus. Warten Sie, bis die Deinstallation aller Erweiterungen abgeschlossen ist, bevor Sie mit Schritt 2 fortfahren.

2. Deaktivieren Sie den Azure-VM-Gast-Agent.

   Um den Azure-VM-Gast-Agent zu deaktivieren, müssen Sie über eine Remotedesktopverbindung (Windows) oder SSH (Linux) eine Verbindung mit Ihrem virtuellen Computer herstellen. Führen Sie nach dem Herstellen der Verbindung die folgenden Befehle aus, um den Gast-Agent zu deaktivieren.

   Führen Sie für Windows die folgenden PowerShell-Befehle aus:

   ```powershell
   Set-Service WindowsAzureGuestAgent -StartupType Disabled -Verbose
   Stop-Service WindowsAzureGuestAgent -Force -Verbose
   ```

   Für Linux führen Sie die folgenden Befehle aus:

   ```bash
   sudo service walinuxagent stop
   sudo waagent -deprovision -force
   sudo rm -rf /var/lib/waagent
   ```

3. Blockieren Sie den Zugriff auf den Azure IMDS-Endpunkt.

   Führen Sie bei bestehender Verbindung mit dem Server die folgenden Befehle aus, um den Zugriff auf den Azure IMDS-Endpunkt zu blockieren. Führen Sie für Windows den folgenden PowerShell-Befehl aus:

   ```powershell
   New-NetFirewallRule -Name BlockAzureIMDS -DisplayName "Block access to Azure IMDS" -Enabled True -Profile Any -Direction Outbound -Action Block -RemoteAddress 169.254.169.254
   ```

   Für Linux lesen Sie in der Dokumentation Ihrer Distribution nach, wie Sie den ausgehenden Zugriff auf `169.254.169.254/32` über den TCP-Port 80 am besten blockieren können. Normalerweise blockieren Sie den ausgehenden Zugriff über die integrierte Firewall, aber Sie können ihn auch vorübergehend mithilfe von **iptables** oder **nftables** blockieren.

   Wenn Ihre Azure-VM unter Ubuntu ausgeführt wird, führen Sie die folgenden Schritte aus, um dessen unkomplizierte Firewall (UFW) zu konfigurieren:

   ```bash
   sudo ufw --force enable
   sudo ufw deny out from any to 169.254.169.254
   sudo ufw default allow incoming
   sudo apt-get update
   ```


   Führen Sie zum Konfigurieren einer generischen iptables-Konfiguration den folgenden Befehl aus:

   ```bash
   iptables -A OUTPUT -d 169.254.169.254 -j DROP
   ```

   > [!NOTE]
   > Diese Konfiguration muss nach jedem Neustart festgelegt werden, es sei denn, es wird eine persistente iptables-Lösung verwendet.

4. Installieren und konfigurieren Sie den Agent des Servers mit Azure Arc-Unterstützung.

   Der virtuelle Computer ist jetzt bereit, damit Sie mit der Auswertung von Servern mit Azure Arc-Unterstützung beginnen können. Informationen zum Installieren und Konfigurieren des Agents des Servers mit Azure Arc-Unterstützung finden Sie unter [Verbinden von Hybridcomputern über das Azure-Portal](onboard-portal.md). Befolgen Sie dann die Schritte zum Generieren eines Installationsskripts und Installieren mithilfe der Skriptmethode.

   > [!NOTE]
   > Wenn ausgehende Verbindungen mit dem Internet von Ihrem virtuellen Azure-Computer eingeschränkt sind, müssen Sie das Agent-Paket manuell herunterladen. Kopieren Sie das Agent-Paket auf den virtuellen Azure-Computer, und ändern Sie das Installationsskript für Server mit Azure Arc-Unterstützung, um auf den Quellordner zu verweisen.

Wenn Sie einen der Schritte verpasst haben, erkennt das Installationsskript, dass es auf einem virtuellen Azure-Computer ausgeführt wird, und wird mit einem Fehler beendet. Vergewissern Sie sich, dass Sie die Schritte 1 bis 3 ausgeführt haben, und führen Sie dann das Skript erneut aus.

## <a name="verify-the-connection-with-azure-arc"></a>Überprüfen der Verbindung mit Azure Arc

Überprüfen Sie im Azure-Portal, ob die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Registrierung bei Azure Arc-fähigen Servern konfiguriert haben. Sehen Sie sich Ihre Computer im [Azure-Portal](https://portal.azure.com) an.

![Erfolgreiche Serververbindung](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie Sie eine große Anzahl von Computern für Server mit Azure Arc-Unterstützung planen und aktivieren](plan-at-scale-deployment.md), um die Konfiguration wichtiger Sicherheits-, Verwaltungs- und Überwachungsfunktionen in Azure zu vereinfachen.

* Erfahren Sie mehr über unsere [unterstützten Azure-VM-Erweiterungen](manage-vm-extensions.md), um die Bereitstellung mit anderen Azure-Diensten wie Automation, KeyVault und anderen für Ihren Windows- oder Linux-Computer zu vereinfachen.

* Wenn Sie die Tests abgeschlossen haben, finden Sie weitere Informationen unter [Entfernen des Agents für Server mit Azure Arc-Unterstützung](manage-agent.md#remove-the-agent).
