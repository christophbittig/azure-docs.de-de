---
title: Steuerungen des Medien-Blaupausenbeispiels
description: Steuerungszuordnung der Medien-Blaupausenbeispiele Jede Steuerung wird mindestens einer Azure Policy-Definition zugeordnet, die Sie bei der Bewertung unterstützt.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 05349865fc5927c52b669e55157d97dec09f51a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674887"
---
# <a name="control-mapping-of-the-media-blueprint-sample"></a>Steuerungszuordnung des Medien-Blaupausenbeispiels

In diesem Artikel wird erläutert, wie das Medien-Blaupausenbeispiel von Azure Blueprints den Mediensteuerungen zugeordnet wird. Weitere Informationen zu den Steuerungen finden Sie unter [Medien](https://www.motionpictures.org/best-practices).

Die folgenden Zuordnungen gelten für die **Mediensteuerungen**. Über den rechten Navigationsbereich können Sie direkt zu einer bestimmten Steuerungszuordnung springen. Viele der zugeordneten Steuerungen werden mit einer [Azure Policy](../../../policy/overview.md)-Initiative implementiert. Zum Anzeigen der vollständigen Initiative öffnen Sie **Richtlinie** im Azure-Portal und wählen dann die Seite **Definitionen** aus. Suchen Sie anschließend die integrierte Richtlinieninitiative **\[Vorschauversion\]: Audit Media controls** (Mediensteuerungen überprüfen).

> [!IMPORTANT]
> Jede Steuerung unten ist einer oder mehreren [Azure Policy](../../../policy/overview.md)-Definitionen zugeordnet. Diese Richtlinien können Ihnen bei der [Konformitätsbewertung](../../../policy/how-to/get-compliance-data.md) mit der Steuerung helfen. Es gibt jedoch oft keine 1:1- oder vollständige Übereinstimmung zwischen einer Steuerung und einer bzw. mehreren Richtlinien. Daher bezieht sich **Konform** in Azure Policy nur auf die Richtlinien selbst und gewährleistet nicht die vollständige Konformität mit allen Anforderungen einer Steuerung. Außerdem enthält der Kompatibilitätsstandard Steuerungen, die derzeit von keiner Azure Policy-Definition abgedeckt werden. Daher ist die Konformität in Azure Policy nur eine partielle Ansicht Ihres gesamten Konformitätsstatus. Die Zuordnungen zwischen Steuerungen und Azure Policy-Definitionen für dieses Konformitätsblaupausenbeispiel können sich im Laufe der Zeit ändern. Den Änderungsverlaufs finden Sie im [GitHub-Commit-Verlauf](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/media/control-mapping.md).

## <a name="access-control"></a>Zugriffssteuerung

### <a name="ac-11--ensure-no-root-access-key-exists"></a>AC-1.1: Sicherstellen, dass es keinen Stammzugriffsschlüssel gibt

- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, die nicht die angegebenen Zertifikate im vertrauenswürdigen Stamm enthalten

### <a name="ac-12---passwords-pins-and-tokens-must-be-protected"></a>AC-1.2: Kennwörter, PINs und Tokens müssen geschützt sein.

- \[Vorschau\]: Erforderliche Komponenten zum Überwachen von Windows-VMs bereitstellen, für die keine Mindestkennwortlänge von 14 Zeichen gilt

### <a name="ac-18---shared-account-access-is-prohibited"></a>AC-1.8: Gemeinsamer Zugriff auf Konten ist unzulässig.

- Alle Autorisierungsregeln außer RootManageSharedAccessKey sollten aus dem Service Bus-Namespace entfernt werden.

### <a name="ac-19--system-must-restrict-access-to-authorized-users"></a>AC-1.9: Das System muss den Zugriff auf autorisierte Benutzer beschränken.

- Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen

### <a name="ac--114--system-must-enforce-access-rights"></a>AC-1.14: Das System muss Zugriffsrechte erzwingen.

- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VM-Konfigurationen in „Zuweisen von Benutzerrechten“ bereitstellen

### <a name="ac--115--prevent-unauthorized-access-to-security-relevant-information-or-functions"></a>AC-1.15: Nicht autorisierten Zugriff auf sicherheitsrelevante Informationen oder Funktionen verhindern

- \[Vorschau\]: Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Sicherheitsoptionen – Systemeinstellungen“ anzeigen

### <a name="ac-1-21---separation-of-duties-must-be-enforced-through-appropriate-assignment-of-role"></a>AC-1.21: Aufgabentrennung muss durch Zuweisen entsprechender Rollen durchgesetzt werden.

- [Vorschau\]: Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) sollte für Kubernetes Service verwendet werden.

