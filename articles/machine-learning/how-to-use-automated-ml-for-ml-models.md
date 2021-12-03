---
title: Einrichten des automatisierten maschinellen Lernens mit der Studio-Benutzeroberfläche
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Trainingsausführungen für automatisiertes maschinelles Lernen ohne eine einzige Codezeile in Azure Machine Learning Studio für Azure Machine Learning einrichten.
services: machine-learning
ms.service: machine-learning
ms.subservice: automl
ms.author: nibaccam
author: cartacioS
ms.reviewer: nibaccam
ms.date: 11/15/2021
ms.topic: how-to
ms.custom: automl, FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: d4c4188a04db444a153577ead82d79f32c9b137d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518243"
---
# <a name="set-up-no-code-automl-training-with-the-studio-ui"></a>Einrichten des Trainings für automatisiertes maschinelles Lernen ohne Code über die Studio-Benutzeroberfläche 

In diesem Artikel erfahren Sie, wie Sie Trainingsausführungen für automatisiertes maschinelles Lernen ohne eine einzige Codezeile in [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md) für Azure Machine Learning einrichten.

Automatisiertes maschinelles Lernen (AutoML) ist ein Prozess, bei dem der beste Algorithmus für das maschinelle Lernen anhand Ihrer spezifischen Daten für Sie ausgewählt wird. Dieser Prozess ermöglicht Ihnen die schnelle Erstellung von Machine Learning-Modellen. [Weitere Informationen zur Implementierung des automatisierten maschinellen Lernens in Azure Machine Learning.](concept-automated-ml.md)
 
Ein umfassendes Beispiel finden Sie im [Tutorial: Trainieren eines Klassifizierungsmodells mit AutoML ohne Schreiben von Code in Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md). 

Wenn Sie eine auf Python-Code basierende Umgebung bevorzugen, [konfigurieren Sie Ihre Experimenten mit automatisiertem maschinellem Lernen](how-to-configure-auto-train.md) mit dem Azure Machine Learning SDK.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Ein Azure Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md). 

## <a name="get-started"></a>Erste Schritte

