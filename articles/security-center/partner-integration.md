---
title: Integrieren von Sicherheitslösungen in Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Partner in Microsoft Defender für Cloud integriert werden, um die allgemeine Sicherheit Ihrer Azure-Ressourcen zu verbessern.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b5b6869cf1d61f89ce9ac072411b0786da552a46
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095405"
---
# <a name="integrate-security-solutions-in-microsoft-defender-for-cloud"></a>Integrieren von Sicherheitslösungen in Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieses Dokument unterstützt Sie bei der Verwaltung von bereits mit Microsoft Defender für Cloud verbundenen Sicherheitslösungen sowie beim Hinzufügen neuer Lösungen.

## <a name="integrated-azure-security-solutions"></a>Integrierte Azure-Sicherheitslösungen
Mit Defender für Cloud können Sie ganz einfach integrierte Sicherheitslösungen in Azure aktivieren. Dies hat unter anderem folgende Vorteile:

- **Vereinfachte Bereitstellung:** Defender für Cloud bietet eine optimierte Bereitstellung integrierter Partnerlösungen. Für Lösungen wie Antischadsoftware und Sicherheitsrisikobewertungen kann Defender für Cloud den Agent auf Ihren virtuellen Computern bereitstellen. Bei Firewallgeräten kann sich Defender für Cloud um den Großteil der erforderlichen Netzwerkkonfiguration kümmern.
- **Integrierte Erkennungen:** Sicherheitsereignisse von Partnerlösungen werden automatisch gesammelt, aggregiert und im Rahmen der Defender für Cloud-Warnungen und -Incidents angezeigt. Diese Ereignisse werden mit Erkennungen anderer Quellen zusammengeführt, um erweiterte Bedrohungserkennungsfunktionen bereitzustellen.
- **Einheitliche Integritätsüberwachung und -verwaltung:** Mithilfe integrierter Integritätsereignisse können Kunden alle Partnerlösungen auf einen Blick überwachen. Es ist eine grundlegende Verwaltung verfügbar – mit einfachem Zugriff auf die erweiterte Konfiguration über die Partnerlösung.

