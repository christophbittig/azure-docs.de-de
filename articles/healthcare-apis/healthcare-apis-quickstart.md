---
title: Bereitstellen eines Arbeitsbereichs im Azure-Portal – Azure Healthcare-APIs
description: In diesem Dokument erfahren Benutzer, wie Sie einen Arbeitsbereich im Azure-Portal bereitstellen.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 07/12/2021
ms.author: ginle
ms.custom: mode-portal
ms.openlocfilehash: 89be6d8bab9ef544ffa62a85f0b87afb15fab013
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061577"
---
# <a name="deploy-healthcare-apis-preview-workspace-using-azure-portal"></a>Bereitstellen eines Arbeitsbereichs für Gesundheits-APIs (Vorschauversion) mithilfe von Azure-Portal

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie einen Arbeitsbereich erstellen, indem Sie Azure Healthcare-APIs über die Azure-Portal bereitstellen. Der Arbeitsbereich ist ein zentralisierter logischer Container für alle Ihre Gesundheits-APIs-Dienste wie FHIR-Dienste, DICOM®-Dienste und IoT-Connectors. Damit können Sie bestimmte Konfigurationseinstellungen organisieren und verwalten, die ggf. von allen zugrunde liegenden Datasets und Diensten gemeinsam genutzt werden.


## <a name="prerequisite"></a>Voraussetzung

Bevor Sie einen Arbeitsbereich im Azure-Portal erstellen können, benötigen Sie ein Kontoabonnement. Wenn Sie noch kein Azure-Abonnement besitzen, finden Sie weitere Informationen unter [Erstellen Ihres kostenlosen Azure-Kontos.](https://azure.microsoft.com/free/search/?OCID=AID2100131_SEM_c4b0772dc7df1f075552174a854fd4bc:G:s&ef_id=c4b0772dc7df1f075552174a854fd4bc:G:s&msclkid=c4b0772dc7df1f075552174a854fd4bc)

## <a name="create-new-azure-service"></a>Erstellen eines neuen Azure-Diensts

Klicken Sie im Azure-Portal auf **Ressource erstellen**.

[![Ressource ](media/create-resource.png) erstellen ](media/create-resource.png#lightbox)

## <a name="search-for-azure-healthcare-apis"></a>Suchen nach Azure Healthcare-APIs

Geben Sie im Suchfeld **Azure Healthcare-APIs** ein.

[![Suchen nach ApIs ](media/search-for-healthcare-apis.png) für das Gesundheitswesen ](media/search-for-healthcare-apis.png#lightbox)

## <a name="create-azure-healthcare-api-account"></a>Erstellen eines Azure Healthcare-API-Kontos

Wählen Sie **Erstellen** aus, um ein neues Azure Healthcare-APIs-Konto zu erstellen.

   [![Erstellen einer Arbeitsbereichsvorschau ](media/create-workspace-preview.png) ](media/create-workspace-preview.png#lightbox)

## <a name="enter-subscription-and-instance-details"></a>Eingeben von Abonnement- und Instanzdetails

1. Wählen Sie in den Dropdownlisten ein **Abonnement** und eine **Ressourcengruppe** aus, oder wählen **Sie Neu erstellen** aus.

   [![Erstellen eines neuen ](media/create-healthcare-api-workspace-new.png) Arbeitsbereichs ](media/create-healthcare-api-workspace-new.png#lightbox)

2. Geben Sie einen **Namen** für den Arbeitsbereich ein, und wählen Sie dann **Region** aus. Der Name muss 3 bis 24 alphanumerische Zeichen in Kleinbuchstaben enthalten. Verwenden Sie keinen Bindestrich "-", da es sich um ein ungültiges Zeichen für den Namen handelt. Informationen zu Regionen und Verfügbarkeitszonen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure.](../availability-zones/az-overview.md)

3. (**Optional)** Wählen Sie **Weiter: Tags >** aus. Geben Sie einen **Namen** und **einen Wert** ein, und wählen Sie dann **Weiter: Überprüfen + erstellen** aus. 

   [![Schilder ](media/tags-new.png) ](media/tags-new.png#lightbox)

   Tags sind Name-Wert-Paare, die zum Kategorisieren von Ressourcen verwendet werden. Weitere Informationen zu Tags finden Sie unter [Verwenden von Tags zum Organisieren Ihrer Azure-Ressourcen und Verwaltungshierarchie.](.././azure-resource-manager/management/tag-resources.md)

4. Klicken Sie auf **Erstellen**.

[![Arbeitsbereichsbedingungen ](media/workspace-terms.png) ](media/workspace-terms.png)


   **Optional:** Sie können **Vorlage zur Automatisierung** Ihres neu erstellten Arbeitsbereichs herunterladen auswählen.


## <a name="next-steps"></a>Nächste Schritte

Nachdem der Arbeitsbereich erstellt wurde, haben Sie folgende Möglichkeiten:

* Bereitstellen von FHIR-Diensten
* Bereitstellen von DICOM-Diensten
* Stellen Sie eine IoT Connector bereit, und erfassen Sie Daten in Ihrem FHIR-Dienst.
* Transformieren Sie Ihre Daten in verschiedene Formate und die sekundäre Verwendung mithilfe unserer APIs zur Konvertierung und Identifizierungsentgrenzung.


[![Bereitstellen verschiedener Dienste ](media/healthcare-apis-deploy-services.png) ](media/healthcare-apis-deploy-services.png)

>[!div class="nextstepaction"]
>[Übersicht über den Arbeitsbereich](workspace-overview.md)
