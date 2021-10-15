---
title: Apache Hadoop-Komponenten und -Versionen – Azure HDInsight
description: Lernen Sie die Apache Hadoop-Komponenten und -Versionen in Azure HDInsight kennen.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 16b9e7f2b790e6f4e758f07c4f352b30357ae32b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129277307"
---
# <a name="azure-hdinsight-versions"></a>Versionen von Azure HDInsight

HDInsight bündelt Apache Hadoop-Umgebungskomponenten und die HDInsight-Platform in einem Paket, das in einem Cluster bereitgestellt wird. Weitere Informationen finden Sie unter [How versioning works in HDInsight](hdinsight-overview-versioning.md) (Funktionsweise der Versionsverwaltung in HDInsight, in englischer Sprache).

## <a name="supported-hdinsight-versions"></a>Unterstützte HDInsight-Versionen

Die Tabelle enthält die im Azure-Portal und über andere Bereitstellungsmethoden, z. B. PowerShell, die CLI und das .NET SDK, verfügbaren Versionen von HDInsight.

| HDInsight-Version | Betriebssystem des virtuellen Computers | Veröffentlichungsdatum| Supporttyp | Supportablaufdatum | Deaktivierungstermin | Hochverfügbarkeit |
| --- | --- | --- | --- | --- | --- | ---|
| [HDInsight 4.0](hdinsight-40-component-versioning.md) |Ubuntu 18.0.4 LTS |24. September 2018 | [Standard](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | | |Ja |
| [HDInsight 3.6](hdinsight-36-component-versioning.md) |Ubuntu 16.0.4 LTS |4\. April 2017      | [Grundlegend](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) | Der Standard-Support ist am 30. Juni 2021 für alle Clustertypen abgelaufen.<br> Der Basic-Support läuft am 3. April 2022 aus. Weitere Informationen zum Clustertyp finden Sie unter [HDInsight 3.6-Komponentenversionen](hdinsight-36-component-versioning.md). |4\. April 2022 |Ja |

**Supportablauf** bedeutet, dass Microsoft für die jeweilige HDInsight-Version keinen Support mehr bereitstellt. Die Version ist zudem möglicherweise nicht mehr über das Azure-Portal für die Clustererstellung verfügbar.

**Deaktivierung** bedeutet, dass vorhandene Cluster einer HDInsight-Version weiterhin unverändert ausgeführt werden. Neue Cluster dieser Version können jedoch nicht erstellt werden – auch nicht mithilfe der CLI und SDKs. Auch andere Funktionen auf Steuerungsebene, z. B. die manuelle und automatische Skalierung, können nach dem Datum der Deaktivierung möglicherweise nicht mehr verwendet werden. Für deaktivierte Versionen ist kein Support verfügbar.

## <a name="support-options-for-hdinsight-versions"></a>Supportoptionen für HDInsight-Versionen

Support ist als ein Zeitraum definiert, in dem eine HDInsight-Version vom Microsoft-Kundendienst und -Support unterstützt wird. HDInsight bietet zwei Arten von Support: 
- **Standard-Support**
- **Basic-Support**

### <a name="standard-support"></a>Standard-Support

Im Rahmen des Standard-Supports werden Updates und Unterstützung für HDInsight-Cluster bereitstellt. Microsoft empfiehlt, Lösungen mit der neuesten, vollständig unterstützten Version zu entwickeln. 

Der Standard-Support umfasst Folgendes:
- Möglichkeit zum Erstellen von Supportanfragen in HDInsight 4.0-Clustern
- Unterstützung bei der Problembehandlung für Lösungen, die auf 4.0-Clustern basieren 
- Anfordern des Neustarts von Diensten oder Knoten
- Analysieren der Grundursache bei Supportanfragen
- Analysieren der Grundursache oder Fehlerbehebungen zur Verbesserung der Auftrags- oder Abfrageleistung
- Analysieren der Grundursache oder Fehlerbehebungen zur Verbesserung der von der Kundschaft initiierten Änderungen (z. B. Ändern von Dienstkonfigurationen oder Probleme aufgrund benutzerdefinierter Skriptaktionen)
- Produktupdates für kritische Sicherheitsfixes bis zur Versionseinstellung
- Eingegrenzte Produktupdates für den HDInsight-Ressourcenanbieter
- Selektive Fixes oder Änderungen an HDInsight 4.0-Images oder OSS-Komponentenversionen (Open-Source-Software)

### <a name="basic-support"></a>Basic-Support

Der Basic-Support bietet eingeschränkte Wartung für den HDInsight-Ressourcenanbieter. HDInsight-Images und OSS-Komponenten (Open-Source-Software) werden nicht gewartet. Nur wichtige Sicherheitsfixes werden in HDInsight-Clustern gepatcht. 

Der Basic-Support umfasst Folgendes:
- Fortgesetzte Verwendung vorhandener HDInsight 3.6-Cluster
- Möglichkeit der Erstellung neuer 3.6-Cluster für bestehende HDInsight 3.6-Kundschaft
- Möglichkeit zum Hoch- und Herunterskalieren von HDInsight 3.6-Clustern per Autoskalierung oder manueller Skalierung
- Eingegrenzte Produktupdates für den HDInsight-Ressourcenanbieter
- Produktupdates für kritische Sicherheitsfixes bis zur Versionseinstellung
- Möglichkeit zum Erstellen von Supportanfragen in HDInsight 3.6-Clustern
- Anfordern des Neustarts von Diensten oder Knoten

Folgendes ist beim Basic-Support nicht enthalten:
- Fixes oder Änderungen an HDInsight 3.6-Images oder OSS-Komponentenversionen (Open-Source-Software)
- Unterstützung bei der Problembehandlung für Lösungen, die auf 3.6-Clustern basieren 
- Hinzufügen neuer Features oder Funktionen
- Unterstützung für Ratschläge oder Ad-hoc-Abfragen
- Analysieren der Grundursache bei Supportanfragen
- Analysieren der Grundursache oder Fehlerbehebungen zur Verbesserung der Auftrags- oder Abfrageleistung
- Analysieren der Grundursache oder Fehlerbehebungen zur Verbesserung der von der Kundschaft initiierten Änderungen (z. B. Ändern von Dienstkonfigurationen oder Probleme aufgrund benutzerdefinierter Skriptaktionen)

Microsoft empfiehlt nicht das Erstellen von Analysepipelines oder -lösungen in Clustern mit Basic-Support. Es wird empfohlen, vorhandene Cluster zur neuesten, vollständig unterstützten Version zu migrieren. 

## <a name="release-notes"></a>Versionshinweise

In den [HDInsight-Versionshinweisen](hdinsight-release-notes.md) finden Sie zusätzliche Anmerkungen zu den aktuellen Versionen von HDInsight.

## <a name="versioning-considerations"></a>Überlegungen zur Versionsverwaltung
- Nachdem ein Cluster mit einem Image bereitgestellt wurde, wird dieser Cluster nicht automatisch auf eine neuere Imageversion aktualisiert. Beim Erstellen neuer Cluster wird die aktuelle Imageversion bereitgestellt.
- Kunden sollten überprüfen, ob Anwendungen bei Verwendung einer neuen HDInsight-Version ordnungsgemäß ausgeführt werden.
- HDInsight behält sich das Recht vor, die Standardversion ohne vorherige Ankündigung zu ändern. Wenn eine Versionsabhängigkeit vorliegt, geben Sie beim Erstellen der Cluster die HDInsight-Version an.
- HDInsight deaktiviert möglicherweise eine OSS-Komponentenversion vor dem Deaktivieren der HDInsight-Version.

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten von Clustern in HDInsight mit Apache Hadoop, Spark, Kafka usw.](hdinsight-hadoop-provision-linux-clusters.md)
- [Enterprise-Sicherheitspaket](./enterprise-security-package.md)
- [Verwenden eines Windows-Computers mit Apache Hadoop in HDInsight](hdinsight-hadoop-windows-tools.md)
