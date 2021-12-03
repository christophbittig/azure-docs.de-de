---
title: Problembehandlung bei Geräten über den Befehl „dsregcmd“ – Azure Active Directory
description: In diesem Artikel wird beschrieben, wie Sie die Ausgabe des Befehls „dsregcmd“ verwenden, um den Zustand von Geräten in Azure AD zu ermitteln.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ade33d37584595bcc5c4e9ce1d1fda6edadd67a4
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158077"
---
# <a name="troubleshoot-devices-by-using-the-dsregcmd-command"></a>Problembehandlung bei Geräten über den Befehl „dsregcmd“

In diesem Artikel wird beschrieben, wie Sie die Ausgabe des Befehls `dsregcmd` verwenden, um den Zustand von Geräten in Azure Active Directory (Azure AD) zu ermitteln. Das Hilfsprogramm `dsregcmd /status` muss unter einem Domänenbenutzerkonto ausgeführt werden.

## <a name="device-state"></a>Gerätestatus

In diesem Abschnitt werden die Statusparameter für den Geräte-Join aufgelistet. In der folgenden Tabelle sind die Kriterien aufgeführt, die erforderlich sind, damit sich das Gerät in verschiedenen Einbindungszuständen befindet:

| AzureAdJoined | EnterpriseJoined | DomainJoined | Gerätestatus |
| ---   | ---   | ---   | ---   |
| YES | Nein | Nein | In Azure AD eingebunden |
| Nein | Nein | YES | In die Domäne eingebunden |
| YES | Nein | YES | In Hybrid-AD eingebunden |
| Nein | YES | YES | In lokales DRS eingebunden |
| | |

