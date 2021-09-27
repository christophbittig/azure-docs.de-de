---
title: Bereitstellen eines Proof of Concept
description: Erfahren Sie, wie Sie einen Proof of Concept bereitstellen, damit Azure DevTest Labs erfolgreich in eine Unternehmensumgebung integriert werden kann.
ms.topic: how-to
ms.date: 06/2/2020
ms.openlocfilehash: 03c8cc087f33ea6385fc0690f5634f33a3cbf0fd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128589965"
---
# <a name="deliver-a-proof-of-concept"></a>Bereitstellen eines Proof of Concept 

Eines der wichtigsten Szenarien für Azure DevTest Labs ist die Bereitstellung von Entwicklungs- und Testumgebungen in der Cloud. Beispiele:

* Erstellen von Entwicklerdesktops in der Cloud
* Konfigurieren von Umgebungen für Tests
* Ermöglichen des Zugriffs auf virtuelle Computer und andere Azure-Ressourcen
* Einrichten eines Sandboxbereichs für Entwickler zum Lernen und Experimentieren

DevTest Labs bietet auch den Richtlinienrahmen und Kostenkontrollen, um dem Unternehmen die Bereitstellung von „Self-Service-Azure“ für Entwickler zu ermöglichen, die Sicherheitsrichtlinien des Unternehmens, gesetzliche Bestimmungen und Konformitätsrichtlinien einhalten. 

Jedes Unternehmen hat unterschiedliche Anforderungen in Hinsicht darauf, wie Azure DevTest Labs erfolgreich in die jeweilige Umgebung integriert werden kann. In diesem Artikel werden die häufigsten Schritte beschrieben, die Unternehmen ausführen müssen, um einen erfolgreichen Proof of Concept zu gewährleisten. Ein Proof of Concept ist der erste Schritt zu einer erfolgreichen End-to-End-Bereitstellung. 

## <a name="getting-started"></a>Erste Schritte 

Um mit der Bereitstellung eines Proof of Concept zu beginnen, ist es wichtig, zunächst etwas über Azure und DevTest Labs zu erfahren.  Hier sind einige Ressourcen für den Einstieg: 

