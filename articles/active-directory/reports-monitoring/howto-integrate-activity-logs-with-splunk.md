---
title: Integrieren von Splunk mit Azure Monitor | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Azure Active Directory-Protokolle mithilfe von Azure Monitor in Splunk integrieren.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/05/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: a91a5df939ee55b37369b73e02fa0921a9e4688c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355125"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Gewusst wie: Integrieren von Azure Active Directory-Protokollen in Splunk mit Azure Monitor

In diesem Artikel erfahren Sie, wie Sie Azure Active Directory-Protokolle (Azure AD) in Splunk mit Azure Monitor integrieren. Zunächst leiten Sie die Protokolle an einen Azure Event Hub weiter und integrieren dann den Event Hub in Splunk.

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um dieses Feature verwenden zu können:

- Ein Azure Event Hub, der Azure AD-Aktivitätsprotokolle enthält. Erfahren Sie, [wie Sie Aktivitätsprotokolle an einen Event Hub streamen](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  Das [Splunk-Add-On für Microsoft Cloud Services](https://splunkbase.splunk.com/app/3110/#/details). 

## <a name="integrate-azure-active-directory-logs"></a>Integrieren von Azure Active Directory-Protokollen 

1. Öffnen Sie Ihre Splunk-Instanz, und wählen Sie **Data Summary** (Datenzusammenfassung) aus.

    ![Die Schaltfläche für die Datenzusammenfassung](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Wählen Sie die Registerkarte **Sourcetypes** (Quelltypen) und dann **mscs:azure:eventhub** aus.

    ![Die Registerkarte mit Quelltypen der Datenzusammenfassung](./media/howto-integrate-activity-logs-with-splunk/source-eventhub.png)

Fügen Sie **body.records.category=AuditLogs** an die Suche an. Die Azure AD-Aktivitätsprotokolle werden wie in der folgenden Abbildung angezeigt:

   ![Aktivitätsprotokolle](./media/howto-integrate-activity-logs-with-splunk/activity-logs.png)

> [!NOTE]
> Falls Sie kein Add-On in Ihrer Splunk-Instanz installieren können, z.B. bei Verwendung eines Proxys oder bei Ausführung in Splunk Cloud, können Sie diese Ereignisse an die HTTP-Ereignissammlung von Splunk weiterleiten. Verwenden Sie dazu diese [Azure-Funktion](https://github.com/splunk/azure-functions-splunk), die durch neue Nachrichten in Event Hub ausgelöst wird. 
>

## <a name="next-steps"></a>Nächste Schritte

* [Interpret audit logs schema in Azure Monitor](./overview-reports.md) (Interpretieren des Überwachungsprotokollschemas in Azure Monitor)
* [Interpret sign-in logs schema in Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md) (Interpretieren des Anmeldeprotokollschemas in Azure Monitor)
* [Häufig gestellte Fragen und bekannte Probleme](concept-activity-logs-azure-monitor.md#frequently-asked-questions)