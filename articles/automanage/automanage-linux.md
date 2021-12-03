---
title: Azure Automanage für Linux
description: Erfahren Sie mehr über bewährte Methoden für Azure Automanage für virtuelle Computer für Dienste, für die automatisch das Onboarding und die Konfiguration für Linux-Computer durchgeführt wird.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 5e0dea425d309e6a92036214dfe43a5c779c586c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327574"
---
# <a name="azure-automanage-for-machines-best-practices---linux"></a>Bewährte Methoden für Azure Automanage für Computer: Linux

Diese Azure-Dienste werden automatisch für Sie integriert, wenn Sie Profile bewährter Methoden für Automanage für Computer auf einer Linux-VM verwenden. Sie sind für unser Whitepaper über bewährte Methoden unerlässlich, das Sie in unserem [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management) finden.

Bei all diesen Diensten werden wir das Onboarding automatisch durchführen, sie automatisch konfigurieren, auf Datendrift überwachen und korrigieren, wenn ein Datendrift festgestellt wird. Weitere Informationen finden Sie unter [Azure Automanage für virtuelle Computer](automanage-virtual-machines.md).

## <a name="supported-linux-distributions-and-versions"></a>Unterstützte Linux-Distributionen und -Versionen

Automanage unterstützt die folgenden Linux-Distributionen und -Versionen:

- CentOS 7.3+, 8
- RHEL 7.4+, 8
- Ubuntu 16.04 und 18.04
- SLES 12 (nur SP3–SP5)

## <a name="participating-services"></a>Beteiligte Dienste

>[!NOTE]
> Microsoft Antimalware wird auf Linux-Computern zurzeit nicht unterstützt.

|Dienst    |BESCHREIBUNG    |Unterstütztes Konfigurationsprofil<sup>1</sup>|
|-----------|---------------|----------------------|
|[Überwachung von Computererkenntnissen](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor für Computer überwacht die Leistung und Integrität Ihrer virtuellen Computer, einschließlich ihrer ausgeführten Prozesse und Abhängigkeiten von anderen Ressourcen. [Weitere Informationen](../azure-monitor/vm/vminsights-overview.md).    |Bereitstellung    |
|[Backup](../backup/backup-overview.md)   |Azure Backup bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen der Daten auf Ihren VMs. [Weitere Informationen](../backup/backup-azure-vms-introduction.md). Die Gebühren basieren auf der Anzahl und Größe der VMs, die geschützt werden. [Weitere Informationen](https://azure.microsoft.com/pricing/details/backup/).    |Bereitstellung    |
|[Microsoft Defender für Cloud](../security-center/security-center-introduction.md)    |Microsoft Defender für Cloud ist ein vereinheitlichtes Sicherheitsverwaltungssystem für Infrastrukturen, mit dem der Sicherheitsstatus Ihrer Rechenzentren gestärkt wird und ein erweiterter Schutz vor Bedrohungen für Ihre Hybridworkloads in der Cloud bereitgestellt wird. [Weitere Informationen](../security-center/security-center-introduction.md).  Automanage konfiguriert das Abonnement, in dem sich Ihr virtueller Computer befindet, für das Free-Tarifangebot von Microsoft Defender für Cloud. Wenn Ihr Abonnement bereits mit Microsoft Defender für Cloud verbunden ist, wird es von Automanage nicht neu konfiguriert.    |Produktion, Dev/Test    |
|[Updateverwaltung](../automation/update-management/overview.md)    |Sie können die Updateverwaltung in Azure Automation verwenden, um Betriebssystemupdates für Ihre Computer zu verwalten. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren. [Weitere Informationen](../automation/update-management/overview.md).    |Produktion, Dev/Test    |
|[Änderungsnachverfolgung und Bestand](../automation/change-tracking/overview.md) |„Änderungsnachverfolgung und Bestand“ kombiniert Änderungsnachverfolgungs- und Bestandsfunktionen, mit denen Sie Änderungen an virtuellen Computern und Serverinfrastrukturen nachverfolgen können. Da der Dienst auch eine Änderungsnachverfolgung für Dienste, Daemons, Software, Registrierung und Dateien in Ihrer Umgebung unterstützt, können Sie unerwünschte Änderungen ermitteln und entsprechende Warnungen auslösen. Durch die Unterstützung einer Bestandserfassung ist es zudem möglich, Ressourcen auf Gastsystemen abzurufen, um sich über die installierten Anwendungen und andere Konfigurationselemente zu informieren.  [Weitere Informationen](../automation/change-tracking/overview.md).    |Produktion, Dev/Test    |
|[Gastkonfiguration](../governance/policy/concepts/guest-configuration.md)  | Die Gastkonfiguration dient zur Überwachung der Konfiguration sowie zur Generierung von Berichten zur Konformität des Computers. Der Automanage-Dienst installiert die Azure Linux-Baseline mithilfe der Gastkonfigurationserweiterung. Bei Linux-Computern installiert der Gastkonfigurationsdienst die Baseline im Modus „Nur überwachen“. Sie können erkennen, wo Ihre VM nicht mit der Baseline konform ist, aber die Nichtkonformität wird nicht automatisch korrigiert. [Weitere Informationen](../governance/policy/concepts/guest-configuration.md).    |Produktion, Dev/Test    |
|[Startdiagnose](../virtual-machines/boot-diagnostics.md)  | Die Startdiagnose ist ein Debuggingfeature für Azure-VMs, die eine Diagnose von Fehlern beim Starten von VMs ermöglicht. Mit der Startdiagnose können Benutzer anhand von Informationen des seriellen Protokolls und Screenshots den Zustand ihrer VMs beim Systemstart beobachten. Dies wird nur für Computer aktiviert, die verwaltete Datenträger verwenden. |Produktion, Dev/Test    |
|[Azure Automation-Konto](../automation/automation-create-standalone-account.md)    |Azure Automation unterstützt die Verwaltung des gesamten Lebenszyklus Ihrer Infrastruktur und Anwendungen. [Weitere Informationen](../automation/automation-intro.md).    |Produktion, Dev/Test    |
|[Log Analytics-Arbeitsbereich](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor speichert Protokolldaten in einem Log Analytics-Arbeitsbereich, bei dem es sich um eine Azure-Ressource und einen Container handelt, in dem Daten gesammelt und aggregiert werden und der als eine administrative Grenze dient. [Weitere Informationen](../azure-monitor/logs/design-logs-deployment.md).    |Produktion, Dev/Test    |


<sup>1</sup> Die Auswahl des Konfigurationsprofils ist verfügbar, wenn Sie Automanage aktivieren. [Weitere Informationen](automanage-virtual-machines.md#configuration-profile). Sie können auch Ihr eigenes benutzerdefiniertes Profil mit den von Ihnen benötigten Azure-Diensten und -Einstellungen erstellen.


## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, Automanage für Computer im Azure-Portal zu aktivieren.

> [!div class="nextstepaction"]
> [Aktivieren von Automanage für Computer im Azure-Portal](quick-create-virtual-machines-portal.md)
