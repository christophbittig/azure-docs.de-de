---
title: Einführung in Azure Advisor
description: Nutzen Sie Azure Advisor, um Ihre Azure-Bereitstellungen zu optimieren.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 48423a56983f052e9e048fca111fd77b9188a577
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494360"
---
# <a name="introduction-to-azure-advisor"></a>Einführung in Azure Advisor

Hier erfahren Sie mehr über die wichtigsten Funktionen vom Azure Advisor und erhalten Antworten auf häufig gestellte Fragen.

## <a name="what-is-advisor"></a>Was ist der Advisor?
Beim Advisor handelt es sich um einen personalisierten Cloudberater, der Sie mit bewährten Methoden zum Optimieren von Azure-Bereitstellungen unterstützt. Das Tool analysiert die Konfiguration Ihrer Ressourcen und Telemetriedaten zur Nutzung und macht anschließend Vorschläge, wie Sie die Kosteneffizienz, Leistung, Zuverlässigkeit (früher als „Hochverfügbarkeit“ bezeichnet) und Sicherheit Ihrer Azure-Ressourcen steigern können.

Der Advisor ermöglicht Folgendes:
* Abrufen proaktiver, umsetzbarer und individueller Empfehlungen und bewährter Methoden. 
* Verbessern der Leistung, Sicherheit und Zuverlässigkeit Ihrer Ressourcen und Ermitteln von Möglichkeiten der Senkung Ihrer Gesamtausgaben für Azure.
* Abrufen von Empfehlungen mit vorgeschlagenen Inlineaktionen.

Der Zugriff auf Advisor erfolgt im [Azure-Portal](https://aka.ms/azureadvisordashboard). Melden Sie sich beim [Portal](https://portal.azure.com) an, und suchen Sie den **Advisor** im Navigationsmenü oder im Menü **Alle Dienste**.

Auf dem Advisor-Dashboard werden personalisierte Empfehlungen für alle Ihre Abonnements angezeigt.  Sie können Filter zum Anzeigen von Empfehlungen für bestimmte Abonnements und Ressourcentypen anwenden.  Die Empfehlungen sind in fünf Kategorien unterteilt: 

* **Zuverlässigkeit (früher als „Hochverfügbarkeit“ bezeichnet):** Der Ratgeber hilft, die ununterbrochene Verfügbarkeit Ihrer unternehmenskritischen Anwendungen zu gewährleisten und zu verbessern. Weitere Informationen finden Sie unter [Verbessern der Zuverlässigkeit Ihrer Anwendung mit dem Azure Advisor](advisor-high-availability-recommendations.md).
* **Sicherheit**: Der Ratgeber hilft beim Erkennen von Bedrohungen und Sicherheitsrisiken, die zu Sicherheitslücken führen können. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zur Sicherheit](advisor-security-recommendations.md).
* **Leistung**: Der Ratgeber hilft, die Geschwindigkeit Ihrer Anwendungen zu verbessern. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zur Leistung](advisor-performance-recommendations.md).
* **Kosten**: Mit dem Ratgeber können Sie Ihre Gesamtausgaben für Azure senken und optimieren. Weitere Informationen finden Sie unter [Advisor-Empfehlungen zu Kosten](advisor-cost-recommendations.md).
* **Optimaler Betrieb**: Dieser Ratgeber hilft Ihnen bei der Implementierung effizienter Prozesse und Workflows, unterstützt Sie bei der Ressourcenverwaltung und enthält bewährte Methoden für die Bereitstellung. . Weitere Informationen finden Sie unter [Advisor-Empfehlungen für einen optimalen Betrieb](advisor-operational-excellence-recommendations.md).

  ![Typen von Advisor-Empfehlungen](./media/advisor-overview/advisor-dashboard.png)

