---
title: Resilienz-Standardwerte für den bedingten Azure AD-Zugriff
description: Resilienz-Standardwerte und der Azure AD-Sicherungsauthentifizierungsdienst
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2bebe9769d38502a37134326228d41979cad5a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432679"
---
# <a name="conditional-access-resilience-defaults-preview"></a>Bedingter Zugriff: Resilienz-Standardwerte (Vorschau)

Bei einem Ausfall des primären Authentifizierungsdiensts kann der Azure Active Directory (Azure AD)-Sicherungsauthentifizierungsdienst automatisch Zugriffstoken für Anwendungen für vorhandene Sitzungen ausstellen. Diese Funktionalität kann die Resilienz von Azure AD deutlich erhöhen, da erneute Authentifizierungen für vorhandene Sitzungen mehr als 90 Prozent der Authentifizierungen für Azure AD ausmachen. Der Sicherungsauthentifizierungsdienst unterstützt keine neuen Sitzungen oder Authentifizierungen von Gastbenutzern.

Für Authentifizierungen, die durch bedingten Zugriff geschützt sind, werden Richtlinien vor der Ausgabe von Zugriffstoken neu bewertet, um Folgendes zu ermitteln:

1.  Welche Richtlinien für bedingten Zugriff werden angewendet?
1.  Wurden die erforderlichen Kontrollen für die angewendeten Richtlinien erfüllt?

Bei einem Ausfall können nicht alle Bedingungen vom Backup-Authentifizierungsdienst in Echtzeit ausgewertet werden, um zu bestimmen, ob eine Richtlinie für bedingten Zugriff angewendet werden soll. Resilienz-Standardwerte für den bedingten Zugriff sind eine neue Sitzungssteuerung, mit der Administratoren entscheiden können, ob Authentifizierungen bei einem Ausfall blockiert werden sollen, wenn eine Richtlinienbedingung nicht in Echtzeit ausgewertet werden kann, oder Richtlinien mithilfe von Daten ausgewertet werden können, die zu Beginn der Benutzersitzung gesammelt wurden. 

> [!IMPORTANT]
> Resilienz-Standardwerte werden automatisch für alle neuen und vorhandenen Richtlinien aktiviert, und Microsoft empfiehlt dringend, diese Standards aktiviert zu lassen, um die Auswirkungen eines Ausfalls zu minimieren. Administratoren können Resilienz-Standardwerte für einzelne Richtlinien für den bedingten Zugriff deaktivieren. 

## <a name="how-does-it-work"></a>Wie funktioniert dies?

Bei einem Ausfall stellt der Sicherungsauthentifizierungsdienst automatisch Zugriffstoken für bestimmte Sitzungen aus:

| Sitzungsbeschreibung | Der Zugriff wird gewährt. |
| --- | --- |
| Neue Sitzung | No |
| Vorhandene Sitzung – keine Richtlinien für den bedingten Zugriff konfiguriert | Yes |
| Vorhandene Sitzung – Richtlinien für den bedingten Zugriff wurden konfiguriert und die erforderlichen Kontrollen wie MFA wurden zuvor erfüllt. | Yes |
| Vorhandene Sitzung – Richtlinien für den bedingten Zugriff wurden konfiguriert und die erforderlichen Kontrollen wie MFA wurden zuvor nicht erfüllt. | Bestimmt durch Resilienz-Standard |

Wenn eine vorhandene Sitzung bei einem Azure AD-Ausfalls abläuft, wird die Anforderung eines neuen Zugriffstokens an den Sicherungsauthentifizierungsdienst weitergeleitet, und alle Richtlinien für den bedingten Zugriff werden neu ausgewertet. Wenn keine Richtlinien für den bedingten Zugriff vorhanden sind oder alle erforderlichen Kontrollen wie MFA zuvor zu Beginn der Sitzung erfüllt wurden, gibt der Sicherungsauthentifizierungsdienst ein neues Zugriffstoken aus, um die Sitzung zu verlängern. 

Wenn die erforderlichen Kontrollen einer Richtlinie zuvor nicht erfüllt wurden, wird die Richtlinie neu ausgewertet, um zu bestimmen, ob der Zugriff gewährt oder verweigert werden soll. Allerdings können nicht alle Bedingungen während eines Ausfalls in Echtzeit neu ausgewertet werden. Zu diesen Bedingungen zählen 

- Gruppenmitgliedschaft
- Rollenmitgliedschaft
- Anmelderisiko
- Benutzerrisiko
- Land/Standort (Auflösen neuer IP-Adressen oder GPS-Koordinaten)

