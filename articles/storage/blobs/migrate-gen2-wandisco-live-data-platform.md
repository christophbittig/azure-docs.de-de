---
title: Data Lake Storage und WANdisco LiveData Platform for Azure
description: Hier erfahren Sie, wie Sie Petabytes an lokalen Hadoop-Daten ohne Downtime oder Unterbrechung von Datenvorgängen zu Azure Data Lake Storage Gen2-Dateisystemen migrieren.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 10/26/2021
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: b0b78d1233d6e605691e06c69c9d2167c7ecb592
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019218"
---
# <a name="migrate-on-premises-hadoop-data-to-azure-data-lake-storage-gen2-with-wandisco-livedata-platform-for-azure"></a>Migrieren lokaler Hadoop-Daten mithilfe von WANdisco LiveData Platform for Azure zu Azure Data Lake Storage Gen2

Mit [WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) können Sie Petabytes an lokalen Hadoop-Daten ohne Downtime oder Unterbrechung von Datenvorgängen zu Azure Data Lake Storage Gen2-Dateisystemen migrieren. Dank der kontinuierlichen Überprüfungen der Plattform gehen keine Daten verloren, und sie bleiben auf beiden Seiten der Übertragung konsistent, auch wenn sie geändert werden.

Die Plattform umfasst zwei Dienste: [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) für die Migration aktiv genutzter Daten aus lokalen Umgebungen zu Azure Storage und [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) zur Sicherstellung der konsistenten Replikation aller geänderten oder erfassten Daten.

> [!div class="mx-imgBorder"]
> ![Abbildung: Übersicht über LiveData Platform](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Beide Dienste können über das Azure-Portal und über die Azure CLI verwaltet werden. Beide Dienste folgen dem gleichen getakteten Abrechnungsmodell mit nutzungsbasierter Bezahlung wie alle anderen Azure-Dienste: Der Datenverbrauch von LiveData Platform for Azure wird auf der monatlichen Azure-Rechnung ausgewiesen, um Nutzungsmetriken bereitzustellen.

Im Gegensatz zur *Offlinemigration* von Daten durch [Kopieren statischer Informationen in Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md) und zur Verwendung von Hadoop-Tools wie [DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html) bleiben Ihre Unternehmenssysteme während der *Onlinemigration* mit WANdisco LiveData for Azure vollständig funktionsfähig. Ihre Big Data-Umgebungen bleiben auch beim Verschieben der Daten nach Azure in Betrieb.

## <a name="key-benefits-of-wandisco-livedata-platform-for-azure"></a>Wichtige Vorteile von WANdisco LiveData Platform for Azure

Das WAN-fähige Konsensmodul von [WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) sorgt für Datenkonsistenz und führt Echtzeitdatenreplikationen im großen Stil durch. Weitere Informationen erhalten Sie im folgenden Video:<br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho]

Die Plattform bietet unter anderem folgende Vorteile:

- **Datengenauigkeit:** Die End-to-End-Überprüfung von Daten verhindert Datenverluste und stellt sicher, dass übertragene Daten verwendungsbereit sind.

- **Datenkonsistenz:** Datenvolumes werden automatisch zwischen Umgebungen konsistent gehalten, auch wenn sie sich kontinuierlich ändern.

- **Dateneffizienz:** Große Datenmengen können kontinuierlich und mit vollständiger Kontrolle über die beanspruchte Bandbreite übertragen werden.

- **Keine Downtime:** Daten können während der Migration nach Belieben mit anderen Anwendungen erstellt, geändert, gelesen und gelöscht werden, ohne den Geschäftsbetrieb während der Datenübertragung an Azure unterbrechen zu müssen. Führen Sie Anwendungen, Analyseinfrastrukturen, Erfassungsaufträge und andere Verarbeitungsvorgänge weiterhin aus.

- **Unkomplizierte Verwendung:** Dank der Azure-Integration der Plattform können Sie automatisierte Migrationen erstellen, konfigurieren und planen sowie deren Status nachverfolgen. Bei Bedarf können Sie außerdem eine selektive Datenreplikation, Hive-Metadaten, Datensicherheit und Vertraulichkeit konfigurieren.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Wichtige Funktionen der WANdisco LiveData Platform für Azure

