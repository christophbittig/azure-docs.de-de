---
title: Konfigurieren von Dienstendpunktrichtlinien
description: Konfigurieren Sie Dienstendpunktrichtlinien für Azure Storage, um Azure SQL Managed Instance vor der Exfiltration in nicht autorisierte Azure Storage-Konten zu schützen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: references_regions, ignite-fall-2021
ms.devlang: ''
ms.topic: how-to
author: zoran-rilak-msft
ms.author: zoranrilak
ms.reviewer: mathoma
ms.date: 11/02/2021
ms.openlocfilehash: 9760285da827e884c5dfa49ca1da58b3e76292de
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095086"
---
# <a name="configure-service-endpoint-policies-preview-for-azure-sql-managed-instance"></a>Konfigurieren von Dienstendpunktrichtlinien (Vorschauversion) für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

VNET-[Dienstendpunktrichtlinien](../../virtual-network/virtual-network-service-endpoint-policies-overview.md) für Azure Storage ermöglichen es Ihnen, ausgehenden virtuellen Netzwerkdatenverkehr zu Azure Storage-Konten zu filtern und die Datenübertragung nur auf bestimmte Azure Storage-Konten zu beschränken.

Die Option, Ihre Endpunktrichtlinien zu konfigurieren und sie Ihrer Instanz von SQL Managed Instance zuzuordnen, befindet sich derzeit in der Vorschauversion. 

## <a name="key-benefits"></a>Hauptvorteile

Durch das Konfigurieren von VNET-Dienstendpunktrichtlinien für Azure Storage in Ihrer Instanz von Azure SQL Managed Instance können Sie von den folgenden Vorteilen profitieren:

- __Verbesserte Sicherheit für den Datenverkehr von Ihrer Azure SQL Managed Instance-Instanz an Azure Storage__: Endpunktrichtlinien dienen als Sicherheitskontrolle, durch die eine fehlerhafte oder böswillige Exfiltration unternehmenskritischer Daten verhindert wird. Der Datenverkehr kann auf die Speicherkonten beschränkt werden, die mit Ihren Datengovernanceanforderungen übereinstimmen.

- __Präzise Steuerung, auf welche Speicherkonten zugegriffen werden kann__: Dienstendpunktrichtlinien können die Datenübertragung zu Speicherkonten auf verschiedenen Ebenen (Abonnement, Ressourcengruppe oder individuelles Speicherkonto) zulassen. Administratoren können Dienstendpunktrichtlinien verwenden, um die Einhaltung der Datenschutzarchitektur ihrer Organisation in Azure zu erzwingen.

- __Der Systemdatenverkehr ist nicht betroffen__: Dienstendpunktrichtlinien blockieren niemals den Zugriff auf Speicherplatz, der für die ordnungsgemäße Funktion von Azure SQL Managed Instance erforderlich ist. Dazu gehört das Speichern von Sicherungsdateien, Datendateien, Transaktionsprotokolldateien und anderen Ressourcen.

> [!IMPORTANT]
> Mit Dienstendpunktrichtlinien wird nur der Datenverkehr gesteuert, der aus dem SQL Managed Instance-Subnetz stammt und an Azure Storage geleitet wird. Die Richtlinien wirken sich bspw. nicht auf das Exportieren der Datenbank in eine lokale BACPAC-Datei, die Integration von Azure Data Factory, die Sammlung von Diagnoseinformationen über Azure Diagnostic Settings oder andere Datenextraktionsmechanismen aus, die nicht direkt auf Azure Storage abzielen.

## <a name="limitations"></a>Einschränkungen

Folgende Einschränkungen sind beim Aktivieren von Dienstendpunktrichtlinien für Ihre Instanz von Azure SQL Managed Instance zu beachten:

- In der Vorschauversion ist dieses Feature in allen Azure-Regionen verfügbar, in denen SQL Managed Instance unterstützt wird, mit Ausnahme von **China, Osten 2**, **China, Norden 2**, **USA, Mitte EUAP**, **USA, Osten 2 EUAP**, **US Gov Arizona**, **US Gov Texas**, **US Gov Virginia** und **USA, Westen-Mitte**.
- Das Feature ist nur für virtuelle Netzwerke verfügbar, für die das Azure Resource Manager-Bereitstellungsmodell verwendet wird.
- Das Feature ist nur in Subnetzen verfügbar, in denen [Dienstendpunkte](../../virtual-network/virtual-network-service-endpoints-overview.md) für Azure Storage aktiviert sind.
- Das Aktivieren von Dienstendpunkten für Azure Storage wird auch auf Regionspaare ausgeweitet, in denen Sie das virtuelle Netzwerk bereitstellen, um den Datenverkehr für georedundanten Speicher mit Lesezugriff (RA-GRS) und georedundanten Speicher (GRS) zu unterstützen.
- Durch das Zuweisen einer Dienstendpunktrichtlinie zu einem Dienstendpunkt wird dessen regionaler Bereich zu einem globalen Bereich erweitert. Das heißt, dass der gesamte Datenverkehr zu Azure Storage, unabhängig von der Region, in der sich das Speicherkonto befindet, über den Dienstendpunkt abgewickelt wird.

