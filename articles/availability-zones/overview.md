---
title: Resilienz in Azure
description: Erfahren Sie mehr über Resilienz in Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 8956555eb5393919b1e951fd13d2de17013744cc
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858579"
---
# <a name="resiliency-in-azure"></a>Resilienz in Azure

**Resilienz** ist die Möglichkeit eines Systems, nach Ausfällen eine Wiederherstellung durchzuführen und die Betriebsbereitschaft sicherzustellen. Es geht nicht nur um das Vermeiden von Ausfällen, sondern auch um das Reagieren auf Ausfälle, um Ausfallzeiten oder Datenverluste zu minimieren. Da Ausfälle auf verschiedenen Ebenen auftreten können, ist es wichtig, Schutz bei allen Arten von Ausfällen entsprechend den Dienstverfügbarkeitsanforderungen zu bieten. Resilienz in Azure unterstützt und erweitert Funktionen, die auf Ausfälle in Echtzeit reagieren, um eine kontinuierliche Dienst- und Datenschutzgarantie für unternehmenskritische Anwendungen zu gewährleisten, die Ausfallzeiten nahe null und ein hohes Kundenvertrauen erfordern.

Azure umfasst integrierte Resilienzdienste, die Sie basierend auf Ihren Geschäftsanforderungen nutzen und verwalten können. Azure bietet Lösungen, die die Resilienz verbessern, unabhängig davon, ob es sich um den Ausfall eines einzelnen Hardwareknotens, einen Ausfall auf Rackebene, den Ausfall eines Rechenzentrums oder einen umfangreichen regionalen Ausfall handelt. Verfügbarkeitsgruppen sorgen beispielsweise dafür, dass die von Ihnen in Azure bereitgestellten virtuellen Computer auf mehrere isolierte Hardwareknoten in einem Cluster verteilt werden. Verfügbarkeitszonen schützen die Anwendungen und Daten der Kunden vor Ausfällen von Rechenzentren an mehreren physischen Standorten innerhalb einer Region. **Regionen** und **Verfügbarkeitszonen** sind für Ihren Anwendungsentwurf und Ihre Resilienzstrategie von zentraler Bedeutung und werden weiter unten in diesem Artikel ausführlicher erläutert.

## <a name="resiliency-requirements"></a>Resilienzanforderungen

Der für eine Azure-Lösung erforderliche Resilienzgrad hängt von mehreren Überlegungen ab. Verfügbarkeits- und Latenz-SLA und andere Geschäftsanforderungen steuern die Architekturoptionen und den Resilienzgrad und sollten zuerst berücksichtigt werden. Die Verfügbarkeitsanforderungen reichen von der für Ihr Unternehmen tolerierbaren Ausfallzeit und den damit verbundenen Kosten bis hin zu der Menge an Geld und Zeit, die Sie realistisch in die Hochverfügbarkeit einer Anwendung investieren können.  

Das Erstellen resilienter Systeme in Azure ist eine **gemeinsame Verantwortung**. Microsoft ist für die Zuverlässigkeit der Cloudplattform verantwortlich, einschließlich des globalen Netzwerks und der Rechenzentren. Azure-Kunden und -Partner sind für die Resilienz ihrer Cloudanwendungen und die Verwendung bewährter Architekturmethoden basierend auf den Anforderungen der einzelnen Workloads verantwortlich. Während Azure ständig nach größtmöglicher Resilienz in der SLA für die Cloudplattform strebt, müssen Sie für jede Workload in Ihrer Lösung eigene Ziel-SLAs definieren. Anhand einer Vereinbarung zum Servicelevel kann ausgewertet werden, ob die Architektur die geschäftlichen Anforderungen erfüllt. In dem Maße, wie Sie einen höheren Prozentsatz an garantierter SLA-Betriebszeit anstreben, steigen die Kosten und die Komplexität, um dieses Verfügbarkeitsniveau zu erreichen. Eine Betriebszeit von 99,99 % entspricht etwa fünf Minuten Gesamtausfallzeit pro Monat. Ist das Erreichen dieses Prozentsatzes die zusätzliche Komplexität und die Mehrkosten wert? Die Antwort hängt von Ihren individuellen geschäftlichen Anforderungen ab. Während Sie Ihre endgültigen SLA-Verpflichtungen beschließen, machen Sie sich mit den von Microsoft unterstützten SLAs vertraut. Jeder Azure-Dienst verfügt über eine eigene SLA. 

## <a name="building-resiliency"></a>Herstellen von Resilienz

