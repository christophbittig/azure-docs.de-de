---
title: Anmelden bei einem virtuellen Windows-Computer in Azure mithilfe von Azure Active Directory
description: Azure AD-Anmeldung bei einem virtuellen Azure-Computer unter Windows
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 08/19/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli, subject-rbac-steps
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec984ebaa6b12019b1f1942d2849f7e9efaf288
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049784"
---
# <a name="login-to-windows-virtual-machine-in-azure-using-azure-active-directory-authentication"></a>Anmelden bei einem virtuellen Windows-Computer in Azure mit der Azure Active Directory-Authentifizierung

Organisationen können jetzt die Sicherheit von virtuellen Windows-Computern (VMs) in Azure verbessern, indem sie die Azure Active Directory-Authentifizierung integrieren. Sie können jetzt Azure AD als Hauptauthentifizierungsplattform für RDP in einer **Windows Server 2019 Datacenter-Edition** oder unter **Windows 10 1809** und höher verwenden. Darüber hinaus können Sie die rollenbasierte Zugriffssteuerung (RBAC) von Azure und die Richtlinien für bedingten Zugriff, die den Zugriff auf virtuelle Computer zulassen oder verweigern, zentral steuern und erzwingen. In diesem Artikel wird beschrieben, wie Sie einen virtuellen Windows-Computer erstellen und konfigurieren und sich mithilfe der Azure AD-gestützten Authentifizierung anmelden.

Die Verwendung der Azure AD-gestützten Authentifizierung für die Anmeldung bei virtuellen Windows-Computern in Azure bietet in Bezug auf die Sicherheit viele Vorteile. Dazu zählen:
- Verwenden der AD-Anmeldeinformationen Ihres Unternehmens zum Anmelden bei virtuellen Windows-Computern in Azure.
- Reduzieren der Abhängigkeit von lokalen Administratorkonten. Sie müssen sich nicht damit auseinandersetzen, dass Anmeldeinformationen verloren gehen oder gestohlen werden, dass Benutzer unsichere Anmeldeinformationen konfigurieren usw.
- Auch die für Ihr Azure AD-Verzeichnis konfigurierten Richtlinien für die Kennwortkomplexität und Kennwortgültigkeitsdauer tragen zur Sicherheit von virtuellen Windows-Computern bei.
- Mit der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie angeben, wer sich bei einem virtuellen Computer als normaler Benutzer oder mit Administratorrechten anmelden kann. Wenn Benutzer Ihrem Team beitreten oder es verlassen, können Sie die Azure RBAC-Richtlinie für den virtuellen Computer aktualisieren, um den Zugriff entsprechend zuzuweisen. Wenn Mitarbeiter Ihre Organisation verlassen und ihre Benutzerkonten in Azure AD deaktiviert oder entfernt werden, haben sie keinen Zugriff mehr auf Ihre Ressourcen.
- Konfigurieren Sie mit dem bedingten Zugriff Richtlinien, um die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) und andere Signale wie geringes Benutzer- und Anmelderisiko anzufordern, bevor Sie eine RDP-Verbindung mit einem virtuellen Windows-Computer herstellen können. 
- Verwenden Sie Azure-Bereitstellungs- und Überwachungsrichtlinien, um die Azure AD-Anmeldung für virtuelle Windows-Computer zu erzwingen und die Verwendung nicht genehmigter lokaler Konten auf virtuellen Computern zu kennzeichnen.
- Die Anmeldung bei virtuellen Windows-Computern mit Azure Active Directory funktioniert auch bei Kunden, die Verbunddienste nutzen.
- Automatisieren und skalieren Sie die Azure AD-Einbindung mit der automatischen MDM-Registrierung bei Intune von virtuellen Azure Windows-Computern, die Teil Ihrer VDI-Bereitstellungen sind. Die automatische MDM-Registrierung erfordert eine Azure AD P1-Lizenz. Bei virtuellen Computern mit Windows Server 2019 wird keine MDM-Registrierung unterstützt.

> [!NOTE]
> Nachdem Sie diese Funktion aktiviert haben, werden Ihre virtuellen Windows-Computer in Azure mit Azure AD verknüpft. Es ist nicht möglich, sie mit einer anderen Domäne wie dem lokalen Active Directory oder Azure AD DS zu verknüpfen. Wenn dies erforderlich ist, müssen Sie die VM von Ihrem Azure AD-Mandanten trennen, indem Sie die Erweiterung deinstallieren.

## <a name="requirements"></a>Requirements (Anforderungen)

### <a name="supported-azure-regions-and-windows-distributions"></a>Unterstützte Azure-Regionen und Windows-Distributionen

Für diese Funktion werden derzeit die folgenden Windows-Distributionen unterstützt:

- Windows Server 2019 Datacenter
- Windows 10 1809 und höher

> [!IMPORTANT]
> Eine Remoteverbindung mit in Azure AD eingebundenen virtuellen Computern ist nur von Windows 10-PCs zulässig, die entweder in Azure AD registriert (ab Windows 10 20H1) oder über Azure AD normal oder hybrid im **selben** Verzeichnis wie der virtuelle Computer eingebunden sind. 

Diese Funktion ist jetzt in den folgenden Azure-Clouds verfügbar:

- Azure Global
- Azure Government
- Azure China

### <a name="network-requirements"></a>Netzwerkanforderungen

Zum Aktivieren der Azure AD-Authentifizierung für Ihre virtuellen Windows-Computer in Azure müssen Sie sicherstellen, dass die Netzwerkkonfiguration der virtuellen Computer den ausgehenden Zugriff auf die folgenden Endpunkte über TCP-Port 443 zulässt:

Azure Global
- `https://enterpriseregistration.windows.net`: Für die Geräteregistrierung.
- `http://169.254.169.254`: Endpunkt des Azure Instance Metadata Service.
- `https://login.microsoftonline.com`: Für Authentifizierungsflows.
- `https://pas.windows.net`: Für Azure RBAC-Flows.

Azure Government
- `https://enterpriseregistration.microsoftonline.us`: Für die Geräteregistrierung.
- `http://169.254.169.254`: Azure Instance Metadata Service.
- `https://login.microsoftonline.us`: Für Authentifizierungsflows.
- `https://pasff.usgovcloudapi.net`: Für Azure RBAC-Flows.

Azure China
- `https://enterpriseregistration.partner.microsoftonline.cn`: Für die Geräteregistrierung.
- `http://169.254.169.254`: Endpunkt des Azure Instance Metadata Service.
- `https://login.chinacloudapi.cn`: Für Authentifizierungsflows.
- `https://pas.chinacloudapi.cn`: Für Azure RBAC-Flows.

## <a name="enabling-azure-ad-login-in-for-windows-vm-in-azure"></a>Aktivieren der Azure AD-Anmeldung für einen virtuellen Windows-Computer in Azure

Zur Verwendung der Azure AD-Anmeldung für einen virtuellen Windows-Computer in Azure müssen Sie zunächst die Azure AD-Anmeldeoption für den virtuellen Windows-Computer aktivieren. Anschließend müssen Sie Azure-Rollenzuweisungen für Benutzer konfigurieren, die berechtigt sind, sich bei dem virtuellen Computer anzumelden.
Es gibt mehrere Möglichkeiten, wie Sie die Azure AD-Anmeldung für den virtuellen Windows-Computer aktivieren können:

- Über das Azure-Portal beim Erstellen eines virtuellen Windows-Computers
- Unter Verwendung von Azure Cloud Shell beim Erstellen eines virtuellen Windows-Computers **oder für einen vorhandenen virtuellen Windows-Computer**

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Aktivieren der Azure AD-Anmeldung über das Azure-Portal beim Erstellen eines virtuellen Computers

Sie können die Azure AD-Anmeldung für VM-Images unter Windows Server 2019 Datacenter oder Windows 10 1809 und höher aktivieren. 

So erstellen Sie einen virtuellen Windows Server 2019 Datacenter-Computer in Azure mit Azure AD-Anmeldung 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Konto an, das Zugriff zum Erstellen von virtuellen Computern hat, und wählen Sie **+ Ressource erstellen** aus.
1. Geben Sie **Windows Server** auf der Suchleiste „Marketplace durchsuchen“ ein.
   1. Klicken Sie auf **Windows Server**, und wählen Sie in der Dropdownliste „Softwareplan auswählen“ den Eintrag **Windows Server 2019 Datacenter** aus.
   1. Klicken Sie auf **Erstellen**.
1. Ändern Sie im Abschnitt „Azure Active Directory“ auf der Registerkarte „Verwaltung“ die Option für **Mit AAD-Anmeldeinformationen anmelden** von „Aus“ in **Ein**.
1. Stellen Sie sicher, dass **Systemseitig zugewiesene verwaltete Identität** im Bereich „Identität“ auf **Ein** festgelegt ist. Dies sollte automatisch erfolgen, nachdem Sie „Mit AAD-Anmeldeinformationen anmelden“ aktiviert haben.
1. Führen Sie die weiteren Schritte zum Erstellen eines virtuellen Computers aus. Sie müssen einen Administratorbenutzernamen und ein Kennwort für den virtuellen Computer erstellen.

![Anmelden mit Azure AD-Anmeldeinformationen zum Erstellen eines virtuellen Computers](./media/howto-vm-sign-in-azure-ad-windows/azure-portal-login-with-azure-ad.png)

