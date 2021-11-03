---
title: 'Auffordern von Benutzern zur Einrichtung der Microsoft Authenticator-App (Vorschauversion): Azure Active Directory'
description: Hier erfahren Sie, wie Sie in Ihrer Organisation den Umstieg von weniger sicheren Authentifizierungsmethoden auf die Microsoft Authenticator-App vorantreiben.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: justinha
author: mjsantani
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd27eb5e55b679d8cfd1589857cc88d7f27917d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054360"
---
# <a name="how-to-nudge-users-to-set-up-microsoft-authenticator-preview---microsoft-authenticator-app"></a>Auffordern von Benutzern zur Einrichtung von Microsoft Authenticator (Vorschauversion): Microsoft Authenticator-App

Sie können Benutzer bei der Anmeldung zur Einrichtung von Microsoft Authenticator auffordern. Benutzer durchlaufen ihre reguläre Anmeldung, führen wie gewohnt eine mehrstufige Authentifizierung durch und werden dann aufgefordert, Microsoft Authenticator einzurichten. Sie können Benutzer oder Gruppen ein- oder ausschließen, um zu steuern, wer zur Einrichtung der App aufgefordert werden soll. Dies ermöglicht gezielte Kampagnen, um zu erreichen, dass Benutzer weniger sicherer Authentifizierungsmethoden auf Microsoft Authenticator umsteigen.  

Sie können nicht nur auswählen, wer aufgefordert werden soll, sondern auch definieren, wie viele Tage ein Benutzer den Vorschlag aufschieben kann bzw. nach wie vielen Tagen der Benutzer erneut erinnert werden soll. Wenn ein Benutzer auf **Nicht jetzt** tippt, um das App-Setup zu verschieben, wird er beim nächsten MFA-Versuch nach Ablauf des Verschiebungszeitraums erneut erinnert. 

## <a name="prerequisites"></a>Voraussetzungen 

- In Ihrer Organisation muss Azure MFA aktiviert sein. 
- Der Benutzer darf Microsoft Authenticator noch nicht für Pushbenachrichtigungen für sein Konto eingerichtet haben. 
- Administratoren müssen Benutzer mithilfe einer der folgenden Richtlinien für Microsoft Authenticator aktivieren:  
  - MFA-Registrierungsrichtlinie: Benutzer müssen für **Benachrichtigung über mobile App** aktiviert werden.  
  - Authentifizierungsmethodenrichtlinie: Benutzer müssen für Microsoft Authenticator aktiviert werden, und der Authentifizierungsmodus muss auf **Beliebig** oder **Push** festgelegt werden. Wenn die Richtlinie auf **Kennwortlos** festgelegt ist, ist es nicht möglich, den Benutzer aufzufordern. 

## <a name="user-experience"></a>Benutzererfahrung

1. Der Benutzer durchläuft erfolgreich die mehrstufige Authentifizierung über Azure MFA. 

1. Der Benutzer wird aufgefordert, die Microsoft Authenticator-App einzurichten, um die Anmeldung zu verbessern. Hinweis: Die Aufforderung wird nur Benutzern angezeigt, die Pushbenachrichtigungen von Microsoft Authenticator erhalten dürfen und bei denen Microsoft Authenticator derzeit nicht eingerichtet ist. 

   ![Benutzer führt mehrstufige Authentifizierung durch](./media/how-to-nudge-authenticator-app/user-mfa.png)

1. Der Benutzer tippt auf **Weiter** und durchläuft die Einrichtung von Microsoft Authenticator. 
   1. Zunächst lädt der Benutzer die App herunter.  

      ![Benutzer lädt Microsoft Authenticator herunter](./media/how-to-nudge-authenticator-app/download.png)

   1. Anschließend werden Informationen zur Einrichtung von Microsoft Authenticator angezeigt. 
   
      ![Benutzer richtet Microsoft Authenticator ein](./media/how-to-nudge-authenticator-app/setup.png)

   1. Als Nächstes scannt der Benutzer den QR-Code. 

      ![Benutzer scannt QR-Code](./media/how-to-nudge-authenticator-app/scan.png)

   1. Der Benutzer genehmigt die Testbenachrichtigung.

      ![Benutzer genehmigt Testbenachrichtigung](./media/how-to-nudge-authenticator-app/test.png)

   1. Die Benachrichtigung wurde genehmigt.

      ![Bestätigung der Genehmigung](./media/how-to-nudge-authenticator-app/approved.png)

   1. Die Authenticator-App ist nun als Standardanmeldemethode des Benutzers eingerichtet.

      ![Installation abgeschlossen](./media/how-to-nudge-authenticator-app/finish.png)

