---
title: Erstellen von Computeressourcen für Training und Bereitstellung (Studio)
titleSuffix: Azure Machine Learning
description: Verwenden von Studio zum Erstellen von Computeressourcen (Computeziele) für Training und Bereitstellung für maschinelles Lernen
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: contperf-fy21q1
ms.openlocfilehash: 669b14511c13ac1571082507ad8c16ed6b571600
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556538"
---
# <a name="create-compute-targets-for-model-training-and-deployment-in-azure-machine-learning-studio"></a>Erstellen von Computezielen für Modelltraining und -bereitstellung in Azure Machine Learning Studio

In diesem Artikel erfahren Sie, wie Sie Computeziele in Azure Machine Learning Studio erstellen und verwalten.  Sie können Computeziele auch mit folgenden Komponenten erstellen und verwalten:

* Azure Machine Learning SDK- oder CLI-Erweiterung für Azure Machine Learning
  * [Compute-Instanz](how-to-create-manage-compute-instance.md)
  * [Computecluster](how-to-create-attach-compute-cluster.md)
  * [Azure Kubernetes Service-Cluster](how-to-create-attach-kubernetes.md)
  * [Weitere Berechnungsressourcen](how-to-attach-compute-targets.md)
* [VS Code-Erweiterung](how-to-manage-resources-vscode.md#compute-clusters) für Azure Machine Learning

> [!IMPORTANT]
> Die in diesem Artikel markierten Elemente (Vorschau) sind aktuell als öffentliche Vorschau verfügbar.
> Die Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.
* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

## <a name="whats-a-compute-target"></a>Was ist ein Computeziel? 

Mit Azure Machine Learning können Sie Ihr Modell für eine Vielzahl von Ressourcen oder Umgebungen trainieren, die zusammen als [__Computeziele__](concept-azure-machine-learning-architecture.md#compute-targets) bezeichnet werden. Ein Computeziel kann ein lokaler Computer oder eine Cloudressource sein, wie beispielsweise Azure Machine Learning Compute, Azure HDInsight oder ein virtueller Remotecomputer.  Sie können auch Computeziele für die Modellimplementierung erstellen, wie in [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](how-to-deploy-and-where.md) beschrieben.

## <a name="view-compute-targets"></a><a id="portal-view"></a>Anzeigen von Computezielen

Führen Sie die folgenden Schritte aus, um alle Computeziele für Ihren Arbeitsbereich anzuzeigen:

1. Navigieren Sie zu [Azure Machine Learning Studio](https://ml.azure.com).
 
1. Wählen Sie unter __Verwalten__ die Option __Compute__ aus.

1. Wählen Sie oben Registerkarten aus, um die einzelnen Computeziele anzuzeigen.

    :::image type="content" source="media/how-to-create-attach-studio/view-compute-targets.png" alt-text="Anzeigen der Liste mit Computezielen":::

## <a name="start-creation-process"></a><a id="portal-create"></a>Starten des Erstellungsprozesses

Führen Sie die vorherigen Schritte zum Anzeigen der Liste der Computeziele aus. Führen Sie dann die folgenden Schritte aus, um ein Computeziel zu erstellen:

1. Wählen Sie oben die Registerkarte aus, die dem von Ihnen erstellten Computetyp entspricht.

1. Wenn Sie keine Computeziele besitzen, wählen Sie in der Mitte der Seite **Erstellen** aus.
  
    :::image type="content" source="media/how-to-create-attach-studio/create-compute-target.png" alt-text="Erstellen eines Computeziels":::

1. Wenn eine Liste der Computeressourcen angezeigt wird, wählen Sie oberhalb der Liste **+ Neu** aus.

    :::image type="content" source="media/how-to-create-attach-studio/select-new.png" alt-text="Auswählen von „Neu“":::


1. Füllen Sie das Formular für Ihren Computetyp aus:

    * [Compute-Instanz](how-to-create-manage-compute-instance.md?tabs=azure-studio#create)
    * [Computecluster](#amlcompute)
    * [Rückschlusscluster](#inference-clusters)
    * [Angefügte Computeressourcen](#attached-compute)

1. Klicken Sie auf __Erstellen__.

1. Sie können den Status des Erstellungsvorgangs anzeigen, indem Sie das Computeziel in der Liste auswählen:

    :::image type="content" source="media/how-to-create-attach-studio/view-list.png" alt-text="Anzeigen des Computestatus in einer Liste":::

Befolgen Sie die Schritte in [Erstellen und Verwalten einer Azure Machine Learning-Compute-Instanz](how-to-create-manage-compute-instance.md?tabs=azure-studio#create).


## <a name="create-compute-clusters"></a><a name="amlcompute"></a> Erstellen von Computeclustern

Erstellen Sie einen Computecluster mit einem oder mehreren Knoten für Ihre Workloads in den Bereichen Training, Batchrückschlüsse und vertiefendes Lernen. Führen Sie die [oben beschriebenen Schritte](#portal-create) aus, um den Computecluster zu erstellen.  Füllen Sie das Formular anschließend wie folgt aus:

|Feld  |BESCHREIBUNG  |
|---------|---------|
| Standort | Die Azure-Region, in der der Computecluster erstellt wird. Standardmäßig ist dies derselbe Standort wie für den Arbeitsbereich. Das Festlegen des Standorts auf eine andere Region als für den Arbeitsbereich befindet sich in der __Vorschau__ und ist nur für __Computecluster__, nicht für Compute-Instanzen verfügbar.</br>Wenn Sie eine andere Region als für Ihren Arbeitsbereich oder Ihre Datenspeicher nutzen, können erhöhte Netzwerklatenz und Datenübertragungskosten die Folge sein. Latenz und Kosten können beim Erstellen des Clusters und beim Anwenden von Aufträgen auf den Cluster anfallen. |
|Typ des virtuellen Computers |  Wählen Sie CPU oder GPU aus. Dieser Typ kann nach der Erstellung nicht mehr geändert werden.     |
|VM-Priorität | Wählen Sie **Dediziert** oder **Mit niedriger Priorität** aus.  Virtuelle Computer mit niedriger Priorität sind kostengünstiger, bieten jedoch keine garantierten Computeknoten. Ihr Auftrag wird unter Umständen vorzeitig entfernt.
|Größe des virtuellen Computers     |  Die Größe der unterstützten virtuellen Computer kann in Ihrer Region eingeschränkt sein. Überprüfen Sie die [Verfügbarkeitsliste](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).     |

Wählen Sie **Weiter** aus, um mit **Erweiterte Einstellungen** fortzufahren, und füllen Sie das Formular wie folgt aus:

|Feld  |BESCHREIBUNG  |
|---------|---------|
|Computename     |  <li>Der Name ist erforderlich und muss zwischen 3 und 24 Zeichen lang sein.</li><li>Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und das Zeichen **-** .</li><li>Der Name muss mit einem Buchstaben beginnen.</li><li>Der Name muss auf allen vorhandenen Compute-Instanzen innerhalb einer Azure-Region eindeutig sein. Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.</li><li>Wenn ein **-** -Zeichen verwendet wird, muss darauf im Namen mindestens ein Buchstabe folgen.</li>     |
|Mindestanzahl von Knoten | Mindestanzahl von Knoten, die Sie bereitstellen möchten. Wenn Sie eine dedizierte Anzahl von Knoten verwenden möchten, legen Sie diese Anzahl hier fest. Sparen Sie Geld, indem Sie die Mindestanzahl auf 0 festlegen, sodass Sie für keine Knoten bezahlen, wenn sich der Cluster im Leerlauf befindet. |
|Maximale Knotenanzahl | Maximale Anzahl von Knoten, die Sie bereitstellen möchten. Beim Übermitteln eines Auftrags wird die Computeressource automatisch auf den Höchstwert dieser Knotenanzahl skaliert. |
| Leerlauf in Sekunden vor dem Herunterskalieren | Leerlaufzeit, bevor der Cluster auf die Mindestknotenanzahl herunterskaliert wird. |
| Aktivieren des SSH-Zugriffs | Befolgen Sie dieselben Anweisungen wie bei [Aktivieren des SSH-Zugriffs](#enable-ssh) für eine Compute-Instanz (oben). |
|Erweiterte Einstellungen     |  Optional. Konfigurieren Sie ein virtuelles Netzwerk. Geben Sie **Ressourcengruppe**, **Virtuelles Netzwerk** und **Subnetz** an, um die Compute-Instanz innerhalb von Azure Virtual Network (VNET) zu erstellen. Weitere Informationen finden Sie unter diesen [Netzwerkanforderungen](./how-to-secure-training-vnet.md) für VNET.   Fügen Sie außerdem [verwaltete Identitäten](#managed-identity) an, um Zugriff auf Ressourcen zu gewähren.     |

### <a name="enable-ssh-access"></a><a name="enable-ssh"></a> Aktivieren des SSH-Zugriffs

Der SSH-Zugriff ist standardmäßig deaktiviert.  Der SSH-Zugang kann nach der Erstellung nicht geändert werden. Stellen Sie sicher, dass Sie den Zugriff aktivieren, wenn Sie das interaktive Debuggen mit [VS Code Remote](how-to-set-up-vs-code-remote.md) planen.  

[!INCLUDE [amlinclude-info](../../includes/machine-learning-enable-ssh.md)]

Sobald der Computecluster erstellt ist und ausgeführt wird, finden Sie weitere Informationen unter [Herstellen einer Verbindung mit SSH-Zugriff](#ssh-access).

### <a name="set-up-managed-identity"></a><a name="managed-identity"></a> Einrichten einer verwalteten Identität

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

Schalten Sie beim Erstellen von Clustern oder beim Bearbeiten von Computeclusterdetails unter **Erweiterte Einstellungen** die Option **Verwaltete Identität zuweisen** um, und geben Sie eine systemseitig oder eine benutzerseitig zugewiesene Identität an.

### <a name="managed-identity-usage"></a>Nutzung von verwalteten Identitäten

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="create-inference-clusters"></a><a name="inference-clusters"></a> Erstellen von Rückschlussclustern

> [!IMPORTANT]
> Für die Verwendung des Azure Kubernetes Service mit Azure Machine Learning gibt es mehrere Konfigurationsoptionen. In bestimmten Szenarien wie Netzwerken ist eine zusätzliche Einrichtung und Konfiguration erforderlich. Weitere Informationen zum Verwenden von AKS mit Azure Machine Learning finden Sie unter [Erstellen und Anfügen eines Azure Kubernetes Service-Clusters](how-to-create-attach-kubernetes.md).

Sie können einen AKS-Cluster (Azure Kubernetes Service) für umfangreiche Rückschlüsse erstellen oder anfügen. Führen Sie die [oben beschriebenen Schritte](#portal-create) aus, um den AKS-Cluster zu erstellen.  Füllen Sie das Formular anschließend wie folgt aus:


|Feld  |BESCHREIBUNG  |
|---------|---------|
|Computename     |  <li>Der Name ist erforderlich. Der Name muss zwischen 2 und 16 Zeichen lang sein. </li><li>Gültige Zeichen sind Groß- und Kleinbuchstaben, Ziffern und das Zeichen **-** .</li><li>Der Name muss mit einem Buchstaben beginnen.</li><li>Der Name muss auf allen vorhandenen Compute-Instanzen innerhalb einer Azure-Region eindeutig sein. Sie erhalten eine Warnung, wenn der von Ihnen gewählte Name nicht eindeutig ist.</li><li>Wenn ein **-** -Zeichen verwendet wird, muss darauf im Namen mindestens ein Buchstabe folgen.</li>     |
|Kubernetes-Dienst | Wählen Sie **Neu erstellen** aus, und füllen Sie das restliche Formular aus.  Oder wählen Sie **Vorhandene verwenden** und dann einen vorhandenen AKS-Cluster aus Ihrem Abonnement aus.
|Region |  Wählen Sie die Region aus, an dem der Cluster erstellt wird. |
|Größe des virtuellen Computers     |  Die Größe der unterstützten virtuellen Computer kann in Ihrer Region eingeschränkt sein. Überprüfen Sie die [Verfügbarkeitsliste](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).     |
|Clusterzweck  | Wählen Sie **Produktion** oder **Dev-Test** aus. |
|Anzahl von Knoten | Die Anzahl von Knoten multipliziert mit der Anzahl von VM-Kernen (vCPUs) muss mindestens 12 ergeben. |
| Netzwerkkonfiguration | Wählen Sie **Erweitert** aus, um die Computeressource in einem vorhandenen virtuellen Netzwerk zu erstellen. Weitere Informationen zu AKS in einem virtuellen Netzwerk finden Sie unter [Netzwerkisolation während Training und Rückschluss mit privaten virtuellen Netzwerken](./how-to-secure-inferencing-vnet.md). |
| Enable SSL configuration (SSL-Konfiguration aktivieren) | Hiermit konfigurieren Sie das SSL-Zertifikat für das Computing. |

## <a name="attach-other-compute"></a><a name="attached-compute"></a> Anfügen anderer Computeressourcen

Um Computeziele zu verwenden, die außerhalb des Azure Machine Learning-Arbeitsbereichs erstellt wurde, müssen Sie sie anfügen. Durch das Anfügen eines Computeziels wird es Ihrem Arbeitsbereich zur Verfügung gestellt.  Verwenden Sie **Angefügte Computeressourcen**, um ein Computeziel für **Training** anzufügen.  Verwenden Sie **Rückschlusscluster**, um einen AKS-Cluster für **Rückschlüsse** anzufügen.

Führen Sie die [oben beschriebenen Schritte](#portal-create) aus, um eine Computeressource anzufügen.  Füllen Sie das Formular anschließend wie folgt aus:

1. Geben Sie einen Namen für das Computeziel ein. 
1. Wählen Sie den Computetyp aus, den Sie anfügen möchten. Nicht alle Computetypen können in Azure Machine Learning Studio angefügt werden. Derzeit können folgende Computetypen für das Training angefügt werden:
    * Eine Azure-VM (zum Anfügen einer Data Science Virtual Machine)
    * Azure Databricks (zur Verwendung in Machine Learning-Pipelines)
    * Azure Data Lake Analytics (zur Verwendung in Machine Learning-Pipelines)
    * Azure HDInsight
    * Kubernetes (Vorschau)

1. Füllen Sie das Formular aus, und geben Sie Werte für die Pflichteigenschaften an.

    > [!NOTE]
    > Microsoft empfiehlt die Verwendung von SSH-Schlüsseln, die sicherer als Kennwörter sind. Kennwörter sind anfällig für Brute-Force-Angriffe. SSH-Schlüssel basieren auf kryptografische Signaturen. Informationen zum Erstellen von SSH-Schlüsseln für die Verwendung mit Azure Virtual Machines finden Sie in den folgenden Dokumenten:
    >
    > * [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md)
    > * [Verwenden von SSH-Schlüsseln mit Windows in Azure](../virtual-machines/linux/ssh-from-windows.md)

1. Wählen Sie __Anfügen__ aus.

[!INCLUDE [arc-enabled-machine-learning-create-training-compute](../../includes/machine-learning-create-arc-enabled-training-computer-target.md)]

> [!IMPORTANT]
> Um einen Azure Kubernetes Services- (AKS) oder Kubernetes-Cluster mit Azure Arc-Unterstützung anzufügen, müssen Sie Besitzer des Abonnements sein oder über die Berechtigung verfügen, auf AKS-Clusterressourcen unter dem Abonnement zuzugreifen. Andernfalls ist die Clusterliste auf der Seite „Neue Computeressource anfügen“ leer.

Führen Sie die folgenden Schritte aus, um Ihre Computeressourcen zu trennen:

1. Wählen Sie in Azure Machine Learning Studio die Optionen __Compute__, __Angefügte Computeressourcen__ und die Computeressource aus, die Sie entfernen möchten.
1. Verwenden Sie den Link __Trennen__, um Ihre Computeressource zu trennen.

## <a name="connect-with-ssh-access"></a><a name="ssh-access"></a> Herstellen einer Verbindung mit SSH-Zugriff

Wenn Sie Ihre Compute-Instanz oder Ihren Computecluster mit aktiviertem SSH-Zugriff erstellt haben, verwenden Sie diese Schritte für den Zugriff.

1. Suchen Sie die Computeressourcen in Ihren Arbeitsbereichsressourcen:
    1. Wählen Sie links **Compute** aus.
    1. Verwenden Sie die Registerkarten oben, um **Compute-Instanz** oder **Computecluster** für die Suche nach Ihrem Computer auszuwählen.
1. Wählen Sie den Namen der Computeressource in der Liste der Ressourcen aus.
1. Suchen Sie die Verbindungszeichenfolge:

    * Wählen Sie für eine **Compute-Instanz** die Option **Verbinden** am oberen Rand des Abschnitts **Details** aus.

        :::image type="content" source="media/how-to-create-attach-studio/details.png" alt-text="Screenshot: Tool „Verbinden“ oben auf der Seite „Details“":::

    * Wählen Sie für einen **Compute-Cluster** oben die Option **Knoten** und dann in der Tabelle die **Verbindungszeichenfolge** für Ihren Knoten aus.
        :::image type="content" source="media/how-to-create-attach-studio/compute-nodes.png" alt-text="Screenshot: Verbindungszeichenfolge für einen Knoten in einem Computecluster":::

1. Kopieren Sie die Verbindungszeichenfolge.
1. Unter Windows öffnen Sie PowerShell oder eine Eingabeaufforderung:
   1. Wechseln Sie in das Verzeichnis oder den Ordner, in dem Ihr Schlüssel gespeichert ist.
   1. Fügen Sie der Verbindungszeichenfolge das Flag „-i“ hinzu, um den privaten Schlüssel zu finden und auf seinen Speicherort zu verweisen:
    
      `ssh -i <keyname.pem> azureuser@... (rest of connection string)`

1. Führen Sie für Linux-Benutzer die Schritte unter [Erstellen und Verwenden eines SSH-Schlüsselpaars für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md) aus.
1. Für die SCP-Verwendung: 

   `scp -i key.pem -P {port} {fileToCopyFromLocal }  azureuser@yourComputeInstancePublicIP:~/{destination}`

## <a name="next-steps"></a>Nächste Schritte

Nachdem ein Ziel erstellt und an Ihren Arbeitsbereich angefügt wurde, verwenden Sie es in Ihrer [Laufzeitkonfiguration](how-to-set-up-training-targets.md) mit einem `ComputeTarget`-Objekt:

```python
from azureml.core.compute import ComputeTarget
myvm = ComputeTarget(workspace=ws, name='my-vm-name')
```

* Verwenden Sie die Computeressource zum [Übermitteln einer Trainingsausführung](how-to-set-up-training-targets.md).
* [Tutorial: Trainieren eines Modells](tutorial-train-models-with-aml.md) verwendet ein verwaltetes Computeziel zum Trainieren eines Modells.
* Erfahren Sie, wie [Hyperparameter optimiert werden](how-to-tune-hyperparameters.md), um bessere Modelle zu erstellen.
* Erfahren Sie nach der Erstellung eines trainierten Modells, [wie und wo Modelle bereitgestellt werden](how-to-deploy-and-where.md).
* [Verwenden von Azure Machine Learning mit virtuellen Azure-Netzwerken](./how-to-network-security-overview.md)
