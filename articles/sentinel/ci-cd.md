---
title: Bereitstellen benutzerdefinierter Inhalte aus Ihrem Repository | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Verbindungen mit einem GitHub- oder Azure DevOps-Repository erstellen, in dem Sie Ihre benutzerdefinierten Inhalte speichern und diese für Azure Sentinel bereitstellen können.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/20/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2b31e2e67058e6762e590f99e49eb1e4658b6c2e
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990075"
---
# <a name="deploy-custom-content-from-your-repository-public-preview"></a>Bereitstellen benutzerdefinierter Inhalte aus Ihrem Repository (Public Preview)

> [!IMPORTANT]
>
> Die Azure Sentinel-Seite **Repositorys** befindet sich derzeit in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

*Inhalte* von Azure Sentinel sind SIEM-Inhalte (Security Information & Event Management), die Kunden unter anderem bei der Erfassung und Überwachung sowie bei Warnungen und beim Hunting in Azure Sentinel unterstützen. Zu Azure Sentinel-Inhalten zählen beispielsweise Datenconnectors, Parser, Arbeitsmappen und Analyseregeln. Weitere Informationen finden Sie unter [Informationen zu Azure Sentinel-Inhalt und -Lösungen](sentinel-solutions.md).

Sie können die über den Azure Sentinel Content Hub bereitgestellten vorgefertigten (integrierten) Inhalte verwenden und an Ihre eigenen Anforderungen anpassen oder von Grund auf eigene benutzerdefinierte Inhalte erstellen.

Wenn Sie benutzerdefinierte Inhalte erstellen, können Sie sie in Ihren eigenen Azure Sentinel-Arbeitsbereichen oder in einem externen Repository für die Quellcodeverwaltung speichern und verwalten – beispielsweise in einem GitHub- oder Azure DevOps-Repository. In diesem Artikel erfahren Sie, wie Sie die Verbindungen zwischen Azure Sentinel und externen Repositorys für die Quellcodeverwaltung erstellen und verwalten. Wenn Sie Ihre Inhalte in einem externen Repository verwalten, können Sie die Inhalte außerhalb von Azure Sentinel aktualisieren und automatisch in Ihren Arbeitsbereichen bereitstellen lassen.