Sie können auf eine Kategorie klicken, um die Liste der Empfehlungen innerhalb dieser Kategorie anzuzeigen, und eine Empfehlung auswählen, um weitere Informationen zu erhalten.  Sie lernen außerdem Aktionen kennen, die Sie ausführen können, um eine Chance zu nutzen oder ein Problem zu beheben.

![Kategorie der Advisor-Empfehlungen](./media/advisor-overview/advisor-ha-category-example.png) 

Wählen Sie die empfohlene Aktion für eine Empfehlung aus, um die Empfehlung zu implementieren.  Eine einfache Benutzeroberfläche wird geöffnet, in der Sie die Empfehlung implementieren können, oder um Sie auf die Dokumentation zu verweisen, die Ihnen bei der Implementierung hilft.  Sobald Sie eine Empfehlung implementieren, kann es einen Tag dauern, bis der Advisor dies erkennt.

Wenn Sie nicht vorhaben, bei einer Empfehlung direkt eine Maßnahme zu ergreifen, können Sie sie für einen bestimmten Zeitraum zurückstellen oder verwerfen.  Wenn Sie für ein bestimmtes Abonnement oder eine bestimmte Ressourcengruppe keine Empfehlungen erhalten möchten, können Sie den Advisor so konfigurieren, dass er nur Empfehlungen für angegebene Abonnements und Ressourcengruppen generiert.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="how-do-i-access-advisor"></a>Wie greife ich auf Advisor zu?
Der Zugriff auf Advisor erfolgt im [Azure-Portal](https://aka.ms/azureadvisordashboard). Melden Sie sich beim [Portal](https://portal.azure.com) an, und suchen Sie den **Advisor** im Navigationsmenü oder im Menü **Alle Dienste**.

Sie können die Advisor-Empfehlungen auch auf der VM-Ressourcenbenutzeroberfläche anzeigen. Wählen Sie einen virtuellen Computer aus, und wechseln Sie dann zu den Advisor-Empfehlungen im Menü. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Welche Berechtigungen benötige ich für den Zugriff auf Advisor?
 
Sie können als *Besitzer*, *Mitwirkende* oder *Leser* eines Abonnements, einer Ressourcengruppe oder einer Ressource auf die Advisor-Empfehlungen zugreifen.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Für welche Ressourcen bietet Advisor Empfehlungen?

Advisor bietet Empfehlungen für Application Gateway, App Services, Verfügbarkeitsgruppen, Azure Cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, öffentliche Azure-IP-Adressen, Azure Synapse Analytics, SQL-Server, Speicherkonten, Traffic Manager-Profile und virtuelle Computer.

Außerdem enthält Azure Advisor auch Ihre Empfehlungen aus [Microsoft Defender für Cloud](../defender-for-cloud/defender-for-cloud-introduction.md), zu denen auch Empfehlungen für weitere Ressourcentypen gehören können.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Kann ich eine Empfehlung zurückstellen oder verwerfen?

Um eine Empfehlung zurückzustellen oder zu verwerfen, klicken Sie auf den Link **Zurückstellen**. Sie können einen Zurückstellungszeitraum angeben oder **Nie** auswählen, um die Empfehlung zu verwerfen.

## <a name="next-steps"></a>Nächste Schritte

Hier finden Sie weitere Informationen zu Empfehlungen des Advisor:

* [Erste Schritte mit Advisor](advisor-get-started.md)
* [Advisor-Bewertung](azure-advisor-score.md)
* [Verbessern der Zuverlässigkeit Ihrer Anwendung mit dem Azure Advisor](advisor-high-availability-recommendations.md)
* [Advisor-Empfehlungen zur Sicherheit](advisor-security-recommendations.md)
* [Advisor-Empfehlungen zur Leistung](advisor-performance-recommendations.md)
* [Advisor-Empfehlungen zu Kosten](advisor-cost-recommendations.md)
* [Sicherstellen des optimalen Betriebs mit dem Azure Advisor](advisor-operational-excellence-recommendations.md)
