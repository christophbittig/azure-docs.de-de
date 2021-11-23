---
title: Neues in Azure Virtual Desktop - Azure
description: Neue Features und Produktupdates für Azure Virtual Desktop.
author: Heidilohr
ms.topic: overview
ms.date: 09/27/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 1781a566f84825971ac3728a360b52fcd1270e0a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490229"
---
# <a name="whats-new-in-azure-virtual-desktop"></a>Neues in Azure Virtual Desktop

Azure Virtual Desktop wird regelmäßig aktualisiert. In diesem Artikel werden Sie über Folgendes informiert:

- Die neuesten Updates
- Neue Funktionen
- Verbesserungen an vorhandenen Features
- Behebung von Programmfehlern

Dieser Artikel wird monatlich aktualisiert. Kehren Sie hier oft zurück, um mit neuen Updates auf dem Laufenden zu bleiben.

## <a name="client-updates"></a>Clientupdates

Lesen Sie die folgenden Artikel, um sich über die Updates für unsere Clients für Azure Virtual Desktop und Remotedesktopdienste zu informieren:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="azure-virtual-desktop-agent-updates"></a>Updates für Azure Virtual Desktop-Agent

Der Azure Virtual Desktop-Agent wird mindestens einmal pro Monat aktualisiert.

Hier sind die Änderungen für Azure Virtual Desktop-Agent angegeben:

- Version 1.0.3719.1700: Dieses Update wurde im November 2021 veröffentlicht und umfasst die folgenden Änderungen:
    - Agent-Fehlermeldungen aktualisiert
    - Behebt ein Problem, aufgrund dessen der Agent bei jeder Aktualisierung des parallelen Stapels neu gestartet wurde.
    - Allgemeine Agent-Verbesserungen.
- Version 1.0.3583.2600: Dieses Update wurde im Oktober 2021 veröffentlicht und behebt ein Problem, aufgrund dessen der parallele Stapel bei einem Upgrade von Windows 10 auf Windows 11 deaktiviert wurde.
- Version 1.0.3373.2605: Dieses Update wurde im September 2021 veröffentlicht und behebt ein Problem, aufgrund dessen die Aufhebung der Paketregistrierung bei Verwendung des MSIX-Features zum Anfügen von Apps hängen geblieben ist.
- Version 1.0.3373.2600: Dieses Update wurde im September 2021 veröffentlicht und umfasst die folgenden Änderungen:
    - Allgemeine Agent-Verbesserungen.
    - Behebung von Problemen beim Neustart des Agents auf VMs mit Windows 7.
    - Behebung eines Problems, bei dem Felder in der Tabelle „WVDAgentHealthStatus“ nicht richtig angezeigt wurden.
- Version 1.0.3130.2900: Dieses Update wurde im Juli 2021 veröffentlicht und umfasst die folgenden Änderungen:
    - allgemeine Verbesserungen und Fehlerbehebungen
    - Problembehebung beim Abrufen des Hostpool-Pfads für die Intune-Registrierung.
    - Hinzugefügte Protokollierung zur besseren Diagnose von Agent-Problemen.
    - Behebt ein Problem mit Orchestrierungstimeouts.
- Version 1.0.3050.2500: Dieses Update wurde im Juli 2021 veröffentlicht und umfasst die folgenden Änderungen:
    - Aktualisierte Interne Monitore für die Agent-Integrität.
    - Aktualisierte Wiederholungslogik für die Stapelüberwachung.
- Version 1.0.2990.1500: Dieses Update wurde im April 2021 veröffentlicht und umfasst die folgenden Änderungen:
    - Agent-Fehlermeldungen aktualisiert
    - Hinzugefügte Ausnahme, die verhindert, dass Sie Nicht-Windows 7-Agenten auf virtuellen Windows 7-Computern installieren.
    - Heartbeatdienstlogik aktualisiert
- Version 1.0.2944.1400: Dieses Update wurde im April 2021 veröffentlicht und umfasst die folgenden Änderungen:
    - Links zum Leitfaden zur Problembehandlung für Azure Virtual Desktop-Agent in den Protokollen der Ereignisanzeige für Agent-Fehler platziert
    - Zusätzliche Ausnahme hinzugefügt, um eine bessere Fehlerbehandlung zu ermöglichen
    - WVDAgentUrlTool.exe hinzugefügt. Damit können Kunden überprüfen, auf welche erforderlichen URLs sie zugreifen können.
-   Version 1.0.2866.1500: Dieses Update wurde im März 2021 veröffentlicht. Damit wird ein Problem mit der Integritätsprüfung des Stapels behoben.
-   Version 1.0.2800.2802: Dieses Update wurde im März 2021 veröffentlicht und enthält allgemeine Verbesserungen und Fehlerbehebungen.
-   Version 1.0.2800.2800: Dieses Update wurde im März 2021 veröffentlicht. Damit wird ein Problem mit der umgekehrten Verbindung behoben.
-   Version 1.0.2800.2700: Dieses Update wurde im Februar 2021 veröffentlicht. Damit wird ein Orchestrierungsproblem im Zusammenhang mit verweigertem Zugriff behoben.

## <a name="fslogix-updates"></a>FSLogix-Updates

Sie sind neugierig auf die neuesten Updates für FSLogix? Informieren Sie sich über die [Neuerungen für FSLogix](/fslogix/whats-new).

## <a name="october-2021"></a>Oktober 2021

Das hat sich im Oktober 2021 geändert:

### <a name="azure-virtual-desktop-support-for-windows-11"></a>Unterstützung von Azure Virtual Desktop für Windows 11

Die Unterstützung von Azure Virtual Desktop für Windows 11 ist jetzt allgemein für Einzel- und Multisession-Bereitstellungen verfügbar. Sie können jetzt Windows 11-Images verwenden, wenn Sie Host-Pools im Azure-Portal erstellen. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/windows-11-is-now-generally-available-on-azure-virtual-desktop/ba-p/2810545).

### <a name="rdp-shortpath-now-generally-available"></a>RDP Shortpath jetzt allgemein verfügbar

Remote Desktop Protocol (RDP) Shortpath für verwaltete Netzwerke ist jetzt allgemein verfügbar. RDP Shortpath stellt eine direkte Verbindung zwischen dem Remotedesktop-Client und dem Sitzungshost her. Diese direkte Verbindung verringert die Abhängigkeit von Gateways, verbessert die Zuverlässigkeit der Verbindung und erhöht die für jede Benutzersitzung verfügbare Bandbreite. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/rdp-shortpath-for-managed-networks-is-generally-available/m-p/2861468).

### <a name="screen-capture-protection-updates"></a>Updates für den Schutz von Bildschirmaufnahmen

Der Schutz von Bildschirmaufnahmen wird jetzt auf dem macOS-Client und in den Azure Government und Azure China Clouds unterstützt. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/screen-capture-protection-for-macos-client-and-support-for/m-p/2840089#M7940).

### <a name="azure-active-directory-domain-join"></a>Azure Active Directory-Domänenbeitritt 

Azure Active Directory Domain Join für Azure Virtual Desktop VMs ist jetzt in den Azure Government und Azure China Clouds verfügbar. Microsoft Endpoint Manager (Intune) wird derzeit nur in der Azure Public Cloud unterstützt. Weitere Informationen finden Sie unter [Bereitstellung von Azure AD-gekoppelten virtuellen Maschinen in Azure Virtual Desktop](deploy-azure-ad-joined-vm.md).

### <a name="breaking-change-in-azure-virtual-desktop-azure-resource-manager-template"></a>Änderung in der Azure Virtual Desktop Azure Resource Manager-Vorlage

In der Azure Resource Manager-Vorlage für Azure Virtual Desktop wurde eine wichtige Änderung eingeführt. Wenn Sie einen Code verwenden, der von der Änderung abhängt, müssen Sie die Anweisungen in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/azure-virtual-desktop-arm-template-change-removal-of-script/m-p/2851538#M7971) befolgen, um das Problem zu beheben.

### <a name="autoscale-preview-public-preview"></a>Autoscale (Vorschau) öffentliche Vorschau

Autoscale für Azure Virtual Desktop ist jetzt in der öffentlichen Vorschau. Diese Funktion schaltet Ihre virtuellen Maschinen (VMs) in gepoolten Host-Pools je nach Verfügbarkeitsbedarf automatisch ein oder aus. Durch die Planung des Ein- und Ausschaltens Ihrer VMs werden die Bereitstellungskosten optimiert, und diese Funktion bietet außerdem flexible, auf Ihre Bedürfnisse abgestimmte Planungsoptionen. Sobald Sie die erforderliche benutzerdefinierte RBAC-Rolle (Role-Based Access Control) konfiguriert haben, können Sie mit der Konfiguration Ihres Skalierungsplans beginnen. Weitere Informationen finden Sie unter [Autoscale (Vorschau) für Azure Virtual Desktop-Hostpools](autoscale-scaling-plan.md).