Sie sollten die Verfügbarkeitsanforderungen Ihrer Anwendung zu Beginn der Planung definieren. Viele Anwendungen benötigen keine 100 %-Hochverfügbarkeit. Dies im Hinterkopf zu behalten, kann dazu beitragen, die Kosten während nicht kritischer Zeiten zu optimieren. Identifizieren Sie die Art der Fehler, die eine Anwendung tolerieren kann, sowie die potenziellen Auswirkungen jedes Fehlers. Ein Wiederherstellungsplan sollte alle kritischen Dienste abdecken, indem Sie die Wiederherstellungsstrategie auf Ebene der einzelnen Komponente sowie der gesamten Anwendung ausarbeiten. Entwerfen Sie Ihre Wiederherstellungsstrategie so, dass sie vor Ausfällen auf Zonen-, Regions- und Anwendungsebene schützt. Führen Sie außerdem Tests der End-to-End-Anwendungsumgebung durch, um die Resilienz und Wiederherstellung von Anwendungen bei unerwarteten Fehlern zu messen.  

In der folgenden Prüfliste wird der Umfang der Resilienzplanung behandelt. 

| **Resilienzplanung** |
| --- | 
| **Definieren** Sie Verfügbarkeits- und Wiederherstellungsziele, um die geschäftlichen Anforderungen zu erfüllen. | 
| **Entwerfen** Sie Resilienzfeatures für Ihre Anwendungen basierend auf den Verfügbarkeitsanforderungen. |
| **Passen** Sie Anwendungs- und Datenplattformen an Ihre Zuverlässigkeitsanforderungen an. | 
| **Konfigurieren** Sie Verbindungspfade, um die Verfügbarkeit zu verbessern. | 
| **Verwenden** Sie Verfügbarkeitszonen und Notfallwiederherstellungsplanung, wo erforderlich, um die Zuverlässigkeit zu steigern und Kosten zu optimieren. |
| **Vergewissern Sie sich**, dass Ihre Anwendungsarchitektur ausfallsicher ist. | 
| **Berücksichtigen** Sie, was geschieht, wenn die SLA-Anforderungen nicht erfüllt werden. |
| **Identifizieren** Sie mögliche Fehlerpunkte im System. Der Anwendungsentwurf sollte Abhängigkeitsfehler tolerieren, indem Verbindungsunterbrechung bereitgestellt wird. | 
| **Erstellen** Sie Anwendungen, die auch ohne ihre jeweiligen Abhängigkeiten in Betrieb bleiben. | 

## <a name="regions-and-availability-zones"></a>Regionen und Verfügbarkeitszonen

Regionen und Verfügbarkeitszonen sind ein großer Teil der Resilienzgleichung. Die Regionen verfügen über mehrere physisch getrennte Verfügbarkeitszonen, die über ein Hochleistungsnetzwerk mit einer Latenzzeit von weniger als 2 ms zwischen den physischen Zonen verbunden sind, sodass Ihre Daten auch bei Problemen synchronisiert und verfügbar bleiben. Sie können diese Infrastruktur strategisch nutzen, indem Sie Anwendungen und Dateninfrastrukturen entwickeln, die automatisch repliziert werden und unterbrechungsfreie Dienste zwischen Zonen und Regionen bereitstellen. Wählen Sie die jeweils beste Region für Ihre Anforderungen basierend auf den technischen und gesetzlichen Aspekten aus – Dienstfunktionen, Datenresidenz, Konformitätsanforderungen, Wartezeit – und beginnen Sie mit der Erweiterung Ihrer Resilienzstrategie.

Microsoft Azure-Dienste unterstützen Verfügbarkeitszonen, bieten optimale Hochverfügbarkeit für Ihren Cloudbetrieb und unterstützen gleichzeitig Ihre Anforderungen an die Notfallwiederherstellung und Geschäftskontinuität. Wählen Sie die jeweils beste Region für Ihre Anforderungen basierend auf den technischen und gesetzlichen Aspekten aus – Dienstfunktionen, Datenresidenz, Konformitätsanforderungen, Wartezeit – und beginnen Sie mit der Erweiterung Ihrer Resilienzstrategie. Weitere Informationen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure](az-overview.md).

## <a name="shared-responsibility"></a>Gemeinsame Verantwortung

Das Erstellen resilienter Systeme in Azure ist eine gemeinsame Verantwortung. Microsoft ist für die Zuverlässigkeit der Cloudplattform verantwortlich, und dies schließt das globale Netzwerk und Rechenzentren ein. Azure-Kunden und -Partner sind für die Resilienz ihrer Cloudanwendungen und die Verwendung bewährter Architekturmethoden basierend auf den Anforderungen der einzelnen Workloads verantwortlich. Weitere Informationen finden Sie unter [Programm zur Verwaltung der Geschäftskontinuität in Azure](business-continuity-management-program.md). 

## <a name="next-steps"></a>Nächste Schritte

- [Regionen und Verfügbarkeitszonen in Azure](az-overview.md)
- [Azure-Dienste mit Unterstützung für Verfügbarkeitszonen](az-region.md)
- [Whitepaper zur Resilienz in Azure](https://azure.microsoft.com/mediahandler/files/resourcefiles/resilience-in-azure-whitepaper/Resilience%20in%20Azure.pdf)
- [Azure Well-Architected Framework](https://www.aka.ms/WellArchitected/Framework)
- [Anleitung zur Azure-Architektur](/architecture/high-availability/building-solutions-for-high-availability)
