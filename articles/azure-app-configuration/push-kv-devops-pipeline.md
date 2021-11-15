---
title: Pushen von Einstellungen an App Configuration mit Azure Pipelines
description: Hier erfahren Sie, wie Sie Schlüsselwerte mithilfe von Azure Pipelines in einen App Configuration-Speicher pushen.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 02/23/2021
ms.author: alkemper
ms.openlocfilehash: 2f5ac3579489637e5adcdfaa40fac90b5ff6e8b4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427150"
---
# <a name="push-settings-to-app-configuration-with-azure-pipelines"></a>Pushen von Einstellungen an App Configuration mit Azure Pipelines

Mit der Aufgabe [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) werden Schlüsselwerte aus einer Konfigurationsdatei in Ihren App Configuration-Speicher gepusht. Diese Aufgabe bietet umfassende Funktionen für die Pipeline, da Sie nun Einstellungen aus dem App Configuration-Speicher pullen sowie Einstellungen in den App Configuration-Speicher pushen können.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- App Configuration-Ressource ([kostenlos über das Azure-Portal erstellen](https://portal.azure.com))
- Azure DevOps-Projekt ([kostenlos erstellen](https://go.microsoft.com/fwlink/?LinkId=2014881))
- Aufgabe „Azure App Configuration Push“ ([kostenlos aus dem Visual Studio Marketplace herunterladen](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push))
- [Knoten 10:](https://nodejs.org/en/blog/release/v10.21.0/) für Benutzer*innen, die die Aufgabe auf selbstgehosteten Agents ausführen.

## <a name="create-a-service-connection"></a>Erstellen einer Dienstverbindung

[!INCLUDE [azure-app-configuration-service-connection](../../includes/azure-app-configuration-service-connection.md)]

## <a name="add-role-assignment"></a>Rollenzuweisung hinzufügen

[!INCLUDE [azure-app-configuration-role-assignment](../../includes/azure-app-configuration-role-assignment.md)]

## <a name="use-in-builds"></a>Verwenden in Builds

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration Push“ in einer Azure DevOps-Buildpipeline verwenden.

1. Klicken Sie auf **Pipelines** > **Pipelines**, um zur Seite für Buildpipelines zu navigieren. Die Dokumentation zu Buildpipelines finden Sie [hier](/azure/devops/pipelines/create-first-pipeline?tabs=tfs-2018-2).
      - Wenn Sie eine neue Buildpipeline erstellen, wählen Sie im letzten Schritt des Vorgangs auf der Registerkarte **Review** (Überprüfen) auf der rechten Seite der Pipeline **Show assistant** (Assistenten anzeigen) aus.
      ![Screenshot der Schaltfläche „Show assistant“ für eine neue Pipeline.](./media/new-pipeline-show-assistant.png)
      - Wenn Sie eine vorhandene Buildpipeline verwenden, klicken Sie oben rechts auf die Schaltfläche **Bearbeiten**.
      ![Screenshot der Schaltfläche „Bearbeiten“ für eine vorhandene Pipeline.](./media/existing-pipeline-show-assistant.png)
1. Suchen Sie nach der Aufgabe **Azure App Configuration Push**.
![Screenshot des Dialogfelds „Aufgabe hinzufügen“ mit „Azure App Configuration Push“ im Suchfeld.](./media/add-azure-app-configuration-push-task.png)
1. Konfigurieren Sie die erforderlichen Parameter für die Aufgabe, um die Schlüsselwerte aus der Konfigurationsdatei in den App Configuration-Speicher zu pushen. Die Parameter werden weiter unten im Abschnitt **Parameter** sowie in QuickInfos neben dem jeweiligen Parameter erläutert.
![Screenshot der „Parameter“ für die Aufgabe „App Configuration Push“.](./media/azure-app-configuration-push-parameters.png)
1. Speichern Sie Ihre Angaben, und reihen Sie einen Build in die Warteschlange ein. Im Buildprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="use-in-releases"></a>Verwenden in Releases

In diesem Abschnitt erfahren Sie, wie Sie die Aufgabe „Azure App Configuration Push“ in Azure DevOps-Releasepipelines verwenden.

1. Wählen Sie **Pipelines** > **Releases** aus, um zur Seite für Releasepipelines zu navigieren. Die Dokumentation zu Releasepipelines finden Sie [hier](/azure/devops/pipelines/release).
1. Wählen Sie eine vorhandene Releasepipeline aus. Sollten Sie über keine verfügen, wählen Sie **+ Neu** aus, um eine zu erstellen.
1. Wählen Sie rechts oben die Schaltfläche **Bearbeiten** aus, um die Releasepipeline zu bearbeiten.
1. Wählen Sie im Dropdown **Tasks** (Aufgaben) die **Stage** (Phase) aus, in der Sie die Aufgabe hinzufügen möchten. Weitere Informationen zu Phasen finden Sie [hier](/azure/devops/pipelines/release/environments).
![Screenshot der ausgewählten Phase im Dropdown „Tasks“.](./media/pipeline-stage-tasks.png)
1. Klicken Sie neben dem Auftrag, dem Sie eine neue Aufgabe hinzufügen möchten, auf **+** .
![Screenshot der Plus-Schaltfläche neben dem Auftrag.](./media/add-task-to-job.png)
1. Geben Sie im Dialogfeld **Add tasks** (Aufgaben hinzufügen) die Zeichenfolge **Azure App Configuration Push** in das Suchfeld ein, und wählen Sie es aus.
1. Konfigurieren Sie die erforderlichen Parameter in der Aufgabe, um Ihre Schlüsselwerte aus Ihrer Konfigurationsdatei in Ihren App Configuration-Speicher zu pushen. Die Parameter werden weiter unten im Abschnitt **Parameter** sowie in QuickInfos neben dem jeweiligen Parameter erläutert.
1. Speichern Sie Ihre Angaben, und reihen Sie ein Release in die Warteschlange ein. Im Releaseprotokoll werden alle Fehler angezeigt, die ggf. bei der Aufgabenausführung aufgetreten sind.

## <a name="parameters"></a>Parameter

Von der Aufgabe „Azure App Configuration Push“ werden folgende Parameter verwendet:

- **Azure-Abonnement**: Eine Dropdownliste mit Ihren verfügbaren Azure-Dienstverbindungen. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Azure-Dienstverbindungen rechts neben dem Textfeld auf die Schaltfläche **Refresh Azure subscription** (Azure-Abonnement aktualisieren).
- **App Configuration Name** (App Configuration-Name): Eine Dropdownliste mit Ihren verfügbaren Konfigurationsspeichern unter dem ausgewählten Abonnement. Klicken Sie zum Aktualisieren der Liste mit den verfügbaren Konfigurationsspeichern rechts neben dem Textfeld auf die Schaltfläche **Refresh App Configuration Name** (App Configuration-Name aktualisieren).
- **Konfigurationsdateipfad**: Der Pfad zu Ihrer Konfigurationsdatei. Der Parameter **Konfigurationsdateipfad** beginnt am Stamm des Dateirepositorys. Sie können Ihr Buildartefakt durchsuchen, um eine Konfigurationsdatei auszuwählen. (Verwenden Sie hierzu die Schaltfläche `...` rechts neben dem Textfeld.) Die unterstützten Dateiformate sind: YAML, JSON und Eigenschaften. Im Folgenden sehen Sie eine Beispielkonfigurationsdatei im JSON-Format.
    ```json
    {
        "TestApp:Settings:BackgroundColor":"#FFF",
        "TestApp:Settings:FontColor":"#000",
        "TestApp:Settings:FontSize":"24",
        "TestApp:Settings:Message": "Message data"
    }
    ```
- **Trennzeichen**: Das Trennzeichen zum Vereinfachen von JSON- und YML-Dateien.
- **Tiefe**: Die Tiefe für die Vereinfachung der JSON- und YML-Dateien.
- **Präfix:** Eine Zeichenfolge, die jedem Schlüssel vorangestellt wird, der in den App Configuration-Speicher gepusht wird.
- **Bezeichnung:** Eine Zeichenfolge, die jedem Schlüsselwert als Bezeichnung im App Configuration-Speicher hinzugefügt wird.
- **Inhaltstyp**: Eine Zeichenfolge, die jedem Schlüsselwert als Inhaltstyp im App Configuration-Speicher hinzugefügt wird.
- **Tags**: Ein JSON-Objekt im Format `{"tag1":"val1", "tag2":"val2"}` zum Definieren von Tags, die den einzelnen Schlüsselwerten hinzugefügt werden, die in Ihren App Configuration-Speicher gepusht werden.
- **Delete all other Key-Values in store with the specified prefix and label** (Alle anderen Schlüsselwerte im Speicher mit dem angegebenen Präfix und der angegebenen Bezeichnung löschen): Standardmäßig **nicht aktiviert**.
  - **Aktiviert**: Alle Schlüsselwerte, die sowohl dem angegebenen Präfix als auch der angegebenen Bezeichnung entsprechen, werden aus dem App Configuration-Speicher gelöscht, bevor neue Schlüsselwerte aus der Konfigurationsdatei gepusht werden.
  - **Nicht aktiviert**: Alle Schlüsselwerte werden aus der Konfigurationsdatei in den App Configuration-Speicher gepusht, und alles andere im App Configuration-Speicher bleibt erhalten.



## <a name="troubleshooting"></a>Problembehandlung

Sollte ein unerwarteter Fehler auftreten, können Sie Debugprotokolle aktivieren, indem Sie die Pipelinevariable `system.debug` auf `true` festlegen.

## <a name="faq"></a>Häufig gestellte Fragen

**Wie kann ich mehrere Konfigurationsdateien hochladen?**

Erstellen Sie innerhalb der gleichen Pipeline mehrere Instanzen der Aufgabe „Azure App Configuration Push“, um mehrere Konfigurationsdateien in den App Configuration-Speicher zu pushen.

**Wie kann ich mithilfe dieser Aufgabe Key Vault-Verweise erstellen?**

Legen Sie zum Erstellen von Key Vault-Verweisen den Parameter „Inhaltstyp“ auf *application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8* fest. Wenn es sich nicht bei allen Schlüsselwerten in einer Konfigurationsdatei um Key Vault-Verweise handelt, platzieren Sie Key Vault-Verweise und normale Schlüsselwerte in separaten Konfigurationsdateien, und pushen Sie sie separat.

**Warum erhalte ich einen Fehler vom Typ 409, wenn ich versuche, Schlüsselwerte in meinen Konfigurationsspeicher zu pushen?**

Eine Fehlermeldung vom Typ „409 – Konflikt“ wird ausgegeben, wenn die Aufgabe versucht, einen Schlüsselwert zu entfernen oder zu überschreiben, der im App Configuration-Speicher gesperrt ist.
