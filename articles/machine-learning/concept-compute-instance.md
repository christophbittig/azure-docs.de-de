---
title: Was ist eine Azure Machine Learning-Compute-Instanz?
titleSuffix: Azure Machine Learning
description: Erfahren Sie mehr über die Azure Machine Learning-Compute-Instanz, eine vollständig verwaltete cloudbasierte Arbeitsstation.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 09/22/2021
ms.openlocfilehash: af7069aae9412f7964cca82c034d0e0966220ada
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129081128"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Was ist eine Azure Machine Learning-Compute-Instanz?

Eine Azure Machine Learning-Compute-Instanz ist eine verwaltete cloudbasierte Arbeitsstation für Datenanalysten.

Compute-Instanzen vereinfachen den Einstieg in die Azure Machine Learning-Entwicklung und verhelfen IT-Administratoren zu Verwaltungs- und Unternehmensbereitschaftsfunktionen.

Nutzen Sie eine Compute-Instanz als Ihre vollständig konfigurierte und verwaltete Entwicklungsumgebung in der Cloud für maschinelles Lernen. Sie können auch als Computeziel für Entwicklungs- und Testzwecke für Training und Rückschluss verwendet werden.

Verwenden Sie für das Modelltraining in Produktionsqualität einen [Azure Machine Learning-Computecluster](how-to-create-attach-compute-cluster.md) mit Skalierungsmöglichkeiten dank mehrerer Knoten. Verwenden Sie für die Modellimplementierung in Produktionsqualität den [Azure Kubernetes Service-Cluster](how-to-deploy-azure-kubernetes-service.md).

Damit die Jupyter-Funktionen für Computeinstanzen eingesetzt werden können, stellen Sie sicher, dass die Websocketkommunikation nicht deaktiviert ist. Stellen Sie sicher, dass Ihr Netzwerk WebSocket-Verbindungen mit *.instances.azureml.net und *.instances.azureml.ms zulässt.

> [!IMPORTANT]
> Die in diesem Artikel markierten Elemente (Vorschau) sind aktuell als öffentliche Vorschau verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="why-use-a-compute-instance"></a>Gründe für eine Compute-Instanz

Eine Compute-Instanz ist eine vollständig verwaltete cloudbasierte Arbeitsstation, die für Ihre Machine Learning-Entwicklungsumgebung optimiert ist. Ihnen bieten sich folgende Vorteile:

|Hauptvorteile|BESCHREIBUNG|
|----|----|
|Produktivität|Sie können Modelle mit integrierten Notebooks und den folgenden Tools im Azure Machine Learning Studio erstellen und bereitstellen:<br/>-  Jupyter<br/>-  JupyterLab<br/>-  VS Code (Vorschauversion)<br/>-  RStudio (Vorschauversion)<br/>Die Compute-Instanz ist vollständig in den Arbeitsbereich und das Studio von Azure Machine Learning integriert. Sie können Notebooks und Daten mit anderen Datenanalysten im Arbeitsbereich gemeinsam nutzen.<br/> Sie können auch VS Code mit Compute-Instanzen verwenden.
|Verwaltet und sicher|Verringern Sie Ihren Sicherheitsaufwand, und gewinnen Sie Compliance mit Anforderungen der Unternehmenssicherheit. Compute-Instanzen bieten verlässliche Verwaltungsrichtlinien und sichere Netzwerkkonfigurationen wie die folgenden:<br/><br/>– Automatisierte Bereitstellung über Resource Manager-Vorlagen oder das Azure Machine Learning SDK<br/>- [Rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Unterstützung virtueller Netzwerke](./how-to-secure-training-vnet.md#compute-cluster)<br/> - SSH-Richtlinie zum Aktivieren/Deaktivieren des SSH-Zugriffs<br/>– Mit der Option [Keine öffentliche IP-Adresse](./how-to-secure-training-vnet.md#no-public-ip) können Sie eine sichere Konnektivitätslösung ohne Abhängigkeit von einer öffentlichen IP-Adresse aktivieren.</br>– Aktivierung von TLS 1.2 |
|Vorkonfiguriert&nbsp;für&nbsp;ML|Sparen Sie Zeit bei der Einrichtung von Aufgaben mit vorkonfigurierten und aktuellen ML-Paketen, Deep Learning- Frameworks und GPU-Treibern.|
|Vollständig anpassbar|Umfassende Unterstützung für Azure-VM-Typen einschließlich GPUs und durchweg einfache Anpassungen wie die Installation von Paketen und Treibern machen erweiterte Szenarien zu einem Kinderspiel. |

* Schützen Sie Ihre Compute-Instanz mit der Option **[Keine öffentliche IP-Adresse (Vorschau)](./how-to-secure-training-vnet.md#no-public-ip)** .
* Die Compute-Instanz ist ähnlich Computeclustern ebenfalls ein sicheres Computeziel, es handelt sich aber um einen einzelnen Knoten.
* Sie können selbst eine [Compute-Instanz erstellen](how-to-create-manage-compute-instance.md?tabs=python#create) oder ein Administrator kann eine **[Compute-Instanz für Sie erstellen](how-to-create-manage-compute-instance.md?tabs=python#on-behalf)** .
* Sie können auch ein **[Setupskript (Vorschau) verwenden](how-to-create-manage-compute-instance.md#setup-script)** , um die Computeinstanz gemäß Ihren Anforderungen automatisch anzupassen und zu konfigurieren.
* Um Kosten zu sparen, **[erstellen Sie einen Zeitplan (Vorschau)](how-to-create-manage-compute-instance.md#schedule)** , um die Compute-Instanz automatisch zu starten und zu beenden.


## <a name="tools-and-environments"></a><a name="contents"></a>Tools und Umgebungen

Eine Azure Machine Learning-Compute-Instanz ermöglicht Ihnen das Erstellen, Trainieren und Bereitstellen von Modellen in einer vollständig integrierten Notebookumgebung in Ihrem Arbeitsbereich.

Sie können Jupyter-Notebooks in [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) unter Verwendung der Compute-Instanz als Remoteserver ausführen, ohne dass SSH erforderlich ist. Sie können die VS Code-Integration auch über die [Remote-SSH-Erweiterung](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/) aktivieren.

Sie können [Pakete installieren](how-to-access-terminal.md#install-packages) und [Kernel](how-to-access-terminal.md#add-new-kernels) zu Ihrer Compute-Instanz hinzufügen.

Die folgenden Tools und Umgebungen sind in der Compute-Instanz bereits installiert:

|Allgemeine Tools und Umgebungen|Details|
|----|:----:|
|Treiber|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI Library||
|Azure CLI ||
|Azure Machine Learning-Beispiele ||
|Docker||
|nginx||
|NCCL 2.0 ||
|Protobuf||

|**R**-Tools und -Umgebungen|Details|
|----|:----:|
|RStudio Server Open Source Edition (Vorschauversion)||
|R-Kernel||

|**PYTHON**-Tools und Umgebungen|Details|
|----|----|
|Anaconda Python||
|Jupyter und Erweiterungen||
|Jupyterlab und Erweiterungen||
[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro)</br>aus PyPI|Umfasst die meisten der AzureML-Zusatzpakete.  Um die vollständige Liste anzuzeigen, [öffnen Sie auf Ihrer Compute-Instanz ein Terminalfenster](how-to-access-terminal.md), und führen Sie Folgendes aus: <br/> `conda list -n azureml_py36 azureml*` |
|Andere PyPi-Pakete|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-Pakete|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Deep Learning-Pakete|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-Pakete|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Python-Beispiele für Azure Machine Learning||

Python-Pakete sind alle in der **Python 3.8 – AzureML**-Umgebung installiert. Die Compute-Instanz verfügt über Ubuntu 18.04 als Basisbetriebssystem.

## <a name="accessing-files"></a>Zugreifen auf Dateien

Notebooks und R-Skripts werden im Standardspeicherkonto Ihres Arbeitsbereichs in der Azure-Dateifreigabe gespeichert.  Diese Dateien befinden sich im Verzeichnis „Benutzerdateien“. Dieser Speicher erleichtert die gemeinsame Nutzung von Notebooks zwischen Compute-Instanzen. Das Speicherkonto bewahrt auch Ihre Notebooks sicher auf, wenn Sie eine Compute-Instanz beenden oder löschen.

Das Azure-Dateifreigabekonto Ihres Arbeitsbereichs wird als Laufwerk in die Compute-Instanz eingebunden. Dieses Laufwerk ist das Standardarbeitsverzeichnis für Jupyter, Jupyter Labs und RStudio. Das bedeutet, dass die Notebooks und andere Dateien, die Sie in Jupyter, JupyterLab oder RStudio erstellen, automatisch auf der Dateifreigabe gespeichert werden und auch für die Verwendung in anderen Compute-Instanzen zur Verfügung stehen.

Auf die Dateien in der Dateifreigabe kann von allen Compute-Instanzen im gleichen Arbeitsbereich zugegriffen werden. Änderungen an diesen Dateien auf der Compute-Instanz werden zuverlässig dauerhaft in der Dateifreigabe zurückgespeichert.

Sie können auch die neuesten Azure Machine Learning-Beispiele in Ihren Ordner im Verzeichnis mit den Benutzerdateien in der Dateifreigabe des Arbeitsbereichs klonen.

Das Schreiben kleiner Dateien kann auf Netzlaufwerken langsamer sein als das Schreiben auf den lokalen Datenträger der Compute-Instanz selbst.  Wenn Sie viele kleine Dateien schreiben, sollten Sie versuchen, ein Verzeichnis direkt auf der Compute-Instanz zu verwenden, z. B. ein `/tmp`-Verzeichnis. Beachten Sie, dass auf diese Dateien von anderen Compute-Instanzen nicht zugegriffen werden kann.

Speichern Sie keine Trainingsdaten auf der Notebook-Dateifreigabe. Sie können das Verzeichnis `/tmp` auf der Compute-Instanz für Ihre temporären Daten verwenden.  Schreiben Sie jedoch keine sehr großen Dateien mit Daten auf den Betriebssystemdatenträger der Compute-Instanz. Der Betriebssystemdatenträger der Compute-Instanz verfügt über eine Kapazität von 128 GB. Sie können temporäre Trainingsdaten auch auf einem temporären Datenträger speichern, der auf /mnt eingebunden ist. Die temporäre Datenträgergröße kann basierend auf der gewählten VM-Größe konfiguriert werden und kann größere Datenmengen speichern, wenn eine VM mit höherer Größe ausgewählt wird. Sie können auch [Datenspeicher und Datasets](concept-azure-machine-learning-architecture.md#datasets-and-datastores) einbinden. Alle Softwarepakete, die Sie installieren, werden auf dem Betriebssystemdatenträger der Compute-Instanz gespeichert. Beachten Sie, dass die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für Betriebssystemdatenträger derzeit nicht unterstützt wird. Der Betriebssystemdatenträger für die Compute-Instanz wird mit von Microsoft verwalteten Schlüsseln verschlüsselt. 

### <a name="create-a-compute-instance"></a><a name="create"></a>Erstellen einer Compute-Instanz

Als Administrator können Sie eine **[Compute-Instanz für andere Benutzer im Arbeitsbereich (Vorschau) erstellen](how-to-create-manage-compute-instance.md#on-behalf)** .

Sie können auch ein **[Setupskript (Vorschau) verwenden](how-to-create-manage-compute-instance.md#setup-script)** , um die Computeinstanz gemäß Ihren Anforderungen automatisch anzupassen und zu konfigurieren.

Erstellen Sie für eine neue Compute-Instanz für sich in Ihrem Arbeitsbereich im Azure Machine Learning Studio [eine neue Compute-Instanz](how-to-create-manage-compute-instance.md?tabs=azure-studio#create), und zwar entweder im Abschnitt **Compute** oder im Abschnitt **Notebooks**, wenn Sie bereit sind, eines Ihrer Notebooks auszuführen.

Sie können auch eine Instanz
* direkt in der [integrierten Notebookumgebung](tutorial-train-models-with-aml.md#azure) erstellen.
* Im Azure-Portal
* Über eine Azure Resource Manager-Vorlage. Eine Beispielvorlage finden Sie unter [Erstellen einer Azure Machine Learning Compute-Instanzvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.machinelearningservices/machine-learning-compute-create-computeinstance).
* Mit dem [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Über die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

Das Kontingent dedizierter Kerne pro Region pro VM-Familie und gesamte regionale Kontingent, das für die Erstellung von Compute-Instanzen gilt, ist einheitlich und wird mit dem Kontingent für Azure Machine Learning-Trainingcomputecluster gemeinsam genutzt. Das Beenden der Compute-Instanz gibt keine Kontingente frei, um sicherzustellen, dass Sie die Compute-Instanz erneut starten können. Beenden Sie die Compute-Instanz nicht über das Betriebssystemterminal, indem Sie ein „sudo shutdown“ durchführen.

Die Compute-Instanz verfügt über einen P10-Betriebssystemdatenträger. Der Typ des temporären Datenträgers hängt von der ausgewählten VM-Größe ab. Derzeit ist es nicht möglich, den Betriebssystemdatenträgertyp zu ändern.


## <a name="compute-target"></a>Computeziel

Compute-Instanzen können als [Trainingscomputeziele](concept-compute-target.md#train) verwendet werden, ähnlich wie Azure Machine Learning-Computetrainingscluster.

Folgendes gilt für eine Compute-Instanz:
* Sie verfügt über eine Auftragswarteschlange.
* Sie führt Aufträge sicher in einer virtuellen Netzwerkumgebung aus, ohne dass Unternehmen hierfür SSH-Ports öffnen müssen. Der Auftrag wird in einer Containerumgebung ausgeführt und packt die Abhängigkeiten Ihres Modells in einen Docker-Container.
* Sie kann mehrere kleine Aufträge parallel ausführen (Vorschauversion).  Zwei Aufträge pro Kern können parallel ausgeführt werden, während die restlichen Aufträge in die Warteschlange eingereiht werden.
* Unterstützt [verteilte Trainingsaufträge](how-to-train-distributed-gpu.md) mit einem einzelnen Knoten mit mehreren GPUs

Sie können die Compute-Instanz als gefolgertes lokales Bereitstellungsziel für Test-/Debugszenarien verwenden.

> [!TIP]
> Die Compute-Instanz verfügt über einen 120 GB Betriebssystemdatenträger. Wenn kein Speicherplatz mehr verfügbar ist und Sie in einen nicht arbeitsfähigen Zustand geraten, löschen Sie mindestens 5 GB Speicherplatz auf dem Betriebssystemdatenträger (in „/“ eingebunden) über das Computeinstanz-Terminal, indem Sie Dateien bzw. Ordner entfernen und anschließend `sudo reboot` ausführen. Um auf das Terminal zuzugreifen, wechseln Sie zur Seite „Computeliste“ oder zur Seite „Computeinstanz-Details“, und klicken Sie auf den Link **Terminal**. Sie können den verfügbaren Speicherplatz auf dem Datenträger überprüfen, indem Sie `df -h` im Terminal ausführen. Löschen Sie mindestens 5 GB Speicherplatz, bevor Sie `sudo reboot` ausführen. Beenden oder starten Sie die Computeinstanz erst dann über Studio neu, wenn 5 GB Speicherplatz gelöscht wurde.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Verwalten einer Compute-Instanz](how-to-create-manage-compute-instance.md)
* [Tutorial: Trainieren Ihres ersten ML-Modells](tutorial-1st-experiment-sdk-train.md) zeigt, wie eine Compute-Instanz mit einem integrierten Notebook verwendet wird.
