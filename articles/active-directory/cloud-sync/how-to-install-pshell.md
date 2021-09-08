---
title: Installieren des Azure AD Connect Cloudbereitstellungs-Agents mithilfe einer Befehlszeilenschnittstelle (Command-Line Interface, CLI) und PowerShell
description: Erfahren Sie, wie Sie den Azure AD Connect-Cloudbereitstellungs-Agent mithilfe von PowerShell-Cmdlets installieren.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c150f4bf18f9c4f5b9215122a45df1de32f48c32
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506239"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-by-using-a-cli-and-powershell"></a>Installieren des Azure AD Connect-Bereitstellungs-Agents mit CLI und PowerShell
In diesem Artikel erfahren Sie, wie Sie den Azure AD Connect-Bereitstellungs-Agent (Azure Active Directory) mithilfe von PowerShell-Cmdlets installieren.
 
>[!NOTE]
>In diesem Artikel wird die Installation des Bereitstellungs-Agents über die Befehlszeilenschnittstelle (Command-Line Interface, CLI) behandelt. Informationen zum Installieren des Azure AD Connect-Bereitstellungs-Agents mithilfe des Assistenten finden Sie unter [Installieren des Azure AD Connect-Bereitstellungs-Agent](how-to-install.md).

## <a name="prerequisite"></a>Voraussetzung

Auf dem Windows-Server muss TLS 1.2 aktiviert sein, bevor Sie den Azure AD Connect-Bereitstellungs-Agent mithilfe von PowerShell-Cmdlets installieren können. Um TLS 1.2 zu aktivieren, führen Sie die Schritte unter [Voraussetzungen für die Azure AD Connect-Cloudsynchronisierung](how-to-prerequisites.md#tls-requirements) aus.

>[!IMPORTANT]
>Die folgenden Installationsanweisungen setzen voraus, dass alle [Voraussetzungen](how-to-prerequisites.md) erfüllt sind.

## <a name="install-the-azure-ad-connect-provisioning-agent-by-using-powershell-cmdlets"></a>Installieren des Azure AD Connect-Bereitstellungs-Agents mit PowerShell-Cmdlets 

 1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Azure Active Directory**.
 1. Wählen Sie im Menü im linken Bereich **Azure AD Connect** aus.
 1. Wählen Sie **Bereitstellung verwalten (Vorschau)**  > **Alle Agents überprüfen** aus.
 1. Laden Sie den Azure AD Connect-Bereitstellungs-Agent über das Azure-Portal herunter.

    ![Screenshot: Herunterladen des lokalen Agent.](media/how-to-install/install-9.png)</br>

 1. Für diesen Leitfaden wurde der Agent in „C:\ProvisioningSetup“ heruntergeladen. 
 1. Installieren Sie den Bereitstellungs-Agent im stillen Modus.

       ```
       $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
       $installerProcess.WaitForExit()  
       ```
 1. Importieren Sie das PS-Modul für den Bereitstellungs-Agent.

       ```
       Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
       ```
 1. Stellen Sie mit den Anmeldeinformationen eines globalen Administrators eine Verbindung mit Azure AD her. Sie können diesen Abschnitt anpassen, um ein Kennwort aus einem sicheren Speicher abzurufen. 

       ```
       $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 
    
       $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
       
       Connect-AADCloudSyncAzureAD -Credential $globalAdminCreds 
       ```
 1. Fügen Sie das gMSA-Konto hinzu, und geben Sie die Anmeldeinformationen des Domänenadministrators an, um das Standard-gMSA-Konto zu erstellen.
 
       ```
       $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 
    
       Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
       ```
 1. Geben Sie alternativ mit dem obigen Cmdlet ein vorab erstelltes gMSA-Konto an.
 
       ```
       Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
       ```
 1. Fügen Sie den Domänennamen hinzu.

       ```
       $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 
    
       $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
       ```
 1. Konfigurieren Sie alternativ mit dem obigen Cmdlet bevorzugte Domänencontroller.

       ```
       $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 
    
       Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
       ```
 1. Wiederholen Sie den vorherigen Schritt, um weitere Domänen hinzuzufügen. Geben Sie die Kontonamen und Domänennamen der jeweiligen Domänen an.
 
 1. Starten Sie den Dienst neu.
 
       ```
       Restart-Service -Name AADConnectProvisioningAgent  
       ```
 1. Wechseln Sie zum Azure-Portal, um die Konfiguration für die Cloudsynchronisierung zu erstellen.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>gMSA-PowerShell-Cmdlets für den Bereitstellungs-Agent
Nachdem Sie den Agent installiert haben, können Sie präzisere Berechtigungen auf das gMSA anwenden. Weitere Informationen und Schrittanleitungen zum Konfigurieren der Berechtigungen finden Sie unter [gMSA-PowerShell-Cmdlets für den Agent für die Azure AD Connect-Cloudbereitstellung](how-to-gmsa-cmdlets.md).

## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [gMSA-PowerShell-Cmdlets für den Agent für die Azure AD Connect-Cloudbereitstellung](how-to-gmsa-cmdlets.md)
- [Worum handelt es sich bei der Azure AD Connect-Cloudsynchronisierung?](what-is-cloud-sync.md)