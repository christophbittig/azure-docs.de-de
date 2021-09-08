---
title: Informationen zum Einzelmandanten-Workflow-Designer
description: Hier erfahren Sie, wie der Designer in Einzelmandanten-Azure Logic Apps Ihnen hilft, Workflows visuell über das Azure-Portal erstellen. Entdecken Sie die Vorteile und Features dieser neuesten Version.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: ad280e8c052b14ce64734012c3677f8fd2ce5096
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113129274"
---
# <a name="about-the-workflow-designer-in-single-tenant-azure-logic-apps"></a>Informationen zum Workflow-Designer in Einzelmandanten-Azure Logic Apps

Wenn Sie mit Azure Logic Apps im Azure-Portal arbeiten, können Sie Ihre [*Workflows*](logic-apps-overview.md#workflow) visuell oder programmatisch bearbeiten. Wenn Sie eine [*Logik-App*](logic-apps-overview.md#logic-app)-Ressource im Portal geöffnet haben, können Sie im Ressourcenmenü unter **Entwickler** zwischen den Ansichten [**Code**](#code-view) und **Designer** wählen. Wenn Sie Ihren Workflow visuell entwickeln, bearbeiten und ausführen möchten, wählen Sie die Ansicht „Designer“ aus. Sie können jederzeit zwischen den Ansichten „Code“ und „Designer“ wechseln.

> [!IMPORTANT]
> Derzeit ist die neueste Version des Designers nur für *Standard*-Logik-App-Ressourcen verfügbar, die in der *Einzelmandanten*-Azure Logic Apps-Umgebung ausgeführt werden. Weitere Informationen zu verschiedenen Ressourcentypen und Laufzeitumgebungen in Logic Apps finden Sie unter [Vergleich zwischen Umgebungen mit einem Mandanten und mehreren Mandanten bzw. Integrationsdienstumgebung für Azure Logic Apps](single-tenant-overview-compare.md).

:::image type="content" source="./media/designer-overview/choose-developer-view.png" alt-text="Screenshot einer Logik-App-Ressourcenseite im Azure-Portal mit den Randleistenoptionen zum Anzeigen eines Workflows in der Code- oder Designeransicht.":::

Wenn Sie die Ansicht **Designer** auswählen, wird Ihr Workflow im Workflow-Designer geöffnet.

:::image type="content" source="./media/logic-apps-overview/example-enterprise-workflow.png" alt-text="Screenshot, der den Workflow-Designer und einen Beispielworkflow für Unternehmen zeigt, der Optionen und Bedingungen verwendet." lightbox="./media/logic-apps-overview/example-enterprise-workflow.png":::

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).
- Eine *Standard*-Logik-App-Ressource [in einer Einzelmandantenumgebung](single-tenant-overview-compare.md). Weitere Informationen finden Sie unter [Erstellen eines Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal](create-single-tenant-workflows-azure-portal.md).
- Ein Workflow für Ihre Einzelmandanten-Logik-App.

## <a name="latest-version-features"></a>Features der neuesten Version

Der neueste Workflow-Designer bietet eine neue Erfahrung mit beachtenswerten Features und Vorteilen, z. B.:

