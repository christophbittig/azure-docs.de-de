---
title: Konfigurieren einer Azure Active Directory-Anwendungsproxyanwendung
description: Erfahren Sie, wie Sie in wenigen einfachen Schritten eine Azure Active Directory-Anwendungsproxyanwendung erstellen und konfigurieren.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: 40377abd1faffb51e686099a2574785db3811701
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990328"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Konfigurieren einer Anwendungsproxyanwendung

In diesem Artikel wird das Konfigurieren einer Anwendungsproxyanwendung in Azure AD erläutert, um Ihre lokalen Anwendungen in der Cloud verfügbar zu machen.

## <a name="recommended-documents"></a>Empfohlene Dokumente

Informationen zu den Erstkonfigurationen und zur Erstellung einer Anwendungsproxyanwendung über das Administratorportal finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md).

Ausführliche Informationen zum Konfigurieren von Connectors finden Sie unter [Aktivieren des Anwendungsproxys über das Azure-Portal](application-proxy-add-on-premises-application.md).

Informationen zum Hochladen von Zertifikaten und Verwenden von benutzerdefinierten Domänen finden Sie unter [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Erstellen der Anwendung/Festlegen der URLs

Wenn Sie die Schritte in der Dokumentation [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md) befolgen und beim Erstellen der Anwendung einen Fehler erhalten, finden Sie weitere Informationen und Vorschläge zur Behebung der Anwendung in den Fehlerdetails. Die meisten Fehlermeldungen enthalten eine empfohlene Problemlösung. Stellen Sie Folgendes sicher, um häufige Fehler zu vermeiden:

- Sie sind ein Administrator mit der Berechtigung zum Erstellen einer Anwendungsproxyanwendung.
- Die interne URL ist eindeutig.
- Die externe URL ist eindeutig.
- Die URLs beginnen mit „http“ oder „https“ und enden mit einem „/“.
- Die URL muss ein Domänenname, keine IP-Adresse sein.

Die Fehlermeldung sollte beim Erstellen der Anwendung in der oberen rechten Ecke angezeigt werden. Sie können auch das Benachrichtigungssymbol auswählen, um die Fehlermeldungen anzuzeigen.

![Zeigt, wo Sie die Benachrichtigungsaufforderung im Azure-Portal finden](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Konfigurieren von Connectors/Connectorgruppen

Wenn Sie aufgrund einer Warnung zu Connectors oder Connectorgruppen Probleme beim Konfigurieren Ihrer Anwendung haben, finden Sie weitere Informationen zum Herunterladen von Connectors in den Anweisungen zum Aktivieren des Anwendungsproxys. Weitere Informationen zu Connectors finden Sie in der [Connectors-Dokumentation](application-proxy-connectors.md).

Wenn die Connectors inaktiv sind, bedeutet dies, dass sie den Dienst nicht erreichen können. Der Grund hierfür ist häufig, dass die erforderlichen Ports nicht geöffnet sind. Eine Liste der erforderlichen Ports finden Sie im Abschnitt „Voraussetzungen“ der Dokumentation zum Aktivieren des Anwendungsproxys.

## <a name="upload-certificates-for-custom-domains"></a>Hochladen von Zertifikaten für benutzerdefinierte Domänen

Benutzerdefinierte Domänen bieten Ihnen die Möglichkeit, die Domäne Ihrer externen URLs anzugeben. Sie müssen das Zertifikat für diese Domäne hochladen, damit Sie benutzerdefinierte Domänen verwenden können. Informationen zu benutzerdefinierten Domänen und Zertifikaten finden Sie unter [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).

Wenn beim Hochladen des Zertifikats Probleme auftreten, suchen Sie im Portal nach den Fehlermeldungen, um weitere Informationen zu den Problemen mit dem Zertifikat zu erhalten. Allgemeine Zertifikatprobleme umfassen Folgendes:

- Abgelaufenes Zertifikat
- Selbstsigniertes Zertifikat
- Fehlender privater Schlüssel für Zertifikat

Die Fehlermeldung wird in der oberen rechten Ecke angezeigt, während Sie versuchen, das Zertifikat hochzuladen. Sie können auch das Benachrichtigungssymbol auswählen, um die Fehlermeldungen anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

[Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md)
