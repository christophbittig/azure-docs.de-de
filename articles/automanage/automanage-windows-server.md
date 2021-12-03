---
title: Azure Automanage für Windows Server
description: Erfahren Sie mehr über die bewährten Methoden für Azure Automanage für virtuelle Computer für Dienste, für die automatisch das Onboarding und die Konfiguration für Windows Server-Computer durchgeführt wird.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 72ef8193215d3dc35f576a96a005bde8bf40e49f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371940"
---
# <a name="azure-automanage-for-machines-best-practices---windows-server"></a>Bewährte Methoden für Azure Automanage für Computer: Windows Server

Diese Azure-Dienste werden automatisch für Sie integriert, wenn Sie die bewährten Methoden für Automanage für Computer auf einem virtuellen Windows Server-Compute verwenden. Sie sind für unser Whitepaper über bewährte Methoden unerlässlich, das Sie in unserem [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management) finden.

Bei all diesen Diensten werden wir das Onboarding automatisch durchführen, sie automatisch konfigurieren, auf Datendrift überwachen und korrigieren, wenn ein Datendrift festgestellt wird. Weitere Informationen finden Sie unter [Azure Automanage für virtuelle Computer](automanage-virtual-machines.md).

## <a name="supported-windows-server-versions"></a>Unterstützte Windows Server-Versionen

Automanage unterstützt die folgenden Windows Server-Versionen:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2022
- Windows Server 2022 Azure Edition

## <a name="participating-services"></a>Beteiligte Dienste

