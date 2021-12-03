---
title: Azure Sicherungs- und Wiederherstellungsplan zum Schutz vor Ransomware | Microsoft-Dokumentation
description: Erfahren Sie, was Sie vor und während eines Ransomware-Angriffs tun müssen, um Ihre kritischen Geschäftssysteme zu schützen und eine schnelle Wiederherstellung des Geschäftsbetriebs zu gewährleisten.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 08/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5affe181185d726432596bfda6dad6f4f041de80
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277921"
---
# <a name="backup-and-restore-plan-to-protect-against-ransomware"></a>Sicherungs- und Wiederherstellungsplan zum Schutz vor Ransomware

Ransomwareangriffe verschlüsseln oder löschen absichtlich Daten und Systeme, um Ihre Organisation zu zwingen, Geld an Angreifer zu zahlen. Diese Angriffe zielen auf Ihre Daten, Ihre Sicherungskopien und auch die wichtige Dokumentation ab, die für die Wiederherstellung erforderlich ist, ohne die Angreifer zu bezahlen (um die Wahrscheinlichkeit zu erhöhen, dass Ihre Organisation zahlen wird).

Erfahren Sie, was Sie vor und während eines Ransomware-Angriffs tun müssen, um Ihre kritischen Geschäftssysteme zu schützen und eine schnelle Wiederherstellung des Geschäftsbetriebs zu gewährleisten.

