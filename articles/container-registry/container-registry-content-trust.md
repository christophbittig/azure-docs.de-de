---
title: Verwalten von signierten Images
description: Erfahren Sie, wie Inhaltsvertrauen für Ihre Azure-Containerregistrierung aktiviert wird und signierte Images gepusht und gepullt werden. Das Inhaltsvertrauen implementiert Docker-Inhaltsvertrauen und ist ein Feature der Dienstebene „Premium“.
ms.topic: how-to
ms.date: 07/26/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: e6f4eb302d2ac5f6fc711e438a48a064efef0d27
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450951"
---
# <a name="content-trust-in-azure-container-registry"></a>Inhaltsvertrauen in Azure Container Registry

Azure Container Registry implementiert das Docker-Modell [Inhaltsvertrauen][docker-content-trust], um Push- und Pullvorgänge für signierte Images zu ermöglichen. Dieser Artikel enthält erste Schritte zum Aktivieren des Inhaltsvertrauens in Ihren Containerregistrierungen.

> [!NOTE]
> Das Inhaltsvertrauen ist ein Funktionsmerkmal des [Premium-Tarifs](container-registry-skus.md) von Azure Container Registry.

## <a name="how-content-trust-works"></a>Funktionsweise von Inhaltsvertrauen

Für alle verteilten Systeme, die unter Berücksichtigung von Sicherheitsaspekten entworfen werden, ist es wichtig, sowohl die *Quelle* als auch die *Integrität* von Daten zu überprüfen, die in das System gelangen. Consumer der Daten müssen in der Lage sein, sowohl den Herausgeber (Quelle) der Daten zu überprüfen, als auch sicherzustellen, dass sie nach der Veröffentlichung nicht geändert wurden (Integrität). 

Als Imageherausgeber ermöglicht Ihnen das Inhaltsvertrauen das **Signieren** der Images, die Sie per Push in Ihre Registrierung übertragen. Consumer Ihrer Images (Personen oder Systeme, die Images mittels Pull aus Ihrer Registrierung beziehen) können ihre Clients so konfigurieren, dass *nur* signierte Images bezogen werden. Wenn ein Consumer ein signiertes Image mittels Pull abruft, überprüft der Docker-Client die Integrität des Images. In diesem Modell können sich Consumer darauf verlassen, dass die signierten Images in Ihrer Registrierung tatsächlich von Ihnen veröffentlicht und seit der Veröffentlichung nicht geändert wurden.

### <a name="trusted-images"></a>Vertrauenswürdige Images

Inhaltsvertrauen arbeitet mit den **Tags** in einem Repository. Image-Repositorys können Images mit Bilder mit signierten und unsignierten Tags enthalten. Beispielsweise ist es möglich, nur die Images `myimage:stable` und `myimage:latest` zu signieren, `myimage:dev` jedoch nicht.

### <a name="signing-keys"></a>Signaturschlüssel

