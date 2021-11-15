---
title: 'Schnellstart: Erstellen von Arbeitsbereichsressourcen'
titleSuffix: Azure Machine Learning
description: Erstellen Sie einen Azure Machine Learning Arbeitsbereich und Cloudressourcen, die für das Training von Machine Learning-Modellen verwendet werden können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 10/21/2021
adobe-target: true
ms.custom: FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 749400cc3f5c798b16dc08485f0cbff14cb276e7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556945"
---
# <a name="quickstart-create-workspace-resources-you-need-to-get-started-with-azure-machine-learning"></a>Schnellstart: So erstellen Sie Arbeitsbereichsressourcen, die Sie für die ersten Schritte mit Azure Machine Learning benötigen

In dieser Schnellstartanleitung erstellen Sie einen Arbeitsbereich und fügen dem Arbeitsbereich dann die Computeressourcen hinzu. Anschließend verfügen Sie über alles, was Sie für die ersten Schritte mit Azure Machine Learning benötigen.  

Der Arbeitsbereich ist die Ressource der obersten Ebene für Ihre Aktivitäten des maschinellen Lernens und ein zentraler Ort für das Anzeigen und Verwalten der Artefakte, die Sie während der Nutzung von Azure Machine Learning erstellen. Die Computeressourcen bieten eine vorkonfigurierte cloudbasierte Umgebung, die Ihnen das Trainieren, Bereitstellen, Automatisieren, Verwalten und Nachverfolgen von Machine Learning-Modellen ermöglicht.


## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-workspace"></a>Erstellen des Arbeitsbereichs

Wenn Sie bereits über einen Arbeitsbereich verfügen, überspringen Sie diesen Abschnitt, und fahren Sie mit dem [Erstellen einer Compute-Instanz](#instance) fort.

Wenn Sie noch keinen Arbeitsbereich haben, erstellen Sie jetzt einen Arbeitsbereich:

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="create-compute-instance"></a><a name="instance"></a> Erstellen einer Compute-Instanz

Sie können Azure Machine Learning auf Ihrem eigenen Computer installieren.  In diesem Schnellstart erstellen Sie jedoch eine Onlinecomputeressource, auf der bereits eine Entwicklungsumgebung installiert ist und die einsatzbereit ist.  Sie verwenden diesen Onlinecomputer (eine *Compute-Instanz*) für Ihre Entwicklungsumgebung, um Code in Python-Skripts und Jupyter Notebook-Instanzen zu schreiben und auszuführen.

Erstellen Sie eine *Compute-Instanz*, um diese Entwicklungsumgebung für die restlichen Tutorials und Schnellstarts zu verwenden:

1. Wenn Sie im vorherigen Abschnitt nicht **Zu Arbeitsbereich wechseln** ausgewählt haben, melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com) an, und wählen Sie Ihren Arbeitsbereich aus.
1. Wählen Sie auf der linken Seite **Compute** aus.
1. Wählen Sie **+Neu** aus, um eine neue Compute-Instanz zu erstellen.
1. Geben Sie einen Namen an. Behalten Sie alle Standardwerte auf der ersten Seite bei.
1. Klicken Sie auf **Erstellen**.
 
In etwa zwei Minuten sehen Sie, dass sich der **Status** der Compute-Instanz von *Erstellen...* in *Wird ausgeführt.* ändert.  Sie kann nun verwendet werden.  

## <a name="create-compute-clusters"></a><a name="cluster"></a> Erstellen von Computeclustern

Als Nächstes erstellen Sie einen Computecluster.  Sie können Cluster verwenden, um einen Trainings- oder Batchrückschlussprozess in einem Cluster von CPU- oder GPU-Serverknoten in der Cloud zu verteilen.

Erstellen Sie einen Computecluster, der automatisch zwischen null und vier Knoten skaliert wird:

1. Wählen Sie auf der oberen Registerkarte im Abschnitt **Compute** die Option **Computecluster** aus.
1. Wählen Sie **+Neu** aus, um einen neuen Computecluster zu erstellen.
1. Behalten Sie alle Standardwerte auf der ersten Seite bei, und wählen Sie **Weiter** aus.
1. Nennen Sie den Cluster **cpu-cluster**.  Wenn dieser Name bereits vorhanden ist, fügen Sie dem Namen Ihre Initialen hinzu, um ihn eindeutig zu machen.
1. Behalten Sie für die **Mindestanzahl von Knoten** die Einstellung „0“ bei.
1. Ändern Sie ggf. die **maximale Knotenanzahl** in 4.  Abhängig von Ihren Einstellungen gilt unter Umständen ein niedrigerer Grenzwert.
1. Ändern Sie **Leerlauf in Sekunden, bevor ein Herunterskalieren erforderlich ist** in „2400“.
1. Übernehmen Sie die übrigen Standardeinstellungen, und wählen Sie **Erstellen** aus.

