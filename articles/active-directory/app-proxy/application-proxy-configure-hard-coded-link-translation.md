---
title: Verschieben von Links und URLs – Azure Active Directory-Anwendungsproxy
description: Erfahren Sie, wie Sie hartcodierte Links für Apps, die mit dem Azure Active Directory-Anwendungsproxy veröffentlicht wurden, umleiten.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 08782f57ecf82924f48e25a1e8f1a62a1a94264b
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613831"
---
# <a name="redirect-hard-coded-links-for-apps-published-with-azure-active-directory-application-proxy"></a>Umleiten von hartcodierte Links für Apps, die mit dem Azure Active Directory-Anwendungsproxy veröffentlicht wurden.

Azure AD-Anwendungsproxy stellt Ihre lokalen Apps Benutzern zur Verfügung, die remote oder an eigenen Geräten arbeiten. Einige Apps wurden jedoch mit lokalen in HTML eingebetteten Links entwickelt. Diese Links funktionieren allerdings nicht ordnungsgemäß, wenn die App remote genutzt wird. Wenn mehrere lokale Anwendungen aufeinander zeigen, erwarten Ihre Benutzer, dass die Links auch außerhalb ihrer Büroumgebung weiter funktionieren. 

Die beste Möglichkeit sicherzustellen, dass Links sowohl innerhalb als auch außerhalb des Unternehmensnetzwerks identisch funktionieren, besteht darin, die externen URLs Ihrer Apps entsprechend ihren internen URLs zu konfigurieren. Arbeiten Sie mit [benutzerdefinierten Domänen](application-proxy-configure-custom-domain.md), um Ihre externen URLs so zu konfigurieren, dass sie den Namen Ihrer Unternehmensdomäne anstelle der standardmäßigen Anwendungsproxydomäne aufweisen.


Wenn Sie in Ihrem Mandanten keine benutzerdefinierten Domänen verwenden können, stehen mehrere andere Optionen für die Bereitstellung dieser Funktionalität zur Verfügung. Alle diese Optionen sind auch mit benutzerdefinierten Domänen und untereinander kompatibel, sodass Sie bei Bedarf benutzerdefinierte Domänen und andere Lösungen konfigurieren können.

> [!NOTE]
> Die Linkübersetzung wird für hartcodierte interne URLs, die mit JavaScript generiert wurden, nicht unterstützt.

**Option 1: Verwenden von Microsoft Edge:** Diese Lösung ist nur anwendbar, wenn Sie empfehlen oder festlegen möchten, dass Benutzer über den Browser Microsoft Edge auf die Anwendung zugreifen. Es werden alle veröffentlichten URLs verarbeitet. 

**Option 2: Verwenden der MyApps-Erweiterung:** Für diese Lösung müssen Benutzer eine clientseitige Browsererweiterung installieren. Die Lösung kann aber alle veröffentlichten URLs verarbeiten und in den meisten gängigen Browsern verwendet werden. 

**Option 3: Verwenden der Einstellung für die Linkübersetzung:** Diese Einstellung wird vom Administrator festgelegt und ist für Benutzer nicht sichtbar. Allerdings werden nur URLs in HTML und CSS verarbeitet.   

Mit diesen drei Features funktionieren Ihre Links unabhängig davon, wo sich Ihre Benutzer befinden. Wenn Sie über Apps verfügen, die direkt auf interne Endpunkte oder Ports verweisen, können Sie diese internen URLs den veröffentlichten externen Anwendungsproxy-URLs zuordnen. 

 
> [!NOTE]
> Die letzte Option ist nur für Mandanten vorgesehen, die aus verschiedenen Gründen keine benutzerdefinierten Domänen verwenden können, damit sie über dieselben internen und externen URLs für ihre Apps verfügen. Bevor Sie dieses Feature aktivieren, finden Sie heraus, ob [benutzerdefinierte Domänen in Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md) für Sie funktionieren. 
> 
> Wenn die Anwendung, die Sie mit Linkübersetzung konfigurieren müssen, SharePoint ist, finden Sie unter [Konfigurieren alternativer Zugriffszuordnungen für SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings) ein weiteres Verfahren zum Zuordnen von Links. 

 
### <a name="option-1-microsoft-edge-integration"></a>Option 1: Microsoft Edge-Integration 

