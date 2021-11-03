---
title: 'Azure AD Connect Health: Versionsverlauf'
description: In diesem Dokument werden die Versionen von Azure AD Connect Health beschrieben und was darin enthalten ist.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc0495e4ddd3e266b375a9d6a137497f457803da
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049252"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Verlauf der Versionsveröffentlichungen
Das Azure Active Directory-Team aktualisiert Azure AD Connect Health regelmäßig mit neuen Features und Funktionen. In diesem Artikel werden die veröffentlichten Versionen und Features beschrieben.  

> [!NOTE]
> Connect Health-Agents werden automatisch aktualisiert, wenn die neue Version veröffentlicht wird. Stellen Sie im Azure-Portal sicher, dass die Einstellungen für die automatische Aktualisierung aktiviert sind.
>

Azure AD Connect Health für die Synchronisierung ist in die Azure AD Connect-Installation integriert. Weitere Informationen zum Releaseverlauf von Azure AD Connect finden Sie [hier](./reference-connect-version-history.md). Featurefeedback können Sie über den [Benutzerfeedbackkanal für Connect Health](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) abgeben.

## <a name="september-2021"></a>September 2021
**Agent-Aktualisierung**
- Der Azure AD Connect Health-Agent für die AD FS (Version 3.1.113.0)
  - Fix zum Extrahieren von Geräteinformationen wie Gerätekonformität und verwaltetem Status, Gerätebetriebssystem und Gerätebetriebssystemversion aus AD FS-Prüfungen in bestimmten gerätebasierten Authentifizierungsszenarios
  - Fix zum Ausfüllen von OAuth-Anwendungsinformationen in Fehlerfällen und Kategorisieren von OAuth-Fehlern mit spezifischeren Fehlercodes
  - Fix für Warnungen bei fehlerhaften WMI-Aufrufen auf dem Kundencomputer Jetzt würden solche Aufrufe das Ergebnis/den Status auf „notRun“ festlegen.

## <a name="may-2021"></a>Mai 2021
**Agent-Aktualisierung**
- Der Azure AD Connect Health-Agent für die AD FS (Version 3.1.99.0)
  - Korrektur für einen niedrigen eindeutigen Benutzeranzahlwert in dem AD FS-Anwendungsaktivitätsbericht
  - Korrektur für Anmeldungen mit leerer oder Standard GUID-Korrelations-ID

## <a name="march-2021"></a>März 2021
**Agent-Aktualisierung**

- Azure AD Connect Health-Agent für AD FS (Version 3.1.95.0)

  - Korrektur zum Auflösen des Benutzernamens von NT4 in einen UPN bei Anmeldungsereignissen.
  - Korrektur zur Identifizierung falscher Anwendungsbezeichner-Szenarien mit einem dedizierten Fehlercode.
  - Ändert das Hinzufügen einer neuen Eigenschaft für den OAuth-Client-Bezeichner.
  - Korrektur, um korrekte Werte in den Feldern **Protokoll** und **Authentifizierungstyp** in Azure AD Anmeldungsbericht für bestimmte Anmeldeszenarien anzuzeigen.
  - Korrektur zum Anzeigen von IP-Adressen in Azure AD IP-Kettenfeld des Anmeldeberichtes in der Reihenfolge der Anforderung.
  - Änderungen an der Einführung eines neuen Felds, um zu unterscheiden, ob bei der Anmeldung eine sekundäre Authentifizierung angefordert wurde.
  - Korrektur für AD FS Anwendungsbezeichner-Eigenschaft, die in Azure AD Anmeldebericht angezeigt werden soll.

## <a name="april-2020"></a>April 2020
**Agent-Aktualisierung**

- Azure AD Connect Health-Agent für AD FS (Version 3.1.77.0)

   - Fehlerbehebung für die Warnung "Ungültiger Dienstprinzipalname für AD FS-Dienst", für den die Warnung fehlerhaft ausgegeben wurde.


## <a name="july-2019"></a>Juli 2019
**Agent-Aktualisierung**
* Azure AD Connect Health-Agent für AD FS (Version 3.1.59.0) 
   1. Textänderung in TestWindowsTransport
   2. Änderungen für AD FS RP-Upload
   
