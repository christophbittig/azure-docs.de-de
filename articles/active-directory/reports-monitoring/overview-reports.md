---
title: Was sind Azure Active Directory-Berichte? | Microsoft-Dokumentation
description: Dieser Artikel bietet eine allgemeine Übersicht über Azure Active Directory-Berichte.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 09/30/2020
ms.author: markvi
ms.reviewer: sarbar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e2f83a6b59c8f71ddd8a56a618bfad653f3b792
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995027"
---
# <a name="what-are-azure-active-directory-reports"></a>Was sind Azure Active Directory-Berichte?

Mit Azure Active Directory-Berichten (Azure AD) erhalten Sie einen umfassenden Überblick über Aktivitäten in Ihrer Umgebung. Die bereitgestellten Daten ermöglichen Ihnen Folgendes:

- Ermitteln, wie Ihre Apps und Dienste von Ihren Benutzern genutzt werden
- Erkennen potenzieller Risiken für die Integrität Ihrer Umgebung
- Behandeln von Problemen, die Ihre Benutzer an der Erledigung ihrer Arbeit hindern  

Die Architektur der Berichterstellung basiert auf zwei Hauptkomponenten:

- [Sicherheitsberichte](#security-reports)
- [Aktivitätsberichte](#activity-reports)

![Berichterstellung](./media/overview-reports/01.png)


## <a name="security-reports"></a>Sicherheitsberichte

Mit den Sicherheitsberichten können Sie die Identitäten Ihrer Organisation schützen. Es gibt zwei Arten von Sicherheitsberichten:

- **Benutzer mit Risikomarkierung:** Im [Sicherheitsbericht „Benutzer mit Risikomarkierung“](../identity-protection/overview-identity-protection.md) erhalten Sie eine Übersicht über Benutzerkonten, die unter Umständen kompromittiert wurden.

- **Riskante Anmeldungen:** Mit dem [Sicherheitsbericht „Riskante Anmeldungen“](../identity-protection/overview-identity-protection.md) erhalten Sie einen Indikator für Anmeldeversuche von Benutzern, die nicht der rechtmäßige Besitzer eines Benutzerkontos sind. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Welche Azure AD-Lizenz benötigen Sie für den Zugriff auf einen Sicherheitsbericht?  

Alle Editionen von Azure AD verfügen über die Sicherheitsberichte „Benutzer mit Risikomarkierung“ und „Riskante Anmeldungen“. Die Granularitätsebene von Berichten kann für die einzelnen Editionen aber variieren: 

- In den **Free- und Basic-Editionen von Azure Active Directory** erhalten Sie die Listen „Benutzer mit Risikomarkierung“ und „Riskante Anmeldungen“. 

- Mit der Edition **Azure Active Directory Premium 1** wird dieses Modell erweitert, indem Sie zusätzlich jeweils einige zugrunde liegende Risikoerkennungen untersuchen können, die für einen Bericht erkannt wurden. 

- In der Edition **Azure Active Directory Premium 2** erhalten Sie die ausführlichsten Informationen zu den zugrunde liegenden Risikoerkennungen, und Sie können Sicherheitsrichtlinien konfigurieren, mit denen automatisch auf konfigurierte Risikostufen reagiert wird.


## <a name="activity-reports"></a>Aktivitätsberichte

Anhand von Aktivitätsberichten können Sie das Verhalten von Benutzern in Ihrer Organisation nachvollziehen. In Azure AD gibt es zwei Arten von Aktivitätsberichten:

- **Überwachungsprotokolle:** Mit dem [Aktivitätsbericht „Überwachungsprotokolle“](concept-audit-logs.md) erhalten Sie Zugriff auf den Verlauf aller Aufgaben, die in Ihrem Mandanten durchgeführt werden.

- **Anmeldungen:** Mit dem [Aktivitätsbericht „Anmeldungen“](concept-sign-ins.md) können Sie ermitteln, von wem die Aufgaben durchgeführt wurden, die im Bericht „Überwachungsprotokolle“ aufgeführt sind.



> [!VIDEO https://www.youtube.com/embed/ACVpH6C_NL8]




### <a name="audit-logs-report"></a>Bericht „Überwachungsprotokolle“ 

Der [Bericht „Überwachungsprotokolle“](concept-audit-logs.md) enthält Datensätze zu Systemaktivitäten, die für die Konformität relevant sind. Diese Daten sind in folgenden häufigen Szenarien hilfreich:

- Ein Benutzer meines Mandanten hat Zugriff auf eine Administratorgruppe erhalten. Wer hat dem Benutzer den Zugriff gewährt? 

- Ich möchte eine Liste mit Benutzern erhalten, die sich an einer bestimmten App angemeldet haben, seitdem ich das App-Onboarding durchgeführt habe, und ich möchte Informationen zur Leistung der App erhalten.

- Ich möchte wissen, wie viele Kennwortzurücksetzungen unter meinem Mandanten durchgeführt werden.


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Welche Azure AD-Lizenz benötigen Sie, um auf den Bericht „Überwachungsprotokolle“ zuzugreifen?  

Der Bericht „Überwachungsprotokolle“ ist für Features verfügbar, für die Sie über Lizenzen verfügen. Wenn Sie im Besitz einer Lizenz für ein bestimmtes Feature sind, haben Sie auch Zugriff auf die dazugehörigen Informationen zum Überwachungsprotokoll. Einen detaillierten Vergleich der Funktionen bei den [verschiedenen Lizenztypen](../fundamentals/active-directory-whatis.md#what-are-the-azure-ad-licenses) finden Sie auf der Seite [Azure Active Directory – Preise](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing). Weitere Informationen finden Sie unter [Azure Active Directory-Features und -Funktionen](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad).

### <a name="sign-ins-report"></a>Bericht zu Anmeldeaktivitäten

Mit dem [Bericht „Anmeldungen“](concept-sign-ins.md) können Sie beispielsweise folgende Fragen beantworten:

- Wie sieht das Anmeldemuster eines Benutzers aus?
- Wie viele Benutzer sind für Benutzer im Laufe einer Woche angemeldet?
- Wie lautet der Status dieser Anmeldungen?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Welche Azure AD-Lizenz benötigen Sie, um auf den Aktivitätsbericht „Anmeldungen“ zuzugreifen?  

Ihrem Mandanten muss eine Azure AD Premium-Lizenz zugewiesen sein, um auf den Aktivitätsbericht „Anmeldungen“ zugreifen zu können.

## <a name="programmatic-access"></a>Programmgesteuerter Zugriff

Zusätzlich zur Benutzeroberfläche ermöglicht Azure AD Ihnen über eine Reihe von REST-basierten APIs auch den [programmgesteuerten Zugriff](concept-reporting-api.md) auf die Berichtsdaten. Sie können diese APIs über verschiedene Programmiersprachen und Tools aufrufen. 

## <a name="next-steps"></a>Nächste Schritte

- [Bericht „Riskante Anmeldungen“](../identity-protection/overview-identity-protection.md)
- [Bericht „Überwachungsprotokolle“](concept-audit-logs.md)
- [Bericht „Anmeldungen“](concept-sign-ins.md)