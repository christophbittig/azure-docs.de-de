---
title: Verwalten von Zuverlässigkeit und Leistung mit Azure Dev/Test-Abonnements
description: Stellen Sie mithilfe von Dev/Test-Abonnements die Zuverlässigkeit im Zusammenhang mit Ihren Anwendungen sicher.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: fe34eb3b5fff32ab59c72c90041b095a82806e9b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096127"
---
# <a name="reliability-management"></a>Zuverlässigkeitsverwaltung  

Produktionsdienste sind zwar nicht in einem Dev/Test-Abonnement enthalten, aber Sie können andere Phasen in Ihrem Azure Dev/Test-Abonnement verwenden, um die Zuverlässigkeit in der Produktion sicherzustellen.  

Wenn Sie Organization Dev/Test-Abonnements verwenden, müssen Sie sich entscheiden, wie Sie die folgenden Aufgaben erfolgreich erledigen:  

- Steuerungsdaten  
- Steuern von Sicherheit und Zugriff  
- Verwalten der Uptime dieses Produktionssystems  

In der Regel gibt es verschiedene Bereitstellungsphasen, die Sie vor der Produktionsphase durchlaufen: Freigabe, QA, Integration, Staging und Failover. Je nachdem, wie Ihr Unternehmen diese Phasen definiert, kann sich die Verwendung eines Organization Dev/Test-Abonnements ändern.  

Wenn Sie unternehmenskritische Dienste wie Anwendungen für Kunden ausführen, verwenden Sie kein Dev/Test-Abonnement. Für Dev/Test-Abonnements gibt es keine SLA mit finanzieller Absicherung. Diese Abonnements sind für Tests und die Entwicklung vor der Produktionsphase vorgesehen.  

## <a name="site-reliability-engineering-sre"></a>Site Reliability Engineering (SRE)  

Weitere Informationen zur Zuverlässigkeitstechnik und -verwaltung finden Sie in Artikeln, die sich mit Site Reliability Engineering beschäftigen. Dies ist eine technische Disziplin, mit der Organisationen dabei unterstützt werden sollen, im Kontext ihrer Systeme, Dienste und Produkte eine angemessene Zuverlässigkeit zu erzielen.  

Wie sich SRE und DevOps unterscheiden, wird in diesem Bereich noch diskutiert. Zu den weitgehend offensichtlichen Unterschieden gehören folgende:  

- SRE ist eine technische Disziplin, die auf Zuverlässigkeit ausgerichtet ist. DevOps ist eine kulturelle Bewegung, die sich aus der Idee heraus entwickelt hat, die Silos aufzubrechen, die in Entwicklungs- und Betriebsorganisationen vorliegen.  
- SRE kann der Name einer Rolle sein (Beispielsatz: „Ich bin Site Reliability Engineer (SRE).“). Bei DevOps ist dies nicht möglich.  
- SRE ist in der Regel präskriptiv. DevOps ist dies absichtlich nicht. Die nahezu universelle Einführung von Continuous Integration/Continuous Delivery und agilen Prinzipien ist das, was DevOps am nächsten kommt.  

Weitere Informationen zur Praxis im Zusammenhang mit SRE finden Sie unter den folgenden Links:  

- [SRE im Kontext](/learn/modules/intro-to-site-reliability-engineering/3-sre-in-context.md)  
- [Wichtige SRE-Prinzipien und -Methoden: Erfolgszyklus](/learn/modules/intro-to-site-reliability-engineering/4-key-principles-1-virtuous-cycles.md)  
- [Wichtige SRE-Prinzipien und -Methoden: Die menschliche Seite von SRE](/learn/modules/intro-to-site-reliability-engineering/5-key-principles-2-human-side-of-sre.md)  
- [Erste Schritte mit SRE](/learn/modules/intro-to-site-reliability-engineering/6-getting-started.md)  

## <a name="service-level-agreements"></a>Vereinbarungen zum Servicelevel  

Enterprise Dev/Test ist ausschließlich für die Entwicklung und das Testen Ihrer Anwendungen konzipiert. Bei Verwendung des Abonnements gibt es keine SLA mit finanzieller Absicherung.  

## <a name="learn-to-use-different-types-of-devtest-subscriptions"></a>Verwenden verschiedener Typen von Dev/Test-Abonnements  

