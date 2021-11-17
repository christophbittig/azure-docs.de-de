---
title: Konfigurieren des Dateiuploads in IoT Hub mit der Azure CLI | Microsoft Docs
description: Erfahren Sie, wie Sie Dateiuploads in Azure IoT Hub mithilfe der plattformübergreifenden Azure CLI konfigurieren.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.openlocfilehash: c3aa2d496151d8c9d9f6ef5acfea6dee55548b2b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555601"
---
# <a name="configure-iot-hub-file-uploads-using-azure-cli"></a>Konfigurieren von IoT Hub-Dateiuploads mit der Azure CLI

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

In diesem Artikel erfahren Sie, wie Sie Dateiuploads in Ihren IoT-Hub unter Verwendung der Azure CLI konfigurieren. 

Damit Sie die [Dateiuploadfunktion in IoT Hub](iot-hub-devguide-file-upload.md) nutzen können, müssen Sie Ihrem IoT-Hub zuerst ein Azure-Speicherkonto und einen Blobcontainer zuordnen. IoT Hub generiert automatisch SAS-URIs mit Schreibberechtigungen für diesen Blobcontainer, die Geräte beim Hochladen von Dateien verwenden können. Zusätzlich zum Speicherkonto und Blobcontainer können Sie die Gültigkeitsdauer (TTL) für den SAS-URI und den Authentifizierungstyp festlegen, der von IoT Hub für Azure Storage verwendet wird. Sie können auch Einstellungen für die optionalen Dateiuploadbenachrichtigungen konfigurieren, die IoT Hub an Back-End-Dienste übermitteln kann.

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Einen Azure IoT Hub. Wenn Sie über keinen IoT Hub verfügen, können Sie mit dem [`az iot hub create`-Befehl](/cli/azure/iot/hub#az_iot_hub_create) oder [über das Portal](iot-hub-create-through-portal.md) einen IoT Hub erstellen.

* Azure Storage-Konto Wenn Sie über kein Azure Storage Konto verfügen, können Sie über die Azure CLI ein Konto erstellen. Weitere Informationen finden Sie unter [Erstellen eines Speicherkontos](../storage/common/storage-account-create.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Anmelden und Festlegen Ihres Azure-Kontos

Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie Ihr Abonnement aus. Wenn Sie Azure Cloud Shell verwenden, sollten Sie bereits angemeldet sein. Wenn Sie über mehrere Abonnements verfügen, müssen Sie aber möglicherweise weiterhin Ihr Azure-Abonnement auswählen.

1. Führen Sie an der Eingabeaufforderung den [Anmeldebefehl](/cli/azure/get-started-with-azure-cli) aus:

    ```azurecli
    az login
    ```

    Befolgen Sie die Anweisungen zur Authentifizierung mit dem Code, und melden Sie sich in einem Webbrowser bei Ihrem Azure-Konto an.

2. Wenn Sie über mehrere Azure-Abonnements verfügen, erhalten Sie durch die Anmeldung bei Azure Zugriff auf alle Azure-Konten, die mit Ihren Anmeldeinformationen verknüpft sind. Führen Sie den folgenden [Befehl aus, um eine Liste der Azure-Konten anzuzeigen](/cli/azure/account), die Sie verwenden können:

    ```azurecli
    az account list
    ```

    Führen Sie den folgenden Befehl aus, um das Abonnement auszuwählen, das Sie zum Ausführen der Befehle zum Erstellen Ihres IoT Hubs verwenden möchten. Sie können entweder den Abonnementnamen oder die ID aus der Ausgabe des vorherigen Befehls verwenden:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="retrieve-your-storage-account-details"></a>Abrufen der Details Ihres Speicherkontos

In den folgenden Schritten wird davon ausgegangen, dass Sie Ihr Speicherkonto mit dem **Ressourcen-Manager**-Bereitstellungsmodell und nicht mit dem **klassischen** Bereitstellungsmodell erstellt haben.

Sie benötigen die Verbindungszeichenfolge eines Azure Storage-Kontos, um das Hochladen von Dateien von Ihren Geräten zu konfigurieren. Das Speicherkonto muss sich in demselben Abonnement wie der IoT-Hub befinden. Sie benötigen auch den Name des Blobcontainers im Speicherkonto. Verwenden Sie den folgenden Befehl, um Ihre Speicherkontoschlüssel abzurufen:

```azurecli
az storage account show-connection-string --name {your storage account name} \
  --resource-group {your storage account resource group}
```
Die Verbindungszeichenfolge sieht in etwa wie folgt aus:

```json
{
  "connectionString": "DefaultEndpointsProtocol=https;EndpointSuffix=core.windows.net;AccountName={your storage account name};AccountKey={your storage account key}"
}
```

Notieren Sie sich den Wert für `connectionString`. Sie benötigen ihn in den folgenden Schritten.

Sie können einen vorhandenen Blobcontainer für das Hochladen von Dateien verwenden oder einen neuen Blobcontainer erstellen:

* Um die vorhandenen Blobcontainer in Ihrem Speicherkonto aufzulisten, verwenden Sie den folgenden Befehl:

    ```azurecli
    az storage container list --connection-string "{your storage account connection string}"
    ```

* Um einen Blobcontainer in Ihrem Speicherkonto zu erstellen, verwenden Sie den folgenden Befehl:

    ```azurecli
    az storage container create --name {container name} \
      --connection-string "{your storage account connection string}"
    ```

## <a name="configure-your-iot-hub"></a>Konfigurieren Ihres IoT-Hubs

Sie können nun Ihren IoT Hub so konfigurieren, dass er die Möglichkeit zum [Hochladen von Dateien auf den IoT Hub](iot-hub-devguide-file-upload.md) unter Verwendung Ihrer Speicherkontodetails bietet.

Die Konfiguration erfordert die folgenden Werte:

* **Speichercontainer**: Ein Blobcontainer in einem Azure-Speicherkonto Ihres aktuellen Azure-Abonnements, der Ihrer IoT Hub-Instanz zugeordnet werden soll. Sie haben die erforderlichen Speicherkontoinformationen im vorherigen Abschnitt abgerufen. IoT Hub generiert automatisch SAS-URIs mit Schreibberechtigungen für diesen Blobcontainer, die Geräte beim Hochladen von Dateien verwenden können.

* **Benachrichtigungen für hochgeladene Dateien empfangen**: Aktivieren oder deaktivieren Sie Benachrichtigungen zum Hochladen von Dateien.

* **SAS-TTL**: Diese Einstellung dient zum Festlegen der Gültigkeitsdauer der SAS-URIs, die von IoT Hub an das Gerät zurückgegeben werden. Standardmäßig auf 1 Stunde festgelegt.

* **Standard-TTL für Dateibenachrichtigungseinstellungen**: Die Gültigkeitsdauer einer Dateiuploadbenachrichtigung (Zeit bis zum Ablauf). Standardmäßig auf 1 Tag festgelegt.

* **Anzahl maximaler Zustellungen für Dateibenachrichtigungen**: Gibt an, wie oft IoT Hub versucht, eine Dateiuploadbenachrichtigung zu senden. Standardmäßig auf 10 festgelegt.

* **Dauer der Dateibenachrichtigungssperre**: Die Sperrdauer für die Dateibenachrichtigungswarteschlange. Standardmäßig auf 60 Sekunden festgelegt.

* **Authentifizierungstyp**: Die Art der Authentifizierung für IoT Hub zur Verwendung mit Azure Storage. Diese Einstellung bestimmt, wie sich Ihr IoT-Hub authentifiziert und bei Azure Storage autorisiert. Standardmäßig ist die schlüsselbasierte Authentifizierung festgelegt. Es können jedoch auch systemseitig zugewiesene und benutzerseitig zugewiesene verwaltete Identitäten verwendet werden. Verwaltete Identitäten stellen Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD auf sichere Weise bereit. Informationen zum Konfigurieren von verwalteten Identitäten für Ihren IoT-Hub und Ihr Azure Storage-Konto finden Sie unter [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md). Nach der Konfiguration können Sie eine Ihrer verwalteten Identitäten für die Authentifizierung bei Azure Storage festlegen.

    > [!NOTE]
    > Mit der Einstellung für den Authentifizierungstyp wird konfiguriert, wie sich Ihr IoT-Hub bei Ihrem Azure Storage-Konto authentifiziert. Geräte authentifizieren sich bei Azure Storage immer mithilfe des SAS-URI, den sie vom IoT-Hub abrufen. 


Die folgenden Befehle zeigen, wie Sie die Einstellungen für Dateiuploads für Ihren IoT-Hub konfigurieren. Diese Befehle werden aus Gründen der Übersichtlichkeit separat angezeigt, aber in der Regel würden Sie einen einzelnen Befehl mit allen erforderlichen Parametern für Ihr Szenario ausführen. Fügen Sie Anführungszeichen ein, wenn sie in der Befehlszeile angezeigt werden. Fügen Sie keine geschweiften Klammern ein. Weitere Informationen zu den einzelnen Parametern finden Sie in der Azure CLI-Dokumentation für den Befehl [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update).

Mit dem folgenden Befehl werden das Speicherkonto und der Blobcontainer konfiguriert.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-connectionstring "{your storage account connection string}" \
    --fileupload-storage-container-name "{your container name}" 
```

Mit dem folgenden Befehl wird der TTL-Wert für den SAS-URI auf den Standardwert (eine Stunde) festgelegt.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-sas-ttl 1 
```

Der folgende Befehl aktiviert Dateibenachrichtigungen und legt die Dateibenachrichtigungseigenschaften auf ihre Standardwerte fest. (Der TTL-Wert für Dateiuploadbenachrichtigungen wird auf eine Stunde und die Sperrdauer auf 60 Sekunden festgelegt.)

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-notifications true  \
    --fileupload-notification-max-delivery-count 10 \
    --fileupload-notification-ttl 1 \
    --fileupload-notification-lock-duration 60
```

Der folgende Befehl konfiguriert die schlüsselbasierte Authentifizierung:

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type keyBased
```

Der folgende Befehl konfiguriert die Authentifizierung mithilfe der systemseitig zugewiesenen verwalteten Identität des IoT-Hubs. Bevor Sie diesen Befehl ausführen können, müssen Sie die systemseitig zugewiesene verwaltete Identität für Ihren IoT-Hub aktivieren und ihr die richtige RBAC-Rolle für Ihr Azure Storage-Konto zuweisen. Weitere Informationen hierzu finden Sie unter [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md).

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity [system] 
```

Die folgenden Befehle rufen die benutzerseitig zugewiesenen verwalteten Identitäten ab, die für Ihren IoT-Hub konfiguriert sind, und konfigurieren die Authentifizierung mit einer dieser Identitäten. Bevor Sie eine benutzerseitig zugewiesene verwaltete Identität für die Authentifizierung verwenden können, müssen Sie die Identität für Ihren IoT-Hub konfigurieren und ihr eine geeignete RBAC-Rolle für Ihr Azure Storage-Konto zuweisen. Weitere Einzelheiten und Schritte finden Sie unter [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md).

Verwenden Sie den Befehl [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show), um die benutzerseitig zugewiesenen verwalteten Identitäten für Ihren IoT-Hub abzufragen.

```azurecli
az iot hub identity show --name {your iot hub name} --query userAssignedIdentities
```

Der Befehl gibt eine Sammlung der benutzerseitig zugewiesenen verwalteten Identitäten zurück, die für Ihren IoT-Hub konfiguriert sind. Die folgende Ausgabe zeigt eine Sammlung, die eine einzelne benutzerseitig zugewiesene verwaltete Identität enthält.

```json
{
  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}": 
  {
    "clientId": "<client ID GUID>",
    "principalId": "<principal ID GUID>"
  }
}
```

Mit dem folgenden Befehl wird die Authentifizierung so konfiguriert, dass die oben aufgeführte benutzerseitig zugewiesene Identität verwendet wird.

```azurecli
az iot hub update --name {your iot hub name} \
    --fileupload-storage-auth-type identityBased \
    --fileupload-storage-identity  "/subscriptions/{your subscription ID}/resourcegroups/{your resource group}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{your user-assigned managed identity name}"
```

Sie können die Einstellungen für Ihren IoT-Hub mithilfe des folgenden Befehls überprüfen:

```azurecli
az iot hub show --name {your iot hub name}
```

Verwenden Sie den folgenden Befehl, um nur die Einstellungen für den Dateiupload zu überprüfen:

```azurecli
az iot hub show --name {your iot hub name}
    --query '[properties.storageEndpoints, properties.enableFileUploadNotifications, properties.messagingEndpoints.fileNotifications]'
```

In den meisten Fällen ist die Verwendung der benannten Parameter in Azure CLI-Befehlen am einfachsten. Sie können die Einstellungen für den Dateiupload aber auch mit dem Parameter `--set` konfigurieren. Die folgenden Befehle können zu einem besseren Verständnis beitragen. 

```azurecli
az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.connectionString="{your storage account connection string}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.containerName="{your storage container name}"

az iot hub update --name {your iot hub name} \
  --set properties.storageEndpoints.'$default'.sasTtlAsIso8601=PT1H0M0S

az iot hub update --name {your iot hub name} \
  --set properties.enableFileUploadNotifications=true

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.maxDeliveryCount=10

az iot hub update --name {your iot hub name} \
  --set properties.messagingEndpoints.fileNotifications.ttlAsIso8601=PT1H0M0S
```

## <a name="next-steps"></a>Nächste Schritte

* [Hochladen von Dateien von einem Gerät – Übersicht](iot-hub-devguide-file-upload.md)
* [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md)
* [Anleitungen zum Dateiupload](./iot-hub-csharp-csharp-file-upload.md)
* Azure CLI-Befehle [az iot hub update](/cli/azure/iot/hub#az_iot_hub_update), [az iot hub identity show](/cli/azure/iot/hub/identity#az_iot_hub_identity_show) und [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create)