Derzeit umfassen integrierte Sicherheitslösungen die Sicherheitsrisikobewertung durch [Qualys](https://www.qualys.com/public-cloud/#azure), [Rapid7](https://www.rapid7.com/products/insightvm/) und [Microsoft Azure Web Application Firewall für Azure Application Gateway](../web-application-firewall/ag/ag-overview.md).

> [!NOTE]
> Defender für Cloud installiert den Log Analytics-Agent nicht auf virtuellen Geräten von Partnern, da die meisten Sicherheitsanbieter die Ausführung externer Agents auf ihren Geräten untersagen.

Weitere Informationen zur Integration von Qualys-Überprüfungstools für Sicherheitsrisiken, einschließlich einer integrierten Überprüfung für Kunden, die Microsoft Defender für Server aktiviert haben, finden Sie unter [In Defender für Cloud integrierte Qualys-Überprüfung auf Sicherheitsrisiken für Azure- und Hybridcomputer](deploy-vulnerability-assessment-vm.md).

Defender für Cloud bietet auch eine Sicherheitsrisikoanalyse für Folgendes:

* SQL-Datenbanken – siehe [Untersuchen der Berichte zur Sicherheitsrisikobewertung im Dashboard „Sicherheitsrisikobewertung“](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)
* Azure Container Registry-Images – siehe [Verwenden von Microsoft Defender für Containerregistrierungen zum Überprüfen Ihrer Images auf Sicherheitsrisiken](defender-for-container-registries-usage.md)

## <a name="how-security-solutions-are-integrated"></a>Informationen zur Integration von Sicherheitslösungen
Über Defender für Cloud bereitgestellte Azure-Sicherheitslösungen werden automatisch verbunden. Sie können auch andere Sicherheitsdatenquellen verwenden, so auch Computer, die lokal oder in anderen Clouds ausgeführt werden.

:::image type="content" source="./media/partner-integration/security-solutions-page.png" alt-text="Integration von Partnerlösungen" lightbox="./media/partner-integration/security-solutions-page.png":::

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Verwalten integrierter Azure-Sicherheitslösungen und anderer Datenquellen

1. Öffnen Sie im [Azure-Portal](https://azure.microsoft.com/features/azure-portal/) den Dienst **Defender für Cloud**.

1. Wählen Sie im Menü von Defender für Cloud die Option **Sicherheitslösungen** aus.

Auf der Seite **Sicherheitslösungen** können Sie die Integrität integrierter Azure-Sicherheitslösungen anzeigen und grundlegende Verwaltungsaufgaben ausführen.

### <a name="connected-solutions"></a>Verbundene Lösungen

Der Abschnitt **Verbunde Lösungen** enthält alle Sicherheitslösungen, die derzeit mit Defender für Cloud verbunden sind. Außerdem wird der Integritätsstatus der einzelnen Lösungen angezeigt.  

![Verbundene Lösungen](./media/partner-integration/connected-solutions.png)

Eine Partnerlösung kann den folgenden Status aufweisen:

* **Fehlerfrei** (grün): Es liegen keine Integritätsprobleme vor.
* **Fehlerhaft** (rot): Es liegt ein Integritätsproblem vor, das sofort untersucht werden muss.
* **Berichterstellung beendet** (orange): Die Lösung hat die Berichterstattung über ihre Integrität beendet.
* **Nicht berichtet** (grau): Die Lösung hat noch nichts berichtet, und es sind keine Integritätsdaten verfügbar. Der Status einer Lösung kann möglicherweise nicht gemeldet werden, wenn Sie erst vor kurzem angeschlossen wurde und noch bereitgestellt wird.

> [!NOTE]
> Wenn keine Integritätsstatusdaten verfügbar sind, zeigt Defender für Cloud das Datum und die Uhrzeit des letzten empfangenen Ereignisses an, um anzugeben, ob die Lösung Berichte übermittelt. Wenn keine Integritätsdaten verfügbar sind und innerhalb der letzten 14 Tage keine Warnungen empfangen wurden, gibt Defender für Cloud an, dass die Lösung fehlerhaft ist oder nicht berichtet.
>
>

Wählen Sie **ANZEIGEN** aus, um zusätzliche Informationen und Optionen anzuzeigen, z. B.:

   - **Lösungskonsole**: Öffnet die Verwaltungsoberfläche für diese Lösung.
   - **Virtuellen Computer verknüpfen**: Öffnet die Seite „Anwendungen verknüpfen“. Hier können Sie Ressourcen mit der Partnerlösung verknüpfen.
   - **Lösung löschen**
   - **Konfigurieren**

   ![Details der Partnerlösung](./media/partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Ermittelte Lösungen

Defender für Cloud erkennt Sicherheitslösungen automatisch, die in Azure ausgeführt werden, aber nicht mit Defender für Cloud verbunden sind, und zeigt die Lösungen im Abschnitt **Ermittelte Lösungen** an. Zu diesen Lösungen gehören Azure-Lösungen wie [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) und Partnerlösungen.

> [!NOTE]
> Aktivieren Sie **erweiterten Schutz** auf Abonnementebene für das Feature „Ermittelte Lösungen“. Weitere Informationen finden Sie unter [Schnellstart: Aktivieren erweiterter Sicherheitsfeatures](enable-enhanced-security.md).

Wählen Sie unterhalb einer Lösung die Option **VERBINDEN** aus, um die Integration in Defender für Cloud durchzuführen und über Sicherheitswarnungen benachrichtigt zu werden.

### <a name="add-data-sources"></a>Hinzufügen von Datenquellen

Der Abschnitt **Datenquellen hinzufügen** enthält weitere verfügbare Datenquellen, mit denen eine Verbindung hergestellt werden kann. Klicken Sie **HINZUFÜGEN**, um eine Anleitung zum Hinzufügen von Daten aus einer dieser Quellen anzuzeigen.

![Datenquellen.](./media/partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Partnerlösungen in Defender für Cloud integrieren. Weitere Informationen dazu, wie Sie eine Integration mit Microsoft Sentinel oder einem anderen SIEM einrichten, finden Sie unter [Fortlaufendes Exportieren von Defender für Cloud-Daten](continuous-export.md).
