---
title: Verwalten von Kosten und Besitz
description: Die Informationen in diesem Artikel helfen Ihnen beim Optimieren der Kosten und beim Festlegen des Besitzes in Ihrer gesamten Umgebung.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: a62ba95203ffea3162f7e79360bd36eb0f8f9733
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397601"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Governance der Azure DevTest Labs-Infrastruktur – Verwalten der Kosten und des Besitzes
Kosten und Besitz sind zentrale Überlegungen beim Aufbau Ihrer Entwicklungs- und Testumgebungen. In diesem Abschnitt finden Sie Informationen, die Ihnen beim Optimieren der Kosten und beim Festlegen des Besitzes in Ihrer gesamten Umgebung helfen.

## <a name="optimize-for-cost"></a>Optimieren für Kosten

### <a name="question"></a>Frage
Wie kann ich in meiner DevTest Labs-Umgebung für die Kosten optimieren?

### <a name="answer"></a>Antwort
Mehrere integrierte Features von DevTest Labs helfen Ihnen bei der Kostenoptimierung. Informationen zum Beschränken der Benutzeraktivitäten finden Sie in den Artikeln [zur Kostenverwaltung und zu Schwellenwerten](devtest-lab-configure-cost-management.md) [und zu Richtlinien](devtest-lab-set-lab-policy.md). 

Wenn Sie DevTest Labs für Entwicklungs- und Testworkloads verwenden, sollten Sie den [Enterprise Dev/Test-Abonnementvorteil](https://azure.microsoft.com/offers/ms-azr-0148p/) im Rahmen Ihres Enterprise Agreements in Erwägung ziehen. Wenn Sie ein Kunde mit nutzungsbasierter Zahlung sind, sollten Sie stattdessen das [Dev/Test-Angebot mit nutzungsbasierter Zahlung](https://azure.microsoft.com/offers/ms-azr-0023p/) berücksichtigen.

Dieser Ansatz bietet mehrere Vorteile:

- Niedrigere Dev/Test-Tarife für virtuelle Windows-Computer, Clouddienste, HDInsight, App Service und Logic Apps
- Großartige Enterprise Agreement-Tarife (EA) für andere Azure-Dienste
- Zugriff auf exklusive Dev/Test-Images im Katalog, einschließlich Windows 8.1 und Windows 10
 
Nur aktive Visual Studio-Abonnenten (Standardabonnements oder Jahres- und Monatscloudabonnements) können die im Rahmen eines Enterprise Dev/Test-Abonnements ausgeführten Azure-Dienste nutzen. Endbenutzer können jedoch auch auf die Anwendung zugreifen, um Feedback zu geben oder Akzeptanztests durchzuführen. Sie können Ressourcen in diesem Abonnement nur zum Entwickeln und Testen von Anwendungen verwenden. Es gibt keine Verfügbarkeitsgarantie.

Wenn Sie das DevTest-Angebot nutzen möchten, verwenden Sie diesen Vorteil ausschließlich für das Entwickeln und Testen Ihrer Anwendungen. Die Nutzung im Rahmen des Abonnements unterliegt keiner Vereinbarung zum Servicelevel (SLA) mit finanzieller Absicherung, mit Ausnahme der Nutzung von Azure DevOps und HockeyApp.

## <a name="define-role-based-access-across-your-organization"></a>Definieren von rollenbasiertem Zugriff in Ihrer gesamten Organisation
### <a name="question"></a>Frage
Wie definiere ich die rollenbasierte Zugriffssteuerung in Azure für meine DevTest Labs-Umgebungen, um sicherzustellen, dass die IT-Abteilung die Steuerung übernimmt, während Entwickler und Tester ihre Arbeit erledigen können? 

### <a name="answer"></a>Antwort
Es gibt ein allgemeines Muster, aber die Details sind von Ihrer Organisation abhängig.

Die zentrale IT-Abteilung sollte nur das Nötigste verwalten und den Projekt- und Anwendungsteams das erforderliche Maß an Kontrolle ermöglichen. In der Regel bedeutet dies, dass die IT-Zentrale der Besitzer des Abonnements ist und die wichtigsten IT-Funktionen übernimmt, z.B. Netzwerkkonfigurationen. Die Gruppe von **Besitzern** eines Abonnement sollte klein sein. Diese Besitzer können weitere Besitzer vorschlagen, wenn dies erforderlich ist, oder Richtlinien auf Abonnementebene anwenden, z. B. „Keine öffentliche IP-Adresse“.

Möglicherweise gibt es eine Teilmenge von Benutzern, die Zugriff auf ein Abonnement benötigen, z.B. der Support auf erster und zweiter Ebene. In diesem Fall wird empfohlen, diesen Benutzern Zugriff auf der Ebene **Mitwirkender** zu gewähren, sodass sie die Ressourcen verwalten können, aber keinen Benutzerzugriff erhalten oder Richtlinien anpassen können.

DevTest Labs-Ressourcenbesitzer sollten sich in der Nähe des Projekts oder Anwendungsteams befinden. Diese Besitzer verstehen die Computer- und Softwareanforderungen. In den meisten Organisationen ist der Besitzer dieser DevTest Labs-Ressource der Projekt- oder Entwicklungsleiter. Dieser Besitzer kann Benutzer und Richtlinien in der Lab-Umgebung und alle VMs in der DevTest Labs-Umgebung verwalten.

Fügen Sie der Rolle „DevTest Labs-Benutzer“ Mitglieder des Projekt- und Anwendungsteams hinzu. Diese Benutzer können VMs erstellen (entsprechend den Richtlinien auf Lab- und Abonnementebene). Benutzer können auch ihre eigenen VMs verwalten, aber keine VMs, die anderen Benutzern gehören.

Weitere Informationen finden Sie unter [Azure-Unternehmensgerüst – präskriptive Abonnementgovernance](/azure/architecture/cloud-adoption/appendix/azure-scaffold).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Unternehmensrichtlinie und Compliance](devtest-lab-guidance-governance-policy-compliance.md).