## <a name="prepare-storage-inventory"></a>Bestandsaufnahme der Speicherkonten vorbereiten

Bevor Sie mit dem Konfigurieren von Dienstendpunktrichtlinien in einem Subnetz beginnen, erstellen Sie eine Liste mit Speicherkonten, auf die die verwaltete Instanz in diesem Subnetz zugreifen soll.

Es folgt eine Liste von Workflows, die mit Azure Storage kommunizieren können:

- [Überwachung](auditing-configure.md) an Azure Storage
- Ausführen einer [Kopiesicherung](/sql/relational-databases/backup-restore/copy-only-backups-sql-server) in Azure Storage
- [Wiederherstellen](restore-sample-database-quickstart.md) einer Datenbank aus Azure Storage
- Importieren von Daten mithilfe von [BULK INSERT oder OPENROWSET(BULK...)](/sql/relational-databases/import-export/import-bulk-data-by-using-bulk-insert-or-openrowset-bulk-sql-server)
- Protokollieren [erweiterter Ereignisse](../database/xevent-db-diff-from-svr.md) an eine Ereignisdatei in Azure Storage
- [Offline-Migration von Azure DMS](../../dms/tutorial-sql-server-to-managed-instance.md) zu Azure SQL Managed Instance
- [Migration von Log Replay Service](log-replay-service-migrate.md) zu Azure SQL Managed Instance
- Synchronisieren von Tabellen mit der [Transaktionsreplikation](replication-transactional-overview.md)

Notieren Sie sich den Kontonamen, die Ressourcengruppe und das Abonnement für jedes Speicherkonto, das an diesen oder anderen Workflows beteiligt ist, die auf den Speicher zugreifen. 


## <a name="configure-policies"></a>Konfigurieren von Richtlinien

Zunächst müssen Sie Ihre Dienstendpunktrichtlinie erstellen und die Richtlinie dann dem SQL Managed Instance-Subnetz zuordnen. Passen Sie den Workflow in diesem Abschnitt an Ihre konkreten Geschäftsanforderungen an.


> [!NOTE]
> - SQL Managed Instance-Subnetze erfordern Richtlinien, die den Dienstalias /Services/Azure/ManagedInstance enthalten (siehe Schritt 5). 
> - Verwaltete Instanzen, die in einem Subnetz bereitgestellt werden, das bereits Dienstendpunktrichtlinien enthält, werden automatisch für den Dienstalias /Services/Azure/ManagedInstance aktualisiert.

### <a name="create-a-service-endpoint-policy"></a>Erstellen einer Dienstendpunktrichtlinie

