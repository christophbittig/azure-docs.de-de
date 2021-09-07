---
title: AD FS-Anmeldungen bei Azure AD mit Connect Health | Microsoft-Dokumentation
description: In diesem Dokument wird beschrieben, wie Sie AD FS-Anmeldungen in den Bericht für Azure AD Connect Health-Anmeldeaktivitäten integrieren.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 890bf61eb98218df53a6aa66c9e8562881b8d2df
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895596"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>AD FS-Anmeldungen in Azure AD mit Connect Health – Vorschau

AD FS-Anmeldungen können nun mithilfe von Connect Health in den Azure Active Directory-Anmeldebericht integriert werden. Der [Azure AD-Anmeldebericht](../reports-monitoring/concept-all-sign-ins.md) enthält Informationen dazu, wann sich Benutzer, Anwendungen und verwaltete Ressourcen bei Azure AD anmelden und auf Ressourcen zugreifen. 

Der Connect Health für AD FS-Agent korreliert mehrere Ereignis-IDs aus AD FS in Abhängigkeit von der Serverversion, um Informationen über die Anforderung und im Fall von Anforderungsfehlern Fehlerdetails bereitzustellen. Diese Informationen werden mit dem Azure AD-Anmeldberichtsschema korreliert und auf der Benutzeroberfläche des Azure AD-Anmeldeberichts angezeigt. Neben dem Bericht ist ein neuer Log Analytics-Stream mit den AD FS-Daten und eine neue Azure Monitor-Arbeitsmappenvorlage verfügbar. Die Vorlage kann zur ausführlichen Analyse von Szenarien wie AD FS-Kontosperrungen, fehlerhaften Kennwortantwortversuchen und Spitzen unerwarteter Anmeldeversuche verwendet und geändert werden.

## <a name="prerequisites"></a>Voraussetzungen
* Azure AD Connect Health für AD FS wurde installiert und auf die aktuelle Version aktualisiert (3.1.95.0 oder höher).
* Rolle „Globaler Administrator“ oder „Berichtsleser“ zum Anzeigen der Azure AD-Anmeldungen

## <a name="what-data-is-displayed-in-the-report"></a>Welche Daten werden im Bericht angezeigt?
Die verfügbaren Daten spiegeln die gleichen Daten wider, die für Azure AD-Anmeldungen verfügbar sind. Es werden fünf Registerkarten mit Informationen verfügbar sein, basierend auf dem Anmeldetyp, entweder Azure AD oder AD FS. Connect Health korreliert Ereignisse aus AD FS in Abhängigkeit von der Serverversion und ordnet sie dem AD FS-Schema zu. 



#### <a name="user-sign-ins"></a>Benutzeranmeldungen 
Jede Registerkarte auf dem Blatt „Anmeldungen“ enthält die unten aufgeführten Standardwerte:
* Sign-in date (Anmeldedatum)
* Anfrage-ID
* Benutzername oder Benutzer-ID
* Status der Anmeldung
* IP-Adresse des für die Anmeldung verwendeten Geräts
* Anmeldebezeichner

#### <a name="authentication-method-information"></a>Informationen zur Authentifizierungsmethode
Die folgenden Werte können auf der Registerkarte „Authentifizierung" angezeigt werden. Die Authentifizierungsmethode wird aus den AD FS-Auditprotokollen übernommen.

|Authentifizierungsmethode|BESCHREIBUNG|
|-----|-----|
|Formulare|Authentifizierung mit Benutzername/Kennwort|
|Windows|Integrierte Windows-Authentifizierung|
|Zertifikat|Authentifizierung mit SmartCard-/VirtualSmart-Zertifikaten|
|WindowsHelloForBusiness|Dieses Feld ist für die Authentifizierung mit Windows Hello for Business bestimmt. (Microsoft Passport-Authentifizierung)|
|Gerät | Wird angezeigt, wenn die Geräteauthentifizierung als „primäre“ Authentifizierung aus dem Intranet/Extranet ausgewählt ist und die Geräteauthentifizierung durchgeführt wird.  In diesem Szenario gibt es keine separate Benutzerauthentifizierung.| 
|Im Verbund|AD FS hat die Authentifizierung nicht durchgeführt, sondern an einen Identitätsanbieter eines Drittanbieters gesendet.|
|SSO |Dieses Feld wird angezeigt, wenn ein Token für einmaliges Anmelden verwendet wurde. Wenn einmaliges Anmelden eine MFA aufweist, wird Multifactor angezeigt.|
|Multifactor|Wenn ein Token für einmaliges Anmelden eine MFA aufweist und für die Authentifizierung verwendet wurde, wird in diesem Feld „Multifactor“ angezeigt.|
|Azure MFA|Azure MFA ist als zusätzlicher Authentifizierungsanbieter in AD FS ausgewählt und wurde für die Authentifizierung verwendet.|
|ADFSExternalAuthenticationProvider|Dieses Feld wird angezeigt, wenn ein Drittanbieter für die Authentifizierung registriert und für die Authentifizierung verwendet wurde.|


#### <a name="ad-fs-additional-details"></a>Zusätzliche Details zu AD FS
Bei AD FS-Anmeldungen sind folgende Details verfügbar:
* Servername
* IP-Adresskette
* Protokoll

