---
title: Bereitstellen eines ML-Modells mithilfe eines Onlineendpunkts (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie Ihr Machine Learning-Modell als Webdienst in Azure bereitstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2, ignite-fall-2021
ms.openlocfilehash: 2bcd276b5c6d80de9266e41a95e1f59b3453a63c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289848"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-an-online-endpoint-preview"></a>Bereitstellen und Bewerten eines Machine Learning-Modells mit einem Onlineendpunkt (Vorschau)

Erfahren Sie, wie Sie einen Onlineendpunkt (Vorschau) zum Bereitstellen Ihres Modells verwenden, damit Sie die zugrunde liegende Infrastruktur nicht erstellen und verwalten müssen. Sie beginnen mit der Bereitstellung eines Modells auf Ihrem lokalen Computer, um eventuelle Fehler zu beheben, und anschließend werden Sie es in Azure bereitstellen und testen.

Außerdem erfahren Sie, wie Sie die Protokolle anzeigen und die Vereinbarung zum Servicelevel (SLA) überwachen. Sie beginnen mit einem Modell und erhalten am Ende einen skalierbaren HTTPS/REST-Endpunkt, den Sie für die Online- und Echtzeitbewertung verwenden können. 

Weitere Informationen finden Sie unter [Was sind Azure Machine Learning-Endpunkte (Vorschau)?](concept-endpoints.md).

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>Voraussetzungen

* Für die Verwendung von Azure Machine Learning ist ein Azure-Abonnement erforderlich. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.

* Installieren und konfigurieren Sie die Azure CLI und die `ml`-Erweiterung für die Azure CLI. Weitere Informationen finden Sie unter [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschauversion)](how-to-configure-cli.md). 

* Sie müssen über eine Azure-Ressourcengruppe verfügen, und Sie (oder der von Ihnen verwendete Dienstprinzipal) müssen auch über den Zugriff vom Typ „Mitwirkender“ darauf verfügen. Eine Ressourcengruppe wird in [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschau)](how-to-configure-cli.md) erstellt. 

* Sie müssen über einen Azure Machine Learning-Arbeitsbereich verfügen. Ein Arbeitsbereich wird in [Installieren, Einrichten und Verwenden der CLI (v2) (Vorschau)](how-to-configure-cli.md) erstellt.