> [!NOTE]
> Der Zustand „In den Arbeitsplatz eingebunden“ (bei Azure AD registriert) wird im Abschnitt [Benutzerstatus](#user-state) angezeigt.

- **AzureAdJoined**: Der Zustand wird auf *YES* festgelegt, wenn das Gerät in Azure AD eingebunden ist. Andernfalls wird der Zustand auf *NO* festgelegt.
- **EnterpriseJoined**: Der Zustand wird auf *YES* festgelegt, wenn das Gerät in einen lokalen Datenreplikationsdienst (DRS) eingebunden ist. Für ein Gerät kann nicht gleichzeitig „EnterpriseJoined“ und „AzureAdJoined“ aktiviert sein.
- **DomainJoined**: Der Zustand wird auf *YES* festgelegt, wenn das Gerät in eine Domäne (Active Directory) eingebunden ist.
- **DomainName**: Der Zustand wird auf den Namen der Domäne festgelegt, wenn das Gerät in eine Domäne eingebunden ist.

### <a name="sample-device-state-output"></a>Beispielstatusausgabe für Gerät

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+
             AzureAdJoined : YES
          EnterpriseJoined : NO
              DomainJoined : YES
                DomainName : HYBRIDADFS
+----------------------------------------------------------------------+
```

## <a name="device-details"></a>Gerätedetails

Der Zustand wird nur angezeigt, wenn das Gerät in Azure AD oder Azure AD Hybrid eingebunden (nicht bei Azure AD registriert) ist. In diesem Abschnitt werden die in Azure AD gespeicherten Details zur Identifikation von Geräten aufgelistet.

- **DeviceId**: Die eindeutige ID des Geräts im Azure AD-Mandanten.
- **Thumbprint**: Der Fingerabdruck des Gerätezertifikats.
- **DeviceCertificateValidity**: Der Gültigkeitsstatus des Gerätezertifikats.
- **KeyContainerId**: Die containerId des privaten Geräteschlüssels, der dem Gerätezertifikat zugeordnet ist.
- **KeyProvider**: Der KeyProvider (Hardware/Software), der zum Speichern des privaten Geräteschlüssels verwendet wird.
- **TpmProtected**: Der Zustand ist auf *YES* festgelegt, wenn der private Geräteschlüssel in einem Hardware-TPM (Trusted Platform Module) gespeichert ist.
- **DeviceAuthStatus**: Eine Überprüfung wird ausgeführt, um die Integrität des Geräts in Azure AD zu ermitteln. Mögliche Integritätsstatus:  
  * *SUCCESS*, wenn das Gerät in Azure AD vorhanden und aktiviert ist.  
  * *FAILED. Device is either disabled or deleted*, wenn das Gerät deaktiviert oder gelöscht wurde. Weitere Informationen zu diesem Problem finden Sie unter [Häufig gestellte Fragen zur Azure Active Directory-Geräteverwaltung](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-11-devices). 
  * *FAILED. ERROR*, wenn der Test nicht ausgeführt werden konnte. Für diesen Test ist eine Netzwerkverbindung zu Azure AD erforderlich.
    > [!NOTE]
    > Das Feld **DeviceAuthStatus** wurde im Windows 10-Update vom 10. Mai 2021 (Version 21H1) hinzugefügt.  

### <a name="sample-device-details-output"></a>Beispieldetailausgabe des Geräts

```
+----------------------------------------------------------------------+
| Device Details                                                       |
+----------------------------------------------------------------------+

                  DeviceId : e92325d0-xxxx-xxxx-xxxx-94ae875dxxxx
                Thumbprint : D293213EF327483560EED8410CAE36BB67208179
 DeviceCertificateValidity : [ 2019-01-11 21:02:50.000 UTC -- 2029-01-11 21:32:50.000 UTC ]
            KeyContainerId : 13e68a58-xxxx-xxxx-xxxx-a20a2411xxxx
               KeyProvider : Microsoft Software Key Storage Provider
              TpmProtected : NO
          DeviceAuthStatus : SUCCESS
+----------------------------------------------------------------------+
```

## <a name="tenant-details"></a>Mandantendetails

Die Mandantendetails werden nur angezeigt, wenn das Gerät in Azure AD oder Azure AD Hybrid eingebunden (nicht bei Azure AD registriert) ist. In diesem Abschnitt werden die allgemeinen Mandantendetails aufgeführt, die bei Einbindung eines Geräts in Azure AD angezeigt werden.

> [!NOTE]
> Wenn die URL-Felder der Verwaltung mobiler Geräte (MDM) in diesem Abschnitt leer sind, weist dies darauf hin, dass die MDM nicht konfiguriert wurde oder dass sich der aktuelle Benutzer nicht im Bereich der MDM-Registrierung befindet. Überprüfen Sie die Mobilitätseinstellungen in Azure AD, um Ihre MDM-Konfiguration zu überprüfen.

> [!NOTE]
> Auch wenn MDM-URLs angezeigt werden, bedeutet dies nicht, dass das Gerät von einer MDM verwaltet wird. Die Informationen werden angezeigt, wenn der Mandant über eine MDM-Konfiguration für die automatische Registrierung verfügt, auch wenn das Gerät selbst nicht verwaltet wird.

### <a name="sample-tenant-details-output"></a>Beispielausgabe für Mandantendetails

```
+----------------------------------------------------------------------+
| Tenant Details                                                       |
+----------------------------------------------------------------------+

                TenantName : HybridADFS
                  TenantId : 96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
                       Idp : login.windows.net
               AuthCodeUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/authorize
            AccessTokenUrl : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token
                    MdmUrl : https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
                 MdmTouUrl : https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
          MdmComplianceUrl : https://portal.manage-beta.microsoft.com/?portalAction=Compliance
               SettingsUrl : eyJVxxxxIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyxxxx==
            JoinSrvVersion : 1.0
                JoinSrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/device/
                 JoinSrvId : urn:ms-drs:enterpriseregistration.windows.net
             KeySrvVersion : 1.0
                 KeySrvUrl : https://enterpriseregistration.windows.net/EnrollmentServer/key/
                  KeySrvId : urn:ms-drs:enterpriseregistration.windows.net
        WebAuthNSrvVersion : 1.0
            WebAuthNSrvUrl : https://enterpriseregistration.windows.net/webauthn/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
             WebAuthNSrvId : urn:ms-drs:enterpriseregistration.windows.net
    DeviceManagementSrvVer : 1.0
    DeviceManagementSrvUrl : https://enterpriseregistration.windows.net/manage/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/
     DeviceManagementSrvId : urn:ms-drs:enterpriseregistration.windows.net
+----------------------------------------------------------------------+
```

## <a name="user-state"></a>Benutzerzustand

In diesem Abschnitt werden die Status verschiedener Attribute für Benutzer aufgeführt, die zurzeit beim Gerät angemeldet sind.

> [!NOTE]
> Der Befehl muss in einem Benutzerkontext ausgeführt werden, um einen gültigen Status abzurufen.

- **NgcSet**: Der Zustand wird auf *YES* festgelegt, wenn für den derzeit angemeldeten Benutzer ein Windows Hello-Schlüssel festgelegt wurde.
- **NgcKeyId**: ID des Windows Hello-Schlüssels, wenn für den derzeit angemeldeten Benutzer ein Schlüssel festgelegt wurde.
- **CanReset**: Gibt an, ob der Windows Hello-Schlüssel vom Benutzer zurückgesetzt werden kann.
- **Mögliche Werte**: „DestructiveOnly“, „NonDestructiveOnly“, „DestructiveAndNonDestructive“ oder „Unknown“ bei einem Fehler.
- **WorkplaceJoined**: Der Zustand wird auf *YES* festgelegt, wenn dem Gerät bei Azure AD registrierte Konten im aktuellen NTUSER-Kontext hinzugefügt wurden.
- **WamDefaultSet**: Der Zustand wird auf *YES* festgelegt, wenn für den angemeldeten Benutzer ein standardmäßiges WAM-WebAccount (Web Account Manager) erstellt wird. In diesem Feld wird unter Umständen ein Fehler angezeigt, wenn `dsregcmd /status` an einer Eingabeaufforderung mit erweiterten Rechten ausgeführt wird.
- **WamDefaultAuthority**: Der Zustand wird für Azure AD auf *organizations* festgelegt.
- **WamDefaultId**: Verwenden Sie für Azure AD immer *https://login.microsoft.com* .
- **WamDefaultGUID**: Die GUID des WAM-Anbieters (Azure AD/Microsoft-Konto) für das standardmäßige WAM-WebAccount.

### <a name="sample-user-state-output"></a>Beispielausgabe für den Benutzerstatus

```
+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

                    NgcSet : YES
                  NgcKeyId : {FA0DB076-A5D7-4844-82D8-50A2FB42EC7B}
                  CanReset : DestructiveAndNonDestructive
           WorkplaceJoined : NO
             WamDefaultSet : YES
       WamDefaultAuthority : organizations
              WamDefaultId : https://login.microsoft.com
            WamDefaultGUID : { B16898C6-A148-4967-9171-64D755DA8520 } (AzureAd)

+----------------------------------------------------------------------+
```

## <a name="sso-state"></a>SSO-Status

Dieser Abschnitt kann für Geräte, die bei Azure AD registriert sind, ignoriert werden.

> [!NOTE]
> Der Befehl muss in einem Benutzerkontext ausgeführt werden, um einen gültigen Status für diesen Benutzer abzurufen.

- **AzureAdPrt**: Der Zustand wird auf *YES* festgelegt, wenn für den angemeldeten Benutzer auf dem Gerät ein primäres Aktualisierungstoken (Primary Refresh Token, PRT) vorhanden ist.
- **AzureAdPrtUpdateTime**: Der Zustand wird auf den Zeitpunkt in koordinierter Weltzeit (UTC) festgelegt, zu dem das PRT zuletzt aktualisiert wurde.
- **AzureAdPrtExpiryTime**: Der Zustand wird auf den Zeitpunkt in UTC festgelegt, zu dem das PRT abläuft, wenn es nicht erneuert wird.
- **AzureAdPrtAuthority**: Die URL der Azure AD-Autorität.
- **EnterprisePrt**: Der Zustand wird auf *YES* festgelegt, wenn das Gerät über ein PRT der lokalen Active Directory-Verbunddienste (AD FS) verfügt. Hybride, in Azure AD eingebundene Geräte können gleichzeitig ein PRT von Azure AD und von der lokalen Active Directory-Instanz aufweisen. Lokal eingebundene Geräte verfügen nur über ein Unternehmens-PRT.
- **EnterprisePrtUpdateTime**: Der Zustand wird auf den Zeitpunkt in UTC festgelegt, zu dem das Unternehmens-PRT zuletzt aktualisiert wurde.
- **EnterprisePrtExpiryTime**: Der Zustand wird auf den Zeitpunkt in UTC festgelegt, zu dem das PRT abläuft, wenn es nicht erneuert wird.
- **EnterprisePrtAuthority**: Die URL der AD FS-Autorität.

>[!NOTE]
> Die folgenden Felder der PRT-Diagnose wurden im Windows 10-Update vom 10. Mai 2021 (Version 21H1) hinzugefügt.

>[!NOTE]
> * Die im Feld **AzureAdPrt** angezeigten Diagnoseinformationen sind für den Abruf oder die Aktualisierung eines Azure AD-PRT vorgesehen, und die im Feld **EnterprisePrt** angezeigten Diagnoseinformationen sind für den Abruf oder die Aktualisierung eines Unternehmens-PRT bestimmt.
> * Die Diagnoseinformationen werden nur angezeigt, wenn der Abruf- oder Aktualisierungsfehler nach dem Zeitpunkt der letzten erfolgreichen PRT-Aktualisierung (AzureAdPrtUpdateTime/EnterprisePrtUpdateTime) aufgetreten ist.  
>Auf einem freigegebenen Gerät können diese Diagnoseinformationen vom Anmeldeversuch eines anderen Benutzers herrühren.

- **AcquirePrtDiagnostics**: Der Zustand wird auf *PRESENT* festgelegt, wenn die abgerufenen PRT-Diagnoseinformationen in den Protokollen enthalten sind.  
   Dieses Feld wird übersprungen, wenn keine Diagnoseinformationen verfügbar sind.
- **Previous Prt Attempt**: Die Ortszeit in UTC, zu der der fehlerhafte PRT-Versuch erfolgt ist.  
- **Attempt Status**: Der zurückgegebene Clientfehlercode (HRESULT).
- **User Identity**: Der UPN des Benutzers, für den der PRT-Versuch stattgefunden hat.
- **Credential Type**: Die Anmeldeinformationen, die zum Abrufen oder Aktualisieren des PRT verwendet wurden. Gängige Anmeldeinformationstypen: Kennwort und NGC (Next Generation Credential, für Windows Hello).
- **Correlation ID**: Die Korrelations-ID, die vom Server für den fehlerhaften PRT-Versuch gesendet wird.
- **Endpoint URI**: Der vor dem Fehler zuletzt aufgerufene Endpunkt.
- **HTTP Method**: Die HTTP-Methode, die für den Zugriff auf den Endpunkt verwendet wird.
- **HTTP Error**: Der Fehlercode für den WinHttp-Transport. [Hier](/windows/win32/winhttp/error-messages) finden Sie weitere Netzwerkfehlercodes.
- **HTTP Status**: Der vom Endpunkt zurückgegebene HTTP-Status.
- **Server Error Code**: Der vom Server gesendete Fehlercode.  
- **Server Error Description**: Die vom Server gesendete Fehlermeldung.
- **RefreshPrtDiagnostics**: Der Zustand wird auf *PRESENT* festgelegt, wenn die abgerufenen PRT-Diagnoseinformationen in den Protokollen enthalten sind.  
Dieses Feld wird übersprungen, wenn keine Diagnoseinformationen verfügbar sind.
Die Diagnoseinformationsfelder sind mit denen von **AcquirePrtDiagnostics** identisch.


### <a name="sample-sso-state-output"></a>Beispielausgabe für den SSO-Status

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : NO
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
     AcquirePrtDiagnostics : PRESENT
      Previous Prt Attempt : 2020-07-18 20:10:33.789 UTC
            Attempt Status : 0xc000006d
             User Identity : john@contoso.com
           Credential Type : Password
            Correlation ID : 63648321-fc5c-46eb-996e-ed1f3ba7740f
              Endpoint URI : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx/oauth2/token/
               HTTP Method : POST
                HTTP Error : 0x0
               HTTP status : 400
         Server Error Code : invalid_grant
  Server Error Description : AADSTS50126: Error validating credentials due to invalid username or password.
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2019-01-24 19:15:33.000 UTC
   EnterprisePrtExpiryTime : 2019-02-07 19:15:33.000 UTC
    EnterprisePrtAuthority : https://fs.hybridadfs.nttest.microsoft.com:443/adfs

+----------------------------------------------------------------------+
```

## <a name="diagnostics-data"></a>Diagnosedaten

### <a name="pre-join-diagnostics"></a>Diagnose vor dem Join

Dieser Diagnoseabschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden ist und nicht hybrid in Azure AD eingebunden werden kann.

In diesem Abschnitt werden verschiedene Tests durchgeführt, um die Diagnose von Join-Fehlern zu erleichtern. Die Informationen umfassen die Fehlerphase, den Fehlercode, die Serveranforderungs-ID, den HTTP-Status der Serverantwort und die Fehlermeldung der Serverantwort.

- **User Context**: Der Kontext, in dem die Diagnose durchgeführt wird. Mögliche Werte: SYSTEM, UN-ELEVATED User (Benutzer ohne erhöhte Rechte), ELEVATED User (Benutzer mit erhöhten Rechten).

   > [!NOTE]
   > Da die eigentliche Einbindung im SYSTEM-Kontext durchgeführt wird, ist die Ausführung der Diagnose im SYSTEM-Kontext dem tatsächlichen Einbindungsszenario am nächsten. Um die Diagnose im SYSTEM-Kontext auszuführen, muss der Befehl `dsregcmd /status` über eine Befehlszeile mit erweiterten Rechten ausgeführt werden.

- **Client Time**: Die Systemzeit in UTC.
- **AD Connectivity Test**: Dieser Test führt einen Konnektivitätstest für den Domänencontroller durch. Ein Fehler bei diesem Test führt wahrscheinlich zu Einbindungsfehlern in der Vorabprüfungsphase.
- **AD-Konfigurationstest**: Dieser Test liest und prüft, ob das SCP-Objekt (Service Connection Point) in der lokalen Active Directory-Gesamtstruktur ordnungsgemäß konfiguriert ist. Fehler bei diesem Test führen wahrscheinlich zu Einbindungsfehlern mit dem Fehlercode 0x801c001d in der Ermittlungsphase.
- **DRS Discovery Test**: Dieser Test ruft die DRS-Endpunkte vom Endpunkt für Ermittlungsmetadaten ab und führt eine Benutzerbereichsanforderung aus. Fehler bei diesem Test führen wahrscheinlich zu Einbindungsfehlern in der Ermittlungsphase.
- **DRS Connectivity Test**: Dieser Test führt einen grundlegenden Konnektivitätstest für den DRS-Endpunkt aus.
- **Token Acquisition Test**: Dieser Test versucht, ein Azure AD-Authentifizierungstoken abzurufen, wenn der Benutzermandant einem Verbund angehört. Fehler bei diesem Test führen wahrscheinlich zu Einbindungsfehlern in der Authentifizierungsphase. Bei einem Authentifizierungsfehler wird „sync join“ als Fallback versucht, sofern Fallbacks nicht explizit über die folgenden Registrierungsschlüsseleinstellungen deaktiviert werden:

  ```
  Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
  Value: FallbackToSyncJoin
  Type:  REG_DWORD
  Value: 0x0 -> Disabled
  Value: 0x1 -> Enabled
  Default (No Key): Enabled
  ```

- **Fallback to Sync-Join**: Der Zustand wird auf *Enabled* festgelegt, wenn der obige Registrierungsschlüssel *nicht* vorhanden ist, um Fallbacks auf „Sync Join“ bei Authentifizierungsfehlern zu vermeiden. Diese Option ist ab Windows 10 1803 verfügbar.
- **Previous Registration**: Der Zeitpunkt des vorherigen Einbindungsversuchs. Es werden nur fehlerhafte Einbindungsversuche protokolliert.
- **Error Phase**: Die Einbindungsphase, in der der Abbruch erfolgte. Mögliche Werte: *pre-check*, *discover*, *auth* und *join*.
- **Client ErrorCode**: Der zurückgegebene Clientfehlercode (HRESULT).
- **Server ErrorCode**: Der Serverfehlercode, der angezeigt wird, wenn eine Anforderung an den Server gesendet wurde und der Server mit einem Fehlercode geantwortet hat.
- **Server Message**: Die Servermeldung, die zusammen mit dem Fehlercode zurückgegeben wird.
- **Https Status**: Der vom Server zurückgegebene HTTP-Status.
- **Request ID**: Die an den Server gesendete Anforderungs-ID (requestId) des Clients. Die Anforderungs-ID ist nützlich, um Zusammenhänge mit serverseitigen Protokollen herzustellen.

### <a name="sample-pre-join-diagnostics-output"></a>Beispielausgabe für Diagnose vor dem Join

Das folgende Beispiel zeigt einen Diagnosetest mit einem Ermittlungsfehler.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:25:31.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x801c000c]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:23:30.000 UTC
               Error Phase : discover
          Client ErrorCode : 0x801c0021

