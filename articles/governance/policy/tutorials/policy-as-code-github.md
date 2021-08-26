---
title: 'Tutorial: Implementieren von Azure Policy-as-Code mit GitHub'
description: In diesem Tutorial implementieren Sie einen Azure Policy-as-Code-Workflow mit Export, GitHub Actions und GitHub-Workflows.
ms.date: 08/17/2021
ms.topic: tutorial
ms.openlocfilehash: 4bfbdf20fb0189f36832b40b1a924c5fbdc4fed9
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324701"
---
# <a name="tutorial-implement-azure-policy-as-code-with-github"></a>Tutorial: Implementieren von Azure Policy-as-Code mit GitHub

Mit einem Azure Policy-as-Code-Workflow können Sie Ihre Richtliniendefinitionen und -zuweisungen als Code verwalten, den Lebenszyklus der Aktualisierung dieser Definitionen steuern und die Überprüfung von Complianceergebnissen automatisieren. In diesem Tutorial erfahren Sie, wie Sie Azure Policy-Funktionen mit GitHub verwenden, um einen Lebenszyklusprozess zu erstellen. Zu diesen Aufgaben zählt Folgendes:

> [!div class="checklist"]
> - Exportieren von Richtliniendefinitionen und -zuweisungen nach GitHub
> - Pushen von in GitHub aktualisierten Richtlinienobjekten zu Azure
> - Auslösen einer Complianceüberprüfung aus der GitHub-Aktion

Lesen Sie die Schnellstartartikel, wenn Sie eine Richtlinie zum Ermitteln des aktuellen Konformitätszustands Ihrer vorhandenen Ressourcen zuweisen möchten.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
- Lesen Sie [Entwerfen eines Azure Policy-as-Code-Workflows](../concepts/policy-as-code.md), um ein Verständnis für die in diesem Tutorial verwendeten Entwurfsmuster zu bekommen.

### <a name="export-azure-policy-objects-from-the-azure-portal"></a>Exportieren von Azure Policy-Objekten aus dem Azure-Portal

Führen Sie die folgenden Schritte aus, um eine Richtliniendefinition aus dem Azure-Portal zu exportieren:

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Richtlinie** suchen und die entsprechende Option auswählen.

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Definitionen** aus.

1. Verwenden Sie die Schaltfläche **Definitionen exportieren**, oder wählen Sie die Auslassungspunkte in der Zeile einer Richtliniendefinition und dann **Definition exportieren** aus.

