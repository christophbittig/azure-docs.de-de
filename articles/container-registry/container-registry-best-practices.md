---
title: Registrierung – bewährte Methoden
description: Erfahren Sie, wie Sie Azure Container Registry anhand dieser bewährten Methoden effektiv verwenden.
ms.topic: article
ms.date: 08/13/2021
ms.openlocfilehash: 1b713ac047b575c68cd8ed539187e3caac13a322
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128626967"
---
# <a name="best-practices-for-azure-container-registry"></a>Bewährte Methoden für Azure Container Registry

Durch Befolgen dieser bewährten Methoden sorgen Sie für eine optimale Leistung und kostengünstige Verwendung Ihrer privaten Registrierung in Azure zum Speichern und Bereitstellen von Containerimages und anderen Artefakten.

Hintergrundinformationen zu Registrierungskonzepten finden Sie unter [Informationen zu Registrierungen, Repositorys und Image](container-registry-concepts.md). Informationen zu Strategien zum Taggen und für die Versionsverwaltung von Images in Ihrer Registrierung finden Sie unter [Empfehlungen für das Taggen und die Versionsverwaltung von Containerimages](container-registry-image-tag-version.md). 

## <a name="network-close-deployment"></a>Netzwerknahe Bereitstellung

Erstellen Sie Ihre Containerregistrierung in derselben Azure-Region, in der Sie Container bereitstellen. Indem Ihre Registrierung in eine Region platziert wird, die sich in Netzwerknähe Ihrer Containerhosts befindet, können Sie sowohl Wartezeiten als auch Kosten senken.

Die netzwerknahe Bereitstellung ist einer der Hauptgründe für die Verwendung einer privaten Containerregistrierung. Docker-Images verfügen über ein effizientes [Ebenenkonstrukt](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), das inkrementelle Bereitstellungen ermöglicht. Allerdings müssen neue Knoten alle Ebenen mithilfe von Pull übertragen, die für ein bestimmtes Image erforderlich sind. Bei diesem anfänglichen `docker pull` können schnell mehrere Gigabyte zusammenkommen. Durch eine private Registrierung in der Nähe Ihrer Bereitstellung wird die Netzwerklatenz auf ein Mindestmaß verringert.
Darüber hinaus werden für alle öffentlichen Clouds, einschließlich Azure, Gebühren für ausgehenden Netzwerkdatenverkehr erhoben. Wenn Images aus einem Rechenzentrum in ein anderes übertragen werden, entstehen neben Wartezeiten auch Kosten für ausgehenden Netzwerkdatenverkehr.

## <a name="geo-replicate-multi-region-deployments"></a>Georeplikation von Bereitstellungen in mehreren Regionen

Verwenden Sie das Feature [Georeplikation](container-registry-geo-replication.md) von Azure Container Registry, wenn Sie Container in mehreren Regionen bereitstellen. Ganz gleich, ob Sie globale Kunden von lokalen Rechenzentren aus bedienen oder Ihr Entwicklungsteam sich an unterschiedlichen Standorten befindet – durch die Georeplikation Ihrer Registrierung können Sie die Registrierungsverwaltung vereinfachen und Wartezeiten auf ein Minimum reduzieren. Sie können auch regionale [Webhooks](container-registry-webhook.md) konfigurieren, um über Ereignisse in bestimmten Replikaten benachrichtigt zu werden, z. B. wenn Images per Push übertragen werden.

Georeplikation ist mit [Premium](container-registry-skus.md)-Registrierungen verfügbar. Sehen Sie sich die dreiteilige Tutorialreihe [Georeplikation in Azure Container Registry](container-registry-tutorial-prepare-registry.md) an, um sich über die Verwendung der Georeplikation zu informieren.

## <a name="maximize-pull-performance"></a>Maximieren der Leistung von Pullvorgängen

Auch wenn die Images in der Nähe der Bereitstellungen platziert werden, können sich die Eigenschaften der Images auf die Leistung des Pullvorgangs auswirken.