* [Grundlegendes zum Azure-Portal](https://azure.microsoft.com/features/azure-portal/)
* [Grundlagen von DevTest Labs](devtest-lab-overview.md)
* [Von DevTest Labs unterstützte Szenarien](devtest-lab-guidance-get-started.md)
* [Dokumentation zu DevTest Labs im Unternehmen](devtest-lab-guidance-prescriptive-adoption.md)
* [Einführung in Azure-Netzwerke](../virtual-network/virtual-networks-overview.md)

## <a name="prerequisites"></a>Voraussetzungen 

Zum erfolgreichen Abschluss eines Pilotprojekts oder Proof of Concept mit DevTest Labs müssen einige Voraussetzungen erfüllt sein: 

* **Azure-Abonnement:** Unternehmen verfügen häufig über ein bestehendes [Enterprise Agreement](https://azure.microsoft.com/pricing/purchase-options/enterprise-agreement/), das den Zugriff auf Azure ermöglicht, und sie können ein bestehendes oder neues Abonnement für DevTest Labs verwenden. Alternativ können Unternehmen während des Pilotprojekts ein [Visual Studio-Abonnement](https://azure.microsoft.com/pricing/member-offers/visual-studio-subscriptions/) verwenden (und das kostenlose Azure-Guthaben nutzen). Wenn keine dieser Optionen zur Verfügung steht, kann ein Unternehmen ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/search/?&OCID=AID719825_SEM_g4lyBqgB&lnkd=Bing_Azure_Brand&msclkid=ecc4275a31b61375749e7a5322c20de8&dclid=CMGW5-m78-ICFaLt4QodmUwGtQ) erstellen und verwenden. Ist ein Enterprise Agreement vorhanden, stellt die Verwendung eines [Enterprise Dev/Test-Abonnements](https://azure.microsoft.com/offers/ms-azr-0148p/) eine gute Möglichkeit dar, um Zugriff auf Windows 10/Windows 8.1-Clientbetriebssysteme und vergünstigte Preise für Entwicklungs- und Testworkloads zu erhalten. 
* **Azure Active Directory-Mandant:** Damit Benutzer verwaltet werden können (z. B. Hinzufügen von Benutzern oder Hinzufügen von Lab-Besitzern), müssen diese Benutzer Teil des [Azure Active Directory-Mandanten](https://azure.microsoft.com/services/active-directory/) sein, der im Azure-Abonnement für das Pilotprojekt verwendet wird. Häufig richten Unternehmen eine [Hybrididentität](../active-directory/hybrid/whatis-hybrid-identity.md) ein, damit Benutzer die eigene lokale Identität in der Cloud verwenden können, doch ist dies für das DevTest Labs-Pilotprojekt nicht erforderlich. 

## <a name="scoping-of-the-pilot"></a>Umfang des Pilotprojekts 

Es ist wichtig, ein Pilotprojekt zu planen, bevor Sie mit der Implementierung beginnen. Da im Voraus bekannt ist, dass die Ressourcen nicht unbegrenzt zur Verfügung stehen, bestehen bereits entsprechende Erwartungen an die Benutzer des Pilotprojekts. 

> [!IMPORTANT]
> An dieser Stelle muss noch einmal deutlich darauf hingewiesen werden, wie wichtig es ist, den Umfang des Pilotprojekts genau festzulegen und die Erwartungen im Voraus zu definieren.

Beantworten Sie die folgenden wichtigen Fragen, bevor Sie mit dem Pilotprojekt beginnen: 

* Was möchten Sie aus dem Pilotprojekt lernen, und wie sieht eine erfolgreiche Umsetzung aus? 
* Welche Workloads oder Szenarien werden im Pilotprojekt abgedeckt? Es ist wichtig, nur eine kleine Gruppe zu definieren, damit sichergestellt ist, dass der Umfang des Pilotprojekts schnell festgelegt und das Projekt schnell abgeschlossen werden kann. 
* Welche Ressourcen müssen im Lab verfügbar sein? Zum Beispiel: benutzerdefinierte Images, Marketplace-Images, Richtlinien, Netzwerktopologie. 
* Welche Benutzer und Teams sind am Pilotprojekt zum Überprüfen der Benutzererfahrung beteiligt?  
* Wie lange dauert das Pilotprojekt? Wählen Sie einen Zeitrahmen aus, der gut zum geplanten Umfang passt, z.B. zwei Wochen oder einen Monat. 
* Was geschieht nach Abschluss des Pilotprojekts mit den zugeordneten Ressourcen, die während des Projekts verwendet wurden? Haben Sie die Absicht, die Pilotprojektressourcen zu verwerfen? Vielleicht haben Sie folgenden Gedanken:
   
   „Wenn wir so planen können, dass die virtuellen Computer und Labs am Ende des Pilotprojekts entsorgt werden, können wir ein einzelnes Abonnement für das Pilotprojekt einrichten und alle unsere Arbeiten dort ausführen, während wir parallel die Fragen zum umfassenden Rollout lösen.“ 

Es besteht allgemein die Tendenz, das Pilotprojekt „perfekt“ zu machen, damit es genau den endgültigen Zustand darstellt, der nach dem Rollout des Diensts im Unternehmen gegeben sein wird. Dies ist aber eine falsche Annahme. Je mehr Sie sich dem „perfekten“ Zustand nähern, desto mehr müssen Sie bereits abgeschlossen haben, *bevor* Sie mit dem Pilotprojekt beginnen. Der Zweck des Pilotprojekts besteht darin, die richtigen Entscheidungen für das zentrale Hochskalieren und den Rollout des endgültigen Diensts zu treffen. 

Der Schwerpunkt des Pilotprojekts sollte auf der Auswahl der notwendigen minimalen Workloads und Abhängigkeiten liegen, damit die Frage beantwortet wird, ob Azure DevTest Labs der richtige Dienst für Ihr Unternehmen ist. Es wird empfohlen, die einfachste Workload mit den geringsten Abhängigkeiten auszuwählen, um einen schnellen und klaren Erfolg sicherzustellen. Wenn das nicht möglich ist, wählen Sie eine möglichst repräsentative Workload aus, die potenzielle Komplexität bietet, damit der Erfolg in der Pilotphase in der Phase der horizontalen Skalierung repliziert werden kann. 

Das folgende Beispiel zeigt einen gut ausgelegten Proof of Concept. 

## <a name="example-proof-of-concept-plan"></a>Beispiel: Proof of Concept-Plan 

In diesem Abschnitt wird ein Beispiel für die Bereichsdefinition eines Proof of Concept des Pilotprojekts für DevTest Labs gezeigt. 

> [!TIP]
> Es wird dringend empfohlen, das in diesem Abschnitt beschriebene Beispiel nicht zu überspringen, um die Möglichkeit einer fehlerhaften Einrichtung Ihres Projekts zu minimieren. 

### <a name="overview"></a>Übersicht 

Wir planen die Entwicklung einer neuen Umgebung in Azure auf Grundlage von DevTest Labs, die von Anbietern als isolierte Umgebung aus dem Unternehmensnetzwerk genutzt werden kann. Um festzustellen, ob die Lösung die Anforderungen erfüllt, entwickeln wir einen Proof of Concept zum Überprüfen der End-to-End-Lösung. Wir haben mehrere Anbieter zum Testen und Überprüfen der Benutzererfahrung einbezogen. 

### <a name="outcomes"></a>Ergebnisse 

Beim Erstellen eines Proof of Concept konzentrieren wir uns zunächst auf die Ergebnisse (was möchten wir erreichen). Am Ende des Proof of Concept erwarten wir Folgendes: 

* Eine funktionierende End-to-End-Lösung, mit der Anbieter Gastkonten in Azure Active Directory (Azure AD) verwenden können, um auf eine isolierte Umgebung in Azure zuzugreifen. Die Umgebung verfügt über die zur Produktivität erforderlichen Ressourcen. 
* Mögliche Blockierprobleme, die sich auf die breitere Verwendung und Einführung auswirken, sind aufgelistet und verstanden.
* Die Personen, die an der Entwicklung des Proof of Concept beteiligt sind, haben gute Kenntnisse des gesamten Codes. Sie verstehen auch betroffene Sicherheiten und sind zuversichtlich in Hinsicht auf eine breitere Einführung.

### <a name="open-questions-and-prerequisites"></a>Offene Fragen und Voraussetzungen 

* Haben wir ein Abonnement erstellt, das wir für dieses Projekt verwenden können? 
* Haben wir einen Azure AD-Mandanten und einen globalen Azure AD-Administrator bestimmt, der Hilfe und Anleitung bei Azure AD-bezogene Fragen bieten kann? 
* Verfügen wir über einen Ort, an dem die am Projekt beteiligten Personen zusammenarbeiten können? 

   * Quellcode und Skripts (z.B. Azure Repos) 
   * Dokumente (z.B. Microsoft Teams oder SharePoint)  
   * Unterhaltungen (z.B. Microsoft Teams) 
   * Arbeitselemente (z.B. Azure Boards) 
* Welche Ressourcen sind für Anbieter erforderlich? Dazu gehören im Netzwerk verfügbare Anwendungen, sowohl lokal auf den virtuellen Computern als auch auf anderen erforderlichen Servern. 
* Werden die virtuellen Computer einer Domäne in Azure hinzugefügt? Handelt es sich dabei um Azure Active Directory Domain Services (Azure AD DS) oder etwas anderes? 
* Haben wird das Team oder Anbieter festgelegt, die das Ziel des Proof of Concept sein werden? Wer werden die Kunden für die Umgebung sein?
* Welche Azure-Region wird für den Proof of Concept verwendet? 
* Gibt es eine Liste von Diensten, die Anbieter neben IaaS (VMs) über DevTest Labs verwenden dürfen? 
* Haben wir einen Plan für die Schulung von Anbietern/Benutzern zur Verwendung des Labs? 

### <a name="components-of-the-proof-of-concept-solution"></a>Komponenten der Proof of Concept-Lösung 

Wir erwarten, dass die Lösung die folgenden Komponenten umfasst: 

* Verschiedene Anbieterteams verwenden eine Reihe von DevTest Labs in Azure.
* Die Labs sind mit einer Netzwerkinfrastruktur verbunden, die die Anforderungen unterstützt.
* Die Anbieter haben über Azure AD und Rollenzuweisungen Zugriff auf die Labs.
* Anbieter haben eine Möglichkeit, erfolgreich eine Verbindung mit ihren Ressourcen herzustellen. Insbesondere ein Site-to-Site-VPN ermöglicht direkten Zugriff auf virtuelle Computer ohne öffentliche IP-Adressen.
* Eine Reihe von Artefakten deckt die erforderliche Software ab, die die Anbieter auf den virtuellen Computern benötigen.

## <a name="additional-planning-and-design-decisions"></a>Weitere Planungs- und Entwurfsentscheidungen 

Bevor Sie eine vollständige DevTest Labs-Lösung veröffentlichen, müssen Sie einige wichtige Planungs- und Entwurfsentscheidungen treffen. Die Erfahrungen, die beim Arbeiten an einem Proof of Concept gesammelt werden, können Ihnen bei diesen Entscheidungen helfen. Weitere Überlegungen umfassen Folgendes: 

* **Abonnementtopologie:** Die Anforderungen an Ressourcen in Azure auf Unternehmensebene können die [verfügbaren Kontingente innerhalb eines einzelnen Abonnements](../azure-resource-manager/management/azure-subscription-service-limits.md) übersteigen. Dadurch sind mehrere Azure-Abonnements und/oder Dienstanforderungen erforderlich, um die anfänglichen Abonnementgrenzwerte zu erhöhen. Es ist wichtig, im Voraus zu entscheiden, wie Ressourcen auf Abonnements verteilt werden sollen. Eine wertvolle Ressource ist der [Leitfaden zur Entscheidungsfindung für Abonnements](/azure/architecture/cloud-adoption/decision-guides/subscriptions/), da es schwierig ist, Ressourcen später in ein anderes Abonnement zu verschieben. Beispielsweise kann ein Lab nicht in ein anderes Abonnement verschoben werden, nachdem es erstellt wurde.  
* **Netzwerktopologie:** Die [Standardnetzwerkinfrastruktur](../app-service/networking-features.md), die DevTest Labs automatisch erstellt, reicht möglicherweise nicht aus, um die Anforderungen und Einschränkungen für die Unternehmensbenutzer zu erfüllen. Es ist üblich, dass [über Azure ExpressRoute verbundene virtuelle Netzwerke](/azure/architecture/reference-architectures/hybrid-networking/), [Hub-and-Spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) für Konnektivität zwischen Abonnements und sogar [erzwungenes Routing](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) zur Gewährleistung ausschließlich lokaler Konnektivität verwendet werden. Mit DevTest Labs können vorhandene virtuelle Netzwerke mit dem Lab verbunden werden, um deren Verwendung beim Erstellen neuer virtueller Computer im Lab zu ermöglichen. 
* **Remotezugriff auf VMs:** Es gibt viele Möglichkeiten für den Remotezugriff auf VMs in DevTest Labs. Am einfachsten ist es, öffentliche IP-Adressen oder freigegebene öffentliche IP-Adressen zu verwenden. Dies sind [im Lab verfügbare Einstellungen](devtest-lab-shared-ip.md). Wenn diese Optionen nicht ausreichen, stellt auch die Verwendung eines Remotezugriffgateways eine Möglichkeit dar. Diese Option ist unter [Azure DevTest Labs: Referenzarchitektur für Unternehmen](devtest-lab-reference-architecture.md) gezeigt und in der [Dokumentation zu Remotedesktopgateways in DevTest Labs](configure-lab-remote-desktop-gateway.md) ausführlicher beschrieben. Unternehmen können auch ExpressRoute oder ein Site-to-Site-VPN verwenden, um ihre Labs mit dem lokalen Netzwerk zu verbinden. Diese Option ermöglicht direkte Verbindungen mit den virtuellen Computern über Remotedesktop oder SSH basierend auf der privaten IP-Adresse ohne Zugriff über das Internet. 
* **Handhabung von Berechtigungen:** Die beiden wichtigsten Berechtigungen, die häufig in DevTest Labs verwendet werden, sind [Besitzer und Lab-Benutzer](devtest-lab-add-devtest-user.md). Es ist wichtig, sich vor dem umfassenden Rollout von DevTest Labs zu entscheiden, wem die einzelnen Zugriffsebenen im Lab anvertraut werden. Bei einem gängigen Modell ist der Budgetbesitzer (z.B. der Teamleiter) der Lab-Besitzer, und die Teammitglieder sind die Lab-Benutzer. Bei diesem Modell kann die Person (Teamleiter), die für das Budget verantwortlich ist, die Richtlinieneinstellungen entsprechend anpassen, und das Team innerhalb des Budgets halten.  

## <a name="completing-the-proof-of-concept"></a>Abschließen des Proof of Concept 

Sobald die erwarteten Erkenntnisse vorliegen, ist es an der Zeit, das Pilotprojekt abzuschließen. Dies ist der Zeitpunkt, um Feedback von den Benutzern zu sammeln, festzustellen, ob das Pilotprojekt erfolgreich war, und zu entscheiden, ob die Organisation mit einem umfassenden Rollout von DevTest Labs im Unternehmen fortfahren soll. Es ist auch ein guter Zeitpunkt, eine automatisierte Bereitstellung von DevTest Labs und zugeordneten Ressourcen in Betracht zu ziehen, um Konsistenz während des gesamten Rollouts sicherzustellen. 

## <a name="next-steps"></a>Nächste Schritte 

* [Dokumentation zu DevTest Labs im Unternehmen](devtest-lab-guidance-prescriptive-adoption.md)
* [Referenzarchitektur für ein Unternehmen](devtest-lab-reference-architecture.md)
* [Zentrales Hochskalieren der DevTest Labs-Bereitstellung](devtest-lab-guidance-orchestrate-implementation.md)
* [Orchestrieren der Implementierung von Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
