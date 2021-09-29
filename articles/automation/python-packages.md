---
title: Verwalten von Python 2-Paketen in Azure Automation
description: In diesem Artikel wird erläutert, wie Sie Python 2-Pakete in Azure Automation verwalten.
services: automation
ms.subservice: process-automation
ms.date: 08/13/2021
ms.topic: conceptual
ms.custom: devx-track-python
ms.openlocfilehash: 916cd5fa9251a1f6c14b4518db7b77398c7ab5e0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124799983"
---
# <a name="manage-python-2-packages-in-azure-automation"></a>Verwalten von Python 2-Paketen in Azure Automation

Mit Azure Automation können Sie Python 2-Runbooks in Azure und Hybrid Runbook Worker unter Linux ausführen. Um die Runbooks zu vereinfachen, können Sie mithilfe von Python-Paketen die erforderlichen Module importieren. Dieser Artikel beschreibt, wie Sie Python-Pakete in Azure Automation verwalten und verwenden.

## <a name="import-packages"></a>Importieren von Paketen

Wählen Sie in Ihrem Automation-Konto unter **Python 2-Pakete** die Option **Freigegebene Ressourcen** aus. Klicken Sie auf **Python 2-Paket hinzufügen**.

:::image type="content" source="media/python-packages/add-python-package.png" alt-text="Screenshot mit der Seite „Python 2-Pakete“ mit Python 2-Paketen im linken Menü und Hervorhebung von „Python 2-Paket hinzufügen“.":::

Wählen Sie auf der Seite „Python 2-Paket hinzufügen“ ein lokales Paket für den Upload aus. Das Paket kann eine Datei im Format **.whl** oder **.tar.gz** sein. Klicken Sie nach der Auswahl des Pakets auf **OK**, um es hochzuladen.

:::image type="content" source="media/python-packages/upload-package.png" alt-text="Screenshot der Seite „Python 2-Paket hinzufügen“ mit Auswahl der hochgeladenen Datei „tar.gz“.":::

Sobald ein Paket importiert wurde, wird es in Ihrem Automation-Konto auf der Seite „Python 2-Pakete“ aufgelistet. Um ein Paket zu entfernen, wählen Sie das Paket aus, und klicken Sie dann auf **Löschen**.

:::image type="content" source="media/python-packages/package-list.png" alt-text="Screenshot der Seite „Python 2-Pakete“ nach dem Import eines Pakets.":::

## <a name="import-packages-with-dependencies"></a>Importieren von Paketen mit Abhängigkeiten

Azure Automation löst Abhängigkeiten für Python-Pakete während des Importvorgangs nicht auf. Es gibt zwei Möglichkeiten, um ein Paket mit allen seinen Abhängigkeiten zu importieren. Nur einer der folgenden Schritte muss verwendet werden, um die Pakete in Ihr Automation-Konto zu importieren.

### <a name="manually-download"></a>Manuelles Herunterladen

Führen Sie auf einem Windows-Computer (64-Bit), auf dem [Python2.7](https://www.python.org/downloads/release/latest/python2) und [pip](https://pip.pypa.io/en/stable/) installiert sind, den folgenden Befehl aus, um ein Paket mit allen seinen Abhängigkeiten herunterzuladen:

```cmd
C:\Python27\Scripts\pip2.7.exe download -d <output dir> <package name>
```

Sobald die Pakete heruntergeladen sind, können Sie sie in Ihr Automation-Konto importieren.

### <a name="runbook"></a>Runbook

 Um ein Runbook zu erhalten, importieren Sie aus der Azure Automation-GitHub-Organisation [Python 2-Pakete aus pypi in Ihr Azure Automation-Konto](https://github.com/azureautomation/import-python-2-packages-from-pypi-into-azure-automation-account). Stellen Sie sicher, dass die Ausführungseinstellungen auf **Azure** festgelegt sind, und starten Sie das Runbook mit den Parametern. Das Runbook erfordert ein „Ausführendes Konto“, damit das Automation-Konto funktioniert. Für jeden Parameter stellen Sie sicher, dass Sie ihn mit dem Parameter starten, wie er in der folgende Liste und Abbildung zu sehen ist:

* -s \<subscriptionId\>
* -g \<resourceGroup\>
* -a \<automationAccount\>
* -m \<modulePackage\>

:::image type="content" source="media/python-packages/import-python-runbook.png" alt-text="Screenshot der Seite „Übersicht“ für import_py2package_from_pypi mit dem Bereich „Runbook starten“ auf der rechten Seite.":::

Mithilfe des Runbooks können Sie angeben, welches Paket heruntergeladen werden soll. Verwenden Sie beispielsweise den `Azure`-Parameter, um alle Azure-Module und alle Abhängigkeiten (etwa 105) herunterzuladen.

Nach Abschluss des Runbooks können Sie die Seite **Python 2-Pakete** unter **Freigegebene Ressourcen** in Ihrem Automation-Konto überprüfen, um zu überprüfen, ob das Paket ordnungsgemäß importiert wurde.

## <a name="use-a-package-in-a-runbook"></a>Verwenden eines Pakets in einem Runbook

Nachdem Sie ein Paket importiert haben, können Sie es in einem Runbook verwenden. Das folgende Beispiel verwendet das [Azure Automation-Hilfsprogrammpaket](https://github.com/azureautomation/azure_automation_utility). Dieses Paket erleichtert die Verwendung von Python mit Azure Automation. Um das Paket zu verwenden, folgen Sie den Anweisungen im GitHub-Repository, und fügen Sie es dem Runbook hinzu. Beispielsweise können Sie `from azure_automation_utility import get_automation_runas_credential` verwenden, um die Funktion zum Abrufen des ausführenden Kontos zu importieren.

```python
import azure.mgmt.resource
import automationassets
from azure_automation_utility import get_automation_runas_credential

# Authenticate to Azure using the Azure Automation RunAs service principal
runas_connection = automationassets.get_automation_connection("AzureRunAsConnection")
azure_credential = get_automation_runas_credential()

# Intialize the resource management client with the RunAs credential and subscription
resource_client = azure.mgmt.resource.ResourceManagementClient(
    azure_credential,
    str(runas_connection["SubscriptionId"]))

# Get list of resource groups and print them out
groups = resource_client.resource_groups.list()
for group in groups:
    print group.name
```

> [!NOTE]
> Da das Python-Paket `automationassets` auf pypi.org nicht verfügbar ist, kann es nicht auf einen Windows-Computer importiert werden.

## <a name="develop-and-test-runbooks-offline"></a>Entwickeln und Testen von Runbooks im Offlinemodus

Um Ihre Python 2-Runbooks offline zu entwickeln und zu testen, können Sie die für das [Python-Modul emulierten Azure Automation-Assets](https://github.com/azureautomation/python_emulated_assets) auf GitHub verwenden. Mit diesem Modul können Sie auf Ihre freigegebenen Ressourcen wie Anmeldeinformationen, Variablen, Verbindungen und Zertifikate verweisen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Vorbereiten eines Python-Runbooks finden Sie unter [Tutorial: Erstellen eines Python-Runbooks](./learn/automation-tutorial-runbook-textual-python-3.md).