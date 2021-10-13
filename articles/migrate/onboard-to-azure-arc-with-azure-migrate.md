---
title: Onboarding lokaler Server in der virtuellen VMware-Umgebung in Azure Arc
description: Onboarding lokaler Server in der virtuellen VMware-Umgebung in Azure Arc
author: deseelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 04/27/2021
ms.openlocfilehash: 83bee24ca8da03f6a6896bb47cc298860f941f4c
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129711976"
---
# <a name="onboard-on-premises-servers-in-vmware-virtual-environment-to-azure-arc"></a>Onboarding lokaler Server in der virtuellen VMware-Umgebung in Azure Arc   

In diesem Artikel wird beschrieben, wie Sie lokale VMware-VMs mithilfe des Tools Azure Migrate: Ermittlung und Bewertung für die Azure-Verwaltung in Azure Arc integrieren. 

Mit Azure Arc können Sie Ihre Hybrid-IT-Infrastruktur auf einer zentralen Benutzeroberfläche verwalten, indem Sie die Azure-Verwaltungsfunktionen auf Ihre lokalen Server ausdehnen, die nicht für die Migration geeignet sind. [Weitere Informationen zu Azure Arc](../azure-arc/servers/overview.md) 

## <a name="before-you-get-started"></a>Bevor Sie beginnen

