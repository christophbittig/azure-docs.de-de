---
title: Überwachen von App-Lebenszyklusereignissen mithilfe des Azure-Aktivitätsprotokolls und der Azure Service Health
description: Überwachen Sie App-Lebenszyklusereignisse und richten Sie Warnungen mit dem Azure-Aktivitätsprotokoll und der Azure Service Health ein.
author: karlerickson
ms.author: shiqiu
ms.service: spring-cloud
ms.topic: how-to
ms.date: 08/19/2021
ms.custom: devx-track-java
ms.openlocfilehash: 594c8f48a51b386c687feda5d46a2423dd4e973b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131064029"
---
# <a name="monitor-app-lifecycle-events-using-azure-activity-log-and-azure-service-health"></a>Überwachen von App-Lebenszyklusereignissen mithilfe des Azure-Aktivitätsprotokolls und der Azure Service Health

In diesem Artikel erfahren Sie, wie Sie App-Lebenszyklusereignisse überwachsen und Warnungen mit dem Azure-Aktivitätsprotokoll und der Azure Service Health einrichten.

Der Azure Spring Cloud-Dienst bietet integrierte Tools zum Überwachen des Status und der Integrität Ihrer Anwendungen. Die App-Lebenszyklusereignisse helfen Ihnen, alle Änderungen zu verstehen, die an Ihren Anwendungen vorgenommen wurden, damit Sie bei Bedarf Maßnahmen ergreifen können. 

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Eine bereitgestellte Azure Spring Cloud Dienstinstanz und mindestens eine Anwendung, die bereits in Ihrer Dienstinstanz erstellt wurde. Weitere Informationen finden Sie unter [Schnellstart: Bereitstellen Ihrer ersten Spring Boot-App in Azure Spring Cloud](quickstart.md). 

## <a name="monitor-app-lifecycle-events-triggered-by-users-in-azure-activity-logs"></a>Überwachen von App-Lebenszyklusereignissen, die von Benutzern in Azure-Aktivitätsprotokollen ausgelöst werden

[Azure-Aktivitätsprotokolle](../azure-monitor/essentials/activity-log.md) enthalten Ressourcenereignisse, die von Vorgängen ausgegeben werden, die an Ressourcen in Ihrem Abonnement ausgeführt werden. Die folgenden Details für Lebenszyklusereignisse für Anwendungen (Starten, Beenden, Neustart) werden zu den Azure-Aktivitätsprotokollen hinzugefügt:

- Wann der Vorgang durchgeführt wurde
- Status des Vorgangs
- Welche Instanz(en) beim Starten der App erstellt wird/werden
- Welche Instanz(en) gelöscht wird/werden, wenn Sie Ihre App beenden
- Welche Instanz(en) gelöscht und erstellt wird/werden, wenn Sie Ihre App neu starten

Wenn Sie Ihre App beispielsweise neu starten, finden Sie die betroffenen Instanzen auf der Detailseite von dem **Aktivitätsprotokoll** im Azure-Portal.

:::image type="content" source="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" lightbox="media/monitor-app-lifecycle-events/activity-log-restart-detail.png" alt-text="Ein Azure-Portal Screenshot der die Details zum Neustart im Aktivitätsprotokoll zeigt":::

## <a name="monitor-app-lifecycle-events-in-azure-service-health"></a>Überwachen von App-Lebenszyklusereignissen in Azure Service Health

[Azure Resource Health](../service-health/resource-health-overview.md) unterstützt Sie beim Diagnostizieren und Abrufen von Support für Probleme, die sich auf die Verfügbarkeit Ihres Diensts auswirken können. Zu diesen Problemen gehören Dienstvorfälle, geplante Wartungszeiträume und regionale Ausfälle. Die Ereignisse zum Neustarten von Anwendungen werden der Azure Service Health hinzugefügt. Dazu zählen sowohl unerwartete Vorfälle (z. B. ein unerwarteter Absturz einer App) als auch geplante Aktionen (z. B. eine geplante Wartung).

