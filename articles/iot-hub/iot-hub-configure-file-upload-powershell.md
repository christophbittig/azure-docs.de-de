---
title: Konfigurieren des Hochladens von Dateien mit Azure PowerShell | Microsoft-Dokumentation
description: Informationen zum Konfigurieren des IoT Hubs mit Azure PowerShell-Cmdlets zum Aktivieren des Hochladens von Dateien von verbundenen Geräten. Enthält Informationen zum Konfigurieren des Azure-Zielspeicherkontos.
author: eross-msft
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/20/2021
ms.author: lizross
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f20236edd7a905f05d59ddafd3132569a851816a
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553931"
---
# <a name="configure-iot-hub-file-uploads-using-powershell"></a>Konfigurieren des Hochladens von Dateien in IoT Hub mit PowerShell

[!INCLUDE [iot-hub-file-upload-selector](../../includes/iot-hub-file-upload-selector.md)]

In diesem Artikel erfahren Sie, wie Sie Dateiuploads auf Ihrem IoT-Hub mithilfe von PowerShell konfigurieren. 

Damit Sie die [Dateiuploadfunktion in IoT Hub](iot-hub-devguide-file-upload.md) nutzen können, müssen Sie Ihrem IoT-Hub zuerst ein Azure-Speicherkonto und einen Blobcontainer zuordnen. IoT Hub generiert automatisch SAS-URIs mit Schreibberechtigungen für diesen Blobcontainer, die Geräte beim Hochladen von Dateien verwenden können. Zusätzlich zum Speicherkonto und Blobcontainer können Sie die Gültigkeitsdauer (TTL) für den SAS-URI festlegen und Einstellungen für die optionalen Benachrichtigungen zum Dateiupload konfigurieren, die IoT Hub an Back-End-Dienste übermitteln kann.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives Azure-Konto. Wenn Sie nicht über ein Konto verfügen, können Sie in nur wenigen Minuten ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen.

* Einen Azure IoT Hub. Wenn Sie keinen IoT Hub haben, können Sie mit dem Cmdlet [New-AzIoTHub](/powershell/module/az.iothub/new-aziothub) einen erstellen. Sie können auch im Portal einen [IoT Hub erstellen](iot-hub-create-through-portal.md).

* Ein Azure-Speicherkonto. Wenn Sie kein Azure-Speicherkonto haben, können Sie mithilfe der [PowerShell-Cmdlets für Azure Storage](/powershell/module/az.storage/) eines erstellen. Sie können auch im Portal ein [Speicherkonto erstellen](../storage/common/storage-account-create.md).

