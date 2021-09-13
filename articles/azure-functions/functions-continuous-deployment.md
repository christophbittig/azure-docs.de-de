---
title: Continuous Deployment für Azure Functions
description: Hier erfahren Sie, wie Sie Ihre Funktionen mithilfe der Continuous Deployment-Features von Azure App Service verwenden veröffentlichen.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 0018f1c74708035206a8a2bb9a540f00f98f43a4
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321022"
---
# <a name="continuous-deployment-for-azure-functions"></a>Continuous Deployment für Azure Functions

Mit Azure Functions können Sie Ihren Code mithilfe der [Integration der Quellcodeverwaltung](functions-deployment-technologies.md#source-control) kontinuierlich bereitstellen. Die Integration der Quellcodeverwaltung ermöglicht einen Workflow, bei dem eine Codeaktualisierung eine Bereitstellung in Azure auslöst. Sollten Sie noch nicht mit Azure Functions vertraut sein, sehen Sie sich zunächst die [Einführung in Azure Functions](functions-overview.md) an.

Continuous Deployment eignet sich hervorragend für Projekte mit häufiger Integration zahlreicher Beiträge. Bei Verwendung von Continuous Deployment wird für Ihren Code eine einzelne alleingültige Quelle (Single Source Of Truth, SSOT) gepflegt, wodurch Teams problemlos zusammenarbeiten können. Sie können Continuous Deployment in Azure Functions von folgenden Speicherorten für Quellcode konfigurieren:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Die Bereitstellungseinheit für Funktionen in Azure ist die Funktions-App. Alle Funktionen in einer Funktions-App werden gleichzeitig bereitgestellt. Nach Aktivierung von Continuous Deployment wird der Zugriff auf Funktionscode im Azure-Portal als *schreibgeschützt* konfiguriert, da die Source of Truth auf einen anderen Ort festgelegt ist.

## <a name="requirements-for-continuous-deployment"></a>Anforderungen für Continuous Deployment

Damit Continuous Deployment erfolgreich ausgeführt werden kann, muss Ihre Verzeichnisstruktur mit der grundlegenden Ordnerstruktur kompatibel sein, die von Azure Functions erwartet wird.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> Continuous Deployment wird derzeit nicht für Linux-Apps unterstützt, die mit einem Verbrauchstarif ausgeführt werden. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Einrichten von Continuous Deployment

Gehen Sie wie folgt vor, um Continuous Deployment für eine vorhandene Funktions-App zu konfigurieren. Die Schritte zeigen die Integration eines GitHub-Repositorys. Die Vorgehensweise für Azure Repos und andere Quellcoderepositorys ist jedoch ähnlich.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) in ihrer Funktions-App **Deployment Center**, **GitHub** und dann **Autorisieren** aus. Falls Sie GitHub bereits autorisiert haben, wählen Sie **Weiter** aus und überspringen den nächsten Schritt. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Azure App Service Deployment Center":::

3. Wählen Sie in GitHub **AzureAppService autorisieren** aus.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Autorisieren von Azure App Service":::

    Geben Sie Ihr GitHub-Kennwort ein, und wählen Sie dann **Weiter** aus.

4. Wählen Sie einen der folgenden Buildanbieter aus:

    * **App Service-Builddienst**: Empfiehlt sich, wenn Sie keinen Build oder einen generischen Build benötigen.
    * **Azure-Pipelines (Vorschau)** : Empfiehlt sich, wenn Sie mehr Kontrolle über den Build benötigen. Dieser Anbieter befindet sich derzeit in der Vorschauphase.

    Wählen Sie **Weiter**.

5. Konfigurieren Sie Informationen, die für die von Ihnen angegebene Option der Quellcodeverwaltung spezifisch sind. Für GitHub müssen Werte für **Organisation**, **Repository** und **Branch** eingegeben bzw. ausgewählt werden. Die Werte basieren auf dem Ort, an dem sich Ihr Code befindet. Wählen Sie anschließend **Weiter** aus.

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="Konfigurieren von GitHub":::

6. Überprüfen Sie alle Angaben, und wählen Sie anschließend **Fertig stellen** aus, um die Bereitstellungskonfiguration abzuschließen.

Nach Abschluss des Prozesses wird der gesamte Code aus der angegebenen Quelle für Ihre App bereitgestellt. An diesem Punkt lösen Änderungen in der Bereitstellungsquelle eine Bereitstellung dieser Änderungen in Ihrer Funktions-App in Azure aus.

> [!NOTE]
> Nach dem Konfigurieren von Continuous Integration können Sie Ihre Quelldateien im Functions-Portal nicht mehr bearbeiten. Wenn Sie Ihren Code ursprünglich über Ihren lokalen Computer veröffentlicht haben, müssen Sie möglicherweise die `WEBSITE_RUN_FROM_PACKAGE`-Einstellung in Ihrer Funktions-App in `0` ändern. 

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewährte Methoden für Azure Functions](functions-best-practices.md)