> [!TIP]
> In diesem Artikel wird *nicht* beschrieben, wie Sie bestimmte Inhaltstypen von Grund auf neu erstellen. Weitere Informationen finden Sie im entsprechenden [Azure Sentinel-GitHub-Wiki](https://github.com/Azure/Azure-Sentinel/wiki#get-started) für den jeweiligen Inhaltstyp.
>

## <a name="prerequisites-and-scope"></a>Voraussetzungen und Umfang

Stellen Sie sicher, dass Sie über Folgendes verfügen, bevor Sie Ihren Azure Sentinel-Arbeitsbereich mit einem externen Repository für die Quellcodeverwaltung verbinden:

- Zugriff auf ein GitHub- oder Azure DevOps-Repository mit allen benutzerdefinierten Inhaltsdateien, die Sie in Ihren Arbeitsbereichen bereitstellen möchten, in relevanten [ARM-Vorlagen (Azure Resource Manager)](/azure/azure-resource-manager/templates/).

    Azure Sentinel unterstützt derzeit nur Verbindungen mit GitHub- und Azure DevOps-Repositorys.

- Die Rolle **Besitzer** in der Ressourcengruppe, die Ihren Azure Sentinel-Arbeitsbereich enthält. Die Rolle **Besitzer** ist erforderlich, um die Verbindung zwischen Azure Sentinel und Ihrem Repository für die Quellcodeverwaltung herstellen zu können.

### <a name="maximum-connections-and-deployments"></a>Maximale Anzahl von Verbindungen und Bereitstellungen

- Jeder Azure Sentinel-Arbeitsbereich ist derzeit auf **fünf Verbindungen** beschränkt.

- Jede Azure-Ressourcengruppe ist im Laufe ihrer Bereitstellung auf **800 Bereitstellungen** beschränkt. Wenn Ihre Ressourcengruppen eine hohe Anzahl von ARM-Vorlagenbereitstellungen enthalten, tritt möglicherweise ein Fehler vom Typ `Deployment QuotaExceeded` auf. Weitere Informationen finden Sie in der Dokumentation zu Azure Resource Manager-Vorlagen unter [DeploymentQuotaExceeded](/azure/azure-resource-manager/templates/deployment-quota-exceeded).

### <a name="validate-your-content"></a>Überprüfen Ihrer Inhalte

Wenn Sie Inhalte für Azure Sentinel über eine Repositoryverbindung bereitstellen, wird lediglich überprüft, ob die Daten im korrekten ARM-Vorlagenformat vorliegen.

Es wird empfohlen, die Inhaltsvorlagen mithilfe Ihres regulären Überprüfungsprozesses zu überprüfen. Sie können den [Azure Sentinel-GitHub-Validierungsprozess](https://github.com/Azure/Azure-Sentinel/wiki#test-your-contribution) sowie die entsprechenden Tools nutzen, um einen eigenen Validierungsprozess einzurichten.

## <a name="connect-a-repository"></a>Verbinden eines Repositorys

Hier erfahren Sie, wie Sie ein GitHub- oder Azure DevOps-Repository mit Ihrem Azure Sentinel-Arbeitsbereich verbinden, um Ihre benutzerdefinierten Inhalte dort zu speichern und zu verwalten anstatt in Azure Sentinel.

Jede Verbindung kann mehrere Arten von benutzerdefinierten Inhalten unterstützen, einschließlich Analyseregeln, Automatisierungsregeln, Hunting-Abfragen, Parser, Playbooks und Arbeitsmappen. Weitere Informationen finden Sie unter [Informationen zu Azure Sentinel-Inhalt und -Lösungen](sentinel-solutions.md).


**So erstellen Sie die Verbindung:**

1. Stellen Sie sicher, dass Sie bei Ihrer Quellcodeverwaltungs-App mit den Anmeldeinformationen angemeldet sind, die Sie für Ihre Verbindung verwenden möchten.  Wenn Sie derzeit mit anderen Anmeldeinformationen angemeldet sind, melden Sie sich zuerst ab.

1. Wählen Sie in Azure Sentinel links unter **Inhaltsverwaltung** die Option **Repositorys** aus.

1. Wählen Sie **Neu hinzufügen** aus, und geben Sie dann auf der Seite **Neue Verbindung erstellen** einen aussagekräftigen Namen und eine Beschreibung für Ihre Verbindung ein.

1. Wählen Sie in der Dropdownliste **Quellcodeverwaltung** die Art des Repositorys aus, mit dem Sie eine Verbindung herstellen möchten, und wählen Sie anschließend **Autorisieren** aus.

1. Wählen Sie je nach Verbindungstyp eine der folgenden Registerkarten aus:
    # <a name="github"></a>[GitHub](#tab/github)

    1. Geben Sie bei entsprechender Aufforderung Ihre GitHub-Anmeldeinformationen ein.

        Wenn Sie erstmals eine Verbindung hinzufügen, wird ein neues Browserfenster oder ein neuer Tab geöffnet, in dem Sie aufgefordert werden, die Verbindung mit Azure Sentinel zu autorisieren. Falls Sie im gleichen Browser bereits bei Ihrem GitHub-Konto angemeldet sind, werden Ihre GitHub-Anmeldeinformationen automatisch aufgefüllt.

    1. Auf der Seite **Neue Verbindung erstellen** wird nun ein Bereich vom Typ **Repository** angezeigt. Dort können Sie ein vorhandenes Repository auswählen, mit dem eine Verbindung hergestellt werden soll. Wählen Sie in der Liste Ihr Repository und anschließend **Repository hinzufügen** aus.

        Wenn Sie erstmals eine Verbindung mit einem Repository herstellen, wird ein neues Browserfenster oder ein neuer Tab geöffnet, in dem Sie aufgefordert werden, die App **Azure-Sentinel** in Ihrem Repository zu installieren. Sollten Sie über mehrere Repositorys verfügen, wählen Sie die Repositorys aus, in denen Sie die App **Azure-Sentinel** installieren möchten, und installieren Sie sie.

        Daraufhin werden Sie zu GitHub weitergeleitet, um mit der App-Installation fortzufahren.

    1. Nachdem die App **Azure-Sentinel** in Ihrem Repository installiert wurde, wird die Dropdownliste **Branch** auf der Seite **Neue Verbindung erstellen** mit Ihren Branches aufgefüllt. Wählen Sie den Branch aus, den Sie mit Ihrem Azure Sentinel-Arbeitsbereich verbinden möchten.

    1. Wählen Sie in der Dropdownliste **Inhaltstypen** den Inhaltstyp aus, den Sie bereitstellen möchten.

        - Sowohl Parser als auch Hunting-Abfragen verwenden die API **Gespeicherte Suchvorgänge**, um Inhalte für Azure Sentinel bereitzustellen. Wenn Sie einen dieser Inhaltstypen auswählen und in Ihrem Branch auch Inhalte des anderen Typs vorhanden sind, werden beide Inhaltstypen bereitgestellt.

        - Bei allen anderen Inhaltstypen führt die Wahl eines Inhaltstyps im Bereich **Neue Verbindung erstellen** dazu, dass nur diese Inhalte für Azure Sentinel bereitgestellt werden. Inhalte anderer Art werden nicht bereitgestellt.

    1. Wählen Sie **Erstellen** aus, um die Verbindung zu erstellen. Beispiel:

        :::image type="content" source="media/ci-cd/create-new-connection-github.png" alt-text="Screenshot: Neue GitHub-Repositoryverbindung":::


    # <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

    Sie werden bei Azure DevOps automatisch mit Ihren aktuellen Azure-Anmeldeinformationen angemeldet. Wenn Sie bei Azure DevOps derzeit nicht mit den Anmeldeinformationen angemeldet sind, die Sie auch in Azure Sentinel verwenden, wechseln Sie Ihr Konto in Azure DevOps, sodass es dem Konto für Azure Sentinel entspricht.

    1.  Wählen Sie in den in Azure Sentinel angezeigten Dropdownlisten Ihre **Organisation**, Ihr **Projekt**, Ihr **Repository**, Ihren **Branch** und Ihre **Inhaltstypen** aus.

        - Sowohl Parser als auch Hunting-Abfragen verwenden die API **Gespeicherte Suchvorgänge**, um Inhalte für Azure Sentinel bereitzustellen. Wenn Sie einen dieser Inhaltstypen auswählen und in Ihrem Branch auch Inhalte des anderen Typs vorhanden sind, werden beide Inhaltstypen bereitgestellt.

        - Bei allen anderen Inhaltstypen führt die Wahl eines Inhaltstyps im Bereich **Neue Verbindung erstellen** dazu, dass nur diese Inhalte für Azure Sentinel bereitgestellt werden. Inhalte anderer Art werden nicht bereitgestellt.

    1. Wählen Sie **Erstellen** aus, um die Verbindung zu erstellen. Beispiel:

        :::image type="content" source="media/ci-cd/create-new-connection-devops.png" alt-text="Screenshot: Neue GitHub-Repositoryverbindung":::

    ---

    > [!NOTE]
    > In einem Azure Sentinel-Arbeitsbereich können keine doppelten Verbindungen mit dem gleichen Repository und Branch erstellt werden.
    >

Nachdem die Verbindung erstellt wurde, wird ein neuer Workflow oder eine neue Pipeline in Ihrem Repository generiert, und die in Ihrem Repository gespeicherten Inhalte werden in Ihrem Azure Sentinel-Arbeitsbereich bereitgestellt.

Die Bereitstellungszeit kann abhängig von der Menge der bereitgestellten Inhalte variieren. 

### <a name="view-the-deployment-status"></a>Sehen Sie sich den Bereitstellungsstatus an:

- **Auf GitHub:** Auf der Registerkarte **Aktionen** des Repositorys. Wählen Sie die dort angezeigte **YAML**-Workflowdatei aus, um auf ausführliche Bereitstellungsprotokolle und ggf. auf spezifische Fehlermeldungen zuzugreifen.
- **In Azure DevOps:** Auf der Registerkarte **Pipelines** des Repositorys.

Nach Abschluss der Bereitstellung geschieht Folgendes:

- Die in Ihrem Repository gespeicherten Inhalte werden in Ihrem Azure Sentinel-Arbeitsbereich auf der entsprechenden Azure Sentinel-Seite angezeigt.

- Die Verbindungsdetails auf der Seite **Repositorys** werden mit dem Link zu den Bereitstellungsprotokollen der Verbindung aktualisiert. Zum Beispiel:

    :::image type="content" source="media/ci-cd/deployment-logs-link.png" alt-text="Screenshot: Bereitstellungsprotokolle einer GitHub-Repositoryverbindung":::

### <a name="customize-the-deployment-workflow"></a>Anpassen des Bereitstellungsworkflows

Bei der standardmäßigen Inhaltsbereitstellung werden alle relevanten benutzerdefinierten Inhalte aus dem verbundenen Repositorybranch bereitgestellt, sobald ein Pushvorgang für ein Element in diesem Branch ausgeführt wird.

Sollte die Standardkonfiguration für Ihre Inhaltsbereitstellung von GitHub oder Azure DevOps nicht alle Ihre Anforderungen erfüllen, können Sie die Umgebung an Ihre Anforderungen anpassen.

Beispielsweise können Sie verschiedene Bereitstellungstrigger konfigurieren oder Inhalte nur aus einem bestimmten Stammordner bereitstellen.

Wählen Sie je nach Verbindungstyp eine der folgenden Registerkarten aus:

# <a name="github"></a>[GitHub](#tab/github)

**So passen Sie Ihren GitHub-Bereitstellungsworkflow an:**

1. Wechseln Sie auf GitHub zu Ihrem Repository, und suchen Sie im Verzeichnis `.github/workflows` nach Ihrem Workflow.

    Der Workflowname wird in der ersten Zeile der Workflowdatei angezeigt und basiert standardmäßig auf folgender Namenskonvention: `Deploy Content to <workspace-name> [<deployment-id>]`.

    Beispiel: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. Klicken Sie rechts oben auf der Seite auf die Stiftschaltfläche, um die Datei zur Bearbeitung zu öffnen, und ändern Sie die Bereitstellung wie folgt:

    - Aktualisieren Sie zum **Ändern des Bereitstellungstriggers** den Codeabschnitt `on`. Darin wird das Ereignis beschrieben, das die Ausführung des Workflows auslöst.

        Standardmäßig ist diese Konfiguration auf `on: push` festgelegt. Das bedeutet, dass der Workflow bei jedem Pushvorgang für den verbundenen Branch ausgelöst wird. Dies schließt sowohl Änderungen an vorhandenen Inhalten als auch das Hinzufügen neuer Inhalte zum Repository ein. Zum Beispiel:

        ```yml
        on:
            push:
                branches: [ main ]
                paths:
                - `**`
                - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
                - `.github/workflows/sentinel-deploy-<deployment-id>.yml`
        ```

        Sie können diese Einstellungen ändern, um beispielsweise die regelmäßige Ausführung des Workflows zu planen oder verschiedene Workflowereignisse miteinander zu kombinieren.

        Weitere Informationen zum Konfigurieren von Workflowereignissen finden Sie in der [GitHub-Dokumentation](https://docs.github.com/en/actions/learn-github-actions/events-that-trigger-workflows#configuring-workflow-events).

    - **So ändern Sie den Bereitstellungspfad:**

        In der oben gezeigten Standardkonfiguration für den Abschnitt `on` geben die Platzhalter (`**`) in der ersten Zeile des Abschnitts `paths` an, dass sich der gesamte Branch am Pfad für die Bereitstellungstrigger befindet.

        Diese Standardkonfiguration bedeutet, dass jedes Mal, wenn Inhalte an einen beliebigen Teil des Branchs gepusht werden, ein Bereitstellungsworkflow ausgelöst wird.

        Der Abschnitt `jobs` weiter unten in der Datei enthält die folgende Standardkonfiguration: `directory: '${{ github.workspace }}'`. Diese Zeile gibt an, dass sich der gesamte GitHub-Branch am Pfad für die Inhaltsbereitstellung befindet und nicht nach Ordnerpfaden gefiltert wird.

        Wenn Inhalte nur von einem bestimmten Ordnerpfad aus bereitgestellt werden sollen, fügen Sie ihn sowohl der Konfiguration `paths` als auch der Konfiguration `directory` hinzu. Wenn Inhalte also beispielsweise nur aus einem Stammordner namens `SentinelContent` bereitgestellt werden sollen, aktualisieren Sie Ihren Code wie folgt:

        ```yml
        paths:
        - `SentinelContent/**`
        - `!.github/workflows/**` # this filter prevents other workflow changes from triggering this workflow
        - `.github/workflows/sentinel-deploy-<deployment-id>.yml`

        ...
            directory: '${{ github.workspace }}/SentinelContent'
        ```

Weitere Informationen zu GitHub Actions sowie zur Bearbeitung von GitHub-Workflows finden Sie in der [GitHub-Dokumentation](https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#onpushpull_requestpaths).

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

**So passen Sie Ihre Azure DevOps-Bereitstellungspipeline an:**

1. Wechseln Sie in Azure DevOps zu Ihrem Repository, und suchen Sie im Verzeichnis `.sentinel` nach der Pipelinedefinitionsdatei.

    Der Pipelinename wird in der ersten Zeile der Pipelinedatei angezeigt und basiert standardmäßig auf folgender Namenskonvention: `Deploy Content to <workspace-name> [<deployment-id>]`.

    Beispiel: `name: Deploy Content to repositories-demo [xxxxx-dk5d-3s94-4829-9xvnc7391v83a]`

1. Klicken Sie rechts oben auf der Seite auf die Stiftschaltfläche, um die Datei zur Bearbeitung zu öffnen, und ändern Sie die Bereitstellung wie folgt:

    - Aktualisieren Sie zum **Ändern des Bereitstellungstriggers** den Codeabschnitt `trigger`. Darin wird das Ereignis beschrieben, das die Ausführung des Workflows auslöst.

        Standardmäßig ist diese Konfiguration so festgelegt, dass der Workflow bei jedem Pushvorgang für den verbundenen Branch ausgelöst wird. Dies schließt sowohl Änderungen an vorhandenen Inhalten als auch das Hinzufügen neuer Inhalte zum Repository ein.

        Ändern Sie diesen Trigger in einen beliebigen verfügbaren Azure DevOps-Trigger (beispielsweise in einen Zeitplan- oder Pull Request-Trigger). Weitere Informationen finden Sie in der [Azure DevOps-Dokumentation zu Triggern](/azure/devops/pipelines/yaml-schema).


    - **So ändern Sie den Bereitstellungspfad:**

        Die Standardkonfiguration für den Abschnitt `trigger` enthält den folgenden Code. Dieser gibt an, dass sich der Branch `main` am Pfad für die Bereitstellungstrigger befindet:

        ```yml
        trigger:
            branches:
                include:
                - main
        ```

        Diese Standardkonfiguration bedeutet, dass jedes Mal, wenn Inhalte an einen beliebigen Teil des Branchs `main` gepusht werden, eine Bereitstellungspipeline ausgelöst wird.

        Wenn Inhalte nur von einem bestimmten Ordnerpfad aus bereitgestellt werden sollen, fügen Sie den Ordnernamen nach Bedarf dem Abschnitt `include` (für den Trigger) und dem Abschnitt `steps` (für den Bereitstellungspfad) hinzu.

        Wenn Inhalte also beispielsweise nur aus einem Stammordner namens `SentinelContent` in Ihrem Branch `main` bereitgestellt werden sollen, fügen Sie Ihrem Code die Einstellungen `include` und `workingDirectory` hinzu:

        ```yml
        paths:
            exclude:
            - .sentinel/*
            include:
            - .sentinel/sentinel-deploy-39d8ekc8-397-5963-49g8-5k63k5953829.yml
            - SentinelContent
        ....
        steps:
        - task: AzurePowerShell@5
          inputs:
            azureSubscription: `Sentinel_Deploy_ServiceConnection_0000000000000000`
            workingDirectory: `SentinelContent`
        ```

Weitere Informationen zum YAML-Schema von Azure DevOps finden Sie in der [Azure DevOps-Dokumentation](/azure/devops/pipelines/yaml-schema).

---

> [!IMPORTANT]
> Die Triggerpfad- und Bereitstellungspfadverzeichnisse müssen sowohl bei GitHub als auch bei Azure DevOps konsistent sein.
>
## <a name="edit-or-delete-content-in-your-repository"></a>Bearbeiten oder Löschen von Inhalten in Ihrem Repository

Nachdem Sie erfolgreich eine Verbindung mit Ihrem Repository für die Quellcodeverwaltung hergestellt haben, wird der Bereitstellungsworkflow bei jeder Änderung oder Ergänzung des Inhalts erneut ausgeführt und stellt alle Inhalte im Repository für alle verbundenen Azure Sentinel-Arbeitsbereiche bereit.

Es wird empfohlen, in einem verbundenen Repository gespeicherte Inhalte *ausschließlich* im Repository und nicht in Azure Sentinel zu bearbeiten. Wenn Sie beispielsweise Ihre Analyseregeln ändern möchten, nehmen Sie diese Änderungen direkt auf GitHub oder in Azure DevOps vor.

Falls Sie den Inhalt in Azure Sentinel bearbeitet haben, müssen Sie ihn in Ihr Repository für die Quellcodeverwaltung exportieren, um zu verhindern, dass Ihre Änderungen bei der nächsten Bereitstellung des Repositoryinhalts in Ihrem Arbeitsbereich überschrieben werden.

Achten Sie beim Löschen von Inhalten darauf, dass Sie sie sowohl aus Ihrem Repository als auch aus dem Azure-Portal löschen. Inhalte, die Sie aus Ihrem Repository löschen, werden nicht automatisch aus Ihrem Azure Sentinel-Arbeitsbereich gelöscht.

## <a name="remove-a-repository-connection"></a>Entfernen einer Repositoryverbindung

Hier erfahren Sie, wie Sie die Verbindung mit einem Repository für die Quellcodeverwaltung aus Azure Sentinel entfernen.

**So entfernen Sie die Verbindung:**

1. Wählen Sie in Azure Sentinel links unter **Inhaltsverwaltung** die Option **Repositorys** aus.
1. Wählen Sie im Raster die Verbindung aus, die Sie entfernen möchten, und wählen Sie dann **Löschen** aus.
1. Wählen Sie **Ja** aus, um den Löschvorgang zu bestätigen.

Nachdem Sie die Verbindung entfernt haben, bleiben Inhalte, die zuvor über die Verbindung bereitgestellt wurden, in Ihrem Azure Sentinel-Arbeitsbereich. Inhalte, die dem Repository nach dem Entfernen der Verbindung hinzugefügt werden, werden nicht bereitgestellt.

> [!TIP]
> Falls beim Löschen der Verbindung Probleme auftreten oder eine Fehlermeldung angezeigt wird, empfiehlt es sich, die Quellcodeverwaltung zu überprüfen, um sicherzugehen, dass der GitHub-Workflow oder die Azure DevOps-Pipeline, der bzw. die der Verbindung zugeordnet war, gelöscht wurde.
>

### <a name="removing-the-azure-sentinel-app-from-your-github-repository"></a>Entfernen der Azure Sentinel-App aus Ihrem GitHub-Repository

Wenn Sie die Azure Sentinel-App aus einem GitHub-Repository löschen möchten, sollten Sie *zuerst* alle zugeordneten Verbindungen von der Seite Azure Sentinel-Seite **Repositorys** entfernen.

Jede Installation der Azure Sentinel-App verfügt über eine eindeutige ID, die beim Hinzufügen und Entfernen der Verbindung verwendet wird. Wenn die ID fehlt oder geändert wurde, müssen Sie sowohl die Verbindung von der Azure Sentinel-Seite **Repositorys** entfernen als auch den Workflow manuell aus Ihrem GitHub-Repository entfernen, um zukünftige Inhaltsbereitstellungen zu verhindern.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie Ihre benutzerdefinierten Inhalte in Azure Sentinel auf die gleiche Weise wie vorgefertigte Inhalte.

Weitere Informationen finden Sie unter:

- [Entdecken und Bereitstellen von Azure Sentinel-Lösungen (Public Preview)](sentinel-solutions-deploy.md)
- [Datenconnectors für Azure Sentinel](connect-data-sources.md)
- [Azure Sentinel Informationsmodell (ASIM) Parsers (Öffentliche Vorschau)](normalization-about-parsers.md)
- [Visualisieren gesammelter Daten](get-visibility.md)
- [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md)
- [Suchen nach Bedrohungen mit Azure Sentinel](hunting.md)
- [Verwenden von Azure Sentinel-Watchlists](watchlists.md)
- [Automatisieren der Bedrohungsabwehr mit Playbooks in Azure Sentinel](automate-responses-with-playbooks.md)
