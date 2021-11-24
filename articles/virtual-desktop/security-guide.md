---
title: Bewährte Sicherheitsmethoden für Azure Virtual Desktop – Azure
titleSuffix: Azure
description: Bewährte Methoden zum Schützen Ihrer Azure Virtual Desktop Umgebung.
author: heidilohr
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: helohr
ms.service: virtual-desktop
manager: femila
ms.openlocfilehash: e453f6f104a79ee4d364b7f52ab7c805741e3a22
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326833"
---
# <a name="security-best-practices"></a>Bewährte Sicherheitsmethoden

Azure Virtual Desktop ist ein verwalteter virtueller Desktopdienst, der zahlreiche Sicherheitsfunktionen umfasst, mit denen Sie Ihre Organisation schützen können. Bei einer Azure Virtual Desktop-Bereitstellung verwaltet Microsoft Teile der Dienste im Auftrag des Kunden. Der Dienst verfügt über viele integrierte fortschrittliche Sicherheitsfeatures, z. B. Reverse Connect, die das Risiko verringern, dass Remotedesktops von überall her zugänglich sind.

In diesem Artikel werden zusätzliche Schritte beschrieben, die Sie als Administrator unternehmen können, um die Azure Virtual Desktop-Bereitstellung Ihrer Kunden sicher zu gestalten.

## <a name="security-responsibilities"></a>Sicherheitsaufgaben

Was Clouddienste von herkömmlichen lokalen virtuellen Desktopinfrastrukturen (VDIs) unterscheidet, ist die Art und Weise, wie sie mit den Verantwortlichkeiten für die Sicherheit umgehen. Bei einer herkömmlichen lokalen VDI wäre der Kunde z. B. für alle Sicherheitsaspekte verantwortlich. Bei den meisten Clouddiensten werden diese Verantwortlichkeiten jedoch zwischen dem Kunden und dem Unternehmen geteilt.

Wenn Sie Azure Virtual Desktop verwenden, ist es wichtig zu verstehen, dass zwar einige Komponenten bereits für Ihre Umgebung gesichert sind, Sie aber andere Bereiche selbst konfigurieren müssen, um den Sicherheitsanforderungen Ihrer Organisation gerecht zu werden.

Hier folgen die Sicherheitsanforderungen, für die Sie bei der Bereitstellung von Azure Virtual Desktop zuständig sind:

| Sicherheitsanforderung | Ist der Kunde dafür verantwortlich? |
|---------------|:-------------------------:|
|Identity|Ja|
|Benutzergeräte (mobile Geräte und PCs)|Ja|
|App-Sicherheit|Ja|
|Sitzungshostbetriebssystem|Ja|
|Bereitstellungskonfiguration|Ja|
|Netzwerksteuerungen|Ja|
|Virtualisierungssteuerungsebene|Nein|
|Physische Hosts|Nein|
|Physisches Netzwerk|Nein|
|Physisches Rechenzentrum|Nein|

Um die Sicherheitsanforderungen, für die der Kunde nicht zuständig ist, kümmert sich Microsoft.

## <a name="azure-security-best-practices"></a>Azure-Sicherheit – bewährte Methoden

Azure Virtual Desktop ist ein Dienst unter Azure. Um die Sicherheit Ihrer Azure Virtual Desktop-Bereitstellung zu maximieren, sollten Sie auch die umgebende Azure-Infrastruktur und die Verwaltungsebene sichern. Berücksichtigen Sie zur Sicherung Ihrer Infrastruktur, wie sich Azure Virtual Desktop in Ihr umfassenderes Azure-Ökosystem einfügt. Weitere Informationen zum Azure-Ökosystem finden Sie unter [Sicherheit in Azure – bewährte Methoden und Muster](../security/fundamentals/best-practices-and-patterns.md).

In diesem Abschnitt werden bewährte Methoden zum Sichern Ihres Azure-Ökosystems beschrieben.

### <a name="enable-microsoft-defender-for-cloud"></a>Aktivieren von Microsoft Defender für Cloud

