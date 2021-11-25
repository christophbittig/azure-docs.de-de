---
title: Bereitstellen des DICOM-Diensts mithilfe Azure-Portal – Azure Healthcare-APIs
description: In diesem Artikel wird beschrieben, wie Sie den DICOM-Dienst im Azure-Portal.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 08/04/2021
ms.author: aersoy
ms.custom: mode-portal
ms.openlocfilehash: fefaf7fc2daced95ffde90834ad38131ecfc6b94
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2021
ms.locfileid: "133041678"
---
# <a name="deploy-dicom-service-using-the-azure-portal"></a>Bereitstellen des DICOM-Diensts mithilfe Azure-Portal

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Schnellstartanleitung erfahren Sie, wie Sie den DICOM-Dienst mithilfe der Azure-Portal.

Nachdem die Bereitstellung abgeschlossen ist, können Sie mithilfe der Azure-Portal zum neu erstellten DICOM-Dienst navigieren, um die Details einschließlich Ihrer Dienst-URL anzuzeigen. Die Dienst-URL für den Zugriff auf Ihren DICOM-Dienst ist: ```https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com``` . Stellen Sie sicher, dass Sie die Version als Teil der URL angeben, wenn Sie Anforderungen stellen. Weitere Informationen finden Sie in der [Dokumentation api versioning for DICOM service (API-Versionierung für den DICOM-Dienst).](api-versioning-dicom-service.md)

## <a name="prerequisite"></a>Voraussetzung

Zum Bereitstellen des DICOM-Diensts müssen Sie über einen Arbeitsbereich verfügen, der in der Azure-Portal. Weitere Informationen zum Erstellen eines Arbeitsbereichs finden Sie unter **Bereitstellen eines Arbeitsbereichs im Azure-Portal**.

## <a name="deploying-dicom-service"></a>Bereitstellen des DICOM-Diensts

1. Wählen Sie **auf der Seite** Ressourcengruppe des Azure-Portal den Namen Ihres Arbeitsbereichs für Die Gesundheits-APIs **aus.**

   [![Wählen Sie Arbeitsbereichsressourcengruppe aus. ](media/select-workspace-resource-group.png) ](media/select-workspace-resource-group.png#lightbox)

2. Wählen Sie **DICOM-Dienst bereitstellen aus.**

   [![bereitstellen des dicom-Diensts. ](media/workspace-deploy-dicom-services.png) ](media/workspace-deploy-dicom-services.png#lightbox)


3. Wählen Sie **DICOM-Dienst hinzufügen aus.**

   [![fügen Sie den dicom-Dienst hinzu. ](media/add-dicom-service.png) ](media/add-dicom-service.png#lightbox)


4. Geben Sie einen Namen für den DICOM-Dienst ein, und wählen Sie **dann Überprüfen + erstellen aus.** 

    [![dicom-Dienstname. ](media/enter-dicom-service-name.png) ](media/enter-dicom-service-name.png#lightbox)


   (**Optional**) Wählen **Sie Weiter: Tags >.**

    Tags sind Name-Wert-Paare, die zum Kategorisieren von Ressourcen verwendet werden. Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren ihrer Azure-Ressourcen und Verwaltungshierarchie.](../../azure-resource-manager/management/tag-resources.md)

5. Wenn Sie das grüne Überprüfungs-Häkchen sehen, wählen Sie **Erstellen aus,** um den DICOM-Dienst bereitzustellen.

6. Wenn der Bereitstellungsprozess abgeschlossen ist, wählen Sie **Zu Ressource wechseln aus.**  

   [![dicom wechseln Sie zur Ressource. ](media/go-to-resource.png) ](media/go-to-resource.png#lightbox)



   Das Ergebnis des neu bereitgestellten DICOM-Diensts ist unten dargestellt.

   [![dicom hat die Bereitstellung abgeschlossen. ](media/results-deployed-dicom-service.png) ](media/results-deployed-dicom-service.png#lightbox)



## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)
