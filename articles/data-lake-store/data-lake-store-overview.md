---
title: Was ist Azure Data Lake Storage Gen1? | Microsoft-Dokumentation
description: Übersicht über Data Lake Storage Gen1 (zuvor als Azure Data Lake Store bezeichnet) und die Vorteile, die es im Vergleich zu anderen Datenspeichern bietet
author: normesta
ms.service: data-lake-store
ms.topic: overview
ms.date: 04/17/2019
ms.author: normesta
ms.openlocfilehash: f105815e6c20777060c1350b9f12fbb160dbc710
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128655579"
---
# <a name="what-is-azure-data-lake-storage-gen1"></a>Was ist Azure Data Lake Storage Gen1?

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Azure Data Lake Storage Gen1 ist ein unternehmensweites Repository mit Hyperskalierung für Big Data-Analyseworkloads. Azure Data Lake bietet Ihnen die Möglichkeit, Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit zur Durchführung operativer und explorativer Analysen an einem einzigen Ort zu erfassen.

Auf Data Lake Storage Gen1 kann über Hadoop (verfügbar mit HDInsight-Cluster) mithilfe der WebHDFS-kompatiblen REST-APIs zugegriffen werden. Dieser Speichertyp wurde speziell für Analysen der gespeicherten Daten konzipiert und ist für eine hohe Leistung in Datenanalyseszenarien optimiert. Data Lake Storage Gen1 bietet alle Funktionen für Unternehmen: Sicherheit, Verwaltbarkeit, Skalierbarkeit, Zuverlässigkeit und Verfügbarkeit.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

## <a name="key-capabilities"></a>Wichtige Funktionen

Data Lake Storage Gen1 bietet u.a. die folgenden wichtigen Funktionen.

### <a name="built-for-hadoop"></a>Konzipiert für Hadoop

Bei Data Lake Storage Gen1 handelt es sich um ein Apache Hadoop-Dateisystem, das mit dem HDFS (Hadoop Distributed File System) kompatibel ist und mit dem Hadoop-Ökosystem eingesetzt werden kann. Ihre vorhandenen HDInsight-Anwendungen oder -Dienste, die die WebHDFS-API verwenden, können problemlos in Data Lake Storage Gen1 integriert werden. Data Lake Storage Gen1 macht auch eine WebHDFS-kompatible REST-Schnittstelle für Anwendungen verfügbar.

Sie können in Data Lake Storage Gen1 gespeicherte Daten mühelos mit Hadoop-Analyseframeworks wie MapReduce oder Hive analysieren. Sie können Azure HDInsight-Cluster bereitstellen und für den direkten Zugriff auf in Data Lake Storage Gen1 gespeicherte Daten konfigurieren.

### <a name="unlimited-storage-petabyte-files"></a>Unbegrenzter Speicher, Dateigrößen bis in den Petabytebereich

Data Lake Storage Gen1 bietet unbegrenzten Speicher und kann unterschiedlichste Daten für Analysezwecke speichern. Es gibt keinerlei Einschränkungen für Kontogrößen, Dateigrößen oder die Menge an Daten, die in einem Data Lake gespeichert werden kann. Einzelne Dateien können von Kilobyte bis Petabyte groß sein. Daten werden dauerhaft gespeichert, indem mehrere Kopien angefertigt werden. Die Daten können für unbegrenzte Zeit im Data Lake gespeichert werden.

### <a name="performance-tuned-for-big-data-analytics"></a>Leistungsoptimiert für Big Data-Analysen

Data Lake Storage Gen1 ist für die Ausführung großer Analysesysteme ausgelegt, die zum Abfragen und Analysieren großer Datenmengen einen enormen Durchsatz erfordern. Der Data Lake verteilt Teile einer Datei auf mehrere einzelne Speicherserver. Dies verbessert den Lesedurchsatz, wenn die Datei zum Ausführen von Datenanalysen parallel gelesen wird.

### <a name="enterprise-ready-highly-available-and-secure"></a>Bereit für den Einsatz in Unternehmen: Hoch verfügbar und sicher

Data Lake Storage Gen1 bietet Verfügbarkeit und Zuverlässigkeit nach Branchenstandard. Ihre Datenassets werden dauerhaft gespeichert, indem zum Schutz vor unerwarteten Fehlern redundante Kopien erstellt werden.

