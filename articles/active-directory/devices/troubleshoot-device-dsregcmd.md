---
title: Problembehandlung mit dem Befehl „dsregcmd“ – Azure Active Directory
description: Verwenden der Ausgabe von „dsregcmd“, um den Zustand von Geräten in Azure AD zu verstehen
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff1c0d1e552ad26832b2c142f5ca1506654a9a0c
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219536"
---
# <a name="troubleshooting-devices-using-the-dsregcmd-command"></a>Problembehandlung von Geräten mit dem Befehl „dsregcmd“

Das Hilfsprogramm „dsregcmd /status“ muss als Domänenbenutzerkonto ausgeführt werden.

## <a name="device-state"></a>Gerätestatus

In diesem Abschnitt werden die Statusparameter für den Geräte-Join aufgelistet. In der folgenden Tabelle sind die Kriterien für das Gerät in verschiedenen Join-Zuständen aufgeführt.

| AzureAdJoined | EnterpriseJoined | DomainJoined | Gerätestatus |
| ---   | ---   | ---   | ---   |
| YES | Nein | Nein | In Azure AD eingebunden |
| Nein | Nein | YES | In die Domäne eingebunden |
| YES | Nein | YES | In Hybrid-AD eingebunden |
| Nein | YES | YES | In lokales DRS eingebunden |

> [!NOTE]
> Der Status „Workplace Join“ (bei Azure AD registered) wird im Abschnitt „Benutzerstatus“ angezeigt.

- **AzureAdJoined:** Auf „JA“ festlegen, wenn das Gerät in Azure AD eingebunden ist. Andernfalls „NEIN“.
- **EnterpriseJoined:** Auf „JA“ festlegen, wenn das Gerät in ein lokales DRS eingebunden ist. Für ein Gerät kann nicht gleichzeitig „EnterpriseJoined“ und „AzureAdJoined“ aktiviert sein.
- **DomainJoined:** Auf „JA“ festlegen, wenn das Gerät in eine Domäne (AD) eingebunden ist.
- **DomainName:** Auf den Namen der Domäne festlegen, wenn das Gerät in eine Domäne eingebunden ist.

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

Wird nur angezeigt, wenn das Gerät in Azure AD oder Azure AD Hybrid eingebunden ist (nicht bei Azure AD registriert). In diesem Abschnitt werden die in Azure AD gespeicherten Gerätedetails aufgelistet.

- **DeviceId:** Eindeutige ID des Geräts im Azure AD-Mandanten.
- **Thumbprint:** Fingerabdruck des Gerätezertifikats.
- **DeviceCertificateValidity:** Gültigkeit des Gerätezertifikats.
- **KeyContainerId:** Container-ID des privaten Schlüssels des Geräts, der dem Gerätezertifikat zugeordnet ist.
- **KeyProvider:** KeyProvider (Hardware/Software), der zum Speichern des privaten Schlüssels des Geräts verwendet wird.
- **TpmProtected:** „JA“, wenn der private Schlüssel des Geräts in einem Hardware-TPM gespeichert ist.

> [!NOTE]
> Das Feld **DeviceAuthStatus** wurde in **Windows 10 Update vom 10. Mai 2021 (Version 21H1)** hinzugefügt.

- **DeviceAuthStatus:** Führt eine Überprüfung aus, um die Integrität des Geräts in Azure AD zu bestimmen.  
„SUCCESS“, wenn das Gerät in Azure AD vorhanden und aktiviert ist  
„FAILED. Device is either disabled or deleted.“, wenn das Gerät entweder deaktiviert oder gelöscht ist, [weitere Informationen](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-devices)  
„FAILED. ERROR“, wenn der Test nicht ausgeführt werden konnte. Für diesen Test ist eine Netzwerkverbindung zu Azure AD erforderlich.  

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

Wird nur angezeigt, wenn das Gerät in Azure AD oder Azure AD Hybrid eingebunden ist (nicht bei Azure AD registriert). In diesem Abschnitt werden die allgemeinen Mandantendetails aufgelistet, wenn ein Gerät in Azure AD eingebunden wird.

