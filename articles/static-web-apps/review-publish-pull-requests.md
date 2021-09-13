---
title: Überprüfen von Pull Requests in Präproduktionsumgebungen in Azure Static Web Apps
description: Es wird beschrieben, wie Sie Präproduktionsumgebungen verwenden, um Änderungen von Pull Requests in Azure Static Web Apps zu überprüfen.
services: static-web-apps
author: sinedied
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: yolasors
ms.openlocfilehash: 3869b18f580d0a12b2581554acf63258cb08f64a
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/19/2021
ms.locfileid: "112380678"
---
# <a name="review-pull-requests-in-pre-production-environments-in-azure-static-web-apps"></a>Überprüfen von Pull Requests in Präproduktionsumgebungen in Azure Static Web Apps

In diesem Artikel wird veranschaulicht, wie Sie Präproduktionsumgebungen zum Überprüfen der Änderungen von Anwendungen verwenden, die mit [Azure Static Web Apps](overview.md) bereitgestellt werden.

Eine Präproduktionsumgebung (Stagingumgebung) ist eine voll funktionsfähige bereitgestellte Version Ihrer Anwendung mit Änderungen, die in der Produktion nicht verfügbar sind.

Mit Azure Static Web Apps wird im Repository ein GitHub Actions-Workflow generiert. Wenn ein Pull Request für einen Branch erstellt wird, der vom Workflow überwacht wird, wird die Präproduktionsumgebung erstellt. In der Präproduktionsumgebung wird die App bereitgestellt, und Sie können vor dem Pushvorgang in die Produktion Überprüfungen durchführen.

Bei Verwendung von Azure Static Web Apps können mehrere Präproduktionsumgebungen nebeneinander vorhanden sein. Bei jeder Erstellung eines Pull Requests für den überwachten Branch wird eine bereitgestellte Version mit Ihren Änderungen in einer eigenen Präproduktionsumgebung bereitgestellt.

Die Verwendung von Präproduktionsumgebungen hat viele Vorteile. Beispielsweise können Sie folgende Aktionen ausführen:

- Überprüfen von visuellen Änderungen zwischen Produktion und Staging. Beispielsweise das Anzeigen von Aktualisierungen für Inhalt und Layout.
- Demonstrieren der Änderungen für Ihr Team
- Vergleichen unterschiedlicher Versionen Ihrer Anwendung
- Überprüfen von Änderungen mit Akzeptanztests
- Durchführen von Integritätsprüfungen vor der Bereitstellung in der Produktion

> [!NOTE]
> Pull Requests und Präproduktionsumgebungen werden derzeit nur in GitHub Actions-Bereitstellungen unterstützt.

## <a name="prerequisites"></a>Voraussetzungen

- Ein vorhandenes GitHub-Repository, das mit Azure Static Web Apps konfiguriert wurde. Falls Sie nicht über ein Repository dieser Art verfügen, helfen Ihnen die Informationen unter [Erstellen Ihrer ersten statischen App](getting-started.md) weiter.

## <a name="make-a-change"></a>Vornehmen einer Änderung

Nehmen Sie zunächst eine Änderung in Ihrem Repository vor. Sie können dies direkt auf GitHub durchführen (wie in den folgenden Schritten gezeigt).

1. Navigieren Sie zum Repository Ihres Projekts auf GitHub, und klicken Sie dann auf die Schaltfläche **Branch**, um einen neuen Branch zu erstellen.

    :::image type="content" source="./media/review-publish-pull-requests/create-branch.png" alt-text="Erstellen eines neuen Branchs über die GitHub-Benutzeroberfläche":::]

    Geben Sie einen Branchnamen ein, und klicken Sie auf **Branch erstellen**.

1. Wechseln Sie zum Ordner _app_, und ändern Sie den Textinhalt. Sie können beispielsweise einen Titel oder Absatz ändern. Nachdem Sie die Datei gefunden haben, die Sie bearbeiten möchten, klicken Sie auf **Bearbeiten**, um die Änderung vorzunehmen.

    :::image type="content" source="./media/review-publish-pull-requests/edit-file.png" alt-text="Schaltfläche „Datei bearbeiten“ auf der GitHub-Benutzeroberfläche":::

1. Klicken Sie nach dem Vornehmen der Änderungen auf **Commit für Änderungen ausführen**, um Ihre Änderungen für den Branch zu committen.

    :::image type="content" source="./media/review-publish-pull-requests/commit-changes.png" alt-text="Schaltfläche „Commit für Änderungen ausführen“ auf der GitHub-Benutzeroberfläche":::

