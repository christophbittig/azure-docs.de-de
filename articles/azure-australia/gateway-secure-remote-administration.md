---
title: Sichere Remoteverwaltung von Gateways in Azure Australien
description: Leitfaden zum Konfigurieren der sicheren Remoteverwaltung in den australischen Regionen, um die spezifischen Anforderungen der Richtlinien, Bestimmungen und Gesetze der australischen Regierung zu erfüllen
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 96d4d6273ef365435f7d21721600f145d40daae4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323536"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Sichere Remoteverwaltung Ihres Gateways in Azure Australien

Es ist entscheidend für die Verfügbarkeit und Integrität jedes Systems, dass administrative Aktivitäten sicher ausgeführt und gesteuert werden. Administrative Aktivitäten sollten über ein sicheres Gerät und eine sichere Verbindung erfolgen und durch strenge Authentifizierungs- und Autorisierungsprozesse unterstützt werden. Durch die sichere Remoteverwaltung wird dafür gesorgt, dass nur autorisierte Aktionen von autorisierten Administratoren ausgeführt werden.

Dieser Artikel enthält Informationen zum Implementieren einer sicheren Remoteverwaltungsfunktion für ein über das Internet zugängliches System, das in Azure gehostet wird und dem Verbraucherleitfaden des Australian Cyber Security Centre (ACSC) und der Absicht des Leitfadens für Informationssicherheit (Information Security Manual, ISM) des ACSC entspricht.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Anforderungen des Australian Cyber Security Centre (ACSC)

Die allgemeinen Sicherheitsanforderungen für Systeme im Commonwealth sind im ISM festgelegt. Zur Unterstützung von Commonwealth-Entitäten bei der sicheren Verwaltung hat das ACSC [ACSC Protect: Sichere Verwaltung](https://www.acsc.gov.au/publications/protect/secure-administration.htm) veröffentlicht.

In diesem Dokument wird die Wichtigkeit der sicheren Verwaltung erläutert und eine Methode zur Implementierung einer sicheren Verwaltungsumgebung vorgeschlagen. Die Elemente einer sicheren Verwaltungslösung werden im Dokument wie folgt beschrieben:

|Element   |BESCHREIBUNG   |
|---|---|
|Steuerung des privilegierten Zugriffs   |Das Steuern des Zugriffs auf privilegierte Konten ist eine grundlegende Sicherheitsmaßnahme, die privilegierte Konten vor Missbrauch schützt. Die Methoden für die Zugriffssteuerung umfassen die Konzepte „Geringste Rechte“ und „Erforderlich“ sowie Prozesse und Prozeduren zum Verwalten von Dienstkonten und Bewegungen des Personals.   |
|Multi-Factor Authentication   |Das Implementieren zusätzlicher Authentifizierungsfaktoren neben Benutzernamen und Passphrasen wie z. B. physische Token oder Smartcards kann dazu beitragen, kritische Ressourcen zu schützen. Wenn ein Angreifer Anmeldeinformationen für privilegierte Konten kompromittiert, können die Konsequenzen erheblich reduziert werden, wenn alle administrativen Aktionen zuerst eine Art der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) durchlaufen müssen.|
|Privilegierte Arbeitsstationen|Die Verwendung einer bekannten sicheren Umgebung für administrative Aufgaben kann das Risiko verringern, dass das Netzwerk durch die Implementierung zusätzlicher Sicherheitskontrollen kompromittiert wird.|
|Protokollierung und Überwachung   |Durch das automatisierte Generieren, Sammeln und Analysieren von sicherheits- und verwaltungsbezogenen Ereignissen von Arbeitsstationen, Servern, Netzwerkgeräten und Jumpboxes können Kompromittierungen und versuchte Kompromittierungen erkannt werden. Dank der Automatisierung können Organisationen schneller reagieren und die Auswirkungen einer Kompromittierung reduzieren.|
|Netzwerksegmentierung und -trennung|Das Segmentieren eines Netzwerks in logische Zonen wie z. B. unterschiedliche Sicherheitsdomänen und das weitere Trennen dieser logischen Netzwerke durch Einschränken der Datentypen, die von einer Zone in eine andere fließen, erschweren Lateral-Movement-Vorgänge. Durch Segmentierung wird verhindert, dass ein Angreifer Zugriff auf zusätzliche Ressourcen erhält.|
|Jumpboxes|Eine Jumpbox ist ein gehärteter Remotezugriffsserver, der häufig die Remotedesktopdienste von Microsoft oder die Software Secure Shell (SSH) nutzt. Jumpboxes fungieren als Punkt, von dem aus Administratoren auf kritische Systeme und alle administrativen Aktionen zugreifen können, die vom dedizierten Host ausgeführt werden.|