> [!NOTE]
> Wenn die MDM-URLs in diesem Abschnitt leer sind, wird angezeigt, dass die MDM entweder nicht konfiguriert wurde oder sich der aktuelle Benutzer nicht im Bereich der MDM-Registrierung befindet. Überprüfen Sie die Mobilitätseinstellungen in Azure AD, um Ihre MDM-Konfiguration zu überprüfen.

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

In diesem Abschnitt wird der Status verschiedener Attribute für den aktuell am Gerät angemeldeten Benutzer aufgelistet.

> [!NOTE]
> Der Befehl muss in einem Benutzerkontext ausgeführt werden, um einen gültigen Status abzurufen.

- **NgcSet:** Auf „JA“ festlegen, wenn für den derzeit angemeldeten Benutzer eine Windows Hello-Taste eingestellt ist.
- **NgcKeyId:** ID der Windows Hello-Taste, wenn diese für den derzeit angemeldeten Benutzer eingestellt ist.
- **CanReset:** Gibt an, ob die Windows Hello-Taste vom Benutzer zurückgesetzt werden kann.
- **Mögliche Werte:** „DestructiveOnly“, „NonDestructiveOnly“, „DestructiveAndNonDestructive“ oder „Unknown“ bei einem Fehler.
- **WorkplaceJoined:** Auf „JA“ festlegen, wenn für Azure AD registrierte Konten im aktuellen NTUSER-Kontext zum Gerät hinzugefügt wurden.
- **WamDefaultSet:** Auf „JA“ festlegen, wenn für den angemeldeten Benutzer ein standardmäßiges WAM-WebAccount erstellt wird. In diesem Feld wird u.U. ein Fehler angezeigt, wenn „dsregcmd /status“ von einer Eingabeaufforderung mit erhöhten Rechten ausgeführt wird.
- **WamDefaultAuthority:** Für Azure AD auf „organizations“ (Organisationen) festgelegt.
- **WamDefaultId:** Immer „https://login.microsoft.com“ für Azure AD.
- **WamDefaultGUID:** Die GUID des WAM-Anbieters (Azure AD/Microsoft-Konto) für das standardmäßige WAM-WebAccount.

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

Dieser Abschnitt kann für in Azure AD registrierte Geräte ignoriert werden.

> [!NOTE]
> Der Befehl muss in einem Benutzerkontext ausgeführt werden, um einen gültigen Status für diesen Benutzer abzurufen.

- **AzureAdPrt:** Auf „JA“ festlegen, wenn auf dem Gerät für den angemeldeten Benutzer ein PRT vorhanden ist.
- **AzureAdPrtUpdateTime:** Wird auf die Zeit in UTC festgelegt, zu der das PRT zuletzt aktualisiert wurde.
- **AzureAdPrtExpiryTime:** Wird auf die Zeit in UTC festgelegt, wann das PRT abläuft, wenn es nicht erneuert wird.
- **AzureAdPrtAuthority:** Autoritäts-URL von Azure AD.
- **EnterprisePrt:** Auf „JA“ festlegen, wenn das Gerät über ein PRT von einem lokalen ADFS verfügt. Für Azure AD Hybrid eingebundene Geräte könnte das Gerät gleichzeitig ein PRT von Azure AD und lokalem AD erhalten. Lokal eingebundene Geräte verfügen nur über ein Enterprise PRT.
- **EnterprisePrtUpdateTime:** Wird auf die Zeit in UTC festgelegt, zu der das Enterprise PRT zuletzt aktualisiert wurde.
- **EnterprisePrtExpiryTime:** Wird auf die Zeit in UTC festgelegt, wann das PRT abläuft, wenn es nicht erneuert wird.
- **EnterprisePrtAuthority:** Autoritäts-URL für ADFS.

>[!NOTE]
> Die folgenden PRT-Diagnosefelder wurden in **Windows 10 Update vom 10. Mai 2021 (Version 21H1)** hinzugefügt.

>[!NOTE]
> Diagnoseinformationen, die im Feld **AzureAdPrt** angezeigt werden, gelten für AzureAD PRT-Erwerb/-Aktualisierung und Diagnoseinformationen, die unter **EnterprisePrt** bzw. für Enterprise PRT-Abruf/-Aktualisierung angezeigt werden.

