---
title: Erste Schritte mit der Azure AD-Berichterstellungs-API | Microsoft-Dokumentation
description: Vorgehensweise zum Einstieg in die Azure Active Directory-Berichterstellungs-API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08d3f5de7f84caca2e16c39adf422b9309d544f0
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997094"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Erste Schritte mit der Berichterstellungs-API von Azure Active Directory

Unter Azure Active Directory können Sie viele verschiedene [Berichte](overview-reports.md) nutzen, die nützliche Informationen für Anwendungen enthalten, z.B. SIEM-Systeme, Überwachungsberichte und Business Intelligence-Tools. 

Mit der Microsoft Graph-API für Azure AD-Berichte können Sie über eine Gruppe von REST-basierten APIs programmgesteuerten Zugriff auf die Daten erhalten. Sie können diese APIs über verschiedene Programmiersprachen und Tools aufrufen.

Dieser Artikel enthält eine Übersicht über die Berichterstellungs-API und beispielsweise auch Informationen zu den Zugriffsmöglichkeiten.

Wenn Probleme auftreten sollten, lesen Sie [Gewusst wie: Beziehen von Support für Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um – mit oder ohne Benutzereingriff – auf die Berichterstellungs-API zuzugreifen:

1. Zuweisen von Rollen (Sicherheitsleseberechtigter, Sicherheitsadministrator, globaler Administrator)
2. Registrieren einer Anwendung
3. Erteilen von Berechtigungen
4. Erfassen von Konfigurationseinstellungen

Ausführliche Anweisungen finden Sie in den [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md). 

## <a name="api-endpoints"></a>API-Endpunkte 

Der Microsoft Graph-API-Endpunkt für Überwachungsprotokolle ist `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits`, und für Anmeldungen lautet er `https://graph.microsoft.com/v1.0/auditLogs/signIns`. Weitere Informationen finden Sie in der [Referenz zur Überwachungs-API](/graph/api/resources/directoryaudit) und in der [Referenz zur Anmelde-API](/graph/api/resources/signIn).

Sie können die [API für Identity Protection-Risikoerkennungen](/graph/api/resources/identityprotection-root) verwenden, um programmgesteuerten Zugriff auf Sicherheitserkennungen über Microsoft Graph zu erhalten. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Active Directory Identity Protection und Microsoft Graph](../identity-protection/howto-identity-protection-graph-api.md). 
  
Sie können auch die [Bereitstellungsprotokolle-API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta&preserve-view=true) verwenden, um programmgesteuerten Zugriff auf Bereitstellungsereignisse in Ihrem Mandanten zu erhalten. 

## <a name="apis-with-microsoft-graph-explorer"></a>APIs mit Microsoft Graph-Tester

Sie können den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer) verwenden, um Ihre Anmeldung zu überprüfen und API-Daten zu überwachen. Achten Sie darauf, bei der Anmeldung bei Ihrem Konto beide Anmeldeschaltflächen auf der Benutzeroberfläche des Graph-Testers zu verwenden und die Berechtigungen **AuditLog.Read.All** und **Directory.Read.All** wie dargestellt festzulegen.   

![Graph-Tester](./media/concept-reporting-api/graph-explorer.png)

![Benutzeroberfläche zum Ändern von Berechtigungen](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>Verwenden von Zertifikaten für den Zugriff auf die Azure AD-Berichterstellungs-API 

Verwenden Sie die Azure AD-Berichterstellungs-API mit Zertifikaten, wenn Sie die Daten für die Berichterstellung ohne Benutzeraktion abrufen möchten.

Ausführliche Anweisungen finden Sie unter [Abrufen von Daten per Azure AD-Berichterstellungs-API mit Zertifikaten](tutorial-access-api-with-certificates.md).

## <a name="next-steps"></a>Nächste Schritte

 * [Voraussetzungen für den Zugriff auf die Azure Active Directory-Berichterstellungs-API](howto-configure-prerequisites-for-reporting-api.md) 
 * [Abrufen von Daten per Azure AD Reporting-API mit Zertifikaten](tutorial-access-api-with-certificates.md)
 * [Beheben von Fehlern in der Azure AD-Berichterstellungs-API](troubleshoot-graph-api.md)
