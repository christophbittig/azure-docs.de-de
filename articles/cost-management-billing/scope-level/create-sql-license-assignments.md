---
title: Erstellen von SQL Server-Lizenzzuweisungen für Azure-Hybridvorteil
description: In diesem Artikel wird erklärt, wie Sie SQL Server-Lizenzzuweisungen für Azure-Hybridvorteil erstellen können.
author: bandersmsft
ms.author: banders
ms.date: 09/30/2021
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisri
ms.openlocfilehash: e97c4a51366dbfd2c9016de4684129a91df0cbe9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130218937"
---
# <a name="create-sql-server-license-assignments-for-azure-hybrid-benefit"></a>Erstellen von SQL Server-Lizenzzuweisungen für Azure-Hybridvorteil

Die neue zentrale Azure-Hybridvorteil-Benutzeroberfläche im Azure-Portal unterstützt SQL Server-Lizenzzuweisungen auf Kontoebene oder auf einer bestimmten Abonnementebene. Wenn die Zuweisung auf Kontoebene erstellt wird, werden Azure-Hybridvorteil-Rabatte automatisch auf SQL-Ressourcen in allen Abonnements des Kontos bis zur in der Zuweisung angegebenen Lizenzanzahl angewendet.

Für jede Lizenzzuweisung wird ein Bereich ausgewählt. Diesem Bereich werden die Lizenzen zugewiesen. Jeder Bereich kann mehrere Lizenzeinträge enthalten.

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Voraussetzungen müssen erfüllt werden, um SQL Server-Lizenzzuweisungen erstellen zu können.

- Ihre Organisation verfügt über einen unterstützten Vereinbarungstyp und ein unterstütztes Angebot.
- Sie sind Mitglied einer Rolle, die über Berechtigungen zum Zuweisen von SQL-Lizenzen verfügt.
- Ihre Organisation verfügt über SQL Server-Kernlizenzen mit Software Assurance oder Kernabonnementlizenzen, die Azure zugewiesen werden können.
- Ihre Organisation ist für die automatische Registrierung der Azure SQL-VMs mit der IaaS-Erweiterung registriert. Weitere Informationen finden Sie unter [Automatische Registrierung mit der Agent-Erweiterung für SQL-IaaS](../../azure-sql/virtual-machines/windows/sql-agent-extension-automatic-registration-all-vms.md).
  > [!IMPORTANT]
  > Wenn diese Voraussetzung nicht erfüllt wird, erzeugt Azure unvollständige Daten zu Ihrer aktuellen Nutzung des Azure-Hybridvorteils. Diese Situation kann zu falschen Lizenzzuweisungen und zu unnötigen nutzungsbasierten Gebühren für SQL Server führen.

Die erforderlichen Rollen unterscheiden sich je nach Vereinbarungstyp.