### <a name="ac-140--ensure-that-systems-are-not-connecting-trusted-network-and-untrusted-networks-at-the-same-time"></a>AC-1.40: Sicherstellen, dass Systeme nicht gleichzeitig mit vertrauenswürdigen und nicht vertrauenswürdigen Netzwerken verbunden sind

- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VM-Konfigurationen in „Sicherheitsoptionen – Netzwerkzugriff“ bereitstellen

### <a name="ac-142--ac--143---remote-access-for-non-employees-must-be-restricted-to-allow-access-only-to-specifically-approved-information-systems"></a>AC-1.42 & AC-1.43: Remotezugriff für Personen, die keine Mitarbeiter sind, muss auf Zugriff über explizit genehmigte Informationssysteme beschränkt sein

- \[Vorschau\]: Überwachungsergebnisse von Linux-VMs anzeigen, die Remoteverbindungen über Konten ohne Kennwörter zulassen

### <a name="ac-150--log-security-related-events-for-all-information-system-components"></a>AC-1.50: Protokollieren sicherheitsbezogener Ereignisse für alle Informationssystemkomponenten

- In Logic Apps müssen Diagnoseprotokolle aktiviert sein

### <a name="ac-154--ensure-multi-factor-authentication-mfa-is-enabled-for-all-cloud-console-users"></a>AC-1.54: Sicherstellen, dass Multi-Factor Authentication (MFA) für alle Cloudkonsolenbenutzer aktiviert ist

- MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden
- MFA (Multi-Factor Authentication) muss für alle Abonnementkonten mit Schreibrechten aktiviert sein, um eine Sicherheitsverletzung von Konten oder Ressourcen zu verhindern.

## <a name="auditing--logging"></a>Überwachung und Protokollierung

### <a name="al-21--successful-and-unsuccessful-events-must-be-logged"></a>AL-2.1: Erfolgreiche und fehlgeschlagene Ereignisse müssen protokolliert werden.

- In den Suchdiensten müssen Diagnoseprotokolle aktiviert sein

### <a name="al--216---network-devicesinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-network-deviceinstance-elbs-web-application-firewalls-etc"></a>AL-2.16: Netzwerkgeräte/-instanzen müssen alle Ereignisse, die von diesen Netzwerkgeräten/-instanzen als kritische sicherheitsrelevante Ereignisse eingestuft werden, (ELBs, Web Application Firewalls etc.) protokollieren.

- \[Vorschau\]: Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Sicherheitsoptionen – Konten“ anzeigen

### <a name="al-217--serversinstances-must-log-any-event-classified-as-a-critical-security-event-by-that-serverinstance"></a>AL-2.17: Server/Instanzen müssen alle Ereignisse, die von diesen Servern/Instanzen als kritische sicherheitsrelevante Ereignisse eingestuft werden, protokollieren.

- \[Vorschau\]: Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Sicherheitsoptionen – Konten“ anzeigen

### <a name="al-219---domain-events-must-log-any-event-classified-as-a-critical-or-high-security-event-by-the-domain-management-software"></a>AL-2.19: Domänenereignisse müssen alle Ereignisse, die von der Domänenverwaltungssoftware als kritische sicherheitsrelevante Ereignisse oder höchst sicherheitsrelevante Ereignisse eingestuft werden, protokollieren.

- \[Vorschau\]: Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Sicherheitsoptionen – Konten“ anzeigen
- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VM-Konfigurationen in „Sicherheitsoptionen – Microsoft-Netzwerk (Client)“ bereitstellen

### <a name="al-220--domain-events-must-log-any-event-classified-as-a-critical-security-event-by-domain-security-controls"></a>AL-2.20: Domänenereignisse müssen alle Ereignisse, die von Domänensicherheitssteuerungen als kritische sicherheitsrelevante Ereignisse eingestuft werden, protokollieren.

- \[Vorschau\]: Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Sicherheitsoptionen – Konten“ anzeigen

