---
title: Verbinden und Verwalten mehrerer Azure-Quellen
description: In diesem Handbuch wird beschrieben, wie Sie eine Verbindung mit mehreren Azure-Quellen in Azure Purview gleichzeitig herstellen und die Funktionen von Purview verwenden, um Ihre Quellen zu überprüfen und zu verwalten.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 49e6ca8cf0fc3121aba3c3216d2a1dd5936b56d7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449524"
---
# <a name="connect-to-and-manage-multiple-azure-sources-in-azure-purview"></a>Verbinden und Verwalten mehrerer Azure-Quellen in Azure Purview

In diesem Artikel wird beschrieben, wie Sie mehrer Azure-Quellen registrieren und wie Sie diese in Azure Purview authentifizieren und damit interagieren. Weitere Informationen zu Azure Purview finden Sie im [Einführungsartikel](overview.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

|**Metadatenextrahierung**|  **Vollständige Überprüfung**  |**Inkrementelle Überprüfung**|**Bereichsbezogene Überprüfung**|**Klassifizierung**|**Zugriffsrichtlinie**|**Herkunft**|
|---|---|---|---|---|---|---|
| [Ja](#register) | [Ja](#scan) | [Ja](#scan) | [Ja](#scan)| [Ja](#scan)| Nein| [Quelle abhängig](catalog-lineage-user-guide.md)|

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine aktive [Purview-Ressource](create-catalog-portal.md)

* Sie müssen ein Datenquellenadministrator und Datenleser sein, um eine Quelle zu registrieren und in Purview Studio zu verwalten. Weitere Informationen finden Sie auf der [Seite Azure Purview-Berechtigungen](catalog-permissions.md).

## <a name="register"></a>Register

In diesem Abschnitt wird beschrieben, wie Sie mehrere Azure-Quellen in Azure Purview mithilfe von [Purview Studio](https://web.purview.azure.com/) registrieren.

### <a name="prerequisites-for-registration"></a>Voraussetzungen für die Registrierung

Sie müssen eine Authentifizierung einrichten, um Ressourcen unter einem Abonnement oder einer Ressourcengruppe aufzählen zu können.

1. Gehen Sie zu dem Abonnement oder der Ressourcengruppe im Azure-Portal.  
1. Wählen Sie im linken Menü  **Zugriffskontrolle (IAM)**  aus.
1. Wählen Sie **+ Hinzufügen** aus.
1. Wählen Sie im **Eingabefeld Auswählen** die Rolle **Reader** aus und geben Sie den Namen Ihres Azure Purview-Kontos ein (der den Namen der MSI-Datei darstellt). 
1. Klicken Sie auf **Speichern**, um die Rollenzuweisung abzuschließen.

### <a name="authentication-for-registration"></a>Authentifizierung für die Registrierung

Es sind zwei Möglichkeiten verfügbar, die Authentifizierung für mehrere Quellen in Azure einzurichten:

* Verwaltete Identität
* Dienstprinzipal

Sie müssen die Authentifizierung für jede Ressource innerhalb Ihres Abonnements oder Ihrer Ressourcengruppe einrichten, die Sie registrieren und überprüfen möchten. Azure-Storage-Ressourcentypen (Azure Blob Storage und Azure Data Lake Storage Gen2) erleichtern es, indem sie es Ihnen ermöglichen, die MSI-Datei oder den Dienstprinzipal auf der Abonnement- oder Ressourcengruppenebene als Speicher-Blob-Datenleser hinzuzufügen. Die Berechtigungen werden dann auf jedes Speicherkonto innerhalb dieses Abonnements oder dieser Ressourcengruppe übertragen. Für alle anderen Ressourcentypen müssen Sie die MSI-Datei oder das Dienstprinzipal auf jede Ressource anwenden oder ein Skript zu diesem Zweck erstellen.

Wie Sie Berechtigungen für jeden Ressourcentyp innerhalb eines Abonnements oder einer Ressourcengruppe hinzufügen, erfahren Sie in den folgenden Ressourcen:
    
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen1](register-scan-adls-gen1.md#authentication-for-a-scan)
- [Azure Data Lake Storage Gen2](register-scan-adls-gen2.md#authentication-for-a-scan)
- [Azure SQL-Datenbank](register-scan-azure-sql-database.md#authentication-for-a-scan)
- [Verwaltete Azure SQL-Datenbank-Instanz](register-scan-azure-sql-database-managed-instance.md#authentication-for-registration)
- [Azure Synapse Analytics](register-scan-azure-synapse-analytics.md#authentication-for-registration)

### <a name="steps-to-register"></a>Schritte zur Registrierung

1. Gehen Sie zu Ihrem Azure Purview-Konto.
1. Wählen Sie im linken Menü **Data Map** aus.
1. Wählen Sie **Registrieren**.
1. Wählen Sie unter **Quellen registrieren** die Option **Azure (mehrere)** aus.

   :::image type="content" source="media/register-scan-azure-multiple-sources/register-azure-multiple.png" alt-text="Screenshot, der die Fläche für Azure Multiple auf der Anzeige für die Registrierung von mehreren Quellen anzeigt.":::

1. Wählen Sie **Weiter**.
1. Auf der Anzeige **Quellen registrieren (Azure)** gehen Sie wie folgt vor:

   1. Geben Sie im **Namensfeld** einen Namen ein, unter dem die Datenquelle im Katalog aufgelistet werden soll. 
   1. Wählen Sie im **Managementgruppen-Feld** fakultativ eine Managementgruppe aus, nach der gefiltert werden soll.
   1. Wählen Sie in den Dropdownlistenfeldern **Abonnement** und **Ressourcengruppe** ein Abonnement, bzw. eine bestimmte Ressourcengruppe aus. Der Registrierungsbereich wird auf das ausgewählte Abonnement oder die Ressourcengruppe festgelegt.  

      :::image type="content" source="media/register-scan-azure-multiple-sources/azure-multiple-source-setup.png" alt-text="Screenshot, der die Felder zur Auswahl eines Abonnements und einer Ressourcengruppe anzeigt.":::

   1. Wählen **Sie** im Feld Sammlung auswählen eine Sammlung aus, oder erstellen Sie eine neue Sammlung (optional).
   1. Wählen Sie **Registrieren** aus, um die Datenquellen zu registrieren.

## <a name="scan"></a>Überprüfen

Führen Sie die folgenden Schritte aus, um mehrer Azure-Quellen zu überprüfen und automatisch Assets zu identifizieren und Ihre Daten zu klassifizieren. Weitere Informationen zum Scannen im Allgemeinen finden Sie in unserer [Einführung in Scans und Aufnahme](concept-scans-and-ingestion.md).

### <a name="create-and-run-scan"></a>Scan erstellen und ausführen

Gehen Sie zum Erstellen und Ausführen einer neuen Überprüfung wie folgt vor:

1. Wählen Sie im linken Bereich in Purview Studio die Registerkarte **Data Map** aus.
1. Wählen Sie die von Ihnen registrierte Datenquelle aus.
1. Wählen Sie **Details anzeigen** >  **+** Neue Überprüfung, oder verwenden Sie das Schnellaktionssymbol **Überprüfen** auf der Quellfläche.
1. Geben Sie unter **Name** den Namen ein.
1. Wählen Sie unter **Typ** die Ressourcentypen aus, die Sie in dieser Quelle überprüfen möchten. Wählen Sie eine der Optionen aus:

    - Belassen Sie es als **Alle**. Diese Auswahl umfasst zukünftige Ressourcentypen, die möglicherweise derzeit nicht in diesem Abonnement oder dieser Ressourcengruppe vorhanden sind.
    - Verwenden Sie die Felder, um gezielt Ressourcentypen auszuwählen, die Sie überprüfen möchten. Wenn Sie diese Option wählen, werden zukünftige Ressourcentypen, die innerhalb dieses Abonnements oder dieser Ressourcengruppe erstellt werden könnten, nicht für Überprüfungen berücksichtigt, es sei denn, die Überprüfung wird zukünftig explizit bearbeitet.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-scan.png" alt-text="Screenshot, der die Optionen zum Überprüfen mehrerer Quellen anzeigt.":::

1. Wählen Sie die Zugangsdaten für die Verbindung zu den Ressourcen innerhalb Ihrer Datenquelle:
    - Sie können Anmeldeinformationen auf der übergeordneten Ebene als MSI-Datei auswählen, oder Sie können Anmeldeinformationen für einen bestimmten Dienstprinzipaltyp auswählen. Sie können diese Anmeldeinformationen dann für alle Ressourcentypen unter dem Abonnement oder der Ressourcengruppe verwenden.
    - Sie können den Ressourcentyp gezielt auswählen und eine andere Anmeldeinformation für diesen Ressourcentyp anwenden.

    Jede Anmeldeinformation wird als Authentifizierungsmethode für alle Ressourcen unter einem bestimmten Typ betrachtet. Sie müssen die ausgewählten Anmeldeinformationen auf den Ressourcen einstellen, um sie erfolgreich zu überprüfen, wie [weiter oben in diesem Artikel](#authentication-for-registration) beschrieben.
1. Innerhalb jedes Typs können Sie wählen, ob Sie entweder alle Ressourcen oder eine Teilmenge davon nach Namen überprüfen möchten:
    - Wenn Sie die Option auf **Alle** belassen, werden auch zukünftige Ressourcen dieses Typs bei zukünftigen Überprüfungsläufen überprüft.
    - Wenn Sie bestimmte Speicherkonten oder SQL-Datenbanken auswählen, werden zukünftige Ressourcen dieses Typs, die innerhalb dieses Abonnements oder dieser Ressourcengruppe erstellt wurden, nicht für Überprüfungen berücksichtigt, es sei denn, die Überprüfung wird in Zukunft explizit bearbeitet.

1. Klicken Sie auf **Verbindung testen**. Dies testet zunächst den Zugriff, um zu prüfen, ob Sie die Azure Purview MSI-Datei als Leser auf das Abonnement oder die Ressourcengruppe angewendet haben. Wenn Sie eine Fehlermeldung erhalten, befolgen Sie diese [Anweisungen](#prerequisites-for-registration), um es zu beheben. Anschließend wird die Authentifizierung und Verbindung mit den einzelnen ausgewählten Quellen testen und ein Bericht generiert. Die Anzahl der ausgewählten Quellen wirkt sich auf die Zeit aus, die zum Generieren dieses Berichts benötigt wird. Wenn bei einigen Ressourcen ein Fehler aufgetreten ist, wird beim Bewegen des Mauszeigers auf das **X**-Symbol die ausführliche Fehlermeldung angezeigt.

    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection.png" alt-text="Screenshot: Schieberegler für die Einrichtung der Überprüfung mit hervorgehobener Schaltfläche &quot;Verbindung testen&quot;.":::
    :::image type="content" source="media/register-scan-azure-multiple-sources/test-connection-report.png" alt-text="Screenshot eines Beispielberichts für eine Testverbindung, bei dem einige Verbindungen bestehen und einige fehlschlagen. Wenn Sie mit dem Zeigen auf eine der fehlerhaften Verbindungen zeigen, wird ein ausführlicher Fehlerbericht angezeigt.":::

1. Nachdem Sie die Verbindung getestet haben, wählen Sie **Weiter** aus, um fortzufahren.

1. Wählen Sie Überprüfungsregelsätze für jeden Ressourcentyp aus, den Sie im vorherigen Schritt ausgewählt haben. Sie können Überprüfungsregelsätze auch inline erstellen.
  
   :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-rule-set.png" alt-text="Screenshot, der die Überprüfungsregeln für jeden Ressourcentyp anzeigt.":::

1. Wählen Sie den Auslöser für die Überprüfung. Sie können es so planen, dass es wöchentlich, monatlich oder einmal ausgeführt wird.

1. Begutachten Sie Ihre Überprüfung, und schließen Sie die Einrichtung mit **Speichern** ab.

## <a name="view-your-scans-and-scan-runs"></a>Betrachten Sie Ihre Überprüfungen und Überprüfungsausführungen

1. Zeigen Sie Details zur Quelle an, indem Sie auf der Kachel unter dem Abschnitt **Data Map** die Option **Details anzeigen** auswählen.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-source-detail.png" alt-text="Screenshot, der die Quellendetails anzeigt."::: 

1. Zeigen Sie die Details der Überprüfung-Ausführung an, indem Sie auf die Seite mit den **Überprüfungsdetails** gehen.
   
    Die *Statusleiste* ist eine kurze Zusammenfassung des Ausführungsstatus der untergeordneten Ressourcen. Sie wird auf Abonnement- oder Ressourcengruppenebene angezeigt. Die Farben haben die folgenden Bedeutungen:
    
    - Grün: Die Überprüfung war erfolgreich.
    - Rot: Die Überprüfung ist fehlgeschlagen. 
    - Grau: Der Überprüfungsvorgang wird noch ausgeführt.
   
    Sie können jede Überprüfung auswählen, um genauere Details anzuzeigen.

    :::image type="content" source="media/register-scan-azure-multiple-sources/multiple-scan-full-details.png" alt-text="Screenshot, das die Überprüfungsdetails anzeigt.":::

1. Zeigen Sie unten in den Quellendetails eine Zusammenfassung der letzten fehlgeschlagenen Überprüfungsausführungen an. Sie können auch genauere Details zu diesen Ausführungen anzeigen.

## <a name="manage-your-scans-edit-delete-or-cancel"></a>Verwalten Sie Ihre Überprüfungen: bearbeiten, löschen oder abbrechen

Gehen Sie zum Verwalten oder Löschen einer Überprüfung wie folgt vor:

1. Navigieren Sie zum Verwaltungscenter.
1. Wählen Sie **Datenquellen** unter dem Abschnitt **Quellen und Überprüfungen** aus und wählen Sie dann die gewünschte Datenquelle aus.
1. Wählen Sie die Überprüfung aus, die Sie verwalten möchten. Führen Sie anschließend Folgendes durch: 

   - Wenn Sie die Überprüfung bearbeiten möchten, wählen Sie **Bearbeiten** aus.
   - Sie können die Überprüfung löschen, indem Sie **Löschen** wählen.
   - Wenn die Überprüfung ausgeführt wird, kann sie abgebrochen werden, indem Sie **Abbrechen** auswählen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihre Quelle registriert haben, halten Sie sich an die folgenden Anleitungen, um mehr über Purview und Ihre Daten zu erfahren.

- [Dateneinblicke in Azure Purview](concept-insights.md)
- [Datenherkunft in Azure Purview](catalog-lineage-user-guide.md)
- [Data Catalog suchen](how-to-search-catalog.md)