1. Melden Sie sich bei [Azure Machine Learning Studio](https://ml.azure.com) an. 

1. Wählen Sie Ihr Abonnement und Ihren Arbeitsbereich aus. 

1. Navigieren Sie zum linken Bereich. Wählen Sie im Abschnitt **Erstellen** die Option **Automatisiertes ML** aus.

[![Navigationsbereich von Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Wenn Sie zum ersten Mal Experimente ausführen, werden eine leere Liste und Links zur Dokumentation angezeigt. 

Andernfalls wird eine Liste Ihrer letzten Experimente für automatisiertes maschinelles Lernen angezeigt, einschließlich derjenigen, die mit dem SDK erstellt wurden. 

## <a name="create-and-run-experiment"></a>Erstellen und Ausführen eines Experiments

1. Wählen Sie **+ Neue Ausführung von automatisiertem ML** aus, und füllen Sie das Formular aus.

1. Wählen Sie ein Dataset in Ihrem Speichercontainer aus, oder erstellen Sie ein neues Dataset. Datasets können aus lokalen Dateien, Web-URLs, Datenspeichern oder Azure Open Datasets erstellt werden. Weitere Informationen zur Erstellung von Datasets finden Sie [hier](how-to-create-register-datasets.md).  

    >[!Important]
    > Anforderungen für Trainingsdaten:
    >* Die Daten müssen in Tabellenform vorliegen.
    >* Der Wert, den Sie vorhersagen möchten (Zielspalte), muss in den Daten vorhanden sein.

    1. Um ein neues Dataset aus einer Datei auf Ihrem lokalen Computer zu erstellen, wählen Sie **+Dataset erstellen** und anschließend **Aus lokaler Datei** aus. 

    1. Geben Sie Ihrem Dataset im Formular **Grundlegende Informationen** einen eindeutigen Namen, und geben Sie optional eine Beschreibung an. 

    1. Wählen Sie **Weiter** aus, um das Formular **Datenspeicher- und Dateiauswahl** zu öffnen. In diesem Formular wählen Sie aus, wo das Dataset hochgeladen werden soll. Dies kann der Standardspeichercontainer sein, der automatisch mit Ihrem Arbeitsbereich erstellt wird, oder ein Speichercontainer, den Sie für das Experiment auswählen. 
    
        1. Wenn sich Ihre Daten hinter einem virtuellen Netzwerk befinden, müssen Sie die Funktion zum **Überspringen der Überprüfung** aktivieren, um sicherzustellen, dass der Arbeitsbereich auf Ihre Daten zugreifen kann. Weitere Informationen finden Sie unter [Verwenden von Azure Machine Learning Studio in einem virtuellen Azure-Netzwerk](how-to-enable-studio-virtual-network.md). 
    
    1. Wählen Sie **Durchsuchen** aus, um die Datendatei für das Dataset hochzuladen. 

    1. Überprüfen Sie das Formular **Einstellungen und Vorschau** auf Genauigkeit. Das Formular wird ausgehend vom Dateityp intelligent aufgefüllt. 

        Feld| BESCHREIBUNG
        ----|----
        Dateiformat| Definiert das Layout und den Typ der in einer Datei gespeicherten Daten.
        Trennzeichen| Mindestens ein Zeichen zum Angeben der Grenze zwischen separaten, unabhängigen Regionen in Nur-Text- oder anderen Datenströmen.
        Codieren| Gibt an, welche Bit-zu-Zeichen-Schematabelle verwendet werden soll, um Ihr Dataset zu lesen.
        Spaltenüberschriften| Gibt an, wie die Header des Datasets, sofern vorhanden, behandelt werden.
        Zeilen überspringen | Gibt an, wie viele Zeilen im Dataset übersprungen werden.
    
        Wählen Sie **Weiter** aus.

    1. Das Formular **Schema** wird auf intelligente Weise entsprechend den auf dem Formular **Einstellungen und Vorschau** ausgewählten Optionen aufgefüllt. Konfigurieren Sie hier den Datentyp für jede Spalte, überprüfen Sie die Spaltennamen, und wählen Sie für die Spalten, die nicht in Ihr Experiment eingeschlossen werden sollen, die Option **Nicht einschließen** aus. 
            
        Wählen Sie **Weiter** aus.

    1. Das Formular **Details bestätigen** ist eine Zusammenfassung der Informationen, die zuvor in die Formulare **Grundlegende Infos** und **Einstellungen und Vorschau** eingetragen wurden. Sie haben auch die Möglichkeit, ein Datenprofil für Ihr Dataset zu erstellen, indem Sie Compute mit aktivierter Profilerstellung verwenden. Weitere Informationen zur [Datenprofilerstellung](how-to-connect-data-ui.md#profile).

        Wählen Sie **Weiter** aus.
1. Wählen Sie Ihr neu erstelltes Dataset aus, sobald es angezeigt wird. Sie können auch eine Vorschau des Datasets und der Stichprobenstatistiken anzeigen. 

1. Wählen Sie im Formular zum **Konfigurieren der Ausführung** die Option **Neu erstellen** aus, und geben Sie als Experimentnamen **Tutorial-automl-deploy** ein.

1. Wählen Sie eine Zielspalte aus: Dies ist die Spalte, für die Sie Vorhersagen ausführen möchten.

1. Wählen Sie einen Computetyp für den Datenprofilerstellungs- und Trainingsauftrag aus. Sie können einen [Computecluster](concept-compute-target.md#azure-machine-learning-compute-managed) oder eine [Compute-Instanz](concept-compute-instance.md) auswählen. 
    
1. Wählen Sie in der Dropdownliste Ihrer vorhandenen Computeressourcen eine Computeressource aus.  Folgen Sie den Anweisungen in Schritt 8, um eine neue Computeressource zu erstellen.

1. Wählen Sie **Create a new compute** (Neue Computeressource erstellen) aus, um den Computekontext für dieses Experiment zu konfigurieren.

    Feld|BESCHREIBUNG
    ---|---
    Computename| Geben Sie einen eindeutigen Namen ein, der Ihren Computekontext identifiziert.
    VM-Priorität| Virtuelle Computer mit niedriger Priorität sind kostengünstiger, bieten jedoch keine garantierten Computeknoten. 
    Typ des virtuellen Computers| Wählen Sie CPU oder GPU als VM-Typ aus.
    Größe des virtuellen Computers| Wählen Sie die Größe für Ihren Computes aus.
    Min/Max nodes (Min./Max. Knoten)| Um ein Datenprofil zu erstellen, müssen Sie mindestens einen Knoten angeben. Geben Sie die maximale Anzahl von Knoten für Ihren Compute ein. Der Standardwert ist 6 Knoten für einen AML-Compute.
    Erweiterte Einstellungen | Mit diesen Einstellungen können Sie ein Benutzerkonto und ein vorhandenes virtuelles Netzwerk für Ihr Experiment konfigurieren. 
    
    Klicken Sie auf **Erstellen**. Das Erstellen einer neuen Computeressource kann einige Minuten dauern.

    >[!NOTE]
    > Ihr Computename gibt an, ob für den von Ihnen ausgewählten/erstellten Compute *Profilerstellung aktiviert* ist. (Weitere Informationen finden Sie im Abschnitt [Datenprofilerstellung](how-to-connect-data-ui.md#profile).)

    Wählen Sie **Weiter** aus.

1. Wählen Sie auf dem Formular **Aufgabentyp und Einstellungen** den Aufgabentyp aus: Klassifizierung, Regression oder Prognose (Vorhersage). Weitere Informationen finden Sie unter [Unterstützte Aufgabentypen](concept-automated-ml.md#when-to-use-automl-classification-regression-forecasting--computer-vision).

    1. Für die **Klassifizierung** können Sie auch Deep Learning aktivieren.
    
        Wenn Deep Learning aktiviert ist, ist die Überprüfung auf _train_validation split_ beschränkt. [Erfahren Sie mehr zu Überprüfungsoptionen](how-to-configure-cross-validation-data-splits.md).

    1. Für **Vorhersagen** haben Sie folgende Möglichkeiten: 
    
        1. Aktivieren Sie Deep Learning.
    
        1. Auswählen der *Zeitspalte*: Diese Spalte enthält die zu verwendenden Zeitdaten.

        1. Auswählen des *Vorhersagehorizonts*: Geben Sie an, wie viele Zeiteinheiten (Minuten/Stunden/Tage/Wochen/Monate/Jahre) das Modell die Zukunft vorhersagen können soll. Je weiter das Modell in die Zukunft vorausschauen muss, desto ungenauer wird es. [Weitere Informationen zu Vorhersagen und zum Vorhersagehorizont](how-to-auto-train-forecast.md).

1. (Optional:) Anzeigen weiterer Konfigurationseinstellungen: zusätzliche Einstellungen, mit denen Sie den Trainingsauftrag besser steuern können. Andernfalls werden die Standardwerte auf Basis der Experimentauswahl und -daten angewendet. 

    Zusätzliche Konfigurationen|BESCHREIBUNG
    ------|------
    Primary metric (Primäre Metrik)| Die wichtigste Metrik, die für die Bewertung Ihres Modells verwendet wird. [Weitere Informationen zur Modellmetriken](how-to-configure-auto-train.md#primary-metric).
    Explain best model (Bestes Modell erläutern) | Wählen Sie diese Option aus, um die Erläuterungen für das empfohlene beste Modell anzuzeigen oder zu deaktivieren. <br> Diese Funktion ist derzeit nicht für [bestimmte Vorhersagealgorithmen](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model) verfügbar. 
    Blocked algorithm (Blockierter Algorithmus)| Wählen Sie Algorithmen aus, die Sie aus den Trainingsauftrag ausschließen möchten. <br><br> Das Zulassen von Algorithmen ist nur für [SDK-Experimente](how-to-configure-auto-train.md#supported-models) verfügbar. <br> Weitere Informationen finden Sie auf der Seite zu den [unterstützten Modellen für einzelne Aufgabentypen](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels).
    Beendigungskriterium| Wenn eines dieser Kriterien erfüllt ist, wird der Trainingsauftrag beendet. <br> *Training job time (hours)* Trainingsauftragszeit (Stunden): Gibt an, wie lange der Trainingsauftrag ausgeführt werden soll. <br> *Metric score threshold* (Metrischer Bewertungsschwellenwert):  Die Metrikmindestbewertung für alle Pipelines. Auf diese Weise wird sichergestellt, dass Sie nicht mehr Zeit für den Trainingsauftrag aufwenden als nötig, wenn Sie eine definierte Zielmetrik verwenden, die Sie erreichen möchten.
    Parallelität| *Max concurrent iterations* (Maximale Anzahl gleichzeitiger Iterationen): Die maximale Anzahl von Pipelines (Iterationen), die im Trainingsauftrag getestet werden. Der Auftrag wird nicht häufiger als die angegebene Anzahl von Iterationen ausgeführt. Erfahren Sie mehr darüber, wie automatisiertes ML [mehrere untergeordnete Ausführungen in Clustern](how-to-configure-auto-train.md#multiple-child-runs-on-clusters) durchführt.

1. (Optional) Anzeigen von Featurisierungseinstellungen: Wenn Sie im Formular **Additional configuration settings** (Zusätzliche Konfigurationseinstellungen) die Option **Automatische Featurisierung** aktivieren, werden standardmäßige Featurisierungstechniken angewendet. Diese Standardeinstellungen können unter **Featurisierungseinstellungen anzeigen** geändert und entsprechend angepasst werden. Informationen zum Anpassen von Featurisierungen finden Sie [hier](#customize-featurization). 

    ![Der Screenshot zeigt das Dialogfeld zum Auswählen von Aufgabentypen, in dem die Einstellungen für die Anzeige der Featurisierung beschrieben sind.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)


1. Mit dem Formular **[Optional] Validieren und testen** können Sie Folgendes tun. 

    1. Geben Sie die Art der Validierung an, die für Ihren Ausbildungsauftrag verwendet werden soll. [Weitere Informationen zur Kreuzvalidierung](how-to-configure-cross-validation-data-splits.md#prerequisites). 
    
        1. Vorhersageaufgaben unterstützen nur die k-fache Kreuzvalidierung.
    
    1. Stellen Sie einen Testdatensatz (Vorschau) zur Verfügung, um das empfohlene Modell zu bewerten, das automatisierte ML am Ende Ihres Experiments für Sie erstellt. Wenn Sie Testdaten bereitstellen, wird am Ende Ihres Experiments automatisch ein Testlauf ausgelöst. Dieser Testlauf wird nur mit dem besten Modell durchgeführt, das vom automatischen ML empfohlen wurde. Erfahren Sie, wie Sie die [Ergebnisse des Remote-Testlaufs](#view-remote-test-run-results-preview) erhalten.
    
        >[!IMPORTANT]
        > Die Bereitstellung eines Testdatensatzes zur Bewertung der erstellten Modelle ist eine Previewfunktion. Diese Funktion ist eine [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktion, die jederzeit geändert werden kann.

        1. Die Testdaten werden getrennt von den Trainings- und Validierungsdaten betrachtet, um die Ergebnisse des Testlaufs des empfohlenen Modells nicht zu verfälschen. [Mehr über Verzerrungen bei der Modellvalidierung erfahren](concept-automated-ml.md#training-validation-and-test-data).
        1. Sie können entweder Ihren eigenen Testdatensatz zur Verfügung stellen oder sich dafür entscheiden, einen Prozentsatz Ihres Trainingsdatensatzes zu verwenden.          
        1. Das Schema des Testdatensatzes sollte mit dem Trainingsdatensatz übereinstimmen. Die Zielspalte ist optional, aber wenn keine Zielspalte angegeben wird, werden keine Testmetriken berechnet.
        1. Der Testdatensatz sollte nicht derselbe sein wie der Trainingsdatensatz oder der Validierungsdatensatz.
        1. Prognoseläufe unterstützen keine Aufteilung zwischen Training und Test.
        
        ![Der Screenshot zeigt das Formular für die Auswahl der Validierungs- und Testdaten](media/how-to-use-automated-ml-for-ml-models/validate-test-form.png)
        
## <a name="customize-featurization"></a>Anpassen der Featurisierung

Im Formular **Featurisierung** können Sie die automatische Featurisierung aktivieren/deaktivieren und die Einstellungen der automatischen Featurisierung für Ihr Experiment anpassen. Informationen zum Öffnen dieses Formulars finden Sie in Schritt 10 des Abschnitts [Erstellen und Ausführen eines Experiments](#create-and-run-experiment). 

In der folgenden Tabelle sind die derzeit in Studio verfügbaren Anpassungen zusammengefasst: 

Column| Anpassung
---|---
Enthalten | Gibt an, welche Spalten in das Training einbezogen werden sollen.
Featuretyp| Dient zum Ändern des Werttyps für die ausgewählte Spalte.
Imputation mit| Dient zum Auswählen des Werts, mit dem fehlende Werte in Ihren Daten imputiert werden sollen.

![Benutzerdefinierte Featurisierung für Azure Machine Learning Studio](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Ausführen des Experiments und Anzeigen der Ergebnisse

Wählen Sie **Fertig stellen** aus, um das Experiment auszuführen. Der Vorgang zum Vorbereiten eines Experiments kann bis zu 10 Minuten dauern. Ein Ausführen von Trainingsaufträgen kann für jede Pipeline weitere 2 bis 3 Minuten beanspruchen.

> [!NOTE]
> Die Algorithmen, die beim automatisierten maschinellen Lernen eingesetzt werden, weisen eine inhärente Zufälligkeit auf, die zu geringfügigen Abweichungen in der abschließenden metrischen Bewertung eines empfohlenen Modells führen kann, z. B. bei der Genauigkeit. Automatisiertes maschinelles Lernen führt bei Bedarf auch Vorgänge an Daten wie Training-Test-Aufteilung, Training-Validierung-Aufteilung oder Kreuzvalidierung durch. Wenn Sie also ein Experiment mit denselben Konfigurationseinstellungen und derselben primären Metrik mehrmals durchführen, werden Sie aufgrund dieser Faktoren wahrscheinlich bei jedem Experiment eine Abweichung in der abschließenden metrischen Bewertung sehen. 

### <a name="view-experiment-details"></a>Anzeigen von Details zum Experiment

Die Anzeige für **Ausführungsdetails** wird mit der Registerkarte **Details** geöffnet. In dieser Anzeige wird eine Zusammenfassung der Experimentausführung einschließlich einer Statusleiste oben neben der Ausführungsnummer angezeigt. 

Die Registerkarte **Modelle** enthält eine Liste der erstellten Modelle, wobei diese nach der Metrikbewertung (Metrikscore) geordnet sind. Standardmäßig steht das Modell, das anhand der ausgewählten Metrik die höchste Bewertung erhält, in der Liste ganz oben. Während der Trainingsauftrag weitere Modelle testet, werden diese zur Liste hinzugefügt. Verwenden Sie diese Liste, um einen schnellen Vergleich der Metriken für die bisher generierten Modelle zu erhalten.

![Ausführungsdetails](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Anzeigen der Details der Trainingsausführung

Lassen Sie zu jedem abgeschlossenen Modell Detailinformationen zur Trainingsausführung anzeigen. Zeigen Sie auf der Registerkarte **Modell** Details wie eine Modellzusammenfassung und die für das ausgewählte Modell verwendeten Hyperparameter an. 

[![Hyperparameterdetails](media/how-to-use-automated-ml-for-ml-models/hyperparameter-button.png)](media/how-to-use-automated-ml-for-ml-models/hyperparameter-details.png)

 Sie können auch modellspezifische Leistungsmetrikdiagramme auf der Registerkarte **Metriken** anzeigen. [Hier finden Sie weitere Informationen zu Diagrammen.](how-to-understand-automated-ml.md)

![Details zur Iteration](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

Auf der Registerkarte „Datentransformation“ wird ein Diagramm mit der Datenvorverarbeitung, der Featurisierung, den Skalierungstechniken und dem Algorithmus für maschinelles Lernen angezeigt, die zum Generieren dieses Modells angewandt wurden.

>[!IMPORTANT]
> Die Registerkarte „Datentransformation“ befindet sich in der Vorschau. Diese Funktion sollte als [experimentell](/python/api/overview/azure/ml/#stable-vs-experimental) betrachtet werden und kann jederzeit geändert werden.

![Datentransformation](./media/how-to-use-automated-ml-for-ml-models/data-transformation.png)

## <a name="view-remote-test-run-results-preview"></a>Ergebnisse des Remote-Testlaufs anzeigen (Vorschau)

Wenn Sie bei der Einrichtung Ihres Experiments einen Testdatensatz angegeben oder sich für eine Aufteilung in Training und Test entschieden haben - im Formular **Validieren und Testen** -, testet Automated ML standardmäßig automatisch das empfohlene Modell. Infolgedessen berechnet die automatisierte ML Testmetriken, um die Qualität des empfohlenen Modells und seiner Vorhersagen zu bestimmen. 

>[!IMPORTANT]
> Das Testen Ihrer Modelle mit einem Testdatensatz zur Bewertung der generierten Modelle ist eine Previewfunktion. Diese Funktion ist eine [experimentelle](/python/api/overview/azure/ml/#stable-vs-experimental) Previewfunktion, die jederzeit geändert werden kann.

Zur Anzeige der Testlaufmetriken des empfohlenen Modells,
 
1. Navigieren Sie zur Seite **Modelle** und wählen Sie das beste Modell. 
1. Wählen Sie die Registerkarte **Testergebnisse (Vorschau)** . 
1. Wählen Sie den gewünschten Lauf aus, und sehen Sie sich die Registerkarte **Metriken** an.  Registerkarte ![Testergebnisse des automatisch getesteten, empfohlenen Modells](./media/how-to-use-automated-ml-for-ml-models/test-best-model-results.png)
    
So zeigen Sie die Testvorhersagen an, die zur Berechnung der Testmetriken verwendet wurden, 

1. Navigieren Sie zum unteren Ende der Seite und wählen Sie den Link unter **Outputs dataset**, um den Datensatz zu öffnen. 
1. Wählen Sie auf der Seite **Datensätze** die Registerkarte **Durchsuchen**, um die Vorhersagen des Testlaufs zu betrachten.
    1. Alternativ kann die Vorhersagedatei auch auf der Registerkarte **Ausgaben + Protokolle** eingesehen/heruntergeladen werden. Erweitern Sie den Ordner **Vorhersagen**, um Ihre `predicted.csv` Datei zu finden.

Alternativ kann die Vorhersagedatei auch auf der Registerkarte Ausgaben + Protokolle angezeigt/heruntergeladen werden. Erweitern Sie den Ordner Vorhersagen, um die Datei predictions.csv zu finden.

## <a name="test-an-existing-automated-ml-model-preview"></a>Testen Sie ein bestehendes automatisiertes ML-Modell (Vorschau)

Nach Abschluss Ihres Experiments können Sie das/die Modell(e) testen, die das automatisierte ML für Sie erstellt hat. Wenn Sie nicht das empfohlene Modell, sondern ein anderes automatisch generiertes ML-Modell testen möchten, können Sie dies mit den folgenden Schritten tun. 

1. Wählen Sie einen vorhandenen automatisierten ML-Experimentierlauf aus.  
1. Navigieren Sie zur Registerkarte **Modelle** des Laufs und wählen Sie das fertige Modell, das Sie testen möchten.
1. Wählen Sie auf der Seite Modell **Details** die Schaltfläche **Testmodell (Vorschau)** , um die Fensterfläche **Testmodell** zu öffnen.
1. Wählen Sie im Bereich **Testmodell** den Rechencluster und einen Testdatensatz, den Sie für Ihren Testlauf verwenden möchten. 
1. Wählen Sie die Schaltfläche **Test**. Das Schema des Testdatensatzes sollte mit dem Trainingsdatensatz übereinstimmen, aber die **Zielspalte** ist optional.
1. Nach erfolgreicher Erstellung eines Modellprüflaufs wird auf der Seite **Details** eine Erfolgsmeldung angezeigt. Wählen Sie die Registerkarte **Testergebnisse**, um den Fortschritt des Laufs zu sehen.

1. Um die Ergebnisse des Testlaufs zu betrachten, öffnen Sie die Seite **Details** und folgen Sie den Schritten im Abschnitt [Ergebnisse des Remote-Testlaufs anzeigen](#view-remote-test-run-results-preview). 

    ![Testmodell-Formular](./media/how-to-use-automated-ml-for-ml-models/test-model-form.png)
    

## <a name="model-explanations-preview"></a>Modellerklärungen (Vorschau)

Zum besseren Verständnis Ihres Modells können Sie sich auf dem Dashboard für Modellerklärungen ansehen, welche Datenfeatures (Rohdaten oder verarbeitete Daten) die Vorhersagen des Modells beeinflusst haben. 

Das Dashboard für Modellerklärungen enthält eine Gesamtanalyse des trainierten Modells sowie die zugehörigen Vorhersagen und Erklärungen. Außerdem können Sie einen einzelnen Datenpunkt und die Bedeutung der einzelnen Merkmale aufschlüsseln. Weitere Informationen zu den Visualisierungen des Erklärungsdashboards finden Sie [hier](how-to-machine-learning-interpretability-aml.md#visualizations).

So rufen Sie Erklärungen für ein bestimmtes Modell ab: 

1. Wählen Sie auf der Registerkarte **Modelle** das Modell aus, zu dem Sie Informationen wünschen. 
1. Wählen Sie die Schaltfläche **Modell erklären** aus, und geben Sie eine Computeressource zum Generieren der Erklärungen an.
1. Überprüfen Sie den Status auf der Registerkarte **Untergeordnete Ausführungen**. 
1. Navigieren Sie nach Abschluss des Vorgangs zur Registerkarte **Erklärungen (Vorschau)** . Dort finden Sie das Erklärungsdashboard. 

    ![Dashboard für Modellerklärungen](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>Bereitstellen Ihres Modells

Sobald Ihnen das beste Modell vorliegt, ist es an der Zeit, das Modell als Webdienst bereitzustellen, um Vorhersagen für neue Daten zu erstellen.

>[!TIP]
> Wenn Sie ein Modell bereitstellen möchten, das über das `automl`-Paket mit dem Python SDK generiert wird, müssen Sie [Ihr Modell im Arbeitsbereich registrieren](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1). 
>
> Nachdem Sie Ihr Modell registriert haben, navigieren Sie im Studio zu diesem, indem Sie im linken Bereich auf **Modelle** klicken. Sobald Sie Ihr Modell geöffnet haben, können Sie oben auf **Bereitstellen** klicken und dann die Anweisungen ausführen, die unter **Schritt 2** des Abschnitts **Bereitstellen Ihres Modells** beschrieben werden.

Automatisiertes maschinelles Lernen unterstützt Sie dabei, das Modell bereitzustellen, ohne Code zu schreiben:

1. Sie haben einige Optionen für die Bereitstellung. 

    + Option 1: Stellen Sie das beste Modell gemäß den von Ihnen definierten Metrikkriterien bereit. 
        1. Navigieren Sie nach Abschluss des Experiments zur Seite mit der übergeordneten Ausführung, indem Sie oben auf dem Bildschirm **Ausführung 1** auswählen. 
        1.  Wählen Sie das Modell aus, das im Abschnitt **Zusammenfassung des besten Modells** aufgeführt ist. 
        1. Wählen Sie oben links im Fenster **Bereitstellen** aus. 

    + Option 2: Stellen Sie eine bestimmte Modelliteration aus diesem Experiment bereit.
        1. Wählen Sie das gewünschte Modell auf der Registerkarte **Modelle** aus.
        1. Wählen Sie oben links im Fenster **Bereitstellen** aus.

1. Füllen Sie den Bereich **Modell bereitstellen** aus.

    Feld| Wert
    ----|----
    Name| Geben Sie einen eindeutigen Namen für die Bereitstellung ein.
    BESCHREIBUNG| Geben Sie eine Beschreibung ein, um den Zweck dieser Bereitstellung genauer anzugeben.
    Computetyp| Wählen Sie den Typ des Endpunkts, den Sie bereitstellen möchten: [*Azure Kubernetes Service (AKS)*](../aks/intro-kubernetes.md) oder [*Azure Containerinstanz (ACI)* ](../container-instances/container-instances-overview.md).
    Computename| *Nur für AKS:* Wählen Sie den Namen des AKS-Clusters aus, der als Ziel für die Bereitstellung verwendet werden soll.
    Authentifizierung aktivieren | Wählen Sie diese Option aus, um eine token- oder schlüsselbasierte Authentifizierung zu ermöglichen.
    Use custom deployment assets (Benutzerdefinierte Bereitstellungsressourcen verwenden)| Aktivieren Sie dieses Feature, wenn Sie Ihr eigenes Bewertungsskript und Ihre eigene Umgebungsdatei hochladen möchten. Andernfalls stellt die automatisierte ML diese Assets standardmäßig für Sie bereit. Weitere Informationen zu Bewertungsskripts finden Sie [hier](how-to-deploy-and-where.md).

    >[!Important]
    > Ein Dateiname muss weniger als 32 Zeichen haben und muss mit einem alphanumerischen Zeichen beginnen und enden. Dazwischen darf ein Name Bindestriche, Unterstriche, Punkte und alphanumerische Zeichen enthalten. Leerzeichen sind nicht zulässig.

    Das Menü *Erweitert* enthält Standard-Bereitstellungsfeatures wie [Datensammlung](how-to-enable-app-insights.md) und Einstellungen für die Ressourcenauslastung. Wenn Sie diese Standardeinstellungen überschreiben möchten, verwenden Sie dafür dieses Menü.

1. Klicken Sie auf **Bereitstellen**. Die Bereitstellung kann bis zu 20 Minuten dauern.
    Sobald die Bereitstellung beginnt, wird die Registerkarte **Modellzusammenfassung** angezeigt. Der Status der Bereitstellung wird im Abschnitt **Bereitstellungsstatus** angezeigt. 

Nun haben Sie einen einsatzfähigen Webdienst, mit dem Vorhersagen generiert werden können! Sie können die Vorhersagen testen, indem Sie den Dienst über die [in Power BI integrierte Azure Machine Learning-Unterstützung](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context) abfragen.

## <a name="next-steps"></a>Nächste Schritte

* [Informationen zum Nutzen eines Webdiensts](how-to-consume-web-service.md)
* [Grundlagen von Ergebnissen des automatisierten maschinellen Lernens](how-to-understand-automated-ml.md)
* [Weitere Informationen zu automatisiertem Machine Learning](concept-automated-ml.md) und Azure Machine Learning.
