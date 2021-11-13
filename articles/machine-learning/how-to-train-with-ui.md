---
title: Erstellen eines Trainingsauftrags mit der Benutzeroberfläche für die Auftragserstellung
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die Benutzeroberfläche für die Auftragserstellung in Azure Machine Learning Studio verwenden, um einen Trainingsauftrag zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devplatv2
author: wenxwei
ms.author: wenxwei
ms.date: 10/21/2021
ms.reviewer: laobri
ms.openlocfilehash: a626c789737b8af53c18946450c9be7ee5f91176
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562008"
---
# <a name="create-a-training-job-with-the-job-creation-ui-preview"></a>Erstellen eines Trainingsauftrags mit der Benutzeroberfläche für die Auftragserstellung (Vorschau)

Es gibt viele Möglichkeiten zum Erstellen eines Trainingsauftrags mit Azure Machine Learning. Sie können die Befehlszeilenschnittstelle (siehe [Trainieren von Modellen (Erstellen von Aufträgen) mit der CLI (v2) (Vorschau)](how-to-train-cli.md)), die REST-API (siehe [Trainieren von Modellen mit REST (Vorschau)](how-to-train-with-rest.md)) oder die Benutzeroberfläche verwenden, um direkt einen Trainingsauftrag zu erstellen. In diesem Artikel erfahren Sie, wie Sie Ihre eigenen Daten und Ihren eigenen Code verwenden, um ein Machine Learning-Modell mit der Benutzeroberfläche für die Auftragserstellung in Azure Machine Learning Studio zu trainieren.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) noch heute aus.

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md). 

* Grundlegende Informationen zur Bedeutung eines Auftrags in Azure Machine Learning. Siehe [Wie man Modelle mit der CLI (v2) trainiert ](how-to-train-cli.md).

## <a name="get-started"></a>Erste Schritte

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com) an. 

1. Wählen Sie Ihr Abonnement und Ihren Arbeitsbereich aus.
 
* Sie können über die Homepage zur Benutzeroberfläche für die Auftragserstellung wechseln. Klicken Sie auf **Neu erstellen**, und wählen Sie dann **Auftrag** aus. 
[![Azure Machine Learning Studio: Homepage](media/how-to-train-with-ui/home-entry.png)](media/how-to-train-with-ui/home-entry.png)

* Oder Sie können über den linken Bereich zur Auftragserstellung wechseln. Klicken Sie auf **+Neu**, und wählen Sie dann **Auftrag** aus. 
[![Azure Machine Learning Studio: Linker Navigationsbereich](media/how-to-train-with-ui/left-nav-entry.png)](media/how-to-train-with-ui/left-nav-entry.png)

* Wenn Sie sich auf der Seite „Experiment“ befinden, können Sie auch zur Registerkarte **Alle Ausführungen** wechseln und auf **Auftrag erstellen** klicken. 
[![Seiteneintrag „Experiment“ für die Benutzeroberfläche zur Auftragserstellung](media/how-to-train-with-ui/experiment-entry.png)](media/how-to-train-with-ui/experiment-entry.png)

Über diese Optionen gelangen Sie zum Panel für die Auftragserstellung, das einen Assistenten für die Konfiguration und Erstellung von Trainingsaufträgen enthält. 

## <a name="select-compute-resources"></a>Auswählen von Computeressourcen

Der erste Schritt auf der Benutzeroberfläche für die Auftragserstellung besteht im Auswählen des Computeziels, auf dem Ihr Auftrag ausgeführt werden soll. Die Benutzeroberfläche für die Auftragserstellung unterstützt mehrere Computetypen:

