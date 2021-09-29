---
title: Steuerungen des Blaupausenbeispiels für ISM PROTECTED der australischen Regierung
description: Steuerungszuordnung des Blaupausenbeispiels für „ISM PROTECTED der australischen Regierung“ Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 293c1a0566b830f9aab84d3c675479f91eff4976
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128632270"
---
# <a name="control-mapping-of-the-australian-government-ism-protected-blueprint-sample"></a>Steuerungszuordnung des Blaupausenbeispiels für „ISM PROTECTED der australischen Regierung“

In diesem Artikel wird beschrieben, wie das Blaupausenbeispiel „ISM PROTECTED der australischen Regierung“ von Azure Blueprints den ISM PROTECTED-Steuerungen zugeordnet ist. Weitere Informationen zu den Steuerungen finden Sie unter [ISM PROTECTED](https://www.cyber.gov.au/ism).

Für die **ISM PROTECTED**-Steuerungen gelten die folgenden Zuordnungen. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **\[Vorschauversion\]: ISM PROTECTED-Steuerungen der australischen Regierung überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen**.

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../../../policy/overview.md)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../../../policy/how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und Azure Policy-Definitionen für dieses Konformitätsblaupausenbeispiel können sich im Laufe der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ism-protected/control-mapping.md).

## <a name="location-constraints"></a>Standorteinschränkungen

Mit dieser Blaupause können Sie den Standort für die Bereitstellung aller Ressourcen und Ressourcengruppen auf „Australien, Mitte“, „Australien, Mitte 2“, „Australien, Osten“ und „Australien, Südosten“ beschränken, indem Sie die folgenden Azure Policy-Definitionen zuweisen:

- Zulässige Standorte (hartcodiert für „Australien, Mitte“, „Australien, Mitte 2“, „Australien, Osten“ und „Australien, Südosten“)
- Zulässige Standorte für Ressourcengruppen (hartcodiert für „Australien, Mitte“, „Australien, Mitte 2“, „Australien, Osten“ und „Australien, Südosten“)

## <a name="guidelines-for-personnel-security---access-to-systems-and-their-resources"></a>Richtlinien zur Sicherheit des Personals: Zugriff auf Systeme und deren Ressourcen

### <a name="0414-personnel-granted-access-to-a-system-and-its-resources-are-uniquely-identifiable"></a>0414 Personal, dem Zugriff auf ein System und die zugehörigen Ressourcen gewährt wird, ist eindeutig identifizierbar

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein

### <a name="1503-standard-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1503 Standardzugriff auf Systeme, Anwendungen und Datenrepositorys ist auf die Berechtigungen beschränkt, die vom Personal für dessen Aufgaben benötigt werden

- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Überwachungsergebnisse von Windows-VMs anzeigen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält

### <a name="1507-privileged-access-to-systems-applications-and-data-repositories-is-validated-when-first-requested-and-revalidated-on-an-annual-or-more-frequent-basis"></a>1507 Privilegierter Zugriff auf Systeme, Anwendungen und Datenrepositorys wird überprüft, wenn dieser erstmalig angefordert wird, und dann mindestens jährlich einer erneuten Überprüfung unterzogen

- Überwachungsergebnisse von Windows-VMs anzeigen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält

### <a name="1508-privileged-access-to-systems-applications-and-data-repositories-is-limited-to-that-required-for-personnel-to-undertake-their-duties"></a>1508 Privilegierter Zugriff auf Systeme, Anwendungen und Datenrepositorys ist auf die Berechtigungen beschränkt, die vom Personal für dessen Aufgaben benötigt werden

- Maximal 3 Besitzer sollten für Ihr Abonnement festgelegt sein.
- Ihrem Abonnement sollte mehr als ein Besitzer zugewiesen sein.
- Überwachungsergebnisse von Windows-VMs anzeigen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Die Just-In-Time-Netzwerkzugriffssteuerung sollte auf virtuelle Computer angewendet werden.

### <a name="0415-the-use-of-shared-user-accounts-is-strictly-controlled-and-personnel-using-such-accounts-are-uniquely-identifiable"></a>0415 Die Nutzung von gemeinsamen Benutzerkonten wird streng kontrolliert, und Personal, das diese Konten verwendet, ist eindeutig identifizierbar