In weniger als einer Minute ändert sich der **Status** des Clusters von *Erstellen...* in *Erfolgreich*.  In der Liste wird der bereitgestellte Computecluster zusammen mit der Anzahl der Knoten im Leerlauf, der ausgelasteten Knoten und der nicht bereitgestellten Knoten angezeigt.  Da Sie den Cluster noch nicht verwendet haben, sind alle Knoten derzeit nicht bereitgestellt. 

> [!NOTE]
> Nachdem der Cluster erstellt wurde, weist er keine bereitgestellten Knoten auf. Der Cluster verursacht *keine* Kosten, bis Sie dafür einen Auftrag übermitteln. Der Cluster wird herunterskaliert, nachdem er sich 2.400 Sekunden (40 Minuten) im Leerlauf befunden hat.  Dies gibt Ihnen Zeit, ihn ggf. in einigen Tutorials zu verwenden, ohne darauf warten zu müssen, dass er wieder hochskaliert wird.

## <a name="quick-tour-of-the-studio"></a><a name="studio"></a> Überblick über Studio

Studio ist Ihr Webportal für Azure Machine Learning. In diesem Portal werden codefreie Umgebungen und Code First-Umgebungen zu einer inklusiven Data Science-Plattform kombiniert.

Überprüfen Sie auf der linken Navigationsleiste die Teile von Studio:

* Der Abschnitt **Autor** von Studio enthält mehrere Möglichkeiten für den Einstieg in die Erstellung von Machine Learning-Modellen.  Sie haben folgende Möglichkeiten:

    * Im Abschnitt **Notebooks** können Sie Jupyter Notebook-Instanzen erstellen, Beispiel-Notebooks kopieren und Notebooks und Python-Skripts ausführen.
    * **Automatisiertes ML** ermöglicht die schrittweise Erstellung eines Machine Learning-Modells, ohne Code zu schreiben.
    * Der **Designer** bietet Ihnen eine Drag & Drop-Option zum Erstellen von Modellen mithilfe vordefinierter Komponenten.

* Im Abschnitt **Ressourcen** von Studio können Sie die Ressourcen nachverfolgen, die Sie beim Ausführen Ihrer Aufträge erstellen.  Wenn Sie über einen neuen Arbeitsbereich verfügen, enthalten diese Abschnitte noch keine Elemente.

* Sie haben bereits den Abschnitt **Verwalten** von Studio verwendet, um Ihre Computeressourcen zu erstellen.  In diesem Abschnitt können Sie auch Daten und externe Dienste erstellen und verwalten, die Sie mit Ihrem Arbeitsbereich verknüpfen.  

### <a name="workspace-diagnostics"></a>Arbeitsbereichsdiagnose

[!INCLUDE [machine-learning-workspace-diagnostics](../../includes/machine-learning-workspace-diagnostics.md)]

## <a name="clean-up-resources"></a><a name="clean-up"></a>Bereinigen von Ressourcen

Wenn Sie nun mit dem nächsten Tutorial fortfahren möchten, wechseln Sie zu [Nächste Schritte](#next-steps).

### <a name="stop-compute-instance"></a>Beenden der Compute-Instanz

Wenn Sie die Compute-Instanz jetzt nicht verwenden möchten, beenden Sie sie:

1. Wählen Sie in Studio auf der linken Seite **Compute** aus.
1. Wählen Sie auf den oberen Registerkarten **Compute-Instanzen** aus.
1. Wählen Sie in der Liste die Compute-Instanz aus.
1. Wählen Sie auf der oberen Symbolleiste **Beenden** aus.

### <a name="delete-all-resources"></a>Löschen aller Ressourcen

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie verfügen nun über einen Azure Machine Learning-Arbeitsbereich, der Folgendes enthält:

- Eine Compute-Instanz, die für Ihre Entwicklungsumgebung verwendet werden soll
- Einen Computecluster, der zum Übermitteln von Trainingsausführungen verwendet werden soll.

Verwenden Sie die folgenden Ressourcen, um mehr über Azure Machine Learning zu erfahren und ein Modell mit Python-Skripts zu trainieren:

> [!div class="nextstepaction"]
> [Tutorial: Ausführen eines Python-Skripts „Hello World!“ (Teil 2 von 4)](tutorial-1st-experiment-hello-world.md)
>