* Azure AD Connect Health-Agent für AD FS (Version 3.1.56.0) 
   1. Hinzufügen des TestWindowsTransport-Tests und Entfernen von WsTrust-Endpunktprüfungen im CheckOffice365Endpoints-Test
   2. Protokollieren von Betriebssystem- und .NET-Informationen
   3. Die Uploadgröße für die RP-Konfigurationsmeldung wurde auf 1 MB erhöht.
   4. Behebung von Programmfehlern
   
* Azure AD Connect Health-Agent für AD DS (Version 3.1.56.0) 
   1. Protokollieren von Betriebssystem- und .NET-Informationen 
   2. Behebung von Programmfehlern

## <a name="may-2019"></a>Mai 2019
**Agent-Aktualisierung:** 
* Azure AD Connect Health-Agent für AD FS (Version 3.1.51.0) 
   1. Fehlerbehebung, um zwischen mehreren Anmeldungen zu unterscheiden, die die gleiche Clientanforderungs-ID verwenden
   2. Fehlerbehebung für die Analyse von Fehlern aufgrund von ungültigen Benutzernamen/Kennwörtern auf Servern mit lokalisierter Sprache   

## <a name="april-2019"></a>April 2019
**Agent-Aktualisierung:** 
* Azure AD Connect Health-Agent für AD FS (Version 3.1.46.0) 
   1. SPN-Warnungsprozess für AD FS beim Überprüfen auf Duplikate korrigiert

## <a name="march-2019"></a>März 2019
**Agent-Aktualisierung:** 
* Azure AD Connect Health-Agent für AD DS (Version 3.1.41.0)  
   1. .NET-Versionsauflistung
   2. Verbesserung der Erfassung von Leistungsindikatoren bei Fehlen bestimmter Kategorien
   3. Fehlerbehebung zum Verhindern der Erzeugung mehrerer Monitoring Agent-Instanzen

* Azure AD Connect Health-Agent für AD FS (Version 3.1.41.0) 
   1. Integrieren und Aktualisieren von AD FS-Testskripts über ADFSToolBox
   2. Implementieren der .NET-Versionsauflistung
   3. Verbesserung der Erfassung von Leistungsindikatoren bei Fehlen bestimmter Kategorien
   4. Fehlerbehebung zum Verhindern der Erzeugung mehrerer Monitoring Agent-Instanzen


## <a name="november-2018"></a>November 2018
**Neue allgemein verfügbare Features:** 
* Azure AD Connect Health für die Synchronisierung: Diagnostizieren und Beheben von Synchronisierungsfehlern durch doppelte Attribute über das Portal

**Agent-Aktualisierung:** 
* Azure AD Connect Health-Agent für AD DS (Version 3.1.24.0) 
   1. TLS-Protokollversion 1.2 (Transport Layer Security): Compliance und Erzwingung
   2. Verringerung des Warnungsaufkommens für den globalen Katalog
   3. Fehlerbehebungen für die Health-Agent-Registrierung

* Azure AD Connect Health-Agent für AD FS (Version 3.1.24.0)  
   1. TLS-Protokollversion 1.2 (Transport Layer Security): Compliance und Erzwingung
   2. Unterstützung von „Test-ADFSRequestToken“ für lokalisiertes Betriebssystem
   3. EventHandler-Sperrproblem des Diagnose-Agents behoben
   4. Fehlerbehebungen für die Health-Agent-Registrierung

