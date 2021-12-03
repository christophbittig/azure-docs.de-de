---
title: Azure Active Directory-Sicherheitsvorgänge für Geräte
description: Hier erfahren Sie, wie Baselines eingerichtet und Geräte zur Erkennung potenzieller Sicherheitsrisiken überwacht und entsprechende Bericht erstellt werden.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a80564d341ad6e3fbefe1500a222ad20fc3a482c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349279"
---
# <a name="azure-active-directory-security-operations-for-devices"></a>Azure Active Directory-Sicherheitsvorgänge für Geräte

Geräte sind in der Regel nicht Ziel von identitätsbasierten Angriffen, *können* jedoch verwendet werden, um Sicherheitskontrollen zu umgehen und die Identität von Benutzern anzunehmen. Zwischen Geräten und Azure AD kann eine von vier Beziehungen bestehen: 

* Registrierung aufgehoben

* [Bei Azure Active Directory (Azure AD) registriert](../devices/concept-azure-ad-register.md)

* [In Azure AD eingebunden](../devices/concept-azure-ad-join.md)

* [Als Hybridgerät in Azure AD eingebunden](../devices/concept-azure-ad-join-hybrid.md)   
‎

Für registrierte und eingebundene Geräte wird ein [primäres Aktualisierungstoken (Primary Refresh Token, PRT)](../devices/concept-primary-refresh-token.md) ausgestellt, das als primäres Authentifizierungsartefakt und gelegentlich auch als Artefakt für eine mehrstufige Authentifizierung verwendet werden kann. Angreifer können versuchen, eigene Geräte zu registrieren, mit PRTs auf rechtmäßigen Geräten auf Geschäftsdaten zuzugreifen, PRT-basierte Token von rechtmäßigen Benutzergeräten zu stehlen oder Konfigurationsfehler in gerätebasierten Kontrollen in Azure Active Directory ausfindig zu machen. Bei in Azure AD Hybrid eingebundenen Geräten wird die Einbindung von Administratoren initiiert und kontrolliert. Dadurch wird die Gefahr möglicher Angriffe reduziert.

Weitere Informationen zu Methoden zur Geräteintegration finden Sie unter [Auswählen Ihrer Integrationsmethoden](../devices/plan-device-deployment.md) im Artikel [Planen der Azure AD-Gerätebereitstellung](../devices/plan-device-deployment.md).

Verringern Sie die Gefahr, dass böswillige Akteure über Geräte auf Ihre Infrastruktur zugreifen, indem Sie Folgendes überwachen

* Geräteregistrierung und Azure AD-Einbindung

* Nicht konforme Geräte, die auf Anwendungen zugreifen

* Abruf von BitLocker-Schlüsseln

* Geräteadministratorrollen

* Anmeldungen bei virtuellen Computern

## <a name="where-to-look"></a>Zu untersuchende Protokolle

Zur Untersuchung und Überwachung verwenden Sie die folgenden Protokolldateien: 

* [Azure AD-Überwachungsprotokolle](../reports-monitoring/concept-audit-logs.md)

* [Anmeldeprotokolle](../reports-monitoring/concept-all-sign-ins.md)

* [Microsoft 365-Überwachungsprotokolle](/microsoft-365/compliance/auditing-solutions-overview) 

