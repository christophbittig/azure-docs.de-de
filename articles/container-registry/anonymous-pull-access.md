---
title: Aktivieren von anonymen Pull-Zugriff
description: Aktivieren Sie optional den anonymen Pull-Zugriff, um die Inhalte Ihrer Azure Container-Registrierung öffentlich zugänglich zu machen
ms.topic: how-to
ms.date: 09/17/2021
ms.custom: ''
ms.openlocfilehash: 816e99cd5ba2ba83958a9ec7b9b0ad679e4a6550
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129547353"
---
# <a name="make-your-container-registry-content-publicly-available"></a>Machen Sie den Inhalt Ihrer Container-Registrierung öffentlich zugänglich

Das Einrichten einer Azure Container-Registrierung für anonymen (nicht authentifizierten) Pull-Zugriff ist eine optionale Funktion, die es jedem Benutzer mit Internetzugang ermöglicht, beliebige Inhalte aus der Registrierung zu pullen.

Der anonyme Pull-Zugriff ist eine Previewfunktion, die in der Standard- und Premium-[Dienstebene](container-registry-skus.md) verfügbar ist. Um den anonymen Pull-Zugriff zu konfigurieren, aktualisieren Sie eine Registrierung unter Verwendung der Azure CLI (Version 2.21.0 oder höher). Informationen zum Ausführen einer Installation oder eines Upgrades finden Sie unter [Installieren der Azure CLI](/cli/azure-install-cli).

## <a name="about-anonymous-pull-access"></a>Der anonyme Pull-Zugriff

Standardmäßig ist der Zugriff auf Pull- oder Push-Inhalte aus einer Azure Container-Registrierung nur für [authentifizierten](container-registry-authentication.md) Benutzer möglich. Wenn Sie den anonymen (nicht authentifizierten) Pull-Zugriff aktivieren, sind alle Inhalte der Registrierung öffentlich für Lese- (Pull-) Aktionen verfügbar. Der anonyme Pull-Zugriff kann in Szenarien verwendet werden, die keine Benutzerauthentifizierung erfordern, wie z. B. die Verteilung öffentlicher Containerimages.

- Aktivieren Sie den anonymen Pull-Zugriff, indem Sie die Eigenschaften einer bestehenden Registrierung aktualisieren.
- Nachdem Sie den anonymen Pull-Zugriff aktiviert haben, können Sie diesen Zugriff jederzeit wieder deaktivieren.
- Für nicht authentifizierte Clients sind nur Datenebenenvorgänge verfügbar.
- Die Registrierung kann eine hohe Quote an nicht authentifizierten Anforderungen drosseln.
- Wenn Sie sich zuvor bei der Registrierung authentifiziert haben, stellen Sie sicher, dass Sie die Anmeldeinformationen löschen, bevor Sie einen anonymen Pullvorgang versuchen.

> [!WARNING]
> Der anonyme Pullzugriff gilt zurzeit für alle Repositorys in der Registrierung. Wenn Sie den Repository-Zugriff mit [Repositoryumfang-Tokens](container-registry-repository-scoped-permissions.md) verwalten, können alle Benutzer aus diesen Repositories in einer Registrierung, die für anonymes Pull aktiviert ist, pullen. Es wird empfohlen, Token zu löschen, wenn der anonyme Pullzugriff aktiviert ist.

## <a name="configure-anonymous-pull-access"></a>Konfigurieren Sie den anonymen Pull-Zugriff 

### <a name="enable-anonymous-pull-access"></a>Aktivieren von anonymen Pull-Zugriff
Aktualisieren Sie eine Registrierung unter Verwendung des Befehls [az acr update](/cli/azure/acr#az_acr_update) und übergeben Sie den Parameter `--anonymous-pull-enabled`. Standardmäßig ist der anonyme Pull-Zugriff in der Registrierung deaktiviert.
          
```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

> [!IMPORTANT]
> Wenn Sie sich zuvor mit Docker-Anmeldedaten bei der Registrierung authentifiziert haben, führen Sie `docker logout` aus, um sicherzustellen, dass Sie die vorhandenen Anmeldedaten löschen, bevor Sie anonyme Pull-Vorgänge versuchen. Andernfalls wird möglicherweise eine Fehlermeldung ähnlich wie "Pull-Zugriff verweigert" angezeigt.

### <a name="disable-anonymous-pull-access"></a>Deaktivieren von dem anonymen Pull-Zugriff
Deaktivieren Sie den anonymen Pull-Zugriff, indem Sie `--anonymous-pull-enabled` auf `false` setzen.

```azurecli
az acr update --name myregistry --anonymous-pull-enabled false
```

## <a name="next-steps"></a>Nächste Schritte

* Lernen Sie über den Gebrauch von [Repositoryumfang-Tokens](container-registry-repository-scoped-permissions.md).
* Lernen Sie die Optionen zum [Authentifizieren](container-registry-authentication.md) für eine Azure Container-Registrierung kennen.
