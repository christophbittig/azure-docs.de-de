---
title: Konfigurieren der Herausgeberdomäne einer App | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie die Herausgeberdomäne einer Anwendung konfigurieren, damit Benutzer wissen, wohin ihre Informationen gesendet werden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 06/23/2021
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, zachowd
ms.custom: contperf-fy21q4, aaddev
ms.openlocfilehash: 43fef26f3006120cb62676bfd5675c1e79b849d4
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579970"
---
# <a name="how-to-configure-an-applications-publisher-domain"></a>Gewusst wie: Konfigurieren der Herausgeberdomäne einer Anwendung

Die Herausgeberdomäne einer Anwendung wird für Benutzer in der [Zustimmungsaufforderung der Anwendung](application-consent-experience.md) angezeigt, damit sie wissen, wohin ihre Informationen gesendet werden. Mehrinstanzenfähige Anwendungen, die nach dem 21. Mai 2019 registriert werden und über keine Herausgeberdomäne verfügen, werden als **nicht überprüft** angezeigt. Mehrinstanzenfähige Anwendungen sind Anwendungen, in denen Konten außerhalb eines einzelnen Organisationsverzeichnisses unterstützt werden, z. B. in denen alle Azure AD-Konten oder alle Azure AD-Konten und persönlichen Microsoft-Konten unterstützt werden.

## <a name="new-applications"></a>Neue Anwendungen

Wenn Sie eine neue App registrieren, wird die Herausgeberdomäne der App möglicherweise auf einen Standardwert festgelegt. Der Wert hängt davon ab, wo die App registriert ist, vor allem davon, ob die App in einem Mandanten registriert ist und ob der Mandant über überprüfte Domänen verfügt.

Wenn im Mandanten überprüfte Domänen vorhanden sind, ist die Herausgeberdomäne der App standardmäßig die primäre überprüfte Domäne des Mandanten. Wenn im Mandanten keine überprüften Domänen vorhanden sind (dies ist der Fall, wenn die Anwendung nicht in einem Mandanten registriert ist), wird die Herausgeberdomäne der App auf NULL festgelegt.

In der folgenden Tabelle ist das Standardverhalten des Werts der Herausgeberdomäne zusammengefasst.  

| Überprüfte Domänen im Mandanten | Standardwert der Herausgeberdomäne |
|-------------------------|----------------------------|
| NULL | NULL |
| *.onmicrosoft.com | *.onmicrosoft.com |
| - *.onmicrosoft.com<br/>- domain1.com<br/>- domain2.com (primär) | domain2.com |

Wenn die Herausgeberdomäne einer mehrinstanzenfähigen Anwendung nicht festgelegt ist oder wenn sie auf eine Domäne festgelegt ist, die auf „.onmicrosoft.com“ endet, wird in der Zustimmungsaufforderung der App **nicht überprüft** anstelle der Herausgeberdomäne angezeigt.

## <a name="grandfathered-applications"></a>Bestehende Anwendungen

Wenn Ihre App vor dem 21. Mai 2019 registriert wurde und Sie keine Herausgeberdomäne festgelegt haben, wird in der Zustimmungsaufforderung der App nicht **nicht überprüft** angezeigt. Es wird empfohlen, dass Sie den Wert der Herausgeberdomäne festlegen, sodass Benutzer diese Informationen in der Zustimmungsaufforderung der App sehen können.

## <a name="configure-publisher-domain-using-the-azure-portal"></a>Konfigurieren der Herausgeberdomäne über das Azure-Portal

Gehen Sie folgendermaßen vor, um die Herausgeberdomäne Ihrer App festzulegen.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, unter dem die App registriert ist.
1. Navigieren Sie zu [Azure Active Directory > App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908), um die App, die Sie konfigurieren möchten, zu suchen und auszuwählen.

   Nachdem Sie die App ausgewählt haben, wird die Seite **Übersicht** der App angezeigt.
1. Wählen Sie unter **Verwalten** die Option **Branding** aus.
1. Suchen Sie das Feld **Herausgeberdomäne**, und wählen Sie eine der folgenden Optionen aus:

   - Wählen Sie **Domäne konfigurieren** aus, wenn Sie noch keine Domäne konfiguriert haben.
   - Wählen Sie **Domäne aktualisieren** aus, wenn bereits eine Domäne konfiguriert wurde.

Wenn Ihre App in einem Mandanten registriert ist, werden zwei auszuwählende Registerkarten angezeigt: **Verifizierte Domäne auswählen** und **Neue Domäne verifizieren**.

Wenn Ihre App nicht in einem Mandanten registriert ist, wird nur die Option zum Verifizieren einer neuen Domäne für Ihre App angezeigt.

### <a name="to-verify-a-new-domain-for-your-app"></a>So verifizieren Sie eine neue Domäne für Ihre App

1. Erstellen Sie eine Datei mit dem Namen `microsoft-identity-association.json`, und fügen Sie den folgenden JSON-Codeausschnitt ein.

   ```json
   {
      "associatedApplications": [
         {
            "applicationId": "{YOUR-APP-ID-HERE}"
         },
         {
            "applicationId": "{YOUR-OTHER-APP-ID-HERE}"
         }
      ]
    }
   ```

