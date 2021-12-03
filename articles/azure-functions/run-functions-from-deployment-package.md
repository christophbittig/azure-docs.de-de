---
title: Ausführen von Azure Functions aus einem Paket
description: Lassen Sie die Azure Functions-Laufzeit Ihre Funktionen ausführen, indem Sie eine Bereitstellungspaketdatei einbinden, die Ihre Projektdateien für die Funktions-App enthält.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: c2c0797f256cfd302f4b8a5a80300ef4469355e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256987"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Ausführen Ihrer Azure Functions aus einem Paket

In Azure können Sie Ihre Functions direkt aus der Bereitstellungspaketdatei in Ihrer Funktions-App ausführen. Die andere Option ist die Bereitstellung Ihrer Dateien im Verzeichnis `d:\home\site\wwwroot` Ihrer Funktions-App.

Dieser Artikel beschreibt die Vorteile einer Ausführung Ihrer Functions aus einem Paket. Außerdem wir erläutert, wie Sie diese Funktionalität in Ihrer Funktions-App aktivieren.

## <a name="benefits-of-running-from-a-package-file"></a>Vorteile der Ausführung aus einer Paketdatei
  
Es gibt mehrere Vorteile bei der Ausführung aus einer Paketdatei:

+ Reduziert das Risiko von Sperrungen beim Kopieren von Dateien.
+ Kann in einer Produktions-App (mit Neustart) bereitgestellt werden.
+ Sie können sich sicher sein, dass die Dateien, die in Ihrer App ausgeführt werden, sicher sind.
+ Verbessert die Leistung von [Azure Resource Manager-Bereitstellungen](functions-infrastructure-as-code.md).
+ Kann Kaltstartzeiten verringern, insbesondere für JavaScript-Funktionen mit großen npm-Paketstrukturen.

