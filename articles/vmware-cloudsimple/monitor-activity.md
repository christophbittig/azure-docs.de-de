---
title: Überwachen von Aktivitäten in der privaten Cloud
titleSuffix: Azure VMware Solution by CloudSimple
description: Beschreibt die Informationen, die zur Aktivität in der Azure VMware Solution by CloudSimple-Umgebung verfügbar sind, einschließlich Warnungen, Ereignissen, Aufgaben und Überwachung.
author: suzizuber
ms.author: v-szuber
ms.date: 08/13/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 46b6f703599637ce7fa032c14d6d799db3010d9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322263"
---
# <a name="monitor-vmware-solution-by-cloudsimple-activity"></a>Überwachen der VMware Solution by CloudSimple-Aktivität

CloudSimple-Aktivitätsprotokolle bieten Einblicke in Vorgänge, die im CloudSimple-Portal durchgeführt werden.  Die Liste enthält Warnungen, Ereignisse, Aufgaben und Überwachung.  Verwenden Sie die Aktivitätsprotokolle, um zu bestimmen, wann welche Vorgänge ausgeführt wurden, und wer sie ausgeführt hat.  Aktivitätsprotokolle enthalten keine Lesevorgänge, die von einem Benutzer durchgeführt werden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="access-the-cloudsimple-portal"></a>Zugriff auf das CloudSimple-Portal

Rufen Sie das [CloudSimple-Portal](access-cloudsimple-portal.md) auf.

## <a name="activity-information"></a>Informationen zur Aktivität

Um auf die Aktivitätsseiten zuzugreifen, wählen Sie im seitlichen Menü **Aktivität** aus.

![Übersicht zur Aktivitätsseite](media/activity-page-overview.png)

Um Details zu Aktivitäten auf der Aktivitätsseite anzuzeigen, wählen Sie die Aktivität aus. Ein Detailbereich wird auf der rechten Seite geöffnet. Die Aktionen im Bereich sind vom Typ der Aktivität abhängig. Klicken Sie auf **X**, um den Bereich zu schließen.

Klicken Sie auf eine Spaltenüberschrift, um die Anzeige zu sortieren.  Sie können Spalten nach bestimmten Werten filtern, die Sie anzeigen möchten.  Laden Sie den Aktivitätsbericht durch Klicken auf das Symbol **Als CSV herunterladen** herunter.

## <a name="alerts"></a>Alerts

Bei Warnungen handelt es sich um Benachrichtigungen zu wichtigen Aktivitäten in der CloudSimple-Umgebung.  Warnungen enthalten Ereignisse, die sich auf die Abrechnung oder den Benutzerzugriff auswirken.

Wenn Sie Warnungen bestätigen und aus der Liste entfernen möchten, wählen Sie mindestens eine in der Liste aus, und klicken Sie auf **Bestätigen**.

Die folgenden Spalten mit Informationen sind für Warnungen verfügbar. Klicken Sie auf **Spalten bearbeiten**, und wählen Sie die Spalten aus, die Sie anzeigen möchten.

| Column | BESCHREIBUNG |
------------ | ------------- |
| Warnungstyp | Kategorie der Warnung.|
| Zeit | Zeitpunkt, zu dem die Warnung auftrat. |
| severity | Bedeutung der Warnung.|
| Ressourcenname | Name, der der Ressource zugewiesen ist, z. B. der Name der privaten Cloud. |
| Ressourcentyp | Kategorie der Ressource: Private Cloud, Cloud Rack. |
| Ressourcen-ID | Bezeichner der Ressource. |
| BESCHREIBUNG | Beschreibung des Auslösers der Warnung. |
| Bestätigt | Angabe, ob die Warnung bestätigt wurde. |

## <a name="events"></a>Ereignisse

Mit Ereignissen werden Benutzer- und Systemaktivitäten im CloudSimple-Portal angezeigt. Auf der Seite „Ereignisse“ werden die mit einer bestimmten Ressource verknüpften Aktivitäten sowie der Schweregrad der Auswirkungen angezeigt.

