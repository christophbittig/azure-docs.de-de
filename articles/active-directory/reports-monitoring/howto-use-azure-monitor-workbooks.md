---
title: Azure Monitor-Arbeitsmappen für Berichte | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte verwenden.
services: active-directory
author: MarkusVi
manager: karenhoran
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 5/19/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: fe9639049573f62dbd403ab39c3c2067355a60f6
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131995880"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Verwenden von Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte

> [!IMPORTANT]
> Um die zugrunde liegenden Abfragen in dieser Arbeitsmappe zu optimieren, klicken Sie auf „Bearbeiten“, klicken Sie auf das Symbol „Einstellungen“, und wählen Sie den Arbeitsbereich aus, in dem diese Abfragen ausgeführt werden sollen. Für Arbeitsmappen werden standardmäßig alle Arbeitsbereiche ausgewählt, an die Sie die Azure AD-Protokolle weiterleiten. 

Möchten Sie:

- Die Auswirkung Ihrer [Richtlinien für bedingten Zugriff](../conditional-access/overview.md) auf die Anmeldung Ihrer Benutzer verstehen?

- Anmeldefehler beheben, um eine bessere Übersicht über die Anmeldeintegrität in Ihrer Organisation zu erhalten, sowie Probleme schnell beheben?

- Kennen Sie Risikobenutzer und Risikoerkennungstrends in Ihrem Mandanten?

- Wissen, welche Benutzer sich über Legacyauthentifizierungen bei Ihrer Umgebung anmelden? (Durch [Blockieren der Legacyauthentifizierung](../conditional-access/block-legacy-authentication.md) können Sie den Schutz Ihres Mandanten verbessern.)

- Müssen Sie die Auswirkungen der Richtlinien für bedingten Zugriff im Mandanten verstehen?

- Möchten Sie Abfragen des Anmeldeprotokolls mit einer Arbeitsmappe überprüfen können, die angibt, wie vielen Benutzern der Zugriff gewährt oder verweigert wurde und wie viele Benutzer beim Zugriff auf Ressourcen die Richtlinien für bedingten Zugriff umgangen haben?

- Sind Sie daran interessiert, ein tieferes Verständnis für bedingten Zugriff zu entwickeln, mit einer Arbeitsmappe mit Details pro Bedingung, sodass die Auswirkungen einer Richtlinie pro Bedingung kontextualisiert werden können, einschließlich Geräteplattform, Gerätezustand, Client-App, Anmeldungsrisiko, Standort und Anwendung?

- Möchten Sie für mehr als ein Jahr historischer Anwendungsrollen und [Zugriffspaket-Zuordnungsaktivitäten](../governance/entitlement-management-logs-and-reporting.md) archivieren und berichten?

Damit Sie diese Fragen beantworten können, stellt Azure Active Directory Arbeitsmappen für die Überwachung bereit. [Azure Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md) kombinieren Text, Analyseabfragen, Metriken und Parameter zu umfassenden interaktiven Berichten.



Dieser Artikel:

- Setzt voraus, dass Sie mit dem [Erstellen interaktiver Berichte mit Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md) vertraut sind.

- Erläutert die Verwendung von Monitor-Arbeitsmappen, um die Auswirkungen Ihrer Richtlinien für bedingten Zugriff verstehen, Anmeldefehler beheben und Legacyauthentifizierungen identifizieren zu können.
 


## <a name="prerequisites"></a>Voraussetzungen

Zum Verwenden von Monitor-Arbeitsmappen benötigen Sie:

- Einen Azure Active Directory-Mandanten mit einer Premium-Lizenz (P1 oder P2). Wie Sie eine Premium-Lizenz erhalten, erfahren Sie [hier](../fundamentals/active-directory-get-started-premium.md).

- Einen [Log Analytics-Arbeitsbereich](../../azure-monitor/logs/quick-create-workspace.md).

- [Zugriff](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) auf den Log Analytics-Arbeitsbereich
- Folgende Rollen in Azure Active Directory (beim Zugriff auf Log Analytics über das Azure Active Directory-Portal)
    - Sicherheitsadministrator
    - Sicherheitsleseberechtigter
    - Berichtsleseberechtigter
    - Globaler Administrator

## <a name="roles"></a>Rollen
Sie müssen Mitglied einer der folgenden Rollen sein und über [Zugriff auf den zugrunde liegenden Log Analytics-Arbeitsbereich](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) verfügen, um die Arbeitsmappen verwalten zu können:
-   Globaler Administrator
-   Sicherheitsadministrator
-   Sicherheitsleseberechtigter
-   Berichtsleseberechtigter
-   Anwendungsadministrator

## <a name="workbook-access"></a>Arbeitsmappenzugriff 

So greifen Sie auf Arbeitsmappen zu

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie zu **Azure Active Directory** > **Überwachung** > **Arbeitsmappen**. 