### <a name="al-221--domain-events-must-log-any-access-or-changes-to-the-domain-log"></a>AL-2.21: Domänenereignisse müssen Zugriffe auf das und Änderungen am Domänenprotokoll protokollieren.

- \[Vorschau\]: Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Sicherheitsoptionen – Wiederherstellungskonsole“ anzeigen

## <a name="cryptographic-controls"></a>Kryptografische Steuerungen

### <a name="cc-42--applications-and-systems-must-use-current-cryptographic-solutions-for-protecting-data"></a>CC-4.2: Anwendungen und Systeme müssen aktuelle kryptografische Lösungen zum Schutz von Daten verwenden.

- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.
- TDE (Transparent Data Encryption) sollte aktiviert werden, um ruhende Daten zu schützen und Konformitätsanforderungen zu erfüllen.

### <a name="cc-45--digital-certificates-must-be-signed-by-an-approved-certificate-authority"></a>CC-4.5: Digitale Zertifikate müssen von einer genehmigten Zertifizierungsstelle signiert sein.

- \[Vorschau\]: Überwachungsergebnisse von Windows-VMs anzeigen, auf denen Zertifikate innerhalb der angegebenen Anzahl von Tagen ablaufen

### <a name="cc-46--digital-certificates-must-be-uniquely-assigned-to-a-user-or-device"></a>CC-4.6: Digitale Zertifikate müssen eindeutig einem Benutzer oder Gerät zugewiesen sein.

- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VMs bereitstellen, auf denen Zertifikate innerhalb der angegebenen Anzahl von Tagen ablaufen

### <a name="cc-47--cryptographic-material-must-be-stored-to-enable-decryption-of-the-records-for-the-length-of-time-the-records-are-retained"></a>CC-4.7: Kryptografisches Material muss so gespeichert werden, dass während des Aufbewahrungszeitraums eine Entschlüsselung der Datensätze möglich ist.

- Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
- Hiermit werden virtuelle Computer ohne aktivierte Datenträgerverschlüsselung über Azure Security Center in Form von Empfehlungen überwacht.

### <a name="cc-48--secret-and-private-keys-must-be-stored-securely"></a>CC-4.8: Geheime und private Schlüssel müssen sicher gespeichert werden.

- Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.
- TDE (Transparent Data Encryption) sollte aktiviert werden, um ruhende Daten zu schützen und Konformitätsanforderungen zu erfüllen.

## <a name="change--config-management"></a>Änderungs- und Konfigurationsverwaltung

### <a name="cm-52--only-authorized-users-may-implement-approved-changes-on-the-system"></a>CM-5.2: Nur autorisierte Benutzer können genehmigte Änderungen am System implementieren.

- Systemupdates sollten auf Ihren Computern installiert sein.
- Hiermit werden fehlende Sicherheitssystemupdates auf Ihren Servern über Azure Security Center in Form von Empfehlungen überwacht.

### <a name="cm-512--maintain-an-up-to-date-complete-accurate-and-readily-available-baseline-configuration-of-the-information-system"></a>CM-5.12: Aktuelle, vollständige, korrekte und unmittelbar verfügbare Baselinekonfiguration des Informationssystems bereitstellen

- Systemupdates sollten auf Ihren Computern installiert sein.
- Hiermit werden fehlende Sicherheitssystemupdates auf Ihren Servern über Azure Security Center in Form von Empfehlungen überwacht.

### <a name="cm-513--employ-automated-tools-to-maintain-a-baseline-configuration-of-the-information-system"></a>CM-5.13: Verwendung automatisierter Tools zum Bereitstellen einer Baselinekonfiguration des Informationssystems

- Systemupdates sollten auf Ihren Computern installiert sein.
- Hiermit werden fehlende Sicherheitssystemupdates auf Ihren Servern über Azure Security Center in Form von Empfehlungen überwacht.

### <a name="cm-514--identify-and-disable-unnecessary-andor-non-secure-functions-ports-protocols-and-services"></a>CM-5.14: Identifizieren und Deaktivieren nicht erforderlicher und/oder nicht sicherer Funktionen, Ports, Protokolle und Dienste

- Netzwerkschnittstellen müssen die IP-Weiterleitung deaktivieren
- \[Vorschau\]: IP-Weiterleitung für Ihre VM muss deaktiviert sein

### <a name="cm-519--monitor-changes-to-the-security-configuration-settings"></a>CM-5.19: Überwachung von Änderungen an den Sicherheitskonfigurationseinstellungen