|Dienst    |BESCHREIBUNG    |Konfigurationsprofil<sup>1</sup>    |
|-----------|---------------|----------------------|
|[Überwachung von Computererkenntnissen](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor für Computer überwacht die Leistung und Integrität Ihrer virtuellen Computer, einschließlich ihrer ausgeführten Prozesse und Abhängigkeiten von anderen Ressourcen.    |Bereitstellung    |
|[Backup](../backup/backup-overview.md)    |Azure Backup stellt unabhängige und isolierte Sicherungen bereit, um Schutz vor einer unbeabsichtigten Löschung von Daten auf Ihren Computern zu bieten. Die Gebühren basieren auf der Anzahl und Größe der VMs, die geschützt werden.    |Bereitstellung    |
|[Microsoft Defender für Cloud](../defender-for-cloud/defender-for-cloud-introduction.md)    |Microsoft Defender für Cloud ist ein einheitliches Sicherheitsverwaltungssystem für die Infrastruktur, das den Sicherheitsstatus Ihrer Rechenzentren stärken und erweiterten Bedrohungsschutz für Ihre Hybridworkloads in der Cloud bietet.  Die automatische Verwaltung konfiguriert das Abonnement, in dem sich Ihr virtueller Computer befindet, für das Free-Tier-Angebot von Microsoft Defender für Cloud. Wenn Ihr Abonnement bereits mit Microsoft Defender für Cloud verbunden ist, wird es von Automanage nicht neu konfiguriert.    |Produktion, Dev/Test    |
|[Microsoft Antimalware](../security/fundamentals/antimalware.md)    |Microsoft Antimalware für Azure ist eine kostenlose Echtzeit-Schutzfunktion zum Erkennen und Entfernen von Viren, Spyware und anderer Schadsoftware. Das Tool generiert Warnungen, wenn bekannte schädliche oder unerwünschte Software versucht, sich selbst auf Ihren Azure-Systemen zu installieren oder dort auszuführen. **Hinweis**: Microsoft Antimalware setzt für eine ordnungsgemäße Funktion voraus, dass keine andere Antimalware-Software installiert ist. |Produktion, Dev/Test    |
|[Updateverwaltung](../automation/update-management/overview.md)    |Sie können die Updateverwaltung in Azure Automation verwenden, um Betriebssystemupdates für Ihre Computer zu verwalten. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren.    |Produktion, Dev/Test    |
|[Änderungsnachverfolgung und Bestand](../automation/change-tracking/overview.md) |„Änderungsnachverfolgung und Bestand“ kombiniert Änderungsnachverfolgungs- und Bestandsfunktionen, mit denen Sie Änderungen an virtuellen Computern und Serverinfrastrukturen nachverfolgen können. Da der Dienst auch eine Änderungsnachverfolgung für Dienste, Daemons, Software, Registrierung und Dateien in Ihrer Umgebung unterstützt, können Sie unerwünschte Änderungen ermitteln und entsprechende Warnungen auslösen. Durch die Unterstützung einer Bestandserfassung ist es zudem möglich, Ressourcen auf Gastsystemen abzurufen, um sich über die installierten Anwendungen und andere Konfigurationselemente zu informieren.    |Produktion, Dev/Test    |
|[Gastkonfiguration](../governance/policy/concepts/guest-configuration.md) | Die Gastkonfigurationsrichtlinie dient zur Überwachung der Konfiguration sowie zur Generierung von Berichten zur Konformität des Computers. Der Automanage-Dienst installiert die [Windows-Sicherheitsbaselines](/windows/security/threat-protection/windows-security-baselines) mithilfe der Gastkonfigurationserweiterung. Bei Windows-Computern wendet der Gastkonfigurationsdienst die Baselineeinstellungen automatisch erneut an, wenn sie nicht mehr konform sind.    |Produktion, Dev/Test    |
|[Startdiagnose](../virtual-machines/boot-diagnostics.md)    | Die Startdiagnose ist ein Debuggingfeature für Azure-VMs, die eine Diagnose von Fehlern beim Starten von VMs ermöglicht. Mit der Startdiagnose können Benutzer anhand von Informationen des seriellen Protokolls und Screenshots den Zustand ihrer VMs beim Systemstart beobachten. Dies wird nur für Computer aktiviert, die verwaltete Datenträger verwenden. |Produktion, Dev/Test    |
|[Windows Admin Center](/windows-server/manage/windows-admin-center/azure/manage-vm)    | Verwenden Sie Windows Admin Center (Vorschau) im Azure-Portal, um das Betriebssystem Windows Server auf einem virtuellen Azure-Computer zu verwalten. Dies wird nur für Computer unterstützt, die Windows Server 2016 oder höher verwenden. Automanage konfiguriert Windows Admin Center über eine private IP-Adresse. Wenn Sie über eine öffentliche IP-Adresse eine Verbindung mit Windows Admin Center herstellen möchten, öffnen Sie eine Eingangsportregel für Port 6516. Automanage führt standardmäßig ein Onboarding von Windows Admin Center für das Dev/Test-Profil durch. Verwenden Sie die Einstellungen, um Windows Admin Center für die Produktions- und Dev/Testumgebungen zu aktivieren oder zu deaktivieren. |Produktion, Dev/Test    |
|[Azure Automation-Konto](../automation/automation-create-standalone-account.md)    |Azure Automation unterstützt die Verwaltung des gesamten Lebenszyklus Ihrer Infrastruktur und Anwendungen.    |Produktion, Dev/Test    |
|[Log Analytics-Arbeitsbereich](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor speichert Protokolldaten in einem Log Analytics-Arbeitsbereich, bei dem es sich um eine Azure-Ressource und einen Container handelt, in dem Daten gesammelt und aggregiert werden und der als eine administrative Grenze dient.    |Produktion, Dev/Test    |


<sup>1</sup> Die Auswahl des [Konfigurationsprofils](automanage-virtual-machines.md#configuration-profile) ist verfügbar, wenn Sie Automanage aktivieren. Sie können auch Ihr eigenes benutzerdefiniertes Profil mit den von Ihnen benötigten Azure-Diensten und -Einstellungen erstellen.


## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, Automanage für Computer im Azure-Portal zu aktivieren.

> [!div class="nextstepaction"]
> [Aktivieren von Automanage für Computer im Azure-Portal](quick-create-virtual-machines-portal.md)
