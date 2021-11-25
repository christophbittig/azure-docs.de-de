---
title: Erstellen von Incidents aus Warnungen in Microsoft Azure Sentinel | Microsoft-Dokumentation
description: Informationen zum Erstellen von Incidents aus Warnungen in Microsoft Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8f68294e2b1ab473e17552e708bf82a1bad6d8a5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523637"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Automatisches Erstellen von Incidents aus Microsoft-Sicherheitswarnungen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Mit Microsoft Azure Sentinel in Zusammenhang stehende Warnungen, die in Microsoft-Sicherheitslösungen ausgelöst werden, wie Microsoft Defender for Cloud Apps und Microsoft Defender for Identity (vormals Azure ATP), erstellen nicht automatisch Incidents in Microsoft Azure Sentinel. Bei der Verbindung einer Microsoft-Lösung mit Microsoft Azure Sentinel werden alle in diesem Dienst generierten Warnungen standardmäßig als Rohdaten in Microsoft Azure Sentinel in der Tabelle mit den Sicherheitswarnungen in Ihrem Microsoft Azure Sentinel-Arbeitsbereich gespeichert. Sie können diese Daten dann wie alle anderen Rohdaten verwenden, die Sie mit Microsoft Azure Sentinel verbinden.

Sie können Microsoft Azure Sentinel ganz einfach so konfigurieren, dass Incidents immer automatisch erstellt werden, wenn eine Warnung in einer verbundenen Microsoft-Sicherheitslösung ausgelöst wird. Befolgen Sie hierzu die Anweisungen in diesem Artikel.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen [Microsoft-Sicherheitslösungen verbinden](connect-data-sources.md#data-connection-methods), um die Erstellung von Incidents aus Sicherheitsdienstwarnungen zu aktivieren.

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>Verwenden von Analyseregeln zur Erstellung von Microsoft-Sicherheitsincidents

Verwenden Sie die in Microsoft Azure Sentinel integrierten Regeln, um auszuwählen, welche verbundenen Microsoft-Sicherheitslösungen automatisch Microsoft Azure Sentinel-Incidents in Echtzeit erstellen sollen. Sie können mithilfe der Regeln auch spezifischere Filteroptionen definieren und somit festlegen, welche der durch die Microsoft-Sicherheitslösung generierten Warnungen automatisch Incidents in Microsoft Azure Sentinel erstellen sollen. Sie können beispielsweise festlegen, dass Microsoft Azure Sentinel-Incidents automatisch nur aus Microsoft Defender for Cloud-Benachrichtigungen (vormals Azure Security Center) mit hohem Schweregrad erstellt werden.

1. Wählen Sie im Azure-Portal unter „Microsoft Azure Sentinel“ die Option **Analytics** aus.

1. Wählen Sie die Registerkarte **Rule templates** (Regelvorlagen) aus, um alle integrierten Analyseregeln anzuzeigen.

    ![Regelvorlagen](media/incidents-from-alerts/rule-templates.png)

1. Wählen Sie die gewünschte Regelvorlage für **Microsoft-Sicherheitsanalysen** aus, und klicken Sie auf **Regel erstellen**.

    ![Regel zur Sicherheitsanalyse](media/incidents-from-alerts/security-analytics-rule.png)

1. Sie können die Regeldetails ändern und die Warnungen, die Incidents erstellen, nach dem Schweregrad der Warnung oder nach Text im Namen der Warnung filtern.  
      
    Wenn Sie beispielsweise im Feld **Microsoft-Sicherheitsdienst** die Option **Microsoft Defender for Cloud** (möglicherweise heißt es dort noch *Azure Security Center*) und im Feld **Nach Schweregrad filtern** die Option **Hoch** auswählen, erzeugen nur Sicherheitswarnungen mit hohem Schweregrad automatisch Incidents in Microsoft Azure Sentinel.  

    ![Assistent zum Erstellen von Regeln](media/incidents-from-alerts/create-rule-wizard.png)

1. Sie können ebenso eine neue **Microsoft-Sicherheitsregel** erstellen, mit der Warnungen von verschiedenen Microsoft-Sicherheitsdiensten gefiltert werden, indem Sie auf **+Erstellen** klicken und **Regel zum Erstellen eines Microsoft-Incidents** auswählen.

    ![Regel zum Erstellen von Incidents](media/incidents-from-alerts/incident-creation-rule.png)

    Sie können mehr als eine **Microsoft-Sicherheitsanalyseregel** pro **Microsoft-Sicherheitsdiensttyp** erstellen. Dadurch werden keine doppelten Incidents erstellt, da jede Regel als Filter verwendet wird. Auch wenn eine Warnung mit mehr als einer **Microsoft-Sicherheitsanalyseregel** übereinstimmt, wird nur ein Microsoft Azure Sentinel-Incident erstellt.

## <a name="enable-incident-generation-automatically-during-connection"></a>Automatisches Aktivieren der Incidentgenerierung während der Verbindung

Wenn Sie eine Verbindung mit einer Microsoft-Sicherheitslösung herstellen, können Sie auswählen, ob die Warnungen aus der Sicherheitslösung automatisch Incidents in Microsoft Azure Sentinel generieren sollen.

1. Verbinden Sie eine Microsoft-Sicherheitslösungsdatenquelle. 

   ![Erstellen von Sicherheitsincidents](media/incidents-from-alerts/generate-security-incidents.png)

1. Wählen Sie unter **Create incidents** (Incidents erstellen) die Option **Aktivieren** aus, um die Standardanalyseregel zu aktivieren, die automatisch Incidents aus im verbundenen Sicherheitsdienst generierten Warnungen erstellt. Anschließend können Sie diese Regel unter **Analytics** und dann unter **Aktive Regeln** bearbeiten.

## <a name="next-steps"></a>Nächste Schritte

- Für den Einstieg in Microsoft Azure Sentinel benötigen Sie ein Microsoft Azure-Abonnement. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/free/)registrieren.
- Hier erfahren Sie, wie Sie [ein Onboarding Ihrer Daten in Microsoft Azure Sentinel durchführen](quickstart-onboard.md) und [Einblicke in Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