Wir empfehlen die Aktivierung der erweiterten Sicherheitsfunktionen von Microsoft Defender für Cloud für folgende Aktionen:

- Verwalten von Sicherheitsrisiken
- Bewerten der Konformität mit gängigen Frameworks wie PCI
- Erhöhen Sie die allgemeine Sicherheit Ihrer Umgebung.

Weitere Informationen finden Sie unter [Aktivieren erweiterter Sicherheitsfunktionen](../security-center/enable-enhanced-security.md).

### <a name="improve-your-secure-score"></a>Verbessern Ihrer Sicherheitsbewertung

Die Sicherheitsbewertung bietet Empfehlungen und Ratschläge zu bewährten Methoden zur Verbesserung Ihrer allgemeinen Sicherheit. Diese Empfehlungen sind nach Prioritäten geordnet, um Ihnen bei der Auswahl der wichtigsten Empfehlungen zu helfen, und die Optionen für die schnelle Problembehebung helfen Ihnen, potenzielle Sicherheitsrisiken schnell zu beheben. Zudem werden diese Empfehlungen im Laufe der Zeit aktualisiert, sodass Sie immer auf dem Laufenden sind, wie Sie die Sicherheit Ihrer Umgebung am besten sicherstellen können. Weitere Informationen finden Sie unter [Verbessern Ihres Secure Scores (Indikator für Sicherheitsbewertung) in Microsoft Defender für Cloud](../security-center/secure-score-security-controls.md).

## <a name="azure-virtual-desktop-security-best-practices"></a>Bewährte Sicherheitsmethoden für Azure Virtual Desktop

Azure Virtual Desktop verfügt über viele integrierte Sicherheitskontrollen. In diesem Abschnitt erfahren Sie mehr über Sicherheitskontrollen, mit denen Sie Ihre Benutzer und Daten schützen können.

### <a name="require-multi-factor-authentication"></a>Mehrstufige Authentifizierung erforderlich

