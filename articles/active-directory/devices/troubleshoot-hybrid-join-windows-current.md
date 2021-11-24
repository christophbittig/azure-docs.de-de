---
title: Beheben von Problemen bei in Azure Active Directory eingebundenen Hybridgeräten
description: Dieser Artikel hilft Ihnen beim Beheben von Problemen bei in Azure Active Directory eingebundenen Windows&nbsp;10- und Windows Server 2016-Hybridgeräten.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f5320812be623437099ed0ab587dbbe3d4abe6ef
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158755"
---
# <a name="troubleshoot-hybrid-azure-ad-joined-devices"></a>Beheben von Problemen bei in Azure Active Directory eingebundenen Hybridgeräten

Dieser Artikel enthält Anleitungen zur Problembehandlung, mit deren Hilfe Sie potenzielle Probleme bei Geräten beheben können, auf denen Windows&nbsp;10 oder Windows Server 2016 ausgeführt wird.

Die Azure Active Directory (Azure AD)-Hybrideinbindung unterstützt das Windows&nbsp;10-Update vom 10. November 2015 und höher.

Informationen zur Problembehandlung bei anderen Windows-Clients finden Sie unter [Beheben von Problemen bei kompatiblen Azure AD-Hybridgeräten](troubleshoot-hybrid-join-windows-legacy.md).

In diesem Artikel wird vorausgesetzt, dass Sie [in Azure AD eingebundene Hybridgeräte konfiguriert haben](hybrid-azuread-join-plan.md), um die folgenden Szenarios zu unterstützen:

- Gerätebasierter bedingter Zugriff
- [Enterprise State Roaming](./enterprise-state-roaming-overview.md)
- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification)