## <a name="september-2021"></a>September 2021

Änderungen im September 2021:

### <a name="azure-portal-updates"></a>Azure-Portalupdates

Sie können jetzt Azure Resource Manager-Vorlagen für alle Updates verwenden, die Sie nach der Bereitstellung auf Ihre Sitzungshosts anwenden möchten. Sie können auf dieses Feature zugreifen, indem Sie beim Erstellen eines Hostpools die Registerkarte **Virtuelle Computer** auswählen.

Darüber hinaus können Sie jetzt auch während der Erstellung von Hostpools Diagnoseeinstellungen für Hostpools, App-Gruppen und Arbeitsbereiche festlegen und müssen dies nicht mehr hinterher erledigen. Beim Konfigurieren dieser Einstellungen während der Hostpoolerstellung werden zudem automatisch Berichtsdaten für Erkenntnisse zu Azure Virtual Desktop eingerichtet.

### <a name="azure-active-directory-domain-join"></a>Azure Active Directory-Domänenbeitritt

Der Azure Active Directory-Domänenbeitritt ist jetzt allgemein verfügbar. Mit diesem Dienst können Sie Ihre Sitzungshosts in Azure Active Directory einbinden. Über den Domänenbeitritt können Sie auch unter Microsoft Endpoint Manager die automatische Registrierung bei Intune durchführen. Sie können auf dieses Feature in der öffentlichen Azure-Cloud zugreifen, aber nicht in der Government- oder Azure China-Cloud. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-azure-ad-joined-vms-support/ba-p/2751083).

### <a name="azure-china"></a>Azure China

Azure Virtual Desktop ist in der Azure China-Cloud jetzt allgemein verfügbar. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://azure.microsoft.com/updates/general-availability-azure-virtual-desktop-is-now-available-in-the-azure-china-cloud/).

### <a name="automatic-migration-module-tool"></a>Tool für das Modul „Automatische Migration“

Mit dem Tool für die automatische Migration können Sie mit wenigen PowerShell-Befehlen Ihre Organisation aus Azure Virtual Desktop (klassisch) in Azure Virtual Desktop verschieben. Dieses Feature befindet sich derzeit in der öffentlichen Vorschauphase. Weitere Informationen finden Sie unter [Automatische Migration](automatic-migration.md).

## <a name="august-2021"></a>August 2021

Änderungen im August 2021:

### <a name="windows-11-preview-for-azure-virtual-desktop"></a>Windows 11 (Vorschau) für Azure Virtual Desktop

Windows 11 -Images (Vorschauversion) sind jetzt im Azure Marketplace verfügbar, damit Kunden sie mit Azure Virtual Desktop testen und überprüfen können. Weitere Informationen finden Sie in [unserer Ankündigung](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/windows-11-preview-is-now-available-on-azure-virtual-desktop/ba-p/2666468).

### <a name="multimedia-redirection-is-now-in-public-preview"></a>Beginn der öffentlichen Vorschauphase für Multimediaumleitung

Die Multimediaumleitung ermöglicht eine reibungslose Wiedergabe von Videos in Ihrem Azure Virtual Desktop-Webbrowser und funktioniert mit Microsoft Edge und Google Chrome. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/public-preview-announcing-public-preview-of-multimedia/m-p/2663244#M7692).

### <a name="windows-defender-application-control-and-azure-disk-encryption-support"></a>Unterstützung für Windows Defender Application Control und Azure Disk Encryption

