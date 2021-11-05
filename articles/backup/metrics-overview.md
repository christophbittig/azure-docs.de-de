---
title: Überwachen Sie die Integrität Ihrer Sicherungen mithilfe Azure Backup Metriken (Vorschau)
description: In diesem Artikel erfahren Sie mehr über die verfügbaren Metriken für Azure Backup zur Überwachung des Zustands Ihrer Datensicherung
ms.topic: conceptual
author: v-amallick
ms.date: 10/20/2021
ms.author: v-amallick
ms.service: backup
ms.openlocfilehash: 31e68c8a25dcf4fee199a7c73869cc5de64793e9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094893"
---
# <a name="monitor-the-health-of-your-backups-using-azure-backup-metrics-preview"></a>Überwachen Sie die Integrität Ihrer Sicherungen mithilfe Azure Backup Metriken (Vorschau)

Azure Backup bietet jetzt über Azure Monitor eine Reihe von integrierten Metriken, mit denen Sie den Zustand Ihrer Backups überwachen können. Außerdem können Sie Warnungsregeln konfigurieren, die ausgelöst werden, wenn die Metriken definierte Schwellenwerte überschreiten.

Azure Backup bietet die folgenden Hauptfunktionen:

* Die Möglichkeit, sofort einsatzbereite Metriken in Bezug auf den Sicherungs- und Wiederherstellungszustand Ihrer Sicherungselemente sowie die damit verbundenen Trends anzuzeigen
* Die Möglichkeit, benutzerdefinierte Warnregeln für diese Metriken zu erstellen, um den Zustand Ihrer Sicherungselemente effizient zu überwachen
* Möglichkeit zum Routen ausgelöster Metrikwarnungen an verschiedene Benachrichtigungskanäle, die von Azure Monitor unterstützt werden, z. B. E-Mail, ITSM, Webhook, Logik-Apps und so weiter.

Erfahren Sie mehr über [Azure Monitor-Metriken](/azure/azure-monitor/essentials/data-platform-metrics).

## <a name="supported-scenarios"></a>Unterstützte Szenarien

- Unterstützt integrierte Metriken für die folgenden Workloadtypen:

  - Azure VM, SQL-Datenbanken in Azure VM
  - SAP HANA-Datenbanken in Azure VM
  - Azure-Dateien.

  Metriken für den Workloadtyp der HANA-Instanz werden derzeit nicht unterstützt.

- Metriken können für alle Recovery Services-Tresore in jeder Region und jedem Abonnement gleichzeitig angezeigt werden. Das Anzeigen von Metriken für einen größeren Bereich im Azure-Portal wird derzeit nicht unterstützt. Die gleichen Grenzwerte gelten auch für die Konfiguration von Metrikwarnungsregeln.

## <a name="supported-built-in-metrics"></a>Unterstützte integrierte Metrikenen

Derzeit unterstützt Azure Backup die folgenden Metriken:

- **Sicherungszustandsereignisse**: Der Wert dieser Metrik stellt die Anzahl der Integritätsereignisse im Zusammenhang mit der Integrität des Sicherungsauftrags dar, die innerhalb eines bestimmten Zeitpunkts für den Tresor ausgelöst wurden. Nach Abschluss eines Sicherungsauftrags erstellt der Azure Backup Service ein Sicherungszustandsereignis. Je nach Auftragsstatus (z. B. erfolgreich oder fehlgeschlagen) variieren die Dimensionen, die dem Ereignis zugeordnet sind.

- **Integritätsereignisse wiederherstellen**: Der Wert dieser Metrik stellt die Anzahl der Integritätsereignisse im Zusammenhang mit der Integrität des Wiederherstellungsauftrags dar, die innerhalb eines bestimmten Zeitpunkts für den Tresor ausgelöst wurden. Nach Abschluss eines Sicherungsauftrags erstellt der Azure Backup Service ein Sicherungszustandsereignis. Je nach Auftragsstatus (z. B. erfolgreich oder fehlgeschlagen) variieren die Dimensionen, die dem Ereignis zugeordnet sind.

