---
title: Problembehandlung bei FabricInternalServerError oder ServiceAllocationFailure beim Bereitstellen eines Clouddiensts (klassisch) in Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie die Ausnahme FabricInternalServerError oder ServiceAllocationFailure beheben, wenn Sie einen Clouddienst (klassisch) in Azure bereitstellen.
services: cloud-services
author: hirenshah1
ms.author: hirshah
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 219adb2d8d1f0fc6f08a1a4aede465619585df6e
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822967"
---
# <a name="troubleshoot-fabricinternalservererror-or-serviceallocationfailure-when-deploying-a-cloud-service-classic-to-azure"></a>Problembehandlung bei FabricInternalServerError oder ServiceAllocationFailure beim Bereitstellen eines Clouddiensts (klassisch) in Azure

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

In diesem Artikel behandeln Sie Zuordnungsfehler, bei denen eine Zuordnung durch den Fabric Controller nicht möglich ist, wenn Sie einen Azure-Clouddienst (klassisch) bereitstellen.

Wenn Sie Instanzen für einen Clouddienst bereitstellen oder neue Instanzen von Web- oder Workerrollen hinzufügen, weist Microsoft Azure Compute-Ressourcen zu.

Unter Umständen erhalten Sie bei diesen Vorgängen auch dann gelegentlich Fehlermeldungen, wenn Sie die Grenzwerte des Azure-Abonnements noch nicht erreicht haben.

> [!TIP]
> Diese Informationen können auch hilfreich sein, wenn Sie die Bereitstellung Ihrer Dienste planen.

## <a name="symptom"></a>Symptom

Navigieren Sie im Azure-Portal zu Ihrem Clouddienst (klassisch), und klicken Sie in der Randleiste auf *Vorgangsprotokolle (klassisch)* , um die Protokolle anzuzeigen.

![Das Bild zeigt das Blatt „Vorgangsprotokolle (klassisch)“.](./media/cloud-services-troubleshoot-fabric-internal-server-error/cloud-services-troubleshoot-allocation-logs.png)

Wenn Sie die Protokolle Ihres Clouddiensts (klassisch) überprüfen, wird die folgende Ausnahme angezeigt:

|Ausnahme  |Fehlermeldung  |
|---------|---------|
FabricInternalServerError     |Fehler beim Vorgang. Fehlercode „InternalError“ und errorMessage „Interner Serverfehler. Versuchen Sie die Anfrage noch einmal.“|
|ServiceAllocationFailure     |Fehler beim Vorgang. Fehlercode „InternalError“ und errorMessage „Interner Serverfehler. Versuchen Sie die Anfrage noch einmal.“|

## <a name="cause"></a>Ursache

*FabricInternalServerError* und *ServiceAllocationFailure* sind Ausnahmen, die auftreten können, wenn der Fabric Controller keine Instanzen im Cluster zuordnen kann. Die Ursache hängt davon ab, ob der Clouddienst **angeheftet** oder **nicht angeheftet** ist.