Azure Virtual Desktop unterstützt jetzt Windows Defender Application Control, damit gesteuert werden kann, welche Treiber und Anwendungen auf virtuellen Windows-Computern (VMs) ausgeführt werden dürfen. Darüber hinaus wird auch Azure Disk Encryption unterstützt. Dies ist ein Dienst, bei dem Windows BitLocker verwendet wird, um die Volumeverschlüsselung für das Betriebssystem und die Datenträger Ihrer VMs zu ermöglichen. Weitere Informationen finden Sie in [unserer Ankündigung](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/support-for-windows-defender-application-control-and-azure-disk/m-p/2658633#M7685).
 
### <a name="signing-into-azure-ad-using-smart-cards-are-now-supported-in-azure-virtual-desktop"></a>Unterstützung in Azure Virtual Desktop für die Anmeldung bei Azure AD mithilfe von Smartcards

Obwohl dies kein neues Feature für Azure AD ist, wird die Konfiguration der Active Directory-Verbunddienste (AD FS) für die Anmeldung mit Smartcards jetzt in Azure Virtual Desktop unterstützt. Weitere Informationen finden Sie in [unserer Ankündigung](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/signing-in-to-azure-ad-using-smart-cards-now-supported-in-azure/m-p/2654209#M7671).

### <a name="screen-capture-protection-is-now-generally-available"></a>Bildschirmaufnahmeschutz jetzt allgemein verfügbar

Mit dem Bildschirmaufnahmeschutz in Azure Virtual Desktop können Sie verhindern, dass vertrauliche Informationen von Software, die auf den Clientendpunkten ausgeführt wird, erfasst werden. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-screen-capture-protection-for/m-p/2699684).

## <a name="july-2021"></a>Juli 2021

Änderungen im Juli 2021:

### <a name="azure-virtual-desktop-images-now-include-optimized-teams"></a>Azure Virtual Desktop-Images enthalten jetzt optimiertes Teams

In allen verfügbaren Images im Azure Virtual Desktop-Imagekatalog, die Microsoft 365 Apps for Enterprise enthalten, ist jetzt die medienoptimierte Version von Teams für Azure Virtual Desktop vorinstalliert. Weitere Informationen finden Sie in [unserer Ankündigung](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/media-optimization-for-microsoft-teams-now-part-of-win10/m-p/2550054#M7442).

### <a name="azure-active-directory-domain-join-for-session-hosts-is-in-public-preview"></a>Azure Active Directory-Domänenbeitritt für Sitzungshosts in der öffentlichen Vorschau

Sie können Ihre Azure Virtual Desktop-VMs jetzt direkt in Azure Active Directory (Azure AD) einbinden. Mit diesem Feature können Sie auf jedem Gerät mithilfe einfacher Anmeldeinformationen eine Verbindung mit Ihren VMs herstellen. Sie können Ihre VMs auch automatisch bei Microsoft Endpoint Manager registrieren. In bestimmten Szenarien wird dies dazu beitragen, die Notwendigkeit eines Domänencontrollers zu beseitigen, die Kosten zu senken und Ihre Bereitstellung zu optimieren. Weitere Informationen finden Sie unter [Bereitstellen von in Azure AD eingebundenen VMs in Azure Virtual Desktop](deploy-azure-ad-joined-vm.md).

### <a name="fslogix-version-2105-is-now-available"></a>FSLogix-Version 2105 jetzt verfügbar

Die FSLogix-Version 2105 ist jetzt allgemein verfügbar. Diese Version enthält verbesserte Anmeldezeiten und Fehlerbehebungen, die in der öffentlichen Vorschau (Version 2105) nicht verfügbar waren. Ausführlichere Informationen finden Sie in den [FSLogix-Versionshinweisen](/fslogix/whats-new) und in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-general-availability-of-fslogix-2105-2-9-7838-44263/m-p/2539491#M7412).

### <a name="azure-virtual-desktop-in-china-has-entered-public-preview"></a>Azure Virtual Desktop in China jetzt in der öffentlichen Vorschau

Durch die Verfügbarkeit von Azure Virtual Desktop in China bieten wir jetzt eine erweiterte globale Abdeckung, die Organisationen dabei unterstützt, Kunden in dieser Region mit verbesserten Leistungs- und Latenzwerten zu unterstützen. Weitere Informationen finden Sie auf [unserer Ankündigungsseite](https://azure.microsoft.com/updates/azure-virtual-desktop-is-now-available-in-the-azure-china-cloud-in-preview/).
 
### <a name="the-getting-started-feature-for-azure-virtual-desktop"></a>Das Feature „Erste Schritte“ für Azure Virtual Desktop

Dieses Feature bietet eine optimierte Onboardingumgebung im Azure-Portal zum Einrichten Ihrer Azure Virtual Desktop-Umgebung. Sie können dieses Feature verwenden, um Bereitstellungen zu erstellen, die die Systemanforderungen für automatisierte Azure Active Directory Domain Services auf einfache Weise erfüllen. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385).

### <a name="start-vm-on-connect-is-now-generally-available"></a>„VM bei Verbindung starten“ jetzt allgemein verfügbar

Das Feature „VM bei Verbindung starten“ ist jetzt allgemein verfügbar. Dieses Feature hilft Ihnen bei der Kostenoptimierung, indem Sie VMs deaktivieren können, deren Zuordnung aufgehoben wurde oder die beendet wurden, sodass Ihre Bereitstellung flexibel auf Benutzeranforderungen reagieren kann. Weitere Informationen finden Sie unter [VM bei Verbindung starten](start-virtual-machine-connect.md).

### <a name="remote-app-streaming-documentation"></a>Dokumentation zum Remote-App-Streaming

Wir haben vor Kurzem eine neue Preisoption für das Remote-App-Streaming angekündigt, wenn Azure Virtual Desktop zum Bereitstellen von Apps als Dienst für Kunden und Geschäftspartner verwendet wird. Beispielsweise können Softwarehersteller das Remote-App-Streaming verwenden, um Apps als SaaS-Lösung (Software-as-a-Service) bereitzustellen, auf die ihre Kunden zugreifen können. Weitere Informationen zum Remote-App-Streaming finden Sie in [unserer Dokumentation](./remote-app-streaming/overview.md).

Vom 14. Juli 2021 bis zum 31. Dezember 2021 halten wir Kunden, die Remote-App-Streaming verwenden, ein Werbeangebot bereit, mit dem ihre Geschäftspartner und Kunden kostenlos auf Azure Virtual Desktop zugreifen können. Dieses Angebot gilt nur für externe Benutzerzugriffsrechte. Die reguläre Abrechnung wird am 1. Januar 2022 fortgesetzt. In der Zwischenzeit können Sie weiterhin Ihre vorhandenen Windows-Lizenzberechtigungen verwenden, wie sie in Lizenzen wie Microsoft 365 E3 oder Windows E3 zu finden sind. Weitere Informationen zu diesem Angebot finden Sie auf der [Preisseite für Azure Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

### <a name="new-azure-virtual-desktop-handbooks"></a>Neue Handbücher für Azure Virtual Desktop

Wir haben vor Kurzem vier neue Handbücher veröffentlicht, die Sie beim Entwerfen und Bereitstellen von Azure Virtual Desktop in verschiedenen Szenarien unterstützen: 

- [Anwendungsverwaltung](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-application-management/) zeigt Ihnen, wie Sie die Anwendungsbereitstellung modernisieren und die IT-Verwaltung vereinfachen können.  
- In [Notfallwiederherstellung](https://azure.microsoft.com/resources/azure-virtual-desktop-handbook-disaster-recovery/) erfahren Sie, wie Sie die geschäftliche Resilienz durch Entwicklung einer Strategie für die Notfallwiederherstellung stärken.  
- Nutzen Sie Ihre Citrix-Investitionen besser mit dem Migrationsleitfaden für [Citrix Cloud mit Azure Virtual Desktop](https://azure.microsoft.com/resources/migration-guide-citrix-cloud-with-azure-virtual-desktop/).
- Nutzen Sie Ihre vorhandenen VMware-Investitionen besser mit dem Migrationsleitfaden für [VMware Horizon mit Azure Virtual Desktop](https://azure.microsoft.com/resources/migration-guide-vmware-horizon-cloud-and-azure-virtual-desktop/).

## <a name="june-2021"></a>Juni 2021

Hier sind die Informationen zu den Änderungen angegeben, die im Juni 2021 vorgenommen wurden:

### <a name="windows-virtual-desktop-is-now-azure-virtual-desktop"></a>Der Windows Virtual Desktop heißt jetzt Azure Virtual Desktop

Um unserer Vision einer flexiblen Cloud-Desktop- und Remote-Anwendungsplattform besser gerecht zu werden, haben wir Windows Virtual Desktop in Azure Virtual Desktop umbenannt. [Weitere Informationen finden Sie in der Ankündigung in unserem Blog](https://azure.microsoft.com/blog/azure-virtual-desktop-the-desktop-and-app-virtualization-platform-for-the-hybrid-workplace/).

### <a name="eu-uk-and-canada-geographies-are-now-generally-available"></a>Die Regionen der EU, des Vereinigten Königreiches und Kanadas sind jetzt allgemein verfügbar

Der Metadatendienst für die Europäische Union, das Vereinigte Königreich und Kanada ist jetzt allgemein verfügbar. Diese neuen Standorte sind für die Datenhoheit außerhalb der USA sehr wichtig. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/announcing-public-preview-of-azure-virtual-desktop-service/m-p/2478401#M7314).

### <a name="the-getting-started-tool-is-now-in-public-preview"></a>Das „Erste Schritte-Tool“ befindet sich jetzt in der öffentlichen Vorschau

Wir haben das Tool für die Azure Virtual Desktop-Erste Schritte erstellt, um den Bereitstellungsprozess für die Erstbenutzer zu vereinfachen. Durch die Vereinfachung und Automatisierung des Bereitstellungsprozesses hoffen wir, dass dieses Tool dazu beiträgt, die Einführung von Azure Virtual Desktop für eine größere Vielzahl von Benutzern schneller und zugänglicher zu machen. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/azure-virtual-desktop/getting-started-wizard-in-azure-virtual-desktop/m-p/2451385).

### <a name="azure-virtual-desktop-pricing-calculator-updates"></a>Updates des Azure Virtual Desktop-Preisrechners

Wir haben einige wichtige Updates vorgenommen, um die Azure Virtual Desktop-Preiserfahrung im Azure-Preisrechner zu verbessern, einschließlich der Folgenden:  
  
- Wir haben den Dienstnamen in Azure Virtual Desktop aktualisiert  
- Außerdem haben wir das Layout mit den folgenden neuen Elementen aktualisiert:  
   - Ein Speicherabschnitt mit einer Bandbreite für verwalteten Datenträger und Dateispeicher  
   - Ein benutzerdefinierter Abschnitt, in dem die Kosten pro Benutzer angezeigt werden

Sie können auf [dieser Seite](https://azure.microsoft.com/pricing/calculator/) auf den Preiskalkulator zugreifen.

### <a name="single-sign-on-sso-using-active-directory-federation-services-ad-fs"></a>Einmaliges Anmelden unter Verwendung der Active Directory-Verbunddienste (AD FS)

Die AD FS-Funktion für das einmalige Anmelden ist jetzt allgemein verfügbar. Mit dieser Funktion können Kunden die AD FS verwenden, um Benutzern auf den Windows und Webclients ein einmaliges Anmelden zu ermöglichen. Weitere Informationen finden Sie unter [Konfigurieren des einmaligen Anmeldens für AD FS für Azure Virtual Desktop](configure-adfs-sso.md).

## <a name="may-2021"></a>Mai 2021

Neuerungen im Mai 2021:

### <a name="smart-card-authentication"></a>Authentifizierung mit Smartcards

Die RDP-Eigenschaften (Remotedesktopprotokoll) des Schlüsselverteilungscenter-Proxys (Key Distribution Center, KDC) wurden jetzt offiziell veröffentlicht. Diese Eigenschaften ermöglichen die Kerberos-Authentifizierung für den RDP-Teil einer Azure Virtual Desktop-Sitzung. Dies umfasst auch die Authentifizierung auf Netzwerkebene ohne Kennwort. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/new-feature-smart-card-authentication-for-windows-virtual/m-p/2323226).

### <a name="the-web-client-now-supports-file-transfer"></a>Webclient unterstützt jetzt die Dateiübertragung

Ab der öffentlichen Vorschauversion des Webclients (Version 1.0.24.7) können Benutzer Dateien jetzt zwischen ihrer Remotesitzung und dem lokalen Computer übertragen. Wählen Sie zum Hochladen von Dateien in die Remotesitzung das Uploadsymbol aus, das sich im Menü oben auf der Webclientseite befindet. Suchen Sie zum Herunterladen von Dateien im Startmenü Ihrer Remotesitzung nach dem **Remote Desktop Virtual Drive** (Virtuelles Laufwerk des Remotedesktops). Nachdem Sie Ihr virtuelles Laufwerk geöffnet haben, können Sie Ihre Dateien einfach per Drag & Drop in den Ordner „Downloads“ ziehen. Der Browser beginnt dann mit dem Herunterladen der Dateien auf Ihren lokalen Computer.

### <a name="start-vm-on-connect-support-updates"></a>Starten von virtuellen Computern bei der Verbindungsherstellung: Supportupdates

Für das Starten von virtuellen Computern bei der Verbindungsherstellung (Vorschau) werden jetzt gepoolte Hostpools und die Azure Government Cloud unterstützt. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/leverage-start-vm-on-connect-for-pooled-host-pools-and-azure-gov/m-p/2349866).

### <a name="latency-improvements-for-the-united-arab-emirates-region"></a>Verringerte Latenz für die Region „Vereinigte Arabische Emirate“

Da wir nun auch über eine Azure-Steuerungsebene in den Vereinigten Arabischen Emiraten (VAE) verfügen, können Kunden in dieser Region mit einer Verringerung der Latenz rechnen. Weitere Informationen finden Sie in unserer [Roadmap für Azure Virtual Desktop](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop&searchterms=64545).

### <a name="ending-internet-explorer-11-support"></a>Supportende für Internet Explorer 11

Ab dem 30. September 2021 wird Internet Explorer 11 vom Azure Virtual Desktop-Webclient nicht mehr unterstützt. Wir empfehlen Ihnen daher, schon jetzt mit der Verwendung des [Microsoft Edge](https://www.microsoft.com/edge?form=MY01R2&OCID=MY01R2&r=1)-Browsers für Ihre Webclient- und Remotesitzungen zu beginnen. Weitere Informationen finden Sie unter der Ankündigung in [diesem Blogbeitrag](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-web-client-to-end-support-for-internet/m-p/2369007).

### <a name="microsoft-endpoint-manager-public-preview"></a>Öffentliche Vorschauversion von Microsoft Endpoint Manager

Wir haben die öffentliche Vorschauversion für die Unterstützung von Microsoft Endpoint Manager unter Windows 10 Enterprise (mehrere Sitzungen) gestartet. Mit diesem neuen Feature können Sie Ihre Windows 10-VMs mit denselben Tools verwalten, die Sie auch für Ihre lokalen Geräte nutzen. Weitere Informationen finden Sie in unserer [Microsoft Endpoint Manager-Dokumentation](/mem/intune/fundamentals/windows-virtual-desktop-multi-session).

### <a name="fslogix-agent-public-preview"></a>Öffentliche Vorschauversion des FSLogix-Agents

Wir haben eine öffentliche Vorschauversion der neuesten Version des FSLogix-Agents veröffentlicht. Lesen Sie unseren [Blogbeitrag](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/public-preview-fslogix-release-2105-is-now-available-in-public/m-p/2380996/thread-id/7105), um weitere Informationen zu erhalten, und übermitteln Sie das Formular, das Sie für den Zugriff auf die Vorschauversion benötigen.

### <a name="may-2021-updates-for-teams-for-azure-virtual-desktop"></a>Updates vom Mai 2021 für Teams für Azure Virtual Desktop

Für diesen Updatevorgang haben wir ein Problem behoben, bei dem der Bildschirm während der Videofreigabe schwarz geblieben ist. Darüber hinaus haben wir einen Konflikt bei der Videoauflösung zwischen dem Sitzungsclient und dem Teams-Server behoben. Für Teams unter Azure Virtual Desktop sollten die Auflösung und die Bitraten nun basierend auf der Eingabe des Teams-Servers geändert werden.

### <a name="azure-portal-deployment-updates"></a>Updates für die Bereitstellung im Azure-Portal

Wir haben am Bereitstellungsprozess im Azure-Portal die folgenden Updates vorgenommen:

- Neue Images (einschließlich GEN2) dem Dropdown-Listenfeld von „Image“ hinzugefügt, das beim Erstellen einer neuen Azure Virtual Desktop-Sitzungshost-VM angezeigt wird.
- Sie können beim Erstellen eines Hostpools jetzt die Startdiagnose für virtuelle Computer konfigurieren.
- Auf der Registerkarte mit den RDP-Eigenschaften des erweiterten Hostpools wurde dem RDP-Proxy eine QuickInfo hinzugefügt.
- Informationssprechblase für den Symbolpfad hinzugefügt, die angezeigt wird, wenn eine Anwendung aus einem MSIX-Paket hinzugefügt wird.
- Es ist nicht mehr möglich, eine verwaltete Startdiagnose mit einem nicht verwalteten Datenträger durchzuführen.
- Vorlage für die Erstellung eines Hostpools in Azure Resource Manager aktualisiert, damit im Azure-Portal jetzt die Erstellung von Hostpools mit Marketplace-Images von Drittanbietern unterstützt werden kann.

### <a name="single-sign-on-using-active-directory-federation-services-public-preview"></a>Einmaliges Anmelden mit Active Directory-Verbunddienste (AD FS): Öffentliche Vorschauversion

Wir haben eine öffentliche Vorschauversion der Unterstützung des einmaligen Anmeldens (Single Sign-On, SSO) pro Hostpool für Active Directory-Verbunddienste (AD FS) gestartet. Weitere Informationen finden Sie unter [Konfigurieren des einmaligen Anmeldens für AD FS für Azure Virtual Desktop](configure-adfs-sso.md). 

### <a name="enterprise-scale-support"></a>Unterstützung auf Unternehmensebene

Wir haben einen aktualisierten Abschnitt des Cloud Adoption Framework veröffentlicht, um die Unterstützung auf Unternehmensebene für Azure Virtual Desktop zu ermöglichen. Weitere Informationen finden Sie unter [Unterstützung auf Unternehmensebene für den Azure Virtual Desktop-Bausatz](/azure/cloud-adoption-framework/scenarios/wvd/enterprise-scale-landing-zone).

### <a name="customer-adoption-kit"></a>Kit für Einführung beim Kunden

Vor Kurzem haben wir ein Kit für die Einführung von Azure Virtual Desktop beim Kunden veröffentlicht, das Kunden und Partnern beim Einrichten von Azure Virtual Desktop für deren Kunden als Hilfe dient. Sie können das Kit [hier](https://www.microsoft.com/azure/partners/resources/customer-adoption-kit-windows-virtual-desktop) herunterladen.

## <a name="april-2021"></a>April 2021

Neuerungen im April:

### <a name="use-the-start-vm-on-connect-feature-preview-in-the-azure-portal"></a>Verwenden des Features „VM bei Verbindung starten“ (Vorschau) im Azure-Portal

Sie können jetzt das Feature „VM bei Verbindung starten“ (Vorschau) im Azure-Portal konfigurieren. Mit diesem Update können Benutzer über die Android- und macOS-Clients auf ihre VMs zugreifen. Weitere Informationen finden Sie im Abschnitt zum Thema [VM bei Verbindung starten](start-virtual-machine-connect.md#use-the-azure-portal).

### <a name="required-url-check-tool"></a>Erforderliches URL-Überprüfungstool 

Version 1.0.2944.400 des Azure Virtual Desktop-Agents enthält ein Tool, mit dem URLs überprüft werden und angezeigt wird, ob der virtuelle Computer auf die benötigten URLs zugreifen kann. Wenn der Zugriff auf erforderliche URLs möglich ist, werden diese vom Tool aufgelistet, damit Sie bei Bedarf dafür die Blockierung aufheben können. Weitere Informationen finden Sie in der [Liste mit den sicheren URLs](safe-url-list.md#required-url-check-tool).

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Updates der Azure-Portal-Benutzeroberfläche für Azure Virtual Desktop

Folgendes hat sich mit dem letzten Update der Azure-Portal-Benutzeroberfläche für Azure Virtual Desktop geändert:

- Es wurde ein Problem behoben, das zu einem Fehler beim Abrufen des Sitzungshosts bei aktiviertem Ausgleichsmodus geführt hat.
- Für das Portal-SDK wurde das Upgrade auf Version 7.161.0 durchgeführt.
- Es wurde ein Problem behoben, bei dem auf der Registerkarte „Benutzersitzungen“ eine Fehlermeldung zu einer fehlenden Ressourcen-ID angezeigt wurde.
- Im Azure-Portal werden jetzt detaillierte Meldungen zum Unterstatus für Sitzungshosts angezeigt.

### <a name="april-2021-updates-for-teams-on-azure-virtual-desktop"></a>Updates für Teams unter Azure Virtual Desktop vom April 2021

Neuerungen für Teams unter Azure Virtual Desktop:

- Hardwarebeschleunigung für die Videoverarbeitung von ausgehenden Videodatenströmen für Windows 10-basierte Clients wurde hinzugefügt.
- Beim Beitritt zu einer Besprechung mit einer nach vorn gerichteten Kamera und einer nach hinten gerichteten oder externen Kamera wird standardmäßig die vordere Kamera ausgewählt.
- Es wurde ein Problem behoben, bei dem Teams auf x86-basierten Computern abgestürzt ist.
- Es wurde ein Problem behoben, das während der Bildschirmfreigabe zu Streifenbildung geführt hat.
- Es wurde ein Problem behoben, bei dem Besprechungsteilnehmer eingehende Videodaten oder freigegebene Bildschirme nicht sehen konnten.

### <a name="msix-app-attach-is-now-generally-available"></a>MSIX-App-Anfügung ist jetzt allgemein verfügbar

Für die MSIX-App-Anfügung für Azure Virtual Desktop wurde die öffentliche Vorschauphase abgeschlossen und die allgemeine Verfügbarkeit für alle Benutzer erreicht. Weitere Informationen zur MSIX-App-Anfügung finden Sie in [unserer Tech Community-Ankündigung](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-is-now-generally-available/m-p/2270468).

### <a name="the-macos-client-now-supports-apple-silicon-and-big-sur"></a>Für den macOS-Client werden jetzt Apple Silicon und Big Sur unterstützt

Für den Azure Virtual Desktop-Client unter macOS werden jetzt Apple Silicon und Big Sur unterstützt. Die Liste mit allen Updates finden Sie unter [Neues beim macOS-Client](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew).

## <a name="march-2021"></a>März 2021

Hier sind die Informationen zu den Änderungen angegeben, die im März 2021 vorgenommen wurden.

### <a name="updates-to-the-azure-portal-ui-for-azure-virtual-desktop"></a>Updates der Azure-Portal-Benutzeroberfläche für Azure Virtual Desktop

Für das Azure-Portal haben wir für Azure Virtual Desktop die folgenden Updates vorgenommen:

- Wir haben neue Verfügbarkeitsoptionen (Verfügbarkeitsgruppe und -zonen) für die Workflows aktiviert, um Hostpools zu erstellen und VMs hinzuzufügen.
- Wir haben ein Problem behoben, bei dem ein Host mit dem Status „Needs assistance“ (Unterstützung erforderlich) als nicht verfügbar angezeigt wurde. Neben dem Host wird nun ein Warnsymbol angezeigt.
- Wir haben die Sortierung für aktive Sitzungen aktiviert.
- Sie können auf der Registerkarte mit den Hostdetails jetzt Nachrichten an bestimmte Benutzer senden oder diese abmelden.
- Wir haben das Feld für den Grenzwert für die maximale Sitzungsanzahl geändert.
- Wir haben dem Workflow einen OE-Überprüfungspfad für die Erstellung eines Hostpools hinzugefügt.
- Sie können beim Erstellen eines persönlichen Hostpools jetzt die aktuelle Version des Windows 10-Images verwenden.

### <a name="generation-2-images-and-trusted-launch"></a>Images der 2. Generation und vertrauenswürdiger Start

Auf dem Azure Marketplace sind jetzt Images der 2. Generation für Windows 10 Enterprise und Windows 10 Enterprise (mehrere Sitzungen) verfügbar. Mit diesen Images können Sie VMs mit dem Feature „Vertrauenswürdiger Start“ verwenden. Weitere Informationen zu VMs der 2. Generation finden Sie auf der [Seite mit der Entscheidungshilfe zur Erstellung einer VM der 1. oder 2. Generation](../virtual-machines/generation-2.md). Informationen zum Bereitstellen von Azure Virtual Desktop-VMs mit „Vertrauenswürdiger Start“ finden Sie in [diesem Tech Community-Beitrag](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170).

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix ist jetzt auf Images für Windows 10 Enterprise (mehrere Sitzungen) vorinstalliert

Basierend auf dem erhaltenen Kundenfeedback haben wir eine neue Version des Images für Windows 10 Enterprise (mehrere Sitzungen) eingerichtet, auf dem bereits eine nicht konfigurierte Version von FSLogix installiert ist. Wir hoffen, dass diese Maßnahme Ihnen die Bereitstellung Ihrer Azure Virtual Desktop-Instanz erleichtert.

### <a name="azure-monitor-for-azure-virtual-desktop-is-now-in-general-availability"></a>Azure Monitor für Azure Virtual Desktop ist jetzt allgemein verfügbar

Azure Monitor für Azure Virtual Desktop ist jetzt für die Öffentlichkeit allgemein verfügbar. Bei diesem Feature handelt es sich um einen automatisierten Dienst, der Ihre Bereitstellungen überwacht und Ihnen das Anzeigen von Ereignissen, Integritätsinformationen und Vorschlägen zur Problembehandlung an einem zentralen Ort ermöglicht. Weitere Informationen finden Sie in [unserer Dokumentation](azure-monitor.md) und [diesem Tech Community-Beitrag](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861).

### <a name="march-2021-updates-for-teams-on-azure-virtual-desktop"></a>Updates für Teams unter Azure Virtual Desktop vom März 2021

Wir haben die folgenden Updates für Teams unter Azure Virtual Desktop vorgenommen:

- Wir haben die Leistung der Videoqualität für Anrufe und den 2x2-Modus verbessert.
- Wir konnten die CPU-Auslastung um 5 bis 10 % (je nach CPU-Generation) reduzieren, indem für die Videoverarbeitung die Hardwareauslagerung (XVP) genutzt wird.
- Für ältere Computer können nun XVP und die Hardwaredecodierung verwendet werden, um weitere eingehende Videostreams reibungslos im 2x2-Modus anzuzeigen.
- Wir haben den WebRTC-Stapel von M74 auf M88 aktualisiert, um die Leistung der Audio-/Videosynchronisierung zu verbessern und die Anzahl von vorübergehenden Problemen zu verringern.
- Wir haben unseren H264-Softwareencoder durch OpenH264 ersetzt (Verwendung von Open-Source-Software in Teams im Web), um die Videoqualität der Kamera für die ausgehenden Daten zu steigern.
- Wir haben den 2x2-Modus für Teams-Server am 30. März für die öffentliche Nutzung aktiviert. Im 2x2-Modus werden bis zu vier eingehende Videostreams gleichzeitig angezeigt.

### <a name="start-vm-on-connect-public-preview"></a>VM bei Verbindung starten: Öffentliche Vorschauphase

Die neue Hostpooleinstellung „VM bei Verbindung starten“ befindet sich jetzt in der öffentlichen Vorschauphase und ist entsprechend verfügbar. Mit dieser Einstellung können Sie Ihre VMs bei Bedarf aktivieren. Falls Sie Kosten sparen möchten, müssen Sie die Zuordnung Ihrer VMs aufheben, indem Sie Ihre Azure Compute-Einstellungen konfigurieren. Weitere Informationen finden Sie in [diesem Blogbeitrag](https://aka.ms/wvdstartvmonconnect) und [unserer Dokumentation](start-virtual-machine-connect.md).

### <a name="azure-virtual-desktop-specialty-certification"></a>Azure Virtual Desktop Specialty-Zertifizierung

Wir haben eine Betaversion der Prüfung AZ-140 veröffentlicht, mit der Sie Ihre Azure Virtual Desktop-Kenntnisse in Azure nachweisen können. Weitere Informationen finden Sie in [unserem Tech Community-Beitrag](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107).

## <a name="february-2021"></a>Februar 2021

Hier sind die Änderungen angegeben, die im Februar 2021 vorgenommen wurden.

### <a name="portal-experience"></a>Portalfunktion

Wir haben die Benutzeroberfläche des Azure-Portals wie folgt verbessert:

- Massenausgleichsmodus auf Hosts auf der Registerkarte mit dem Sitzungshostraster. 
- MSIX-Feature zum Anfügen von Apps ist jetzt für die öffentliche Vorschau verfügbar.
- Fehler in Hostpoolübersicht für dunklen Modus behoben.

### <a name="eu-metadata-storage-now-in-public-preview"></a>EU-Metadatenspeicher befindet sich jetzt in der öffentlichen Vorschauphase

Wir hosten jetzt eine öffentliche Vorschauversion für Europa (EU) als Speicheroption für Dienstmetadaten in Azure Virtual Desktop. Kunden können beim Erstellen ihrer Dienstobjekte zwischen „Europa, Westen“ und „Europa, Norden“ wählen. Die Dienstobjekte und Metadaten für die Hostpools werden jeweils in der Azure-Geografie gespeichert, die den einzelnen Regionen zugeordnet ist. Weitere Informationen finden Sie in [unserem Blogbeitrag zur Ankündigung der öffentlichen Vorschauversion](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939).

### <a name="teams-on-azure-virtual-desktop-plugin-updates"></a>Updates des Plug-Ins für Teams in Azure Virtual Desktop

Wir haben die Qualität von Videoanrufen für das Azure Virtual Desktop-Plug-In verbessert, indem wir die am häufigsten gemeldeten Probleme behoben haben. Beispiele hierfür sind die Fälle, in denen der Bildschirm plötzlich dunkel wurde oder das Video und der Ton nicht mehr synchron waren. Aufgrund dieser Verbesserungen sollte bei der Einzelanzeige von Videos mit Wechsel zum jeweils aktiven Sprecher eine höhere Leistung erzielt werden. Wir haben auch ein Problem behoben, bei dem Hardwaregeräte mit Sonderzeichen in Teams nicht verfügbar waren.

## <a name="january-2021"></a>Januar 2021

Änderungen im Januar 2021:

### <a name="new-azure-virtual-desktop-offer"></a>Neues Azure Virtual Desktop-Angebot

Neue Kunden sparen bis zu 90 Tage lang 30 Prozent bei den Azure Virtual Desktop-Computingkosten für VMs der D- und Bs-Serie, wenn sie die native Microsoft-Lösung nutzen. Sie können dieses Angebot im Azure-Portal bis zum 31. März 2021 einlösen. Weitere Informationen finden Sie auf unserer [Angebotsseite für Azure Virtual Desktop](https://azure.microsoft.com/services/virtual-desktop/offer/).

### <a name="networksecuritygrouprules-value-change"></a>Änderung bei Wert für „networkSecurityGroupRules“ 

In der geschachtelten Azure Resource Manager-Vorlage haben wir den Standardwert für „networkSecurityGroupRules“ von einem Objekt in ein Array geändert. Hierdurch werden die Fehler verhindert, die sonst ggf. auftreten, wenn Sie „managedDisks-customimagevm.json“ ohne Angabe eines Werts für „networkSecurityGroupRules“ verwenden. Dies ist keine grundlegende Änderung (Breaking Change), und es besteht Abwärtskompatibilität.

### <a name="fslogix-hotfix-update"></a>FSLogix-Hotfix-Update

Wir haben FSLogix Version 2009 HF_01 (2.9.7654.46150) veröffentlicht, um Probleme zu beheben, die im vorherigen Release (2.9.7621.30127) aufgetreten sind. Wir empfehlen Ihnen, die vorherige Version nicht mehr zu nutzen und das Update für FSLogix so schnell wie möglich durchzuführen.

Weitere Informationen finden Sie in den Versionshinweisen auf der [Seite mit den Neuerungen für FSLogix](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150).

### <a name="azure-portal-experience-improvements"></a>Verbesserungen bei der Azure-Portal-Benutzeroberfläche

Wir haben die folgenden Verbesserungen am Azure-Portal vorgenommen:

- Sie können jetzt direkt Anmeldeinformationen für Administratoren lokaler VMs hinzufügen, anstatt ein lokales Konto hinzuzufügen, das mit den Kontoanmeldeinformationen für den Active Directory-Domänenbeitritt erstellt wurde.
- Benutzer können jetzt sowohl einzelne als auch Gruppenzuweisungen auf separaten Registerkarten für bestimmte Benutzer und Gruppen auflisten.
- Die Versionsnummer des Azure Virtual Desktop-Agents wird jetzt in der VM-Übersicht für Hostpools angezeigt.
- Die Funktion zum Massenlöschen für Hostpools und Anwendungsgruppen wurde hinzugefügt.
- Sie können jetzt den Ausgleichsmodus für mehrere Sitzungshosts in einem Hostpool aktivieren oder deaktivieren.
- Das Feld für die öffentliche IP-Adresse wurde von der Seite mit den VM-Details entfernt.

### <a name="azure-virtual-desktop-agent-troubleshooting"></a>Problembehandlung für Azure Virtual Desktop-Agent

Wir haben vor Kurzem den [Leitfaden zur Problembehandlung für den Azure Virtual Desktop-Agent](troubleshoot-agent.md) veröffentlicht, der als Hilfe für Kunden dienen soll, bei denen häufige Probleme auftreten.

### <a name="microsoft-defender-for-endpoint-integration"></a>Integration in Microsoft Defender für Endpunkt

Integration in Microsoft Defender für Endpunkt ist jetzt allgemein verfügbar. Mit diesem Feature erhalten Ihre Azure Virtual Desktop-VMs die gleiche Untersuchungsoberfläche wie ein lokaler Windows 10-Computer. Bei Verwendung von Windows 10 Enterprise (mehrere Sitzungen) unterstützt Microsoft Defender für Endpunkt bis zu 50 gleichzeitige Benutzerverbindungen. Sie profitieren von den Kosteneinsparungen, die mit Windows 10 Enterprise (mehrere Sitzungen) möglich sind, und von der Zuverlässigkeit von Microsoft Defender für Endpunkt. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-azure-virtual-desktop"></a>Azure-Sicherheitsbaseline für Azure Virtual Desktop

Wir haben vor Kurzem einen [Artikel zur Azure-Sicherheitsbaseline](security-baseline.md) für Azure Virtual Desktop veröffentlicht, auf den wir Sie aufmerksam machen möchten. Er enthält Informationen dazu, wie Sie Version 2.0 des Vergleichstests für die Azure-Sicherheit auf Azure Virtual Desktop anwenden. Mit dem Vergleichstest für die Azure-Sicherheit werden die Einstellungen und Methoden beschrieben, deren Nutzung wir Ihnen empfehlen, um Ihre Cloudlösungen in Azure zu schützen.

## <a name="december-2020"></a>Dezember 2020

Änderungen im Dezember 2020: 

### <a name="azure-monitor-for-azure-virtual-desktop"></a>Azure Monitor für Azure Virtual Desktop

Die öffentliche Vorschauversion für Azure Monitor für Azure Virtual Desktop ist jetzt verfügbar. Dieses neue Feature umfasst ein stabiles Dashboard, das auf Azure Monitor-Arbeitsmappen basiert, damit IT-Experten ihre Azure Virtual Desktop-Umgebungen besser verstehen. Weitere Informationen finden Sie in der [Ankündigung in unserem Blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587). 

### <a name="azure-resource-manager-template-change"></a>Änderung der Azure Resource Manager-Vorlage 

Beim letzten Update haben wir alle Parameter für öffentliche IP-Adressen aus der Azure Resource Manager-Vorlage zum Erstellen und Bereitstellen von Hostpools entfernt. Wir empfehlen Ihnen dringend, keine öffentlichen IP-Adressen für Azure Virtual Desktop zu verwenden, damit für die Sicherheit Ihrer Bereitstellung gesorgt ist. Wenn Ihre Bereitstellung auf öffentlichen IP-Adressen basiert, müssen Sie die Konfiguration so ändern, dass stattdessen private IP-Adressen verwendet werden. Andernfalls funktioniert Ihre Bereitstellung nicht richtig.

### <a name="msix-app-attach-public-preview"></a>MSIX-Feature zum Anfügen von Apps: Öffentliche Vorschauversion 

Das MSIX-Feature zum Anfügen von Apps ist ein weiterer Dienst, für den in diesem Monat die öffentliche Vorschauphase begonnen hat. Beim MSIX-Feature zum Anfügen von Apps handelt es sich um einen Dienst, mit dem MSIX-Anwendungen für die Host-VMs Ihrer Azure Virtual Desktop-Sitzung dynamisch bereitgestellt werden. Weitere Informationen finden Sie in der [Ankündigung in unserem Blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231). 

### <a name="screen-capture-protection"></a>Schutz von Bildschirmaufnahmen 

In diesem Monat hat auch die öffentliche Vorschauphase für den Schutz von Bildschirmaufnahmen begonnen. Sie können dieses Feature verwenden, um zu verhindern, dass vertrauliche Informationen auf den Clientendpunkten erfasst werden. Informationen zum Schutz von Bildschirmaufnahmen finden Sie auf [dieser Seite](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Integrierte Rollen

Wir haben neue integrierte Rollen für Azure Virtual Desktop in Bezug auf Administratorberechtigungen hinzugefügt. Weitere Informationen finden Sie unter [Integrierte Rollen für Azure Virtual Desktop](rbac.md). 

### <a name="application-group-limit-increase"></a>Erhöhung des Grenzwerts für Anwendungsgruppen

Wir haben den Standardgrenzwert für Anwendungsgruppen pro Azure Active Directory-Mandant auf 200 Gruppen erhöht.

### <a name="client-updates-for-december-2020"></a>Clientupdates für Dezember 2020

Wir haben neue Versionen der folgenden Clients veröffentlicht: 

- Android
- macOS
- Windows

Weitere Informationen zu Clientupdates finden Sie unter [Clientupdates](whats-new.md#client-updates).

## <a name="november-2020"></a>November 2020

### <a name="azure-portal-experience"></a>Azure-Portal-Benutzeroberfläche

Wir haben zwei Fehler im Azure-Portal behoben:

- Der Anzeigename der Desktopanwendung wird im Workflow „VM hinzufügen“ nicht mehr überschrieben.
- Die Sitzungshost-Registerkarte wird nun geladen, wenn Sitzungshosts Teil von Skalierungsgruppen sind.

### <a name="fslogix-client-version-2009"></a>FSLogix-Client, Version 2009 

Wir haben eine neue Version des FSLogix-Clients mit vielen Fixes und Verbesserungen veröffentlicht. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>RDP Shortpath (Public Preview)

Mit RDP Shortpath wird die direkte Konnektivität mit Ihrem Azure Virtual Desktop-Sitzungshost eingeführt, indem Point-to-Site-/Site-to-Site-VPNs und ExpressRoute verwendet werden. Außerdem wird das URCP-Transportprotokoll eingeführt. RDP Shortpath ist auf die Reduzierung von Wartezeit und Netzwerkhops ausgelegt, um die Benutzerfreundlichkeit zu verbessern. Weitere Informationen finden Sie unter [Azure Virtual Desktop – RDP Shortpath (Vorschau)](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, Version 2.0.1

Wir haben Version 2.0.1 der Azure Virtual Desktop-Cmdlets veröffentlicht. Dieses Update enthält Cmdlets für die Verwaltung des MSIX-Features zum Anfügen von Apps. Sie können die neue Version aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1) herunterladen.

### <a name="azure-advisor-updates"></a>Azure Advisor-Updates

Azure Advisor verfügt nun über eine neue Empfehlung für die Näherungsplatzierung in Azure Virtual Desktop sowie eine neue Empfehlung für die Leistungsoptimierung in Hostpools mit tiefenorientiertem Lastenausgleich. Weitere Informationen finden Sie auf der [Azure-Website](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Oktober 2020

Änderungen im Oktober 2020:

### <a name="improved-performance"></a>Verbesserte Leistung

- Wir haben die Leistung optimiert, indem wir die Verbindungslatenz in den folgenden Azure-Regionen reduziert haben:
    - Schweiz
    - Kanada

Sie können nun den [Qualitätsschätzer](https://azure.microsoft.com/services/virtual-desktop/assessment/) verwenden, um die Qualität der Benutzererfahrung in diesen Bereichen zu schätzen.

### <a name="azure-government-cloud-availability"></a>Verfügbarkeit der Azure Government-Cloud

Die Azure Government-Cloud ist jetzt allgemein verfügbar. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="azure-virtual-desktop-azure-portal-updates"></a>Aktualisierungen im Azure-Portal für Azure Virtual Desktop

Wir haben einige Aktualisierungen im Azure-Portal für Azure Virtual Desktop vorgenommen:

- Es wurde ein resourceID-Fehler behoben, der dazu führte, dass Benutzer die Registerkarte „Sitzungen“ nicht öffnen konnten.
- Die Benutzeroberfläche auf der Registerkarte „Sitzungshosts“ wurde optimiert.
- Unter „RDP-Eigenschaften“ wurden die Einstellungen „Standardwerte“, „Verwendbarkeit“ und „Standardwerte wiederherstellen“ korrigiert.
- Die Funktionen „Entfernen“ und „Löschen“ wurden auf allen Registerkarten vereinheitlicht.
- Im Portal werden App-Namen nun im Workflow „App hinzufügen“ überprüft.
- Ein Problem wurde behoben, das dazu führte, dass die Exportdaten des Sitzungshosts in den Spalten nicht ausgerichtet waren.
- Ein Problem wurde behoben, das dazu führte, dass vom Portal keine Benutzersitzungen abgerufen werden konnten.
- Ein Problem mit dem Abruf von Sitzungshosts wurde behoben, das auftrat, wenn der virtuelle Computer in einer anderen Ressourcengruppe erstellt wurde.
- Die Registerkarte „Sitzungshost“ wurde aktualisiert, sodass sowohl aktive als auch getrennte Sitzungen aufgeführt werden.
- Die Registerkarte „Anwendungen“ verfügt jetzt über Seiten.
- Ein Problem wurde behoben, das dazu führte, dass der Hinweis auf eine erforderliche Befehlszeile nicht ordnungsgemäß auf der Registerkarte „Anwendungsliste“ angezeigt wurde.
- Ein Problem wurde behoben, das dazu führte, dass bei Verwendung der deutschsprachigen Version von Shared Image Gallery keine Hostpools oder virtuellen Computer durch das Portal bereitgestellt werden konnten.

### <a name="client-updates-for-october-2020"></a>Clientupdates für Oktober 2020

Wir haben neue Clientversionen veröffentlicht. Weitere Informationen finden Sie in diesen Artikeln:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Weitere Informationen zu den anderen Clients finden Sie unter [Clientupdates](#client-updates).

## <a name="september-2020"></a>September 2020

Änderungen im September 2020:

- Wir haben die Leistung optimiert, indem wir die Verbindungslatenz in den folgenden Azure-Regionen reduziert haben:
    - Deutschland
    - Südafrika (nur für Validierungsumgebungen)

Sie können nun den [Qualitätsschätzer](https://azure.microsoft.com/services/virtual-desktop/assessment/) verwenden, um die Qualität der Benutzererfahrung in diesen Bereichen zu schätzen.

- Wir haben Version 1.2.1364 des Windows-Desktopclients für Azure Virtual Desktop veröffentlicht. In diesem Update haben wir die folgenden Änderungen vorgenommen:
    - Es wurde ein Problem behoben, bei dem Einmaliges Anmelden (Single Sign-On, SSO) unter Windows 7 nicht funktionierte.
    - Es wurde ein Problem behoben, das dazu geführt hat, dass der Client die Verbindung trennt, wenn ein Benutzer, der die Medienoptimierung für Teams aktiviert hat, versucht, einer Teams-Besprechung beizutreten bzw. diese anzurufen, während eine andere App einen Audiostream im exklusiven Modus geöffnet hat.
    - Es wurde ein Problem behoben, bei dem Teams keine Audio- oder Videogeräte aufgezählt haben, wenn die Medienoptimierung für Teams aktiviert war.
    - Ein Link namens „Need help with settings?“ (Benötigen Sie Hilfe mit den Einstellungen?), der zur Desktopeinstellungsseite führt, wurde ergänzt.
    - Es wurde ein Problem mit der Schaltfläche „Abonnieren“ behoben, das bei der Verwendung von dunklen Designs mit hohem Kontrast auftrat.
    
- Dank der enormen Hilfe unserer Benutzer haben wir zwei kritische Probleme für den Microsoft Store-Remotedesktopclient behoben. Wir werden weiterhin Feedback überprüfen und Probleme beheben, während wir unser stufenweises Release des Clients auf mehr Benutzer weltweit ausweiten.
    
- Wir haben ein neues Feature hinzugefügt, mit dem Sie den VM-Speicherort, das Image, die Ressourcengruppe, den Präfixnamen und die Netzwerkkonfiguration als Teil des Workflows zum Hinzufügen einer VM zu Ihrer Bereitstellung im Azure-Portal ändern können.

- IT-Spezialisten können nun mithilfe von Microsoft Endpoint Manager hybride mit Azure Active Directory verbundene Windows 10 Enterprise-VMs verwalten. Weitere Informationen finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>August 2020

Änderungen im August 2020:

- Die Leistung wurde verbessert, um die Verbindungswartezeit in den folgenden Azure-Regionen zu reduzieren: 

    - United Kingdom
    - Frankreich
    - Norwegen
    - Südkorea

   Sie können den [Qualitätsschätzer](https://azure.microsoft.com/services/virtual-desktop/assessment/) verwenden, um eine allgemeine Vorstellung davon zu bekommen, wie sich diese Änderungen auf Ihre Benutzer auswirken.

- Der Microsoft Store-Remotedesktopclient (v10.2.1522+) ist jetzt allgemein verfügbar. Diese Version des Microsoft Store-Remotedesktopclients ist mit Azure Virtual Desktop kompatibel. Darüber hinaus wurden aktualisierte Benutzeroberflächenflows eingeführt, um die Benutzerfreundlichkeit zu verbessern. Dieses Update beinhaltet ein flüssiges Design, einen hellen und einen dunklen Modus sowie viele weitere interessante Änderungen. Der Client wurde außerdem umgeschrieben und verwendet nun die gleiche zugrunde liegende RDP-Engine (Remotedesktopprotokoll) wie die iOS-, macOS- und Android-Clients. Dadurch können neue Features schneller auf allen Plattformen bereitgestellt werden. [Laden Sie den Client herunter](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab), und probieren Sie ihn aus.

- Ein Problem im Teams-Desktopclient (Version 1.3.00.21759) wurde behoben, bei dem der Client im Chat, in Kanälen und im Kalender nur die UTC-Zeitzone angezeigt hat. Im aktualisierten Client wird nun stattdessen die Zeitzone der Remotesitzung angezeigt.

- Azure Advisor ist jetzt Teil von Azure Virtual Desktop. Wenn Sie über das Azure-Portal auf Azure Virtual Desktop zugreifen, können Sie Empfehlungen zur Optimierung der Azure Virtual Desktop-Umgebung anzeigen. Weitere Informationen zu [Azure Advisor](azure-advisor.md)

- Die Azure CLI unterstützt jetzt Azure Virtual Desktop (`az desktopvirtualization`), um Ihnen die Automatisierung der Azure Virtual Desktop-Bereitstellungen zu erleichtern. Eine Liste der Erweiterungsbefehle finden Sie unter [desktopvirtualization](/cli/azure/desktopvirtualization).

- Wir haben die Bereitstellungsvorlagen aktualisiert, damit sie vollständig mit den Azure Resource Manager-Schnittstellen von Azure Virtual Desktop kompatibel sind. Die Vorlagen finden Sie auf [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Das US Gov-Portal von Azure Virtual Desktop ist jetzt als öffentliche Vorschauversion verfügbar. Weitere Informationen finden Sie in [unserer Ankündigung](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Juli 2020  

Ab Juli ist Azure Virtual Desktop mit Azure Resource Manager-Integration allgemein verfügbar.

Hier ist aufgeführt, was sich mit diesem neuen Release geändert hat: 

- Das „Release vom Herbst 2019“ wird jetzt als „Azure Virtual Desktop (klassisch)“ bezeichnet, und das „Release vom Frühjahr 2020“ einfach als „Azure Virtual Desktop“. Weitere Informationen finden Sie in [diesem Blogbeitrag](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Weitere Informationen zu neuen Features finden Sie in [diesem Blogbeitrag](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Aktualisierung des Tools für die automatische Skalierung

Die aktuelle Version des Tools für die automatische Skalierung, die sich in der Vorschauphase befunden hat, ist jetzt allgemein verfügbar. Für dieses Tool werden ein Azure Automation-Konto und die Azure-Logik-App verwendet, um Sitzungshost-VMs in einem Hostpool automatisch herunterzufahren und neu zu starten und so die Infrastrukturkosten zu reduzieren. Weitere Informationen finden Sie unter [Skalieren von Sitzungshosts mit Azure Automation](set-up-scaling-script.md).

### <a name="azure-portal"></a>Azure-Portal

Sie können im Azure-Portal in Azure Virtual Desktop nun Folgendes durchführen: 

- Direktes Zuweisen von Benutzern zu Sitzungshosts von persönlichen Desktops  
- Ändern der Einstellung für die Überprüfungsumgebung für Hostpools 

### <a name="diagnostics"></a>Diagnose

Wir haben einige neue vordefinierte Abfragen für den Log Analytics-Arbeitsbereich veröffentlicht. Navigieren Sie für den Zugriff auf die Abfragen zu **Protokolle**, und wählen Sie unter **Kategorie** die Option **Azure Virtual Desktop** aus. Weitere Informationen finden Sie unter [Verwenden von Log Analytics für die Diagnosefunktion](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Update für Remotedesktopclient für Android

Der [Remotedesktopclient für Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) unterstützt jetzt Azure Virtual Desktop-Verbindungen. Ab Version 10.0.7 verfügt der Android-Client über eine neue Benutzeroberfläche, um die Benutzerfreundlichkeit zu erhöhen. Der Client ist auch in Microsoft Authenticator auf Android-Geräten integriert, um beim Abonnieren von Azure Virtual Desktop-Arbeitsbereichen den bedingten Zugriff zu ermöglichen.  

Die vorherige Version des Remotedesktopclients wird jetzt als „Remotedesktop 8“ bezeichnet. Alle vorhandenen Verbindungen, über die Sie in der früheren Version des Clients verfügen, werden für den neuen Client nahtlos übernommen. Der neue Client wurde für die gleiche zugrunde liegende RDP-Haupt-Engine wie für die iOS- und macOS-Clients umgeschrieben. Dies ermöglicht eine schnellere übergreifende Veröffentlichung neuer Features auf allen Plattformen. 

### <a name="teams-update"></a>Teams-Update

Wir haben an Microsoft Teams für Azure Virtual Desktop Verbesserungen vorgenommen. Was am wichtigsten ist: Azure Virtual Desktop unterstützt jetzt die Audio- und Videooptimierung für den Windows Desktop-Client. Die Umleitung verbessert die Latenz, indem direkte Pfade zwischen Benutzern erstellt werden, wenn sie Audio- oder Videofunktionen für Anrufe und Besprechungen nutzen. Eine geringere Entfernung bedeutet weniger Hops, wodurch Optik und Klang verbessert werden. Weitere Informationen finden Sie unter [Verwenden von Microsoft Teams in Azure Virtual Desktop](./teams-on-avd.md).

## <a name="june-2020"></a>Juni 2020

Im letzten Monat haben wir Azure Virtual Desktop mit Azure Resource Manager-Integration als Vorschauversion eingeführt. Dieses Update enthält viele interessante neue Features, über die wir Sie gerne informieren möchten. Hier sind die Neuerungen für diese Version von Azure Virtual Desktop beschrieben.

### <a name="azure-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Azure Virtual Desktop ist jetzt in Azure Resource Manager integriert

Azure Virtual Desktop ist jetzt in Azure Resource Manager integriert. Im neuesten Update handelt es sich bei allen Azure Virtual Desktop-Objekten jetzt um Azure Resource Manager-Ressourcen. Dieses Update ist auch in Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung) integriert. Lesen Sie [Was ist Azure Resource Manager?](../azure-resource-manager/management/overview.md), um mehr zu erfahren.

Diese Änderung umfasst Folgendes:

- Azure Virtual Desktop ist jetzt in das Azure-Portal integriert. Dies bedeutet, dass Sie alles direkt im Portal verwalten können, ohne dass PowerShell, Web-Apps oder Drittanbietertools erforderlich sind. Informationen zu den ersten Schritten finden Sie in unserem Tutorial unter [Erstellen eines Hostpools mit dem Azure-Portal](create-host-pools-azure-marketplace.md).

- Vor diesem Update konnten Sie RemoteApps und Desktops nur für einzelne Benutzer veröffentlichen. Mit Azure Resource Manager können Sie jetzt Ressourcen in Azure Active Directory-Gruppen veröffentlichen.

- Bei der früheren Version von Azure Virtual Desktop hat es vier integrierte Administratorrollen gegeben, die Sie einem Mandanten oder Hostpool zuweisen konnten. Diese Rollen sind jetzt in [Azure RBAC (Role-Based Access Control, rollenbasierte Zugriffssteuerung)](../role-based-access-control/overview.md) enthalten. Sie können diese Rollen auf jedes Azure Resource Manager-Objekt von Azure Virtual Desktop anwenden, um ein vollständiges, umfangreiches Delegierungsmodell zu erhalten.

- Bei diesem Update müssen Sie Azure Marketplace oder die GitHub-Vorlage nicht mehr wiederholt ausführen, um einen Hostpool zu erweitern. Zum Erweitern eines Hostpools müssen Sie lediglich im Azure-Portal zu Ihrem Hostpool wechseln und **+ Hinzufügen** auswählen, um weitere Sitzungshosts bereitzustellen.

- Die Hostpoolbereitstellung ist jetzt in die [Azure Shared Image Gallery](../virtual-machines/shared-image-galleries.md) (Katalog der freigegebenen Images) vollständig integriert. Shared Image Gallery ist ein separater Azure-Dienst, unter dem VM-Imagedefinitionen, z. B. die Versionsverwaltung für Images, gespeichert werden. Sie können Ihre Images auch mithilfe der globalen Replikation kopieren und für lokale Bereitstellung in andere Azure-Regionen senden.

- Überwachungsfunktionen, die über PowerShell oder die Diagnostics Service-Webapp erledigt wurden, wurden jetzt im Azure-Portal in Log Analytics verschoben. Außerdem stehen Ihnen jetzt zwei Optionen zum Visualisieren Ihrer Berichte zur Verfügung. Sie können Kusto-Abfragen ausführen und Arbeitsmappen zum Erstellen virtueller Berichte verwenden.

- Sie müssen nicht mehr den Einwilligungsvorgang für Azure Active Directory (Azure AD) durchführen, um Azure Virtual Desktop verwenden zu können. Bei diesem Update authentifiziert der Azure AD-Mandant in Ihrem Azure-Abonnement Ihre Benutzer und stellt Azure RBAC-Steuerelemente für Ihre Administratoren bereit.

### <a name="powershell-support"></a>PowerShell-Unterstützung

Wir haben dem Az-Modul von Azure PowerShell im Rahmen dieses Updates neue AzWvd-Cmdlets hinzugefügt. Dieses neue Modul wird in PowerShell Core unterstützt, das unter .NET Core ausgeführt wird.

Befolgen Sie zum Installieren des Modells die Anleitungen unter [Einrichten des PowerShell-Moduls für Azure Virtual Desktop](powershell-module.md).

Eine Liste der verfügbaren Befehle finden Sie auch in der [AzWvd PowerShell-Referenz](/powershell/module/az.desktopvirtualization/#desktopvirtualization).

Weitere Informationen zu den neuen Features finden Sie in [unserem Blogbeitrag](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Zusätzliche Gateways

Wir haben in Südafrika einen neuen Gatewaycluster hinzugefügt, um die Verbindungslatenz zu reduzieren.

### <a name="microsoft-teams-on-azure-virtual-desktop-preview"></a>Microsoft Teams in Azure Virtual Desktop (Vorschau)

Wir haben an Microsoft Teams für Azure Virtual Desktop einige Verbesserungen vorgenommen. Die wichtigste Änderung besteht darin, dass Azure Virtual Desktop bei Anrufen jetzt die akustische und visuelle Umleitung unterstützt. Die Umleitung verbessert die Latenz, indem direkte Pfade zwischen Benutzern erstellt werden, wenn sie Audio- oder Videoanrufe durchführen. Eine geringere Entfernung bedeutet weniger Hops, wodurch Optik und Klang verbessert werden.

Weitere Informationen finden Sie in [unserem Blogbeitrag](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu zukünftigen Plänen finden Sie in der [Microsoft 365-Roadmap für Azure Virtual Desktop](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
