---
title: 'Schnellstart: Erstellen und Verwalten von Ressourcen in Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Ihre erste Azure Communication Services-Ressource erstellen und verwalten.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-azcli-net-ps
ms.openlocfilehash: 4516e753248b133ef0a48ab12ffe032ef7a7809e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253574"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Schnellstart: Erstellen und Verwalten einer Communication Services-Ressource

Führen Sie erste Schritte mit Azure Communication Services aus, indem Sie Ihre erste Communication Services-Ressource bereitstellen. Communication Services-Ressourcen können über das [Azure-Portal](https://portal.azure.com) oder mit dem .NET Management SDK bereitgestellt werden. Mithilfe des Management SDK und des Azure-Portals können Sie Ihre Ressourcen und Schnittstellen mit [Azure Resource Manager](../../azure-resource-manager/management/overview.md), dem Bereitstellungs- und Verwaltungsdienst von Azure, erstellen, konfigurieren, aktualisieren und löschen. Alle in den SDKs verfügbaren Funktionen sind auch im Azure-Portal verfügbar. 


> [!WARNING]
> Beachten Sie Folgendes: Obwohl Communication Services in mehreren geografischen Regionen verfügbar sind, muss für die Ressource ein Datenspeicherort auf „US“ festgelegt sein, um eine Telefonnummer zu erhalten. Beachten Sie ferner, dass es nicht möglich ist, eine Ressourcengruppe zugleich mit einer Ressource für Azure Communication Services zu erstellen. Beim Erstellen einer Ressource muss eine bereits erstellte Ressourcengruppe verwendet werden.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

::: zone pivot="platform-powershell"
[!INCLUDE [PowerShell](./includes/create-resource-powershell.md)]
::: zone-end


## <a name="access-your-connection-strings-and-service-endpoints"></a>Zugreifen auf die Verbindungszeichenfolgen und Dienstendpunkte

Mit Verbindungszeichenfolgen können Communication Services-SDKs eine Verbindung mit Azure herstellen und sich bei Azure authentifizieren. Sie können auf die Communication Services-Verbindungszeichenfolgen und -Dienstendpunkte über das Azure-Portal oder programmgesteuert mit Azure Resource Manager-APIs zugreifen.

Navigieren Sie zur Communication Services-Ressource, wählen Sie im Navigationsmenü **Schlüssel** aus, und kopieren Sie die Werte **Verbindungszeichenfolge** oder **Endpunkt** zur Verwendung durch die Communication Services-SDKs. Beachten Sie, dass Sie Zugriff auf Primär- und Sekundärschlüssel haben. Dies kann in Szenarien nützlich sein, in denen Sie für einen Drittanbieter oder eine Stagingumgebung temporären Zugriff auf Ihre Communication Services-Ressourcen bereitstellen möchten.

:::image type="content" source="./media/key.png" alt-text="Screenshot: Communication Services-Seite „Schlüssel“":::

Auf Schlüsselinformationen (etwa die Ressourcengruppe oder die Schlüssel für eine bestimmte Ressource) kann auch über die Azure CLI zugegriffen werden: 

Installieren Sie die [Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli), und verwenden Sie den folgenden Befehl für die Anmeldung. Sie müssen Ihre Anmeldeinformationen eingeben, um eine Verbindung mit Ihrem Azure-Konto herzustellen.
```azurecli
az login
```

Nun können Sie auf wichtige Informationen zu Ihren Ressourcen zugreifen.
```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

Wenn Sie ein bestimmtes Abonnement auswählen möchten, können Sie auch das Flag ```--subscription``` und die Abonnement-ID angeben.
```
az communication list --resource-group  "resourceGroup>"  --subscription "<subscriptionID>"

az communication list-key --name "<communicationName>" --resource-group "resourceGroup>" --subscription "<subscriptionID>"
```

## <a name="store-your-connection-string"></a>Speichern der Verbindungszeichenfolge

Communication Services-SDKs verwenden Verbindungszeichenfolgen, um an Communication Services gesendete Anforderungen zu autorisieren. Es gibt mehrere Möglichkeiten, die Verbindungszeichenfolge zu speichern:

* Eine Anwendung, die auf dem Desktop oder auf einem Gerät ausgeführt wird, kann die Verbindungszeichenfolge in der Datei **app.config** oder **web.config** speichern. Fügen Sie die Verbindungszeichenfolge dem Abschnitt **AppSettings** in diesen Dateien hinzu.
* Eine Anwendung, die in einer Azure App Service-Instanz ausgeführt wird, kann die Verbindungszeichenfolge in den [App Service-Anwendungseinstellungen](../../app-service/configure-common.md) speichern. Fügen Sie die Verbindungszeichenfolge dem Abschnitt **Verbindungszeichenfolgen** auf der Registerkarte „Anwendungseinstellungen“ im Portal hinzu.
* Sie können die Verbindungszeichenfolge in [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md) speichern.
* Wenn Sie Ihre Anwendung lokal ausführen, können Sie die Verbindungszeichenfolge in einer Umgebungsvariablen speichern.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Speichern der Verbindungszeichenfolge in einer Umgebungsvariablen

Öffnen Sie zum Konfigurieren einer Umgebungsvariablen ein Konsolenfenster, und wählen Sie über die folgenden Registerkarten Ihr Betriebssystem aus. Ersetzen Sie `<yourconnectionstring>` durch Ihre Verbindungszeichenfolge.

#### <a name="windows"></a>[Windows](#tab/windows)

Öffnen Sie ein Konsolenfenster, und geben Sie den folgenden Befehl ein:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Nachdem Sie die Umgebungsvariable hinzugefügt haben, müssen Sie unter Umständen alle ausgeführten Programme neu starten, von denen die Umgebungsvariable gelesen werden muss, z.B. das Konsolenfenster. Wenn Sie beispielsweise Visual Studio als Editor verwenden, müssen Sie Visual Studio neu starten, bevor Sie das Beispiel ausführen.

#### <a name="macos"></a>[macOS](#tab/unix)

Bearbeiten Sie die Datei vom Typ **.zshrc**, und fügen Sie die Umgebungsvariable hinzu:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.zshrc` aus, damit die Änderungen wirksam werden. Wenn Sie die Umgebungsvariable bei geöffneter IDE erstellt haben, muss der Editor, die IDE oder die Shell unter Umständen geschlossen und erneut geöffnet werden, um auf die Variable zuzugreifen.

#### <a name="linux"></a>[Linux](#tab/linux)

Bearbeiten Sie **.bash_profile**, und fügen Sie die Umgebungsvariable hinzu:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Führen Sie nach dem Hinzufügen der Umgebungsvariablen im Konsolenfenster `source ~/.bash_profile` aus, damit die Änderungen wirksam werden. Wenn Sie die Umgebungsvariable bei geöffneter IDE erstellt haben, muss der Editor, die IDE oder die Shell unter Umständen geschlossen und erneut geöffnet werden, um auf die Variable zuzugreifen.

---

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. 

Wenn bei der Ressourcenlöschung der Ressource Telefonnummern zugewiesen waren, werden die Telefonnummern automatisch zum gleichen Zeitpunkt aus der Ressource freigegeben. 

> [!Note]
> Das Löschen von Ressourcen ist **endgültig**, und nach dem Löschen können keine Daten wiederhergestellt werden, auch nicht Event Grid-Filter, Telefonnummern oder andere Daten, die an Ihre Ressource gebunden sind.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer Communication Services-Ressource
> * Konfigurieren von Ressourcengeografie und -tags
> * Zugreifen auf die Schlüssel für diese Ressource
> * Löschen der Ressource

> [!div class="nextstepaction"]
> [Erstellen Ihrer ersten Benutzerzugriffstokens](access-tokens.md)