## <a name="create-a-pull-request"></a>Erstellen eines Pull Requests

Erstellen Sie als Nächstes einen Pull Request für diese Änderung.

1. Öffnen Sie die Registerkarte **Pull Request** Ihres Projekts auf GitHub:

    :::image type="content" source="./media/review-publish-pull-requests/tab.png" alt-text="Registerkarte „Pull Request“ in einem GitHub-Repository":::

1. Klicken Sie auf die Schaltfläche **Compare & Pull Request** (Vergleich und Pull Request) Ihres Branchs.

1. Sie können optional einige Details zu Ihren Änderungen einfügen und dann auf **Pull Request erstellen** klicken.

    :::image type="content" source="./media/review-publish-pull-requests/open.png" alt-text="Pull Request-Erstellung auf GitHub":::

Sie können Prüfer zuweisen und Ihre Änderungen bei Bedarf mit Kommentaren versehen.

> [!NOTE]
> Sie können mehrere Änderungen vornehmen, indem Sie neue Commits in Ihren Branch pushen. Der Pull Request wird dann automatisch aktualisiert, um alle Änderungen widerzuspiegeln.

## <a name="review-changes"></a>Änderungen überprüfen

Nach der Erstellung des Pull Requests wird der Workflow für die [GitHub Actions](https://github.com/features/actions)-Bereitstellung ausgeführt, und Ihre Änderungen werden in einer Präproduktionsumgebung bereitgestellt.

Nachdem der Workflow das Erstellen und Bereitstellen Ihrer App abgeschlossen hat, wird Ihrem Pull Request vom GitHub-Bot ein Kommentar hinzugefügt, der die URL der Präproduktionsumgebung enthält. Sie können auf diesen Link klicken, um Ihre bereitgestellten Änderungen anzuzeigen.

:::image type="content" source="./media/review-publish-pull-requests/bot-comment.png" alt-text="Pull Request-Kommentar mit der Präproduktions-URL":::

Klicken Sie auf die generierte URL, um die Änderungen anzuzeigen.

Wenn Sie sich die URL genauer ansehen, erkennen Sie, dass sie sich wie folgt zusammensetzt: `https://<SUBDOMAIN-PULL_REQUEST_ID>.<AZURE_REGION>.azurestaticapps.net`.

Bei einem Pull Request bleibt die URL auch dann gleich, wenn Sie neue Updates pushen. Es bleibt nicht nur die URL gleich, sondern für die gesamte Lebensdauer des Pull Requests wird auch immer wieder dieselbe Präproduktionsumgebung verwendet.

## <a name="publish-changes"></a>Veröffentlichen von Änderungen

Nach der Genehmigung der Änderungen können Sie diese in der Produktion veröffentlichen, indem Sie den Pull Request zusammenführen.

Klicken Sie auf **Merge pull request** (Pull Request zusammenführen):

:::image type="content" source="./media/review-publish-pull-requests/merge.png" alt-text="Schaltfläche „Merge pull request“ (Pull Request zusammenführen) auf der GitHub-Benutzeroberfläche":::

Beim Zusammenführen werden Ihre Änderungen in den nachverfolgten Branch („Produktionsbranch“) kopiert. Anschließend beginnt der Bereitstellungsworkflow auf dem nachverfolgten Branch, und die Änderungen befinden sich nach dem erneuten Buildvorgang für die Anwendung im Livezustand.

Öffnen Sie zum Überprüfen der Änderungen in der Produktion Ihre Produktions-URL, um die Liveversion der Website zu laden.

## <a name="limitations"></a>Einschränkungen

- Auf bereitgestellte Versionen Ihrer Anwendung besteht derzeit auch dann öffentlicher Zugriff über die entsprechende URL, wenn Ihr GitHub-Repository ein privates Repository ist.

    > [!WARNING]
    > Gehen Sie beim Veröffentlichen von vertraulichen Inhalten auf bereitgestellten Versionen mit Bedacht vor, da der Zugriff auf Präproduktionsumgebungen nicht eingeschränkt ist.

- Die Anzahl von Präproduktionsumgebungen, die für mit Static Web Apps bereitgestellte Apps verfügbar sind, richtet sich nach dem von Ihnen gewählten [Hostingplan](plans.md). Beim Free-Tarif können Sie beispielsweise zusätzlich zur Produktionsumgebung drei Präproduktionsumgebungen nutzen.

- Präproduktionsumgebungen sind nicht geografisch verteilt.

- Derzeit werden Präproduktionsumgebungen nur von GitHub Actions-Bereitstellungen unterstützt.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Einrichten einer benutzerdefinierten Domäne](custom-domain.md)
