---
title: Verwalten von inaktiven Benutzerkonten in Azure AD | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie in Azure AD veraltete Benutzerkonten erkennen und entfernen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/06/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: efc82f409c6d05ea6441747e718ec2ea3c1429a6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131994970"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Anleitung: Verwalten inaktiver Benutzerkonten in Azure AD

In umfangreichen Umgebungen werden Benutzerkonten nicht immer gelöscht, wenn Mitarbeiter eine Organisation verlassen. Als IT-Administrator sollten Sie diese veralteten Benutzerkonten erkennen und entfernen, da sie ein Sicherheitsrisiko darstellen.

In diesem Artikel wird ein Verfahren zum Umgang mit veralteten Benutzerkonten in Azure AD beschrieben. 

> [!IMPORTANT]
> APIs in der `/beta`-Version in Microsoft Graph ändern sich möglicherweise noch. Die Verwendung dieser APIs in Produktionsanwendungen wird nicht unterstützt. Verwenden Sie den Selektor **Version**, um zu ermitteln, ob eine API in v1.0 verfügbar ist.

## <a name="what-are-inactive-user-accounts"></a>Was sind inaktive Benutzerkonten?

Bei inaktiven Konten handelt es sich um Benutzerkonten, die von Mitgliedern Ihrer Organisation nicht mehr benötigt werden, um auf Ressourcen zuzugreifen. Ein wichtiges Anzeichen für inaktive Konten ist, dass sie schon *längere Zeit* nicht mehr für die Anmeldung an Ihrer Umgebung verwendet wurden. Da inaktive Konten an die Anmeldeaktivität gebunden sind, können Sie für die Ermittlung den Zeitstempel der letzten erfolgreichen Anmeldung nutzen. 

Die Schwierigkeit bei diesem Verfahren ist die Definition der Bedeutung *längere Zeit* für Ihre Umgebung. Es kann beispielsweise sein, dass sich Benutzer *längere Zeit* nicht bei einer Umgebung anmelden, weil sie im Urlaub sind. Beim Definieren Ihres Deltazeitraums für inaktive Benutzerkonten müssen Sie alle legitimen Gründe für die fehlende Anmeldung bei Ihrer Umgebung berücksichtigen. In vielen Organisationen liegt der Deltazeitraum für inaktive Benutzerkonten zwischen 90 und 180 Tagen. 

Die letzte erfolgreiche Anmeldung liefert potenzielle Erkenntnisse dazu, ob ein Benutzer weiterhin Zugriff auf Ressourcen benötigt.  Diese Erkenntnisse sind hilfreich bei der Ermittlung, ob die Gruppenmitgliedschaft oder der App-Zugriff noch benötigt wird oder entfernt werden kann. Bei der Verwaltung externer Benutzer können Sie ermitteln, ob ein externer Benutzer auf dem Mandanten noch aktiv ist oder ob die Bereinigung durchgeführt werden sollte. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Erkennen von inaktiven Benutzerkonten

Sie erkennen inaktive Konten, indem Sie sich die **lastSignInDateTime**-Eigenschaft des Ressourcentyps **signInActivity** der **Microsoft Graph**-API ansehen. Die Eigenschaft **lastSignInDateTime** gibt Aufschluss darüber, wann ein Benutzer zuletzt eine erfolgreiche interaktive Anmeldung bei Azure AD durchgeführt hat. Mit dieser Eigenschaft können Sie eine Lösung für die folgenden Szenarien implementieren:

- **Benutzer nach Name**: In diesem Szenario suchen Sie anhand des Namens nach einem bestimmten Benutzer, um das Datum der letzten Anmeldung (lastSignInDateTime) ermitteln zu können: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Benutzer nach Datum**: In diesem Szenario fordern Sie eine Liste mit Benutzern an, für die das Datum bzw. die Uhrzeit der letzten Anmeldung (lastSignInDateTime) vor einem bestimmten Datum liegen: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Möglicherweise ist es erforderlich, einen Bericht über das Datum der letzten Anmeldung aller Benutzer zu generieren, wenn dies der Fall ist.
> **Datum und Uhrzeit der letzten Anmeldung für alle Benutzer**: in diesem Szenario fordern Sie eine Liste aller Benutzer und den letzten lastsignindatetime-Wert für jeden Benutzer an: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Wichtige Informationen

In diesem Abschnitt wird beschrieben, was Sie über die „lastSignInDateTime“-Eigenschaft wissen müssen.

### <a name="how-can-i-access-this-property"></a>Wie kann ich auf diese Eigenschaft zugreifen?

Die **lastSignInDateTime**-Eigenschaft wird vom [Ressourcentyp „signInActivity“](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) der [Microsoft Graph-REST-API](/graph/overview#whats-in-microsoft-graph) verfügbar gemacht.   

> [!NOTE]
> Der Graph-API-Endpunkt signInActivity wird in US Government GCC High-Umgebungen noch nicht unterstützt.

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>Ist die lastSignInDateTime-Eigenschaft über das Cmdlet „Get-AzureAdUser“ verfügbar?

Nein.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Welche Edition von Azure AD benötige ich zum Zugreifen auf die Eigenschaft?

Für den Zugriff auf diese Eigenschaft benötigen Sie eine Azure Active Directory Premium-Edition.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Welche Berechtigung benötige ich zum Lesen der Eigenschaft?

Zum Lesen dieser Eigenschaft müssen Sie die folgenden Rechte gewähren: 

- AuditLogs.Read.All
- Organization.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Wann wird die Eigenschaft von Azure AD aktualisiert?

Jede erfolgreiche interaktive Anmeldung führt zu einem Update des zugrunde liegenden Datenspeichers. Normalerweise werden erfolgreiche Anmeldungen innerhalb von zehn Minuten im zugehörigen Anmeldebericht angezeigt.
 

### <a name="what-does-a-blank-property-value-mean"></a>Was bedeutet ein leerer Eigenschaftswert?

Zum Generieren eines „lastSignInDateTime“-Zeitstempels ist eine erfolgreiche Anmeldung erforderlich. Da die „lastSignInDateTime“-Eigenschaft ein neues Feature ist, kann der Wert dieser Eigenschaft leer sein, wenn Folgendes gilt:

- Die letzte erfolgreiche Anmeldung eines Benutzers erfolgte vor dem April 2020.
- Das betroffene Benutzerkonto wurde niemals für eine erfolgreiche Anmeldung verwendet.

## <a name="next-steps"></a>Nächste Schritte

* [Abrufen von Daten per Berichtserstellungs-API von Azure Active Directory mit Zertifikaten](tutorial-access-api-with-certificates.md)
* [Referenz zur Überwachungs-API](/graph/api/resources/directoryaudit) 
* [Referenz zur Anmeldeaktivitätsbericht-API](/graph/api/resources/signin)

