---
title: Authentifizieren mit dem Dienstprinzipal
description: Gewähren Sie Zugriff auf Images in Ihrer privaten Containerregistrierung, indem Sie einen Azure Active Directory-Dienstprinzipal verwenden.
ms.topic: article
ms.date: 03/15/2021
ms.openlocfilehash: 117929df4c8b0fa7a54a23bcc039294122cd5afa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651881"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>Azure Container Registry-Authentifizierung mit Dienstprinzipalen

Sie können einen Dienstprinzipal von Azure Active Directory (Azure AD) verwenden, um den Push, Pull oder anderen Zugriff auf Ihre Containerregistrierung zu gewähren. Mithilfe eines Dienstprinzipals können Sie den Zugriff für „monitorlose“ Dienste und Anwendungen ermöglichen.

## <a name="what-is-a-service-principal"></a>Was ist ein Dienstprinzipal?

[*Dienstprinzipale*](../active-directory/develop/app-objects-and-service-principals.md) von Azure AD ermöglichen den Zugriff auf Azure-Ressourcen innerhalb Ihres Abonnements. Sie können sich einen Dienstprinzipal als Benutzeridentität für einen Dienst vorstellen, wobei „Dienst“ jede Anwendung, jeder Dienst oder jede Plattform sein kann, die auf die Ressourcen zugreifen muss. Sie haben die Möglichkeit, einen Dienstprinzipal mit Zugriffsrechten so zu konfigurieren, dass diese nur für die von Ihnen angegebenen Ressourcen gelten. Anschließend legen Sie für Ihre Anwendung bzw. Ihren Dienst fest, dass diese bzw. dieser die Anmeldeinformationen des Dienstprinzipals verwendet, um auf diese Ressourcen zuzugreifen.

Im Rahmen der Azure Container Registry können Sie einen Azure AD-Dienstprinzipal mit Pull-, Push- und Pull- oder anderen Berechtigungen für Ihre private Registrierung in Azure erstellen. Eine vollständige Liste finden Sie unter [Azure Container Registry – Rollen und Berechtigungen](container-registry-roles.md).

## <a name="why-use-a-service-principal"></a>Gründe für die Verwendung eines Dienstprinzipals

Mithilfe eines Azure AD-Dienstprinzipals können Sie bereichsbezogenen Zugriff auf Ihre private Containerregistrierung gewähren. Erstellen Sie für Ihre einzelnen Anwendungen oder Dienste verschiedene Dienstprinzipale, die jeweils über abgestimmte Zugriffsrechte für die Registrierung verfügen. Und da Sie die Anmeldeinformationen nicht an Dienste und Anwendungen weitergeben müssen, haben Sie die Möglichkeit, ausschließlich für den gewählten Dienstprinzipal (und somit für die Anwendung) Anmeldeinformationen weiterzugeben oder Zugriffsrechte zu widerrufen.

Konfigurieren Sie beispielsweise Ihre Webanwendung für die Verwendung eines Dienstprinzipals, der ihr nur Zugriff auf Image `pull` ermöglicht, während Ihr Buildsystem einen Dienstprinzipal verwendet, der ihm sowohl Zugriff auf `push` als auch `pull` bietet. Wenn die Entwicklung Ihrer Anwendung von einer anderen Person übernommen wird, können Sie die Anmeldeinformationen für den Dienstprinzipal weitergeben, ohne das Buildsystem zu beeinflussen.

## <a name="when-to-use-a-service-principal"></a>Einsatzbereiche eines Dienstprinzipals

