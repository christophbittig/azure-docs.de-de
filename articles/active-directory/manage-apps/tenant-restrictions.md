---
title: Verwalten des Zugriffs auf SaaS-Apps mithilfe von Mandanteneinschränkungen
description: Hier erfahren Sie, wie Sie mithilfe von Mandanteneinschränkungen auf der Grundlage des verwendeten Azure AD-Mandanten steuern, welcher Benutzer auf Apps zugreifen kann.
titleSuffix: Azure AD
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 7/30/2021
ms.author: davidmu
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3dc75a0c9d5f47d7f415a5df29398e8d407d7cf
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546537"
---
# <a name="restrict-access-to-a-tenant"></a>Beschränken des Zugriffs auf einen Mandanten

Große Organisationen mit hohen Sicherheitsanforderungen, die Clouddienste wie etwa Microsoft 365 nutzen möchten, müssen sich darauf verlassen können, dass ihre Benutzer nur auf genehmigte Ressourcen zugreifen können. Zur Steuerung des Zugriffs schränken Unternehmen in der Regel Domänennamen oder IP-Adressen ein. In einer Welt, in der SaaS-Apps (Software-as-a-Service) in einer öffentlichen Cloud gehostet und unter freigegebenen Domänennamen wie [outlook.office.com](https://outlook.office.com/) und [login.microsoftonline.com](https://login.microsoftonline.com/) ausgeführt werden, ist diese Vorgehensweise jedoch zum Scheitern verurteilt. Die Blockierung der Adressen würde dazu führen, dass Benutzer gar nicht mehr über das Web auf Outlook zugreifen können, anstatt lediglich ihren Zugriff auf genehmigte Identitäten und Ressourcen zu beschränken.

Zur Lösung dieses Problems bietet Azure Active Directory (Azure AD) ein Feature namens Mandanteneinschränkungen. Mithilfe von Mandanteneinschränkungen können Organisationen den Zugriff auf SaaS-Cloudanwendungen in Abhängigkeit von dem Azure AD-Mandanten steuern, den die Anwendungen für einmaliges Anmelden verwenden. So können Sie beispielsweise den Zugriff auf die Microsoft 365-Anwendungen Ihrer Organisation ermöglichen und den Zugriff auf Instanzen der gleichen Anwendungen anderer Organisationen verhindern.  

Mit Mandanteneinschränkungen können Organisationen eine Liste mit Mandanten angeben, für die ihre Benutzer zugriffsberechtigt sind. Azure AD gewährt daraufhin nur Zugriff auf die zugelassenen Mandanten.

In diesem Artikel liegt der Schwerpunkt auf Mandanteneinschränkungen für Microsoft 365, aber das Feature schützt alle Apps, die den Benutzer zum einmaligen Anmelden an Azure AD senden. Falls Sie SaaS-Apps mit einem Azure AD-Mandanten verwenden, der nicht dem von Microsoft 365 verwendeten Mandanten entspricht, müssen Sie sicherstellen, dass alle erforderlichen Mandanten zugelassen sind (z. B. in B2B Collaboration-Szenarien). Weitere Informationen zu SaaS-Cloud-Apps finden Sie im [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

Darüber hinaus unterstützt das Mandanteneinschränkungsfeature nun [das Blockieren der Verwendung aller Microsoft-Consumeranwendungen](#blocking-consumer-applications) (MSA-Apps), wie z. B. OneDrive, Hotmail und Xbox.com.  Dieses verwendet einen separaten Header zum `login.live.com`-Endpunkt und wird am Ende des Dokuments ausführlich erläutert.

## <a name="how-it-works"></a>Funktionsweise

Die Lösung umfasst folgende Komponenten:

1. **Azure AD**: Wenn der `Restrict-Access-To-Tenants: <permitted tenant list>`-Header vorhanden ist, stellt Azure AD nur Sicherheitstoken für die zugelassenen Mandanten aus.

2. **Lokale Proxyserver-Infrastruktur**: Bei dieser Infrastruktur handelt es sich um ein Proxygerät, das zur TLS-Überprüfung (Transport Layer Security) geeignet ist. Sie müssen den Proxy so konfigurieren, dass er den Header mit der Liste der zulässigen Mandanten in den für Azure AD bestimmten Datenverkehr einfügt.

3. **Clientsoftware**: Zur Unterstützung von Mandanteneinschränkungen muss Clientsoftware Token direkt von Azure AD anfordern, damit Datenverkehr von der Proxyinfrastruktur abgefangen werden kann. Browserbasierte Microsoft 365-Anwendungen unterstützen derzeit Mandanteneinschränkungen, ebenso wie Office-Clients, die eine moderne Authentifizierung verwenden (wie OAuth 2.0).

4. **Moderne Authentifizierung**: Clouddienste müssen eine moderne Authentifizierung verwenden, um Mandanteneinschränkungen nutzen und den Zugriff auf nicht zugelassene Mandanten blockieren zu können. Microsoft 365-Clouddienste müssen so konfiguriert werden, dass sie standardmäßig moderne Authentifizierungsprotokolle verwenden. Aktuelle Informationen zur Unterstützung von moderner Authentifizierung in Microsoft 365 finden Sie unter [Office 2013: Public Preview für moderne Authentifizierung angekündigt](/microsoft-365/enterprise/modern-auth-for-office-2013-and-2016).

Das folgende Diagramm veranschaulicht den allgemeinen Datenverkehrsfluss. Mandanteneinschränkungen erfordern die TLS-Überprüfung nur bei Datenverkehr für Azure AD, nicht bei Datenverkehr für die Microsoft 365-Clouddienste. Diese Unterscheidung ist wichtig, da der durch die Authentifizierung bedingte Datenverkehr für Azure AD in der Regel erheblich geringer ausfällt als der Datenverkehr für SaaS-Anwendungen wie Exchange Online und SharePoint Online.

![Datenverkehrsfluss für Mandanteneinschränkungen – Diagramm](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Einrichten von Mandanteneinschränkungen

Für die Verwendung von Mandanteneinschränkungen sind zunächst zwei Schritte erforderlich. Stellen Sie zunächst sicher, dass Ihre Clients eine Verbindung mit den richtigen Adressen herstellen können. Konfigurieren Sie dann Ihre Proxyinfrastruktur.

### <a name="urls-and-ip-addresses"></a>URLs und IP-Adressen

Um Mandanteneinschränkungen verwenden zu können, müssen Ihre Clients für die Authentifizierung eine Verbindung mit den folgenden Azure AD-URLs herstellen können: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/) und [login.windows.net](https://login.windows.net/). Für den Zugriff auf Office 365 müssen Ihre Clients außerdem eine Verbindung mit den unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) definierten vollqualifizierten Domänennamen (FQDNs), URLs und IP-Adressen herstellen können.

### <a name="proxy-configuration-and-requirements"></a>Proxykonfiguration und Anforderungen

Die folgende Konfiguration ist erforderlich, um Mandanteneinschränkungen über Ihre Proxyinfrastruktur zu aktivieren. Hierbei handelt es sich um einen allgemeinen Leitfaden. Spezifische Implementierungsschritte finden Sie in der Dokumentation Ihres Proxyanbieters.

#### <a name="prerequisites"></a>Voraussetzungen

- Der Proxy muss TLS abfangen, HTTP-Header einfügen und Ziele unter Verwendung von FQDNs/URLs filtern können.

- Clients müssen der Zertifikatkette vertrauen, die vom Proxy für die TLS-Kommunikation präsentiert wird. Wenn also z. B. Zertifikate einer internen [PKI (Public Key-Infrastruktur)](/windows/desktop/seccertenroll/public-key-infrastructure) verwendet werden, muss dem Zertifikat der internen ausstellenden Stammzertifizierungsstelle vertraut werden.

- Zur Verwendung von Mandanteneinschränkungen sind Azure AD Premium 1-Lizenzen erforderlich.

#### <a name="configuration"></a>Konfiguration

Fügen Sie für jede ausgehende, an „login.microsoftonline.com“, „login.microsoft.com“ oder „login.windows.net“ gerichtete Anforderung zwei HTTP-Header ein: *Restrict-Access-To-Tenants* und *Restrict-Access-Context*.

> [!NOTE]
> Fügen Sie in der Proxykonfiguration unter `*.login.microsoftonline.com` keine Unterdomänen ein. Dadurch wird „device.login.microsoftonline.com“ eingefügt, was möglicherweise die Authentifizierung mit Clientzertifikaten beeinträchtigt, die für die Geräteregistrierung und den gerätebasierten bedingten Zugriff verwendet werden. Konfigurieren Sie den Proxyserver so, dass „device.login.microsoftonline.com“ von TLSI (TLS break and inspect) und Header Injection ausgeschlossen ist.

Die Header müssen folgende Elemente enthalten:

- *Restrict-Access-To-Tenants:* Verwenden Sie den Wert \<permitted tenant list\>. Dabei handelt es sich um eine durch Kommas getrennte Liste mit Mandanten, auf die Benutzer zugreifen können sollen. Der Mandant in dieser Liste kann mithilfe jeder bei einem Mandanten registrierten Domäne sowie mit der Verzeichnis-ID selbst identifiziert werden. In einem Beispiel aller drei Möglichkeiten, einen Mandanten zu beschreiben, sieht das Name-Wert-Paar, um Contoso, Fabrikam und Microsoft zuzulassen, folgendermaßen aus: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`.

- *Restrict-Access-Context:* Verwenden Sie einen Wert für eine einzelne Verzeichnis-ID, um zu deklarieren, welcher Mandant die Mandanteneinschränkungen festlegt. Wenn Sie z. B. Contoso als den Mandanten deklarieren möchten, der die Mandanteneinschränkungsrichtlinie festlegt, sieht das Name-Wert-Paar wie folgt aus: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`. Sie *müssen* hier Ihre eigene Verzeichnis-ID verwenden, um Protokolle für diese Authentifizierungen abzurufen. Wenn Sie eine andere Verzeichnis-ID als Ihre eigene verwenden, *werden diese Anmeldeprotokolle im Mandanten einer anderen Person angezeigt*, wobei alle persönlichen Informationen entfernt werden. Weitere Informationen finden Sie unter [Administratorerfahrung](#admin-experience).

> [!TIP]
> Ihre Verzeichnis-ID finden Sie im [Azure Active Directory-Portal](https://aad.portal.azure.com/). Melden Sie sich als Administrator an, und wählen Sie **Azure Active Directory** und anschließend **Eigenschaften** aus.
>
> Zur Überprüfung, ob eine Verzeichnis-ID oder ein Domänenname sich auf denselben Mandanten beziehen, verwenden Sie diese ID oder Domäne anstelle von \<tenant\> in der folgenden URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration`.  Wenn die Ergebnisse mit der Domäne und der ID identisch sind, beziehen sie sich auf denselben Mandanten.

Um zu verhindern, dass Benutzer ihre eigenen HTTP-Header mit nicht genehmigten Mandanten einfügen, muss der Proxy den *Restrict-Access-To-Tenants*-Header ersetzen, falls er in der eingehenden Anforderung bereits vorhanden ist.

Für Clients muss bei allen Anforderungen an „login.microsoftonline.com“, „login.microsoft.com“ oder „login.windows.net“ die Verwendung des Proxys erzwungen werden. Wenn Clients also z. B. mithilfe von PAC-Dateien zur Verwendung des Proxys angewiesen werden, sollten Endbenutzer die PAC-Dateien nicht bearbeiten oder deaktivieren können.

## <a name="the-user-experience"></a>Die Benutzererfahrung

In diesem Abschnitt wird die Erfahrung für Endbenutzer und Administratoren erläutert.

### <a name="end-user-experience"></a>Endbenutzererfahrung

Ein Beispielbenutzer im Netzwerk von Contoso versucht, online auf die Fabrikam-Instanz einer freigegebenen SaaS-Anwendung wie Outlook zuzugreifen. Wenn Fabrikam ein nicht zugelassener Mandant für die Contoso-Instanz ist, wird dem Benutzer eine Meldung zur Zugriffsverweigerung angezeigt. Diese besagt, dass Sie versuchen, auf eine Ressource zuzugreifen, die zu einer Organisation gehört, die von Ihrer IT-Abteilung nicht genehmigt wurde.

![Fehlermeldung zur Einschränkung von Mandanten vom April 2021](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Administratorerfahrung

Die Konfiguration der Mandanteneinschränkungen erfolgt zwar in der Proxyinfrastruktur des Unternehmens, Administratoren können jedoch über das Azure-Portal direkt auf die Mandanteneinschränkungsberichte zugreifen. So zeigen Sie die Berichte an

1. Melden Sie sich beim [Azure Active Directory-Portal](https://aad.portal.azure.com/) an. Das Dashboard **Azure Active Directory Admin Center** wird angezeigt.

2. Wählen Sie im linken Bereich **Azure Active Directory** aus. Die Übersichtsseite „Azure Active Directory Admin Center“ wird angezeigt.

3. Wählen Sie auf der Übersichtsseite **Mandanteneinschränkungen** aus.

Der Administrator für den Mandanten, der als Restricted-Access-Context-Mandant angegeben ist, kann sich anhand dieses Berichts über Anmeldungen informieren, die aufgrund der Mandanteneinschränkungsrichtlinie blockiert wurden (einschließlich der jeweils verwendeten Identität und der Zielverzeichnis-ID). Anmeldungen sind enthalten, wenn es sich beim Mandanten, der die Einschränkung festlegt, entweder um den Benutzermandanten oder den Ressourcenmandanten für die Anmeldung handelt.

Der Bericht enthält möglicherweise eingeschränkte Informationen (z. B. die Zielverzeichnis-ID), wenn sich ein Benutzer anmeldet, der sich in einem anderen Mandanten als dem Restricted-Access-Context-Mandanten befindet. In diesem Fall werden benutzerbezogene Informationen wie Name und Benutzerprinzipalname maskiert, um Benutzerdaten in anderen Mandanten zu schützen (je nachdem "{PII Removed}@domain.com" oder 00000000-0000-0000-0000-000000000000 anstelle von Benutzernamen und Objekt-IDs).

Genau wie bei anderen Berichten im Azure-Portal können Sie auch hier mithilfe von Filtern den gewünschten Umfang des Berichts angeben. Der Bericht kann nach einem bestimmten Zeitintervall, einem Benutzer, einer Anwendung, einem Client oder einem Status gefiltert werden. Wenn Sie die Schaltfläche **Spalten** auswählen, können Sie Daten mit einer beliebigen Kombination der folgenden Felder anzeigen:

- **Benutzer**: In diesem Feld können personenbezogene Informationen entfernt werden, dieses wird auf `00000000-0000-0000-0000-000000000000` festgelegt.
- **Anwendung**
- **Status**
- **Date**
- **Datum (UTC)** (UTC bedeutet Coordinated Universal Time)
- **IP-Adresse**
- **Client**
- **Benutzername** - In diesem Feld können personenbezogene Informationen entfernt werden, dieses wird auf festgelegt`{PII Removed}@domain.com`
- **Location**
- **Zielmandanten-ID**

## <a name="microsoft-365-support"></a>Microsoft 365-Support

Zur vollständigen Unterstützung von Mandanteneinschränkungen müssen Microsoft 365-Anwendungen zwei Kriterien erfüllen:

1. Der verwendete Client muss die moderne Authentifizierung unterstützen.
2. Die moderne Authentifizierung muss als Standardauthentifizierungsprotokoll für den Clouddienst aktiviert sein.

Aktuelle Informationen zu den Office-Clients, die die moderne Authentifizierung unterstützen, finden Sie unter [Updated Office 365 modern authentication](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) (Aktualisierte moderne Authentifizierung für Office 365). Diese Seite enthält außerdem Links zu Schritten, mit denen Sie die moderne Authentifizierung für bestimmte Exchange Online- und Skype for Business Online-Mandanten aktivieren können. SharePoint Online ermöglicht bereits standardmäßig die moderne Authentifizierung. Teams unterstützt nur die moderne Autorisierung, nicht aber die Legacyautorisierung, sodass dieses Umgehungsszenario für Teams nicht zutrifft. 

Browserbasierte Microsoft 365-Anwendungen (das Office-Portal, Yammer, SharePoint-Websites, Outlook im Web und andere) unterstützen derzeit Mandanteneinschränkungen. Thick Clients (Outlook, Skype for Business, Word, Excel, PowerPoint und andere) können Mandanteneinschränkungen nur bei Verwendung moderner Authentifizierung erzwingen.  

Outlook- und Skype for Business-Clients, die die moderne Authentifizierung unterstützen, können für Mandanten, bei denen keine moderne Authentifizierung aktiviert ist, unter Umständen weiterhin ältere Protokolle verwenden und dadurch die Mandanteneinschränkungen umgehen. Anwendungen, die ältere Protokolle verwenden, werden unter Umständen durch Mandanteneinschränkungen blockiert, wenn sie bei der Authentifizierung „login.microsoftonline.com“, „login.microsoft.com“ oder „login.windows.net“ kontaktieren.

Bei Outlook unter Windows können Kunden ggf. Einschränkungen implementieren, die verhindern, dass Endbenutzer ihren Profilen nicht genehmigte E-Mail-Konten hinzufügen. Informationen hierzu finden Sie beispielsweise unter der Gruppenrichtlinieneinstellung [Das Hinzufügen nicht standardmäßiger Exchange-Konten verhindern](https://gpsearch.azurewebsites.net/default.aspx?ref=1).

## <a name="testing"></a>Testen

Wenn Sie die Mandanteneinschränkungen ausprobieren möchten, bevor Sie sie für Ihre gesamte Organisation implementieren, können Sie entweder einen hostbasierten Ansatz mit einem Tool wie Fiddler oder einen gestaffelten Rollout von Proxyeinstellungen verwenden.

### <a name="fiddler-for-a-host-based-approach"></a>Fiddler für einen hostbasierten Ansatz

Fiddler ist ein kostenloser Web Debugging Proxy, mit dem Sie HTTP/HTTPS-Datenverkehr erfassen und modifizieren können, um beispielsweise HTTP-Header einzufügen. Gehen Sie wie folgt vor, um Fiddler zum Testen von Mandanteneinschränkungen zu konfigurieren:

1. [Laden Sie Fiddler herunter, und installieren Sie es.](https://www.telerik.com/fiddler)

2. Konfigurieren Sie Fiddler wie in dessen [Hilfedokumentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS) beschrieben für die Entschlüsselung von HTTPS-Datenverkehr.

3. Konfigurieren Sie Fiddler mithilfe von benutzerdefinierten Regeln so, dass die Header *Restrict-Access-To-Tenants* und *Restrict-Access-Context* eingefügt werden:

   1. Wählen Sie im Fiddler Web Debugger-Tool das Menü **Rules** (Regeln) und anschließend **Customize Rules...** (Regeln anpassen...) aus, um die Datei „CustomRules“ zu öffnen.

   2. Fügen Sie am Anfang der `OnBeforeRequest`-Funktion die folgenden Zeilen hinzu: Ersetzen Sie \<List of tenant identifiers\> durch eine bei Ihrem Mandanten registrierte Domäne (z. B. `contoso.onmicrosoft.com`). Ersetzen Sie \<directory ID\> durch den Azure AD-GUID Ihres Mandanten.  Sie **müssen** den richtigen GUID-Bezeichner einschließen, damit die Protokolle in Ihrem Mandanten angezeigt werden.

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   Falls Sie mehrere Mandanten zulassen möchten, trennen Sie die einzelnen Mandantennamen jeweils durch ein Komma. Beispiel:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Speichern und schließen Sie die Datei „CustomRules“.

Nachdem Sie Fiddler konfiguriert haben, können Sie Datenverkehr erfassen, indem Sie im Menü **File** (Datei) die Option **Capture Traffic** (Datenverkehr erfassen) auswählen.

### <a name="staged-rollout-of-proxy-settings"></a>Gestaffelter Rollout von Proxyeinstellungen

Abhängig von den Funktionen Ihrer Proxyinfrastruktur können Sie unter Umständen einen gestaffelten Rollout der Einstellungen für Ihre Benutzer durchführen. Dabei sollten Sie folgende allgemeine Optionen berücksichtigen:

1. Verwenden Sie PAC-Dateien, um Testbenutzer an eine Testproxyinfrastruktur weiterzuleiten, während normale Benutzer weiterhin die Produktionsproxyinfrastruktur verwenden.
2. Einige Proxyserver unterstützen möglicherweise verschiedene Konfigurationen mit Gruppen.

Spezifische Details finden Sie in der Dokumentation Ihres Proxyservers.

## <a name="blocking-consumer-applications"></a>Blockieren von Consumer-Anwendungen

Anwendungen von Microsoft, die sowohl Consumerkonten als auch Organisationskonten unterstützen (z. B. [OneDrive](https://onedrive.live.com/) oder [Microsoft Learn](/learn/)), können manchmal auf derselben URL gehostet werden.  Dies bedeutet, dass Benutzer, die für Arbeitszwecke auf diese URL zugreifen müssen, auch für den persönlichen Gebrauch darauf zugreifen können. Dies ist nach Ihren Betriebsrichtlinien möglicherweise nicht zulässig.

Einige Organisationen versuchen, das Problem zu beheben, indem sie `login.live.com` blockieren, um persönliche Konten für die Authentifizierung zu blockieren.  Dies hat mehrere Nachteile:

1. Durch die Blockierung von `login.live.com` wird die Verwendung persönlicher Konten in B2B-Gastszenarien blockiert, was für Besucher und die Zusammenarbeit nachteilig sein kann.
1. [Autopilot erfordert die Verwendung von `login.live.com`](/mem/autopilot/networking-requirements) zur Bereitstellung. Bei Intune- und Autopilot-Szenarien können Fehler auftreten, wenn `login.live.com` blockiert ist.
1. Organisationstelemetrie und Windows-Updates, die vom login.live.com-Dienst für Geräte-IDs abhängig sind, [funktionieren nicht mehr](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are).

### <a name="configuration-for-consumer-apps"></a>Konfiguration für Consumer-Apps

Während der `Restrict-Access-To-Tenants`Header als Positivliste fungiert, weist die Blockierung des Microsoft-Kontos (MSA) die Microsoft-Kontoplattform an, die Anmeldung von Benutzern bei Consumeranwendungen zu verweigern. Um dieses Signal zu senden, wird der `sec-Restrict-Tenant-Access-Policy`-Header mithilfe desselben Unternehmensproxys oder derselben Firewall wie [oben](#proxy-configuration-and-requirements) in den Datenverkehr zu `login.live.com` eingefügt. Der Wert des Headers muss `restrict-msa` sein. Wenn der Header vorhanden ist und eine Consumer-App versucht, einen Benutzer direkt anzumelden, wird diese Anmeldung blockiert.

Derzeit wird die Authentifizierung bei Consumeranwendungen nicht in den [Administratorprotokollen](#admin-experience) angezeigt, da „login.live.com“ separat von Azure AD gehostet wird.

### <a name="what-the-header-does-and-does-not-block"></a>Was der Header blockiert und was nicht

Die `restrict-msa`-Richtlinie blockiert die Verwendung von Consumeranwendungen, lässt jedoch verschiedene andere Arten von Datenverkehr und Authentifizierung zu:

1. Benutzerloser Datenverkehr für Geräte.  Dies schließt den Datenverkehr für Autopilot, Windows Update und Organisationstelemetrie ein.
1. B2B-Authentifizierung von Consumerkonten. Benutzer mit Microsoft-Konten, die [zur Zusammenarbeit mit einem Mandanten eingeladen](../external-identities/redemption-experience.md#invitation-redemption-flow) werden, authentifizieren sich bei „login.live.com“, um auf einen Ressourcenmandanten zuzugreifen.
    1. Dieser Zugriff wird mithilfe des `Restrict-Access-To-Tenants`-Headers gesteuert, um den Zugriff auf diesen Ressourcenmandanten zuzulassen oder zu verweigern.
1. „Pass-Through“-Authentifizierung, die von vielen Azure-Apps und Office.com verwendet wird, wobei Apps Azure AD zum Anmelden von Consumerbenutzern in einem Consumerkontext verwenden.
    1. Dieser Zugriff wird auch mit dem `Restrict-Access-To-Tenants`-Header gesteuert, um den Zugriff auf den speziellen „Pass-through“-Mandanten zuzulassen oder zu verweigern (`f8cdef31-a31e-4b4a-93e4-5f571e91255a`).  Wenn dieser Mandant nicht in der `Restrict-Access-To-Tenants`-Liste der zulässigen Domänen angezeigt wird, wird die Anmeldung von Consumerkonten bei diesen Apps durch Azure AD blockiert.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Updated Office 365 modern authentication](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/) (Aktualisierte moderne Authentifizierung für Office 365).
- Informieren Sie sich über die [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).
