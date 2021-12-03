---
title: Unterstützte Kubernetes-Versionen in Azure Kubernetes Service
description: Grundlegendes zur Richtlinie zur Unterstützung der Kubernetes-Version und zum Lebenszyklus von Clustern in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/09/2021
author: palma21
ms.author: jpalma
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4a2d90c797585ad025a9540045fe65dee385000
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493598"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Unterstützte Kubernetes-Versionen in Azure Kubernetes Service (AKS)

Die Kubernetes-Community veröffentlicht etwa alle drei Monate Nebenversionen. Kürzlich hat die Kubernetes-Community das [Unterstützungsfenster für die einzelnen Versionen von 9 Monaten auf 12 Monate](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/) erhöht, beginnend mit Version 1.19.

Nebenversionsreleases enthalten neue Features und Verbesserungen. Patchreleases kommen häufiger vor (manchmal wöchentlich) und sind für wichtige Fehlerbehebungen in einer Nebenversion gedacht. Patchreleases enthalten Fixes für Sicherheitsrisiken oder schwer wiegende Fehler.

## <a name="kubernetes-versions"></a>Kubernetes-Versionen

Kubernetes verwendet als Standardversionierungsschema für jede Version die [semantische Versionierung](https://semver.org/):

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Für jede Zahl in der Version gilt, dass allgemeine Kompatibilität mit der vorherigen Version besteht:

* **Hauptversionen** ändern sich, wenn nicht kompatible API-Updates vorgenommen werden oder möglicherweise keine Abwärtskompatibilität mehr gewährleistet wird.
* **Nebenversionen** ändern sich, wenn Updates an der Funktionalität vorgenommen werden, die zu den anderen Nebenreleases abwärtskompatibel sind.
* **Patchversionen** werden geändert, wenn abwärtskompatible Fehlerbehebungen vorgenommen werden.

Versuchen Sie, das neueste Patchrelease der ausgeführten Nebenversion zu verwenden. Ihr Produktionscluster befindet sich beispielsweise auf **`1.17.7`** . **`1.17.8`** ist die neueste für die *1.17*-Serie verfügbare Patchversion. Sie sollten so bald wie möglich ein Upgrade auf **`1.17.8`** durchführen, um sicherzustellen, dass Ihr Cluster vollständig gepatcht ist und unterstützt wird.

## <a name="kubernetes-version-support-policy"></a>Richtlinie zur Unterstützung der Kubernetes-Version

In AKS ist eine allgemein verfügbare Version als eine Version definiert, die in allen SLO- oder SLA-Messungen aktiviert und in allen Regionen verfügbar ist. AKS unterstützt drei allgemein verfügbare Nebenversionen von Kubernetes:

* Die neueste allgemein verfügbare Nebenversion, die in AKS veröffentlicht wurde (diese wird als „N“ bezeichnet).
* Die beiden vorherigen Nebenversionen.
    * Jede unterstützte Nebenversion unterstützt auch maximal zwei stabile Patches.

AKS unterstützt möglicherweise auch Vorschauversionen, die explizit als solche gekennzeichnet sind und den [Nutzungsbedingungen für Vorschauversionen][preview-terms] unterliegen.

> [!NOTE]
> AKS verwendet sichere Bereitstellungsmethoden einschließlich einer graduellen Bereitstellung in Regionen. Das bedeutet, dass es bis zu zehn Werktage dauern kann, bis ein neues Release oder eine neue Version in allen Regionen verfügbar ist.

Das unterstützte Fenster für Kubernetes-Versionen in AKS wird als „N-2“ bezeichnet: („N“ für das neueste Release – 2 für die Anzahl der Nebenversionen).

Wenn AKS beispielsweise die Version *1.17.a* einführt, werden die folgenden Versionen unterstützt:

Neue Nebenversion    |    Liste mit unterstützten Versionen
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

Dabei steht der Buchstabe für die jeweiligen Patchversionen.

Wenn eine neue Nebenversion eingeführt wird, werden die älteste Nebenversion und die unterstützten Patchreleases nicht mehr unterstützt und entfernt. Angenommen, die aktuelle Liste mit den unterstützten Versionen sieht wie folgt aus:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS veröffentlicht 1.18.\* und entfernt alle 1.15.\*-Versionen in 30 Tagen aus dem Support.

> [!NOTE]
> Wenn Kunden eine nicht unterstützte Kubernetes-Version verwenden, werden sie aufgefordert, ein Upgrade vorzunehmen, sobald sie Support für den Cluster anfordern. Die [AKS-Supportrichtlinien](./support-policies.md) gelten nicht für Cluster, auf denen nicht unterstützte Kubernetes-Releases ausgeführt werden.

Darüber hinaus unterstützt AKS maximal zwei **Patchreleases** für eine bestimmte Nebenversion. Angenommen, folgende Versionen werden unterstützt:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Wenn AKS `1.17.9` und `1.16.11` veröffentlicht, werden die ältesten Patchversionen als veraltet markiert und entfernt. Die Liste mit den unterstützten Versionen ändert sich dann wie folgt:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Unterstützte `kubectl`-Versionen

Sie können eine relativ zu Ihrer *kube-apiserver*-Version niedrigere oder höhere Nebenversion von `kubectl` verwenden, die mit der [Kubernetes-Unterstützungsrichtlinie für kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl) konsistent ist.

Wenn Ihr *kube-apiserver* beispielsweise die Version *1.17* hat, können Sie die Versionen *1.16* bis *1.18* von `kubectl` mit diesem *kube-apiserver* verwenden.

Um `kubectl` zu installieren oder auf die neueste Version zu aktualisieren, führen Sie Folgendes aus:

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az aks install-cli
```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```powershell
Install-AzAksKubectl -Version latest
```
---

## <a name="release-and-deprecation-process"></a>Veröffentlichen und Markieren als veraltet

Sie können neue Releases und veraltete Versionen im [Releasekalender für AKS Kubernetes](#aks-kubernetes-release-calendar) einsehen.

Bei neuen **Nebenversionen** von Kubernetes:
  * AKS veröffentlicht mindestens 30 Tage vor dem Entfernen einer Version in den [AKS-Versionshinweisen](https://aka.ms/aks/releasenotes) eine Vorankündigung mit dem geplanten Datum der neuen Version und der Entfernung der entsprechenden alten Version.
  * AKS warnt Benutzer mit [Azure Advisor](../advisor/advisor-overview.md), wenn eine neue Version aufgrund veralteter APIs Probleme in ihrem Cluster verursacht. Azure Advisor wird auch verwendet, um den Benutzer zu warnen, wenn er derzeit keinen Supportanspruch mehr hat.
  * AKS veröffentlicht eine [Benachrichtigung zur Dienstintegrität](../service-health/service-health-overview.md), die für alle Benutzer mit Zugriff auf AKS und das Portal verfügbar ist. Außerdem sendet AKS eine E-Mail an die Abonnementadministratoren mit den geplanten Daten für die Entfernung von Versionen.

    > [!NOTE]
    > Unter [Zuweisen eines Abonnementadministrators](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator) erfahren Sie, wie Sie herausfinden, wer Ihr Abonnementadministrator ist, oder wie Sie ihn ändern können.

  * Benutzer haben nach der Entfernung einer Version **30** Tage lang Zeit, ein Upgrade auf eine unterstützte Nebenversion durchzuführen, um sicherzustellen, dass sie weiterhin Support erhalten.

Bei neuen **Patchversionen** von Kubernetes:
  * Da Patchversionen dringend sind, können sie in den Dienst eingefügt werden, sobald sie verfügbar sind.
  * Im der Regel macht AKS die Veröffentlichung neuer Patchversionen nicht allgemein bekannt. AKS überwacht und überprüft allerdings verfügbare CVE-Patches, um diese möglichst schnell in AKS zu unterstützen. Wenn ein kritischer Patch gefunden wird oder eine Benutzeraktion erforderlich ist, benachrichtigt AKS die Benutzer, damit diese ein Upgrade auf den neu verfügbaren Patch ausführen.
  * Benutzer haben ab der Entfernung eines Patchreleases von AKS **30 Tage** lang Zeit, ein Upgrade auf einen unterstützten Patch auszuführen, um weiterhin Support zu erhalten.

### <a name="supported-versions-policy-exceptions"></a>Richtlinienausnahmen für unterstützte Versionen

AKS behält sich das Recht vor, ohne Vorankündigung neue Versionen hinzuzufügen/bestehende zu entfernen, wenn in diesen kritische, die Produktion einschränkende Fehler oder Sicherheitsprobleme gefunden werden.

Bestimmte Patchreleases können übersprungen oder Rollouts je nach Schweregrad des Fehlers oder Sicherheitsproblems beschleunigt werden.

## <a name="azure-portal-and-cli-versions"></a>Versionen für Azure-Portal and CLI

Wenn Sie einen AKS-Cluster im Portal, mit der Azure CLI oder mit Azure PowerShell bereitstellen, wird der Cluster standardmäßig auf die Nebenversion N-1 und den neuesten Patch festgelegt. Wenn AKS beispielsweise *1.17.a*, *1.17.b*, *1.16.c*, *1.16.d*, *1.15.e* und *1.15.f* unterstützt, ist die standardmäßig ausgewählte Version *1.16.c*.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um herauszufinden, welche Versionen für Ihr Abonnement und Ihre Region derzeit verfügbar sind, verwenden Sie den Befehl [az aks get-versions][az-aks-get-versions]. Im folgenden Beispiel sind die verfügbaren Kubernetes-Versionen für die Region *EastUS* aufgelistet:

```azurecli-interactive
az aks get-versions --location eastus --output table
```


### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Um herauszufinden, welche Versionen für Ihr Abonnement und Ihre Region derzeit verfügbar sind, verwenden Sie das Cmdlet [Get-AzAksVersion][get-azaksversion]. Im folgenden Beispiel sind die verfügbaren Kubernetes-Versionen für die Region *EastUS* aufgelistet:

```azurepowershell-interactive
Get-AzAksVersion -Location eastus
```

---

## <a name="aks-kubernetes-release-calendar"></a>Releasekalender für AKS Kubernetes

Den Verlauf der letzten Versionen finden Sie unter [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  Kubernetes-Version | Upstreamrelease  | AKS – Vorschau  | AKS – allgemeine Verfügbarkeit (GA)  | Ende der Lebensdauer |
|--------------|-------------------|--------------|---------|-------------|
| 1.19*  | 04. August 2020  | Sep 2020   | November 2020  | 1.22 GA |
| 1.20  | Dec-08-20  | Jan 2021   | März 2021  | 1.23 GA |
| 1.21  | 08. April 2021 | Mai 2021   | Juli 2021  | 1.24: Allgemeine Verfügbarkeit |
| 1.22  | 04. August 21 | September 2021   | November 2021  | 1.25 Allgemeine Verfügbarkeit |
| 1.23  | Dezember 2021 | Januar 2022   | Februar 2022  | 1.26 Allgemeine Verfügbarkeit |

> [!NOTE]
> 1.19 ist veraltet und wird Ende Januar 2022 aus AKS entfernt.

## <a name="faq"></a>Häufig gestellte Fragen

**Wie benachrichtigt mich Microsoft, wenn neue Kubernetes-Versionen vorliegen?**

Das AKS-Team veröffentlicht Vorankündigungen mit geplanten Daten der neuen Kubernetes-Versionen in unserer Dokumentation, auf [GitHub](https://github.com/Azure/AKS/releases) sowie in E-Mails an Abonnementadministratoren, die Cluster besitzen, die aus dem Support herausfallen.  Zusätzlich zu Ankündigungen benachrichtigt AKS Kunden auch im Azure-Portal mit [Azure Advisor](../advisor/advisor-overview.md), um Benutzer zu warnen, wenn sie aus dem Support herausfallen, und sie vor veralteten APIs zu warnen, die sich auf ihre Anwendung oder ihren Entwicklungsprozess auswirken.

**Wie oft muss ich damit rechnen, ein Upgrade auf Kubernetes-Versionen durchzuführen, um weiterhin Unterstützung zu erhalten?**

Ab Kubernetes 1.19 hat die [Open-Source-Community die Unterstützung auf 1 Jahr](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/) erweitert. AKS führt einen Commit aus, um Patches und Unterstützung zu ermöglichen, die den Upstreamzusagen entsprechen. Für AKS-Cluster ab Version 1.19 können Sie mindestens einmal pro Jahr ein Upgrade durchführen, um bei einer unterstützten Version zu bleiben.

**Was geschieht, wenn ein Benutzer ein Upgrade eines Kubernetes-Clusters mit einer Nebenversion durchführt, die nicht unterstützt wird?**

Wenn Sie die Version *N-3* oder älter verwenden, wird diese Version nicht mehr unterstützt, und Sie werden zu einem Upgrade aufgefordert. Wenn das Upgrade von Version N-3 auf N-2 erfolgreich ist, greifen die Supportrichtlinien wieder, und die Version wird wieder unterstützt. Beispiel:

- Wenn die älteste unterstützte AKS-Version *1.15.a* ist und Sie *1.14.b* oder eine ältere Version verwenden, wird diese Version nicht mehr unterstützt.
- Wenn Ihr Upgrade von *1.14.b* auf *1.15.a* oder höher erfolgreich ist, greifen die Supportrichtlinien wieder, und die Version wird wieder unterstützt.

Downgrades werden nicht unterstützt.

**Was bedeutet „nicht unterstützt“?**

„Nicht unterstützt“ bedeutet:
* Die Version, die Sie ausführen, befindet sich nicht auf der Liste der unterstützten Versionen.
* Wenn Sie Support anfordern, werden Sie aufgefordert, für den Cluster ein Upgrade auf eine unterstützte Version vorzunehmen, sofern Sie sich nicht mehr innerhalb des 30-Tage-Zeitraums befinden, der beginnt, nachdem eine Version als veraltet gekennzeichnet wurde.

Des Weiteren stellt AKS für Cluster mit Versionen, die nicht in der Liste der unterstützten Versionen aufgeführt sind, weder Laufzeitgarantien noch sonstige Garantien bereit.

**Was geschieht, wenn ein Benutzer einen Kubernetes-Clusters mit einer Nebenversion skaliert, die nicht unterstützt wird?**

Bei Nebenversionen, die von AKS nicht unterstützt werden, sollte das Ab- oder Aufskalieren weiterhin einwandfrei funktionieren. Da es keine Garantien für die Dienstqualität gibt, sollten Sie ein Upgrade durchführen, damit für Ihren Cluster wieder Support gewährt wird.

**Kann ein Benutzer eine Kubernetes-Version für immer verwenden?**

Wenn ein Cluster für mehr als drei (3) Nebenversionen keinen Supportanspruch mehr hat und festgestellt wurde, dass für ihn Sicherheitsrisiken bestehen, setzt Azure sich proaktiv bezüglich eines Upgrades Ihres Clusters mit Ihnen in Verbindung. Wenn Sie keine weiteren Maßnahmen ergreifen, behält Azure sich das Recht vor, automatisch ein Upgrade Ihres Clusters in Ihrem Namen auszuführen.

**Welche Version wird von der Steuerungsebene unterstützt, wenn der Knotenpool keine der unterstützten AKS-Versionen verwendet?**

Die Steuerungsebene muss sich innerhalb eines Fensters von Versionen aller Knotenpools befinden. Ausführliche Informationen zum Aktualisieren der Steuerungsebene oder der Knotenpools finden Sie in der Dokumentation zum [Aktualisieren von Knotenpools](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Kann ich während eines Clusterupgrades mehrere AKS-Versionen überspringen?**

Beim Upgrade eines unterstützten AKS-Clusters können Nebenversionen von Kubernetes nicht übersprungen werden. Die [Richtlinie zur Versionsabweichung](https://kubernetes.io/releases/version-skew-policy/) der Kubernetes-Steuerungsebenen unterstützt das Überspringen von Nebenversionen nicht. Beispielsweise Upgrades zwischen:

  * *1.12.x* -> *1.13.x*: zulässig.
  * *1.13.x* -> *1.14.x*: zulässig.
  * *1.12.x* -> *1.14.x*: nicht zulässig.

So führen Sie ein Upgrade von *1.12.x* -> *1.14.x* aus:
1. Führen Sie ein Upgrade von *1.12.x* -> *1.13.x* aus.
1. Führen Sie ein Upgrade von *1.13.x* -> *1.14.x* aus.

Das Überspringen mehrerer Versionen ist nur möglich, wenn ein Upgrade von einer nicht unterstützten Version auf eine mindestens unterstützte Version erfolgt. Beispielsweise können Sie ein Upgrade von einer nicht unterstützten Version *1.10.x* auf eine unterstützte Version *1.15.x* durchführen, wenn *1.15* die mindestens unterstützte Nebenversion ist.

**Kann ich während des 30-tägigen Supportzeitraums einen neuen 1.xx.x-Cluster erstellen?**

Nein. Nachdem eine Version als veraltet markiert/entfernt wurde, können Sie keinen Cluster mit dieser Version erstellen. Mit Inkrafttreten der Änderung sehen Sie, dass die alte Version aus ihrer Versionsliste entfernt wurde. Dieser Vorgang kann bis zu zwei Wochen nach der Ankündigung dauern, schrittweise nach Region.

**Ich bin auf einer neu veralteten Version, kann ich dennoch neue Knoten-Pools hinzufügen? Oder muss ich ein Upgrade durchführen?**

Nein. Sie sind nicht berechtigt, dem Cluster Knoten-Pools der veralteten Version hinzuzufügen. Sie können Knoten-Pools einer neuen Version hinzufügen. Dies erfordert jedoch möglicherweise zuerst die Aktualisierung der Steuerungsebene.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Upgrade Ihres Clusters finden Sie unter [Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az_aks_get_versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[get-azaksversion]: /powershell/module/az.aks/get-azaksversion