Data Lake Storage Gen1 bietet außerdem Sicherheit auf Unternehmensniveau für die gespeicherten Daten. Weitere Informationen finden Sie unter [Schützen von Daten in Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="all-data"></a>Alle Daten

Data Lake Storage Gen1 kann beliebige Daten ohne vorherige Umwandlung im nativen Format speichern. Bei Data Lake Storage Gen1 ist es nicht erforderlich, vor dem Laden der Daten ein Schema zu definieren. Die Interpretation der Daten und Definition eines Schemas erfolgt zum Zeitpunkt der Analyse durch die einzelnen Analyseframeworks. Dank der Möglichkeit, Dateien beliebiger Größe und Formate zu speichern, kann Data Lake Storage Gen1 strukturierte, teilweise strukturierte und unstrukturierte Daten verwalten.

Data Lake Storage Gen1-Container für Daten sind im Wesentlichen Ordner und Dateien. Sie arbeiten mit den gespeicherten Daten über SDKs, das Azure-Portal und Azure PowerShell. Wenn Sie Ihre Daten unter Verwendung dieser Schnittstellen im Speicher ablegen und die entsprechenden Container verwenden, können Sie jeden Datentyp speichern. Data Lake Storage Gen1 führt keine spezielle Verarbeitung von Daten basierend auf dem Typ der gespeicherten Daten durch.

## <a name="securing-data"></a><a name="DataLakeStoreSecurity"></a>Absichern von Daten

Data Lake Storage Gen1 verwendet Azure Active Directory (Azure AD) zur Authentifizierung und Zugriffssteuerungslisten (ACLs) zum Verwalten des Zugriffs auf Ihre Daten.

| Funktion | BESCHREIBUNG |
| --- | --- |
| Authentifizierung |Zur Identitäts- und Zugriffsverwaltung für alle in Data Lake Storage Gen1 gespeicherten Daten ist Data Lake Storage Gen1 in Azure AD integriert. Durch diese Integration nutzt Data Lake Storage Gen1 sämtliche Azure AD-Features, z. B. mehrstufige Authentifizierung, bedingten Zugriff, rollenbasierte Zugriffssteuerung in Azure, Überwachung der Anwendungsnutzung, Sicherheitsüberwachung und -warnungen usw. Data Lake Storage Gen1 unterstützt das OAuth 2.0-Protokoll für die Authentifizierung mit der REST-Schnittstelle. Weitere Informationen finden Sie unter [Authentifizierung bei Azure Data Lake Storage Gen1](data-lakes-store-authentication-using-azure-active-directory.md).|
| Zugriffssteuerung |Data Lake Storage Gen1 ermöglicht eine Zugriffssteuerung durch die Unterstützung von POSIX-Berechtigungen, die vom WebHDFS-Protokoll verfügbar gemacht werden. ACLs können für Stammordner, Unterordner und einzelne Dateien aktiviert werden. Weitere Informationen zur Funktionsweise von ACLs im Kontext von Data Lake Storage Gen1 finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](data-lake-store-access-control.md). |
| Verschlüsselung |Data Lake Storage Gen1 bietet auch eine Verschlüsselung für Daten, die im Konto gespeichert sind. Beim Erstellen eines Data Lake Storage Gen1-Kontos geben Sie die Einstellungen für die Verschlüsselung an. Sie können auswählen, ob Ihre Daten verschlüsselt werden sollen. Weitere Informationen finden Sie unter [Verschlüsselung von Daten in Azure Data Lake Storage Gen1](data-lake-store-encryption.md). Anweisungen zur Bereitstellung einer verschlüsselungsbezogenen Konfiguration finden Sie unter [Erste Schritte mit Data Lake Storage Gen1 über das Azure-Portal](data-lake-store-get-started-portal.md). |

Anweisungen zum Schutz von Daten in Data Lake Storage Gen1 finden Sie unter [Schutz von Daten in Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="application-compatibility"></a>Anwendungskompatibilität

Data Lake Storage Gen1 ist mit den meisten Open Source-Komponenten im Hadoop-Ökosystem kompatibel. Außerdem ist eine problemlose Integration in andere Azure-Dienste möglich. Weitere Informationen zur Verwendung von Data Lake Storage Gen1 mit Open-Source-Komponenten und anderen Azure-Diensten finden Sie unter den folgenden Links:

- Eine Liste der mit Data Lake Storage Gen1 interoperablen Open Source-Anwendungen finden Sie unter [Open Source-Big Data-Anwendungen, die mit Azure Data Lake Storage Gen1 verwendet werden können](data-lake-store-compatible-oss-other-applications.md).
- Informationen zur Verwendung von Data Lake Storage Gen1 mit anderen Azure-Diensten, um ein breiteres Spektrum von Szenarien zu ermöglichen, finden Sie unter [Integration in andere Azure-Dienste](data-lake-store-integrate-with-other-services.md).
- Informationen dazu, wie Data Lake Storage Gen1 beispielsweise zum Erfassen, Verarbeiten, Herunterladen und Visualisieren von Daten verwendet werden kann, finden Sie unter [Verwenden von Azure Data Lake Storage Gen1 zur Erfüllung von Big Data-Anforderungen](data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system"></a>Data Lake Storage Gen1-Dateisystem

In Hadoop-Umgebungen (verfügbar mit HDInsight-Clustern) kann über das Dateisystem AzureDataLakeFilesystem (adl://) auf Data Lake Storage Gen1 zugegriffen werden. Anwendungen und Dienste, die adl:// verwenden, können weitere Leistungsoptimierungen nutzen, die gegenwärtig nicht in WebHDFS verfügbar sind. Daher bietet Ihnen Data Lake Storage Gen1 die Flexibilität, entweder mit adl:// (empfohlene Option) die optimale Leistung zu nutzen oder vorhandenen Code beizubehalten, indem Sie die WebHDFS-API weiterhin direkt verwenden. Azure HDInsight schöpft die Möglichkeiten des „AzureDataLakeFilesystem“ voll aus, um eine optimale Leistung für Data Lake Storage Gen1 bereitzustellen.

Sie können mithilfe von `adl://<data_lake_storage_gen1_name>.azuredatalakestore.net` auf Ihre Daten in Data Lake Storage Gen1 zugreifen. Weitere Informationen zum Zugriff auf die Daten in Data Lake Storage Gen1 finden Sie unter [Anzeigen der Eigenschaften der gespeicherten Daten](data-lake-store-get-started-portal.md#properties).

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit Data Lake Storage Gen1 unter Verwendung des Azure-Portals](data-lake-store-get-started-portal.md)
- [Erste Schritte mit Data Lake Storage Gen1 mit dem .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Erstellen von HDInsight-Clustern mithilfe von Azure Data Lake Storage Gen1 im Azure-Portal](data-lake-store-hdinsight-hadoop-use-portal.md)