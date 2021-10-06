---
title: Releaseverlauf des Kennwortschutz-Agents – Azure Active Directory
description: Dokumentiert den Verlauf der Versionsveröffentlichungen und Verhaltensänderungen.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 06/04/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa4c7625637c84404b5ed7074c078717127ee1f
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545357"
---
# <a name="azure-ad-password-protection-agent-version-history"></a>Versionsverlauf des Azure AD-Kennwortschutz-Agents

## <a name="121760"></a>1.2.176.0

Releasedatum: 4. Juni 2021

* Kleinere Fehlerbehebungen bei Problemen, die die erfolgreiche Ausführung von Proxy- und DC-Agents in bestimmten Umgebungen verhinderten.

## <a name="121720"></a>1.2.172.0

Releasedatum: 22. Februar 2021

Vor fast zwei Jahren wurden die GA-Versionen der lokalen Azure AD-Kennwortschutz-Agents veröffentlicht. Jetzt ist ein neues Update verfügbar. Weitere Informationen finden Sie unten in den Änderungsbeschreibungen. Wir danken allen, die uns Feedback zum Produkt gegeben haben. 

* Die DC-Agent- und Proxy-Agent-Software setzen nun die Installation von .NET 4.7.2 voraus.
  * Wenn .NET 4.7.2 nicht bereits installiert ist, laden Sie das Installationsprogramm unter [.NET Framework 4.7.2-Offlineinstallationsprogramm für Windows](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2) herunter, und führen Sie es aus.
* Das PowerShell-Modul AzureADPasswordProtection wird nun auch von der DC-Agent-Software installiert.
* Es wurden zwei neue integritätsbezogene PowerShell-Cmdlets hinzugefügt: Test-AzureADPasswordProtectionDCAgent und Test-AzureADPasswordProtectionProxy.
* Die Kennwortfilter-DLL für den AzureADPasswordProtection-DC-Agent wird nun auf Computern geladen und ausgeführt, auf denen lsass.exe für die Ausführung im PPL-Modus konfiguriert ist.
* Eine Fehlerbehebung wurde bei dem Kennwortalgorithmus durchgeführt, der zuließ, dass gesperrte Kennwörter mit weniger als fünf Zeichen fälschlicherweise akzeptiert wurden.
  * Diese Fehlerbehebung ist nur relevant, wenn Ihre lokale AD-Richtlinie für die Mindestlänge von Kennwörtern so konfiguriert wurde, dass sie Kennwörter mit weniger als fünf Zeichen zulässt.
* Weitere geringfügige Fehlerbehebungen.

Die neuen Installationsprogramme aktualisieren ältere Versionen der Software automatisch. Wenn Sie sowohl die DC-Agent- als auch die Proxysoftware auf einem einzelnen Computer installiert haben (dies wird nur für Testumgebungen empfohlen), müssen Sie beide gleichzeitig aktualisieren.

Die Ausführung älterer und neuerer Versionen der DC-Agent-und Proxysoftware in einer Domäne oder Gesamtstruktur wird unterstützt, doch als bewährte Vorgehensweise wird empfohlen, alle Agents auf die neueste Version zu aktualisieren. Jede Reihenfolge der Agent-Upgrades wird unterstützt. Neue DC-Agents können über ältere Proxy-Agents kommunizieren, und ältere DC-Agents können über neuere Proxy-Agents kommunizieren.

## <a name="121250"></a>1.2.125.0

Releasedatum: 2. März 2019

* Beheben von kleineren Tippfehlern in Ereignisprotokollmeldungen
* Aktualisieren des Endbenutzer-Lizenzvertrags auf die endgültige Version der allgemeinen Verfügbarkeit

> [!NOTE]
> Build 1.2.125.0 ist der Build für „Allgemeine Verfügbarkeit“ Vielen Dank für Ihr Feedback zum Produkt!

## <a name="121160"></a>1.2.116.0

Releasedatum: 3. März 2019

* Mit den Cmdlets „Get-AzureADPasswordProtectionProxy“ und „Get-AzureADPasswordProtectionDCAgent“ werden nun die Softwareversion und der aktuelle Azure-Mandant angezeigt. Dabei gelten folgende Einschränkungen:
  * Die Daten zur Softwareversion und zum Azure-Mandanten sind nur für DC-Agents und Proxys verfügbar, die mit Version 1.2.116.0 oder höher ausgeführt werden.
  * Die Daten des Azure-Mandanten können erst angezeigt werden, nachdem eine erneute Registrierung (oder Erneuerung) des Proxys oder der Gesamtstruktur erfolgt ist.
* Für den Proxydienst ist nun die Installation von .NET 4.7 erforderlich.
  * Wenn .NET 4.7 nicht bereits installiert ist, laden Sie das Installationsprogramm unter [.NET Framework 4.7-Offlineinstallationsprogramm für Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows) herunter, und führen Sie es aus.
  * Auf Server Core-Systemen muss möglicherweise das Flag „/q“ an das .NET 4.7-Installationsprogramm übergeben werden, damit dieses erfolgreich ausgeführt werden kann.