Mithilfe von Microsoft Edge können Sie die Anwendung und die Inhalte zusätzlich schützen. Zur Verwendung dieser Lösung müssen Sie empfehlen oder festlegen, dass Benutzer über den Browser Microsoft Edge auf die Anwendung zugreifen. Alle internen mit Anwendungsproxy veröffentlichten URLs werden von Microsoft Edge erkannt und an die entsprechende externe URL umgeleitet. Dadurch wird sichergestellt, dass alle hartcodierten internen URLs funktionieren. Wenn ein Benutzer im Browser direkt die interne URL eingibt, funktioniert diese, auch wenn der Benutzer remote arbeitet.  

Weitere Informationen, einschließlich Informationen zum Konfigurieren dieser Option, finden Sie in der Dokumentation [Verwalten des Webzugriffs mithilfe von Microsoft Edge für iOS und Android mit Microsoft Intune](/mem/intune/apps/manage-microsoft-edge).  

### <a name="option-2-myapps-browser-extension"></a>Option 2: MyApps-Browsererweiterung 

Mit der MyApps-Browsererweiterung werden alle internen mit dem Anwendungsproxy veröffentlichten URLs erkannt und an die entsprechende externe URL umgeleitet. Dadurch wird sichergestellt, dass alle hartcodierten internen URLs funktionieren. Wenn ein Benutzer auf der Adressleiste des Browsers direkt die interne URL eingibt, funktioniert diese, auch wenn der Benutzer remote arbeitet.  

Zur Verwendung dieses Features muss der Benutzer die Erweiterung herunterladen und angemeldet sein. Administratoren oder Benutzer müssen keine weitere Konfiguration vornehmen. 

