---
title: Automatisches Skalieren von VM-Skalierungsgruppen im Azure-Portal
description: Erfahren Sie, wie Sie Regeln für die automatische Skalierung für VM-Skalierungsgruppen im Azure-Portal erstellen.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: a66eeec561b422cb7ce644facd7f273ab055e871
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423662"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>Automatisches Skalieren einer VM-Skalierungsgruppe im Azure-Portal

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Beim Erstellen einer Skalierungsgruppe definieren Sie die Anzahl von VM-Instanzen, die Sie ausführen möchten. Wenn sich die Nachfrage nach Ihrer Anwendung ändert, können Sie die Anzahl von VM-Instanzen automatisch erhöhen oder verringern lassen. Dank der Möglichkeit zum automatischen Skalieren können Sie über den gesamten Lebenszyklus Ihrer App die Kundennachfrage decken oder auf Änderungen der Anwendungsleistung reagieren.

In diesem Artikel wird veranschaulicht, wie Sie im Azure-Portal Regeln für die automatische Skalierung erstellen, mit denen die Leistung der VM-Instanzen in Ihrer Skalierungsgruppe überwacht wird. Mit diesen Regeln für die automatische Skalierung wird die Anzahl von VM-Instanzen erhöht oder verringert, um auf die Werte dieser Leitungsmetriken zu reagieren. Sie können diese Schritte auch mit [Azure PowerShell](tutorial-autoscale-powershell.md) oder der [Azure CLI](tutorial-autoscale-cli.md) ausführen.


## <a name="prerequisites"></a>Voraussetzungen
Zum Erstellen von Regeln für die automatische Skalierung benötigen Sie eine vorhandene VM-Skalierungsgruppe. Sie können eine Skalierungsgruppe im [Azure-Portal](quick-create-portal.md), mit [Azure PowerShell](quick-create-cli.md) oder der [Azure CLI](quick-create-powershell.md) erstellen.


## <a name="create-a-rule-to-automatically-scale-out"></a>Erstellen einer Regel für das automatische Aufskalieren
Wenn sich die Nachfrage für Ihre Anwendung erhöht, erhöht sich auch die Last für die VM-Instanzen in Ihrer Skalierungsgruppe. Falls es sich um eine dauerhafte Last und nicht nur um eine kurzzeitige höhere Nachfrage handelt, können Sie die Regeln für die automatische Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu erhöhen. Nachdem diese VM-Instanzen erstellt und Ihre Anwendungen bereitgestellt wurden, beginnt die Skalierungsgruppe damit, über das Lastenausgleichsmodul Datenverkehr darauf zu verteilen. Sie steuern, welche Metriken überwacht werden, z.B. CPU oder Datenträger, wie lange die Anwendungslast einen bestimmten Schwellenwert einhalten muss und wie viele VM-Instanzen der Skalierungsgruppe hinzugefügt werden sollen.

