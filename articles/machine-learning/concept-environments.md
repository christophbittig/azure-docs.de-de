---
title: Informationen zu Azure Machine Learning-Umgebungen
titleSuffix: Azure Machine Learning
description: In diesem Artikel finden Sie Informationen über Machine Learning-Umgebungen, die reproduzierbare, überprüfbare und portierbare Machine Learning-Abhängigkeitsdefinitionen für verschiedene Computeziele ermöglichen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.openlocfilehash: e798893b28eaaa6aabb1d3f3623aea60df7bc2c6
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576147"
---
# <a name="what-are-azure-machine-learning-environments"></a>Was sind Azure Machine Learning-Umgebungen?

Azure Machine Learning-Umgebungen sind eine Kapselung der Umgebung, in der Ihr Training für das maschinelle Lernen stattfindet. Sie geben die Python-Pakete, Umgebungsvariablen und Softwareeinstellungen bezüglich Ihrer Trainings- und Bewertungsskripts an. Sie geben auch Laufzeiten an (Python, Spark oder Docker). Die Umgebungen sind verwaltete und versionsverwaltete Entitäten innerhalb Ihres Machine Learning-Arbeitsbereichs, die reproduzierbare, überprüfbare und portierbare Machine Learning-Workflows über verschiedene Computeziele hinweg ermöglichen.

Sie können ein `Environment`-Objekt auf Ihrem lokalen Computer verwenden, um Folgendes zu erreichen:
* Entwickeln Sie Ihr Trainingsskript.
* Verwenden Sie dieselbe Umgebung in Azure Machine Learning Compute für das skalierte Modelltraining wieder.
* Stellen Sie Ihr Modell in derselben Umgebung bereit.
* Überprüfen Sie die Umgebung, in der ein bestehendes Modell trainiert wurde.

Das folgende Diagramm veranschaulicht, wie Sie ein einzelnes `Environment`-Objekt sowohl in Ihrer Ausführungskonfiguration (für Trainingszwecke) als auch in Ihrer Rückschluss- und Bereitstellungskonfiguration (für Webdienstbereitstellungen) verwenden können.

![Diagramm einer Umgebung im Machine Learning-Workflow](./media/concept-environments/ml-environment.png)

Die Umgebung, das Computeziel und das Trainingsskript bilden zusammen die Ausführungskonfiguration: die vollständige Spezifikation einer Trainingsausführung.

## <a name="types-of-environments"></a>Typen von Umgebungen

Umgebungen lassen sich weitgehend in drei Kategorien unterteilen: *zusammengestellt*, *benutzerverwaltet* und *systemverwaltet*.

Zusammengestellte Umgebungen werden von Azure Machine Learning bereitgestellt und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Sie sind zur sofortigen Verwendung gedacht und enthalten Sammlungen von Python-Paketen und -Einstellungen, die Ihnen den Einstieg in die verschiedenen Machine Learning-Frameworks erleichtern sollen. Diese vorab erstellten Umgebungen ermöglichen auch eine schnellere Bereitstellungszeit. Eine vollständige Liste finden Sie im [Artikel zu zusammengestellten Umgebungen](resource-curated-environments.md).

In benutzerverwalteten Umgebungen sind Sie für die Einrichtung Ihrer Umgebung und die Installation aller Pakete, die Ihr Trainingsskript auf dem Computeziel benötigt, verantwortlich. Stellen Sie außerdem sicher, dass Sie alle Abhängigkeiten einschließen, die für die Modellbereitstellung erforderlich sind.