Standardmäßig werden die Zählungen auf Tresorebene angezeigt. Um die Anzahl für ein bestimmtes Sicherungselement und den Auftragsstatus anzuzeigen, können Sie die Metriken nach einer der unterstützten Dimensionen filtern.

In der folgenden Tabelle sind die Dimensionen aufgeführt, die von den Metriken "Sicherungszustandsereignisse" und "Wiederherstellungszustandsereignisse" unterstützt werden:
 
| **Dimensionsname**        | **Beschreibung**   | 
| ----------------------------| ----------------- |
| DataSource ID               | Die individuelle ID der [Datenquelle](azure-backup-glossary.md#datasource), die dem Auftrag zugeordnet ist. <br><br> <ul><li> Für Azure-Ressourcen wie VMs und Dateien enthält dies die Azure Resource Manager-ID (ARM-ID) der Ressource. <br> Zum Beispiel, `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM` </li><br><br> <li> Für SQL/HANA-Datenbanken auf virtuellen Computer enthält dies die ARM-ID des virtuellen Computers, gefolgt von Details zur Datenbank. <br> Zum Beispiel, `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM/providers/Microsoft.RecoveryServices/backupProtectedItem/SQLDataBase;mssqlserver;msdb` </li></ul>  <br><br> Bei SQL-Datenbanksicherung ist das Feld **Datenquellen-ID** leer, da in solchen Szenarien keine Datenquelle (VM) vorhanden ist. Um Metriken für eine bestimmte Datenbank innerhalb einer AG anzuzeigen, verwenden Sie das Feld **Sicherungsinstanz-ID**.|
| Datenquellentyp             | Typ der [Datenquelle](azure-backup-glossary.md#datasource), die dem Auftrag zugeordnet ist. Es folgen die unterstützten Datenquellentypen: <br><br> <ul><li> Microsoft.Compute/virtualMachines (Azure Virtual Machines – VMs)</li> <br><br> <li> Microsoft.Storage/storageAccounts/fileServices/shares (Azure Files) </li>  <br><br> <li> SQLDatabase (SQL in Azure-VM) </li><br><br> <li> SAPHANADataBase (SAP HANA in Azure VM)</li></ul>   |
| Backup Instance ID          | Die ARM-ID der [Sicherungsinstanz](azure-backup-glossary.md#backup-instance--backup-item), die dem Auftrag zugeordnet ist. <br><br> Zum Beispiel, `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;iaasvmcontainerv2;testRG;testVM` |
| Backup Instance Name        | Der Benutzername der Sicherungsinstanz in einfacher Lesbarkeit. Er hat das Format vor `{protectedContainerName};{backupItemFriendlyName}`. <br><br> Zum Beispiel, `testStorageAccount;testFileShare`      |
| Integritätsstatus               | Stellt die Integrität des Sicherungselements nach Abschluss des Auftrags dar. Dies kann einen der folgenden Werte enthalten: Fehlerfrei, Vorübergehend Fehlerhaft, Dauerhaft fehlerhaft, Vorübergehend heruntergestuft, Dauerhaft heruntergestuft. <br> <br> <ul> <li> Wenn ein Sicherungs-/Wiederherstellungsauftrag erfolgreich ist, wird ein Integritätsereignis mit dem Status _Fehlerfrei_ angezeigt. </li><br><br><li>_Fehlerhaft_ erscheint bei Störungen aufgrund von Dienstfehlern, und _Heruntergestuft_ erscheint bei Störungen aufgrund von Benutzerfehlern. </li> <br><br><li> Wenn der gleiche Fehler für dasselbe Sicherungselement wiederholt auftritt, ändert sich der Status von _Vorübergehend Fehlerhaft/Heruntergestuft_ in _Dauerhaft Fehlerhaft/Heruntergestuft_. </li></ul> |

## <a name="monitoring-scenarios"></a>Überwachungsszenarien

### <a name="view-metrics-in-the-azure-portal"></a>Anzeigen von Metriken im Azure-Portal

Um Metriken im Azure-Portal anzuzeigen, führen Sie die folgenden Schritte aus:

1. Gehen Sie zum **Sicherungscenter**  im  Azure-Portal und klicken Sie im Menü auf **Metriken**.

   :::image type="content" source="./media/metrics-overview/backup-center-metrics-selection-inline.png" alt-text="Screenshot: Auswahl von Metriken im Backup Center." lightbox="./media/metrics-overview/backup-center-metrics-selection-expanded.png":::

1. Wählen Sie einen Tresor oder eine Gruppe von Tresoren aus, für die Sie Metriken anzeigen möchten.

   Derzeit können Sie Metriken maximal anzeigen für: Alle Recovery Services-Tresore in einem bestimmten Abonnement und einer bestimmten Region. Beispiel: Alle Recovery Services-Tresore in USA, Osten in _TestSubscription1_.

1. **Wählen Sie eine Metrik aus**, um _Sicherungszustandsereignisse oder Wiederherstellungszustandsereignisse anzeigen zu können_.

   Dadurch wird ein Diagramm erstellt, das die Anzahl der Integritätsereignisse für die Tresore zeigt. Sie können den Zeitbereich und die Auflösung anpassen, indem Sie die Filter am oberen Bildschirmrand verwenden.

   :::image type="content" source="./media/metrics-overview/metrics-chart-inline.png" alt-text="Screenshot: Prozess zum Auswählen einer Metrik." lightbox="./media/metrics-overview/metrics-chart-expanded.png":::

1. Um Metriken nach verschiedenen Dimensionen zu filtern, klicken Sie **auf die Schaltfläche Filter hinzufügen** und wählen Sie die relevanten Dimensionswerte aus. 

   - Wenn Sie beispielsweise die Anzahl der Integritätsereignisse nur für Azure-VM-Sicherungen sehen möchten, fügen Sie einen Filter `Datasource Type = Microsoft.Compute/virtualMachines` hinzu. 
   - Verwenden Sie zum Anzeigen von Integritätsereignissen für eine bestimmte Datenquelle oder Sicherungsinstanz im Tresor die Filter für Datenquellen-ID/Sicherungsinstanz-ID.
   - Wenn Sie Integritätsereignisse nur für fehlerhafte Sicherungen anzeigen möchten, verwenden Sie einen Filter für HealthStatus, indem Sie die Werte auswählen, die dem integritätsgefährdenden oder beeinträchtigten Integritätsstatus entsprechen.

   :::image type="content" source="./media/metrics-overview/metrics-filters-inline.png" alt-text="Screenshot: Prozess zum Filtern von Metriken nach verschiedenen Dimensionen." lightbox="./media/metrics-overview/metrics-filters-expanded.png":::

### <a name="configure-alerts-and-notifications-on-your-metrics"></a>Konfigurieren von Warnungen und Benachrichtigungen für Ihre Metriken

Führen Sie die folgenden Schritte aus, um Warnungen und Benachrichtigungen für Ihre Metriken zu konfigurieren:

1. Klicken Sie oben im Fenster des Metrikdiagramms auf **Neue Warnungsregel**.

1. Wählen Sie den Bereich aus, für den Sie Warnungen erstellen möchten.   <br><br>    Die Bereichsgrenzwerte sind identisch mit den Grenzwerten, die im Abschnitt [Anzeigen von Metriken](#view-metrics-in-the-azure-portal) beschrieben sind.

1. Wählen Sie die Bedingung aus, unter der die Warnung ausgelöst werden soll.      <br><br>   Standardmäßig werden einige Felder basierend auf der Auswahl im Metrikdiagramm vorab aufgefüllt. Sie können die Parameter nach Bedarf bearbeiten. Um einzelne Warnungen für jede Datenquelle im Tresor zu generieren, verwenden Sie die **Dimensionsauswahl** in der Metrikwarnungsregel. Im Folgenden finden Sie einige Szenarien:

   - Auslösen von Warnungen zu fehlgeschlagenen Sicherungsaufträgen für jede Datenquelle:

     **Warnungsregel: Eine Warnung wird ausgelöst, wenn für die Sicherungszustandsereignisse > 0 in den letzten 24 Stunden** angezeigt wurde für:
     - Dimensions["HealthStatus"]= "Dauerhaft Fehlerhaft / Vorübergehend Fehlerhaft"
     - Dimensions["DatasourceId"]= "Alle aktuellen und zukünftigen Werte"

   - Auslösen von Warnungen, wenn alle Sicherungen im Tresor für den Tag erfolgreich waren:

     **Warnungsregel: Eine Warnung wird ausgelöst, wenn für die Sicherungszustandsereignisse > 1 in den letzten 24 Stunden** angezeigt wurde für:
     - Dimensions["HealthStatus"]="Dauerhaft Fehlerhaft / Vorübergehend Fehlerhaft / Dauerhaft heruntergestuft / Vorübergehend heruntergestuft"

   :::image type="content" source="./media/metrics-overview/metric-alert-condition-inline.png" alt-text="Screenshot: Option zum Auswählen der Bedingung, unter der die Warnung ausgelöst werden soll." lightbox="./media/metrics-overview/metric-alert-condition-expanded.png":::

   >[!NOTE]
   >Wenn Sie mehr Dimensionen als Teil der Warnungsregelbedingung auswählen, steigen die Kosten (proportional zur Anzahl eindeutiger Kombinationen von Dimensionswerten). Wenn Sie mehr Dimensionen auswählen, können Sie mehr Kontext für eine ausgelöste Warnung erhalten.


1. Um Benachrichtigungen für diese Warnungen mithilfe von Aktionsgruppen zu konfigurieren, konfigurieren Sie eine Aktionsgruppe als Teil der Warnungsregel, oder erstellen Sie eine separate Aktionsregel.

   Wir unterstützen verschiedene Benachrichtigungskanäle, z. B. E-Mail, ITSM, Webhook, Logik-App, SMS. [Erfahren Sie mehr über Aktionsgruppen](/azure/azure-monitor/alerts/action-groups).

   :::image type="content" source="./media/metrics-overview/action-group-inline.png" alt-text="Screenshot: Prozess zum Konfigurieren von Benachrichtigungen für diese Warnungen mithilfe von Aktionsgruppen." lightbox="./media/metrics-overview/action-group-expanded.png":::

1. Konfigurieren des Verhaltens der automatischen Auflösung: Sie können Metrikwarnungen bei Bedarf als _zustandsunabhängig_ oder _zustandsbezogen_ konfigurieren.

   - Deaktivieren Sie die Option **Warnungen automatisch auflösen in der Warnungsregel, um ein** e Warnung bei jedem Auftragsfehler unabhängig davon zu generieren, ob der Fehler auf die gleiche zugrunde liegende Ursache (zustandsunabhängiges Verhalten) zurückgeht.
   - Alternativ können Sie dasselbe Kontrollkästchen aktivieren, um die Warnungen als zustandsabhängig zu konfigurieren. Wenn eine Metrikwarnung für den Bereich ausgelöst wird, wird daher bei einem anderen Fehler keine neue Metrikwarnung erstellt. Die Warnung wird automatisch aufgelöst, wenn die Warnungsgenerierungsbedingung für drei aufeinander folgende Auswertungszyklen als Fehlerhaft ausgewertet wird. Neue Warnungen werden generiert, wenn die Bedingung erneut als "Korrekt" ausgewertet wird.

[Lernen Sie mehr über das zustandsabhängige und zustandsunabhängige Verhalten von Azure Monitor Metrikwarnungen](/azure/azure-monitor/alerts/alerts-troubleshoot-metric#make-metric-alerts-occur-every-time-my-condition-is-met).

:::image type="content" source="./media/metrics-overview/auto-resolve-alert-inline.png" alt-text="Screenshot: Prozess zum Konfigurieren des Verhaltens der automatischen Auflösung." lightbox="./media/metrics-overview/auto-resolve-alert-expanded.png":::

### <a name="managing-alerts"></a>Verwalten von Warnungen

Führen Sie die folgenden Schritte aus, um die ausgelösten Metrikwarnungen anzeigen zu können:

1. Wechseln Sie zu **Backup Center** > **Warnungen**.
1. Filtern nach der **Signaltyp** = **Metrik** und dem **Warnungstyp** =  **, der konfiguriert ist**.
1. Klicken Sie auf eine Warnung, um weitere Details zur Warnung anzuzeigen und ihren Status zu ändern.

   :::image type="content" source="./media/metrics-overview/backup-center-metric-alerts-inline.png" alt-text="Screenshot: Prozess zum Anzeigen der ausgelösten Metrikwarnungen." lightbox="./media/metrics-overview/backup-center-metric-alerts-expanded.png":::

>[!NOTE]
>Die Warnung verfügt über zwei Felder: **Monitorbedingung (ausgelöst/aufgelöst)** und **Warnungsstatus (Neu/Bestätigt/Abgeschlossen)** .
>- **Warnungsstatus**: Sie können dieses Feld bearbeiten (wie im folgenden Screenshot gezeigt).
>- **Monitorbedingung**: Dieses Feld kann nicht bearbeitet werden. Dieses Feld wird häufiger in Szenarien verwendet, in denen der Dienst die Warnung selbst löst. Beispielsweise verwendet das Verhalten der automatischen Auflösung in Metrikwarnungen das Feld **Bedingung überwachen**, um eine Warnung aufzulösen.


#### <a name="datasource-alerts-and-global-alerts"></a>Datenquellenwarnungen und Allgemeine Warnungen

Basierend auf der Konfiguration der Warnungsregeln wird die ausgelöste Warnung im Abschnitt **Datenquellenwarnungen** oder im Abschnitt **Allgemeine Warnungen** im Backup Center angezeigt:

- Wenn der Warnung eine Dimension der Datenquellen-ID zugeordnet ist, wird die ausgelöste Warnung unter **Datenquellenwarnungen** angezeigt.
- Wenn der Warnung keine Dimension der Datenquellen-ID zugeordnet ist, wird die ausgelöste Warnung unter **Allgemeine Warnungen** angezeigt, da keine Informationen vorhanden sind, die die Warnung mit einer bestimmten Datenquelle verknüpft.

[Weitere Informationen zu Datenquellen und globalen Warnungen finden Sie hier](backup-center-monitor-operate.md#alerts)

### <a name="accessing-metrics-programmatically"></a>Programmgesteuertes Zugreifen auf Metrikdaten

Sie können die verschiedenen programmgesteuerten Clients wie PowerShell, CLI oder REST-API verwenden, um auf die Metrikfunktionen zuzugreifen. Weitere Informationen finden Sie in der[Azure Monitor REST-API-Dokumentation](/azure/azure-monitor/essentials/rest-api-walkthrough).

## <a name="next-steps"></a>Nächste Schritte
- [Weitere Informationen zu Überwachung und Berichterstellung mit Azure Backup](monitoring-and-alerts-overview.md)
- Erfahren Sie mehr über [Azure Monitor-Metriken](/azure/azure-monitor/essentials/data-platform-metrics).
- [Weitere Informationen zu Azure Warnungen](/azure/azure-monitor/alerts/alerts-overview).