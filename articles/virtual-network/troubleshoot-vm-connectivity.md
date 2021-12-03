---
title: Problembehandlung für Azure-VM-Konnektivitätsprobleme
description: Erfahren Sie, wie Sie Konnektivitätsprobleme diagnostizieren und beheben, die sich auf virtuelle Azure-Computer (VMs) auswirken.
author: TobyTu
ms.author: kaushika
manager: dcscontentpm
audience: ITPro
ms.topic: troubleshooting
ms.service: virtual-network
localization_priority: Normal
ms.date: 08/29/2019
ms.openlocfilehash: ac29c46f9e7b1bdb3201e33bc35e35811d8adc98
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335345"
---
# <a name="troubleshoot-azure-vm-connectivity-problems"></a>Problembehandlung für Azure-VM-Konnektivitätsprobleme

Dieser Artikel unterstützt die Administratoren beim Diagnostizieren und Beheben von Konnektivitätsproblemen, die sich auf virtuelle Azure-Computer (VMs) auswirken.

## <a name="problems"></a>Probleme

- [Ein virtueller Azure-Computer, der mithilfe von Resource Manager bereitgestellt wurde, kann keine Verbindung mit einem anderen virtuellen Azure-Computer in demselben virtuellen Netzwerk herstellen](#azure-vm-cannot-connect-to-another-azure-vm-in-same-virtual-network).
- [Eine Azure-VM kann keine Verbindung mit dem zweiten Netzwerkadapter eines virtuellen Azure-Computers in demselben virtuellen Netzwerk herstellen.](#azure-vm-cannot-connect-to-the-second-network-adapter-of-an-azure-vm-in-same-virtual-network)
- [Eine Azure-VM kann keine Verbindung mit dem Internet herstellen](#azure-vm-cannot-connect-to-the-internet).

Führen Sie die Schritte im folgenden Abschnitt aus, um diese Probleme zu beheben.

## <a name="resolution"></a>Lösung

### <a name="azure-vm-cannot-connect-to-another-azure-vm-in-same-virtual-network"></a>Eine Azure-VM kann keine Verbindung mit einer anderen Azure-VM in demselben virtuellen Netzwerk herstellen

#### <a name="step-1-verify-that-vms-can-communicate-with-each-other"></a>Schritt 1: Überprüfen Sie, ob VMs miteinander kommunizieren können.

1. Laden Sie TCping auf Ihren virtuellen Quellcomputer herunter.
2. Öffnen Sie ein Eingabeaufforderungsfenster.
3. Navigieren Sie zu dem Ordner, in den Sie die TCping-Dateien heruntergeladen haben.
4. Pingen Sie das Ziel vom Quellcomputer aus mit folgendem Befehl an:

    ![Screenshot, der ein Eingabeaufforderungsfenster zeigt in dem eine IP-Adresse kontinuierlich pingt.](media/troubleshoot-vm-connectivity/tcping.png)

    ```cmd
    tcping64.exe -t <destination VM address> 3389
    ```

> [!TIP]
> Wenn der Pingtest erfolgreich ist, fahren Sie mit Schritt 3 fort. Fahren Sie andernfalls mit dem nächsten Schritt fort.

#### <a name="step-2-check-the-network-security-group-settings"></a>Schritt 2: Überprüfen Sie die Einstellungen der Netzwerksicherheitsgruppe.

Überprüfen Sie für jeden virtuellen Computer die Standardregeln für eingehende Ports („Eingehendes VNET zulassen“ und „Eingehenden Load Balancer zulassen“). Stellen Sie außerdem sicher, dass keine übereinstimmenden Blockierungsregeln vorhanden sind, die unter einer Regel mit niedrigerer Priorität aufgeführt sind.

> [!NOTE]
> Regeln mit einer niedrigeren Zahl werden zuerst abgeglichen. Wenn Sie beispielsweise über eine Regel mit der Priorität 1000 und 6500 verfügen, wird zuerst die Regel mit der Priorität 1000 abgeglichen.

Versuchen Sie anschließend erneut, das Ziel von der Quell-VM zu pingen:

```cmd
tcping64.exe -t <destination VM address> 3389
```

#### <a name="step-3-check-whether-you-can-connect-to-the-destination-vm-by-using-remote-desktop-or-ssh"></a>Schritt 3: Überprüfen Sie, ob Sie mithilfe von Remotedesktop oder SSH eine Verbindung mit dem virtuellen Zielcomputer herstellen können.

Führen Sie die folgenden Schritte aus, um über Remotedesktop eine Verbindung herzustellen.

**Windows**:

1. Melden Sie sich beim Azure-Portal an.
2. Wählen Sie im Menü auf der linken Seite die Option **Virtuelle Computer** aus.
3. Wählen Sie den gewünschten virtuellen Computer aus der Liste aus.
4. Wählen Sie auf der Seite für den virtuellen Computer die Schaltfläche **Verbinden** aus.

Weitere Informationen hierzu finden Sie unter [Gewusst wie: Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](../virtual-machines/windows/connect-logon.md).

**Linux:**

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit einer Linux-VM in Azure](../virtual-machines/linux/quick-create-portal.md).

Wenn die Remotedesktop- oder SSH-Verbindung erfolgreich ist, fahren Sie mit dem nächsten Schritt fort.

#### <a name="step-4-perform-a-connectivity-check"></a>Schritt 4: Durchführen einer Konnektivitätsprüfung.

Führen Sie eine Konnektivitätsprüfung auf dem virtuellen Quellcomputer aus, und überprüfen Sie die Antwort.

**Windows**: [Überprüfen der Konnektivität mit Azure Network Watcher mithilfe von PowerShell](../network-watcher/network-watcher-connectivity-powershell.md)

**Linux**: [Überprüfen der Konnektivität mit Azure Network Watcher mithilfe der Azure CLI 2.0](../network-watcher/network-watcher-connectivity-cli.md)

Hier sehen Sie eine Beispielantwort:

```
ConnectionStatus : Unreachable
AvgLatencyInMs   :
MinLatencyInMs   :
MaxLatencyInMs   :
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
```

#### <a name="step-5-fix-the-issue-in-the-connectivity-check-result"></a>Schritt 5: Beheben sie das Problem im Ergebnis der Konnektivitätsprüfung.

1. Überprüfen Sie im Abschnitt **Hops** der Antwort zur Konnektivitätsprüfung, die Sie erhalten haben und überprüfen Sie die aufgeführten **Probleme**.

    ![Konnektivitätsantwort](media/troubleshoot-vm-connectivity/connectivity-response.png)

2. Ermitteln Sie die entsprechende Lösung in der folgenden Tabelle und führen Sie die angegebenen Schritte aus, um die Probleme zu beheben.

    |Typ des Problems  |Wert  |Lösungsaktion |
    |---------|---------|---------|
    |NetworkSecurityRule|Name der blockierenden NSG|Sie können [die NSG-Regel löschen](./manage-network-security-group.md#delete-a-security-rule) oder die Regel wie [hier](./manage-network-security-group.md#change-a-security-rule)beschrieben ändern.|
    |UserDefinedRoute     |   Name der blockierenden UDR      | Wenn Sie diese Route nicht benötigen, löschen Sie die UDR. Wenn Sie die Route nicht löschen können, aktualisieren Sie die Route mit dem entsprechenden Adresspräfix und dem nächsten Hop. Sie können auch das virtuelle Netzwerkgerät anpassen, um den Datenverkehr entsprechend weiterzuleiten. Weitere Informationen finden Sie unter: [Routing des Datenverkehrs virtueller Netzwerke](./virtual-networks-udr-overview.md) und Weiterleiten von [Netzwerkdatenverkehr mit einer Routingtabelle mithilfe von PowerShell](./tutorial-create-route-table-powershell.md).|
    |CPU    |    Verwendung     |     Befolgen Sie diese Empfehlungen, die unter Problembehandlung bei der [generischen Leistung für virtuelle Azure-Computer unter Linux oder Windows](https://support.microsoft.com/en-in/help/3150851/generic-performance-troubleshooting-for-azure-virtual-machine-running)beschrieben sind.|
    |Arbeitsspeicher    |      Verwendung   |    Befolgen Sie die Empfehlungen, die unter [Problembehandlung bei der generischen Leistung für virtuelle Azure-Computer unter Linux oder Windows](https://support.microsoft.com/en-in/help/3150851/generic-performance-troubleshooting-for-azure-virtual-machine-running)beschrieben sind.|
    |Gastfirewall    |      Name der Firewall-Blockierung   |      Führen Sie die folgenden Schritte aus: [Aktivieren oder deaktivieren Sie die Windows Defender Firewall](https://support.microsoft.com/help/4028544/windows-turn-windows-firewall-on-or-off).|
    |DNS-Auflösung     |    Name des DNS     |    Führen Sie die folgenden Schritte aus: [Azure DNS Leitfaden zur Problembehandlung ](../dns/dns-troubleshoot.md) und [Namensauflösung für Ressourcen in virtuellen Azure-Netzwerken aus](./virtual-networks-name-resolution-for-vms-and-role-instances.md).     |
    |Socketfehler    |      Nicht zutreffend   |     Der angegebene Port wird bereits von einer anderen Anwendung verwendet. Versuchen Sie, einen anderen Port zu verwenden.    |

3. Führen Sie die Konnektivitätsprüfung erneut aus, um zu ermitteln, ob das Problem behoben wurde.

### <a name="azure-vm-cannot-connect-to-the-second-network-adapter-of-an-azure-vm-in-same-virtual-network"></a>Eine Azure-VM kann keine Verbindung mit dem zweiten Netzwerkadapter eines virtuellen Azure-Computers in demselben virtuellen Netzwerk herstellen

#### <a name="step-1-make-sure-that-the-second-network-adapter-is-enabled-to-talk-outside-the-subnet"></a>Schritt 1: Stellen Sie sicher, dass der zweite Netzwerkadapter aktiviert ist, um außerhalb des Subnetzes zu kommunizieren.

Standardmäßig sind sekundäre Netzwerkadapter (auch als Netzwerkschnittstellenkarten oder Netzwerkadapter bekannt) nicht so konfiguriert, dass sie über ein Standardgateway verfügen. Daher ist der Datenverkehrsfluss auf dem sekundären Adapter auf dasselbe Subnetz beschränkt.

![IP-Konfigurationen](media/troubleshoot-vm-connectivity/ipconfig.png)

Wenn die Benutzer die Kommunikation der sekundären Netzwerkadapter mit Adressen außerhalb ihres eigenen Subnetzes zulassen möchten, müssen sie, der Routingtabelle einen Eintrag hinzufügen, mit dem das Gateway konfiguriert wird. Gehen Sie hierzu folgendermaßen vor:

1. Öffnen Sie auf dem virtuellen Computer, auf dem der zweite Netzwerkadapter konfiguriert ist, ein Eingabeaufforderungsfenster als Administrator.
2. Führen Sie den folgenden Befehl aus, um den Eintrag in der Routingtabelle hinzuzufügen:

    ```cmd
    Route add 0.0.0.0 mask 0.0.0.0 -p <Gateway IP>
    ```

    Wenn die zweite IP-Adresse beispielsweise 192.168.0.4 lautet, sollte die Gateway-IP-Adresse 192.168.0.1 sein. Dazu müssen Sie den folgenden Befehl ausführen:

    ```cmd
    Route add 0.0.0.0 mask 0.0.0.0 -p 192.168.0.1
    ```

3. Ausführen des Routendrucks. Wenn der Eintrag erfolgreich hinzugefügt wurde, wird ein Eintrag angezeigt, der dem folgenden ähnelt:

    ![IP-Route](media/troubleshoot-vm-connectivity/iproute.png)

Versuchen Sie nun, eine Verbindung mit dem sekundären Netzwerkadapter herzustellen. Wenn die Verbindung weiterhin nicht erfolgreich ist, fahren Sie mit dem nächsten Schritt fort.

#### <a name="step-2-check-nsg-settings-for-the-network-adapters"></a>Schritt 2: Überprüfen Sie die NSG-Einstellungen für die Netzwerkadapter.

Überprüfen Sie für die primären und sekundären Netzwerkadapter die Standardregeln für die eingehenden Ports (Eingehendes VNET zulassen, Load Balancer zulassen) auf beiden Netzwerkadaptern. Sie sollten auch sicherstellen, dass keine übereinstimmenden Blockierungsregeln vorhanden sind, die über einer Regel mit niedrigerer Priorität verfügen.

![Screenshot der die Netzwerkeinstellungen für einen virtuellen Computer, auf dem „V NET eingehend zulassen“ und „V NET ausgehend zulassen“ angezeigt werden.](media/troubleshoot-vm-connectivity/nsg.png)

#### <a name="step-3-run-a-connectivity-check-to-the-secondary-network-adapter"></a>Schritt 3: Führen Sie eine Konnektivitätsprüfung für den sekundären Netzwerkadapter aus.

1. Führen Sie eine Konnektivitätsprüfung für den sekundären Netzwerkadapter aus.
2. Führen Sie eine Umgebungsübergreifende Konnektivitätsprüfung aus, um sicherzustellen, dass der Prozess End-to-End funktioniert.

Weitere Informationen zum Ausführen der Konnektivitätsprüfung finden Sie in den folgenden Artikeln:

**Windows**: [Überprüfen der Konnektivität mit Azure Network Watcher mithilfe von PowerShell](../network-watcher/network-watcher-connectivity-powershell.md)

**Linux**: [Überprüfen der Konnektivität mit Azure Network Watcher mithilfe der Azure CLI 2.0](../network-watcher/network-watcher-connectivity-cli.md).

Hier sehen Sie eine Beispielantwort:

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
```

#### <a name="step-4-refer-the-table-under-step-5-and-follow-these-steps-to-resolve-the-issues"></a>Schritt 4: Lesen Sie die Tabelle unter [Schritt 5](#step-5-fix-the-issue-in-the-connectivity-check-result)und führen Sie die folgenden Schritte aus, um die Probleme zu beheben.

### <a name="azure-vm-cannot-connect-to-the-internet"></a>Eine Azure-VM kann keine Verbindung mit dem Internet herstellen

#### <a name="step-1-check-whether-the-network-adapter-is-in-a-failed-state"></a>Schritt 1: Überprüfen Sie, ob sich der Netzwerkadapter in einem fehlerhaften Zustand befindet.

Führen Sie die folgenden Schritte aus, um den Status der NIC zu überprüfen:

1. Melden Sie sich beim Ressourcen Explorer-Portal an.
2. Wählen Sie im linken Bereich die Option **Abonnements** aus.
3. Wählen Sie im linken Bereich die Ressourcengruppe aus, zu der der betroffene Netzwerkadapter oder der virtuelle Computer gehört.
4. Gehen Sie zum **Microsoft-Netzwerk**.
5. Wählen Sie die Option **Netzwerkschnittstellen** aus.
6. Wählen Sie die betroffene Netzwerkschnittstelle aus.
7. Wählen Sie oben im Portal die Option **Lesen/Schreiben** aus.
8. Wählen Sie die Option **Bearbeiten** aus.

    ![NIC1](media/troubleshoot-vm-connectivity/nicedit1.png)

    > [!NOTE]
    > Nachdem Sie die Option **Bearbeiten** ausgewählt haben, ändert sich die Option „Beziehen“ in eine **Platzieren** Option.

    ![NIC2](media/troubleshoot-vm-connectivity/nicedit2.png)

9. Wählen Sie die betroffene Netzwerkschnittstelle und dann die Option **Put** aus.

    > [!NOTE]
    > Nachdem Sie diese Auswahl getroffen haben, wird der **ProvisioningState** als wird **aktualisiert** angezeigt. Das gleiche Ergebnis wird in dem regulären Azure Resource Manager-Portal angezeigt. Wenn der Vorgang erfolgreich abgeschlossen wurde, ändert sich der **ProvisioningState-Wert** wie gezeigt in **Erfolgreich**.
10. Aktualisieren Sie Ihr Portal. Der Netzwerkadapter sollte sich im Erfolgszustand befinden.

#### <a name="step-2-follow-step-4-to-run-a-connectivity-check"></a>Schritt 2: Führen Sie [Schritt 4](#step-4-perform-a-connectivity-check) aus, um eine Konnektivitätsprüfung auszuführen.

#### <a name="step-3-refer-the-table-under-step-5-and-follow-the-steps-to-resolve-the-issues"></a>Schritt 3: Lesen Sie die Tabelle unter [Schritt 5](#step-5-fix-the-issue-in-the-connectivity-check-result)und führen Sie die folgenden Schritte aus, um die Probleme zu beheben.

## <a name="next-steps"></a>Nächste Schritte

[Problembehandlung bei Konnektivitätsproblemen zwischen Azure-VMs](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)
