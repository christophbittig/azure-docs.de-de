---
title: Azure Lighthouse und verwaltete Azure-Anwendungen
description: Verstehen Sie, wie Azure Lighthouse und verwaltete Azure-Anwendungen gemeinsam verwendet werden können.
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 629f9f1f6b6db69816d26cc7eed1e305dd55923b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768570"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse und verwaltete Azure-Anwendungen

Sowohl verwaltete Azure-Anwendungen als auch Azure Lighthouse funktionieren, indem sie einem Dienstanbieter den Zugriff auf Ressourcen ermöglichen, die sich im Mandanten des Kunden befinden. Die Unterschiede in der Arbeitsweise, die Szenarien, die sie ermöglichen und die Möglichkeiten zur gemeinsamen Verwendung zu verstehen, kann nützlich sein.

> [!TIP]
> Zwar beziehen wir uns in diesem Thema auf Dienstanbieter und Kunden, doch können [Unternehmen, die mehrere Mandanten verwalten](enterprise.md), denselben Prozess und dieselben Tools verwenden.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Vergleich von Azure Lighthouse und verwalteten Azure-Anwendungen

Diese Tabelle veranschaulicht einige allgemeine Unterschiede, die sich auf Ihre Wahl von Azure Lighthouse oder verwalteten Azure-Anwendungen für einen bestimmten Zweck auswirken können. Wie unten erwähnt, können Sie auch Lösungen entwerfen, in denen beide gemeinsam verwendet werden.

|Aspekt  |Azure Lighthouse  |Verwaltete Azure-Anwendungen  |
|---------|---------|---------|
|Typischer Benutzer     |Dienstanbieter oder Unternehmen, die mehrere Mandanten verwalten         |Unabhängige Softwareanbieter (ISVs)         |
|Umfang des mandantenübergreifenden Zugriffs     |Abonnement(s) oder Ressourcengruppe(n)         |Ressourcengruppe (mit einer einzelnen Anwendung als Bereich)         |
|In Azure Marketplace zu erwerben     |Nein (Angebote können auf Azure Marketplace veröffentlicht werden, Kunden werden jedoch separat abgerechnet)        |Ja         |
|IP-Schutz     |Ja (IP kann im Mandanten des Dienstanbieters verbleiben)        |Ja (entwurfsbedingt, die Ressourcengruppe ist an Kunden gebunden)         |
|Ablehnungszuweisungen     |Nein         |Ja        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

Mit [Azure Lighthouse](../overview.md) kann ein Dienstanbieter eine Vielzahl verschiedener Verwaltungsaufgaben direkt im Abonnement eines Kunden (oder in einer Ressourcengruppe) ausführen. Dieser Zugriff wird durch eine logische Projektion erreicht, die es dem Dienstanbieter gestattet, sich bei seinem eigenen Mandanten anzumelden und auf Ressourcen zuzugreifen, die zum Mandanten des Kunden gehören. Der Kunde kann bestimmen, welche Abonnements oder Ressourcengruppen an den Dienstanbieter delegiert werden sollen, und der Kunde behält vollständigen Zugriff auf diese Ressourcen. Er kann den Zugriff des Dienstanbieters auch jederzeit entfernen.

Um Azure Lighthouse zu verwenden, wird ein Onboarding der Kunden durchgeführt, entweder durch [Bereitstellen von ARM-Vorlagen](../how-to/onboard-customer.md) oder über ein [Angebot für verwaltete Dienste in Azure Marketplace](managed-services-offers.md). Sie können Ihre Auswirkungen auf Kundenbindungen nachverfolgen, indem Sie [Ihre Partner-ID verknüpfen](../how-to/partner-earned-credit.md).

Azure Lighthouse wird in der Regel verwendet, wenn ein Dienstanbieter Verwaltungsaufgaben für einen Kunden kontinuierlich ausführen soll. Weitere Informationen zur Funktionsweise von Azure Lighthouse auf technischer Ebene finden Sie unter [Azure Lighthouse Architektur](architecture.md).

### <a name="azure-managed-applications"></a>Verwaltete Azure-Anwendungen

[Verwaltete Azure-Anwendungen](../../azure-resource-manager/managed-applications/overview.md) erlauben es einem Dienstanbieter oder ISV, Cloudlösungen anzubieten, die von den Kunden einfach bereitgestellt und in ihren eigenen Abonnements verwendet werden können.