### <a name="enabling-log-analytics-and-azure-monitor"></a>Aktivieren von Log Analytics und Azure Monitor
Log Analytics kann für AD FS-Anmeldungen aktiviert werden und mit sonstigen in Log Analytics integrierten Komponenten wie Sentinel verwendet werden.

> [!NOTE] 
> Log Analytics-Kosten können sich durch AD FS-Anmeldungen deutlich erhöhen, je nachdem, wie viele Anmeldungen bei AD FS in Ihrer Organisation zu verzeichnen sind. Aktivieren oder deaktivieren Sie das Kontrollkästchen für den Stream, um Log Analytics zu aktivieren bzw. zu deaktivieren.

Um Log Analytics für die Funktion zu aktivieren, navigieren Sie zum Blatt „Log Analytics“, und wählen Sie den Stream „ADFSSignIns“ aus. Wenn dieser ausgewählt ist, können AD FS-Anmeldungen an Log Analytics weitergeleitet werden.

Um auf die aktualisierte Azure Monitor-Arbeitsmappenvorlage zuzugreifen, navigieren Sie zu „Azure Monitor-Vorlagen“, und wählen Sie die Arbeitsmappe „Anmeldungen“ aus.
Weitere Informationen zu Arbeitsmappen finden Sie unter [Azure Monitor-Arbeitsmappen](https://aka.ms/adfssigninspreview).




### <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
***Welche Arten von Anmeldungen werden ggf. angezeigt?***
Der Anmeldebericht unterstützt Anmeldungen über die Protokolle O-Auth, WS-Fed, SAML und WS-Trust. 

***Wie werden unterschiedliche Arten von Anmeldungen im Anmeldebericht aufgeführt?***
Wenn nahtloses einmaliges Anmelden ausgeführt wird, gibt es eine Zeile zur Anmeldung mit einer Korrelations-ID.
Bei einer einstufigen Authentifizierung enthalten zwei Zeilen die gleiche Korrelations-ID, jedoch mit zwei verschiedenen Authentifizierungsmethoden (Formular, SSO).
Bei der Multi-Factor Authentication gibt es drei Zeilen mit gleicher Korrelations-ID und drei entsprechenden Authentifizierungsmethoden (Formular, AzureMFA, Multifactor). In diesem Fall weist „Multifactor“ eine MFA für das einmalige Anmelden aus.

***Welche Fehler werden ggf. im Bericht aufgeführt?***
Eine vollständige Liste mit Fehlern zu AD FS, die im Anmeldebericht und den entsprechenden Beschreibungen stehen können, finden Sie in der [Referenz zu Fehlercodes in der AD FS-Hilfe](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference).

***Ich sehe „00000000-0000-0000-0000-000000000000" im Abschnitt „Benutzer" bei einer Anmeldung. Was bedeutet das?***
Wenn bei der Anmeldung ein Fehler aufgetreten ist und der verwendete UPN nicht mit einem vorhandenen UPN-Wert identisch ist, enthalten die Felder „Benutzer“, „Benutzername“ und „Benutzer-ID“ den Wert „00000000-0000-0000-0000-000000000000“, und „Anmeldebezeichner“ enthält den beim Versuch vom Benutzer eingegebenen Wert. In diesen Fällen ist der Benutzer, der versucht, sich anzumelden, nicht vorhanden.

***Wie kann ich meine lokalen Ereignisse mit dem Azure AD-Anmeldebericht korrelieren?***
Der Azure AD Connect Health-Agent für AD FS korreliert Ereignis-IDs aus AD FS in Abhängigkeit von der Serverversion. Die Ereignisse sind dann im Sicherheitsprotokoll der AD FS-Server verfügbar. 

***Warum wird bei bestimmten AD FS-Anmeldungen in der Anwendungs-ID/dem Anwendungsnamen „NotSet“ oder „NotApplicable“ angezeigt?***
Der AD FS-Anmeldebericht zeigt für OAuth-Anmeldungen OAuth-IDs im Feld Anwendungs-ID an. In den WS-Fed-, WS-Trust-Sign-In-Szenarien wird die Anwendungs-ID auf NotSet oder NotApplicable gesetzt und die Ressourcen-IDs und Relying-Party-Identifikatoren werden im Feld Ressourcen-ID angezeigt.

***Warum wird für die Felder „Ressourcen-ID“ und „Ressourcenname“ der Status „NotSet“ angezeigt?***
Bei einigen Fehlern wird für die Felder „Ressourcen-ID“ und „Ressourcenname“ der Status „NotSet“ angezeigt, z. B. bei „Benutzername und Kennwort fehlerhaft“ und WSTrust-basierten Anmeldefehlern.

***Gibt es in der Vorschauversion weitere bekannte Probleme mit dem Bericht?***
Der Bericht weist ein bekanntes Problem auf, bei dem das Feld „Authentifizierungsanforderung“ auf der Registerkarte „Grundlegende Informationen“ unabhängig von der Anmeldung den Wert für eine einstufige Authentifizierung für AD FS-Anmeldungen enthält. Außerdem wird auf der Registerkarte „Authentifizierungsdetails“ unter dem Feld „Anforderung“ der Wert „Primär oder sekundär“ angezeigt, wobei gerade eine Korrektur in Arbeit ist, um die primären oder sekundären Authentifizierungstypen zu unterscheiden.


## <a name="related-links"></a>Verwandte Links
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Installieren des Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md)
* [Bericht über riskante IP-Adressen](how-to-connect-health-adfs-risky-ip.md)