* [Azure Key Vault-Protokolle](../..//key-vault/general/logging.md?tabs=Vault)

Im Azure-Portal können Sie die Azure AD-Überwachungsprotokolle anzeigen und als CSV- oder JSON-Dateien (Comma-Separated Value, JavaScript Object Notation) herunterladen. Das Azure-Portal bietet mehrere Möglichkeiten zur Integration von Azure AD-Protokollen in andere Tools, die eine umfassendere Automatisierung von Überwachung und Warnmeldungen ermöglichen:

* **[Microsoft Sentinel](../../sentinel/overview.md)** : Ermöglicht intelligente Sicherheitsanalysen auf Unternehmensebene, indem SIEM-Funktionen (Security Information and Event Management) zur Verfügung gestellt werden. 

* **[Azure Monitor](../..//azure-monitor/overview.md)** : ermöglicht die automatisierte Überwachung verschiedener Bedingungen und entsprechende Warnungen. Mit dem Tool können Arbeitsmappen erstellt oder verwendet werden, um Daten aus verschiedenen Quellen zu kombinieren.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) mit Integration in ein SIEM-System**- [ Azure AD-Protokolle können über die Azure Event Hub-Integration mit anderen SIEM-Systemen](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) wie Splunk, ArcSight, QRadar und Sumo Logic integriert werden.

* **[Microsoft Defender für Cloud-Apps](/cloud-app-security/what-is-cloud-app-security)** : Ermöglicht Ihnen die Erkennung und Verwaltung von Apps, die Steuerung von Apps und Ressourcen sowie die Überprüfung der Kompatibilität Ihrer Cloud-Apps. 

Ein Großteil der Überwachung und zugehörigen Warnungen hängt von den Auswirkungen Ihrer Richtlinien für bedingten Zugriff ab. Sie können die Arbeitsmappe [Erkenntnisse und Berichterstellung zum bedingten Zugriff](../conditional-access/howto-conditional-access-insights-reporting.md) verwenden, um die Auswirkungen einer oder mehrere Richtlinien für bedingten Zugriff auf Ihre Anmeldungen sowie die Ergebnisse von Richtlinien, einschließlich Gerätestatus, zu untersuchen. Diese Arbeitsmappe bietet Ihnen die Möglichkeit, eine Zusammenfassung der Auswirkungen anzuzeigen und die Auswirkungen über einen bestimmten Zeitraum zu ermitteln. Sie können mithilfe der Arbeitsmappe auch die Anmeldungen eines bestimmten Benutzers untersuchen. 

 Im restlichen Teil dieses Artikels wird beschrieben, was wir Ihnen zur Überwachung und für zugehörige Warnungen empfehlen, und zwar gegliedert nach der Art der Bedrohung. Wo es spezielle vordefinierte Lösungen gibt, verweisen wir auf diese oder stellen Beispiele im Anschluss an die Tabelle zur Verfügung. Andernfalls können Sie Warnungen mithilfe der oben genannten Tools erstellen. 

 ## <a name="device-registrations-and-joins-outside-policy"></a>Geräteregistrierungen und -einbindungen außerhalb der Richtlinie

Bei Azure AD registrierte und in Azure AD eingebundene Geräte verfügen über primäre Aktualisierungstoken (Primary Refresh Tokens, PRTs), die einer einstufigen Authentifizierung entsprechen. Diese Geräte können bisweilen den Anspruch auf eine strenge Authentifizierung enthalten. Weitere Informationen dazu, wann PRTs den Anspruch auf eine strenge Authentifizierung enthalten, finden Sie unter [Wann erhält ein PRT einen MFA-Anspruch?](../devices/concept-primary-refresh-token.md) Böswillige Akteure halten Sie davon ab, Geräte zu registrieren oder einzubinden, indem Sie zum Registrieren oder Einbinden von Geräten eine mehrstufige Authentifizierung (MFA) verlangen. Dann können Sie überwachen, ob Geräte ohne MFA registriert oder eingebunden werden. Darüber hinaus müssen Sie darauf achten, ob an MFA-Einstellungen und -Richtlinien Änderungen vorgenommen und die Konformitätsrichtlinien für Geräte eingehalten werden.

 | Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Registrierung oder Einbindung eines Geräts ohne MFA| Medium| Anmeldeprotokolle| Aktivität: erfolgreiche Authentifizierung beim Geräteregistrierungsdienst <br>Und<br>Keine MFA erforderlich| Warnung, wenn: <br>ein Gerät ohne MFA registriert oder eingebunden wird |
| Änderungen an der MFA-Umschaltung der Geräteregistrierung in Azure AD| Hoch| Überwachungsprotokoll| Aktivität: Festlegen von Richtlinien für die Geräteregistrierung| Suchen nach: <br>Die Umschaltung wurde deaktiviert. Im Überwachungsprotokoll ist kein Eintrag vorhanden. Regelmäßige Überprüfungen planen. |
| Änderungen an den Richtlinien für bedingten Zugriff, die in Domänen eingebundene oder konforme Geräte erfordern.| Hoch| Überwachungsprotokoll| Änderungen an Richtlinien für bedingten Zugriff<br>| Warnung, wenn: <br><li> eine Richtlinie geändert wird, die in Domänen eingebundene oder konforme Geräte erfordert.<li>Änderungen an vertrauenswürdigen Speicherorten vorgenommen wurden.<li> den MFA-Richtlinienausnahmen Konten oder Geräte hinzugefügt wurden. |


Sie können eine Warnung erstellen, die die entsprechenden Administratoren benachrichtigt, wenn ein Gerät ohne MFA registriert oder eingebunden wird, indem Sie Microsoft Sentinel verwenden.

```
Sign-in logs

| where ResourceDisplayName == “Device Registration Service”

| where conditionalAccessStatus ==”success”

| where AuthenticationRequirement <> “multiFactorAuthentication”
```

Sie können auch [Konformitätsrichtlinien zum Festlegen von Regeln für Geräte verwenden, die Sie mit Intune verwalten](/mem/intune/protect/device-compliance-get-started).

## <a name="non-compliant-device-sign-in"></a>Anmelden von nicht konformen Geräten

Es ist unter Umständen nicht möglich, den Zugriff auf alle Cloud- und Software-as-a-Service-Anwendungen mit Richtlinien für bedingten Zugriff, die konforme Geräte erfordern, zu blockieren. 

Mit der [Verwaltung mobiler Geräte (Mobile Device Management, MDM)](/windows/client-management/mdm/) können Sie Windows 10-Geräte konform halten. Mit Windows 1809 wurde eine [Sicherheitsbaseline](/windows/client-management/mdm/) von Richtlinien veröffentlicht. Azure Active Directory kann [mit MDM integriert](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) werden, um die Gerätekonformität mit Unternehmensrichtlinien durchzusetzen und den Konformitätsstatus eines Geräts zu melden. 

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Anmeldungen durch nicht konforme Geräte| Hoch| Anmeldeprotokolle| DeviceDetail.isCompliant ==false| Ausgabe einer Warnung in folgenden Fällen, wenn eine Anmeldung von konformen Geräten erforderlich ist:<br><li> Anmeldung durch nicht konforme Geräte<li> Zugriff ohne MFA oder ohne vertrauenswürdigen Speicherort<p>Wenn Sie darauf hinarbeiten, dass Geräte konform sein müssen, achten Sie auf verdächtige Anmeldungen. |
| Anmeldungen durch unbekannte Geräte| Niedrig| Anmeldeprotokolle| <li>DeviceDetail ist leer<li>Einstufige Authentifizierung<li>Anmeldung über einen nicht vertrauenswürdigen Speicherort| Suchen nach: <br><li>Zugriff über nicht konforme Geräte<li>Zugriff ohne MFA oder ohne vertrauenswürdigen Speicherort |


### <a name="use-loganalytics-to-query"></a>Verwenden von LogAnalytics für Abfragen

**Anmeldungen durch nicht konforme Geräte**

```
SigninLogs

| where DeviceDetail.isCompliant ==false

| where conditionalAccessStatus == “success”
```
 

**Anmeldungen durch unbekannte Geräte**

```

SigninLogs
| where isempty(DeviceDetail.deviceId)

| where AuthenticationRequirement == "singleFactorAuthentication"

| where ResultType == "0"

| where NetworkLocationDetails == "[]"
```
 
## <a name="stale-devices"></a>Veraltete Geräte

Veraltete Geräte sind Geräte, die für einen bestimmten Zeitraum nicht angemeldet waren. Geräte können veralten, wenn ein Benutzer ein neues Gerät erhält oder ein Gerät verliert, oder wenn ein in Azure AD eingebundenes Gerät zurückgesetzt oder neu bereitgestellt wird. Geräte können auch registriert oder eingebunden bleiben, wenn der Benutzer nicht mehr dem Mandanten zugeordnet ist. Veraltete Geräte sollten entfernt werden, damit deren primäre Aktualisierungstoken (Primary Refresh Tokens, PRTs) nicht verwendet werden können.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Datum der letzten Anmeldung| Niedrig| Graph-API| approximateLastSignInDateTime| Verwendung von Graph API oder PowerShell, um veraltete Geräte zu erkennen und zu entfernen |

## <a name="bitlocker-key-retrieval"></a>Abruf von BitLocker-Schlüsseln

Angreifer, die das Gerät eines Benutzers kompromittiert haben, können die [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10)-Schlüssel in Azure AD abrufen. Da es nicht üblich ist, dass Benutzer Schlüssel abrufen, sollten diese Vorgänge überwacht und untersucht werden.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Abruf von Schlüsseln| Medium| Überwachungsprotokolle| OperationName == "Read BitLocker key”| Suchen nach <br><li>Abruf von Schlüsseln<li> anderen anormalen Verhaltensweisen durch Benutzer, die Schlüssel abrufen |


Erstellen Sie in LogAnalytics eine Abfrage wie die folgende:

```
AuditLogs

| where OperationName == "Read BitLocker key” 
```

## <a name="device-administrator-roles"></a>Geräteadministratorrollen

Globale Administratoren und Cloudgeräteadministratoren erhalten automatisch auf allen in Azure AD eingebundenen Geräten lokale Administratorrechte. Daher ist es wichtig, zu überwachen, wer über diese Rechte verfügt, damit Ihre Umgebung sicher bleibt.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Benutzer, die den Rollen „Globaler Administrator“ oder „Geräteadministrator“ hinzugefügt wurden| Hoch| Überwachungsprotokolle| Aktivitätstyp = Mitglied zu Rolle hinzufügen| Suchen nach:<li> neuen Benutzern, die zu diesen Azure AD-Rollen hinzugefügt wurden<li> anschließende anormale Verhaltensweisen von Computern oder Benutzern |


## <a name="non-azure-ad-sign-ins-to-virtual-machines"></a>Anmeldungen bei virtuellen Computern ohne Azure AD

Bei Anmeldungen bei virtuellen Windows- oder LINUX-Computern (Virtual Machines, VMs) sollte auf die Anmeldung über Konten geachtet werden, bei denen es sich nicht um Azure AD-Konten handelt.

### <a name="azure-ad-sign-in-for-linux"></a>Azure AD-Anmeldung für LINUX

Mit der Azure AD-Anmeldung für LINUX können sich Organisationen bei ihren virtuellen Azure LINUX-Computern über Azure AD-Konten mithilfe des SSH-Protokolls (Secure Shell) anmelden.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Anmeldungen über Konten, bei denen es sich nicht um Azure AD-Konten handelt, insbesondere Anmeldungen über SSH| Hoch| Protokolle für lokale Authentifizierung| Ubuntu:  <br>‎Überwachung von „/var/log/auth.log“ auf SSH-Verwendung<br>RedHat: <br>Überwachung von „/var/log/sssd/“ auf SSH-Verwendung| Suchen nach:<li> Einträgen, [bei denen über Konten, bei denen es sich nicht um Azure AD-Konten handelt, eine Verbindung mit virtuellen Computern hergestellt wurde](../devices/howto-vm-sign-in-azure-ad-linux.md) <li>Siehe folgendes Beispiel: |


Ubuntu-Beispiel:

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: Version: 1.0.015570001; user: localusertest01

   May 9 23:49:39 ubuntu1804 aad_certhandler[3915]: User 'localusertest01' is not an AAD user; returning empty result.

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: Version: 1.0.015570001; user: localusertest01

   May 9 23:49:43 ubuntu1804 aad_certhandler[3916]: User 'localusertest01' is not an AAD user; returning empty result.

   May 9 23:49:43 ubuntu1804 sshd[3909]: Accepted publicly for localusertest01 from 192.168.0.15 port 53582 ssh2: RSA SHA256:MiROf6f9u1w8J+46AXR1WmPjDhNWJEoXp4HMm9lvJAQ

   May 9 23:49:43 ubuntu1804 sshd[3909]: pam_unix(sshd:session): session opened for user localusertest01 by (uid=0).

Sie können Richtlinien für Anmeldungen durch virtuelle LINUX-Computer festlegen und virtuelle Linux-Computer, denen nicht genehmigte lokale Konten hinzugefügt wurden, erkennen und kennzeichnen. Weitere Informationen finden Sie unter [Verwenden von Azure Policy zum Sicherstellen von Standards und für die Konformitätsbewertung](../devices/howto-vm-sign-in-azure-ad-linux.md). 

### <a name="azure-ad-sign-ins-for-windows-server"></a>Azure AD-Anmeldungen für Windows Server

Mit Azure AD-Anmeldungen für Windows kann sich Ihre Organisation bei Ihren virtuellen Computern ab Azure Windows 2019 mithilfe von Azure AD-Konten über das Remotedesktopprotokoll (RDP) anmelden.

| Zu überwachende Elemente| Risikostufe| Hierbei gilt:| Filter/Unterfilter| Notizen |
| - |- |- |- |- |
| Anmeldungen über Konten, bei denen es sich nicht um Azure AD-Konten handelt, insbesondere Anmeldungen über RDP| Hoch| Windows Server-Ereignisprotokolle| Interaktive Anmeldung bei virtuellen Windows-Computern| Ereignis 528, Anmeldetyp 10 (RemoteInteractive).<br>Gibt an, wenn sich ein Benutzer über Terminaldienste oder Remotedesktop anmeldet. |


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln im Leitfaden zu Sicherheitsvorgängen:

[Azure AD: Übersicht über Sicherheitsvorgänge](security-operations-introduction.md)

[Sicherheitsvorgänge für Benutzerkonten](security-operations-user-accounts.md)

[Sicherheitsvorgänge für privilegierte Konten](security-operations-privileged-accounts.md)

[Sicherheitsvorgänge für Privileged Identity Management](security-operations-privileged-identity-management.md)

[Sicherheitsvorgänge für Anwendungen](security-operations-applications.md)

[Sicherheitsvorgänge für Geräte](security-operations-devices.md)

 
[Sicherheitsvorgänge für die Infrastruktur](security-operations-infrastructure.md)