1. Öffnen Sie das Azure-Portal, und wählen Sie im Menü links im Dashboard **Ressourcengruppen** aus.
2. Wählen Sie die Ressourcengruppe aus, die Ihre Skalierungsgruppe enthält, und dann in der Liste der Ressourcen Ihre Skalierungsgruppe aus.
3. Wählen Sie auf der linken Seite des Fensters „Skalierungsgruppe“ im Menü **Skalierung** aus. Klicken Sie auf die Schaltfläche **Automatische Skalierung aktivieren**:

    ![Aktivieren der automatischen Skalierung im Azure-Portal](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. Geben Sie einen Namen für Ihre Einstellungen ein, z.B. *AutoSkalierung*. Wählen Sie dann die Option **Regel hinzufügen** aus.

5. Wir erstellen eine Regel, mit der die Anzahl von VM-Instanzen in einer Skalierungsgruppe erhöht wird, wenn die durchschnittliche CPU-Last für einen Zeitraum von 10 Minuten über 70% liegt. Wenn die Regel ausgelöst wird, wird die Anzahl von VM-Instanzen um 20% erhöht. In Skalierungsgruppen mit einer kleinen Anzahl von VM-Instanzen können Sie den **Vorgang** auf *Anzahl erhöhen um* festlegen und dann *1* oder *2* für *Anzahl der Instanzen* angeben. In Skalierungsgruppen mit einer großen Zahl von VM-Instanzen ist eine Erhöhung um 10 oder 20 Prozent für die VM-Instanzen unter Umständen angemessener.

    Geben Sie für Ihre Regel die folgenden Einstellungen an:
    
    | Parameter              | Erklärung                                                                                                         | Wert          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | *Zeitaggregation*     | Definiert, wie die gesammelten Metriken zu Analysezwecken aggregiert werden sollen.                                                | Average        |
    | *Metrikname*          | Die Leistungsmetrik zum Überwachen und Anwenden von Skalierungsgruppenaktionen.                                                   | CPU in Prozent |
    | *Statistik zum Aggregationsintervall* | Definiert, wie die in jedem Aggregationsintervall gesammelten Metriken zu Analysezwecken aggregiert werden sollen.                             | Average        |
    | *Operator*             | Operator zum Vergleichen der Metrikdaten mit dem Schwellenwert.                                                     | Größer als   |
    | *Schwellenwert*            | Der Prozentsatz, der für die Regel für die automatische Skalierung das Auslösen einer Aktion bewirkt.                                                 | 70             |
    | *Duration*             | Der überwachte Zeitraum, bevor die Metrik und Schwellenwerte verglichen werden. Die Abkühldauer ist dabei nicht eingeschlossen.                                   | 10 Minuten     |
    | *Vorgang*            | Definiert, ob und in welchen Schritten die Skalierungsgruppe hoch- oder herunterskaliert werden soll, wenn die Regel zutrifft.                        | Prozentsatz erhöhen um |
    | *Anzahl der Instanzen*       | Der Prozentsatz der VM-Instanzen sollte geändert werden, wenn diese Regel ausgelöst wird.                                            | 20             |
    | *Abkühlen (Minuten)*  | Gibt an, wie lange gewartet wird, bevor die Regel erneut angewendet wird, damit die Aktionen für die automatische Skalierung wirksam werden können. | 5 Minuten      |

    Die folgenden Beispiele zeigen eine im Azure-Portal erstellte Regel, die mit diesen Einstellungen übereinstimmt:
    
    ![Erstellen einer Regel für die automatische Skalierung zum Erhöhen der Anzahl von VM-Instanzen](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

    > [!NOTE]
    > Aufgaben, die innerhalb der Instanz ausgeführt werden, werden plötzlich beendet, und die Instanz wird herunterskaliert, sobald die Kühlzeit abgeschlossen ist.

6. Klicken Sie zum Erstellen der Regel auf **Hinzufügen**.


## <a name="create-a-rule-to-automatically-scale-in"></a>Erstellen einer Regel für automatisches Abskalieren
Es kann sein, dass die Nachfrage nach Ihrer Anwendung abends oder am Wochenende abnimmt. Wenn diese Verringerung der Last für einen bestimmten Zeitraum anhält, können Sie Regeln zur automatischen Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu reduzieren. Mit dieser Aktion zum horizontalen Herunterskalieren werden die Kosten für die Ausführung Ihrer Skalierungsgruppe gesenkt, da Sie nur so viele Instanzen ausführen, wie für die Erfüllung der derzeitigen Nachfrage erforderlich sind.

1. Klicken Sie erneut auf **Regel hinzufügen**.
2. Erstellen Sie eine Regel, mit der die Anzahl von VM-Instanzen in einer Skalierungsgruppe verringert wird, wenn die durchschnittliche CPU-Last über einen Zeitraum von 10 Minuten unter 30% fällt. Wenn die Regel ausgelöst wird, wird die Anzahl von VM-Instanzen um 20% verringert.

    Verwenden Sie die gleiche Vorgehensweise wie bei der vorherigen Regel. Passen Sie die folgenden Einstellungen für Ihre Regel an:
    
    | Parameter              | Erklärung                                                                                                          | Wert          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *Operator*             | Operator zum Vergleichen der Metrikdaten mit dem Schwellenwert.                                                      | Kleiner als   |
    | *Schwellenwert*            | Der Prozentsatz, der für die Regel für die automatische Skalierung das Auslösen einer Aktion bewirkt.                                                 | 30             |
    | *Vorgang*            | Definiert, ob und in welchen Schritten die Skalierungsgruppe hoch- oder herunterskaliert werden soll, wenn die Regel zutrifft.                         | Prozentsatz verringern um |
    | *Anzahl der Instanzen*       | Der Prozentsatz der VM-Instanzen sollte geändert werden, wenn diese Regel ausgelöst wird.                                             | 20             |

3. Klicken Sie zum Erstellen der Regel auf **Hinzufügen**.


## <a name="define-autoscale-instance-limits"></a>Definieren von Grenzwerten für die automatische Skalierung von Instanzen
Ihr Profil für die automatische Skalierung muss eine Mindest-, Höchst- und Standardanzahl von VM-Instanzen definieren. Bei Anwendung Ihrer Regeln für die automatische Skalierung stellen diese Grenzwerte für Instanzen sicher, dass keine Aufskalierung über die Höchstanzahl von Instanzen hinaus bzw. Abskalierung über die Mindestanzahl von Instanzen hinaus erfolgt.

1. Legen Sie die folgenden Grenzwerte für Instanzen fest:

    | Minimum | Maximum | Standard|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. Um Ihre Regeln für die automatische Skalierung und Grenzwerte für Instanzen anzuwenden, klicken Sie auf **Speichern**.


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Überwachen der Anzahl von Instanzen in einer Skalierungsgruppe
Um Anzahl und Status der VM-Instanzen anzuzeigen, wählen Sie im Menü auf der linken Seite des Fensters „Skalierungsgruppe“ **Instanzen** aus. Der Status zeigt an, ob die VM-Instanz beim automatischen horizontalen Hochskalieren *erstellt wird* oder ob die Bereitstellung beim automatischen horizontalen Herunterskalieren *gelöscht wird*.

![Anzeigen einer Liste von VM-Instanzen in einer Skalierungsgruppe](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>Automatisches Skalieren nach Zeitplan
In den vorherigen Beispielen wurde eine Skalierungsgruppe anhand von einfachen Hostmetriken, z.B. der CPU-Auslastung, automatisch herunter- oder hochskaliert. Sie können Regeln für die automatische Skalierung auch anhand von Zeitplänen erstellen. Mit diesen zeitplanbasierten Regeln können Sie die Anzahl von VM-Instanzen vor einem erwarteten Anstieg der Anwendungsnachfrage, z.B. der Kernarbeitszeit, automatisch aufskalieren und dann für Zeiträume mit geringerer Nachfrage, z.B. am Wochenende, wieder automatisch abskalieren.

1. Wählen Sie auf der linken Seite des Fensters „Skalierungsgruppe“ im Menü **Skalierung** aus. Um die in den vorherigen Beispielen erstellten Regeln für die automatische Skalierung zu löschen, klicken Sie auf das Papierkorbsymbol.

    ![Löschen vorhandener Regeln für die automatische Skalierung](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. Klicken Sie auf **Skalierungsbedingung hinzufügen**. Klicken Sie auf das Bleistiftsymbol neben dem Regelnamen, und geben Sie einen Namen wie z.B. *An jedem Geschäftstag aufskalieren* ein.

    ![Umbenennen der Standardregel für automatische Skalierung](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. Wählen Sie das Optionsfeld **Auf eine bestimmte Anzahl von Instanzen skalieren** aus.
4. Wenn die Anzahl der Instanzen hochskaliert werden sollen, geben Sie *10* als Anzahl der Instanzen ein.
5. Wählen Sie **An bestimmten Tagen wiederholen** als Typ für **Zeitplan** aus.
6. Wählen Sie alle Geschäftstage, Montag bis Freitag, aus.
7. Wählen Sie die entsprechende Zeitzone aus, und geben Sie dann *09:00 Uhr* als **Startzeit** ein.
8. Klicken Sie nochmals auf **Skalierungsbedingung hinzufügen**. Wiederholen Sie den Vorgang zum Erstellen eines Zeitplans mit dem Namen *Abends abskalieren*, der eine Skalierung auf *3* Instanzen vorsieht, sich jeden Wochentag wiederholt und um *18:00* beginnt.
9. Klicken Sie auf **Speichern**, um Ihre zeitplanbasierten Regeln für die automatische Skalierung anzuwenden.

    ![Erstellen von Regeln für die automatische Skalierung nach Zeitplan](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

Um zu prüfen, wie Ihre Regeln für die automatische Skalierung angewendet werden, wählen Sie **Ausführungsverlauf** oben im Fenster **Skalierung** aus. Die Grafik und Ereignisliste zeigen an, wann die Regeln für die automatische Skalierung ausgelöst werden und die Anzahl der VM-Instanzen in Ihrer Skalierungsgruppe steigt oder sinkt.


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde beschrieben, wie Sie mit Regeln für die automatische Skalierung horizontal skalieren und die *Anzahl* von VM-Instanzen in Ihrer Skalierungsgruppe erhöhen oder verringern. Sie können auch vertikal skalieren, um die *Größe* der VM-Instanz zu erhöhen oder zu verringern. Weitere Informationen finden Sie unter [Vertikale automatische Skalierung mit VM-Skalierungsgruppen](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Informationen zur Verwaltung Ihrer VM-Instanzen finden Sie unter [Verwalten einer VM-Skalierungsgruppe mit Azure PowerShell](./virtual-machine-scale-sets-manage-powershell.md).

Informationen zum Generieren von Warnungen bei der Auslösung von Regeln für die automatische Skalierung finden Sie unter [Verwenden von automatischen Skalierungsvorgängen zum Senden von E-Mail- und Webhook-Warnbenachrichtigungen in Azure Monitor](../azure-monitor/autoscale/autoscale-webhook-email.md). Informieren Sie sich auch über das [Aufrufen eines Webhooks für Azure-Aktivitätsprotokollwarnungen](../azure-monitor/alerts/alerts-log-webhook.md).