- Überwachungsergebnisse von Windows-VMs anzeigen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält

### <a name="0445-privileged-users-are-assigned-a-dedicated-privileged-account-to-be-used-solely-for-tasks-requiring-privileged-access"></a>0445 Privilegierten Benutzern wird ein dediziertes privilegiertes Konto zugewiesen, das ausschließlich für Aufgaben bestimmt ist, für die privilegierter Zugriff erforderlich ist

- Überwachungsergebnisse von Windows-VMs anzeigen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält
- Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, bei denen die Gruppe „Administratoren“ beliebige der angegebenen Mitglieder enthält

### <a name="0430-access-to-systems-applications-and-data-repositories-is-removed-or-suspended-on-the-same-day-personnel-no-longer-have-a-legitimate-requirement-for-access"></a>0430 Zugriff auf Systeme, Anwendungen und Datenrepositorys wird an dem Tag entfernt oder ausgesetzt, an dem für Personal kein legitimer Zugriff mehr erforderlich ist

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

### <a name="0441-when-personnel-are-granted-temporary-access-to-a-system-effective-security-controls-are-put-in-place-to-restrict-their-access-to-only-information-required-for-them-to-undertake-their-duties"></a>0441 Wenn für Personal temporärer Zugriff auf ein System gewährt wird, werden effektive Sicherheitskontrollen installiert, um den Zugriff ausschließlich auf Informationen zu beschränken, die für die Erledigung der Aufgaben benötigt werden

- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

## <a name="guidelines-for-system-hardening---operating-system-hardening"></a>Richtlinien für die Systemhärtung: Härtung des Betriebssystems

### <a name="1407-the-latest-version-n-or-n-1-version-of-an-operating-system-is-used-for-standard-operating-environments-soes"></a>1407 Für Standardbetriebsumgebungen (Standard Operating Environments, SOEs) wird die aktuelle Version (N) oder die Version N-1 eines Betriebssystems verwendet

- Systemupdates sollten auf Ihren Computern installiert sein.
- Systemupdates für VM-Skalierungsgruppen sollten installiert werden.

### <a name="0380-unneeded-operating-system-accounts-software-components-services-and-functionality-are-removed-or-disabled"></a>0380 Nicht benötigte Konten, Software, Komponenten, Dienste und Funktionen von Betriebssystemen werden entfernt oder deaktiviert

- Veraltete Konten sollten aus Ihrem Abonnement entfernt werden.
- Veraltete Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.

### <a name="1490-an-application-whitelisting-solution-is-implemented-on-all-servers-to-restrict-the-execution-of-executables-software-libraries-scripts-and-installers-to-an-approved-set"></a>1490 Eine Lösung für das Whitelisting von Anwendungen wird auf allen Servern implementiert, um die Ausführung von ausführbaren Dateien, Softwarebibliotheken, Skripts und Installationsprogrammen auf eine genehmigte Gruppe zu beschränken

- Die adaptive Anwendungssteuerung sollte auf virtuellen Computern aktiviert werden.

### <a name="1417-antivirus-software-is-implemented-on-workstations-and-servers-and-configured-with-signature-based-detection-enabled-and-set-to-a-high-level-heuristic-based-detection-enabled-and-set-to-a-high-level-detection-signatures-checked-for-currency-and-updated-on-at-least-a-daily-basis-automatic-and-regular-scanning-configured-for-all-fixed-disks-and-removable-media"></a>1417 Antivirensoftware wird auf Arbeitsstationen und Servern implementiert und wie folgt konfiguriert: aktivierte Erkennung auf Signaturbasis und Einstellung auf eine hohe Ebene, Erkennung auf Heuristikbasis und Einstellung auf eine hohe Ebene, Überprüfung von Erkennungssignaturen auf Aktualität und mindestens tägliche Aktualisierung, Konfiguration von automatischen regelmäßigen Scans für alle fest installierten Datenträger und Wechselmedien

- Die Microsoft IaaSAntimalware-Erweiterung muss auf Windows-Servern bereitgestellt werden
- Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.
- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen

## <a name="guidelines-for-system-hardening---authentication-hardening"></a>Richtlinien für die Systemhärtung: Härtung der Authentifizierung