## <a name="resilience-defaults-enabled"></a>Resilienz-Standardwerte aktiviert

Wenn Resilienz-Standardwerte aktiviert sind, kann der Sicherungsauthentifizierungsdienst, wenn Echtzeitdaten fehlen, mit den zu Beginn der Sitzung gesammelten Daten auswerten, ob die Richtlinie angewendet werden soll. Standardmäßig sind für alle Richtlinien Resilienz-Standardwerte aktiviert. Die Einstellung kann für einzelne Richtlinien deaktiviert werden, wenn für den Zugriff auf vertrauliche Anwendungen während eines Ausfalls eine Echtzeit-Richtlinienauswertung erforderlich ist.

**Beispiel**: Eine Richtlinie mit aktivierten Resilienz-Standardwerten verpflichtet alle globalen Administratoren, MFA für den Zugriff auf Azure-Portal zu verwenden. Wenn ein Benutzer, der kein globaler Administrator ist, vor einem Ausfall auf die Azure-Portal zugreift, wird die Richtlinie nicht angewendet und dem Benutzer wird ohne MFA Zugriff gewährt. Während eines Ausfalls wertet der Sicherungsauthentifizierungsdienst die Richtlinie neu aus, um zu bestimmen, ob der Benutzer zur MFA aufgefordert werden soll. **Da der Sicherungsauthentifizierungsdienst die Rollenmitgliedschaft nicht in Echtzeit auswerten kann, bestimmt er mit den zu Beginn der Benutzersitzung gesammelten Daten, dass die Richtlinie weiterhin nicht angewendet werden soll. Daher wird dem Benutzer ohne MFA Zugriff gewährt.**

## <a name="resilience-defaults-disabled"></a>Resilienz-Standardwerte deaktiviert

Wenn Resilienz-Standardwerte deaktiviert sind, verwendet der Sicherungsauthentifizierungsdienst die zu Beginn der Sitzung gesammelten Daten nicht für die Auswertung der Bedingungen. Wenn eine Richtlinienbedingung bei einem Ausfall nicht in Echtzeit ausgewertet werden kann, wird der Zugriff verweigert.

**Beispiel**: Eine Richtlinie mit deaktivierten Resilienz-Standardwerten verpflichtet alle globalen Administratoren, MFA für den Zugriff auf Azure-Portal zu verwenden. Wenn ein Benutzer, der kein globaler Administrator ist, vor einem Ausfall auf die Azure-Portal zugreift, wird die Richtlinie nicht angewendet und dem Benutzer wird ohne MFA Zugriff gewährt. Während eines Ausfalls wertet der Sicherungsauthentifizierungsdienst die Richtlinie neu aus, um zu bestimmen, ob der Benutzer zur MFA aufgefordert werden soll. **Da der Sicherungsauthentifizierungsdienst die Rollenmitgliedschaft nicht in Echtzeit auswerten kann, blockiert er den Zugriff auf Azure-Portal für den Benutzer.**

> [!WARNING]
> Das Deaktivieren der Resilienz-Standardwerte für eine Richtlinie, die auf eine Gruppe oder Rolle angewendet wird, verringert die Resilienz für alle Benutzer in Ihrem Mandanten. Da die Gruppen- und Rollenmitgliedschaft bei einem Ausfall nicht in Echtzeit ausgewertet werden kann, wird auch Benutzern, die nicht der Gruppe oder Rolle in der Richtlinienzuweisung angehören, der Zugriff auf die Anwendung im Gültigkeitsbereich der Richtlinie verweigert. Damit die Resilienz nicht für alle Benutzer verringert wird, die nicht zum Gültigkeitsbereich der Richtlinie gehören, sollten Sie die Richtlinie auf einzelne Benutzer anstatt auf Gruppen oder Rollen anwenden. 

## <a name="testing-resilience-defaults"></a>Testen von Resilienz-Standardwerten

Es ist nicht möglich, mit dem Sicherungsauthentifizierungsdiensts einen Probelauf durchzuführen oder das Ergebnis einer Richtlinie mit aktivierten oder deaktivierten Resilienz-Standardwerten zu simulieren. Azure AD führt monatliche Übungen mit dem Sicherungsauthentifizierungsdienst durch, und die Anmeldeprotokolle zeigen, ob der Sicherungsauthentifizierungsdienst zum Ausstellen des Zugriffstokens verwendet wurde.

## <a name="configuring-resilience-defaults"></a>Konfigurieren von Resilienz-Standardwerten

Sie können die Resilienz-Standardwerte für den bedingten Zugriff über die Azure-Portal, MS Graph-APIs oder PowerShell konfigurieren. 

