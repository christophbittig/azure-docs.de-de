---
title: Verbinden von Azure Defender-Warnungen mit Azure Sentinel
description: In diesem Artikel erfahren Sie, wie Sie Warnungen aus Azure Defender verbinden und in Azure Sentinel streamen.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 07/08/2021
ms.author: yelevin
ms.openlocfilehash: 9612b5aaaec10358609abba3878f6f09405ad076
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260110"
---
# <a name="connect-azure-defender-alerts-from-azure-security-center"></a>Verbinden von Azure Defender-Benachrichtigungen aus Azure Security Center

## <a name="background"></a>Hintergrund

[Azure Defender](../security-center/azure-defender.md), die integrierte Plattform für Cloudworkloadschutz (Cloud Workload Protection Platform, CWPP) von [Azure Security Center](../security-center/security-center-introduction.md), ist ein Sicherheitsverwaltungstool, mit dem Sie Bedrohungen über Hybridworkloads hinweg erkennen und diesen schnell entgegenwirken können. 

Mit diesem Connector können Sie Ihre Azure Defender-Sicherheitswarnungen von Azure Security Center in Azure Sentinel streamen, sodass Sie Defender-Warnungen und die von ihnen generierten Incidents in einem größeren organisationsweiten Bedrohungskontext anzeigen, analysieren und darauf reagieren können.

Wenn Azure Defender pro Abonnement aktiviert ist, ist auch der Azure Defender-Connector gesondert für jedes Abonnement aktiviert oder deaktiviert.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>Warnungssynchronisierung

- Wenn Sie Azure Defender mit Azure Sentinel verbinden, wird der Status der in den Azure Sentinel-Dienst übernommenen Azure Defender-Warnungen zwischen den beiden Diensten synchronisiert. Wenn also beispielsweise eine Warnung in einem Azure Defender geschlossen wird, wird diese Warnung auch in Azure Sentinel als geschlossen angezeigt.

- Das Ändern des Status einer Warnung in Azure Defender wirkt sich *nicht* auf den Status von Azure Sentinel-**Incidents** aus, die die Azure Sentinel-Warnung enthalten, sondern nur auf den Status der Warnung selbst.

### <a name="bi-directional-alert-synchronization"></a>Bidirektionale Synchronisierung von Warnungen

> [!IMPORTANT]
>
> - Das Feature der **bidirektionalen Warnungssynchronisierung** ist derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

- Wenn Sie die **bidirektionale Synchronisierung** aktivieren, wird der Status der ursprünglichen Azure Defender-Warnungen automatisch mit dem Status der Azure Sentinel-Incidents synchronisiert, die diese Warnungen enthalten. Wenn also beispielsweise ein Azure Sentinel-Incident, der eine Azure Defender-Warnung enthält, geschlossen wird, wird in Azure Defender automatisch die entsprechende ursprüngliche Warnung geschlossen.

## <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für Ihren Azure Sentinel-Arbeitsbereich.

- Sie müssen in den Abonnements, deren Protokolle Sie streamen, über die Rolle „Sicherheitsleseberechtigter“ verfügen.

- Sie müssen für jedes Abonnement, für das Sie den Connector aktivieren möchten, im Azure Security Center mindestens einen **Azure Defender-Plan** aktivieren. Wenn Sie Azure Defender für ein Abonnement aktivieren möchten, müssen Sie über die Rolle **Sicherheitsadministrator** für das Abonnement verfügen.

-  Zum Aktivieren der bidirektionalen Synchronisierung müssen Sie über die Rolle **Mitwirkender** oder **Sicherheitsadministrator** für das betreffende Abonnement verfügen.

## <a name="connect-to-azure-defender"></a>Verbinden mit Azure Defender

1. Wählen Sie im Navigationsmenü von Azure Sentinel **Datenconnectors** aus.

1. Wählen Sie im Katalog für Datenconnectors **Azure Defender** aus, und wählen Sie im Detailbereich **Connectorseite öffnen** aus.

1. Unter **Konfiguration** werden eine Liste der Abonnements im Mandanten und der Status ihrer Verbindung mit Azure Defender angezeigt. Klicken Sie neben jedem Abonnement, dessen Warnungen Sie in Azure Sentinel streamen möchten, auf die Umschaltfläche **Status**. Wenn Sie eine Verbindung mit mehreren Abonnements auf einmal herstellen möchten, aktivieren Sie die Kontrollkästchen neben den relevanten Abonnements, und klicken Sie dann auf der Leiste über der Liste auf die Schaltfläche **Verbinden**.

    > [!NOTE]
    > - Die Kontrollkästchen und die Umschaltflächen **Verbinden** sind nur für die Abonnements aktiviert, für die Sie über die erforderlichen Berechtigungen verfügen.
    > - Die Schaltfläche **Verbinden** ist nur aktiviert, wenn das Kontrollkästchen für mindestens ein Abonnement aktiviert wurde.

