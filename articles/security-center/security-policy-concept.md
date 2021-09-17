---
title: Grundlegendes zu Sicherheitsrichtlinien, Initiativen und Empfehlungen in Azure Security Center
description: Enthält grundlegende Informationen zu Sicherheitsrichtlinien, Initiativen und Empfehlungen in Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: e065aa7d3d269632c77a810917ec913c43e518db
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605272"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?

Security Center wendet Sicherheitsinitiativen auf Ihre Abonnements an. Diese Initiativen enthalten mindestens eine Sicherheitsrichtlinie. Jede dieser Richtlinien führt zu einer Sicherheitsempfehlung für die Verbesserung des Sicherheitsstatus. Auf dieser Seite werden die einzelnen Konzepte ausführlich beschrieben.


## <a name="what-is-a-security-policy"></a>Was ist eine Sicherheitsrichtlinie?

Eine Azure-Richtliniendefinition, die in Azure Policy erstellt wird, ist eine Regel für bestimmte Sicherheitsbedingungen, die Sie steuern möchten. Mit integrierten Definitionen kann beispielsweise gesteuert werden, welche Art von Ressourcen bereitgestellt werden kann, oder es kann die Verwendung von Tags für alle Ressourcen erzwungen werden. Sie können auch Ihre eigenen benutzerdefinierten Richtliniendefinitionen erstellen.

Um diese Richtliniendefinitionen (integriert oder benutzerdefiniert) implementieren zu können, müssen Sie sie zuweisen. Sie können diese Richtlinien über das Azure-Portal, PowerShell oder die Azure CLI zuweisen.

In Azure Policy gibt es unterschiedliche Arten von Richtlinien. In Security Center werden hauptsächlich Überwachungsrichtlinien verwendet, mit denen bestimmte Bedingungen und Konfigurationen überprüft werden und anschließend die Konformität gemeldet wird. Außerdem gibt es Erzwingungsrichtlinien, die zum Anwenden von sicheren Einstellungen verwendet werden können.

## <a name="what-is-a-security-initiative"></a>Was ist eine Sicherheitsinitiative?

Eine Azure Policy ist eine Sammlung mit Azure-Richtliniendefinitionen oder -Regeln, die gruppiert werden, um ein bestimmtes Ziel zu erreichen bzw. einen bestimmten Zweck zu erfüllen. Azure-Initiativen vereinfachen die Verwaltung Ihrer Richtlinien, indem Richtlinien logisch unter einem einzelnen Element gruppiert werden.

Eine Sicherheitsinitiative definiert die gewünschte Konfiguration Ihrer Workloads und trägt zur Erfüllung unternehmensbezogener oder gesetzlicher Sicherheitsanforderungen bei.

Wie Sicherheitsrichtlinien auch, werden Security Center-Initiativen in Azure Policy erstellt. Sie können [Azure Policy](../governance/policy/overview.md) nutzen, um Ihre Richtlinien zu verwalten und Initiativen zu erstellen und mehreren Abonnements oder für ganze Verwaltungsgruppen zuzuweisen.

Die Standardinitiative, die jedem Abonnement in Azure Security Center automatisch zugewiesen wird, ist der Vergleichstest für die Azure-Sicherheit (Azure Security Benchmark). Bei diesem Vergleichstest handelt es sich um einen von Microsoft erstellten Satz mit Azure-spezifischen Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit. Informieren Sie sich über [Azure Security-Vergleichstests](/security/benchmark/azure/introduction).

Security Center verfügt über die folgenden Optionen für die Arbeit mit Sicherheitsinitiativen und -richtlinien:

- **Anzeigen und Bearbeiten der integrierten Standardinitiative**: Wenn Sie Security Center aktivieren, wird die Initiative mit dem Namen „Azure Security Benchmark“ (Azure Security-Vergleichstest) automatisch allen in Security Center registrierten Abonnements zugewiesen. Zum Anpassen dieser Initiative können Sie die einzelnen enthaltenen Richtlinien aktivieren bzw. deaktivieren, indem Sie die Parameter einer Richtlinie bearbeiten. Sehen Sie sich die Liste der [integrierten Sicherheitsrichtlinien](./policy-reference.md) an, um die standardmäßig verfügbaren Optionen zu verstehen.

- **Hinzufügen eigener benutzerdefinierter Initiativen:** Wenn Sie die auf Ihr Abonnement angewendeten Sicherheitsinitiativen anpassen möchten, können Sie dies in Security Center durchführen. Sie erhalten dann Empfehlungen, wenn Ihre Computer Ihre erstellten Richtlinien nicht einhalten. Anweisungen zum Erstellen und Zuweisen von benutzerdefinierten Richtlinien finden Sie unter [Erstellen von benutzerdefinierten Sicherheitsinitiativen und -richtlinien](custom-security-policies.md).