Sie sollten einen Dienstprinzipal verwenden, um in **monitorlosen Szenarien** Zugriff auf die Registrierung bieten. Das heißt, dies betrifft Anwendungen, Dienste oder Skripte, die Containerimages in automatisierter oder anderweitig unbeaufsichtigter Weise pushen oder pullen müssen. Beispiel:

  * *Pull*: Bereitstellen von Containern aus einer Registrierung für Orchestrierungssysteme, z. B. Kubernetes, DC/OS und Docker Swarm. Sie können auch Pullvorgänge aus Containerregistrierungen in verwandte Azure-Dienste durchführen, z. B. [Azure Container Instances](container-registry-auth-aci.md), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](../service-fabric/index.yml) und weitere Dienste.

    > [!TIP]
    > Ein Dienstprinzipal wird in mehreren [Kubernetes-Szenarien](authenticate-kubernetes-options.md) empfohlen, um Images aus einer Azure-Containerregistrierung zu pullen. Mit Azure Kubernetes Service (AKS) können Sie auch einen automatisierten Mechanismus zur Authentifizierung bei einer Zielregistrierung verwenden, indem Sie die [verwaltete Identität](../aks/cluster-container-registry-integration.md)des Clusters aktivieren. 
  * *Push*: Erstellen von Containerimages und deren Übertragung per Pushvorgang in eine Registrierung mithilfe von Lösungen für Continuous Integration und Continuous Deployment wie Azure Pipelines oder Jenkins.

