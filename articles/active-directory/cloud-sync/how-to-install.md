---
title: Installieren des Azure AD Connect-Bereitstellungs-Agents
description: Erfahren Sie, wie Sie den Azure AD Connect-Bereitstellungs-Agent installieren und im Azure-Portal konfigurieren.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 294e66b8b7cef3cec9931b5369562a51b138ab28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448669"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installieren des Azure AD Connect-Bereitstellungs-Agents
In diesem Artikel erhalten Sie Informationen zum Installationsvorgang für den Azure AD Connect-Bereitstellungs-Agent (Azure Active Directory) und dessen Erstkonfiguration im Azure-Portal.

>[!IMPORTANT]
>Die folgenden Installationsanweisungen setzen voraus, dass alle [Voraussetzungen](how-to-prerequisites.md) erfüllt sind.

>[!NOTE]
>In diesem Artikel wird die Installation des Bereitstellungs-Agents mithilfe des Assistenten behandelt. Informationen zum Installieren des Azure AD Connect-Bereitstellungs-Agents mithilfe einer Befehlszeilenschnittstelle (CLI) finden Sie unter [Installieren des Azure AD Connect Bereitstellungs-Agents mithilfe einer CLI und PowerShell](how-to-install-pshell.md).

Weitere Informationen und ein Beispiel finden Sie im folgenden Video.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RWK5mR]

