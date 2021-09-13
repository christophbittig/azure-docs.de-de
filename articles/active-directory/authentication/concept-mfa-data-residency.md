---
title: 'Azure Active Directory Multifactor Authentication: Datenresidenz'
description: Erfahren Sie, welche personen- und organisationsbezogenen Daten von Azure Active Directory Multifactor Authentication über Sie und Ihre Benutzer gespeichert werden und welche Daten im Ursprungsland bzw. in der Ursprungsregion verbleiben.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa8098192340505ecb7555f407375690c36ecdfb
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2021
ms.locfileid: "111525802"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Azure Active Directory Multifactor Authentication: Datenresidenz und Kundendaten

Azure Active Directory (Azure AD) speichert Kundendaten an einem geografischen Ort, der auf der Adresse basiert, die eine Organisation beim Abonnieren eines Microsoft-Onlinediensts wie Microsoft 365 oder Azure angibt. Informationen darüber, wo Ihre Kundendaten gespeichert werden, finden Sie im Microsoft Trust Center im Abschnitt [Wo wir Ihre Daten speichern](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located).

Von der cloudbasierten mehrstufigen Authentifizierung mit Azure AD und von Azure MFA Server werden personen- und organisationsbezogene Daten verarbeitet und gespeichert. In diesem Artikel wird beschrieben, welche Daten wo gespeichert werden.

Der Dienst „Azure Active Directory Multifactor Authentication“ nutzt Rechenzentren in den USA, Europa und der Region „Asien-Pazifik“. Sofern nicht anderes angegeben, erfolgen die folgenden Aktivitäten in den regionalen Rechenzentren:

* Telefonanrufe zur mehrstufigen Authentifizierung stammen aus Rechenzentren in der Region des Kunden und werden von globalen Netzbetreibern weitergeleitet. Telefonanrufe mit benutzerspezifischen Begrüßungen stammen immer aus Rechenzentren in den USA.
* Universelle Benutzerauthentifizierungsanforderungen aus anderen Regionen werden aktuell abhängig vom Standort des Benutzers verarbeitet.
* Von der App „Microsoft Authenticator“ gesendete Pushbenachrichtigungen werden derzeit in den regionalen Rechenzentren basierend auf dem Standort des Benutzers verarbeitet. Herstellerspezifische Gerätedienste wie Apple Push Notification Service können außerhalb des Benutzerstandorts angesiedelt sein.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Von Azure AD Multi-Factor Authentication gespeicherte personenbezogene Daten

Bei personenbezogenen Daten handelt es sich um Informationen auf Benutzerebene, die einer bestimmten Person zugeordnet sind. Die folgenden Datenspeicher enthalten persönliche Informationen:

* Blockierte Benutzer
* Umgangene Benutzer
* Änderungsanforderungen für Microsoft Authenticator-Gerätetoken
* Aktivitätsberichte für die mehrstufige Authentifizierung – Speichern der mehrstufigen Authentifizierungsaktivität aus den lokalen Komponenten der mehrstufigen Authentifizierung: NPS-Erweiterung, AD FS-Adapter und MFA-Server.
* Microsoft Authenticator-Aktivierungen

Diese Informationen werden 90 Tage lang aufbewahrt.

Azure AD Multi-Factor Authentication protokolliert keine personenbezogenen Daten, wie Benutzernamen, Telefonnummern oder IP-Adressen. Allerdings werden anhand von *UserObjectId* Authentifizierungsversuche von Benutzern erkannt. Protokolldaten werden für 30 Tage gespeichert.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Von Azure AD Multi-Factor Authentication gespeicherte Daten

Für öffentliche Azure-Clouds, mit Ausnahme der Azure AD B2C-Authentifizierung, der NPS-Erweiterung und des AD FS-Adapters (Active Directory Federation Services, Active Directory-Verbunddienste) für Windows Server 2016 oder 2019, werden die folgenden personenbezogenen Daten gespeichert:

