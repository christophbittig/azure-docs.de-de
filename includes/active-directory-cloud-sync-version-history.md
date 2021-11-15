---
ms.openlocfilehash: 9dabeaa9dd8e22e63b356768e34ebf828ac43632
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860752"
---
In diesem Artikel werden die veröffentlichten Versionen und Features des Azure Active Directory Connect-Bereitstellungs-Agent aufgeführt. Das Azure AD-Team aktualisiert den Bereitstellungs-Agent regelmäßig mit neuen Features und Funktionen. 

Microsoft stellt direkte Unterstützung für die neueste Agent-Version und die unmittelbare Vorgängerversion bereit.

## <a name="download-link"></a>Downloadlink
Sie können die neueste Version des Agent über [diesen Link](https://download.msappproxy.net/Subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/Connector/provisioningAgentInstaller) herunterladen.

## <a name="115870"></a>1.1.587.0

2\. November 2021 – Für den Download veröffentlicht

Neue Features und Verbesserungen

- Cmdlet zum Konfigurieren des Kennwortrückschreibens


## <a name="115840"></a>1.1.584.0 

20. August 2021 – Für den Download veröffentlicht

### <a name="fixed-issues"></a>Behobene Probleme

- Wir haben einen Fehler behoben, bei dem die Kennworthashsynchronisierung beim Umbenennen einer Domäne fehlschlägt und im Ereignisprotokoll die Meldung „Eine angegebene Umwandlung ist ungültig“ steht. Dies ist eine Regression aus früheren Builds.

## <a name="115820"></a>1.1.582.0

8\. August 2021 – Für den Download veröffentlicht

>[!NOTE] 
>Dies ist ein Sicherheitsupdaterelease von Azure AD Connect. Mit diesem Release soll wie in [diesem CVE-Bericht](https://msrc.microsoft.com/update-guide/vulnerability/CVE-2021-36949) dokumentiert ein Sicherheitsrisiko behoben werden. Weitere Informationen zu diesem Sicherheitsrisiko finden Sie im CVE-Bericht.

## <a name="113590"></a>1.1.359.0

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
- GMSA-Cmdlets zum Festlegen/Zurücksetzen von Berechtigungen

### <a name="fixed-issues"></a>Behobene Probleme
- Fehlerbehebung der GMSA-Ordnerberechtigung (ursprünglich führte das Problem zu Bootstrapproblemen)
- Fehlerbehebung für die Behandlung mehrerer Änderungen an einem einzelnen Wertverweisattribut (z. B. Manager)
- Fehlerbehebung für Fehler in der anfänglichen Enumeration sowie verbesserte Ablaufverfolgung des Fehlers
- Optimieren von Gruppenmitgliedschaftsupdates für eine Bereichsgruppe Dadurch können Kunden jetzt eine Gruppe von bis zu 50.000 Mitgliedern mithilfe des Bereichsfilters für Gruppen synchronisieren. 
- Unterstützung des Abrufens eines einzelnen Objekts durch DN mit Bereichsbestimmung, die von der Bedarfsbereitstellung verwendet wird, um Bereichslogik zu befolgen





## <a name="113540"></a>1.1.354.0

20. Januar 2021: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen
- Verbesserung der GMSA-Erfahrung, einschließlich Unterstützung vordefinierter GMSA-Konten
- Unterstützung von [PowerShell-Cmdlets](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) für das Setup von GMSA
- [CLI-Unterstützung](../articles/active-directory/cloud-sync/how-to-install-pshell.md) für die Agent-Installation (automatische Installation)
- Zusätzliche Diagnose für Quarantäneprobleme von Agent-Quellen
- Fehlerkorrekturen, die u. a. Reduzierung des Speicherbedarfs von Bereichsfiltern für Organisationseinheiten, Ausführen von PHS ausschließlich für Benutzer im Bereich, Behandlung geschachtelter Objekte in Organisationseinheiten bei Begrenzung von Organisationseinheiten auf bestimmte Bereiche usw. umfassen. 


### <a name="fixed-issues"></a>Behobene Probleme
-    Verhindern der Quarantäne, wenn sich die Bereichsgruppe nicht im Bereich befindet
-   Bei Konfiguration von Bereichsfiltern: PHS-Auftrag gilt ausschließlich für Benutzer im Bereich
-   Der Agent reagiert manchmal nicht mehr während des Upgrades
-   Anfängliche Synchronisierung für Objekte in geschachtelten Organisationseinheiten bei der Bereichsdefinition von Organisationseinheiten
-   Cmdlet Repair-AADCloudSyncToolsAccount nun stabiler
-   Reduzierung der großen Arbeitsspeicherbelegung von Bereichsfiltern für Organisationseinheiten
-   Die Überprüfung der Rolle „Administrator“ schlägt fehl, wenn die Rollenmitglieder eine Sicherheitsgruppe enthalten
-   Behebung des Problems mit der Berechtigung für den GMSA-Ordner, das das Verlängern des Agent-Zertifikats verhindert







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Releasestatus

23. November 2020: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Unterstützung für [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Unterstützung während des inkrementellen oder Delta-Synchronisierungsprozesses für Gruppen, die kleiner als 1500 Mitglieder sind. Dies gilt, wenn der Gruppenbereichsfilter verwendet wird.
* Unterstützung für große Gruppen mit einer Mitgliedsgröße von bis zu 15.000.
* Verbesserungen der anfänglichen Synchronisierung
* Erweiterte ausführliche Protokollierung
* Einführung des [AADCloudSyncTools-PowerShell-Moduls](../articles/active-directory/cloud-sync/reference-powershell.md)
* Einschränkungen für die Installation des Agents auf einem nicht englischsprachigen Server wurden korrigiert.
* Unterstützung für PHS-Filterung nur für Objekte im Gültigkeitsbereich (ursprünglich wurden Kennworthashes für alle Objekte synchronisiert)
* Es wurde ein Problem mit dem Arbeitsspeicherleck im Agent behoben.
* Verbesserte Bereitstellungsprotokolle
* Unterstützung für das Konfigurieren des [LDAP-Verbindungstimeouts](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Unterstützung für das Konfigurieren der [Empfehlungsverfolgung](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Releasestatus

4\. Dezember 2019 Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Unterstützung der Synchronisierung von Benutzer-, Kontakt- und Gruppendaten zwischen dem lokalen Active Directory und Azure AD für die [Azure AD Connect-Cloudsynchronisierung](../articles/active-directory/cloud-sync/what-is-cloud-sync.md).


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Releasestatus

9\. September 2019: Veröffentlichung für automatisches Update

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Möglichkeit zum Konfigurieren zusätzlicher Ablaufverfolgung und Protokollierung für das Debuggen des Bereitstellungs-Agent
* Möglichkeit zum Abrufen nur jener Azure AD-Attribute, die in der Zuordnung konfiguriert sind, um die Synchronisierungsleistung zu verbessern

### <a name="fixed-issues"></a>Behobene Probleme

* Ein Fehler wurde behoben, aufgrund dessen der Agent nicht mehr reagierte, wenn Probleme durch Azure AD-Verbindungsfehler aufgetreten sind.
* Ein Fehler wurde behoben, der beim Lesen von Binärdaten aus Azure Active Directory Probleme verursacht hat.
* Ein Fehler wurde behoben, aufgrund dessen Agent die Vertrauensstellung mit dem Cloud-Hybrididentitätsdienst nicht erneuern konnte.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Releasestatus

23. Januar 2019: Für den Download veröffentlicht

### <a name="new-features-and-improvements"></a>Neue Features und Verbesserungen

* Bereitstellungs-Agent und Connector-Architektur wurden für bessere Leistung, Stabilität und Zuverlässigkeit überarbeitet 
* Die Konfiguration des Bereitstellungs-Agent wurde durch Verwendung eines Installationsassistenten auf der Benutzeroberfläche vereinfacht