## <a name="group-managed-service-accounts"></a>Gruppenverwaltete Dienstkonten
Ein gruppenverwaltetes Dienstkonto (group Managed Service Account, gMSA) ist ein verwaltetes Domänenkonto, das eine automatische Kennwortverwaltung, eine vereinfachte Verwaltung von Dienstprinzipalnamen (Service Principal Name, SPN) und die Möglichkeit bietet, die Verwaltung an andere Administratoren zu delegieren. Außerdem wird diese Funktionalität auf mehrere Server erweitert. Die Azure AD Connect-Cloudsynchronisierung unterstützt und empfiehlt die Verwendung eines gruppenverwalteten Dienstkontos für die Ausführung des Agents. Weitere Informationen über gruppenverwaltete Dienstkonten finden Sie unter [Übersicht zu gruppenverwalteten Dienstkonten](how-to-prerequisites.md#group-managed-service-accounts).


### <a name="upgrade-an-existing-agent-to-use-the-gmsa"></a>Aktualisieren eines vorhandenen Agents für die gMSA-Verwendung
Um einen vorhandenen Agent für die Verwendung des während der Installation erstellten gruppenverwalteten Dienstkontos zu aktualisieren, müssen Sie einfach den Agent-Dienst auf die neueste Version aktualisieren, indem Sie die Datei „AADConnectProvisioningAgent.msi“ ausführen. Führen Sie jetzt den Installations-Assistenten erneut aus, und geben Sie bei entsprechender Aufforderung die Anmeldeinformationen ein, um das Konto zu erstellen.

## <a name="install-the-agent"></a>Installieren des Agents

So installieren Sie den Agent:

 1. Melden Sie sich mit Berechtigungen eines Unternehmensadministrators bei dem Server an, den Sie verwenden werden.
 1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Azure Active Directory**.
 1. Wählen Sie im Menü im linken Bereich **Azure AD Connect** aus.
 1. Wählen Sie **Cloudsynchronisierung verwalten** > **Alle Agents überprüfen** aus.
 1. Laden Sie den Azure AD Connect-Bereitstellungs-Agent über das Azure-Portal herunter.
 
    ![Screenshot: Herunterladen des lokalen Agents.](media/how-to-install/install-9.png)</br>
 1. Akzeptieren Sie die Geschäftsbedingungen, und wählen Sie **Herunterladen** aus.
 1. Führen Sie das Installationsprogramm für den Azure AD Connect-Bereitstellungs-Agent („AADConnectProvisioningAgentSetup.msi“) aus.
 1. Akzeptieren Sie auf dem Bildschirm **Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket** die Lizenzbedingungen, und wählen Sie **Installieren** aus.
 
    ![Screenshot des Bildschirms „Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket“.](media/how-to-install/install-1.png)</br>
 1. Nach Abschluss dieses Vorgangs wird der Konfigurations-Assistent gestartet. Melden Sie sich mit dem Konto Ihres globalen Azure AD-Administrators an.
 1. Wählen Sie auf dem Bildschirm **Dienstkonto konfigurieren** entweder die Option **GMSA erstellen** oder **Benutzerdefiniertes GMSA verwenden** aus. Wenn Sie das Konto vom Agent erstellen lassen, wird dem Konto der Name provAgentgMSA$ zugewiesen. Wenn Sie **Benutzerdefiniertes gMSA verwenden** angeben, werden Sie aufgefordert, dieses Konto anzugeben.
 1. Geben Sie die Anmeldeinformationen des Domänenadministrators ein, um das gruppenverwaltete Dienstkonto zu erstellen, das zum Ausführen des Agent-Diensts verwendet wird. Klicken Sie auf **Weiter**.
  
    ![Screenshot: Option „gMSA erstellen“.](media/how-to-install/install-12.png)</br>
 1. Wählen Sie auf dem Bildschirm **Active Directory verbinden** die Option **Verzeichnis hinzufügen** aus. Melden Sie sich dann mit Ihrem Active Directory-Administratorkonto an. Dadurch wird Ihr lokales Verzeichnis hinzugefügt. 
 1. Optional können Sie die Präferenz der vom Agent verwendeten Domänencontroller verwalten, indem Sie das Kontrollkästchen **Domänencontrollerpriorität auswählen** aktivieren und die Liste der Domänencontroller entsprechend sortieren. Klicken Sie auf **OK**.
 
    ![Screenshot: Sortieren der Domänencontroller.](media/how-to-install/install-2a.png)</br>
 1. Klicken Sie auf **Weiter**.
 
    ![Screenshot: Bildschirm „Active Directory verbinden“.](media/how-to-install/install-3a.png)</br>
 1. Überprüfen Sie die Einstellungen auf dem Bildschirm **Agent-Installation** und das zu erstellende Konto, und wählen Sie dann **Bestätigen** aus.
 
    ![Screenshot: „Bestätigen“ der Einstellungen.](media/how-to-install/install-11.png)</br>
 1. Nach Abschluss dieses Vorgangs sollte die Meldung **Ihre Agent-Installation ist abgeschlossen** angezeigt werden. Wählen Sie **Beenden** aus.
 
    ![Screenshot: Bildschirm „Konfiguration abgeschlossen“.](media/how-to-install/install-4a.png)</br>
 1. Wenn weiterhin der erste Bildschirm **Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket** angezeigt wird, wählen Sie **Schließen** aus.

## <a name="verify-agent-installation"></a>Überprüfen der Agent-Installation
Die Agent-Überprüfung erfolgt im Azure-Portal und auf dem lokalen Server, auf dem der Agent ausgeführt wird.

### <a name="azure-portal-agent-verification"></a>Agent-Überprüfung im Azure-Portal
So überprüfen Sie, ob der Agent von Azure erkannt wird:

 1. Melden Sie sich beim Azure-Portal an.
 1. Wählen Sie auf der linken Seite **Azure Active Directory** > **Azure AD Connect** aus. Wählen Sie in der Mitte die Option **Cloudsynchronisierung verwalten** aus.

    ![Screenshot des Azure-Portals](media/how-to-install/install-6.png)</br>

 1. Wählen Sie auf dem Bildschirm **Azure AD Connect-Cloudsynchronisierung** die Option **Alle Agents überprüfen** aus.

    ![Screenshot: Option „Alle Agents überprüfen“.](media/how-to-install/install-7.png)</br>
 
 1. Auf dem Bildschirm **Lokale Bereitstellungs-Agents** werden die von Ihnen installierten Agents angezeigt. Vergewissern Sie sich, dass der betreffende Agent aufgeführt wird und als *Aktiviert* markiert ist.

    ![Screenshot: Bildschirm „Lokale Bereitstellungs-Agents“.](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>Auf dem lokalen Server
So überprüfen Sie, ob der Agent ausgeführt wird:

1. Melden Sie sich beim Server mit einem Administratorkonto an.
1. Öffnen Sie **Dienste**, indem Sie zu dieser Seite navigieren oder **Start** > **Ausführen** > **Services.msc** auswählen.
1. Vergewissern Sie sich, dass unter **Dienste** die Dienste **Microsoft Azure AD Connect Agent Updater** und **Microsoft Azure AD Connect Provisioning Agent** mit dem Status *Wird ausgeführt* angezeigt werden.

    ![Screenshot: Bildschirm „Dienste“.](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>Nachdem Sie den Agent installiert haben, muss er konfiguriert und aktiviert werden, bevor er mit der Synchronisierung von Benutzern beginnen kann. Informationen zum Konfigurieren eines neuen Agents finden Sie unter [Erstellen einer neuen Konfiguration für die Azure AD Connect-Cloudsynchronisierung](how-to-configure.md).

### <a name="enable-password-writeback-in-azure-ad-connect-cloud-sync"></a>Aktivieren des Kennwortrückschreibens in der Azure AD Connect-Cloudsynchronisierung 

Um das Kennwortrückschreiben zu verwenden und den SSPR-Dienst zum Erkennen des Cloudsynchronisierungs-Agents zu aktivieren, müssen Sie das Cmdlet `Set-AADCloudSyncPasswordWritebackConfiguration` und die Anmeldeinformationen des globalen Administrators des Mandanten verwenden: 

  ```   
   Import-Module "C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll" 
   Set-AADCloudSyncPasswordWritebackConfiguration -Enable $true -Credential $(Get-Credential)
  ```

Weitere Informationen zur Verwendung des Kennwortrückschreibens mit der Azure AD Connect-Cloudsynchronisierung finden Sie unter.


## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?](what-is-cloud-sync.md)
- [Erstellen einer neuen Konfiguration für die Azure AD Connect-Cloudsynchronisierung](how-to-configure.md)