Weitere Informationen, auch zum Konfigurieren dieser Option, finden Sie in der Dokumentation [MyApps-Browsererweiterung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510#download-and-install-the-my-apps-secure-sign-in-extension).

> [!NOTE]
> Die MyApps-Browsererweiterung unterstützt keine Linkübersetzung für Platzhalter-URLs.

### <a name="option-3-link-translation-setting"></a>Option 3: Einstellung für die Linkübersetzung 

Wenn die Linkübersetzung aktiviert ist, durchsucht der Anwendungsproxydienst HTML- und CSS-Tags nach veröffentlichten internen Links und übersetzt sie, sodass bei Benutzern keine Unterbrechung stattfindet. Die MyApps-Browsererweiterung wird gegenüber der Einstellung für die Linkübersetzung bevorzugt verwendet, da sie dem Benutzer eine leistungsstärkere Erfahrung bietet.

> [!NOTE]
> Wenn Sie Option 2 oder 3 verwenden, sollte jeweils nur eine dieser Optionen aktiviert werden.

## <a name="how-link-translation-works"></a>Funktionsweise der Linkübersetzung

Wenn der Proxyserver nach der Authentifizierung die Anwendungsdaten an den Benutzer übergibt, scannt Anwendungsproxy die Anwendung auf hartcodierte Links und ersetzt diese durch die jeweiligen veröffentlichten externen URLs.

Der Anwendungsproxy geht davon aus, dass Anwendungen mit UTF-8 codiert sind. Wenn das nicht der Fall ist, geben Sie den Codierungstyp in einem HTTP-Antwortheader wie z. B. `Content-Type:text/html;charset=utf-8` an.

### <a name="which-links-are-affected"></a>Welche Links sind betroffen?

Das Linkübersetzungsfeature sucht nur nach Links, die sich in Codetags im Text einer App befinden. Anwendungsproxy weist ein separates Feature für die Übersetzung von Cookies oder URLs in Headern auf. 

Es gibt zwei verbreitete Typen von internen Links in lokalen Anwendungen:

- **Relative interne Links**, die auf eine freigegebene Ressource in einer lokalen Dateistruktur wie `/claims/claims.html` zeigen. Diese Links funktionieren automatisch in Apps, die über den Anwendungsproxy veröffentlicht werden, und funktionieren auch weiterhin mit oder ohne Linkübersetzung. 
- **Hartcodierte interne Links** zu anderen lokalen Apps wie `http://expenses` oder veröffentlichten Dateien wie `http://expenses/logo.jpg`. Das Linkübersetzungsfeature funktioniert für hartcodierte interne Links und ändert diese, sodass sie auf die externen URLs zeigen, die für Remotebenutzer gelten.

Die vollständige Liste der HTML-Codetags, für die der Anwendungsproxy die Übersetzung von Links unterstützt, enthält Folgendes:
* a (href)
* audio (src)
* base (href)
* button (formaction)
* div (data-background, style, data-src)
* embed (src)
* form (action)
* frame (src)
* head (profile)
* html (manifest)
* iframe (longdesc, src)
* img (longdesc, src)
* input (formaction, src, value)
* link (href)
* menuitem (icon)
* meta (content)
* object (archive, data, codebase)
* script (src)
* source (src)
* track (src)
* video (src, poster)

Zusätzlich wird das URL-Attribut innerhalb von CSS übersetzt.

### <a name="how-do-apps-link-to-each-other"></a>Wie funktionieren Links zwischen Apps?

Die Linkübersetzung ist für jede Anwendung aktiviert, damit Sie die Kontrolle über die Benutzererfahrung für die einzelnen Apps behalten. Aktivieren Sie die Linkübersetzung für eine App, wenn die Links *aus* dieser App übersetzt werden sollen, nicht Links *auf* diese App. 

Beispiel: Angenommen, drei Ihrer Anwendungen werden über Anwendungsproxy veröffentlicht und verweisen mit Links aufeinander: „Vorteile“, „Ausgaben“ und „Reisen“. Es gibt eine vierte App, „Feedback“, die nicht über Anwendungsproxy veröffentlicht wird.

Wenn Sie die Linkübersetzung für Vorteile aktivieren, werden die Links zu „Ausgaben“ und „Reisen“ an die externen URLs für diese Apps umgeleitet, der Link zu „Feedback“ wird jedoch nicht umgeleitet, da keine externe URL vorhanden ist. Links von „Ausgaben“ und „Reisen“ zurück zu „Vorteile“ funktionieren nicht, da die Linkübersetzung für diese beiden Apps nicht aktiviert wurde.

![Links von „Vorteile“ zu anderen Apps, wenn Linkübersetzung aktiviert ist](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Welche Links werden nicht übersetzt?

Zum Verbessern der Leistung und Sicherheit werden einige Links nicht übersetzt:

- Links außerhalb von Codetags 
- Nicht in HTML- oder CSS-Tags enthaltene Links 
- Links im URL-codierten Format
- Interne Links, die von anderen Programmen geöffnet wurden Links, die über E-Mail oder Sofortnachrichten gesendet wurden oder in anderen Dokumenten enthalten sind, werden nicht übersetzt. Den Benutzern muss bekannt sein, dass sie zur externen URL navigieren müssen.

Wenn Sie eines dieser beiden Szenarien unterstützen müssen, können Sie dieselben internen und externen URLs anstelle der Linkübersetzung verwenden.  

## <a name="enable-link-translation"></a>Aktivieren der Linkübersetzung

Die ersten Schritte mit der Linkübersetzung sind einfache Klicks auf Schaltflächen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) als Administrator an.
2. Wechseln Sie zu **Azure Active Directory** > **Unternehmensanwendungen** > **Alle Anwendungen**, wählen Sie die App, die Sie verwaltenden möchten, und dann **Anwendungsproxy** aus.
3. Legen Sie **URLs im Hauptteil der Anwendung übersetzen** auf **Ja** fest.

   ![Wählen Sie „Ja“ aus, um die URLs im Hauptteil der Anwendung zu übersetzen.](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Klicken Sie zum Übernehmen der Änderungen auf **Speichern**.

Wenn Ihre Benutzer auf diese Anwendung zugreifen, scannt der Proxy nun automatisch auf interne URLs, die über Anwendungsproxy für Ihren Mandanten veröffentlicht wurden.

## <a name="next-steps"></a>Nächste Schritte
[Verwenden von benutzerdefinierten Domänen mit Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md), damit diese dieselbe interne und externe URL aufweisen

[Konfigurieren alternativer Zugriffszuordnungen für SharePoint 2013](/SharePoint/administration/configure-alternate-access-mappings)