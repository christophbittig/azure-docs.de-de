---
title: Erstellen und Verwalten einer Compute-Instanz
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie eine Azure Machine Learning-Compute-Instanz erstellen und verwalten. Verwenden Sie es als Ihre Entwicklungsumgebung oder als Computeziel für Entwicklungs-/Testzwecke.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devx-track-azurecli, references_regions
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/21/2021
ms.openlocfilehash: 8515ad8c9a3db24c8504094cf7273cd149b7ab2d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131559179"
---
# <a name="create-and-manage-an-azure-machine-learning-compute-instance"></a>Erstellen und Verwalten einer Azure Machine Learning-Compute-Instanz

Hier erfahren Sie, wie Sie eine [Compute-Instanz](concept-compute-instance.md) in Ihrem Azure Machine Learning-Arbeitsbereich erstellen und verwalten.

Nutzen Sie eine Compute-Instanz als Ihre vollständig konfigurierte und verwaltete Entwicklungsumgebung in der Cloud. Zu Entwicklungs- und Testzwecken können Sie die Instanz auch als [Trainingscomputeziel](concept-compute-target.md#train) oder [Rückschlussziel](concept-compute-target.md#deploy) verwenden.   Computeinstanzen können mehrere Aufträge parallel ausführen und verfügen über eine Auftragswarteschlange. Da es sich bei einer Compute-Instanz um eine Entwicklungsumgebung handelt, kann diese nicht für andere in Ihrem Arbeitsbereich freigegeben werden.

In diesem Artikel werden folgende Vorgehensweisen behandelt:

* [Erstellen](#create) einer Compute-Instanz
* [Verwalten](#manage) (starten, stoppen, neu starten, löschen) einer Compute-Instanz
* [Erstellen eines Zeitplans](#schedule) zum automatischen Starten und Stoppen der Compute-Instanz (Vorschau)
* [Verwenden Sie ein Setup-Skript](#setup-script) zum Anpassen und Konfigurieren der Compute-Instanz

Sie können Aufträge sicher in einer [virtuellen Netzwerkumgebung](how-to-secure-training-vnet.md) ausführen, ohne dass Unternehmen hierfür SSH-Ports öffnen müssen. Der Auftrag wird in einer Containerumgebung ausgeführt und packt die Abhängigkeiten Ihres Modells in einen Docker-Container.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

* Die [Azure CLI-Erweiterung für Machine Learning Service](reference-azure-machine-learning-cli.md), das [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/intro) oder die [Visual Studio Code-Erweiterung für Azure Machine Learning](how-to-setup-vs-code.md).

## <a name="create"></a>Erstellen

> [!IMPORTANT]
> Die unten markierten Elemente (Vorschau) sind aktuell als öffentliche Vorschau verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

**Geschätzter Zeitaufwand**: Ca. fünf Minuten.

Das Erstellen einer Compute-Instanz ist ein für Ihren Arbeitsbereich einmaliger Prozess. Sie können die Compute-Instanz als Entwicklungsarbeitsstation oder als Computeziel für das Training wiederverwenden. Sie können mehrere Compute-Instanzen an Ihren Arbeitsbereich anfügen. 

Das Kontingent dedizierter Kerne pro Region pro VM-Familie und gesamte regionale Kontingent, das für die Erstellung von Compute-Instanzen gilt, ist einheitlich und wird mit dem Kontingent für Azure Machine Learning-Trainingcomputecluster gemeinsam genutzt. Das Beenden der Compute-Instanz gibt keine Kontingente frei, um sicherzustellen, dass Sie die Compute-Instanz erneut starten können. Es ist nicht möglich, die Größe des virtuellen Computers einer Compute-Instanz zu ändern, nachdem sie erstellt wurde.

<a name="create-instance"></a>Das folgende Beispiel zeigt, wie eine Compute-Instanz erstellt wird:

# <a name="python"></a>[Python](#tab/python)

```python
import datetime
import time

from azureml.core.compute import ComputeTarget, ComputeInstance
from azureml.core.compute_target import ComputeTargetException

# Choose a name for your instance
# Compute instance name should be unique across the azure region
compute_name = "ci{}".format(ws._workspace_id)[:10]

# Verify that instance does not exist already
try:
    instance = ComputeInstance(workspace=ws, name=compute_name)
    print('Found existing instance, use it.')
except ComputeTargetException:
    compute_config = ComputeInstance.provisioning_configuration(
        vm_size='STANDARD_D3_V2',
        ssh_public_access=False,
        # vnet_resourcegroup_name='<my-resource-group>',
        # vnet_name='<my-vnet-name>',
        # subnet_name='default',
        # admin_user_ssh_public_key='<my-sshkey>'
    )
    instance = ComputeInstance.create(ws, compute_name, compute_config)
    instance.wait_for_completion(show_output=True)
```

Weitere Informationen zu den in diesem Beispiel verwendeten Klassen, Methoden und Parametern finden Sie in den folgenden Referenzdokumenten:

* [„ComputeInstance“-Klasse](/python/api/azureml-core/azureml.core.compute.computeinstance.computeinstance)
* [ComputeTarget.create](/python/api/azureml-core/azureml.core.compute.computetarget#create-workspace--name--provisioning-configuration-)
* [ComputeInstance.wait_for_completion](/python/api/azureml-core/azureml.core.compute.computeinstance(class)#wait-for-completion-show-output-false--is-delete-operation-false-)


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli-interactive
az ml computetarget create computeinstance  -n instance -s "STANDARD_D3_V2" -v
```

Weitere Informationen finden Sie in der Referenz zu [az ml computetarget create computeinstance](/cli/azure/ml(v1)/computetarget/create#az_ml_computetarget_create_computeinstance).

# <a name="studio"></a>[Studio](#tab/azure-studio)

1. Navigieren Sie zu [Azure Machine Learning Studio](https://ml.azure.com).
1. Wählen Sie unter __Verwalten__ die Option __Compute__ aus.
1. Wählen Sie die **Compute-Instanz** oben aus.
1. Wenn Sie keine Compute-Instanz haben, wählen Sie **Erstellen** in der Mitte aus der Seite.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Erstellen eines Computeziels":::

1. Wenn eine Liste der Computeressourcen angezeigt wird, wählen Sie oberhalb der Liste **+ Neu** aus.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Auswählen von „Neu“":::
1. Füllen Sie das Formular aus:

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |Computename     |  <ul><li>Der Name ist erforderlich und muss zwischen 3 und 24 Zeichen lang sein.</li><li>Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und das Zeichen **-** .</li><li>Der Name muss mit einem Buchstaben beginnen.</li><li>Der Name muss auf allen vorhandenen Compute-Instanzen innerhalb einer Azure-Region eindeutig sein. Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.</li><li>Wenn ein **-** -Zeichen verwendet wird, muss darauf im Namen mindestens ein Buchstabe folgen.</li></ul>     |
    |Typ des virtuellen Computers |  Wählen Sie CPU oder GPU aus. Dieser Typ kann nach der Erstellung nicht mehr geändert werden.     |
    |Größe des virtuellen Computers     |  Die Größe der unterstützten virtuellen Computer kann in Ihrer Region eingeschränkt sein. Überprüfen Sie die [Verfügbarkeitsliste](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).     |

1. Wählen Sie **Erstellen**, es sei denn, Sie möchten erweiterte Einstellungen für die Compute-Instanz konfigurieren.
1. <a name="advanced-settings"></a>Wählen Sie **Weiter: Erweiterte Einstellungen** wenn Sie folgendes möchten:

    * Aktivieren vom SSH-Zugang.  Folgen Sie den [ausführlichen Anweisungen für den SSH-Zugang](#enable-ssh) unten.
    * Virtuelles Netz aktivieren. Geben Sie **Ressourcengruppe**, **Virtuelles Netzwerk** und **Subnetz** an, um die Compute-Instanz innerhalb von Azure Virtual Network (VNET) zu erstellen. Sie können auch __Keine öffentliche IP-Adresse__ (Vorschau) auswählen, um die Erstellung einer öffentlichen IP-Adresse zu verhindern, die einen Private Link-Arbeitsbereich erfordert. Sie müssen diese [Netzwerkanforderungen](./how-to-secure-training-vnet.md) auch für die Einrichtung virtueller Netzwerke erfüllen. 
    * Den Computer einem anderen Benutzer zuweisen. Für weitere Informationen über die Zuweisung an andere Benutzer siehe [Erstellen im Namen von](#on-behalf).
    * Mit einem Einrichtungsskript bereitstellen (Vorschau) – weitere Einzelheiten zur Erstellung und Verwendung eines Einrichtungsskripts finden Sie unter [Anpassen der Compute-Instanz mit einem Skript](#setup-script).
    * Zeitplan hinzufügen (Vorschau). Legen Sie Zeiten für das automatische Starten und/oder Herunterfahren der Compute-Instanz fest. Siehe [Details zu Zeitplänen](#schedule) unten.


---

Sie können eine Compute-Instanz auch mit einer [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance) erstellen.

## <a name="enable-ssh-access"></a><a name="enable-ssh"></a> Aktivieren Sie den SSH-Zugang

Der SSH-Zugriff ist standardmäßig deaktiviert.  Der SSH-Zugang kann nach der Erstellung nicht geändert werden. Stellen Sie sicher, dass Sie den Zugriff aktivieren, wenn Sie vorhaben, interaktiv mit [VS Code Remote](how-to-set-up-vs-code-remote.md) zu debuggen.  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

Nachdem die Compute-Instanz erstellt wurde und läuft, siehe [Verbindung mit SSH-Zugang](how-to-create-attach-compute-studio.md#ssh-access).

## <a name="create-on-behalf-of-preview"></a><a name="on-behalf"></a> Erstellen im Namen von (Vorschau)

Als Administrator können Sie im Namen einer wissenschaftlichen Fachkraft für Daten eine Compute-Instanz erstellen und ihr die Instanz mit der folgenden Methode zuweisen:

* Konfigurieren Sie die [Advanced Einstellungen](?tabs=azure-studio#advanced-settings)

* [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance).  Ausführliche Informationen zum Suchen der in dieser Vorlage benötigten „TenantID“ und „ObjectID“ finden Sie unter [Ermitteln von Identitätsobjekt-IDs für die Authentifizierungskonfiguration](../healthcare-apis/azure-api-for-fhir/find-identity-object-ids.md).  Sie können diese Werte auch im Azure Active Directory-Portal abrufen.

* REST-API

Die wissenschaftliche Fachkraft für Daten, für die Sie die Compute-Instanz erstellen, benötigt die folgenden [Azure RBAC-Berechtigungen](../role-based-access-control/overview.md) (rollenbasierte Zugriffssteuerung in Azure):

* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

Die wissenschaftliche Fachkraft für Daten kann die Compute-Instanz starten, beenden und neu starten. Sie kann die Compute-Instanz für folgende Zwecke verwenden:
* Jupyter
* JupyterLab
* RStudio
* Integrierte Notebooks

## <a name="schedule-automatic-start-and-stop-preview"></a><a name="schedule"></a>Planen des automatischen Startens und Beendens (Vorschau)

Definieren Sie mehrere Zeitpläne für automatische Herunterfahren und automatische Starten. Erstellen Sie beispielsweise einen Zeitplan, der von Montag bis Donnerstag um 9 Uhr morgens beginnt und um 18 Uhr endet, und einen zweiten Zeitplan, der um 9 Uhr morgens beginnt und um 16 Uhr endet, für Freitag.  Sie können insgesamt vier Zeitpläne pro Compute-Instanz erstellen.

Zeitpläne können auch für [Erstellen im Namen von](#on-behalf) Compute-Instanzen definiert werden. Sie können einen Zeitplan erstellen, um eine Compute-Instanz in einem angehaltenen Zustand zu erstellen. Dies ist besonders nützlich, wenn ein Benutzer eine Compute-Instanz im Namen eines anderen Benutzers erstellt.

### <a name="create-a-schedule-in-studio"></a>Einen Zeitplan in Studio erstellen

1. [Füllen Sie das Formular aus.](?tabs=azure-studio#create-instance)
1. Öffnen Sie auf der zweiten Seite des Formulars **Advanced Einstellungen anzeigen**.
1. Wählen Sie **Zeitplan hinzufügen**, um einen neuen Zeitplan hinzuzufügen.

    :::image type="content" source="media/how-to-create-attach-studio/create-schedule.png" alt-text="Screenshot: Zeitplan in den erweiterten Einstellungen hinzufügen.":::

1. Wählen Sie **Compute-Instanz starten** oder **Compute-Instanz beenden** aus.
1. Wählen Sie die **Zeitzone**.
1. Wählen Sie die **Startzeit** oder **Abschaltzeit**.
1. Wählen Sie die Tage, an denen dieser Zeitplan aktiv ist.

    :::image type="content" source="media/how-to-create-attach-studio/stop-compute-schedule.png" alt-text="Screenshot: Planen Sie das Herunterfahren einer Compute-Instanz.":::

1. Wählen Sie erneut **Zeitplan** hinzufügen, wenn Sie einen weiteren Zeitplan erstellen möchten.

Nachdem die Compute-Instanz erstellt wurde, können Sie im Bereich Details der Compute-Instanz neue Zeitpläne anzeigen, bearbeiten oder hinzufügen.
Bitte beachten Sie, dass die Zeitzonenbezeichnungen die Sommerzeit nicht berücksichtigen. Zum Beispiel ist (UTC+01:00) Amsterdam, Berlin, Bern, Rom, Stockholm, Wien während der Sommerzeit UTC+02:00.

### <a name="create-a-schedule-with-a-resource-manager-template"></a>Erstellen eines Zeitplans mithilfe einer Resource Manager-Vorlage

Sie können den automatischen Start und Stopp einer Compute-Instanz planen, indem Sie eine Ressourcenmanager-[Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance) verwenden.

Fügen Sie in der Ressourcenmanager-Vorlage hinzu:

```
"schedules": "[parameters('schedules')]"
```

Verwenden Sie dann entweder cron- oder LogicApps-Ausdrücke, um den Zeitplan für das Starten oder Stoppen der Instanz in Ihrer Parameterdatei zu definieren:
 
```json
        "schedules": {
        "value": {
        "computeStartStop": [
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 18 * * *"
            },
            "action": "Stop",
            "status": "Enabled"
          },
          {
            "triggerType": "Cron",
            "cron": {              
              "timeZone": "UTC",
              "expression": "0 8 * * *"
            },
            "action": "Start",
            "status": "Enabled"
          },
          { 
            "triggerType": "Recurrence", 
            "recurrence": { 
              "frequency": "Day", 
              "interval": 1, 
              "timeZone": "UTC", 
              "schedule": { 
                "hours": [17], 
                "minutes": [0]
              } 
            }, 
            "action": "Stop", 
            "status": "Enabled" 
          } 
        ]
      }
    }
```

* Die Aktion kann den Wert „Start“ oder „Stop“ haben.
* Für den Auslösertyp `Recurrence` verwenden Sie dieselbe Syntax wie für LogicApps mit diesem [-Serienschema](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).
* Für den Auslösertyp `cron` verwenden Sie die Standard-Cron-Syntax:  

    ```cron
    // Crontab expression format: 
    // 
    // * * * * * 
    // - - - - - 
    // | | | | | 
    // | | | | +----- day of week (0 - 6) (Sunday=0) 
    // | | | +------- month (1 - 12) 
    // | | +--------- day of month (1 - 31) 
    // | +----------- hour (0 - 23) 
    // +------------- min (0 - 59) 
    // 
    // Star (*) in the value field above means all legal values as in 
    // braces for that column. The value column can have a * or a list 
    // of elements separated by commas. An element is either a number in 
    // the ranges shown above or two numbers in the range separated by a 
    // hyphen (meaning an inclusive range). 
    ```
### <a name="azure-policy-support-to-default-a-schedule"></a>Azure Policy-Unterstützung zur Vorgabe eines Zeitplans
Verwenden Sie die Azure-Policy, um für jede Compute-Instanz in einem Abonnement einen Zeitplan für das Herunterfahren zu erzwingen, oder verwenden Sie standardmäßig einen Zeitplan, wenn nichts vorhanden ist.
Nachfolgend finden Sie ein Beispiel für eine Richtlinie, die das Herunterfahren um 22.00 Uhr PST vorsieht.
```json
{
    "mode": "All",
    "policyRule": {
     "if": {
      "allOf": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/computeType",
        "equals": "ComputeInstance"
       },
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "exists": "false"
       }
      ]
     },
     "then": {
      "effect": "append",
      "details": [
       {
        "field": "Microsoft.MachineLearningServices/workspaces/computes/schedules",
        "value": {
         "computeStartStop": [
          {
           "triggerType": "Cron",
           "cron": {
            "startTime": "2021-03-10T21:21:07",
            "timeZone": "Pacific Standard Time",
            "expression": "0 22 * * *"
           },
           "action": "Stop",
           "status": "Enabled"
          }
         ]
        }
       }
      ]
     }
    }
}    
```

## <a name="customize-the-compute-instance-with-a-script-preview"></a><a name="setup-script"></a> Anpassen der Compute-Instanz mit einem Skript (Vorschau)

Verwenden Sie ein Setupskript, um die Compute-Instanz zum Zeitpunkt der Bereitstellung automatisch anzupassen und zu konfigurieren. Als Administrator können Sie ein Anpassungsskript schreiben, mit dem alle Compute-Instanzen im Arbeitsbereich gemäß Ihren Anforderungen bereitgestellt werden.

Einige Beispiele für die Verwendungsmöglichkeiten eines Setupskripts:

* Installieren von Paketen, Tools und Software
* Einbinden von Daten
* Erstellen einer benutzerdefinierten Conda-Umgebung und von Jupyter-Kernels
* Klonen von Git-Repositorys und Festlegen der Git-Konfiguration
* Festlegen von Netzwerkproxys
* Festlegen von Umgebungsvariablen
* Installieren von JupyterLab-Erweiterungen

### <a name="create-the-setup-script"></a>Erstellen des Setupskripts

Das Setup-Skript ist ein Shellskript, das als *Stammbenutzer* ausgeführt wird.  Erstellen Sie das Skript, oder laden Sie es in Ihre **Notebooks**-Dateien hoch:

1. Melden Sie sich bei [Studio](https://ml.azure.com) an, und wählen Sie Ihren Arbeitsbereich aus.
2. Wählen Sie links **Notebooks** aus.
3. Verwenden Sie das Tool **Dateien hinzufügen**, um das Setupshellskript zu erstellen oder hochzuladen.  Der Skriptdateiname muss unbedingt mit „.sh“ enden.  Wenn Sie eine neue Datei erstellen, ändern Sie auch den **Dateityp** in *bash(.sh)* .

:::image type="content" source="media/how-to-create-manage-compute-instance/create-or-upload-file.png" alt-text="Erstellen oder Hochladen Ihres Setupskripts in die Notebooks-Datei in Studio":::

Wenn das Skript ausgeführt wird, ist das aktuelle Arbeitsverzeichnis des Skriptes das Verzeichnis, in das es hochgeladen wurde. Wenn Sie z. B. das Skript in **Benutzer>admin** hochladen, ist der Speicherort des Skripts auf der Compute-Instanz und des aktuellen Arbeitsverzeichnisses, wenn das Skript ausgeführt wird, */home/azureuser/cloudfiles/code/Users/admin*. Dadurch können Sie relative Pfade im Skript verwenden.

Skriptargumente können im Skript als $1, $2 usw. bezeichnet werden.

Wenn Ihr Skript etwas Spezielles für azureuser tut, wie z. B. die Installation der conda-Umgebung oder des Jupyter-Kernels, müssen Sie es in den Block *sudo -u azureuser* wie folgt einfügen

:::code language="bash" source="~/azureml-examples-main/setup-ci/install-pip-package.sh":::

Der Befehl *sudo -u azureuser* ändert den aktuelle Arbeitsordner in  */home/azureuser*. Sie können auch nicht auf die Skriptargumente in diesem Block zugreifen.

Sehen Sie andere Skriptbeispiele in [azureml-examples](https://github.com/Azure/azureml-examples/tree/main/setup-ci).

In Ihrem Skript können zudem die folgenden Umgebungsvariablen verwendet werden:

1. CI_RESOURCE_GROUP
2. CI_WORKSPACE
3. CI_NAME
4. CI_LOCAL_UBUNTU_USER. Dies verweist auf azureuser

Sie können das Einrichtungsskript in Verbindung mit der **Azure-Policy verwenden, um entweder ein Einrichtungsskript für jede Compute-Instanz-Erstellung zu erzwingen oder vorzugeben**. Der Standardwert für den Timeout des Einrichtungsskripts beträgt 15 Minuten. Dieser Wert kann über die Studio-Benutzeroberfläche oder über ARM-Vorlagen unter Verwendung des Parameters DURATION geändert werden.
DURATION ist eine Fließkommazahl mit einem optionalen Suffix: "s" für Sekunden (der Standard), "m" für Minuten, "h" für Stunden oder "d" für Tage.

### <a name="use-the-script-in-the-studio"></a>Verwenden des Skripts in Studio

Nachdem Sie das Skript gespeichert haben, geben Sie es während der Erstellung Ihrer Compute-Instanz an:

1. Melden Sie sich bei [Studio](https://ml.azure.com/) an, und wählen Sie Ihren Arbeitsbereich aus.
1. Wählen Sie links **Compute** aus.
1. Wählen Sie **+Neu** aus, um eine neue Compute-Instanz zu erstellen.
1. [Füllen Sie das Formular aus.](?tabs=azure-studio#create-instance)
1. Öffnen Sie auf der zweiten Seite des Formulars **Erweiterte Einstellungen anzeigen**.
1. Aktivieren Sie die Option **Bereitstellen mit Einrichtungsskript**.
1. Navigieren Sie zu dem Shellskript, das Sie gespeichert haben.  Oder laden Sie ein Skript von Ihrem Computer hoch.
1. Fügen Sie nach Bedarf Befehlsargumente hinzu.

:::image type="content" source="media/how-to-create-manage-compute-instance/setup-script.png" alt-text="Bereitstellen einer Compute-Instanz mit einem Setupskript in Studio":::

Wenn der Arbeitsbereichsspeicher an ein virtuelles Netz angeschlossen ist, können Sie möglicherweise nicht auf die Datei mit dem Einrichtungsskript zugreifen, es sei denn, Sie greifen von einem virtuellen Netz aus auf das Studio zu.

### <a name="use-script-in-a-resource-manager-template"></a>Verwenden eines Skripts in einer Resource Manager-Vorlage

Fügen Sie `setupScripts` zu einer Resource Manager-[Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance) hinzu, um das Setupskript beim Bereitstellen der Compute-Instanz aufzurufen. Beispiel:

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"workspaceStorage",
        "scriptData":"[parameters('creationScript.location')]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```
*scriptData* oben gibt den Speicherort des Erstellungsskripts in der Dateifreigabe für Notebooks an, z. B. *Users/admin/testscript.sh*. *scriptArguments* ist optional und gibt die Argumente für das Erstellungsskript an.

Sie können das Skript stattdessen inline für eine Resource Manager-Vorlage bereitstellen.  Der Shellbefehl kann auf alle Abhängigkeiten verweisen, die in die Notebooks-Dateifreigabe hochgeladen wurden.  Wenn Sie eine Inlinezeichenfolge verwenden, ist das Arbeitsverzeichnis für das Skript */mnt/batch/tasks/shared/LS_root/mounts/clusters/**ciname**/code/Users*.

Geben Sie beispielsweise eine Base64-codierte Befehlszeichenfolge für `scriptData` an:

```json
"setupScripts":{
    "scripts":{
        "creationScript":{
        "scriptSource":"inline",
        "scriptData":"[base64(parameters('inlineCommand'))]",
        "scriptArguments":"[parameters('creationScript.cmdArguments')]"
        }
    }
}
```

### <a name="setup-script-logs"></a>Setupskriptprotokolle

Protokolle der Setupskriptausführung werden im Protokollordner auf der Detailseite der Compute-Instanz angezeigt. Protokolle werden wieder in Ihrer Notebooks-Dateifreigabe im Ordner „Protokolle\<compute instance name>“ gespeichert. Skriptdatei- und Befehlsargumente für eine bestimmte Compute-Instanz werden auf der Detailseite angezeigt.


## <a name="manage"></a>Verwalten

Starten, Beenden, Neustarten und Löschen einer Compute-Instanz. Eine Compute-Instanz wird nicht automatisch herunterskaliert. Stellen Sie daher sicher, dass die Ressource beendet wird, um laufende Gebühren zu vermeiden. Das Beenden einer Compute-Instanz gibt diese frei. Starten Sie sie dann erneut, wenn Sie sie benötigen. Das Beenden der Compute-Instanz stoppt zwar die Abrechnung der Computestunden, es werden Ihnen aber weiterhin Datenträger, öffentliche IP-Adresse und Standardlastenausgleich in Rechnung gestellt. 

Sie können einen [Zeitplan erstellen](#schedule) für die Compute-Instanz, um diese automatisch zu einer bestimmten Uhrzeit und an einem bestimmten Wochentag zu starten und zu stoppen.

> [!TIP]
> Die Compute-Instanz verfügt über einen 120 GB Betriebssystemdatenträger. Wenn Ihnen der Speicherplatz ausgeht, [verwenden Sie das Terminal](how-to-access-terminal.md), um mindestens 1–2 GB zu löschen, bevor Sie die Compute-Instanz beenden oder neu starten. Bitte beenden Sie die Compute-Instanz nicht, indem Sie sudo Herunterfahren im Terminal eingeben. Die Größe des temporären Datenträgers auf der Compute-Instanz hängt von der gewählten VM-Größe ab und wird in „/mnt“ eingebunden.

# <a name="python"></a>[Python](#tab/python)

In den unten stehenden Beispielen lautet der Name der Compute-Instanz **instance**.

* Abrufen des Status

    ```python
    # get_status() gets the latest status of the ComputeInstance target
    instance.get_status()
    ```

* Beenden

    ```python
    # stop() is used to stop the ComputeInstance
    # Stopping ComputeInstance will stop the billing meter and persist the state on the disk.
    # Available Quota will not be changed with this operation.
    instance.stop(wait_for_completion=True, show_output=True)
    ```

* Start

    ```python
    # start() is used to start the ComputeInstance if it is in stopped state
    instance.start(wait_for_completion=True, show_output=True)
    ```

* Neu starten

    ```python
    # restart() is used to restart the ComputeInstance
    instance.restart(wait_for_completion=True, show_output=True)
    ```

* Löschen

    ```python
    # delete() is used to delete the ComputeInstance target. Useful if you want to re-use the compute name
    instance.delete(wait_for_completion=True, show_output=True)
    ```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

In den unten stehenden Beispielen lautet der Name der Compute-Instanz **instance**.

* Beenden

    ```azurecli-interactive
    az ml computetarget stop computeinstance -n instance -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget stop computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_stop).

* Start

    ```azurecli-interactive
    az ml computetarget start computeinstance -n instance -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget start computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_start).

* Neu starten

    ```azurecli-interactive
    az ml computetarget restart computeinstance -n instance -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget restart computeinstance](/cli/azure/ml(v1)/computetarget/computeinstance#az_ml_computetarget_computeinstance_restart).

* Löschen

    ```azurecli-interactive
    az ml computetarget delete -n instance -v
    ```

    Weitere Informationen finden Sie unter [az ml computetarget delete computeinstance](/cli/azure/ml(v1)/computetarget#az_ml_computetarget_delete).

# <a name="studio"></a>[Studio](#tab/azure-studio)
<a name="schedule"></a>

Wählen Sie Ihrem Arbeitsbereich in Azure Machine Learning Studio **Compute** aus, und wählen sie dann oben **Compute-Instanz** aus.

![Verwalten einer Compute-Instanz](./media/concept-compute-instance/manage-compute-instance.png)

Sie können folgende Aktionen ausführen:

* Erstellen einer neuen Compute-Instanz
* Aktualisieren der Registerkarte „Compute-Instanzen“.
* Starten, Beenden und erneutes Starten einer Compute-Instanz.  Sie zahlen für die Instanz, wann immer sie ausgeführt wird. Beenden Sie die Compute-Instanz, wenn Sie sie nicht verwenden, um Kosten zu sparen. Das Beenden einer Compute-Instanz gibt diese frei. Starten Sie sie dann erneut, wenn Sie sie benötigen. Sie können auch eine Zeit für das Starten und Stoppen der Compute-Instanz planen.
* Löschen einer Compute-Instanz.
* Filtern Sie die Liste der Compute-Instanzen, um nur diejenigen anzuzeigen, die Sie erstellt haben.

Für jede Compute-Instanz in einem Arbeitsbereich, den Sie erstellt haben (oder der für Sie erstellt wurde), können Sie:

* Auf der Compute-Instanz auf Jupyter, JupyterLab, RStudio zugreifen.
* SSH-Verbindung mit Compute-Instanz. Der SSH-Zugriff ist standardmäßig deaktiviert, kann aber zum Zeitpunkt der Erstellung der Compute-Instanz aktiviert werden. Der SSH-Zugriff erfolgt über einen Mechanismus mit öffentlichem/privatem Schlüssel. Auf der Registerkarte finden Sie Details zur SSH-Verbindung wie IP-Adresse, Benutzername und Portnummer. In einer virtuellen Netzbereitstellung verhindert die Deaktivierung von SSH den SSH-Zugang aus dem öffentlichen Internet. Sie können jedoch weiterhin SSH aus dem virtuellen Netz über die private IP-Adresse des Knotens der Compute-Instanz und Port 22 nutzen.
* Wählen Sie den Computernamen, um:
    * Details zu einer bestimmten Compute-Instanz anzuzeigen, wie z. B. die IP-Adresse und die Region.
    * Den Zeitplan für das Starten und Stoppen der Compute-Instanz zu erstellen oder zu ändern (Vorschau).  Scrollen Sie zum unteren Ende der Seite, um den Zeitplan zu bearbeiten.

---

Mithilfe von [Azure RBAC](../role-based-access-control/overview.md) können Sie steuern, welche Benutzer im Arbeitsbereich eine Compute-Instanz erstellen, löschen, starten, beenden und neu starten können. Alle Benutzer mit der Rolle „Mitwirkender“ und „Besitzer“ des Arbeitsbereichs können Compute-Instanzen im gesamten Arbeitsbereich erstellen, löschen, starten, beenden und neu starten. Allerdings darf nur der Ersteller einer bestimmten Compute-Instanz oder der zugewiesene Benutzer (falls sie in seinem Namen erstellt wurde) auf dieser Compute-Instanz auf Jupyter, JupyterLab und RStudio zugreifen. Eine Compute-Instanz ist für einen einzelnen Benutzer vorgesehen, der über Root-Zugriff und Terminalzugriff über Jupyter/JupyterLab/RStudio verfügt. Die Compute-Instanz weist die Einzelbenutzeranmeldung auf, und alle Aktionen verwenden die Identität dieses Benutzers für Azure RBAC und die Zuordnung von Experimentausführungen. Der SSH-Zugriff wird über einen Mechanismus mit öffentlichem/privatem Schlüssel gesteuert.

Diese Aktionen können von Azure RBAC gesteuert werden:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/updateSchedules/action*

Zum Erstellen einer Compute-Instanz benötigen Sie Berechtigungen für die folgenden Aktionen verfügen:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


## <a name="next-steps"></a>Nächste Schritte

* [Zugreifen auf ein Compute-Instanzterminal in Ihrem Arbeitsbereich](how-to-access-terminal.md)
* [Erstellen und Verwalten von Dateien](how-to-manage-files.md)
* [Übermitteln einer Trainingsausführung](how-to-set-up-training-targets.md)
