---
title: Diagnose- und Lösungstool
description: Erfahren Sie, wie Sie Probleme mit Ihrer App in Azure App Service mit dem Diagnose- und Lösungstool im Azure-Portal beheben können.
keywords: App Service, Azure App Service, Diagnose, Unterstützung, Web-App, Problembehandlung, Selbsthilfe
ms.topic: article
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: e912e1794f789ff2b670f512c3d575e95715d0ae
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131465837"
---
# <a name="azure-app-service-diagnostics-overview"></a>Übersicht über die Azure App Service-Diagnose

Wenn Sie eine Webanwendung ausführen, möchten Sie auf möglicherweise auftretende Probleme vorbereitet sein – von Fehlern mit dem Code 500 bis zu Benachrichtigungen durch Benutzer, dass die Website ausgefallen ist. Die App Service-Diagnose ist eine intelligente und interaktive Komponente, mit der Sie Probleme bei Ihrer App beheben können, ohne dass eine Konfiguration erforderlich ist. Wenn bei Ihrer App Probleme auftreten, zeigt die App Service-Diagnose den Fehler auf, damit Sie die richtigen Informationen finden, um das Problem einfacher und schneller behandeln und lösen zu können.

Diese Komponente ist besonders hilfreich, wenn innerhalb der letzten 24 Stunden Probleme bei der App aufgetreten sind, jedoch stehen Ihnen alle Diagnosediagramme jederzeit für die Analyse zur Verfügung.