* **Imagegröße**: Verringern Sie die Größe der Images, indem Sie unnötige [Ebenen](container-registry-concepts.md#manifest) entfernen oder die Größe der Ebenen reduzieren. Eine Möglichkeit zum Verringern der Imagegröße ist die Verwendung [mehrstufiger Docker-Builds](https://docs.docker.com/develop/develop-images/multistage-build/), um nur die erforderlichen Laufzeitkomponenten einzuschließen. 

  Überprüfen Sie außerdem, ob das Image ein einfacheres Basisbetriebssystemimage enthalten kann. Wenn Sie eine Bereitstellungsumgebung wie Azure Container Instances verwenden, die bestimmte Basisimages zwischenspeichert, überprüfen Sie, ob Sie eine Imageebene durch eines der zwischengespeicherten Images ersetzen können. 
* **Anzahl der Ebenen**: Verwenden Sie eine ausgewogene Anzahl von Ebenen. Wenn die Anzahl zu gering ist, profitieren Sie nicht von der Wiederverwendung von Ebenen und ihrer Zwischenspeicherung auf dem Host. Wenn die Anzahl zu hoch ist, erfordert die Bereitstellungsumgebung mehr Zeit für Pullvorgänge und Dekomprimierung. Ein optimale Anzahl sind 5 bis 10 Ebenen.

Wählen Sie außerdem eine [Dienstebene](container-registry-skus.md) von Azure Container Registry aus, die Ihren Leistungsanforderungen entspricht. Der Premium-Tarif bietet die größte Bandbreite und die höchste Anzahl gleichzeitiger Lese- und Schreibvorgänge, wenn Sie über Bereitstellungen mit hohem Volumen verfügen.

## <a name="repository-namespaces"></a>Repositorynamespaces

Mithilfe von Repositorynamespaces können Sie die gemeinsame Nutzung einer einzigen Registrierung in mehreren Gruppen innerhalb Ihrer Organisation ermöglichen. Registrierungen können in mehreren Bereitstellungen und Teams gemeinsam verwendet werden. Azure Container Registry unterstützt geschachtelte Namespaces und ermöglicht damit die Isolation von Gruppen. Allerdings verwaltet die Registrierung alle Repositorys unabhängig voneinander und nicht als Hierarchie.

Sehen Sie sich beispielsweise die folgenden Containerimagetags an. Images, die unternehmensweit verwendet werden (z. B. `aspnetcore`), werden im Stammnamespace abgelegt, während Containerimages, die zu den Produk- und Marketinggruppen gehören, jeweils eigene Namespaces verwenden.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedizierte Ressourcengruppe

Da Containerregistrierungen Ressourcen sind, die über mehrere Containerhosts verwendet werden, sollte sich eine Registrierung in einer eigenen Ressourcengruppe befinden.

Auch wenn Sie mit einem bestimmten Hosttyp (z. B. [Azure Container Instances](../container-instances/container-instances-overview.md)) experimentieren, möchten Sie die Containerinstanz wahrscheinlich am Ende löschen. Möglicherweise empfiehlt es sich jedoch, die Sammlung von Images beizubehalten, die Sie mithilfe von Push in Azure Container Registry übertragen haben. Indem Sie Ihre Registrierung in einer eigenen Ressourcengruppe ablegen, minimieren Sie das Risiko, dass die Sammlung von Images in der Registrierung versehentlich gelöscht wird, wenn Sie die Ressourcengruppe der Containerinstanz löschen.

## <a name="authentication-and-authorization"></a>Authentifizierung und Autorisierung

Bei der Authentifizierung bei einer Azure-Containerregistrierung gibt es im Wesentlichen zwei Szenarien: die individuelle Authentifizierung und die Dienstauthentifizierung (oder „monitorlose Authentifizierung“). Die folgende Tabelle enthält eine kurze Übersicht über diese Szenarien sowie die jeweils empfohlene Authentifizierungsmethode.

| type | Beispielszenario | Empfohlene Methode |
|---|---|---|
| Einzelne Identität | Ein Entwickler, der Images mittels Pull auf seinen bzw. mittels Push von seinem Entwicklungscomputer überträgt. | [az acr login](/cli/azure/acr#az_acr_login) |
| Monitorlose/Dienstidentität | Build- und Bereitstellungspipelines, an denen der Benutzer nicht direkt beteiligt ist. | [Dienstprinzipal](container-registry-authentication.md#service-principal) |

Ausführliche Informationen zu diesen und weiteren Azure Container Registry-Authentifizierungsszenarien finden Sie unter [Authentifizieren bei einer Azure-Containerregistrierung](container-registry-authentication.md).

Azure Container Registry unterstützt Sicherheitsverfahren in Ihrer Organisation, um Aufgaben und Berechtigungen an unterschiedliche Identitäten zu verteilen. Weisen Sie mithilfe von [rollenbasierter Zugriffssteuerung](container-registry-roles.md) den unterschiedlichen Benutzern, Dienstprinzipalen oder anderen Identitäten, die unterschiedliche Registrierungsvorgänge ausführen, die entsprechenden Berechtigungen zu. Weisen Sie z. B. einem Dienstprinzipal, der in einer Buildpipeline verwendet wird, Pushberechtigungen zu, und weisen Sie einer anderen für die Bereitstellung verwendeten Identität Pullberechtigungen zu. Erstellen Sie [Token](container-registry-repository-scoped-permissions.md) für einen differenzierten, zeitlich begrenzten Zugriff auf bestimmte Repositorys.

## <a name="manage-registry-size"></a>Verwalten von Registrierungsgrößen      

Die Speichereinschränkungen jeder [Dienstebene der Containerregistrierung][container-registry-skus] sind jeweils für ein typisches Szenario konzipiert: **Basic** für die ersten Schritte, **Standard** für die meisten Produktionsanwendungen und **Premium** für Leistung mit Hyperskalierung und [Georeplikation][container-registry-geo-replication]. Während der gesamten Dauer Ihrer Registrierung sollten Sie die Größe durch regelmäßiges Löschen von ungenutztem Inhalt verwalten.

Verwenden Sie den Azure CLI-Befehl [az acr show-usage][az-acr-show-usage], um den aktuellen Verbrauch des Speichers und anderen Ressourcen in Ihrer Registrierung anzuzeigen:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

Beispielausgabe:

```
NAME                        LIMIT         CURRENT VALUE    UNIT
--------------------------  ------------  ---------------  ------
Size                        536870912000  215629144        Bytes
Webhooks                    500           1                Count
Geo-replications            -1            3                Count
IPRules                     100           1                Count
VNetRules                   100           0                Count
PrivateEndpointConnections  10            0                Count
```

Sie finden den aktuellen Speicherverbrauch auch in der **Übersicht** Ihrer Registrierung im Azure-Portal:

![Informationen zur Registrierungsnutzung im Azure-Portal][registry-overview-quotas]

> [!NOTE]
> In einer [georeplizierten](container-registry-geo-replication.md) Registrierung wird der Speicherverbrauch für die Startregion angezeigt. Multiplizieren Sie mit der Anzahl der Replikationen, um den gesamten verbrauchten Registrierungsspeicher zu erhalten.

### <a name="delete-image-data"></a>Löschen von Imagedaten

Azure Container Registry unterstützt mehrere Methoden zum Löschen von Imagedaten aus Ihrer Containerregistrierung. Sie können Images anhand von Tags oder Manifesthashes löschen oder ein gesamtes Repository löschen.

Weitere Informationen zum Löschen von Imagedaten aus Ihrer Registrierung, einschließlich nicht mit Tags gekennzeichneter Images (manchmal als „verbleibend“ oder „verwaist“ bezeichnet) finden Sie unter [Löschen von Containerimages in Azure Container Registry](container-registry-delete.md). Eine [Aufbewahrungsrichtlinie](container-registry-retention-policy.md) kann auch für nicht markierte Manifeste festgelegt werden.

## <a name="next-steps"></a>Nächste Schritte

Azure Container Registry ist mit mehreren Dienstebenen (auch als SKUs bezeichnet) verfügbar, die verschiedene Funktionen bereitstellen. Ausführliche Informationen zu den verfügbaren Tarifen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

Empfehlungen zur Verbesserung des Sicherheitsstatus ihrer Containerregistrierungen finden Sie unter [Azure-Sicherheitsbaseline für Azure Container Registry](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md