| Ereignistyp                           | Datenspeichertyp |
|--------------------------------------|-----------------|
| OATH-Token                           | Multi-Factor Authentication-Protokolle     |
| Unidirektionale SMS                          | Multi-Factor Authentication-Protokolle     |
| Anruf                           | Multi-Factor Authentication-Protokolle<br/>Multi-Factor Authentication-Protokolle<br/>Blockierte Benutzer (bei Betrugsmeldung) |
| Microsoft Authenticator-Benachrichtigung | Multi-Factor Authentication-Protokolle<br/>Multi-Factor Authentication-Protokolle<br/>Blockierte Benutzer (bei Betrugsmeldung)<br/>Änderungsanforderungen bei Änderung des Microsoft Authenticator-Gerätetokens |

Für Microsoft Azure Government, Microsoft Azure Deutschland, Microsoft Azure, betrieben von 21ViaNet, Azure AD B2C-Authentifizierung, NPS-Erweiterung und AD FS-Adapter für Windows Server 2016 oder 2019 werden die folgenden personenbezogenen Daten gespeichert:

| Ereignistyp                           | Datenspeichertyp |
|--------------------------------------|-----------------|
| OATH-Token                           | Multi-Factor Authentication-Protokolle<br/>Multi-Factor Authentication-Protokolle |
| Unidirektionale SMS                          | Multi-Factor Authentication-Protokolle<br/>Multi-Factor Authentication-Protokolle |
| Anruf                           | Multi-Factor Authentication-Protokolle<br/>Multi-Factor Authentication-Protokolle<br/>Blockierte Benutzer (bei Betrugsmeldung) |
| Microsoft Authenticator-Benachrichtigung | Multi-Factor Authentication-Protokolle<br/>Multi-Factor Authentication-Protokolle<br/>Blockierte Benutzer (bei Betrugsmeldung)<br/>Änderungsanforderungen bei Änderung des Microsoft Authenticator-Gerätetokens |

### <a name="data-stored-by-mfa-server"></a>Daten, die von MFA Server gespeichert werden

Wenn Sie MFA Server nutzen, werden die folgenden personenbezogenen Daten gespeichert.

> [!IMPORTANT]
> Seit dem 1. Juli 2019 bietet Microsoft für neue Bereitstellungen keine MFA-Server mehr an. Neukunden, die von ihren Benutzer eine mehrstufige Authentifizierung anfordern möchten, können stattdessen cloudbasierte Azure AD Multi-Factor Authentication verwenden. Bestandskunden, die Multi-Factor Authentication-Server vor dem 1. Juli 2019 aktiviert haben, können weiterhin die neuesten Versionen und Updates herunterladen sowie Anmeldedaten zur Aktivierung wie gewohnt generieren.

| Ereignistyp                           | Datenspeichertyp |
|--------------------------------------|-----------------|
| OATH-Token                           | Multi-Factor Authentication-Protokolle<br />Multi-Factor Authentication-Protokolle |
| Unidirektionale SMS                          | Multi-Factor Authentication-Protokolle<br />Multi-Factor Authentication-Protokolle |
| Anruf                           | Multi-Factor Authentication-Protokolle<br />Multi-Factor Authentication-Protokolle<br />Blockierte Benutzer (bei Betrugsmeldung) |
| Microsoft Authenticator-Benachrichtigung | Multi-Factor Authentication-Protokolle<br />Multi-Factor Authentication-Protokolle<br />Blockierte Benutzer (bei Betrugsmeldung)<br />Änderungsanforderungen bei Änderung des Microsoft Authenticator-Gerätetokens |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Von Azure AD Multi-Factor Authentication gespeicherte organisationsbezogene Daten

Organisationsdaten sind Informationen auf Mandantenebene, die die Konfiguration oder Einrichtung der Umgebung verfügbar machen können. In den Mandanteneinstellungen können auf den folgenden Azure-Portalseiten zu Multi-Factor Authentication Organisationsdaten wie Informationen zu Sperrschwellenwerten oder zur Anruferkennung für eingehende telefonische Authentifizierungsanforderungen gespeichert sein:

* Kontosperrung
* Betrugswarnung
* Benachrichtigungen
* Einstellungen für Telefonanruf

Für MFA Server können die folgenden Azure-Portalseiten organisationsbezogene Daten enthalten:

