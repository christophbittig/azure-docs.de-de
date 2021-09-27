---
title: Arbeitsmappe für Erkenntnisse und Berichterstellung für den bedingten Zugriff mit Azure Active Directory
description: Verwenden der Arbeitsmappe für Erkenntnisse und Berichterstellung für den bedingten Zugriff mit Azure AD zur Problembehandlung bei Richtlinien
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 919003a8ad8b1ede8a7e5064e5e499be45d62019
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577296"
---
# <a name="conditional-access-insights-and-reporting"></a>Erkenntnisse und Berichterstellung zum bedingten Zugriff

Mithilfe der Arbeitsmappe für Erkenntnisse und Berichterstellung für den bedingten Zugriff können Sie die Auswirkungen von Richtlinien für den bedingten Zugriff in Ihrer Organisation im zeitlichen Verlauf nachvollziehen. Bei der Anmeldung können eine oder mehrere Richtlinien für den bedingten Zugriff angewendet werden, wobei der Zugriff bei erfolgreicher Ausführung bestimmter Gewährungssteuerelemente gewährt oder andernfalls verweigert wird. Da bei jeder Anmeldung mehrere Richtlinien für den bedingten Zugriff ausgewertet werden können, können Sie in der Arbeitsmappe für Erkenntnisse und Berichterstellung die Auswirkungen einer bestimmten Richtlinie oder einer Teilmenge aller Richtlinien überprüfen.  

## <a name="prerequisites"></a>Voraussetzungen

Zum Aktivieren der Arbeitsmappe für Erkenntnisse und Berichterstellung muss Ihr Mandant über einen Log Analytics-Arbeitsbereich verfügen, um Anmeldeprotokolldaten beizubehalten. Um den bedingten Zugriff verwenden zu können, müssen Sie über eine Azure AD Premium P1- oder P2-Lizenz verfügen.

Die folgenden Rollen können auf die Arbeitsmappe für Erkenntnisse und Berichterstellung zugreifen:  

- Administrator für den bedingten Zugriff 
- Sicherheitsleseberechtigter 
- Sicherheitsadministrator 
- Globaler Leser 
- Globaler Administrator 

Außerdem benötigen Sie eine der folgenden Log Analytics-Arbeitsbereichsrollen:  

- Mitwirkender  
- Besitzer 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Streamen von Anmeldeprotokollen aus Azure AD in Azure Monitor-Protokolle 

Wenn in Azure Monitor-Protokolle keine Azure AD-Protokolle integriert sind, müssen Sie die folgenden Schritte ausführen, bevor die Arbeitsmappe geladen werden kann:  