1. Wählen Sie die Schaltfläche **Mit GitHub anmelden** aus. Wenn Sie sich noch nicht bei GitHub authentifiziert haben, um Azure Policy zum Exportieren der Ressource zu autorisieren, überprüfen Sie in dem neuen Fenster, das geöffnet wird, welchen Zugriff [GitHub Actions](https://github.com/features/actions) benötigt, und wählen Sie **Authorize AzureGitHubActions** (AzureGitHubActions autorisieren), um mit dem Exportvorgang fortzufahren. Sobald der Vorgang abgeschlossen ist, wird das neue Fenster automatisch geschlossen.

1. Legen Sie auf der Registerkarte **Grundlagen** die folgenden Optionen fest, und wählen Sie dann die Registerkarte **Richtlinien** oder die Schaltfläche **Weiter: Richtlinien** unten auf der Seite aus.

   - **Repositoryfilter**: Legen Sie hierfür _Meine Repositorys_ fest, sodass nur die Repositorys in Ihrem Besitz angezeigt werden, oder _Alle Repositorys_, um alle anzuzeigen, denen Sie den Zugriff auf GitHub Actions gewährt haben.
   - **Repository**: Legen Sie hierfür das Repository fest, in das Sie die Azure Policy-Ressourcen exportieren möchten.
   - **Branch**: Legen Sie den Branch im Repository fest. Die Verwendung eines anderen Branches als des standardmäßigen ist eine gute Möglichkeit, Ihre Updates zu validieren, bevor Sie sie in den Quellcode einbringen.
   - **Verzeichnis:** Der _Ordner auf Stammebene_, in den die Azure Policy-Ressourcen exportiert werden sollen. Die Unterordner in diesem Verzeichnis werden auf Basis der exportierten Ressourcen erstellt.

1. Legen Sie auf der Registerkarte **Richtlinien** den Suchbereich fest, indem Sie die Auslassungspunkte und dann eine Kombination von Verwaltungsgruppen, Abonnements oder Ressourcengruppen auswählen.

1. Suchen Sie mit der Schaltfläche **Richtliniendefinition(en) hinzufügen** den Bereich, für den Objekte exportiert werden sollen. Wählen Sie im daraufhin geöffneten seitlichen Fenster die einzelnen zu exportierenden Objekte aus. Filtern Sie die Auswahl nach Suchfeld oder Typ. Nachdem Sie alle zu exportierenden Objekte ausgewählt haben, verwenden Sie die Schaltfläche **Hinzufügen** am unteren Rand der Seite.

1. Wählen Sie für jedes ausgewählte Objekt die gewünschten Exportoptionen für eine Richtliniendefinition aus, z. B. _Nur Definition_ oder _Definition und Zuweisung(en)_ . Wählen Sie dann die Registerkarte **Überprüfen + exportieren** oder die Schaltfläche **Weiter: Überprüfen + exportieren** am unteren Rand der Seite aus.

   > [!NOTE]
   > Wenn die Option _Definition und Zuweisung(en)_ ausgewählt wird, werden nur die Richtlinienzuweisungen exportiert, die innerhalb des Bereichs liegen, der beim Hinzufügen der Richtliniendefinition vom Filter festgelegt wird.

1. Überprüfen Sie auf der Registerkarte **Überprüfen + exportieren** die Übereinstimmung im Detail, und verwenden Sie dann die Schaltfläche **Exportieren** unten auf der Seite.

1. Überprüfen Sie das GitHub-Repository, den Branch und den _Ordner auf Stammebene_, um zu sehen, dass die ausgewählten Ressourcen nun in die Quellcodeverwaltung exportiert werden.

Die Azure Policy-Ressourcen werden in die folgende Struktur innerhalb des ausgewählten GitHub-Repositorys und des _Ordners auf Stammebene_ exportiert:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

### <a name="push-policy-objects-updated-in-github-to-azure"></a>Pushen von in GitHub aktualisierten Richtlinienobjekten zu Azure

1. Wenn Richtlinienobjekte exportiert werden, wird ebenfalls eine [GitHub-Workflow](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows)datei namens `.github/workflows/manage-azure-policy-<randomLetters>.yml` erstellt, um Ihnen den Einstieg zu erleichtern.

   > [!NOTE]
   > Die GitHub-Workflowdatei wird jedes Mal erstellt, wenn der Export verwendet wird. Jede Instanz der Datei ist für die während dieser Exportaktion verwendeten Optionen spezifisch.

1. Diese Workflowdatei verwendet die [Azure Policy verwalten](https://github.com/marketplace/actions/manage-azure-policy)-Aktion, um Änderungen, die an den exportierten Richtlinienobjekten im GitHub-Repository vorgenommen wurden, an Azure Policy zurückzuleiten. Standardmäßig berücksichtigt und synchronisiert die Aktion nur die Dateien, die sich von den in Azure vorhandenen Dateien unterscheiden. Sie können auch den Parameter `assignments` in der Aktion verwenden, um nur Änderungen zu synchronisieren, die an bestimmten Zuweisungsdateien vorgenommen wurden. Dieser Parameter kann verwendet werden, um Richtlinienzuweisungen nur für eine bestimmte Umgebung anzuwenden. Weitere Informationen finden Sie in der [Repository-Infodatei zu „Azure Policy verwalten“](https://github.com/Azure/manage-azure-policy).

1. Standardmäßig muss der Workflow manuell ausgelöst werden. Verwenden Sie hierzu die **Aktionen** in GitHub, wählen Sie den Workflow `manage-azure-policy-<randomLetters>` aus, wählen Sie **Workflow ausführen** aus und dann noch mal **Workflow ausführen**.

   :::image type="content" source="../media/policy-as-code-github/manually-trigger-workflow.png" alt-text="Screenshot der Registerkarte „Aktion“ sowie der Schaltflächen „Workflow“ und „Workflow ausführen“ in der GitHub-Webschnittstelle.":::

   > [!NOTE]
   > Die Workflowdatei muss sich im Standardbranch befinden, damit sie erkannt und manuell ausgeführt werden muss.

1. Der Workflow synchronisiert die an Richtlinienobjekten vorgenommenen Änderungen mit Azure und gibt den Status in die Protokolle aus.

   :::image type="content" source="../media/policy-as-code-github/workflow-logging.png" alt-text="Screenshot des Workflows in Aktion sowie protokollierte Details in den Protokollen.":::

1. Der Workflow fügt außerdem Details zu `properties.metadata` von Azure Policy-Objekten hinzu, die Sie nachverfolgen können.

   :::image type="content" source="../media/policy-as-code-github/updated-definition-metadata.png" alt-text="Screenshot der Azure Policy-Definition im Azure-Portal, aktualisiert mit für die GitHub-Aktion spezifischen Metadaten.":::

### <a name="trigger-compliance-scans-using-github-action"></a>Auslösen einer Complianceüberprüfung mithilfe einer GitHub-Aktion

Mithilfe der [Aktion „Azure Policy-Complianceüberprüfung“](https://github.com/marketplace/actions/azure-policy-compliance-scan) können Sie von Ihrem [GitHub-Workflow](https://docs.github.com/en/actions/configuring-and-managing-workflows/configuring-a-workflow#about-workflows) aus für eine oder mehrere Ressourcen, Ressourcengruppen oder Abonnements bei Bedarf eine Compliancebewertungsprüfung auslösen und den Workflowpfad basierend auf dem Compliancestatus dieser Ressourcen ändern. Sie können den Workflow auch so konfigurieren, dass er zu einer geplanten Zeit abläuft, um zu einem geeigneten Zeitpunkt den neuesten Konformitätsstatus zu erhalten. Optional kann mit dieser GitHub-Aktion auch ein Bericht über den Konformitätsstatus der überprüften Ressourcen zur weiteren Analyse oder zur Archivierung erstellt werden.

Im folgenden Beispiel wird eine Konformitätsprüfung für ein Abonnement ausgeführt.

```yaml

on:
  schedule:
    - cron:  '0 8 * * *'  # runs every morning 8am
jobs:
  assess-policy-compliance:
    runs-on: ubuntu-latest
    steps:
    - name: Login to Azure
      uses: azure/login@v1
      with:
        creds: ${{secrets.AZURE_CREDENTIALS}}

    - name: Check for resource compliance
      uses: azure/policy-compliance-scan@v0
      with:
        scopes: |
          /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

## <a name="review"></a>Überprüfung

In diesem Tutorial haben Sie folgende Aufgaben durchgeführt:

> [!div class="checklist"]
> - Exportieren von Richtliniendefinitionen und -zuweisungen nach GitHub
> - Pushen von in GitHub aktualisierten Richtlinienobjekten zu Azure
> - Auslösen einer Complianceüberprüfung aus der GitHub-Aktion

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Strukturen von Richtliniendefinitionen finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md)
