---
title: 'Schnellstart: Durchführen des Onboardings in Microsoft Sentinel'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie das Onboarding von Microsoft Sentinel ausführen, indem Sie die Lösung zunächst aktivieren und dann Datenquellen verbinden.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: quickstart
ms.date: 11/09/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 46ac4308035491dc2d6b75e34bc0c58a58f93535
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520805"
---
# <a name="quickstart-on-board-microsoft-sentinel"></a>Schnellstart: Durchführen des Onboardings für Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In dieser Schnellstartanleitung erfahren Sie, wie Sie das Onboarding für Microsoft Sentinel durchführen. Wenn Sie Microsoft Sentinel integrieren möchten, müssen Sie Microsoft Sentinel zuerst aktivieren und dann eine Verbindung mit Ihren Datenquellen herstellen.

Microsoft Sentinel enthält eine Reihe von Connectors für Microsoft-Lösungen, die vorkonfiguriert verfügbar sind und Echtzeitintegration bieten, u. a. für Microsoft 365 Defender-Lösungen (ehemals Microsoft Threat Protection), Microsoft 365-Quellen (einschließlich Office 365), Azure AD, Microsoft Defender for Identity (ehemals Azure ATP), Microsoft Defender for Cloud und Sicherheitswarnungen von Microsoft Defender for Cloud. Außerdem stehen integrierte Connectors für Sicherheitslösungen von anderen Anbietern als Microsoft zur Verfügung. Sie können auch Common Event Format (CEF), Syslog oder eine REST-API verwenden, um Ihre Datenquellen mit Microsoft Sentinel zu verbinden.

Nachdem Sie die Datenquellen verbunden haben, steht Ihnen ein Katalog von professionell erstellten Arbeitsmappen zur Anzeige der Erkenntnisse, die Sie aus Ihren Daten gewinnen, zur Auswahl. Diese Arbeitsmappen können einfach an Ihre Anforderungen angepasst werden.

>[!IMPORTANT]
> Informationen zu den Gebühren im Zusammenhang mit Microsoft Sentinel finden Sie unter [Microsoft Sentinel – Preise](https://azure.microsoft.com/pricing/details/azure-sentinel/) und [Microsoft Sentinel – Kosten und Abrechnung](azure-sentinel-billing.md).

## <a name="global-prerequisites"></a>Globale Voraussetzungen

- Aktives Azure-Abonnement: Wenn Sie über keines verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Log Analytics-Arbeitsbereich. Informationen zum Erstellen eines Log Analytics-Arbeitsbereichs finden Sie [hier](../azure-monitor/logs/quick-create-workspace.md). Weitere Informationen zu Log Analytics-Arbeitsbereichen finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../azure-monitor/logs/design-logs-deployment.md).

- Für das Abonnement, in dem sich der Microsoft Sentinel-Arbeitsbereich befindet, benötigen Sie die Berechtigungen für Mitwirkende, um Microsoft Sentinel zu aktivieren. 
- Für die Ressourcengruppe, zu der der Arbeitsbereich gehört, benötigen Sie entweder Berechtigungen für Mitwirkende oder für Leser, um Microsoft Sentinel zu verwenden.
- Möglicherweise sind für die Verbindung mit bestimmten Datenquellen zusätzliche Berechtigungen erforderlich.
- Microsoft Sentinel ist ein kostenpflichtiger Dienst. Weitere Informationen finden Sie unter [Informationen zu Microsoft Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).

Weitere Informationen finden Sie unter [Aktivitäten vor der Bereitstellung und Voraussetzungen für die Bereitstellung von Microsoft Sentinel](prerequisites.md).

### <a name="geographical-availability-and-data-residency"></a>Geografische Verfügbarkeit und Data Residency

- Microsoft Sentinel kann für Arbeitsbereiche in den meisten [GA-Regionen von Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) mit Ausnahme der Regionen China und Deutschland (Sovereign) ausgeführt werden. Das Onboarding des Microsoft Sentinel-Diensts kann für neue Log Analytics-Regionen einige Zeit dauern. 

- Von Microsoft Sentinel generierte Daten, z. B. Incidents, Lesezeichen und Analyseregeln, enthalten ggf. Kundendaten, die aus den Log Analytics-Arbeitsbereichen des Kunden stammen. Diese von Microsoft Sentinel generierten Daten werden in einer der geografischen Regionen gespeichert, die in der folgenden Tabelle aufgeführt sind (basierend auf der Region, in der sich der Arbeitsbereich befindet):

    | Geografische Region des Arbeitsbereichs | Geografische Region für von Microsoft Sentinel generierte Daten |
    | --- | --- |
    | USA<br>Indien | USA |
    | Europa<br>Frankreich | Europa |
    | Australien | Australien |
    | United Kingdom | United Kingdom |
    | Canada | Canada |
    | Japan | Japan |
    | Asien-Pazifik | Asien-Pazifik * |
    | Brasilien | Brasilien * |
    | Norwegen | Norwegen |
    | Afrika | Afrika |
    | Korea | Korea |
    | Deutschland | Deutschland |
    | Vereinigte Arabische Emirate | Vereinigte Arabische Emirate |
    | Schweiz | Schweiz |
    |

    \* Datenresidenz in einer einzelnen Region wird derzeit nur in der Region „Asien, Südosten“ (Singapur) des geografischen Raums „Asien-Pazifik“ und in der Region „Brasilien, Süden“ (São Paulo, Bundesstaat) des geografischen Raums „Brasilien“ bereitgestellt. Für alle anderen Regionen können Kundendaten an einer beliebigen Stelle in der Geografie des Arbeitsbereichs gespeichert werden, wo Sentinel integriert ist.

    > [!IMPORTANT]
    > - Durch Aktivieren bestimmter Regeln, die die Machine Learning-Engine (ML) verwenden, **erteilen Sie Microsoft die Berechtigung zum Kopieren von relevanten erfassten Daten außerhalb der geografischen Region Ihres Microsoft Sentinel-Arbeitsbereichs**, da dies möglicherweise für die Verarbeitung dieser Regeln durch die Machine Learning-Engine erforderlich ist.