1. [Erstellen Sie einen Log Analytics-Arbeitsbereich in Azure Monitor.](../../azure-monitor/logs/quick-create-workspace.md)
1. [Integrieren Sie Azure AD-Protokolle in Azure Monitor-Protokolle.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="how-it-works"></a>Funktionsweise 

So greifen Sie auf die Arbeitsmappe für Erkenntnisse und Berichterstellung zu  

1. Melden Sie sich beim **Azure-Portal** an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff** > **Erkenntnisse und Berichterstellung**.

### <a name="get-started-select-parameters"></a>Erste Schritte: Wählen Sie die Parameter aus. 

Mit dem Dashboard für Erkenntnisse und Berichterstellung können Sie die Auswirkung einer oder mehrerer Richtlinien für den bedingten Zugriff in einem bestimmten Zeitraum verfolgen. Legen Sie zunächst die einzelnen Parameter oben in der Arbeitsmappe fest. 

![Dashboard für Erkenntnisse und Berichterstellung für den bedingten Zugriff im Azure-Portal](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Richtlinie für bedingten Zugriff**: Wählen Sie mindestens eine Richtlinie für den bedingten Zugriff aus, um ihre gemeinsame Auswirkung anzuzeigen. Die Richtlinien sind in zwei Gruppen unterteilt: Aktivierte und Nur-Bericht-Richtlinien. Standardmäßig sind alle aktivierten Richtlinien ausgewählt. In Ihrem Mandanten werden derzeit die aktivierten Richtlinien erzwungen.  

**Zeitbereich**: Wählen Sie einen Zeitbereich von 4 Stunden bis zu 90 Tagen aus. Wenn Sie einen Zeitbereich auswählen, der weiter zurückliegt als die Integration der Azure AD-Protokolle in Azure Monitor, werden nur die Anmeldungen nach dem Zeitpunkt der Integration angezeigt.  

**Benutzer:** Auf dem Dashboard werden die Auswirkungen der ausgewählten Richtlinien standardmäßig für alle Benutzer angezeigt. Um nach einem bestimmten Benutzer zu filtern, geben Sie seinen Namen in das Textfeld ein. Wenn Sie nach allen Benutzern filtern möchten, geben Sie „Alle Benutzer“ in das Textfeld ein, oder lassen Sie den Parameter leer. 

**App**: Auf dem Dashboard werden die Auswirkungen der ausgewählten Richtlinien standardmäßig für alle Apps angezeigt. Um nach einer bestimmten App zu filtern, geben Sie ihren Namen in das Textfeld ein. Wenn Sie nach allen Apps filtern möchten, geben Sie „Alle Apps“ in das Textfeld ein, oder lassen Sie den Parameter leer. 

**Datenansicht**: Wählen Sie aus, ob das Dashboard Ergebnisse mit der Anzahl der Benutzer oder der Anzahl der Anmeldungen anzeigen soll. Für einen einzelnen Benutzer werden möglicherweise Hunderte von Anmeldungen bei vielen verschiedenen Apps mit vielen unterschiedlichen Ergebnissen für einen bestimmten Zeitbereich angezeigt. Wenn Sie die Datenansicht als Benutzer auswählen, kann sowohl in der Erfolgs- als auch in der Fehleranzahl ein Benutzer enthalten sein. Bei 10 Benutzern könnten demnach in den letzten 30 Tagen 8 von ihnen ein erfolgreiches Ergebnis und 9 von ihnen ein Fehlerergebnis gehabt haben.

## <a name="impact-summary"></a>Zusammenfassung der Auswirkungen 

Nachdem die Parameter festgelegt wurden, wird die Zusammenfassung der Auswirkungen geladen. Die Zusammenfassung zeigt, bei wie vielen Benutzern oder Anmeldungen beim Auswerten der ausgewählten Richtlinien die Ergebnisse „Erfolg“, „Fehler“, „Benutzeraktion erforderlich“ oder „Nicht angewendet“ in einem bestimmten Zeitbereich verzeichnet wurden.  

![Zusammenfassung der Auswirkungen in der Arbeitsmappe für den bedingten Zugriff](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Total**: Die Anzahl der Benutzer oder Anmeldungen in einem bestimmten Zeitraum, in dem mindestens eine der ausgewählten Richtlinien ausgewertet wurde

**Erfolg**: Die Anzahl der Benutzer oder Anmeldungen in einem bestimmten Zeitraum, in dem die Auswertung der ausgewählten Richtlinien das kombinierte Ergebnis „Erfolg“ oder „Nur melden: Erfolgreich“ zurückgab.

**Fehler**: Die Anzahl der Benutzer oder Anmeldungen in einem bestimmten Zeitraum, in dem die Auswertung mindestens einer ausgewählten Richtlinie das Ergebnis „Fehler“ oder „Nur melden: Fehler“ zurückgab.

**Benutzeraktion erforderlich**: Die Anzahl der Benutzer oder Anmeldungen in einem bestimmten Zeitraum, in dem die Auswertung der ausgewählten Richtlinien das kombinierte Ergebnis „Nur melden: Benutzeraktion erforderlich“ zurückgab. Es ist eine Benutzeraktion erforderlich, wenn ein interaktives Gewährungssteuerelement wie die mehrstufige Authentifizierung für eine Nur-Bericht-Richtlinie für bedingten Zugriff erforderlich ist. Da interaktive Gewährungssteuerelemente nicht durch Nur-Bericht-Richtlinien erzwungen werden, kann nicht bestimmt werden, ob ein Vorgang erfolgreich oder fehlerhaft war.  

**Nicht angewendet**: Die Anzahl der Benutzer oder Anmeldungen in einem bestimmten Zeitraum, in dem keine der ausgewählten Richtlinien angewendet wurde

### <a name="understanding-the-impact"></a>Grundlegendes zu den Auswirkungen 

![Aufschlüsselung der Arbeitsmappe nach Bedingung und Anmeldestatus](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Zeigen Sie die Aufschlüsselung der Benutzer und Anmeldungen für die jeweiligen Bedingungen an. Sie können die Anmeldungen nach einem bestimmten Ergebnis (z. B. Erfolg oder Fehler) filtern. Klicken Sie hierzu oben in der Arbeitsmappe auf die Kacheln „Zusammenfassung“. Sie können die Aufschlüsselung der Anmeldungen für die einzelnen Bedingungen für den bedingten Zugriff anzeigen: Gerätestatus, Geräteplattform, Client-App, Standort, Anwendung und Anmelderisiko.  

## <a name="sign-in-details"></a>Details zur Anmeldung 

![Details zur Arbeitsmappenanmeldung](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Sie können unten im Dashboard auch die Anmeldungen eines bestimmten Benutzers überprüfen. Die Abfrage auf der linken Seite zeigt die häufigsten Benutzer an. Wenn Sie einen Benutzer auswählen, wird die Abfrage nach rechts gefiltert.  

> [!NOTE]
> Wählen Sie beim Herunterladen der Anmeldeprotokolle das JSON-Format aus, um nur berichtsspezifische Ergebnisdaten für den bedingten Zugriff einzubeziehen.

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Konfigurieren einer Richtlinie für bedingten Zugriff im reinen Berichtsmodus

Gehen Sie wie folgt vor, um eine Richtlinie für bedingten Zugriff im reinen Berichtsmodus zu konfigurieren:

1. Melden Sie sich als Administrator für bedingten Zugriff, Sicherheitsadministrator oder globaler Administrator beim **Azure-Portal** an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie eine vorhandene Richtlinie aus, oder erstellen Sie eine neue Richtlinie.
1. Legen Sie unter **Richtlinie aktivieren** die Umschaltfläche auf den Modus **Nur Bericht** fest.
1. Wählen Sie **Speichern** aus.

> [!TIP]
> Wenn Sie den Status **Richtlinie aktivieren** einer vorhandenen Richtlinie von **Ein** in **Nur melden** ändern, wird dadurch die vorhandene Richtlinienerzwingung deaktiviert. 

## <a name="troubleshooting"></a>Problembehandlung

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Warum schlagen Abfragen aufgrund eines Berechtigungsfehlers fehl?

Um auf die Arbeitsmappe zuzugreifen, benötigen Sie die richtigen Berechtigungen für Azure AD und den Log Analytics-Arbeitsbereich. Um zu testen, ob Sie über die richtigen Berechtigungen für den Arbeitsbereich verfügen, führen Sie eine Log Analytics-Beispielabfrage aus:

1. Melden Sie sich beim **Azure-Portal** an.
1. Navigieren Sie zu **Azure Active Directory** > **Protokolle**.
1. Geben Sie `SigninLogs` im Abfragefeld ein, und wählen Sie **Ausführen** aus.
1. Wenn die Abfrage keine Ergebnisse zurückgibt, wurde der Arbeitsbereich möglicherweise nicht ordnungsgemäß konfiguriert. 

![Problembehandlung bei fehlgeschlagenen Abfragen](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Weitere Informationen zum Streamen von Azure AD Anmeldeprotokollen in einen Log Analytics-Arbeitsbereich finden Sie im Artikel [Integrieren von Azure AD-Protokollen in Azure Monitor-Protokolle](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Warum können die Abfragen in der Arbeitsmappe nicht ausgeführt werden?

Kunden haben festgestellt, dass Abfragen manchmal nicht ausgeführt werden, wenn der Arbeitsmappe falsche oder mehrere Arbeitsbereiche zugeordnet sind. Um dieses Problem zu beheben, klicken Sie oben in der Arbeitsmappe auf **Bearbeiten**, und klicken Sie dann auf das Zahnradsymbol „Einstellungen“. Wählen Sie nicht der Arbeitsmappe zugeordnete Arbeitsbereiche aus, und entfernen Sie diese Arbeitsbereiche. Jeder Arbeitsmappe sollte nur ein Arbeitsbereich zugeordnet sein.

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Warum ist der Parameter für Richtlinien für bedingten Zugriff leer?

Die Liste der Richtlinien wird anhand der Richtlinien generiert, die für das letzte Anmeldeereignis ausgewertet wurden. Wenn es in Ihrem Mandanten keine aktuellen Anmeldungen gibt, müssen Sie möglicherweise einige Minuten warten, bis die Arbeitsmappe die Liste der Richtlinien für bedingten Zugriff lädt. Dies kann unmittelbar nach dem Konfigurieren von Log Analytics passieren oder länger dauern, wenn ein Mandant über keine aktuelle Anmeldeaktivität verfügt.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Warum dauert das Laden der Arbeitsmappe so lange?  

Je nachdem, welchen Zeitbereich Sie ausgewählt haben und wie groß der Mandant ist, muss die Arbeitsmappe eine sehr große Anzahl von Anmeldeereignissen auswerten. Bei großen Mandanten kann das Volumen der Anmeldungen die Log Analytics-Abfragekapazität überschreiten. Verkürzen Sie den Zeitraum auf vier Stunden, und testen Sie, ob die Arbeitsmappe jetzt geladen wird.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Warum werden nach dem Laden der Arbeitsmappe nach einigen Minuten keine Ergebnisse zurückgegeben? 

Wenn die Anzahl der Anmeldungen die Log Analytics-Abfragekapazität überschreitet, gibt die Arbeitsmappe keine Ergebnisse zurück. Verkürzen Sie den Zeitraum auf vier Stunden, und testen Sie, ob die Arbeitsmappe jetzt geladen wird.  

### <a name="can-i-save-my-parameter-selections"></a>Kann ich die Parameterauswahl speichern?  

Sie können die Parameterauswahl am oberen Rand der Arbeitsmappe speichern, indem Sie zu **Azure Active Directory** > **Arbeitsmappen** > **Erkenntnisse und Berichte zum bedingten Zugriff** navigieren. Hier finden Sie die Arbeitsmappenvorlage, in der Sie die Arbeitsmappe bearbeiten und eine Kopie einschließlich der Parameterauswahl in Ihrem Arbeitsbereich unter **Eigene Berichte** oder **Freigegebene Berichte** speichern können. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Kann ich die Arbeitsmappe bearbeiten und zusätzliche Abfragen hinzufügen? 

Sie können die Arbeitsmappe bearbeiten und anpassen, indem Sie zu **Azure Active Directory** > **Arbeitsmappen** > **Erkenntnisse und Berichte zum bedingten Zugriff** navigieren. Hier finden Sie die Arbeitsmappenvorlage, in der Sie die Arbeitsmappe bearbeiten und eine Kopie einschließlich der Parameterauswahl in Ihrem Arbeitsbereich unter **Eigene Berichte** oder **Freigegebene Berichte** speichern können. Klicken Sie oben in der Arbeitsmappe auf **Bearbeiten**, um die Abfragen zu bearbeiten.  
 
## <a name="next-steps"></a>Nächste Schritte

- [Nur-Bericht-Modus des bedingten Zugriffs](concept-conditional-access-report-only.md)

- Weitere Informationen zu Azure AD-Arbeitsmappen finden Sie im Artikel [Verwenden von Azure Monitor-Arbeitsmappen für Azure Active Directory-Berichte](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

- [Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)
