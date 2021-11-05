---
title: Anmelden bei einem virtuellen Linux-Computer in Azure mit Azure Active Directory (Vorschau)
description: Azure AD-Anmeldung bei einem virtuellen Azure-Computer mit Linux
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli, subject-rbac-steps
ms.collection: M365-identity-device-management
ms.openlocfilehash: aeca09f5763cf11edc13cecd2df0c267fad8a23d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049746"
---
# <a name="preview-login-to-a-linux-virtual-machine-in-azure-with-azure-active-directory-using-ssh-certificate-based-authentication"></a>Vorschau: Anmelden bei einem virtuellen Linux-Computer in Azure mit Azure Active Directory mithilfe der zertifikatbasierten SSH-Authentifizierung

Zur Optimierung der Sicherheit von virtuellen Linux-Computern in Azure können Sie diese in die Azure Active Directory-Authentifizierung (Azure AD) integrieren. Sie können jetzt Azure AD als Kernauthentifizierungsplattform und Zertifizierungsstelle verwenden, um mit AD und der zertifikatbasierten SSH-Authentifizierung eine SSH-Verbindung zu einem virtuellen Linux-Computer herzustellen. Mit dieser Funktion können Organisationen die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure und Richtlinien für bedingten Zugriff (zur Verwaltung des Zugriffs auf die virtuellen Computer) zentral steuern und erzwingen. In diesem Artikel wird beschrieben, wie Sie einen virtuellen Linux-Computer erstellen und konfigurieren und mithilfe der zertifikatbasierten SSH-Authentifizierung eine Anmeldung bei Azure AD realisieren.

