---
title: Verwalten von Umgebungen im Studio
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Umgebungen im Azure Machine Learning Studio erstellen und verwalten können.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: 38dc05a1b2d0baf4058ce3c4d8a90384f7dce8ff
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560889"
---
# <a name="manage-software-environments-in-azure-machine-learning-studio"></a>Verwalten von Softwareumgebungen in Azure Machine Learning Studio

In diesem Artikel erfahren Sie, wie Sie Azure Machine Learning-[Umgebungen](/python/api/azureml-core/azureml.core.environment.environment) im Azure Machine Learning Studio erstellen und verwalten können. Verwenden Sie die Umgebungen, um die Softwareabhängigkeiten Ihrer Projekte in ihrem zeitlichen Ablauf nachzuverfolgen und zu reproduzieren.

Die Beispiele in diesem Artikel veranschaulichen Folgendes:

* Durchsuchen zusammengestellter Umgebungen.
* Erstellen einer Umgebung und Angeben von Paketabhängigkeiten
* Bearbeiten einer vorhandenen Umgebungsspezifikation und ihrer Eigenschaften.
* Erneutes Erstellen einer Umgebung und Anzeigen von Protokollen zur Imageerstellung.

Eine allgemeine Übersicht zur Funktionsweise von Umgebungen in Azure Machine Learning finden Sie unter [Was sind ML-Umgebungen?](concept-environments.md). Weitere Informationen finden Sie unter [Einrichten einer Entwicklungsumgebung für Azure Machine Learning](how-to-configure-environment.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

## <a name="browse-curated-environments"></a>Durchsuchen zusammengestellter Umgebungen

Zusammengestellte Umgebungen enthalten Sammlungen mit Python-Paketen und sind standardmäßig in Ihrem Arbeitsbereich verfügbar. Diese Umgebungen werden durch zwischengespeicherte Docker-Images unterstützt, wodurch die Kosten für die Vorbereitung der Ausführung reduziert und Trainings- und Rückschlussszenarios unterstützt werden. 

Klicken Sie auf eine Umgebung, um detaillierte Informationen über deren Inhalt zu erhalten. Weitere Informationen finden Sie unter [Azure Machine Learning – zusammengestellte Umgebungen](resource-curated-environments.md). 

## <a name="create-an-environment"></a>Erstellen einer Umgebung

So erstellen Sie eine Umgebung
1. Öffnen Sie Ihren Arbeitsbereich in [Azure Machine Learning Studio](https://ml.azure.com).
1. Wählen Sie auf der linken Seite **Umgebungen** aus.
1. Wählen Sie die Registerkarte **Benutzerdefinierte Umgebungen** aus. 
1. Wählen Sie die Schaltfläche **Erstellen**. 

Erstellen Sie eine Umgebung, indem Sie eine der folgenden Optionen angeben:
* [Pip-Anforderungsdatei](https://pip.pypa.io/en/stable/cli/pip_install)
* [Conda YAML-Datei](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html)
* [Docker-Image](https://hub.docker.com/search?q=&type=image)
* [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

:::image type="content" source="media/how-to-manage-environments-in-studio/create-page.jpg" alt-text="Assistent für die Umgebungserstellung":::

Sie können die Konfigurationsdatei anpassen, Tags und Beschreibungen hinzufügen und die Eigenschaften überprüfen, bevor Sie die Entität erstellen. 

Wenn eine neue Umgebung denselben Namen wie eine bestehende Umgebung im Arbeitsbereich erhält, wird eine neue Version der bestehenden Umgebung erstellt.

## <a name="view-and-edit-environment-details"></a>Anzeigen und Bearbeiten von Umgebungsdetails

Nachdem eine Umgebung erstellt wurde, können Sie ihre Details durch Anklicken des Namens anzeigen. Verwenden Sie die Dropdownliste, um verschiedene Versionen der Umgebung auszuwählen. Hier können Sie Metadaten und den Inhalt der Umgebung über ihre Docker- und Conda-Ebenen anzeigen. 

Klicken Sie auf die Stiftsymbole, um Tags und Beschreibungen sowie die Konfigurationsdateien oder das Image zu bearbeiten. Beachten Sie, dass jede Änderung an den Docker- oder Conda-Abschnitten eine neue Version der Umgebung erstellt. 

:::image type="content" source="media/how-to-manage-environments-in-studio/details-page.jpg" alt-text="Seite „Umgebungsdetails“":::

## <a name="view-image-build-logs"></a>Anzeigen von Protokollen zur Imageerstellung

Klicken Sie auf der Detailseite auf die Registerkarte **Erstellungsprotokoll**, um die Protokolle zum Erstellen der Images einer Umgebungsversion anzuzeigen. 

## <a name="rebuild-an-environment"></a>Erneutes Erstellen einer Umgebung

Klicken Sie auf der Detailseite auf die Schaltfläche **Neu erstellen**, um die Umgebung neu zu erstellen. Alle nicht angehefteten Paketversionen in Ihren Konfigurationsdateien können mit dieser Aktion auf die neueste Version aktualisiert werden. 