Einige der wichtigsten Funktionen der Plattform:

- **Metadatenmigration:** Neben HDFS-Daten können mithilfe von LiveData Migrator for Azure auch Metadaten (aus Hive und anderen Speichern) migriert werden.

- **Geplante Übertragung:** Mit LiveData Migrator for Azure können Sie steuern und automatisieren, wann die Datenübertragung initiiert wird, sodass Änderungen an Daten nicht manuell migriert werden müssen.

- **Kerberos:** LiveData Migrator for Azure unterstützt kerberisierte Cluster.

- **Ausschlussvorlagen:** In LiveData Migrator for Azure können Regeln erstellt werden, um zu verhindern, dass bestimmte (mithilfe von Globmustern definierte) Dateigrößen oder Dateinamen zu Ihrem Zielspeicher migriert werden. Erstellen Sie Ausschlussvorlagen im Azure-Portal oder mit der CLI, und wenden Sie sie auf eine beliebige Anzahl von Migrationen an.

- **Pfadzuordnungen:** Sie können alternative Zielpfade für bestimmte Zieldateisysteme definieren, damit übertragene Daten automatisch in von Ihnen angegebene Verzeichnisse verschoben werden.

- **Bandbreitenverwaltung:** Sie können die maximale Netzwerkbandbreite konfigurieren, die LiveData Migrator for Azure zur Verfügung stehen soll, um eine Überbeanspruchung von Bandbreite zu verhindern.

- **Ausschlüsse:** Sie können Vorlagenabfragen definieren, um die Migration von Dateien und Verzeichnissen zu verhindern, die den Kriterien entsprechen, was die selektive Migration von Daten aus Ihrem Quellsystem ermöglicht.

- **Metriken:** Sie können Details zur Datenübertragung in LiveData Migrator for Azure anzeigen – etwa die im Laufe der Zeit übertragenen Dateien, ausgeschlossene Pfade oder Elemente mit Übertragungsfehler.


   > [!div class="mx-imgBorder"]
   > ![LiveData](./media/migrate-gen2-wandisco-live-data-platform/metrics-1.png)

## <a name="migrate-big-data-faster-without-risk"></a>Schnelleres risikofreies Migrieren von Big Data

Der erste Dienst von WANdisco LiveData Platform for Azure ist [LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure). Er wird verwendet, um aktiv genutzte Daten aus lokalen Umgebungen zu Azure Storage zu migrieren. Nachdem Sie LiveData Migrator in Ihrem lokalen Hadoop-Cluster bereitgestellt haben, wird automatisch die optimale Konfiguration für Ihr Dateisystem erstellt. Geben Sie anschließend die Kerberos-Details für das System an. Und schon können von LiveData Migrator for Azure Daten zu Azure Storage migriert werden.

> [!div class="mx-imgBorder"]
> ![LiveData Migrator for Azure Architecture](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture-1.png)