Inhaltsvertrauen wird durch die Verwendung einer Gruppe von kryptografischen Signaturschlüssel verwaltet. Diese Schlüssel sind einem bestimmten Repository in einer Registrierung zugeordnet. Es gibt mehrere Typen von Signaturschlüsseln, die Docker-Clients und Ihre Registrierung bei der Verwaltung von Vertrauensstellungen für die Tags in einem Repository verwenden. Wenn Sie das Inhaltsvertrauen aktivieren und in Ihre Containerveröffentlichungs- und -nutzungspipeline integrieren, müssen Sie diese Schlüssel sorgfältig verwalten. Weitere Informationen finden Sie unter [Schlüsselverwaltung](#key-management) weiter unten in diesem Artikel sowie unter [Manage keys for content trust][docker-manage-keys] (Verwalten von Schlüsseln für Inhaltsvertrauen) in der Docker-Dokumentation.

> [!TIP]
> Dies war eine sehr allgemeine Übersicht über das Inhaltsvertrauensmodells von Docker. Eine ausführliche Erläuterung von Inhaltsvertrauen finden Sie unter [Content trust in Docker][docker-content-trust] (Inhaltsvertrauen in Docker).

## <a name="enable-registry-content-trust"></a>Aktivieren von Inhaltsvertrauen in der Registrierung

Der erste Schritt ist das Aktivieren des Inhaltsvertrauens auf Ebene der Registrierung. Nachdem Sie das Inhaltsvertrauen aktiviert haben, können Clients (Benutzer oder Dienste) signierte Image per Push in Ihre Registrierung übertragen. Durch Aktivieren des Inhaltsvertrauens in Ihrer Registrierung wird die Registrierungsnutzung nicht auf Consumer beschränkt, bei denen das Inhaltsvertrauen ebenfalls aktiviert ist. Consumer ohne aktiviertes Inhaltsvertrauen können Ihre Registrierung weiterhin wie gewohnt verwenden. Consumer, in deren Clients das Inhaltsvertrauen aktiviert ist, sehen jedoch *nur* signierte Images in Ihrer Registrierung.

Zum Aktivieren des Inhaltsvertrauens für Ihre Registrierung navigieren Sie zunächst im Azure-Portal zur Registrierung. Wählen Sie unter **Richtlinien** die Optionen **Inhaltsvertrauen** > **Aktiviert** > **Speichern** aus. Sie können auch den Azure CLI-Befehl [az acr config content-trust update][az-acr-config-content-trust-update] verwenden.

![Screenshot, der das Aktivieren von Inhaltsvertrauen für eine Registrierung im Azure-Portal zeigt.][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>Aktivieren von Inhaltsvertrauen für Clients

Zum Arbeiten mit vertrauenswürdigen Images müssen sowohl Imageherausgeber als auch Consumer das Inhaltsvertrauen für ihre Docker-Clients aktivieren. Als Herausgeber können Sie die Images signieren, die Sie per Push an eine Registrierung mit aktiviertem Inhaltsvertrauen übertragen. Als Consumer beschränkt das Aktivieren des Inhaltsvertrauens Ihre Ansicht einer Registrierung auf signierte Images. Inhaltsvertrauen ist in Docker-Clients standardmäßig deaktiviert, Sie können es jedoch für eine Shell-Sitzung oder einen Befehl aktivieren.

Zum Aktivieren von Inhaltsvertrauen für eine Shell-Sitzung legen Sie die Umgebungsvariable `DOCKER_CONTENT_TRUST` auf **1** fest. Beispiel in der Bash-Shell:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Wenn Sie Inhaltsvertrauen stattdessen für einen einzelnen Befehl aktivieren oder deaktivieren möchten, unterstützen mehrere Docker-Befehle das Argument `--disable-content-trust`. Aktivieren von Inhaltsvertrauen für einen einzelnen Befehl:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Wenn Sie Inhaltsvertrauen Ihre Shell-Sitzung aktiviert haben und sie für einen einzelnen Befehl deaktivieren möchten:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Gewähren von Berechtigungen zum Signieren von Images

Nur Benutzer oder Systeme, denen Sie die entsprechende Berechtigung erteilt haben, können vertrauenswürdige Images per Push an Ihre Registrierung übertragen. Um einem Benutzer (oder ein System mit einem Dienstprinzipal) die Berechtigung zur Pushübertragung von Images zu gewähren, teilen Sie dessen Azure Active Directory-Identitäten die Rolle `AcrImageSigner` zu. Dies erfolgt zusätzlich zur Rolle `AcrPush` (oder einer ähnlichen Rolle), die für die Pushübertragung von Images an die Registrierung erforderlich ist. Ausführliche Informationen hierzu finden Sie unter [Azure Container Registry: Rollen und Berechtigungen](container-registry-roles.md).

> [!IMPORTANT]
> Sie können den folgenden Administratorkonten keine Pushberechtigung für vertrauenswürdige Images erteilen: 
> * dem [Administratorkonto](container-registry-authentication.md#admin-account) einer Azure Container Registry
> * einem Benutzerkonto in Azure Active Directory mit der [klassischen Systemadministratorrolle](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

> [!NOTE]
> Ab Juli 2021 umfasst die Rolle `AcrImageSigner` sowohl die Aktion `Microsoft.ContainerRegistry/registries/sign/write` als auch die Datenaktion `Microsoft.ContainerRegistry/registries/trustedCollections/write`.

Details zum Erteilen der Rolle `AcrImageSigner` im Azure-Portal und in der Azure CLI folgen.

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgende Rolle zu. In diesem Beispiel wird die Rolle einem einzelnen Benutzer zugewiesen. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | AcrImageSigner |
    | Zugriff zuweisen zu | User |
    | Members | Alain |

    ![Seite „Rollenzuweisung hinzufügen“ im Azure-Portal](../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="azure-cli"></a>Azure CLI

Um einem Benutzer mit der Azure CLI Berechtigungen zum Signieren zu gewähren, weisen Sie ihm Rolle `AcrImageSigner` zu, die auf Ihre Registrierung beschränkt ist. Das Format des Befehls lautet:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Beispiel: Um einem Benutzer, der kein Administrator ist, die Rolle zu erteilen, können Sie z. B. die folgenden Befehle in einer authentifizierten Azure CLI-Sitzung ausführen. Ändern Sie den Wert von `REGISTRY` in den Namen Ihrer Azure-Containerregistrierung.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)
```

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee azureuser@contoso.com
```

Sie können auch einem [Dienstprinzipal](container-registry-auth-service-principal.md) die Rechte zur Pushübertragung von vertrauenswürdigen Images an Ihre Registrierung gewähren. Die Verwendung eines Dienstprinzipals empfiehlt sich für Buildsysteme und andere unbeaufsichtigte Systeme, die vertrauenswürdige Images per Push an Ihre Registrierung übertragen müssen. Das Format ist mit dem Gewähren einer Benutzerberechtigung vergleichbar, Sie geben jedoch eine Dienstprinzipal-ID als `--assignee`-Wert an.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

Die `<service principal ID>` kann das **appId**- oder **objectId**-Element des Dienstprinzipal oder eines seiner **ServicePrincipalNames**-Elemente sein. Weitere Informationen zum Arbeiten mit Dienstprinzipalen und Azure Container Registry finden Sie unter [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](container-registry-auth-service-principal.md).

> [!IMPORTANT]
> Führen Sie nach Rollenänderungen `az acr login` aus, um das lokale Identitätstoken für die Azure CLI zu aktualisieren, damit die neuen Rollen wirksam werden können. Informationen zur Überprüfung von Rollen für eine Identität finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md) sowie unter [Behandeln von Problemen bei Azure RBAC](../role-based-access-control/troubleshooting.md).

## <a name="push-a-trusted-image"></a>Pushübertragung eines vertrauenswürdigen-Images

Um ein vertrauenswürdiges Image-Tag per Push an Ihre Containerregistrierung zu übertragen, aktivieren Sie das Inhaltsvertrauen und führen die Pushübertragung des Image mit `docker push` durch. Nach Abschluss der ersten Pushübertragung mit einem signierten Tag werden Sie aufgefordert, eine Passphrase für einen Stammsignaturschlüssel und für einen Repositorysignaturschlüssel zu erstellen. Die Stamm- und die Repositoryschlüssel werden generiert und lokal auf Ihrem Computer gespeichert.

```console
$ docker push myregistry.azurecr.io/myimage:v1
[...]
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Nach Ihrem ersten `docker push`-Vorgang mit aktiviertem Inhaltsvertrauen verwendet der Docker-Client für nachfolgende Übertragungen den gleichen Stammschlüssel. Bei jeder nachfolgenden Pushübertragung an dasselbe Repository werden Sie nur nach dem Repositoryschlüssel gefragt. Bei jeder Pushübertragung eines vertrauenswürdigen Images an ein neues Repository werden Sie zur Angabe einer Passphrase für einen neuen Repositoryschlüssel aufgefordert.

## <a name="pull-a-trusted-image"></a>Pull-Abruf eines vertrauenswürdigen-Images

Zum Abrufen eines vertrauenswürdigen Images per Pull aktivieren Sie das Inhaltsvertrauen und führen den Befehl `docker pull` wie gewohnt aus. Für Pullvorgänge für vertrauenswürdige Images ist die Rolle `AcrPull` für normale Benutzer ausreichend. Es sind keine zusätzlichen Rollen (wie die Rolle `AcrImageSigner`) erforderlich. Consumer mit aktiviertem Inhaltsvertrauen können nur Images mit signierten Tags abrufen. Hier ist ein Beispiel für den Pull-Abruf eines signierten Tags:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Wenn ein Client mit aktiviertem Inhaltsvertrauen versucht, ein nicht signiertes Tag abzurufen, schlägt der Vorgang mit einem dem folgenden ähnlichen Fehler fehl:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
Error: remote trust data does not exist
```

### <a name="behind-the-scenes"></a>Abläufe im Hintergrund

Beim Ausführen von `docker pull` verwendet der Docker-Client die gleiche Bibliothek wie in der die [Notar-CLI][docker-notary-cli], um die Zuordnung des per Pull abgerufenen Tags zu SHA-256 anzufordern. Nach der Überprüfung der Signaturen der Vertrauensdaten weist der Client die Docker-Engine an, einen Pull-Vorgang per Digest auszuführen. Während des Pull-Vorgangs verwendet die Engine die SHA-256-Prüfsumme als Inhaltsadresse zum Anzufordern und Überprüfen des Image-Manifests aus der Azure-Containerregistrierung.

> [!NOTE]
> Azure Container Registry unterstützt offiziell nicht die Notary CLI, ist aber mit der Notary Server-API kompatibel, die in Docker Desktop enthalten ist. Zurzeit wird die Notary-Version **0.6.0** empfohlen.

## <a name="key-management"></a>Schlüsselverwaltung

Wie in der `docker push`-Ausgabe angegeben ist der Stammschlüssel bei der Pushübertragung Ihres ersten vertrauenswürdigen Images der sensibelste Schlüssel. Achten Sie darauf, den Stammschlüssel zu sichern und an einem sicheren Ort zu speichern. Der Docker-Client speichert Signaturschlüssel standardmäßig im folgenden Verzeichnis:

```sh
~/.docker/trust/private
```

Sichern Sie Ihre Stamm- und Repositoryschlüssel, indem Sie sie in einem Archiv komprimieren und an einem sicheren Ort speichern. Beispiel in Bash:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Zusammen mit den lokal erstellten Stamm- und Repositoryschlüsseln werden mehrere weitere Schlüssel generiert und von Azure Container Registry gespeichert, wenn Sie ein vertrauenswürdiges Image per Push übertragen. Eine ausführliche Erläuterung der verschiedenen Schlüssel bei der Implementierung von Inhaltsvertrauen in Docker, einschließlich zusätzlicher Verwaltungsanleitungen, finden Sie unter [Manage keys for content trust][docker-manage-keys] in der Docker-Dokumentation.

### <a name="lost-root-key"></a>Verlorener Stammschlüssel

Wenn Sie den Zugriff auf Ihren Stammschlüssel verlieren, verlieren Sie den Zugriff auf die signierten Tags in allen Repositorys, deren Tags mit diesem Schlüssel signiert wurden. Azure Container Registry kann den Zugriff auf Image-Tags, die mit einem verlorenen Stammschlüssel signiert sind, nicht wiederherstellen. Zum Entfernen aller Vertrauensdaten (Signaturen) für Ihre Registrierung deaktivieren Sie das Inhaltsvertrauen für die Registrierung und aktivieren es anschließend wieder.

> [!WARNING]
> Durch Deaktivieren und erneutes Aktivieren von Inhaltsvertrauen in Ihrer Registrierung **werden alle Vertrauensdaten für alle signierten Tags in jedem Repository in Ihrer Registrierung gelöscht**. Diese Aktion kann nicht rückgängig gemacht werden – Azure Container Registry kann gelöschte Vertrauensdaten nicht wiederherstellen. Die Images selbst werden durch Deaktivieren des Inhaltsvertrauens nicht gelöscht.

Zum Deaktivieren des Inhaltsvertrauens für Ihre Registrierung navigieren Sie im Azure-Portal zur Registrierung. Wählen Sie unter **Richtlinien** die Optionen **Inhaltsvertrauen** > **Deaktiviert** > **Speichern** aus. Sie werden vor dem Verlust aller Signaturen in der Registrierung gewarnt. Wählen Sie **OK**, um alle Signaturen in Ihrer Registrierung endgültig zu löschen.

![Deaktivieren von Inhaltsvertrauen für eine Registrierung im Azure-Portal][content-trust-03-portal]

## <a name="next-steps"></a>Nächste Schritte

* Zusätzliche Informationen zu Inhaltsvertrauen finden Sie unter [Inhaltsvertrauen in Docker][docker-content-trust], einschließlich [docker trust](https://docs.docker.com/engine/reference/commandline/trust/)-Befehlen und [Vertrauensdelegierungen](https://docs.docker.com/engine/security/trust/trust_delegation/). In diesem Artikel wurden einige wichtige Punkte angesprochen, das Inhaltsvertrauen ist jedoch ein umfangreiches Thema und wird in der Dokumentation zu Docker ausführlicher behandelt.

* In der [Azure Pipelines](/azure/devops/pipelines/build/content-trust)-Dokumentation finden Sie ein Beispiel für die Verwendung von Inhaltsvertrauen beim Erstellen und Pushen eines Docker-Images.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-config-content-trust-update]: /cli/azure/acr/config/content-trust#az_acr_config_content_trust_update