Die Anforderung einer mehrstufigen Authentifizierung für alle Benutzer und Administratoren in Azure Virtual Desktop verbessert die Sicherheit Ihrer gesamten Bereitstellung. Weitere Informationen finden Sie unter [Aktivieren von Azure AD Multi-Factor Authentication für Azure Virtual Desktop](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Aktivieren des bedingten Zugriffs

Durch die Aktivierung des [bedingten Zugriffs](../active-directory/conditional-access/overview.md) können Sie Risiken verwalten, bevor Sie Benutzern Zugriff auf Ihre Azure Virtual Desktop-Umgebung gewähren. Bei der Entscheidung, welchen Benutzern der Zugriff gewährt werden soll, sollten Sie auch berücksichtigen, wer der Benutzer ist, wie er sich anmeldet und welches Gerät er verwendet.

### <a name="collect-audit-logs"></a>Sammeln von Überwachungsprotokollen

Wenn Sie das Sammeln von Überwachungsprotokollen aktivieren, können Sie Benutzer- und Verwaltungsaktivitäten im Zusammenhang mit Azure Virtual Desktop anzeigen. Einige Beispiele für wichtige Überwachungsprotokolle sind:

-   [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md)
-   [Azure Active Directory-Aktivitätsprotokoll](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Sitzungshosts](../azure-monitor/agents/agent-windows.md)
-   [Azure Virtual Desktop-Diagnoseprotokoll](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault-Protokolle](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Verwenden von RemoteApps

Wenn Sie sich für ein Bereitstellungsmodell entscheiden, können Sie Remotebenutzern entweder Zugriff auf ganze virtuelle Desktops oder nur auf ausgewählte Anwendungen gewähren. Remoteanwendungen oder RemoteApps bieten eine problemlose Erfahrung, da der Benutzer mit Apps auf seinem virtuellen Desktop arbeitet. RemoteApps verringern das Risiko, da der Benutzer nur mit einer Teilmenge des Remotecomputers arbeiten kann, die von der Anwendung zugänglich gemacht wird.

### <a name="monitor-usage-with-azure-monitor"></a>Überwachen der Nutzung mit Azure Monitor

Überwachen Sie die Nutzung und Verfügbarkeit Ihres Azure Virtual Desktop-Diensts mit [Azure Monitor](https://azure.microsoft.com/services/monitor/). Ziehen Sie in Erwägung, [Warnungen zur Dienstintegrität](../service-health/alerts-activity-log-service-notifications-portal.md) für den Azure Virtual Desktop-Dienst zu erstellen, um Benachrichtigungen zu erhalten, wann immer ein Ereignis auftritt, das sich auf den Dienst auswirkt.

## <a name="session-host-security-best-practices"></a>Bewährte Sicherheitsmethoden für den Sitzungshost

Sitzungshosts sind virtuelle Computer, die in einem Azure-Abonnement und einem virtuellen Netzwerk ausgeführt werden. Die Gesamtsicherheit der Azure Virtual Desktop-Bereitstellung hängt von den Sicherheitskontrollen ab, die Sie auf Ihren Sitzungshosts einrichten. In diesem Abschnitt werden bewährte Methoden für die Sicherheit der Sitzungshosts beschrieben.

### <a name="enable-endpoint-protection"></a>Aktivieren von Endpoint Protection

Zum Schutz Ihrer Bereitstellung vor bekannter bösartiger Software empfehlen wir, Endpoint Protection auf allen Sitzungshosts zu aktivieren. Sie können entweder Windows Defender Antivirus oder ein Programm eines Drittanbieters verwenden. Weitere Informationen finden Sie unter [Leitfaden zur Bereitstellung für Windows Defender Antivirus in einer VDI-Umgebung](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

Für Profillösungen wie FSLogix oder andere Lösungen, die VHD-Dateien einbinden, empfehlen wir, VHD-Dateierweiterungen auszuschließen.

### <a name="install-an-endpoint-detection-and-response-product"></a>Installieren eines Produkts zur Endpunkterkennung und -antwort

Es wird empfohlen, ein EDR-Produkt (Endpunkterkennung und -antwort) zu installieren, um erweiterte Erkennungs- und Antwortfunktionen bereitzustellen. Bei Serverbetriebssystemen mit aktiviertem [Microsoft Defender für Cloud](../security-center/security-center-services.md) wird bei der Installation eines EDR-Produkts Defender ATP bereitgestellt. Für Clientbetriebssysteme können Sie [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) oder ein Produkt eines Drittanbieters an diesen Endpunkten bereitstellen.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Aktivieren von Bewertungen des Bedrohungs- und Sicherheitsrisikomanagements

Die Identifizierung von Softwaresicherheitsrisiken, die in Betriebssystemen und Anwendungen vorhanden sind, ist entscheidend für die Sicherheit Ihrer Umgebung. Microsoft Defender für Cloud kann Ihnen bei der Identifizierung von Problemzonen durch Bewertungen von Sicherheitsrisiken für Serverbetriebssysteme helfen. Sie können auch Defender ATP verwenden, das Bedrohungs- und Sicherheitsrisikomanagement für Desktopbetriebssysteme bietet. Sie können auch Produkte von Drittanbietern verwenden, wenn Sie dazu geneigt sind, obwohl wir die Verwendung von Microsoft Defender für Cloud und Defender ATP empfehlen.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Patchen von Softwaresicherheitsrisiken in Ihrer Umgebung

Nachdem Sie ein Sicherheitsrisiko identifiziert haben, müssen Sie es Patchen. Dies gilt auch für virtuelle Umgebungen, d. h. für die aktiven Betriebssysteme, die darin bereitgestellten Anwendungen und die Images, mit denen Sie neue Computer erstellen. Befolgen Sie die Mitteilungen Ihrer Anbieter zur Patchbenachrichtigung, und wenden Sie Patches zeitnah an. Es wird empfohlen, Ihre Basisimages monatlich zu patchen, um sicherzustellen, dass neu bereitgestellte Computer so sicher wie möglich sind.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Einrichten von Richtlinien für die maximale inaktive Zeit und die Trennung von Verbindungen

Das Abmelden von Benutzern, wenn diese inaktiv sind, schont die Ressourcen und verhindert den Zugriff durch nicht autorisierte Benutzer. Es wird empfohlen, dass Zeitlimits die Benutzerproduktivität und die Ressourcennutzung ausgleichen. Für Benutzer, die mit zustandslosen Anwendungen interagieren, sollten offensivere Richtlinien in Betracht gezogen werden, die Computer abschalten und Ressourcen bewahren. Das Trennen der Verbindung zeitintensiver Anwendungen, die weiterhin ausgeführt werden, wenn ein Benutzer im Leerlauf ist, z. B. eine Simulation oder ein CAD-Rendering, kann die Arbeit des Benutzers unterbrechen und möglicherweise sogar einen Neustart des Computers erfordern.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Einrichten von Bildschirmsperren für Leerlaufsitzungen

Sie können unerwünschten Systemzugriff verhindern, indem Sie Azure Virtual Desktop so konfigurieren, dass der Bildschirm eines Computers während des Leerlaufs gesperrt wird und eine Authentifizierung zum Entsperren erforderlich ist.

### <a name="establish-tiered-admin-access"></a>Einrichten eines mehrstufigen Administratorzugriffs

Es wird empfohlen, Ihren Benutzern keinen Administratorzugriff auf virtuelle Desktops zu gewähren. Wenn Sie Softwarepakete benötigen, empfehlen wir Ihnen, diese über Hilfsprogramme für die Konfigurationsverwaltung wie Microsoft Endpoint Manager zur Verfügung zu stellen. In einer Umgebung mit mehreren Sitzungen wird empfohlen, dass Benutzer die Software nicht direkt installieren dürfen.

### <a name="consider-which-users-should-access-which-resources"></a>Berücksichtigen, welche Benutzer auf welche Ressourcen zugreifen sollten

Betrachten Sie Sitzungshosts als eine Erweiterung Ihrer bestehenden Desktopbereitstellung. Es wird empfohlen, den Zugriff auf Netzwerkressourcen auf dieselbe Weise zu steuern, wie für andere Desktops in Ihrer Umgebung, z. B. durch Netzwerksegmentierung und Filterung. Standardmäßig können Sitzungshosts eine Verbindung zu jeder beliebigen Ressource im Internet herstellen. Es gibt verschiedene Möglichkeiten, den Datenverkehr einzuschränken, einschließlich der Verwendung von Azure Firewall, virtueller Netzwerkgeräte oder Proxys. Wenn Sie den Datenverkehr einschränken müssen, stellen Sie sicher, dass Sie die richtigen Regeln hinzufügen, damit Azure Virtual Desktop ordnungsgemäß funktionieren kann.

### <a name="manage-office-pro-plus-security"></a>Verwalten der Office Pro Plus-Sicherheit

Zusätzlich zum Schutz der Sitzungshosts ist es wichtig, die darin ausgeführten Anwendungen ebenfalls zu sichern. Office Pro Plus ist eine der am häufigsten in Sitzungshosts eingesetzten Anwendungen. Um die Sicherheit der Office-Bereitstellung zu verbessern, empfehlen wir Ihnen die Verwendung des [Sicherheitsrichtlinienratgebers](/DeployOffice/overview-of-security-policy-advisor) für Microsoft 365-Apps for Enterprise. Dieses Tool identifiziert Richtlinien, die Sie für mehr Sicherheit auf Ihre Bereitstellung anwenden können. Der Sicherheitsrichtlinienberater empfiehlt Richtlinien auch auf der Grundlage ihrer Auswirkungen auf Ihre Sicherheit und Produktivität.

### <a name="other-security-tips-for-session-hosts"></a>Weitere Sicherheitstipps für Sitzungshosts

Indem Sie die Funktionen des Betriebssystems einschränken, können Sie die Sicherheit Ihrer Sitzungshosts erhöhen. Hier folgen einige durchführbare Schritte:

- Steuern Sie die Geräteumleitung, indem Sie Laufwerke, Drucker und USB-Geräte in einer Remotedesktopsitzung auf das lokale Gerät eines Benutzers umleiten. Es wird empfohlen, dass Sie Ihre Sicherheitsanforderungen auswerten und prüfen, ob diese Features deaktiviert werden sollten oder nicht.

- Schränken Sie den Zugriff auf Windows Explorer ein, indem Sie lokale und Remotelaufwerkszuordnungen ausblenden. Dadurch wird verhindert, dass Benutzer unerwünschte Informationen über Systemkonfigurationen und Benutzer entdecken.

- Vermeiden Sie den direkten RDP-Zugriff auf Sitzungshosts in Ihrer Umgebung. Wenn Sie direkten RDP-Zugriff für die Verwaltung oder Problembehandlung benötigen, aktivieren Sie [Just-In-Time](../security-center/security-center-just-in-time.md)-Zugriff, um die potenzielle Angriffsfläche auf einem Sitzungshost einzuschränken.

- Gewähren Sie Benutzern eingeschränkte Berechtigungen, wenn sie auf lokale und Remotedateisysteme zugreifen. Sie können Berechtigungen einschränken, indem Sie sicherstellen, dass Ihre lokalen und Remotedateisysteme Zugriffssteuerungslisten mit geringsten Rechten verwenden. Auf diese Weise können Benutzer nur auf erforderliche Daten zugreifen und können wichtige Ressourcen nicht ändern oder löschen.

- Verhindern Sie die Ausführung unerwünschter Software auf Sitzungshosts. Sie können AppLocker für zusätzliche Sicherheit auf Sitzungshosts aktivieren, um sicherzustellen, dass nur die von Ihnen zugelassenen Apps auf dem Host ausgeführt werden können.

## <a name="azure-virtual-desktop-support-for-trusted-launch"></a>Azure Virtual Desktop-Unterstützung für vertrauenswürdigen Start

Der vertrauenswürdige Start bezieht sich auf Azure-Gen2-VMs mit erweiterten Sicherheitsfeatures zum Schutz vor Bedrohungen in der unteren Ebene des Stapels durch Angriffsvektoren wie z. B. Rootkits, Bootkits und Schadsoftware auf Kernelebene. Im Folgenden werden alle erweiterten Sicherheitsfeatures des vertrauenswürdigen Starts beschrieben, die in Azure Virtual Desktop unterstützt werden. Weitere Informationen zum vertrauenswürdigen Start finden Sie unter [Vertrauenswürdiger Start für Azure-VMs (Vorschau)](../virtual-machines/trusted-launch.md).

### <a name="secure-boot"></a>Sicherer Start

Der sichere Start ist ein in der Plattformfirmware unterstützter Modus, der die Firmware vor schadsoftwarebasierten Rootkits und Bootkits schützt. In diesem Modus kann der Computer ausschließlich über signierte Betriebssysteme und Treiber gestartet werden. 

### <a name="monitor-boot-integrity-using-remote-attestation"></a>Überwachen der Startintegrität über Remotenachweis

Der Remotenachweis eignet sich ideal zum Überprüfen der Integrität Ihrer VMs. Mit dem Remotenachweis wird geprüft, ob Datensätze des kontrollierten Starts vorhanden und echt sind und aus dem virtuellen Trusted Platform Module (vTPM) stammen. Als Integritätsprüfung bietet er kryptografische Sicherheit, dass eine Plattform ordnungsgemäß gestartet wurde. 

### <a name="vtpm"></a>vTPM

Ein vTPM ist eine virtualisierte Version eines Trusted Platform Module (TPM) mit einer virtuellen Instanz eines TPM pro VM. vTPM ermöglicht den Remotenachweis über eine Integritätsmessung der gesamten Startkette der VM (UEFI, Betriebssystem, System und Treiber). 

Es wird empfohlen, das vTPM für die Verwendung des Remotenachweises auf Ihren VMs zu aktivieren. Bei aktiviertem vTPM können Sie außerdem die BitLocker-Funktionalität aktivieren, mit der vollständige Volumeverschlüsselung zum Schutz von ruhenden Daten gewährleistet wird. Alle Funktionen, bei denen das vTPM verwendet wird, haben zur Folge, dass Geheimnisse an die spezifische VM gebunden sind. Wenn Benutzer in einem Poolszenario eine Verbindung mit dem Azure Virtual Desktop-Dienst herstellen, können sie zu jeder VM im Hostpool umgeleitet werden. Je nachdem, wie die Funktion gestaltet ist, wirkt sich dies möglicherweise negativ aus.

>[!NOTE]
>BitLocker sollte nicht dazu verwendet werden, den Datenträger zu verschlüsseln, auf dem Ihre FSLogix-Profildaten gespeichert sind.

### <a name="virtualization-based-security"></a>Virtualisierungsbasierte Sicherheit

Bei der virtualisierungsbasierten Sicherheit (VBS) wird über Hypervisor ein sicherer Speicherbereich erstellt und isoliert, der für das Betriebssystem nicht zugänglich ist. Bei HVCI (Hypervisor-Protected Code Integrity) und Windows Defender Credential Guard sorgt VBS für einen verstärkten Schutz vor Sicherheitsrisiken. 

#### <a name="hypervisor-protected-code-integrity"></a>Hypervisor-Protected Code Integrity

HVCI bietet eine leistungsstarke Risikominderung für das System unter Verwendung von VBS und schützt Windows-Kernelmodusprozesse vor der Einschleusung und Ausführung von schädlichem oder nicht überprüftem Code.

#### <a name="windows-defender-credential-guard"></a>Windows Defender Credential Guard

Mit Windows Defender Credential Guard werden Geheimnisse mithilfe von VBS isoliert und geschützt, sodass der Zugriff nur über privilegierte Systemsoftware erfolgen kann. Dies verhindert einen nicht autorisierten Zugriff auf diese Geheimnisse und Angriffe zum Diebstahl von Anmeldeinformationen, z. B. Pass-the-Hash-Angriffe.

### <a name="deploy-trusted-launch-in-your-azure-virtual-desktop-environment"></a>Bereitstellen des vertrauenswürdigen Starts in Ihrer Azure Virtual Desktop-Umgebung

In Azure Virtual Desktop ist es derzeit nicht möglich, den vertrauenswürdigen Start beim Setupprozess des Hostpools automatisch zu konfigurieren. Zur Verwendung des vertrauenswürdigen Starts in Ihrer Azure Virtual Desktop-Umgebung müssen Sie den vertrauenswürdigen Start normal bereitstellen und dann die VM manuell in den gewünschten Hostpool einfügen.

## <a name="nested-virtualization"></a>Geschachtelte Virtualisierung

Die Ausführung der geschachtelten Virtualisierung in Azure Virtual Desktop wird unter den folgenden Betriebssystemen unterstützt:

- Windows Server 2016
- Windows Server 2019
- Windows Server 2022
- Windows 10 Enterprise
- Windows 10 Enterprise (mehrere Sitzungen)

## <a name="windows-defender-application-control"></a>Windows Defender Application Control

Die folgenden Betriebssysteme unterstützen die Verwendung der Windows Defender-Anwendungssteuerung mit Azure Virtual Desktop:

- Windows Server 2016
- Windows Server 2019
- Windows Server 2022
- Windows 10 Enterprise
- Windows 10 Enterprise (mehrere Sitzungen)

>[!NOTE]
>Wenn Sie die Windows Defender-Anwendungssteuerung verwenden, wird empfohlen, nur Richtlinien auf Geräteebene als Ziel zu verwenden. Obwohl es möglich ist, Richtlinien auf einzelne Benutzer auszurichten, wirkt sich die Richtlinie, sobald sie angewendet wird, auf alle Benutzer des Geräts gleichermaßen aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Aktivieren der mehrstufigen Authentifizierung finden Sie unter [Einrichten der mehrstufigen Authentifizierung](set-up-mfa.md).