Die App Service-Diagnose funktioniert nicht nur bei Ihrer App unter Windows, sondern auch bei Apps für [Linux-Container](./overview.md#app-service-on-linux), die [App Service-Umgebung](./environment/intro.md) und [Azure Functions](../azure-functions/functions-overview.md).

## <a name="open-app-service-diagnostics"></a>Öffnen der App Service-Diagnose

Navigieren Sie für den Zugriff auf die App Service-Diagnose zu Ihrer App Service-Web-App oder App Service-Umgebung im [Azure-Portal](https://portal.azure.com). Klicken Sie im linken Navigationsbereich auf **Diagnose und Problembehandlung**.

Navigieren Sie für Azure Functions zu Ihrer Funktions-App, klicken Sie im oberen Navigationsbereich auf **Plattformfeatures**, und wählen Sie **Diagnose und Problembehandlung** im Abschnitt **Ressourcenverwaltung** aus.

Auf der Startseite der App Service-Diagnose können Sie mithilfe der Schlüsselwörter auf den einzelnen Kacheln der Startseite die Kategorie auswählen, die auf das Problem mit Ihrer App am ehesten zutrifft. Außerdem finden Sie auf dieser Seite **Diagnosetools**. Siehe [Diagnosetools](#diagnostic-tools).

![Startseite](./media/app-service-diagnostics/app-service-diagnostics-homepage-1.png)

> [!NOTE]
> Wenn Ihre App nicht aktiv oder langsam ist, können Sie [eine Profilerstellungs-Ablaufverfolgung erfassen](https://azure.github.io/AppService/2018/06/06/App-Service-Diagnostics-Profiling-an-ASP.NET-Web-App-on-Azure-App-Service.html), um die Ursache des Problems zu identifizieren. Die Profilerstellung ist schlank und für Produktionsszenarien konzipiert.
>

## <a name="interactive-interface"></a>Interaktive Benutzeroberfläche

Nachdem Sie auf der Startseite eine Kategorie ausgewählt haben, die am ehesten auf das bei der App aufgetretene Problem zutrifft, können Sie über die interaktive Benutzeroberfläche Genie der App Service-Diagnose durch die Diagnose und Problembehebung für Ihre App geleitet werden. Sie können die in Genie angegebenen Kachelverknüpfungen verwenden, um den vollständigen Diagnosebericht der jeweiligen Problemkategorie anzuzeigen. Über die Kachelverknüpfungen haben Sie direkt die Möglichkeit, auf die Diagnosemetriken zuzugreifen.

![Kachelverknüpfungen](./media/app-service-diagnostics/tile-shortcuts-2.png)

Nach dem Klicken auf diese Kacheln wird eine Liste mit Themen angezeigt, die sich auf das in der jeweiligen Kachel beschriebene Problem beziehen. Diese Themen enthalten Auszüge mit relevanten Informationen aus dem vollständigen Bericht. Sie können auf jedes einzelne Thema klicken, um die Probleme weiter zu untersuchen. Zudem können Sie auf **Vollständigen Bericht anzeigen** klicken, um alle Themen auf einer Seite zu untersuchen.

![Themen](./media/app-service-diagnostics/application-logs-insights-3.png)

![Vollständigen Bericht anzeigen](./media/app-service-diagnostics/view-full-report-4.png)

## <a name="diagnostic-report"></a>Diagnosebericht

Nachdem Sie auf ein Thema geklickt haben, um das Problem weiter zu untersuchen, können Sie weitere Details zum Thema anzeigen, die häufig durch Diagramme und Markdowns ergänzt werden. Der Diagnosebericht kann sich als leistungsfähiges Tool für die genaue Ermittlung des Problems mit Ihrer App erweisen.

![Diagnosebericht](./media/app-service-diagnostics/full-diagnostic-report-5.png)

## <a name="health-checkup"></a>Integritätsüberprüfung

Wenn Sie nicht wissen, welches Problem in der App auftritt oder mit welchen Problembehandlungsschritten Sie beginnen sollten, empfiehlt sich zunächst das Ausführen einer Integritätsüberprüfung. Bei der Integritätsüberprüfung werden Ihre Anwendungen analysiert, um Ihnen eine schnelle, interaktive Übersicht zu bieten. In dieser werden ordnungsgemäße und problembehaftete Elemente angegeben, und Sie erhalten Hinweise auf Bereiche, die Sie zum Untersuchen des Problems überprüfen sollten. Die intelligente und interaktive Benutzeroberfläche leitet Sie durch den Prozess der Problembehandlung. Die Integritätsüberprüfung ist in der Genie-Umgebung für Windows-Apps und im Diagnosebericht „Web-App ausgefallen“ für Linux-Apps integriert.

### <a name="health-checkup-graphs"></a>Diagramme in der Integritätsüberprüfung

Es gibt vier verschiedene Diagramme in der Integritätsüberprüfung.

- **Anforderungen und Fehler:** In diesem Diagramm werden die Anzahl der in den letzten 24 Stunden gesendeten Anforderungen mit den HTTP-Serverfehlern angezeigt.
- **Anwendungsleistung:** In diesem Diagramm wird die Antwortzeit in den letzten 24 Stunden für verschiedene Perzentilgruppen angezeigt.
- **CPU-Auslastung:** In diesem Diagramm wird die prozentuale CPU-Gesamtauslastung pro Instanz in den letzten 24 Stunden angezeigt.  
- **Speicherauslastung:** In diesem Diagramm wird die prozentuale Gesamtauslastung des physischen Speichers pro Instanz in den letzten 24 Stunden angezeigt.

![Integritätsüberprüfung](./media/app-service-diagnostics/health-checkup-6.png)

### <a name="investigate-application-code-issues-only-for-windows-app"></a>Untersuchen von Problemen mit dem Anwendungscode (nur für Windows-Apps)

Da viele App-Probleme auf Ihren Anwendungscode zurückzuführen sind, ist die App Service-Diagnose in [Application Insights](../azure-monitor/app/app-insights-overview.md) integriert, um Ausnahmen und Probleme mit Abhängigkeiten zur entsprechend ausgewählten Ausfallzeiten hervorzuheben. Application Insights muss separat aktiviert werden.

![Application Insights](./media/app-service-diagnostics/application-insights-7.png)

Wählen Sie zum Anzeigen von Application Insights-Ausnahmen und -Abhängigkeiten eine der Kachelverknüpfungen **Web-App ausgefallen** oder **Web-App langsam** aus.

### <a name="troubleshooting-steps-only-for-windows-app"></a>Schritte zur Problembehandlung (nur für Windows-Apps)

Wenn innerhalb der letzten 24 Stunden ein Problem einer bestimmten Kategorie erkannt wurde, können Sie den vollständigen Diagnosebericht anzeigen. Möglicherweise werden Sie von der App Service-Diagnose aufgefordert, weitere Hinweise zur Fehlerbehandlung und die nächsten Schritte anzuzeigen, um eine umfassendere Anleitung zu erhalten.

![Application Insights, Problembehandlung und nächste Schritte](./media/app-service-diagnostics/troubleshooting-and-next-steps-8.png)

## <a name="diagnostic-tools"></a>Diagnosetools 

Diagnosetools umfassen erweiterte Diagnosetools, mit denen Sie Probleme mit dem Anwendungscode, langsame Ausführung, Verbindungszeichenfolgen und vieles mehr untersuchen können. Darüber hinaus sind proaktive Tools enthalten, mit denen Sie Probleme mit der CPU-Auslastung, mit Anforderungen und dem Speicher beheben können.

### <a name="proactive-cpu-monitoring-only-for-windows-app"></a>Proaktive CPU-Überwachung (nur für Windows-Apps)

Die proaktive CPU-Überwachung stellt eine einfache und proaktive Möglichkeit für Sie dar, mit einer Aktion einzugreifen, wenn eine App oder ein untergeordneter Prozess der App CPU-Ressourcen in hohem Maße auslastet. Sie können eigene Regeln für den CPU-Schwellenwert festlegen, um eine hohe CPU-Auslastung vorübergehend zu beheben, bis die tatsächliche Ursache für das unerwartete Problem gefunden wird. Weitere Informationen finden Sie unter [Behandeln von CPU-Problemen, bevor sie auftreten](https://azure.github.io/AppService/2019/10/07/Mitigate-your-CPU-problems-before-they-even-happen.html).

![Proaktive CPU-Überwachung](./media/app-service-diagnostics/proactive-cpu-monitoring-9.png)

### <a name="auto-healing"></a>Automatische Reparatur 

Die automatische Reparatur ist eine Abhilfemaßnahme, die Sie durchführen können, wenn Ihre App ein unerwartetes Verhalten aufweist. Sie können eigene Regeln basierend auf der Anzahl der Anforderungen, langsamen Anforderungen, dem Speicherlimit und dem HTTP-Statuscode festlegen, um Abhilfeaktionen auszulösen. Verwenden Sie das Tool, um ein unerwartetes Verhalten vorübergehend einzudämmen, bis Sie die Ursache gefunden haben. Das Tool steht derzeit nur für Windows-Web-Apps, Linux-Web-Apps und benutzerdefinierte Linux-Container zur Verfügung. Unterstützte Bedingungen und Entschärfungen variieren je nach Typ der Web-App. Weitere Informationen finden Sie unter [Ankündigung der neuen Funktion zur automatischen Reparatur in der App Service-Diagnose](https://azure.github.io/AppService/2018/09/10/Announcing-the-New-Auto-Healing-Experience-in-App-Service-Diagnostics.html) und unter [Ankündigung der automatischen Reparatur für Linux](https://azure.github.io/AppService/2021/04/21/Announcing-Autoheal-for-Azure-App-Service-Linux.html).

![Automatische Reparatur](./media/app-service-diagnostics/auto-healing-10.png)

### <a name="proactive-auto-healing-only-for-windows-app"></a>Proaktive automatische Reparatur (nur für Windows-Apps)

Wie die proaktive CPU-Überwachung ist die proaktive automatische Reparatur eine einfache Lösung, unerwartetes Verhalten Ihrer App in den Griff zu bekommen. Die proaktive automatische Reparatur startet Ihre App neu, wenn App Service feststellt, dass sie sich in einem nicht wiederherstellbaren Zustand befindet. Weitere Informationen finden Sie in der [Einführung in die proaktive automatische Reparatur](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html).

## <a name="navigator-and-change-analysis-only-for-windows-app"></a>Navigator und Änderungsanalyse (nur für Windows-Apps)

In einem großen Team mit Continuous Integration und einer App mit vielen Abhängigkeiten kann es schwierig sein, die spezifische Änderung zu ermitteln, die ein fehlerhaftes Verhalten verursacht. Navigator hilft Ihnen, die Topologie Ihrer App sichtbar zu machen, indem eine Abhängigkeitsübersicht Ihrer App und aller Ressourcen im selben Abonnement automatisch gerendert wird. Mit Navigator können Sie eine konsolidierte Liste der von Ihrer App und ihren Abhängigkeiten vorgenommenen Änderungen einsehen und auf eine Änderung eingrenzen, die ein fehlerhaftes Verhalten bewirkt. Der Zugriff erfolgt über die Kachel **Navigator** auf der Startseite. Navigator muss aktiviert werden, ehe Sie das Tool erstmals einsetzen. Weitere Informationen finden [Get visibility into your app's dependencies with Navigator](https://azure.github.io/AppService/2019/08/06/Bring-visibility-to-your-app-and-its-dependencies-with-Navigator.html) (Verschaffen von Einblicken in die Abhängigkeiten Ihrer App mit Navigator).

![Standardseite von Navigator](./media/app-service-diagnostics/navigator-default-page-11.png)

![Vergleichsansicht](./media/app-service-diagnostics/diff-view-12.png)

Die Änderungsanalyse für Anwendungsänderungen kann über Kachelverknüpfungen, **Anwendungsänderungen** und **Anwendungsabstürze** in **Verfügbarkeit und Leistung** aufgerufen werden, sodass Sie sie mit anderen Metriken gleichzeitig verwenden können. Bevor Sie dieses Feature verwenden können, müssen Sie es zuerst aktivieren. Weitere Informationen finden Sie unter [Announcing the new change analysis experience in App Service Diagnostics](https://azure.github.io/AppService/2019/05/07/Announcing-the-new-change-analysis-experience-in-App-Service-Diagnostics-Analysis.html) (Bekanntgabe der neuen Funktion zur Änderungsanalyse in der App Service-Diagnose).

Posten Sie Ihre Fragen oder Ihr Feedback auf [UserVoice](https://feedback.azure.com/d365community/forum/b09330d1-c625-ec11-b6e6-000d3a4f0f1c), indem Sie dem Titel „[Diag]“ hinzufügen.
