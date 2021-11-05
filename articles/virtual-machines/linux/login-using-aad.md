---
title: Anmelden bei einem virtuellen Linux-Computer mit Azure Active Directory-Anmeldeinformationen
description: Es wird beschrieben, wie Sie eine Linux-VM erstellen und für die Anmeldung per Azure Active Directory-Authentifizierung konfigurieren.
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/21/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.custom: references_regions
ROBOTS: NOINDEX
ms.openlocfilehash: 5ad78f50685aeb7a5b2133173ae726980012878b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074466"
---
# <a name="deprecated-login-to-a-linux-virtual-machine-in-azure-with-azure-active-directory-using-device-code-flow-authentication"></a>Veraltet: Anmeldung bei einem virtuellen Linux-Computer in Azure mit Azure Active Directory mithilfe der Gerätecode-Flussauthentifizierung

> [!CAUTION]
> **Das in diesem Artikel beschriebene Public Preview-Feature ist seit dem 15. August 15, 2021 veraltet.**
> 
> Dieses Feature wird durch die Möglichkeit ersetzt, Azure AD und SSH über zertifikatbasierte Authentifizierung zu verwenden. Weitere Informationen finden Sie im Artikel [Vorschau: Anmelden bei einem virtuellen Linux-Computer in Azure mit Azure Active Directory mithilfe der zertifikatbasierten SSH-Authentifizierung](../../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md). Informationen zum Migrieren von der alten Version zu dieser Version finden Sie unter [Migration von der vorherigen Vorschauversion](../../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md#migration-from-previous-preview).

Zur Verbesserung der Sicherheit von virtuellen Linux-Computern in Azure können Sie die Integration in die Azure Active Directory-Authentifizierung (AD) durchführen. Bei Verwendung der Azure AD-Authentifizierung für virtuelle Linux-Computer werden Richtlinien, mit denen der Zugriff auf die virtuellen Computer zugelassen oder verweigert wird, zentral gesteuert und erzwungen. In diesem Artikel wird beschrieben, wie Sie einen virtuellen Linux-Computer zur Verwendung der Azure AD-Authentifizierung erstellen und konfigurieren.

Die Verwendung der Azure AD-Authentifizierung für die Anmeldung bei virtuellen Linux-Computern in Azure bietet viele Vorteile, z.B.:

- **Verbesserte Sicherheit:**
  - Sie können sich mit Ihren Unternehmens-AD-Anmeldeinformationen bei virtuellen Linux-Computern in Azure anmelden. Es ist nicht erforderlich, lokale Administratorkonten zu erstellen und die Gültigkeitsdauer von Anmeldeinformationen zu verwalten.
  - Durch die Verringerung der Abhängigkeit von lokalen Administratorkonten müssen Sie sich nicht damit auseinandersetzen, dass Anmeldeinformationen verloren gehen oder gestohlen werden, dass Benutzer unsichere Anmeldeinformationen konfigurieren usw.
  - Auch die für Ihr Azure AD-Verzeichnis konfigurierten Richtlinien zur Kennwortkomplexität und Kennwortgültigkeitsdauer tragen zur Sicherheit von virtuellen Linux-Computern bei.
  - Zum weiteren Schutz der Anmeldung bei virtuellen Azure-Computern können Sie die mehrstufige Authentifizierung konfigurieren.
  - Die Möglichkeit zur Anmeldung bei virtuellen Linux-Computern mit Azure Active Directory besteht auch für Kunden, die [Verbunddienste](../../active-directory/hybrid/how-to-connect-fed-whatis.md) verwenden.

- **Nahtlose Zusammenarbeit:** Mithilfe der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) können Sie angeben, wer sich bei einem bestimmten virtuellen Computer als normaler Benutzer oder als Benutzer mit Administratorrechten anmelden kann. Wenn Benutzer Ihrem Team beitreten oder es verlassen, können Sie die Azure RBAC-Richtlinie für den virtuellen Computer aktualisieren, um den Zugriff entsprechend zuzuweisen. Diese Vorgehensweise ist wesentlich einfacher, als virtuelle Computer bereinigen zu müssen, um unnötige öffentliche SSH-Schlüssel zu entfernen. Wenn Mitarbeiter Ihre Organisation verlassen und ihre Benutzerkonten in Azure AD deaktiviert oder entfernt werden, haben sie keinen Zugriff mehr auf Ihre Ressourcen.