* Verwenden Sie die PowerShell-Umgebung in [Azure Cloud Shell](../cloud-shell/quickstart-powershell.md).

   [![Starten von Cloud Shell in einem neuen Fenster](./media/iot-hub-configure-file-upload-powershell/hdi-launch-cloud-shell.png)](https://shell.azure.com)

* Wenn es Ihnen lieber ist, [installieren Sie](/powershell/scripting/install/installing-powershell) PowerShell lokal.

  * [Installieren Sie das Azure Az PowerShell-Modul](/powershell/azure/install-az-ps). (Das Modul wird standardmäßig in der PowerShell-Umgebung von Azure Cloud Shell installiert.) 
  * Melden Sie sich bei PowerShell mit dem Befehl [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) an.  Führen Sie die in Ihrem Terminal angezeigten Schritte aus, um den Authentifizierungsprozess abzuschließen.  Weitere Anmeldeoptionen finden Sie unter [Anmelden mit Azure PowerShell](/powershell/azure/authenticate-azureps).


## <a name="sign-in-and-set-your-azure-account"></a>Anmelden und Festlegen Ihres Azure-Kontos

Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie Ihr Abonnement aus. Wenn Sie Azure Cloud Shell verwenden, sollten Sie bereits angemeldet sein. Wenn Sie mehrere Abonnements haben, müssen Sie aber möglicherweise weiterhin Ihr Azure-Abonnement auswählen.

1. Führen Sie an der PowerShell-Eingabeaufforderung das Cmdlet **Connect-AzAccount** aus:

    ```powershell
    Connect-AzAccount
    ```

2. Wenn Sie über mehrere Azure-Abonnements verfügen, erhalten Sie durch die Anmeldung bei Azure Zugriff auf alle Azure-Abonnements, die mit Ihren Anmeldeinformationen verknüpft sind. Mit dem Befehl [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) können Sie die Liste der für Sie verfügbaren Azure-Abonnements anzeigen:

    ```powershell
    Get-AzSubscription
    ```

    Führen Sie den folgenden Befehl aus, um das Abonnement auszuwählen, das Sie zum Ausführen der Befehle zum Verwalten Ihres IoT Hubs verwenden möchten. Sie können entweder den Abonnementnamen oder die ID aus der Ausgabe des vorherigen Befehls verwenden:

    ```powershell
    Select-AzSubscription `
        -Name "{your subscription name}"
    ```

    > [!NOTE]
    > Der Befehl **Select-AzSubscription** ist ein Alias des Befehls [Select-AzContext](/powershell/module/az.accounts/select-azcontext). Er ermöglicht Ihnen die Verwendung des vom Befehl **Get-AzSubscription** zurückgegebenen Abonnementnamens (**Name**) oder der Abonnement-ID (**Id**) statt des komplexeren Kontextnamens, der für den Befehl **Select-AzContext** erforderlich ist.

## <a name="retrieve-your-storage-account-details"></a>Abrufen der Details Ihres Speicherkontos

In den folgenden Schritten wird davon ausgegangen, dass Sie Ihr Speicherkonto mit dem **Ressourcen-Manager**-Bereitstellungsmodell und nicht mit dem **klassischen** Bereitstellungsmodell erstellt haben.

Sie benötigen die Verbindungszeichenfolge eines Azure-Speicherkontos, um das Hochladen von Dateien von Ihren Geräten zu konfigurieren. Das Speicherkonto muss sich in demselben Abonnement wie der IoT-Hub befinden. Sie benötigen auch den Name des Blobcontainers im Speicherkonto. Verwenden Sie den Befehl [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) zum Abrufen Ihrer Speicherkontoschlüssel:

```powershell
Get-AzStorageAccountKey `
  -Name {your storage account name} `
  -ResourceGroupName {your storage account resource group}
```

Notieren Sie den Wert **key1** des Speicherkontoschlüssels. Sie benötigen ihn in den folgenden Schritten.

Sie können entweder einen vorhandenen Blobcontainer für das Hochladen von Dateien verwenden oder einen neuen erstellen:

* Zum Auflisten der vorhandenen Blobcontainer in Ihrem Speicherkonto verwenden Sie die Befehle [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) und [Get-AzStorageContainer](/powershell/module/az.storage/get-azstoragecontainer):

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    Get-AzStorageContainer -Context $ctx
    ```

* Zum Erstellen eines Blobcontainers in Ihrem Speicherkonto verwenden Sie die Befehle [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) und [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer):

    ```powershell
    $ctx = New-AzStorageContext `
        -StorageAccountName {your storage account name} `
        -StorageAccountKey {your storage account key}
    New-AzStorageContainer `
        -Name {your new container name} `
        -Permission Off `
        -Context $ctx
    ```

## <a name="configure-your-iot-hub"></a>Konfigurieren Ihres IoT-Hubs

Sie können nun Ihren IoT Hub für das [Hochladen von Dateien auf den IoT Hub](iot-hub-devguide-file-upload.md) unter Verwendung Ihrer Speicherkontodetails konfigurieren.

Die Konfiguration erfordert die folgenden Werte:

* **Speichercontainer**: Ein Blobcontainer in einem Azure-Speicherkonto Ihres aktuellen Azure-Abonnements, der Ihrer IoT Hub-Instanz zugeordnet werden soll. Sie haben die erforderlichen Speicherkontoinformationen im vorherigen Abschnitt abgerufen. IoT Hub generiert automatisch SAS-URIs mit Schreibberechtigungen für diesen Blobcontainer, die Geräte beim Hochladen von Dateien verwenden können.

* **Benachrichtigungen für hochgeladene Dateien empfangen**: Aktivieren oder deaktivieren Sie Benachrichtigungen zum Hochladen von Dateien.

* **SAS-TTL**: Diese Einstellung dient zum Festlegen der Gültigkeitsdauer der SAS-URIs, die von IoT Hub an das Gerät zurückgegeben werden. Standardmäßig auf 1 Stunde festgelegt.

* **Standard-TTL für Dateibenachrichtigungseinstellungen**: Die Gültigkeitsdauer einer Dateiuploadbenachrichtigung (Zeit bis zum Ablauf). Standardmäßig auf 1 Tag festgelegt.

* **Anzahl maximaler Zustellungen für Dateibenachrichtigungen**: Gibt an, wie oft IoT Hub versucht, eine Dateiuploadbenachrichtigung zu senden. Standardmäßig auf 10 festgelegt.

Verwenden Sie den Befehl [Set-AzIotHub](/powershell/module/az.iothub/set-aziothub) zum Konfigurieren der Dateiuploadeinstellungen auf Ihrem IoT-Hub:

```powershell
Set-AzIotHub `
    -ResourceGroupName "{your iot hub resource group}" `
    -Name "{your iot hub name}" `
    -FileUploadNotificationTtl "01:00:00" `
    -FileUploadSasUriTtl "01:00:00" `
    -EnableFileUploadNotifications $true `
    -FileUploadStorageConnectionString "DefaultEndpointsProtocol=https;AccountName={your storage account name};AccountKey={your storage account key};EndpointSuffix=core.windows.net" `
    -FileUploadContainerName "{your blob container name}" `
    -FileUploadNotificationMaxDeliveryCount 10
```

> [!NOTE]
> Standardmäßig authentifiziert sich IoT Hub bei Azure Storage mithilfe des Kontoschlüssels in der Verbindungszeichenfolge. Die Authentifizierung mithilfe von system- oder benutzerseitig zugewiesenen verwalteten Identitäten steht ebenfalls zur Verfügung. Verwaltete Identitäten stellen Azure-Dienste mit einer automatisch verwalteten Identität in Azure AD auf sichere Weise bereit. Weitere Informationen finden Sie unter [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md). Zurzeit gibt es im Befehl **Set-AzIotHub** keine Parameter zum Festlegen des Authentifizierungstyps. Stattdessen können Sie das [Azure-Portal](./iot-hub-configure-file-upload.md) oder die [Azure CLI](./iot-hub-configure-file-upload-cli.md) nutzen. 

## <a name="next-steps"></a>Nächste Schritte

* [Hochladen von Dateien von einem Gerät – Übersicht](iot-hub-devguide-file-upload.md)
* [IoT Hub-Unterstützung für verwaltete Identitäten](./iot-hub-managed-identity.md)
* [Anleitungen zum Dateiupload](./iot-hub-csharp-csharp-file-upload.md)