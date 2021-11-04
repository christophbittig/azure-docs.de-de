---
title: Geplante Wartung – Azure Database for MySQL – Flexible Server
description: In diesem Artikel wird das Feature für die geplante Wartung in Azure Database for MySQL – Flexible Server beschrieben.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 477d3f5c83c8fa50a4a7d895681dc0c7b2b401a7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442310"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Geplante Wartung in Azure Database for MySQL: Flexible Server

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In Azure Database for MySQL – Flexible Server werden regelmäßige Wartungen durchgeführt, um die Sicherheit, Stabilität und Aktualität Ihrer verwalteten Datenbank sicherzustellen. Während der Wartung erhält der Server neue Features, Updates und Patches.

## <a name="select-a-maintenance-window"></a>Auswählen eines Wartungsfensters

Sie können die Wartung für einen bestimmten Wochentag und ein bestimmtes Zeitfenster innerhalb dieses Tages planen. Alternativ können Sie dem System gestatten, einen Tag und ein Zeitfenster automatisch auszuwählen. In beiden Fällen werden Sie vom System fünf Tage vor einer Wartung benachrichtigt. Das System informiert Sie außerdem, wenn die Wartung startet und erfolgreich abgeschlossen wird.

Benachrichtigungen zu bevorstehenden geplanten Wartungsarbeiten können in folgenden Formaten vorliegen:

* E-Mail an eine bestimmte Adresse
* E-Mail an eine Azure Resource Manager-Rolle
* SMS an ein mobiles Gerät
* Pushbenachrichtigung an eine Azure-App
* Sprachnachricht

Beim Konfigurieren der Einstellungen für den Wartungszeitplan können Sie einen Wochentag und ein Zeitfenster auswählen. Wenn Sie nichts angeben, wird vom System je nach Zeitzone des Servers ein Zeitfenster zwischen 23:00 und 7:00 Uhr ausgewählt. Sie können für jeden flexiblen Server in Ihrem Azure-Abonnement einen eigenen Zeitplan definieren.

> [!IMPORTANT]
> Normalerweise vergehen zwischen erfolgreichen geplanten Wartungen eines Servers 30 Tage.
>
> Bei einem kritischen Notfallupdate, z. B. zur Behebung eines schwerwiegenden Sicherheitsrisikos, kann das Benachrichtigungsfenster aber auch kürzer als fünf Tage sein. Das kritische Update kann auch dann auf Ihren Server angewendet werden, wenn innerhalb der letzten 30 Tage eine erfolgreiche geplante Wartung durchgeführt wurde.

Sie können die Zeitplaneinstellungen jederzeit aktualisieren. Wenn für Ihren flexiblen Server eine Wartung geplant ist und Sie die Zeitplaneinstellungen aktualisieren, wird die aktuelle Einführung planungsgemäß fortgesetzt, und die Änderungen an den Zeitplaneinstellungen werden nach dem erfolgreichen Abschluss bis zur nächsten geplanten Wartung wirksam.

Sie können einen vom System verwalteten oder benutzerdefinierten Zeitplan für jeden flexiblen Server in Ihrem Azure-Abonnement definieren.
* Mit einem benutzerdefinierten Zeitplan können Sie Ihr Wartungsfenster für den Server angeben, indem Sie den Wochentag und ein Ein-Stunden-Zeitfenster auswählen.
* Bei einem vom System verwalteten Zeitplan wählt das System ein beliebiges einstündiges Fenster zwischen 23:00 Uhr und 7:00 Uhr in der Region Ihres Servers aus.

Im Rahmen der Einführung von Änderungen wenden wir die Updates auf die Server an, die mit einem vom System verwalteten Zeitplan konfiguriert wurden, gefolgt von Servern mit benutzerdefiniertem Zeitplan nach einer Mindestlücke von 7 Tagen innerhalb einer bestimmten Region. Wenn Sie beabsichtigen, frühzeitige Updates für eine Liste von Entwicklungs- und Testumgebungsservern zu erhalten, empfiehlt es sich, den vom System verwalteten Zeitplan für Server zu konfigurieren, die in der Entwicklungs- und Testumgebung verwendet werden. Dadurch können Sie das neueste Update zuerst in Ihrer Dev/Test-Umgebung zum Testen und Auswerten für die Validierung erhalten. Wenn Verhaltensänderungen oder Breaking Changes auftreten, haben Sie Zeit, sie zu beheben, bevor das gleiche Update auf Produktionsservern mit einem benutzerdefinierten verwalteten Zeitplan ausgeführt wird. Das Update beginnt nach sieben Tagen mit der Einführung auf flexiblen Servern nach benutzerdefiniertem Zeitplan und wird im definierten Wartungsfenster auf Ihren Server angewendet. Zu diesem Zeitpunkt gibt es keine Option, das Update hinauszuzögern, nachdem die Benachrichtigung gesendet wurde. Diese benutzerdefinierte Methode wird nur für Produktionsumgebungen empfohlen.

In seltenen Fällen kann ein Wartungsereignis vom System abgebrochen oder möglicherweise nicht erfolgreich abgeschlossen werden. Wenn das Update fehlschlägt, wird das Update wiederhergestellt, und die vorherige Version der Binärdateien wird wiederhergestellt. In solchen Szenarios mit fehlerhaften Updates kann während des Wartungsfensters weiterhin ein Neustart des Servers angezeigt werden. Wenn das Update abgebrochen wird oder fehlgeschlagen ist, erstellt das System eine Benachrichtigung über ein abgebrochenes oder fehlgeschlagenes Wartungsereignis, das Sie benachrichtigt. Der nächste Wartungsversuch wird gemäß Ihren aktuellen Zeitplaneinstellungen geplant, und Sie erhalten fünf Tage im Voraus eine Benachrichtigung.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum [Bearbeiten eines Wartungszeitplans](how-to-maintenance-portal.md)
* Weitere Informationen zum Abonnieren von [Benachrichtigungen zu bevorstehenden Wartungsarbeiten](../../service-health/service-notifications.md) mit Azure Service Health
* Weitere Informationen zur [Einrichtung von Benachrichtigungen zu bevorstehenden geplanten Wartungsereignissen](../../service-health/resource-health-alert-monitor-guide.md)
