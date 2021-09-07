---
title: Erstellen einer Azure-Funktions-App mit Java und Eclipse
description: Anleitung zum Erstellen und Veröffentlichen einer einfachen über HTTP ausgelösten und serverlosen App für Azure Functions mithilfe von Java und Eclipse.
author: ggailey777
ms.topic: how-to
ms.date: 07/01/2018
ms.author: glenga
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 3720ac243fd90be23c18f760977dc2a0d13e0412
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/28/2021
ms.locfileid: "112989109"
---
# <a name="create-your-first-function-with-java-and-eclipse"></a>Erstellen der ersten Funktion mit Java und Eclipse 

In diesem Artikel erfahren Sie, wie Sie ein [serverloses](https://azure.microsoft.com/solutions/serverless/) Functions-Projekt mit der Eclipse-IDE und Apache Maven erstellen, testen und debuggen und dann für Azure Functions bereitstellen. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Wenn Sie eine Funktions-App mit Java und Eclipse entwickeln möchten, muss Folgendes installiert sein:

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/), Version 8.
-  [Apache Maven](https://maven.apache.org), Version 3.0 oder höher
-  [Eclipse](https://www.eclipse.org/downloads/packages/) mit Java- und Maven- Unterstützung
-  [Azure-Befehlszeilenschnittstelle](/cli/azure)

> [!IMPORTANT] 
> Damit Sie diesen Schnellstart durchführen können, muss die Umgebungsvariable JAVA_HOME auf den Installationsspeicherort des JDK festgelegt sein.

Es wird dringend empfohlen, auch [Version 2 der Azure Functions Core Tools](functions-run-local.md#v2) zu installieren. Diese Tools bieten eine lokale Umgebung zum Ausführen und Debuggen von Azure Functions. 

## <a name="create-a-functions-project"></a>Erstellen eines Functions-Projekts

1. Wählen Sie in Eclipse das Menü **File** (Datei) und dann **New -&gt; Maven Project** (Neu -> Maven-Projekt) aus. 
1. Übernehmen Sie die Standardeinstellungen im Dialogfeld **New Maven Project** (Neues Maven-Projekt), und wählen Sie **Next** (Weiter) aus.
1. Suchen Sie nach [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype), wählen Sie das Element aus, und klicken Sie auf **Next** (Weiter).
1. Sie müssen Werte für alle Felder eingeben, einschließlich `resourceGroup`, `appName` und `appRegion` (geben Sie einen anderen appName als **fabrikam-function-20170920120101928** an), und wählen Sie abschließend **Fertig stellen** aus.
    ![Erstellung eines Maven-Projekts in Eclipse 2](media/functions-create-first-java-eclipse/functions-create-eclipse2.png)  

Maven erstellt die Projektdateien in einem neuen Ordner mit dem Namen _artifactId_. Bei dem generierten Code im Projekt handelt es sich um eine einfache [durch HTTP ausgelöste](./functions-bindings-http-webhook.md) Funktion, die den Körper der über HTTP ausgelösten Anforderung wiederholt.

## <a name="run-functions-locally-in-the-ide"></a>Lokales Ausführen von Funktionen in der IDE

> [!NOTE]
> [Version 2 der Azure Functions Core Tools](functions-run-local.md#v2) muss installiert sein, damit Funktionen lokal ausgeführt und gedebuggt werden können.

1. Klicken Sie mit der rechten Maustaste auf das generierte Projekt und wählen Sie **Run As** (Ausführen als) und **Maven build** (Maven-Build) aus.
1. Geben Sie im Dialogfeld **Edit Configuration** (Konfiguration bearbeiten) den Text `package` in das Feld **Goals** (Ziele) ein, und wählen Sie anschließend **Run** (Ausführen) aus. Daraufhin wird der Funktionscode erstellt und verpackt.
1. Sobald der Erstellungsvorgang abgeschlossen ist, erstellen Sie eine weitere Laufzeitkonfiguration wie oben beschrieben. Verwenden Sie dabei `azure-functions:run` als Ziel und Name. Wählen Sie **Run** (Ausführen) aus, um die Funktion in der IDE auszuführen.

Beenden Sie die Laufzeit im Konsolenfenster, wenn Sie Ihre Funktion getestet haben. Es kann nicht mehr als ein Funktionshost gleichzeitig aktiv sein und lokal ausgeführt werden.

### <a name="debug-the-function-in-eclipse"></a>Debuggen der Funktion in Eclipse

Ändern Sie in Ihrer im vorherigen Schritt eingerichteten **Run As**-Konfiguration (Ausführen als) `azure-functions:run` zu `azure-functions:run -DenableDebug`, und führen Sie die aktualisierte Konfiguration aus, um die Funktions-App im Debugmodus zu starten.

Wählen Sie das Menü **Run** (Ausführen) aus, und öffnen Sie **Debug Configurations** (Konfigurationen debuggen). Wählen Sie **Remote Java Application** (Remote-Java-Anwendung) aus, und erstellen Sie eine neue. Benennen Sie Ihre Konfiguration, und geben Sie die Einstellungen ein. Der Port sollte mit dem Debugport konsistent sein, der vom Funktionshost geöffnet wird (standardmäßig `5005`). Klicken Sie nach dem Setup auf `Debug`, um das Debuggen zu starten.

![Debuggen von Funktionen in Eclipse](media/functions-create-first-java-eclipse/debug-configuration-eclipse.PNG)

Legen Sie Breakpoints fest, und überprüfen Sie Objekte in Ihrer Funktion mit der IDE. Wenn Sie fertig sind, beenden Sie den Debugger und den laufenden Funktionshost. Es kann nicht mehr als ein Funktionshost gleichzeitig aktiv sein und lokal ausgeführt werden.

## <a name="deploy-the-function-to-azure"></a>Bereitstellen der Funktion in Azure

Bei dem Bereitstellungsprozess in Azure Functions werden die Anmeldeinformationen aus der Azure CLI verwendet. [Melden Sie sich mit der Azure CLI an](/cli/azure/authenticate-azure-cli), bevor Sie die Eingabeaufforderung Ihres Computers weiterverwenden.

```azurecli
az login
```

Stellen Sie Ihren Code mit `azure-functions:deploy` als Maven-Ziel in einer neuen **Run As**-Konfiguration (Ausführen als) in einer neuen Funktions-App bereit.

Wenn die Bereitstellung abgeschlossen ist, wird die URL angezeigt, mit der Sie auf Ihre Azure-Funktionen-App zugreifen können:

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Entwickeln von Java-Funktionen finden Sie im [Azure Functions-Java-Entwicklerhandbuch](functions-reference-java.md).
- Fügen Sie mit `azure-functions:add` als Maven-Ziel zusätzliche Funktionen mit verschiedenen Triggern zu Ihrem Projekt hinzu.