Sie verwenden systemverwaltete Umgebungen, wenn Sie möchten, dass [Conda](https://conda.io/docs/) die Python-Umgebung für Sie verwalten soll. Eine neue Conda-Umgebung wird aus Ihrer Conda-Spezifikation auf Grundlage eines Docker-Basisimages materialisiert.

## <a name="create-and-manage-environments"></a>Erstellen und Verwalten von Umgebungen

Sie können Umgebungen von Clients wie dem AzureML Python SDK, Azure Machine Learning CLI, der Seite Umgebungen in Azure Machine Learning Studio und [VS Code Erweiterung](how-to-manage-resources-vscode.md#create-environment) erstellen. Mit jedem Client können Sie das Basisimage, die Dockerfile-Ebene und die Python-Ebene bei Bedarf anpassen.

Bestimmte Codebeispiele finden Sie im Abschnitt „Erstellen einer Umgebung“ unter [Verwenden von Umgebungen](how-to-use-environments.md#create-an-environment). 

Umgebungen können auch problemlos über Ihren Arbeitsbereich verwaltet werden. So können Sie Folgendes tun:

* Registrieren von Umgebungen
* Abrufen von Umgebungen aus Ihrem Arbeitsbereich, die für das Training oder die Bereitstellung verwendet werden sollen
* Erstellen einer neuen Instanz einer Umgebung, indem Sie eine vorhandene bearbeiten
* Betrachten von Änderungen an Ihren Umgebungen im Laufe der Zeit, was die Reproduzierbarkeit sicherstellt
* Automatisches Erstellen von Docker-Images aus Ihren Umgebungen

„Anonyme“ Umgebungen werden beim Übermitteln eines Experiments automatisch bei Ihrem Arbeitsbereich registriert. Sie werden nicht aufgeführt, können aber nach Version abgerufen werden.

Codebeispiele finden Sie im Abschnitt „Verwalten von Umgebungen“ unter [Verwenden von Umgebungen](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Erstellen, Zwischenspeichern und Wiederverwenden von Umgebungen

Azure Machine Learning erstellt Umgebungsdefinitionen in Docker-Images und Conda-Umgebungen. Außerdem werden die Umgebungen zwischengespeichert, sodass sie bei späteren Trainingsdurchläufen und Bereitstellungen von Dienstendpunkten wiederverwendet werden können. Die Remoteausführung eines Trainingsskripts erfordert die Erstellung eines Docker-Images, während eine lokale Ausführung direkt eine Conda-Umgebung verwenden kann. 

### <a name="submitting-a-run-using-an-environment"></a>Übermitteln einer Ausführung mithilfe einer Umgebung

Der Azure Machine Learning Service ruft beim ersten Übermitteln einer Remoteausführung in einer Umgebung eine [ACR-Buildaufgabe](../container-registry/container-registry-tasks-overview.md) in der Azure Container Registry (ACR) auf, die dem Arbeitsbereich zugeordnet ist. Das erstellte Docker-Image wird dann in der ACR des Arbeitsbereichs zwischengespeichert. Zusammengestellte Umgebungen werden durch Docker-Images unterstützt, die in Global ACR zwischengespeichert werden. Zu Beginn der Ausführung wird das Image vom Computeziel von der relevanten ACR abgerufen.

Für lokale Ausführungen wird auf der Grundlage der Umgebungsdefinition eine Docker- oder Conda-Umgebung erstellt. Die Skripts werden dann auf dem Zielcomputer – einer lokalen Laufzeitumgebung oder einer lokalen Docker-Engine – ausgeführt.

### <a name="building-environments-as-docker-images"></a>Erstellen von Umgebungen als Docker-Images

Wenn das Image für eine bestimmte Umgebungsdefinition nicht bereits in der ACR des Arbeitsbereichs vorhanden ist, wird ein neues Image erstellt. Die Imageerstellung besteht aus zwei Schritten:

 1. Herunterladen eines Basisimages und Ausführen beliebiger Docker-Schritte
 2. Erstellen einer Conda-Umgebung gemäß den in der Umgebungsdefinition angegebenen Conda-Abhängigkeiten.

Der zweite Schritt entfällt, wenn Sie [vom Benutzer verwaltete Abhängigkeiten](/python/api/azureml-core/azureml.core.environment.pythonsection) angeben. In diesem Fall sind Sie für die Installation von Python-Paketen verantwortlich, indem Sie diese in Ihr Basisimage einbeziehen oder im ersten Schritt benutzerdefinierte Docker-Schritte festlegen. Sie sind auch dafür verantwortlich, den richtigen Speicherort für die ausführbare Python-Datei anzugeben. Es ist auch möglich, ein [benutzerdefiniertes Docker-Basisimage](./how-to-deploy-custom-container.md) zu verwenden.

### <a name="image-caching-and-reuse"></a>Zwischenspeicherung und Wiederverwendung von Images

Wenn Sie dieselbe Umgebungsdefinition für eine weitere Ausführung verwenden, verwendet Azure Machine Learning das zwischengespeicherte Image aus der ACR des Arbeitsbereichs wieder, um Zeit zu sparen.

Überprüfen Sie die Seite Umgebungen in Azure Machine Learning Studio, oder verwenden Sie die [`Environment.get_image_details`](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-)-Methode, um die Details eines zwischengespeicherten Images anzuzeigen.

AzureML berechnet [einen Hashwert](https://en.wikipedia.org/wiki/Hash_table) aus der Umgebungsdefinition und vergleicht ihn mit den Hashwerten bestehender Umgebungen, Um zu bestimmen, ob ein zwischengespeichertes Image wiederverwendet oder ein neues erstellt werden soll. Der Hashwert basiert auf den folgenden Parametern der Umgebungsdefinition:
 
 * Base image
 * Benutzerdefinierte Docker-Schritte
 * Python-Pakete
 * Spark-Pakete

Der Hashwert wird vom Umgebungsnamen oder der Version nicht beeinflusst. Wenn Sie Ihre Umgebung umbenennen oder eine neue Umgebung mit den gleichen Einstellungen und Paketen wie eine andere Umgebung erstellen, bleibt der Hashwert unverändert. Änderungen der Umgebungsdefinition, z. B. das Hinzufügen oder Entfernen eines Python-Pakets oder die Änderung der Paketversion, führen jedoch zu einer Änderung des entstehenden Hashwerts. Das Ändern der Reihenfolge von Abhängigkeiten oder Kanälen in einer Umgebung ändert auch den Hash und erfordert einen neuen Imagebuild. Ebenso führt jede Änderung an einer zusammengestellten Umgebung zur Erstellung einer neuen „nicht zusammengestellten“ Umgebung. 

> [!NOTE]
> Sie können keine lokalen Änderungen an eine zusammengestellte Umgebung übermitteln, ohne den Namen der Umgebung zu ändern. Die Präfixe „AzureML-“ und „Microsoft“ sind ausschließlich für zusammengestellte Umgebungen reserviert, und Ihre Auftragsübermittlung schlägt fehl, wenn der Name mit einem der beiden beginnt.

Der berechnete Hashwert der Umgebung wird mit den Werten im Arbeitsbereich und in der globalen ACR oder auf dem Computeziel (nur für lokale Ausführungen) verglichen. Wenn es eine Übereinstimmung gibt, wird das zwischengespeicherte Image gepullt und verwendet, andernfalls wird eine Imageerstellung ausgelöst.

Das folgende Diagramm zeigt drei Umgebungsdefinitionen. Zwei davon haben unterschiedliche Namen und Versionen, aber identische Basisimages und Python-Pakete, was zu demselben Hash und dem entsprechenden zwischengespeicherten Image führt. Die dritte Umgebung weist verschiedene Python-Pakete und -Versionen auf und führt daher zu einem anderen Hash und zwischengespeicherten Image.

![Diagramm zur Zwischenspeicherung der Umgebung und Docker-Images](./media/concept-environments/environment-caching.png)

Tatsächliche zwischengespeicherte Images in Ihrem Arbeitsbereich ACR haben Namen wie `azureml/azureml_e9607b2514b066c851012848913ba19f` mit dem Hash, der am Ende angezeigt wird.

>[!IMPORTANT]
> * Wenn Sie eine Umgebung mit einer getrennten Paketabhängigkeit erstellen – z. B. `numpy` –, verwendet die Umgebung die Paketversion, die *beim Erstellen der Umgebung verfügbar war*. Jede zukünftige Umgebung, die eine entsprechende Definition verwendet, nutzt die ursprüngliche Version. 
>
>   Geben Sie zum Aktualisieren des Pakets eine Versionsnummer an, um eine Neuerstellung des Images zu erzwingen. Ein Beispiel dafür ist die Änderung von `numpy` zu `numpy==1.18.1`. Neue Abhängigkeiten, einschließlich geschachtelter Abhängigkeiten, werden installiert, und können ein zuvor funktionierendes Szenario unterbrechen.
>
> * Wenn Sie ein getrenntes Basisimage wie `mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04` in Ihrer Umgebungsdefinition verwenden, wird das Image bei jeder Aktualisierung des `latest`-Tags neu erstellt. Dadurch erhält das Image die neuesten Patches und Systemupdates.

> [!WARNING]
>  Die Methode [`Environment.build`](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) erstellt das zwischengespeicherte Image neu, mit dem möglichen Nebeneffekt, dass losgelöste Pakete aktualisiert werden und die Reproduzierbarkeit für alle Umgebungsdefinitionen, die diesem zwischengespeicherten Image entsprechen, unterbrochen wird.

### <a name="image-patching"></a>Patchen von Images

Microsoft ist für das Patchen der Basisimages für bekannte Sicherheitsrisiken verantwortlich. Updates für unterstützte Images werden alle zwei Wochen veröffentlicht, wobei in der neuesten Version des Images keine ungepatchten Sicherheitsrisiken enthalten sind, die älter als 30 Tage sind. Gepatchte Images werden mit einem neuen unveränderlichen Tag veröffentlicht, und das `:latest`-Tag wird auf die neueste Version des gepatchten Images aktualisiert. 

Wenn Sie Ihre eigenen Images bereitstellen, sind Sie für die Aktualisierung verantwortlich.

Weitere Informationen zu den Basisimages finden Sie unter den folgenden Links:

* [Azure Machine Learning Basisimages](https://github.com/Azure/AzureML-Containers) GitHub Repository
* [Trainieren eines Modells mit einem benutzerdefinierten Image](how-to-train-with-custom-image.md)
* [Bereitstellen eines TensorFlow-Modells mithilfe eines benutzerdefinierten Containers](how-to-deploy-custom-container.md)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [Umgebungen in Azure Machine Learning erstellen und verwenden](how-to-use-environments.md).
* Die [Umgebungsklasse](/python/api/azureml-core/azureml.core.environment%28class%29) finden Sie in der Referenzdokumentation des Python SDK.
