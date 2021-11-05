---
title: Roadmap zur Defender für Cloud-Bereitschaft | Microsoft-Dokumentation
description: Dieses Dokument enthält eine Roadmap zur Bereitschaft für die Nutzung von Defender für Cloud.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 045dbd56f1177f334916850f481c7fa4f9f48f4c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009508"
---
# <a name="defender-for-cloud-readiness-roadmap"></a>Roadmap zur Defender für Cloud-Bereitschaft

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieses Dokument enthält eine Roadmap zur Bereitschaft, die Ihnen als Hilfe bei den ersten Schritten mit Defender für Cloud dient.

## <a name="understanding-defender-for-cloud"></a>Grundlegendes zu Defender für Cloud
Defender für Cloud ermöglicht eine einheitliche Sicherheitsverwaltung und erweiterten Schutz vor Bedrohungen für Workloads, die in Azure, lokal und in anderen Clouds ausgeführt werden. 

Verwenden Sie für die ersten Schritte mit Defender für Cloud die unten angegebenen Ressourcen.

Artikel
- [Einführung in Defender für Cloud](defender-for-cloud-introduction.md)
- [Schnellstartanleitung für Defender für Cloud](get-started.md)

Videos
- [Kurzes Einführungsvideo](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [Übersicht über die Präventions-, Erkennungs- und Reaktionsfunktionen von Defender für Cloud](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>Planung und Betrieb

Für eine optimale Nutzung von Defender für Cloud sollten Sie verstehen, wie der Dienst von den verschiedenen Personen oder Teams in Ihrer Organisation verwendet wird, um die Anforderungen in puncto sicherer Betrieb, Überwachung, Governance und Reaktionen auf Vorfälle zu erfüllen.

Verwenden Sie die folgenden Ressourcen als Hilfe bei der Planung und beim Betrieb.

- [Defender für Cloud: Leitfaden zu Planung und Betrieb](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-defender-for-cloud"></a>Durchführen des Onboardings von Computern für Defender für Cloud
Defender für Cloud erkennt automatisch alle Azure-Abonnements oder -Arbeitsbereiche, die nicht durch Azure Defender geschützt sind. Hierzu zählen unter anderem Azure-Abonnements mit Defender für Cloud Free sowie Arbeitsbereiche, für die die Sicherheitslösung nicht aktiviert ist.

Verwenden Sie die folgenden Ressourcen als Hilfe während des Onboarding-Prozesses.

- [Integrieren Azure-fremder Computer](quickstart-onboard-machines.md)
- [Defender für Cloud Hybrid – Übersicht](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-defender-for-cloud"></a>Beheben von Sicherheitsproblemen mit Defender für Cloud
Defender für Cloud erfasst, analysiert und bündelt automatisch Protokolldaten von Ihren Azure-Ressourcen, aus dem Netzwerk und aus verbundenen Partnerlösungen, z. B. Firewalls und Endpunktschutz, um echte Bedrohungen zu erkennen und die Anzahl von False Positives zu reduzieren.

Verwenden Sie die folgenden Ressourcen als Hilfe beim Verwalten von Sicherheitswarnungen und Schützen Ihrer Ressourcen.

Artikel    
- [Schützen Ihres Netzwerks in Defender für Cloud](./protect-network-resources.md)
- [Schützen des Azure SQL-Diensts und der zugehörigen Daten in Defender für Cloud](./implement-security-recommendations.md)


Video    
- [Beheben von Sicherheitsproblemen mit Defender für Cloud](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="defender-for-cloud-for-incident-response"></a>Defender für Cloud für die Reaktion auf Vorfälle
Zur Reduzierung von Kosten und Schäden ist es wichtig, dass ein Plan zur Reaktion auf Vorfälle vorhanden ist, bevor es zu einem Angriff kommt. Sie können Defender für Cloud in verschiedenen Phasen einer Reaktion auf Vorfälle verwenden.

Informieren Sie sich unter den folgenden Ressourcen darüber, wie Defender für Cloud in Ihren Prozess zur Reaktion auf Vorfälle integriert werden kann.

Videos    
* [Defender für Cloud für die Reaktion auf Vorfälle](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Respond quickly to threats with next-generation security operation, and investigation](https://youtu.be/e8iFCz5RM4g) (Schnelles Reagieren auf Bedrohungen mit Sicherheitsabläufen und -untersuchungen der nächsten Generation)

Artikel    
* [Verwenden von Defender für Cloud für die Reaktion auf Vorfälle](./tutorial-security-incident.md)
* [Nutzen der Automatisierung zum Reagieren auf Defender für Cloud-Trigger](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>Erweiterter Cloudschutz

Für Azure-VMs können erweiterte Funktionen für die Cloudverteidigung in Defender für Cloud genutzt werden. Zu diesen Funktionen gehören auch der bedarfsabhängige Zugriff auf virtuelle Computer (VMs) und die adaptive Anwendungssteuerung.

Informieren Sie sich unter den folgenden Ressourcen darüber, wie Sie diese Funktionen in Defender für Cloud verwenden können.

Videos    
* [Defender für Cloud: Just-In-Time-VM-Zugriff](https://youtu.be/UOQb2FcdQnU)
* [Defender für Cloud: Adaptive Anwendungssteuerung](https://youtu.be/wWWekI1Y9ck)

Artikel    
* [Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features](./just-in-time-access-usage.md)
* [Adaptive Anwendungssteuerung in Defender für Cloud](./adaptive-application-controls.md)

## <a name="hands-on-activities"></a>Praktische Aktivitäten

* [Defender für Cloud: Praxislab](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Playbook zu Web Application Firewall (WAF) mit Empfehlungen in Defender für Cloud](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Defender für Cloud-Playbook: Sicherheitswarnungen](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Defender für Cloud-Dokumentation: Startseite](./index.yml)
* [Defender für Cloud-Dokumentation: REST-API-Seite](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Häufig gestellte Fragen (FAQ) zu Defender für Cloud](./faq-general.yml)
* [Seite mit der Preisübersicht](https://azure.microsoft.com/pricing/details/security-center/)
* [Azure-Identitätsverwaltung und Sicherheit der Zugriffssteuerung – Bewährte Methoden](../security/fundamentals/identity-management-best-practices.md)
* [Bewährte Methoden für die Netzwerksicherheit](../security/fundamentals/network-best-practices.md)
* [PaaS-Empfehlungen](../security/fundamentals/paas-deployments.md)
* [Compliance](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics-Kunden können jetzt ihre Hybrid Cloud-Workloads mit Defender für Cloud schützen](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)