1. Wählen Sie einen Bericht oder eine Vorlage aus, oder wählen Sie auf der Symbolleiste **Öffnen** aus. 

![Suchen der Azure Monitor-Arbeitsmappen in Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Analyse der Anmeldungen

Wählen Sie für den Zugriff auf die Arbeitsmappe zur Analyse der Anmeldungen im Abschnitt **Nutzung** die Option **Anmeldungen** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für Anmeldungen angezeigt:

- Alle Anmeldungen

- Erfolg

- Benutzeraktion ausstehend

- Fehler

Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Analyse der Anmeldungen](./media/howto-use-azure-monitor-workbooks/43.png)


Für jeden Trend erhalten Sie eine Aufschlüsselung nach folgenden Kategorien:

- Standort

    ![Anmeldungen nach Ort](./media/howto-use-azure-monitor-workbooks/45.png)

- Sicherungsmedium

    ![Anmeldungen nach Gerät](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Anmeldungen mit Legacyauthentifizierung 


Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen mit [Legacyauthentifizierung](../conditional-access/block-legacy-authentication.md) im Abschnitt **Nutzung** die Option **Anmeldungen mit Legacyauthentifizierung** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für Anmeldungen angezeigt:

- Alle Anmeldungen

- Erfolg


Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

- Protokolle

![Anmeldungen mit Legacyauthentifizierung](./media/howto-use-azure-monitor-workbooks/47.png)


Für jeden Trend erhalten Sie eine Aufschlüsselung nach App und Protokoll.

![Anmeldungen mit Legacyauthentifizierung nach App und Protokoll](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Anmeldungen mithilfe des bedingten Zugriffs 


Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen nach [Richtlinien für bedingten Zugriff](../conditional-access/overview.md) im Abschnitt **Bedingter Zugriff** die Option **Anmeldungen mithilfe des bedingten Zugriffs** aus. 

Mit dieser Arbeitsmappe werden die Trends für deaktivierte Anmeldungen angezeigt. Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Anmeldungen mithilfe des bedingten Zugriffs](./media/howto-use-azure-monitor-workbooks/49.png)


Für deaktivierte Anmeldungen erhalten Sie eine Aufschlüsselung nach dem Status des bedingten Zugriffs.

![Screenshot: Status des bedingten Zugriffs und kürzlich erfolgte Anmeldungen](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Erkenntnisse zum bedingten Zugriff

### <a name="overview"></a>Übersicht

Arbeitsmappen enthalten Anmeldeprotokollabfragen, mit denen IT-Administratoren die Auswirkung von Richtlinien für bedingten Zugriff im Mandanten überwachen können. Sie können berichten, wie vielen Benutzern der Zugriff gewährt oder verweigert wurde. Die Arbeitsmappe enthält Informationen darüber, wie viele Benutzer die Richtlinien für bedingten Zugriff umgangen haben, auf Grundlage der Attribute der Benutzer zum Zeitpunkt der Anmeldung. Sie enthält Informationen pro Bedingung, sodass die Auswirkung einer Richtlinie pro Bedingung, einschließlich Geräteplattform, Gerätestatus, Client-App, Anmelderisiko, Standort und Anwendung, kontextualisiert werden kann.

### <a name="instructions"></a>Instructions 
Um auf die Arbeitsmappe für Erkenntnisse zum bedingten Zugriff zuzugreifen, wählen Sie im Abschnitt „Bedingter Zugriff“ die Arbeitsmappe **Erkenntnisse zum bedingten Zugriff** aus. In dieser Arbeitsmappe werden die erwarteten Auswirkungen der einzelnen Richtlinien für bedingten Zugriff im Mandanten angezeigt. Wählen Sie in der Dropdownliste eine oder mehrere Richtlinien für bedingten Zugriff aus, und schränken Sie den Bereich der Arbeitsmappe ein, indem Sie die folgenden Filter anwenden: 

- **Zeitbereich**

- **Benutzer**

- **Apps**

- **Datenansicht**

![Screenshot: Bereich „Bedingter Zugriff“, in dem Sie eine Richtlinie für bedingten Zugriff auswählen können](./media/howto-use-azure-monitor-workbooks/access-insights.png)


In „Zusammenfassung der Auswirkungen“ wird die Anzahl der Benutzer oder Anmeldungen angezeigt, für die die ausgewählten Richtlinien ein bestimmtes Ergebnis hatten. „Gesamt“ gibt die Anzahl der Benutzer oder Anmeldungen an, für die die ausgewählten Richtlinien im ausgewählten Zeitbereich ausgewertet wurden. Klicken Sie auf eine Kachel, um die Daten in der Arbeitsmappe nach dem entsprechenden Ergebnistyp zu filtern. 

![Screenshot: Kacheln zum Filtern von Ergebnissen z. B. nach Gesamtzahl oder nach erfolgreichen und fehlerhaften Anmeldungen](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

In dieser Arbeitsmappe werden auch die Auswirkungen der ausgewählten Richtlinien nach sechs einzelnen Bedingungen untergliedert angezeigt: 
- **Gerätestatus**
- **Geräteplattform**
- **Client-Apps**
- **Anmelderisiko**
- **Location**
- **Anwendungen**

![Screenshot: Details nach Verwendung des Filters für die Gesamtzahl der Anmeldungen](./media/howto-use-azure-monitor-workbooks/device-platform.png)

Sie können auch einzelne Anmeldungen untersuchen, die nach den in der Arbeitsmappe ausgewählten Parametern gefiltert sind. Suchen Sie nach einzelnen Benutzern, sortiert nach Anmeldehäufigkeit, und zeigen Sie die entsprechenden Anmeldeereignisse an. 

![Screenshot: Individuelle Anmeldungen, die Sie überprüfen können](./media/howto-use-azure-monitor-workbooks/filtered.png)

## <a name="sign-ins-by-grant-controls"></a>Anmeldungen nach Steuerelementen zur Rechteerteilung

Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen nach [Steuerelementen zur Rechteerteilung](../conditional-access/controls.md) im Abschnitt **Bedingter Zugriff** die Option **Anmeldungen nach Steuerelementen zur Rechteerteilung** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für deaktivierte Anmeldungen angezeigt:

- Anfordern von MFA
 
- Vorschreiben der Verwendung von Nutzungsbedingungen

- Vorschreiben von Datenschutzbestimmungen

- Andere


Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Anmeldungen nach Steuerelementen zur Rechteerteilung](./media/howto-use-azure-monitor-workbooks/50.png)


Für jeden Trend erhalten Sie eine Aufschlüsselung nach App und Protokoll.

![Aufschlüsselung der aktuellen Anmeldungen](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse der Anmeldungsfehler

Verwenden Sie die Arbeitsmappe zur **Analyse der Anmeldungsfehler**, um Fehler in folgenden Bereichen zu beheben:

- Anmeldungen
- Richtlinien für bedingten Zugriff
- Legacyauthentifizierung 


Wählen Sie für den Zugriff auf die Arbeitsmappe für Anmeldungen nach Daten zum bedingten Zugriff im Abschnitt **Problembehandlung** die Option **Anmeldungen mit Legacyauthentifizierung** aus. 

Mit dieser Arbeitsmappe werden die folgenden Trends für Anmeldungen angezeigt:

- Alle Anmeldungen

- Erfolg

- Ausstehende Aktion

- Fehler


Sie können jeden Trend nach den folgenden Kategorien filtern:

- Uhrzeitbereich

- Apps

- Benutzer

![Problembehandlung bei Anmeldungen](./media/howto-use-azure-monitor-workbooks/52.png)


Damit Sie Probleme mit Anmeldungen beheben können, bietet Azure Monitor Ihnen eine Aufschlüsselung nach den folgenden Kategorien:

- Häufigste Fehler

    ![Zusammenfassung der häufigsten Fehler](./media/howto-use-azure-monitor-workbooks/53.png)

- Anmeldungen mit Benutzeraktionen

    ![Zusammenfassung der Anmeldungen mit Benutzeraktionen](./media/howto-use-azure-monitor-workbooks/54.png)


## <a name="identity-protection-risk-analysis"></a>Identity Protection-Risikoanalyse

Verwenden Sie im Bereich **Nutzung** die Arbeitsmappe **Identity Protection-Risikoanalyse**, um Folgendes besser zu verstehen:

- Verteilung von Risikobenutzern und Risikoerkennungen nach Stufe und Typ
- Möglichkeiten zur besseren Risikobehandlung
- Wo in der Welt das Risiko erkannt wird

Die Trends für die Risikoerkennung können Sie nach folgenden Optionen filtern:
- Typ des Erkennungszeitpunkts
- Risikostufe

Risikoerkennungen in Echtzeit sind solche, die zum Zeitpunkt der Authentifizierung erkannt werden können. Mithilfe des bedingten Zugriffs und Richtlinien für Risikoanmeldungen können Sie den Herausforderungen durch diese Risikoerkennungen begegnen und eine mehrstufige Authentifizierung anfordern. 

Die Trends für Risikobenutzer können Sie nach folgenden Optionen filtern:
- Risikodetail
- Risikostufe

Wenn Sie eine große Anzahl von Risikobenutzern haben, bei denen „keine Aktion“ ergriffen wurde, sollten Sie erwägen, eine Richtlinie für bedingten Zugriff zu aktivieren, um eine sichere Kennwortänderung anzufordern, wenn ein Benutzer ein hohes Risiko aufweist.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen interaktiver Berichte mit Monitor-Arbeitsmappen](../../azure-monitor/visualize/workbooks-overview.md).
* [Erstellen von benutzerdefinierten Azure Monitor-Abfragen mit Azure PowerShell](../governance/entitlement-management-logs-and-reporting.md).
