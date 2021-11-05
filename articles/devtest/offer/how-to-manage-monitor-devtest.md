---
title: Verwalten und Überwachen von Azure Dev/Test-Abonnements
description: Verwalten Sie Ihre Azure Dev/Test-Abonnements mit der Flexibilität der Azure-Cloudumgebung. In diesem Leitfaden wird auch Azure Monitor beschrieben, um Ihnen dabei zu helfen, die Verfügbarkeit und Leistung von Anwendungen und Diensten zu erhöhen.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 22fa6c747d7c15cebe1d36228965c3119fea25ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095558"
---
# <a name="managing-azure-devtest-subscriptions"></a>Verwalten von Azure DevTest-Abonnements

Das Verwalten Ihrer Azure Dev/Test-Abonnements ist wichtig. Die Kostenverwaltung und die Überwachung der Produktionsphasen, bereitgestellten Ressourcen und konfigurierten Prozesse haben bei der Abonnementverwaltung höchste Priorität.  

Die Azure-Cloudumgebung bietet Ihnen Flexibilität beim Verwalten von Kosten und Workloads. Bei der lokalen Verwaltung hängt die Leistungsfähigkeit von Ihren Investitionen ab.  

Dienste innerhalb des Abonnements und der Ressourcengruppenebenen verursachen keine Kosten, nur die Ressourcen selbst sorgen für Kosten.  

![Abbildung von Azure-Organisationen und der Governance](media/how-to-manage-monitor-devtest/orgs-and-governance.png "Azure-Organisationen und Governance")

Bei der Verwaltung zahlen Sie, in Bezug auf die Betriebskosten, nur für das, was Sie tatsächlich nutzen. Es gibt mehrere Tools für Azure Dev/Test-Abonnements, mit denen Sie die Kosten während der Bereitstellung verwalten können.  

## <a name="monitoring-through-a-different-lens"></a>Überwachung aus einer anderen Perspektive

Mit [Azure Monitor](../../azure-monitor/overview.md) können Sie die Verfügbarkeit und Leistung Ihrer Anwendungen und Dienste maximieren. Die Dienstplattform bietet eine umfassende Lösung für das Sammeln, Analysieren und Verarbeiten von Daten aus der Cloud und lokalen Umgebungen. Sie sind immer über die Leistung Ihrer Anwendungen informiert. Azure Monitor erkennt proaktiv Probleme, die sich auf Ihre Anwendungen und die Ressourcen auswirken, von denen sie abhängig sind.  

Verwenden Sie die Überwachung in Azure, um die Markteinführungszeit zu verkürzen und Leistungsdaten in Ihren Produktionsdiensten sicherzustellen. Sie können Metriken, Protokolle und Ablaufverfolgungen aggregieren und analysieren. Durch die Überwachung können Sie auch Warnungen ausgeben, Benachrichtigungen senden oder automatisierte Lösungen aufrufen.  

Mit Azure Monitor können Sie Dev/Test-Vorteile zum Optimieren der Markteinführungszeit Ihrer Apps verwenden und diese dann bereitstellen.  

Mithilfe der Überwachung können Sie Ihre Dev/Test-Vorteile für neue und vorhandene Anwendungen nutzen.  

- Vor der Produktion mit neuen Greenfield-Anwendungen: Sie können mit neuen Apps benutzerdefinierte Metriken mit Protokollanalysen oder intelligente Warnungen in Vorproduktionsphasen erstellen und aktivieren, die Sie später in der Produktion verwenden. Durch die frühzeitige Überwachung werden Ihre Leistungsdaten für Ihre Produktionsdienste optimiert.  
- Nach der Produktion mit vorhandenen Anwendungen: Wenn Sie neue Features bereitstellen oder neue Funktionen mit einer API zu vorhandenen Apps hinzufügen, können Sie dieses Feature in der Vorproduktionsphase zur Verfügung stellen und die Überwachung optimieren, um frühzeitig korrekte Datenfeeds sicherzustellen. Die Verwendung der Nachverfolgung in der Vorproduktionsphase des neuen Features bietet Ihnen deutliche Erkenntnisse und ermöglicht es Ihnen, diese Überwachung nach der Produktion mit Ihrem gesamten Überwachungssystem zu kombinieren. Aktuelle Leistungsdaten werden in vorhandene Protokolle integriert, um sicherzustellen, dass die Überwachung verwendet wird.  
- Die Überwachung verschiedener Bereitstellungsphasen außerhalb der Produktion spiegelt die Überwachung während der Produktion wider. Sie verwalten Ihre Kosten und analysieren Ihre Ausgaben vor und nach der Produktion.  

## <a name="cost-management"></a>Cost Management