> [!NOTE]
> Damit Sie sich bei dem virtuellen Computer mit Ihren Azure AD-Anmeldeinformationen anmelden können, müssen Sie zunächst wie in einem der folgenden Abschnitte beschrieben Rollenzuweisungen für den virtuellen Computer konfigurieren.

### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Aktivieren der Azure AD-Anmeldung mithilfe von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Allgemeine Tools sind in Cloud Shell vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Wählen Sie einfach die Schaltfläche „Kopieren“ aus, um den Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen. Cloud Shell kann auf mehrere Arten geöffnet werden:

- Klicken Sie in der rechten oberen Ecke eines Codeblocks auf **Ausprobieren**.
- Öffnen Sie Cloud Shell in Ihrem Browser.
- Wählen Sie im Menü rechts oben im [Azure-Portal](https://portal.azure.com) die Schaltfläche „Cloud Shell“ aus.

Wenn Sie die Befehlszeilenschnittstelle (CLI) lokal installieren und verwenden möchten, müssen Sie für diesen Artikel mindestens Version 2.0.31 der Azure-Befehlszeilenschnittstelle ausführen. Führen Sie „az --version“ aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie im Artikel [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

1. Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe. 
1. Erstellen Sie mit [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer unter Verwendung einer unterstützten Distribution in einer unterstützten Region. 
1. Installieren Sie die VM-Erweiterung für die Azure AD-Anmeldung. 

Im folgenden Beispiel wird der virtuelle Computer „myVM“, auf dem Win2019Datacenter ausgeführt wird, in der Ressourcengruppe „myResourceGroup“ in der Region „southcentralus“ bereitgestellt. In den folgenden Beispielen können Sie den Namen Ihrer Ressourcengruppe und Ihres virtuellen Computers nach Bedarf angeben.

```AzureCLI
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Win2019Datacenter \
    --assign-identity \
    --admin-username azureuser \
    --admin-password yourpassword
```

> [!NOTE]
> Es ist erforderlich, dass Sie die systemseitig zugewiesene verwaltete Identität auf dem virtuellen Computer aktivieren, bevor Sie die VM-Erweiterung für die Azure AD-Anmeldung installieren.

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten.

Installieren Sie schließlich die VM-Erweiterung für die Azure AD-Anmeldung, um die Azure AD-Anmeldung für den virtuellen Windows-Computer zu aktivieren. VM-Erweiterungen sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Verwenden Sie [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), um die Erweiterung AADLoginForWindows auf dem virtuellen Computer `myVM` in der Ressourcengruppe `myResourceGroup` zu installieren:

> [!NOTE]
> Sie können die Erweiterung AADLoginForWindows auf einem vorhandenen virtuellen Computer unter Windows Server 2019 oder Windows 10 1809 und höher installieren, um ihn für die Azure AD-Authentifizierung zu aktivieren. Ein Beispiel für die Azure-Befehlszeilenschnittstelle ist nachstehend dargestellt.

```AzureCLI
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADLoginForWindows \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Nachdem die Erweiterung auf dem virtuellen Computer installiert wurde, wird der Wert `Succeeded` für `provisioningState` angezeigt.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurieren der Rollenzuweisungen für den virtuellen Computer

Nach dem Erstellen des virtuellen Computers müssen Sie eine Azure-RBAC-Richtlinie konfigurieren, um festzulegen, wer sich bei dem virtuellen Computer anmelden kann. Zur Autorisierung der VM-Anmeldung werden zwei Azure-Rollen verwendet:

- **VM-Administratoranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit Administratorberechtigungen bei einem virtuellen Azure-Computer anmelden.
- **VM-Benutzeranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit normalen Benutzerberechtigungen bei einem virtuellen Azure-Computer anmelden.

> [!NOTE]
> Damit sich ein Benutzer über RDP bei dem virtuellen Computer anmelden kann, müssen Sie ihm die Rolle „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“ zuweisen. Ein Azure-Benutzer, dem eine der Rollen „Besitzer“ oder „Mitwirkender“ für einen virtuellen Computer zugewiesen ist, verfügt nicht automatisch über die Berechtigungen für die Anmeldung bei dem virtuellen Computer über RDP. Dadurch wird eine überprüfte Trennung zwischen den Personen, die virtuelle Computer steuern können, und den Personen, die auf virtuelle Computer zugreifen können, ermöglicht.

Es gibt mehrere Möglichkeiten, wie Sie Rollenzuweisungen für den virtuellen Computer konfigurieren können:

- Verwenden des Azure AD-Portals
- Verwenden von Azure Cloud Shell

> [!NOTE]
> Die Rollen „Anmeldeinformationen des VM-Administrators“ und „Anmeldeinformationen für VM-Benutzer“ verwenden dataActions und können daher nicht im Bereich der Verwaltungsgruppe zugewiesen werden. Diese Rollen können derzeit nur im Abonnement-, Ressourcengruppen- oder Ressourcenbereich zugewiesen werden.

### <a name="using-azure-ad-portal-experience"></a>Verwenden des Azure AD-Portals

So konfigurieren Sie Rollenzuweisungen für Azure AD-fähige virtuelle Computer unter Windows Server 2019 Datacenter

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgende Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Rolle | **VM-Administratoranmeldung** oder **VM-Benutzeranmeldung** |
    | Zugriff zuweisen zu | Benutzer, Gruppe, Dienstprinzipal oder verwaltete Identität |

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="using-the-azure-cloud-shell-experience"></a>Verwenden von Azure Cloud Shell

Im folgenden Beispiel wird [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) verwendet, um dem aktuellen Azure-Benutzer die Rolle „VM-Administratoranmeldung“ für den virtuellen Computer zuzuweisen. Der Benutzername des aktiven Azure-Kontos wird mit [az account show](/cli/azure/account#az_account_show) abgerufen. Der Bereich wird mit [az vm show](/cli/azure/vm#az_vm_show) auf den in einem vorherigen Schritt erstellten virtuellen Computer festgelegt. Der Bereich kann auch auf Ebene einer Ressourcengruppe oder eines Abonnements zugewiesen werden. Dann gelten normale Azure RBAC-Vererbungsberechtigungen. Weitere Informationen finden Sie unter [Anmelden bei einem virtuellen Linux-Computer in Azure mit der Azure Active Directory-Authentifizierung](../../virtual-machines/linux/login-using-aad.md).

```   AzureCLI
$username=$(az account show --query user.name --output tsv)
$vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Wenn die AAD-Domäne und die Domäne des Benutzeranmeldenamens nicht übereinstimmen, müssen Sie die Objekt-ID des Benutzerkontos mit `--assignee-object-id` angeben. Die Angabe des Benutzernamens für `--assignee` genügt nicht. Sie können die Objekt-ID für Ihr Benutzerkonto mithilfe der [Azure Active Directory-Benutzerliste (az as user list)](/cli/azure/ad/user#az_ad_user_list) erhalten.

Weitere Informationen zur Verwendung der rollenbasierten Zugriffssteuerung (Azure RBAC) zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen finden Sie in folgenden Artikeln:

- [Zuweisen von Azure-Rollen mithilfe der Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md)
- [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md)
- [Zuweisen von Azure-Rollen mithilfe von Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="using-conditional-access"></a>Verwenden von bedingtem Zugriff

Sie können Richtlinien für bedingten Zugriff erzwingen, z. B. die mehrstufige Authentifizierung oder Überprüfung des Anmelderisikos für Benutzer, bevor der Zugriff auf virtuelle Windows-Computer in Azure gewährt wird, für die die Azure AD-Anmeldung aktiviert ist. Zum Anwenden einer Richtlinie für bedingten Zugriff müssen Sie die App **Azure Windows VM Sign-In** über die Zuweisungsoption für Cloud-Apps oder Aktionen auswählen und dann „Anmelderisiko“ als Bedingung und/oder „Mehrstufige Authentifizierung erforderlich“ als Zugriffssteuerung verwenden. 

> [!NOTE]
> Wenn Sie „Mehrstufige Authentifizierung erforderlich“ als Zugriffssteuerung für das Anfordern des Zugriffs auf die App Azure Windows VM Sign-In verwenden, müssen Sie den Anspruch der mehrstufigen Authentifizierung als Teil des Clients angeben, der die RDP-Sitzung für den virtuellen Windows-Zielcomputer in Azure initiiert. Dies kann auf einem Windows 10-Client nur durch Verwendung der Windows Hello for Business-PIN oder der biometrischen Authentifizierung mit dem RDP-Client erreicht werden. Die Unterstützung für die biometrische Authentifizierung wurde dem RDP-Client in Windows 10 Version 1809 hinzugefügt. Remotedesktop unter Verwendung der Windows Hello for Business-Authentifizierung ist nur für Bereitstellungen verfügbar, die das Modell der Zertifikatvertrauensstellung verwenden und derzeit nicht für das Modell der schlüsselbasierten Vertrauensstellung verfügbar sind.

> [!WARNING]
> Eine aktivierte/erzwungene Authentifizierung über Azure AD Multi-Factor Authentication pro Benutzer wird für VM-Anmeldungen nicht unterstützt.

## <a name="log-in-using-azure-ad-credentials-to-a-windows-vm"></a>Anmelden bei einem virtuellen Windows-Computer mithilfe von Azure AD-Anmeldeinformationen

> [!IMPORTANT]
> Eine Remoteverbindung mit in Azure AD eingebundenen VMs ist nur auf Windows 10-PCs zulässig, die entweder über Azure AD (mindestens Build 20H1 erforderlich) registriert oder über Azure AD im **selben** Verzeichnis wie die VM regulär oder hybrid eingebunden sind. Zusätzlich muss dem Benutzer für eine RDP-Verbindung unter Verwendung von Azure AD-Anmeldeinformationen eine der beiden Azure-Rollen „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“ zugewiesen sein. Bei Verwendung eines für Azure AD registrierten Windows 10-Computers müssen Sie Anmeldeinformationen im Format `AzureAD\UPN` eingeben (z. B. `AzureAD\john@contoso.com`). Derzeit kann Azure Bastion nicht für die Anmeldung mithilfe der Azure Active Directory-Authentifizierung und der Erweiterung „AADLoginForWindows“ verwendet werden. Nur direktes RDP wird unterstützt.

So melden Sie sich mithilfe von Azure AD bei Ihrer Windows Server 2019-VM an: 

1. Navigieren Sie zur Übersichtsseite des virtuellen Computers, der für die Azure AD-Anmeldung aktiviert wurde.
1. Wählen Sie **Verbinden** aus, um das Blatt „Verbindung mit virtuellem Computer herstellen“ zu öffnen.
1. Wählen Sie **RDP-Datei herunterladen** aus.
1. Wählen Sie **Öffnen** aus, um den Remotedesktopverbindungs-Client zu starten.
1. Wählen Sie **Verbinden** aus, um das Dialogfeld „Windows-Anmeldung“ zu öffnen.
1. Melden Sie sich mit Ihren Azure AD-Anmeldeinformationen an.

Sie sind nun mit den entsprechend zugewiesenen Rollenberechtigungen (z. B. „VM-Benutzer“ oder „VM-Administrator“) bei dem virtuellen Azure Windows Server 2019-Computer angemeldet. 

> [!NOTE]
> Sie können die RDP-Datei lokal auf dem Computer speichern, um damit zukünftige Remotedesktopverbindungen mit dem virtuellen Computer zu starten, anstatt zur Übersichtsseite des virtuellen Computers im Azure-Portal navigieren und die Option „Verbinden“ verwenden zu müssen.

## <a name="using-azure-policy-to-ensure-standards-and-assess-compliance"></a>Verwenden von Azure Policy zum Sicherstellen von Standards und für die Konformitätsbewertung

Verwenden Sie Azure Policy, um sicherzustellen, dass die Azure AD-Anmeldung für Ihre neuen und vorhandenen virtuellen Windows-Computer aktiviert ist, und bewerten Sie bedarfsgerecht die Konformität Ihrer Umgebung auf dem Compliancedashboard von Azure Policy. Mit dieser Funktion können Sie viele Erzwingungsstufen verwenden: Sie können neue und vorhandene virtuelle Windows-Computer in Ihrer Umgebung kennzeichnen, für die keine Azure AD-Anmeldung aktiviert ist. Sie können Azure Policy auch zum Bereitstellen der Azure AD-Erweiterung auf neuen Windows-VMs verwenden, auf denen diese noch nicht aktiviert ist. Sie können aber auch vorhandene virtuelle Windows-Computer auf denselben Standard aktualisieren. Neben diesen Funktionen können Sie Azure Policy auch zum Erkennen und Kennzeichnen von Windows-VMs verwenden, auf denen nicht genehmigte lokale Konten erstellt wurden. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md).

## <a name="troubleshoot"></a>Problembehandlung

### <a name="troubleshoot-deployment-issues"></a>Problembehandlung bei Bereitstellungsproblemen

Die Erweiterung AADLoginForWindows muss installiert sein, damit der Azure AD-Einbindungsprozess auf dem virtuellen Computer abgeschlossen werden kann. Führen Sie die folgenden Schritte aus, wenn die VM-Erweiterung nicht ordnungsgemäß installiert wird.

1. Stellen Sie über das lokale Administratorkonto eine RDP-Verbindung mit dem virtuellen Computer her, und überprüfen Sie die Datei `CommandExecution.log` unter:
   
   `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.ActiveDirectory.AADLoginForWindows\0.3.1.0.`

   > [!NOTE]
   > Wenn die Erweiterung nach dem anfänglichen Fehler neu gestartet wird, wird das Protokoll mit dem Bereitstellungsfehler als `CommandExecution_YYYYMMDDHHMMSSSSS.log` gespeichert. "
1. Öffnen Sie ein PowerShell-Fenster auf dem virtuellen Computer, und überprüfen Sie, ob diese Abfragen für den auf dem Azure-Host ausgeführten Instance Metadata Service-Endpunkt (IMDS) Folgendes zurückgeben:

   | Auszuführender Befehl | Erwartete Ausgabe |
   | --- | --- |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/instance?api-version=2017-08-01"` | Richtige Informationen zum virtuellen Azure-Computer |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/info?api-version=2018-02-01"` | Gültige dem Azure-Abonnement zugeordnete Mandanten-ID |
   | `curl -H @{"Metadata"="true"} "http://169.254.169.254/metadata/identity/oauth2/token?resource=urn:ms-drs:enterpriseregistration.windows.net&api-version=2018-02-01"` | Gültiges Zugriffstoken, das von Azure Active Directory für die verwaltete Identität, die dem virtuellen Computer zugewiesen ist, ausgestellt wird |

   > [!NOTE]
   > Das Zugriffstoken kann mithilfe eines Tools wie [calebb.net](http://calebb.net/) decodiert werden. Vergewissern Sie sich, dass die `appid` im Zugriffstoken mit der verwalteten Identität übereinstimmt, die dem virtuellen Computer zugewiesen ist.

1. Überprüfen Sie mit PowerShell, ob über den virtuellen Computer auf die erforderlichen Endpunkte zugegriffen werden kann:
   
   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`

   > [!NOTE]
   > Ersetzen Sie `<TenantID>` durch die ID des Azure AD-Mandanten, die dem Azure-Abonnement zugeordnet ist.<br/> `enterpriseregistration.windows.net` und `pas.windows.net` sollten „404 Nicht gefunden“ zurückgeben, was dem erwarteten Verhalten entspricht.
            
1. Der Gerätestatus kann durch Ausführen von `dsregcmd /status` angezeigt werden. Ziel ist, dass der Gerätestatus `AzureAdJoined : YES` angezeigt wird.

   > [!NOTE]
   > Die Azure AD-Einbindungsaktivität wird in der Ereignisanzeige unter dem Protokoll `User Device Registration\Admin` aufgezeichnet.

Wenn beim Ausführen der Erweiterung AADLoginForWindows ein bestimmter Fehlercode ausgegeben wird, können Sie die folgenden Schritte ausführen:

#### <a name="issue-1-aadloginforwindows-extension-fails-to-install-with-terminal-error-code-1007-and-exit-code--2145648574"></a>Problem 1: Erweiterung AADLoginForWindows kann mit dem Terminalfehlercode „1007“ und dem Exitcode „-2145648574“ nicht installiert werden.

Dieser Exitcode ergibt `DSREG_E_MSI_TENANTID_UNAVAILABLE`, da die Erweiterung die Informationen des Azure AD-Mandanten nicht abfragen kann.

1. Vergewissern Sie sich, dass der virtuelle Azure-Computer die Mandanten-ID von Instance Metadata Service abrufen kann.

   - Stellen Sie als lokaler Administrator eine RDP-Verbindung mit dem virtuellen Computer her, und überprüfen Sie, ob der Endpunkt eine gültige Mandanten-ID zurückgibt. Führen Sie dazu den folgenden Befehl über ein PowerShell-Fenster mit erhöhten Rechten auf dem virtuellen Computer aus:
      
      - `curl -H Metadata:true http://169.254.169.254/metadata/identity/info?api-version=2018-02-01`

1. Der VM-Administrator versucht, die Erweiterung AADLoginForWindows zu installieren, aber eine systemseitig zugewiesene verwaltete Identität hat den virtuellen Computer zuvor nicht aktiviert. Navigieren Sie zum Blatt „Identität“ des virtuellen Computers. Überprüfen Sie auf der Registerkarte „Vom System zugewiesen“, ob der Status auf „Ein“ festgelegt ist.

#### <a name="issue-2-aadloginforwindows-extension-fails-to-install-with-exit-code--2145648607"></a>Problem 2: Erweiterung AADLoginForWindows kann mit dem Exitcode „-2145648607“ nicht installiert werden

Dieser Exitcode ergibt `DSREG_AUTOJOIN_DISC_FAILED`, da die Erweiterung den Endpunkt `https://enterpriseregistration.windows.net` nicht erreichen kann.

1. Überprüfen Sie mit PowerShell, ob über den virtuellen Computer auf die erforderlichen Endpunkte zugegriffen werden kann:

   - `curl https://login.microsoftonline.com/ -D -`
   - `curl https://login.microsoftonline.com/<TenantID>/ -D -`
   - `curl https://enterpriseregistration.windows.net/ -D -`
   - `curl https://device.login.microsoftonline.com/ -D -`
   - `curl https://pas.windows.net/ -D -`
   
   > [!NOTE]
   > Ersetzen Sie `<TenantID>` durch die ID des Azure AD-Mandanten, die dem Azure-Abonnement zugeordnet ist. Wenn Sie die Mandanten-ID suchen möchten, können Sie auf den Namen Ihres Kontos zeigen, um die Verzeichnis-ID oder Mandanten-ID abzurufen, oder im Azure-Portal die Optionen **Azure Active Directory > Eigenschaften > Verzeichnis-ID** auswählen.<br/>`enterpriseregistration.windows.net` und `pas.windows.net` sollten „404 Nicht gefunden“ zurückgeben, was dem erwarteten Verhalten entspricht.

1. Wenn bei einem der Befehle der Fehler „Der Hostname `<URL>` konnte nicht aufgelöst werden“ auftritt, führen Sie den folgenden Befehl aus, um den DNS-Server zu ermitteln, der von dem virtuellen Computer verwendet wird.
   
   `nslookup <URL>`

   > [!NOTE] 
   > Ersetzen Sie `<URL>` durch die von den Endpunkten verwendeten vollqualifizierten Domänennamen, z. B. `login.microsoftonline.com`.

1. Überprüfen Sie dann, ob der Befehl durch die Angabe eines öffentlichen DNS-Servers ausgeführt werden kann:

   `nslookup <URL> 208.67.222.222`

1. Ändern Sie gegebenenfalls den DNS-Server, der der Netzwerksicherheitsgruppe, zu der der virtuelle Azure-Computer gehört, zugewiesen ist.

#### <a name="issue-3-aadloginforwindows-extension-fails-to-install-with-exit-code-51"></a>Problem 3: Erweiterung AADLoginForWindows kann mit folgendem Exitcode nicht installiert werden: 51

Der Exitcode „51“ ergibt „Diese Erweiterung wird vom Betriebssystem der VM nicht unterstützt“.

Die Erweiterung „AADLoginForWindows“ ist nur für die Installation unter Windows Server 2019 oder Windows 10 (Build 1809 oder höher) vorgesehen. Vergewissern Sie sich, dass die Windows-Version unterstützt wird. Wenn der Windows-Build nicht unterstützt wird, deinstallieren Sie die VM-Erweiterung.

### <a name="troubleshoot-sign-in-issues"></a>Beheben von Problemen bei der Anmeldung

Zu den häufig auftretenden Fehlern beim Herstellen einer RDP-Verbindung mithilfe von Azure AD-Anmeldeinformationen gehören nicht zugewiesene Azure-Rollen, ein nicht autorisierter Client und eine erforderliche 2FA-Anmeldemethode. Im Folgenden finden Sie Informationen zum Beheben dieser Probleme.

Der Gerätestatus und der SSO-Status können durch Ausführen von `dsregcmd /status` angezeigt werden. Ziel ist, dass der Gerätestatus `AzureAdJoined : YES` und für `SSO State` der Wert `AzureAdPrt : YES` angezeigt werden.

Außerdem wird die RDP-Anmeldung über Azure AD-Konten in der Ereignisanzeige in den Protokollen unter `AAD\Operational` aufgezeichnet.

#### <a name="azure-role-not-assigned"></a>Azure-Rolle nicht zugewiesen

Beim Initiieren einer Remotedesktopverbindung mit dem virtuellen Computer wird die folgende Fehlermeldung angezeigt: 

- Die Konfiguration Ihres Kontos lässt die Verwendung dieses Geräts nicht zu. Wenden Sie sich an den Systemadministrator, um weitere Informationen zu erhalten.

![Die Konfiguration Ihres Kontos lässt die Verwendung dieses Geräts nicht zu.](./media/howto-vm-sign-in-azure-ad-windows/rbac-role-not-assigned.png)

Überprüfen Sie, ob Sie für den virtuellen Computer [Azure RBAC-Richtlinien konfiguriert](../../virtual-machines/linux/login-using-aad.md) haben, mit denen dem Benutzer die Rolle „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“ zugewiesen wird:

> [!NOTE]
> Wenn Probleme mit Azure-Rollenzuweisungen auftreten, finden Sie weitere Informationen unter [Behandeln von Problemen bei Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).
 
#### <a name="unauthorized-client"></a>Nicht autorisierter Client

Beim Initiieren einer Remotedesktopverbindung mit dem virtuellen Computer wird die folgende Fehlermeldung angezeigt: 

- Ihre Anmeldeinformationen haben nicht funktioniert.

![Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

Vergewissern Sie sich, dass der Windows 10-PC, den Sie zum Initiieren der Remotedesktopverbindung verwenden, über Azure AD regulär oder hybrid im selben Azure AD-Verzeichnis eingebunden ist, in dem auch der virtuelle Computer eingebunden ist. Weitere Informationen zur Geräteidentität finden Sie im Artikel [Was ist eine Geräteidentität](./overview.md).

> [!NOTE]
> Im Build 20H1 für Windows 10 wurde Unterstützung für einen für Azure AD registrierten Computer hinzugefügt, um RDP-Verbindungen zu Ihrer VM zu initiieren. Bei Verwendung eines für Azure AD registrierten (nicht über Azure AD regulär oder hybrid eingebundenen) Computers als RDP-Client zum Initiieren von Verbindungen mit Ihrer VM müssen Sie Anmeldeinformationen im Format `AzureAD\UPN` (z. B. `AzureAD\john@contoso.com`) eingeben.

Vergewissern Sie sich, dass die Erweiterung „AADLoginForWindows“ nach Abschluss der Azure AD-Einbindung nicht deinstalliert wurde.

Stellen Sie außerdem sicher, dass die Sicherheitsrichtlinie „Netzwerksicherheit: Lässt an diesen Computer gerichtete PKU2U-Authentifizierungsanforderungen zu, um die Verwendung von Online-Identitäten zu ermöglichen“ sowohl auf dem Server **als auch** auf dem Client aktiviert ist.
 
#### <a name="mfa-sign-in-method-required"></a>MFA-Anmeldemethode erforderlich

Beim Initiieren einer Remotedesktopverbindung mit dem virtuellen Computer wird die folgende Fehlermeldung angezeigt: 

- „The sign-in method you're trying to use isn't allowed.“ (Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig.) Try a different sign-in method or contact your system administrator.“ (Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig. Verwenden Sie eine andere Anmeldemethode, oder wenden Sie sich an Ihren Systemadministrator.)

![„The sign-in method you're trying to use isn't allowed.“ (Die Anmeldemethode, die Sie verwenden möchten, ist nicht zulässig.)](./media/howto-vm-sign-in-azure-ad-windows/mfa-sign-in-method-required.png)

Wenn Sie eine Richtlinie für bedingten Zugriff konfiguriert haben, die die mehrstufige Authentifizierung (MFA) erforderlich macht, damit Sie auf die Ressource zugreifen können, müssen Sie sicherstellen, dass die Anmeldung auf dem Windows 10-PC, über den die Remotedesktopverbindung mit dem virtuellen Computer initiiert wird, mithilfe einer starken Authentifizierungsmethode erfolgt, z. B. mit Windows Hello. Wenn Sie keine starke Authentifizierungsmethode für die Remotedesktopverbindung verwenden, wird dieser Fehler angezeigt.

- Ihre Anmeldeinformationen haben nicht funktioniert.

![Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden](./media/howto-vm-sign-in-azure-ad-windows/your-credentials-did-not-work.png)

> [!WARNING]
> Eine aktivierte/erzwungene Authentifizierung über Azure AD Multi-Factor Authentication pro Benutzer wird für VM-Anmeldungen nicht unterstützt. Diese Einstellung führt dazu, dass bei der Anmeldung die Fehlermeldung „Ihre Anmeldeinformationen funktionieren nicht" angezeigt wird.

Sie können das oben genannte Problem beheben, indem Sie die MFA-Einstellung pro Benutzer mit folgenden Schritten entfernen:

```

# Get StrongAuthenticationRequirements configure on a user
(Get-MsolUser -UserPrincipalName username@contoso.com).StrongAuthenticationRequirements
 
# Clear StrongAuthenticationRequirements from a user
$mfa = @()
Set-MsolUser -UserPrincipalName username@contoso.com -StrongAuthenticationRequirements $mfa
 
# Verify StrongAuthenticationRequirements are cleared from the user
(Get-MsolUser -UserPrincipalName username@contoso.com).StrongAuthenticationRequirements

```

Wenn Sie Windows Hello for Business nicht bereitgestellt haben und dies derzeit keine Option ist, können Sie die MFA-Anforderung ausschließen, indem Sie eine Richtlinie für bedingten Zugriff konfigurieren, die die App **Azure Windows VM Sign-In** aus der Liste der Cloud-Apps ausschließt, für die die mehrstufige Authentifizierung erforderlich ist. Weitere Informationen zu Windows Hello for Business finden Sie in der [Übersicht zu Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification).

> [!NOTE]
> Die Authentifizierung über die Windows Hello for Business-PIN mit RDP wird unter Windows 10 in mehreren Versionen unterstützt. Die Unterstützung für die biometrische Authentifizierung mit RDP wurde dagegen in Windows 10 Version 1809 hinzugefügt. Die Verwendung der Windows Hello for Business-Authentifizierung bei der RDP-Verbindung ist nur für Bereitstellungen verfügbar, die das Modell der Zertifikatvertrauensstellung verwenden und derzeit nicht für das Modell der schlüsselbasierten Vertrauensstellung verfügbar sind.
 
Geben Sie Feedback zu dieser Funktion, oder melden Sie Probleme bei der Verwendung der Funktion im [Azure AD-Feedbackforum](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory finden Sie unter [Was ist Azure Active Directory?](../fundamentals/active-directory-whatis.md).