> [!NOTE]
> Die Vorbereitung auf Ransomware verbessert auch die Widerstandsfähigkeit gegenüber Naturkatastrophen und schnellen Angriffen wie [WannaCry](https://en.wikipedia.org/wiki/WannaCry_ransomware_attack) & [ (Not)Petya](https://attack.mitre.org/software/S0368/).

## <a name="what-is-ransomware"></a>Was ist Ransomware?

Ransomware ist eine Art von Erpressungsangriff, bei dem Dateien und Ordner verschlüsselt werden und so der Zugriff auf wichtige Dateien verhindert wird. Angreifer nutzen Ransomware, um Geld von ihren Opfern zu erpressen, indem sie Geld, in der Regel in Form von Kryptowährungen, als Gegenleistung für einen Entschlüsselungsschlüssel oder als Gegenleistung dafür verlangen, dass sensible Daten nicht im Dark Web oder im öffentlichen Internet veröffentlicht werden.

Während frühe Ransomware meist Schadsoftware nutzte, die sich durch Phishing oder zwischen Geräten verbreitete, hat sich [menschengesteuerte Ransomware](/security/compass/human-operated-ransomware) herausgebildet, bei der eine Bande aktiver Angreifer, die von menschlichen Angreifern gesteuert wird, alle Systeme in einem Unternehmen angreift (und nicht nur ein einzelnes Gerät oder eine Gruppe von Geräten). Ein Angriff kann:

- Verschlüsseln Ihrer Daten
- Ihre Daten exfiltrieren
- Ihre Sicherungskopien beschädigen

Die Ransomware nutzt das Wissen der Angreifer über gängige System- und Sicherheitsfehlkonfigurationen und Schwachstellen, um in das Unternehmen einzudringen, sich im Unternehmensnetzwerk zurechtzufinden und sich an die Umgebung und ihre Schwachstellen anzupassen.

Ransomware kann so ausgelegt werden, dass Ihre Daten zuerst über mehrere Wochen oder Monate exfiltriert werden, bevor die Ransomware tatsächlich an einem bestimmten Datum ausgeführt wird.

Ransomware kann Ihre Daten auch langsam verschlüsseln, während Ihr Schlüssel auf dem System gespeichert wird. Wenn Ihr Schlüssel weiterhin verfügbar ist, können Ihre Daten für Sie verwendet werden, und die Ransomware wird nicht bemerkt. Ihre Sicherungskopien bestehen jedoch aus den verschlüsselten Daten. Sobald alle Ihre Daten verschlüsselt sind und aktuelle Sicherungskopien auch verschlüsselte Daten enthalten, wird Ihr Schlüssel entfernt, sodass Sie Ihre Daten nicht mehr lesen können.

Der eigentliche Schaden entsteht oft, wenn der Angriff Dateien exfiltriert und gleichzeitig Hintertüren im Netzwerk für künftige böswillige Aktivitäten zurücklässt – und diese Risiken bleiben bestehen, egal ob das Lösegeld gezahlt wird oder nicht. Diese Angriffe können schwerwiegende Folgen für den Geschäftsbetrieb haben und sind schwer zu bereinigen, da eine vollständige Entfernung der Angreifer erforderlich ist, um sich vor zukünftigen Angriffen zu schützen. Im Gegensatz zur anfänglichen Ransomware, die nur eine Beseitigung der Schadsoftware erfordert hat, kann von Menschen gesteuerte Ransomware auch nach dem ersten Auftreten eine Bedrohung für Ihren Geschäftsbetrieb darstellen.

### <a name="impact-of-an-attack"></a>Auswirkungen eines Angriffs

Die Auswirkungen eines Ransomware-Angriffs auf irgendeine Organisation lassen sich nur schwer genau quantifizieren. Je nach Umfang des Angriffs kann dies folgende Auswirkungen haben:

- Verlust des Datenzugriffs
- Unterbrechung des Geschäftsbetriebs
- Finanzieller Verlust
- Diebstahl von geistigem Eigentum
- Beeinträchtigtes Kundenvertrauen oder geschädigter Ruf
- Rechtskosten

## <a name="how-can-you-protect-yourself"></a>Wie können Sie sich schützen?

Der beste Weg, um zu verhindern, Opfer von Ransomware zu werden, besteht darin, Vorsichtsmaßnahmen zu ergreifen und über Tools zu verfügen, die Ihr Unternehmen vor jedem Schritt schützen, den Angreifer unternehmen, um Ihre Systeme zu infiltrieren.

Sie können Ihre lokale Gefährdung reduzieren, indem Sie Ihre Organisation in einen Clouddienst verschieben. Microsoft hat in native Sicherheitsfunktionen investiert, die Microsoft Azure resilient gegen Ransomware-Angriffe machen und Organisationen dabei unterstützen, Ransomware-Angriffstechniken abzuwehren. Eine umfassende Übersicht über Ransomware und Erpressung und den Schutz Ihrer Organisation erfahren Sie in der PowerPoint-Präsentation [Human-Operated Ransomware Mitigation Project Plan](https://download.microsoft.com/download/7/5/1/751682ca-5aae-405b-afa0-e4832138e436/RansomwareRecommendations.pptx) (Projektplan zur Entschärfung von Menschen platzierter Ransomware).

Sie sollten davon ausgehen, dass Sie irgendwann einem Ransomware-Angriff zum Opfer fallen werden. Einer der wichtigsten Schritte zum Schutz Ihrer Daten und zur Vermeidung von Lösegeldzahlungen ist ein zuverlässiger Sicherungs- und Wiederherstellungsplan für Ihre unternehmenskritischen Daten. Da Ransomware-Angreifer viel in die Neutralisierung von Datensicherungsprogrammen und Betriebssystemfunktionen wie Volumen-Schattenkopie investiert haben, ist es von entscheidender Bedeutung, Datensicherungen zu haben, die für bösartige Angreifer unzugänglich sind.

### <a name="azure-backup"></a>Azure Backup

[Azure Backup](../../backup/backup-overview.md) bietet Sicherheit für Ihre Sicherungsumgebung – sowohl während der Übertragung als auch bei ruhenden Daten. Mit Azure Backup [können Sie folgendes sichern](../../backup/backup-overview.md#what-can-i-back-up):

- Lokale Dateien, Ordner und Systemstatus
- Komplette Windows/Linux-VMs
- Azure Managed Disks
- Azure Dateifreigaben in einem Speicherkonto
- SQL Server-Datenbanken, die auf Azure-VMs laufen

Die Sicherungsdatei wird im Azure-Speicher gespeichert und der Gast oder Angreifer hat keinen direkten Zugriff auf den Sicherungsspeicher oder dessen Inhalt. Bei der Sicherung virtueller Maschinen erfolgt die Erstellung und Speicherung von Sicherungsmomentaufnahmen durch Azure Fabric, wobei der Gast oder der Angreifer nichts anderes zu tun hat, als den Workload für anwendungskonsistente Sicherungskopien stillzulegen. Mit SQL und SAP HANA erhält die Sicherungserweiterung temporären Schreibzugriff auf bestimmte Blobs. Auf diese Weise können vorhandene Sicherungskopien auch in einer kompromittierten Umgebung nicht von dem Angreifer manipuliert oder gelöscht werden.

Azure Backup bietet integrierte Überwachungs- und Warnungsfunktionen zum Anzeigen und Konfigurieren von Aktionen für Ereignisse im Zusammenhang mit Azure Backup. Sicherungsberichte stellen den zentralen Ort für die Nachverfolgung der Nutzung, das Überwachen von Sicherungen und Wiederherstellungen und die Identifizierung wichtiger Trends mit unterschiedlicher Granularität dar. Mithilfe der Überwachungs- und Berichterstellungstools von Azure Backup können Sie über alle nicht autorisierten, verdächtigen oder bösartigen Aktivitäten benachrichtigt werden, sobald sie auftreten.

Es wurden Prüfungen hinzugefügt, um sicherzustellen, dass bestimmte Vorgänge nur von gültigen Benutzern ausgeführt werden können. Dazu gehört das Hinzufügen einer zusätzlichen Authentifizierungsebene. Im Rahmen des Hinzufügens einer zusätzlichen Authentifizierungsebene für kritische Vorgänge werden Sie aufgefordert, eine Sicherheits-PIN einzugeben, bevor Sie [Online Sicherungskopien ändern](../../backup/backup-azure-security-feature.md#prevent-attacks).

Lernen Sie mehr über die in Azure Backup integrierten [Sicherheitsfunktionen](../../backup/security-overview.md).

### <a name="validate-backups"></a>Überprüfen von Sicherungen

Vergewissern Sie sich, dass Ihre Sicherungskopie gut ist, wenn sie erstellt wird und vor einer Wiederherstellung. Wie empfehlen, dass Sie einen [Recovery Services-Tresor](../../backup/backup-azure-recovery-services-vault-overview.md) benutzen, der eine Speicherentität in Azure ist, die Daten enthält. Bei den Daten handelt es sich in der Regel um Kopien von Daten oder Konfigurationsinformationen für virtuelle Computer (VMs), Workloads, Server oder Arbeitsstationen. Sie können Recovery Services-Datenspeicher verwenden, um Sicherungsdaten für verschiedene Azure-Dienste wie IaaS-VMs (Linux oder Windows) und Azure-SQL-Datenbanken sowie für lokale Ressourcen zu speichern. Recovery Services-Tresore machen es leicht, Ihre Sicherungsdaten zu organisieren und bieten Funktionen wie diese:

- Verbesserte Funktionen, die sicherstellen, dass Sie Ihre Sicherheitskopien schützen und Daten sicher wiederherstellen können, selbst wenn Produktions- und Sicherungsserver gefährdet sind. [Weitere Informationen](../../backup/backup-azure-security-feature.md)
- Überwachung Ihrer hybriden IT-Umgebung (Azure IaaS-VMs und lokale Ressourcen) über ein zentrales Portal. [Weitere Informationen](../../backup/backup-azure-monitoring-built-in-monitor.md)
- Kompatibilität mit Rollenbasierter Zugriffssteuerung (Azure RBAC), die den Zugriff auf Sicherungskopien und Wiederherstellungen auf die definierten Benutzerrollen beschränkt. Azure RBAC sieht verschiedene integrierte Rollen vor, und Azure Backup verfügt über drei integrierte Rollen zur Verwaltung von Wiederherstellungspunkten. [Weitere Informationen](../../backup/backup-rbac-rs-vault.md)
- Schutz mit vorläufigem Löschen, selbst wenn ein bösartiger Akteur eine Sicherheitskopie löscht (oder die Sicherungsdaten versehentlich gelöscht werden). Sicherungskopien werden 14 Tage länger aufbewahrt, damit ein jeweiliges Sicherungselement ohne Datenverluste wiederhergestellt werden kann. [Weitere Informationen](../../backup/backup-azure-security-feature-cloud.md)
- Die regionsübergreifende Wiederherstellung (Cross-Region Restore, CRR) ermöglicht es Ihnen, virtuelle Azure-Computer in einer sekundären Region wiederherzustellen, die eine gekoppelte Azure-Region ist. Sie können die replizierten Daten in der sekundären Region jederzeit wiederherstellen. Dadurch können Sie die Daten der sekundären Region zur Überwachung der Konformität und bei Ausfallszenarien wiederherstellen, ohne darauf warten zu müssen, dass Azure den Notfall deklariert (im Gegensatz zu den GRS-Einstellungen des Tresors). [Weitere Informationen](../../backup/backup-azure-arm-restore-vms.md#cross-region-restore)

> [!NOTE]
> Es gibt zwei Arten von Tresoren in Azure Backup. Zusätzlich zu den Recovery Services-Tresoren gibt es auch [Backup-Tresore](../../backup/backup-vault-overview.md), die Daten für neuere Workloads enthalten, die von Azure Backup unterstützt werden.

## <a name="what-to-do-before-an-attack"></a>Was vor einem Angriff zu tun ist

Sie sollten davon ausgehen, dass Sie irgendwann einem Ransomware-Angriff zum Opfer fallen werden. Wenn Sie Ihre unternehmenskritischen Systeme identifizieren und bewährte Methoden anwenden, bevor ein Angriff ausgeführt wird, werden Sie so schnell wie möglich wieder einsatzbereit sein. 

### <a name="determine-what-is-most-important-to-you"></a>Bestimmen, was für Sie am wichtigsten ist

Ransomware kann angriffsfähig sein, während Sie für einen Angriff planen. Daher sollte Ihre erste Priorität darin bestehen, die unternehmenskritischen Systeme zu identifizieren, die für Sie am wichtigsten sind, und mit der Durchführung regelmäßiger Sicherungskopien auf diesen Systemen beginnen.

Unserer Erfahrung nach fallen die fünf wichtigsten Anwendungen für Kunden in dieser Prioritätsreihenfolge in die folgenden Kategorien:

- Identitätssysteme– erforderlich, damit Benutzer auf alle Systeme (einschließlich aller anderen unten beschriebenen) zugreifen können, z. B.Active Directory, [Azure AD Connect](../../active-directory/hybrid/whatis-azure-ad-connect.md), AD-Domänencontroller
- Menschliches Leben – jedes System, das menschliches Leben unterstützt oder gefährden könnte, wie z. B. medizinische oder lebenserhaltende Systeme, Sicherheitssysteme (Krankenwagen, Abfertigungssysteme, Ampelsteuerungen), große Maschinen, chemische/biologische Systeme, Produktion von Lebensmitteln oder persönlichen Produkten und andere
- Finanzsysteme – Systeme zur Verarbeitung von Geldtransaktionen und zur Aufrechterhaltung des Geschäftsbetriebs, z. B. Zahlungssysteme und zugehörige Datenbanken, Finanzsystem für die vierteljährliche Berichterstattung
- Produkt- oder Dienstleistungsbefähigung – alle Systeme, die für die Erbringung der Unternehmensdienstleistungen oder die Herstellung/Lieferung der physischen Produkte, für die Ihre Kunden bezahlen, erforderlich sind, Fabriksteuerungssysteme, Produktlieferungs-/Versandsysteme und Ähnliches
- Sicherheit (Minimum) – Sie sollten auch den Sicherheitssystemen Priorität einräumen, die zur Überwachung von Angriffen und zur Bereitstellung von Mindestsicherheitsdiensten erforderlich sind. Hierbei sollte der Schwerpunkt darauf liegen, sicherzustellen, dass die aktuellen Angreifer (oder einfache opportunistische Angreifer) nicht sofort in der Lage sind, Zugang zu Ihren wiederhergestellten Systemen zu erhalten (oder wiederzuerlangen)

Ihre priorisierte Sicherungsliste wird auch zu Ihrer priorisierten Wiederherstellungsliste. Nachdem Sie Ihre kritischen Systeme identifiziert haben und regelmäßige Sicherungen durchführen, führen Sie Schritte aus, um Ihre Gefährdungsstufe zu verringern.

### <a name="steps-to-take-before-an-attack"></a>Schritte vor einem Angriff

Wenden Sie diese bewährten Methoden vor einem Angriff an.

| Aufgabe | Detail |
| --- | --- |
| Bestimmen Sie die wichtigen Systeme, die Sie zuerst wieder in Betrieb nehmen müssen (anhand der fünf oben genannten Kategorien), und beginnen Sie sofort mit der regelmäßigen Erstellung von Sicherungskopien dieser Systeme. | Um nach einem Angriff so schnell wie möglich wieder einsatzbereit zu sein, sollten Sie heute bestimmen, was für Sie am wichtigsten ist. |
| Migrieren Sie Ihre Organisation zur Cloud. <br><br>Erwägen Sie den Erwerb eines Microsoft Unified Supportplans oder die Zusammenarbeit mit einem Microsoft-Partner, um Ihren Wechsel in die Cloud zu unterstützen. | Reduzieren Sie Ihre lokale Gefährdung, indem Sie Daten mit automatischer Sicherung und Self-Service-Rollback in Clouddienste verschieben. Microsoft Azure verfügt über einen stabilen Satz von Tools, mit denen Sie Ihre unternehmenskritischen Systeme sichern und Ihre Sicherungskopien schneller wiederherstellen können. <br><br>[Microsoft Unified Support](https://www.microsoft.com/en-us/msservices/unified-support-solutions) ist ein Clouddienst-Supportmodell, das Sie jederzeit bei Bedarf unterstützt. Einheitlicher Support: <br><br>Stellt ein ausgewiesenes Team bereit, das rund um die Uhr für die Lösung von Problemen und die Eskalation kritischer Vorfälle zur Verfügung steht <br><br>Hilft Ihnen bei der Überwachung des Zustands Ihrer IT-Umgebung und arbeitet proaktiv, um sicherzustellen, dass Probleme verhindert werden, bevor sie auftreten |
| Verschieben Sie Benutzerdaten in Cloud-Lösungen wie OneDrive und SharePoint, um die Vorteile der [Versionierung und der Papierkorbfunktionen](/compliance/assurance/assurance-malware-and-ransomware-protection#sharepoint-online-and-onedrive-for-business-protection-against-ransomware) zu nutzen. <br><br>Schulung von Benutzern zur selbstständigen Wiederherstellung ihrer Dateien, um Verzögerungen und Wiederherstellungskosten zu reduzieren.   Wenn z. B. die OneDrive Dateien eines Benutzers von Schadsoftware infiziert wurden, kann der gesamte OneDrive auf einen früheren Zeitpunkt [wiederhergestellt](https://support.microsoft.com/office/restore-your-onedrive-fa231298-759d-41cf-bcd0-25ac53eb8a15?ui=en-US&rs=en-US&ad=US) werden. <br><br>Ziehen Sie eine Verteidigungsstrategie in Betracht, wie z. B. [Microsoft 365 Defender](/microsoft-365/security/defender/microsoft-365-defender), bevor Sie den Benutzern erlauben, ihre eigenen Dateien wiederherzustellen. | Benutzerdaten in der Microsoft Cloud können mit integrierten Features für die Sicherheit und Datenverwaltung geschützt werden. <br><br>Es ist gut, den Benutzern beizubringen, wie sie ihre eigenen Dateien wiederherstellen können, aber Sie müssen darauf achten, dass Ihre Benutzer nicht die Schadsoftware wiederherstellen, die für den Angriff verwendet wurde. Sie müssen folgende Schritte durchführen: <br><br>Stellen Sie sicher, dass Ihre Benutzer ihre Dateien erst dann wiederherstellen, wenn Sie sicher sind, dass der Angreifer entfernt wurde <br><br>Sorgen Sie für Risikominderung, falls ein Benutzer einen Teil der Schadsoftware wiederherstellt <br><br>Microsoft 365 Defender nutzt KI-gestützte automatische Aktionen und Playbooks, um betroffene Ressourcen wieder in einen sicheren Zustand zu versetzen. Microsoft 365 Defender nutzt die automatischen Abhilfemöglichkeiten der Produkte der Testsammlungen, um sicherzustellen, dass alle mit einem Vorfall in Verbindung stehenden betroffenen Ressourcen automatisch beseitigt werden, sofern dies möglich ist. |
| Implementieren Sie [Azure Security Benchmark](/security/benchmark/azure/introduction). | Azure Security Benchmark ist das Azure-eigene Sicherheitskontroll-Framework, das auf branchenüblichen Sicherheitskontroll-Frameworks wie NIST SP800-53, CIS Controls v7.1 basiert. Sie bietet Organisationen Anleitungen zum Konfigurieren von Azure- und Azure-Diensten und zum Implementieren der Sicherheitskontrollen. Siehe [Sicherung und Wiederherstellung](/security/benchmark/azure/security-controls-v2-backup-recovery). |
| Regelmäßiges Durchführen von Übungen für Ihren Plan für Geschäftskontinuität/Notfallwiederherstellung (BC/DR) <br><br>Simulieren Sie Szenarios zur Reaktion auf Vorfälle. Übungen, die Sie zur Vorbereitung auf einen Angriff durchführen, sollten geplant und rund um Ihre priorisierten Sicherungs- und Wiederherstellungslisten durchgeführt werden. <br><br>Testen Sie regelmäßig das Szenario " Wiederherstellen von Null", um sicherzustellen, dass Ihr BC/DR-System kritische Geschäftsabläufe schnell wieder in Betrieb nehmen kann, wenn alle Systeme ausgefallen sind. | Stellt eine schnelle Wiederherstellung des Geschäftsbetriebs sicher, indem ein Ransomware- oder Erpressungsangriff mit der gleichen Wichtigkeit wie eine Naturkatastrophe behandelt wird. <br><br>Durchführung von praktischen Übungen zur Validierung teamübergreifender Prozesse und technischer Verfahren, einschließlich der Kommunikation mit Mitarbeitern und Kunden außerhalb des Netzes (unter der Annahme, dass keine E-Mails und Chats verfügbar sind). |
| Ziehen Sie die Erstellung eines Risikoregisters in Erwägung, um potenzielle Risiken zu ermitteln und zu klären, wie Sie durch vorbeugende Kontrollen und Maßnahmen Abhilfe schaffen wollen. Fügen Sie Ransomware zum Risikoregister hinzu als ein Szenario mit hoher Wahrscheinlichkeit und hoher Auswirkung. | Ein Risikoregister kann Ihnen dabei helfen, Risiken auf der Grundlage der Wahrscheinlichkeit ihres Eintretens und der Schwere der Auswirkungen auf Ihr Unternehmen im Falle ihres Vorkommens zu priorisieren. <br><br>Verfolgen Sie den Status der Risikominderung über den Auswertungszyklus des [Enterprise Risikomanagement (ERM)](/compliance/assurance/assurance-risk-management). |
| Sichern Sie alle kritischen Geschäftssysteme automatisch in regelmäßigen Abständen (einschließlich der Sicherung kritischer Abhängigkeiten wie Active Directory). <br><br>Vergewissern Sie sich, dass Ihre Sicherung gut ist, wenn Die Sicherung erstellt wird. | Ermöglicht Ihnen die Wiederherstellung der Daten bis zur letzten Sicherung. |
| Schützen (oder drucken) Sie unterstützende Dokumente und Systeme, die für die Wiederherstellung erforderlich sind, wie z. B. Dokumente zum Wiederherstellungsverfahren, CMDB, Netzwerkdiagramme und SolarWinds-Instanzen. | Diese Ressourcen werden von Angreifern gezielt ins Visier genommen, weil für Sie bei einer Kompromittierung die Wiederherstellung erschwert wird. |
| Stellen Sie sicher, dass Sie über gut dokumentierte Prozeduren verfügen, mit deren Hilfe Sie Unterstützung von Drittanbietern erhalten, insbesondere Unterstützung von Threat Intelligence-Anbietern, Antischadsoftware-Lösungsanbietern und vom Anbieter für die Schadsoftwareanalyse. Schützen (oder drucken) Sie diese Prozeduren. | Drittanbieterkontakte können nützlich sein, wenn die jeweilige Ransomware-Variante bekannte Schwachstellen aufweist oder Entschlüsselungstools verfügbar sind. |
| Stellen Sie sicher, dass die Sicherungs- und Wiederherstellungsstrategie Folgendes umfasst: <br><br>Die Möglichkeit, Daten zu einem bestimmten Zeitpunkt zu sichern. <br><br>Mehrere Kopien der Sicherungen werden an isolierten, offline (air-gapped) Standorten gespeichert. <br><br>Zielvorgaben für die Wiederherstellungszeit, die festlegen, wie schnell die gesicherten Daten abgerufen und in die Produktionsumgebung eingespielt werden können. <br><br>Schnelle Wiederherstellung von Sicherungskopien in einer Produktionsumgebung/Sandbox. | Sicherungen sind für die Widerstandsfähigkeit nach einem Einbruch in ein Unternehmen unerlässlich. Wenden Sie die 3-2-1-Regel für maximalen Schutz und maximale Verfügbarkeit an: 3 Kopien (Original + 2 Backups), 2 Speichertypen und 1 Offsite- oder Kaltkopie. |
| Schützen von Sicherungen vor gezielter Löschung und Verschlüsselung: <br><br>Speichern Sie Sicherungskopien offline oder außerhalb des Standorts und/oder in unveränderlichen Speichern. <br><br>Verlangen Sie Außerbandschritte (z. B. [MFA](../../active-directory/authentication/concept-mfa-howitworks.md) oder eine Sicherheits-PIN), bevor Sie erlauben, dass eine Online-Sicherungskopie geändert oder gelöscht wird. <br><br>Erstellen Sie private Endpunkte innerhalb Ihres virtuellen Azure-Netzwerks, um Daten aus Ihrem Recovery Services-Datenspeicher geschützt zu sichern und wiederherzustellen. | Sicherungen, auf die von Angreifern zugegriffen werden kann, können für die Wiederherstellung des Geschäftsbetriebs als unbrauchbar gekennzeichnet werden. <br><br>Die Offline-Speicherung gewährleistet eine robuste Übertragung von Sicherungsdaten, ohne die Netzwerkbandbreite zu beanspruchen. Azure Backup unterstützt die [Offline-Sicherung](../../backup/offline-backup-overview.md), bei der die anfänglichen Sicherungsdaten offline und ohne Nutzung der Netzwerkbandbreite übertragen werden. Es stellt einen Mechanismus zum Kopieren von Sicherungsdaten auf physische Speichergeräte bereit. Die Geräte werden dann zu einem nahegelegenen Azure-Rechenzentrum transportiert und in einen [Recovery Services-Tresor](../../backup/backup-azure-recovery-services-vault-overview.md) hochgeladen. <br><br>Mit unveränderlichem Online-Speicher (wie [Azure Blob](../../storage/blobs/immutable-storage-overview.md)) können Sie geschäftskritische Datenobjekte in einem WORM-Zustand (Write Once, Read Many) speichern. In diesem Zustand sind die Daten für einen vom Benutzer angegebenen Zeitraum nicht löschbar und nicht änderbar. <br><br>Die [mehrstufige Authentifizierung (Multi-Factor Authentication, MFA)](../../active-directory/authentication/concept-mfa-howitworks.md) sollte für alle Administratorkonten obligatorisch sein und wird für alle Nutzer dringend empfohlen. Die bevorzugte Methode ist die Verwendung einer Authentifizierungs-App anstelle von SMS oder Sprache, wenn möglich. Wenn Sie Azure Backup einrichten, können Sie Ihre Wiederherstellungsdienste so konfigurieren, dass MFA mit einer im Azure-Portal generierten Sicherheits-PIN aktiviert wird. Dadurch wird sichergestellt, dass eine Sicherheits-PIN generiert wird, um kritische Vorgänge wie das Aktualisieren oder Entfernen eines Wiederherstellungspunkts durchzuführen. |
| Benennen Sie [geschützte Ordner](/windows/security/threat-protection/microsoft-defender-atp/controlled-folders). | Mit dieser Maßnahme wird es für nicht autorisierte Anwendungen erschwert, die Daten in diesen Ordnern zu ändern. |
| Überprüfen Ihrer Berechtigungen: <br><br>Entdecken Sie umfassende Schreib-/Löschberechtigungen für Dateifreigaben, SharePoint und andere Lösungen. Mit „allgemein“ ist hier gemeint, dass viele Benutzer über Schreib-/Löschberechtigungen für unternehmenskritische Daten verfügen. <br><br>Reduzieren Sie die Zahl der Berechtigungen bei gleichzeitiger Erfüllung der Anforderungen an die geschäftliche Zusammenarbeit. <br><br>Überprüfen und überwachen Sie, um sicherzustellen, dass weit verbreitete Berechtigungen nicht wieder auftauchen. | Verringert das Risiko durch umfassende Ransomware-Aktivitäten zur Gewährung von Zugriff. |
| Schützen Sie sich vor einem Phishing-Versuch: <br><br>Führen Sie regelmäßig Schulungen zum Sicherheitsbewusstsein durch, damit die Benutzer einen Phishing-Versuch erkennen und nicht auf etwas klicken, das einen ersten Einstiegspunkt für eine Kompromittierung darstellen kann. <br><br>Wenden Sie Sicherheitsfilterkontrollen auf E-Mails an, um die Wahrscheinlichkeit eines erfolgreichen Phishing-Versuchs zu erkennen und zu minimieren. | Die häufigste Methode von Angreifern, ein Unternehmen zu infiltrieren, sind Phishing-Versuche per E-Mail. [Exchange Online Protection (EOP)](/microsoft-365/security/office-365-security/exchange-online-protection-overview) ist der cloudbasierte Filterdienst, der Ihr Unternehmen vor Spam, Schadsoftware und anderen E-Mail-Bedrohungen schützt. EOP ist in allen Microsoft 365-Organisationen mit Exchange Online-Postfächern enthalten. <br><br>Ein Beispiel für eine Sicherheitsfilterkontrolle für E-Mails ist [Safe Links](/microsoft-365/security/office-365-security/safe-links). Safe Links ist eine Funktion in Defender für Office 365, die URL-Scans und das Umschreiben von eingehenden E-Mail-Nachrichten im Mailflow sowie die Überprüfung von URLs und Links in E-Mail-Nachrichten und an anderen Orten zum Zeitpunkt des Klicks ermöglicht. Die Überprüfung von Safe Links erfolgt zusätzlich zum regulären Spam- und Schadsoftwareschutz in eingehenden E-Mail-Nachrichten in EOP. Die Überprüfung sicherer Links kann Ihr Unternehmen vor bösartigen Links schützen, die für Phishing- und andere Angriffe verwendet werden. <br><br>Erfahren Sie mehr über den [Anti-Phishing-Schutz](/microsoft-365/security/office-365-security/tuning-anti-phishing). |

## <a name="what-to-do-during-an-attack"></a>Was bei einem Angriff tun ist

Wenn Sie angegriffen werden, wird Ihre priorisierte Sicherungs-Liste zu Ihrer priorisierten Wiederherstellungsliste. Vergewissern Sie sich vor der Wiederherstellung noch einmal, dass Ihre Sicherung in Ordnung ist. Möglicherweise können Sie in der Sicherung nach Schadsoftware suchen.

### <a name="steps-to-take-during-an-attack"></a>Schritte, die während eines Angriffs zu tun sind

Wenden Sie diese bewährten Methoden während einem Angriff an.

| Aufgabe | Detail |
| --- | --- |
| Holen Sie sich bereits in der Anfangsphase des Angriffs Unterstützung von Dritten, insbesondere von Threat-Intelligence-Anbietern, Anbietern von Antischadsoftware Lösungen und von Anbietern von Schadsoftware Analysen. | Diese Kontakte können nützlich sein, wenn die jeweilige Ransomware-Variante eine bekannte Schwachstelle aufweist oder Entschlüsselungstools verfügbar sind. <br><br>Das [Microsoft Detection and Response Team (DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) kann Sie vor Angriffen schützen. Das DART arbeitet mit Kunden auf der ganzen Welt zusammen und hilft ihnen, sich vor Angriffen zu schützen und diese abzuwehren, bevor sie auftreten, und untersucht und behebt sie, wenn ein Angriff erfolgt ist. <br><br>Microsoft bietet auch Rapid Ransomware Recovery Services an. Die Dienste werden ausschließlich von der Microsoft Global [Compromise Recovery Security Practice (CRSP)](https://www.microsoft.com/security/blog/2021/06/09/crsp-the-emergency-team-fighting-cyber-attacks-beside-customers/) erbracht. Der Schwerpunkt dieses Teams liegt bei einem Ransomware-Angriff auf der Wiederherstellung des Authentifizierungsdienstes und der Begrenzung der Auswirkungen der Ransomware. <br><br>DART und CRSP sind Teil der Sicherheitsdienstlinie [Industry Solutions Delivery](https://www.microsoft.com/en-us/msservices/security) von Microsoft. |
| Wenden Sie sich an Ihre örtlichen oder bundesstaatlichen Strafverfolgungsbehörden. | Wenn Sie sich in den USA befinden, wenden Sie sich an das FBI, um eine Ransomware-Verletzung mithilfe des [IC3 Complaint Referral Form](https://ransomware.ic3.gov/default.aspx) zu melden. |
| Ergreifen Sie Maßnahmen, um Schadsoftware oder Ransomware aus Ihrer Umgebung zu entfernen und die Verbreitung zu stoppen. <br><br>Führen Sie auf allen verdächtigen Computern und Geräten einen vollständigen, aktuellen Antiviren-Scan durch, um die mit der Ransomware verbundene Nutzlast zu erkennen und zu entfernen. <br><br>Scannen Sie Geräte, die Daten synchronisieren, oder die Ziele von zugeordneten Netzlaufwerken. | Sie können [Windows Defender](https://www.microsoft.com/windows/comprehensive-security) oder (für ältere Clients) [Microsoft Security Essentials](https://www.microsoft.com/download/details.aspx?id=5201) verwenden. <br><br>Eine Alternative, die Ihnen ebenfalls hilft, Ransomware oder Malware zu entfernen, ist das [Tool zum Entfernen bösartiger Software (MSRT)](https://www.microsoft.com/download/details.aspx?id=9905). |
| Stellen Sie zuerst unternehmenskritische Systeme wieder her. Vergewissern Sie sich vor der Wiederherstellung noch einmal, dass Ihre Sicherung in Ordnung ist.| An diesem Punkt müssen Sie nicht alles wiederherstellen. Konzentrieren Sie sich auf die fünf wichtigsten unternehmenskritischen Systeme aus Ihrer Wiederherstellungsliste. |
| Wenn Sie über Offlinesicherungen verfügen,können Sie die verschlüsselten Daten wahrscheinlich wiederherstellen, **nachdem** Sie die Ransomware-Nutzlast (Schadsoftware) aus Ihrer Umgebung entfernt haben. | Stellen Sie vor der Wiederherstellung sicher, dass sich keine Ransomware oder Schadsoftware in Ihrer Offlinesicherung befindet, um zukünftige Angriffe zu verhindern. |
| Identifizieren Sie ein sicheres Point-in-Time-Sicherungsimage, von dem bekannt ist, dass es nicht infiziert ist. <br><br>Wenn Sie den Recovery Services-Tresor verwenden, überprüfen Sie sorgfältig die Vorfallszeitachse, um den richtigen Zeitpunkt für die Wiederherstellung einer Sicherung zu verstehen. | Um zukünftige Angriffe zu verhindern, überprüfen Sie die Sicherung vor der Wiederherstellung auf Ransomware oder Schadsoftware. |
| Verwenden Sie eine Sicherheitsüberprüfungsanwendung und andere Tools für die vollständige Wiederherstellung des Betriebssystems sowie für Datenwiederherstellungsszenarien. | [Microsoft Safety Scanner](/windows/security/threat-protection/intelligence/safety-scanner-download) ist ein Überprüfungstool zum Suchen und Entfernen von Schadsoftware von Windows Computern. Laden Sie es einfach herunter, und lassen Sie eine Überprüfung laufen, um Schadsoftware zu finden, und versuchen Sie, Änderungen rückgängig zu machen, die durch identifizierte Bedrohungen vorgenommen wurden. |
| Stellen Sie sicher, dass Ihre Antiviren- EDR(EDR)-Lösung auf dem neuesten Stand ist. Außerdem benötigen Sie aktuelle Patches. | Eine EDR-Lösung, wie z. B.[Microsoft Defender für Endpunkt](/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint), wird bevorzugt. |
| Sobald die geschäftskritischen Systeme wieder in Betrieb sind, stellen Sie andere Systeme wieder her. <br><br>Beginnen Sie mit der Wiederherstellung der Systeme und sammeln Sie Telemetriedaten, damit Sie fundierte Entscheidungen über die wiederhergestellten Systeme treffen können. | Anhand der Telemetriedaten sollten Sie feststellen können, ob sich noch Malware auf Ihren Systemen befindet. |

## <a name="post-attack-or-simulation"></a>Nach dem Angriff oder der Simulation

Führen Sie nach einem Ransomware-Angriff oder einer Simulation eines Zwischenfalls die folgenden Schritte durch, um Ihre Sicherungs- und Wiederherstellungspläne sowie Ihre Sicherheitslage zu verbessern:

1. Identifizieren Sie die Lektionen, die Sie gelernt haben, wenn der Prozess nicht gut funktioniert hat (und Möglichkeiten zur Vereinfachung, Beschleunigung oder anderweitigen Verbesserung des Prozesses)
2. Führen Sie eine Ursachenanalyse für die größten Herausforderungen durch (ausreichend detailliert, um sicherzustellen, dass die Lösungen das richtige Problem angehen – unter Berücksichtigung von Menschen, Prozessen und Technologie)
3. Untersuchen und beheben Sie den ursprünglichen Angriff (beauftragen Sie das [Microsoft Detection and Response Team (DART)](https://www.microsoft.com/security/blog/2019/03/25/dart-the-microsoft-cybersecurity-team-we-hope-you-never-meet/) zur Hilfe)
4. Aktualisieren Sie Ihre Sicherungs- und Wiederherstellungsstrategie basierend auf den gewonnenen Erkenntnissen und Möglichkeiten – priorisieren Sie dabei zuerst basierend auf den höchsten Auswirkungen und den schnellsten Implementierungsschritten

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Ihren Sicherungs- und Wiederherstellungsplan zum Schutz vor Ransomware verbessern. Bewährte Methoden für die Bereitstellung von Ransomware-Schutz finden Sie unter [Schnelles Schützen vor Ransomware und Erpressung](/security/compass/protect-against-ransomware).

Wichtige Brancheninformationen:

- [Microsoft Digital Defense Bericht](https://www.microsoft.com/security/business/security-intelligence-report?rtc=1) (siehe Seiten 22-24)

Microsoft Azure:

- [Schutz vor Ransomware mit Microsoft Azure Backup](https://www.youtube.com/watch?v=VhLOr2_1MCg) (26-minütiges Video)
- [Wiederherstellen nach kompromittierter Systemidentität](./recover-from-identity-compromise.md)
- [Erweiterte mehrstufige Angriffserkennung in Microsoft Sentinel](../../sentinel/fusion.md#fusion-for-ransomware)

Microsoft 365:

- [Wiederherstellen nach einem Ransomware-Angriff in Microsoft Office 365](/microsoft-365/security/office-365-security/recover-from-ransomware)
- [Schutz vor Schadsoftware und Ransomware in Microsoft 365](/compliance/assurance/assurance-malware-and-ransomware-protection)
- [Schützen Ihres Windows 10-PCs vor Ransomware](https://support.microsoft.com/windows/protect-your-pc-from-ransomware-08ed68a7-939f-726c-7e84-a72ba92c01c3)
- [Behandeln von Ransomware in SharePoint Online](/sharepoint/troubleshoot/security/handling-ransomware-in-sharepoint-online)

Microsoft 365 Defender:

- [Suchen nach Ransomware mit erweitertem Hunting](/microsoft-365/security/defender/advanced-hunting-find-ransomware)

Blogbeiträge des Microsoft-Sicherheitsteams:

- [Resilient werden durch Verstehen von Cybersicherheitsrisiken: Teil 4 – Umgang mit aktuellen Bedrohungen (Mai 2021)](https://www.microsoft.com/security/blog/2021/05/26/becoming-resilient-by-understanding-cybersecurity-risks-part-4-navigating-current-threats/). Weitere Informationen finden Sie im Abschnitt Ransomware
- [Von Menschen durchgeführte Ransomware-Angriffe: Eine vermeidbare Katastrophe (März 2020)](https://www.microsoft.com/security/blog/2020/03/05/human-operated-ransomware-attacks-a-preventable-disaster/). Enthält eine Analyse der Angriffskette tatsächlicher, von Menschen durchgeführter Ransomware-Angriffe
- [Reaktion auf Ransomware: Bezahlen oder nicht bezahlen? (Dezember 2019)](https://www.microsoft.com/security/blog/2019/12/16/ransomware-response-to-pay-or-not-to-pay/)
- [Transparente Reaktion auf Ransomware-Angriffe von Norsk Soll (Dezember 2019)](https://www.microsoft.com/security/blog/2019/12/17/norsk-hydro-ransomware-attack-transparency/)