* Der Proxydienst unterstützt jetzt das automatische Upgrade. Für das automatische Upgrade wird der Microsoft Azure AD Connect Agent Updater-Dienst verwendet, der zusammen mit dem Proxydienst installiert wird. Das automatische Upgrade ist standardmäßig aktiviert.
* Das automatische Upgrade kann mithilfe des Cmdlets „Set-AzureADPasswordProtectionProxyConfiguration“ aktiviert und deaktiviert werden. Die aktuelle Einstellung kann mithilfe des Cmdlets „Get-AzureADPasswordProtectionProxyConfiguration“ abgefragt werden.
* Die Dienstbinärdatei für den DC-Agent-Dienst wurde in „AzureADPasswordProtectionDCAgent.exe“ umbenannt.
* Die Dienstbinärdatei für den Proxydienst wurde in „AzureADPasswordProtectionProxy.exe“ umbenannt. Firewallregeln müssen möglicherweise entsprechend geändert werden, wenn eine Drittanbieterfirewall verwendet wird.
  * HINWEIS: Wenn eine HTTP-Proxy-Konfigurationsdatei in einer vorherigen Proxyinstallation verwendet wurde, muss sie nach diesem Upgrade umbenannt werden (von *proxyservice.exe.config* in *AzureADPasswordProtectionProxy.exe.config*).
* Alle zeitlich begrenzten Funktionsüberprüfungen wurden aus dem DC-Agent entfernt.
* Behebung kleinerer Programmfehler und Verbesserungen bei der Protokollierung

## <a name="12650"></a>1.2.65.0

Releasedatum: 1. Februar 2019

Änderungen:

* DC-Agent und Proxydienst werden jetzt unter Server Core unterstützt. Die Mindestanforderungen an das Betriebssystem bleiben unverändert: Windows Server 2012 für DC-Agents und Windows Server 2012 R2 für Proxys.
* Die Cmdlets Register-AzureADPasswordProtectionProxy und Register-AzureADPasswordProtectionForest unterstützen jetzt gerätecodebasierte Azure-Authentifizierungsmodi.
* Das Cmdlet Get-AzureADPasswordProtectionDCAgent ignoriert fehlerhafte und/oder ungültige Dienstverbindungspunkte. Diese Änderung behebt den Fehler, dass Domänencontroller manchmal mehrmals in der Ausgabe aufgeführt wurden.
* Das Cmdlet Get-AzureADPasswordProtectionSummaryReport ignoriert fehlerhafte und/oder ungültige Dienstverbindungspunkte. Diese Änderung behebt den Fehler, dass Domänencontroller manchmal mehrmals in der Ausgabe aufgeführt wurden.
* Das Proxy-PowerShell-Modul wird jetzt von „%ProgramFiles%\WindowsPowerShell\Modules“ registriert. Die Umgebungsvariable PSModulePath des Computers wird nicht mehr geändert.
* Das neue Cmdlet Get-AzureADPasswordProtectionProxy wurde hinzugefügt, um bei der Ermittlung von registrierten Proxys in einer Gesamtstruktur oder Domäne zu helfen.
* Der DC-Agent verwendet einen neuen Ordner in der SYSVOL-Freigabe für die Replikation von Kennwortrichtlinien und anderen Dateien.

   Alter Speicherort des Ordners:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Neuer Speicherort des Ordners:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   (Diese Änderung wurde vorgenommen, um falsch positive Warnungen zu verwaisten Gruppenrichtlinienobjekten zu vermeiden.)

   > [!NOTE]
   > Es erfolgt keine Migration oder Freigabe von Daten zwischen dem alten und dem neuen Ordner. Ältere Versionen des DC-Agents verwenden weiterhin den alten Speicherort, bis sie auf diese oder eine neuere Version aktualisiert werden. Sobald alle DC-Agents in Version 1.2.65.0 oder höher ausgeführt werden, kann der alte SYSVOL-Ordner manuell gelöscht werden.

* Der DC-Agent und der Proxydienst erkennen und löschen jetzt beschädigte Kopien von ihren jeweiligen Dienstverbindungspunkten.
* Jeder DC-Agent löscht regelmäßig beschädigte und veraltete DC-Agent- und Proxydienst-Verbindungspunkte in seiner Domäne. DC-Agent- und Proxydienst-Verbindungspunkte werden als veraltet betrachtet, wenn der Heartbeat-Zeitstempel älter als sieben Tage ist.
* Der DC-Agent erneuert nun das Zertifikat der Gesamtstruktur nach Bedarf.
* Der Proxydienst erneuert nun das Proxyzertifikat nach Bedarf.
* Updates am Kennwort-Überprüfungsalgorithmus: Die Liste der global gesperrten Kennwörter und die Liste der kundenspezifischen gesperrten Kennwörter (sofern konfiguriert) werden vor Kennwortüberprüfungen kombiniert. Ein bestimmtes Kennwort kann nun abgelehnt werden (Fehler oder nur Überwachung), wenn es Token sowohl aus der globalen als auch aus der kundenspezifischen Liste enthält. Die Ereignisprotokolldokumentation wurde entsprechend aktualisiert. Weitere Informationen finden Sie unter [Überwachen des Kennwortschutzes für Azure AD](howto-password-ban-bad-on-premises-monitor.md).
* Fehlerbehebungen bei Leistung und Stabilität
* Verbesserte Protokollierung.

