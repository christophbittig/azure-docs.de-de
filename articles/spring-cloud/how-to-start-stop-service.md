---
title: Starten oder Beenden einer Azure Spring Cloud-Dienstinstanz
description: Beschreibt das Starten oder Beenden einer Azure Spring Cloud-Dienstinstanz
author: karlerickson
ms.author: wepa
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 010c117218edccd31fb397785358a35c1774754a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027487"
---
# <a name="start-or-stop-your-azure-spring-cloud-service-instance"></a>Starten oder Beenden Ihrer Azure Spring Cloud-Dienstinstanz

In diesem Artikel wird beschrieben, wie eine Azure Spring Cloud-Dienstinstanz gestartet oder beendet wird.

> [!NOTE]
> Das Beenden und Starten befindet sich derzeit in der Vorschauphase.

Ihre Anwendungen, die in Azure Spring Cloud ausgeführt werden, müssen möglicherweise nicht kontinuierlich ausgeführt werden, etwa wenn Sie über eine Dienstinstanz verfügen, die nur während der Geschäftszeiten verwendet wird. In diesen Zeiten kann sich Azure Spring Cloud im Leerlauf befinden und nur die Systemkomponenten ausführen.

Sie können den aktiven Speicherbedarf von Azure Spring Cloud reduzieren, indem Sie die ausgeführten Instanzen reduzieren und sicherstellen, dass die Kosten für Computeressourcen reduziert werden.

Um Ihre Kosten weiter zu senken, können Sie Ihre Azure Spring Cloud-Dienstinstanz vollständig beenden. Alle Benutzer-Apps und Systemkomponenten werden beendet. Allerdings werden alle Ihre Objekte und Netzwerkeinstellungen gespeichert, damit Sie Ihre Dienstinstanz neu starten und direkt dort fortfahren können, wo Sie aufgehört haben.

> [!NOTE]
> Der Status einer beendeten Azure Spring Cloud-Dienstinstanz wird während der Vorschauphase bis zu 90 Tage lang beibehalten. Wenn Ihr Cluster länger als 90 Tage beendet wird, kann der Clusterstatus nicht mehr wiederhergestellt werden.
> Die maximale Beendigungszeit kann sich nach der Vorschau ändern.

Sie können eine beendete Azure Spring Cloud-Dienstinstanz nur starten, anzeigen oder löschen. Sie müssen Ihre Dienstinstanz starten, bevor Sie einen Aktualisierungsvorgang ausführen, z. B. das Erstellen oder Skalieren einer App.

## <a name="prerequisites"></a>Voraussetzungen

- Eine vorhandene Dienstinstanz in Azure Spring Cloud. Weitere Informationen zum Erstellen einer neuen Dienstinstanz finden Sie unter [Schnellstart: Bereitstellen Ihrer ersten Anwendung in Azure Spring Cloud](./quickstart.md).
- (Optional) [Azure CLI](/cli/azure/install-azure-cli), Version 2.11.2 oder höher.

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="stop-a-running-instance"></a>Beenden einer ausgeführten Instanz

Führen Sie im Azure-Portal die folgenden Schritte aus, um eine ausgeführte Azure Spring Cloud-Instanz zu beenden:

1. Navigieren Sie zur Seite „Übersicht“ des Azure Spring Cloud-Diensts.
2. Wählen Sie **Beenden** aus, um eine ausgeführte Instanz zu beenden.

   :::image type="content" source="media/stop-start-service/spring-cloud-stop-service.png" alt-text="Screenshot des Azure-Portals: Seite „Übersicht“ von Azure Spring Cloud mit hervorgehobener Schaltfläche &quot;Beenden&quot; und Wert für „Status“":::

3. Nachdem die Instanz beendet wurde, wird als Status **Erfolgreich (Beendet)** angezeigt.

## <a name="start-a-stopped-instance"></a>Starten einer beendeten Instanz

Führen Sie im Azure-Portal die folgenden Schritte aus, um eine beendete Azure Spring Cloud-Instanz zu starten:

1. Navigieren Sie zur Seite „Übersicht“ des Azure Spring Cloud-Diensts.
2. Wählen Sie **Starten** aus, um eine beendete Instanz zu starten.

   :::image type="content" source="media/stop-start-service/spring-cloud-start-service.png" alt-text="Screenshot des Azure-Portals: Seite „Übersicht“ von Azure Spring Cloud mit hervorgehobener Schaltfläche &quot;Starten&quot; und Wert für „Status“":::

3. Nachdem die Instanz gestartet wurde, wird als Status **Erfolgreich (Wird ausgeführt)** angezeigt.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

## <a name="stop-a-running-instance"></a>Beenden einer ausgeführten Instanz

Verwenden Sie mit der Azure CLI den folgenden Befehl, um eine ausgeführte Azure Spring Cloud-Instanz zu beenden:

```azurecli
az spring-cloud stop \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="start-a-stopped-instance"></a>Starten einer beendeten Instanz

Verwenden Sie mit der Azure CLI den folgenden Befehl, um eine beendete Azure Spring Cloud-Instanz zu starten:

```azurecli
az spring-cloud start \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

## <a name="check-the-power-state"></a>Überprüfen des Energiezustands

Nachdem die Instanz beendet oder gestartet wurde, verwenden Sie den folgenden Befehl, um den Energiezustand zu überprüfen:

```azurecli
az spring-cloud show \
    --name <service-instance-name> \
    --resource-group <resource-group-name>
```

---

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von App-Lebenszyklusereignissen mithilfe des Azure-Aktivitätsprotokolls und der Azure Service Health](./monitor-app-lifecycle-events.md)
* [Überwachen der Nutzung und geschätzten Kosten in Azure Monitor](../azure-monitor/usage-estimated-costs.md)
