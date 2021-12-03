---
title: 'Problembehandlung beim Azure Virtual Desktop-Sitzungshost: Azure'
description: Beheben von Problemen beim Konfigurieren von Host-VMs in einer Azure Virtual Desktop-Sitzung.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 4e3b2ab5f775b566a3d820523737200dd3dc5dd3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467072"
---
# <a name="session-host-virtual-machine-configuration"></a>Konfiguration des virtuellen Sitzungshostcomputers

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md).

In diesem Artikel erfahren Sie, wie Sie Probleme behandeln, die bei der Konfiguration von Azure Virtual Desktop-Sitzungshost-VMs auftreten.

## <a name="provide-feedback"></a>Feedback geben

In der [Azure Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/bd-p/AzureVirtualDesktopForum) können Sie sich mit dem Produktteam und aktiven Communitymitgliedern über den Azure Virtual Desktop-Dienst austauschen.

## <a name="vms-arent-joined-to-the-domain"></a>Virtuelle Computer werden der Domäne nicht hinzugefügt.

Gehen Sie wie hier beschrieben vor, wenn Sie Probleme beim Einbinden von virtuellen Computern in die Domäne haben.

- Fügen Sie die VM manuell über den unter [Hinzufügen eines virtuellen Windows Server-Computers zu einer verwalteten Domäne](../active-directory-domain-services/join-windows-vm.md) beschriebenen Prozess hinzu, oder verwenden Sie die [Vorlage für den Domänenbeitritt](https://azure.microsoft.com/resources/templates/vm-domain-join-existing/).
- Versuchen Sie, den Domänennamen über eine Befehlszeile auf dem virtuellen Computer zu pingen.
- Überprüfen Sie die Liste der Fehlermeldungen zum Domänenbeitritt unter [Fehlermeldungen bei der Problembehandlung von Domänenbeitritten](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Error: Falsche Anmeldeinformationen

**Ursache:** Es gab einen Schreibfehler, als die Anmeldeinformationen in die Schnittstellenkorrekturen der Azure Resource Manager-Vorlage eingegeben wurden.

**Behebung:** Führen Sie eine der folgenden Aktionen aus, um dieses Problem zu beheben.

- Fügen Sie die virtuellen Computer einer Domäne manuell hinzu.
- Stellen Sie die Vorlage erneut bereit, nachdem die Anmeldeinformationen bestätigt wurden. Siehe [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md).
- Fügen Sie einer Domäne VMs mithilfe einer Vorlage zum [Hinzufügen eines vorhandenen virtuellen Windows-Computers zu einer AD-Domäne](https://azure.microsoft.com/resources/templates/vm-domain-join-existing/) hinzu.

### <a name="error-timeout-waiting-for-user-input"></a>Error: Timeout beim Warten auf eine Benutzereingabe.

**Ursache:** Das Konto, dar zum Abschließen des Domänenbeitritts verwendet wird, nutzt möglicherweise mehrstufige Authentifizierung (MFA).

**Behebung:** Führen Sie eine der folgenden Aktionen aus, um dieses Problem zu beheben.

- Entfernen Sie vorübergehend MFA für das Konto.
- Verwenden Sie ein Dienstkonto.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Error: Das während der Bereitstellung verwendete Konto ist nicht berechtigt, den Vorgang abzuschließen.

**Ursache:** Das verwendete Konto besitzt aufgrund von Compliance und Vorschriften keine Berechtigungen, der Domäne VMs hinzuzufügen.

**Behebung:** Führen Sie eine der folgenden Aktionen aus, um dieses Problem zu beheben.

- Verwenden Sie ein Konto, das Mitglied der Gruppe „Administratoren“ ist.
- Erteilen Sie dem verwendeten Konto die erforderlichen Berechtigungen.

### <a name="error-domain-name-doesnt-resolve"></a>Error: Der Domänenname wird nicht aufgelöst.

**Ursache 1:** VMs befinden sich in einem virtuellen Netzwerk, das nicht dem virtuellen Netzwerk (VNET) zugeordnet ist, in dem sich die Domäne befindet.

**Behebung 1:** Erstellen Sie VNET-Peering zwischen dem VNET, in dem VMs bereitgestellt wurden, und dem VNET, in dem der Domänencontroller (DC) ausgeführt wird. Siehe [Erstellen eines Peerings in virtuellen Netzwerken: Resource Manager, verschiedene Abonnements](../virtual-network/create-peering-different-subscriptions.md).

**Ursache 2:** Bei Verwendung von Azure Active Directory Domain Services (Azure AD DS) werden die DNS-Servereinstellungen für das virtuelle Netzwerk nicht so aktualisiert, dass sie auf die verwalteten Domänencontroller verweisen.

**Behebung 2:** Informationen zum Aktualisieren der DNS-Einstellungen für das virtuelle Netzwerk mit Azure AD DS finden Sie unter [Aktualisieren der DNS-Einstellungen für das virtuelle Azure-Netzwerk](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Ursache 3:** Die DNS-Servereinstellungen der Netzwerkschnittstelle zeigen nicht auf den entsprechenden DNS-Server im virtuellen Netzwerk.

**Behebung 3:** Führen Sie eine der folgenden Aktionen aus, um das Problem zu beheben, und befolgen Sie dabei die Schritte unter [Ändern von DNS-Servern].
- Ändern Sie die DNS-Servereinstellungen der Netzwerkschnittstelle anhand der Schritte unter [Ändern von DNS-Servern](../virtual-network/virtual-network-network-interface.md#change-dns-servers) in **Benutzerdefiniert**, und geben Sie die privaten IP-Adressen der DNS-Server im virtuellen Netzwerk an.
- Ändern Sie die DNS-Servereinstellungen der Netzwerkschnittstelle über die Schritte unter [Ändern von DNS-Servern](../virtual-network/virtual-network-network-interface.md#change-dns-servers) so, dass sie **vom virtuellen Netzwerk geerbt werden**, und ändern Sie dann die DNS-Servereinstellungen des virtuellen Netzwerks anhand der Schritte unter [Ändern von DNS-Servern](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="azure-virtual-desktop-agent-and-azure-virtual-desktop-boot-loader-arent-installed"></a>Der Azure Virtual Desktop-Agent und das Azure Virtual Desktop-Startladeprogramm sind nicht installiert.

Die empfohlene Methode zur Bereitstellung virtueller Computer ist die Verwendung der Erstellungsvorlage aus dem Azure-Portal. Die Vorlage installiert automatisch den Azure Virtual Desktop-Agent und das Azure Virtual Desktop-Agent-Startladeprogramm.

Befolgen Sie diese Anweisungen, um die Installation der Komponenten zu bestätigen und nach Fehlermeldungen zu suchen.

1. Vergewissern Sie sich, dass die beiden Komponenten installiert sind, indem Sie **Systemsteuerung** > **Programme** > **Programme und Funktionen** auswählen. Wenn der **Azure Virtual Desktop-Agent** und das **Azure Virtual Desktop-Agent-Startladeprogramm** nicht angezeigt werden, sind sie auf der VM nicht installiert.
2. Öffnen Sie den **Datei-Explorer**, und navigieren Sie zu **C:\Windows\Temp\ScriptLog.log**. Wenn die Datei nicht vorhanden ist, zeigt dies an, dass die PowerShell-DSC, die die beiden Komponenten installiert hat, nicht in der Lage war, im bereitgestellten Sicherheitskontext ausgeführt zu werden.
3. Wenn die Datei **C:\Windows\Temp\ScriptLog.log** vorhanden ist, öffnen Sie sie, und suchen Sie nach Fehlermeldungen.

### <a name="error-azure-virtual-desktop-agent-and-azure-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Fehler: Der Azure Virtual Desktop-Agent und der Azure Virtual Desktop-Agent-Bootloader sind nicht vorhanden. „C:\Windows\Temp\ScriptLog.log“ fehlt ebenfalls.

**Ursache 1:** Die bei der Eingabe für die Azure Resource Manager-Vorlage angegebenen Anmeldeinformationen waren falsch, oder die Berechtigungen waren unzureichend.

**Behebung 1:** Fügen Sie den VMs die fehlenden Komponenten manuell mithilfe von [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md) hinzu.

**Ursache 2:** Die PowerShell-DSC konnte gestartet und ausgeführt, aber nicht abgeschlossen werden, da sie sich nicht bei Azure Virtual Desktop anmelden und die erforderlichen Informationen abrufen kann.

**Behebung 2:** Bestätigen Sie die einzelnen Punkte in der folgenden Liste.

- Stellen Sie sicher, dass das Konto keine MFA verwendet.
- Vergewissern Sie sich, dass der Name des Hostpools korrekt und der Hostpool in Azure Virtual Desktop vorhanden ist.
- Vergewissern Sie sich, dass das Konto mindestens über Berechtigungen vom Typ „Mitwirkender“ für das Azure-Abonnement oder die Ressourcengruppe verfügt.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Error: Authentifizierungsfehler, Fehler in „C:\Windows\Temp\ScriptLog.log“.

**Ursache:** Die PowerShell-DSC konnte ausgeführt werden, aber keine Verbindung mit Azure Virtual Desktop herstellen.

**Behebung:** Bestätigen Sie die einzelnen Punkte in der folgenden Liste.

- Registrieren Sie die VMs manuell beim Azure Virtual Desktop-Dienst.
- Vergewissern Sie sich, dass das für die Verbindung mit Azure Virtual Desktop verwendete Konto im Azure-Abonnement oder in der Ressourcengruppe über Berechtigungen zum Erstellen von Hostpools verfügt.
- Bestätigen Sie, dass das Konto keine MFA verwendet.

## <a name="azure-virtual-desktop-agent-isnt-registering-with-the-azure-virtual-desktop-service"></a>Der Azure Virtual Desktop-Agent ist nicht beim Azure Virtual Desktop-Dienst registriert.

Wenn der Azure Virtual Desktop-Agent zum ersten Mal auf Sitzungshost-VMs installiert wird (entweder manuell oder über die Azure Resource Manager-Vorlage und PowerShell DSC), stellt er ein Registrierungstoken bereit. Im folgenden Abschnitt wird die Behandlung von Problemen im Zusammenhang mit dem Azure Virtual Desktop-Agent und dem Token beschrieben.

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>Error: Der Status im Cmdlet „Get-AzWvdSessionHost“ ist als „Nicht verfügbar“ angegeben.

> [!div class="mx-imgBorder"]
> ![Vom Cmdlet „Get-AzWvdSessionHost“ wird der Status als „Nicht verfügbar“ angezeigt.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Ursache:** Der Agent kann sich nicht selbst auf eine neue Version aktualisieren.

**Behebung:** Führen Sie diese Anweisungen manuell aus, um den Agent zu aktualisieren.

1. Laden Sie eine neue Version des Agents auf die Sitzungshost-VM herunter.
2. Starten Sie den Task-Manager, und beenden Sie auf der Registerkarte „Dienst“ den RDAgentBootLoader-Dienst.
3. Führen Sie das Installationsprogramm für die neue Version des Azure Virtual Desktop-Agents aus.
4. Wenn Sie zur Eingabe des Registrierungstokens aufgefordert werden, entfernen Sie den Eintrag INVALID_TOKEN, und klicken Sie auf „Weiter“ (ein neues Token ist nicht erforderlich).
5. Führen Sie den Installations-Assistenten aus.
6. Öffnen Sie den Task-Manager, und starten Sie den RDAgentBootLoader-Dienst.

## <a name="error-azure-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Fehler: Der Registrierungseintrag IsRegistered des Azure Virtual Desktop-Agents hat den Wert „0“.

**Ursache:** Das Registrierungstoken ist abgelaufen.

**Behebung:** Befolgen Sie diese Anweisungen, um den Fehler in der Agent-Registrierung zu beheben.

1. Sollte bereits ein Registrierungstoken vorhanden sein, entfernen Sie es mit „Remove-AzWvdRegistrationInfo“.
2. Führen Sie das Cmdlet **New-AzWvdRegistrationInfo** aus, um ein neues Token zu generieren.
3. Vergewissern Sie sich, dass der Parameter *-ExpriationTime* auf drei Tage festgelegt ist.

### <a name="error-azure-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>Fehler: Der Azure Virtual Desktop-Agent meldet beim Ausführen von „Get-AzWvdSessionHost“ keinen Heartbeat.

**Ursache 1:** Der RDAgentBootLoader-Dienst wurde beendet.

**Behebung 1:** Starten Sie den Task-Manager, und starten Sie den Dienst, wenn die Registerkarte „Dienst“ einen beendeten Status für den RDAgentBootLoader-Dienst meldet.

**Ursache 2:** Port 443 ist ggf. geschlossen.

**Behebung 2:** Befolgen Sie diese Anweisungen, um Port 443 zu öffnen.

1. Bestätigen Sie, dass Port 443 geöffnet ist, indem Sie das PSPing-Tool von [Sysinternal Tools](/sysinternals/downloads/psping/) herunterladen.
2. Installieren Sie PSPing auf der Sitzungshost-VM, auf der der Agent ausgeführt wird.
3. Öffnen Sie die Eingabeaufforderung als Administrator, und geben Sie den unten gezeigten Befehl aus:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Bestätigen Sie, das PSPing Informationen vom RDBroker zurückerhalten hat:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-azure-virtual-desktop-side-by-side-stack"></a>Problembehandlung beim parallelen Stapel für Azure Virtual Desktop

Der parallele Stapel für Azure Virtual Desktop wird automatisch mit Windows Server 2019 und neuer installiert. Verwenden Sie Microsoft Installer (MSI), um den parallelen Stapel unter Microsoft Windows Server 2016 oder Windows Server 2012 R2 zu installieren. Für Microsoft Windows 10 wird der parallele Stapel für Azure Virtual Desktop mit **enablesxstackrs.ps1** aktiviert.

Es gibt drei Hauptmethoden, wie der parallele Stapel auf Sitzungshostpool-VMs installiert oder aktiviert wird:

- Mit der Erstellungsvorlage aus dem Azure-Portal
- Durch Einbinden in das und Aktivieren im Masterimage.
- Durch manuelles Installieren oder Aktivieren auf jeder VM (oder mit Erweiterungen/PowerShell).

Wenn Sie Probleme mit dem parallelen Stapel von Azure Virtual Desktop haben, geben Sie den Befehl **qwinsta** in die Eingabeaufforderung ein, um zu bestätigen, dass der parallele Stapel installiert oder aktiviert ist.

Die Ausgabe von **qwinsta** listet **rdp-sxs** in der Ausgabe auf, wenn der parallele Stapel installiert und aktiviert ist.

> [!div class="mx-imgBorder"]
> ![Paralleler Stapel installiert oder aktiviert, qwinsta-Ausgabe mit rdp-sxs.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Untersuchen Sie die unten aufgeführten Registrierungseinträge und bestätigen Sie, dass ihre Werte übereinstimmen. Wenn Registrierungsschlüssel fehlen oder die Werte nicht übereinstimmen, stellen Sie sicher, dass Sie [ein unterstütztes Betriebssystem](troubleshoot-agent.md#error-operating-a-pro-vm-or-other-unsupported-os) verwenden. Wenn dies der Fall ist, befolgen Sie die Anweisungen unter [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md) zum erneuten Installieren des parallelen Stapels.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Error: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE-Fehlercode.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Ursache:** Der parallele Stapel ist nicht auf der Sitzungshost-VM installiert.

**Behebung:** Befolgen Sie diese Anweisungen, um den parallelen Stapel auf der Sitzungshost-VM zu installieren.

1. Verwenden Sie RDP (Remote Desktop Protocol), um als lokaler Administrator direkt in die Sitzungshost-VM zu gelangen.
2. Installieren Sie den parallelen Stapel mithilfe von [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-an-azure-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Beheben von Fehlfunktionen eines parallelen Stapels für Azure Virtual Desktop

Es sind Umstände bekannt, die zu einer Fehlfunktion des parallelen Stapels führen können:

- Nichtbefolgen der richtigen Reihenfolge der Schritte zum Aktivieren des parallelen Stapels
- Automatisches Update auf Windows 10 Enhanced Versatile Disc (EVD)
- Fehlende RDSH-Rolle (Remotedesktop-Sitzungshost)
- Mehrmaliges Ausführen von Enablesxsstackrc.ps1
- Ausführen von Enablesxsstackrc.ps1 in einem Konto, das nicht über lokale Administratorrechte verfügt

Mit den Anweisungen in diesem Abschnitt deinstallieren Sie den parallelen Stapel für Azure Virtual Desktop. Nachdem Sie den parallelen Stapel deinstalliert haben, finden Sie unter „Registrieren der VM beim Azure Virtual Desktop-Hostpool“ in [Erstellen eines Hostpools mit PowerShell](create-host-pools-powershell.md) Informationen dazu, wie Sie den parallelen Stapel erneut installieren.

Die VM, mit der die Wiederherstellung durchgeführt wird, muss sich im gleichen Subnetz und in derselben Domäne befinden wie die VM mit dem fehlerhaften parallelen Stapel.

Befolgen Sie diese Anweisungen, um die Fehlerbehebung aus demselben Subnetz und derselben Domäne durchzuführen:

1. Stellen Sie eine Verbindung mit der VM mit dem RDP-Standardprotokoll (Remote Desktop Protocol) her, von der aus die Fehlerkorrektur angewendet wird.
2. Laden Sie PsExec von [https://docs.microsoft.com/sysinternals/downloads/psexec](/sysinternals/downloads/psexec) herunter.
3. Extrahieren Sie die heruntergeladene Datei.
4. Starten Sie eine Eingabeaufforderung als lokaler Administrator.
5. Navigieren Sie zu dem Ordner, in den PsExec extrahiert wurde.
6. Verwenden Sie in der Eingabeaufforderung den folgenden Befehl:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname ist der Computername des virtuellen Computers mit dem fehlerhaften parallelen Stapel.

7. Stimmen Sie dem PsExec-Lizenzvertrag zu, indem Sie auf „Agree“ (Ich stimme zu) klicken.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Softwarelizenzvereinbarung.](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >Dieses Dialogfeld wird nur bei der ersten Ausführung von PsExec angezeigt.

8. Nachdem die Eingabeaufforderungssitzung auf der VM mit dem fehlerhaften parallelen Stapel geöffnet wurde, führen Sie qwinsta aus und bestätigen, dass ein Eintrag namens rdp-sxs verfügbar ist. Wenn dies nicht der Fall ist, ist auf der VM kein paralleler Stapel vorhanden, sodass das Problem nicht durch den parallelen Stapel verursacht wird.

    > [!div class="mx-imgBorder"]
    > ![Administratoreingabeaufforderung](media/AdministratorCommandPrompt.png)

9. Führen Sie den folgenden Befehl aus, der die auf der VM installierten Microsoft-Komponenten mit dem fehlerhaften parallelen Stapel auflistet.

    ```cmd
        wmic product get name
    ```

10. Führen Sie den folgenden Befehl mit Produktnamen aus dem vorherigen Schritt aus.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Deinstallieren Sie alle Produkte, die mit „Remotedesktop“ beginnen.

12. Nachdem alle Azure Virtual Desktop-Komponenten deinstalliert wurden, führen Sie die Anweisungen für Ihr Betriebssystem aus:

13. Wenn Ihr Betriebssystem Windows Server ist, starten Sie die VM neu, die den fehlerhaften parallelen Stapel aufwies (entweder mit dem Azure-Portal oder mit dem PsExec-Tool).

Wenn das Betriebssystem Microsoft Windows 10 ist, fahren Sie mit den folgenden Anweisungen fort:

14. Öffnen Sie auf der VM, die PsExec ausführt, den Datei-Explorer, und kopieren Sie „disablesxsstackrc.ps1“ auf das Systemlaufwerk der VM mit dem fehlerhaften parallelen Stapel.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname ist der Computername des virtuellen Computers mit dem fehlerhaften parallelen Stapel.

15. Empfohlene Vorgehensweise: Starten Sie PowerShell aus dem PsExec-Tool, und navigieren Sie zum Ordner aus dem vorherigen Schritt. Führen Sie dann „disablesxsstackrc.ps1“ aus. Alternativ können Sie die folgenden Cmdlets ausführen:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Wenn die Ausführung der Cmdlets abgeschlossen ist, starten Sie die VM mit dem fehlerhaften parallelen Stapel neu.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Remotedesktop-Lizenzierungsmodus ist nicht konfiguriert

Wenn Sie sich bei Windows 10 Enterprise (mehrere Sitzungen) mit einem Administratorkonto anmelden, erhalten Sie möglicherweise folgende Benachrichtigung: „Der Remotedesktop-Lizenzierungsmodus ist nicht konfiguriert. Die Remotedesktopdienste können in X Tagen nicht mehr ausgeführt werden. Geben Sie mit dem Server-Manager auf dem Verbindungsbrokerserver einen Remotedesktop-Lizenzierungsmodus an.“

Wenn das Zeitlimit abgelaufen ist, wird die folgende Fehlermeldung angezeigt: „Die Verbindung mit der Remotesitzung wurde getrennt, da keine Remotedesktop-Clientzugriffslizenzen für diesen Computer vorhanden sind.“

Wenn eine dieser Meldungen angezeigt wird, bedeutet dies, dass für das Image nicht die neuesten Windows-Updates installiert sind oder Sie den Remotedesktop-Lizenzierungsmodus über Gruppenrichtlinien festlegen. Führen Sie die Schritte in den nächsten Abschnitten aus, um die Gruppenrichtlinieneinstellung zu überprüfen, die Version von Windows 10 Enterprise Multisession zu ermitteln und das entsprechende Update zu installieren.

>[!NOTE]
>Azure Virtual Desktop erfordert nur dann eine RDS-Clientzugriffslizenz (Client Access License, CAL), wenn Ihr Hostpool Windows Server-Sitzungshosts enthält. Informationen zum Konfigurieren einer RDS-CAL finden Sie unter [Lizenzieren deiner RDS-Bereitstellung mit Clientzugriffslizenzen (CALs)](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Deaktivieren der Gruppenrichtlinieneinstellung für den Remotedesktop-Lizenzierungsmodus

Überprüfen Sie die Gruppenrichtlinieneinstellung, indem Sie den Gruppenrichtlinien-Editor in der VM öffnen und zu **Administrative Vorlagen** > **Windows-Komponenten** > **Remotedesktopdienste** > **Remotedesktop-Sitzungshost** > **Lizenzierung** > **Remotedesktop-Lizenzierungsmodus festlegen** navigieren. Wenn die Gruppenrichtlinieneinstellung **Aktiviert** ist, ändern Sie sie in **Deaktiviert**. Wenn sie bereits deaktiviert ist, lassen Sie die Option unverändert.

>[!NOTE]
>Wenn Sie die Gruppenrichtlinie über Ihre Domäne festlegen, deaktivieren Sie diese Einstellung für Richtlinien, die diese Windows 10 Enterprise Multisession-VMs als Ziel verwenden.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Ermitteln, welche Version von Windows 10 Enterprise (mehrere Sitzungen) verwendet wird

Gehen Sie wie folgt vor, um zu ermitteln, mit welcher Version von Windows 10 Enterprise (mehrere Sitzungen) Sie arbeiten:

1. Melden Sie sich mit Ihrem Administratorkonto an.
2. Geben Sie „Info“ in die Suchleiste neben dem Startmenü ein.
3. Wählen Sie **PC-Infos** aus.
4. Überprüfen Sie die Nummer neben „Version“. Die Nummer muss entweder „1809“ oder „1903“ lauten, wie in der folgenden Abbildung dargestellt.

    > [!div class="mx-imgBorder"]
    > ![Screenshot des Fensters für Windows-Spezifikationen. Die Versionsnummer ist blau hervorgehoben.](media/windows-specifications.png)

Nun, da Sie die Versionsnummer wissen, wechseln Sie zu dem entsprechenden Abschnitt.

### <a name="version-1809"></a>Version 1809

Wenn die Versionsnummer „1809“ lautet, installieren Sie das [KB4516077-Update ](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Version 1903

Stellen Sie das Hostbetriebssystem mit der neuesten Windows 10-Imageversion (1903) aus dem Azure-Katalog erneut bereit.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Aufgrund eines Sicherheitsfehlers konnte keine Verbindung mit dem Remotecomputer hergestellt werden.

Wenn für Ihre Benutzer ein Fehler mit folgender Aussage angezeigt wird: „Aufgrund eines Sicherheitsfehlers konnte keine Verbindung mit dem Remotecomputer hergestellt werden. Wenn dies weiterhin auftritt, bitten Sie Ihren Administrator oder den technischen Support um Hilfe“. Überprüfen Sie in diesem Fall alle vorhandenen Richtlinien, die die RDP-Standardberechtigungen ändern. Eine Richtlinie, die diesen Fehler verursachen könnte, ist „Anmeldung über die Sicherheitsrichtlinie für Remotedesktopdienste zulassen“.

Weitere Informationen zu dieser Richtlinie finden Sie unter [Anmeldung über Remotedesktopdienste zulassen](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="i-cant-deploy-the-golden-image"></a>Ich kann das goldene Image nicht bereitstellen.

Goldene Images dürfen den Azure Virtual Desktop-Agent nicht enthalten. Sie können den Agent erst nach dem Bereitstellen des goldenen Image installieren.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht über die Problembehandlung von Azure Virtual Desktop und die Eskalationspfade finden Sie unter [Überblick über Problembehandlung, Feedback und Support](troubleshoot-set-up-overview.md).
- Informationen zur Problembehandlung beim Erstellen eines Hostpools in einer Azure Virtual Desktop-Umgebung finden Sie unter [Mandanten- und Hostpoolerstellung](troubleshoot-set-up-issues.md).
- Informationen zur Problembehandlung bei der Konfiguration eines virtuellen Computers (VM) in Azure Virtual Desktop finden Sie unter [Konfiguration des virtuellen Sitzungshostcomputers](troubleshoot-vm-configuration.md).
- Informationen zur Problembehandlung beim Azure Virtual Desktop-Agent oder der Sitzungskonnektivität finden Sie unter [Beheben häufiger Probleme mit dem Azure Virtual Desktop-Agent](troubleshoot-agent.md).
- Informationen zur Behebung von Problemen bei Azure Virtual Desktop-Clientverbindungen finden Sie unter [Azure Virtual Desktop – Clientverbindungen](troubleshoot-service-connection.md).
- Informationen zur Behebung von Problemen bei Remotedesktop-Clients finden Sie unter [Problembehandlung für den Remotedesktop-Client](troubleshoot-client.md).
- Informationen zur Problembehandlung bei der Verwendung von PowerShell mit Azure Virtual Desktop finden Sie unter [Azure Virtual Desktop – PowerShell](troubleshoot-powershell.md).
- Weitere Informationen zum Dienst finden Sie unter [Azure Virtual Desktop-Umgebung](environment-setup.md).
- Ein Tutorial zur Problembehandlung finden Sie unter [Tutorial: Problembehandlung von Bereitstellungen der Resource Manager-Vorlage](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Informationen zur Überwachung von Aktionen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](../azure-monitor/essentials/activity-log.md).
- Weitere Informationen zu Aktionen zum Bestimmen von Fehlern während der Bereitstellung finden Sie unter [Anzeigen von Bereitstellungsvorgängen mit dem Azure-Portal](../azure-resource-manager/templates/deployment-history.md).