Weitere Informationen dazu finden Sie in [dieser Ankündigung](https://github.com/Azure/app-service-announcements/issues/84).

## <a name="enabling-functions-to-run-from-a-package"></a>Aktivieren von Funktionen zum Ausführen aus einem Paket

Damit Ihre Funktions-App aus einem Paket ausgeführt werden kann, müssen Sie nur eine Einstellung `WEBSITE_RUN_FROM_PACKAGE` zu Ihren Einstellungen in der Funktions-App hinzufügen. Die Einstellung `WEBSITE_RUN_FROM_PACKAGE` kann einen der folgenden Werte aufweisen:

| Wert  | BESCHREIBUNG  |
|---------|---------|
| **`1`**  | Für unter Windows ausgeführte Funktions-Apps empfohlen. Ausführen aus einer Paketdatei im Ordner `d:\home\data\SitePackages` der Funktions-App. Wenn Sie nicht [mit ZIP Deploy bereitstellen](#integration-with-zip-deployment), muss der Ordner für diese Option außerdem eine Datei namens `packagename.txt` enthalten. Diese Datei enthält nur den Namen der Paketdatei im Ordner ohne Leerzeichen. |
|**`<URL>`**  | Speicherort der spezifischen Paketdatei, die Sie ausführen möchten. Wenn Sie eine URL angeben, müssen Sie nach dem Veröffentlichen eines aktualisierten Pakets auch [Trigger synchronisieren](functions-deployment-technologies.md#trigger-syncing). <br/>Bei Verwendung von Blob Storage sollten Sie in der Regel kein öffentliches Blob verwenden. Nutzen Sie stattdessen einen privaten Container mit einer [Shared Access Signature (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) oder eine [verwaltete Identität](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity), um der Functions-Runtime den Zugriff auf das Paket zu gewähren. Sie können den [Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) zum Hochladen von Dateien in Ihr Blob Storage-Konto verwenden. |

> [!CAUTION]
> Bei der Ausführung einer Funktions-App unter Windows ergibt die Option mit externer URL ein schlechteres Startverhalten. Beim Bereitstellen Ihrer Funktions-App unter Windows sollten Sie `WEBSITE_RUN_FROM_PACKAGE` auf `1` festlegen und per ZIP-Bereitstellung veröffentlichen.

Das folgende Beispiel zeigt eine Funktions-App, die so konfiguriert ist, dass Sie über eine ZIP-Datei ausgeführt wird, die im Azure Blob Storage gehostet wird:

![WEBSITE_RUN_FROM_ZIP-Appeinstellung](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Derzeit werden nur ZIP-Paketdateien unterstützt.

### <a name="fetch-a-package-from-azure-blob-storage-using-a-managed-identity"></a>Abrufen eines Pakets aus Azure Blob Storage mithilfe einer verwalteten Identität

[!INCLUDE [Run from package via Identity](../../includes/app-service-run-from-package-via-identity.md)]

## <a name="integration-with-zip-deployment"></a>Integration mit einer ZIP-Bereitstellung

[ZIP-Bereitstellung][Zip deployment for Azure Functions] ist eine Funktion von Azure App Service, mit der Sie Ihr Funktions-App-Projekt im Verzeichnis `wwwroot` bereitstellen können. Das Projekt ist als ZIP-Bereitstellungsdatei verpackt. Mit derselben API können Sie Ihr Paket im Ordner `d:\home\data\SitePackages` bereitstellen. Mit dem `WEBSITE_RUN_FROM_PACKAGE`-App-Einstellungswert von `1` kopieren die ZIP-Bereitstellungs-APIs Ihr Paket in den Ordner `d:\home\data\SitePackages`, anstatt die Dateien nach `d:\home\site\wwwroot` zu extrahieren. Außerdem wird die Datei `packagename.txt` erstellt. Nach einem Neustart wird das Paket in `wwwroot` als schreibgeschütztes Dateisystem bereitgestellt. Weitere Informationen zur ZIP-Bereitstellung finden Sie unter [ZIP-Bereitstellung für Azure Functions](deployment-zip-push.md).

> [!NOTE]
> Wenn eine Bereitstellung erfolgt, wird ein Neustart der Funktions-App ausgelöst. Vor einem Neustart können alle bestehenden Funktionsausführungen abgeschlossen oder ein Timeout erreicht werden. Weitere Informationen finden Sie unter [Bereitstellungsverhalten](functions-deployment-technologies.md#deployment-behaviors).

## <a name="adding-the-website_run_from_package-setting"></a>Hinzufügen der Einstellung WEBSITE_RUN_FROM_PACKAGE

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="example-workflow-for-manually-uploading-a-package-hosted-in-azure-storage"></a>Beispielworkflow für das manuelle Hochladen eines in Azure Storage gehosteten Pakets

Um bei Verwendung der URL-Option ein ZIP-Paket bereitzustellen, müssen Sie ein ZIP-komprimiertes Bereitstellungspaket erstellen und in das Ziel hochladen. In diesem Beispiel wird ein Blob Storage-Container verwendet. 

1. Erstellen Sie mithilfe des Hilfsprogramms Ihrer Wahl ein ZIP-Paket für Ihr Projekt.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrem Speicherkontonamen, oder durchsuchen Sie Ihre Speicherkonten nach diesem Namen.
 
1. Wählen Sie im Speicherkonto unter **Datenspeicherung** die Option **Container** aus.

1. Wählen Sie **+ Container** aus, um einen neuen Blob Storage-Container in Ihrem Konto zu erstellen.

1. Geben Sie auf der Seite **Neuer Container** einen **Namen** ein (z. B. „deployments“), stellen Sie sicher, dass die **Öffentliche Zugriffsebene** **Privat** ist, und wählen Sie dann **Erstellen** aus.

1. Wählen Sie den erstellten Container aus, wählen Sie **Hochladen** aus, navigieren Sie zum Speicherort der ZIP-Datei, die Sie mit Ihrem Projekt erstellt haben, und wählen Sie **Hochladen** aus.

1. Wählen Sie nach Abschluss des Uploads Ihre hochgeladene Blobdatei aus, und kopieren Sie die URL. Möglicherweise müssen Sie eine SAS-URL generieren, wenn Sie keine [Identität verwenden](#fetch-a-package-from-azure-blob-storage-using-a-managed-identity).

1. Suchen Sie nach Ihrer Funktions-App, oder durchsuchen Sie die Seite **Funktions-App** nach der App. 

1. Wählen Sie unter **Einstellungen** für ihre Funktions-App die Option **Konfigurationen** aus.

1. Wählen Sie auf der Registerkarte **Anwendungseinstellungen** die Option **Neue Anwendungseinstellung** aus.

1. Geben Sie den Wert `WEBSITE_RUN_FROM_PACKAGE` als **Namen** ein, und fügen Sie die URL Ihres Pakets in Blob Storage als **Wert** ein.

1. Wählen Sie **OK** aus. Wählen Sie dann **Speichern** > **Weiter** aus, um die Einstellung zu speichern und die App neu zu starten.

Nun können Sie Ihre Funktion in Azure ausführen, um zu überprüfen, ob die Bereitstellung erfolgreich war, indem Sie die ZIP-Datei des Bereitstellungspakets verwenden.

## <a name="troubleshooting"></a>Problembehandlung

- Durch Ausführen aus Paket wird `wwwroot` schreibgeschützt, sodass Sie einen Fehler erhalten, wenn Sie Dateien in dieses Verzeichnis schreiben möchten.
- Das TAR- und das GZIP-Format werden nicht unterstützt.
- Die ZIP-Datei kann höchstens 1GB groß sein.
- Diese Funktionalität funktioniert nicht mit lokalem Cache.
- Um die Kaltstartleistung zu verbessern, verwenden Sie die lokale ZIP-Option (`WEBSITE_RUN_FROM_PACKAGE`=1).
- Die Option „Aus Paket ausführen“ ist nicht mit der Anpassungsoption für die Bereitstellung (`SCM_DO_BUILD_DURING_DEPLOYMENT=true`) kompatibel. Der Erstellungsschritt wird während der Bereitstellung ignoriert.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Kontinuierliche Bereitstellung für Azure Functions](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
