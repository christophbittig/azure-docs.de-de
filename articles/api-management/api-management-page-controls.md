---
title: Seitensteuerelemente in Azure API Management | Microsoft-Dokumentation
description: Enthält Informationen zu den Seitensteuerelementen, die für die Verwendung in Entwicklerportalvorlagen in Azure API Management verfügbar sind.
services: api-management
documentationcenter: ''
author: dlepow
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: danlep
ms.openlocfilehash: cdaa82308e4208306327dc064642c2b0897597d5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128635202"
---
# <a name="azure-api-management-page-controls"></a>Seitensteuerelemente in Azure API Management
Azure API Management enthält die folgenden Seitensteuerelemente zur Verwendung in Entwicklerportalvorlagen.  
  
Um ein Steuerelement zu verwenden, platzieren Sie es an der gewünschten Stelle in der Entwicklerportalvorlage. Einige Steuerelemente, z.B. das Steuerelement [app-actions](#app-actions), verfügen über Parameter, wie im folgenden Beispiel gezeigt:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Die Werte für die Parameter werden als Teil des Datenmodells für die Vorlage übergeben. In den meisten Fällen können Sie einfach das bereitgestellte Beispiel für jedes Steuerelement einfügen, damit es ordnungsgemäß funktioniert. Weitere Informationen zu den Parameterwerten finden Sie im Datenmodellabschnitt für jede Vorlage, in der ein Steuerelement verwendet werden kann.  

Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Seitensteuerelemente in Entwicklerportalvorlagen  
  
-   [app-actions](#app-actions)  
-   [basic-signin](#basic-signin)  
-   [paging-control](#paging-control)  
-   [providers](#providers)  
-   [search-control](#search-control)  
-   [sign-up](#sign-up)  
-   [subscribe-button](#subscribe-button)  
-   [subscription-cancel](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a> app-actions  
 Das Steuerelement `app-actions` bietet eine Benutzeroberfläche für die Interaktion mit Anwendungen auf der Benutzerprofilseite im Entwicklerportal.  
  
 ![Steuerelement „app&#45;actions“](./media/api-management-page-controls/APIM-app-actions-control.png "APIM: Steuerelement „app-actions“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parameter  
  
|Parameter|BESCHREIBUNG|  
|---------------|-----------------|  
|appId|Die ID der Anwendung.|  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `app-actions` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Anwendungen](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a> basic-signin  
 Das Steuerelement `basic-signin` stellt ein Steuerelement zum Sammeln von Informationen zur Benutzeranmeldung auf der Anmeldeseite im Entwicklerportal bereit.  
  
 ![Steuerelement „basic&#45;signin“](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM: Steuerelement „basic-signin“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parameter  
 Keine.  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `basic-signin` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Melden Sie sich an:](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a> paging-control  
 `paging-control` stellt eine Seitennavigation für Entwicklerportalseiten bereit, auf denen eine Liste von Elementen angezeigt wird.  
  
 ![Steuerelement „paging-control“](./media/api-management-page-controls/APIM-paging-control.png "APIM: Steuerelement „paging-control“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parameter  
 Keine.  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `paging-control` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [API-Liste](api-management-api-templates.md#APIList)  
  
-   [Problemliste](api-management-issue-templates.md#IssueList)  
  
-   [Produktliste](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a> providers  
 Das Steuerelement `providers` stellt ein Steuerelement zur Auswahl von Authentifizierungsanbietern auf der Anmeldeseite im Entwicklerportal bereit.  
  
 ![Steuerelement „providers“](./media/api-management-page-controls/APIM-providers-control.png "APIM: Steuerelement „providers“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parameter  
 Keine.  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `providers` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Melden Sie sich an:](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a> search-control  
 `search-control` stellt eine Suchfunktion für Entwicklerportalseiten bereit, auf denen eine Liste von Elementen angezeigt wird.  
  
 ![Steuerelement „search-control“](./media/api-management-page-controls/APIM-search-control.png "APIM: Steuerelement „search-control“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parameter  
 Keine.  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `search-control` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [API-Liste](api-management-api-templates.md#APIList)  
  
-   [Produktliste](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a> sign-up  
 Das Steuerelement `sign-up` stellt ein Steuerelement zum Sammeln von Benutzerprofilinformationen auf der Registrierungsseite im Entwicklerportal bereit.  
  
 ![Steuerelement „sign&#45;up“](./media/api-management-page-controls/APIM-sign-up-control.png "APIM: Steuerelement „sign-up“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parameter  
 Keine.  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `sign-up` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Registrieren](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a> subscribe-button  
 `subscribe-button` bietet ein Steuerelement, mit dem ein Benutzer ein Produkt abonnieren kann.  
  
 ![Steuerelement „subscribe&#45;button“](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM: Steuerelement „subscribe-button“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parameter  
 Keine.  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `subscribe-button` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Produkt](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a> subscription-cancel  
 Das Steuerelement `subscription-cancel` stellt auf der Benutzerprofilseite im Entwicklerportal ein Steuerelement für das Kündigen des Abonnements für ein Produkt bereit.  
  
 ![Steuerelement „subscription&#45;cancel“](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM: Steuerelement „subscription-cancel“")  
  
### <a name="usage"></a>Verwendung  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parameter  
  
|Parameter|BESCHREIBUNG|  
|---------------|-----------------|  
|subscriptionId|Die ID des Abonnements, das gekündigt werden soll.|  
|cancelUrl|Die URL zum Kündigen des Abonnements|  
  
### <a name="developer-portal-templates"></a>Entwicklerportal: Vorlagen  
 Das Steuerelement `subscription-cancel` kann in den folgenden Vorlagen des Entwicklerportals verwendet werden:  
  
-   [Produkt](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Arbeiten mit Vorlagen finden Sie unter [So passen Sie das Azure API Management-Entwicklerportal mithilfe von Vorlagen an](api-management-developer-portal-templates.md).