### <a name="1546-users-are-authenticated-before-they-are-granted-access-to-a-system-and-its-resources"></a>1546 Benutzer werden authentifiziert, bevor ihnen Zugriff auf ein System und die zugehörigen Ressourcen gewährt wird

- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen
- Service Fabric-Cluster sollten nur Azure Active Directory für die Clientauthentifizierung verwenden.
- Überwachungsergebnisse von Linux-VMs anzeigen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Erforderliche Komponenten zum Überwachen von Linux-VMs bereitstellen, die Remoteverbindungen über Konten ohne Kennwörter zulassen
- Überwachungsergebnisse von Linux-VMs anzeigen, die Konten ohne Kennwörter verwenden
- Erforderliche Komponenten für die Überwachung von Linux-VMs bereitstellen, die Konten ohne Kennwörter verwenden

### <a name="0974-multi-factor-authentication-is-used-to-authenticate-standard-users"></a>0974 Mehrstufige Authentifizierung wird verwendet, um Standardbenutzer zu authentifizieren

- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein

### <a name="1173-multi-factor-authentication-is-used-to-authenticate-all-privileged-users-and-any-other-positions-of-trust"></a>1173 Mehrstufige Authentifizierung wird verwendet, um alle privilegierten Benutzer und alle anderen Vertrauenspositionen zu authentifizieren

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden

### <a name="0421-passphrases-used-for-single-factor-authentication-are-a-minimum-of-14-characters-with-complexity-ideally-as-4-random-words"></a>0421 Passphrases, die für die einstufige Authentifizierung verwendet werden, verfügen über mindestens 14 Zeichen und eine Komplexität, die idealerweise vier zufällige Wörter umfasst

- Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Sicherheitseinstellungen – Kontorichtlinien“ anzeigen
- Erforderliche Komponenten für die Überwachung von Windows-VM-Konfigurationen in „Sicherheitseinstellungen – Kontorichtlinien“ bereitstellen

## <a name="guidelines-for-system-management---system-administration"></a>Richtlinien für die Systemverwaltung: Systemverwaltung

### <a name="1384-multi-factor-authentication-is-used-to-authenticate-users-each-time-they-perform-privileged-actions"></a>1384 Mehrstufige Authentifizierung wird verwendet, um Benutzer bei jeder Durchführung von privilegierten Aktionen zu authentifizieren

- MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.
- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein

### <a name="1386-management-traffic-is-only-allowed-to-originate-from-network-zones-that-are-used-to-administer-systems-and-applications"></a>1386 Verwaltungsdatenverkehr darf nur aus Netzwerkzonen stammen, die zum Verwalten von Systemen und Anwendungen genutzt werden

- Die Just-In-Time-Netzwerkzugriffssteuerung sollte auf virtuelle Computer angewendet werden.
- Remotedebuggen sollte für API-Apps deaktiviert sein.
- Remotedebuggen sollte für Funktions-Apps deaktiviert sein.
- Remotedebuggen sollte für Webanwendungen deaktiviert sein.

## <a name="guidelines-for-system-management---system-patching"></a>Richtlinien für die Systemverwaltung: Systempatching

### <a name="1144-security-vulnerabilities-in-applications-and-drivers-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1144 Sicherheitsrisiken in Anwendungen und Treibern, die eine extrem hohe Risikoeinstufung aufweisen, werden innerhalb von 48 Stunden, nachdem sie von Anbietern, unabhängigen Dritten oder Systemmanagern oder -benutzern identifiziert werden, gepatcht, per Update behoben oder beseitigt

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren
- Sicherheitsrisikobewertung für Computer muss aktiviert sein.
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.
- Die Einstellungen für die Sicherheitsrisikobewertung für SQL Server sollten eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten.

### <a name="0940-security-vulnerabilities-in-applications-and-drivers-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>0940 Sicherheitsrisiken in Anwendungen und Treibern, die eine hohe Risikoeinstufung aufweisen, werden innerhalb von zwei Wochen, nachdem sie von Anbietern, unabhängigen Dritten oder Systemmanagern oder -benutzern identifiziert werden, gepatcht, per Update behoben oder beseitigt

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren
- Die Sicherheitsrisikobewertung muss auf VMs aktiviert sein
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.
- Die Einstellungen für die Sicherheitsrisikobewertung für SQL Server sollten eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten.

