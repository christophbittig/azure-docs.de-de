---
title: Verwenden von Notebooks mit Microsoft Sentinel für Security Hunting
description: In diesem Artikel wird beschrieben, wie Notebooks mit den Ermittlungsfunktionen von Microsoft Sentinel verwendet werden.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: ace7df5954d288bb46430c84ccde8d2822f08115
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521223"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Aufspüren von Sicherheitsrisiken mit Jupyter Notebooks

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Grundlage für Microsoft Sentinel ist der Datenspeicher. Dieser zeichnet sich durch Hochleistungsabfragen, ein dynamisches Schema und die Skalierung auf große Datenvolumen aus. Das Azure-Portal und alle Microsoft Sentinel-Tools greifen über eine gemeinsame API auf diesen Datenspeicher zu.

Die gleiche API ist auch für externe Tools wie [Jupyter Notebooks](https://jupyter.org/) und Python verfügbar. Viele gängige Aufgaben können im Portal durchgeführt werden, Jupyter erweitert jedoch den Rahmen der Verarbeitung dieser Daten. Jupyter bietet sowohl umfassende Programmierbarkeit als auch eine große Sammlung von Bibliotheken für Machine Learning, Visualisierung und Datenanalyse. Dies macht Jupyter zu einem überzeugenden Tool für Sicherheitsuntersuchungen und Aufspüren von Sicherheitsrisiken.

Verwenden Sie Notebooks beispielsweise für Folgendes:

- **Durchführen von Analysen**, die nicht standardmäßig in Microsoft Sentinel enthalten sind, z. B. einige Python-Features für maschinelles Lernen
- **Erstellen von Datenvisualisierungen**, die nicht standardmäßig in Microsoft Sentinel enthalten sind, z. B. benutzerdefinierte Zeitachsen und Prozessstrukturen
- **Integrieren von Datenquellen**, die sich außerhalb von Microsoft Sentinel befinden, z. B. eines lokalen Datasets

Wir haben die Jupyter-Benutzeroberfläche in das Azure-Portal integriert, sodass Sie Notebooks auf einfache Weise erstellen und mit diesen Notebooks Daten analysieren können. Mit der *Kqlmagic*-Bibliothek als verbindende Komponente können Sie [KQL](https://kusto.azurewebsites.net/docs/kusto/query/index.html)-Abfragen von Microsoft Sentinel annehmen und diese direkt in einem Notebook ausführen.

Verschiedene von Microsoft-Sicherheitsanalysten entwickelte Notebooks werden mit Microsoft Sentinel verpackt:

- Einige diese Notebooks wurden auf ein bestimmtes Szenario ausgelegt, und sie sind ohne weitere Änderungen sofort verwendungsfähig.
- Andere sollen beispielhaft Techniken und Funktionen veranschaulichen, die Sie kopieren oder für die Verwendung in Ihren eigenen Notebooks anpassen können.

Weitere Notebooks können auch aus dem [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel-Notebooks/) importiert werden.

## <a name="notebook-components"></a>Notebookkomponenten

Notebooks verfügen über zwei Komponenten:

- **Die browserbasierte Benutzeroberfläche**, in der Sie Abfragen und Code eingeben und ausführen und in der die Ergebnisse der Ausführung angezeigt werden.
- **Einen *Kernel***, der für das Analysieren und Ausführen des Codes selbst zuständig ist.

Der Kernel des Notebooks in Microsoft Sentinel wird auf einer Azure-VM ausgeführt. Wenn Ihre Notebooks komplexe Machine Learning-Modelle umfassen, stehen auch weitere Lizenzierungsoptionen zur Verfügung, um leistungsfähigere VMs nutzen zu können.

Die Microsoft Sentinel-Notebooks nutzen viele beliebte Python-Bibliotheken wie *pandas*, *matplotlib*, *bokeh* und andere. Zahlreiche weitere Python-Pakete stehen zur Auswahl, die u.a. die folgenden Bereiche abdecken:

- Visualisierungen und Grafiken
- Datenverarbeitung und -analyse
- Statistiken und numerisches Computing
- Machine Learning und Deep Learning

Um zu vermeiden, komplexen und sich wiederholenden Code in Notebookzellen eingeben oder einfügen zu müssen, basieren die meisten Python-Notebooks auf Bibliotheken von Drittanbietern, die als *Pakete* bezeichnet werden. Um ein Paket in einem Notebook zu verwenden, müssen Sie das Paket installieren und importieren. In Azure ML Compute sind die gängigsten Pakete vorinstalliert. Stellen Sie sicher, dass Sie das Paket oder den relevanten Teil des Pakets, z. B. ein Modul, eine Datei, eine Funktion oder eine Klasse, importieren.

Microsoft Sentinel-Notebooks verwenden das Python-Paket [MSTICPy](https://github.com/Microsoft/msticpy/), bei dem es sich um eine Sammlung von Cybersicherheitstools für den Datenabruf, die Datenanalyse, die Datenanreicherung und die Datenvisualisierung handelt. 

MSTICPy-Tools sollen Sie insbesondere beim Erstellen von Notebooks zum Aufspüren und Untersuchen von Sicherheitsrisiken unterstützen, und wir arbeiten aktiv an neuen Features und Verbesserungen. Weitere Informationen finden Sie unter

- [Dokumentation zu MSTIC Jupyter und Python Security Tools](https://msticpy.readthedocs.io/)
- [Tutorial: Erste Schritte mit Jupyter-Notebooks und MSTICPy in Microsoft Sentinel](notebook-get-started.md)
- [Erweiterte Konfigurationen für Jupyter Notebooks und MSTICPy in Microsoft Sentinel](notebooks-msticpy-advanced.md)

Im [GitHub-Repository von Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/) finden sich alle künftig von Microsoft erstellten oder von der Community beigesteuerten Microsoft Sentinel-Notebooks.

## <a name="manage-access-to-microsoft-sentinel-notebooks"></a>Verwalten des Zugriffs auf Microsoft Sentinel-Notebooks

Um Jupyter Notebooks in Microsoft Sentinel verwenden zu können, müssen Sie je nach Ihrer Benutzerrolle zuerst über die richtigen Berechtigungen verfügen.

Während Sie Microsoft Sentinel-Notebooks in JupyterLab oder Jupyter (klassisch) ausführen können, werden Notebooks in Microsoft Sentinel auf einer [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md)-Plattform (Azure ML) ausgeführt. Für die Ausführung von Notebooks in Microsoft Sentinel müssen Sie über entsprechenden Zugriff sowohl auf den Microsoft Sentinel-Arbeitsbereich als auch auf einen [Azure ML-Arbeitsbereich](../machine-learning/concept-workspace.md) verfügen.

|Berechtigung  |BESCHREIBUNG  |
|---------|---------|
|**Berechtigungen in Microsoft Sentinel**     |   Wie bei anderen Microsoft Sentinel-Ressourcen ist für den Zugriff auf Notebooks auf dem Blatt für Microsoft Sentinel-Notebooks die Rolle „Microsoft Sentinel-Leser“, „Microsoft Sentinel-Antwortberechtigter“ oder „Microsoft Sentinel-Mitwirkender“ erforderlich. <br><br>Weitere Informationen hierzu finden Sie unter [Berechtigungen in Microsoft Sentinel](roles.md).|
|**Azure Machine Learning-Berechtigungen**     | Ein Azure Machine Learning-Arbeitsbereich ist eine Azure-Ressource. Wie jede andere Azure-Ressource verfügt ein neu erstellter Azure Machine Learning-Arbeitsbereich über Standardrollen. Sie können dem Arbeitsbereich Benutzer hinzufügen und diesen eine dieser integrierten Rollen zuweisen. Weitere Informationen finden Sie unter [Azure Machine Learning-Standardrollen](../machine-learning/how-to-assign-roles.md) und [Integrierte Azure-Rollen](../role-based-access-control/built-in-roles.md). <br><br>   **Wichtig**: Der Rollenzugriff kann für mehrere Ebenen in Azure gelten. Es kann z. B. sein, dass ein Benutzer mit Vollzugriff für einen Arbeitsbereich für die Ressourcengruppe, die diesen Arbeitsbereich enthält, keinen Vollzugriff hat. Weitere Informationen finden Sie unter [Funktionsweise von Azure RBAC](../role-based-access-control/overview.md). <br><br>Wenn Sie Besitzer eines Azure ML-Arbeitsbereichs sind, können Sie Rollen für den Arbeitsbereich hinzufügen und entfernen und Benutzern Rollen zuweisen. Weitere Informationen finden Sie unter:<br>    - [Azure-Portal](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [Azure CLI](../role-based-access-control/role-assignments-cli.md)<br>   - [REST-API](../role-based-access-control/role-assignments-rest.md)<br>    - [Azure Resource Manager-Vorlagen](../role-based-access-control/role-assignments-template.md)<br> - [Azure Machine Learning-CLI ](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>Wenn die integrierten Rollen nicht ausreichend sind, können Sie auch benutzerdefinierte Rollen erstellen. Benutzerdefinierte Rollen können über Berechtigung zum Lesen, Schreiben, Löschen und für Computeressourcen in diesem Arbeitsbereich verfügen. Sie können die Rolle auf einer bestimmten Arbeitsbereichsebene, einer bestimmten Ressourcengruppenebene oder einer bestimmten Abonnementebene verfügbar machen. Weitere Informationen finden Sie unter [Erstellen einer benutzerdefinierten Rolle](../machine-learning/how-to-assign-roles.md#create-custom-role). |
|     |         |

## <a name="create-an-azure-ml-workspace-from-microsoft-sentinel"></a>Erstellen eines Azure ML-Arbeitsbereichs aus Microsoft Sentinel

In diesem Verfahren wird beschrieben, wie Sie einen Azure ML-Arbeitsbereich für Ihre Microsoft Sentinel Notebooks aus Microsoft Sentinel erstellen.

**So erstellen Sie den Arbeitsbereich**

1. Navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Bedrohungsverwaltung** > **Notebooks**, und wählen Sie dann **Create a new AML workspace** (Neuen AML-Arbeitsbereich erstellen) aus.

1. Geben Sie die folgenden Informationen ein, und wählen Sie dann **Weiter** aus:

    |Feld|Beschreibung|
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

1. Wählen Sie auf der Registerkarte **Netzwerk** aus, ob Sie Ihren Arbeitsbereich über einen öffentlichen Endpunkt oder einen von Ihnen konfigurierten privaten Endpunkt verbinden möchten. Wenn Ihr Microsoft Sentinel-Arbeitsbereich über einen öffentlichen Endpunkt verfügt, wird empfohlen, einen öffentlichen Endpunkt für Ihren Azure ML-Arbeitsbereich zu verwenden, um potenzielle Probleme bei der Netzwerkkommunikation zu vermeiden. Wählen Sie abschließend **Überprüfen + erstellen** aus.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen**, ob die Informationen korrekt sind, und klicken Sie dann auf **Erstellen**, um die Bereitstellung Ihres Arbeitsbereichs zu starten. Zum Beispiel:

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Überprüfen und erstellen Sie Ihren Machine Learning-Arbeitsbereich aus Microsoft Sentinel.":::

    Die Erstellung des Arbeitsbereichs in der Cloud kann einige Minuten dauern. Während dieser Zeit wird auf der Seite **Übersicht** des Arbeitsbereichs der aktuelle Bereitstellungsstatus angezeigt. Wenn die Bereitstellung abgeschlossen ist, wird dieser aktualisiert.

1. Nach Abschluss der Bereitstellung können Sie zu den Microsoft Sentinel-**Notebooks** zurückkehren und Notebooks aus Ihrem neuen Azure ML-Arbeitsbereich starten.

    Wenn Sie über mehrere Notebooks verfügen, stellen Sie sicher, dass Sie einen Standard-AML-Arbeitsbereich auswählen, der beim Starten Ihrer Notebooks verwendet werden soll. Zum Beispiel:

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="Wählen Sie einen Standardarbeitsbereich AML für Ihre Notebooks aus.":::


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Starten eines Notebooks in Ihrem Azure ML-Arbeitsbereich

Nachdem Sie einen AML-Arbeitsbereich erstellt haben, starten Sie Ihre Notebooks aus Microsoft Sentinel in Ihrem Azure ML-Arbeitsbereich.

> [!NOTE]
> Sie können ein Notebook als statisches Dokument anzeigen, z. B. im statischen Notebookrenderer, der in GitHub integriert ist. Zum Ausführen von Code in einem Notebook müssen Sie das Notebook jedoch an einen Back-End-Prozess anfügen, der als Jupyter-Kernel bezeichnet wird. Der Kernel führt den Code aus und enthält alle Variablen und Objekte, die vom Code erstellt werden. Der Browser ist der Viewer für diese Daten.
>
> In Azure ML wird der Kernel auf einem virtuellen Computer ausgeführt, der als Azure ML Compute bezeichnet wird. Die Compute-Instanz kann die gleichzeitige Ausführung vieler Notebooks unterstützen.
>

**So starten Sie Ihr Notebook über Microsoft Sentinel**:

1. Navigieren Sie vom Azure-Portal aus zu **Microsoft Sentinel** > **Bedrohungsverwaltung** > **Notebooks**, wo die von Microsoft Sentinel bereitgestellten Notebooks angezeigt werden.

    > [!TIP]
    > Wählen Sie oben auf der Seite **Notebooks** die Option **Leitfäden und Feedback** aus, um weitere Ressourcen und Leitfäden in einem Bereich auf der rechten Seite anzuzeigen.

1. Wählen Sie ein Notebook aus, um dessen Beschreibung, erforderliche Datentypen und Datenquellen anzuzeigen.

    Wenn Sie das Notebook gefunden haben, das Sie verwenden möchten, wählen Sie **Notebook speichern** aus, um es in Ihren eigenen Arbeitsbereich zu klonen.

    Bearbeiten Sie den Namen nach Bedarf. Wenn das Notebook bereits in Ihrem Arbeitsbereich vorhanden ist, können Sie das vorhandene Notebook überschreiben oder ein neues erstellen.

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="Speichern Sie ein Notebook, um es in Ihren eigenen Arbeitsbereich zu klonen.":::

1. Nachdem das Notebook gespeichert wurde, ändert sich die Schaltfläche **Notebook speichern** in **Notebook starten**. Wählen Sie **Notebook starten** aus, um es in Ihrem AML-Arbeitsbereich zu öffnen.

    Zum Beispiel:

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="Starten Sie Ihr Notebook in Ihrem AML-Arbeitsbereich.":::

1. Wählen Sie oben auf der Seite eine **Compute**-Instanz aus, die für Ihren Notebook-Server verwendet werden soll.

    Wenn Sie über keine Compute-Instanz verfügen, [erstellen Sie eine neue](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio). Wenn Ihre Compute-Instanz beendet wurde, müssen Sie sie starten. Weitere Informationen finden Sie unter [Ausführen eines Notebooks in Azure Machine Learning Studio](../machine-learning/how-to-run-jupyter-notebooks.md).

    Nur Sie können die von Ihnen erstellten Computeinstanzen sehen und verwenden. Ihre Benutzerdateien werden getrennt vom virtuellen Computer gespeichert und von allen Computeinstanzen im Arbeitsbereich gemeinsam genutzt.

    > [!TIP]
    > Wenn Sie eine neue Compute-Instanz erstellen, um Ihre Notebooks zu testen, erstellen Sie Ihre Compute-Instanz mit der Kategorie **Universell**.
    >
    > Der Kernel wird auch oben rechts in Ihrem Azure ML-Fenster angezeigt. Wenn der von Ihnen benötigte Kernel nicht ausgewählt ist, wählen Sie in der Dropdownliste eine andere Version aus.
    >

1. Nachdem der Notebookserver erstellt und gestartet wurde, können Sie mit dem Ausführen Ihrer Notebookzellen beginnen. Wählen Sie in jeder Zelle das Symbol **Ausführen** aus, um Ihren Notebookcode auszuführen.

    Weitere Informationen finden Sie unter [Tastenkombinationen im Befehlsmodus](../machine-learning/how-to-run-jupyter-notebooks.md).

1. Wenn Ihr Notebook nicht mehr reagiert oder Sie von vorn beginnen möchten, können Sie den Kernel neu starten und die Notebookzellen von Anfang an erneut ausführen. Wählen Sie **Kernelvorgänge** > **Kernel neu starten** aus. Zum Beispiel:

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="Starten Sie einen Notebookkernel neu.":::

    > [!IMPORTANT]
    > Beim Neustarten des Kernels werden alle Variablen und andere Zustände zurückgesetzt. Sie müssen alle Initialisierungs- und Authentifizierungszellen nach dem Neustart erneut ausführen.
    >

## <a name="run-code-in-your-notebook"></a>Ausführen von Code in Ihrem Notebook

In einem Notebook:

- **Markdownzellen** enthalten Text, einschließlich HTML, und statische Bilder.
- **Codezellen** enthalten Code. Nachdem Sie eine Codezelle ausgewählt haben, führen Sie den Code in der Zelle aus, indem Sie links neben der Zelle das Symbol **Wiedergeben** auswählen oder indem Sie **UMSCHALT+EINGABE** drücken.

> [!IMPORTANT]
> Führen Sie Notebook-Codezellen immer nacheinander aus. Das Überspringen von Zellen kann zu Fehlern führen.
>

Führen Sie beispielsweise die folgende Codezelle in Ihrem Notebook aus:

```python
# This is your first code cell. This cell contains basic Python code.

# You can run a code cell by selecting it and then selecting
# the Play button to the left of the cell, or by pressing SHIFT+ENTER.
# Code output displays below the code.

print("Congratulations, you just ran this code cell")

y = 2 + 2

print("2 + 2 =", y)

```

Der oben gezeigte Beispielcode erzeugt die folgende Ausgabe:

```python
Congratulations, you just ran this code cell

2 + 2 = 4
```

Variablen, die in einer Notebook-Codezelle festgelegt werden, bleiben zwischen Zellen bestehen, so dass Sie Zellen miteinander verketten können. Die folgende Codezelle verwendet beispielsweise den Wert `y` aus der vorherigen Zelle:

```python
# Note that output from the last line of a cell is automatically
# sent to the output cell, without needing the print() function.

y + 2
```

Die Ausgabe ist:

```output
6
```

## <a name="download-all-microsoft-sentinel-notebooks"></a>Herunterladen aller Microsoft Sentinel-Notebooks

In diesem Abschnitt wird beschrieben, wie Sie Git verwenden, um alle Notebooks, die im [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel-Notebooks/) verfügbar sind, aus einem Microsoft Sentinel-Notebook, direkt in Ihren Azure ML-Arbeitsbereich herunterzuladen.

Wenn Microsoft Sentinel-Notebooks in Ihrem Azure ML-Arbeitsbereich gespeichert sind, können Sie sie problemlos auf dem neuesten Stand halten.

1. Geben Sie in einem Microsoft Sentinel-Notebook den folgenden Code in eine leere Zelle ein, und führen Sie dann die Zelle aus:

    ```python
    !git clone https://github.com/Azure/Azure-Sentinel-Notebooks.git azure-sentinel-nb
    ```

    Eine Kopie des GitHub-Repositoryinhalts wird im Verzeichnis **azure-Sentinel-nb** in Ihrem Benutzerordner in Ihrem Azure ML-Arbeitsbereich erstellt.

1. Kopieren Sie die gewünschten Notebooks aus diesem Ordner in Ihr Arbeitsverzeichnis.

1. Wenn Sie Ihre Notebooks mit aktuellen Änderungen von GitHub aktualisieren möchten, führen Sie Folgendes aus:

    ```python
    !cd azure-sentinel-nb && git pull
    ```

## <a name="troubleshooting"></a>Problembehandlung

In der Regel wird ein Notebook nahtlos erstellt oder an einen Kernel angefügt, und Sie müssen keine manuellen Änderungen vornehmen. Wenn Sie Fehler erhalten oder das Notebook scheinbar nicht ausgeführt wird, müssen Sie möglicherweise die Version und den Status des Kernels überprüfen.

Wenn Probleme mit Ihren Notebooks auftreten, finden Sie weitere Informationen unter [Problembehandlung bei Azure Machine Learning-Notebooks](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting).

### <a name="force-caching-for-user-accounts-and-credentials-between-notebook-runs"></a>Erzwingen der Zwischenspeicherung für Benutzerkonten und Anmeldeinformationen zwischen Notebookausführungen

Standardmäßig werden Benutzerkonten und Anmeldeinformationen zwischen Notebookausführungen nicht zwischengespeichert, auch nicht für dieselbe Sitzung.

**So erzwingen Sie die Zwischenspeicherung für die Dauer Ihrer Sitzung**

1. Authentifizieren Sie sich über die Azure-Befehlszeilenschnittstelle. Geben Sie in einer leeren Notebookzelle den folgenden Code ein, und führen Sie ihn aus:

    ```python
    !az login
    ```

    Die folgende Ausgabe wird angezeigt:

    ```python
    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the 9-digit device code to authenticate.
    ```

1. Wählen Sie in der Ausgabe das aus neun Zeichen bestehende Token aus, und kopieren Sie es. Wählen Sie dann die `devicelogin`-URL aus, um zur angegebenen Seite zu navigieren. 

1. Fügen Sie das Token in das Dialogfeld ein, und fahren Sie mit der Anmeldung fort, wenn Sie dazu aufgefordert werden.

    Nach erfolgreichem Abschluss der Anmeldung wird die folgende Ausgabe angezeigt:

    ```python
    Subscription <subscription ID> 'Sample subscription' can be accessed from tenants <tenant ID>(default) and <tenant ID>. To select a specific tenant when accessing this subscription, use 'az login --tenant TENANT_ID'.

> [!NOTE]
> The following tenants don't contain accessible subscriptions. Use 'az login --allow-no-subscriptions' to have tenant level access.
>
> ```
> <tenant ID> 'foo'
><tenant ID> 'bar'
>[
> {
>    "cloudName": "AzureApp",
>    "homeTenantId": "<tenant ID>",
>    "id": "<ID>",
>    "isDefault": true,
>    "managedByTenants": [
>    ....
>```
>
### Error: *Runtime dependency of PyGObject is missing*

If the *Runtime dependency of PyGObject is missing* error appears when you load a query provider, try troubleshooting using the following steps:

1. Proceed to the cell with the following code and run it:

    ```python
    qry_prov = QueryProvider("AzureSentinel")
    ```

    Es wird eine Warnung angezeigt, die der folgenden Meldung ähnelt. Sie weist auf eine fehlende Python-Abhängigkeit (`pygobject`) hin:

    ```output
    Runtime dependency of PyGObject is missing.

    Depends on your Linux distribution, you can install it by running code similar to the following:
    sudo apt install python3-gi python3-gi-cairo gir1.2-secret-1

    If necessary, see PyGObject's documentation: https://pygobject.readthedocs.io/en/latest/getting_started.html

    Traceback (most recent call last):
      File "/anaconda/envs/azureml_py36/lib/python3.6/site-packages/msal_extensions/libsecret.py", line 21, in <module>
    import gi  # https://github.com/AzureAD/microsoft-authentication-extensions-for-python/wiki/Encryption-on-Linux
    ModuleNotFoundError: No module named 'gi'
    ```

1. Verwenden Sie das Skript [aml-compute-setup.sh](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/HowTos/aml-compute-setup.sh), das sich im Microsoft Sentinel-Notebooks-GitHub-Repository befindet, um `pygobject` in allen Notebooks und Anaconda-Umgebungen auf der Compute-Instanz automatisch zu installieren.

> [!TIP]
> Sie können diese Warnung auch beheben, indem Sie den folgenden Code in einem Notebook ausführen:
>
> ```python
> !conda install --yes -c conda-forge pygobject
> ```
>


## <a name="next-steps"></a>Nächste Schritte

Integrieren Sie Ihre Notebookumgebung in Big Data-Analysen in Azure Synapse. Weitere Informationen finden Sie unter [Integrieren von Notebooks mit Azure Synapse (Public Preview)](notebooks-with-synapse.md).

Andere im [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel-Notebooks) verfügbare Notebooks sind als nützliche Tools, Veranschaulichungen und Codebeispiele gedacht, die Sie beim Entwickeln eigener Notebooks verwenden können.

Wir freuen uns über Feedback, Vorschläge, gewünschte Funktionen, eigene Notebooks, Fehlerberichte oder Verbesserungen und Ergänzungen zu vorhandenen Notebooks. Besuchen Sie das [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel), um ein Problem zu melden oder eine Verzweigung anzulegen oder um einen Beitrag hochzuladen.

- **Informieren Sie sich** über die Verwendung von Notebooks bei der Bedrohungssuche und -untersuchung, indem Sie sich einige Notebookvorlagen ansehen, z. B. [**Anmeldeinformationsüberprüfung in Azure Log Analytics**](https://www.youtube.com/watch?v=OWjXee8o04M) und **Interaktive Untersuchung – Prozesswarnung**.

    Weitere Notebookvorlagen finden Sie auf der Registerkarte Microsoft Sentinel > **Notebooks** > **Vorlagen**.

- **Weitere Notebooks** finden Sie im [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel-Notebooks):

  - Im Verzeichnis [`Sample-Notebooks`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) befinden sich Beispielnotebooks, die mit Daten gespeichert wurden, die die beabsichtigte Ausgabe veranschaulichen.

  - Das Verzeichnis [`HowTos`](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) enthält Notebooks, die Konzepte wie das Festlegen der Standard-Python-Version oder das Erstellen von Microsoft Sentinel-Lesezeichen aus einem Notebook veranschaulichen.

Weitere Informationen finden Sie unter

- [Tutorial: Erste Schritte mit Jupyter-Notebooks und MSTICPy in Microsoft Sentinel](notebook-get-started.md)
- [Tutorial: Microsoft Sentinel: Notebooks – erste Schritte](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks) (Video)
- [Tutorial: Bearbeiten und Ausführen von Jupyter Notebooks, ohne Azure ML Studio zu verlassen](https://www.youtube.com/watch?v=AAj-Fz0uCNk) (Video)
- [Webinar: Grundlagen von Microsoft Sentinel-Notebooks](https://www.youtube.com/watch?v=rewdNeX6H94)
- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Behalten des Überblicks über Daten bei einer Bedrohungssuche](bookmarks.md)
- [Jupyter, msticpy und Microsoft Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
