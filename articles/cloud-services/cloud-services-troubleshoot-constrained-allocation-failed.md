---
title: Problembehandlung von ConstrainedAllocationFailed beim Bereitstellen eines Clouddiensts (klassisch) in Azure | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie eine ConstrainedAllocationFailed-Ausnahme bei der Bereitstellung eines Clouddiensts (klassisch) in Azure auflösen.
services: cloud-services
author: hirenshah1
ms.author: hirshah
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.openlocfilehash: 6c277fb073776a7cdbc31cd4ccc9183f8024ce1d
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823534"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-classic-to-azure"></a>Problembehandlung von ConstrainedAllocationFailed beim Bereitstellen eines Clouddiensts (klassisch) in Azure

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

In diesem Artikel beheben Sie Zuteilungsfehler, bei denen Azure-Clouddienste (klassisch) aufgrund von Zuteilungseinschränkungen nicht bereitgestellt werden können.

Wenn Sie Instanzen in einem Clouddienst (klassisch) bereitstellen oder neue Web- oder Workerrolleninstanzen hinzufügen, weist Microsoft Azure Computeressourcen zu.

Unter Umständen erhalten Sie bei diesen Vorgängen auch dann gelegentlich Fehlermeldungen, wenn Sie die Grenzwerte des Azure-Abonnements noch nicht erreicht haben.

> [!TIP]
> Diese Informationen können auch hilfreich sein, wenn Sie die Bereitstellung Ihrer Dienste planen.

## <a name="symptom"></a>Symptom

Navigieren Sie im Azure-Portal zu Ihrem Clouddienst (klassisch), und klicken Sie in der Randleiste auf *Vorgangsprotokolle (klassisch)* , um die Protokolle anzuzeigen.

![Das Bild zeigt das Blatt „Vorgangsprotokolle (klassisch)“.](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-allocation-logs.png)

Wenn Sie die Protokolle Ihres Clouddiensts (klassisch) überprüfen, wird die folgende Ausnahme angezeigt:

|Ausnahmetyp  |Fehlermeldung  |
|---------|---------|
|ConstrainedAllocationFailed     |Fehler bei Azure-Vorgang '`{Operation ID}`‘ mit dem Code Compute.ConstrainedAllocationFailed. Details: Fehler bei der Zuordnung. Einschränkungen in der Anforderung konnten nicht erfüllt werden. Die angeforderte neue Dienstbereitstellung ist an eine Affinitätsgruppe gebunden oder auf ein virtuelles Netzwerk ausgerichtet, oder unter diesem gehosteten Dienst befindet sich eine vorhandene Bereitstellung. Alle diese Bedingungen beschränken die neue Bereitstellung auf bestimmte Azure-Ressourcen. Versuchen Sie es später noch mal, oder verringern Sie die VM-Größe oder die Anzahl von Rolleninstanzen. Sie können, wenn möglich, auch die zuvor erwähnten Einschränkungen entfernen oder den virtuellen Computer in einer anderen Region bereitstellen.|

## <a name="cause"></a>Ursache

Wenn die erste Instanz in einem Clouddienst bereitgestellt wird (entweder beim Staging oder in der Produktion), wird der Clouddienst an einen Cluster angeheftet.

Im Laufe der Zeit werden die Ressourcen in diesem Cluster unter Umständen vollständig ausgelastet. Wenn ein Clouddienst (klassisch) eine Zuteilungsanforderung für weitere Ressourcen sendet, wenn die Ressourcen im angehefteten Cluster nicht ausreichen, führt die Anforderung zu einem Zuteilungsfehler. Weitere Informationen finden Sie unter [Häufige Probleme bei Zuteilungsfehlern](cloud-services-allocation-failures.md#common-issues).

## <a name="solution"></a>Lösung

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