- [Sehen Sie sich die Anforderungen an](./tutorial-discover-vmware.md#prerequisites), um die in einer VMware-Umgebung ausgeführten Server mit Azure Migrate: Ermittlung und Bewertung zu ermitteln.  
- Bereiten Sie [VMware vCenter](./tutorial-discover-vmware.md#prepare-vmware) für die Verwendung vor, und überprüfen Sie die [VMware-Anforderungen](migrate-support-matrix-vmware.md#vmware-requirements) für die Softwareinventur. Die Softwareinventur muss abgeschlossen sein, bevor Sie mit dem Onboarding der ermittelten Server in Azure Arc beginnen können.   
- Sehen Sie sich die [Anforderungen zur Anwendungsermittlung](migrate-support-matrix-vmware.md#software-inventory-requirements) an, bevor Sie die Softwareinventur auf Servern initiieren. Auf Windows-Servern muss PowerShell ab Version 3.0 installiert sein. 
- Überprüfen Sie die Voraussetzungen, um Remoteverbindungen mit dem Bestand der ermittelten Server für das Onboarding dieser Server zu Azure Arc zuzulassen. 
    1. Zulassen eingehender Remoteverbindungen mit den gefundenen Servern 
        - _Für Windows:_ Eingehende Verbindung an WinRM-Port 5985 (HTTP). Führen Sie auf Windows-Zielservern den Befehl „winrm qc“ aus, um das WS-Management-Protokoll auf dem lokalen Computer zu aktivieren. 
        - _Für Linux:_ Lassen Sie auf allen Linux-Zielservern eingehende Verbindungen an Port 22 (SSH) zu.
        - Sie können auch die IP-Adressen der Remotecomputer (ermittelte Server) der WinRM TrustedHosts-Liste auf der Appliance hinzufügen. 
    2. Die Azure Migrate-Appliance sollte über eine direkte Netzwerkverbindung mit den Zielservern verfügen. 
- Sehen Sie sich unbedingt die [Voraussetzungen für Azure Arc](../azure-arc/servers/agent-overview.md#prerequisites) sowie die folgenden Überlegungen an:
    - Das Onboarding in Azure Arc kann erst initiiert werden, nachdem die vCenter Server-Ermittlung und die Softwareinventur abgeschlossen wurden. Die Softwareinventur kann vom Start bis zum Abschluss bis zu sechs Stunden dauern.
    -  Während des Onboardings in Arc wird der [Azure Arc Hybrid Connected Machine-Agent](../azure-arc/servers/learn/quick-enable-hybrid-vm.md) auf den ermittelten Servern installiert. Für die Agent-Installation müssen Sie Anmeldeinformationen mit Administratorberechtigungen auf den Servern angeben. Geben Sie dazu unter Linux das root-Konto und unter Windows ein Konto an, das der Gruppe „Lokale Administratoren“ angehört. 
    - Vergewissern Sie sich, dass auf den Servern ein [unterstütztes Betriebssystem](../azure-arc/servers/agent-overview.md#supported-operating-systems) ausgeführt wird.
    - Stellen Sie sicher, das dem Azure-Konto die [erforderlichen Azure-Rollen](../azure-arc/servers/agent-overview.md#required-permissions) zugewiesen sind.
    - Achten Sie darauf, dass die [erforderlichen URLs](../azure-arc/servers/agent-overview.md#networking-configuration) nicht blockiert werden, wenn die ermittelten Server eine Verbindung über eine Firewall oder einen Proxyserver herstellen, um über das Internet zu kommunizieren.
    - Sehen Sie sich die für Azure Arc [unterstützten Regionen](../azure-arc/servers/overview.md#supported-regions) an. 
    - Server mit Azure Arc-Unterstützung unterstützen bis zu 5.000 Computerinstanzen in einer Ressourcengruppe.


## <a name="set-up-the-azure-migrate-project"></a>Einrichten des Azure Migrate-Projekts  

1. Bereiten Sie vorab das [Azure-Benutzerkonto](./tutorial-discover-vmware.md#prepare-an-azure-user-account) vor, und vergewissern Sie sich, dass Sie über die [erforderlichen Rollen](./create-manage-projects.md#verify-permissions) im Abonnement verfügen, um die für Azure Migrate erforderlichen Ressourcen zu erstellen. 
2. Lesen Sie [diesen Artikel](./create-manage-projects.md) zum Einrichten eines neuen Azure Migrate-Projekts, dem das Tool Azure Migrate: Ermittlung und Bewertung hinzugefügt ist.  

    > [!Note]
    > Sie können auch ein vorhandenes Migrate-Projekt verwenden und für den Bestand an gefundenen Servern ein Onboarding in Azure Arc durchführen. Starten Sie dazu den Appliance-Konfigurations-Manager von Ihrem Applianceserver aus, und vergewissern Sie sich, dass die Dienste auf die neuesten Versionen aktualisiert wurden. [Weitere Informationen](./migrate-appliance.md#appliance-upgrades) <br/> <br/> Befolgen Sie als Nächstes [diese Anweisungen](#onboard-to-azure-arc) für das Onboarding Ihrer Server.  

## <a name="deploy-and-register-the-azure-migrate-appliance"></a>Bereitstellen und Registrieren der Azure Migrate-Appliance 

Für das Azure Migrate-Tool zur Ermittlung und Bewertung wird eine einfache Azure Migrate-Appliance verwendet. Die Appliance führt die Serverermittlung durch und sendet Metadaten zur Serverkonfiguration und -leistung an Azure Migrate. 

Vor dem Einrichten der Appliance: 

1. [Überprüfen Sie die Anforderungen](migrate-appliance.md#appliance---vmware) für die Bereitstellung der Azure Migrate-Appliance.
2. Überprüfen Sie die Azure-URLs, die die Appliance für den Zugriff in [öffentlichen Clouds](migrate-appliance.md#public-cloud-urls) und [Government-Clouds](migrate-appliance.md#government-cloud-urls) benötigt. 
3. Beachten Sie die [Portzugriffsanforderungen](migrate-support-matrix-vmware.md#port-access-requirements) für die Appliance. 


Als Nächstes

- Befolgen Sie diesen Artikel zum [Einrichten der Azure Migrate-Appliance](./tutorial-discover-vmware.md#set-up-the-appliance), um die vCenter Server-Ermittlung zu starten. Zum Bereitstellen der Appliance können Sie eine OVA-Vorlage herunterladen und in VMware importieren, um einen Server zu erstellen, der in Ihrer vCenter Server-Instanz ausgeführt wird.  
- Nach der Bereitstellung der Appliance müssen Sie sie beim Projekt registrieren, bevor Sie die Ermittlung initiieren können. Befolgen Sie [diese Anweisungen](./tutorial-discover-vmware.md#register-the-appliance-with-azure-migrate), um die Appliance zu registrieren. 

## <a name="configure-the-appliance-and-start-discovery"></a>Konfigurieren der Appliance und Starten der Ermittlung  

In [diesem Artikel](./tutorial-discover-vmware.md#start-continuous-discovery) erfahren Sie, wie Sie die Azure Migrate-Appliance konfigurieren und die vCenter Server-Ermittlung starten. 

Beim Konfigurieren der Appliance für die Ermittlung müssen Sie Folgendes im Appliance-Konfigurations-Manager angeben:

- Die Details der vCenter-Server-Instanz, zu der eine Verbindung hergestellt werden soll  
- Anmeldeinformationen für vCenter Server, die für die Ermittlung des Servers in Ihrer VMware-Umgebung ausgelegt sind 
- Serveranmeldeinformationen mit Administratorberechtigungen. Das Onboarding in Azure Arc erfordert Anmeldeinformationen mit Administratorberechtigungen auf dem Server, um den Azure Arc Hybrid Connect Machine-Agent zu installieren und zu konfigurieren. [Erfahren Sie mehr](add-server-credentials.md) zur Bereitstellung von Anmeldeinformationen und zu deren Handhabung.

Nach dem erfolgreichen Abschluss der Ermittlung dauert es etwa 15 Minuten, bis die ermittelten Server im Portal angezeigt werden.


## <a name="onboard-to-azure-arc"></a>Onboarding in Azure Arc

>[!Important]
>Die Softwareinventur muss abgeschlossen sein, bevor Sie mit dem Onboarding der ermittelten Server in Azure Arc beginnen können. 

Nachdem die vCenter Server-Ermittlung abgeschlossen ist, wird automatisch die Softwareinventur (Ermittlung installierter Anwendungen) initiiert. Während der Softwareinventur werden die bereitgestellten Serveranmeldeinformationen durchlaufen und mit den ermittelten Servern überprüft. Sie können das Onboarding starten, nachdem die Softwareinventur abgeschlossen ist und die Anmeldeinformationen zugeordnet wurden. Die Softwareinventur kann vom Start bis zum Abschluss bis zu sechs Stunden dauern.  
1. Navigieren Sie zum Bereich **Onboarding in Azure Arc**. 

    ![Onboarding in Arc](./media/onboard-to-azure-arc-with-azure-migrate/azure-arc-onboarding-panel-after-being-enabled.png)

2. Geben Sie das **Abonnement** und die **Ressourcengruppe** für die Verwaltung des Servers in Azure an.

3. Wählen Sie in der Dropdownliste **Regionen** die Azure-Region aus, in der die Metadaten des Servers gespeichert werden sollen.

4. Geben Sie Details zum **Azure Active Directory-Dienstprinzipal** für das Onboarding im großen Stil an. Lesen Sie diesen Artikel zum [Erstellen eines Dienstprinzipals mithilfe der Azure-Portals oder von Azure PowerShell](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale). <br/>

    Die folgenden Eingaben sind erforderlich:
    - **Verzeichnis-ID (Mandant):** der [eindeutige Bezeichner (GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), der Ihre dedizierte Instanz von Azure AD darstellt 
    - **Anwendungs-ID (Client):** der [eindeutige Bezeichner (GUID)](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in), der die Anwendungs-ID des Dienstprinzipals darstellt
    - **Dienstprinzipalgeheimnis (Anwendungsgeheimnis):** der [geheime Clientschlüssel](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret) für die kennwortbasierte Authentifizierung 
    
5. _Optional:_ Geben Sie die **IP-Adresse des Proxyservers** oder den Namen und die **Portnummer** an, wenn Ihre ermittelten Server einen Proxyserver benötigen, um eine Verbindung mit dem Internet herzustellen. Geben Sie den Wert im Format `http://<proxyURL>:<proxyport>` ein. Dieser Proxyserver, der von den ermittelten Servern verwendet wird, kann sich von dem Proxyserver unterscheiden, der vom Applianceserver für Verbindungen mit dem Internet benötigt wird (im Abschnitt „Voraussetzungen“ im Appliance-Konfigurations-Manager angegeben).   

    > [!Note]
    > Das Festlegen der Proxyauthentifizierung wird nicht unterstützt. 

6. Wählen Sie **Onboarding starten** aus, um den Azure Arc-Onboardingprozess zu initiieren. Der Onboardingprozess nimmt einige Zeit in Anspruch. Nach Abschluss des Onboardings werden der Abschlussstatus und ein detaillierter Onboardingstatusbericht angezeigt.    

    ![Bericht zum Arc-Onboarding](./media/onboard-to-azure-arc-with-azure-migrate/onboarding-completion-report.png)

    > [!Note]
    > Wenn Ihre Anmeldung abläuft, wählen Sie **Erneut anmelden** aus. Dabei wird ein modales Dialogfeld mit dem Gerätecode angezeigt. Wählen Sie **Code kopieren und anmelden** aus, um den Gerätecode zu kopieren und eine Azure-Anmeldeaufforderung auf einer neuen Browserregisterkarte zu öffnen. Sollte keine Anmeldung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.

7. Navigieren Sie nach Abschluss des Onboardings zur [Homepage von Azure Arc](https://portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/servers), um die Server, für die das Onboarding durchgeführt wurde, anzuzeigen und zu verwalten.   

8. Zeigen Sie den detaillierten Onboardingbericht an, um den Onboardingstatus Ihrer Server zu verstehen und entsprechende Maßnahmen zu ergreifen. 

> [!Note]
> Die Azure Migrate-Appliance initiiert WinRM-Sitzungen, um das Azure Arc-Onboardingskript auszuführen. Stellen Sie sicher, dass keine Einstellungen vorhanden sind, die den Zugriff auf den WinRM-Dienst auf den Zielservern einschränken.    


## <a name="next-steps"></a>Nächste Schritte 

- Weitere Informationen und Fehlerdetails finden Sie im detaillierten Onboardingbericht. Beheben Sie eventuell vorhandene Fehler, um das Onboarding der Server durchzuführen. 
- Sie können **Onboarding starten** auswählen, um den Azure Arc-Onboardingprozess zu initiieren. Das Onboarding wird für alle neu ermittelten Server und für Server versucht, die in der vorherigen Ausführung nicht erfolgreich integriert werden konnten.   

## <a name="troubleshooting-azure-arc-onboarding-errors"></a>Problembehandlung beim Azure Arc-Onboarding

Wenn beim Onboarding in Azure Arc mithilfe der Azure Migrate-Appliance ein Fehler auftritt, können Sie mithilfe des folgenden Abschnitts die wahrscheinliche Ursache und die vorgeschlagenen Schritte zum Beheben des Problems ermitteln. 

Wenn der unten aufgeführte Fehlercode nicht angezeigt wird oder wenn dieser mit **_AZCM_** beginnt, lesen Sie [diesen Leitfaden zur Problembehandlung bei Azure Arc](../azure-arc/servers/troubleshoot-agent-onboard.md).

### <a name="error-60001---unabletoconnecttophysicalserver"></a>Fehler 60001: UnableToConnectToPhysicalServer  

**Mögliche Ursachen**  
Entweder sind die [Voraussetzungen](./migrate-support-matrix-physical.md) für die Verbindung mit dem Server nicht erfüllt, oder es liegen Netzwerkprobleme beim Herstellen einer Verbindung mit dem Server vor, z. B. einige Proxyeinstellungen.

**Empfohlene Aktionen**   
- Stellen Sie sicher, dass der Server die [Voraussetzungen](#before-you-get-started) und [Portzugriffsanforderungen](./migrate-support-matrix-physical.md) erfüllt. 
- Fügen Sie die IP-Adressen der Remotecomputer (ermittelte Server) der WinRM TrustedHosts-Liste auf der Azure Migrate Appliance hinzu, und wiederholen Sie den Vorgang. Dies soll eingehende Remoteverbindungen auf Servern zulassen: _Windows:_ WinRM-Port 5985 (HTTP) und _Linux:_ SSH-Port 22 (TCP).
- Vergewissern Sie sich, dass Sie auf der Appliance die richtige Authentifizierungsmethode für die Verbindung mit dem Server ausgewählt haben. 
   > [!Note] 
   > Azure Migrate unterstützt sowohl die kennwortbasierte als auch die auf SSH-Schlüsseln basierende Authentifizierung für Linux-Server.
- Wenn das Problem weiterhin besteht, übermitteln Sie den Fall an den Microsoft-Support, und geben Sie die Computer-ID der Appliance an (zu finden in der Fußzeile des Appliance-Konfigurations-Managers).     
   

### <a name="error-60002---invalidservercredentials"></a>Fehler 60002: InvalidServerCredentials  

**Mögliche Ursachen**  
Die Verbindung mit dem Server kann nicht hergestellt werden. Entweder haben Sie falsche Anmeldeinformationen auf der Appliance angegeben, oder die zuvor angegebenen Anmeldeinformationen sind abgelaufen.

**Empfohlene Aktionen**  
- Vergewissern Sie sich, dass Sie auf der Appliance die richtigen Anmeldeinformationen für den Server angegeben haben. Sie können dies überprüfen, indem Sie versuchen, mit diesen Anmeldeinformationen eine Verbindung mit dem Server herzustellen.
- Wenn die hinzugefügten Anmeldeinformationen falsch oder abgelaufen sind, bearbeiten Sie sie auf der Appliance, und wiederholen Sie die Überprüfung für die hinzugefügten Server. Wenn die Überprüfung erfolgreich ist, wurde das Problem behoben.
- Wenn das Problem weiterhin besteht, übermitteln Sie den Fall an den Microsoft-Support, und geben Sie die Computer-ID der Appliance an (zu finden in der Fußzeile des Appliance-Konfigurations-Managers).

### <a name="error-60005---sshoperationtimeout"></a>Fehler 60005: SSHOperationTimeout  

**Mögliche Ursachen**  
- Der Vorgang dauerte aufgrund von Problemen mit der Netzwerklatenz oder aufgrund fehlender aktueller Updates auf dem Server länger als erwartet. 

**Empfohlene Aktionen**  
- Stellen Sie sicher, dass auf dem betroffenen Server die neuesten Kernel- und Betriebssystemupdates installiert sind.
- Stellen Sie sicher, dass zwischen der Appliance und dem Server keine Netzwerklatenz besteht. Es wird empfohlen, die Appliance und den Quellserver in derselben Domäne zu verwenden, um Latenzprobleme zu vermeiden.
- Stellen Sie von der Appliance aus eine Verbindung mit dem betroffenen Server her, und führen Sie die [hier beschriebenen Befehle](./troubleshoot-appliance.md) aus, um zu überprüfen, ob NULL-Werte oder leere Daten zurückgegeben werden.
- Wenn das Problem weiterhin besteht, übermitteln Sie den Fall an den Microsoft-Support, und geben Sie die Computer-ID der Appliance an (zu finden in der Fußzeile des Appliance-Konfigurations-Managers).  

### <a name="error-60108---softwareinventorycredentialnotassociated"></a>Fehler 60108: SoftwareInventoryCredentialNotAssociated  

**Mögliche Ursachen**  
- Es wurden keine Anmeldeinformationen gefunden, die dem Server zugeordnet sind.

**Empfohlene Aktionen**  
- Die Softwareinventur muss abgeschlossen sein, bevor Sie mit dem Onboarding der ermittelten Server in Azure Arc beginnen können. [Weitere Informationen](./how-to-discover-applications.md#add-credentials-and-initiate-discovery)
- Stellen Sie sicher, dass die im Appliance-Konfigurations-Manager angegebenen Anmeldeinformationen gültig sind und der Zugriff auf den Server mit diesen Anmeldeinformationen möglich ist.
- Wechseln Sie zurück zum Appliance-Konfigurations-Manager, um andere Anmeldeinformationen anzugeben oder die vorhandenen zu bearbeiten.  

### <a name="error-60109---arcosnotsupported"></a>Fehler 60109: ArcOsNotSupported  

**Mögliche Ursachen**  
- Der Server hostet ein für das Onboarding in Azure Arc nicht unterstütztes Betriebssystem.

**Empfohlene Aktionen**  
- Sehen Sie sich die [unterstützten Betriebssysteme](../azure-arc/servers/agent-overview.md#supported-operating-systems) für Azure Arc an. 
 
### <a name="error-10002---scriptexecutiontimedoutonvm"></a>Fehler 10002: ScriptExecutionTimedOutOnVm  

**Mögliche Ursachen**  
- Der Onboardingtask wurde nicht rechtzeitig abgeschlossen. Die Ausführung dauerte dann länger als erwartet. 

**Empfohlene Aktionen**  
- Das Problem sollte sich nach einiger Zeit automatisch lösen. Wenden Sie sich an den Microsoft Support, wenn das Problem weiterhin besteht.  
 
### <a name="error-50000---accessdenied"></a>Fehler 50000: AccessDenied 

**Mögliche Ursachen**  
- Der Vorgang konnte aufgrund eines unzulässigen Zugriffs auf den Server nicht abgeschlossen werden. Das Benutzerkonto, das auf der Appliance für den Zugriff auf den Server bereitgestellt wurde, verfügt nicht über die erforderlichen Berechtigungen, oder die Anmeldeinformationen sind falsch. WinRM-Fehlercode: 0x80070005

**Empfohlene Aktionen**  
- Überprüfen Sie die möglichen Ursachen, und wiederholen Sie den Vorgang. Wenden Sie sich an den Support, wenn das Problem weiterhin besteht.

### <a name="error-9609516000960078---nullresultunhandledexceptionserverunknownerrorunhandlederror"></a>Fehler 960/951/60009/60078: NullResult/UnhandledException/ServerUnknownError/UnhandledError

**Mögliche Ursachen**  
- Bei dem Vorgang ist ein interner Fehler aufgetreten. 

**Empfohlene Aktionen**  
- Wiederholen Sie den Vorgang nach einiger Zeit. Wenn das Problem weiterhin besteht, wenden Sie sich an den Support, und geben Sie die Computer-ID der Appliance an (zu finden in der **Fußzeile** des Appliance-Konfigurations-Managers).