- **Hinzufügen von Standards für die Einhaltung gesetzlicher Bestimmungen als Initiativen:** Im Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen wird der Status aller Bewertungen in Ihrer Umgebung im Zusammenhang mit einem bestimmten Standard oder einer Verordnung (z. B. Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020) angezeigt. Weitere Informationen finden Sie unter [Verbessern der Einhaltung gesetzlicher Vorschriften](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Was ist eine Sicherheitsempfehlung?

Mithilfe der Richtlinien analysiert Security Center regelmäßig den Konformitätsstatus Ihrer Ressourcen, um potenzielle Fehlkonfigurationen und Schwachstellen in Bezug auf die Sicherheit zu identifizieren. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Probleme beheben können. Empfehlungen sind das Ergebnis der Bewertung Ihrer Ressourcen anhand der relevanten Richtlinien und der Identifizierung von Ressourcen, von denen Ihre definierten Anforderungen nicht erfüllt werden.

Security Center erstellt die Sicherheitsempfehlungen basierend auf den ausgewählten Initiativen. Wenn eine Richtlinie Ihrer Initiative mit Ihren Ressourcen verglichen wird und mindestens eine nicht konforme Ressource gefunden wird, wird hierfür in Security Center eine Empfehlung angezeigt.

Empfehlungen sind Maßnahmen, die Sie ergreifen sollten, um Ihre Ressourcen zu härten und zu schützen. Jede Empfehlung enthält die folgenden Informationen:

- Eine kurze Problembeschreibung
- Die Schritte zur Bereinigung, die zum Implementieren der Empfehlung ausgeführt werden müssen
- Die betroffenen Ressourcen

Dies funktioniert in der Praxis wie folgt:

1. Der Vergleichstest für die Azure-Sicherheit ist eine ***Initiative***.
1. Sie enthält eine ***Richtlinie***, mit der für alle Azure Storage-Konten der Netzwerkzugriff erzwungen wird, um die Angriffsfläche zu verkleinern. Diese Richtlinie hat die Bezeichnung „Speicherkonten müssen den Netzwerkzugriff mithilfe von VNET-Regeln einschränken“. Sie kann über Azure Policy deaktiviert oder aktiviert werden.
1. Falls Azure Security Center unter einem Ihrer geschützten Abonnements ein Azure Storage-Konto findet, wird es bewertet, um zu ermitteln, ob es durch VNET-Regeln geschützt ist. Wenn nicht, wird eine ***Empfehlung*** zum Beheben dieser Situation und Durchführen einer Sicherheitshärtung für diese Ressourcen angezeigt. 

Eine Initiative (1) enthält also Richtlinien (2), die in bestimmten Fällen Empfehlungen generieren (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Anzeigen der Beziehung zwischen einer Empfehlung und einer Richtlinie

Wie oben bereits erwähnt, basieren die integrierten Empfehlungen von Security Center auf dem Vergleichstest für die Azure-Sicherheit. Fast jede Empfehlung weist eine zugrunde liegende Richtlinie auf, die von einer Anforderung im Vergleichstest abgeleitet ist.

Wenn Sie die Details einer Empfehlung überprüfen, ist es häufig hilfreich, die zugrunde liegende Richtlinie zu kennen. Verwenden Sie für jede Empfehlung, die von einer Richtlinie unterstützt wird, den Link **Richtliniendefinition anzeigen** auf der Seite mit den Empfehlungsdetails, um direkt zum Azure Policy-Eintrag für die entsprechende Richtlinie zu gelangen:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link zur Azure Policy-Seite für eine bestimmte Richtlinie, die eine Empfehlung unterstützt":::

Verwenden Sie diesen Link, um die Richtliniendefinition anzuzeigen und die Bewertungslogik zu überprüfen. 

In der Liste mit den Empfehlungen in unserem [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) finden Sie auch Links zu den Seiten mit Richtliniendefinitionen:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Zugriff auf die Azure Policy-Seite für eine bestimmte Richtlinie direkt über die Seite mit der Azure Security Center-Empfehlungsreferenz":::


## <a name="next-steps"></a>Nächste Schritte

Diese Seite enthält eine allgemeine Beschreibung der grundlegenden Konzepte und Beziehungen zwischen Richtlinien, Initiativen und Empfehlungen. Verwandte Informationen

- [Erstellen von benutzerdefinierten Initiativen](custom-security-policies.md)
- [Deaktivieren von Sicherheitsrichtlinien zum Deaktivieren von Empfehlungen](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)