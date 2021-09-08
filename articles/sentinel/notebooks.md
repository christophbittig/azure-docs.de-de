---
title: Verwenden von Notebooks mit Azure Sentinel für Security Hunting
description: In diesem Artikel wird beschrieben, wie Notebooks mit den Ermittlungsfunktionen von Azure Sentinel verwendet werden.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/24/2021
ms.openlocfilehash: 7b1202648db3aed73ea040fb60610879a6816dd9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346577"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Aufspüren von Sicherheitsrisiken mit Jupyter Notebook

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Grundlage für Azure Sentinel ist der Datenspeicher. Dieser zeichnet sich durch Hochleistungsabfragen, ein dynamisches Schema und die Skalierung auf große Datenvolumen aus. Das Azure-Portal und alle Azure Sentinel-Tools greifen über eine gemeinsame API auf diesen Datenspeicher zu. Die gleiche API ist auch für externe Tools wie [Jupyter Notebooks](https://jupyter.org/) und Python verfügbar. Viele gängige Aufgaben können im Portal durchgeführt werden, Jupyter erweitert jedoch den Rahmen der Verarbeitung dieser Daten. Jupyter bietet sowohl umfassende Programmierbarkeit als auch eine große Sammlung von Bibliotheken für Machine Learning, Visualisierung und Datenanalyse. Dies macht Jupyter zu einem überzeugenden Tool für Sicherheitsuntersuchungen und Aufspüren von Sicherheitsrisiken.

Verwenden Sie Notebooks beispielsweise für Folgendes:

- Durchführen von Analysen, die nicht in Azure Sentinel integriert sind, z. B. einige Python-Features für maschinelles Lernen
- Erstellen von Datenvisualisierungen, die nicht in Azure Sentinel integriert sind, z. B. benutzerdefinierte Zeitachsen und Prozessstrukturen
- Integrieren von Datenquellen, die sich außerhalb von Azure Sentinel befinden, z. B. eines lokalen Datasets.

Wir haben die Jupyter-Benutzeroberfläche in das Azure-Portal integriert, sodass Sie Notebooks auf einfache Weise erstellen und mit diesen Notebooks Daten analysieren können. Mit der *Kqlmagic*-Bibliothek als verbindende Komponente können Sie Abfragen von Azure Sentinel annehmen und diese direkt in einem Notebook ausführen. Für Abfragen wird die Abfragesprache [Kusto](https://kusto.azurewebsites.net/docs/kusto/query/index.html) verwendet. Verschiedene von Microsoft-Sicherheitsanalysten entwickelte Notebooks werden mit Azure Sentinel verpackt. Einige diese Notebooks wurden auf ein bestimmtes Szenario ausgelegt, und sie sind ohne weitere Änderungen sofort verwendungsfähig. Andere sollen beispielhaft Techniken und Funktionen veranschaulichen, die Sie kopieren oder für die Verwendung in Ihren eigenen Notebooks anpassen können. Andere Notebooks können auch über die Community-GitHub-Seite für Azure Sentinel importiert werden.

Diese integrierte Jupyter-Benutzererfahrung verwendet [Azure Notebooks](https://notebooks.azure.com/) zum Speichern, Freigeben und Ausführen von Notebooks. Sie können diese Notebooks auch lokal (wenn Sie über eine Python-Umgebung verfügen und Jupyter auf Ihrem Computer installiert ist) oder in anderen JupyterHub-Umgebungen wie Azure Databricks ausführen.


## <a name="notebook-components"></a>Notebookkomponenten

Notebooks verfügen über zwei Komponenten:
- **Die browserbasierte Benutzeroberfläche**, in der Sie Abfragen und Code eingeben und ausführen und in der die Ergebnisse der Ausführung angezeigt werden.
- **Einen *Kernel***, der für das Analysieren und Ausführen des Codes selbst zuständig ist.

Der Kernel des Notebooks in Azure Sentinel wird auf einer Azure-VM ausgeführt. Wenn Ihre Notebooks komplexe Machine Learning-Modelle umfassen, stehen auch weitere Lizenzierungsoptionen zur Verfügung, um leistungsfähigere VMs nutzen zu können.

Die Azure Sentinel-Notebooks nutzen viele beliebte Python-Bibliotheken wie *pandas*, *matplotlib*, *bokeh* und andere. Zahlreiche weitere Python-Pakete stehen zur Auswahl, die u.a. die folgenden Bereiche abdecken:

- Visualisierungen und Grafiken
- Datenverarbeitung und -analyse
- Statistiken und numerisches Computing
- Machine Learning und Deep Learning

Im Paket [msticpy](https://github.com/Microsoft/msticpy/) haben wir zudem einige Open Source-Jupyter-Sicherheitstools veröffentlicht. Dieses Paket wird in vielen der enthaltenen Notebooks verwendet. Msticpy-Tools sollen Sie insbesondere beim Erstellen von Notebooks zum Aufspüren und Untersuchen von Sicherheitsrisiken unterstützen, und wir arbeiten aktiv an neuen Features und Verbesserungen. Weitere Informationen finden Sie in der [Dokumentation zu MSTIC Jupyter und Python Security Tools](https://msticpy.readthedocs.io/).

Im [GitHub-Repository der Azure Sentinel-Community](https://github.com/Azure/Azure-Sentinel) finden sich alle künftig von Microsoft erstellten oder von der Community beigesteuerten Azure Sentinel-Notebooks.


## <a name="manage-access-to-azure-sentinel-notebooks"></a>Verwalten des Zugriffs auf Azure Sentinel-Notebooks

Um die Notebooks verwenden zu können, müssen Sie je nach Ihrer Benutzerrolle zuerst über die richtigen Berechtigungen verfügen.

Da Azure Sentinel Notebooks auf der [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md)-Plattform (Azure ML) ausgeführt werden, müssen Sie über entsprechenden Zugriff sowohl auf den Azure Sentinel Arbeitsbereich als auch auf einen [Azure ML-Arbeitsbereich](../machine-learning/concept-workspace.md) verfügen.

|Berechtigung  |BESCHREIBUNG  |
|---------|---------|
|**Berechtigungen in Azure Sentinel**     |   Wie bei anderen Azure Sentinel-Ressourcen ist für den Zugriff auf Notebooks auf dem Blatt für Azure Sentinel-Notebooks die Rolle „Azure Sentinel-Leser“, „Azure Sentinel-Antwortberechtigter“ oder „Azure Sentinel-Mitwirkender“ erforderlich. <br><br>Weitere Informationen hierzu finden Sie unter [Berechtigungen in Azure Sentinel](roles.md).|
|**Azure Machine Learning-Berechtigungen**     | Ein Azure Machine Learning-Arbeitsbereich ist eine Azure-Ressource. Wie jede andere Azure-Ressource verfügt ein neu erstellter Azure Machine Learning-Arbeitsbereich über Standardrollen. Sie können dem Arbeitsbereich Benutzer hinzufügen und diesen eine dieser integrierten Rollen zuweisen. Weitere Informationen finden Sie unter [Azure Machine Learning-Standardrollen](../machine-learning/how-to-assign-roles.md) und [Integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md). <br><br>   **Wichtig**: Der Rollenzugriff kann für mehrere Ebenen in Azure gelten. Es kann z. B. sein, dass ein Benutzer mit Vollzugriff für einen Arbeitsbereich für die Ressourcengruppe, die diesen Arbeitsbereich enthält, keinen Vollzugriff hat. Weitere Informationen finden Sie unter [Funktionsweise von Azure RBAC](../role-based-access-control/overview.md). <br><br>Wenn Sie Besitzer eines Azure ML-Arbeitsbereichs sind, können Sie Rollen für den Arbeitsbereich hinzufügen und entfernen und Benutzern Rollen zuweisen. Weitere Informationen finden Sie unter:<br>    - [Azure-Portal](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Azure CLI](../role-based-access-control/role-assignments-cli.md)<br>   - [REST-API](../role-based-access-control/role-assignments-rest.md)<br>    - [Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)<br> - [Azure Machine Learning-CLI ](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>Wenn die integrierten Rollen nicht ausreichend sind, können Sie auch benutzerdefinierte Rollen erstellen. Benutzerdefinierte Rollen können über Berechtigung zum Lesen, Schreiben, Löschen und für Computeressourcen in diesem Arbeitsbereich verfügen. Sie können die Rolle auf einer bestimmten Arbeitsbereichsebene, einer bestimmten Ressourcengruppenebene oder einer bestimmten Abonnementebene verfügbar machen. Weitere Informationen finden Sie unter [Erstellen einer benutzerdefinierten Rolle](../machine-learning/how-to-assign-roles.md#create-custom-role). |
|     |         |


## <a name="create-an-azure-ml-workspace-from-azure-sentinel"></a>Erstellen eines Azure ML-Arbeitsbereichs aus Azure Sentinel

In diesem Verfahren wird beschrieben, wie Sie einen Azure ML-Arbeitsbereich für Ihre Azure Sentinel Notebooks aus Azure Sentinel erstellen.

**So erstellen Sie den Arbeitsbereich**

1. Navigieren Sie im Azure-Portal zu **Azure Sentinel** > **Bedrohungsverwaltung** > **Notebooks**, und wählen Sie dann **Create a new AML workspace** (Neuen AML-Arbeitsbereich erstellen) aus.

1. Geben Sie die folgenden Informationen ein, und wählen Sie dann **Weiter** aus:

    |Feld|BESCHREIBUNG|
    |--|--|
    |**Abonnement**|Wählen Sie das gewünschte Azure-Abonnement aus.|
    |**Ressourcengruppe**|Verwenden Sie eine vorhandene Ressourcengruppe in Ihrem Abonnement, oder geben Sie einen Namen ein, um eine neue Ressourcengruppe zu erstellen. Eine Ressourcengruppe enthält verwandte Ressourcen für eine Azure-Lösung.|
    |**Arbeitsbereichsname**|Geben Sie einen eindeutigen Namen ein, der Ihren Arbeitsbereich identifiziert. Namen müssen in der Ressourcengruppe eindeutig sein. Verwenden Sie einen Namen, der leicht zu merken ist und sich von den von anderen Benutzern erstellten Arbeitsbereichen unterscheidet.|
    |**Region**|Wählen Sie den Standort aus, der Ihren Benutzern und den Datenressourcen am nächsten ist, um Ihren Arbeitsbereich zu erstellen.|
    |**Speicherkonto**| Ein Speicherkonto wird als Standarddatenspeicher für den Arbeitsbereich verwendet. Sie können eine neue Azure Storage-Ressource erstellen oder eine in Ihrem Abonnement vorhandene Ressource auswählen.|
    |**KeyVault**| Ein Schlüsseltresor wird zum Speichern von Geheimnissen und anderen vertraulichen Informationen verwendet, die vom Arbeitsbereich benötigt werden. Sie können eine neue Azure Key Vault-Ressource erstellen oder eine in Ihrem Abonnement vorhandene Ressource auswählen.|
    |**Application Insights**| Der Arbeitsbereich verwendet Azure Application Insights, um Überwachungsinformationen zu Ihren bereitgestellten Modellen zu speichern. Sie können eine neue Azure Application Insights-Ressource erstellen oder eine in Ihrem Abonnement vorhandene Ressource auswählen.|
    |**Containerregistrierung**| Eine Containerregistrierung wird verwendet, um Docker-Images zu registrieren, die zu Trainingszwecken und in Bereitstellungen verwendet werden. Um Kosten zu minimieren, wird erst dann eine neue Azure Container Registry-Ressource erstellt, nachdem Sie Ihr erstes Image erstellt haben. Alternativ können Sie jetzt die Ressource erstellen oder eine vorhandene Ressource in Ihrem Abonnement auswählen oder aber **Keine** auswählen, wenn Sie keine Containerregistrierung verwenden möchten.|
    | | |

1. Wählen Sie auf der Registerkarte **Netzwerk** aus, ob Sie Ihren Arbeitsbereich über einen öffentlichen Endpunkt oder einen von Ihnen konfigurierten privaten Endpunkt verbinden möchten. Wenn Ihr Azure Sentinel-Arbeitsbereich über einen öffentlichen Endpunkt verfügt, wird empfohlen, einen öffentlichen Endpunkt für Ihren Azure ML-Arbeitsbereich zu verwenden, um potenzielle Probleme bei der Netzwerkkommunikation zu vermeiden. Wählen Sie abschließend **Überprüfen + erstellen** aus.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen**, ob die Informationen korrekt sind, und klicken Sie dann auf **Erstellen**, um die Bereitstellung Ihres Arbeitsbereichs zu starten. Zum Beispiel:

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Überprüfen und erstellen Sie Ihren Machine Learning-Arbeitsbereich aus Azure Sentinel.":::

    Die Erstellung des Arbeitsbereichs in der Cloud kann einige Minuten dauern. Während dieser Zeit wird auf der Seite **Übersicht** des Arbeitsbereichs der aktuelle Bereitstellungsstatus angezeigt. Wenn die Bereitstellung abgeschlossen ist, wird dieser aktualisiert.

1. Nach Abschluss der Bereitstellung können Sie zu den Azure Sentinel-**Notebooks** zurückkehren und Notebooks aus Ihrem neuen Azure ML-Arbeitsbereich starten.

    Wenn Sie über mehrere Notebooks verfügen, stellen Sie sicher, dass Sie einen Standard-AML-Arbeitsbereich auswählen, der beim Starten Ihrer Notebooks verwendet werden soll. Zum Beispiel:

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="Wählen Sie einen Standardarbeitsbereich AML für Ihre Notebooks aus.":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Starten eines Notebooks in Ihrem Azure ML-Arbeitsbereich

Nachdem Sie einen AML-Arbeitsbereich erstellt haben, starten Sie Ihre Notebooks aus Azure Sentinel in Ihrem Azure ML-Arbeitsbereich.

**So starten Sie Ihr Notebook**

1. Navigieren Sie vom Azure-Portal aus zu **Azure Sentinel** > **Bedrohungsverwaltung** > **Notebooks**, wo die von Azure Sentinel bereitgestellten Notebooks angezeigt werden.

    > [!TIP]
    > Wählen Sie oben auf der Seite **Notebooks** die Option **Leitfäden und Feedback** aus, um weitere Ressourcen und Leitfäden in einem Bereich auf der rechten Seite anzuzeigen.

1. Wählen Sie ein Notebook aus, um dessen Beschreibung, erforderliche Datentypen und Datenquellen anzuzeigen.

    Wenn Sie das Notebook gefunden haben, das Sie verwenden möchten, wählen Sie **Notebook speichern** aus, um es in Ihren eigenen Arbeitsbereich zu klonen.

    Bearbeiten Sie den Namen nach Bedarf. Wenn das Notebook bereits in Ihrem Arbeitsbereich vorhanden ist, haben Sie die Möglichkeit, das vorhandene Notebook zu überschreiben oder ein neues zu erstellen.

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="Speichern Sie ein Notebook, um es in Ihren eigenen Arbeitsbereich zu klonen.":::

1. Nachdem das Notebook gespeichert wurde, ändert sich die Schaltfläche **Notebook speichern** in **Notebook starten**. Wählen Sie **Notebook starten** aus, um es in Ihrem AML-Arbeitsbereich zu öffnen.

    Zum Beispiel:

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="Starten Sie Ihr Notebook in Ihrem AML-Arbeitsbereich.":::

1. Wählen Sie oben auf der Seite eine **Compute**-Instanz aus, die für Ihren Notebook-Server verwendet werden soll.

    Wenn Sie über keine Compute-Instanz verfügen, [erstellen Sie eine neue](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio). Wenn Ihre Compute-Instanz beendet wurde, müssen Sie sie starten. Weitere Informationen finden Sie unter [Ausführen eines Notebooks in Azure Machine Learning Studio](../machine-learning/how-to-run-jupyter-notebooks.md).

    Nur Sie können die von Ihnen erstellten Computeinstanzen sehen und verwenden. Ihre Benutzerdateien werden getrennt vom virtuellen Computer gespeichert und von allen Computeinstanzen im Arbeitsbereich gemeinsam genutzt.

    > [!TIP]
    > Wenn Sie eine neue Compute-Instanz erstellen, um Ihre Notebooks zu testen, erstellen Sie Ihre Compute-Instanz mit der Kategorie **Universell**.

1. Nachdem der Notebookserver erstellt und gestartet wurde, können Sie mit dem Ausführen Ihrer Notebookzellen beginnen. Wählen Sie in jeder Zelle das Symbol **Ausführen** aus, um Ihren Notebookcode auszuführen.

    Weitere Informationen finden Sie unter [Tastenkombinationen im Befehlsmodus](../machine-learning/how-to-run-jupyter-notebooks.md).

1. Wenn Sie den Notebookkernel neu starten und die Notebookzellen von Anfang an erneut ausführen müssen, wählen Sie **Kernelvorgänge** > **Kernel neu starten** aus. Zum Beispiel:

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="Starten Sie einen Notebookkernel neu.":::

## <a name="troubleshooting"></a>Problembehandlung

Wenn Probleme mit Ihren Notebooks auftreten, finden Sie weitere Informationen unter [Problembehandlung bei Azure Machine Learning-Notebooks](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting).


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über die Verwendung von Notebooks bei der Bedrohungssuche und -untersuchung, indem Sie sich einige Notebookvorlagen ansehen, z. B. [**Anmeldeinformationsüberprüfung in Azure Log Analytics**](https://www.youtube.com/watch?v=OWjXee8o04M) und **Interaktive Untersuchung – Prozesswarnung**. 

    Notebookvorlagen finden Sie unter Azure Sentinel > **Notebooks** > **Vorlagen**.

- Weitere finden Sie im [Azure Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel-Notebooks):

    - Im Verzeichnis [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) befinden sich Beispielnotebooks, die mit Daten gespeichert wurden, die die beabsichtigte Ausgabe veranschaulichen.

    - Das Verzeichnis [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) enthält Notebooks, die Konzepte wie das Festlegen der Standard-Python-Version oder das Erstellen von Azure Sentinel-Lesezeichen aus einem Notebook veranschaulichen.

    Weitere Informationen finden Sie im Leitfaden [Erste Schritte mit Notebooks in Azure Machine Learning und Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb).

> [!NOTE]
> Die im [Azure Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel-Notebooks) verfügbaren Notebooks sind als nützliche Tools, Veranschaulichungen und Codebeispiele gedacht, die Sie beim Entwickeln eigener Notebooks verwenden können.
>
> Wir freuen uns über Feedback, Vorschläge, gewünschte Funktionen, eigene Notebooks, Fehlerberichte oder Verbesserungen und Ergänzungen zu vorhandenen Notebooks. Besuchen Sie die [Azure Sentinel-Community GitHub](https://github.com/Azure/Azure-Sentinel), um ein Problem zu melden oder eine Verzweigung anzulegen oder um einen Beitrag hochzuladen.
>

Weitere Informationen finden Sie unter:

- [Webinar: Grundlagen von Azure Sentinel-Notebooks](https://www.youtube.com/watch?v=rewdNeX6H94)
- [Tutorial: Azure Sentinel: Notebooks – erste Schritte](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks)
- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Behalten des Überblicks über Daten bei einer Bedrohungssuche](bookmarks.md)
- [Jupyter, msticpy und Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
- [Tutorial: Bearbeiten und Ausführen von Jupyter Notebooks, ohne Azure ML Studio zu verlassen](https://www.youtube.com/watch?v=AAj-Fz0uCNk)