+----------------------------------------------------------------------+
```

Das folgende Beispiel zeigt, dass Diagnosetests erfolgreich durchgeführt werden, aber beim Registrierungsversuch ein Verzeichnisfehler aufgetreten ist. Dies ist für „sync-join“ ein erwartetes Verhalten. Nachdem der Synchronisierungsauftrag von Azure AD Connect abgeschlossen wurde, kann das Gerät eingebunden werden.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                       |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : SYSTEM
               Client Time : 2019-01-31 09:16:50.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : PASS
     DRS Connectivity Test : PASS
    Token acquisition Test : PASS
     Fallback to Sync-Join : ENABLED

     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnose nach dem Join

In diesem Diagnoseabschnitt wird die Ausgabe von Integritätsprüfungen angezeigt, die für ein in die Cloud eingebundenes Gerät durchgeführt werden.

- **AadRecoveryEnabled**: Wenn der Wert *YES* lautet, sind die im Gerät gespeicherten Schlüssel nicht verwendbar, und das Gerät wird für die Wiederherstellung markiert. Die nächste Anmeldung löst den Wiederherstellungsflow aus und führt zu einer erneuten Registrierung des Geräts.
- **KeySignTest**: Wenn der Wert *PASSED* lautet, sind die Geräteschlüssel fehlerfrei. Wenn bei „KeySignTest“ ein Fehler auftritt, wird das Gerät in der Regel für die Wiederherstellung markiert. Die nächste Anmeldung löst den Wiederherstellungsflow aus und führt zu einer erneuten Registrierung des Geräts. Für hybride, in Azure AD eingebundene Geräte erfolgt die Wiederherstellung im Hintergrund. Wenn die Geräte in Azure AD eingebunden oder bei Azure AD registriert sind, fordern sie zur Benutzerauthentifizierung auf, um das Gerät wiederherzustellen und ggf. neu zu registrieren. 
   > [!NOTE]
   > KeySignTest erfordert erhöhte Berechtigungen.

#### <a name="sample-post-join-diagnostics-output"></a>Beispielausgabe für Diagnose nach dem Join

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisites-check"></a>Überprüfung der NGC-Voraussetzungen

In diesem Diagnoseabschnitt werden die Voraussetzungen für die Einrichtung von Windows Hello for Business (WHFB) überprüft.

> [!NOTE]
> Wenn der Benutzer WHFB bereits erfolgreich konfiguriert hat, werden in `dsregcmd /status` unter Umständen keine Details zur Überprüfung der Voraussetzungen angezeigt.

- **IsDeviceJoined**: Der Zustand wird auf *YES* festgelegt, wenn das Gerät in Azure AD eingebunden ist.
- **IsUserAzureAD**: Der Zustand wird auf *YES* festgelegt, wenn der angemeldete Benutzer in Azure AD enthalten ist.
- **PolicyEnabled**: Der Zustand wird auf *YES* festgelegt, wenn die WHFB-Richtlinie auf dem Gerät aktiviert ist.
- **PostLogonEnabled**: Der Zustand wird auf *YES* festgelegt, wenn die WHFB-Registrierung nativ durch die Plattform ausgelöst wird. Wenn der Zustand auf *NO* festgelegt ist, weist dies darauf hin, dass die Registrierung für Windows Hello for Business durch einen benutzerdefinierten Mechanismus ausgelöst wird.
- **DeviceEligible**: Der Zustand wird auf *YES* festgelegt, wenn das Gerät die Hardwareanforderungen für die WHFB-Registrierung erfüllt.
- **SessionIsNotRemote**: Der Zustand wird auf *YES* festgelegt, wenn der aktuelle Benutzer nicht remote, sondern direkt beim Gerät angemeldet ist.
- **CertEnrollment**: Diese Einstellung ist spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung und gibt die Zertifikatregistrierungsstelle für WHFB an. Der Zustand wird auf *enrollment authority* festgelegt, wenn es sich bei der Quelle der WHFB-Richtlinie um eine Gruppenrichtlinie handelt, bzw. auf *mobile device management*, wenn es sich bei der Quelle um die Verwaltung mobiler Geräte handelt. Wenn keine der beiden Quellen zutrifft, wird der Zustand auf *none* festgelegt.
- **AdfsRefreshToken**: Diese Einstellung ist spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung und nur vorhanden, wenn der CertEnrollment-Zustand auf *enrollment authority* festgelegt ist. Die Einstellung gibt an, ob das Gerät über ein Unternehmens-PRT für den Benutzer verfügt.
- **AdfsRaIsReady**: Diese Einstellung ist spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung und nur vorhanden, wenn der CertEnrollment-Zustand auf *enrollment authority* festgelegt ist. Der Zustand wird auf *YES* festgelegt, wenn AD FS in Ermittlungsmetadaten angibt, dass WHFB unterstützt wird *und* die Anmeldezertifikatvorlage verfügbar ist.
- **LogonCertTemplateReady**: Diese Einstellung ist spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung und nur vorhanden, wenn der CertEnrollment-Zustand auf *enrollment authority* festgelegt ist. Der Zustand wird auf *YES* festgelegt, wenn der Zustand der Anmeldezertifikatvorlage gültig ist und zur Problembehandlung der AD FS-Registrierungsstelle beiträgt.
- **PreReqResult**: Gibt das Gesamtergebnis der Auswertung der WHFB-Voraussetzungen an. Der Zustand wird auf *Will Provision* festgelegt, wenn die WHFB-Registrierung bei der nächsten Benutzeranmeldung als Aufgabe nach der Anmeldung gestartet wird.

### <a name="sample-ngc-prerequisites-check-output"></a>Beispielausgabe für die Überprüfung der NGC-Voraussetzungen

```
+----------------------------------------------------------------------+
| Ngc Prerequisite Check                                               |
+----------------------------------------------------------------------+

            IsDeviceJoined : YES
             IsUserAzureAD : YES
             PolicyEnabled : YES
          PostLogonEnabled : YES
            DeviceEligible : YES
        SessionIsNotRemote : YES
            CertEnrollment : enrollment authority
          AdfsRefreshToken : YES
             AdfsRaIsReady : YES
    LogonCertTemplateReady : YES ( StateReady )
              PreReqResult : WillProvision
+----------------------------------------------------------------------+
```

## <a name="next-steps"></a>Nächste Schritte

Wechseln Sie zum [Microsoft-Fehlersuchtool](/windows/win32/debug/system-error-code-lookup-tool).
