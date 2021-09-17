---
title: 'Tutorial: Veröffentlichen von Azure Static Web Apps mit Azure DevOps'
description: Hier erfahren Sie, wie Sie Azure Static Web Apps mithilfe von Azure DevOps veröffentlichen.
services: static-web-apps
author: scubaninja
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: apedward
ms.openlocfilehash: 9df037177aac3dd909795f18c6e903eedd1c98a6
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608870"
---
# <a name="tutorial-publish-azure-static-web-apps-with-azure-devops"></a>Tutorial: Veröffentlichen von Azure Static Web Apps mit Azure DevOps

In diesem Artikel erfahren Sie, wie Sie [Azure DevOps](https://dev.azure.com/) für Bereitstellungen in [Azure Static Web Apps](./overview.md) verwenden.

In diesem Tutorial lernen Sie Folgendes:

- Einrichten einer Azure Static Web Apps-Website
- Erstellen einer Azure-Pipeline zum Erstellen und Veröffentlichen einer statischen Web-App

## <a name="prerequisites"></a>Voraussetzungen

- **Aktives Azure-Konto:** Sollten Sie über kein Konto verfügen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/).
- **Azure DevOps-Projekt:** Sollten Sie über kein Projekt verfügen, können Sie ein [kostenloses Projekt erstellen](https://azure.microsoft.com/pricing/details/devops/azure-devops-services/).
  - Azure DevOps beinhaltet **Azure-Pipelines**. Informationen zu den ersten Schritten mit Azure-Pipelines, finden Sie bei Bedarf unter [Erstellen Ihrer ersten Pipeline](/azure/devops/pipelines/create-first-pipeline?preserve-view=true&view=azure-devops).
  - Die Static Web-App Pipeline-Task funktioniert derzeit nur auf **Linux**-Computern. Stellen Sie beim Ausführen der unten genannten Pipeline sicher, dass sie auf einem virtuellen Linux-Computer ausgeführt wird.

## <a name="create-a-static-web-app-in-an-azure-devops"></a>Erstellen einer statischen Web-App in Azure DevOps

  > [!NOTE]
  > Wenn Sie bereits über eine App in Ihrem Repository verfügen, können Sie direkt mit dem nächsten Abschnitt fortfahren.
  
  > [!NOTE]
  > Die Anwendung muss .NET Core 3.1 als Ziel haben, damit die Pipeline erfolgreich ausgeführt werden kann.

1. Navigieren Sie zu Ihrem Repository in Azure Repos.

1. Wählen Sie **Importieren** aus, um mit dem Importieren einer Beispielanwendung zu beginnen.
  
    :::image type="content" source="media/publish-devops/devops-repo.png" alt-text="DevOps-Repository":::

1. Geben Sie unter **URL klonen** Folgendes ein: `https://github.com/staticwebdev/vanilla-api.git`.

1. Klicken Sie auf **Import** (Importieren).

## <a name="create-a-static-web-app"></a>Erstellen einer statischen Web-App

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).

1. Klicken Sie auf **Ressource erstellen**.

1. Suchen Sie nach **Static Web Apps**.

1. Klicken Sie auf **statische Web-Apps**.

1. Klicken Sie auf **Erstellen**.

1. Erstellen Sie eine neue statische Web-App mit den folgenden Werten.

    :::image type="content" source="media/publish-devops/azure-portal-static-web-apps-devops.png" alt-text="Bereitstellungsdetails: Option „Andere“":::

    | Einstellung | Wert |
    |---|---|
    | Subscription | Den Namen Ihres Azure-Abonnements |
    | Ressourcengruppe | Wählen Sie einen vorhandenen Gruppennamen aus, oder erstellen Sie eine neue. |
    | Name | Geben Sie **myDevOpsApp** ein. |
    | Typ des Hostingplans | Wählen Sie **Free** aus. |
    | Region | Wählen Sie die Region aus, die Ihnen am nächsten ist. |
    | `Source` | Wählen Sie **Andere** (Other) aus. |

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie nach erfolgreicher Bereitstellung **Zu Ressource wechseln** aus.