1. Wenn ein Benutzer die Authenticator-App nicht installieren möchte, kann er auf **Jetzt nicht** tippen, um erst wieder nach einer bestimmten Anzahl von Tagen erinnert zu werden. Dieser Zeitraum kann durch einen Administrator definiert werden. 
 
   ![Aufschieben der Installation](./media/how-to-nudge-authenticator-app/snooze.png)

## <a name="enable-the-nudge-policy"></a>Aktivieren der Aufforderungsrichtlinie

Wenn Sie die Aufforderung aktivieren möchten, müssen Sie dazu die Authentifizierungsmethodenrichtlinie über Graph-APIs verwenden. Die Richtlinie kann von **globalen Administratoren** oder von Administratoren für die **Authentifizierungsmethodenrichtlinie** aktualisiert werden. 

So konfigurieren Sie die Richtlinie mithilfe von Graph-Tester:

1. Melden Sie sich bei Graph-Tester an, und vergewissern Sie sich, dass Sie den Berechtigungen **Policy.Read.All** und **Policy.ReadWrite.AuthenticationMethod** zugestimmt haben.

   So öffnen Sie das Panel mit den Berechtigungen:

   ![Screenshot: Graph-Tester](./media/how-to-nudge-authenticator-app/permissions.png)

1. Rufen Sie die Authentifizierungsmethodenrichtlinie ab: `GET https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

1. Aktualisieren Sie die Abschnitte „registrationEnforcement“ und „authenticationMethodsRegistrationCampaign“ der Richtlinie, um die Aufforderung für einen Benutzer oder eine Gruppe zu aktivieren.

   ![Kampagnenabschnitt](./media/how-to-nudge-authenticator-app/campaign.png)

Führen Sie zum Aktualisieren der Richtlinie einen PATCH-Vorgang für die Authentifizierungsmethodenrichtlinie aus, und beschränken Sie ihn auf den aktualisierten Abschnitt „registrationEnforcement“: `PATCH https://graph.microsoft.com/beta/policies/authenticationmethodspolicy`

Die folgende Tabelle enthält Eigenschaften für **AuthenticationMethodsRegistrationCampaign**:

| Name | Mögliche Werte | BESCHREIBUNG |
|------|-----------------|-------------|
| state | "enabled"<br>"disabled"<br>"default" | Ermöglicht das Aktivieren oder Deaktivieren des Features.<br>Der Standardwert wird verwendet, wenn die Konfiguration nicht explizit festgelegt wurde. In diesem Fall wird dann der Azure AD-Standardwert für diese Einstellung verwendet. Er ist aktuell „disabled“ (deaktiviert) zugeordnet.<br>Ändern Sie den Zustand nach Bedarf in „enabled“ (aktiviert) oder „disabled“ (deaktiviert).  |
| snoozeDurationInDays | Bereich: 0–14 | Definiert, nach wie vielen Tagen der Benutzer erneut erinnert wird.<br>Ist der Wert auf „0“ festgelegt, wird der Benutzer bei jedem MFA-Versuch erinnert.<br>Standardwert: 1 Tag |
| includeTargets | – | Ermöglicht es, verschiedene Benutzer und Gruppen einzuschließend, auf die das Feature ausgerichtet sein soll. |
| excludeTargets | – | Ermöglicht es, verschiedene Benutzer und Gruppen auszuschließend, die von dem Feature ausgenommen werden sollen. Wenn sich ein Benutzer in einer ausgeschlossenen Gruppe und einer eingeschlossenen Gruppe befindet, wird der Benutzer von dem Feature ausgenommen.|

Die folgende Tabelle enthält Eigenschaften für **includeTargets**:

| Name | Mögliche Werte | BESCHREIBUNG |
|------|-----------------|-------------|
| targetType| "user"<br>„Gruppe“ | Die Art der Zielentität. |
| Id | Ein GUID-Bezeichner | Die ID des Zielbenutzers oder der Zielgruppe. |
| targetedAuthenticationMethod | "microsoftAuthenticator" | Der Benutzer der Authentifizierungsmethode wird zur Registrierung aufgefordert. Der einzige zulässige Wert ist „microsoftAuthenticator“. |

