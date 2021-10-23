---
title: Azure Active Directory-Anwendungsproxy und Tableau
description: In diesem Artikel erfahren Sie, wie mithilfe des Azure Active Directory-Anwendungsproxys (Azure AD) Remotezugriff für Ihre Tableau-Bereitstellung zur Verfügung gestellt werden kann.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: d7634858c1cc44d2a04847b7fef39eed588280ee
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989245"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory-Anwendungsproxy und Tableau 

Durch die Partnerschaft von Azure Active Directory-Anwendungsproxy und Tableau soll sichergestellt werden, dass Sie mit dem Anwendungsproxy problemlos Remotezugriff für Ihre Tableau-Bereitstellung bereitstellen können. In diesem Artikel wird das Konfigurieren dieses Szenarios erläutert.  

## <a name="prerequisites"></a>Voraussetzungen 

Bei dem Szenario in diesem Artikel wird davon ausgegangen, dass folgende Voraussetzung erfüllt sind:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) ist konfiguriert. 

- Ein [Anwendungsproxyconnector](../app-proxy/application-proxy-add-on-premises-application.md) wurde installiert. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Aktivieren des Anwendungsproxys für Tableau 

Der Anwendungsproxy unterstützt den OAuth 2.0 Grant Flow, der für das ordnungsgemäße Funktionieren von Tableau erforderlich ist. Dies bedeutet, dass keine besonderen Schritte mehr erforderlich sind, um diese Anwendung zu aktivieren, außer der Konfiguration durch die folgenden Veröffentlichungsschritte.


## <a name="publish-your-applications-in-azure"></a>Veröffentlichen Ihrer Anwendungen in Azure 

Um Tableau zu veröffentlichen, müssen Sie eine Anwendung im Azure-Portal veröffentlichen.

Für:

- Ausführliche Anweisungen zu den Schritten 1-8 finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](../app-proxy/application-proxy-add-on-premises-application.md). 
- Wie Sie Tableau-Werte für die Felder des App-Proxys ermitteln, erfahren Sie in der Dokumentation zu Tableau.  

**So veröffentlichen Sie Ihre App** 


1. Melden Sie sich als Anwendungsadministrator beim [Azure-Portal](https://portal.azure.com) an. 

2. Wählen Sie **Azure Active Directory > Unternehmensanwendungen** aus. 

3. Wählen Sie oben auf dem Blatt **Hinzufügen** aus. 

4. Wählen Sie **Lokale Anwendung** aus. 

5. Füllen Sie die Pflichtfelder mit Informationen zur neuen App aus. Befolgen Sie diese Anleitung für die folgenden Einstellungen: 

    - **Interne URL**: Diese Anwendung muss über eine interne URL verfügen, die die Tableau-URL selbst ist. Beispiel: `https://adventure-works.tableau.com`. 

    - **Methode für die Vorauthentifizierung**: Azure Active Directory (empfohlen, aber nicht erforderlich). 

6. Wählen Sie oben auf dem Blatt **Hinzufügen** aus. Ihre Anwendung wird hinzugefügt, das Schnellstartmenü wird geöffnet. 

7. Wählen Sie im Schnellstartmenü **Zuweisen eines Benutzers zu Testzwecken** aus, und fügen Sie der Anwendung mindestens einen Benutzer hinzu. Stellen Sie sicher, dass dieses Testkonto auf die lokale Anwendung zugreifen kann. 

8. Wählen Sie **Zuweisen** aus, um die Zuweisung des Testbenutzers zu speichern. 

9. (Optional) Wählen Sie auf der Seite „App-Verwaltung“ die Option **Einmaliges Anmelden** aus. Wählen Sie **Integrierte Windows-Authentifizierung** im Dropdownmenü aus, und füllen Sie die erforderlichen Felder auf der Grundlage Ihrer Tableau-Konfiguration aus. Wählen Sie **Speichern** aus. 

 

## <a name="testing"></a>Testen 

Ihre Anwendung kann jetzt getestet werden. Greifen Sie auf die externe URL zu, die Sie zum Veröffentlichen von Tableau verwendet haben, und melden Sie sich als Benutzer an, der beiden Anwendungen zugewiesen ist.



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Azure AD-Anwendungsproxy finden Sie unter [Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen](application-proxy.md).