Die folgenden Spalten mit Informationen sind für Warnungen verfügbar. Klicken Sie auf **Spalten bearbeiten**, und wählen Sie die Spalten aus, die Sie anzeigen möchten.

| Column | BESCHREIBUNG |
------------ | ------------- |
| Time | Angabe des Zeitpunkts, zu dem das Ereignis aufgetreten ist, mit Datum und Uhrzeit. |
| Ereignistyp | Numerischer Code, der das Ereignis identifiziert. |
| severity | Ereignisschweregrad.|
| Ressourcenname | Name, der der Ressource zugewiesen ist, z. B. der Name der privaten Cloud. |
| Ressourcentyp | Kategorie der Ressource: Private Cloud, Cloud Rack. |
| BESCHREIBUNG | Beschreibung des Auslösers der Warnung. |

## <a name="tasks"></a>Aufgaben

Bei Aufgaben handelt es sich um Aktivitäten in einer privaten Cloud, die voraussichtlich 30 Sekunden oder länger dauern. (Aktivitäten, die voraussichtlich weniger als 30 Sekunden dauern, werden nur als Ereignisse gemeldet.) Öffnen Sie die Aufgaben-Seiten, um den Fortschritt von Aufgaben für die Private Cloud zu verfolgen.

Die folgenden Spalten mit Informationen sind für Warnungen verfügbar. Klicken Sie auf **Spalten bearbeiten**, und wählen Sie die Spalten aus, die Sie anzeigen möchten.

| Column | BESCHREIBUNG |
------------ | ------------- |
| Aufgaben-ID | Eindeutiger Bezeichner für die Aufgabe. |
| Vorgang | Aktion, die die Aufgabe ausführt. |
| Benutzer | Der Benutzer, dem die Durchführung der Aufgabe zugewiesen ist. |
| Ressourcenname | Name, der der Ressource zugewiesen ist. |
| Ressourcentyp | Kategorie der Ressource: Private Cloud, Cloud Rack. |
| Ressourcen-ID | Bezeichner der Ressource. |
| Start | Startzeit für die Aufgabe. |
| Ende | Endzeit für die Aufgabe. |
| Status | Aktueller Aufgabenstatus. |
| Verstrichene Zeit | Zeitspanne, die die Durchführung der Aufgabe (sofern durchgeführt) in Anspruch nahm bzw. (bei laufender Durchführung) bisher in Anspruch genommen hat. |
| BESCHREIBUNG | Aufgabenbeschreibung. |

## <a name="audit"></a>Audit

Überwachungsprotokolle verfolgen die Benutzeraktivität nach. Überwachungsprotokolle können Sie zum Überwachen von Benutzeraktivitäten für alle Benutzer verwenden.

Die folgenden Spalten mit Informationen sind für Warnungen verfügbar. Klicken Sie auf **Spalten bearbeiten**, und wählen Sie die Spalten aus, die Sie anzeigen möchten.

| Column | BESCHREIBUNG |
------------ | ------------- |
| Time | Zeitpunkt des Überwachungseintrags. |
| Vorgang | Aktion, die die Aufgabe ausführt. |
| Benutzer | Benutzer, der der Aufgabe zugewiesen ist. |
| Ressourcenname | Name, der der Ressource zugewiesen ist. |
| Ressourcentyp | Kategorie der Ressource: Private Cloud, Cloud Rack. |
| Ressourcen-ID | Bezeichner der Ressource. |
| Ergebnis | Ergebnis der Aktivität, z. B. **Erfolg**. |
| Benötigte Zeit | Zeitspanne zum Durchführen dieser Aufgabe. |
| BESCHREIBUNG | Beschreibung der Aktion. |

## <a name="next-steps"></a>Nächste Schritte

* [Nutzen von virtuellen VMware-Computern in Azure](quickstart-create-vmware-virtual-machine.md)
* Weitere Informationen zu [privaten Clouds](cloudsimple-private-cloud.md)