### <a name="monitor-unplanned-app-lifecycle-events"></a>Überwachen von ungeplanten Ereignissen im App-Lebenszyklus

Wenn Ihre Anwendung aufgrund ungeplanter Ereignisse neu gestartet wird, zeigt Ihre Azure Spring Cloud-Instanz im Abschnitt **Ressourcenintegrität** des Microsoft Azure-Portals den Status **Beeinträchtigt** an. Beeinträchtigt bedeutet, dass die Ressource einen Leistungsverlust festgestellt hat, obwohl sie noch für die Nutzung verfügbar ist. Beispiele für ungeplante Ereignisse sind App-Absturz, Integritätsprüfungsfehler und Systemausfall.

:::image type="content" source="media/monitor-app-lifecycle-events/resource-health-detail.png" alt-text="Ein Screenshot, der den Bereich Ressourcenintegrität zeigt":::

Den aktuellen Status, die Grundursache und die betroffenen Instanzen finden Sie auf der Seite mit dem Integritätsverlauf.

:::image type="content" source="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" lightbox="media/monitor-app-lifecycle-events/unplanned-app-lifecycle-event-details.png" alt-text="Ein Screenshot, der Beispielprotokolle für ungeplante App-Lebenszyklusereignisse zeigt":::


### <a name="monitor-planned-app-lifecycle-events"></a>Überwachen von geplanten Ereignissen im App-Lebenszyklus

Ihre App kann während der Plattformwartung neu gestartet werden. Sie können über die Seite **Geplante Wartung** von Azure Service Health im Voraus eine Wartungsbenachrichtigung erhalten.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-notification.png" alt-text="Ein Screenshot, der eine Beispielbenachrichtigung für eine geplante Wartung zeigt":::

Wenn eine Plattformwartung erfolgt, zeigt Ihre Azure Spring Cloud-Instanz auch den Status **beeinträchtigt** an. Wenn während der Plattformwartung ein Neustart erforderlich ist, führt Azure Spring Cloud ein paralleles Update aus, um Ihre Anwendungen inkrementell zu aktualisieren. Parallele Updates sind so konzipiert, dass Ihre Workloads ohne Ausfallzeiten aktualisiert werden. Den aktuellen Status finden Sie auf der Seite Integritätsverlauf.

:::image type="content" source="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" lightbox="media/monitor-app-lifecycle-events/planned-maintenance-in-progress.png" alt-text="Ein Screenshot, der eine Beispielprotokolls für die geplante Wartung in Bearbeitung zeigt":::

>[!NOTE]
> Derzeit führt Azure Spring Cloud eine regelmäßige geplante Wartung durch, um die zugrunde liegende Kubernetes-Version alle zwei bis vier Monate zu aktualisieren. Eine ausführliche Wartungszeitachse finden Sie auf der Seite „Azure Service Health Benachrichtigungen“.

## <a name="set-up-alerts"></a>Einrichten von Warnungen

Sie können Warnungen für App-Lebenszyklusereignisse einrichten. Benachrichtigungen zur Dienstintegrität werden im Azure-Aktivitätsprotokoll gespeichert. Das Aktivitätsprotokoll speichert eine große Menge an Informationen. Daher ist eine separate Benutzeroberfläche vorhanden, um die Anzeige und Einrichtung von Benachrichtigungen zur Dienstintegrität zu erleichtern.

In der folgenden Liste werden die wichtigsten Schritte zum Einrichten einer Warnung beschrieben: 

1. Richten Sie eine Aktionsgruppe mit den Aktionen ein, die beim Auslösen einer Warnung ergriffen werden sollen. Beispiele hierfür sind das Senden eines Sprachanrufs, einer SMS, E-Mail oder das Auslösen verschiedener Arten von automatisierten Aktionen. Verschiedene Warnungen können je nach den Bedürfnissen des Benutzers die gleiche Aktionsgruppe oder verschiedene Aktionsgruppen verwenden.
2. Richten Sie Warnungsregeln ein. Die Warnungen verwenden Aktionsgruppen, um Benutzer darüber zu informieren, dass eine Warnung für ein bestimmtes App-Lebenszyklusereignis ausgelöst wurde.