Führen Sie die folgenden Schritte aus, um eine Dienstendpunktrichtlinie zu erstellen:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) an. 
1. Wählen Sie **+ Ressource erstellen**. 
1. Geben Sie im Suchbereich _Dienstendpunktrichtlinie_ ein, und wählen Sie anschließend **Dienstendpunktrichtlinie** und dann **Erstellen** aus.

   ![Dienstendpunktrichtlinie erstellen](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

1. Fügen Sie auf der Seite **Basics** (Grundeinstellungen) die folgenden Werte hinzu:

   - Abonnement: Wählen Sie aus dem Dropdown-Menü das Abonnement für Ihre Richtlinie aus.
   - Ressourcengruppe: Wählen Sie die Ressourcengruppe aus, in der sich Ihre verwaltete Instanz befindet, oder wählen Sie **Neu erstellen** aus und geben Sie den Namen für eine neue Ressourcengruppe ein.
   - Name: Geben Sie einen Namen für die Richtlinie ein, z. B. **mySEP**.
   - Standort: Wählen Sie die Region des virtuellen Netzwerks aus, in dem die verwaltete Instanz gehostet wird. 

   ![Grundlagen zur Erstellung von Dienstendpunktrichtlinien](../../virtual-network/media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

1. Wählen Sie unter **Richtliniendefinitionen** die Option **Alias hinzufügen** aus, und geben Sie im Bereich **Alias hinzufügen** die folgenden Informationen ein:
   - Dienstalias: Wählen Sie /Services/Azure/ManagedInstance aus.
   - Wählen Sie **Hinzufügen** aus, um das Hinzufügen des Dienstalias abzuschließen.

   ![Hinzufügen eines Alias zu einer Dienstendpunktrichtlinie](./media/service-endpoint-policies-configure/add-an-alias.png)

1. Wählen Sie im Bereich Richtliniendefinitionen unter **Ressourcen** die Option **+ Hinzufügen** aus, und geben Sie im Bereich **Ressource hinzufügen** die folgenden Informationen ein, oder wählen Sie sie dort aus:
   - Dienst: Wählen Sie **Microsoft.Storage** aus.
   - Bereich: Wählen Sie **Alle Konten im Abonnement** aus.
   - Abonnement: Wählen Sie das Abonnement aus, das die zuzulassenden Speicherkonten enthält. Weitere Informationen finden Sie in Ihrem [Azure-Speicherkontobestand](#prepare-storage-inventory), den Sie im vorherigen Schritt erstellt haben.
   - Wählen Sie **Hinzufügen** aus, um das Hinzufügen der Ressource abzuschließen.
   - Wiederholen Sie diesen Schritt, um weitere Abonnements hinzuzufügen. 

   ![Hinzufügen einer Ressource zu einer Dienstendpunktrichtlinie](./media/service-endpoint-policies-configure/add-a-resource.png)

1. Optional: Sie können Tags für die Dienstendpunktrichtlinie unter **Tags** konfigurieren.
1.  Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie die Informationen und wählen Sie **Erstellen** aus. Um weitere Änderungen vorzunehmen, klicken Sie auf **Zurück**.

   > [!TIP]
   > Als Erstes müssen Sie Richtlinien konfigurieren, um den Zugriff auf ganze Abonnements zuzulassen. Überprüfen Sie die Konfiguration, indem Sie sicherstellen, dass alle Workflows normal funktionieren. Anschließend können Sie die Richtlinien optional neu konfigurieren, um einzelne Speicherkonten oder Konten in einer Ressourcengruppe zuzulassen. Wählen Sie hierzu im Feld _Bereich:_ die Option **Einzelnes Konto** oder **Alle Konten in der Ressourcengruppe** aus, und füllen Sie die anderen Felder entsprechend aus.

### <a name="associate-policy-with-subnet"></a>Zuordnen einer Richtlinie zum Subnetz

Nachdem Sie die Dienstendpunktrichtlinie erstellt haben, ordnen Sie die Richtlinie Ihrem SQL Managed Instance-Subnetz zu.

Führen Sie dazu die folgenden Schritte aus:

1. Suchen Sie im Feld _Alle Dienste_ im Azure-Portal nach _virtuellen Netzwerken_. Wählen Sie **Virtuelle Netzwerke** aus.
1. Suchen Sie das virtuelle Netzwerk, in dem Ihre verwaltete Instanz gehostet wird, und wählen Sie es aus.
1. Wählen Sie dann **Subnetze** und das für Ihre verwaltete Instanz dedizierte Subnetz aus. Geben Sie im Bereich „Subnetze“ die folgenden Informationen ein:
    - Dienste: Wählen Sie **Microsoft.Storage** aus. Wenn dieses Feld leer ist, müssen Sie den Dienstendpunkt für Azure Storage in diesem Subnetz konfigurieren.
    - Dienstendpunktrichtlinien: Wählen Sie alle Dienstendpunktrichtlinien aus, die Sie auf das SQL Managed Instance-Subnetz anwenden möchten.

   ![Zuordnen einer Dienstendpunktrichtlinie zu einem Subnetz](./media/service-endpoint-policies-configure/associate-service-endpoint-policy.png)

1. Wählen Sie **Speichern** aus, um das Konfigurieren des virtuellen Netzwerks abzuschließen.

> [!WARNING]
> Wenn die Richtlinien in diesem Subnetz nicht über den Alias `/Services/Azure/ManagedInstance` verfügen, wird möglicherweise der folgende Fehler angezeigt: ` Failed to save subnet 'subnet'. Error: 'Found conflicts with NetworkIntentPolicy.`
> `Details: Service endpoint policies on subnet are missing definitions`
> Um dies zu beheben, aktualisieren Sie alle Richtlinien im Subnetz so, dass sie den Alias `/Services/Azure/ManagedInstance` enthalten.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Sichern Ihrer Azure Storage-Konten](../../storage/common/storage-network-security.md).
- Informieren Sie sich über die [Sicherheitsfunktionen von SQL Managed Instance](../database/security-overview.md).
- Weitere Informationen über die [Konnektivitätsarchitektur](connectivity-architecture-overview.md) von Azure SQL Managed Instance.