## <a name="enable-microsoft-sentinel"></a>Aktivieren von Microsoft Sentinel <a name="enable"></a>

1. Melden Sie sich beim Azure-Portal an. Achten Sie darauf, dass das Abonnement ausgewählt ist, in dem Microsoft Sentinel erstellt wird.

1. Suchen Sie nach **Microsoft Sentinel**, und wählen Sie diese Lösung aus.

   ![Suche nach Diensten](./media/quickstart-onboard/search-product.png)

1. Wählen Sie **Hinzufügen**.

1. Wählen Sie den Arbeitsbereich aus, den Sie verwenden möchten, oder erstellen Sie einen neuen. Sie können Microsoft Sentinel in mehr als einem Arbeitsbereich ausführen. Die Daten sind aber für einen einzelnen Arbeitsbereich isoliert.

   ![Auswählen eines Arbeitsbereichs](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Von Microsoft Defender for Cloud erstellte Standardarbeitsbereiche werden nicht in der Liste angezeigt. Sie können Microsoft Sentinel nicht in ihnen installieren.
   >

   >[!IMPORTANT]
   >
   > - Nach der Bereitstellung in einem Arbeitsbereich bietet Microsoft Sentinel **derzeit keine Unterstützung** für das Verschieben dieses Arbeitsbereichs in andere Ressourcengruppen oder Abonnements. 
   >
   >   Haben Sie den Arbeitsbereich bereits verschoben, deaktivieren Sie alle aktiven Regeln unter **Analytics**, und aktivieren Sie sie nach fünf Minuten wieder. Dieser Vorgang sollte in den meisten Fällen wirksam sein. Für die Iteration wird er jedoch nicht unterstützt und auf eigenes Risiko ausgeführt.

1. Wählen Sie **Add Microsoft Sentinel** (Microsoft Sentinel hinzufügen) aus.

## <a name="connect-data-sources"></a>Herstellen einer Verbindung mit Datenquellen

Microsoft Sentinel erfasst Daten aus Diensten und Apps, indem eine Verbindung mit dem Dienst hergestellt wird und die Ereignisse und Protokolle an Microsoft Sentinel weitergeleitet werden. Für physische und virtuelle Computer können Sie den Log Analytics-Agent installieren, mit dem die Protokolle gesammelt und an Microsoft Sentinel weitergeleitet werden. Für Firewalls und Proxys installiert Microsoft Sentinel den Log Analytics-Agent auf einem Linux-Syslog-Server, über den der Agent die Protokolldateien erfasst und an Microsoft Sentinel weiterleitet. 
 
1. Wählen Sie im Hauptmenü die Option **Datenconnectors** aus. Der Katalog mit den Datenconnectors wird geöffnet.

1. Der Katalog enthält eine Liste mit allen Datenquellen, mit denen Sie eine Verbindung herstellen können. Wählen Sie eine Datenquelle und dann die Schaltfläche **Connectorseite öffnen** aus.

1. Auf der Connectorseite werden eine Anleitung zum Konfigurieren des Connectors und alle erforderlichen weiteren Hinweise angezeigt.

    Wenn Sie beispielsweise die Datenquelle **Azure Active Directory** auswählen, mit der Sie Protokolle aus Azure AD an Microsoft Sentinel streamen können, können Sie angeben, welche Arten von Protokollen Sie erhalten möchten: Anmeldeprotokolle und/oder Überwachungsprotokolle. <br> Führen Sie die Installationsanweisungen aus oder [lesen Sie sich den entsprechenden Verbindungsleitfaden durch](data-connectors-reference.md), um weitere Informationen zu erhalten. Weite Informationen zu Datenconnectors finden Sie unter [Microsoft Sentinel-Datenconnectors](connect-data-sources.md).

1. Auf der Connectorseite werden auf der Registerkarte **Nächste Schritte** relevante integrierte Arbeitsmappen, Beispielabfragen und Analyseregelvorlagen angezeigt, die zum Datenconnector gehören. Sie können diese Elemente unverändert verwenden oder anpassen. Auf jeden Fall können Sie sofort interessante Erkenntnisse zu Ihren Daten gewinnen.

Sobald Ihre Datenquellen verbunden wurden, beginnt das Streaming Ihrer Daten zu Microsoft Sentinel. Sie können dann mit der Arbeit mit Ihren Daten loslegen. Sie können die Protokolle in den [integrierten Arbeitsmappen](get-visibility.md) anzeigen und mit der Erstellung von Abfragen in Log Analytics beginnen, um [die Daten zu untersuchen](investigate-cases.md).

Weitere Informationen finden Sie unter [Bewährte Methoden für die Datensammlung](best-practices-data.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- **Alternative Bereitstellungsoptionen**:

    - [Bereitstellen von Microsoft Sentinel über eine API](/rest/api/securityinsights/)
    - [Bereitstellen von Microsoft Sentinel über PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)
    - [Bereitstellen von Microsoft Sentinel über eine ARM-Vorlage](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-all-in-one-accelerator/ba-p/1807933)

- **Erste Schritte**:
    - [Erste Schritte mit Microsoft Sentinel](get-visibility.md)
    - [Erstellen benutzerdefinierter Analyseregeln zum Erkennen von Bedrohungen](detect-threats-custom.md)
    - [Verbinden der externen Lösung mithilfe von Common Event Format](connect-common-event-format.md)