>[!NOTE]
>Diagnoseinformationen werden nur angezeigt, wenn der Abruf-/Aktualisierungsfehler nach der letzten erfolgreichen PRT-Updatezeit (AzureAdPrtUpdateTime/EnterprisePrtUpdateTime) erfolgt ist.  
>Auf einem freigegebenen Gerät können diese Diagnoseinformationen vom Anmeldeversuch eines anderen Benutzers stammen.

- **AcquirePrtDiagnostics:** Legen Sie diese Einstellung auf „PRESENT“ fest, wenn in den Protokollen Abruf-PRT-Diagnoseinformationen vorhanden sind.  
Dieses Feld wird übersprungen, wenn keine Diagnoseinformationen verfügbar sind.
- **Previos Prt Attempt:** Ortszeit in UTC, zu der der fehlgeschlagene PRT-Versuch aufgetreten ist  
- **Attempt Status:** Der zurückgegebene Clientfehlercode (HRESULT)
- **User Identity:** UPN des Benutzers, für den der PRT-Versuch stattgefunden hat
- **Credential Type:** Anmeldeinformationen, die zum Abrufen/Aktualisieren von PRT verwendet wurden Gängige Anmeldeinformationstypen sind Password und NGC (Windows Hello).
- **Correlation ID:** Korrelations-ID, die vom Server für den fehlgeschlagenen PRT-Versuch gesendet wurde
- **Endpoint URI:** Letzter Zugriff auf den Endpunkt vor dem Fehler
- **HTTP Method:** HTTP-Methode, die für den Zugriff auf den Endpunkt verwendet wurde
- **HTTP-Fehler:** WinHttp-Transportfehlercode WinHttp-Fehler finden Sie [hier](/windows/win32/winhttp/error-messages).
- **HTTP Status:** Der vom Endpunkt zurückgegebene HTTP-Status
- **Server Error Code:** Fehlercode vom Server  
- **Server Error Description:** Fehlermeldung vom Server
- **RefreshPrtDiagnostics:** Legen Sie diese Einstellung auf „PRESENT“ fest, wenn in den Protokollen Abruf-PRT-Diagnoseinformationen vorhanden sind.  
Dieses Feld wird übersprungen, wenn keine Diagnoseinformationen verfügbar sind.
Die Diagnoseinformationsfelder sind identisch mit **AcquirePrtDiagnostics**.


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

## <a name="diagnostic-data"></a>Diagnosedaten

### <a name="pre-join-diagnostics"></a>Diagnose vor dem Join

Dieser Abschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden ist und nicht in Azure AD Hybrid eingebunden werden kann.

In diesem Abschnitt werden verschiedene Tests durchgeführt, um die Diagnose von Join-Fehlern zu erleichtern. Dieser Abschnitt enthält auch die Details des vorherigen (?). Diese Informationen umfassen die Fehlerphase, den Fehlercode, die Serveranfrage-ID, den HTTP-Status der Serverantwort und die Fehlermeldung der Serverantwort.

- **Benutzerkontext:** Der Kontext, in dem die Diagnose durchgeführt wird. Mögliche Werte: SYSTEM, UN-ELEVATED User (Benutzer ohne erhöhte Rechte), ELEVATED User (Benutzer mit erhöhten Rechten).

   > [!NOTE]
   > Da der eigentliche Join im SYSTEM-Kontext durchgeführt wird, ist die Ausführung der Diagnose im SYSTEM-Kontext dem tatsächlichen Join-Szenario am nächsten. Um die Diagnose im SYSTEM-Kontext auszuführen, muss der Befehl „dsregcmd /status“ über eine Befehlszeile mit erhöhten Rechten ausgeführt werden.