Dieser Artikel enthält eine Referenzarchitektur, die zeigt, wie die oben aufgeführten Elemente für die sichere Verwaltung von Systemen verwendet werden können, die in Azure bereitgestellt werden.

## <a name="architecture"></a>Aufbau

Für eine sichere Verwaltungsfunktion sind mehrere Komponenten erforderlich, die eine zusammenhängende Lösung bilden. In der bereitgestellten Referenzarchitektur werden die Komponenten den in [ACSC Protect: Sichere Verwaltung](https://www.acsc.gov.au/publications/protect/secure-administration.htm) beschriebenen Elementen zugeordnet.

![Architektur der sicheren Remoteverwaltung von Azure](media/remote-admin.png)

## <a name="components"></a>Komponenten

Die Architektur ist darauf ausgelegt, sicherzustellen, dass einem privilegierten Konto nur die erforderlichen Berechtigungen gewährt werden, es sicher identifiziert wird und dann nur von einem autorisierten Gerät und über sichere Kommunikationsmechanismen, die gesteuert und überwacht werden, Zugriff auf administrative Schnittstellen erhält.

|Lösung| Komponenten|Elemente|
|---|---|---|
|Sichere Geräte |<ul><li>Privilegierte Arbeitsstation</li><li>Mobiles Gerät</li><li>Microsoft Intune</li><li>Gruppenrichtlinie</li><li>Jumpserver/Bastionhost</li><li>Just-In-Time-Verwaltung (JIT)</li></ul> |<ul><li>Privilegierte Arbeitsstationen</li><li>Jumpboxes</li></ul>|
|Sichere Kommunikation |<ul><li>Azure-Portal</li><li>Azure VPN Gateway</li><li>Remotedesktopgateway (RD)</li><li>Netzwerksicherheitsgruppen</li></ul> |<ul><li>Netzwerksegmentierung und -trennung</li></ul>|
|Strenge Authentifizierung |<ul><li>Domänencontroller (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Netzwerkrichtlinienserver (NPS)</li><li>Azure AD MFA</li></ul> |<ul><li>Multi-Factor Authentication</li></ul> |
|Strenge Autorisierung |<ul><li>Identity and Access Management (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Bedingter Zugriff</li></ul>|<ul><li>Steuerung des privilegierten Zugriffs</li></ul>|
|||

>[!NOTE]
>Weitere Informationen zum Protokollierungs- und Überwachungselement finden Sie unter [Gateway: Protokollierung, Überwachung und Sichtbarkeit](gateway-log-audit-visibility.md).

## <a name="administration-workflow"></a>Verwaltungsworkflow

Die Verwaltung von in Azure bereitgestellten Systemen wird in zwei verschiedene Kategorien unterteilt, in die Verwaltung der Azure-Konfiguration und die Verwaltung von in Azure bereitgestellten Workloads. Die Azure-Konfiguration wird über das Azure-Portal durchgeführt. Die Workloadverwaltung erfolgt über Verwaltungsmechanismen wie das Remotedesktopprotokoll (Remote Desktop Protocol, RDP), Secure Shell (SSH) oder für Platform-as-a-Service-Funktionen (PaaS) über Tools wie SQL Management Studio.

Der Zugriff für die Verwaltung ist ein mehrstufiger Prozess, der die in der Architektur aufgeführten Komponenten umfasst und Zugriff auf das Azure-Portal und die Azure-Konfiguration erfordert, bevor der Zugriff auf Azure-Workloads erfolgen kann.

>[!NOTE]
> Die hier beschriebenen Schritte sind der allgemeine Prozess, bei dem die Komponenten der grafischen Benutzeroberfläche (Graphical User Interface, GUI) von Azure verwendet werden. Diese Schritte können auch mithilfe von anderen Schnittstellen wie PowerShell ausgeführt werden.

### <a name="azure-configuration-and-azure-portal-access"></a>Azure-Konfiguration und Zugriff auf das Azure-Portal

|Schritt |BESCHREIBUNG |
|---|---|
|Privilegierte Anmeldung bei der Arbeitsstation |Der Administrator meldet sich mit Administratoranmeldeinformationen bei der privilegierten Arbeitsstation an. Mit der Steuerung durch die Gruppenrichtlinie wird verhindert, dass sich andere Konten als Administratorkonten bei der privilegierten Arbeitsstation authentifizieren und sich Administratorkonten bei nicht privilegierten Arbeitsstationen authentifizieren. Microsoft Intune verwaltet die Konformität der privilegierten Arbeitsstation, um sicherzustellen, dass sie bei Softwarepatches, Antischadsoftware und anderen Konformitätsanforderungen auf dem neuesten Stand ist. |
|Anmeldung beim Azure-Portal |Der Administrator öffnet einen Webbrowser mit dem Azure-Portal, der mit Transport Layer Security (TLS) verschlüsselt wird, und meldet sich mit Administratoranmeldeinformationen an. Die Authentifizierungsanforderung wird durch Azure Active Directory direkt oder durch Authentifizierungsmechanismen wie Active Directory-Verbunddienste (Active Directory Federation Services, AD FS) oder Passthrough-Authentifizierung (Pass-Through Authentication, PTA) verarbeitet. |
|Azure AD MFA |Azure AD MFA sendet eine Authentifizierungsanforderung an das registrierte mobile Gerät des privilegierten Kontos. Das mobile Gerät wird von Intune verwaltet, um die Konformität mit Sicherheitsanforderungen zu gewährleisten. Der Administrator muss sich zuerst mithilfe einer PIN oder eines biometrischen Systems beim mobilen Gerät und dann bei der Microsoft Authenticator-App authentifizieren, damit der Authentifizierungsversuch an Azure AD MFA autorisiert wird. |
|Bedingter Zugriff |Durch Richtlinien für bedingten Zugriff wird der Authentifizierungsversuch überprüft, um sicherzustellen, dass er die nötigen Anforderungen erfüllt. Dazu zählen die IP-Adresse, von der die Verbindung ausgeht, die Gruppenmitgliedschaft für das privilegierte Konto und der von Intune gemeldete Verwaltungs- und Konformitätsstatus der privilegierten Arbeitsstation. |
|Privileged Identity Management (PIM) |Der Administrator kann nun über das Azure-Portal die privilegierten Rollen, für die er über Privileged Identity Management (PIM) autorisiert ist, aktivieren oder deren Aktivierung anfordern. PIM stellt sicher, dass privilegierte Konten nicht über ständige Administratorrechte verfügen und dass alle Anforderungen von privilegiertem Zugriff nur für die Zeit gelten, die für die Ausführung von administrativen Aufgaben erforderlich ist. PIM bietet auch eine Protokollierung aller Anforderungen und Aktivierungen zu Überwachungszwecken. |
|Identitäts- und Zugriffsverwaltung|Nachdem das privilegierte Konto sicher identifiziert wurde und Rollen aktiviert wurden, erhält der Administrator Zugriff auf die Azure-Abonnements und -Ressourcen, für die ihm über die Identitäts- und Zugriffsverwaltung (Identity and Access Management, IAM) Berechtigungen erteilt wurden.|

Nachdem für das privilegierte Konto die Schritte für den Administratorzugriff auf das Azure-Portal abgeschlossen wurden, kann der Zugriff auf die Workloads konfiguriert werden, und es können administrative Verbindungen hergestellt werden.

### <a name="azure-workload-administration"></a>Verwaltung von Azure-Workloads

|Schritt |BESCHREIBUNG|
|---|---|
|Just-In-Time-Zugriff (JIT)|Der Administrator verwendet JIT, um Zugriff auf das RDP und den Jumpserver von der IP-Adresse des RD-Gateways sowie ein RDP oder SSH vom Jumpserver auf die entsprechenden virtuellen Computer (Virtual Machine, VM) für die Workloads anzufordern.|
|Azure VPN Gateway|Der Administrator stellt nun eine Point-to-Site-IPSec-VPN-Verbindung von der privilegierten Arbeitsstation zum Azure-VPN-Gateway her, das die Zertifikatauthentifizierung durchführt, um die Verbindung herzustellen.|
|RD-Gateway|Der Administrator versucht nun, eine RDP-Verbindung mit dem Jumpserver über das RD-Gateway herzustellen, das in der Konfiguration der Remotedesktopverbindung angegeben ist. Das RD-Gateway verfügt über eine private IP-Adresse, die über die Azure-VPN-Gatewayverbindung erreichbar ist. Die Richtlinien für das RD-Gateway steuern, ob das privilegierte Konto für den Zugriff auf den angeforderten Jumpserver autorisiert ist. Das RD-Gateway fordert den Administrator zur Eingabe von Anmeldeinformationen auf und leitet die Authentifizierungsanforderung an den Netzwerkrichtlinienserver (Network Policy Server, NPS) weiter.|
|Netzwerkrichtlinienserver (NPS)|Der NPS empfängt die Authentifizierungsanforderung vom RD-Gateway und überprüft den Benutzernamen und das Kennwort bei Active Directory, bevor er eine Anforderung an Azure Active Directory sendet, um eine Azure AD MFA-Authentifizierungsanforderung auszulösen.|
|Azure AD MFA|Azure AD MFA sendet eine Authentifizierungsanforderung an das registrierte mobile Gerät des privilegierten Kontos. Das mobile Gerät wird von Intune verwaltet, um die Konformität mit Sicherheitsanforderungen zu gewährleisten. Der Administrator muss sich zuerst mithilfe einer PIN oder eines biometrischen Systems beim mobilen Gerät und dann bei der Microsoft Authenticator-App authentifizieren, damit der Authentifizierungsversuch an Azure AD MFA autorisiert wird.|
|Jumpserver|Nach der erfolgreichen Authentifizierung wird die RDP-Verbindung mit Transport Layer Security (TLS) verschlüsselt und dann über den verschlüsselten IPSec-Tunnel an das Azure-VPN Gateway, über das RD-Gateway und an den Jumpserver gesendet. Vom Jumpserver aus kann der Administrator nun wie in der JIT-Anforderung angegeben eine RDP- oder SSH-Workload für virtuelle Computer ausführen.|

## <a name="general-guidance"></a>Allgemeine Hinweise

Beim Implementieren der in diesem Artikel aufgeführten Komponenten gelten die folgenden allgemeinen Hinweise:

* Überprüfen Sie die regionale Verfügbarkeit von Diensten, stellen Sie dabei sicher, dass alle Daten innerhalb der genehmigten Standorte verbleiben, und stellen Sie Workloads der Klassifizierungsstufe „PROTECTED“ bevorzugt in „Australien, Mitte“ oder „Australien, Mitte 2“ bereit.

* Nutzen Sie die Publikation *Azure – ACSC Certification Report – Protected 2018 (Azure – ACSC-Zertifizierungsbericht – Klassifizierungsstufe „PROTECTED“ – 2018)*, um den Zertifizierungsstatus einzelner Dienste herauszufinden, und führen Sie für alle relevanten Komponenten, die nicht im Bericht enthalten sind, selbst eine Bewertung gemäß *ACSC CONSUMER GUIDE – Microsoft Azure at PROTECTED (ACSC-VERBRAUCHERLEITFADEN – Microsoft Azure in der Klassifizierungsstufe „PROTECTED“)* durch.

* Stellen Sie Netzwerkkonnektivität und alle benötigten Proxykonfigurationen für den Zugriff auf erforderliche Authentifizierungskomponenten wie Azure AD, AD FS und PTA sicher.

* Verwenden Sie Azure Policy, um die Konformität mit Anforderungen zu überwachen und zu erzwingen.

* Stellen Sie sicher, dass virtuelle Computer, insbesondere Active Directory-Domänencontroller, in verschlüsselten Speicherkonten gespeichert werden und Azure Disk Encryption nutzen.

* Erstellen und verwalten Sie stabile Prozesse und Governance für die Verwaltung von Identitäten und Administratorrechten, um die in diesem Artikel aufgeführten technischen Steuermöglichkeiten zu unterstützen.

|Ressource|URL|
|---|---|
|Dokumente zur Konformität mit Bestimmungen und Richtlinien für Australien|[Dokumente zur Konformität mit Bestimmungen und Richtlinien für Australien](https://aka.ms/au-irap)|
|Azure-Produkte: Australische Regionen und andere|[Azure-Produkte: Australische Regionen und andere](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Strategien zum Mindern von Cybersicherheitsvorfällen|[Strategien zum Mindern von Cybersicherheitsvorfällen](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC Protect: Sichere Verwaltung|[ACSC Protect: Sichere Verwaltung](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|Vorgehensweise: Integrieren Sie Ihre Remotedesktopgateway-Infrastruktur mit der Netzwerkrichtlinienserver-Erweiterung (Network Policy Server, NPS) und Azure AD|[Integrieren der Remotedesktopgateway-Infrastruktur mit der Netzwerkrichtlinienserver-Erweiterung (Network Policy Server, NPS) und Azure AD](../active-directory/authentication/howto-mfa-nps-extension-rdg.md)|

## <a name="component-guidance"></a>Leitfaden zu den Komponenten

Dieser Abschnitt enthält Informationen zum Zweck der einzelnen Komponenten und deren Rolle in der gesamten Architektur der sicheren Remoteverwaltung. Für den Zugriff auf nützliche Ressourcen wie Referenzdokumentation, Anleitungen und Tutorials werden zusätzliche Links bereitgestellt.

## <a name="secure-devices"></a>Sichern von Geräten

Die physischen Geräte, die von privilegierten Benutzern zum Ausführen administrativer Funktionen verwendet werden, sind lohnenswerte Ziele für bösartig Handelnde. Das Aufrechterhalten der Sicherheit und Integrität der physischen Geräte, die Sicherstellung, dass sie frei von Schadsoftware sind, sowie deren Schutz vor Kompromittierung sind wichtige Bestandteile einer sicheren Remoteverwaltungsfunktion. Dazu zählt eine Sicherheitskonfiguration mit hoher Priorität, wie sie in „Essential Eight Strategies to Mitigate Cyber Security Incidents“ (Acht grundlegende Strategien zum Mindern von Cybersicherheitsvorfällen) des ACSC angegeben wird, z. B. Filtern, Patchen und Härten von Anwendungen sowie Patchen von Betriebssystemen. Diese Funktionen müssen installiert, konfiguriert, überwacht, überprüft und gemeldet werden, um sicherzustellen, dass der Status eines Geräts mit den Anforderungen der Organisation konform ist.

### <a name="privileged-workstation"></a>Privilegierte Arbeitsstation

Die privilegierte Arbeitsstation ist ein gehärteter Computer, der zum Ausführen administrativer Aufgaben verwendet werden kann und auf den nur Administratorkonten Zugriff haben. Auf der privilegierten Arbeitsstation sollten Richtlinien und Konfigurationen vorhanden sein, um die Software, die ausgeführt werden kann, und den Zugriff auf Netzwerkressourcen und das Internet zu begrenzen. Zudem sollten die Anmeldeinformationen geschützt werden, falls das Gerät gestohlen oder kompromittiert wird.

|Ressourcen|Link|
|---|---|
|Übersicht über die Arbeitsstationen mit privilegiertem Zugriff|[https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/](/security/compass/privileged-access-deployment)|
|Schützen des privilegierten Zugriffs – Referenzmaterial|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>Mobiles Gerät

Bei einem mobilen Gerät besteht aufgrund seiner Transportierbarkeit und geringen Größe ein erhöhtes Risiko, dass es versehentlich verloren geht oder gestohlen wird. Deshalb muss es entsprechend geschützt werden. Das mobile Gerät bietet einen starken zusätzlichen Authentifizierungsfaktor, da die Authentifizierung für den Gerätezugriff erzwungen und das Gerät über Standortdienste verfolgt werden kann. Zudem verfügt es über Verschlüsselungsfunktionen und die Möglichkeit zur Remotezurücksetzung. Wenn Sie ein mobiles Gerät als zusätzlichen Authentifizierungsfaktor für Azure verwenden, sollte das Gerät so konfiguriert sein, dass es die Microsoft Authenticator-App mit einer PIN oder biometrischer Authentifizierung anstatt Anrufen oder SMS nutzt.

|Ressourcen|Link|
|---|---|
|Azure AD-Authentifizierungsmethoden|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](../active-directory/authentication/concept-authentication-methods.md)|
|Verwenden der Microsoft Authenticator-App|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune ist die Komponente von Enterprise Mobility + Security, die mobile Geräte und Apps verwaltet. Sie lässt sich eng mit anderen Komponenten verzahnen, z. B. in Azure Active Directory für die Identitäts- und Zugriffssteuerung und in Azure Information Protection für den Datenschutz. Intune bietet Richtlinien für Arbeitsstationen und mobile Geräte, um Konformitätsanforderungen für den Zugriff auf Ressourcen festzulegen, sowie Berichts- und Überwachungsfunktionen, mit denen Sie Einblicke in den Status administrativer Geräte erhalten können.

|Ressourcen|Link|
|---|---|
|Microsoft Intune-Dokumentation|[https://docs.microsoft.com/intune/](/intune/)|
|Erste Schritte mit Gerätekonformität in Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](/intune/device-compliance-get-started)|

### <a name="group-policy"></a>Gruppenrichtlinie

Eine Gruppenrichtlinie wird verwendet, um die Konfiguration von Betriebssystemen und Anwendungen zu steuern. Mit Sicherheitsrichtlinien werden die Authentifizierungs-, Autorisierungs- und Überwachungseinstellungen eines Systems gesteuert. Die Gruppenrichtlinie wird zum Härten der privilegierten Arbeitsstation, zum Schützen von Administratoranmeldeinformationen und zum Einschränken nicht privilegierter Konten für den Zugriff auf privilegierte Geräte verwendet.

|Ressourcen|Link|
|---|---|
|Lokale Anmeldung zulassen – Sicherheitsrichtlinieneinstellung|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>Jumpserver/Bastionhost

Der Jumpserver/Bastionhost ist ein zentraler Punkt für die Verwaltung. Er verfügt über die Tools, die zum Ausführen von Verwaltungsaufgaben erforderlich sind, aber auch über den Netzwerkzugriff, der zum Herstellen einer Verbindung mit Ressourcen über administrative Ports benötigt wird. Der Jumpserver ist in diesem Artikel der zentrale Punkt für die Verwaltung von Workloads virtueller Computer. Er kann jedoch auch als autorisierter Punkt für die Verwaltung von PaaS-Funktionen wie SQL konfiguriert werden. Der Zugriff auf PaaS-Funktionen kann pro Dienst mithilfe von Identitäts- und Netzwerksteuerung eingeschränkt werden.

|Ressourcen|Link|
|---|---|
|Implementieren sicherer Verwaltungshosts|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>Just-In-Time-Zugriff (JIT)

JIT ist eine Funktion von Microsoft Defender für Cloud, die Netzwerksicherheitsgruppen (NSGs) nutzt, um den Zugriff auf Verwaltungsprotokolle wie RDP und SSH auf virtuellen Computern zu blockieren. Anwendungen, die auf virtuellen Computern gehostet werden, funktionieren weiterhin wie üblich, aber administrativer Zugriff muss angefordert werden und kann nur für einen festgelegten Zeitraum erteilt werden. Alle Anforderungen werden für die Überprüfung protokolliert.

|Ressourcen |Link |
|---|---|
|Verwalten des Just-In-Time-Zugriffs (JIT)|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](../security-center/security-center-just-in-time.md)|
|Automatisieren des Just-In-Time-Zugriffs auf VMs in Azure|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](/archive/blogs/motiba/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>Sichere Kommunikation

Der Datenverkehr für die Kommunikation bei Verwaltungsaktivitäten kann hochvertrauliche Informationen wie Administratoranmeldeinformationen enthalten und muss entsprechend verwaltet und geschützt werden. Das Bereitstellen einer sicheren Kommunikation umfasst zuverlässige Verschlüsselungsfunktionen, um Abhören zu verhindern, sowie Netzwerksegmentierung und Einschränkungen, wodurch der administrative Datenverkehr auf autorisierte Endpunkte beschränkt und im Fall der Kompromittierung eines Systems das Lateral Movement gesteuert wird.

### <a name="azure-portal"></a>Azure-Portal

Die Kommunikation mit dem Azure-Portal wird mit Transport Layer Security (TLS) verschlüsselt, und die Verwendung des Azure-Portals wurde vom ACSC zertifiziert. Commonwealth-Organisationen sollten die Empfehlungen im *ACSC-Verbraucherleitfaden* befolgen und ihre Webbrowser so konfigurieren, dass sie die neueste Version von TLS mit Unterstützung für Kryptografiealgorithmen verwenden.

|Ressourcen |Link |
|---|---|
|Übersicht über die Azure-Verschlüsselung – Verschlüsselung von Daten während der Übertragung|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

Das Azure-VPN Gateway bietet die sichere verschlüsselte Verbindung von der privilegierten Arbeitsstation zu Azure. Das Azure-VPN Gateway wurde durch das ACSC zertifiziert, da es eine sichere IPSec-Kommunikation gewährleistet. Commonwealth-Entitäten sollten das Azure-VPN Gateway gemäß dem ACSC-Verbraucherleitfaden, dem ACSC-Zertifizierungsbericht und weiteren speziellen Anleitungen konfigurieren.

|Ressourcen |Link |
|---|---|
|Informationen zu Point-to-Site-Verbindungen|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](../vpn-gateway/point-to-site-about.md)|
|Kryptografische Anforderungen und Azure-VPN-Gateways|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](../vpn-gateway/vpn-gateway-about-compliance-crypto.md)|
|Konfiguration von Azure-VPN-Gateways|[Azure VPN Gateway-Konfiguration](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>Remotedesktopgateway (RD)

Das RD-Gateway ist ein sicherer Mechanismus zum Steuern und Genehmigen von RDP-Verbindungen mit Systemen. Es funktioniert, indem RDP-Datenverkehr in Hypertext Transfer-Protokoll Secure (HTTPS) gekapselt und mithilfe von TLS verschlüsselt wird. TLS bietet eine zusätzliche Sicherheitsebene für administrativen Datenverkehr.

|Ressourcen |Link |
|---|---|
|Architektur der Remotedesktopdienste|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>Netzwerksicherheitsgruppen

NSGs fungieren als Zugriffssteuerungslisten (Access Control Lists, ACLs) für den eingehenden und ausgehenden Netzwerkdatenverkehr von Subnetzen oder virtuellen Computern. NSGs bieten Netzwerksegmentierung und einen Mechanismus zum Steuern und Einschränken der zwischen Systemen zulässigen Kommunikationsflows. NSGs sind eine Kernkomponente der Just-In-Time-Verwaltung (JIT) zum Zulassen oder Verweigern des Zugriffs auf administrative Protokolle.

|Ressourcen |Link |
|---|---|
|Übersicht über Azure-Sicherheitsgruppen|[https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|Vorgehensweise: Planen virtueller Netzwerke|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md)|

## <a name="strong-authentication"></a>Strenge Authentifizierung

Die sichere Identifizierung privilegierter Benutzer vor dem Gewähren des Zugriffs auf Systeme ist eine Kernkomponente der sicheren Verwaltung. Es müssen Mechanismen vorhanden sein, um die Anmeldeinformationen zu schützen, die einem privilegierten Konto zugeordnet sind, und zu verhindern, dass Angreifer über Identitätswechsel oder Diebstahl von Anmeldeinformationen Zugriff auf Systeme erlangen.

### <a name="domain-controller-dc"></a>Domänencontroller (DC)

Ein Domänencontroller (Domain Controller, DC) hostet auf hoher Ebene eine Kopie der Active Directory-Datenbank, in der alle Benutzer, Computer und Gruppen in einer Domäne enthalten sind. DCs führen eine Authentifizierung für Benutzer und Computer durch. Die DCs in dieser Architektur werden als virtuelle Computer in Azure gehostet und bieten Authentifizierungsdienste für privilegierte Konten, die eine Verbindung mit Jumpservern und Workload-VMs herstellen.

|Ressourcen |Link |
|---|---|
|Übersicht über Active Directory Domain Services|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD ist der Authentifizierungsdienst für Azure. Sie enthält die Cloud-Identitäten

und stellt Authentifizierung und Autorisierung für eine Azure-Umgebung bereit. Azure AD kann über Azure AD Connect mit Active Directory synchronisiert werden und eine Verbundauthentifizierung über Active Directory-Verbunddienste (AD FS) und Azure AD Connect bereitstellen. Azure AD ist eine Kernkomponente der sicheren Verwaltung.

|Ressourcen |Link |
|---|---|
|Dokumentation zu Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](../active-directory/index.yml)|
|Dokumentation zur Hybrid-Identität|[https://docs.microsoft.com/azure/active-directory/hybrid](../active-directory/hybrid/index.yml)|

### <a name="network-policy-server-nps"></a>Netzwerkrichtlinienserver (NPS)

Ein NPS ist ein Authentifizierungs- und Richtlinienserver, der erweiterte Authentifizierungs- und Autorisierungsprozesse bereitstellt. Der NPS-Server in dieser Architektur wird bereitgestellt, um die Azure AD MFA-Authentifizierung in RD-Gateway-Authentifizierungsanforderungen zu integrieren. Der NPS verfügt über ein bestimmtes Plug-In, das die Integration mit Azure AD MFA in Azure AD unterstützt.

|Ressourcen |Link |
|---|---|
|Dokumentation zum Netzwerkrichtlinienserver|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-ad-mfa"></a>Azure AD MFA

Azure AD MFA ist ein in Azure Active Directory bereitgestellter Authentifizierungsdienst, mit dem Authentifizierungsanforderungen über einen Benutzernamen und ein Kennwort hinaus für den Zugriff auf Cloudressourcen wie das Azure-Portal aktiviert werden können. Azure AD MFA unterstützt mehrere Authentifizierungsmethoden, und diese Architektur nutzt die Microsoft Authenticator-App für erhöhte Sicherheit und Integration mit dem NPS.

|Ressourcen |Link |
|---|---|
|So funktioniert's: Azure AD Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](../active-directory/authentication/concept-mfa-howitworks.md)|
|Vorgehensweise: Bereitstellen der cloudbasierten mehrstufigen Azure AD-Authentifizierung|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](../active-directory/authentication/howto-mfa-getstarted.md)|

## <a name="strong-authorisation"></a>Strenge Autorisierung

Nachdem ein privilegiertes Konto sicher identifiziert wurde, kann ihm der Zugriff auf Ressourcen gewährt werden. Die Autorisierung steuert und verwaltet die Berechtigungen, die einem bestimmten Konto zugewiesen sind. Strenge Autorisierungsprozesse entsprechen den grundlegenden acht Strategien zum Mindern von Cybersicherheitsvorfällen des ACSC zur Einschränkung von Administratorrechten.

### <a name="identity-and-access-management"></a>Identitäts- und Zugriffsverwaltung

Der Zugriff zum Durchführen privilegierter Aktionen in Azure basiert auf Rollen, die diesem Konto zugewiesen sind. Azure beinhaltet viele differenzierte Rollen mit spezifischen Berechtigungen für bestimmte Aufgaben. Diese Rollen können auf mehreren Ebenen wie einem Abonnement oder einer Ressourcengruppe erteilt werden. Die Verwaltung von Rollenzuweisungen und Berechtigungen basiert auf Konten und Gruppen in Azure Active Directory und erfolgt über Access Control (IAM) in Azure.

|Ressourcen |Link |
|---|---|
|Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC)|[https://docs.microsoft.com/azure/role-based-access-control](../role-based-access-control/index.yml)|
|Grundlegendes zu Rollendefinitionen|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](../role-based-access-control/role-definitions.md)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM ist eine Komponente von Azure Active Directory, die den Zugriff auf privilegierte Rollen steuert. Privilegierte Konten erfordern keinen permanenten oder ständigen privilegierten Zugriff, sondern können die Möglichkeit erhalten, privilegierten Zugriff für einen bestimmten Zeitraum anzufordern, um privilegierte Aktivitäten abzuschließen. PIM bietet zusätzliche Steuerungsmechanismen für die Wartung und Einschränkung des privilegierten Zugriffs sowie für die Protokollierung und Überwachung, um die Verwendung von Berechtigungen zu verfolgen.

|Ressourcen |Link |
|---|---|
|Privileged Identity Management-Dokumentation|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](../active-directory/privileged-identity-management/index.yml)|
|Einstieg in die Verwendung von PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](../active-directory/privileged-identity-management/pim-getting-started.md)|

### <a name="conditional-access"></a>Bedingter Zugriff

Der bedingte Zugriff ist eine Komponente von Azure Active Directory, die den Zugriff auf Ressourcen auf der Grundlage von Bedingungen zulässt oder verweigert. Diese Bedingungen können sich z. B. auf die Netzwerkadresse, den Gerätetyp, den Konformitätsstatus oder die Gruppenmitgliedschaft beziehen. Der bedingte Zugriff wird verwendet, um MFA, Geräteverwaltung und Konformität über Intune und Gruppenmitgliedschaften von Administratorkonten zu erzwingen.

|Ressourcen |Link |
|---|---|
|Dokumentation zum bedingten Zugriff|[https://docs.microsoft.com/azure/active-directory/conditional-access](../active-directory/conditional-access/index.yml)|
|Anleitung: Vorschreiben der Verwendung verwalteter Geräte für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](../active-directory/conditional-access/require-managed-devices.md)|

## <a name="next-steps"></a>Nächste Schritte

Ausführliche Informationen zum Steuern des Datenverkehrs durch Ihre Gatewaykomponenten in Azure finden Sie im Artikel [Steuern des eingehenden Datenverkehrs in Azure Australia](gateway-ingress-traffic.md).