* Servereinstellungen
* Einmalumgehung
* Cacheregeln
* Status des Multi-Factor Authentication-Servers

## <a name="multifactor-authentication-activity-reports-for-public-cloud"></a>Aktivitätsberichte für die mehrstufige Authentifizierung für die öffentliche Cloud

Aktivitätsberichte für die mehrstufige Authentifizierung speichern Aktivitäten aus den lokalen Komponenten der mehrstufigen Authentifizierung: NPS-Erweiterung, AD FS-Adapter und MFA-Server. Die Multi-Factor Authentication-Dienstprotokolle werden zum Betrieb des Diensts verwendet.
Die folgenden Abschnitte zeigen, wo Aktivitätsberichte und Dienstprotokolle für bestimmte Authentifizierungsmethoden für die jeweiligen Komponenten in verschiedenen Kundenregionen gespeichert werden. Standard-Sprachanrufe können per Failover auf eine andere Region umgeleitet werden.

>[!NOTE]
>Die Multi-Factor Authentication-Aktivitätsberichte enthalten personenbezogene Daten, etwa den Benutzerprinzipalnamen (User Principal Name, UPN) und die vollständige Telefonnummer.

### <a name="nps-extension-and-ad-fs-adapter"></a>NPS-Erweiterung und AD FS-Adapter

| Authentifizierungsmethode                                                             | Kundenregion                      | Speicherort des Aktivitätsberichts | Speicherort des Dienstprotokolls |
|-----------------------------------------------------------------------------------|--------------------------------------|--------------------------|----------------------|
| OATH-Software- und Hardwaretoken                                                 | Australien und Neuseeland            | Australien/Neuseeland    | Cloud in Region      |
| OATH-Software- und Hardwaretoken                                                 | Außerhalb von Australien und Neuseeland | USA            | Cloud in Region      |
| Sprachanrufe ohne benutzerspezifische Begrüßungen und alle anderen Authentifizierungsmethoden, außer OATH-Software- und Hardwaretoken  | Any                               | USA            | Cloud in Region      |
| Sprachanrufe mit benutzerdefinierten Begrüßungen                                         | Any                                  | USA            | MFA-Back-End in den USA |

### <a name="mfa-server-and-cloud-based-mfa"></a>MFA-Server und cloudbasierte MFA

| Komponente  | Authentifizierungsmethode                          | Kundenregion                      | Speicherort des Aktivitätsberichts        | Speicherort des Dienstprotokolls         |
|------------|------------------------------------------------|--------------------------------------|---------------------------------|------------------------------|
| MFA-Server | Alle Methoden                                    | Any                                  | USA                   | MFA-Back-End in den USA |
| Cloud-MFA  | Standard-Sprachanrufe und alle anderen Methoden     | Any                                  | Azure AD-Anmeldeprotokolle in Region | Cloud in Region              |
| Cloud-MFA  | Sprachanrufe mit benutzerdefinierten Begrüßungen              | Any                                  | Azure AD-Anmeldeprotokolle in Region | MFA-Back-End in den USA |

## <a name="multifactor-authentication-activity-reports-for-sovereign-clouds"></a>Aktivitätsberichte für die mehrstufige Authentifizierung für Sovereign Clouds

Die folgende Tabelle enthält den Speicherort für Dienstprotokolle für Sovereign Clouds:

| Sovereign Cloud                      | Anmeldeprotokolle                         | Multi-Factor Authentication-Aktivitätsbericht | Multi-Factor Authentication-Dienstprotokolle |
|--------------------------------------|--------------------------------------|--------------------------------------------|-----------------------------------------|
| Microsoft Azure Deutschland              | Deutschland                              | USA                              | USA                           |
| Azure China (21Vianet)                 | China                                | USA                              | USA                           |
| Microsoft Government Cloud           | USA                        | USA                              | USA                           |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Benutzerinformationen, die bei der cloudbasierten Azure AD Multi-Factor Authentication und von MFA Server gesammelt werden, finden Sie unter [Azure AD Multi-Factor Authentication: Benutzerdatensammlung](howto-mfa-reporting-datacollection.md).
