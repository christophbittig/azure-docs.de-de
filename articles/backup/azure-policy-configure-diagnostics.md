---
title: Bedarfsgerechtes Konfigurieren von Tresordiagnoseeinstellungen
description: Konfigurieren von Log Analytics-Diagnoseeinstellungen für alle Tresore in einem bestimmten Bereich mithilfe von Azure Policy
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 34b0cac710833e1d1b29060aa37425d2e57ae828
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2021
ms.locfileid: "113303734"
---
# <a name="configure-vault-diagnostics-settings-at-scale"></a>Bedarfsgerechtes Konfigurieren von Tresordiagnoseeinstellungen

Die von Azure Backup bereitgestellte Berichterstellungslösung nutzt Log Analytics (LA). Damit die Daten eines beliebigen Tresors an La gesendet werden, muss für diesen Tresor eine [Diagnoseeinstellung](./backup-azure-diagnostic-events.md) erstellt werden.

Häufig ist das manuelle Hinzufügen einer Diagnoseeinstellung pro Tresor eine mühselige Aufgabe. Außerdem müssen für jeden neu erstellten Tresor auch Diagnoseeinstellungen aktiviert sein, damit für diesen Tresor Berichte angezeigt werden können.

Um das Erstellen von Diagnoseeinstellungen nach Maß (mit LA als Ziel) zu vereinfachen, bietet Azure Backup eine integrierte [Azure Policy](../governance/policy/index.yml). Diese Richtlinie fügt allen Tresoren in einem bestimmten Abonnement oder einer bestimmten Ressourcengruppe eine LA-Diagnoseeinstellung hinzu. Die folgenden Abschnitte enthalten Anweisungen zur Verwendung dieser Richtlinie.

## <a name="supported-scenarios"></a>Unterstützte Szenarien

* Die Richtlinie kann gleichzeitig auf alle Recovery Services-Tresore in einem bestimmten Abonnement (oder auf eine Ressourcengruppe innerhalb des Abonnements) angewendet werden. Der Benutzer, der die Richtlinie zuweist, muss über die Berechtigung **Besitzer** für das Abonnement verfügen, dem die Richtlinie zugewiesen ist.

* Der vom Benutzer angegebene LA-Arbeitsbereich (an den die Diagnosedaten gesendet werden) kann sich in einem anderen Abonnement als die Tresore befinden, denen die Richtlinie zugewiesen ist. Der Benutzer muss über die Zugriffsberechtigung **Leser**, **Mitwirkender** oder **Besitzer** für das Abonnement verfügen, in dem der angegebene LA-Arbeitsbereich vorhanden ist.

* Der Bereich „Verwaltungsgruppe“ wird derzeit nicht unterstützt.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="assigning-the-built-in-policy-to-a-scope"></a>Zuweisen der integrierten Richtlinie zu einem Bereich

Um die Richtlinie für Tresore dem erforderlichen Bereich zuzuweisen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zum Dashboard **Backup Center**.
2. Wählen Sie im linken Menü die Option **Azure-Richtlinien für die Sicherung** aus, um eine Liste aller integrierten Richtlinien für Azure-Ressourcen abzurufen.
3. Suchen Sie die Richtlinie **Stellen Sie dem Log Analytics-Arbeitsbereich für ressourcenspezifische Kategorien Diagnoseeinstellungen für den Recovery Services-Tresor bereit.** .

    ![Bereich „Richtliniendefinition“](./media/backup-azure-policy-configure-diagnostics/policy-definition-blade.png)

4. Wählen Sie den Namen der Richtlinie aus. Sie werden zur detaillierten Definition für diese Richtlinie umgeleitet.

    ![Detaillierte Richtliniendefinition](./media/backup-azure-policy-configure-diagnostics/detailed-policy-definition.png)

5. Wählen Sie oben im Bereich die Schaltfläche **Zuweisen** aus. Dadurch werden Sie zum Bereich **Richtlinie zuweisen** umgeleitet.

6. Wählen Sie unter **Grundlagen** die drei Punkte neben dem Feld **Bereich** aus. Dadurch wird ein Bereich mit dem richtigen Kontext geöffnet, auf dem Sie das Abonnement auswählen können, auf das die Richtlinie angewendet werden soll. Optional können Sie auch eine Ressourcengruppe auswählen, damit die Richtlinie nur für Tresore in einer bestimmten Ressourcengruppe angewendet wird.

    ![Grundlegendes zur Richtlinienzuweisung](./media/backup-azure-policy-configure-diagnostics/policy-assignment-basics.png)

