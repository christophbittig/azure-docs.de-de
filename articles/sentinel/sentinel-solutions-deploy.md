---
title: Zentrales Entdecken und Bereitstellen von vorkonfigurierten Azure Sentinel-Inhalten und -Lösungen | Microsoft-Dokumentation
description: In diesem Artikel wird gezeigt, wie Kunden ganz einfach Datenanalysetools, die zusammen mit Datenconnektoren gepackt sind, suchen und bereitstellen können.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8c2c749ff6b77d9d63778e6ff02e83fe40a0205f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064171"
---
# <a name="centrally-discover-and-deploy-azure-sentinel-out-of-the-box-content-and-solutions-public-preview"></a>Zentrales Entdecken und Bereitstellen von vorkonfigurierten Azure Sentinel-Inhalten und -Lösungen (Öffentliche Vorschauversion)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Die Azure Sentinel-Lösungen und der Azure Sentinel Content Hub befindet sich derzeit genau wie alle einzelnen Lösungspakete in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Der Azure Sentinel Content Hub bietet Zugriff auf vorkonfigurierte (integrierte) Azure Sentinel-Inhalte und -Lösungen, die mit Inhalten für End-to-End-Produkt-, Domänen- oder Branchenanforderungen gepackt sind.

In diesem Artikel wird beschrieben, wie Sie Lösungen in Ihrem Azure Sentinel-Arbeitsbereich installieren und die darin enthaltenen Inhalte für Ihre Verwendung verfügbar machen.

- Suchen Sie Ihre Lösungen im Content Hub basierend auf ihrem Status, dem enthaltenen Inhalt, dem Support und mehr.

- Installieren Sie die Lösung in Ihrem Arbeitsbereich, wenn Sie eine Lösung finden, die den Anforderungen Ihrer Organisation entspricht. Stellen Sie sicher, dass sie mit den neuesten Änderungen aktualisiert wird.

