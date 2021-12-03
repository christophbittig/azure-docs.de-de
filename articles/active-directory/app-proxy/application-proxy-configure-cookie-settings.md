---
title: Cookieeinstellungen für den Anwendungsproxy – Azure Active Directory
description: Azure Active Directory (Azure AD) umfasst Zugriffs- und Sitzungscookies für den Zugriff auf lokale Anwendungen über den Anwendungsproxy. In diesem Artikel erfahren Sie, wie Sie die Cookieeinstellungen verwenden und konfigurieren.
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
ms.openlocfilehash: b621616790394df54d312af86719b24ff90a59c6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068149"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Cookieeinstellungen für den Zugriff auf lokale Anwendungen in Azure Active Directory

Azure Active Directory (Azure AD) umfasst Zugriffs- und Sitzungscookies für den Zugriff auf lokale Anwendungen über den Anwendungsproxy. Finden Sie heraus, wie die Cookieeinstellungen für den Anwendungsproxy verwendet werden. 

## <a name="what-are-the-cookie-settings"></a>Wie lauten die Cookieeinstellungen?

Der [Anwendungsproxy](application-proxy.md) verwendet die folgenden Einstellungen für Zugriffs- und Sitzungscookies.

| Cookieeinstellung | Standard | BESCHREIBUNG | Empfehlungen |
| -------------- | ------- | ----------- | --------------- |
| Nur-HTTP-Cookie verwenden | **Nein** | Bei Festlegung von **Ja** kann der Anwendungsproxy das HTTPOnly-Flag in HTTP-Antwortheadern einschließen. Dieses Flag bietet zusätzliche Sicherheitsvorteile, z.B. wird das Kopieren oder Ändern von Cookies über ein clientseitiges Scripting (CSS) verhindert.<br></br><br></br>Vor Unterstützung der Einstellung „HTTP-Only“ verschlüsselte der Anwendungsproxy Cookies und übermittelte sie über einen sicheren TLS-Kanal, um Schutz vor Änderungen bereitzustellen. | Verwenden Sie **Ja**, um von den zusätzlichen Sicherheitsvorteilen zu profitieren.<br></br><br></br>Verwenden Sie **Nein** für Clients oder Benutzer-Agents, die keinen Zugriff auf das Sitzungscookie benötigen. Verwenden Sie beispielsweise **Nein** für einen RDP- oder MTSC-Client, der über den Anwendungsproxy eine Verbindung mit einem Remotedesktop-Gatewayserver herstellt.|
| Sicheres Cookie verwenden | **Nein** | Bei Festlegung von **Ja** kann der Anwendungsproxy das Flag „Secure“ in HTTP-Antwortheadern einschließen. Sichere Cookies erhöhen die Sicherheit, weil sie über einen TLS-gesicherten Kanal wie z.B. HTTPS übertragen werden. Dadurch wird verhindert, dass Cookies aufgrund der Übertragung in Klartext durch Unbefugte eingesehen werden können. | Verwenden Sie **Ja**, um von den zusätzlichen Sicherheitsvorteilen zu profitieren.|
| Beständiges Cookie verwenden | **Nein** | Bei Festlegung auf **Ja** kann der Anwendungsproxy Zugriffscookies so festlegen, dass sie beim Schließen des Webbrowsers nicht ablaufen. Cookies bleiben erhalten, bis das Zugriffstoken abläuft oder der Benutzer die persistenten Cookies manuell löscht. | Verwenden Sie **Nein** wegen des Sicherheitsrisikos, das mit der Beibehaltung der Authentifizierung von Benutzern verbunden ist.<br></br><br></br>Wir empfehlen, **Ja** nur für ältere Anwendungen zu verwenden, die Cookies nicht zwischen Prozessen gemeinsam verwenden können. Es ist besser, Ihre Anwendung zur gemeinsamen Nutzung von Cookies zwischen Prozessen zu aktualisieren, als beständige Cookies zu verwenden. Angenommen, Sie benötigen beständige Cookies, damit ein Benutzer von einer SharePoint-Website aus Office-Dokumente in der Explorer-Ansicht öffnen kann. Ohne beständige Cookies kommt es bei diesem Vorgang möglicherweise zu Fehlern, wenn die Zugriffscookies nicht vom Browser, dem Explorer-Prozess und dem Office-Prozess gemeinsam verwendet werden. |