* Wenn Sie die Standardeinstellungen für die Azure-Befehlszeilenschnittstelle noch nicht festgelegt haben, speichern Sie Ihre Standardeinstellungen. Um zu vermeiden, dass Sie die Werte für Ihr Abonnement, Ihren Arbeitsbereich und Ihre Ressourcengruppe mehrfach eingeben müssen, führen Sie den folgenden Code aus:

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* (Optional) Zur lokalen Bereitstellung müssen Sie die [Docker-Engine](https://docs.docker.com/engine/install/) auf Ihrem lokalen Computer installieren. Diese Option wird *dringend empfohlen*, um das Debuggen von Problemen zu vereinfachen.

> [!IMPORTANT]
> Bei den Beispielen in diesem Dokument wird davon ausgegangen, dass Sie die Bash-Shell verwenden. Beispielsweise aus einem Linux-System oder [Windows-Subsystem für Linux](/windows/wsl/about). 

## <a name="prepare-your-system"></a>Vorbereiten Ihres Systems

Klonen Sie zunächst das Beispielrepository (azureml-examples), um diesem Artikel zu folgen. Führen Sie dann den folgenden Code aus, um zum Verzeichnis mit den Beispielen zu wechseln:

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

Wählen Sie zum Festlegen Ihres Endpunktnamens je nach Betriebssystem einen der folgenden Befehle aus (ersetzen Sie `YOUR_ENDPOINT_NAME` durch einen eindeutigen Namen).

Führen Sie für Unix den folgenden Befehl aus:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-local-endpoint.sh" ID="set_endpoint_name":::

> [!NOTE]
> Wir haben vor Kurzem die CLI-Schnittstelle geändert: Früher hatten wir sowohl `endpoint` als auch `deployment` unter `az ml endpoint`, jetzt haben wir sie in `az ml online-endpoint` und `az ml online-deployment` getrennt.  Dadurch wird die Verwendung von Endpunkten in CI/CD-Skripts vereinfacht.

> [!NOTE]
> Endpunktnamen müssen innerhalb einer Azure-Region eindeutig sein. In der Azure-Region „`westus2`“ kann es z. B. nur einen Endpunkt namens `my-endpoint` geben. 

## <a name="review-the-endpoint-and-deployment-configurations"></a>Überprüfen der Endpunkt- und Bereitstellungskonfigurationen

Der folgende Codeausschnitt zeigt die Datei *endpoints/online/managed/sample/endpoint.yml:* 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/endpoint.yml":::

> [!NOTE]
> Eine vollständige Beschreibung zu YAML finden Sie in der [YAML-Referenz zu verwalteten Onlineendpunkten (Vorschauversion)](reference-yaml-endpoint-managed-online.md).

Die Referenz für das YAML-Endpunktformat wird in der folgenden Tabelle beschrieben. Wie Sie diese Attribute angeben, erfahren Sie im YAML-Beispiel in [Vorbereiten Ihres Systems](#prepare-your-system) oder in der [YAML-Referenz für Onlineendpunkte](reference-yaml-endpoint-managed-online.md). Informationen zu Grenzwerten im Zusammenhang mit verwalteten Endpunkten finden Sie unter [Verwalten und Erhöhen von Kontingenten für Ressourcen mit Azure Machine Learning](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview).

| Key | BESCHREIBUNG |
| --- | --- |
| `$schema`    | (Optional) Das YAML-Schema. Um alle verfügbaren Optionen in der YAML-Datei anzuzeigen, können Sie sich das Schema aus dem vorangegangenen Beispiel in einem Browser ansehen.|
| `name`       | Der Name des Endpunkts. Er muss in der Azure-Region eindeutig sein.|
| `traffic` | Der Prozentsatz des Datenverkehrs vom Endpunkt, der zu den einzelnen Bereitstellungen umgeleitet wird. Die Summe der Datenverkehrswerte muss 100 sein. |
| `auth_mode` | Verwenden Sie `key` für schlüsselbasierte Authentifizierung. Verwenden Sie `aml_token` für die tokenbasierte Azure Machine Learning-Authentifizierung. `key` läuft nicht ab, dafür aber `aml_token`. (Rufen Sie das letzte Token mit dem Befehl `az ml online-endpoint get-credentials` ab.) |

Das Beispiel enthält alle Dateien, die zum Bereitstellen eines Modells auf einem Onlineendpunkt erforderlich sind. Um ein Modell bereitzustellen, müssen Sie über Folgendes verfügen:

- Modelldateien (oder Name und Version eines Modells, das bereits in Ihrem Arbeitsbereich registriert ist). In diesem Beispiel verwenden wir ein scikit-learn-Modell, das Regressionen durchführt.
- Der Code, der für die Bewertung des Modells erforderlich ist. In diesem Fall verfügen wir über eine *score.py*-Datei.
- Eine Umgebung, in der Ihr Modell ausgeführt wird. Wie Sie sehen werden, kann die Umgebung ein Docker-Image mit Conda-Abhängigkeiten oder eine Dockerfile-Datei sein.
- Einstellungen zum Angeben des Instanztyps und der Skalierungskapazität.

Der folgende Codeausschnitt zeigt die Datei *endpoints/online/managed/sample/blue-deployment.yml* mit allen erforderlichen Eingaben: 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/blue-deployment.yml":::

In der Tabelle werden die Attribute von `deployment` beschrieben:

| Schlüssel | BESCHREIBUNG |
| --- | --- |
| `name`  | Der Name der Bereitstellung |
| `model` | In diesem Beispiel geben wir die Modelleigenschaften inline an: `local_path`. Modelldateien werden automatisch hochgeladen und mit einem automatisch generierten Namen registriert. Informationen zu verwandten bewährten Methoden finden Sie im Tipp im nächsten Abschnitt. |
| `code_configuration.code.local_path` | Das Verzeichnis, das den gesamten Python-Quellcode für die Bewertung des Modells enthält. Sie können geschachtelte Verzeichnisse und Pakete verwenden. |
| `code_configuration.scoring_script` | Die Python-Datei, die sich im `code_configuration.code.local_path`-Bewertungsverzeichnis befindet. Dieser Python-Code muss über eine `init()`- und eine `run()`-Funktion verfügen. Die Funktion `init()` wird aufgerufen, nachdem das Modell erstellt oder aktualisiert wurde (Sie können es verwenden, um das Modell z. B. im Arbeitsspeicher zwischenzuspeichern). Die Funktion `run()` wird bei jedem Aufruf des Endpunkts aufgerufen, um die tatsächliche Bewertung und Vorhersage auszuführen. |
| `environment` | Enthält die Details der Umgebung zum Hosten des Modells und des Codes. In diesem Beispiel verfügen wir über Inlinedefinitionen, die `path` einbeziehen. Wir verwenden `environment.docker.image` für das Image. Die `conda_file`-Abhängigkeiten werden zusätzlich zum Image installiert. Weitere Informationen finden Sie im Tipp im nächsten Abschnitt. |
| `instance_type` | Die VM-SKU, die Ihre Bereitstellungsinstanzen hosten wird. Weitere Informationen finden Sie unter [Unterstützte VM-SKUs für verwaltete Onlineendpunkte](reference-managed-online-endpoints-vm-sku-list.md). |
| `instance_count` | Die Anzahl der Instanzen in der Bereitstellung. Richten Sie den Wert nach der zu erwartenden Workload. Für eine entsprechende Hochverfügbarkeit empfehlen wir, dass Sie `instance_count` mindestens auf `3` festlegen. |

Weitere Informationen zum YAML-Schema finden Sie in der [YAML-Referenz für Onlineendpunkte](reference-yaml-endpoint-managed-online.md).

> [!NOTE]
> So verwenden Sie Kubernetes anstelle von verwalteten Endpunkten als Computeziel
> 1. Erstellen Sie Ihren Kubernetes-Cluster, und fügen Sie ihn mithilfe von [Azure Machine Learning Studio](how-to-attach-arc-kubernetes.md?&tabs=studio#attach-arc-cluster) als Computeziel an Ihren Azure Machine Learning-Arbeitsbereich an.
> 1. Verwenden Sie den [YAML-Endpunktcode](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml), um Kubernetes anstelle des verwalteten YAML-Endpunktcodes als Ziel zu verwenden. Sie müssen den YAML-Code bearbeiten, um den Wert von `target` in den Namen Ihres registrierten Computeziels zu ändern.
>
> Alle Befehle, die in diesem Artikel verwendet werden (mit Ausnahme der optionalen SLA-Überwachung und der Azure Log Analytics-Integration), können entweder mit verwalteten Endpunkten oder mit Kubernetes-Endpunkten verwendet werden.

### <a name="register-your-model-and-environment-separately"></a>Separates Registrieren von Modell und Umgebung

In diesem Beispiel geben wir die `local_path` Inline -Datei (aus der Dateien hochgeladen werden) an. Die CLI lädt die Dateien automatisch hoch und registriert das Modell und die Umgebung. Als bewährte Methode für die Produktion sollten Sie das Modell und die Umgebung registrieren und den registrierten Namen sowie die Version im YAML-Code separat angeben. Verwenden Sie das Format `model: azureml:my-model:1` oder `environment: azureml:my-env:1`.

Zur Registrierung können Sie die YAML-Definitionen von `model` und `environment` in separate YAML-Dateien extrahieren und die Befehle `az ml model create` und `az ml environment create` verwenden. Führen Sie `az ml model create -h` und `az ml environment create -h` aus, um mehr über diese Befehle zu erfahren.

### <a name="use-different-cpu-and-gpu-instance-types"></a>Verwenden verschiedener CPU- und GPU-Instanztypen

Der vorherige YAML-Code verwendet einen universellen Typ (`Standard_F2s_v2`) und ein Docker-Image ohne GPU (im YAML-Code finden Sie das Attribut `image`). Für GPU-Compute wählen Sie eine GPU-Computetyp-SKU und ein GPU-Docker-Image aus.

Die unterstützten universellen und GPU-Instanztypen finden Sie unter [Unterstützte VM-SKUs für verwaltete Onlineendpunkte](reference-managed-online-endpoints-vm-sku-list.md). Eine Liste der CPU- und GPU-Basisimages von Azure Machine Learning finden Sie unter [Azure Machine Learning-Basisimages](https://github.com/Azure/AzureML-Containers).

### <a name="use-more-than-one-model"></a>Verwenden mehrerer Modelle

Derzeit können Sie nur ein Modell pro Bereitstellung in YAML angeben. Wenn Sie beim Registrieren des Modells über mehrere Modelle verfügen, kopieren Sie alle Modelle als Dateien oder Unterverzeichnisse in einen Ordner, den Sie für die Registrierung verwenden. Verwenden Sie in Ihrem Bewertungsskript die Umgebungsvariable `AZUREML_MODEL_DIR`, um den Pfad zum Stammverzeichnis des Modells zu ermitteln. Die zugrunde liegende Verzeichnisstruktur wird beibehalten.

## <a name="understand-the-scoring-script"></a>Grundlegendes zum Bewertungsskript

> [!TIP]
> Das Format des Bewertungsskripts für Onlineendpunkte entspricht dem Format, das in der vorherigen Version der CLI und im Python SDK verwendet wurde.

Wie bereits erwähnt, muss das `code_configuration.scoring_script` eine `init()`-Funktion und eine `run()`-Funktion aufweisen. In diesem Beispiel wird die [score.py-Datei](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py) verwendet. Die `init()`-Funktion wird aufgerufen, wenn der Container initialisiert oder gestartet wird. Die Initialisierung erfolgt in der Regel kurz nach dem Erstellen oder Aktualisieren der Bereitstellung. Schreiben Sie hier eine Logik für globale Initialisierungsvorgänge wie das Zwischenspeichern des Modells im Arbeitsspeicher (wie wir es in diesem Beispiel durchführen). Die Funktion `run()` wird für jeden Aufruf des Endpunkts aufgerufen und sollte die tatsächliche Bewertung und Vorhersage ausführen. Im Beispiel extrahieren wir die Daten aus der JSON-Eingabe, rufen die `predict()`-Methode des scikit-learn-Modells auf und geben dann das Ergebnis zurück.

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>Lokales Bereitstellen und Debuggen mithilfe von lokalen Endpunkten

Um Zeit beim Debuggen zu sparen, *wird dringend empfohlen*, den Endpunkt lokal zu testen. Weitere Informationen finden Sie unter [Lokales Debuggen verwalteter Onlineendpunkte in Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

> [!NOTE]
> * Für die lokale Bereitstellung muss die [Docker-Engine](https://docs.docker.com/engine/install/) installiert sein.
> * Die Docker-Engine muss ausgeführt werden. Die Docker-Engine wird in der Regel gestartet, wenn der Computer gestartet wird. Wenn dies nicht der Fall ist, können Sie die [Problembehandlung für die Docker-Engine durchführen](https://docs.docker.com/config/daemon/#start-the-daemon-manually).

> [!IMPORTANT]
> Das Ziel einer lokalen Endpunktbereitstellung besteht darin, Ihren Code und Ihre Konfiguration vor der Bereitstellung in Azure zu überprüfen und zu debuggen. Die lokale Bereitstellung weist die folgenden Einschränkungen auf:
> - Lokale Endpunkte unterstützen *keine* Datenverkehrsregeln, Authentifizierungen oder Testeinstellungen. 
> - Lokale Endpunkte unterstützen nur eine Bereitstellung pro Endpunkt. 

### <a name="deploy-the-model-locally"></a>Lokales Bereitstellen des Modells

Erstellen Sie zunächst den Endpunkt. Optional können Sie für einen lokalen Endpunkt diesen Schritt überspringen und die Bereitstellung (nächster Schritt) direkt erstellen, wodurch wiederum die erforderlichen Metadaten erstellt werden. Dies ist für Entwicklungs- und Testzwecke nützlich.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-local-endpoint.sh" ID="create_endpoint":::

Erstellen Sie nun eine Bereitstellung mit dem Namen `blue` unter dem Endpunkt.

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-local-endpoint.sh" ID="create_deployment":::

Das `--local`-Flag weist die CLI an, den Endpunkt in der Docker-Umgebung bereitzustellen.

> [!TIP]
> Verwenden Sie Visual Studio Code, um Ihre Endpunkte lokal zu testen und zu debuggen. Weitere Informationen finden Sie unter [Lokales Debuggen verwalteter Onlineendpunkten in Visual Studio Code](how-to-debug-managed-online-endpoints-visual-studio-code.md).

### <a name="verify-the-local-deployment-succeeded"></a>Überprüfen Sie, ob die lokale Bereitstellung erfolgreich war.

Überprüfen Sie den Status, um festzustellen, ob das Modell ohne Fehler bereitgestellt wurde:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-local-endpoint.sh" ID="get_status":::

Die Ausgabe sollte in etwa dem folgenden JSON-Code entsprechen. Beachten Sie, dass der `provisioning_state``Succeeded` ist.

```json
{
  "auth_mode": "key",
  "location": "local",
  "name": "docs-endpoint",
  "properties": {},
  "provisioning_state": "Succeeded",
  "scoring_uri": "http://localhost:49158/score",
  "tags": {},
  "traffic": {}
}
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>Aufrufen des lokalen Endpunkts zum Bewerten von Daten mit Ihrem Modell

Rufen Sie den Endpunkt auf, um das Modell mithilfe des praktischen Befehls `invoke` und der Übergabe von in einer JSON-Datei gespeicherten Abfrageparametern zu bewerten:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-local-endpoint.sh" ID="test_endpoint":::

Wenn Sie einen REST-Client (z. B. curl) verwenden möchten, müssen Sie über den Bewertungs-URI verfügen. Führen Sie `az ml online-endpoint show --local -n $ENDPOINT_NAME` aus, um den Bewertungs-URI abzurufen. Suchen Sie in den zurückgegebenen Daten das Attribut `scoring_uri`. Curl-basierte Beispielbefehle sind weiter später in diesem Dokument verfügbar.

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>Überprüfen der Protokolle hinsichtlich der Ausgaben des Aufrufvorgangs

In der *score.py*-Beispieldatei protokolliert die `run()`-Methode einige Ausgaben in der Konsole. Sie können diese Ausgabe mit dem Befehl `get-logs` erneut anzeigen:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-local-endpoint.sh" ID="get_logs":::

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>Bereitstellen Ihres verwalteten Onlineendpunkts in Azure

Stellen Sie als nächstes Ihren verwalteten Onlineendpunkt in Azure bereit.

### <a name="deploy-to-azure"></a>In Azure bereitstellen

Führen Sie den folgenden Code aus, um den Endpunkt in der Cloud zu erstellen:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="create_endpoint" :::

Führen Sie den folgenden Code aus, um die Bereitstellung mit dem Namen `blue` unter dem Endpunkt zu erstellen:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="create_deployment" :::

Diese Bereitstellung kann bis zu 15 Minuten dauern, je nachdem, ob die zugrunde liegende Umgebung bzw. das zugrunde liegende Image zum ersten Mal erstellt wird. Nachfolgende Bereitstellungen, die dieselbe Umgebung verwenden, werden schneller abgeschlossen.

> [!Important]
> Das Flag "--all-traffic" im obigen `az ml online-deployment create` ordnet 100 % des Datenverkehrs zum Endpunkt der neu erstellten Bereitstellung zu. Obwohl dies für Entwicklungs- und Testzwecke hilfreich ist, sollten Sie für die Produktion den Datenverkehr zur neuen Bereitstellung über einen expliziten Befehl öffnen. Zum Beispiel, `az ml online-endpoint update -n $ENDPOINT_NAME --traffic "blue=100"` 

> [!TIP]
> * Wenn Sie ihre CLI-Konsole nicht blockieren möchten, können Sie dem Befehl das Flag `--no-wait` hinzufügen. Dadurch wird jedoch die interaktive Anzeige des Bereitstellungsstatus nicht mehr angezeigt.
>
> * Verwenden Sie [Problembehandlung für die Bereitstellung verwalteter Onlineendpunkte (Vorschau)](./how-to-troubleshoot-online-endpoints.md), um Fehler zu debuggen.

### <a name="check-the-status-of-the-deployment"></a>Überprüfen Sie den Status der Bereitstellung.

Der Befehl `show` enthält Informationen in `provisioning_status` zum Endpunkt und zur Bereitstellung:

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

Sie können alle Endpunkte im Arbeitsbereich in einem Tabellenformat mit dem Befehl `list` auflisten:

```azurecli
az ml online-endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>Überprüfen des Status der Cloudbereitstellung

Überprüfen Sie die Protokolle, um festzustellen, ob das Modell ohne Fehler bereitgestellt wurde:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

Standardmäßig werden Protokolle per Pullvorgang vom Rückschlussserver abgerufen. Wenn Sie die Protokolle aus dem Speicherinitialisierer (der die Ressourcen wie Modell und Code in den Container einbindet) anzeigen möchten, fügen Sie das Flag `--container storage-initializer` hinzu.

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>Aufrufen des Endpunkts zum Bewerten von Daten mit Ihrem Modell

Sie können entweder den `invoke`-Befehl oder einen REST-Client Ihrer Wahl verwenden, um den Endpunkt aufzurufen und einige Daten zu bewerten: 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

Das folgende Beispiel zeigt, wie Sie den Schlüssel für die Authentifizierung beim Endpunkt erhalten:

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl_get_key":::

Verwenden Sie als Nächstes curl, um Daten zu bewerten.

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint_using_curl" :::

Beachten Sie, dass wir die Befehle `show` und `get-credentials` verwenden, um die Anmeldeinformationen für die Authentifizierung zu erhalten. Beachten Sie zudem, dass wir das Flag `--query` verwenden, um die Attribute auf die erforderlichen Angaben zu beschränken. Weitere Informationen zu `--query` finden Sie unter [Abfragen der Azure CLI-Befehlsausgabe](/cli/azure/query-azure-cli).

Führen Sie `get-logs` erneut aus, um die Aufrufprotokolle anzuzeigen.

### <a name="optional-update-the-deployment"></a>(Optional) Aktualisieren der Bereitstellung

Wenn Sie den Code, das Modell oder die Umgebung aktualisieren möchten, aktualisieren Sie die YAML-Datei und führen Sie dann den Befehl `az ml online-endpoint update` aus. 

> [!Note]
> Wenn Sie die Instanzanzahl und zusammen mit anderen Modelleinstellungen (Code, Modell oder Umgebung) in einem einzigen `update` Befehl aktualisieren: Zuerst wird der Skalierungsvorgang ausgeführt, dann werden die anderen Updates angewendet. In der Produktionsumgebung ist es eine bewährte Methode, diese Vorgänge separat durchzuführen.

Informationen zur Funktionsweise von `update`:

1. Öffnen Sie die Datei *online/model-1/onlinescoring/score.py*.
1. Ändern Sie die letzte Zeile der `init()`-Funktion: Fügen Sie nach `logging.info("Init complete")` einfach `logging.info("Updated successfully")` hinzu. 
1. Speichern Sie die Datei .
1. Führen Sie den folgenden Befehl aus:

    ```azurecli
    az ml online-deployment update -n blue --endpoint $ENDPOINT_NAME -f endpoints/online/managed/sample/blue-deployment.yml
    ```

    > [!Note]
    > Das Aktualisieren mithilfe von YAML ist deklarativ. Dies bedeutet, dass Änderungen am YAML-Code in den zugrunde liegenden Azure Resource Manager-Ressourcen (Endpunkte und Bereitstellungen) widergespiegelt werden. Ein deklarativer Ansatz nutzt [GitOps](https://www.atlassian.com/git/tutorials/gitops): *Alle* Änderungen an Endpunkten bzw. Bereitstellungen (auch `instance_count`) erfolgen über den YAML-Code. Sie können Aktualisierungen vornehmen, ohne den YAML-Code zu verwenden, indem Sie das Flag `--set` verwenden.
    
1. Da Sie die Funktion `init()` geändert haben (`init()` wird ausgeführt, wenn der Endpunkt erstellt oder aktualisiert wird), befindet sich die Meldung `Updated successfully` in den Protokollen. Rufen Sie die Protokolle ab, indem Sie Folgendes ausführen:

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

Der Befehl `update` funktioniert auch mit lokalen Bereitstellungen. Verwenden Sie denselben `az ml online-deployment update`-Befehl mit dem Flag `--local`.

> [!TIP]
> Mit dem Befehl `update` können Sie den [`--set`-Parameter in der Azure CLI](/cli/azure/use-cli-effectively#generic-update-arguments) verwenden, um Attribute in Ihrer YAML-Datei außer Kraft zu setzen *oder* bestimmte Attribute ohne Übergabe der YAML-Datei festzulegen. Die Verwendung von `--set` für einzelne Attribute ist besonders in Entwicklungs- und Testszenarien nützlich. Sie können das Flag `--set instance_count=2` verwenden, um z. B. den `instance_count`-Wert für die erste Bereitstellung hochzuskalieren. Da die YAML-Datei jedoch nicht aktualisiert wird, unterstützt dieses Verfahren [GitOps](https://www.atlassian.com/git/tutorials/gitops) nicht.
> [!Note]
> Das obige Beispiel zeigt ein rollierendes Update, d. h., die gleiche Bereitstellung wird mit der neuen Konfiguration aktualisiert, mit 20 % Knoten gleichzeitig. Wenn die Bereitstellung über 10 Knoten verfügt, werden 2 Knoten gleichzeitig aktualisiert. Für die Verwendung in der Produktion sollten Sie die [Blau-Grün-Bereitstellung](how-to-safely-rollout-managed-endpoints.md)in Betracht ziehen, die eine sicherere Alternative bietet.
### <a name="optional-configure-autoscaling"></a>(Optional) Konfigurieren der automatischen Skalierung

Die automatische Skalierung führt automatisch die richtige Menge an Ressourcen aus, um die Last für Ihre Anwendung zu bewältigen. Verwaltete Onlineendpunkte unterstützen die automatische Skalierung durch die Integration in das Feature für die automatische Skalierung von Azure Monitor. Informationen zum Konfigurieren der automatischen Skalierung finden Sie unter [Automatisches Skalieren von Onlineendpunkten](how-to-autoscale-endpoints.md).

### <a name="optional-monitor-sla-by-using-azure-monitor"></a>(Optional) Überwachen der SLA mit Azure Monitor

Zum Anzeigen von Metriken und Festlegen von Warnungen auf der Grundlage Ihrer SLA führen Sie die Schritte aus, die unter [Überwachen verwalteter Onlineendpunkte](how-to-monitor-online-endpoints.md) beschrieben sind.

### <a name="optional-integrate-with-log-analytics"></a>(Optional) Integrieren mit Log Analytics

Der Befehl `get-logs` stellt nur die letzten paar hundert Protokollzeilen aus einer automatisch ausgewählten Instanz zur Verfügung. Log Analytics bietet jedoch eine Möglichkeit, Protokolle dauerhaft zu speichern und zu analysieren. 

Erstellen Sie zunächst einen Log Analytics-Arbeitsbereich, indem Sie die Schritte unter [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace) durchführen.

Gehen Sie dann im Azure-Portal wie folgt vor:

1. Wechseln Sie zur Ressourcengruppe.
1. Wählen Sie Ihren Endpunkt aus.
1. Wählen Sie die Seite **ARM-Ressource** aus.
1. Wählen Sie **Diagnoseeinstellungen** aus.
1. Wählen Sie **Einstellungen hinzufügen** aus.
1. Aktivieren Sie diese Option, um Konsolenprotokolle an den Log Analytics-Arbeitsbereich zu senden.

Die Verbindung mit den Protokollen kann bis zu einer Stunde dauern. Senden Sie nach einer Stunde einige Bewertungsanforderungen, und überprüfen Sie anschließend die Protokolle mithilfe der folgenden Schritte:

1. Öffnen Sie den Log Analytics-Arbeitsbereich. 
1. Wählen Sie im linken Menü die Option **Protokolle** aus.
1. Schließen Sie das Dialogfeld **Abfragen**, das automatisch geöffnet wird.
1. Doppelklicken Sie auf **AmlOnlineEndpointConsoleLog**.
1. Klicken Sie auf **Run** (Ausführen).

## <a name="delete-the-endpoint-and-the-deployment"></a>Löschen des Endpunkts und der Bereitstellung

Wenn Sie die Bereitstellung nicht verwenden, sollten Sie sie durch Ausführen des folgenden Codes löschen (der Endpunkt und alle zugrunde liegenden Bereitstellungen werden gelöscht):

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Bereitstellen von Modellen per REST (Vorschau)](how-to-deploy-with-rest.md)
- [Erstellen und Verwenden von verwalteten Onlineendpunkten (Vorschau) in Studio](how-to-use-managed-online-endpoint-studio.md)
- [Sicherer Rollout für Onlineendpunkte (Vorschau)](how-to-safely-rollout-managed-endpoints.md)
- [Automatisches Skalieren verwalteter Onlineendpunkte](how-to-autoscale-endpoints.md)
- [Verwenden von Batchendpunkten (Vorschau) für die Batchbewertung](how-to-use-batch-endpoint.md)
- [Anzeigen der Kosten für einen verwalteten Azure Machine Learning-Onlineendpunkt (Vorschau)](how-to-view-online-endpoints-costs.md)
- [Zugreifen auf Azure-Ressourcen mit einem verwalteten Onlineendpunkt und einer verwalteten Identität (Vorschau)](how-to-access-resources-from-endpoints-managed-identities.md)
- [Problembehandlung für die Bereitstellung verwalteter Onlineendpunkte](how-to-troubleshoot-online-endpoints.md)
