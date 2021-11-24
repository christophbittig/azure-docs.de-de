---
title: Erstellen einer anwendungsorientierten Umgebung mit Colony
description: In diesem Artikel erfahren Sie, wie Sie eine anwendungsorientierte Umgebung mit Colony und Azure erstellen.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: 9ce80a7467ac94a69227cc9616c72c622a6f575f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347241"
---
# <a name="create-an-application-centric-environment-with-colony"></a>Erstellen einer anwendungsorientierten Umgebung mit Colony

[Quali CloudShell Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview) ist eine SaaS-Plattform (Software-as-a-Service), mit der Sie Infrastrukturautomatisierung im großen Stil bereitstellen können. Colony unterstützt Entwickler bei der Bereitstellung von Anwendungen in komplexen Cloudumgebungen wie Azure und Kubernetes. Dies ergänzt Azure DevTest Labs während des gesamten Anwendungsbereitstellungsprozesses bis hin zur Produktion. In diesem Artikel erfahren Sie, wie Sie eine anwendungsorientierte Umgebung mit Colony und Azure erstellen.

## <a name="set-up-the-environment-with-colony-and-microsoft-azure"></a>Einrichten der Umgebung mit CloudShell Colony und Microsoft Azure

1. Registrieren Sie sich für Ihre kostenlose Testversion von [Colony](https://azuremarketplace.microsoft.com/marketplace/apps/quali_systems.cloudshell_colony?tab=Overview).

    :::image type="content" source="./media/create-application-centric-environment/free-trial.png" alt-text="Screenshot: Registrierung für eine kostenlose Colony-Testversion.":::
1. [Verknüpfen Sie Ihr Azure-Konto](https://colonysupport.quali.com/hc/articles/360008222234).

    :::image type="content" source="./media/create-application-centric-environment/welcome.png" alt-text="Screenshot des Bildschirms &quot;Willkommen bei Colony“.":::
1. Laden Sie Benutzer in Ihren Bereich ein.
1. [Erstellen Sie Ihre erste Blaupause mithilfe einer YAML-Datei](https://colonysupport.quali.com/hc/articles/360001680807-Steps-to-Developing-a-Blueprint).
    1. Verknüpfen Sie Ihr Blaupausenrepository in GitHub oder BitBucket mit Colony.
    1. Verwenden Sie eine Colony-Beispielblaupause als Grundlage, und ändern sie nach Bedarf.

        :::image type="content" source="./media/create-application-centric-environment/performance-stress-tests.png" alt-text="Screenshot: Belastungstests.":::
    1. Veröffentlichen Sie Ihre Blaupause, damit sie von anderen verwendet werden kann.
1. Starten Ihrer Anwendungsumgebung in einer Sandbox unter Verwendung von Colony.

    :::image type="content" source="./media/create-application-centric-environment/blueprints.png" alt-text="Screenshot vom Starten Ihrer Anwendungsumgebung in einer Sandbox unter Verwendung von Colony.":::

Sie können Ihre Blaupause auch als Teil eines CI/CD-Workflows (Continuous Integration and Continuous Delivery) in Azure Pipelines integrieren. Die Schritte hierzu finden Sie unter [Starten einer Sandbox aus Azure DevOps (VSTS)](https://colonysupport.quali.com/hc/articles/360008464234).

:::image type="content" source="./media/create-application-centric-environment/devops-pipeline.png" alt-text="Screenshot: Herstellen einer Verbindung mit einer Azure Pipelines Pipeline.":::

## <a name="next-steps"></a>Nächste Schritte

[Anfordern einer Colony-Demo](https://info.quali.com/cloudshell-colony-demo-request)