## <a name="samesite-cookies"></a>SameSite-Cookies
Ab Version 80 des Chrome-Browsers und letztendlich in Browsern, die Chromium nutzen, werden Cookies, die das [SameSite](https://web.dev/samesite-cookies-explained)-Attribut nicht angeben, so behandelt, als wären sie auf **SameSite=Lax** festgelegt. Das „SameSite“-Attribut deklariert, wie Cookies auf einen SameSite-Kontext beschränkt werden sollen. Wenn es auf „Lax“ festgelegt ist, wird das Cookie nur mit Anforderungen der gleichen Website und mit Navigation auf oberster Ebene gesendet. Der Anwendungsproxy erfordert jedoch, dass diese Cookies im Drittanbieter-Kontext beibehalten werden, damit die Benutzer während ihrer Sitzung ordnungsgemäß angemeldet bleiben. Aus diesem Grund aktualisieren wir die Zugriffs- und Sitzungscookies des Anwendungsproxys, um negative Auswirkungen auf diese Änderung zu vermeiden. Die Aktualisierungen umfassen Folgendes:

* Das **SameSite**-Attribut wird auf **None** festgelegt. Dadurch können die Zugriffs- und Sitzungscookies des Anwendungsproxys ordnungsgemäß im Drittanbieter-Kontext gesendet werden.
* Für die Einstellung **Sicheres Cookie verwenden** wird **Ja** als Standardeinstellung festgelegt. Chrome erfordert auch, das die Cookies das „Secure“-Flag angeben. Andernfalls werden sie zurückgewiesen. Diese Änderung gilt für alle über den Anwendungsproxy veröffentlichten vorhandenen Anwendungen. Beachten Sie, dass Anwendungsproxy-Zugriffscookies immer auf „Secure“ festgelegt sind und nur über HTTPS übertragen werden. Diese Änderung gilt nur für die Sitzungscookies.

Diese Änderungen an den Anwendungsproxycookies werden im Laufe der nächsten Wochen vor der Veröffentlichung von Chrome 80 eingeführt.

Wenn Ihre Back-End-Anwendung Cookies enthält, die in einem Drittanbieter-Kontext verfügbar sein müssen, ist es außerdem erforderlich, dies explizit anzugeben, indem Sie Ihre Anwendung so ändern, dass „SameSite=None“ für diese Cookies verwendet wird. Der Anwendungsproxy übersetzt den Set-Cookie-Header in seine URLs und berücksichtigt die Einstellungen für diese Cookies, die von der Back-End-Anwendung festgelegt wurden.



## <a name="set-the-cookie-settings---azure-portal"></a>Festlegen der Cookieeinstellungen – Azure-Portal
So legen Sie die Cookieeinstellungen im Azure-Portal fest

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Navigieren Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Alle Anwendungen**.
3. Wählen Sie die Anwendung aus, für die Sie eine Cookieeinstellung aktivieren möchten.
4. Klicken Sie auf **Anwendungsproxy**.
5. Legen Sie unter **Zusätzliche Einstellungen** die Cookieeinstellung auf **Ja** oder **Nein** fest.
6. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**. 

## <a name="view-current-cookie-settings---powershell"></a>Anzeigen der aktuellen Cookieeinstellungen – PowerShell

Um die aktuellen Cookieeinstellungen für die Anwendung anzuzeigen, verwenden Sie diesen PowerShell-Befehl:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Festlegen der Cookieeinstellungen – PowerShell

In den folgenden PowerShell-Befehlen ist ```<ObjectId>``` die ObjectId der Anwendung. 

**Nur-HTTP-Cookie** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Sicheres Cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Beständige Cookies**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