| Vereinbarungstyp | Erforderliche Rolle | Unterstützte Angebote |
| --- | --- | --- |
| Enterprise Agreement | _Unternehmensadministrator:_<p> Weitere Informationen dazu, wie Sie Mitglied einer Rolle werden, finden Sie unter [Hinzufügen eines weiteren Unternehmensadministrators](../manage/ea-portal-administration.md#add-a-department-administrator). | – MS-AZR-0017P (Microsoft Azure Enterprise)<br>– MS-AZR-USGOV-0017P (Azure Government Enterprise) |
| Microsoft-Kundenvereinbarung | *Besitzer des Abrechnungskontos*<br> *Mitwirkender am Abrechnungskonto* <br> *Besitzer des Abrechnungsprofils*<br> *Mitwirkender am Abrechnungsprofil*<br> Weiter Informationen dazu, wie Sie Mitglied der Rollen werden, finden Sie unter [Verwalten von Abrechnungsrollen](../manage/understand-mca-roles.md#manage-billing-roles-in-the-azure-portal). | MS-AZR-0017G (Microsoft Azure-Plan)|
| WebDirect / Nutzungsbasierte Bezahlung | Nicht verfügbar | Keine |
| CSP/partnergeführte Kundenunternehmen | Nicht verfügbar | Keine |

> [!NOTE]
> Das zentrale Zuweisen von Lizenzen zu Bereichen ist für Sponsored-, MSDN Credit- und MPN-Abonnements nicht verfügbar. Die Nutzung von SQL-Software ist für Dev/Test-Abonnements (bei den Angebotstypen MS-AZR-0148P oder MS-AZR-0023P) kostenlos.

## <a name="create-a-sql-license-assignment"></a>Erstellen einer SQL-Lizenzzuweisung

Navigieren Sie im Folgenden von **Cost Management + Billing** zu **Reservations + Hybrid Benefit** (Reservierungen + Hybridvorteil). Navigieren Sie nicht von der Azure-Startseite zu **Reservierungen**. Wenn Sie das tun, verfügen Sie nicht über den erforderlichen Bereich, um die Lizenzzuweisungs-Benutzeroberfläche anzeigen zu können. 

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie zu **Cost Management + Billing**.  
    :::image type="content" source="./media/create-sql-license-assignments/select-cost-management.png" alt-text="Screenshot: Navigation im Azure-Portal zu Cost Management + Billing" lightbox="./media/create-sql-license-assignments/select-cost-management.png" :::
 2. Wenn Sie über ein Enterprise Agreement verfügen, wählen Sie einen Abrechnungsbereich aus.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-scope.png" alt-text="Screenshot: Auswahlmöglichkeiten im EA-Abrechnungsbereich" lightbox="./media/create-sql-license-assignments/select-billing-scope.png" :::
 3. Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, wählen Sie „Abrechnungsprofil“ aus.  
    :::image type="content" source="./media/create-sql-license-assignments/select-billing-profile.png" alt-text="Screenshot: Auswahlmöglichkeiten im Abrechnungsprofil" lightbox="./media/create-sql-license-assignments/select-billing-profile.png" :::
 4. Wählen Sie im linken Menü **Reservations + Hybrid Benefit** (Reservierungen + Hybridvorteil) aus.  
    :::image type="content" source="./media/create-sql-license-assignments/select-reservations.png" alt-text="Screenshot: Auswahlmöglichkeiten von „Reservations + Hybrid Benefit“ (Reservierungen + Hybridvorteil)"  :::
 5. Wählen Sie **Hinzufügen** und dann in der Liste **Azure-Hybridvorteil (Vorschau)** aus.  
    :::image type="content" source="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" alt-text="Screenshot: Auswahlmöglichkeiten von „Azure-Hybridvorteil“" lightbox="./media/create-sql-license-assignments/select-azure-hybrid-benefit.png" :::
 6. Wählen Sie auf dem nächsten Bildschirm **Begin to assign licenses** (Zuweisen von Lizenzen beginnen) aus.  
    :::image type="content" source="./media/create-sql-license-assignments/get-started-centralized.png" alt-text="Screenshot: Auswahlmöglichkeiten von „Add SQL hybrid benefit selection“ (Hinzufügen von SQL-Hybridvorteil)" lightbox="./media/create-sql-license-assignments/get-started-centralized.png" :::
 7. Wählen Sie einen Bereich aus, und geben Sie die Lizenzanzahl ein, die für jede SQL Server-Edition verwendet werden soll. Wenn Sie keine Lizenzen haben, die Sie einer bestimmten Edition zuweisen müssen, geben Sie 0 (Null) ein.  
    > [!NOTE]
    > Sie sind dafür verantwortlich, zu bestimmen, dass die Einträge, die Sie in der Benutzeroberfläche zur Lizenzverwaltung auf Bereichsebene tätigen, korrekt sind und Ihren Lizenzverpflichtungen entsprechen. Die Informationen zur Lizenznutzung werden angezeigt, um Sie beim Tätigen Ihrer Lizenzzuweisungen zu unterstützen. Jedoch könnten die angezeigten Informationen aufgrund verschiedener Faktoren unvollständig oder ungenau sein.
    >
    > Wenn die Anzahl der Lizenzen, die Sie eingeben, geringer ist als die Anzahl der Lizenzen, die Sie derzeit verwenden, wird Ihnen folgende Warnmeldung angezeigt: _You've entered fewer licenses than you're currently using for Azure Hybrid Benefit in this scope. Your bill for this scope will increase._ (Sie haben weniger Lizenzen eingetragen, als Sie derzeit für Azure-Hybridvorteil in diesem Bereich verwenden. Ihre Rechnung für diesen Bereich erhöht sich.)  
    
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition.png" alt-text="Screenshot: Bereichsauswahl und Anzahl der Lizenzen." lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition.png" :::
 8. Wählen Sie optional die Registerkarte **Nutzungsdetails** aus, um Ihre derzeitige Nutzung von Azure-Hybridvorteil im Ressourcenbereich anzuzeigen.  
    :::image type="content" source="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" alt-text="Screenshot: die Registerkarte „Nutzungsdetails“" lightbox="./media/create-sql-license-assignments/select-assignment-scope-edition-usage.png" :::
 9. Wählen Sie **Hinzufügen**.
10. Ändern Sie optional den Standardnamen der Lizenzzuweisung. Das Überprüfungsdatum wird automatisch ein Jahr vorausgesetzt und kann nicht geändert werden. Sein Zweck ist es, Sie daran zu erinnern, Ihre Lizenzzuweisungen regelmäßig zu prüfen.  
    :::image type="content" source="./media/create-sql-license-assignments/license-assignment-commit.png" alt-text="Screenshot: Name der Standardlizenzzuweisung" lightbox="./media/create-sql-license-assignments/license-assignment-commit.png" :::
11. Nachdem Sie Ihre Auswahl überprüft haben, wählen Sie **Next: Review + apply** (Weiter: Überprüfen + Anwenden) aus.
12. Wählen Sie die Nachweisoption **By selecting&quot;Apply&quot;** (Durch die Auswahl von „Apply“) aus, um zu bestätigen, dass Sie über die Berechtigung verfügen, Azure-Hybridvorteil und genügend SQL Server-Lizenzen anzuwenden sowie die Lizenzen so lange zu verwalten, wie sie zugewiesen sind.  
    :::image type="content" source="./media/create-sql-license-assignments/confirm-apply-attestation.png" alt-text="Screenshot: Nachweisoption" lightbox="./media/create-sql-license-assignments/confirm-apply-attestation.png" :::
13. Wählen Sie **Anwenden** und dann **OK** aus.
14. Die Liste der zugewiesenen Lizenzen wird angezeigt.  
    :::image type="content" source="./media/create-sql-license-assignments/assigned-licenses.png" alt-text="Screenshot: Liste der zugewiesenen Lizenzen" lightbox="./media/create-sql-license-assignments/assigned-licenses.png" ::: 

## <a name="track-assigned-license-use"></a>Nachverfolgen der Verwendung zugewiesener Lizenzen

Navigieren Sie zu **Cost Management + Billing**, und wählen Sie dann **Reservations + Hybrid Benefit** (Reservierungen + Hybridvorteil) aus.

Eine Liste aller Reservierungen und Lizenzzuweisungen wird angezeigt. Wenn Sie die Ergebnisse nach Lizenzzuweisungen filtern möchten, legen Sie einen Filter für **SQL-Hybridvorteil** fest.

:::image type="content" source="./media/create-sql-license-assignments/view-the-assignments.png" alt-text="Screenshot: Liste der Reservierungen und Lizenzen" lightbox="./media/create-sql-license-assignments/view-the-assignments.png" :::

Wählen Sie unter **Last Day Utilization** (Auslastung des letzten Tages) oder **7-day Utilization** (Sieben-tägige Auslastung) einen Prozentsatz aus, der leer oder 0 (null) sein kann, um den Nutzungsverlauf der Zuweisungen im Detail zu sehen.

:::image type="content" source="./media/create-sql-license-assignments/assignment-utilization-view.png" alt-text="Screenshot: Zuweisungsnutzungsdetails" lightbox="./media/create-sql-license-assignments/assignment-utilization-view.png" :::

Wenn die Nutzung einer Lizenzzuweisung 100 % beträgt, fallen für einige Ressourcen innerhalb des Bereichs wahrscheinlich nutzungsbasierte Gebühren für SQL Server an. Es wird empfohlen, die Lizenzzuweisungs-Benutzeroberfläche erneut zu verwenden, um zu überprüfen, wie viel Nutzung durch zugewiesene Lizenzen abgedeckt oder nicht abgedeckt wird. Gehen Sie anschließend denselben Prozess wie zuvor durch. Dies schließt die Beratung mit Ihrer Beschaffungs- und Lizenzverwaltungsabteilung mit ein, um zu bestätigen, dass mehr Lizenzen verfügbar sind und um die Lizenzen zuzuweisen.

## <a name="changes-after-license-assignment"></a>Änderungen nach der Lizenzzuweisung

Nachdem Sie SQL-Lizenzzuweisungen erstellt haben, verändert sich Ihre Benutzeroberfläche von Azure-Hybridvorteil im Azure-Portal.

- Alle vorhandenen Azure-Hybridvorteil-Auswahlen, die für einzelne SQL-Ressourcen konfiguriert sind, gelten nicht mehr. Sie werden durch die SQL-Lizenzzuweisung ersetzt, die auf Abonnement- oder Kontoebene erstellt wurde.
- Die Hybridvorteil-Option wird in Ihrer SQL-Ressourcenkonfiguration nicht auf die gleiche Weise angezeigt.
- Anwendungen oder Skripts, die Hybridvorteil programmgesteuert konfigurieren, funktionieren weiterhin, aber die Einstellung hat keine Auswirkungen.
- SQL-Softwarerabatte werden auf die SQL-Ressourcen in dem Bereich angewandt. Der Bereich basiert auf der Anzahl der Lizenzen in den Lizenzzuweisungen, die für das Abonnement oder das Konto erstellt werden, in dem die Ressource erstellt wurde.
- Eine bestimmte Ressource, die für Hybridvorteil konfiguriert ist, kann den Rabatt möglicherweise nicht erhalten, wenn andere Ressourcen alle Lizenzen verbrauchen. Jedoch wird der maximale Rabatt basierend auf der Anzahl der Lizenzen auf den Bereich angewendet. Weitere Informationen zur Anwendung der Rabatte finden Sie unter [Was ist der zentral verwaltete Azure-Hybridvorteil?](overview-azure-hybrid-benefit-scope.md).

## <a name="cancel-a-license-assignment"></a>Kündigen einer Lizenzzuweisung

Überprüfen Sie Ihre Lizenzsituation, bevor Sie Ihre Lizenzzuweisungen kündigen. Wenn Sie eine Lizenzzuweisung kündigen, erhalten Sie für diese keine Rabatte mehr. Folglich kann Ihre Azure-Rechnung steigen. Wenn Sie die letzte verbleibende Lizenzzuweisung kündigen, muss Azure-Hybridvorteil wieder für jede Ressource einzeln verwaltet werden.

### <a name="to-cancel-a-license-assignment"></a>Wie man eine Lizenzzuweisung kündigt

1. Wählen Sie eine Lizenzzuweisung in der Liste der Reservierungen und Lizenzzuweisungen aus.
1. Wählen Sie auf der Seite mit den Details zu den Lizenzzuweisungen **Cancel** (Kündigen) aus.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment-symbol.png" alt-text="Screenshot: die Kündigungsoption" :::
1. Überprüfen Sie auf der Seite „Cancel“ (Kündigen) die Benachrichtigung, und wählen Sie dann **Yes, cancel** (Ja, kündigen) aus.  
    :::image type="content" source="./media/create-sql-license-assignments/cancel-assignment.png" alt-text="Screenshot: die Kündigungsseite" lightbox="./media/create-sql-license-assignments/cancel-assignment.png" :::

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [häufig gestellten Fragen zum zentral verwalteten Azure-Hybridvorteil](faq-azure-hybrid-benefit-scope.yml).
- Informieren Sie sich über die Anwendung von Rabatten unter [Was ist der zentral verwaltete Azure-Hybridvorteil?](overview-azure-hybrid-benefit-scope.md).