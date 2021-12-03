---
title: Erstellen und Ausführen eines Chaos-Experiments mit Azure Chaos Studio
description: Verstehen der Schritte zum Erstellen und Ausführen eines Chaos Studio-Experiments in 10 Minuten
services: chaos-studio
author: prashabora
ms.topic: article
ms.date: 11/10/2021
ms.author: prashabora
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: ec89106c1aee40bc11e6ee2246ef2e01cb8fd466
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282099"
---
# <a name="quickstart-create-and-run-a-chaos-experiment-using-azure-chaos-studio"></a>Schnellstart: Erstellen und Ausführen eines Chaos-Experiments mit Azure Chaos Studio 
Beginnen Sie mit Chaos Studio, indem Sie das Service-Direktexperiment zum Herunterfahren von VMs verwenden, um Ihren Dienst in der realen Welt resilienter gegenüber diesem Fehler zu machen. 

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Eine virtuelle Linux-Maschine. Wenn Sie noch keine virtuelle Maschine haben, können Sie [mit den folgenden Schritten eine erstellen](../virtual-machines/linux/quick-create-portal.md).

## <a name="register-the-chaos-studio-resource-provider"></a>Registrieren des Ressourcenanbieters Chaos Studio
Wenn Sie Chaos Studio zum ersten Mal verwenden, müssen Sie zuerst den Ressourcenanbieter von Chaos Studio registrieren, bevor Sie Ressourcen integrieren und ein Experiment erstellen. Dies muss für jedes Abonnement erfolgen, in dem Sie Chaos Studio verwenden.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
2. Suchen Sie nach **Abonnements** und öffnen Sie die Seite Abonnementverwaltung.
3. Klicken Sie auf das Abonnement, in dem Sie Chaos Studio verwenden möchten.
4. Wählen Sie im linken Navigationsbereich den Eintrag **Ressourcenanbieter** aus.
5. Suchen Sie in der Liste der Ressourcenanbieter nach **Microsoft.Chaos**.
6. Klicken Sie auf den Anbieter Microsoft.Chaos und dann auf die Schaltfläche **Registrieren**.

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
- [Erstellen Sie ein Experiment, das agentenbasierte Fehler verwendet](chaos-studio-tutorial-agent-based-portal.md)
