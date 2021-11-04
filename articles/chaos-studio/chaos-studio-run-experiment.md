---
title: Ausführen und Verwalten eines Chaosexperiments in Azure Chaos Studio
description: Erfahren Sie, wie Sie ein Chaosexperiment in Azure Chaos Studio starten, stoppen, Details anzeigen und den Verlauf anzeigen können.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 06abbe99467af52cb7867d31f31d4aa2d727d0ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094974"
---
# <a name="run-and-manage-an-experiment-in-azure-chaos-studio"></a>Ausführen und Verwalten eines Experiments in Azure Chaos Studio

Sie können ein Chaos-Experiment verwenden, um zu überprüfen, ob Ihre Anwendung fehlerresistent ist, indem Sie diese Fehler in einer kontrollierten Umgebung verursachen. Dieser Artikel gibt einen Überblick darüber, wie Sie ein zuvor erstelltes Chaosexperiment verwenden können.

## <a name="start-an-experiment"></a>Starten Sie ein Experiment

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Suchen Sie in der Suchleiste nach **Chaos Studio (Vorschau)** .

3. Klicken Sie auf **Experimente**. Dies ist die Ansicht der Experimentliste, in der Sie Experimente starten, stoppen oder löschen oder ein neues Experiment erstellen können.

    ![Experimentierliste im Portal](images/run-experiment-list.png)

4. Klicken Sie auf Ihr Experiment. Auf der Übersichtsseite des Experiments können Sie Ihr Experiment starten, stoppen und bearbeiten, wichtige Details zur Ressource anzeigen und den Verlauf einsehen. Klicken Sie auf die Schaltfläche **Start** und dann auf **OK**, um Ihr Experiment zu starten.

    ![Experiment starten](images/run-experiment-start.png)

5. Der Status des Experiments zeigt *PreProcessingQueued*, dann *WaitingToStart*, und schließlich *Running*.

## <a name="view-experiment-history-and-details"></a>Versuchsverlauf und Details anzeigen

1. Sobald das Experiment läuft, klicken Sie auf **Details** für den aktuellen Lauf unter **History**, um den detaillierten Status und Fehler zu sehen.

    ![Ausführungsverlauf](images/run-experiment-history.png)

2. In der Detailansicht des Experiments wird der Ausführungsstatus jedes Schritts, jeder Verzweigung und jedes Fehlers angezeigt. Klicken Sie auf einen Fehler.

    ![Details zum Experiment](images/run-experiment-details.png)

3. Die Fehlerdetails zeigen zusätzliche Informationen über die Fehlerausführung, einschließlich der fehlgeschlagenen oder erfolgreichen Ziele und der Gründe dafür. Wenn bei der Durchführung Ihres Experiments ein Fehler auftritt, werden hier Informationen zur Fehlersuche angezeigt.

    ![Details zur Störung](images/run-experiment-fault.png)

## <a name="edit-experiment"></a>Experiment bearbeiten

1. Kehren Sie zur Experimentübersicht zurück und klicken Sie auf die Schaltfläche **Bearbeiten**.

    ![Experiment bearbeiten](images/run-edit.png)

2. Dies ist derselbe Versuchsplaner, der auch für die Erstellung des Experiments verwendet wurde. Sie können Schritte, Zweige und Fehler hinzufügen oder entfernen sowie Fehlerparameter und Ziele bearbeiten. Um eine Störung zu bearbeiten, klicken Sie auf das Feld **...** neben der Störung.

    ![Fehler bearbeiten](images/run-edit-ellipses.png)

3. Wenn Sie mit der Bearbeitung fertig sind, klicken Sie auf **Speichern**. Wenn Sie Ihre Änderungen verwerfen möchten, ohne zu speichern, klicken Sie auf die Schaltfläche **Schließen (X)** oben rechts.
  ![Experiment speichern](images/run-edit-save.png)

> [!WARNING]
> Wenn Sie Ihrem Experiment Ziele hinzugefügt haben, denken Sie daran, der Zielressource eine Rollenzuweisung für Ihre Experimentidentität hinzuzufügen.

## <a name="delete-experiment"></a>Experiment löschen
1. Kehren Sie zur Experimentliste zurück und markieren Sie das/die Experiment(e), das/die Sie löschen möchten. Klicken Sie auf **Löschen** in der Symbolleiste oberhalb der Experimentliste. Je nach Bildschirmauflösung müssen Sie möglicherweise auf die Ellipse (...) klicken, um die Löschoption zu sehen.

    ![Experiment löschen](images/run-delete.png)

2. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie die Ressource löschen möchten.

3. Alternativ dazu können Sie ein Experiment öffnen und auf die Schaltfläche **Löschen** in der Symbolleiste klicken.