### <a name="1472-security-vulnerabilities-in-applications-and-drivers-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1472 Sicherheitsrisiken in Anwendungen und Treibern, die eine mittlere oder niedrige Risikoeinstufung aufweisen, werden innerhalb von einem Monat, nachdem sie von Anbietern, unabhängigen Dritten oder Systemmanagern oder -benutzern identifiziert werden, gepatcht, per Update behoben oder beseitigt

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren
- Die Sicherheitsrisikobewertung muss auf VMs aktiviert sein
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.
- Die Einstellungen für die Sicherheitsrisikobewertung für SQL Server sollten eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten.

### <a name="1494-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-extreme-risk-are-patched-updated-or-mitigated-within-48-hours-of-the-security-vulnerabilities-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1494 Sicherheitsrisiken in Betriebssystemen und Firmware, die eine extrem hohe Risikoeinstufung aufweisen, werden innerhalb von 48 Stunden, nachdem sie von Anbietern, unabhängigen Dritten oder Systemmanagern oder -benutzern identifiziert werden, gepatcht, per Update behoben oder beseitigt

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren
- Die Sicherheitsrisikobewertung muss auf VMs aktiviert sein
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.
- Die Einstellungen für die Sicherheitsrisikobewertung für SQL Server sollten eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten.

### <a name="1495-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-high-risk-are-patched-updated-or-mitigated-within-two-weeks-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1495 Sicherheitsrisiken in Betriebssystemen und Firmware, die eine hohe Risikoeinstufung aufweisen, werden innerhalb von zwei Wochen, nachdem sie von Anbietern, unabhängigen Dritten oder Systemmanagern oder -benutzern identifiziert werden, gepatcht, per Update behoben oder beseitigt

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren
- Die Sicherheitsrisikobewertung muss auf VMs aktiviert sein
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.
- Die Einstellungen für die Sicherheitsrisikobewertung für SQL Server sollten eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten.

### <a name="1496-security-vulnerabilities-in-operating-systems-and-firmware-assessed-as-moderate-or-low-risk-are-patched-updated-or-mitigated-within-one-month-of-the-security-vulnerability-being-identified-by-vendors-independent-third-parties-system-managers-or-users"></a>1496 Sicherheitsrisiken in Betriebssystemen und Firmware, die eine mittlere oder niedrige Risikoeinstufung aufweisen, werden innerhalb von einem Monat, nachdem sie von Anbietern, unabhängigen Dritten oder Systemmanagern oder -benutzern identifiziert werden, gepatcht, per Update behoben oder beseitigt

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren
- Die Sicherheitsrisikobewertung muss auf VMs aktiviert sein
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.
- Die Einstellungen für die Sicherheitsrisikobewertung für SQL Server sollten eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten.

## <a name="guidelines-for-system-management---data-backup-and-restoration"></a>Richtlinien für die Systemverwaltung: Datensicherung und -wiederherstellung

### <a name="1511-backups-of-important-information-software-and-configuration-settings-are-performed-at-least-daily"></a>1511 Sicherungen von wichtigen Informationen, Software und Konfigurationseinstellungen werden mindestens täglich durchgeführt

- Überwachen von virtuellen Computern ohne konfigurierte Notfallwiederherstellung

## <a name="guidelines-for-system-monitoring---event-logging-and-auditing"></a>Richtlinien für die Systemüberwachung: Ereignisprotokollierung und -überwachung

### <a name="1405-a-centralised-logging-facility-is-implemented-and-systems-are-configured-to-save-event-logs-to-the-centralised-logging-facility-as-soon-as-possible-after-each-event-occurs"></a>1405 Eine zentralisierte Protokollierungseinrichtung wird implementiert, und die Systeme werden so konfiguriert, dass Ereignisprotokolle nach dem Eintreten eines Ereignisses so schnell wie möglich in der zentralisierten Protokollierungseinrichtung gespeichert werden

- Azure-Abonnements benötigen ein Protokollprofil für das Aktivitätsprotokoll