Unabhängig davon, ob Sie [monatliche Azure-Gutschriften für Visual Studio-Abonnenten](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), [Enterprise Dev/Test-Abonnements](https://azure.microsoft.com/offers/ms-azr-0148p/) oder ein [Dev/Test Pay-As-You-Go-Abonnement (PAYG)](https://azure.microsoft.com/offers/ms-azr-0023p/) benötigen, finden Sie problemlos Angebote, die sich für Einzelpersonen oder ein Team eignen.  

## <a name="managing-individual-credit-subscriptions"></a>Verwalten einzelner Gutschriftabonnements  

Azure-Gutschriften in Visual Studio sind ein individueller Vorteil für einzelne Dev/Test-Entwickler*innen und die Inner-Loop-Entwicklung. Gutschriften können nicht für Entwickler*innen gebündelt werden. Gutschriftabonnements sind weiterhin Azure-Abonnements, aber es handelt sich um ein bestimmtes Azure-Angebot. Verwalten Sie Ihre Gutschriftabonnements auf die gleiche Weise wie andere Azure-Abonnements, damit Sie in Gruppen und Teams arbeiten können. Sie können einzelne Ausgabenlimits für eine Kreditkarte entfernen. Dies ist auch möglich, wenn für das Enterprise Dev/Test-Abonnement die ausgewählte Kaufmethode Ihres Unternehmens verwendet wird.  

Für Inner-Loop-Entwickleraktivitäten werden häufig Gutschriften verwendet, danach findet aber ein Wechsel zu Azure Dev/Test-Abonnements (Enterprise oder Organization) und der nutzungsbasierten Bezahlungsmethode statt. Auf diese Weise können Sie Inner-Loop-Aktivitäten im Rahmen von DevOps-Prozessen mit Ihrem individuellen Gutschriftabonnement ausführen. Bei Outer-Loop-Aktivitäten befinden sich nicht für die Produktion vorgesehene Ziele bei DevOps-Prozessen in Enterprise Dev/Test-Abonnements, produktionsbezogene Ziele hingegen in Produktionsabonnements.  

Verwalten Sie Ihre Gutschriftabonnements, Enterprise Dev/Test-Abonnements sowie PAYG-Abonnements, und teilen Sie Ihre Entwickler*innen in [Verwaltungsgruppen](../../governance/management-groups/how-to/protect-resource-hierarchy.md) ein, die jeweils eine eindeutige Hierarchie aufweisen.  

## <a name="using-your-organization-azure-devtest-offers"></a>Verwenden von Azure Dev/Test-Angeboten für Organisationen  

Wenn Sie ein Azure Dev/Test-Abonnement für Organisationen benötigen, stehen Ihnen zwei Angebote zur Verfügung.  

- [Dev/Test Pay-As-You-Go-Abonnement (PAYG) (0023P)](https://azure.microsoft.com/offers/ms-azr-0023p/-)  
- [Enterprise Dev/Test-Abonnement (0148P)](https://azure.microsoft.com/offers/ms-azr-0148p/)  

Jedes Abonnement bietet spezifische Rabatte und erfordert ein Visual Studio-Abonnement.  

Mit jedem Abonnementangebot können Sie dafür sorgen, dass Ihr Team über die benötigten Dev/Test-Umgebungen mit vorkonfigurierten VMs in der Cloud verfügt. Erstellen Sie mehrere Azure-Abonnements, und verwalten Sie sie über ein Konto. Sie können isolierte Umgebungen verwalten und eine separate Rechnung für verschiedene Projekte oder Teams anfordern.  

Enterprise Dev/Test-Abonnements erfordern ein Enterprise Agreement (EA). Dev/Test Pay-As-You-Go-Abonnements erfordern kein EA, können aber mit einem Enterprise Agreement-Konto genutzt werden.  

## <a name="why-would-i-use-payg-offers-vs-enterprise-devtest-offers"></a>Warum sollte ich PAYG-Angebote bzw. Enterprise Dev/Test-Angebote nutzen?  

Ein Dev/Test Pay-As-You-Go-Angebot ist möglicherweise die richtige Wahl, wenn Sie Visual Studio-Abonnent sind. Im Gegensatz zu Gutschriftabonnements für die individuelle Nutzung eignen sich PAYG-Angebote ideal für die Teamentwicklung, und diese Abonnements ermöglichen es Ihnen, mehrere Benutzer*innen innerhalb eines Abonnements zu verwalten. Ein Dev/Test Pay-As-You-Go-Angebot eignet sich möglicherweise in den folgenden Fällen:  

- Sie verfügen über kein Enterprise Agreement. In diesem Fall können Sie nur ein PAYG-Konto mit einer Visual Studio-Lizenz erstellen.  
- Sie erstellen ein Enterprise Agreement, müssen aber ein Abonnement einrichten, das nicht die Vereinbarung Ihrer Organisation verwendet. Möglicherweise verwalten Sie ein spezifisches Projekt, für das ein eigenes Abonnement erforderlich ist, oder Sie müssen eine isolierte Umgebung erstellen, die separat für Projekte oder Teams abgerechnet wird.  
- Sie bevorzugen es, Identitäten isoliert zu verwalten. Möglicherweise müssen bestimmte Identitäten von anderen getrennt bleiben, um den Zugriff auf Daten, Ressourcen und Apps zu schützen.  
