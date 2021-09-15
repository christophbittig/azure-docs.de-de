---
title: Bereitstellen von Azure Sentinel-Lösungen | Microsoft-Dokumentation
description: In diesem Artikel wird gezeigt, wie Kunden ganz einfach Datenanalysetools, die zusammen mit Datenconnectors gepackt sind, suchen und bereitstellen können.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: yelevin
ms.openlocfilehash: 5cb84ad912e5430948d0dedc71d94e69d1dd1a86
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251904"
---
# <a name="discover-and-deploy-azure-sentinel-solutions"></a>Entdecken und Bereitstellen von Azure Sentinel-Lösungen

> [!IMPORTANT]
>
> Die Azure Sentinel-Lösungen befindet sich derzeit in der **VORSCHAU**, ebenso wie alle einzelnen Lösungspakete. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Azure Sentinel-Lösungen bieten Erkennbarkeit innerhalb von Produkten, Bereitstellung in einem Schritt und Aktivierung von End-to-End-Produkt-, Domänen- und/oder vertikale Szenarien in Azure Sentinel. Diese Erfahrung wird durch [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) zur Erkennbarkeit, Bereitstellung und Aktivierung von für Lösungen sowie durch [Microsoft Partner Center](/partner-center/overview) zur die Dokumenterstellung und Veröffentlichung von Lösungen unterstützt.

Lösungen können aus einer oder allen der folgenden Komponenten bestehen:

- **Datenconnectors**, einige mit zugehörigen **Parsern**
- **Arbeitsmappen**
- **Analyseregeln**
- **Hunting-Abfragen**
- **Playbooks**

## <a name="find-your-solution"></a>Suchen Sie Ihre Lösung

1. Wählen Sie im Azure Sentinel-Navigationsmenü die Option **Lösungen (Vorschau)** aus.

1. Auf dem Blatt **Lösungen** wird eine durchsuchbare Liste von Lösungen angezeigt.

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Lösungsliste":::

    - Wenn Sie ans Ende der Liste scrollen, die gesuchte Lösung aber nicht finden, klicken Sie unten auf den Link **Weitere laden**, um die Liste zu erweitern.

        :::image type="content" source="./media/sentinel-solutions-deploy/load-more.png" alt-text="Laden weiterer Lösungen":::

1. Um Ihre Auswahl einzugrenzen und die gewünschte Lösung einfacher zu finden, geben Sie einen beliebigen Teil des Lösungsnamens in das Feld **Suchen** am Anfang der Liste ein. (Die Suchmaschine erkennt nur ganze Wörter.)

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-search-1.png" alt-text="Durchsuchen der Lösungen":::

1. Wählen Sie die gewünschte Lösung aus der Liste aus, um sie bereitzustellen. Die Detailseite der Lösung wird auf der Registerkarte **Übersicht** geöffnet, auf der entscheidende und wichtige Informationen zur Lösung angezeigt werden.

    :::image type="content" source="./media/sentinel-solutions-deploy/proofpoint-tap-solution.png" alt-text="Proofpoint TAP-API-Lösung":::

1. Weitere nützliche Informationen zu Ihrer Lösung werden auf den Registerkarten **Pläne** und **Nutzungsinformationen und Support** angezeigt, und Sie können die Eindrücke anderer Kunden auf der Registerkarte **Bewertungen** abrufen.

## <a name="deploy-your-solution"></a>Bereitstellen der Lösung

1. Wählen Sie die Schaltfläche **Erstellen** aus, um den Bereitstellungs-Assistenten für die Lösung zu starten, der auf der Registerkarte **Grundlagen** geöffnet wird.

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-basics.png" alt-text="Registerkarte „Grundlagen“ des Bereitstellungs-Assistenten":::

1. Geben Sie das Abonnement, die Ressourcengruppe und den Arbeitsbereich ein, in dem/der Sie die Lösung bereitstellen möchten. 

1. Klicken Sie auf **Weiter**, um die verbleibenden Registerkarten (entsprechend den in der Lösung enthaltenen Komponenten) zu durchlaufen, auf denen Sie die einzelnen Komponenten kennenlernen und in einigen Fällen konfigurieren können.

    > [!NOTE]
    > Die unten aufgeführten Registerkarten entsprechen den Komponenten, die von der in den zugehörigen Screenshots gezeigten Lösung angeboten werden. Verschiedene Lösungen können unterschiedliche Arten von Komponenten enthalten. Daher werden möglicherweise nicht in jeder Lösung die gleichen Registerkarten angezeigt, und es werden möglicherweise Registerkarten angezeigt, die unten nicht beschrieben werden.

    1. Registerkarte **Analyse** – :::image type="content" source="./media/sentinel-solutions-deploy/wizard-analytics.png" alt-text="Registerkarte „Analyse“ des Bereitstellungs-Assistenten":::

    1. Registerkarte **Arbeitsmappen** – :::image type="content" source="./media/sentinel-solutions-deploy/wizard-workbooks.png" alt-text="Registerkarte „Arbeitsmappen“ des Bereitstellungs-Assistenten":::

    1. Registerkarte **Playbooks** – Sie müssen hier gültige Proofpoint TAP-Anmeldeinformationen eingeben, damit sich das Playbook bei Ihrem Proofpoint-System authentifizieren kann, um vorgeschriebene Antwortaktionen durchzuführen.
        :::image type="content" source="./media/sentinel-solutions-deploy/wizard-playbooks.png" alt-text="Registerkarte „Playbooks“ des Bereitstellungs-Assistenten":::

1. Warten Sie, bis auf der Registerkarte **Überprüfen und erstellen** die Meldung „Überprüfung erfolgreich“ angezeigt wird, und wählen Sie dann **Erstellen** aus, um die Lösung bereitzustellen. Sie können auch den Link **Vorlage für Automatisierung herunterladen** auswählen, um die Lösung als Code bereitzustellen.

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-create.png" alt-text="Registerkarte „Überprüfen und erstellen“ des Bereitstellungs-Assistenten":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie mehr über Azure Sentinel-Lösungen gelernt und erfahren, wie Sie diese suchen und bereitstellen.

- Erfahren Sie mehr über [Azure Sentinel-Lösungen](sentinel-solutions.md).
- Weitere Informationen finden Sie im vollständigen [Sentinel-Lösungskatalog](sentinel-solutions-catalog.md).