Für den individuellen Zugriff auf eine Registrierung, etwa, wenn Sie manuell ein Containerimage auf Ihre Entwicklungsarbeitsstation pullen, empfehlen wir, dass Sie stattdessen Ihre eigene [Azure AD-Identität](container-registry-authentication.md#individual-login-with-azure-ad) für den Registrierungszugriff verwenden (beispielsweise mit [az acr login][az-acr-login]).

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

### <a name="sample-scripts"></a>Beispielskripts

Die obigen Beispielskripts für die Azure CLI auf GitHub sowie Versionen für Azure PowerShell finden Sie hier:

* [Azure-Befehlszeilenschnittstelle][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="authenticate-with-the-service-principal"></a>Authentifizieren mit dem Dienstprinzipal

Sobald Sie einem Dienstprinzipal Zugriff auf Ihre Containerregistrierung gewährt haben, können Sie seine Anmeldeinformationen für den Zugriff auf „monitorlose“ Dienste und Anwendungen konfigurieren oder sie mit dem Befehl `docker login` eingeben. Verwenden Sie die folgenden Werte:

* **Benutzername**: **Anwendungs-ID (Client)** des Dienstprinzipals
* **Kennwort**: **Kennwort (geheimer Clientschlüssel)** des Dienstprinzipals

Jeder Wert hat das Format `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

> [!TIP]
> Sie können das Kennwort (geheimer Clientschlüssel) eines Dienstprinzipals neu generieren, indem Sie den Befehl [az ad sp credential reset](/cli/azure/ad/sp/credential#az_ad_sp_credential_reset) ausführen.
>

### <a name="use-credentials-with-azure-services"></a>Verwenden von Anmeldeinformationen mit Azure-Diensten

Sie können die Anmeldeinformationen des Dienstprinzipals in jedem Azure-Dienst verwenden, der mit einer Azure-Containerregistrierung authentifiziert wird.  Dienstprinzipal-Anmeldeinformationen können anstelle der Administratoranmeldeinformationen der Registrierung in einer Vielzahl von Szenarien verwendet werden.

So können Sie mithilfe der Anmeldeinformationen beispielsweise ein Image aus einer Azure-Containerregistrierung in [Azure Container Instances](container-registry-auth-aci.md) pullen.

### <a name="use-with-docker-login"></a>Verwenden mit Docker-Anmeldung

Sie können `docker login` unter Verwendung eines Dienstprinzipals ausführen. Im folgenden Beispiel wird die Anwendungs-ID des Dienstprinzipals in der Umgebungsvariablen `$SP_APP_ID` und das Kennwort in der Variablen `$SP_PASSWD` übergeben. Bewährte Methoden zur Verwaltung von Docker-Anmeldeinformationen finden Sie in der Befehlsreferenz zu [Docker-Login](https://docs.docker.com/engine/reference/commandline/login/).

```bash
# Log in to Docker with service principal credentials
docker login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Nach der Anmeldung speichert Docker die Anmeldeinformationen zwischen.

### <a name="use-with-certificate"></a>Verwenden mit Zertifikat

Wenn Sie Ihrem Dienstprinzipal ein Zertifikat hinzugefügt haben, können Sie sich mit zertifikatbasierter Authentifizierung bei der Azure CLI anmelden und dann den Befehl [az acr login][az-acr-login] verwenden, um auf eine Registrierung zuzugreifen. Die Verwendung eines Zertifikats als Geheimnis anstelle eines Kennworts bietet zusätzliche Sicherheit bei der Verwendung der CLI. 

Ein selbstsigniertes Zertifikat kann erstellt werden, wenn Sie [einen Dienstprinzipal erstellen](/cli/azure/create-an-azure-service-principal-azure-cli). Fügen Sie alternativ einem vorhandenen Dienstprinzipal ein oder mehrere Zertifikate hinzu. Wenn Sie z. B. eines der Skripts in diesem Artikel verwenden, um einen Dienstprinzipal mit Rechten zum Pullen oder Pushen von Images aus einer Registrierung zu erstellen oder zu aktualisieren, fügen Sie mithilfe des [az ad sp credential reset][az-ad-sp-credential-reset]-Befehls ein Zertifikat hinzu.

Um den Dienstprinzipal mit einem Zertifikat zum [Anmelden bei der Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal) zu verwenden, muss das Zertifikat im PEM-Format vorliegen und den privaten Schlüssel enthalten. Wenn das Zertifikat nicht im erforderlichen Format vorliegt, verwenden Sie ein Tool wie `openssl`, um es zu konvertieren. Wenn Sie [az login][az-login] ausführen, um sich mithilfe des Dienstprinzipals bei der CLI anzumelden, geben Sie auch die Anwendungs-ID und die Active Directory-Mandanten-ID des Dienstprinzipals an. Das folgende Beispiel zeigt diese Werte als Umgebungsvariablen:

```azurecli
az login --service-principal --username $SP_APP_ID --tenant $SP_TENANT_ID  --password /path/to/cert/pem/file
```

Führen Sie dann [az acr login][az-acr-login] aus, um sich bei der Registrierung zu authentifizieren:

```azurecli
az acr login --name myregistry
```

Die CLI verwendet das Token, das bei Ihrer Ausführung von `az login` erstellt wurde, um Ihre Sitzung bei der Registrierung zu authentifizieren.

## <a name="create-service-principal-for-cross-tenant-scenarios"></a>Erstellen eines Dienstprinzipals für mandantenübergreifende Szenarien

Ein Dienstprinzipal kann auch in Azure-Szenarien verwendet werden, die das Pullen von Images aus einer Containerregistrierung in einem Azure Active Directory (Mandant) in einen Dienst oder eine App in einer anderen erfordern. Beispielsweise kann eine Organisation eine App in Mandant A ausführen, die ein Image aus einer freigegebenen Containerregistrierung in Mandant B pullen muss.

So erstellen Sie einen Dienstprinzipal, der sich in einem mandantenübergreifenden Szenario bei einer Containerregistrierung authentifizieren kann:

*  Erstellen einer [mehrinstanzenfähigen App](../active-directory/develop/single-and-multi-tenant-apps.md) (Dienstprinzipal) in Mandant A 
* Die App in Mandant B bereitstellen.
* Dem Dienstprinzipal Berechtigungen zum Pullen aus der Registrierung in Mandant B erteilen.
* Den Dienst oder die App in Mandant A für die Authentifizierung mit dem neuen Dienstprinzipal aktualisieren.

Beispielschritte finden Sie unter [Pullen von Images aus einer Containerregistrierung in einen AKS-Cluster in einem anderen AD-Mandanten](authenticate-aks-cross-tenant.md).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Szenarios für die Authentifizierung mit einer Azure-Containerregistrierung finden Sie in der [Authentifizierungsübersicht](container-registry-authentication.md).

* Ein Beispiel für die Verwendung eines Azure-Schlüsseltresors zum Speichern und Abrufen von Dienstprinzipal-Anmeldeinformationen für eine Containerregistrierung finden Sie in dem Tutorial zum [Erstellen und Bereitstellen eines Containerimages mithilfe von ACR Tasks](container-registry-tutorial-quick-task.md).

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-login]: /cli/azure/reference-index#az_login
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