## <a name="august-2018"></a>August 2018 
*  Azure AD Connect Health-Agent für die Synchronisierung (Version 3.1.7.0), veröffentlicht mit Azure AD Connect Version 1.1.880.0    
   1. Hotfix für das [Problem einer hohen CPU-Auslastung des Überwachungs-Agents mit .NET Framework-Releases in KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Juni 2018 
**Neue Vorschaufeatures:** 
* Azure AD Connect Health für die Synchronisierung: Diagnostizieren und Beheben von Synchronisierungsfehlern durch doppelte Attribute über das Portal 

**Agent-Aktualisierung:** 
* Azure AD Connect Health-Agent für AD DS (Version 3.1.7.0)    
  1. Hotfix für das [Problem einer hohen CPU-Auslastung des Überwachungs-Agents mit .NET Framework-Releases in KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Azure AD Connect Health-Agent für AD FS (Version 3.1.7.0)  
  1. Hotfix für das [Problem einer hohen CPU-Auslastung des Überwachungs-Agents mit .NET Framework-Releases in KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Korrekturen von Testergebnissen auf dem sekundären Server von ADFS Server 2016
   
* Azure AD Connect Health-Agent für AD FS (Version 3.1.2.0)  
  1. Hotfix für Agent-Speicherverwaltung und zugehörige Warnungen speziell für Version 3.0.244.0


## <a name="may-2018"></a>Mai 2018
**Agent-Aktualisierung:**
* Azure AD Connect Health-Agent für AD DS (Version 3.0.244.0)
  1. Verbesserung des Datenschutzes für den Agent  
  2. Fehlerbehebungen und allgemeine Verbesserungen

* Azure AD Connect Health-Agent für AD FS (Version 3.0.244.0)
  1. Verbesserungen für den Agent-Diagnosedienst und das dazugehörige PowerShell-Modul
  2. Verbesserung des Datenschutzes für den Agent  
  3. Fehlerbehebungen und allgemeine Verbesserungen

* Azure AD Connect Health-Agent für die Synchronisierung (Version 3.0.164.0), veröffentlicht mit Azure AD Connect Version 1.1.819.0 
  1. Verbesserung des Datenschutzes für den Agent  
  2. Fehlerbehebungen und allgemeine Verbesserungen


## <a name="march-2018"></a>März 2018
**Neue Vorschaufeatures:**
* Azure AD Connect Health für AD FS – Bericht über riskante IP-Adressen und Warnungen.

**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD DS (Version 3.0.176.0)
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Fehlerbehebungen und allgemeine Verbesserungen
* Azure AD Connect Health-Agent für AD FS (Version 3.0.176.0)
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Fehlerbehebungen und allgemeine Verbesserungen
* Azure AD Connect Health-Agent für die Synchronisierung (Version 3.0.129.0), veröffentlicht mit Azure AD Connect Version 1.1.750.0  
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Fehlerbehebungen und allgemeine Verbesserungen

## <a name="december-2017"></a>Dezember 2017
**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD DS (Version 3.0.145.0)
  1. Verbesserungen bei der Agent-Verfügbarkeit 
  2. Neue Befehle zur Problembehandlung beim Agent hinzugefügt
  3. Fehlerbehebungen und allgemeine Verbesserungen
* Azure AD Connect Health-Agent für AD FS (Version 3.0.145.0)
  1. Neue Befehle zur Problembehandlung beim Agent hinzugefügt
  2. Verbesserungen bei der Agent-Verfügbarkeit 
  3. Fehlerbehebungen und allgemeine Verbesserungen
  
## <a name="october-2017"></a>Oktober 2017
**Agent-Aktualisierung:**

 * Azure AD Connect Health-Agent für Sync (Version 3.0.129.0), veröffentlicht mit Azure AD Connect Version 1.1.649.0
<br></br> Ein Problem mit der Versionskompatibilität zwischen Azure AD Connect und dem Azure AD Connect Health-Agent für die Synchronisierung wurde behoben. Dieses Problem betrifft Kunden, die ein direktes Upgrade von Azure AD Connect auf Version 1.1.647.0 durchführen, aber derzeit die Health-Agent Version 3.0.127.0 verwenden. Nach dem Upgrade kann der Health-Agent keine Integritätsdaten mehr über den Azure AD Connect-Synchronisierungsdienst an den Azure AD-Integritätsdienst senden. Behoben wird dieses Problem, indem Version 3.0.129.0 des Health-Agents während des direkten Upgrades von Azure AD Connect installiert wird. Zwischen Version 3.0.129.0 des Health-Agents und Version 1.1.649.0 von Azure AD Connect bestehen keine Kompatibilitätsprobleme.

## <a name="july-2017"></a>Juli 2017
**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD DS (Version 3.0.68.0)
  1. Fehlerbehebungen und allgemeine Verbesserungen
  2. Unterstützung für unabhängige Clouds
* Azure AD Connect Health-Agent für AD FS (Version 3.0.68.0)
  1. Fehlerbehebungen und allgemeine Verbesserungen
  2. Unterstützung für unabhängige Clouds
* Azure AD Connect Health-Agent für Sync (Version 3.0.68.0), veröffentlicht mit Azure AD Connect Version 1.1.614.0
  1. Unterstützung für Microsoft Azure Government-Cloud und Microsoft Cloud Deutschland

## <a name="april-2017"></a>April 2017      
**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD FS (Version 3.0.12.0)
  1. Fehlerbehebungen und allgemeine Verbesserungen
* Azure AD Connect Health-Agent für AD DS (Version 3.0.12.0)
  1. Verbesserungen beim Hochladen von Leistungsindikatoren
  2. Fehlerbehebungen und allgemeine Verbesserungen

## <a name="october-2016"></a>Oktober 2016
**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD FS (Version 2.6.408.0)
* Verbesserungen beim Erkennen von Client-IP-Adressen bei Authentifizierungsanforderungen
* Fehlerbehebungen im Zusammenhang mit Warnungen
* Azure AD Connect Health-Agent für AD DS (Version 2.6.408.0)
* Fehlerbehebungen im Zusammenhang mit Warnungen.
* Azure AD Connect Health-Agent für Sync (Version 2.6.353.0), veröffentlicht mit Azure AD Connect Version 1.1.281.0
* Bereitstellen der für die Berichte zu Synchronisierungsfehlern benötigten Daten
* Fehlerbehebungen im Zusammenhang mit Warnungen

**Neue Vorschaufeatures:**

* Berichte zu Synchronisierungsfehlern für Azure AD Connect

**Neue Features:**

* Azure AD Connect Health für AD FS – IP-Adressfeld ist im Bericht über die 50 häufigsten Benutzer mit falschem Benutzernamen/Kennwort verfügbar.

## <a name="july-2016"></a>Juli 2016
**Neue Vorschaufeatures:**

* [Azure AD Connect Health für AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Januar 2016
**Agent-Aktualisierung:**

* Azure AD Connect Health-Agent für AD FS (Version 2.6.91.1512)

**Neue Features:**

* [Tool zum Testen der Verbindung für Azure AD Connect Health-Agents](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>November 2015
**Neue Features:**

* Unterstützung für die [rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

**Neue Vorschaufeatures:**

* [Azure AD Connect Health für die Synchronisierung](how-to-connect-health-sync.md)

**Behobene Probleme:**

* Fehlerbehebungen für bei Agent-Registrierungen aufgetretene Fehler

## <a name="september-2015"></a>September 2015
**Neue Features:**

* Bericht über falschen Benutzernamen/falsches Kennwort für AD FS
* Konfigurationsunterstützung für nicht authentifizierten HTTP-Proxy
* Konfigurationsunterstützung für Agents auf Serverkern
* Verbesserte Warnungen für AD FS
* Verbesserte Verbindungen und Datenuploads beim Azure AD Connect Health-Agent für AD FS

**Behobene Probleme:**

* Fehlerbehebungen bei den Nutzungsinformationen für AD FS-Fehlertypen

## <a name="june-2015"></a>Juni 2015
**Erste Version von Azure AD Connect Health für AD FS und AD FS-Proxy**

**Neue Features:**

* Warnungen für die Überwachung von AD FS und AD FS-Proxy-Server mit E-Mail-Benachrichtigungen
* Einfacher Zugriff auf AD FS-Topologien und Muster in AD FS-Leistungsindikatoren
* Trend bei erfolgreichen Tokenanforderungen auf AD FS-Servern gruppiert nach Anwendungen, Authentifizierungsmethoden, Netzwerkspeicherort-Anforderungen usw.
* Trends bei fehlgeschlagenen Anforderungen an AD FS-Server gruppiert nach Anwendungen, Fehlertypen usw.
* Einfachere Agent-Bereitstellung mit den Anmeldeinformationen für globale Azure AD-Administratoren  

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Überwachen Ihrer lokalen Identitätsinfrastruktur und Synchronisierung von Diensten in der Cloud](./whatis-azure-ad-connect.md)