### <a name="0582-the-following-events-are-logged-for-operating-systems-access-to-important-data-and-processes-application-crashes-and-any-error-messages-attempts-to-use-special-privileges-changes-to-accounts-changes-to-security-policy-changes-to-system-configurations-domain-name-system-dns-and-hypertext-transfer-protocol-http-requests-failed-attempts-to-access-data-and-system-resources-service-failures-and-restarts-system-startup-and-shutdown-transfer-of-data-to-external-media-user-or-group-management-use-of-special-privileges"></a>0582 Für Betriebssysteme werden die folgenden Ereignisse protokolliert: Zugriff auf wichtige Daten und Prozesse, Anwendungsabstürze und alle Fehlermeldungen, versuchte Nutzung von besonderen Privilegien, Änderungen von Konten, Änderungen von Sicherheitsrichtlinien, Änderungen von Systemkonfigurationen, DNS- (Domain Name System) und HTTP-Anforderungen (Hypertext Transfer Protocol), fehlgeschlagene Zugriffsversuche auf Daten und Systemressourcen, Dienstausfälle und -neustarts, Starten und Herunterfahren des Systems, Übertragung von Daten auf externe Medien, Benutzer- oder Gruppenverwaltung, Nutzung von besonderen Privilegien

- \[Vorschau\]: Bereitstellung des Log Analytics-Agents überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Bereitstellung des Log Analytics-Agents in VM-Skalierungsgruppen überwachen – VM-Image (Betriebssystem) nicht aufgelistet
- Überwachen des Log Analytics-Arbeitsbereichs für VM – Berichtskonflikt
- Überwachen der Diagnoseeinstellung

### <a name="1537-the-following-events-are-logged-for-databases-access-to-particularly-important-information-addition-of-new-users-especially-privileged-users-any-query-containing-comments-any-query-containing-multiple-embedded-queries-any-query-or-database-alerts-or-failures-attempts-to-elevate-privileges-attempted-access-that-is-successful-or-unsuccessful-changes-to-the-database-structure-changes-to-user-roles-or-database-permissions-database-administrator-actions-database-logons-and-logoffs-modifications-to-data-use-of-executable-commands"></a>1537 Für Datenbanken werden die folgenden Ereignisse protokolliert: Zugriff auf besonders wichtige Informationen, Hinzufügung neuer Benutzer, Benutzer mit speziellen Privilegien, Abfragen mit Kommentaren, Abfragen mit mehreren eingebetteten Abfragen, Warnungen oder Fehler für Abfragen oder Datenbanken, Versuche zur Rechteerweiterung, erfolgreiche und nicht erfolgreiche Zugriffsversuche, Änderungen der Datenbankstruktur, Änderungen von Benutzerrollen oder Datenbankberechtigungen, Aktionen von Datenbankadministratoren, Datenbankan- und -abmeldungen, Änderungen von Daten, Verwendung von ausführbaren Befehlen

- Advanced Data Security muss für Ihre SQL-Server aktiviert werden.
- Überwachen der Diagnoseeinstellung
- Advanced Data Security muss für Ihre verwalteten SQL-Instanzen aktiviert werden.

## <a name="guidelines-for-system-monitoring---vulnerability-management"></a>Richtlinien für die Systemüberwachung: Verwaltung von Sicherheitsrisiken

### <a name="0911-vulnerability-assessments-and-penetration-tests-are-conducted-by-suitably-skilled-personnel-before-a-system-is-deployed-after-a-significant-change-to-a-system-and-at-least-annually-or-as-specified-by-the-system-owner"></a>0911 Sicherheitsrisikobewertungen und Penetrationstests werden von entsprechend geschultem Personal vor der Bereitstellung eines Systems, nach einer signifikanten Änderung eines Systems und mindestens jährlich oder wie vom Systembesitzer vorgegeben durchgeführt

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.
- Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren
- Die Sicherheitsrisikobewertung muss auf VMs aktiviert sein
- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden.
- Sicherheitsrisiken in der Sicherheitskonfiguration für Ihre Computer sollten beseitigt werden.
- Sicherheitsrisiken in Containersicherheitskonfigurationen sollten behoben werden.

## <a name="guidelines-for-database-systems-management---database-servers"></a>Richtlinien für Datenbanksystemverwaltung: Datenbankserver

### <a name="1425-hard-disks-of-database-servers-are-encrypted-using-full-disk-encryption"></a>1425 Festplatten von Datenbankservern werden per vollständiger Datenträgerverschlüsselung verschlüsselt

- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.