Die folgende Tabelle enthält Eigenschaften für **excludeTargets**:

| Name       | Mögliche Werte   | BESCHREIBUNG                           |
|------------|-------------------|---------------------------------------|
| targetType | "user"<br>„Gruppe“ | Die Art der Zielentität.          |
| Id         | Eine Zeichenfolge          | Die ID des Zielbenutzers oder der Zielgruppe. |

### <a name="examples"></a>Beispiele

Hier finden Sie einige JSON-Beispiele, die Sie für die ersten Schritte verwenden können: 

- Alle Benutzer einbeziehen. 
  
  Wenn Sie ALLE Benutzer in Ihrem Mandanten einschließen möchten, laden Sie einfach [diesen JSON-Code](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/All%20Users%20Enabled.json) herunter, fügen Sie ihn in Graph-Tester ein, und führen Sie `PATCH` für den Endpunkt aus. 

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [],
              "includeTargets": [
                  {
                      "id": "all_users",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

- Einschließen bestimmter Benutzer oder Benutzergruppen

  Wenn Sie bestimmte Benutzer oder Gruppen in Ihrem Mandanten einschließen möchten, laden Sie [diesen JSON-Code](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes.json) herunter, und aktualisieren Sie ihn mit den relevanten GUIDs Ihrer Benutzer bzw. Gruppen. Fügen Sie den JSON-Code anschließend in Graph-Tester ein, und führen Sie `PATCH` für den Endpunkt aus. 

  ```json
  {
  "registrationEnforcement": {
        "authenticationMethodsRegistrationCampaign": {
            "snoozeDurationInDays": 0,
            "state": "enabled",
            "excludeTargets": [],
            "includeTargets": [
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "group",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                },
                {
                    "id": "*********PLEASE ENTER GUID***********",
                    "targetType": "user",
                    "targetedAuthenticationMethod": "microsoftAuthenticator"
                }
            ]
        }
    }
  ```

- Ein- und Ausschließen bestimmter Benutzer/Benutzergruppen

  Wenn Sie bestimmte Benutzer/Benutzergruppen in Ihrem Mandanten ein- UND ausschließen möchten, laden Sie [diesen JSON-Code](https://download.microsoft.com/download/1/4/E/14E6151E-C40A-42FB-9F66-D8D374D13B40/Multiple%20Includes%20and%20Excludes.json) herunter, fügen Sie ihn in Graph-Tester ein, und führen Sie `PATCH` für den Endpunkt aus. Geben Sie die korrekten GUIDs für Ihre Benutzer und Gruppen ein.

  ```json
  {
  "registrationEnforcement": {
          "authenticationMethodsRegistrationCampaign": {
              "snoozeDurationInDays": 0,
              "state": "enabled",
              "excludeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group"
                  },
                {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user"
                  }
              ],
              "includeTargets": [
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "group",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  },
                  {
                      "id": "*********PLEASE ENTER GUID***********",
                      "targetType": "user",
                      "targetedAuthenticationMethod": "microsoftAuthenticator"
                  }
              ]
          }
      }
  }
  ```

### <a name="identify-the-guids-of-users-to-insert-in-the-jsons"></a>Identifizieren der GUIDs von Benutzern, die in den JSON-Code eingefügt werden sollen

1. Navigieren Sie zum Azure-Portal.
1. Tippen Sie auf **Azure Active Directory**.
1. Tippen Sie auf dem Blatt **Verwalten** auf **Benutzer**.
1. Navigieren Sie auf der Seite **Benutzer** zu dem spezifischen Benutzer, den Sie als Ziel verwenden möchten.
1. Wenn Sie auf den spezifischen Benutzer tippen, wird die **Objekt-ID** des Benutzers angezeigt. Dies ist die GUID des Benutzers.

   ![Benutzerobjekt-ID](./media/how-to-nudge-authenticator-app/object-id.png)

### <a name="identify-the-guids-of-groups-to-insert-in-the-jsons"></a>Identifizieren der GUIDs von Gruppen, die in den JSON-Code eingefügt werden sollen

1. Navigieren Sie zum Azure-Portal.
1. Tippen Sie auf **Azure Active Directory**.
1. Tippen Sie auf dem Blatt **Verwalten** auf **Gruppen**.
1. Navigieren Sie auf der Seite **Gruppen** zu der spezifischen Gruppe, die Sie als Ziel verwenden möchten.
1. Tippen Sie auf die Gruppe, um die **Objekt-ID** zu ermitteln.

   ![Auffordern einer Gruppe](./media/how-to-nudge-authenticator-app/group.png)

<!---comment out PS until ready>

### PowerShell

1. Install the module.
1. Ensure you pass the right roles:
   
   ```powershell
   Connect-MgGraph -Scopes "User.Read.All","Group.ReadWrite.All"
   ```

1. Select the beta profile.
1. Call `Update-MgPolicyAuthenticationMethod`.

<---->

## <a name="limitations"></a>Einschränkungen

Die Aufforderung wird nicht auf mobilen Geräten unter Android oder iOS angezeigt.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

**Steht dieses Feature für den MFA-Server zur Verfügung?** Nein. Dieses Feature ist nur für Benutzer verfügbar, die Azure MFA verwenden. 

**Wie lange läuft die Kampagne?** Mithilfe der APIs können Sie die Kampagne beliebig lange aktivieren. Wenn Sie die Kampagne beenden möchten, können Sie sie einfach mithilfe der APIs deaktivieren.  
 
**Kann für jede Benutzergruppe ein anderer Zeitraum bis zur erneuten Erinnerung festgelegt werden?** Nein. Der Zeitraum bis zur erneuten Erinnerung für die Aufforderung ist eine mandantenweite Einstellung und gilt für alle eingeschlossenen Gruppen. 

**Können Benutzer zum Einrichten der kennwortlosen Anmeldung per Telefon aufgefordert werden?** Mithilfe dieses Features wird es Administratoren ermöglicht, Benutzer zur Einrichtung der mehrstufigen Authentifizierung über die Authenticator-App zu bewegen, anstatt die kennwortlose Anmeldung per Telefon zu verwenden.  

**Wird die Aufforderung Benutzern angezeigt, die eine Authenticator-App eines Drittanbieters eingerichtet haben?** Wenn der Benutzer die Microsoft Authenticator-App nicht für Pushbenachrichtigungen eingerichtet hat und dafür per Richtlinie aktiviert wurde, wird ihm die Aufforderung angezeigt. 

**Wird die Aufforderung Benutzern angezeigt, die eine Microsoft Authenticator-App nur für TOTP-Codes eingerichtet haben?** Ja. Wenn die Microsoft Authenticator-App nicht für Pushbenachrichtigungen eingerichtet ist und der Benutzer dafür per Richtlinie aktiviert wurde, wird ihm die Aufforderung angezeigt.

**Wird die Aufforderung einem Benutzer, der gerade eine MFA-Registrierung durchlaufen hat, innerhalb der gleichen Anmeldesitzung angezeigt?** Nein. Aus Gründen der Benutzerfreundlichkeit werden Benutzer nicht innerhalb der gleichen Sitzung, in der sie andere Authentifizierungsmethoden registriert haben, zur Einrichtung von Authenticator aufgefordert.  

**Kann ich meine Benutzer zur Registrierung einer anderen Authentifizierungsmethode auffordern?** Nein. Das Feature zielt vorerst nur darauf ab, Benutzer zur Einrichtung der Microsoft Authenticator-App aufzufordern. 

**Gibt es eine Möglichkeit, die Option für die erneute Erinnerung auszublenden, um die Einrichtung der Authenticator-App zu erzwingen?**  
Die Option für die erneute Erinnerung in der Aufforderung kann nicht ausgeblendet werden. Sie können „snoozeDuration“ auf „0“ festlegen, sodass Benutzern die Aufforderung bei jedem MFA-Versuch angezeigt wird.  

**Kann ich Benutzer auffordern, wenn ich nicht Azure MFA verwende?** Nein. Die Aufforderung funktioniert nur für Benutzer, die eine mehrstufige Authentifizierung über den Azure MFA-Dienst durchlaufen. 

**Wird die Aufforderung Gast-/B2B-Benutzern in meinem Mandanten angezeigt?** Ja. Sofern sie mithilfe der entsprechenden Richtlinie in die Aufforderung eingeschlossen wurden. 

**Was passiert, wenn der Benutzer den Browser schließt?** In diesem Fall wird der Benutzer erneut erinnert.


## <a name="next-steps"></a>Nächste Schritte

[Aktivieren der kennwortlosen Anmeldung mit der Microsoft Authenticator-App (Vorschauversion)](howto-authentication-passwordless-phone.md)