- **Client Time (Clientzeit):** Die Systemzeit in UTC.
- **AD Connectivity Test (AD-Verbindungstest):** Der Test führt einen Verbindungstest mit dem Domänencontroller durch. Ein Fehler in diesem Test wird wahrscheinlich zu Join-Fehlern in der Vorabprüfungsphase führen.
- **AD Configuration Test (AD-Konfigurationstest):** Der Test liest und überprüft, ob das SCP-Objekt in der lokalen AD-Gesamtstruktur ordnungsgemäß konfiguriert ist. Fehler in diesem Test würden wahrscheinlich zu Join-Fehlern mit dem Fehlercode 0x801c001d in der Ermittlungsphase führen.
- **DRS Discovery Test (DRS-Ermittlungstest):** Der Test ruft die DRS-Endpunkte vom Endpunkt für die Ermittlung von Metadaten ab und führt eine Benutzerbereichsanforderung aus. Fehler in diesem Test würden wahrscheinlich zu Join-Fehlern in der Ermittlungsphase führen.
- **DRS Connectivity Test (DRS-Verbindungstest):** Der Test führt einen grundlegenden Verbindungstest mit dem DRS-Endpunkt aus.
- **Token acquisition Test (Tokenbeschaffungstest):** Der Test versucht, ein Azure AD-Authentifizierungstoken abzurufen, wenn der Benutzermandant einem Verbund hinzugefügt wird. Fehler in diesem Test würden wahrscheinlich zu Join-Fehlern in der Authentifizierungsphase führen. Wenn bei der Authentifizierung ein Fehler auftritt, wird „sync join“ als Fallback versucht, sofern der Fallback nicht explizit mit den folgenden Registrierungsschlüsseleinstellungen deaktiviert wird.

```
Keyname: Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ
Value: FallbackToSyncJoin
Type:  REG_DWORD
Value: 0x0 -> Disabled
Value: 0x1 -> Enabled
Default (No Key): Enabled
```

- **Fallback zu Sync-Join:** Auf „Aktiviert“ festlegen, wenn der obige Registrierungsschlüssel NICHT vorhanden ist, um den Fallback zu „Sync Join“ mit Authentifizierungsfehlern zu vermeiden. Diese Option ist ab Windows 10 1803 verfügbar.
- **Previous Registration (Vorherige Registrierung):** Zeitpunkt des vorherigen Join-Versuchs. Es werden nur fehlerhafte Join-Versuche protokolliert.
- **Error Phase (Fehlerphase):** Die Join-Phase, in der der Abbruch erfolgte. Mögliche Werte sind „pre-check“, „discover“, „auth“, „join“.
- **Client ErrorCode (Clientfehlercode):** Der zurückgegebene Clientfehlercode (HRESULT).
- **Server ErrorCode (Serverfehlercode):** Serverfehlercode, wenn eine Anforderung an den Server gesendet wurde und der Server mit einem Fehlercode geantwortet hat.
- **Server Message (Servermeldung):** Servermeldung, die zusammen mit dem Fehlercode zurückgegeben wird.
- **Https Status:** Der vom Server zurückgegebene HTTP-Status.
- **Request ID (Anforderungs-ID):** Die an den Server gesendete Clientanforderungs-ID. Praktisch zum Korrelieren mit serverseitigen Protokollen.

### <a name="sample-pre-join-diagnostics-output"></a>Beispielausgabe für Diagnose vor dem Join

Das folgende Beispiel zeigt, dass der Diagnosetest mit einem Ermittlungsfehler fehlschlägt.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
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

Das folgende Beispiel zeigt, dass Diagnosetests erfolgreich durchgeführt werden, aber beim Registrierungsversuch ein Verzeichnisfehler aufgetreten ist, der für „sync join“ erwartet wird. Nachdem der Synchronisierungsauftrag von Azure AD Connect abgeschlossen ist, kann das Gerät eingebunden werden.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
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
            Server Message : The device object by the given id (e92325d0-7ac4-4714-88a1-94ae875d5245) is not found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e

+----------------------------------------------------------------------+
```

### <a name="post-join-diagnostics"></a>Diagnose nach dem Join

In diesem Abschnitt wird die Ausgabe von Integritätsprüfungen angezeigt, die für ein in die Cloud eingebundenes Gerät durchgeführt wurden.

- **AadRecoveryEnabled:** Bei „JA“ sind die im Gerät gespeicherten Schlüssel nicht verwendbar, und das Gerät ist für die Wiederherstellung markiert. Die nächste Anmeldung löst den Wiederherstellungsflow aus und führt zu einer erneuten Registrierung des Geräts.
- **KeySignTest:** Bei „PASSED“ (Erfolgreich) ist die Integrität der Geräteschlüssel nicht beeinträchtigt. Wenn bei „KeySignTest“ ein Fehler auftritt, wird das Gerät in der Regel zur Wiederherstellung markiert. Die nächste Anmeldung löst den Wiederherstellungsflow aus und führt zu einer erneuten Registrierung des Geräts. Für in Azure AD Hybrid eingebundene Geräte erfolgt die Wiederherstellung im Hintergrund. Obwohl sie in Azure AD eingebunden oder in Azure AD registriert sind, fordern die Geräte zur Benutzerauthentifizierung auf, um das Gerät wiederherzustellen und ggf. neu zu registrieren. **KeySignTest erfordert erhöhte Berechtigungen.**

#### <a name="sample-post-join-diagnostics-output"></a>Beispielausgabe für Diagnose nach dem Join

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

         AadRecoveryEnabled: NO
               KeySignTest : PASSED
+----------------------------------------------------------------------+
```

