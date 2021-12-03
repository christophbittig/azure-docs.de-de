---
title: Azure Automanage für Server mit Azure Arc-Unterstützung
description: Erfahren Sie mehr über Azure Automanage für Server mit Azure Arc-Unterstützung
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 06/24/2021
ms.openlocfilehash: 160cbb8873d962b8fe439e798ace08d2e434c13c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310536"
---
# <a name="azure-automanage-for-machines-best-practices---azure-arc-enabled-servers"></a>Bewährte Methoden für Azure Automanage für Computer – Server mit Azure Arc-Unterstützung

Das Onboarding dieser Azure-Dienste wird automatisch für Sie ausgeführt, wenn Sie die bewährten Methoden für Automanage für Computer auf einem Server mit Azure Arc-Unterstützung verwenden. Sie sind für unser Whitepaper über bewährte Methoden unerlässlich, das Sie in unserem [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management) finden.

Bei all diesen Diensten werden wir das Onboarding automatisch durchführen, sie automatisch konfigurieren, auf Datendrift überwachen und korrigieren, wenn ein Datendrift festgestellt wird. Weitere Informationen finden Sie unter [Azure Automanage für virtuelle Computer](automanage-virtual-machines.md).

## <a name="supported-operating-systems"></a>Unterstützte Betriebssysteme

Automanage unterstützt die folgenden Betriebssysteme für Server mit Azure Arc-Unterstützung

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- CentOS 7.3+, 8
- RHEL 7.4+, 8
- Ubuntu 16.04 und 18.04
- SLES 12 (nur SP3–SP5)

## <a name="participating-services"></a>Beteiligte Dienste

|Dienst    |BESCHREIBUNG    |Konfigurationsprofil<sup>1</sup>    |
|-----------|---------------|----------------------|
|[Überwachung von Computererkenntnissen](../azure-monitor/vm/vminsights-overview.md)    |Azure Monitor für Computer überwacht die Leistung und Integrität Ihrer virtuellen Computer, einschließlich ihrer ausgeführten Prozesse und Abhängigkeiten von anderen Ressourcen.    |Bereitstellung    |
|[Updateverwaltung](../automation/update-management/overview.md)    |Sie können die Updateverwaltung in Azure Automation verwenden, um Betriebssystemupdates für Ihre Computer zu verwalten. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren.    |Produktion, Dev/Test    |
|[Änderungsnachverfolgung und Bestand](../automation/change-tracking/overview.md) |„Änderungsnachverfolgung und Bestand“ kombiniert Änderungsnachverfolgungs- und Bestandsfunktionen, mit denen Sie Änderungen an virtuellen Computern und Serverinfrastrukturen nachverfolgen können. Da der Dienst auch eine Änderungsnachverfolgung für Dienste, Daemons, Software, Registrierung und Dateien in Ihrer Umgebung unterstützt, können Sie unerwünschte Änderungen ermitteln und entsprechende Warnungen auslösen. Durch die Unterstützung einer Bestandserfassung ist es zudem möglich, Ressourcen auf Gastsystemen abzurufen, um sich über die installierten Anwendungen und andere Konfigurationselemente zu informieren.    |Produktion, Dev/Test    |
|[Azure-Gastkonfiguration](../governance/policy/concepts/guest-configuration.md)  | Die Gastkonfigurationsrichtlinie dient zur Überwachung der Konfiguration sowie zur Generierung von Berichten zur Konformität des Computers. Der Automanage-Dienst installiert die Azure Linux-Baseline mithilfe der Gastkonfigurationserweiterung. Bei Linux-Computern installiert der Gastkonfigurationsdienst die Baseline im Modus „Nur überwachen“. Sie können erkennen, wo Ihre VM nicht mit der Baseline konform ist, aber die Nichtkonformität wird nicht automatisch korrigiert.    |Produktion, Dev/Test    |
|[Azure Automation-Konto](../automation/automation-create-standalone-account.md)    |Azure Automation unterstützt die Verwaltung des gesamten Lebenszyklus Ihrer Infrastruktur und Anwendungen.    |Produktion, Dev/Test    |
|[Log Analytics-Arbeitsbereich](../azure-monitor/logs/log-analytics-overview.md) |Azure Monitor speichert Protokolldaten in einem Log Analytics-Arbeitsbereich, bei dem es sich um eine Azure-Ressource und einen Container handelt, in dem Daten gesammelt und aggregiert werden und der als eine administrative Grenze dient.    |Produktion, Dev/Test    |


<sup>1</sup> Die Auswahl des [Konfigurationsprofils](automanage-virtual-machines.md#configuration-profile) ist verfügbar, wenn Sie Automanage aktivieren. Sie können auch Ihr eigenes benutzerdefiniertes Profil mit den von Ihnen benötigten Azure-Diensten und -Einstellungen erstellen.


## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, Automanage für Computer im Azure-Portal zu aktivieren.

> [!div class="nextstepaction"]
> [Aktivieren von Automanage für Computer im Azure-Portal](quick-create-virtual-machines-portal.md)