- [**Nicht angeheftet:** Fehler bei der ersten Bereitstellung eines neuen Clouddiensts](#not-pinned-to-a-cluster)
- [**Angeheftet:** Fehler bei vorhandenen Clouddiensten](#pinned-to-a-cluster)

> [!NOTE]
> Wenn die erste Instanz in einem Clouddienst bereitgestellt wird (entweder beim Staging oder in der Produktion), wird der Clouddienst an einen Cluster angeheftet.
>
> Im Laufe der Zeit können die Ressourcen in diesem Ressourcenpool vollständig ausgelastet werden. Wenn ein Clouddienst eine Zuordnungsanforderung für zusätzliche Ressourcen sendet, wenn die Ressourcen im angehefteten Ressourcenpool unzureichend sind, führt die Anforderung zu einem [Zuordnungsfehler](cloud-services-allocation-failures.md).

## <a name="solution"></a>Lösung

Befolgen Sie die Anleitungen zur Behandlung von Zuordnungsfehlern in den folgenden Szenarien.

### <a name="not-pinned-to-a-cluster"></a>Nicht an einen Cluster angeheftet

Wenn Sie einen Clouddienst (klassisch) zum ersten Mal bereitstellen, wurde der Cluster noch nicht ausgewählt, sodass der Clouddienst nicht *angeheftet* ist. Bei Azure kann aus folgenden Gründen ein Bereitstellungsfehler auftreten:

- Sie haben eine bestimmte Größe ausgewählt, die in der Region nicht verfügbar ist.
- Die Kombination der Größen, die in verschiedenen Rollen benötigt werden, ist in der Region nicht verfügbar.

Wenn in diesem Szenario ein Zuordnungsfehler auftritt, sollten Sie die verfügbaren Größen in der Region überprüfen und die von Ihnen zuvor angegebene Größe ändern.

1. Sie können auf der Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=cloud-services) überprüfen, welche Größen in einer Region verfügbar sind.

    > [!NOTE]
    > Die verfügbare Kapazität wird auf der Seite *Produkte* nicht angezeigt. Für jede neue Zuordnung sollte Azure in der Lage sein, den zu diesem Zeitpunkt optimalen Cluster in Ihrer Region auszuwählen.

1. Aktualisieren Sie die Dienstdefinitionsdatei für Ihren Clouddienst (klassisch), um eine andere [Produktgröße](cloud-services-sizes-specs.md#configure-sizes-for-cloud-services) in Ihrer Region anzugeben.

### <a name="pinned-to-a-cluster"></a>An einen Cluster angeheftet

Vorhandene Clouddienste werden an einen Cluster *angeheftet*. Alle weiteren Bereitstellungen für den Clouddienst (klassisch) erfolgen im gleichen Cluster.

Wenn in diesem Szenario ein Zuteilungsfehler auftritt, wird empfohlen, einen neuen Clouddienst (klassisch) bereitzustellen (und den *CNAME*-Eintrag zu aktualisieren).

> [!TIP]
> Diese Lösung ist wahrscheinlich am erfolgreichsten, da die Plattform hierbei eine Auswahl aus allen Clustern in dieser Region treffen kann.

> [!NOTE]
> Bei dieser Lösung sollten keine Ausfallzeiten entstehen.

1. Stellen Sie die Workload in einem neuen Clouddienst (klassisch) bereit.
    - Weitere Hinweise finden Sie im Leitfaden [Erstellen und Bereitstellen eines Clouddiensts (klassisch)](cloud-services-how-to-create-deploy-portal.md).

    > [!WARNING]
    > Wenn Sie die mit diesem Bereitstellungsslot verknüpfte IP-Adresse nicht verlieren möchten, können Sie [Lösung 3 – Beibehalten der IP-Adresse](cloud-services-allocation-failures.md#solutions) verwenden.

1. Aktualisieren Sie den *CNAME*- oder *A*-Eintrag, um den Datenverkehr an den neuen Clouddienst (klassisch) umzuleiten.
    - Weitere Anleitungen finden Sie im Leitfaden [Konfigurieren eines benutzerdefinierten Domänennamens für einen Azure-Clouddienst (klassisch)](cloud-services-custom-domain-name-portal.md#understand-cname-and-a-records).

1. Wenn kein Datenverkehr mehr an die alte Website geleitet wird, können Sie den alten Clouddienst (klassisch) löschen.
    - Weitere Anleitungen finden Sie im Leitfaden [Löschen von Bereitstellungen und eines Clouddiensts (klassisch)](cloud-services-how-to-manage-portal.md#delete-deployments-and-a-cloud-service).
    - Informationen zum Netzwerkdatenverkehr in Ihrem Clouddienst (klassisch) finden Sie unter [Einführung in die Überwachung von Clouddiensten (klassisch)](cloud-services-how-to-monitor.md).

Weitere Wartungsschritte finden Sie unter [Problembehandlung von Zuteilungsfehlern in Clouddiensten (klassisch) | Microsoft-Dokumentation](cloud-services-allocation-failures.md#common-issues).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Zuteilungsfehlern und Hintergrundinformationen:

> [!div class="nextstepaction"]
> [Zuteilungsfehler – Clouddienste (klassisch)](cloud-services-allocation-failures.md)

Suchen Sie in den Azure-Foren bei [MSDN und Stack Overflow](https://azure.microsoft.com/support/forums/), falls Sie Ihr Azure-Problem mit diesem Artikel nicht beheben konnten. Sie können Ihr Problem in diesen Foren veröffentlichen oder [auf Twitter an @AzureSupport senden](https://twitter.com/AzureSupport). Sie können auch eine Azure-Supportanfrage senden. Wenn Sie eine Supportanfrage senden möchten, wählen Sie auf der [Azure-Support-Seite](https://azure.microsoft.com/support/options/) die Option *Support erhalten* aus.