Mit [Azure Cost Management](../../cost-management-billing/cost-management-billing-overview.md) können Sie die technische Leistung Ihrer Geschäftsworkloads erheblich verbessern. Sie kann auch Ihre Kosten und den Mehraufwand senken, der zum Verwalten von Unternehmensressourcen erforderlich ist.  

Mithilfe der Überwachung können Sie Kostenanalysetools in der Vorproduktionsphase verwenden, bevor Sie Ihre Dienste bereitstellen, um eine Analyse der Compute in der Vorproduktion durchzuführen und somit Produktionskosten vorherzusagen und möglicherweise Geld zu sparen.  

## <a name="performance-management"></a>Leistungsverwaltung

Neben der Überwachung und Kostenverwaltung möchten Sie vor der Produktion auch Computeleistungstests für erwartete Workloads durchführen.  

In der Vorproduktionsphase können Sie die Skalierung so einrichten, dass sie basierend auf der Auslastung erweitert wird. Wenn Sie Ihre Anwendung in einer Dev/Test-Umgebung unter Last testen, erhalten Sie bessere Kostenanalyse- und Überwachungszahlen. Sie haben eine bessere Vorstellung davon, ob Sie mit einer höheren oder niedrigeren Skalierung beginnen müssen.  

Die Auslastungs- und Stressanalyse bietet weitere Daten in der Vorproduktionsphase, sodass Sie Ihre Markteinführungszeit und die Bereitstellung Ihrer Anwendung weiter optimieren können.  

Wenn Sie die Auslastungs- und Stresstests für Ihre Anwendung oder Ihren Dienst ausführen, hängt die Methode für das Hoch- oder Aufskalieren von Ihren Workloads ab. Weitere Informationen zum Skalieren Ihrer Apps in Azure finden Sie hier:  

- [Hochskalieren einer App in Azure App Service](../../app-service/manage-scale-up.md)  
- [Erste Schritte mit der automatischen Skalierung in Azure](../../azure-monitor/platform/autoscale-get-started.md?toc=/azure/app-service/toc.json)  

Aktivieren Sie die Überwachung für Ihre Anwendung mit [Application Insights](../../azure-monitor/app/app-insights-overview.md), um ausführliche Informationen zu erfassen, einschließlich der Anzahl der Seitenaufrufe, Anwendungsanforderungen und Ausnahmen.  

## <a name="azure-automation"></a>Azure-Automatisierung

[Azure Automation](../../automation/automation-intro.md) stellt einen cloudbasierten Automatisierungs- und Konfigurationsdienst bereit, der eine einheitliche Verwaltung Ihrer Azure- und Nicht-Azure-Umgebungen unterstützt. Mit diesem Tool können Sie die Bereitstellung, Ausführung und Außerbetriebnahme von Workloads und Ressourcen steuern. Azure Automation ist immer betriebsbereit. Sie können den Dienst mit vorhandenen Ressourcen verwenden. Außerdem haben Sie die Möglichkeit, bei Bedarf Ressourcen oder Abonnements zu erstellen. Sie bezahlen nur für die tatsächliche Nutzung.  

Beispiel: Bei einer Dev/Test-Produktionsbereitstellung müssen einige der Ressourcen und Phasen ständig betriebsbereit sein. Andere müssen nur einige Male pro Jahr aktualisiert und ausgeführt werden.  

Azure Automation wird in diesem Szenario wichtig. Wenn Sie neue Apps entwickeln und Ihren ersten Pull Request (PR) übermitteln, können Sie einen Automatisierungsauftrag starten. Der Auftrag stellt Infrastruktur als Code über eine ARM-Vorlage (Azure Resource Manager) bereit, um in der Vorproduktionsphase alle Ihre Ressourcen in Ihrem Azure Dev/Test-Abonnement zu erstellen.  

## <a name="azure-resource-manager"></a>Azure Resource Manager

[Azure Resource Manager-Vorlagen (ARM)](../../azure-resource-manager/templates/overview.md) implementieren Infrastruktur als Code für Ihre Azure-Lösungen. Die Vorlage definiert die Infrastruktur und Konfiguration für Ihr Projekt. Sie können Ihre Bereitstellungen automatisieren.  

Zudem haben Sie die Möglichkeit, Ihre Konfigurationen so oft bereitzustellen, wie Sie die Vorproduktionsumgebung aktualisieren und Ihre Kosten nachverfolgen möchten. Mit Azure Automation können Sie Ihre ARM-Vorlagen nach Bedarf ausführen und löschen.  

Wenn ein Dienst oder eine Ressource nur zweimal pro Jahr aktualisiert werden muss, verwenden Sie DevOps-Tools, um Ihre ARM-Vorlage bereitzustellen. Lassen Sie den Automatisierungsauftrag Ihre Ressource deaktivieren und dann bei Bedarf erneut bereitstellen.  