> [!TIP]
> Wenn Sie ein Partner sind, der Ihre eigene Projektmappe erstellen möchte, finden Sie weitere Informationen im Azure Sentinel [Solutions Build Guide](https://aka.ms/sentinelsolutionsbuildguide) für Lösungserstellung und -veröffentlichung.
>
## <a name="find-a-solution"></a>Lösung finden

1. Wählen Sie im Azure Sentinel-Navigationsmenü unter **Inhaltsverwaltung** die Option **Content Hub (Vorschau)** aus.

1. Auf der Seite **Content Hub** wird ein durchsuchbares und filterbares Raster von Lösungen angezeigt.

    Filtern Sie die angezeigte Liste, indem Sie entweder bestimmte Werte aus den Filtern auswählen oder einen beliebigen Teil eines Lösungsnamens oder einer Beschreibung in das Feld **Suchen** eingeben.

    Weitere Informationen finden Sie unter [vorkonfigurierte Azure Sentinel-Inhalten und -Lösungskategorien](sentinel-solutions.md#azure-sentinel-out-of-the-box-content-and-solution-categories).

    > [!TIP]
    > Wenn eine Lösung, die Sie bereitgestellt haben, Updates enthält, seit Sie sie bereitgestellt haben, wird ein orangefarbenes Dreieck angezeigt, dass Updates bereitgestellt werden müssen. Es wird im blauen Dreieck oben auf der Seite angezeigt.
    >

Jede Projektmappe im Raster zeigt die Kategorien an, die auf die Lösung angewendet werden, und die Inhaltstypen, die in der Lösung enthalten sind.

In der folgenden Abbildung zeigt die **Cisco Umbrella-Lösung** beispielsweise eine Kategorie von **Sicherheit - Andere**, und dass diese Lösung 10 Analyseregeln, 11 Hunting-Abfragen, einen Parser, drei Playbooks und mehr umfasst.

:::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Screenshot des Azure Sentinel Content Hubs." lightbox="./media/sentinel-solutions-deploy/solutions-list.png":::

## <a name="install-or-update-a-solution"></a>Installieren oder Aktualisieren einer Lösung

1. Wählen Sie im Content Hub eine Lösung aus, um weitere Informationen auf der rechten Seite anzuzeigen. Wählen Sie dann **Installieren** oder **Aktualisieren** aus, wenn Sie Updates benötigen. Beispiel:


1. Wählen Sie auf der Seite mit den Lösungsdetails **Erstellen** oder **Aktualisieren** aus, um den Lösungsassistenten zu starten. Geben Sie in der Registerkarte **Grundlagen** des Assistenten das Abonnement, die Ressourcengruppe und den Arbeitsbereich ein, in dem/der Sie die Lösung bereitstellen möchten. Beispiel:

    :::image type="content" source="media/sentinel-solutions-deploy/wizard-basics.png" alt-text="Screenshot eines Lösungsinstallationsassistenten mit der Registerkarte Grundlagen.":::

1. Klicken Sie auf **Weiter**, um die verbleibenden Registerkarten (entsprechend den in der Lösung enthaltenen Komponenten) zu durchlaufen, auf denen Sie die einzelnen Inhaltskomponenten kennenlernen und in einigen Fällen konfigurieren können.

    > [!NOTE]
    > Die angezeigten Registerkarten entsprechen dem Inhalt, der von der Lösung angeboten wird. Verschiedene Lösungen können unterschiedliche Arten von Inhalten enthalten, sodass nicht in jeder Lösung die gleichen Registerkarten angezeigt werden.
    >
    > Sie werden möglicherweise auch aufgefordert, Anmeldeinformationen für einen Drittanbieterdienst einzugeben, damit Azure Sentinel sich bei Ihren Systemen authentifizieren können. Bei Playbooks können Sie beispielsweise Reaktionsaktionen wie in Ihrem System vorgeschrieben ausführen.
    >

1. Warten Sie schließlich, bis auf der Registerkarte **Überprüfen und Erstellen** die `Validation Passed`Meldung angezeigt wird, und wählen Sie dann **Erstellen** oder **Aktualisieren** aus, um die Lösung bereitzustellen. Sie können auch den Link **Vorlage für Automatisierung herunterladen** auswählen, um die Lösung als Code bereitzustellen.

Für weitere Informationen beziehen Sie sich auf den [Azure Sentinel Content Hub Katalog](sentinel-solutions-catalog.md) und [Suchen Sie Ihren Azure Sentinel-Datenconnektor](data-connectors-reference.md).


## <a name="find-the-support-model-for-your-solution"></a>Suchen Sie das Supportmodell für Ihre Lösung

Jede Lösung listet Details zu ihrem Supportmodell im Detailbereich der Lösung im Feld **Support** auf, in dem entweder **Microsoft** oder der Name eines Partners aufgeführt ist. Beispiel:

:::image type="content" source="media/sentinel-solutions-deploy/find-support-details.png" alt-text="Screenshot: Wo können Sie Ihr Supportmodell für Ihre Lösung finden." lightbox="media/sentinel-solutions-deploy/find-support-details.png":::

Wenn Sie sich an den Support wenden, benötigen Sie möglicherweise weitere Details zu Ihrer Lösung, z. B. Werte für Herausgeber, Anbieter und Plan-ID. Diese finden Sie auf der Detailseite der Lösung auf der Registerkarte **Nutzungsinformationen & Support**. Zum Beispiel:

:::image type="content" source="media/sentinel-solutions-deploy/usage-support.png" alt-text="Screenshot: Nutzungs- und Supportdetails für eine Lösung.":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie mehr über Azure Sentinel-Lösungen und das Suchen und Bereitstellen von integrierten Inhalten erfahren.

- Erfahren Sie mehr über [Azure Sentinel-Lösungen](sentinel-solutions.md).
- Weitere Informationen finden Sie im vollständigen [Azure Sentinel-Lösungskatalog](sentinel-solutions-catalog.md).