> [!IMPORTANT]
> Diese Funktion befindet sich derzeit in der Phase der öffentlichen Vorschau. [Die bisherige Version, die den Gerätecodeflow verwendet, ist seit dem 15. August 2021 veraltet](../../virtual-machines/linux/login-using-aad.md). Informationen zum Migrieren von der alten Version zu dieser Version finden Sie im Abschnitt [Migration von der vorherigen Vorschauversion](#migration-from-previous-preview).
> Diese Vorschau wird ohne Vereinbarung zum Servicelevel bereitgestellt und nicht für Produktionsworkloads empfohlen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Verwenden Sie dieses Feature auf einem virtuellen Testcomputer, von dem Sie davon ausgehen, dass er nach dem Testen verworfen wird. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Die Verwendung von Azure AD mit der zertifikatbasierten SSH-Authentifizierung für die Anmeldung bei virtuellen Linux-Computern in Azure bietet viele Vorteile, z. B.:

- Verwenden Sie Ihre Azure AD-Anmeldeinformationen, um sich bei virtuellen Linux-Computern in Azure anzumelden.
- Realisieren Sie eine auf SSH-Schlüsseln basierende Authentifizierung, ohne SSH-Schlüssel an Benutzer verteilen oder öffentliche SSH-Schlüssel auf allen bereitgestellten virtuellen Linux-Computern in Azure bereitstellen zu müssen. Das ist sehr viel einfacher, als sich Gedanken über verstreute und veraltete öffentliche SSH-Schlüssel machen zu müssen, die möglicherweise zu einem nicht autorisierten Zugriff führen.
- Reduzieren Sie die Abhängigkeit von lokalen Administratorkonten, und vermeiden Sie schwache Anmeldeinformationen sowie den Diebstahl von Anmeldeinformationen.
- Die für Azure AD konfigurierten Richtlinien zur Kennwortkomplexität und Kennwortgültigkeitsdauer schützen auch virtuelle Linux-Computer.
- Mit der rollenbasierten Zugriffssteuerung von Azure können Sie angeben, wer sich bei einem virtuellen Computer als normaler Benutzer oder mit Administratorrechten anmelden kann. Wenn Benutzer Ihrem Team beitreten oder es verlassen, können Sie die Azure RBAC-Richtlinie für den virtuellen Computer aktualisieren, um den Zugriff entsprechend zuzuweisen. Wenn Mitarbeiter Ihre Organisation verlassen und ihre Benutzerkonten in Azure AD deaktiviert oder entfernt werden, haben sie keinen Zugriff mehr auf Ihre Ressourcen.
- Mit dem bedingten Zugriff können Sie Richtlinien so konfigurieren, dass eine mehrstufige Authentifizierung erforderlich ist und/oder das für SSH verwendete Clientgerät ein verwaltetes Gerät (z. B. kompatibles Gerät oder hybrid in Azure AD eingebunden) sein muss, bevor Sie eine SSH-Verbindung zu virtuellen Linux-Computern herstellen können. 
- Verwenden Sie Azure-Bereitstellungs- und Überwachungsrichtlinien, um die Azure AD-Anmeldung für virtuelle Linux-Computer zu erzwingen und die Verwendung nicht genehmigter lokaler Konten auf den virtuellen Computern zu kennzeichnen.
- Die Anmeldung bei virtuellen Linux-Computern mit Azure Active Directory funktioniert auch bei Kunden, die Verbunddienste verwenden.

## <a name="supported-linux-distributions-and-azure-regions"></a>Unterstützte Linux-Distributionen und Azure-Regionen

Während der Vorschauphase dieser Funktion werden aktuell die folgenden Linux-Distributionen unterstützt, wenn sie in einer unterstützten Region bereitgestellt werden:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 7, CentOS 8 |
| Debian | Debian 9, Debian 10 |
| openSUSE | openSUSE Leap 42.3, openSUSE Leap 15.1 oder höher |
| Red Hat Enterprise Linux | RHEL 7.4 bis RHEL 7.10, RHEL 8.3 oder höher |
| SUSE Linux Enterprise Server | SLES 12, SLES 15.1 oder höher |
| Ubuntu Server | Ubuntu Server 16.04 bis Ubuntu Server 20.04 |

Während der Vorschauphase dieses Features werden derzeit die folgenden Azure-Regionen unterstützt:

- Azure Global

 
Die Verwendung dieser Erweiterung für AKS-Cluster (Azure Kubernetes Service) wird nicht unterstützt. Weitere Informationen finden Sie unter [Unterstützungsrichtlinien für AKS](../../aks/support-policies.md).

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens Version 2.22.1 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

## <a name="requirements-for-login-with-azure-ad-using-ssh-certificate-based-authentication"></a>Anforderungen für die Anmeldung bei Azure AD mit der zertifikatbasierten SSH-Authentifizierung

Um die Azure AD-Anmeldung mit der zertifikatbasierten SSH-Authentifizierung für Ihre virtuellen Linux-Computer in Azure zu aktivieren, müssen Sie sicherstellen, dass die folgenden Anforderungen an Netzwerk, virtuellen Computer und Client (SSH-Client) erfüllt sind.

### <a name="network"></a>Netzwerk

Die Netzwerkkonfiguration der virtuellen Computer muss über TCP-Port 443 ausgehenden Zugriff auf die folgenden Endpunkte zulassen:

Azure Global

- `https://packages.microsoft.com`: Für Paketinstallation und Upgrades.
- `http://169.254.169.254`: Endpunkt des Azure Instance Metadata Service.
- `https://login.microsoftonline.com`: Für PAM-basierte Authentifizierungsflows (Pluggable Authentication Modules).
- `https://pas.windows.net`: Für Azure RBAC-Flows.

Azure Government

- `https://packages.microsoft.com`: Für Paketinstallation und Upgrades.
- `http://169.254.169.254`: Endpunkt des Azure Instance Metadata Service.
- `https://login.microsoftonline.us`: Für PAM-basierte Authentifizierungsflows (Pluggable Authentication Modules).
- `https://pasff.usgovcloudapi.net`: Für Azure RBAC-Flows.

Azure China

- `https://packages.microsoft.com`: Für Paketinstallation und Upgrades.
- `http://169.254.169.254`: Endpunkt des Azure Instance Metadata Service.
- `https://login.chinacloudapi.cn`: Für PAM-basierte Authentifizierungsflows (Pluggable Authentication Modules).
- `https://pas.chinacloudapi.cn`: Für Azure RBAC-Flows.

### <a name="virtual-machine"></a>Virtueller Computer

Stellen Sie sicher, dass Ihr virtueller Computer mit der folgenden Funktionalität konfiguriert ist:

- Systemseitig zugewiesene verwaltete Identität. Diese Option wird automatisch ausgewählt, wenn Sie das Azure-Portal zum Erstellen des virtuellen Computers verwenden und die Azure AD-Anmeldung auswählen. Sie können die systemseitig zugewiesene verwaltete Identität auch mithilfe der Azure CLI auf einem neuen oder vorhandenen virtuellen Computer aktivieren.
- `aadsshlogin` und `aadsshlogin-selinux` (sofern zutreffend). Diese Pakete werden mit der VM-Erweiterung „AADSSHLoginForLinux“ installiert. Die Erweiterung wird installiert, wenn Sie das Azure-Portal (oder die Azure CLI) zum Erstellen eines virtuellen Computers und zum Aktivieren der Azure AD-Anmeldung verwenden (Registerkarte „Verwaltung“).

### <a name="client"></a>Client

Stellen Sie sicher, dass Ihr Client folgende Anforderungen erfüllt:

- Der SSH-Client muss OpenSSH-basierte Zertifikate für die Authentifizierung unterstützen. Zur Erfüllung dieser Anforderung können Sie Az CLI (2.21.1 oder höher) mit OpenSSH (enthalten in Windows 10, Version 1803 oder höher) oder Azure Cloud Shell verwenden. 
- SSH-Erweiterung für Azure CLI. Diese Erweiterung können Sie mithilfe von `az extension add --name ssh` installieren. Sie müssen diese Erweiterung jedoch nicht installieren, wenn Sie Azure Cloud Shell verwenden, da sie vorinstalliert ist.
- Wenn Sie einen anderen SSH-Client als die Azure CLI oder Azure Cloud Shell verwenden, der OpenSSH-Zertifikate unterstützt, müssen Sie weiterhin die Azure CLI mit der SSH-Erweiterung zum Abrufen von kurzlebigen SSH-Zertifikaten (und optional einer Konfigurationsdatei) und dann die Konfigurationsdatei bei Ihrem SSH-Client verwenden.
- TCP-Konnektivität vom Client zur öffentlichen oder privaten IP-Adresse des virtuellen Computers („ProxyCommand“ oder SSH-Weiterleitung an einen Computer mit Konnektivität funktioniert ebenfalls).

## <a name="enabling-azure-ad-login-in-for-linux-vm-in-azure"></a>Aktivieren der Azure AD-Anmeldung für virtuelle Linux-Computer in Azure

Um die Azure AD-Anmeldung für virtuelle Linux-Computer in Azure zu verwenden, müssen Sie zuerst die Azure AD-Anmeldeoption für Ihren virtuellen Linux-Computer aktivieren. Außerdem müssen Sie Azure-Rollenzuweisungen für die Benutzer konfigurieren, die autorisiert sind, sich bei dem virtuellen Computer anzumelden. Verwenden Sie dann den SSH-Client, der OpensSSH unterstützt (z. B. Azure CLI oder Azure Cloud Shell), um eine SSH-Verbindung zu Ihrem virtuellen Linux-Computer herzustellen. Es gibt mehrere Möglichkeiten, die Azure AD-Anmeldung für Ihren virtuellen Linux-Computer zu aktivieren. Zum Beispiel:

- Im Azure-Portal beim Erstellen eines virtuellen Linux-Computers
- Mit der Azure Cloud Shell beim Erstellen eines virtuellen Linux-Computers oder für einen vorhandenen virtuellen Linux-Computer

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>Aktivieren der Azure AD-Anmeldung über das Azure-Portal beim Erstellen eines virtuellen Computers

Im Azure-Portal können Sie die Azure AD-Anmeldung für alle der oben genannten unterstützten Linux-Distributionen aktivieren.

Erstellen Sie als Beispiel in Azure einen virtuellen Computer mit Ubuntu Server 18.04 LTS und Azure AD-Anmeldung:

1. Melden Sie sich im Azure-Portal mit einem Konto an, das Zugriff zum Erstellen virtueller Computer hat, und wählen Sie **+ Ressource erstellen** aus.
1. Klicken Sie in der Ansicht **Beliebt** unter **Ubuntu Server 18.04 LTS** auf **Erstellen**.
1. Navigieren Sie zur Registerkarte **Verwaltung**, 
   1. und aktivieren Sie das Kontrollkästchen, um die **Anmeldung mit Azure Active Directory (Vorschau)** zu aktivieren.
   1. Stellen Sie sicher, dass die Option **Systemseitig zugewiesene verwaltete Identität** aktiviert ist.
1. Führen Sie die weiteren Schritte zum Erstellen eines virtuellen Computers aus. Während dieser Vorschauphase müssen Sie ein Administratorkonto mit Benutzernamen und Kennwort/öffentlichem SSH-Schlüssel erstellen.
 
### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>Aktivieren der Azure AD-Anmeldung mithilfe von Azure Cloud Shell

Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Allgemeine Tools sind in Cloud Shell vorinstalliert und für die Verwendung mit Ihrem Konto konfiguriert. Wählen Sie einfach die Schaltfläche „Kopieren“ aus, um den Code zu kopieren. Fügen Sie ihn anschließend in Cloud Shell ein, und drücken Sie die EINGABETASTE, um ihn auszuführen. Cloud Shell kann auf mehrere Arten geöffnet werden:

- Wählen Sie rechts oben in einem Codeblock die Option „Ausprobieren“ aus.
- Öffnen Sie Cloud Shell in Ihrem Browser.
- Wählen Sie im Menü rechts oben im Azure-Portal die Schaltfläche „Cloud Shell“ aus.

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie als Voraussetzung für diesen Artikel mindestens Version 2.22.1 der Azure CLI ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie im Artikel Installieren der Azure-Befehlszeilenschnittstelle.

1. Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe.
1. Erstellen Sie mit [az vm create](/cli/azure/vm#az_vm_create&preserve-view=true) einen virtuellen Computer unter Verwendung einer unterstützten Distribution in einer unterstützten Region.
1. Installieren Sie mit [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az_vm_extension_set&preserve-view=true) die VM-Erweiterung für die Azure AD-Anmeldung.

Im folgenden Beispiel wird der virtuelle Computer *myVM*, der *Ubuntu 18.04 LTS* verwendet, in der Ressourcengruppe *AzureADLinuxVMPreview* in der Region *southcentralus* bereitgestellt. Anschließend wird die *VM-Erweiterung für die Azure AD-Anmeldung* installiert, um die Azure AD-Anmeldung für den virtuellen Linux-Computer zu aktivieren. VM-Erweiterungen sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen.

Das Beispiel kann angepasst werden, um den Bedarf für Ihre Testanforderungen zu unterstützen.

```azurecli-interactive
az group create --name AzureADLinuxVMPreview --location southcentralus

az vm create \
    --resource-group AzureADLinuxVMPreview \
    --name myVM \   
    --image UbuntuLTS \
    --assign-identity \
    --admin-username azureuser \
    --generate-ssh-keys

az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADSSHLoginForLinux \
    --resource-group AzureADLinuxVMPreview \
    --vm-name myVM
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten.

Die Erweiterung „AADSSHLoginForLinux“ kann auf einem vorhandenen virtuellen Linux-Computer (unterstützte Distribution), auf dem ein VM-Agent ausgeführt wird, installiert werden, um die Azure AD Authentifizierung zu aktivieren. Wenn Sie diese Erweiterung für einen zuvor erstellten virtuellen Computer bereitstellen, müssen Sie sicherstellen, dass auf dem Computer mindestens 1 GB Arbeitsspeicher zugewiesen ist, da die Erweiterung andernfalls nicht installiert werden kann.

Nachdem die Installation der Erweiterung auf dem virtuellen Computer erfolgreich abgeschlossen wurde, wird für provisioningState der Wert Succeeded angezeigt. Auf dem virtuellen Computer muss ein [VM-Agent](../../virtual-machines/extensions/agent-linux.md) ausgeführt werden, damit die Erweiterung installiert werden kann.

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurieren der Rollenzuweisungen für den virtuellen Computer

Nach dem Erstellen des virtuellen Computers müssen Sie eine Azure-RBAC-Richtlinie konfigurieren, um festzulegen, wer sich bei dem virtuellen Computer anmelden kann. Zur Autorisierung der VM-Anmeldung werden zwei Azure-Rollen verwendet:

- **VM-Administratoranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit Administratorberechtigungen bei einem virtuellen Azure-Computer anmelden.
- **VM-Benutzeranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit normalen Benutzerberechtigungen bei einem virtuellen Azure-Computer anmelden.
 
Damit sich ein Benutzer über SSH bei dem virtuellen Computer anmelden kann, müssen Sie ihm entweder die Rolle „VM-Administratoranmeldung“ oder die Rolle „VM-Benutzeranmeldung“ zuweisen. Ein Azure-Benutzer, dem die Rolle „Besitzer“ oder „Mitwirkender“ für einen virtuellen Computer zugewiesen ist, verfügt nicht automatisch über die Berechtigungen für die Azure AD-Anmeldung über SSH bei dem virtuellen Computer. Dadurch wird eine überprüfbare Trennung zwischen den Personen, die virtuelle Computer steuern können, und den Personen, die auf virtuelle Computer zugreifen können, ermöglicht. 

Es gibt mehrere Möglichkeiten, Rollenzuweisungen für virtuelle Computer zu konfigurieren. Zum Beispiel:

- Im Azure AD-Portal
- Mit der Azure Cloud Shell

> [!Note]
> Die Rollen „VM-Administratoranmeldung“ und „VM-Benutzeranmeldung“ verwenden dataActions und können daher nicht im Bereich der Verwaltungsgruppe zugewiesen werden. Diese Rollen können derzeit nur auf Abonnement-, Ressourcengruppen- oder Ressourcenebene zugewiesen werden. Es wird empfohlen, die Rollen auf Abonnement- oder Ressourcenebene und nicht auf der Ebene einzelner virtueller Computer zuzuweisen, um nicht zu riskieren, das [Limit für Azure-Rollenzuweisungen](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit) pro Abonnement zu überschreiten.

### <a name="using-azure-ad-portal-experience"></a>Verwenden des Azure AD-Portals

So konfigurieren Sie Rollenzuweisungen für Azure AD-fähige virtuelle Linux-Computer

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgende Rolle zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Rolle | **VM-Administratoranmeldung** oder **VM-Benutzeranmeldung** |
    | Zugriff zuweisen zu | Benutzer, Gruppe, Dienstprinzipal oder verwaltete Identität |

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

Nach einigen Augenblicken wird dem Sicherheitsprinzipal die Rolle für den Bereich zugewiesen.
 
### <a name="using-the-azure-cloud-shell-experience"></a>Verwenden von Azure Cloud Shell

Im folgenden Beispiel wird [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) verwendet, um dem aktuellen Azure-Benutzer die Rolle „VM-Administratoranmeldung“ für den virtuellen Computer zuzuweisen. Der Benutzername des aktuellen Azure-Kontos wird mit [az account show](/cli/azure/account#az_account_show) abgerufen. Der Bereich wird mit [az vm show](/cli/azure/vm#az_vm_show) auf den in einem vorherigen Schritt erstellten virtuellen Computer festgelegt. Der Bereich kann auch auf Ressourcengruppen- oder Abonnementebene zugewiesen werden, wobei die normalen Azure RBAC-Vererbungsrechte Gültigkeit haben.

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group AzureADLinuxVMPreview --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Wenn die Azure AD-Domäne und die Domäne des Benutzeranmeldenamens nicht übereinstimmen, müssen Sie die Objekt-ID des Benutzerkontos mit `--assignee-object-id` angeben. Die Angabe des Benutzernamens für `--assignee` genügt nicht. Sie können die Objekt-ID für Ihr Benutzerkonto mithilfe der [Azure Active Directory-Benutzerliste (az as user list)](/cli/azure/ad/user#az_ad_user_list) erhalten.

Weitere Informationen zur Verwendung der rollenbasierten Azure-Zugriffssteuerung (Azure RBAC) zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen finden Sie im Artikel [Schritte zum Zuweisen einer Azure-Rolle](../../role-based-access-control/role-assignments-steps.md).

## <a name="install-ssh-extension-for-az-cli"></a>Installieren der SSH-Erweiterung für die Azure CLI

Wenn Sie Azure Cloud Shell verwenden, ist kein anderes Setup erforderlich, da sowohl die mindestens erforderliche Version der Azure CLI als auch die SSH-Erweiterung für die Azure CLI bereits in der Cloud Shell-Umgebung enthalten sind.

Führen Sie den folgenden Befehl aus, um die SSH-Erweiterung für die Azure CLI hinzuzufügen:

```azurecli
az extension add --name ssh
```

Für die Erweiterung ist mindestens Version 0.1.4 erforderlich. Überprüfen Sie die Version der installierten SSH-Erweiterung mit dem folgenden Befehl.

```azurecli
az extension show --name ssh
```

## <a name="using-conditional-access"></a>Verwenden von bedingtem Zugriff

Sie können Richtlinien für bedingten Zugriff erzwingen. Dazu zählen z. B. „MFA erforderlich“ für den Benutzer oder „Kompatibles/Hybrid in Azure AD eingebundenes Gerät erforderlich“ für das Gerät, auf dem der SSH-Client ausgeführt wird. Sie können den Benutzer auch auf ein niedriges Benutzer- und Anmelderisiko überprüfen, bevor Sie den Zugriff auf virtuelle Linux-Computer in Azure autorisieren, auf denen die Azure AD-Anmeldung aktiviert ist. 

Um Richtlinien für bedingten Zugriff anzuwenden, müssen Sie in der Cloud-Apps- oder Aktionszuweisungsoption die App „Azure-Anmeldung für Linux-VM“ auswählen. Verwenden Sie dann das Benutzer- und/oder Anmelderisiko als Bedingung und „Zugriff gewähren“ als Zugriffssteuerung, nachdem die Bedingung „MFA erforderlich“ und/oder „Kompatibles/Hybrid in Azure AD eingebundenes Gerät erforderlich“ erfüllt wurde.

> [!NOTE]
> Eine Richtlinie für bedingten Zugriff, die auf dem Clientgerät für die Ausführung des SSH-Clients ein kompatibles oder hybrid in Azure AD eingebundenes Gerät erfordert, kann nur erzwungen werden, wenn die Azure CLI unter Windows und macOS ausgeführt wird. Diese Funktion wird nicht unterstützt, wenn Sie die Azure CLI mit Linux oder Azure Cloud Shell verwenden.

## <a name="login-using-azure-ad-user-account-to-ssh-into-the-linux-vm"></a>Anmelden mit dem Azure AD-Benutzerkonto bei SSH auf dem virtuellen Linux-Computer

### <a name="using-az-cli"></a>Verwenden der Azure CLI

Führen Sie zuerst „az login“ und dann „az ssh vm“ aus.

```azurecli
az login 
```

Mit diesem Befehl wird ein Browserfenster gestartet, und ein Benutzer kann sich mit seinem Azure AD-Konto anmelden. 

Im folgenden Beispiel wird die entsprechende IP-Adresse für den virtuellen Computer automatisch aufgelöst.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

Geben Sie, wenn Sie dazu aufgefordert werden, auf der Anmeldeseite Ihre Azure AD-Anmeldeinformationen ein, führen Sie eine mehrstufige Authentifizierung (MFA) aus, und/oder erfüllen Sie die Geräteanforderungen. Sie werden nur aufgefordert, wenn Ihre Azure CLI-Sitzung die erforderlichen Kriterien für den bedingten Zugriff noch nicht erfüllt. Schließen Sie das Browserfenster, und kehren Sie zur SSH-Eingabeaufforderung zurück. Sie werden automatisch mit dem virtuellen Computer verbunden.

Sie sind nun mit den entsprechend zugewiesenen Rollenberechtigungen (z.B. VM-Benutzer oder VM-Administrator) bei dem virtuellen Azure Linux-Computer angemeldet. Wenn Ihrem Benutzerkonto die Rolle „VM-Administratoranmeldung“ zugewiesen ist, können Sie mit „sudo“ Befehle ausführen, für die Stammberechtigungen erforderlich sind.

### <a name="using-az-cloud-shell"></a>Verwenden von Azure Cloud Shell

Sie können mit Azure Cloud Shell eine Verbindung zu virtuellen Computern herzustellen, ohne eine lokale Installation auf Ihrem Clientcomputer vornehmen zu müssen. Starten Sie Azure Cloud Shell, indem Sie auf das Shellsymbol in der oberen rechten Ecke des Azure-Portals klicken.
 
Azure Cloud Shell stellt automatisch im Kontext des angemeldeten Benutzers eine Verbindung zu einer Sitzung her. Während der Vorschauphase von „Azure AD-Anmeldung für Linux“ **müssen Sie „az login“ erneut ausführen und einen interaktiven Anmeldeflow durchlaufen**.

```azurecli
az login
```

Anschließend können Sie die normalen Befehle „az ssh vm“ verwenden, um mit dem Namen und der Ressourcengruppe oder der IP-Adresse des virtuellen Computers eine Verbindung herzustellen.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

> [!NOTE]
> Das Erzwingen einer Richtlinie für bedingten Zugriff, die eine Gerätekonformität oder ein hybrid in Azure AD eingebundenes Gerät erfordert, wird bei der Verwendung von Azure Cloud Shell nicht unterstützt.

### <a name="login-using-azure-ad-service-principal-to-ssh-into-the-linux-vm"></a>Anmelden mit dem Azure AD-Dienstprinzipal bei SSH auf dem virtuellen Linux-Computer

Die Azure CLI unterstützt die Authentifizierung mit einem Dienstprinzipal anstelle eines Benutzerkontos. Da Dienstprinzipale Konten sind, die nicht an einen bestimmten Benutzer gebunden sind, können Kunden damit eine SSH-Verbindung zu einem virtuellen Computer herstellen und Automatisierungsszenarien unterstützen. Dem Dienstprinzipal müssen VM-Administrator- oder VM-Benutzerrechte zugewiesen sein. Weisen Sie Berechtigungen auf Abonnement- oder Ressourcengruppenebene zu. 

Im folgenden Beispiel werden dem Dienstprinzipal auf Ressourcengruppenebene VM-Administratorrechte zugewiesen. Ersetzen Sie die Felder „Dienstprinzipalobjekt-ID“, „Abonnement-ID“ und „Ressourcengruppenname“.

```azurecli
az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee-object-id <service-principal-objectid> \
    --assignee-principal-type ServicePrincipal \
    --scope “/subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>"
```

Verwenden Sie das folgende Beispiel, um mithilfe des Dienstprinzipals eine Authentifizierung bei der Azure CLI vorzunehmen. Weitere Informationen zum Anmelden mit einem Dienstprinzipal finden Sie im Artikel [Anmelden bei Azure CLI mit einem Dienstprinzipal](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal).

```azurecli
az login --service-principal -u <sp-app-id> -p <password-or-cert> --tenant <tenant-id>
```

Nachdem die Authentifizierung mit einem Dienstprinzipal abgeschlossen ist, verwenden Sie die normalen „Az CLI SSH“-Befehle, um eine Verbindung zum virtuellen Computer herzustellen.

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

### <a name="exporting-ssh-configuration-for-use-with-ssh-clients-that-support-openssh"></a>Exportieren der SSH-Konfiguration für die Verwendung mit SSH-Clients, die OpenSSH unterstützen

Bei der Anmeldung bei einem virtuellen Linux-Computer in Azure mithilfe von Azure AD wird der Export des OpenSSH-Zertifikats und der Konfiguration unterstützt, sodass Sie für die Anmeldung bei Azure AD alle SSH-Clients verwenden können, die OpenSSH-basierte Zertifikate unterstützen. Im folgenden Beispiel wird die Konfiguration für alle IP-Adressen, die dem virtuellen Computer zugewiesen sind, exportiert.

```azurecli
az ssh config --file ~/.ssh/config -n myVM -g AzureADLinuxVMPreview
```

Alternativ können Sie die Konfiguration exportieren, indem Sie nur die IP-Adresse angeben. Ersetzen Sie die IP-Adresse im Beispiel durch die öffentliche oder private IP-Adresse (für private IP-Adressen müssen Sie Ihre eigene Konnektivität verwenden) für Ihren virtuellen Computer. Geben Sie `az ssh config -h` ein, um Hilfe zu diesem Befehl zu erhalten.

```azurecli
az ssh config --file ~/.ssh/config --ip 10.11.123.456
```

Anschließend können Sie über die normale OpenSSH-Nutzung eine Verbindung zu dem virtuellen Computer herstellen. Die Verbindung kann über einen beliebigen SSH-Client hergestellt werden, der OpenSSH verwendet.

## <a name="sudo-and-azure-ad-login"></a>Sudo und Azure AD-Anmeldung

Wenn Benutzer mit der ihnen zugewiesenen VM-Administratorrolle erfolgreich eine SSH-Verbindung zu einem virtuellen Linux-Computer hergestellt haben, können sie „sudo“ ohne weitere Interaktions- oder Authentifizierungsanforderungen ausführen. Benutzer, denen die VM-Benutzerrolle zugewiesen ist, können „sudo“ nicht ausführen.

## <a name="virtual-machine-scale-set-support"></a>Unterstützung für VM-Skalierungsgruppe

VM-Skalierungsgruppen werden unterstützt, doch sind die Schritte zum Aktivieren und Herstellen einer Verbindung zu den virtuellen Computern der VM-Skalierungsgruppe etwas anders.

1. Erstellen Sie eine VM-Skalierungsgruppe, oder wählen Sie eine bereits vorhandene aus. Aktivieren Sie eine systemseitig zugewiesene verwaltete Identität für Ihre VM-Skalierungsgruppe.

```azurecli
az vmss identity assign --vmss-name myVMSS --resource-group AzureADLinuxVMPreview
```

2. Installieren Sie die Azure AD-Erweiterung in Ihrer VM-Skalierungsgruppe.

```azurecli
az vmss extension set --publisher Microsoft.Azure.ActiveDirectory --name Azure ADSSHLoginForLinux --resource-group AzureADLinuxVMPreview --vmss-name myVMSS
```

In der Regel verfügen VM-Skalierungsgruppen nicht über öffentliche IP-Adressen. Daher müssen Sie von einem anderen Computer, der mit dem virtuellen Azure-Netzwerk verbunden ist, eine Verbindung herstellen. In diesem Beispiel wird gezeigt, wie Sie mithilfe der privaten IP-Adresse eines virtuellen Computers der VM-Skalierungsgruppe eine Verbindung von einem Computer in demselben virtuellen Netzwerk herstellen. 

```azurecli
az ssh vm --ip 10.11.123.456
```

> [!NOTE]
> Sie können die IP-Adressen der virtuellen Computer der VM-Skalierungsgruppe nicht automatisch mithilfe der Switches `--resource-group` und `--name` ermitteln.

## <a name="migration-from-previous-preview"></a>Migration von der vorherigen Vorschauversion

Führen Sie für Kunden, die eine frühere Version der Azure AD-Anmeldung für Linux auf Basis des Gerätecodeflows verwenden, die folgenden Schritte mithilfe von Azure CLI aus:

1. Deinstallieren Sie die Erweiterung „AADLoginForLinux“ auf dem virtuellen Computer.
   
   ```azurecli
   az vm extension delete -g MyResourceGroup --vm-name MyVm -n AADLoginForLinux
   ```

1. Aktivieren Sie eine systemseitig zugewiesene verwaltete Identität auf dem virtuellen Computer.

   ```azurecli
   az vm identity assign -g myResourceGroup -n myVm
   ```

1. Installieren der Erweiterung „AADSSHLoginForLinux“ auf dem virtuellen Computer

    ```azurecli
    az vm extension set \
        --publisher Microsoft.Azure.ActiveDirectory \
        --name AADSSHLoginForLinux \
        --resource-group myResourceGroup \
        --vm-name myVM
    ```

## <a name="using-azure-policy-to-ensure-standards-and-assess-compliance"></a>Verwenden von Azure Policy zum Sicherstellen von Standards und für die Konformitätsbewertung

Verwenden Sie Azure Policy, um sicherzustellen, dass die Azure AD-Anmeldung für Ihre neuen und vorhandenen virtuellen Linux-Computer aktiviert ist, und bewerten Sie bedarfsgerecht die Konformität Ihrer Umgebung auf dem Compliancedashboard von Azure Policy. Mit dieser Funktion können Sie viele Erzwingungsstufen verwenden: Sie können neue und vorhandene virtuelle Linux-Computer in Ihrer Umgebung kennzeichnen, für die keine Azure AD-Anmeldung aktiviert ist. Sie können Azure Policy auch zum Bereitstellen der Azure AD-Erweiterung auf neuen virtuellen Linux-Computern verwenden, auf denen diese noch nicht aktiviert ist. Sie können aber auch vorhandene virtuelle Linux-Computer auf denselben Standard aktualisieren. Neben diesen Funktionen können Sie Azure Policy auch zum Erkennen und Kennzeichnen von virtuellen Linux-Computern verwenden, auf denen nicht genehmigte lokale Konten erstellt wurden. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../governance/policy/overview.md).

## <a name="troubleshoot-sign-in-issues"></a>Beheben von Problemen bei der Anmeldung

Zu den häufig auftretenden Fehlern beim Herstellen einer SSH-Verbindung mit Azure AD-Anmeldeinformationen gehören nicht zugewiesene Azure-Rollen und wiederholte Aufforderungen zur Anmeldung. In den folgenden Abschnitten finden Sie Informationen zum Beheben dieser Probleme.

### <a name="could-not-retrieve-token-from-local-cache"></a>Token konnte nicht aus lokalem Cache abgerufen werden

Sie müssen „az login“ erneut ausführen und einen interaktiven Anmeldeflow durchlaufen. Lesen Sie den Abschnitt [Verwenden von Azure Cloud Shell](#using-az-cloud-shell).

### <a name="access-denied-azure-role-not-assigned"></a>Zugriff verweigert: Azure-Rolle nicht zugewiesen

Wenn an der SSH-Eingabeaufforderung die folgende Fehlermeldung angezeigt wird, überprüfen Sie, ob Sie für den virtuellen Computer Azure RBAC-Richtlinien konfiguriert haben, mit denen dem Benutzer entweder die Rolle „VM-Administratoranmeldung“ oder die Rolle „VM-Benutzeranmeldung“ zugewiesen wurde. Wenn Probleme mit Azure-Rollenzuweisungen auftreten, finden Sie weitere Informationen im Artikel [Behandeln von Problemen bei Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).

### <a name="extension-install-errors"></a>Fehler bei der Installation der Erweiterung

Die Installation der VM-Erweiterung „AADSSHLoginForLinux“ auf vorhandenen Computern schlägt mit einem der folgenden bekannten Fehlercodes fehl:

#### <a name="non-zero-exit-code-22"></a>Exitcode ungleich null: 22

Der Status der VM-Erweiterung „AADSSHLoginForLinux“ wird im Portal als „Übergang“ angezeigt.

Ursache 1: Dieser Fehler ist darauf zurückzuführen, dass eine systemseitig zugewiesene verwaltete Identität erforderlich ist.

Lösung 1: Führen Sie die folgenden Aktionen aus:

1. Deinstallieren Sie die Erweiterung, bei der der Fehler aufgetreten ist.
1. Aktivieren Sie eine systemseitig zugewiesene verwaltete Identität auf dem virtuellen Azure-Computer.
1. Führen Sie den Installationsbefehl für die Erweiterung erneut aus.

#### <a name="non-zero-exit-code-23"></a>Exitcode ungleich null: 23

Der Status der VM-Erweiterung „AADSSHLoginForLinux“ wird im Portal als „Übergang“ angezeigt.

Ursache 1: Dieser Fehler ist darauf zurückzuführen, dass die ältere VM-Erweiterung „AADLoginForLinux“ noch installiert ist.

Lösung 1: Führen Sie die folgenden Aktionen aus:

1. Deinstallieren Sie die ältere VM-Erweiterung „AADLoginForLinux“ auf dem virtuellen Computer. Der Status der neuen VM-Erweiterung „AADSSHLoginForLinux“ wird im Portal in „Bereitstellung erfolgreich“ geändert.

#### <a name="az-ssh-vm-fails-with-keyerror-access_token"></a>Fehler bei „Az ssh vm“ mit KeyError: „access_token“.

Ursache 1: Eine veraltete Version des Azure CLI-Clients wird verwendet.

Lösung 1: Aktualisieren Sie den Azure CLI-Client auf Version 2.21.0 oder höher.

#### <a name="ssh-connection-closed"></a>SSH-Verbindung wurde geschlossen

Nachdem sich der Benutzer mit „az login“ erfolgreich angemeldet hat, schlägt bei Verwendung von `az ssh vm -ip <addres>` oder `az ssh vm --name <vm_name> -g <resource_group>` die Verbindung zum virtuellen Computer mit der Fehlermeldung *Verbindung geschlossen durch <IP-Adresse> Port 22* fehl.

Ursache 1: Dem Benutzer ist im Bereich dieses virtuellen Computers weder die Azure RBAC-Rolle „VM-Administratoranmeldung“ noch „VM-Benutzeranmeldung“ zugewiesen.

Lösung 1: Weisen Sie dem Benutzer im Bereich dieses virtuellen Computers entweder die Azure RBAC-Rolle „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“ zu.

Ursache 2: Der Benutzer verfügt zwar über eine erforderliche Azure RBAC-Rolle, aber die systemseitig zugewiesene verwaltete Identität wurde auf dem virtuellen Computer deaktiviert.

Lösung 2: Führen Sie die folgenden Aktionen aus:

1. Aktivieren Sie die systemseitig zugewiesene verwaltete Identität auf dem virtuellen Computer.
1. Warten Sie einige Minuten, bevor Sie versuchen, eine Verbindung mit `az ssh vm --ip <ip_address>` herzustellen.

### <a name="virtual-machine-scale-set-connection-issues"></a>Verbindungsprobleme bei VM-Skalierungsgruppen

Bei Verbindungen zu VM-Skalierungsgruppen können Fehler auftreten, wenn auf den VM-Skalierungsgruppeninstanzen ein altes Modell ausgeführt wird. Durch ein Upgrade der VM-Skalierungsgruppeninstanzen auf das neueste Modell können die Probleme möglicherweise behoben werden, insbesondere dann, wenn seit der Installation der Erweiterung für die Azure AD-Anmeldung kein Upgrade durchgeführt wurde. Beim Upgrade einer Instanz wird eine Standardkonfiguration für VM-Skalierungsgruppen auf die jeweilige Instanz angewendet.

## <a name="preview-feedback"></a>Feedback zur Vorschauversion

Geben Sie Feedback zu dieser Previewfunktion, oder melden Sie Probleme bei der Verwendung der Funktion im [Azure AD-Feedbackforum](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).

## <a name="next-steps"></a>Nächste Schritte