- Eine neue Layout-Engine, die kompliziertere Workflows unterstützt. 
- Dank der neuen Layout-Engine, einer kompakteren Canvas und Aktualisierungen des kartenbasierten Layouts können Sie komplizierte Workflows sauber und einfach erstellen und anzeigen.
- Fügen Sie Schritte mithilfe von Panels getrennt vom Workflowlayout hinzu, und bearbeiten Sie sie. Diese Änderung bietet Ihnen eine übersichtlichere Canvas zum Anzeigen Ihres Workflowlayouts. Weitere Informationen finden Sie unter [Hinzufügen von Schritten zu Workflows](#add-steps-to-workflows).
- Wechseln Sie im Designer mithilfe der Tastaturnavigation zwischen den Schritten in Ihrem Workflow.
  - Wechsel zur nächsten Karte: **STRG** + **NACH-UNTEN-TASTE (&darr;)**
  - Wechsel zur vorherigen Karte: **STRG** + **NACH-OBEN-TASTE (&uarr;)**

## <a name="add-steps-to-workflows"></a>Hinzufügen von Schritten zu Workflows

Der Workflow-Designer bietet eine visuelle Möglichkeit zum Hinzufügen, Bearbeiten und Löschen von Schritten in Ihrem Workflow. Fügen Sie als ersten Schritt in Ihrem Workflow immer einen [*Trigger*](logic-apps-overview.md#trigger) hinzu. Schließen Sie Ihren Workflow dann ab, indem Sie eine oder mehrere [*Aktionen*](logic-apps-overview.md#action) hinzufügen.

Um Ihrem Workflow entweder den Trigger oder eine Aktion hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Öffnen Sie Ihren Workflow im Designer.
1. Wählen Sie im Designer **Vorgang auswählen** aus, wodurch entweder ein Bereich namens **Auslöser hinzufügen** oder **Aktion hinzufügen** geöffnet wird. 
1. Suchen Sie im geöffneten Bereich nach einem Vorgang, indem Sie die Liste wie folgt filtern:
    1. Geben Sie einen Dienst, einen Connector oder eine Kategorie in die Suchleiste ein, um verwandte Vorgänge anzuzeigen. Zum Beispiel: `Azure Cosmos DB` oder `Data Operations`. 
    1. Wenn Sie den gewünschten Vorgang kennen, geben Sie den Namen in die Suchleiste ein. Zum Beispiel: `Call an Azure function` oder `When an HTTP request is received`.
    1. Wählen Sie die Registerkarte **Integriert** aus, um nur die Kategorien [*integrierter Vorgänge*](logic-apps-overview.md#built-in-operations) anzuzeigen. Wählen Sie alternativ die Registerkarte **Azure** aus, um andere Kategorien von Vorgängen anzuzeigen, die über Azure verfügbar sind.
    1. Sie können Trigger oder Aktionen nur anzeigen, indem Sie die Registerkarte **Trigger** oder **Aktionen** auswählen. Sie können einen Trigger jedoch nur als ersten Schritt und eine Aktion als nachfolgenden Schritt hinzufügen. Basierend auf der Vorgangskategorie sind möglicherweise nur Trigger oder Aktionen verfügbar.
    :::image type="content" source="./media/designer-overview/designer-add-operation.png" alt-text="Screenshot des Logic Apps-Designers im Azure-Portal mit einem Workflow, der bearbeitet wird, um einen neuen Vorgang hinzuzufügen." lightbox="./media/designer-overview/designer-add-operation.png":::
1. Wählen Sie den Vorgang aus, den Sie verwenden möchten. 
    :::image type="content" source="./media/designer-overview/designer-filter-operations.png" alt-text="Screenshot des Logic Apps-Designers mit einem Bereich möglicher Vorgänge, die nach Dienst oder Name gefiltert werden können." lightbox="./media/designer-overview/designer-filter-operations.png":::
1. Konfigurieren Sie Ihren Trigger oder Ihre Aktion nach Bedarf.
    1. Pflichtfelder sind mit einem roten Sternchen (&ast;) vor dem Namen gekennzeichnet.
    1. Bei einigen Triggern und Aktionen müssen Sie möglicherweise eine Verbindung mit einem anderen Dienst herstellen. Möglicherweise müssen Sie sich bei einem Konto anmelden oder Anmeldeinformationen für einen Dienst eingeben. Wenn Sie z. B. den Office 365 Outlook-Connector verwenden möchten, um eine E-Mail zu senden, müssen Sie Ihr Outlook-E-Mail-Konto autorisieren.
    1. Einige Trigger und Aktionen verwenden dynamischen Inhalt, bei dem Sie Variablen auswählen können, anstatt Informationen oder Ausdrücke eingeben zu müssen.
1. Wählen Sie zum Speichern der Änderungen in der Symbolleiste **Speichern** aus. In diesem Schritt wird außerdem überprüft, ob Ihr Workflow gültig ist. 

## <a name="code-view"></a>Codeansicht

In der Ansicht **Code** können Sie die Workflowdefinitionsdatei direkt im JSON-Format bearbeiten. Wählen Sie unbedingt **Speichern** aus, um alle Änderungen in dieser Ansicht zu speichern. 

> [!TIP]
> Die Ansicht **Code** ist auch eine einfache Möglichkeit, die Workflowdefinition zu finden und zu kopieren, anstatt die Azure-Befehlszeilenschnittstelle (Azure CLI) oder andere Methoden zu verwenden.

:::image type="content" source="./media/designer-overview/code-view.png" alt-text="Screenshot eines Logic Apps-Workflows in der Codeansicht, der zeigt, wie die JSON-Workflowdefinition im Azure-Portal bearbeitet wird.":::


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines Integrationsworkflows mit der Azure Logic Apps-Einzelmandanteninstanz (Standard) im Azure-Portal](create-single-tenant-workflows-azure-portal.md)