## <a name="supported-azure-regions-and-linux-distributions"></a>Unterstützte Azure-Regionen und Linux-Distributionen

Während der Vorschauphase dieses Features werden derzeit die folgenden Linux-Distributionen unterstützt:

| Distribution | Version |
| --- | --- |
| CentOS | CentOS 6, CentOS 7 |
| Debian | Debian 9 |
| openSUSE | openSUSE Leap 42.3 |
| Red Hat Enterprise Linux | RHEL 6, RHEL 7 | 
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu 14.04 LTS, Ubuntu Server 16.04 und Ubuntu Server 18.04 |

> [!IMPORTANT]
> Die Vorschau wird in einer Azure Government oder Sovereign Cloud nicht unterstützt.
>
> Die Verwendung dieser Erweiterung für AKS-Cluster (Azure Kubernetes Service) wird nicht unterstützt. Weitere Informationen finden Sie unter [Unterstützungsrichtlinien für AKS](../../aks/support-policies.md).

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.31 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="network-requirements"></a>Netzwerkanforderungen

Zum Aktivieren der Azure AD-Authentifizierung für Ihre virtuellen Linux-Computer in Azure müssen Sie sicherstellen, dass die Netzwerkkonfiguration der virtuellen Computer den ausgehenden Zugriff auf die folgenden Endpunkte über TCP-Port 443 zulässt:

* https:\//login.microsoftonline.com
* https:\//login.windows.net
* https:\//device.login.microsoftonline.com
* https:\//pas.windows.net
* https:\//management.azure.com
* https:\//packages.microsoft.com

> [!NOTE]
> Derzeit können Azure-Netzwerksicherheitsgruppen nicht für VMs konfiguriert werden, die mit Azure AD-Authentifizierung aktiviert sind.

## <a name="create-a-linux-virtual-machine"></a>Erstellen einer virtuellen Linux-Maschine

