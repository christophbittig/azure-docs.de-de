---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/02/2019
ms.author: danlep
ms.openlocfilehash: 929bcb52f724162454fc7c65939a4cec2ffd7ca6
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112254998"
---
## <a name="prerequisites"></a>Voraussetzungen

### <a name="get-sample-code"></a>Abrufen des Beispielcodes

In diesem Tutorial wird davon ausgegangen, dass Sie bereits die Schritte des [vorherigen Tutorials](../articles/container-registry/container-registry-tutorial-quick-task.md) ausgeführt und das Beispielrepository geforkt und geklont haben. Führen Sie bei Bedarf die Schritte im Abschnitt [Voraussetzungen](../articles/container-registry/container-registry-tutorial-quick-task.md#prerequisites) des vorherigen Tutorials aus, bevor Sie mit diesem Tutorial fortfahren.

### <a name="container-registry"></a>Containerregistrierung

Ihr Abonnement muss eine Azure-Containerregistrierung enthalten, um dieses Tutorial bearbeiten zu können. Informationen zum Erstellen einer Registrierung finden Sie bei Bedarf im [vorherigen Tutorial](../articles/container-registry/container-registry-tutorial-quick-task.md) und in der Schnellstartanleitung [Erstellen einer Containerregistrierung mit der Azure-Befehlszeilenschnittstelle](../articles/container-registry/container-registry-get-started-azure-cli.md).

### <a name="create-a-github-personal-access-token"></a>Erstellen eines persönlichen GitHub-Zugriffstokens

Wenn Sie nach einem Commit in einem Git-Repository einen Task auslösen möchten, benötigt ACR Tasks ein [persönliches Zugriffstoken](../articles/container-registry/container-registry-tasks-overview.md#personal-access-token) (Personal Access Token, PAT) für den Zugriff auf das Repository. Besitzen Sie noch kein PAT, führen Sie die folgenden Schritte aus, um eins in GitHub zu erstellen:

1. Navigieren Sie zur PAT-Erstellungsseite von GitHub (https://github.com/settings/tokens/new).
1. Geben Sie eine kurze **Beschreibung** für das Token ein (beispielsweise „ACR Tasks-Demo“).
1. Wählen Sie Bereiche für ACR, um auf das Repository zuzugreifen. Um wie in diesem Tutorial auf ein öffentliches Repository zuzugreifen, aktivieren Sie unter **repo** erst **repo:status** und dann **public_repo**.

   ![Screenshot der Seite zum Generieren eines persönlichen Zugriffstokens in GitHub][build-task-01-new-token]

   > [!NOTE]
   > Um ein persönliches Zugriffstoken für den Zugriff auf ein *privates* Repository zu generieren, wählen Sie den Bereich für die vollständige **Repository** steuerung.

1. Klicken Sie auf die Schaltfläche **Generate token** (Token generieren). Unter Umständen werden Sie zur Bestätigung Ihres Kennworts aufgefordert.
1. Kopieren und speichern Sie das generierte Token an einem **sicheren Ort**. Das Token wird im nächsten Abschnitt beim Definieren einer Aufgabe benötigt.

   ![Screenshot des generierten persönlichen Zugriffstokens in GitHub][build-task-02-generated-token]

<!-- Images -->
[build-task-01-new-token]: ./media/container-registry-task-tutorial-prereq/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-task-tutorial-prereq/build-task-02-generated-token.png
