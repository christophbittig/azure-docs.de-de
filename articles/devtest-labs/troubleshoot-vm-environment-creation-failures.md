---
title: Fehlerbehebung bei VM- und Umgebungsfehlern
description: Erfahren Sie, wie Sie Fehler bei der Erstellung virtueller Computer (VM) und Umgebungen in Azure DevTest Labs beheben können.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c0765254110302c6306cd37b9d55b8465510a586
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561275"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Beheben von Fehlern bei der Erstellung des virtuellen Computers und der Umgebung in Azure DevTest Labs
DevTest Labs zeigt Warnungen an, wenn ein Computername ungültig ist oder Sie im Begriff sind, eine Labrichtlinie zu verletzen. Manchmal wird neben Ihrer Lab-VM oder Ihrem Umgebungsstatus ein rotes `X` angezeigt, das Sie über einen Fehler informiert.  Dieser Artikel enthält einige Tricks, mit denen Sie das zugrunde liegende Problem ermitteln und hoffentlich in Zukunft vermeiden können.

## <a name="portal-notifications"></a>Portalbenachrichtigungen
Wenn Sie das Azure-Portal verwenden, sehen Sie sich als Erstes den Bereich für **Benachrichtigungen** an.  Der Benachrichtigungsbereich, der über die Hauptbefehlsleiste durch Klicken auf das **Glockensymbol** verfügbar ist, zeigt Ihnen an, ob die Erstellung der Lab-VM oder der Umgebung erfolgreich war.  Wenn ein Fehler aufgetreten ist, wird die Fehlermeldung angezeigt, die dem Erstellungsfehler zugeordnet ist. Die Details bieten oft zusätzliche Informationen, die Ihnen helfen, das Problem zu lösen. Im folgenden Beispiel ist bei der Erstellung des virtuellen Computers ein Fehler aufgetreten, da nicht ausreichend Kerne verfügbar sind. Die ausführliche Meldung informiert Sie darüber, wie Sie das Problem beheben und eine Erhöhung der Kernkontingente anfordern können.

![Benachrichtigung im Azure-Portal](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>Beschädigter virtueller Computer
Wenn der Status Ihres virtuellen Computers im Lab als **Beschädigt** angezeigt wird, wurde der zugrunde liegende virtuelle Computer möglicherweise über die Seite **Virtueller Computer** gelöscht, zu der Benutzer über die Seite **Virtual Machines** (nicht über die Seite „DevTest Labs“) navigieren können. Bereinigen Sie Ihr Lab in DevTest Labs, indem Sie den virtuellen Computer aus dem Lab löschen. Erstellen Sie Ihren virtuellen Computer anschließend neu im Lab. 

![Virtueller Computer in beschädigtem Zustand](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Aktivitätsprotokolle
Überprüfen Sie die Aktivitätsprotokolle, wenn Sie einen Fehler nach dem Versuch, Ihre VM oder Umgebung zu erstellen, untersuchen. In diesem Abschnitt erfahren Sie, wie Sie Protokolle für virtuelle Computer und Umgebungen finden.

## <a name="activity-logs-for-virtual-machines"></a>Aktivitätsprotokolle für virtuelle Computer

1. Wählen Sie auf der Startseite Ihres Labs den virtuellen Computer aus, um die Seite **Virtueller Computer** zu starten.
2. Wählen Sie auf der Seite **Virtueller Computer** im Abschnitt **ÜBERWACHUNG** des linken Menüs die Option **Aktivitätsprotokoll** aus, um alle mit dem virtuellen Computer verbundenen Protokolle anzuzeigen.
3. Wählen Sie in den Aktivitätsprotokollpositionen den Vorgang aus, bei dem ein Fehler aufgetreten ist. Normalerweise wird der fehlerhafte Vorgang als `Write Virtualmachines` bezeichnet.
4. Wechseln Sie im rechten Bereich zur Registerkarte „JSON“. Die Details werden in der JSON-Ansicht des Protokolls angezeigt.

    ![Aktivitätsprotokoll für einen virtuellen Computer](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Durchsuchen Sie das JSON-Protokoll, bis Sie die Eigenschaft `statusMessage` finden. Sie enthält die Hauptfehlermeldung und ggf. weitere Detailinformationen. Der folgende JSON-Code ist ein Beispiel für den genannten Fehler aufgrund des überschrittenen Kernkontingents, der zuvor in diesem Artikel beschrieben wurde.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Aktivitätsprotokoll für eine Umgebung

Führen Sie die folgenden Schritte aus, um das Aktivitätsprotokoll für die Erstellung einer Umgebung anzuzeigen:

1. Wählen Sie auf der Startseite Ihres Labs **Konfiguration und Richtlinien** im linken Menü aus.
2. Wählen Sie auf der Seite **Konfiguration und Richtlinien** im Menü die Option **Aktivitätsprotokolle** aus.
3. Suchen Sie nach dem Fehler in der Aktivitätsliste des Protokolls und wählen Sie ihn aus.
4. Wechseln Sie im rechten Bereich zur Registerkarte „JSON“, und suchen Sie nach **statusMessage**.

    ![Aktivitätsprotokoll der Umgebung](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Resource Manager-Vorlagenbereitstellungsprotokolle
Wenn Ihre Umgebung oder der virtuelle Computer durch Automation erstellt wurde, gibt es einen letzten Ort, an dem Sie nach Fehlerinformationen suchen können. Dabei handelt es sich um das Azure Resource Manager-Vorlagenbereitstellungsprotokoll. Wenn eine Labressource durch Automation erstellt wird, erfolgt dies oft über eine Azure Resource Manager-Vorlagenbereitstellung. Azure Resource Manager-Beispielvorlagen, die DevTest Labs-Ressourcen erstellen, finden Sie unter [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).

Führen Sie die folgenden Schritte aus, um die Labprotokolle für die Vorlagenbereitstellung anzuzeigen:

1. Starten Sie die Seite für die Ressourcengruppe, in der sich das Lab befindet.
2. Wählen Sie **Bereitstellungen** im linken Menü unter **Einstellungen** aus.
3. Suchen Sie nach Bereitstellungen mit einem fehlerhaften Status und wählen Sie ihn aus.
4. Wählen Sie auf der Seite **Bereitstellung** den Link **Vorgangsdetails** für den fehlgeschlagenen Vorgang aus.
5. Im Fenster **Vorgangsdetails** werden Details zu dem Vorgang angezeigt, bei dem ein Fehler aufgetreten ist.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Beheben von Artefaktfehlern](devtest-lab-troubleshoot-artifact-failure.md).