| Computetyp | Einführung | 
| --- | --- | 
| Compute-Instanz | [Was ist eine Azure Machine Learning-Compute-Instanz?](concept-compute-instance.md) | 
| Computecluster | [Was ist ein Computecluster?](how-to-create-attach-compute-cluster.md#what-is-a-compute-cluster) | 
| Angefügter Kubernetes-Cluster | [Konfigurieren des maschinellen Lernens mit Azure Arc-Unterstützung (Vorschauversion)](how-to-attach-arc-kubernetes.md). | 

1. Auswählen eines Computetyps
1. Wählen Sie eine vorhandene Computeressource aus. In der Dropdownliste werden die Knoteninformationen und der SKU-Typ angezeigt, um Ihnen die Auswahl zu erleichtern.
1. Für einen Computecluster oder einen Kubernetes-Cluster können Sie auch angeben, wie viele Knoten Sie für den Auftrag in **Anzahl der Instanzen** wünschen. Die Standardanzahl von Instanzen beträgt 1. 
1. Wenn Sie mit Ihrer Auswahl zufrieden sind, wählen Sie **Weiter** aus. 
 [![Auswählen eines Computeclusters](media/how-to-train-with-ui/compute-cluster.png)](media/how-to-train-with-ui/compute-cluster.png)

Wenn Sie zum ersten Mal Azure Machine Learning verwenden, werden eine leere Liste und ein Link zum Erstellen einer neuen Compute-Instanz angezeigt. 

 [![Erstellen einer neuen Compute-Instanz](media/how-to-train-with-ui/create-new-compute.png)](media/how-to-train-with-ui/create-new-compute.png)

Weitere Informationen zum Erstellen der verschiedenen Typen finden Sie unter:

| Computetyp | Vorgehensweise | 
| --- | --- | 
| Compute-Instanz | [Erstellen und Verwalten einer Azure Machine Learning-Compute-Instanz](how-to-create-manage-compute-instance.md) | 
| Computecluster | [Erstellen eines Computeclusters für Azure Machine Learning](how-to-create-attach-compute-cluster.md) | 
| Angefügter Kubernetes-Cluster | [Anfügen eines Kubernetes-Clusters mit Azure Arc-Unterstützung](how-to-attach-arc-kubernetes.md) | 

## <a name="specify-the-necessary-environment"></a>Angeben der erforderlichen Umgebung

Nachdem Sie ein Computeziel ausgewählt haben, müssen Sie die Laufzeitumgebung für Ihren Auftrag angeben. Die Benutzeroberfläche für die Auftragserstellung unterstützt drei Arten von Umgebungen:

* Zusammengestellte Umgebungen
* Benutzerdefinierte Umgebungen
* Containerregistrierungsimage 

### <a name="curated-environments"></a>Zusammengestellte Umgebungen

Zusammengestellte Umgebungen sind von Azure definierte Sammlungen von Python-Paketen, die in gängigen ML-Workloads verwendet werden. Zusammengestellte Umgebungen sind in Ihrem Arbeitsbereich standardmäßig verfügbar. Diese Umgebungen werden durch zwischengespeicherte Docker-Images unterstützt, wodurch der Mehraufwand für die Vorbereitung der Ausführung reduziert wird. Die auf der Seite „Zusammengestellte Umgebungen“ angezeigten Karten enthalten Details zu den einzelnen Umgebungen. Weitere Informationen finden Sie unter [Azure Machine Learning – zusammengestellte Umgebungen](resource-curated-environments.md).

 [![Zusammengestellte Umgebungen](media/how-to-train-with-ui/curated-env.png)](media/how-to-train-with-ui/curated-env.png)

### <a name="custom-environments"></a>Benutzerdefinierte Umgebungen

Benutzerdefinierte Umgebungen sind Umgebungen, die Sie selbst angegeben haben. Sie können eine Umgebung angeben oder eine Umgebung wiederverwenden, die Sie bereits erstellt haben. Weitere Informationen finden Sie unter [Verwalten von Softwareumgebungen in Azure Machine Learning Studio (Vorschau)](how-to-manage-environments-in-studio.md#create-an-environment). 

### <a name="container-registry-image"></a>Containerregistrierungsimage

Wenn Sie nicht die von Azure Machine Learning zusammengestellten Umgebungen verwenden oder Ihre eigene benutzerdefinierte Umgebung angeben möchten, können Sie ein Docker-Image von einer öffentlichen Containerregistrierung wie [Docker Hub](https://hub.docker.com/) verwenden. Wenn sich das Image in einem privaten Container befindet, schalten Sie **Dies ist eine private Containerregistrierung** um. Für private Registrierungen müssen Sie einen gültigen Benutzernamen und ein Kennwort eingeben, sodass Azure das Image abrufen kann. 
[![Containerregistrierungsimage](media/how-to-train-with-ui/container-registry-image.png)](media/how-to-train-with-ui/container-registry-image.png)

## <a name="configure-your-job"></a>Konfigurieren des Auftrags

Nachdem Sie die Umgebung angegeben haben, können Sie Ihren Auftrag mit weiteren Einstellungen konfigurieren. 

|Feld| BESCHREIBUNG|
|------| ------|
|Auftragsname| Das Feld „Auftragsname“ wird verwendet, um Ihren Auftrag eindeutig zu identifizieren. Es wird auch als Anzeigename für Ihren Auftrag verwendet. Das Festlegen dieses Felds ist optional. Azure generiert einen GUID-Namen für den Auftrag, wenn Sie keine Eingabe vornehmen. Hinweis: Der Auftragsname muss eindeutig sein.|
|Experimentname| Dies hilft beim Organisieren des Auftrags in Azure Machine Learning Studio. Die Ausführungsaufzeichnung jedes Auftrags wird unter dem entsprechenden Experiment auf der Registerkarte „Experiment“ des Studios organisiert. Standardmäßig ordnet Azure den Auftrag dem Experiment **Standard** zu.|
|Code| Sie können eine Codedatei oder einen Ordner von Ihrem Computer hochladen oder eine Codedatei aus dem Standardblobspeicher des Arbeitsbereichs hochladen. Azure zeigt die Dateien an, die hochgeladen werden sollen, nachdem Sie die Auswahl getroffen haben. |
|Befehl| Den auszuführenden Befehl. Befehlszeilenargumente können explizit in den Befehl geschrieben oder mithilfe von geschweiften Klammern aus anderen Abschnitten abgeleitet werden, insbesondere **Eingaben**, die die Notation mit geschweiften Klammern verwenden, wie im nächsten Abschnitt beschrieben.|
|Eingaben| Geben Sie die Eingabebindung an. Wir unterstützen drei Arten von Eingaben: 1) Registriertes Azure Machine Learning-Dataset 2) Standardblobspeicher des Arbeitsbereichs 3) Hochladen einer lokalen Datei. Sie können mehrere Eingaben hinzufügen. |
|Umgebungsvariablen| Durch das Festlegen von Umgebungsvariablen können Sie eine dynamische Konfiguration des Auftrags bereitstellen. Sie können die Variable und den Wert hier hinzufügen.|
|Tags| Fügen Sie Ihrem Auftrag Tags hinzu, um die Organisation zu erleichtern.|

