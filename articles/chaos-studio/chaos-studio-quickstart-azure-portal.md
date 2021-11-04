---
title: Erstellen und Ausführen eines Chaos-Experiments mit Azure Chaos Studio
description: Verstehen der Schritte zum Erstellen und Ausführen eines Chaos Studio-Experiments in 10 Minuten
services: chaos-studio
author: prashabora
ms.topic: article
ms.date: 10/21/2021
ms.author: prashabora
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: a5635307b3b736f535dd21f54b3c2b1ec326e5b0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095360"
---
# <a name="quickstart-create-and-run-a-chaos-experiment-using-azure-chaos-studio"></a>Schnellstart: Erstellen und Ausführen eines Chaos-Experiments mit Azure Chaos Studio 
Beginnen Sie mit Chaos Studio, indem Sie das Service-Direktexperiment zum Herunterfahren von VMs verwenden, um Ihren Dienst in der realen Welt resilienter gegenüber diesem Fehler zu machen. 

## <a name="prerequisites"></a>Voraussetzungen
- Öffnen Sie das [Azure-Portal](https://portal.azure.com).
- Einen virtuellen Computer. Wenn Sie noch keine virtuelle Maschine haben, können Sie [mit den folgenden Schritten eine erstellen](../virtual-machines/linux/quick-create-portal.md).

## <a name="enable-chaos-studio-on-the-virtual-machine-you-created"></a>Aktivieren von Chaos Studio auf dem erstellten virtuellen Computer
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie in der Suchleiste nach **Chaos Studio (Vorschau)** .
3. Klicken Sie auf **Ziele** und navigieren Sie zu Ihrem erstellten virtuellen Computer.

4. Markieren Sie das Kästchen neben Ihrem virtuellen Computer und klicken Sie im Dropdown-Menü auf **Ziele aktivieren** und dann auf **Service-Direktziele aktivieren**.

   ![Zielansicht im Azure-Portal](images/quickstart-virtual-machine-enabled.png)

5. Es erscheint eine Benachrichtigung, die anzeigt, dass die ausgewählte(n) Ressource(n) erfolgreich aktiviert wurde(n).
   
   ![Benachrichtigung, dass das Ziel erfolgreich aktiviert wurde](images/tutorial-service-direct-targets-enable-confirm.png)

## <a name="create-an-experiment"></a>Erstellen eines Experiments

1. Klicken Sie auf **Experimente**.                
   ![Zurück zum Experiment](images/quickstart-left-experiment.png)

2. Klicken Sie auf **Ein Experiment hinzufügen**.

   ![Hinzufügen eines Experiments im Azure-Portal](images/add-an-experiment.png)

3. Geben Sie **Abonnement**, **Ressourcengruppe** und **Ort** ein, an dem Sie das Chaos-Experiment durchführen möchten. Gib deinem Experiment einen **Namen**. Klicken Sie auf **Weiter: Experiment-Designer >**

   ![Hinzufügen von Experimentgrundkenntnissen](images/quickstart-service-direct-add-basics.png)

4. Sie befinden sich nun im Chaos Studio Experiment Designer. Geben Sie Ihrem **Schritt** und **Zweig** einen freundlichen Namen, dann klicken Sie auf **Fehler hinzufügen**.

   ![Experiment-Designer](images/quickstart-service-direct-add-designer.png)

5. Wählen Sie im Dropdown-Menü **Herunterfahren von VMs** und geben Sie dann in **Dauer** die Anzahl der Minuten ein, die der Fehler dauern soll. 

   ![Fehlereigenschaften](images/quickstart-service-direct-add-fault.png)

6. Klicken Sie auf **Weiter : Zielressourcen >** .
   ![Ziel hinzufügen](images/quickstart-service-direct-add-targets.png)

7. Klicken Sie auf **Hinzufügen**.

   ![Addt](images/quickstart-add-target.png)

8. Überprüfen Sie, ob Ihr Experiment korrekt ist, und klicken Sie dann auf **Überprüfen + Erstellen**, dann **Erstellen.** .

   ![Erstellen des Experiments](images/quickstart-review-and-create.png)

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Erteilen Sie Ihrem virtuellen Computer eine Experimentiergenehmigung
1. Navigieren Sie zu Ihrem virtuellen Computer und klicken Sie auf **Zugriffskontrolle (IAM)** 
   ![Rollenzuweisung hinzufügen](images/quickstart-access-control.png)
2. Klicken Sie auf **Hinzufügen**.

   ![Schaltfläche „Hinzufügen“](images/add.png)

3. Klicken Sie auf **Rollenzuweisung hinzufügen**.

   ![Schaltfläche Rollenzuweisung hinzufügen](images/add-role-assignment.png)

4. Suchen Sie nach **Mitwirkender für virtuellen Computer** und wählen Sie die Rolle. Klicken Sie auf **Weiter**.

   ![Auswählen der Rolle für den virtuellen Computer](images/quickstart-virtual-machine-contributor.png)
5. Klicken Sie auf **Mitglieder auswählen** und suchen Sie nach Ihrem Experimentnamen. Wählen Sie Ihr Experiment und klicken Sie auf **Auswählen**. 
   ![Auswählen des Experiments](images/quickstart-select-experiment-role-assignment.png)
 
6. Klicken Sie auf **Überprüfen + Zuweisen** und dann auf **Überprüfen + Zuweisen**.



## <a name="run-the-chaos-experiment"></a>Führen Sie das Chaos-Experiment durch

1. Öffnen Sie das Azure-Portal:
    * Wenn Sie ein @microsoft.com Konto verwenden, [klicken Sie auf diesen Link](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Wenn Sie ein externes Konto verwenden, [klicken Sie auf diesen Link](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
2. Markieren Sie das Kästchen neben dem Namen des Experiments und klicken Sie auf **Experiment starten**.
    ![Experiment starten](images/quickstart-experiment-start.png)

3. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Chaosexperiment starten möchten.

    ![Bestätigen Sie, dass Sie das Experiment beginnen möchten](images/start-experiment-confirmation.png)
4. (Optional) Klicken Sie auf den Namen des Experiments, um eine detaillierte Übersicht über den Ausführungsstatus des Experiments zu erhalten.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

1. Markieren Sie das Kästchen neben dem Experimentnamen und klicken Sie auf **Löschen**.

   ![Auswählen des zu löschenden Experiments](images/quickstart-delete-experiment.png)

2. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Experiment löschen möchten.

3. Suchen Sie den virtuellen Computer, den Sie in der Azure-Portal erstellt haben.

   ![Auswählen des virtuellen Computers](images/quickstart-cleanup.png)

4. Klicken Sie auf **Löschen**, um zu vermeiden, dass die Ressource in Rechnung gestellt wird.

   ![Löschen des virtuellen Computers](images/quickstart-cleanup-virtual-machine.png)


## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun ein Service-Direktexperiment zum Herunterfahren des virtuellen Computers durchgeführt haben, sind Sie für Folgendes bereit:
- [Erstellen Sie ein Experiment, das agentenbasierte Fehler verwendet](chaos-studio-tutorial-agent-based.md)