> [!NOTE] 
> Zur Behebung der häufigsten Probleme bei der Geräteregistrierung verwenden Sie das [Device Registration Troubleshooter Tool](https://aka.ms/DSRegTool).


## <a name="troubleshoot-join-failures"></a>Beheben von Einbindungsfehlern

### <a name="step-1-retrieve-the-join-status"></a>Schritt 1: Abrufen des Beitrittsstatus

1. Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator.
1. Geben Sie `dsregcmd /status`ein.

```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-xxxx-xxxx-xxxx-2d7cd011xxxx
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```

### <a name="step-2-evaluate-the-join-status"></a>Schritt 2: Bewerten des Beitrittsstatus

Überprüfen Sie die Felder in der folgenden Tabelle, und vergewissern Sie sich, dass sie die erwarteten Werte enthalten:

| Feld | Erwarteter Wert | Beschreibung |
| --- | --- | --- |
| DomainJoined | YES | Dieses Feld gibt an, ob das Gerät in ein lokales Active Directory eingebunden ist. <br><br>Wenn der Wert *NO* lautet, kann das Gerät keine Azure AD-Hybrideinbindung durchführen. |
| WorkplaceJoined | Nein | Dieses Feld gibt an, ob das Gerät bei Azure AD als privates Gerät registriert ist (markiert als *Workplace Join*). Dieser Wert sollte für in eine Domäne eingebundene Computer mit zusätzlicher Azure AD-Hybrideinbindung *NO* lauten. <br><br>Wenn der Wert *YES* lautet, wurde vor Abschluss der Azure AD-Hybrideinbindung ein Geschäfts-, Schul- oder Unikonto hinzugefügt. In diesem Fall wird das Konto bei Verwendung von Windows&nbsp;10, Version 1607 oder höher, ignoriert. |
| AzureAdJoined | YES | Dieses Feld gibt an, ob das Gerät eingebunden ist. Der Wert lautet *YES*, wenn das Gerät entweder ein in Azure AD eingebundenes Gerät oder ein in Azure AD eingebundenes Hybridgerät ist. <br><br>Wenn der Wert *NO* lautet, wurde die Einbindung in Azure AD noch nicht abgeschlossen. |
|  |  |

Setzen Sie den Vorgang mit den nächsten Schritten zur weiteren Problembehandlung fort.

### <a name="step-3-find-the-phase-in-which-the-join-failed-and-the-error-code"></a>Schritt  3: Suchen der Phase, in der die Einbindung fehlgeschlagen ist, und des zugehörigen Fehlercodes

**Bei Windows&nbsp;10, Version 1803 oder höher**

Suchen Sie im Abschnitt „Diagnosedaten“ der Statusausgabe zur Einbindung nach dem Unterabschnitt „Vorherige Registrierung“. Dieser Abschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden und seine Azure AD-Hybrideinbindung nicht möglich ist.

Das Feld „Fehlerphase“ bezeichnet die Phase, in der der Einbindungsfehler aufgetreten ist, während „Client-Fehlercode“ den Fehlercode des Einbindungsvorgangs angibt.

```
+----------------------------------------------------------------------+
     Previous Registration : 2019-01-31 09:16:43.000 UTC
         Registration Type : sync
               Error Phase : join
          Client ErrorCode : 0x801c03f2
          Server ErrorCode : DirectoryError
            Server Message : The device object by the given id (e92325d0-xxxx-xxxx-xxxx-94ae875d5245) isn't found.
              Https Status : 400
                Request Id : 6bff0bd9-820b-484b-ab20-2a4f7b76c58e
+----------------------------------------------------------------------+
```

**Bei früheren Windows&nbsp;10-Versionen**

Verwenden Sie die Protokolle der Ereignisanzeige, um die Phase und den Fehlercode für die Einbindungsfehler zu ermitteln.

1. Öffnen Sie in der Ereignisanzeige die Ereignisprotokolle zur **Benutzergeräteregistrierung**. Sie sind unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung** gespeichert.
1. Suchen Sie nach Ereignissen mit den folgenden Ereignis-IDs: 304, 305 und 307.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/1.png" alt-text="Screenshot der Ereignisanzeige mit ausgewählter Ereignis-ID 304, den zugehörigen Informationen sowie dem Fehlercode und der Phase, die hervorgehoben sind." border="false":::

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/2.png" alt-text="Screenshot der Ereignisanzeige mit ausgewählter Ereignis-ID 305, den zugehörigen Informationen sowie dem hervorgehobenen Fehlercode." border="false":::

### <a name="step-4-check-for-possible-causes-and-resolutions"></a>Schritt 4: Überprüfen auf mögliche Ursachen und Lösungen

#### <a name="pre-check-phase"></a>Vorabprüfungsphase

Mögliche Fehlerursachen:

- Das Gerät hat keine Sichtverbindung mit dem Domänencontroller.
   - Das Gerät muss sich im internen Netzwerk der Organisation oder in einem virtuellen privaten Netzwerk (VPN) mit Netzwerksichtverbindung zu einem lokalen Active Directory-Domänencontroller befinden.

#### <a name="discover-phase"></a>Ermittlungsphase

Mögliche Fehlerursachen:

-  Das Dienstverbindungspunkt-Objekt ist falsch konfiguriert oder kann vom Domänencontroller nicht gelesen werden.
   - In der AD-Gesamtstruktur, zu der das Gerät gehört, ist ein gültiges Dienstverbindungspunkt-Objekt erforderlich, das auf einen verifizierten Domänennamen in Azure AD verweist.
   - Weitere Informationen finden Sie im [Tutorial: Konfigurieren der Azure Active Directory-Hybrideinbindung für Verbunddomänen](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join) im Abschnitt „Konfigurieren eines Dienstverbindungspunkts“.
- Fehler beim Herstellen einer Verbindung mit dem und beim Abrufen der Ermittlungsmetadaten vom Ermittlungsendpunkt.
   - Das Gerät sollte im Systemkontext auf `https://enterpriseregistration.windows.net` zugreifen können, um die Endpunkte für Registrierung und Autorisierung zu ermitteln.
   - Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass das Computerkonto des Geräts in der Lage ist, den Proxy zu ermitteln und sich im Hintergrund zu authentifizieren.
- Fehler beim Herstellen einer Verbindung mit dem Benutzerbereichsendpunkt und beim Ausführen der Bereichsermittlung (nur Windows&nbsp;10, Version 1809 und höher).
   - Das Gerät sollte im Systemkontext auf `https://login.microsoftonline.com` zugreifen können, um eine Bereichsermittlung für die verifizierte Domäne durchführen und den Domänentyp („verwaltet“ oder „Verbund“) bestimmen zu können.
   - Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass der Systemkontext auf dem Gerät den Proxy ermitteln und sich im Hintergrund bei ihm authentifizieren kann.

**Häufige Fehlercodes:**

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | Das Dienstverbindungsendpunkt-Objekt (Service Connection Point-Objekt, SCP-Objekt) kann nicht gelesen und die Azure AD-Mandanteninformationen können nicht abgerufen werden. | Informationen dazu finden Sie im Abschnitt [Konfigurieren eines Dienstverbindungspunkts](hybrid-azuread-join-federated-domains.md#configure-hybrid-azure-ad-join). |
| **DSREG_AUTOJOIN_DISC_FAILED** (0x801c0021/-2145648607) | Allgemeiner Ermittlungsfehler. Fehler beim Abrufen der Ermittlungsmetadaten aus dem Datenreplikationsdienst (Data Replication Service, DRS). | Wenn Sie dies weiter untersuchen möchten, suchen Sie den Unterfehler in den nächsten Abschnitten. |
| **DSREG_AUTOJOIN_DISC_WAIT_TIMEOUT**  (0x801c001f/-2145648609) | Beim Ausführen der Ermittlung ist eine Zeitüberschreitung aufgetreten. | Sorgen Sie dafür, dass im Systemkontext auf `https://enterpriseregistration.windows.net` zugegriffen werden kann. Weitere Informationen dazu finden Sie im Abschnitt [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **DSREG_AUTOJOIN_USERREALM_DISCOVERY_FAILED** (0x801c003d/-2145648579) | Allgemeiner Fehler bei der Bereichsermittlung. Der Domänentyp (verwaltet/Verbund) konnte vom Sicherheitstokendienst (STS) nicht bestimmt werden. | Wenn Sie dies weiter untersuchen möchten, suchen Sie den Unterfehler in den nächsten Abschnitten. |
| | |

**Häufige Unterfehlercodes:**

Mit einer der folgenden Methoden, können Sie den Unterfehlercode für den Code des Ermittlungsfehlers finden.

##### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10, Version 1803 oder höher

Suchen Sie im Abschnitt „Diagnosedaten“ der Statusausgabe zur Einbindung nach „DRS-Ermittlungstest“. Dieser Abschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden und seine Azure AD-Hybrideinbindung nicht möglich ist.

```
+----------------------------------------------------------------------+
| Diagnostic Data                                                      |
+----------------------------------------------------------------------+

     Diagnostics Reference : www.microsoft.com/aadjerrors
              User Context : UN-ELEVATED User
               Client Time : 2019-06-05 08:25:29.000 UTC
      AD Connectivity Test : PASS
     AD Configuration Test : PASS
        DRS Discovery Test : FAIL [0x801c0021/0x80072ee2]
     DRS Connectivity Test : SKIPPED
    Token acquisition Test : SKIPPED
     Fallback to Sync-Join : ENABLED

+----------------------------------------------------------------------+
```

##### <a name="earlier-windowsnbsp10-versions"></a>Frühere Windows&nbsp;10-Versionen

Suchen Sie in den Protokollen der Ereignisanzeige nach der Phase und dem Fehlercode für die Einbindungsfehler.

1. Öffnen Sie in der Ereignisanzeige die Ereignisprotokolle zur **Benutzergeräteregistrierung**. Sie sind unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung** gespeichert.
1. Suchen Sie nach der Ereignis-ID 201.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/5.png" alt-text="Screenshot der Ereignisanzeige mit ausgewählter Ereignis-ID 201, den zugehörigen Informationen sowie dem hervorgehobenen Fehlercode." border="false":::

**Netzwerkfehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **WININET_E_CANNOT_CONNECT** (0x80072efd/-2147012867) | Verbindung mit dem Server konnte nicht hergestellt werden. | Stellen Sie die Netzwerkverbindung zu den erforderlichen Microsoft-Ressourcen sicher. Weitere Informationen finden Sie unter [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | Allgemeine Netzwerkzeitüberschreitung. | Stellen Sie die Netzwerkverbindung zu den erforderlichen Microsoft-Ressourcen sicher. Weitere Informationen finden Sie unter [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **WININET_E_DECODING_FAILED** (0x80072f8f/-2147012721) | Der Netzwerkstapel konnte die Antwort des Servers nicht entschlüsseln. | Sorgen Sie dafür, dass der Netzwerkproxy keine Beeinträchtigung darstellt und die Serverantwort nicht ändert. |
| | |


**HTTP-Fehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **DSREG_DISCOVERY_TENANT_NOT_FOUND** (0x801c003a/-2145648582) | Das Dienstverbindungspunkt-Objekt wurde mit der falschen Mandanten-ID konfiguriert, oder im Mandanten wurden keine aktiven Abonnements gefunden. | Sorgen Sie dafür, dass das Dienstverbindungspunkt-Objekt mit der richtigen Azure AD-Mandanten-ID und den richtigen aktiven Abonnements konfiguriert wird oder dass der Dienst im Mandanten vorhanden ist. |
| **DSREG_SERVER_BUSY** (0x801c0025/-2145648603) | HTTP 503 vom DRS-Server. | Der Server ist zurzeit nicht verfügbar. Zukünftige Einbindungsversuche sind wahrscheinlich erfolgreich, nachdem der Server wieder online ist. |
| | |


**Sonstige Fehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **E_INVALIDDATA** (0x8007000d/-2147024883) | Der JSON-Code für die Serverantwort konnte nicht analysiert werden, weil der Proxy wahrscheinlich HTTP 200 mit einer HTML-Autorisierungsseite zurückgibt. | Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass der Systemkontext auf dem Gerät den Proxy ermitteln und sich im Hintergrund bei ihm authentifizieren kann. |
| | |


#### <a name="authentication-phase"></a>Authentifizierungsphase

Dieser Inhalt gilt nur für Verbunddomänenkonten.

Fehlerursachen:

- Es kann kein Zugriffstoken für die DRS-Ressource im Hintergrund abgerufen werden.
   - Für Windows&nbsp;10-Geräte wird das Authentifizierungstoken über den Verbunddienst beschafft, indem die integrierte Windows-Authentifizierung über einen aktiven WS-Trust-Endpunkt genutzt wird. Weitere Informationen finden Sie unter [Verbunddienstkonfiguration](hybrid-azuread-join-manual.md#set-up-issuance-of-claims).

**Häufige Fehlercodes**:

Anhand der Protokolle der Ereignisanzeige können Sie den Fehlercode, Unterfehlercode, Serverfehlercode und die Serverfehlermeldung suchen.

1. Öffnen Sie in der Ereignisanzeige die Ereignisprotokolle zur **Benutzergeräteregistrierung**. Sie sind unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung** gespeichert.
1. Suchen Sie nach der Ereignis-ID 305.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/3.png" alt-text="Screenshot der Ereignisanzeige mit ausgewählter Ereignis-ID 305, den zugehörigen Informationen sowie den ADAL-Fehlercodes und dem Status, die hervorgehoben sind." border="false":::

**Konfigurationsfehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **ERROR_ADAL_PROTOCOL_NOT_SUPPORTED** (0xcaa90017/-894894057) | Das ADAL-Authentifizierungsprotokoll (Azure AD Authentication Library, Active Directory-Authentifizierungsbibliothek) ist nicht WS-Trust. | Der lokale Identitätsanbieter muss WS-Trust unterstützen. |
| **ERROR_ADAL_FAILED_TO_PARSE_XML** (0xcaa9002c/-894894036) | Der lokale Verbunddienst hat keine XML-Antwort zurückgegeben. | Sorgen Sie dafür, dass der MEX-Endpunkt (Metadata Exchange) einen gültigen XML-Code zurückgibt. Sorgen Sie dafür, dass der Proxy keine Beeinträchtigung darstellt und keine Antworten in einem Format ohne XML zurückgibt. |
| **ERROR_ADAL_COULDNOT_DISCOVER_USERNAME_PASSWORD_ENDPOINT** (0xcaa90023/-894894045) | Es konnte kein Endpunkt für die Authentifizierung von Benutzername/Kennwort ermittelt werden. | Überprüfen Sie die Einstellungen des lokalen Identitätsanbieters. Sorgen Sie dafür, dass die WS-Trust-Endpunkte aktiviert sind und die MEX-Antwort diese richtigen Endpunkte enthält. |
| | |


**Netzwerkfehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **ERROR_ADAL_INTERNET_TIMEOUT** (0xcaa82ee2/-894947614) | Allgemeine Netzwerkzeitüberschreitung. | Sorgen Sie dafür, dass im Systemkontext auf `https://login.microsoftonline.com` zugegriffen werden kann. Sorgen Sie dafür, dass im Systemkontext auf den lokalen Identitätsanbieter zugegriffen werden kann. Weitere Informationen finden Sie unter [Netzwerkverbindungsanforderungen](hybrid-azuread-join-managed-domains.md#prerequisites). |
| **ERROR_ADAL_INTERNET_CONNECTION_ABORTED** (0xcaa82efe/-894947586) | Die Verbindung mit dem Autorisierungsendpunkt wurde abgebrochen. | Wiederholen Sie die Einbindung nach einer Weile, oder versuchen Sie die Einbindung von einem anderen stabilen Netzwerkspeicherort aus. |
| **ERROR_ADAL_INTERNET_SECURE_FAILURE** (0xcaa82f8f/-894947441) | Das vom Server gesendete TLS-Zertifikat (Transport Layer Security), das früher als „SSL-Zertifikat“ (Secure Sockets Layer) bezeichnet wurde, konnte nicht überprüft werden. | Überprüfen Sie die Zeitabweichung des Clients. Wiederholen Sie die Einbindung nach einer Weile, oder versuchen Sie die Einbindung von einem anderen stabilen Netzwerkspeicherort aus. |
| **ERROR_ADAL_INTERNET_CANNOT_CONNECT** (0xcaa82efd/-894947587) | Der Versuch, eine Verbindung mit `https://login.microsoftonline.com` herzustellen, ist fehlgeschlagen. | Überprüfen Sie die Netzwerkverbindung mit `https://login.microsoftonline.com`. |
| | |


**Sonstige Fehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **ERROR_ADAL_SERVER_ERROR_INVALID_GRANT** (0xcaa20003/-895352829) | Das SAML-Token vom lokalen Identitätsanbieter wurde von Azure AD nicht akzeptiert. | Überprüfen Sie die Einstellungen des Verbundservers. Suchen Sie in den Authentifizierungsprotokollen nach dem Serverfehlercode. |
| **ERROR_ADAL_WSTRUST_REQUEST_SECURITYTOKEN_FAILED** (0xcaa90014/-894894060) | Die Serverantwort von WS-Trust hat eine Fehlerausnahme gemeldet und konnte keine Assertion abrufen. | Überprüfen Sie die Einstellungen des Verbundservers. Suchen Sie in den Authentifizierungsprotokollen nach dem Serverfehlercode. |
| **ERROR_ADAL_WSTRUST_TOKEN_REQUEST_FAIL** (0xcaa90006/-894894074) | Beim Versuch, ein Zugriffstoken vom Tokenendpunkt abzurufen, wurde ein Fehler gemeldet. | Suchen Sie im ADAL-Protokoll nach dem zugrunde liegenden Fehler. |
| **ERROR_ADAL_OPERATION_PENDING** (0xcaa1002d/-895418323) | Allgemeiner ADAL-Fehler. | Suchen Sie in den Authentifizierungsprotokollen nach dem Unterfehlercode oder dem Serverfehlercode. |
| | |


#### <a name="join-phase"></a>Einbindungsphase

Fehlerursachen:

Suchen Sie in den folgenden Tabellen nach dem Registrierungstyp und dem Fehlercode, abhängig von der verwendeten Windows 10-Version.

#### <a name="windowsnbsp10-version-1803-or-later"></a>Windows&nbsp;10, Version 1803 oder höher

Suchen Sie im Abschnitt „Diagnosedaten“ der Statusausgabe zur Einbindung nach dem Unterabschnitt „Vorherige Registrierung“. Dieser Abschnitt wird nur angezeigt, wenn das Gerät in eine Domäne eingebunden und seine Hybrideinbindung in Azure AD nicht möglich ist.

Das Feld „Registrierungstyp“ gibt den Typ der durchgeführten Einbindung an.

```
+----------------------------------------------------------------------+
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

#### <a name="earlier-windowsnbsp10-versions"></a>Frühere Windows&nbsp;10-Versionen

Verwenden Sie die Protokolle der Ereignisanzeige, um die Phase und den Fehlercode für die Einbindungsfehler zu ermitteln.

1. Öffnen Sie in der Ereignisanzeige die Ereignisprotokolle zur **Benutzergeräteregistrierung**. Sie sind unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **Benutzergeräteregistrierung** gespeichert.
1. Suchen Sie nach der Ereignis-ID 204.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/4.png" alt-text="Screenshot der Ereignisanzeige mit ausgewählter Ereignis-ID 204 und zugehörigem Fehlercode, dem HTTP-Status und hervorgehobener Meldung." border="false":::

**Vom DRS-Server zurückgegebene HTTP-Fehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **DSREG_E_DIRECTORY_FAILURE** (0x801c03f2/-2145647630) | Es wurde eine Fehlerantwort vom Datenreplikationsdienst (Data Replication Service, DRS) mit dem Fehlercode „DirectoryError“ empfangen. | Informationen zu möglichen Gründen und Lösungen finden Sie im Serverfehlercode. |
| **DSREG_E_DEVICE_AUTHENTICATION_ERROR** (0x801c0002/-2145648638) | Es wurde eine Fehlerantwort vom DRS mit dem Fehlercode (ErrorCode) „AuthenticationError“ (Authentifizierungsfehler) empfangen, und der Unterfehlercode (ErrorSubCode) lautet  *nicht* „DeviceNotFound“ (Gerät nicht gefunden). | Informationen zu möglichen Gründen und Lösungen finden Sie im Serverfehlercode. |
| **DSREG_E_DEVICE_INTERNALSERVICE_ERROR** (0x801c0006/-2145648634) | Es wurde eine Fehlerantwort vom DRS mit dem Fehlercode „DirectoryError“ (Verzeichnisfehler) empfangen. | Informationen zu möglichen Gründen und Lösungen finden Sie im Serverfehlercode. |
| | |


**TPM-Fehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **NTE_BAD_KEYSET** (0x80090016/-2146893802) | Der TPM-Vorgang (Trusted Platform Module) ist fehlgeschlagen oder ungültig. | Die Fehlerursache ist wahrscheinlich ein fehlerhaftes Sysprep-Image. Sorgen Sie dafür, dass der Computer, von dem das Sysprep-Image erstellt wurde, nicht in Azure AD oder hybrid in Azure AD eingebunden oder aber bei Azure AD registriert ist. |
| **TPM_E_PCP_INTERNAL_ERROR** (0x80290407/-2144795641) | Allgemeiner TPM-Fehler. | Deaktivieren Sie TPM auf Geräten mit diesem Fehler. Windows&nbsp;10, Version 1809 und höher, erkennt TPM-Fehler automatisch und schließt die Azure AD-Hybrideinbindung ohne Verwendung des TPM ab. |
| **TPM_E_NOTFIPS** (0x80280036/-2144862154) | TPM im FIPS-Modus wird zurzeit nicht unterstützt. | Deaktivieren Sie TPM auf Geräten mit diesem Fehler. Windows 10, Version 1809, erkennt TPM-Fehler automatisch und schließt die Azure AD-Hybrideinbindung ohne Verwendung des TPM ab. |
| **NTE_AUTHENTICATION_IGNORED** (0x80090031/-2146893775) | TPM ist gesperrt. | Transient error. (Vorübergehender Fehler.) Warten Sie die Abkühlphase ab. Der Einbindungsversuch sollte nach einer Weile erfolgreich sein. Weitere Informationen finden Sie unter [TPM-Grundlagen](/windows/security/information-protection/tpm/tpm-fundamentals#anti-hammering). |
| | |


**Netzwerkfehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **WININET_E_TIMEOUT** (0x80072ee2/-2147012894) | Allgemeine Netzwerkzeitüberschreitung beim Versuch, das Gerät bei DRS zu registrieren. | Überprüfen Sie die Netzwerkverbindung zu `https://enterpriseregistration.windows.net`. |
| **WININET_E_NAME_NOT_RESOLVED** (0x80072ee7/-2147012889) | Der Servername oder die Adresse konnte nicht aufgelöst werden. | Überprüfen Sie die Netzwerkverbindung zu `https://enterpriseregistration.windows.net`. |
| **WININET_E_CONNECTION_ABORTED** (0x80072efe/-2147012866) | Die Verbindung zum Server wurde abgebrochen. | Wiederholen Sie die Einbindung nach einer Weile, oder versuchen Sie die Einbindung von einem anderen stabilen Netzwerkspeicherort aus. |
| | |


**Sonstige Fehler**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **DSREG_AUTOJOIN_ADCONFIG_READ_FAILED** (0x801c001d/-2145648611) | Ereignis-ID 220 ist in Ereignisprotokollen für die Registrierung von Benutzergeräten enthalten. Windows kann in Active Directory auf das Computerobjekt nicht zugreifen. Möglicherweise ist im Ereignis ein Windows-Fehlercode enthalten. Die Fehlercodes „ERROR_NO_SUCH_LOGON_SESSION (1312)“ und „ERROR_NO_SUCH_USER (1317)“ stehen im Zusammenhang mit Replikationsproblemen im lokalen Active Directory. | Beheben Sie die Replikationsprobleme in Active Directory. Diese Replikationsprobleme könnten vorübergehend und nach einer Weile verschwunden sein. |
| | |


**Serverfehler bei der Verbundeinbindung**:

| Serverfehlercode | Serverfehlermeldung | Mögliche Ursachen | Lösung |
| --- | --- | --- | --- |
| DirectoryError | Ihre Anforderung wird vorübergehend gedrosselt. Versuchen Sie es nach 300 Sekunden erneut. | Dies ist ein erwarteter Fehler – möglicherweise, weil mehrere Registrierungsanforderungen in schneller Folge gestellt wurden. | Wiederholen Sie die Einbindung nach der Abkühlphase. |
| | |

**Serverfehler bei „sync join“** :

| Serverfehlercode | Serverfehlermeldung | Mögliche Ursachen | Lösung |
| --- | --- | --- | --- |
| DirectoryError | AADSTS90002: Mandant `UUID` wurde nicht gefunden. Dieser Fehler tritt möglicherweise auf, wenn es keine aktiven Abonnements für den Mandanten gibt. Wenden Sie sich an Ihren Abonnement-Administrator. | Die Mandanten-ID im Dienstverbindungspunkt-Objekt ist falsch. | Sorgen Sie dafür, dass das Dienstverbindungspunkt-Objekt mit der richtigen Azure AD-Mandanten-ID und den richtigen aktiven Abonnements konfiguriert wird oder dass der Dienst im Mandanten vorhanden ist. |
| DirectoryError | Das Geräteobjekt mit der angegebenen ID wurde nicht gefunden. | Dies ist ein erwarteter Fehler bei „sync-join“. Das Geräteobjekt wurde nicht von AD zu Azure AD synchronisiert | Warten Sie, bis die Azure AD Connect-Synchronisierung abgeschlossen ist. Dann wird das Problem beim nächsten Einbindungsversuch nach Abschluss der Synchronisierung gelöst. |
| AuthenticationError | Die Überprüfung der SID des Zielcomputers | Das Zertifikat auf dem Azure AD-Gerät stimmt mit dem Zertifikat, mit dem der Blob während „sync-join“ signiert wurde, nicht überein. Dieser Fehler bedeutet normalerweise, dass die Synchronisierung noch nicht abgeschlossen ist. |  Warten Sie, bis die Azure AD Connect-Synchronisierung abgeschlossen ist. Dann wird das Problem beim nächsten Einbindungsversuch nach Abschluss der Synchronisierung gelöst. |

### <a name="step-5-collect-logs-and-contact-microsoft-support"></a>Schritt 5: Sammeln Sie Protokolle, und wenden Sie sich an den Microsoft-Support

1. [Laden Sie die Datei *Auth.zip* herunter](https://cesdiagtools.blob.core.windows.net/windows/Auth.zip).

1. Extrahieren Sie die Dateien in einen Ordner, z. B. *c:\temp*, und wechseln Sie zu diesem Ordner.
1. Führen Sie in einer Azure PowerShell-Sitzung mit erhöhten Rechten `.\start-auth.ps1 -v -accepteula` aus.
1. Wählen Sie **Konto wechseln** aus, um zu einer anderen Sitzung mit dem Problembenutzer zu wechseln.
1. Reproduzieren Sie das Problem.
1. Wählen Sie **Konto wechseln** aus, um wieder zurück zu der Administratorsitzung zu wechseln, in der die Ablaufverfolgung ausgeführt wird.
1. Führen Sie in einer PowerShell-Sitzung mit erhöhten Rechten `.\stop-auth.ps1` aus.
1. Zippen (komprimieren) Sie den Ordner *Authlogs* in dem Ordner, in dem die Skripts ausgeführt wurden, und senden Sie ihn.
    
## <a name="troubleshoot-post-join-authentication-issues"></a>Beheben von Authentifizierungsproblemen nach der Einbindung

### <a name="step-1-retrieve-the-prt-status-by-using-dsregcmd-status"></a>Schritt 1: Abrufen des PRT-Status mit `dsregcmd /status`

1. Öffnen Sie ein Eingabeaufforderungsfenster. 
   > [!NOTE] 
   > Um den Status des primären Aktualisierungstokens (Primary Refresh Token, PRT) abzurufen, öffnen Sie das Eingabeaufforderungsfenster im Kontext des angemeldeten Benutzers. 

1. Führen Sie `dsregcmd /status` aus. 

   Im Abschnitt „SSO-Status" wird der aktuelle PRT-Status angezeigt. 

   Wenn das Feld „AzureAdPrt“ auf *NO* festgelegt wurde, ist beim Abrufen des PRT-Status aus Azure AD ein Fehler aufgetreten. 

1. Wenn der Wert für „AzureAdPrtUpdateTime“ größer als „4 Stunden“ ist, gibt es wahrscheinlich ein Problem beim Aktualisieren des PRT. Sperren und entsperren Sie das Gerät, um die PRT-Aktualisierung zu erzwingen, und überprüfen Sie danach, ob die Zeit aktualisiert wurde.

```
+----------------------------------------------------------------------+
| SSO State                                                            |
+----------------------------------------------------------------------+

                AzureAdPrt : YES
      AzureAdPrtUpdateTime : 2020-07-12 22:57:53.000 UTC
      AzureAdPrtExpiryTime : 2019-07-26 22:58:35.000 UTC
       AzureAdPrtAuthority : https://login.microsoftonline.com/96fa76d0-xxxx-xxxx-xxxx-eb60cc22xxxx
             EnterprisePrt : YES
   EnterprisePrtUpdateTime : 2020-07-12 22:57:54.000 UTC
   EnterprisePrtExpiryTime : 2020-07-26 22:57:54.000 UTC
    EnterprisePrtAuthority : https://corp.hybridadfs.contoso.com:443/adfs

+----------------------------------------------------------------------+
```

### <a name="step-2-find-the-error-code"></a>Schritt 2: Suchen des Fehlercodes 

**In der Ausgabe `dsregcmd`**

> [!NOTE]
>  Die Ausgabe steht im Windows-Update &nbsp;vom 10. Mai 2021 (Version 21H1) zur Verfügung.

Das Feld „Versuchsstatus“ unter dem Feld „AzureAdPrt“ gibt den Status des vorhergehenden PRT-Versuchs, zusammen mit anderen erforderlichen Debuginformationen, an. Extrahieren Sie bei früheren Windows-Versionen die Informationen aus den Azure AD-Analyse- und -Betriebsprotokollen.

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
```

**Aus den Azure AD-Analyse- und -Betriebsprotokollen**

Über die Ereignisanzeige können Sie nach den Protokolleinträgen suchen, die während der PRT-Erfassung vom Azure AD-CloudAP-Plug-In protokolliert werden. 

1. Öffnen Sie in der Ereignisanzeige die Azure AD-Ereignisprotokolle für den Betrieb. Sie sind unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows** > **AAD** gespeichert. 

   > [!NOTE]
   > Das CloudAP-Plug-In protokolliert die Fehlerereignisse in den Betriebsprotokollen und die Informationsereignisse in den Analyseprotokollen. Sowohl Analyse- als auch Betriebsprotokollereignisse sind zur Behebung von Problemen erforderlich. 

1. Ereignis 1006 in den Analyseprotokollen gibt den Beginn des PRT-Erfassungsablaufs an, und Ereignis 1007 in den Analyseprotokollen gibt das Ende des PRT-Erfassungsablaufs an. Alle Ereignisse in den Azure AD-Protokollen (Analyse und Betrieb), die zwischen den Ereignissen 1006 und 1007 protokolliert werden, wurden im Rahmen des PRT-Erfassungsablaufs protokolliert. 

1. Ereignis 1007 protokolliert den endgültigen Fehlercode.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-current/event-viewer-prt-acquire.png" alt-text="Screenshot: Ereignisanzeige mit den ausgewählten Ereignis-IDs 1006 und 1007 und dem hervorgehobenen endgültigen Fehlercode." border="false":::

### <a name="step-3-troubleshoot-further-based-on-the-found-error-code"></a>Schritt 3: Weitere Problembehandlung, basierend auf dem gefundenen Fehlercode

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **STATUS_LOGON_FAILURE** (-1073741715/ 0xc000006d)<br>**STATUS_WRONG_PASSWORD** (-1073741718/ 0xc000006a) | <li>Das Gerät kann keine Verbindung mit dem Azure AD-Authentifizierungsdienst herstellen.<li>Es wurde eine Fehlerantwort (HTTP 400) aus dem Azure AD-Authentifizierungsdienst oder vom WS-Trust-Endpunkt empfangen.<br>**Hinweis**: WS-Trust ist für die Verbundanmeldung erforderlich. | <li>Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass das Computerkonto des Geräts in der Lage ist, den Proxy zu ermitteln und sich im Hintergrund zu authentifizieren.<li>Die Ereignisse 1081 und 1088 (Azure AD-Betriebsprotokolle) enthalten den Serverfehlercode für Fehler, die aus dem Azure AD-Authentifizierungsdienst stammen, und die Fehlerbeschreibung für Fehler, die vom WS-Trust-Endpunkt stammen. Häufige Serverfehlercodes und deren Lösungen sind im nächsten Abschnitt aufgeführt. Die erste Instanz von Ereignis 1022 (Azure AD-Analyseprotokolle) vor den Ereignissen 1081 oder 1088 enthält die URL, auf die zugegriffen wird. |
| **STATUS_REQUEST_NOT_ACCEPTED** (-1073741616/ 0xc00000d0) | Es wurde eine Fehlerantwort (HTTP 400) aus dem Azure AD-Authentifizierungsdienst oder vom WS-Trust-Endpunkt empfangen.<br>**Hinweis**: WS-Trust ist für die Verbundanmeldung erforderlich. | Die Ereignisse 1081 und 1088 (Azure AD-Betriebsprotokolle) enthalten den Serverfehlercode und die Fehlerbeschreibung für Fehler, die aus dem Azure AD-Authentifizierungsdienst bzw. vom WS-Trust-Endpunkt stammen. Häufige Serverfehlercodes und deren Lösungen sind im nächsten Abschnitt aufgeführt. Die erste Instanz von Ereignis 1022 (Azure AD-Analyseprotokolle) vor den Ereignissen 1081 oder 1088 enthält die URL, auf die zugegriffen wird. |
| **STATUS_NETWORK_UNREACHABLE** (-1073741252/ 0xc000023c)<br>**STATUS_BAD_NETWORK_PATH** (-1073741634/ 0xc00000be)<br>**STATUS_UNEXPECTED_NETWORK_ERROR** (-1073741628/ 0xc00000c4) | <li>Es wurde eine Fehlerantwort (HTTP > 400) aus dem Azure AD-Authentifizierungsdienst oder vom WS-Trust-Endpunkt empfangen.<br>**Hinweis**: WS-Trust ist für die Verbundanmeldung erforderlich.<li>Problem bei der Netzwerkkonnektivität mit einem erforderlichen Endpunkt. | <li>Bei Serverfehlern enthalten die Ereignisse 1081 und 1088 (Azure AD-Betriebsprotokolle) den Fehlercode aus dem Azure AD-Authentifizierungsdienst und die Fehlerbeschreibung vom WS-Trust-Endpunkt. Häufige Serverfehlercodes und deren Lösungen sind im nächsten Abschnitt aufgeführt.<li>Bei Konnektivitätsproblemen enthält Ereignis 1022 (Azure AD-Analyseprotokolle) die URL, auf die zugegriffen wird, und Ereignis 1084 (Azure AD-Betriebsprotokolle) enthält den Unterfehlercode aus dem Netzwerkstapel. |
| **STATUS_NO_SUCH_LOGON_SESSION**    (-1073741729/ 0xc000005f) | Fehler bei der Benutzerbereichsermittlung, weil der Azure AD-Authentifizierungsdienst die Domäne des Benutzers nicht finden konnte. | <li>Die Domäne mit dem UPN des Benutzers muss in Azure AD als benutzerdefinierte Domäne hinzugefügt werden. Ereignis 1144 (Azure AD-Analyseprotokolle) enthält den bereitgestellten UPN.<li>Wenn der lokale Domänenname nicht routingfähig ist (jdoe@contoso.local), konfigurieren Sie eine alternative Anmelde-ID (AltID). Referenzen: [Voraussetzungen](hybrid-azuread-join-plan.md); [Konfigurieren einer alternativen Anmelde-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). |
| **AAD_CLOUDAP_E_OAUTH_USERNAME_IS_MALFORMED**   (-1073445812/ 0xc004844c) | Der UPN des Benutzers hat nicht das erwartete Format.<br>**Hinweise**:<li>Bei in Azure AD eingebundenen Geräten ist der UPN der vom Benutzer in der LoginUI eingegebene Text. <li>Bei in Azure AD eingebundenen Hybridgeräten wird der UPN während des Anmeldevorgangs vom Domänencontroller zurückgegeben. | <li>Der UPN des Benutzers ist ein Anmeldename im Internetformat, der auf dem Internetstandard [RFC 822](https://www.ietf.org/rfc/rfc0822.txt) basiert. Ereignis 1144 (Azure AD-Analyseprotokolle) enthält den bereitgestellten UPN.<li>Bei eingebundenen Hybridgeräten muss der Domänencontroller so konfiguriert werden, dass der UPN im richtigen Format zurückgegeben wird. Auf dem Domänencontroller sollte `whoami /upn` den konfigurierten UPN anzeigen.<li>Wenn der lokale Domänenname nicht routingfähig ist (jdoe@contoso.local), konfigurieren Sie die alternative Anmelde-ID (AltID). Referenzen: [Voraussetzungen](hybrid-azuread-join-plan.md); [Konfigurieren einer alternativen Anmelde-ID](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id). |
| **AAD_CLOUDAP_E_OAUTH_USER_SID_IS_EMPTY** (-1073445822/ 0xc0048442) | Die Benutzer-SID fehlt in dem ID-Token, das vom Azure AD-Authentifizierungsdienst zurückgegeben wird. | Sorgen Sie dafür, dass der Netzwerkproxy keine Beeinträchtigung darstellt und die Serverantwort nicht ändert. |
| **AAD_CLOUDAP_E_WSTRUST_SAML_TOKENS_ARE_EMPTY** (--1073445695/ 0xc00484c1) | Es wurde ein Fehler vom WS-Trust-Endpunkt empfangen.<br>**Hinweis**: WS-Trust ist für die Verbundanmeldung erforderlich. | <li>Sorgen Sie dafür, dass der Netzwerkproxy keine Beeinträchtigung darstellt und die WS-Trust-Antwort nicht ändert.<li>Ereignis 1088 (Azure AD-Betriebsprotokolle) enthält den Serverfehlercode und die Fehlerbeschreibung vom WS-Trust-Endpunkt. Häufige Serverfehlercodes und deren Lösungen sind im nächsten Abschnitt aufgeführt. |
| **AAD_CLOUDAP_E_HTTP_PASSWORD_URI_IS_EMPTY** (-1073445749/ 0xc004848b) | Der MEX-Endpunkt wurde falsch konfiguriert. Die MEX-Antwort enthält keine Kennwort-URLs. | <li>Sorgen Sie dafür, dass der Netzwerkproxy keine Beeinträchtigung darstellt und die Serverantwort nicht ändert.<li>Korrigieren Sie die MEX-Konfiguration, um gültige URLs als Antwort zurückzugeben. |
| **WC_E_DTDPROHIBITED** (-1072894385/ 0xc00cee4f) | Die XML-Antwort vom WS-Trust-Endpunkt enthielt eine Dokumenttypdefinition (Document Type Definition, DTD). Eine DTD wird in XML-Antworten nicht erwartet, und wenn darin eine DTD enthalten ist, tritt bei der Analyse der Antwort ein Fehler auf.<br>**Hinweis**: WS-Trust ist für die Verbundanmeldung erforderlich. | <li>Korrigieren Sie die Konfiguration im Identitätsanbieter, um das Senden einer DTD in der XML-Antwort zu vermeiden.<li>Ereignis 1022 (Azure AD-Analyseprotokolle) enthält die URL, auf die zugegriffen wird und die eine XML-Antwort mit einer DTD zurückgibt. |
| | |


**Häufige Serverfehlercodes:**

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **AADSTS50155: Fehler bei Geräteauthentifizierung** | <li>Azure AD kann das Gerät nicht für die Ausgabe eines PRT authentifizieren.<li>Bestätigen Sie, dass das Gerät im Azure-Portal nicht gelöscht oder deaktiviert wurde. Weitere Informationen zu diesem Problem finden Sie unter [Häufig gestellte Fragen zur Azure Active Directory-Geräteverwaltung](faq.yml#why-do-my-users-see-an-error-message-saying--your-organization-has-deleted-the-device--or--your-organization-has-disabled-the-device--on-their-windows-10-11-devices). | Folgen Sie den Anleitungen für dieses Problem in [Häufig gestellte Fragen zur Azure Active Directory-Geräteverwaltung](faq.yml#i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell--but-the-local-state-on-the-device-says-it-s-still-registered--what-should-i-do), um das Gerät basierend auf dem Einbindungstyp erneut zu registrieren. |
| **AADSTS50034: Das Benutzerkonto `Account` ist nicht im `tenant id`-Verzeichnis vorhanden** | Azure AD kann das Benutzerkonto im Mandanten nicht finden. | <li>Sorgen Sie dafür, dass der Benutzer den richtigen UPN eingibt.<li>Sorgen Sie dafür, dass das lokale Benutzerkonto mit Azure AD synchronisiert wird.<li>Ereignis 1144 (Azure AD-Analyseprotokolle) enthält den bereitgestellten UPN. |
| **AADSTS50126: Fehler beim Überprüfen der Anmeldeinformationen aufgrund eines ungültigen Benutzernamens oder Kennworts.** | <li>Der Benutzername und das Kennwort, die vom Benutzer in der Windows-LoginUI eingegeben wurden, sind falsch.<li>Wenn beim Mandanten die Kennworthashsynchronisierung aktiviert wurde, ist das Gerät hybrid eingebunden, und wenn der Benutzer das Kennwort gerade geändert hat, wurde das neue Kennwort wahrscheinlich nicht mit Azure AD synchronisiert. | Um ein neues PRT mit den neuen Anmeldeinformationen zu erhalten, warten Sie, bis die Azure AD Kennwortsynchronisierung abgeschlossen ist. |
| | |


**Häufige Netzwerkfehlercodes**:

| Fehlercode | `Reason` | Lösung |
| --- | --- | --- |
| **ERROR_WINHTTP_TIMEOUT** (12002)<br>**ERROR_WINHTTP_NAME_NOT_RESOLVED** (12007)<br>**ERROR_WINHTTP_CANNOT_CONNECT** (12029)<br>**ERROR_WINHTTP_CONNECTION_ERROR** (12030) | Häufige allgemeine Probleme im Zusammenhang mit dem Netzwerk. | <li>Die Ereignisse 1022 (Azure AD-Analyseprotokolle) und 1084 (Azure AD Betriebsprotokolle) enthalten die URL, auf die zugegriffen wird.<li>Wenn die lokale Umgebung einen Proxy für den ausgehenden Datenverkehr erfordert, muss der IT-Administrator sicherstellen, dass das Computerkonto des Geräts in der Lage ist, den Proxy zu ermitteln und sich im Hintergrund zu authentifizieren.<br><br>Weitere Netzwerkfehlercodes finden Sie [hier](/windows/win32/winhttp/error-messages). |
| | |


### <a name="step-4-collect-logs"></a>Schritt 4: Sammeln von Protokollen

**Reguläre Protokolle**

1. Wechseln Sie zu https://aka.ms/icesdptool, damit eine *CAB*-Datei mit dem Diagnosetool automatisch heruntergeladen wird.
1. Führen Sie das Tool aus, und reproduzieren Sie Ihr Szenario.
1. Bei Fiddler akzeptieren Ablaufverfolgungen die angezeigten Zertifikatanforderungen.
1. Der Assistent fordert Sie zur Eingabe eines Kennworts auf, um Ihre Ablaufverfolgungsdateien zu schützen. Geben Sie ein Kennwort an.
1. Öffnen Sie abschließend den Ordner, in dem alle gesammelten Protokolle gespeichert sind, z. B. *%LOCALAPPDATA%\ElevatedDiagnostics\numbers*.
1. Wenden Sie sich mit dem Inhalt der aktuellen *CAB*-Datei an den Support.

**Netzwerkablaufverfolgung**

> [!NOTE]
> Wenn Sie Netzwerkablaufverfolgungen sammeln, ist es wichtig, Fiddler während der Reproduktion *nicht* zu verwenden.

1.  Führen Sie `netsh trace start scenario=internetClient_dbg capture=yes persistent=yes` aus.
1. Sperren und entsperren Sie das Gerät. Warten Sie bei eingebundenen Hybridgeräten mindestens eine Minute, bis die PRT-Erfassungsaufgabe abgeschlossen ist.
1. Führen Sie `netsh trace stop` aus.
1. Geben Sie die Datei *nettrace.cab* für den Support frei.

---

## <a name="known-issues"></a>Bekannte Probleme
- Wenn Sie mit einem mobilen Hotspot oder einem externen WLAN-Netzwerk verbunden sind und zu **Einstellungen** > **Konten** > **Auf Geschäfts-, Schul- oder Unikonto zugreifen** wechseln, werden für in Azure AD eingebundene Hybridgeräte möglicherweise zwei verschiedene Konten (eines für Azure AD und eines für lokales AD) gezeigt. Dies ist nur ein Problem bei der Benutzeroberfläche und wirkt sich nicht auf die Funktionalität aus.

## <a name="next-steps"></a>Nächste Schritte

- [Problembehandlung bei Geräten mithilfe des Befehls `dsregcmd`](troubleshoot-device-dsregcmd.md).
- Wechseln Sie zum [Microsoft-Fehlersuchtool](/windows/win32/debug/system-error-code-lookup-tool).