1. Ersetzen Sie den Platzhalter *{YOUR-APP-ID-HERE}* durch die Anwendungs-ID (Client-ID) Ihrer App.
1. Hosten Sie die Datei unter: `https://{YOUR-DOMAIN-HERE}.com/.well-known/microsoft-identity-association.json`. Ersetzen Sie den Platzhalter *{YOUR-DOMAIN-HERE}* durch die überprüfte Domäne.
1. Klicken Sie auf die Schaltfläche **Domäne überprüfen und speichern**.

Sie müssen keine Ressourcen verwalten, die für die Überprüfung verwendet werden, nachdem eine Domäne überprüft wurde. Nach Abschluss der Überprüfung können Sie die gehostete Datei entfernen.

### <a name="to-select-a-verified-domain"></a>So wählen Sie eine verifizierte Domäne aus

Wenn der Mandant über überprüfte Domänen verfügt, wählen Sie eine der Domänen in der Dropdownliste **Verifizierte Domäne auswählen** aus.

> [!NOTE]
> Der erwartete `Content-Type`-Header, der zurückgegeben werden soll, lautet `application/json`. Sie erhalten möglicherweise eine Fehlermeldung, wenn Sie etwas anderes (beispielsweise `application/json; charset=utf-8`) verwenden:
>
> `Verification of publisher domain failed. Error getting JSON file from https:///.well-known/microsoft-identity-association. The server returned an unexpected content type header value.`
>

## <a name="implications-on-the-app-consent-prompt"></a>Auswirkungen auf die Zustimmungsaufforderung der App

Die Konfiguration der Herausgeberdomäne wirkt sich darauf aus, was Benutzern in der Zustimmungsaufforderung der App angezeigt wird. Umfassende Informationen zu den Komponenten der Zustimmungsaufforderung finden Sie unter [Grundlegendes zu Zustimmungserfahrungen für Azure AD-Anwendungen](application-consent-experience.md).

Die folgende Tabelle enthält Angaben zum Verhalten von Anwendungen, die vor dem 21. Mail 2019 erstellt wurden.

![Zustimmungsaufforderung für Apps, die vor dem 21. Mai 2019 erstellt wurden](./media/howto-configure-publisher-domain/old-app-behavior-table.png)

Das Verhalten für neue Anwendungen, die nach dem 21. Mai 2019 erstellt werden, hängt von der Herausgeberdomäne und dem Typ der Anwendung ab. In der folgenden Tabelle sind die zu erwartenden Änderungen für die verschiedenen Konfigurationskombinationen aufgeführt.

![Zustimmungsaufforderung für Apps, die nach dem 21. Mai 2019 erstellt werden](./media/howto-configure-publisher-domain/new-app-behavior-table.png)

## <a name="implications-on-redirect-uris"></a>Auswirkungen auf Umleitungs-URIs

Für Anwendungen, bei denen sich Benutzer mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto anmelden (mehrinstanzenfähig), gelten einige Einschränkungen bei der Angabe von Umleitungs-URIs.

### <a name="single-root-domain-restriction"></a>Beschränkung auf einzelne Stammdomäne

Wenn der Wert der Herausgeberdomäne für mehrinstanzenfähige Apps auf NULL festgelegt ist, sind die Apps auf eine einzelne Stammdomäne für die Umleitungs-URIs beschränkt. Die folgende Kombination von Werten ist z. B. nicht zulässig, da die Stammdomäne „contoso.com“ nicht mit „fabrikam.com“ übereinstimmt.

```
"https://contoso.com",
"https://fabrikam.com",
```

### <a name="subdomain-restrictions"></a>Einschränkungen für Unterdomänen

Unterdomänen sind zulässig, Sie müssen jedoch die Stammdomäne explizit registrieren. Obwohl die folgenden URIs eine einzelne Stammdomäne verwenden, ist die folgende Kombination z. B. nicht zulässig.

```
"https://app1.contoso.com",
"https://app2.contoso.com",
```

Wenn der Entwickler jedoch explizit die Stammdomäne hinzufügt, ist die Kombination zulässig.

```
"https://contoso.com",
"https://app1.contoso.com",
"https://app2.contoso.com",
```

### <a name="exceptions"></a>Ausnahmen

In den folgenden Fällen gilt die Beschränkung auf eine einzelne Stammdomäne nicht:

- Apps mit einem einzelnen Mandanten oder Apps für Konten in einem einzelnen Verzeichnis
- Verwendung von Localhost als Umleitungs-URIs
- Umleitungs-URIs mit benutzerdefinierten Schemas (nicht HTTP oder HTTPS)

## <a name="configure-publisher-domain-programmatically"></a>Programmgesteuertes Konfigurieren der Herausgeberdomäne

Derzeit kann die Herausgeberdomäne nicht über eine REST-API oder mit PowerShell programmgesteuert konfiguriert werden.
