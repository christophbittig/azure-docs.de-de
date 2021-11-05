---
title: Grundlegendes zu Sicherheitsrichtlinien, Initiativen und Empfehlungen in Microsoft Defender für Cloud
description: Hier erfahren Sie mehr über Sicherheitsrichtlinien, Initiativen und Empfehlungen in Microsoft Defender für Cloud.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 00d73987acd0e5137913872138d6f78ac18e9bdf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083971"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Was sind Sicherheitsrichtlinien, Initiativen und Empfehlungen?

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud wendet Sicherheitsinitiativen auf Ihre Abonnements an. Diese Initiativen enthalten mindestens eine Sicherheitsrichtlinie. Jede dieser Richtlinien führt zu einer Sicherheitsempfehlung für die Verbesserung des Sicherheitsstatus. Auf dieser Seite werden die einzelnen Konzepte ausführlich beschrieben.


## <a name="what-is-a-security-policy"></a>Was ist eine Sicherheitsrichtlinie?

Eine Azure-Richtliniendefinition, die in Azure Policy erstellt wird, ist eine Regel für bestimmte Sicherheitsbedingungen, die Sie steuern möchten. Mit integrierten Definitionen kann beispielsweise gesteuert werden, welche Art von Ressourcen bereitgestellt werden kann, oder es kann die Verwendung von Tags für alle Ressourcen erzwungen werden. Sie können auch Ihre eigenen benutzerdefinierten Richtliniendefinitionen erstellen.

Um diese Richtliniendefinitionen (integriert oder benutzerdefiniert) implementieren zu können, müssen Sie sie zuweisen. Sie können diese Richtlinien über das Azure-Portal, PowerShell oder die Azure CLI zuweisen. Richtlinien können über Azure Policy aktiviert oder deaktiviert werden.

In Azure Policy gibt es unterschiedliche Arten von Richtlinien. Defender für Cloud verwendet hauptsächlich Überwachungsrichtlinien, mit denen bestimmte Bedingungen und Konfigurationen überprüft werden und anschließend die Konformität gemeldet wird. Außerdem gibt es Erzwingungsrichtlinien, die zum Anwenden von sicheren Einstellungen verwendet werden können.

## <a name="what-is-a-security-initiative"></a>Was ist eine Sicherheitsinitiative?

Eine Azure Policy ist eine Sammlung mit Azure-Richtliniendefinitionen oder -Regeln, die gruppiert werden, um ein bestimmtes Ziel zu erreichen bzw. einen bestimmten Zweck zu erfüllen. Azure-Initiativen vereinfachen die Verwaltung Ihrer Richtlinien, indem Richtlinien logisch unter einem einzelnen Element gruppiert werden.

Eine Sicherheitsinitiative definiert die gewünschte Konfiguration Ihrer Workloads und trägt zur Erfüllung unternehmensbezogener oder gesetzlicher Sicherheitsanforderungen bei.

Wie Sicherheitsrichtlinien werden auch Defender für Cloud-Initiativen in Azure Policy erstellt. Sie können [Azure Policy](../governance/policy/overview.md) nutzen, um Ihre Richtlinien zu verwalten und Initiativen zu erstellen und mehreren Abonnements oder für ganze Verwaltungsgruppen zuzuweisen.

Die Standardinitiative, die jedem Abonnement in Microsoft Defender für Cloud automatisch zugewiesen wird, ist der Azure-Sicherheitsvergleichstest (Azure Security Benchmark). Bei diesem Vergleichstest handelt es sich um einen von Microsoft erstellten Satz mit Azure-spezifischen Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit. Informieren Sie sich über [Azure Security-Vergleichstests](/security/benchmark/azure/introduction).

Defender für Cloud verfügt über die folgenden Optionen für die Arbeit mit Sicherheitsinitiativen und -richtlinien:

- **Anzeigen und Bearbeiten der integrierten Standardinitiative**: Wenn Sie Defender für Cloud aktivieren, wird die Initiative mit dem Namen „Azure Security Benchmark“ (Azure-Sicherheitsvergleichstest) automatisch allen in Defender für Cloud registrierten Abonnements zugewiesen. Zum Anpassen dieser Initiative können Sie die einzelnen enthaltenen Richtlinien aktivieren bzw. deaktivieren, indem Sie die Parameter einer Richtlinie bearbeiten. Sehen Sie sich die Liste der [integrierten Sicherheitsrichtlinien](./policy-reference.md) an, um die standardmäßig verfügbaren Optionen zu verstehen.

- **Hinzufügen eigener benutzerdefinierter Initiativen**: Wenn Sie die auf Ihr Abonnement angewendeten Sicherheitsinitiativen anpassen möchten, können Sie dies in Defender für Cloud durchführen. Sie erhalten dann Empfehlungen, wenn Ihre Computer Ihre erstellten Richtlinien nicht einhalten. Anweisungen zum Erstellen und Zuweisen von benutzerdefinierten Richtlinien finden Sie unter [Erstellen von benutzerdefinierten Sicherheitsinitiativen und -richtlinien](custom-security-policies.md).