### <a name="specify-code-and-inputs-in-the-command-box"></a>Angeben von Code und Eingaben im Befehlsfeld

#### <a name="code"></a>Code

Der Befehl wird im Stammverzeichnis des hochgeladenen Codeordners ausgeführt. Nachdem Sie die Codedatei oder den Ordner ausgewählt haben, werden die hochzuladenden Dateien angezeigt. Kopieren Sie den relativen Pfad zu dem Code, der Ihren Einstiegspunkt enthält, und fügen Sie ihn in das Feld **Enter the command to start the job** (Befehl zum Starten des Auftrags eingeben) ein. 

Wenn sich der Code im Stammverzeichnis befindet, können Sie direkt im Befehl darauf verweisen. Beispielsweise `python main.py`.

Wenn sich der Code nicht im Stammverzeichnis befindet, sollten Sie den relativen Pfad verwenden. Die Struktur des [Wort-Sprachmodells](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/train/pytorch/word-language-model) ist z. B. wie folgt:

```tree
.
├── job.yml
├── data
└── src
    └── main.py
```
Hier befindet sich der Quellcode im Unterverzeichnis `src`. Der Befehl würde `python ./src/main.py` lauten (sowie andere Befehlszeilenargumente).

[![Verweisen auf Code im Befehl](media/how-to-train-with-ui/code-command.png)](media/how-to-train-with-ui/code-command.png)

#### <a name="inputs"></a>Eingaben

Es gibt zwei Möglichkeiten, die Eingabebindung vorzunehmen: 

* Eingabename: Wenn Sie eine Eingabe im Befehl verwenden, müssen Sie den Eingabenamen angeben. Um eine Eingabevariable anzugeben, verwenden Sie das Format `{inputs.input_name}`. Beispielsweise `{inputs.wiki}`. Sie können dann im Befehl darauf verweisen, z. B. `--data {inputs.wiki}`.
[![Verweisen auf den Eingabenamen im Befehl](media/how-to-train-with-ui/input-command-name.png)](media/how-to-train-with-ui/input-command-name.png)

* Pfad: Sie können `--data .path` verwenden, um einen Cloudstandort anzugeben. Der Pfad ist das, was Sie in das Feld **Path on compute** (Pfad für Compute) eingeben.
[![Verweisen auf den Eingabepfad im Befehl](media/how-to-train-with-ui/input-command-path.png)](media/how-to-train-with-ui/input-command-path.png)

>[!NOTE] 
>In **command to start the job** (Befehl zum Starten des Auftrags) müssen Sie einen Punkt zum Wert für **Path on compute** (Pfad für Compute) hinzufügen. Beispielsweise wird `/data/wikitext-2` zu `./data/wikitext-2`.

## <a name="review-and-create"></a>Überprüfen und Erstellen 

Sobald Sie Ihren Auftrag konfiguriert haben, wählen Sie **Weiter** aus, um zur Seite für die **Überprüfung** zu gelangen. Um eine Einstellung zu ändern, wählen Sie das Stiftsymbol aus und nehmen die Änderung vor. 

Sie können die **YAML-Spezifikation anzeigen** lassen, um die von dieser Auftragskonfiguration generierte YAML-Datei zu überprüfen und herunterzuladen. Diese YAML-Auftragsdatei kann verwendet werden, um den Auftrag über die CLI (v2) zu übermitteln. (Weitere Informationen finden Sie unter [Trainieren von Modellen (Erstellen von Aufträgen) mit der CLI (v2) (Vorschau)](how-to-train-cli.md).) [![Anzeigen der YAML-Spezifikation](media/how-to-train-with-ui/view-yaml.png)](media/how-to-train-with-ui/view-yaml.png)
[![YAML-Spezifikation](media/how-to-train-with-ui/yaml-spec.png)](media/how-to-train-with-ui/yaml-spec.png)

Wählen Sie **Erstellen** aus, um den Auftrag zu starten. Nachdem der Auftrag erstellt wurde, zeigt Azure die Seite mit den Ausführungsdetails an, auf der Sie Ihren Trainingsauftrag überwachen und verwalten können. 

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen und Bewerten eines Machine Learning-Modells mit einem verwalteten Onlineendpunkt (Vorschau)](how-to-deploy-managed-online-endpoints.md)

* [Trainieren von Modellen (Erstellen von Aufträgen) mit der CLI (v2) (Vorschau)](how-to-train-cli.md)