### <a name="set-up-alerts-on-activity-log"></a>Warnungen zum Aktivitätsprotokoll einrichten

Die folgenden Schritte zeigen, wie Sie eine Regel für eine Aktivitätsprotokollwarnung im Azure-Portal erstellen können:

1. Navigieren Sie zum **Aktivitätsprotokoll**, öffnen Sie die Detailseite für ein beliebiges Aktivitätsprotokoll und wählen Sie dann die Option **Neue Warnregel** aus.

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert.png" alt-text="Ein Screenshot, der eine Aktivitätsprotokollwarnung zeigt":::

2. Wählen Sie den **Bereich** für die Warnung aus.

3. Geben Sie als Nächstes die **Warnungsbedingung** an.

   :::image type="content" source="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" lightbox="media/monitor-app-lifecycle-events/activity-log-alert-condition.png" alt-text="Ein Screenshot, der eine Bedingung für eine Aktivitätsprotokollwarnung zeigt":::

4. Wählen Sie **Aktionen** aus and fügen Sie **Warnregeldetails** hinzu.

5. Wählen Sie **Warnungsregel erstellen** aus.

### <a name="set-up-alerts-to-monitor-app-lifecycle-events-in-azure-service-health"></a>Einrichten von Warnungen um App-Lebenszyklusereignissen in Azure Service Health zu überwachen

Die folgenden Schritte zeigen, wie Sie eine Warnungsregel für Dienstintegrationsbenachrichtigungen im Azure-Portal erstellen:

1. Navigieren Sie zu **Ressourcenintegrität** unter **Dienstintegrität** und wählen Sie dann **Ressourcenintegritätswarnung hinzufügen** aus.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-resource-health-alert.png" alt-text="Ein Screenshot, der den Bereichs „Ressourcenintegrität“ mit der hervorgehobener Schaltfläche „Warnung zur Ressourcenintegrität hinzufügen“ zeigt":::

2. Wählen Sie die **Ressource** für die Warnung aus.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-target.png" alt-text="Ein Screenshot, der ein Warnungsziel für eine Ressourcenintegrität zeigt":::

3. Geben Sie als Nächstes die **Warnungsbedingung** an.

   :::image type="content" source="media/monitor-app-lifecycle-events/resource-health-alert-condition.png" alt-text="Ein Screenshot, der eine Bedingung für ein Warnungsziel einer Ressourcenintegrität zeigt":::

4. Wählen Sie **Aktionen** aus and fügen Sie **Warnregeldetails** hinzu.

5. Wählen Sie **Warnungsregel erstellen** aus.

### <a name="set-up-alerts-to-monitor-the-planned-maintenance-notification"></a>Einrichten von Warnungen zum Überwachen der Benachrichtigung über die geplante Wartung

Die folgenden Schritte zeigen, wie Sie eine Warnungsregel für geplante Benachrichtigungen im Azure-Portal erstellen:

1. Navigieren Sie zu **Integritätswarnung** unter **Dienstintegrität** und wählen Sie dann **Dienstintegritätswarnung hinzufügen** aus.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert.png" alt-text="Ein Screenshot, der den Bereich „Integritätswarnungen“ mit einer hervorgehobener Schaltfläche für „Dienstintegrationswarnung hinzufügen“ zeigt":::

2. Geben Sie die Werte für **Abonnement**, **Dienst(e)** , **Region(en)** , **Ereignistyp**, **Aktionen** and **Warnregeldetails** ein.

   :::image type="content" source="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" lightbox="media/monitor-app-lifecycle-events/add-service-health-alert-details.png" alt-text="Ein Screenshot, der den Bereich „Erstellen einer Regelwarnung“ für die Dienstintegrität":::

3. Wählen Sie **Warnungsregel erstellen** aus.

## <a name="next-steps"></a>Nächste Schritte

[Selbstdiagnose und Lösung von Problemen in Azure Spring Cloud](how-to-self-diagnose-solve.md)
