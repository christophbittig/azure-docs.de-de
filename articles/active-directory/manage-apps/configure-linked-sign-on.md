---
title: Hinzufügen des verknüpften einmaligen Anmeldens zu einer Anwendung
description: Hinzufügen des verknüpften einmaligen Anmeldens zu einer Anwendung in Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: bc50777e503c50ca68955b8324b75c7c6aa66c77
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132548480"
---
# <a name="add-linked-single-sign-on-to-an-application"></a>Hinzufügen des verknüpften einmaligen Anmeldens zu einer Anwendung

In diesem Artikel erfahren Sie, wie Sie das verknüpfte einmalige Anmelden (Single Sign-On, SSO) für Ihre Anwendung in Azure Active Directory (Azure AD) konfigurieren. Über das verknüpfte einmalige Anmelden kann Azure AD das einmalige Anmelden für eine Anwendung, die bereits in einem anderen Dienst für einmaliges Anmelden konfiguriert ist, bereitstellen. Mit der Option „Verknüpft“ können Sie den Zielort konfigurieren, wenn ein Benutzer die Anwendung im Portal „Meine Apps“ der Organisation oder im Microsoft 365-Portal auswählt.

Beim verknüpften einmaligen Anmelden wird die Anmeldefunktionen nicht durch Azure AD bereitgestellt. Mit der Option wird einfach der Ort festgelegt, an den Benutzer weitergeleitet werden, wenn sie die App in „Meine Apps“ oder im Microsoft 365-Portal auswählen.

Die verknüpfte Anmeldung ist unter anderem in den folgenden Szenarien sinnvoll:
- Sie fügen einer benutzerdefinierten Webanwendung, die derzeit einen Verbund verwendet, z. B. Active Directory-Verbunddienste (AD FS), einen Link hinzu.
- Sie fügen Deep-Links auf spezielle Webseiten hinzu, die aus den Zugriffsseiten des betreffenden Benutzers angezeigt werden sollen.
- Sie fügen einer Anwendung, für die keine Authentifizierung erforderlich ist, einen Link hinzu. Die Option „Verknüpft“ bietet zwar keine Anmeldefunktion mittels Azure AD Anmeldeinformationen, aber Sie können trotzdem einige der anderen Features von Unternehmensanwendungen verwenden. Sie können beispielsweise Überwachungsprotokolle verwenden und ein benutzerdefiniertes Logo und einen Anwendungsnamen hinzufügen.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren des verknüpften einmaligen Anmeldens in Ihrem Azure AD-Mandanten benötigen Sie Folgendes:
-   Ein Azure-Konto mit einem aktiven Abonnement. [Kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
-   Eine der folgenden Rollen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.
-   Eine Anwendung, die verknüpftes einmaliges Anmelden unterstützt.

## <a name="configure-linked-based-single-sign-on"></a>Konfigurieren des verknüpften einmaligen Anmeldens

1.  Melden Sie sich mit der entsprechenden Rolle im [Azure-Portal](https://portal.azure.com) an.
2.  Wählen Sie **Azure Active Directory** unter „Azure-Dienste“ und dann **Unternehmensanwendungen** aus.
3.  Suchen Sie nach der Anwendung, für die Sie verknüpftes einmaliges Anmelden hinzufügen möchten, und wählen Sie diese aus.
4.  Wählen Sie **Einmaliges Anmelden** und dann **Verknüpft** aus.
5.  Geben Sie die URL für die Anmeldeseite der Anwendung ein.
6.  Wählen Sie **Speichern** aus. 

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten des Zugriffs auf Apps](what-is-access-management.md)