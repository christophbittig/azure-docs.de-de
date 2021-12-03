---
title: Strategien für Blau-Grün-Bereitstellungen in Azure Spring Cloud
description: In diesem Thema werden zwei Ansätze für Blau-Grün-Bereitstellungen in Azure Spring Cloud erläutert.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: 8b019c5cc8e7e7a8ed132cc6adbf0d4c7d0495a4
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132486267"
---
# <a name="blue-green-deployment-strategies-in-azure-spring-cloud"></a>Strategien für Blau-Grün-Bereitstellungen in Azure Spring Cloud

In diesem Artikel wird die Unterstützung für Blau-Grün-Bereitstellungen in Azure Spring Cloud beschrieben.

Azure Spring Cloud (Standard-Tarif und höher) erlaubt zwei Bereitstellungen für jede App, von denen nur eine Produktionsdatenverkehr empfängt. Dieses Muster wird häufig als Blau-Grün-Bereitstellung bezeichnet. Die Unterstützung von Azure Spring Cloud für Blau-Grün-Bereitstellungen, zusammen mit einer [CD](/devops/deliver/what-is-continuous-delivery)-Pipeline (Continuous Delivery) und rigorosen automatisierten Tests, ermöglicht das agile Bereitstellen von Anwendungen mit hohem Vertrauen.

## <a name="alternating-deployments"></a>Abwechseln der Bereitstellungen

Die einfachste Möglichkeit, eine Blau-Grün-Bereitstellung mit Azure Spring Cloud zu implementieren, besteht darin, zwei feste Bereitstellungen zu erstellen und immer die Bereitstellung bereitzustellen, die keinen Produktionsdatenverkehr empfängt. Mit dem [Azure Spring Cloud-Task für Azure Pipelines](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud) können Sie die Bereitstellung auf diese Weise ausführen, indem Sie nur das `UseStagingDeployment`-Flag auf `true` festlegen.

So funktioniert der Ansatz für das Abwechseln der Bereitstellungen in der Praxis:

Angenommen, Ihre Anwendung verfügt über zwei Bereitstellungen: `deployment1` und `deployment2`. Derzeit `deployment1` ist als Produktionsbereitstellung festgelegt und führt Version `v3` der Anwendung aus.

Dadurch wird `deployment2` zur Stagingbereitstellung. Wenn also die CD-Pipeline (Continuous Delivery) bereit für die Ausführung ist, stellt sie die nächste Version der App (Version `v4`) in der Stagingbereitstellung `deployment2` bereit.

![Zwei Bereitstellungen: deployment1 empfängt Produktionsdatenverkehr](media/spring-cloud-blue-green-patterns/alternating-deployments-1.png)

Nachdem `v4` in `deployment2` gestartet wurde, können Sie automatisierte und manuelle Tests über einen privaten Testendpunkt ausführen, um sicherzustellen, dass `v4` alle Erwartungen erfüllt.

![V4 ist jetzt in deployment2 bereitgestellt und wird getestet.](media/spring-cloud-blue-green-patterns/alternating-deployments-2.png)

Wenn Sie `v4` vertrauen, können Sie `deployment2` als Produktionsbereitstellung festlegen, sodass diese den gesamten Produktionsdatenverkehr empfängt. `v3` wird weiterhin in `deployment1` ausgeführt, falls Sie ein kritisches Problem feststellen, für das ein Rollback erforderlich ist.

![V4 in deployment2 empfängt jetzt Produktionsdatenverkehr](media/spring-cloud-blue-green-patterns/alternating-deployments-3.png)

Nun ist `deployment1` die Stagingbereitstellung. Daher wird die nächste Ausführung der Bereitstellungspipeline in `deployment1` bereitgestellt.

![V5 in deployment1 bereitgestellt](media/spring-cloud-blue-green-patterns/alternating-deployments-4.png)

Sie können jetzt `V5` für den privaten Testendpunkt von `deployment1` testen.

![V5 wird für deployment1 getestet](media/spring-cloud-blue-green-patterns/alternating-deployments-5.png)

Nachdem `v5` alle Ihre Erwartungen erfüllt, legen Sie `deployment1` erneut als Produktionsbereitstellung fest, damit `v5` den gesamten Produktionsdatenverkehr empfängt.

![V5 empfängt Datenverkehr in deployment1](media/spring-cloud-blue-green-patterns/alternating-deployments-6.png)

### <a name="tradeoffs-of-the-alternating-deployments-approach"></a>Nachteile des Ansatzes abwechselnder Bereitstellungen

Der Ansatz für abwechselnde Bereitstellungen ist einfach und schnell, da die Erstellung neuer Bereitstellungen nicht erforderlich ist. Es gibt jedoch mehrere Nachteile, wie in den folgenden Abschnitten beschrieben.

#### <a name="persistent-staging-deployment"></a>Persistente Stagingbereitstellung

Die Stagingbereitstellung wird immer ausgeführt und verbraucht daher Ressourcen der Azure Spring Cloud-Instanz. Dies verdoppelt effektiv die Ressourcenanforderungen jeder Anwendung unter Azure Spring Cloud.

#### <a name="the-approval-race-condition"></a>Die Genehmigungsracebedingung

