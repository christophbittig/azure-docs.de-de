---
title: Starten, Beenden und Löschen einer Anwendung in Azure Spring Cloud | Microsoft-Dokumentation
description: 'Gewusst wie: Starten, Beenden und Löschen einer Anwendung in Azure Spring Cloud'
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 0e27f7ac841635f33bc40db739df4cf2240e2149
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484743"
---
# <a name="start-stop-and-delete-an-application-in-azure-spring-cloud"></a>Starten, Beenden und Löschen einer Anwendung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In diesem Leitfaden wird erläutert, wie Sie den Zustand einer Anwendung in Azure Spring Cloud mithilfe des Azure-Portals oder der Azure CLI ändern.

## <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

Nach dem Bereitstellen einer Anwendung können Sie sie über das Azure-Portal starten, beenden und löschen.

1. Navigieren Sie im Azure-Portal zu Ihrer Azure Spring Cloud-Dienstinstanz.
1. Wählen Sie die Registerkarte **Anwendungsdashboard** aus.
1. Wählen Sie die Anwendung aus, deren Zustand Sie ändern möchten.
1. Auf der Seite **Übersicht** für diese Anwendung wählen Sie eine der Optionen **Starten/Beenden**, **Neu starten** oder **Löschen** aus.

## <a name="using-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

> [!NOTE]
> Sie können optionale Parameter verwenden und Standardwerte mit der Azure CLI konfigurieren. Weitere Informationen zur Azure CLI finden Sie in der [Referenzdokumentation](/cli/azure/spring-cloud).

Installieren Sie mithilfe des folgenden Befehls zuerst die Azure Spring Cloud-Erweiterung für die Azure-Befehlszeilenschnittstelle:

```azurecli
az extension add --name spring-cloud
```

Wählen Sie dann einen der Azure CLI-Vorgänge aus:

* So starten Sie Ihre Anwendung:

    ```azurecli
    az spring-cloud app start -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* So beenden Sie Ihre Anwendung:

    ```azurecli
    az spring-cloud app stop -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* So starten Sie Ihre Anwendung neu:

    ```azurecli
    az spring-cloud app restart -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```

* So löschen Sie Ihre Anwendung:

    ```azurecli
    az spring-cloud app delete -n <application name> -g <resource group> -s <Azure Spring Cloud name>
    ```
