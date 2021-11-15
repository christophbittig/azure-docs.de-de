---
title: Authentifizierungsoptionen für die Registrierung
description: Hier erfahren Sie mehr über Authentifizierungsoptionen für eine private Azure-Containerregistrierung wie z. B. das Anmelden mit einer Azure Active Directory-Identität, mithilfe von Dienstprinzipalen sowie mittels optionalen Administratoranmeldeinformationen.
ms.topic: article
ms.date: 06/16/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7c6510247ff6900145241834841964271db6530b
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578961"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Authentifizieren mit einer Azure-Containerregistrierung

Es gibt mehrere Möglichkeiten, sich mit einer Azure-Containerregistrierung zu authentifizieren, von denen jede für eine oder mehrere Registrierungsverwendungsszenarien gilt.

Empfohlene Methoden:

* Direkte Authentifizierung bei einer Registrierung über die [individuelle Anmeldung](#individual-login-with-azure-ad)
* Anwendungen und Containerorchestratoren können eine unbeaufsichtigte (oder „monitorlose“) Authentifizierung mithilfe eines Azure AD-[Dienstprinzipals](#service-principal) (Azure Active Directory) durchführen.

Wenn Sie eine Containerregistrierung mit Azure Kubernetes Service (AKS) oder einem anderen Kubernetes-Cluster verwenden, finden Sie weitere Informationen unter [Szenarien zum Authentifizieren per Azure Container Registry über Kubernetes](authenticate-kubernetes-options.md).

## <a name="authentication-options"></a>Authentifizierungsoptionen

In der folgenden Tabelle werden die verfügbaren Authentifizierungsmethoden und typische Szenarien aufgeführt. Weitere Informationen finden Sie in den verlinkten Inhalten.

| Methode                               | Authentifizierung                                           | Szenarien                                                            | Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)                             | Einschränkungen                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuelle AD-Identität](#individual-login-with-azure-ad)                | `az acr login`  in der Azure CLI<br/><br/> `Connect-AzContainerRegistry` in Azure PowerShell                             | Interaktiver Push/Pull von Entwicklern oder Testern                                    | Ja                              | Das AD-Token muss alle 3 Stunden erneuert werden.     |
| [AD-Dienstprinzipal](#service-principal)                  | `docker login`<br/><br/>`az acr login` in der Azure CLI<br/><br/> `Connect-AzContainerRegistry` in Azure PowerShell<br/><br/> Registrierungsanmeldungseinstellungen in APIs oder Tools<br/><br/> [PullSecret in Kubernetes](container-registry-auth-kubernetes.md)                                           | Unbeaufsichtigter Push aus der CI/CD-Pipeline<br/><br/> Unbeaufsichtigter Pull in Azure oder externe Dienste  | Ja                              | Standardablaufzeit für Dienstprinzipalkennwörter beträgt 1 Jahr       |
| [Verwaltete Identität für Azure-Ressourcen](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login`  in der Azure CLI<br/><br/> `Connect-AzContainerRegistry` in Azure PowerShell                                       | Unbeaufsichtigter Push aus der Azure CI/CD-Pipeline<br/><br/> Unbeaufsichtigter Pull in Azure-Dienste<br/><br/>   | Ja                              | Kann nur über Azure-Dienste verwendet werden, die [verwaltete Identitäten für Azure-Ressourcen unterstützen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Verwaltete Identität des AKS-Clusters](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Registrierung anfügen, wenn AKS-Cluster erstellt oder aktualisiert werden  | Unbeaufsichtigter Pull des AKS-Clusters im selben oder in einem anderen Abonnement                                                 | Nein, Zugriff ist nur per Pull möglich             | Nur mit AKS-Cluster verfügbar<br/><br/>Kann nicht für die mandantenübergreifende Authentifizierung verwendet werden            |
| [Dienstprinzipal des AKS-Clusters](authenticate-aks-cross-tenant.md)                    | Aktivieren, wenn AKS-Cluster erstellt oder aktualisiert werden  | Unbeaufsichtigter Pull des AKS-Clusters aus der Registrierung in einem anderen AD-Mandanten                                                  | Nein, Zugriff ist nur per Pull möglich             | Nur mit AKS-Cluster verfügbar            |
| [Administratorbenutzer](#admin-account)                            | `docker login`                                          | Interaktiver Push/Pull durch einen einzelnen Entwickler oder Tester<br/><br/>Portalbereitstellung eines Images aus der Registrierung in Azure App Service oder Azure Container Instances                      | Nein, der Zugriff erfolgt immer per Pull/Push.  | Nur ein Konto pro Registrierung, wird nicht für mehrere Benutzer empfohlen         |
| [Repositorybezogene Zugriffstoken](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` in der Azure CLI<br/><br/> `Connect-AzContainerRegistry` in Azure PowerShell<br/><br/> [PullSecret in Kubernetes](container-registry-auth-kubernetes.md)    | Interaktiver Push/Pull zum Repository durch einen einzelnen Entwickler oder Tester<br/><br/> Unbeaufsichtigter Pull aus dem Repository durch ein einzelnes System oder ein externes Gerät                  | Ja                              | Derzeit nicht mit AD-Identität integriert  |

## <a name="individual-login-with-azure-ad"></a>Individuelle Anmeldung bei Azure AD

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie direkt mit Ihrer Registrierung arbeiten, z. B. beim Pullen von Images auf eine bzw. beim Pushen von Images von einer Entwicklungsarbeitsstation in eine von Ihnen erstellte Registrierung, authentifizieren Sie sich mithilfe Ihrer individuellen Azure-Identität. Melden Sie sich mit [az login](/cli/azure/reference-index#az_login) bei der [Azure CLI](/cli/azure/install-azure-cli) an, und führen Sie dann den Befehl [az acr login](/cli/azure/acr#az_acr_login) aus:

```azurecli
az login
az acr login --name <acrName>
```

Bei der Anmeldung mit `az acr login` verwendet die CLI das Token, das erstellt wurde, als Sie `az login` ausgeführt haben, zur nahtlosen Authentifizierung Ihrer Sitzung mit Ihrer Registrierung. Die Docker CLI und der Docker Daemon müssen in Ihrer Umgebung installiert sein und ausgeführt werden, damit der Authentifizierungsfluss abgeschlossen werden kann. `az acr login` verwendet den Docker-Client, um ein Azure Active Directory-Token in der Datei `docker.config` festzulegen. Sobald Sie sich auf diese Weise angemeldet haben, werden Ihre Anmeldeinformationen zwischengespeichert, und nachfolgende `docker`-Befehle in Ihrer Sitzung benötigen weder einen Benutzernamen noch das Kennwort.

> [!TIP]
> Verwenden Sie außerdem `az acr login` zum Authentifizieren einer einzelnen Identität, wenn Sie Artefakte an Ihre Registrierung pushen/pullen möchten, die keine Docker-Images sind (z. B. [OCI-Artefakte](container-registry-oci-artifacts.md)).

Für den Zugriff auf die Registrierung ist das von `az acr login` verwendete Token **3 Stunden** lang gültig. Daher empfehlen wir Ihnen, sich immer bei der Registrierung anzumelden, bevor Sie einen `docker`-Befehl ausführen. Wenn das Token abgelaufen ist, können Sie es aktualisieren, indem Sie den `az acr login`-Befehl erneut zur Authentifizierung verwenden.

Die Verwendung von `az acr login` mit Azure-Identitäten ermöglicht [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). In einigen Szenarien können Sie sich bei einer Registrierung mit Ihrer eigenen individuellen Identität in Azure AD anmelden, oder Sie konfigurieren andere Azure-Benutzer mit spezifischen [Azure-Rollen und Berechtigungen](container-registry-roles.md). Bei dienstübergreifenden Szenarios oder zum Erfüllen der Anforderungen einer Arbeitsgruppe oder eines Entwicklungsworkflows, für die Sie den Zugriff nicht individuell verwalten möchten, können Sie sich auch mit einer [verwalteten Identität für Azure-Ressourcen anmelden](container-registry-authentication-managed-identity.md).

### <a name="az-acr-login-with---expose-token"></a>„az acr login“ mit „--expose-token“

In einigen Fällen müssen Sie sich mit `az acr login` authentifizieren, wenn der Docker-Daemon nicht in Ihrer Umgebung ausgeführt wird. Beispielsweise kann es erforderlich sein, `az acr login` in einem Skript in Azure Cloud Shell auszuführen, das die Docker CLI bereitstellt, den Docker-Daemon aber nicht ausführt.

Führen Sie für dieses Szenario `az acr login` zuerst mit dem Parameter `--expose-token` aus. Diese Option macht ein Zugriffstoken verfügbar, anstatt sich über die Docker CLI anzumelden.

```azurecli
az acr login --name <acrName> --expose-token
```

Die Ausgabe zeigt das hier abgekürzte Zugriffstoken an:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
```
Für die Registrierungs-Authentifizierung wird empfohlen, die Token-Anmeldeinformationen an einem sicheren Ort zu speichern und die empfohlenen Verfahren zur Verwaltung von [Docker-Anmeldeinformationen](https://docs.docker.com/engine/reference/commandline/login/) zu befolgen. Speichern Sie z. b. den Tokenwert in einer Umgebungsvariablen:

```bash
TOKEN=$(az acr login --name <acrName> --expose-token --output tsv --query accessToken)
```

Führen Sie dann `docker login` aus, wobei Sie `00000000-0000-0000-0000-000000000000` als Benutzernamen über und das Zugriffstoken als Kennwort verwenden:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password $TOKEN
```
Ebenso können Sie das von `az acr login` zurückgegebene Token mit dem `helm registry login`-Befehl verwenden, um sich bei der Registrierung zu authentifizieren:

```console
echo $TOKEN | helm registry login myregistry.azurecr.io \
            --username 00000000-0000-0000-0000-000000000000 \
            --password-stdin
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Wenn Sie direkt mit Ihrer Registrierung arbeiten, z. B. beim Pullen von Images auf eine bzw. beim Pushen von Images von einer Entwicklungsarbeitsstation in eine von Ihnen erstellte Registrierung, authentifizieren Sie sich mithilfe Ihrer individuellen Azure-Identität. Melden Sie sich mit [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei [Azure PowerShell](/powershell/azure/uninstall-az-ps) an, und führen Sie dann das Cmdlet [Connect-AzContainerRegistry](/powershell/module/az.containerregistry/connect-azcontainerregistry) aus:

```azurepowershell
Connect-AzAccount
Connect-AzContainerRegistry -Name <acrName>
```

Bei der Anmeldung mit `Connect-AzContainerRegistry` verwendet PowerShell das bei der Ausführung von `Connect-AzAccount` erstellte Token zur nahtlosen Authentifizierung Ihrer Sitzung bei Ihrer Registrierung. Die Docker CLI und der Docker Daemon müssen in Ihrer Umgebung installiert sein und ausgeführt werden, damit der Authentifizierungsfluss abgeschlossen werden kann. `Connect-AzContainerRegistry` verwendet den Docker-Client, um ein Azure Active Directory-Token in der Datei `docker.config` festzulegen. Sobald Sie sich auf diese Weise angemeldet haben, werden Ihre Anmeldeinformationen zwischengespeichert, und nachfolgende `docker`-Befehle in Ihrer Sitzung benötigen weder einen Benutzernamen noch das Kennwort.

> [!TIP]
> Verwenden Sie außerdem `Connect-AzContainerRegistry` zum Authentifizieren einer einzelnen Identität, wenn Sie Artefakte an Ihre Registrierung pushen/pullen möchten, die keine Docker-Images sind (z. B. [OCI-Artefakte](container-registry-oci-artifacts.md)).

Für den Zugriff auf die Registrierung ist das von `Connect-AzContainerRegistry` verwendete Token **3 Stunden** lang gültig. Daher empfehlen wir Ihnen, sich immer bei der Registrierung anzumelden, bevor Sie einen `docker`-Befehl ausführen. Wenn das Token abgelaufen ist, können Sie es aktualisieren, indem Sie den `Connect-AzContainerRegistry`-Befehl erneut zur Authentifizierung verwenden.

Die Verwendung von `Connect-AzContainerRegistry` mit Azure-Identitäten ermöglicht [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). In einigen Szenarien können Sie sich bei einer Registrierung mit Ihrer eigenen individuellen Identität in Azure AD anmelden, oder Sie konfigurieren andere Azure-Benutzer mit spezifischen [Azure-Rollen und Berechtigungen](container-registry-roles.md). Bei dienstübergreifenden Szenarios oder zum Erfüllen der Anforderungen einer Arbeitsgruppe oder eines Entwicklungsworkflows, für die Sie den Zugriff nicht individuell verwalten möchten, können Sie sich auch mit einer [verwalteten Identität für Azure-Ressourcen anmelden](container-registry-authentication-managed-identity.md).

---

## <a name="service-principal"></a>Dienstprinzipal

Wenn Sie Ihrer Registrierung einen [Dienstprinzipal](../active-directory/develop/app-objects-and-service-principals.md) zuweisen, kann Ihre Anwendung oder Ihr Dienst diesen für die monitorlose Authentifizierung verwenden. Dienstprinzipale ermöglichen [rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) für eine Registrierung, und Sie können einer Registrierung mehrere Dienstprinzipale zuweisen. Mit mehreren Dienstprinzipalen können Sie unterschiedliche Zugriffsberechtigungen für verschiedene Anwendungen definieren.

Die folgenden Rollen für eine Containerregistrierung sind verfügbar:

* **AcrPull**: Pull

* **AcrPush**: Pull und Push

* **Besitzer**: Pull, Push und Zuweisen von Rollen an andere Benutzer

Eine vollständige Liste der Rollen finden Sie unter [Azure Container Registry – Rollen und Berechtigungen](container-registry-roles.md).

Informationen über CLI-Skripts zum Erstellen eines Dienstprinzipals für die Authentifizierung mit einer Azure-Containerregistrierung und weitere Anweisungen finden Sie unter [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Administratorkonto

Jede Containerregistrierung enthält ein Administratorbenutzerkonto, das standardmäßig deaktiviert ist. Sie können den Administratorbenutzer aktivieren und seine Anmeldeinformationen im Azure-Portal oder über die Azure CLI, Azure PowerShell oder andere Azure-Tools verwalten. Das Administratorkonto besitzt vollständige Berechtigungen für die Registrierung.

Das Administratorkonto ist zurzeit für einige Szenarien erforderlich, um ein Image aus einer Containerregistrierung in bestimmten Azure-Diensten bereitzustellen. Beispielsweise ist das Administratorkonto erforderlich, wenn Sie ein Containerimage im Portal aus einer Registrierung direkt in [Azure Container Instances](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) oder in [Azure-Web-Apps for Containers](container-registry-tutorial-deploy-app.md) bereitstellen.

> [!IMPORTANT]
> Das Administratorkonto ist dafür ausgelegt, dass ein einzelner Benutzer auf die Registrierung zugreift (hauptsächlich für Testzwecke). Sie sollten die Administratorkonto-Anmeldeinformationen nicht für mehrere Benutzer freigeben. Alle Benutzer, die sich mit dem Administratorkonto authentifizieren, werden als ein einzelner Benutzer mit Push- und Pullzugriff auf die Registrierung angezeigt. Wenn dieses Konto geändert oder deaktiviert wird, wird der Zugriff auf die Registrierung für alle Benutzer deaktiviert, die dessen Anmeldeinformationen verwenden. Für Benutzer und Dienstprinzipale wird für monitorlose Szenarien einzelne Identität empfohlen.
>

Das Administratorkonto erhält zwei Kennwörter, die beide erneut generiert werden können. Die beiden Kennwörter ermöglichen Ihnen, Verbindungen mit der Registrierung aufrechtzuerhalten, indem Sie ein Kennwort verwenden, während Sie das andere Kennwort neu generieren. Wenn das Administratorkonto aktiviert ist, können Sie den Benutzernamen und eines der Kennwörter bei Aufforderung an den Befehl `docker login` übergeben, um die Standardauthentifizierung für die Registrierung zu erhalten. Beispiel:

```
docker login myregistry.azurecr.io
```

Bewährte Methoden zur Verwaltung von Docker-Anmeldeinformationen finden Sie in der Befehlsreferenz zu [Docker-Login](https://docs.docker.com/engine/reference/commandline/login/).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um den Administratorbenutzer für eine vorhandene Registrierung zu aktivieren, können Sie den `--admin-enabled`-Parameter des [az acr update](/cli/azure/acr#az_acr_update)-Befehls in der Azure CLI verwenden:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Um den Administratorbenutzer für eine vorhandene Registrierung zu aktivieren, können Sie den Parameter `EnableAdminUser` des Befehls [Update-AzContainerRegistry](/powershell/module/az.containerregistry/update-azcontainerregistry) in Azure PowerShell verwenden:

```azurepowershell
Update-AzContainerRegistry -Name <acrName> -ResourceGroupName myResourceGroup -EnableAdminUser
```

---

Sie können den Administratorbenutzer im Azure-Portal aktivieren, indem Sie zu Ihrer Registrierung navigieren, **Zugriffsschlüssel** unter **EINSTELLUNGEN** und dann **Aktivieren** unter **Administratorbenutzer** auswählen.

![Aktivieren der Administratorbenutzeroberfläche im Azure-Portal][auth-portal-01]

## <a name="next-steps"></a>Nächste Schritte

* [Freigeben Ihres ersten Image mit der Azure CLI](container-registry-get-started-azure-cli.md)

* [Pushen Ihres ersten Images mithilfe von Azure PowerShell](container-registry-get-started-powershell.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