1. Wählen Sie **Bereitstellungstoken verwalten** aus.

1. Kopieren Sie das **Bereitstellungstoken**, und fügen Sie seinen Wert in einen Text-Editor ein. Es wird später auf einem anderen Bildschirm benötigt.

    > [!NOTE]
    > Dieser Wert wird auf diese Weise zwischengespeichert, da in den nächsten Schritten weitere Werte kopiert und eingefügt werden müssen.

    :::image type="content" source="media/publish-devops/deployment-token.png" alt-text="Bereitstellungstoken":::

## <a name="create-the-pipeline-task-in-azure-devops"></a>Erstellen der Pipelineaufgabe in Azure DevOps

1. Navigieren Sie zu dem zuvor erstellten Repository in Azure Repos.

2. Wählen Sie **Build einrichten** aus.

    :::image type="content" source="media/publish-devops/azdo-build.png" alt-text="Buildpipeline":::

3. Wählen Sie auf dem Bildschirm *Pipeline konfigurieren* die Option **Starterpipeline** aus.

    :::image type="content" source="media/publish-devops/configure-pipeline.png" alt-text="Konfigurieren der Pipeline":::

4. Kopieren Sie den folgenden YAML-Code, und ersetzen Sie die generierte Konfiguration in Ihrer Pipeline durch diesen Code.

    ```yaml
    trigger:
      - main

    pool:
      vmImage: ubuntu-latest

    steps:
      - checkout: self
        submodules: true
      - task: AzureStaticWebApp@0
        inputs:
          app_location: '/src'
          api_location: 'api'
          output_location: '/src'
          azure_static_web_apps_api_token: $(deployment_token)
    ```

    > [!NOTE]
    > Falls Sie nicht die Beispiel-App verwenden, müssen die Werte für `app_location`, `api_location` und `output_location` an die Werte in Ihrer Anwendung angepasst werden.

    [!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

    Der Wert `azure_static_web_apps_api_token` wird selbst verwaltet und manuell konfiguriert.

5. Wählen Sie **Variablen** aus.

6. Wählen Sie **New variable** aus.

7. Nennen Sie die Variable **deployment_token** (gemäß dem Namen im Workflow).

8. Kopieren Sie das Bereitstellungstoken, das Sie zuvor in einen Text-Editor eingefügt haben.

9. Fügen Sie das Bereitstellungstoken in das Feld _Wert_ ein.

    :::image type="content" source="media/publish-devops/variable-token.png" alt-text="Variablentoken":::

10. Aktivieren Sie das Kontrollkästchen **Diesen Geheimniswert beibehalten**.

11. Wählen Sie **OK** aus.

12. Wählen Sie **Speichern** aus, um zu Ihrem Pipeline-YAML-Code zurückzukehren.

13. Wählen Sie **Speichern und ausführen** aus, um das Dialogfeld _Speichern und ausführen_ zu öffnen.

    :::image type="content" source="media/publish-devops/save-and-run.png" alt-text="Pipeline":::

14. Wählen Sie **Speichern und ausführen** aus, um die Pipeline auszuführen.

15. Navigieren Sie nach erfolgreicher Bereitstellung zur **Übersicht** für Azure Static Web Apps. Dort finden Sie Links zur Bereitstellungskonfiguration. Wie Sie sehen, verweist der Link _Quelle_ jetzt auf den Branch und den Standort des Azure DevOps-Repositorys.

16. Wählen Sie die **URL** aus, um die neu bereitgestellte Website anzuzeigen.

    :::image type="content" source="media/publish-devops/deployment-location.png" alt-text="Bereitstellungsspeicherort":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um die bereitgestellten Ressourcen zu bereinigen.

1. Wählen Sie im Azure-Portal im linken Menü die Option **Ressourcengruppe** aus.
2. Geben Sie den Namen der Ressourcengruppe in das Feld **Nach Name filtern** ein.
3. Wählen Sie den Namen der Ressourcengruppe aus, die Sie in diesem Tutorial verwendet haben.
4. Wählen Sie **Ressourcengruppe löschen** aus dem Menü ganz oben aus.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren von Azure Static Web Apps](./configuration.md)