7. Geben Sie unter **Parameter** folgende Informationen ein:

    * **Profilname**: der Name, der den von der Richtlinie erstellten Diagnoseeinstellungen zugewiesen wird.
    * **Log Analytics-Arbeitsbereich**: der Log Analytics-Arbeitsbereich, dem die Diagnoseeinstellung zugeordnet werden soll. Die Diagnosedaten aller Tresore im Gültigkeitsbereich der Richtlinienzuweisung werden per Pushvorgang an den angegebenen LA-Arbeitsbereich übermittelt.

    * **„Name des Ausschlusstags (optional)“ und „Wert des Ausschlusstags (optional)“** : Sie können auswählen, ob Tresore mit einem bestimmten Tagnamen und -wert von der Richtlinienzuweisung ausgeschlossen werden sollen. Wenn Sie z. B. **nicht** möchten, dass diesen Tresoren eine Diagnoseeinstellung hinzugefügt wird, in der ein Tag „isTest“ auf den Wert „yes“ festgelegt ist, müssen Sie im Feld **Name des Ausschlusstags** „isTest“ und im Feld **Wert des Ausschlusstags** „yes“ eingeben. Wenn eines der beiden Felder (oder beide) leer bleibt, wird die Richtlinie unabhängig von den darin enthaltenen Tags auf alle relevanten Tresore angewendet.

    ![Parameter der Richtlinienzuweisung](./media/backup-azure-policy-configure-diagnostics/policy-assignment-parameters.png)

8. **Korrekturtask erstellen**: Sobald die Richtlinie einem Bereich zugewiesen ist, werden für alle neuen Tresore, die in diesem Bereich erstellt werden, automatisch die LA-Diagnoseeinstellungen konfiguriert (innerhalb von 30 Minuten ab dem Zeitpunkt der Erstellung des Tresors). Wenn Sie vorhandenen Tresoren im Gültigkeitsbereich eine Diagnoseeinstellung hinzufügen möchten, können Sie bei der Zuweisung der Richtlinie einen Korrekturtask auslösen. Um einen Korrekturtask auszulösen, aktivieren Sie das Kontrollkästchen **Korrekturtask erstellen**.

    ![Korrektur der Richtlinienzuweisung](./media/backup-azure-policy-configure-diagnostics/policy-assignment-remediation.png)

9. Navigieren Sie zu **Überprüfen + erstellen**, und wählen Sie **Erstellen** aus.

## <a name="under-what-conditions-will-the-remediation-task-apply-to-a-vault"></a>Unter welchen Bedingungen wird der Korrekturtask auf einen Tresor angewendet?

Der Korrekturtask wird auf Tresore angewendet, die gemäß der Definition der Richtlinie nicht kompatibel sind. Ein Tresor ist nicht kompatibel, wenn er eine der folgenden Bedingungen erfüllt:

* Für den Tresor ist keine Diagnoseeinstellung vorhanden.
* Für den Tresor sind Diagnoseeinstellungen vorhanden, aber für keine der Einstellungen sind **alle** ressourcenspezifischen Ereignisse mit LA als Ziel aktiviert und **Ressourcenspezifisch** in der Umschaltfläche ausgewählt.

Also auch wenn ein Benutzer über einen Tresor verfügt, für den das AzureBackupReport-Ereignis im AzureDiagnostics-Modus (der von Sicherungsberichten unterstützt wird) aktiviert ist, wird der Korrekturtask weiterhin für diesen Tresor angewendet, da der ressourcenspezifische Modus [zukünftig](./backup-azure-diagnostic-events.md#legacy-event) die empfohlene Methode zum Erstellen von Diagnoseeinstellungen ist.

Außerdem wird dann, wenn ein Benutzer über einen Tresor verfügt, für den nur eine Teilmenge der sechs ressourcenspezifischen Ereignisse aktiviert ist, der Korrekturtask für diesen Tresor angewendet, da Sicherungsberichte nur dann erwartungsgemäß funktionieren, wenn alle sechs ressourcenspezifischen Ereignisse aktiviert sind.

> [!NOTE]
>
> Wenn für eine Diagnoseeinstellung eines Tresors **eine Teilmenge ressourcenspezifischer** Kategorien aktiviert ist, die zum Senden von Daten an einen bestimmten LA-Arbeitsbereich konfiguriert ist (z. B. Arbeitsbereich X), tritt bei Anwendung des Korrekturtasks (nur für diesen Tresor) ein Fehler auf, wenn dieser Arbeitsbereich mit dem in der Richtlinienzuweisung angegebenen Ziel-LA-Arbeitsbereich **identisch** ist.
>
>Dies liegt daran, dass die Einstellungen nicht denselben LA-Arbeitsbereich als Ziel haben können, wenn sich die von zwei verschiedenen Diagnoseeinstellungen in derselben Ressource aktivierten Ereignisse **überlappen**. Sie müssen diesen Fehler manuell beheben, indem Sie zum relevanten Tresor navigieren und eine Diagnoseeinstellung mit einem anderen LA-Arbeitsbereich als Ziel konfigurieren.
>
> Beachten Sie, dass bei dem Korrekturtask **kein** Fehler auftritt, wenn die vorhandene Diagnoseeinstellung nur als AzureBackupReport mit Arbeitsbereich X als Ziel aktiviert ist, da es in diesem Fall keine Überlappung zwischen den Ereignissen gibt, die von der vorhandenen Einstellung aktiviert werden, und den Ereignissen, die von der durch den Korrekturtask erstellten Einstellung aktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

* [ Weitere Informationen zum Verwenden von Sicherungsberichten](./configure-reports.md)
* [Weitere Informationen zu Azure Policy](../governance/policy/index.yml)
* [Automatisches Aktivieren der Sicherung bei der VM-Erstellung mithilfe von Azure Policy](./backup-azure-auto-enable-backup.md)