> [!WARNING]
> Zeitlich begrenzte Funktionalität: Der DC-Agent-Dienst in diesem Release (1.2.65.0) beendet die Verarbeitung von Anforderungen zur Kennwortvalidierung ab dem 1. September 2019.  DC-Agent-Dienste in früheren Versionen (siehe Liste weiter unten) beenden die Verarbeitung ab dem 1. Juli 2019. Der DC-Agent-Dienst in allen Versionen protokolliert in den beiden Monaten vor diesen Terminen 10021-Ereignisse im Administratorereignisprotokoll. Im bevorstehenden Release mit allgemeiner Verfügbarkeit werden alle zeitlichen Einschränkungen entfernt. Der Proxy-Agent-Dienst ist in keiner Version zeitlich begrenzt, sollte jedoch trotzdem auf die neueste Version aktualisiert werden, um nachfolgende Fehlerbehebungen und Optimierungen nutzen zu können.

## <a name="12250"></a>1.2.25.0

Releasedatum: 1. November 2018

Fehlerbehebungen:

* DC-Agent- und Proxydienst sollten nicht mehr aufgrund von Zertifikatvertrauensfehlern fehlschlagen.
* DC-Agent- und Proxydienst weisen Fixes für FIPS-konforme Computer auf.
* Der Proxydienst funktioniert jetzt in einer reinen TLS 1.2-Netzwerkumgebung ordnungsgemäß.
* Kleinere Fehlerbehebungen bei Leistung und Stabilität.
* Verbesserte Protokollierung.

Änderungen:

* Die mindestens erforderliche Betriebssystemebene für den Proxydienst ist jetzt Windows Server 2012 R2. Die mindestens erforderliche Betriebssystemebene für den DC-Agent-Dienst bleibt Windows Server 2012.
* Der Proxydienst erfordert jetzt .NET Version 4.6.2.
* Der Algorithmus für die Kennwortüberprüfung verwendet eine erweiterte Zeichennormalisierungstabelle. Diese Änderung kann dazu führen, dass Kennwörter abgelehnt werden, die in früheren Versionen akzeptiert wurden.

## <a name="12100"></a>1.2.10.0

Releasedatum: 17. August 2018

Fehlerbehebungen:

* Register-AzureADPasswordProtectionProxy und Register-AzureADPasswordProtectionForest unterstützen jetzt mehrstufige Authentifizierung.
* Register-AzureADPasswordProtectionProxy erfordert eine WS2012-Domänencontroller oder höher in der Domäne, um Verschlüsselungsfehler zu vermeiden.
* Der DC-Agent-Dienst ist zuverlässiger beim Anfordern einer neuen Kennwortrichtlinie von Azure beim Starten.
* Der DC-Agent-Dienst fordert jede Stunde eine neue Kennwortrichtlinie von Azure an, falls erforderlich, führt dies aber jetzt zu einer zufällig ausgewählten Startzeit aus.
* Der DC-Agent-Dienst verursacht keine unbestimmte Verzögerung mehr bei einer neuen DC-Ankündigung, wenn er vor seiner Höherstufung als Replikat auf einem Server installiert wurde.
* Der DC-Agent-Dienst respektiert jetzt die Konfigurationseinstellung „Kennwortschutz für Windows Server Active Directory aktivieren“.
* Beide DC-Agent- und Proxy-Installationsprogramme unterstützen jetzt ein direktes Upgrade beim Upgrade auf zukünftige Versionen.

> [!WARNING]
> Ein direktes Upgrade von Version 1.1.10.3 wird nicht unterstützt und führt zu einem Installationsfehler. Für ein Upgrade auf Version 1.2.10 oder höher müssen Sie zuerst die DC-Agent- und Proxydienst-Software vollständig deinstallieren und dann die neue Version von Grund auf neu installieren. Eine erneute Registrierung des Azure AD-Kennwortschutz-Proxydiensts ist erforderlich.  Es ist nicht erforderlich, die Gesamtstruktur erneut zu registrieren.

> [!NOTE]
> Direkte Upgrades der DC-Agent-Software machen einen Neustart erforderlich.

* Der DC-Agent und Proxy-Dienst unterstützen nun die Ausführung auf einem Server, der für die ausschließliche Verwendung FIPS-konformer Algorithmen konfiguriert ist.
* Kleinere Fehlerbehebungen bei Leistung und Stabilität.
* Verbesserte Protokollierung.

## <a name="11103"></a>1.1.10.3

Releasedatum: 15. Juni 2018

Erste öffentliche Vorschauversion

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen des Kennwortschutzes für Azure AD](howto-password-ban-bad-on-premises-deploy.md)
