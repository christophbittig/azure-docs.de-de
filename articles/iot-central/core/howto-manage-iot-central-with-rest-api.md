---
title: Verwenden der REST-API zum Verwalten und Überwachen von IoT Central-Anwendungen | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie IoT Central-Anwendungen über die REST-API erstellen und verwalten. Mit der REST-API können Sie Ihrer Anwendung auch eine systemseitig zugewiesene verwaltete Identität hinzufügen.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/25/2021
ms.topic: how-to
ms.openlocfilehash: a4df2346346d048bcd4e01e898636c1668720c4c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096116"
---
# <a name="use-the-rest-api-to-create-and-manage-iot-central-applications"></a>Verwenden der REST-API zum Erstellen und Verwalten von IoT Central-Anwendungen

Sie können die [REST-API für die Steuerungsebene](/rest/api/iotcentral/2021-06-01controlplane/apps) zum Erstellen und Verwalten von IoT Central-Anwendungen verwenden. Außerdem können Sie mit der REST-API Ihrer Anwendung eine verwaltete Identität hinzufügen.

Um diese API verwenden zu können, benötigen Sie ein Bearertoken für die Ressource `management.azure.com`. Dieses Bearertoken können Sie über die Azure CLI abrufen:

```azurecli
az account get-access-token --resource https://management.azure.com
```

## <a name="list-your-applications"></a>Auflisten Ihrer Anwendungen

So rufen Sie eine Liste der IoT Central-Anwendungen in einem Abonnement ab:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

So rufen Sie eine Liste der IoT Central-Anwendungen in einer Ressourcengruppe ab:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps?api-version=2021-06-01
```

Die Details für eine einzelne Anwendung können Sie folgendermaßen abrufen:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.IoTCentral/iotApps/{applicationName}?api-version=2021-06-01
```

## <a name="create-an-iot-central-application"></a>Erstellen einer IoT Central-Anwendung

So erstellen Sie eine IoT Central-Anwendung mit einer systemseitig zugewiesenen [verwalteten Identität](../../active-directory/managed-identities-azure-resources/overview.md):

```http
PUT https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

Die folgenden Nutzdaten zeigen die Konfiguration für die neue Anwendung, einschließlich der verwalteten Identität:

```json
{
  "location": "eastus",
  "sku": {
    "name": "ST2"
  },
  "properties": {
    "displayName": "Contoso IoT Central App",
    "subdomain": "my-iot-central-app",
    "template": "iotc-pnp-preview@1.0.0"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

## <a name="modify-an-iot-central-application"></a>Ändern einer IoT Central-Anwendung

Sie können bereits vorhandene IoT Central-Anwendungen ändern. Im folgenden Beispiel wird gezeigt, wie Sie den Anzeigenamen ändern und die systemseitig zugewiesene verwaltete Identität aktivieren:

```http
PATCH https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

Verwenden Sie die folgenden Nutzdaten, um den Anzeigenamen zu ändern und die systemseitig zugewiesene verwaltete Identität zu aktivieren:

```json
{
  "properties": {
    "displayName": "Contoso IoT Central App"
  },
  "identity": {
    "type": "SystemAssigned"
  }
}
```

> [!NOTE]
> Verwaltete Identitäten können nur IoT Central-Anwendungen hinzugefügt werden, die in einer Region erstellt wurden. Alle neuen Anwendungen werden in einer Region erstellt. Weitere Informationen finden Sie bei den [Updates](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/).

## <a name="delete-an-iot-central-application"></a>Löschen einer IoT Central-Anwendung

Verwenden Sie den folgenden Befehl, um eine IoT Central-Anwendung zu löschen:

```http
DELETE https://management.azure.com/subscriptions/<your subscription id>/resourceGroups/<your resource group name>/providers/Microsoft.IoTCentral/iotApps/<your application name>?api-version=2021-06-01
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun erfahren haben, wie Sie Azure IoT Central-Anwendungen über die REST-API erstellen und verwalten, wird als nächster Schritt Folgendes empfohlen:

> [!div class="nextstepaction"]
> [Verwenden der IoT Central-REST-API zum Verwalten von Benutzern und Rollen](howto-manage-users-roles-with-rest-api.md)