- Bereitstellen von Diagnoseeinstellungen für Netzwerksicherheitsgruppen

### <a name="cm-522--ensure-that-only-authorized-software-and-updates-are-installed-on-company-systems"></a>CM-5.22: Sicherstellen, dass auf den Unternehmenssystemen nur autorisierte Software und Updates installiert sind

- Systemupdates sollten auf Ihren Computern installiert sein.
- Hiermit werden fehlende Sicherheitssystemupdates auf Ihren Servern über Azure Security Center in Form von Empfehlungen überwacht.

## <a name="identity--authentication"></a>Identität und Authentifizierung

### <a name="ia-71--user-accounts-must-be-uniquely-assigned-to-individuals-for-access-to-information-that-is-not-classified-as-public-account-ids-must-be-constructed-using-a-standardized-logical-format"></a>IA-7.1: Für den Zugriff auf nicht als öffentlich eingestufte Informationen müssen Benutzerkonten eindeutig bestimmten Personen zugewiesen sein. Konto-IDs müssen in einem standardisierten logischen Format erstellt werden.

- Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.
- Externe Konten mit Besitzerberechtigungen müssen aus Ihrem Abonnement entfernt werden, um einen nicht überwachten Zugriff zu verhindern.

## <a name="network-security"></a>Netzwerksicherheit

### <a name="ns-92--access-to-network-device-management-functionality-is-restricted-to-authorized-users"></a>NS-9.2: Zugriff auf Funktionen zur Netzwerkgeräteverwaltung ist auf autorisierte Benutzer beschränkt.

- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VM-Konfigurationen in „Sicherheitsoptionen – Netzwerkzugriff“ bereitstellen

### <a name="ns-93--all-network-devices-must-be-configured-using-their-most-secure-configurations"></a>NS-9.3: Bei allen Netzwerkgeräten müssen die sichersten Konfigurationen verwendet werden.

- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VM-Konfigurationen in „Sicherheitsoptionen – Netzwerkzugriff“ bereitstellen

### <a name="ns-95--all-network-connections-to-a-system-through-a-firewall-must-be-approved-and-audited-on-a-regular-basis"></a>NS-9.5: Alle Netzwerkverbindungen zu Systemen durch eine Firewall müssen genehmigt und regelmäßig überprüft werden.

- \[Vorschau\]: Überwachungsergebnisse aus Windows-VM-Konfigurationen in „Windows-Firewalleigenschaften“ anzeigen

### <a name="ns-97--appropriate-controls-must-be-present-at-any-boundary-between-a-trusted-network-and-any-untrusted-or-public-network"></a>NS-9.7: Für Grenzen zwischen vertrauenswürdigen und nicht vertrauenswürdigen oder öffentlichen Netzwerken müssen angemessene Steuerungen vorhanden sein.

- \[Vorschau\]: Voraussetzungen für die Überwachung von Windows-VM-Konfigurationen in „Windows-Firewalleigenschaften“ bereitstellen

## <a name="security-planning"></a>Sicherheitsplanung

### <a name="sp-113--threats-must-be-identified-that-could-negatively-impact-the-confidentiality-integrity-or-availability-of-company-information-and-content-along-with-the-likelihood-of-their-occurrence"></a>SP-11.3: Bedrohungen, die negative Auswirkungen auf die Wahrung der Vertraulichkeit, die Integrität oder die Verfügbarkeit von Unternehmensinformationen und -inhalten haben können, müssen zusammen mit der entsprechenden Eintrittswahrscheinlichkeit identifiziert werden.

- Advanced Threat Protection-Typen sollten in den Advanced Data Security-Einstellungen für verwaltete SQL-Instanzen auf „Alle“ festgelegt werden.

### <a name="security-continuity"></a>Sicherheitskontinuität

## <a name="sc-125--data-in-long-term-storage-must-be-accessible-throughout-the-retention-period-and-protected-against-media-degradation-and-technology-changes"></a>SC-12.5: Daten, die langfristig gespeichert werden, müssen für die Dauer des Aufbewahrungszeitraums zugänglich sein und vor Medienalterung und technologischen Änderungen geschützt werden.

