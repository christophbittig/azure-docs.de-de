---
title: Integrieren lokaler Apps mit Cloud App Security unter Verwendung von Application Proxy – Azure Active Directory
description: Konfigurieren Sie eine lokale Anwendung in Azure Active Directory für die Verwendung mit Microsoft Cloud App Security (MCAS). Verwenden Sie die App-Steuerung für bedingten Zugriff in MCAS zum Überwachen und Steuern von Sitzungen in Echtzeit mithilfe von Richtlinien für bedingten Zugriff. Sie können diese Richtlinien auf lokale Anwendungen anwenden, die den Anwendungsproxy in Azure Active Directory (Azure AD) verwenden.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 2a626d7c26f165d554ec7dcc72e5e5f34c5b58c4
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989321"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Konfigurieren der Echtzeitüberwachung des Anwendungszugriffs mit Microsoft Cloud App Security und Azure Active Directory
Konfigurieren Sie eine lokale Anwendung in Azure Active Directory (Azure AD) für die Verwendung von Microsoft Cloud App Security (MCAS) für die Echtzeitüberwachung. MCAS nutzt die App-Steuerung für bedingten Zugriff zum Überwachen und Steuern von Sitzungen in Echtzeit mithilfe von Richtlinien für bedingten Zugriff. Sie können diese Richtlinien auf lokale Anwendungen anwenden, die den Anwendungsproxy in Azure Active Directory (Azure AD) verwenden.

Im Folgenden finden Sie einige Beispiele für die Typen von Richtlinien, die Sie mit MCAS erstellen können:

- Blockieren oder schützen Sie den Download von vertraulichen Dokumenten auf nicht verwalteten Geräten.
- Überwachen Sie, wann sich Benutzer mit hohem Risiko bei Anwendungen anmelden, und protokollieren Sie dann deren Aktionen innerhalb der Sitzung. Mit diesen Informationen können Sie das Benutzerverhalten analysieren, um zu bestimmen, wie Sie Sitzungsrichtlinien anwenden.
- Verwenden Sie Clientzertifikate oder Gerätekonformität, um den Zugriff von nicht verwalteten Geräten auf bestimmte Anwendungen zu blockieren.
- Beschränken Sie Benutzersitzungen, die nicht aus dem Unternehmensnetzwerk stammen. Sie können Benutzern, die von außerhalb des Unternehmensnetzwerks auf eine Anwendung zugreifen, eingeschränkten Zugriff gewähren. Beispielsweise kann dieser eingeschränkte Zugriff den Benutzer daran hindern, vertrauliche Dokumenten herunterzuladen.

Weitere Informationen finden Sie unter [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad).

## <a name="requirements"></a>Anforderungen

Lizenz:

- EMS E5-Lizenz oder 
- Azure Active Directory Premium P1 und MCAS Standalone.

Lokale Anwendung:

- Die für lokale Anwendungen muss die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) verwenden.

Konfigurieren des Anwendungsproxys:

- Konfigurieren Sie Azure AD für die Verwendung des Anwendungsproxys, bereiten Sie die Umgebung vor, und installieren Sie den Anwendungsproxy-Connector. Unter [Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure AD](../app-proxy/application-proxy-add-on-premises-application.md) finden Sie ein Tutorial. 

## <a name="add-on-premises-application-to-azure-ad"></a>Hinzufügen lokaler Anwendungen zu Azure AD

Fügen Sie Azure AD eine lokale Anwendung hinzu. Unter [Hinzufügen einer lokalen App zu Azure AD](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) finden Sie einen Schnellstart. Legen Sie beim Hinzufügen der Anwendung unbedingt die folgenden beiden Einstellungen auf dem Blatt **Lokale Anwendung hinzufügen** fest:

- **Vorauthentifizierung:** Geben Sie **Azure Active Directory** ein.
- **URLs übersetzen in Anwendungstext:** Wählen Sie **Ja** aus.

Diese beiden Einstellungen sind erforderlich, damit die Anwendung mit MCAS funktioniert.

## <a name="test-the-on-premises-application"></a>Testen der lokalen Anwendung

Führen Sie nach dem Hinzufügen Ihrer Anwendung in Azure AD die Schritte unter [Testen der Anwendung](../app-proxy/application-proxy-add-on-premises-application.md#test-the-application) aus, um einen Benutzer für das Testen hinzuzufügen, und testen Sie die Anmeldung. 

## <a name="deploy-conditional-access-app-control"></a>Bereitstellen der App-Steuerung für bedingten Zugriff

Um die Anwendung mit der App-Steuerung für bedingten Zugriff zu konfigurieren, befolgen Sie die Anweisungen unter [Bereitstellen der App-Steuerung für bedingten Zugriff für Azure AD-Apps](/cloud-app-security/proxy-deployment-aad).


## <a name="test-conditional-access-app-control"></a>Testen der App-Steuerung für bedingten Zugriff

Befolgen Sie zum Testen der Bereitstellung von Azure AD-Anwendungen mit der App-Steuerung für bedingten Zugriff die Anweisungen unter [Testen der Bereitstellung für Azure AD-Apps](/cloud-app-security/proxy-deployment-aad).





