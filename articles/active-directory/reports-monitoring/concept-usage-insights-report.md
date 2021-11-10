---
title: Nutzungs- und Insights-Bericht | Microsoft-Dokumentation
description: Einführung in den Bericht „Nutzung & Erkenntnisse“ im Azure Active Directory-Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 3fba300d-18fc-4355-9924-d8662f563a1f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: f9c7011a142f521b33fa8faea830cde90e1ae70a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995804"
---
# <a name="usage-and-insights-report-in-the-azure-active-directory-portal"></a>Bericht „Nutzung & Erkenntnisse“ im Azure Active Directory-Portal

Mit dem Bericht „Nutzung & Erkenntnisse“ erhalten Sie eine anwendungsorientierte Ansicht Ihrer Anmeldedaten. Dort finden Sie Antworten auf die folgenden Fragen:

*   Welche Anwendungen werden in meinem Unternehmen am meisten verwendet?
*   Bei welchen Anwendungen schlagen die meisten Anmeldungen fehl? 
*   Welche Anmeldefehler treten für jede Anwendung am häufigsten auf?

## <a name="prerequisites"></a>Voraussetzungen 

Sie benötigen Folgendes, um auf die Daten des Berichts „Nutzung & Erkenntnisse“ zuzugreifen:

* Einen Azure AD-Mandanten
* Eine Azure AD Premium-Lizenz (P1/P2) zum Anzeigen der Anmeldedaten
* Ein Benutzer mit der Rolle „globaler Administrator“, „Sicherheitsadministrator“, „Benutzer mit Leseberechtigung für Sicherheitsfunktionen“ oder „Benutzer mit Leseberechtigung für Berichte“. Darüber hinaus kann jeder Benutzer (Nicht-Administratoren) auf seine eigenen Anmeldedaten zugreifen. 

## <a name="access-the-usage-and-insights-report"></a>Zugriff auf den Bericht „Nutzung & Erkenntnisse“

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
2. Wählen Sie das richtige Verzeichnis aus, und wählen Sie dann auf **Azure Active Directory** gefolgt von **Unternehmensanwendungen** aus.
3. Klicken Sie im Abschnitt **Aktivität** auf **Nutzung & Erkenntnisse**, um den Bericht zu öffnen. 

![Screenshot der im Abschnitt „Aktivität“ ausgewählten Option „Nutzung & Erkenntnisse“](./media/concept-usage-insights-report/main-menu.png)
                                     

## <a name="use-the-report"></a>Verwenden des Berichts

Der Bericht „Nutzung & Erkenntnisse“ zeigt eine Liste der Anwendungen mit mindestens einem Anmeldungsversuch an. Sie können die Liste nach der Anzahl erfolgreicher Anmeldungen, der Anzahl von Anmeldungen, bei denen Fehler aufgetreten sind, und der Erfolgsrate sortieren.

Sie können weitere Anwendungen auf der Seite anzeigen, indem Sie unten in der Liste auf **Weitere laden** klicken. Sie können den Zeitraum auswählen, um alle Anwendungen anzuzeigen, die in diesem Zeitraum verwendet wurden.

![Screenshot der Seite „Nutzung & Erkenntnisse“ für eine Anwendungsaktivität, auf dem Sie einen Datumsbereich auswählen und Anmeldeaktivitäten für verschiedene Apps anzeigen können](./media/concept-usage-insights-report/usage-and-insights-report.png)

Sie können den Fokus auch auf eine bestimmte Anwendung festlegen. Wählen Sie **Anmeldeaktivität anzeigen** aus, um die Anmeldeaktivität im Laufe der Zeit und die häufigsten Fehler der Anwendung anzuzeigen.  

Wenn Sie einen Tag im Anwendungsnutzungsgraph auswählen, wird eine ausführliche Liste der Anmeldeaktivitäten für die Anwendung angezeigt.  

:::image type="content" source="./media/concept-usage-insights-report/usage-and-insights-application-report.png" alt-text="Screenshot zeigt „Nutzung & Erkenntnisse“ für eine bestimmte Anwendung, wo Sie ein Diagramm für die Anmeldeaktivität sehen können.":::

## <a name="next-steps"></a>Nächste Schritte

* [Berichte zu Anmeldeaktivitäten im Azure Active Directory-Portal](concept-sign-ins.md)