### <a name="1277-information-communicated-between-database-servers-and-web-applications-is-encrypted"></a>1277 Informationen, die zwischen Datenbankservern und Webanwendungen ausgetauscht werden, werden verschlüsselt

- Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden
- Sichere Übertragung in Speicherkonten sollte aktiviert werden.
- Überwachungsergebnisse von Windows-Webservern anzeigen, die keine sicheren Kommunikationsprotokolle verwenden
- Erforderliche Komponenten zum Überwachen von Windows-Webservern bereitstellen, die keine sicheren Kommunikationsprotokolle verwenden

## <a name="guidelines-for-database-systems-management---database-management-system-software"></a>Richtlinien für die Datenbanksystemverwaltung: Software für Datenbankverwaltungssystem

### <a name="1260-default-database-administrator-accounts-are-disabled-renamed-or-have-their-passphrases-changed"></a>1260 Datenbankadministrator-Standardkonten werden deaktiviert oder umbenannt, oder die zugehörigen Passphrases werden geändert

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden

### <a name="1262-database-administrators-have-unique-and-identifiable-accounts"></a>1262 Datenbankadministratoren verfügen über eindeutige und identifizierbare Konten

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden

### <a name="1261-database-administrator-accounts-are-not-shared-across-different-databases"></a>1261 Datenbankadministrator-Konten werden nicht über verschiedene Datenbanken hinweg gemeinsam genutzt

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden

### <a name="1263-database-administrator-accounts-are-used-exclusively-for-administrative-tasks-with-standard-database-accounts-used-for-general-purpose-interactions-with-database"></a>1263 Datenbankadministrator-Konten werden ausschließlich für Verwaltungsaufgaben verwendet, und für allgemeine Interaktionen mit der Datenbank werden Standard-Datenbankkonten genutzt

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden

### <a name="1264-database-administrator-access-is-restricted-to-defined-roles-rather-than-accounts-with-default-administrative-permissions-or-all-permissions"></a>1264 Der Datenbankadministrator-Zugriff ist auf definierte Rollen beschränkt, und es werden keine Konten mit Standardverwaltungsberechtigungen oder allen Berechtigungen verwendet

- Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden

## <a name="guidelines-for-using-cryptography---cryptographic-fundamentals"></a>Richtlinien für die Verwendung von Kryptografie: Grundlagen der Kryptografie

### <a name="0459-encryption-software-used-for-data-at-rest-implements-full-disk-encryption-or-partial-encryption-where-access-controls-will-only-allow-writing-to-the-encrypted-partition"></a>0459 Für Verschlüsselungssoftware, die für ruhende Daten genutzt wird, wird die vollständige Datenträgerverschlüsselung implementiert, oder die Teilverschlüsselung, wobei Zugriffssteuerungen nur das Schreiben auf die verschlüsselte Partition ermöglichen

- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.

## <a name="guidelines-for-using-cryptography---transport-layer-security"></a>Richtlinien für die Verwendung von Kryptografie: Transport Layer Security

### <a name="1139-only-the-latest-version-of-tls-is-used"></a>1139 Es wird nur die neueste Version von TLS verwendet

- In Ihrer API-App sollte die neueste TLS-Version verwendet werden.
- In Ihrer Web-App sollte die neueste TLS-Version verwendet werden.
- In Ihrer Funktions-App sollte die neueste TLS-Version verwendet werden.
- Erforderliche Komponenten zum Überwachen von Windows-Webservern bereitstellen, die keine sicheren Kommunikationsprotokolle verwenden
- Überwachungsergebnisse von Windows-Webservern anzeigen, die keine sicheren Kommunikationsprotokolle verwenden

## <a name="guidelines-for-data-transfers-and-content-filtering---content-filtering"></a>Richtlinien für Datenübertragung und Inhaltsfilterung: Inhaltsfilterung

### <a name="1288-antivirus-scanning-using-multiple-different-scanning-engines-is-performed-on-all-content"></a>1288 Antivirenscans werden mit mehreren unterschiedlichen Überprüfungsengines für den gesamten Inhalt durchgeführt

- Die Microsoft IaaSAntimalware-Erweiterung muss auf Windows-Servern bereitgestellt werden
- Die Endpoint Protection-Lösung sollte für VM-Skalierungsgruppen installiert sein.
- Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen

## <a name="guidelines-for-data-transfers-and-content-filtering---web-application-development"></a>Richtlinien für Datenübertragung und Inhaltsfilterung: Entwicklung von Webanwendungen

### <a name="1552-all-web-application-content-is-offered-exclusively-using-https"></a>1552 Der gesamte Inhalt von Webanwendungen wird ausschließlich per HTTPS bereitgestellt

- Zugriff auf Funktions-App nur über HTTPS gestatten
- Auf API-Apps sollte nur über HTTPS zugegriffen werden können
- Zugriff auf Webanwendung nur über HTTPS gestatten
- Nur sichere Verbindungen mit Ihrer Redis Cache-Instanz sollten aktiviert werden

### <a name="1424-web-browser-based-security-controls-are-implemented-for-web-applications-in-order-to-help-protect-both-web-applications-and-their-users"></a>1424 Webbrowserbasierte Sicherheitskontrollen werden für Webanwendungen implementiert, um sowohl Webanwendungen als auch die zugehörigen Benutzer zu schützen

- CORS sollte nicht jeder Ressource Zugriff auf Ihre Webanwendungen erteilen.

## <a name="guidelines-for-network-management---network-design-and-configuration"></a>Richtlinien für die Netzwerkverwaltung: Netzwerkentwurf und -konfiguration

### <a name="0520-network-access-controls-are-implemented-on-networks-to-prevent-the-connection-of-unauthorised-network-devices"></a>0520 Netzwerkzugriffssteuerungen werden für Netzwerke implementiert, um Verbindungen mit nicht autorisierten Netzwerkgeräten zu verhindern

- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen

### <a name="1182-network-access-controls-are-implemented-to-limit-traffic-within-and-between-network-segments-to-only-those-that-are-required-for-business-purposes"></a>1182 Netzwerkzugriffssteuerungen werden implementiert, um den Datenverkehr in und zwischen Netzwerksegmenten nur auf die Daten zu beschränken, die für geschäftliche Zwecke benötigt werden

- Virtuelle Computer mit Internetzugang sollten über Netzwerksicherheitsgruppen geschützt werden.
- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen
- Auf virtuelle Computer mit Internetzugang müssen Empfehlungen zur adaptiven Netzwerkhärtung angewendet werden.

## <a name="guidelines-for-network-management---service-continuity-for-online-services"></a>Richtlinien für die Netzwerkverwaltung: Dienstkontinuität für Onlinedienste

### <a name="1431-denial-of-service-attack-prevention-and-mitigation-strategies-are-discussed-with-service-providers-specifically-their-capacity-to-withstand-denial-of-service-attacks-any-costs-likely-to-be-incurred-by-customers-resulting-from-denial-of-service-attacks-thresholds-for-notifying-customers-or-turning-off-their-online-services-during-denial-of-service-attacks-pre-approved-actions-that-can-be-undertaken-during-denial-of-service-attacks-denial-of-service-attack-prevention-arrangements-with-upstream-providers-to-block-malicious-traffic-as-far-upstream-as-possible"></a>1431 Strategien zur Verhinderung und Entschärfung von Denial-of-Service-Angriffen werden mit Dienstanbietern in Bezug auf die folgenden Aspekte vereinbart: Fähigkeit, Denial-of-Service-Angriffe zu überstehen, Kosten, die für Kunden aufgrund von Denial-of-Service-Angriffen ggf. entstehen können, Schwellenwerte für die Benachrichtigung von Kunden oder Deaktivierung der Onlinedienste bei Denial-of-Service-Angriffen, vorab genehmigte Aktionen, die bei Denial-of-Service-Angriffen durchgeführt werden können, Vereinbarungen zur Verhinderung von Denial-of-Service-Angriffen mit Upstream-Anbietern, um schädlichen Datenverkehr so früh wie möglich zu blockieren

- DDoS Protection Standard sollte aktiviert sein.

> [!NOTE]
> Die Verfügbarkeit spezifischer Azure Policy-Definitionen kann in Azure Government und anderen nationalen Clouds variieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zu Blaupausen und ihrer Nutzung:

> [!div class="nextstepaction"]
> [Blaupause „ISM PROTECTED“ – Übersicht](./index.md)
> [Blaupause „ISM PROTECTED“ – Bereitstellungsschritte](./deploy.md)

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