## <a name="ngc-prerequisite-check"></a>Überprüfung der NGC-Voraussetzung

In diesem Abschnitt werden die Voraussetzungen für die Bereitstellung von Windows Hello for Business (WHFB) überprüft.

> [!NOTE]
> Wenn der Benutzer WHFB bereits erfolgreich konfiguriert hat, werden in „dsregcmd/status“ möglicherweise keine Details zur Überprüfung der NGC-Voraussetzung angezeigt.

- **IsDeviceJoined:** „YES“ (JA), wenn das Gerät in Azure AD eingebunden ist.
- **IsUserAzureAD:** „YES“ (JA), wenn der angemeldete Benutzer in Azure AD vorhanden ist
- **PolicyEnabled:** „YES“ (JA), wenn die WHFB-Richtlinie auf dem Gerät aktiviert ist.
- **PostLogonEnabled:** „YES“ (JA), wenn die WHFB-Registrierung nativ durch die Plattform ausgelöst wird. „NO“ (NEIN) gibt an, dass die Registrierung für Windows Hello for Business durch einen benutzerdefinierten Mechanismus ausgelöst wird.
- **DeviceEligible:** „YES“ (JA), wenn das Gerät die Hardwareanforderungen für die WHFB-Registrierung erfüllt.
- **SessionIsNotRemote:** „YES“ (JA), wenn der Benutzer nicht remote, sondern direkt bei dem Gerät angemeldet ist.
- **CertEnrollment:** Spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung. Gibt die Zertifikatregistrierungsstelle für WHFB an. Festgelegt auf „enrollment authority“ (Registrierungsstelle), wenn es sich bei der Quelle der WHFB-Richtlinie um eine Gruppenrichtlinie handelt, oder auf „mobile device management“ (mobile Geräteverwaltung), wenn es sich bei der Quelle um die mobile Geräteverwaltung handelt. Andernfalls „none“ (keine).
- **AdfsRefreshToken:** Spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung. Nur vorhanden, wenn „CertEnrollment“ den Wert „enrollment authority“ (Registrierungsstelle) hat. Gibt an, ob das Gerät über ein Enterprise PRT für den Benutzer verfügt.
- **AdfsRaIsReady:** Spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung.  Nur vorhanden, wenn „CertEnrollment“ den Wert „enrollment authority“ (Registrierungsstelle) hat. „YES“ (JA), wenn in Ermittlungsmetadaten von ADFS angegeben wurde, dass WHFB unterstützt wird, *und* wenn eine Anmeldezertifikatvorlage verfügbar ist.
- **LogonCertTemplateReady:** Spezifisch für die Bereitstellung der WHFB-Zertifikatvertrauensstellung. Nur vorhanden, wenn „CertEnrollment“ den Wert „enrollment authority“ (Registrierungsstelle) hat. „YES“ (JA), wenn der Zustand der Anmeldezertifikatvorlage gültig ist. Hilfreich beim Behandeln von Problemen mit der ADFS-Registrierungsstelle.
- **PreReqResult:** Gibt das Gesamtergebnis der Auswertung der WHFB-Voraussetzungen an. „Will Provision“ (Wird bereitgestellt), wenn die WHFB-Registrierung nach der nächsten Benutzeranmeldung gestartet wird.

### <a name="sample-ngc-prerequisite-check-output"></a>Beispielausgabe für die Überprüfung der NGC-Voraussetzung

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

- [Das Microsoft-Fehlersuchtool](/windows/win32/debug/system-error-code-lookup-tool)
