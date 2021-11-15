---
title: Verwenden des integrierten persistenten Speichers in Azure Spring Cloud | Microsoft-Dokumentation
description: Verwenden des integrierten persistenten Speichers in Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b56932b9638ede2b3db36050310a06921d115103
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478849"
---
# <a name="use-built-in-persistent-storage-in-azure-spring-cloud"></a>Verwenden des integrierten persistenten Speichers in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

Azure Spring Cloud bietet zwei Typen von integriertem Speicher für Ihre Anwendung: persistenten und temporären Speicher.

Azure Spring Cloud aktiviert standardmäßig temporären Speicher für jede Anwendungsinstanz. Der temporäre Speicher ist im standardmäßigen Einbindungspfad„/tmp“ auf 5 GB pro Instanz beschränkt.

> [!WARNING]
> Durch das Neustarten einer Anwendungsinstanz wird der zugehörige temporäre Speicher dauerhaft gelöscht.

Der beständige Speicher ist ein von Azure verwalteter Dateifreigabecontainer, der pro Anwendung zugeordnet wird. Im beständigen Speicher gespeicherte Daten werden für alle Instanzen einer Anwendung freigegeben. Eine Azure Spring Cloud-Instanz kann maximal zehn Anwendungen mit aktiviertem beständigen Speicher enthalten. Jeder Anwendung werden 50 GB an beständigem Speicher zugeordnet. Der Standardeinbindungspfad für beständigen Speicher lautet „/persistent“.

> [!WARNING]
> Wenn Sie den beständigen Speicher für eine Anwendung deaktivieren, wird die Zuordnung dieses Speichers aufgehoben, und alle gespeicherten Daten gehen verloren.

## <a name="enable-or-disable-built-in-persistent-storage"></a>Aktivieren oder Deaktivieren des integrierten persistenten Speichers

Sie können den Zustand des integrierten persistenten Speichers über das Azure-Portal oder mithilfe der Azure-Befehlszeilenschnittstelle ändern.

#### <a name="portal"></a>[Portal](#tab/azure-portal)
## <a name="enable-or-disable-built-in-persistent-storage-with-the-portal"></a>Aktivieren oder Deaktivieren des integrierten persistenten Speichers über das Portal
Sie können das Portal verwenden, um den integrierten persistenten Speicher zu aktivieren oder zu deaktivieren.

1. Wählen Sie auf dem **Startbildschirm** des Azure-Portals die Option **Alle Ressourcen** aus.

    >![Suchen des Symbols „Alle Ressourcen“](media/portal-all-resources.jpg)

1. Wählen Sie die Azure Spring Cloud-Ressource aus, für die beständiger Speicher benötigt wird. In diesem Beispiel heißt die Anwendung **upspring**.

    > ![Wählen Sie Ihre Anwendung.](media/select-service.jpg)

1. Wählen Sie unter der Überschrift **Einstellungen** die Option **Apps** aus.

1. Ihre Azure Spring Cloud-Dienste werden in einer Tabelle angezeigt.  Wählen Sie den Dienst aus, dem Sie persistenten Speicher hinzufügen möchten. In diesem Beispiel ist der Dienst **Gateway** ausgewählt.

    > ![Auswählen Ihres Dienstes](media/select-gateway.jpg)

1. Wählen Sie auf der Seite „Konfiguration“ des Diensts die Option **Konfiguration** aus.

1. Wählen Sie die Registerkarte **Persistenter Speicher** und dann **Aktivieren** aus, um persistenten Speicher zu aktivieren, oder wählen Sie **Deaktivieren** aus, um persistenten Speicher zu deaktivieren.

    > ![Aktivieren von beständigem Speicher](media/enable-persistent-storage.jpg)

Wenn der persistente Speicher aktiviert ist, werden Größe und Pfad auf der Registerkarte **Persistenter Speicher** angezeigt.

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
## <a name="use-the-azure-cli-to-enable-or-disable-built-in-persistent-storage"></a>Verwenden der Azure-Befehlszeilenschnittstelle zum Aktivieren oder Deaktivieren des integrierten persistenten Speichers
Installieren Sie bei Bedarf die Spring Cloud-Erweiterung für die Azure-Befehlszeilenschnittstelle mithilfe dieses Befehls:

```azurecli
az extension add --name spring-cloud
```

Andere Vorgänge:

* So erstellen Sie eine App mit aktiviertem integrierten persistenten Speicher

    ```azurecli
    az spring-cloud app create -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* So aktivieren Sie den integrierten persistenten Speicher für eine vorhandene App

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage true
    ```

* So deaktivieren Sie den integrierten persistenten Speicher bei einer vorhandenen App

    ```azurecli
    az spring-cloud app update -n <app> -g <resource-group> -s <service-name> --enable-persistent-storage false
    ```
---
> [!WARNING]
> Wenn Sie den beständigen Speicher einer Anwendung deaktivieren, wird die Zuordnung dieses Speichers aufgehoben, und alle gespeicherten Daten gehen für immer verloren.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Kontingente und Servicepläne für Azure Spring Cloud](./quotas.md).
* Erfahren Sie, wie Sie [eine Anwendung in Azure Spring Cloud skalieren](./how-to-scale-manual.md).
