---
title: Anmerkungen zu dieser Version von Azure HDInsight
description: Die neuesten Versionshinweise für Azure HDInsight. Rufen Sie Tipps zur Entwicklung und Details für Hadoop, Spark, Hive und mehr ab.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: cf726033a90e256a2d41a3249e237f5eb91aee84
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346318"
---
# <a name="azure-hdinsight-release-notes"></a>Versionshinweise zu Azure HDInsight

Dieser Artikel enthält Informationen zu den **neuesten** Versionsupdates für Azure HDInsight. Informationen zu neueren Versionen finden Sie unter [Versionshinweise zu HDInsight – Archiv](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Zusammenfassung

Azure HDInsight ist unter Enterprisekunden einer der beliebtesten Dienste für Open-Source-Analysen in Azure.

Wenn Sie die Anmerkungen zu dieser Version abonnieren möchten, sehen Sie sich die Releases auf [diesem GitHub-Repository](https://github.com/hdinsight/release-notes/releases) an.

## <a name="release-date-07272021"></a>Veröffentlichungsdatum: 27.07.2021

Diese Version gilt sowohl für HDInsight 3.6 als auch für HDInsight 4.0. Das HDInsight-Release wird über mehrere Tage für alle Regionen verfügbar gemacht. Das hier angegebene Veröffentlichungsdatum entspricht dem Veröffentlichungsdatum in der ersten Region. Es kann sein, dass die unten angegebenen Änderungen bei Ihnen erst ein paar Tage später verfügbar werden.

Die Betriebssystemversionen für dieses Release sind:
- HDInsight 3.6: Ubuntu 16.04.7 LTS
- HDInsight 4.0: Ubuntu 18.04.5 LTS

## <a name="new-features"></a>Neue Funktionen
### <a name="new-azure-monitor-integration-experience-preview"></a>Neue Azure Monitor-Integrationserfahrung (Vorschauversion)
Die neue Azure Monitor-Integrationserfahrung ist mit diesem Release in den Regionen „USA, Osten“ und „Europa, Westen“ in der Vorschauphase. Erfahren Sie [hier](./log-analytics-migration.md#migrate-to-the-new-azure-monitor-integration) weitere Details über die neue Azure Monitor-Erfahrung.

## <a name="deprecation"></a>Eingestellte Unterstützung
### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Basic-Support für HDInsight 3.6 ab dem 1. Juli 2021
Ab dem 1. Juli 2021 bietet Microsoft [Basic-Support](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) für bestimmte HDInsight 3.6-Clustertypen an. Der Basic-Support-Plan wird bis 3. April 2022 verfügbar sein. Sie werden ab dem 1. Juli 2021 automatisch für den Basic-Support registriert. Es ist keine Aktion erforderlich, damit Sie sich anmelden können. In [unserer Dokumentation](hdinsight-36-component-versioning.md) finden Sie Informationen dazu, welche Clustertypen unter Basic-Support enthalten sind. 

Es wird nicht empfohlen, neue Lösungen in HDInsight 3.6 zu entwickeln, frieren Sie Änderungen an vorhandenen 3.6-Umgebungen ein. Es wird empfohlen, die [Cluster in HDInsight 4.0 zu migrieren](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Erfahren Sie mehr über die Neuerungen in [HDInsight 4.0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="behavior-changes"></a>Verhaltensänderungen
### <a name="hdinsight-interactive-query-only-supports-schedule-based-autoscale"></a>HDInsight Interactive Query unterstützt nur die zeitplanbasierte Autoskalierung
Aufgrund der zunehmenden Reife und Vielfalt von Kundenszenarien haben wir einige Einschränkungen bei der lastbasierten Autoskalierung von Interactive Query (LLAP) festgestellt. Diese Einschränkungen werden durch die Art der LLAP-Abfragedynamik, Probleme bei der Genauigkeit der Vorhersage der zukünftigen Auslastung und Probleme bei der Neuverteilung von Aufgaben des LLAP-Schedulers verursacht. Aufgrund dieser Einschränkungen stellen Benutzer u. U. fest, dass Abfragen in LLAP-Clustern langsamer ausgeführt werden, wenn die Autoskalierung aktiviert ist. Die Auswirkungen auf die Leistung können die Kostenvorteile der Autoskalierung aufwiegen.

Ab Juli 2021 wird für die Interactive Query-Workload in HDInsight nur die zeitplanbasierte Autoskalierung unterstützt. Sie können die lastenbasierte Autoskalierung für neue Interactive Query-Cluster nicht mehr aktivieren. Vorhandene aktive Cluster können weiterhin mit den oben beschriebenen bekannten Einschränkungen ausgeführt werden. 

Microsoft empfiehlt Ihnen, zu einer zeitplanbasierten Autoskalierung für LLAP zu wechseln.  Sie können das aktuelle Nutzungsmuster Ihres Clusters über das Grafana Hive-Dashboard analysieren. Weitere Informationen finden Sie unter [Automatische Skalierung von Azure HDInsight-Clustern](hdinsight-autoscale-clusters.md). 

## <a name="upcoming-changes"></a>Bevorstehende Änderungen
Die folgenden Änderungen werden in kommenden Versionen durchgeführt.

### <a name="built-in-llap-component-in-esp-spark-cluster-will-be-removed"></a>Die integrierte LLAP-Komponente im ESP Spark-Cluster wird entfernt.
HDInsight 4.0 ESP Spark-Cluster verfügt über integrierte LLAP-Komponenten, die auf beiden Hauptknoten ausgeführt werden. Die LLAP-Komponenten im ESP Spark-Cluster wurden ursprünglich für HDInsight 3.6 ESP Spark hinzugefügt, umfassen aber keinen echten Benutzerfall für HDInsight 4.0 ESP Spark. Im nächsten Release, das für September 2021 geplant ist, entfernt HDInsight die integrierte LLAP-Komponente aus dem HDInsight 4.0 ESP Spark-Cluster. Diese Änderung hilft, die Workload des Hauptknotens abzuladen und Verwechslungen zwischen den Clustertypen ESP Spark und ESP Interactive Hive zu vermeiden.

## <a name="new-region"></a>Neue Region
- USA, Westen 3
- Jio Indien, Westen
- Australien, Mitte

## <a name="component-version-change"></a>Änderung der Komponentenversion
Die folgende Komponentenversion wurde mit diesem Release geändert:
- ORC-Version von 1.5.1 auf 1.5.9

Die aktuellen Komponentenversionen für HDInsight 4.0 und HDInsight 3.6 finden Sie in [dieser Dokumentation](./hdinsight-component-versioning.md).

## <a name="back-ported-jiras"></a>Zurückportierte JIRAs
Dies sind die zurückportierten Apache-JIRAs für dieses Release:

| Betroffenes Feature    |   Apache JIRA                                                      |
|---------------------|--------------------------------------------------------------------|
| Datum/Zeitstempel    | [HIVE-25104](https://issues.apache.org/jira/browse/HIVE-25104)     |
|                     | [HIVE-24074](https://issues.apache.org/jira/browse/HIVE-24074)     |
|                     | [HIVE-22840](https://issues.apache.org/jira/browse/HIVE-22840)     |
|                     | [HIVE-22589](https://issues.apache.org/jira/browse/HIVE-22589)     |
|                     | [HIVE-22405](https://issues.apache.org/jira/browse/HIVE-22405)     |
|                     | [HIVE-21729](https://issues.apache.org/jira/browse/HIVE-21729)     |
|                     | [HIVE-21291](https://issues.apache.org/jira/browse/HIVE-21291)     |
|                     | [HIVE-21290](https://issues.apache.org/jira/browse/HIVE-21290)     |
| UDF                 | [HIVE-25268](https://issues.apache.org/jira/browse/HIVE-25268)     |
|                     | [HIVE-25093](https://issues.apache.org/jira/browse/HIVE-25093)     |
|                     | [HIVE-22099](https://issues.apache.org/jira/browse/HIVE-22099)     |
|                     | [HIVE-24113](https://issues.apache.org/jira/browse/HIVE-24113)     |
|                     | [HIVE-22170](https://issues.apache.org/jira/browse/HIVE-22170)     |
|                     | [HIVE-22331](https://issues.apache.org/jira/browse/HIVE-22331)     |
| ORC                 | [HIVE-21991](https://issues.apache.org/jira/browse/HIVE-21991)     |
|                     | [HIVE-21815](https://issues.apache.org/jira/browse/HIVE-21815)     |
|                     | [HIVE-21862](https://issues.apache.org/jira/browse/HIVE-21862)     |
| Tabellenschema        | [HIVE-20437](https://issues.apache.org/jira/browse/HIVE-20437)     |
|                     | [HIVE-22941](https://issues.apache.org/jira/browse/HIVE-22941)     |
|                     | [HIVE-21784](https://issues.apache.org/jira/browse/HIVE-21784)     |
|                     | [HIVE-21714](https://issues.apache.org/jira/browse/HIVE-21714)     |
|                     | [HIVE-18702](https://issues.apache.org/jira/browse/HIVE-18702)     |
|                     | [HIVE-21799](https://issues.apache.org/jira/browse/HIVE-21799)     |
|                     | [HIVE-21296](https://issues.apache.org/jira/browse/HIVE-21296)     |
| Arbeitsauslastungsverwaltung | [HIVE-24201](https://issues.apache.org/jira/browse/HIVE-24201)     |
| Komprimierung          | [HIVE-24882](https://issues.apache.org/jira/browse/HIVE-24882)     |
|                     | [HIVE-23058](https://issues.apache.org/jira/browse/HIVE-23058)     |
|                     | [HIVE-23046](https://issues.apache.org/jira/browse/HIVE-23046)     |
| Materialisierte Sicht   | [HIVE-22566](https://issues.apache.org/jira/browse/HIVE-22566)     |