### <a name="azure-portal"></a>Azure-Portal

1.  Navigieren Sie zum **Azure-Portal** > **Sicherheit** > **Bedingter Zugriff**.
1.  Wählen Sie eine vorhandene Richtlinie aus, oder erstellen Sie eine neue Richtlinie.
1.  Öffnen Sie die Einstellungen der Sitzungssteuerung.
1.  Wählen Sie „Standardwerte für Resilienz deaktivieren“ aus, um die Einstellung für diese Richtlinie zu deaktivieren. Anmeldungen im Gültigkeitsbereich der Richtlinie werden bei einem Azure AD Ausfall blockiert.
1.  Speichern Sie die Änderungen der Richtlinie.

### <a name="ms-graph-apis"></a>MS Graph-APIs

Sie können die Resilienz-Standardwerte für Ihre Richtlinien für den bedingten Zugriff auch mit der MS Graph-API und dem [Microsoft Graph-Explorers](/graph/graph-explorer/graph-explorer-overview) verwalten. 

URL von Beispielanforderung: 

`PATCH https://graph.microsoft.com/beta/identity/conditionalAccess/policies/policyId`

Text von Beispielanforderung: 

```json

{
"sessionControls": {
"disableResilienceDefaults": true
}
}
```

### <a name="powershell"></a>PowerShell

Dieser Patchvorgang kann nach der Installation des Microsoft.Graph.Authentication-Moduls über die Microsoft PowerShell bereitgestellt werden. Öffnen Sie zum Installieren dieses Moduls eine PowerShell-Eingabeaufforderung mit erhöhten Rechten, und führen Sie den Befehl zum

`Install-Module Microsoft.Graph.Authentication`

Herstellen einer Verbindung mit Microsoft Graph aus. Fordern Sie dabei die benötigten Umfänge an. –

`Connect-MgGraph -Scopes Policy.Read.All,Policy.ReadWrite.ConditionalAccess,Application.Read.All -TenantId <TenantID>`

Führen Sie die Authentifizierung durch, wenn Sie dazu aufgefordert werden.

Erstellen Sie den JSON-Text für die PATCH-Anforderung. –

`$patchBody = '{"sessionControls": {"disableResilienceDefaults": true}}'`

Führen Sie den Patchvorgang aus. –

`Invoke-MgGraphRequest -Method PATCH -Uri https://graph.microsoft.com/beta/identity/conditionalAccess/policies/<PolicyID> -Body $patchBody`

## <a name="recommendations"></a>Empfehlungen

Microsoft empfiehlt, Resilienz-Standardwerte zu aktivieren. Zwar gibt es keine direkten Sicherheitsbedenken. Kunden sollten aber abwägen, ob sie zulassen möchten, dass der Sicherungsauthentifizierungsdienst bei einem Ausfall Richtlinien für den bedingten Zugriff mit den zu Beginn der Sitzung gesammelten Daten auswertet anstatt in Echtzeit. 

Es ist möglich, dass sich die Rollen- oder Gruppenmitgliedschaft eines Benutzers seit dem Beginn der Sitzung geändert hat. Bei der [fortlaufenden Zugriffsevaluierung](concept-continuous-access-evaluation.md) sind Zugriffstoken 24 Stunden lang gültig, unterliegen jedoch sofortigen Sperrungsereignissen. Der Sicherungsauthentifizierungsdienst abonniert dieselbe fortlaufende Sperrungsereignis-Zugriffsevaluierung. Wenn das Token eines Benutzers als Teil der fortlaufenden Zugriffsevaluierung widerrufen wird, kann sich der Benutzer bei einem Ausfall nicht anmelden. Wenn Resilienz-Standardwerte aktiviert sind, werden vorhandene Sitzungen, die bei einem Ausfall ablaufen, verlängert. Sitzungen werden auch dann verlängert, wenn die Richtlinie mit einer Sitzungssteuerung für die Erzwingung einer Anmeldehäufigkeit konfiguriert wurde. Beispielsweise kann eine Richtlinie mit aktivierten Resilienz-Standardwerten einfordern, dass sich Benutzer stündlich erneut authentifizieren, um auf eine SharePoint-Website zuzugreifen. Bei einem Ausfall wird die Benutzersitzung verlängert, auch wenn Azure AD möglicherweise nicht zur erneuten Authentifizierung des Benutzers verfügbar ist. 

## <a name="next-steps"></a>Nächste Schritte

- [Fortlaufende Zugriffsevaluierung](concept-continuous-access-evaluation.md)