Bei einer verwalteten Anwendung werden die von der Anwendung verwendeten Ressourcen in einer Ressourcengruppe gebündelt und bereitgestellt, die vom Herausgeber verwaltet wird. Diese Ressourcengruppe befindet sich zwar im Abonnement des Kunden, eine Identität im Mandanten des Herausgebers hat jedoch Zugriff darauf. Der ISV verwaltet und pflegt die verwaltete Anwendung weiterhin, während der Kunde keinen direkten Zugriff besitzt, um in der Ressourcengruppe zu arbeiten, oder jeglichen Zugriff auf deren Ressourcen.

Verwaltete Anwendungen unterstützen [angepasste Azure-Portal-Erfahrungen](../../azure-resource-manager/managed-applications/concepts-view-definition.md) und die [Integration in benutzerdefinierte Anbieter](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Diese Optionen können verwendet werden, um eine stärker angepasste und integrierte Erfahrung bereitzustellen, sodass Kunden einige Verwaltungsaufgaben leichter selbst ausführen können.

Verwaltete Anwendungen können [im Azure Marketplace veröffentlicht werden](../../marketplace/azure-app-offer-setup.md), entweder als privates Angebot für die Verwendung durch einen bestimmten Kunden oder als öffentliches Angebot, das von mehreren Kunden erworben werden kann. Sie können auch Benutzern in Ihrer Organisation bereitgestellt werden, indem [verwaltete Anwendungen in Ihrem Dienstkatalog veröffentlicht werden](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). Sie können sowohl Dienstkatalog- als auch Marketplace-Instanzen mithilfe von ARM-Vorlagen bereitstellen, die den eindeutigen Bezeichner eines kommerziellen Marketplace-Partners zum Nachverfolgen der [Zuordnung der Nutzung durch Kunden](../../marketplace/azure-partner-customer-usage-attribution.md) enthalten können.

Verwaltete Azure-Anwendungen werden in der Regel für einen bestimmten Kundenbedarf verwendet, was durch eine sofort einsatzbereite Lösung erzielt werden kann, die vollständig verwaltet vom Dienstanbieter verwaltet wird.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Gemeinsame Verwendung von Azure Lighthouse und verwalteten Azure-Anwendungen

Während Azure Lighthouse und verwaltete Azure-Anwendungen verschiedene Zugriffsmechanismen verwenden, um unterschiedliche Ziele zu erreichen, gibt es möglicherweise Szenarien, in denen es für einen Dienstanbieter sinnvoll ist, beide für denselben Kunden zu verwenden.

Angenommen, ein Kunde möchte von einem Dienstanbieter verwaltete Dienste durch Azure Lighthouse geliefert bekommen, damit er Einblick in die Aktionen des Partners und gleichzeitig die fortwährende Kontrolle über sein delegiertes Abonnement erhält. Der Dienstanbieter möchte jedoch möglicherweise nicht, dass der Kunde auf bestimmte Ressourcen zugreifen kann, die im Mandanten des Kunden gespeichert werden, oder er möchte keine angepassten Aktionen für diese Ressourcen zulassen. Um diese Ziele zu erreichen, kann der Dienstanbieter ein privates Angebot als verwaltete Anwendung veröffentlichen. Die verwaltete Anwendung kann eine Ressourcengruppe enthalten, die im Mandanten des Kunden bereitgestellt wird, auf die der Kunden jedoch nicht direkt zugreifen kann.

Kunden können auch an verwalteten Anwendungen von mehreren Dienstanbietern interessiert sein, unabhängig davon, ob Sie auch verwaltete Dienste über Azure Lighthouse von einem dieser Dienstanbieter verwenden. Außerdem können Partner im CSP-Programm (Cloud Solution Provider) bestimmte verwaltete Anwendungen weiterverkaufen, die von anderen ISVs für Kunden veröffentlicht werden, die sie über Azure Lighthouse unterstützen. Mit einer Vielzahl von Optionen können Dienstanbieter das richtige Gleichgewicht finden, um die Anforderungen Ihrer Kunden zu erfüllen und gleichzeitig den Zugriff auf Ressourcen einzuschränken, wo erforderlich.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [verwalteten Azure-Anwendungen](../../azure-resource-manager/managed-applications/overview.md).
- Erfahren Sie mehr über das [Onboarding eines Abonnements in Azure Lighthouse](../how-to/onboard-customer.md).
- Erfahren Sie mehr über [ISV-Szenarien mit Azure Lighthouse](isv-scenarios.md).