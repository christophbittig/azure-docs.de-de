---
title: 'ML Studio (Classic): Bereitstellung und Nutzung (Azure)'
description: Mit dem Azure Machine Learning Studio (Classic) können Sie Machine Learning-Workflows und -Modelle als Webdienste bereitstellen. Diese Webdienste können dann verwendet werden, um die Machine Learning-Modelle in Anwendungen über das Internet aufzurufen und Vorhersagen im Echtzeit- oder Batchmodus zu nutzen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: c4dbf0811bdd6b28b5c64cc197e285e8fd134670
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112581608"
---
# <a name="machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Webdienste im Azure Machine Learning Studio (Classic): Bereitstellung und Nutzung

**GILT FÜR:**  ![Gilt für ](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (Classic) ![Gilt nicht für ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Mit dem Azure Machine Learning Studio (Classic) können Sie Machine Learning-Workflows und -Modelle als Webdienste bereitstellen. Diese Webdienste können dann verwendet werden, um die Machine Learning-Modelle in Anwendungen über das Internet aufzurufen und Vorhersagen im Echtzeit- oder Batchmodus zu nutzen. Da die Webdienste RESTful sind, können Sie sie über verschiedene Programmiersprachen und Plattformen wie etwa .NET und Java sowie über Anwendungen wie Excel aufrufen.

Die nächsten Abschnitte enthalten Links zu exemplarischen Vorgehensweisen, Code und Dokumentationen, die Ihnen beim Einstieg helfen.

## <a name="deploy-a-web-service"></a>Bereitstellen eines Webdiensts

### <a name="with-machine-learning-studio-classic"></a>Mit dem Azure Machine Learning Studio (Classic)

Im Studio-Portal (Classic) und Machine Learning-Webdienstportal können Sie einen Webdienst bereitstellen und verwalten, ohne Code schreiben zu müssen.

Unter den folgenden Links finden Sie allgemeine Informationen zur Bereitstellung eines neuen Webdiensts:

* Eine Übersicht über das Bereitstellen eines neuen Azure Resource Manager-basierten Webdiensts finden Sie unter [Bereitstellen eines neuen Webdiensts](deploy-a-machine-learning-web-service.md).
* Eine exemplarische Vorgehensweise zum Bereitstellen eines Webdiensts finden Sie unter [Bereitstellen eines (klassischen) Azure Machine Learning Studio-Webdiensts](deploy-a-machine-learning-web-service.md).
* Eine umfassende exemplarische Vorgehensweise zum Erstellen und Bereitstellen eines Webdiensts finden Sie unter [Tutorial 1: Vorhersagen des Kreditrisikos](tutorial-part1-credit-risk.md).
* Spezifische Beispiele für das Bereitstellen eines Webdiensts finden Sie hier:

  * [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md)
  * [Gewusst wie: Bereitstellen eines Webdiensts in mehreren Regionen](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Mit Webdienste-Ressourcenanbieter-APIs (Azure Resource Manager-APIs)

Der Ressourcenanbieter für Webdienste im Azure Machine Learning Studio (Classic) ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von REST-API-Aufrufen. Weitere Informationen finden Sie in der Referenz zu [Machine Learning Web Service (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>Mit PowerShell-Cmdlets

Der Ressourcenanbieter für Webdienste im Azure Machine Learning Studio (Classic) ermöglicht die Bereitstellung und Verwaltung von Webdiensten mithilfe von PowerShell-Cmdlets.

Um die Cmdlets verwenden zu können, müssen Sie sich innerhalb Ihrer PowerShell-Umgebung zunächst mithilfe des Cmdlets [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) bei Ihrem Azure-Konto anmelden. Wenn Sie mit dem Aufrufen Resource Manager-basierter PowerShell-Befehle nicht vertraut sind, nutzen Sie die Informationen unter [Verwenden von Azure PowerShell mit Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Verwenden Sie [diesen Beispielcode](https://github.com/ritwik20/AzureML-WebServices), um Ihr Vorhersageexperiment zu exportieren. Nachdem Sie die EXE-Datei auf der Grundlage des Codes erstellt haben, können Sie Folgendes eingeben:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Durch Ausführen der Anwendung wird eine JSON-Webdienstvorlage erstellt. Fügen Sie folgende Informationen hinzu, um mithilfe dieser Vorlage einen Webdienst bereitzustellen:

* Speicherkontoname und -schlüssel

    Den Namen und Schlüssel des Speicherkontos können Sie über das [Azure-Portal](https://portal.azure.com/) abrufen.
* Vertragsplan-ID

    Die Plan-ID können Sie über das [Machine Learning-Webdienstportal](https://services.azureml.net) ermitteln, indem Sie sich anmelden und auf einen Plannamen klicken.

Fügen Sie der JSON-Vorlage die Werte als untergeordnete Elemente des Knotens *Eigenschaften* auf der Ebene des Knotens *MachineLearningWorkspace* hinzu.

Hier sehen Sie ein Beispiel:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Ausführlichere Informationen finden Sie in den folgenden Artikeln sowie im Beispielcode:

* Referenz zu [Cmdlets im Azure Machine Learning Studio (Classic)](/powershell/module/az.machinelearning) auf MSDN

## <a name="consume-the-web-services"></a>Nutzen von Webdiensten

### <a name="from-the-machine-learning-web-services-ui-testing"></a>Über die Benutzeroberfläche der Azure Machine Learning-Webdienste (Testen)

Sie können Ihren Webdienst über das Machine Learning-Webdienstportal testen. Dies schließt das Testen der Schnittstellen des Request-Response Service (RRS) und des Stapelausführungsdiensts (Batch Execution Service, BES) ein.

* [Bereitstellen eines neuen Webdiensts](deploy-a-machine-learning-web-service.md)
* [Bereitstellen eines Machine Learning-Webdiensts](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Bereitstellen eines Kreditrisikomodells](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Über Excel

Sie können eine Excel-Vorlage zur Nutzung des Webdiensts herunterladen:

* [Nutzen eines (klassischen) Azure Machine Learning Studio-Webdiensts aus Excel](consuming-from-excel.md)
* [Excel-Add-In für (klassische) Azure Machine Learning Studio-Webdienste](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Über einen REST-basierten Client

Machine Learning-Webdienste sind RESTful-APIs. Sie können diese APIs von verschiedenen Plattformen (z. B. .NET, Python, R oder Java) nutzen. Die Seite **Consume** (Verbrauch) für Ihren Webdienst im [Machine Learning-Webdienstportal](https://services.azureml.net) enthält Beispielcode, der Ihnen den Einstieg erleichtern kann. Weitere Informationen finden Sie unter [Nutzen eines Webdiensts in Machine Learning Studio (Classic)](consume-web-services.md).