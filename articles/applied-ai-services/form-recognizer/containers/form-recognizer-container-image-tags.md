---
title: Formularerkennung Bild-Tags und Versionshinweise
titleSuffix: Azure Applied AI Services
description: Eine Auflistung aller Formularerkennungs Containerbild-Tags.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: reference
ms.date: 09/02/2021
ms.author: lajanuar
keywords: Docker, Container, Bilder
ms.openlocfilehash: d38fd74324246463e9f74ff8d53c67ccb2ccd039
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481827"
---
# <a name="form-recognizer-container-image-tags-and-release-notes"></a>Tags und Versionshinweise für Container der Formularerkennung

> [!IMPORTANT]
>
> * **Formularerkennung v2.1 Container** befinden sich in der Gated Preview. Wenn Sie die Container verwenden möchten, müssen Sie eine [Onlineanfrage](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu) einreichen und diese genehmigen lassen. 
>
> * Im Onlineanforderungsformular müssen Sie eine gültige E-Mail-Adresse angeben, die zu der Organisation gehört, die die Azure-Abonnement-ID besitzt und in der Sie Zugriff auf dieses Abonnement haben oder erhalten haben.

## <a name="feature-containers"></a>Besondere Container

Die Features der Formularerkennung werden von sieben Containern unterstützt:

| Containername | Vollqualifizierter Imagename |
|---|---|
| **Layout** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout |
| **Visitenkarte** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard |
| **Ausweisdokument** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document |
| **Rechnung** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt |
| **Rechnung** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice |
| **Benutzerdefinierte API** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api |
| **Benutzerdefiniert überwacht** | mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised |

## <a name="microsoft-container-registry"></a>Microsoft-Containerregistrierung

Die Container-Images der Formularerkennung finden Sie in der Microsoft Container Registry **mcr.microsoft.** **<span></span>com** container registry syndicate, der primären Registry für alle von Microsoft veröffentlichten Docker-Images.

* Die Erkennungsfunktion für MCR wird über [docker hub](https://hub.docker.com/publishers/microsoftowner) bereitgestellt.

* Sie können auch [die Liste der Repositories innerhalb von mcr](https://mcr.microsoft.com/v2/_catalog) abfragen.

## <a name="form-recognizer-tags"></a>Tags der Formularerkennung

Die folgenden Tags sind für Formularerkennung verfügbar:

### <a name="latest-version"></a>[Aktuelle Version](#tab/current)

Veröffentlichungshinweise für `v2.1` (eingeschränkte Vorschau):

| Container | Tags |
|------------|:------|
| **Layout**| &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.0.016140001-08108749-amd64-preview`|
| **Visitenkarte** | &bullet; `latest` </br> &bullet; `2.1-preview` </br> &bullet; `2.1.016190001-amd64-preview`  </br> &bullet; `2.1.016320001-amd64-preview`  |
| **Ausweisdokument** | &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Rechnung**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Rechnung**| &bullet; `latest` </br> &bullet; `2.1-preview`</br>&bullet; `2.1.016190001-amd64-preview`</br>&bullet; `2.1.016320001-amd64-preview` |
| **Benutzerdefinierte API** | &bullet; `latest` </br> &bullet;`2.1-distroless-20210622013115034-0cc5fcf6`</br>&bullet; `2.1-preview`|
| **Benutzerdefiniert überwacht**| &bullet; `latest` </br> &bullet; `2.1-distroless-20210622013149174-0cc5fcf6`</br>&bullet; `2.1-preview`|

### <a name="previous-versions"></a>[Vorherige Versionen](#tab/previous)

> [!IMPORTANT]
> Der Formularerkennungscontainer v1.0 wurde eingestellt.

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Installieren und Ausführen eines Containers für die Formularerkennung](form-recognizer-container-install-run.md)
>