- SQL Server-Instanzen sollten mit einer Überwachungsaufbewahrungsdauer von über 90 Tagen konfiguriert werden.
- Hiermit werden SQL Server-Instanzen überwacht, die mit einer Überwachungsaufbewahrungsdauer von weniger als 90 Tagen konfiguriert sind.

## <a name="system-integrity"></a>Systemintegrität

### <a name="si-143--only-authorized-personnel-may-monitor-network-and-user-activities"></a>SI-14.3: Nur autorisiertes Personal kann Netzwerk- und Benutzeraktivitäten überwachen.

- Sicherheitsrisiken in SQL-Datenbanken sollten beseitigt werden.
- Überprüfungsergebnisse der Sicherheitsrisikobewertung und Empfehlungen zum Beheben von Sicherheitsrisiken in der Datenbank überwachen.

### <a name="si-144--internet-facing-systems-must-have-intrusion-detection"></a>SI-14.4: Systeme mit Internetzugriff müssen über Angriffserkennung verfügen.

- Bereitstellen von Bedrohungserkennung auf SQL-Servern

### <a name="si-1413--standardized-centrally-managed-anti-malware-software-should-be-implemented-across-the-company"></a>SI-14.13: Standardisierte, zentral verwaltete Antischadsoftware muss im gesamten Unternehmen implementiert sein.

- Bereitstellung der standardmäßigen Microsoft IaaSAntimalware-Erweiterung für Windows Server

### <a name="si-1414--anti-malware-software-must-scan-computers-and-media-weekly-at-a-minimum"></a>SI-14.14: Antischadsoftware muss Computer und Medien mindestens einmal pro Woche überprüfen.

- Bereitstellung der standardmäßigen Microsoft IaaSAntimalware-Erweiterung für Windows Server

## <a name="vulnerability-management"></a>Verwaltung von Sicherheitsrisiken

### <a name="vm-154--ensure-that-applications-are-scanned-for-vulnerabilities-on-a-monthly-basis"></a>VM-15.4: Sicherstellen, dass Anwendungen monatlich auf Sicherheitsrisiken überprüft werden

- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Hiermit überwachen Sie die Betriebssystem-Sicherheitsrisiken für Ihre VM-Skalierungsgruppen, um sie vor Angriffen zu schützen.

### <a name="vm-155--ensure-that-vulnerabilities-are-identified-paired-to-threats-and-evaluated-for-risk"></a>VM-15.5: Sicherstellen, dass Sicherheitsrisiken identifiziert und Bedrohungen zugeordnet werden und dass eine entsprechende Risikobewertung vorgenommen wird

- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Hiermit überwachen Sie die Betriebssystem-Sicherheitsrisiken für Ihre VM-Skalierungsgruppen, um sie vor Angriffen zu schützen.

### <a name="vm-156--ensure-that-identified-vulnerabilities-have-been-remediated-within-a-mutually-agreed-upon-timeline"></a>VM-15.6: Sicherstellen, dass identifizierte Sicherheitsrisiken innerhalb eines einvernehmlich vereinbarten Zeitraums behoben werden

- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Hiermit überwachen Sie die Betriebssystem-Sicherheitsrisiken für Ihre VM-Skalierungsgruppen, um sie vor Angriffen zu schützen.

### <a name="vm-157--access-to-and-use-of-vulnerability-management-systems-must-be-restricted-to-authorized-personnel"></a>VM-15.7: Der Zugriff auf und die Nutzung von Sicherheitsrisikomanagement-Systemen müssen auf autorisiertes Personal beschränkt sein.

- Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden.
- Hiermit überwachen Sie die Betriebssystem-Sicherheitsrisiken für Ihre VM-Skalierungsgruppen, um sie vor Angriffen zu schützen.

> [!NOTE]
> Die Verfügbarkeit spezifischer Azure Policy-Definitionen kann in Azure Government und anderen nationalen Clouds variieren.

## <a name="next-steps"></a>Nächste Schritte

Sie haben sich die Informationen zur Steuerungszuordnung anhand des Medien-Blaupausenbeispiels durchgelesen. Lesen Sie als Nächstes die folgenden Artikel, um sich mit der Übersicht und der Bereitstellung dieses Beispiels vertraut zu machen:

> [!div class="nextstepaction"]
> [Medien-Blaupause – Steuerungszuordnung](./control-mapping.md)
> [Medien-Blaupause – Bereitstellungsschritte](./deploy.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