- **Hinzufügen von Standards für die Einhaltung gesetzlicher Bestimmungen als Initiativen**: Im Defender für Cloud-Dashboard für die Einhaltung gesetzlicher Bestimmungen wird der Status aller Bewertungen in Ihrer Umgebung im Zusammenhang mit einem bestimmten Standard oder einer Verordnung (z. B. Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020) angezeigt. Weitere Informationen finden Sie unter [Verbessern der Einhaltung gesetzlicher Vorschriften](regulatory-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Was ist eine Sicherheitsempfehlung?

Mithilfe der Richtlinien analysiert Defender für Cloud regelmäßig den Konformitätsstatus Ihrer Ressourcen, um potenzielle Fehlkonfigurationen und Schwachstellen in Bezug auf die Sicherheit zu identifizieren. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Probleme beheben können. Empfehlungen sind das Ergebnis der Bewertung Ihrer Ressourcen anhand der relevanten Richtlinien und der Identifizierung von Ressourcen, von denen Ihre definierten Anforderungen nicht erfüllt werden.

Defender für Cloud erstellt die Sicherheitsempfehlungen basierend auf den ausgewählten Initiativen. Wenn eine Richtlinie Ihrer Initiative mit Ihren Ressourcen verglichen wird und mindestens eine nicht konforme Ressource gefunden wird, wird hierfür in Defender für Cloud eine Empfehlung angezeigt.

Empfehlungen sind Maßnahmen, die Sie ergreifen sollten, um Ihre Ressourcen zu härten und zu schützen. Jede Empfehlung enthält die folgenden Informationen:

- Eine kurze Problembeschreibung
- Die Schritte zur Bereinigung, die zum Implementieren der Empfehlung ausgeführt werden müssen
- Die betroffenen Ressourcen

Dies funktioniert in der Praxis wie folgt:

1. Der Azure-Sicherheitsvergleichstest ist eine ***Initiative***, die Anforderungen enthält.

    Beispielsweise müssen Azure Storage-Konten den Netzwerkzugriff einschränken, um die Angriffsfläche zu verringern.

1. Die Initiative umfasst mehrere ***Richtlinien***, die jeweils einen bestimmten Ressourcentyp erfordern. Diese Richtlinien erzwingen die Anforderungen in der Initiative. 

    Im genannten Beispiel wird die Speicheranforderung mit der Richtlinie „Speicherkonten müssen den Netzwerkzugriff mithilfe von VNET-Regeln einschränken“ erzwungen.

1. Microsoft Defender für Cloud führt eine ständige Bewertung Ihrer verbundenen Abonnements durch. Wenn eine Ressource gefunden wird, die eine Richtlinie nicht erfüllt, wird eine ***Empfehlung*** angezeigt, diese Situation zu beheben und die Sicherheit von Ressourcen zu härten, die Ihre Sicherheitsanforderungen nicht erfüllen.

    Wenn also beispielsweise ein Azure Storage-Konto in einem Ihrer geschützten Abonnements nicht durch VNET-Regeln geschützt ist, wird die Empfehlung angezeigt, diese Ressourcen zu härten. 

Somit enthält eine Initiative (1) Richtlinien (2), die umgebungsspezifische Empfehlungen (3) generieren.

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Anzeigen der Beziehung zwischen einer Empfehlung und einer Richtlinie

Wie oben bereits erwähnt, basieren die integrierten Empfehlungen von Defender für Cloud auf dem Azure-Sicherheitsvergleichstest. Fast jede Empfehlung weist eine zugrunde liegende Richtlinie auf, die von einer Anforderung im Vergleichstest abgeleitet ist.

Wenn Sie die Details einer Empfehlung überprüfen, ist es häufig hilfreich, die zugrunde liegende Richtlinie zu kennen. Verwenden Sie für jede Empfehlung, die von einer Richtlinie unterstützt wird, den Link **Richtliniendefinition anzeigen** auf der Seite mit den Empfehlungsdetails, um direkt zum Azure Policy-Eintrag für die entsprechende Richtlinie zu gelangen:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link zur Azure Policy-Seite für eine bestimmte Richtlinie, die eine Empfehlung unterstützt":::

Verwenden Sie diesen Link, um die Richtliniendefinition anzuzeigen und die Bewertungslogik zu überprüfen. 

In der Liste mit den Empfehlungen in unserem [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) finden Sie auch Links zu den Seiten mit Richtliniendefinitionen:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Zugriff auf die Azure Policy-Seite für eine bestimmte Richtlinie direkt über die Referenzseite mit den Defender für Cloud-Empfehlungen":::


## <a name="next-steps"></a>Nächste Schritte

Diese Seite enthält eine allgemeine Beschreibung der grundlegenden Konzepte und Beziehungen zwischen Richtlinien, Initiativen und Empfehlungen. Verwandte Informationen

- [Erstellen von benutzerdefinierten Initiativen](custom-security-policies.md)
- [Deaktivieren von Sicherheitsrichtlinien zum Deaktivieren von Empfehlungen](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung](../governance/policy/tutorials/create-and-manage.md)
