---
title: Vorabbenachrichtigungen für geplante Wartungsereignisse (Vorschau)
description: Erhalten Sie vor einer geplanten Wartung für Azure SQL-Datenbank eine Benachrichtigung.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: scott-kim-sql
ms.author: scottkim
ms.reviewer: mathoma, wiassaf
ms.date: 09/14/2021
ms.openlocfilehash: 89a3f86995d054573897202d2269e4f1f38821e2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128631189"
---
# <a name="advance-notifications-for-planned-maintenance-events-preview"></a>Vorabbenachrichtigungen für geplante Wartungsereignisse (Vorschau)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Vorabbenachrichtigungen (Vorschau) sind für Datenbanken verfügbar, für die ein nicht standardmäßiges [Wartungsfenster (Vorschau)](maintenance-window.md) konfiguriert ist. Vorabbenachrichtigungen bieten dem Kunden die Möglichkeit, Benachrichtigungen so zu konfigurieren, dass sie bis zu 24 Stunden vor einem geplanten Ereignis gesendet werden.

Durch Konfiguration der Benachrichtigungen können Sie Text, E-Mails, Azure-Pushbenachrichtigungen oder Voicemails erhalten, wenn die geplante Wartung in den nächsten 24 Stunden beginnen soll. Weitere Benachrichtigungen werden bei Beginn und am Ende der Wartung gesendet.

Für das **Systemstandard**-Wartungsfenster können keine Vorabbenachrichtigungen konfiguriert werden. Wenn Sie ein Wartungsfenster auswählen, müssen Sie eine andere Option als **Systemstandard** auswählen, um Vorabbenachrichtigungen zu konfigurieren und zu aktivieren.

> [!NOTE]
> Für verwaltete Azure SQL-Instanzen ist zwar die Option zum Auswählen eines Wartungsfensters verfügbar, Vorabbenachrichtigungen derzeit aber nicht. 

## <a name="create-an-advance-notification"></a>Erstellen einer Vorabbenachrichtigung

Vorabbenachrichtigungen sind für Azure SQL-Datenbanken verfügbar, für die das Wartungsfenster konfiguriert ist. 

Führen Sie die folgenden Schritte aus, um eine Benachrichtigung zu aktivieren.  

1. Wechseln Sie zur Seite [Geplante Wartung](https://portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/plannedMaintenance), wählen Sie **Integritätswarnungen** und dann **Service Health-Warnung hinzufügen** aus.

    :::image type="content" source="media/advance-notifications/health-alerts.png" alt-text="Menüoption zum Erstellen einer neuen Integritätswarnung":::

2. Wählen Sie im Abschnitt **Aktionen** die Option **Aktionsgruppen hinzufügen** aus. 

    :::image type="content" source="media/advance-notifications/add-action-group.png" alt-text="Menüoption „Aktionsgruppen hinzufügen“":::

3. Füllen Sie das Formular **Aktionsgruppe erstellen** aus, und wählen Sie dann **Weiter: Benachrichtigungen** aus.  

    :::image type="content" source="media/advance-notifications/create-action-group.png" alt-text="Formular „Aktionsgruppe erstellen“":::

1. Wählen Sie auf der Registerkarte **Benachrichtigungen** den **Benachrichtigungstyp** aus. Die Option **E-Mail/SMS/Push/Sprachanruf** bietet die größtmögliche Flexibilität und wird empfohlen. Wählen Sie den Stift aus, um die Benachrichtigung zu konfigurieren.  

    :::image type="content" source="media/advance-notifications/notifications.png" alt-text="Konfigurieren von Benachrichtigungen":::



   1. Füllen Sie das Formular *Benachrichtigung hinzufügen oder bearbeiten* aus, und wählen Sie dann **OK** aus: 

   2. Aktionen und Tags sind optional einsetzbar. Hier können Sie zusätzliche Aktionen konfigurieren, die ausgelöst werden sollen, oder Ihre Azure-Ressourcen mithilfe von Tags kategorisieren und organisieren. 

   4. Überprüfen Sie die Details auf der Registerkarte **Überprüfen und erstellen**, und wählen Sie **Erstellen** aus. 

7. Nachdem Sie „Erstellen“ ausgewählt haben, wird der Bildschirm zur Konfiguration von Warnungsregeln geöffnet, und die Aktionsgruppe wird ausgewählt. Benennen Sie die neue Warnungsregel, wählen Sie dann die Ressourcengruppe für die Regel aus, und wählen Sie **Warnungsregel erstellen** aus. 

8. Klicken Sie erneut auf das Menüelement **Integritätswarnungen**, und Sie sehen, dass die Liste mit den Warnungen jetzt die neue Warnung enthält. 


Sie sind fertig, Wenn das nächste geplante Azure SQL-Wartungsereignis ansteht, erhalten Sie eine Vorabbenachrichtigung.

## <a name="receiving-notifications"></a>Empfangen von Benachrichtigungen

In der folgenden Tabelle sind die Benachrichtigungen mit allgemeinen Informationen angezeigt, die Sie möglicherweise erhalten: 

|Status|BESCHREIBUNG|
|:---|:---|
|**Geplante Bereitstellung**| Eingang 24 Stunden vor dem Wartungsereignis. Für das genannte Datum zwischen 17 Uhr und 8 Uhr (Ortszeit) ist eine Wartung für Datenbank XYZ geplant.|
|**Wird ausgeführt** | Die Wartung für Datenbank *XYZ*  beginnt.| 
|**Abgeschlossen** | Die Wartung der Datenbank *XYZ* ist abgeschlossen. |

In der folgenden Tabelle sind zusätzliche Benachrichtigungen aufgeführt, die möglicherweise während der Wartung gesendet werden: 

|Status|BESCHREIBUNG|
|:---|:---|
|**Erweitert** | Wartung wurde für Datenbank *XYZ* ausgeführt, aber nicht abgeschlossen. Die Wartung wird beim nächsten Wartungsfenster fortgesetzt.| 
|**Canceled**| Die Wartung für Datenbank *XYZ* wurde abgebrochen und wird später neu geplant. |
|**Gesperrt**|Bei der Wartung der Datenbank *XYZ* ist ein Problem aufgetreten. Sie werden benachrichtigt, wenn die Wartung fortgesetzt wird.| 
|**Resumed**|Das Problem wurde behoben, und die Wartung wird im nächsten Wartungsfenster fortgesetzt.|


## <a name="next-steps"></a>Nächste Schritte

- [Wartungsfenster](maintenance-window.md)
- [Wartungsfenster – Häufig gestellte Fragen](maintenance-window-faq.yml)
- [Überblick über Warnungen in Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md)
- [E-Mail an Azure Resource Manager-Rolle](../../azure-monitor/alerts/action-groups.md#email-azure-resource-manager-role)
