---
title: Informationen zu den Azure Service Fabric-Supportoptionen
description: Unterstützte Azure Service Fabric-Clusterversionen und Links zu Dateisupporttickets
author: erikadoyle
ms.topic: troubleshooting
ms.date: 5/17/2021
ms.author: edoyle
ms.custom: support-help-page
ms.openlocfilehash: 5228663dfa3a272d4d5aaa569d8a14689ddf7490
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467433"
---
# <a name="azure-service-fabric-support-options"></a>Supportoptionen für Azure Service Fabric

Wir haben eine Reihe von Optionen für Supportanfragen bereitgestellt, um die Anforderungen an die Verwaltung Ihrer Service Fabric-Cluster und Anwendungsworkloads zu erfüllen, abhängig von der Dringlichkeit des benötigten Supports und dem Schweregrad des Problems.

## <a name="self-help-troubleshooting"></a>Selbsthilfe zur Problembehandlung
<div class='icon is-large'>
    <img alt='Self help content' src='./media/logos/doc-logo.png'>
</div>

Dies ist das Material, auf das vom Kundensupportdienst beim Erstellen eines Tickets verwiesen wird, von Service Fabric Site Reliability Engineers, die auf einen Incident reagieren, und von Benutzern beim Ermitteln von Lösungen für aktive Systemprobleme.

Eine vollständige Liste der Inhalte zur Selbsthilfe zur Problembehandlung finden Sie unter [Service Fabric-Leitfäden zur Problembehandlung](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides).

## <a name="create-an-azure-support-request"></a>Erstellen einer Azure-Supportanfrage
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Um Probleme mit Ihrem in Azure ausgeführten Service Fabric-Clusters zu melden, öffnen Sie [im Azure-Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) oder [Microsoft-Supportportal](https://support.microsoft.com/oas/default.aspx?prid=16146) ein Supportticket.

Weitere Informationen:

- [Azure-Supportoptionen](https://azure.microsoft.com/support/plans/?b=16.44).
- [Microsoft Premier Support](https://support.microsoft.com/premier)

> [!Note]
> Auf Clustern, die auf einer Bronze-Zuverlässigkeitsebene oder in einem Cluster mit nur einem Knoten ausgeführt werden, können Sie nur Testworkloads ausführen. Wenn Sie Probleme mit einem Cluster mit Bronze-Zuverlässigkeit oder einem Cluster mit nur einem Knoten haben, wird Ihnen das Microsoft-Supportteam bei der Behebung des Problems helfen, aber keine Analyse der Grundursache durchführen. Weitere Informationen finden Sie unter [Zuverlässigkeitsmerkmale des Clusters](./service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster).
>
> Weitere Informationen darüber, was für einen produktionsbereiten Cluster erforderlich ist, finden Sie in der Checkliste [Produktionsbereitschaft](./service-fabric-production-readiness-checklist.md).

<a id="getlivesitesupportonprem"></a>

## <a name="create-a-support-request-for-standalone-service-fabric-clusters"></a>Erstellen einer Supportanfrage für eigenständige Service Fabric-Cluster
<div class='icon is-large'>
    <img alt='Azure support' src='./media/logos/azure-logo.png'>
</div>

Um Probleme mit Ihrem in lokalen oder anderen Clouds bereitgestellten Service Fabric-Cluster zu melden, erstellen Sie im [Microsoft-Supportportal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) ein Ticket für Professional Support.

Weitere Informationen:

- [Professional Support von Microsoft für lokale Bereitstellungen](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)
- [Microsoft Premier Support](https://support.microsoft.com/en-us/premier)

## <a name="post-a-question-to-microsoft-qa"></a>Stellen einer Frage bei Microsoft Q&A
<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/logos/microsoft-logo.png'>
</div>   

Erhalten Sie direkt von Microsoft-Technikern, von Azure Most Valuable Professionals (MVPs) und von Mitgliedern unserer Experten-Community Antworten auf Ihre Fragen zu Service Fabric.

[Microsoft Q&A](/answers/topics/azure-service-fabric.html) ist die Quelle, die für den Azure-Communitysupport empfohlen wird.

Wenn Sie beim Durchsuchen der Fragen und Antworten keine Lösung für Ihr Problem finden, übermitteln Sie eine neue Frage an Microsoft Q&A. Stellen Sie sicher, dass Sie Ihre Frage mit dem Tag [**azure-service-fabric**](/answers/topics/azure-service-fabric.html) posten. Hier finden Sie einige Tipps von Microsoft zum Formulieren von [hochwertiger Fragen](/answers/articles/24951/how-to-write-a-quality-question.html).

## <a name="open-a-github-issue"></a>Öffnen eines GitHub Issues
<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/logos/github-logo.png'>
</div>

Melden Sie Azure Service Fabric-Probleme in [Service Fabric GitHub](https://github.com/microsoft/service-fabric/issues). Dieses Repository dient zur Meldung und Nachverfolgung von Problemen sowie zum Senden kleiner Funktionsanforderungen, die sich auf Azure Service Fabric beziehen. **Verwenden Sie dieses Medium nicht zum Melden von Problemen mit der Livewebsite**.

## <a name="check-the-stackoverflow-forum"></a>Besuchen des StackOverflow-Forums
<div class='icon is-large'>
    <img alt='Stack Overflow' src='./media/logos/stack-overflow-logo.png'>
</div>

Das Tag `azure-service-fabric` für [StackOverflow][stackoverflow] eignet sich für allgemeine Fragen zur Funktionsweise der Plattform und zur Durchführung bestimmter Aufgaben.

## <a name="stay-informed-of-updates-and-new-releases"></a>Erhalten von Informationen zu Updates und neuen Releases

<div class='icon is-large'>
    <img alt='Stay informed' src='./media/logos/updates-logo.png'>
</div>

Informieren Sie sich unter [Azure-Updates](https://azure.microsoft.com/updates/?product=service-fabric) über wichtige Produktupdates, über die Roadmap sowie über Ankündigungen.

Die neuesten Releases und Updates der Service Fabric-Runtime und SDKs finden Sie unter [Service Fabric-Releases](release-notes.md).



## <a name="next-steps"></a>Nächste Schritte

[Unterstützte Service Fabric-Versionen](service-fabric-versions.md)

<!--references-->
[Microsoft Q&A question page]: /answers/topics/azure-service-fabric.html
[stackoverflow]: https://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/d365community/forum/e622b37a-2225-ec11-b6e6-000d3a4f0f84
[acom-docs]: ./index.yml
[sample-repos]: /samples/browse/?products=azure
