---
title: Pullen von Einstellungen aus App Configuration mit Azure Pipelines
description: Erfahren Sie, wie Sie Schlüsselwerte mithilfe von Azure Pipelines aus einem App Configuration-Speicher pullen.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 2bee0e603d3cde38e56146900d682cc5e2b40bdc
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056471"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Pullen von Einstellungen aus App Configuration mit Azure Pipelines

Mit der Aufgabe [Azure App Configuration](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) werden Schlüsselwerte aus Ihrem App Configuration-Speicher gepullt und als Azure Pipeline-Variablen festgelegt, die von nachfolgenden Tasks verwendet werden können. Diese Aufgabe ergänzt die Aufgabe [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push), mit der Schlüsselwerte aus einer Konfigurationsdatei in Ihren App Configuration-Speicher gepusht werden. Weitere Informationen finden Sie unter [Pushen von Einstellungen an App Configuration mit Azure Pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- App Configuration-Speicher ([kostenlos über das Azure-Portal erstellen](https://portal.azure.com))
- Azure DevOps-Projekt ([kostenlos erstellen](https://go.microsoft.com/fwlink/?LinkId=2014881))
- Aufgabe „Azure App Configuration“ ([kostenlos aus dem Visual Studio Marketplace herunterladen](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.)) 
- [Knoten 10:](https://nodejs.org/en/blog/release/v10.21.0/) für Benutzer*innen, die die Aufgabe auf selbstgehosteten Agents ausführen. 

## <a name="create-a-service-connection"></a>Erstellen einer Dienstverbindung

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>Rollenzuweisung hinzufügen

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>Verwenden in Builds

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration“ in einer Azure DevOps-Buildpipeline verwenden.

1. Klicken Sie auf **Pipelines** > **Pipelines**, um zur Seite für Buildpipelines zu navigieren. Eine Dokumentation zur Buildpipeline finden Sie unter [Erstellen Ihrer ersten Pipeline](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Wenn Sie eine neue Buildpipeline erstellen, wählen Sie im letzten Schritt des Vorgangs auf der Registerkarte **Review** (Überprüfen) auf der rechten Seite der Pipeline **Show assistant** (Assistenten anzeigen) aus.
      ![Screenshot der Schaltfläche „Show assistant“ für eine neue Pipeline.](./media/new-pipeline-show-assistant.png)
      - Wenn Sie eine vorhandene Buildpipeline verwenden, klicken Sie oben rechts auf die Schaltfläche **Bearbeiten**.
      ![Screenshot der Schaltfläche „Bearbeiten“ für eine vorhandene Pipeline.](./media/existing-pipeline-show-assistant.png)
1. Suchen Sie nach der Aufgabe **Azure App Configuration**.
![Screenshot des Dialogfelds „Aufgabe hinzufügen“ mit „Azure App Configuration“ im Suchfeld.](./media/add-azure-app-configuration-task.png)
1. Konfigurieren Sie die erforderlichen Parameter für die Aufgabe, um die Schlüsselwerte aus dem App Configuration-Speicher zu pullen. Die Parameter werden weiter unten im Abschnitt **Parameter** sowie in QuickInfos neben dem jeweiligen Parameter beschrieben.
      - Legen Sie den Parameter **Azure subscription** (Azure-Abonnement) auf den Namen der Dienstverbindung fest, die Sie in einem vorherigen Schritt erstellt haben.
      - Legen Sie **App Configuration name** (App Configuration-Name) auf den Ressourcennamen Ihres App Configuration-Speichers fest.
      - Behalten Sie für die übrigen Parameter die Standardwerte bei.
![Screenshot der „Parameter“ für die Aufgabe „App Configuration“.](./media/azure-app-configuration-parameters.png)
1. Speichern Sie Ihre Angaben, und reihen Sie einen Build in die Warteschlange ein. Im Buildprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="use-in-releases"></a>Verwenden in Releases

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration“ in einer Azure DevOps-Releasepipeline verwenden.

1. Wählen Sie **Pipelines** > **Releases** aus, um zur Seite für Releasepipelines zu navigieren. Eine Dokumentation zu Releasepipelines finden Sie unter [Releasepipelines](/azure/devops/pipelines/release).
1. Wählen Sie eine vorhandene Releasepipeline aus. Sollten Sie nicht über eine verfügen, wählen Sie **Neue Pipeline** aus, um eine zu erstellen.
1. Wählen Sie rechts oben die Schaltfläche **Bearbeiten** aus, um die Releasepipeline zu bearbeiten.
1. Wählen Sie im Dropdown **Tasks** (Aufgaben) die **Stage** (Phase) aus, in der Sie die Aufgabe hinzufügen möchten. Weitere Informationen zu Phasen finden Sie [hier](/azure/devops/pipelines/release/environments).
![Screenshot der ausgewählten Phase im Dropdown „Tasks“.](./media/pipeline-stage-tasks.png)
1. Klicken Sie neben dem Auftrag, dem Sie eine neue Aufgabe hinzufügen möchten, auf **+** .
![Screenshot der Plus-Schaltfläche neben dem Auftrag.](./media/add-task-to-job.png)
1. Suchen Sie nach der Aufgabe **Azure App Configuration**.
![Screenshot des Dialogfelds „Aufgabe hinzufügen“ mit „Azure App Configuration“ im Suchfeld.](./media/add-azure-app-configuration-task.png)
1. Konfigurieren Sie die erforderlichen Parameter in der Aufgabe, um Ihre Schlüsselwerte aus Ihrem App Configuration-Speicher zu pullen. Die Parameter werden weiter unten im Abschnitt **Parameter** sowie in QuickInfos neben dem jeweiligen Parameter beschrieben.
      - Legen Sie den Parameter **Azure subscription** (Azure-Abonnement) auf den Namen der Dienstverbindung fest, die Sie in einem vorherigen Schritt erstellt haben.
      - Legen Sie **App Configuration name** (App Configuration-Name) auf den Ressourcennamen Ihres App Configuration-Speichers fest.
      - Behalten Sie für die übrigen Parameter die Standardwerte bei.
1. Speichern Sie Ihre Angaben, und reihen Sie ein Release in die Warteschlange ein. Im Releaseprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="parameters"></a>Parameter

Von der Aufgabe „Azure App Configuration“ werden folgende Parameter verwendet:

- **Azure-Abonnement**: Eine Dropdownliste mit Ihren verfügbaren Azure-Dienstverbindungen. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Azure-Dienstverbindungen rechts neben dem Textfeld auf die Schaltfläche **Refresh Azure subscription** (Azure-Abonnement aktualisieren).
- **App Configuration Name** (App Configuration-Name): Eine Dropdownliste mit Ihren verfügbaren Konfigurationsspeichern unter dem ausgewählten Abonnement. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Konfigurationsspeichern rechts neben dem Textfeld auf die Schaltfläche **Refresh App Configuration Name** (App Configuration-Name aktualisieren).
- **Key Filter** (Schlüsselfilter): Mit dem Filter können Sie auswählen, welche Schlüsselwerte von Azure App Configuration angefordert werden. Mit dem Wert „*“ werden alle Schlüsselwerte ausgewählt. Weitere Informationen finden Sie unter [Abfragen von Schlüsselwerten](concept-key-value.md#query-key-values).
- **Bezeichnung:** Gibt an, welche Bezeichnung beim Auswählen von Schlüsselwerten aus dem App Configuration-Speicher verwendet werden soll. Wenn keine Bezeichnung angegeben wird, werden Schlüsselwerte ohne eine Bezeichnung abgerufen. Die folgenden Zeichen sind nicht zulässig: „,“, „*“.
- **Trim Key Prefix** (Schlüsselpräfix kürzen): Geben Sie Präfixe an, die von App Configuration-Schlüsseln abgeschnitten werden sollen, bevor diese als Variablen festgelegt werden. Mehrere Präfixe können Sie durch einen Zeilenumbruch voneinander trennen.

## <a name="use-key-values-in-subsequent-tasks"></a>Verwenden von Schlüsselwerten in nachfolgenden Aufgaben

Die aus App Configuration abgerufenen Schlüsselwerte werden als Pipelinevariablen festgelegt, auf die als Umgebungsvariablen zugegriffen werden kann. Der Schlüssel einer solchen Umgebungsvariable ist der Schlüssel des Schlüssel-Wert-Paars, das aus App Configuration abgerufen wurde, nachdem das Präfix gekürzt wurde (sofern angegeben).

Wenn eine nachfolgende Aufgabe beispielsweise ein PowerShell-Skript ausführt, könnte sie einen Schlüsselwert mit dem Schlüssel „myBuildSetting“ wie folgt verwenden:
```powershell
echo "$env:myBuildSetting"
```
Der Wert wird an der Konsole ausgegeben.

> [!NOTE]
> Azure Key Vault-Verweise in der App-Konfiguration werden aufgelöst und als [geheime Variablen](/azure/devops/pipelines/process/variables#secret-variables) festgelegt. In Azure-Pipelines werden geheime Variablen aus dem Protokoll maskiert. Sie werden nicht als Umgebungsvariablen an Aufgaben übermittelt und müssen stattdessen als Eingaben übermittelt werden. 

## <a name="troubleshooting"></a>Problembehandlung

Sollte ein unerwarteter Fehler auftreten, können Sie Debugprotokolle aktivieren, indem Sie die Pipelinevariable `system.debug` auf `true` festlegen.

## <a name="faq"></a>Häufig gestellte Fragen

**Wie stelle ich meine Konfiguration aus mehreren Schlüsseln und Bezeichnungen zusammen?**

Es kann vorkommen, dass eine Konfiguration aus mehreren Bezeichnungen zusammengestellt werden muss, z. B. „default“ und „dev“. Sie können mehrere App Configuration-Aufgaben in einer Pipeline verwenden, um dieses Szenario umzusetzen. Schlüsselwerte, die von einer Aufgabe in einem späteren Schritt abgerufen werden, ersetzen alle Werte aus den vorherigen Schritten. Im obigen Beispiel können mit einer Aufgabe Schlüsselwerte mit der Bezeichnung „default“ ausgewählt werden, während mit einer zweiten Aufgabe Schlüsselwerte mit der Bezeichnung „dev“ ausgewählt werden. Die Schlüssel mit der Bezeichnung „dev“ überschreiben dieselben Schlüssel mit der Bezeichnung „default“.