1. Zum Aktivieren der bidirektionalen Synchronisierung für ein Abonnement suchen Sie das Abonnement in der Liste, und wählen Sie in der Dropdownliste in der Spalte **Bi-directional sync (Preview)** (Bidirektionale Synchronisierung (Vorschau)) die Option **Aktiviert** aus. Um die bidirektionale Synchronisierung für mehrere Abonnements auf einmal zu aktivieren, aktivieren Sie die entsprechenden Kontrollkästchen, und klicken Sie auf der Leiste über der Liste auf die Schaltfläche **Enable bi-directional sync** (Bidirektionale Synchronisierung aktivieren).

    > [!NOTE]
    > - Die Kontrollkästchen und Dropdownlisten sind nur für die Abonnements aktiviert, für die Sie über die [erforderlichen Berechtigungen](#prerequisites) verfügen.
    > - Die Schaltfläche **Enable bi-directional sync** (Bidirektionale Synchronisierung aktivieren) ist nur aktiviert, wenn das Kontrollkästchen für mindestens ein Abonnement aktiviert wurde.

1. In der Spalte **Azure Defender-Pläne** der Liste wird angezeigt, ob für Ihr Abonnement Azure Defender-Pläne aktiviert sind (eine Voraussetzung zum Aktivieren des Connectors). Der Wert für jedes Abonnement in dieser Spalte ist entweder leer (d. h., dass keine Defender-Pläne aktiviert sind) oder lautet „All enabled“ (Alle aktiviert) oder „Some enabled“ (Einige aktiviert). Für Abonnements mit dem Wert „Some enabled“ (Einige aktiviert) ist der Link **Alle aktivieren** vorhanden. Über diesen gelangen Sie zum Azure Defender-Konfigurationsdashboard für das Abonnement, in dem Sie zu aktivierende Defender-Pläne auswählen können. Über die Linkschaltfläche **Enable Azure Defender for all subscriptions** (Azure Defender für alle Abonnements aktivieren) auf der Leiste über der Liste gelangen Sie zur Seite „Erste Schritte“ von Azure Defender. Auf dieser können Sie auswählen, für welche Abonnements Azure Defender insgesamt aktiviert werden soll.

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="Screenshot der Konfiguration des Azure Defender-Connectors":::

1. Sie können auswählen, ob die Azure Defender-Warnungen automatisch Incidents in Azure Sentinel generieren sollen. Wählen Sie unter **Incidents erstellen** die Option **Aktiviert** aus, um die Standardanalyseregel zu aktivieren, durch die [aus Warnungen automatisch Incidents erstellt werden](create-incidents-from-alerts.md). Anschließend können Sie diese Regel unter **Analytics** auf der Registerkarte **Aktive Regeln** bearbeiten.

    > [!TIP]
    > Wenn Sie [benutzerdefinierte Analyseregeln](detect-threats-custom.md) für Warnungen von Azure Defender konfigurieren, berücksichtigen Sie den Warnungsschweregrad, damit keine Incidents für Informationswarnungen geöffnet werden. 
    >
    > Informationswarnungen in Azure Security Center stellen an sich kein Sicherheitsrisiko dar und sind nur in Zusammenhang mit einem vorhandenen, offenen Incident relevant. Weitere Informationen finden Sie unter [Sicherheitswarnungen und -incidents in Azure Security Center](../security-center/security-center-alerts-overview.md).
    > 
    

## <a name="find-and-analyze-your-data"></a>Suchen und Analysieren Ihrer Daten

> [!NOTE]
> Die *bidirektionale* Synchronisierung von Warnungen kann einige Minuten in Anspruch nehmen. Änderungen des Status von Warnungen werden eventuell nicht sofort angezeigt.

- Azure Defender-Warnungen werden in der Tabelle *SecurityAlert* des Log Analytics-Arbeitsbereichs angezeigt.

- Um Azure Defender-Warnungen in Log Analytics abzufragen, kopieren Sie zunächst Folgendes in das Abfragefenster:

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- Auf der Registerkarte **Nächste Schritte** auf der Connectorseite finden Sie weitere nützliche Beispielabfragen, Analyseregelvorlagen und empfohlene Arbeitsmappen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Azure Defender mit Azure Sentinel verbinden und Warnungen zwischen ihnen synchronisieren. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).
- Schreiben Sie eigene Regeln, um [Bedrohungen zu erkennen](detect-threats-custom.md).