Angenommen, in der Anwendung oben erfordert die Releasepipeline eine manuelle Genehmigung, bevor jede neue Version der Anwendung Produktionsdatenverkehr empfangen kann. Dadurch entsteht das Risiko, dass, während eine Version (`v6`) auf die manuelle Genehmigung für die Stagingbereitstellung wartet, die Bereitstellungspipeline erneut ausgeführt wird und sie mit einer neueren Version (`v7`) überschreibt. Wenn dann die Genehmigung für `v6` erteilt wird, legt die Pipeline, die `v6` bereitgestellt hat, die Stagingbereitstellung als Produktionsumgebung fest. Aber jetzt wird die nicht genehmigte `v7`, nicht die genehmigte `v6`, für diese Bereitstellung bereitgestellt und empfängt Datenverkehr.

![Die Genehmigungsracebedingung](media/spring-cloud-blue-green-patterns/alternating-deployments-race-condition.png)

Möglicherweise können Sie die Racebedingung verhindern, indem Sie sicherstellen, dass der Bereitstellungsfluss für eine Version erst gestartet werden kann, wenn der Bereitstellungsfluss für alle vorherigen Versionen abgeschlossen oder abgebrochen wurde. Eine weitere Möglichkeit, die Genehmigungsracebedingung zu verhindern, besteht darin, den unten beschriebenen Ansatz „Benannte Bereitstellungen“ zu verwenden.

## <a name="named-deployments"></a>Benannte Bereitstellungen

Beim Ansatz mit benannten Bereitstellungen wird für jede neue Version der bereitgestellten Anwendung eine neue Bereitstellung erstellt. Nachdem die Anwendung auf ihre maßgeschneiderte Bereitstellung getestet wurde, wird diese Bereitstellung als Produktionsbereitstellung festgelegt. Die Bereitstellung, die die vorherige Version enthält, kann so lange beibehalten werden, bis sichergestellt ist, dass kein Rollback erforderlich ist.

In der folgenden Abbildung wird Version `v5` in der Bereitstellung `deployment-v5` ausgeführt. Der Bereitstellungsname enthält jetzt die Version, weil die Bereitstellung speziell für diese Version erstellt wurde. Es gibt am Anfang keine andere Bereitstellung. Um nun Version `v6` bereitzustellen, erstellt die Bereitstellungspipeline eine neue Bereitstellung `deployment-v6` und stellt dort App-Version `v6` bereit.

![Bereitstellen einer neuen Version in einer benannten Bereitstellung](media/spring-cloud-blue-green-patterns/named-deployment-1.png)

Es besteht kein Risiko, dass eine andere Version parallel bereitgestellt wird. Erstens erlaubt Azure Spring Cloud nicht die Erstellung einer dritten Bereitstellung, wenn bereits zwei Bereitstellungen vorhanden sind. Zweitens, selbst wenn es möglich wäre, mehr als zwei Bereitstellungen zu verwenden, wird jede Bereitstellung durch die Version der Anwendung identifiziert, die sie enthält. Daher würde die Pipeline, die die Bereitstellung von `v6` orchestriert, nur versuchen, `deployment-v6` als Produktionsbereitstellung festzulegen.

![Neue Version empfängt Produktionsdatenverkehr in benannter Bereitstellung](media/spring-cloud-blue-green-patterns/named-deployment-2.png)

Nachdem die für die neue Version erstellte Bereitstellung Produktionsdatenverkehr empfängt, müssen Sie die Bereitstellung mit der vorherigen Version entfernen, um Platz für zukünftige Bereitstellungen zu schaffen. Möglicherweise möchten Sie die Entfernung um einige Minuten oder Stunden aufschieben, damit Sie ein Rollback auf die vorherige Version ausführen können, wenn Sie ein kritisches Problem in der neuen Version feststellen.

![Löschen der vorherigen Bereitstellung nach einem Fallbackzeitraum](media/spring-cloud-blue-green-patterns/named-deployment-3.png)

### <a name="tradeoffs-of-the-named-deployments-approach"></a>Nachteile beim Ansatz benannter Bereitstellungen

Der Ansatz benannter Bereitstellungen hat die folgenden Vorteile:

* Er verhindert die Racebedingung für die Genehmigung.
* Er reduziert den Ressourcenverbrauch, indem die Stagingbereitstellung gelöscht wird, wenn sie nicht verwendet wird.

Es gibt jedoch auch Nachteile, wie im folgenden Abschnitt beschrieben.

#### <a name="deployment-pipeline-failures"></a>Fehler der Bereitstellungspipeline

Zwischen dem Start einer Bereitstellung und dem Löschen der Stagingbereitstellung schlagen alle weiteren Versuche, die Bereitstellungspipeline auszuführen, fehl. Die Pipeline versucht, eine neue Bereitstellung zu erstellen. Dies führt zu einem Fehler, da pro Anwendung in Azure Spring Cloud nur zwei Bereitstellungen zulässig sind.

Daher muss die Bereitstellungsorchestrierung entweder über die Möglichkeit verfügen, einen fehlgeschlagenen Bereitstellungsprozess zu einem späteren Zeitpunkt zu wiederholen, oder sie muss sicherstellen, dass die Bereitstellungsabläufe für jede Version in der Warteschlange bleiben, bis der Ablauf für alle vorherigen Versionen abgeschlossen ist.

## <a name="next-steps"></a>Nächste Schritte

* [Automatisieren von Anwendungsbereitstellungen für Azure Spring Cloud](./how-to-cicd.md)