Erstellen Sie mit [az group create](/cli/azure/group#az_group_create) eine Ressourcengruppe und dann mit [az vm create](/cli/azure/vm#az_vm_create) einen virtuellen Computer mit einer unterstützten Distribution und in einer unterstützten Region. Im folgenden Beispiel wird der virtuelle Computer *myVM*, der *Ubuntu 16.04 LTS* verwendet, in der Ressourcengruppe *myResourceGroup* in der Region *southcentralus*  bereitgestellt. In den folgenden Beispielen können Sie den Namen Ihrer Ressourcengruppe und Ihres virtuellen Computers nach Bedarf angeben.

```azurecli-interactive
az group create --name myResourceGroup --location southcentralus

az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten.

## <a name="install-the-azure-ad-login-vm-extension"></a>Installieren der VM-Erweiterung für die Azure AD-Anmeldung

> [!NOTE]
> Wenn Sie diese Erweiterung für eine zuvor erstellte VM bereitstellen, stellen Sie sicher, dass auf dem Computer mindestens 1 GB Arbeitsspeicher zugeordnet ist, da die Erweiterung andernfalls nicht installiert werden kann.

Für die Anmeldung bei einem virtuellen Linux-Computer mit Azure Active Directory-Anmeldeinformationen müssen Sie die VM-Erweiterung für die Azure Active Directory-Anmeldung installieren. VM-Erweiterungen sind kleine Anwendungen, die Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern nach der Bereitstellung ermöglichen. Verwenden Sie [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), um die Erweiterung *AADLoginForLinux* auf dem virtuellen Computer *myVM* in der Ressourcengruppe *myResourceGroup* zu installieren:

```azurecli-interactive
az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory.LinuxSSH \
    --name AADLoginForLinux \
    --resource-group myResourceGroup \
    --vm-name myVM
```

Nachdem die Installation der Erweiterung auf dem virtuellen Computer erfolgreich abgeschlossen wurde, wird für *provisioningState* der Wert *Succeeded* angezeigt. Die VM benötigt einen aktuell ausgeführten VM-Agent, um die Erweiterung zu installieren. Weitere Informationen finden Sie unter [VM-Agent: Übersicht](../extensions/agent-windows.md).

## <a name="configure-role-assignments-for-the-vm"></a>Konfigurieren der Rollenzuweisungen für den virtuellen Computer

Mit der Richtlinie für die rollenbasierte Zugriffssteuerung in Azure (Azure RBAC) wird festgelegt, wer sich bei dem virtuellen Computer anmelden kann. Zur Autorisierung der VM-Anmeldung werden zwei Azure-Rollen verwendet:

- **VM-Administratoranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit den Berechtigungen eines Windows-Administrators oder Linux-Root-Benutzers bei einem virtuellen Azure-Computer anmelden.
- **VM-Benutzeranmeldung:** Benutzer, denen diese Rolle zugewiesen ist, können sich mit normalen Benutzerberechtigungen bei einem virtuellen Azure-Computer anmelden.

> [!NOTE]
> Damit sich ein Benutzer bei dem virtuellen Computer über SSH anmelden kann, müssen Sie ihm einer der Rollen *VM-Administratoranmeldung* oder *VM-Benutzeranmeldung* zuweisen. Die Rollen „Anmeldeinformationen des VM-Administrators“ und „Anmeldeinformationen für VM-Benutzer“ verwenden dataActions und können daher nicht im Bereich der Verwaltungsgruppe zugewiesen werden. Diese Rollen können derzeit nur im Abonnement-, Ressourcengruppen- oder Ressourcenbereich zugewiesen werden. Ein Azure-Benutzer, dem eine der Rollen *Besitzer* oder *Mitwirkender* für einen virtuellen Computer zugewiesen ist, verfügt nicht automatisch über die Berechtigungen für die Anmeldung bei dem virtuellen Computer über SSH. 

Im folgenden Beispiel wird [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) verwendet, um dem aktuellen Azure-Benutzer die Rolle *VM-Administratoranmeldung* für den virtuellen Computer zuzuweisen. Der Benutzername des aktiven Azure-Kontos wird mit [az account show](/cli/azure/account#az_account_show) abgerufen, und der *Bereich* wird mit [az vm show](/cli/azure/vm#az_vm_show) auf den in einem vorherigen Schritt erstellten virtuellen Computer festgelegt. Der Bereich kann auch auf Ebene einer Ressourcengruppe oder eines Abonnements zugewiesen werden. Dann gelten normale Azure RBAC-Vererbungsberechtigungen. Weitere Informationen finden Sie unter [Azure RBAC](../../role-based-access-control/overview.md).

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group myResourceGroup --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> Wenn die AAD-Domäne und die Domäne des Benutzeranmeldenamens nicht übereinstimmen, müssen Sie die Objekt-ID Ihres Benutzerkontos mit *----assignee-object-id* angeben. Die Angabe des Benutzernamens für *--assignee* reicht nicht aus. Sie können die Objekt-ID für Ihr Benutzerkonto mithilfe der [Azure Active Directory-Benutzerliste (az as user list)](/cli/azure/ad/user#az_ad_user_list) erhalten.

Weitere Informationen zur Verwendung von Azure RBAC zum Verwalten des Zugriffs auf Ihre Azure-Abonnementressourcen finden Sie in den Artikeln zur Verwaltung der rollenbasierten Zugriffssteuerung mit der [Azure CLI](../../role-based-access-control/role-assignments-cli.md), über das [Azure-Portal](../../role-based-access-control/role-assignments-portal.md) oder mit [Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="using-conditional-access"></a>Verwenden von bedingtem Zugriff

Sie können Richtlinien für bedingten Zugriff erzwingen, z. B. die mehrstufige Authentifizierung oder Überprüfung des Anmelderisikos für Benutzer, bevor der Zugriff auf virtuelle Linux-Computer in Azure gewährt wird, für die die Azure AD-Anmeldung aktiviert ist. Zum Anwenden einer Richtlinie für bedingten Zugriff müssen Sie die App „Microsoft Azure Linux Virtual Machine Sign-In“ über die Zuweisungsoption für Cloud-Apps oder Aktionen auswählen und dann „Anmelderisiko“ als Bedingung und/oder „Mehrstufige Authentifizierung erforderlich“ als Zugriffssteuerung verwenden. 

> [!WARNING]
> Eine aktivierte/erzwungene Authentifizierung über Azure AD Multi-Factor Authentication pro Benutzer wird für VM-Anmeldungen nicht unterstützt.

## <a name="log-in-to-the-linux-virtual-machine"></a>Anmelden bei dem virtuellen Linux-Computer

Zeigen Sie zunächst die öffentliche IP-Adresse des virtuellen Computers mit dem Befehl [az vm show](/cli/azure/vm#az_vm_show) an:

```azurecli-interactive
az vm show --resource-group myResourceGroup --name myVM -d --query publicIps -o tsv
```

Melden Sie sich mit Ihren Azure AD-Anmeldeinformationen bei dem virtuellen Azure Linux-Computer an. Mit dem Parameter `-l` können Sie die Adresse Ihres Azure AD-Kontos angeben. Ersetzen Sie das Beispielkonto durch Ihr eigenes Konto. Die Kontoadressen müssen ausschließlich in Kleinbuchstaben eingegeben werden. Ersetzen Sie die IP-Beispieladresse durch die öffentliche IP-Adresse Ihres virtuellen Computers aus dem vorherigen Befehl.

```console
ssh -l azureuser@contoso.onmicrosoft.com 10.11.123.456
```

Sie werden aufgefordert, sich unter [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) mit einem einmalig zu verwendenden Code bei Azure AD anzumelden. Kopieren Sie den einmaligen Code, und fügen Sie ihn auf der Anmeldeseite des Geräts ein.

Geben Sie bei der entsprechenden Aufforderung Ihre Azure AD-Anmeldeinformationen auf der Anmeldeseite ein. 

Nach Ihrer erfolgreichen Authentifizierung wird im Webbrowser die folgende Meldung angezeigt: `You have signed in to the Microsoft Azure Linux Virtual Machine Sign-In application on your device.`

Schließen Sie das Browserfenster, kehren Sie zur SSH-Eingabeaufforderung zurück, und drücken Sie die **Eingabetaste**. 

Sie sind nun mit den entsprechend zugewiesenen Rollenberechtigungen (z.B. *VM-Benutzer* oder *VM-Administrator*) bei dem virtuellen Azure Linux-Computer angemeldet. Wenn Ihrem Benutzerkonto die Rolle *VM-Administratoranmeldung* zugewiesen ist, können Sie `sudo` verwenden, um Befehle auszuführen, für die Rootberechtigungen erforderlich sind.

## <a name="sudo-and-aad-login"></a>Sudo- und AAD-Anmeldung

Wenn Sie sudo das erste Mal ausführen, werden Sie aufgefordert, sich ein zweites Mal zu authentifizieren. Wenn Sie sich zur Ausführung von Sudo nicht erneut authentifizieren möchten, können Sie Ihre sudo-Datei `/etc/sudoers.d/aad_admins` bearbeiten und diese Zeile ersetzen:

```bash
%aad_admins ALL=(ALL) ALL
```

Durch diese Zeile:

```bash
%aad_admins ALL=(ALL) NOPASSWD:ALL
```

## <a name="troubleshoot-sign-in-issues"></a>Beheben von Problemen bei der Anmeldung

Zu den häufig auftretenden Fehlern beim Herstellen einer SSH-Verbindung mit Azure AD-Anmeldeinformationen gehören nicht zugewiesene Azure-Rollen und wiederholte Aufforderungen zur Anmeldung. In den folgenden Abschnitten finden Sie Informationen zum Beheben dieser Probleme.

### <a name="access-denied-azure-role-not-assigned"></a>Zugriff verweigert: Azure-Rolle nicht zugewiesen

Wenn an der SSH-Eingabeaufforderung die folgende Fehlermeldung angezeigt wird, überprüfen Sie, ob Sie für den virtuellen Computer Azure RBAC-Richtlinien konfiguriert haben, mit denen dem Benutzer eine der Rollen *VM-Administratoranmeldung* oder *VM-Benutzeranmeldung* zugewiesen wird:

```output
login as: azureuser@contoso.onmicrosoft.com
Using keyboard-interactive authentication.
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code FJX327AXD to authenticate. Press ENTER when ready.
Using keyboard-interactive authentication.
Access denied:  to sign-in you be assigned a role with action 'Microsoft.Compute/virtualMachines/login/action', for example 'Virtual Machine User Login'
Access denied
```
> [!NOTE]
> Wenn Probleme mit Azure-Rollenzuweisungen auftreten, finden Sie weitere Informationen unter [Behandeln von Problemen bei Azure RBAC](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit).

### <a name="continued-ssh-sign-in-prompts"></a>Wiederholte Aufforderungen zur SSH-Anmeldung

Nachdem Sie die Authentifizierung in einem Webbrowser erfolgreich abgeschlossen haben, werden Sie möglicherweise sofort aufgefordert, sich erneut mit einem neuen Code anzumelden. Dieser Fehler wird normalerweise dadurch verursacht, dass der Anmeldename, den Sie an der SSH-Eingabeaufforderung angegeben haben, nicht mit dem Konto übereinstimmt, mit dem Sie sich bei Azure AD angemeldet haben. So beheben Sie dieses Problem

- Überprüfen Sie, ob der an der SSH-Eingabeaufforderung angegebene Anmeldename richtig ist. Ein Tippfehler im Anmeldenamen kann dazu führen, dass der an der SSH-Eingabeaufforderung angegebene Anmeldename nicht mit dem Konto übereinstimmt, mit dem Sie sich bei Azure AD angemeldet haben. Beispiel: Sie geben *azuresuer\@contoso.onmicrosoft.com* anstelle von *azureuser\@contoso.onmicrosoft.com* ein.
- Wenn Sie über mehrere Benutzerkonten verfügen, achten Sie darauf, dass Sie im Browserfenster kein anderes Benutzerkonto als bei der Anmeldung bei Azure AD angeben.
- Linux ist ein Betriebssystem, bei dem die Groß-/Kleinschreibung berücksichtigt wird. Es besteht also ein Unterschied zwischen „Azureuser@contoso.onmicrosoft.com“ und „azureuser@contoso.onmicrosoft.com“. Dies kann zu einer Nichtübereinstimmung führen. Stellen Sie sicher, dass Sie den Benutzerprinzipalnamen (UPN) an der SSH-Eingabeaufforderung mit der richtigen Groß- und Kleinschreibung angeben.

### <a name="other-limitations"></a>Weitere Einschränkungen

Benutzer, die Zugriffsrechte über geschachtelte Gruppen oder Rollenzuweisungen erben, werden zurzeit nicht unterstützt. Dem Benutzer oder der Gruppe müssen die [erforderlichen Rollenzuweisungen ](#configure-role-assignments-for-the-vm) direkt zugewiesen werden. Beispielsweise werden dem Benutzer durch die Verwendung von Verwaltungsgruppen oder geschachtelten Gruppenrollenzuweisungen nicht die richtigen Berechtigungen erteilt, damit er sich anmelden kann.

## <a name="preview-feedback"></a>Feedback zur Vorschauversion

Geben Sie Feedback zu diesem Vorschaufeature oder melden Sie Probleme bei der Verwendung davon im [Azure AD-Feedbackforum](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Active Directory finden Sie unter [Was ist Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).