Machen Sie sich zunächst mit den [Voraussetzungen](https://docs.wandisco.com/live-data-platform/docs/prereq/) vertraut, bevor Sie mit der Verwendung von LiveData Migrator for Azure beginnen.

So führen Sie eine Migration durch:

1. Führen Sie über die Azure CLI folgende Schritte aus: 

   - Führen Sie über die Azure CLI `az provider register --namespace Wandisco.Fusion --consent-to-permissions` aus, um sich für den WANdisco-Ressourcenanbieter zu registrieren.
   - Führen Sie `az vm image terms accept --offer ldma --plan metered-v1 --publisher Wandisco --subscription <subscriptionID>` aus, um die Bedingungen der getakteten Abrechnung von LiveData Platform zu akzeptieren.

2. Stellen Sie über das Azure-Portal eine LiveData Migrator-Instanz in Ihrem lokalen Hadoop-Cluster bereit. (Der Cluster muss weder geändert noch neu gestartet werden.)

   > [!div class="mx-imgBorder"]
   > ![Erstellen einer LiveData Migrator-Instanz](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

   > [!NOTE]
   > WANdisco LiveData Migrator for Azure ermöglicht das Erstellen eines Hadoop-Testclusters.

3. Konfigurieren Sie ggf. Kerberos-Details.

4. Definieren Sie das Speicherkonto mit aktiviertem Azure Data Lake Storage Gen2.

   > [!div class="mx-imgBorder"]
   > ![Erstellen eines LiveData Migrator-Ziels](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

5. Definieren Sie den Speicherort der Daten, die Sie migrieren möchten. Beispiel: `/user/hive/warehouse`.

   > [!div class="mx-imgBorder"]
   > ![Erstellen einer LiveData Migrator-Migration](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

6. Starten der Migration

Überwachen Sie den Migrationsfortschritt mithilfe von Azure-Standardtools wie der Azure CLI und dem Azure-Portal.

Ausführlichere Anweisungen finden Sie in den [Anleitungsvideos für LiveData Migrator for Azure](https://fast.wistia.com/embed/channel/qg51p8erky).

## <a name="bidirectionally-replicate-data-under-active-change-with-livedata-plane-for-azure"></a>Bidirektionales Replizieren aktiv geänderter Daten mit LiveData Plane for Azure

Der zweite Dienst von LiveData Platform ist [LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure). LiveData Plane verwendet das Koordinationsmodul von WANdisco, um Daten in zahlreichen lokalen Hadoop-Clustern und in Azure Storage konsistent zu halten. Hierzu werden Datenänderungen intelligent auf alle Systeme angewendet, um Datenkonflikte an verschiedenen Verwendungspunkten zu verhindern.

> [!div class="mx-imgBorder"]
> ![LiveData Plane for Azure Architecture](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Mit LiveData Plane for Azure können Sie Ihre Daten nach der ersten Migration konsistent halten:

1. Stellen Sie LiveData Plane for Azure lokal und in Azure bereit. Verwenden Sie dazu zunächst das Azure-Portal. Es sind keine Änderungen an Anwendungen erforderlich.

2. Konfigurieren Sie Replikationsregeln, die die Datenspeicherorte abdecken, die konsistent bleiben sollen. Beispiel: `/user/contoso/sales/region/WA`.

3. Führen Sie Anwendungen aus, die auf Daten an den Speicherorten zugreifen und diese ändern.

LiveData Plane for Azure repliziert Datenänderungen konsistent in allen Umgebungen, ohne dass sich dies signifikant auf den Clusterbetrieb oder die Anwendungsleistung auswirkt.

## <a name="test-drive-or-trial"></a>Testlauf oder Testversion

Auf der [Marketplace-Seite von LiveData Platform for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview) stehen zwei Optionen zur Verfügung:

- Über die Schaltfläche **Jetzt Herunterladen** wird der Dienst in Ihrem Abonnement gestartet. Anschließend können Sie Ihren eigenen Hadoop-Cluster oder den WANdisco-Testcluster verwenden.

- Klicken Sie auf **Testlaufwerk**, um LiveData Migrator for Azure in einer für Sie vorkonfigurierten und gehosteten Umgebung zu testen. So können Sie LiveData Migrator for Azure kostenlos und ganz ohne Risiko für Ihre Daten testen, bevor Sie die Lösung Ihrem Abonnement hinzufügen.

Das [Demovideo für die Testversion](https://fast.wistia.net/embed/channel/qg51p8erky?wchannelid=qg51p8erky&wmediaid=ute6gsc60w) zeigt die Testversion in Aktion.

## <a name="next-steps"></a>Nächste Schritte

- [Planen und Erstellen einer Migration in LiveData Migrator for Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma)

## <a name="see-also"></a>Weitere Informationen

- [LiveData Migrator for Azure auf Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=Overview)

- [LiveData Migrator for Azure – Pläne + Preise](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldma?tab=PlansAndPricee)

- [LiveData Platform for Azure – Häufig gestellte Fragen](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [LiveData Platform for Azure – Bekannte Probleme](https://docs.wandisco.com/live-data-platform/docs/known-issues/)
