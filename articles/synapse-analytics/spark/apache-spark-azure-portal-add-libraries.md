---
title: Paketverwaltung
description: Erfahren Sie, wie Sie Bibliotheken hinzufügen und verwalten, die von Apache Spark in Azure Synapse Analytics verwendet werden.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/01/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 4ad54fe95bc646d7ed3c96ad901ae2ba1337ab7b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251336"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Verwalten von Bibliotheken für Apache Spark in Azure Synapse Analytics
Bibliotheken stellen wiederverwendbaren Code bereit, den Sie in Ihre Programme oder Projekte einschließen können. 

Möglicherweise müssen Sie aus verschiedenen Gründen Ihre serverlose Apache Spark-Poolumgebung aktualisieren. Beispielsweise stellen Sie Folgendes fest:
- Für eine Ihrer Kernabhängigkeiten wurde eine neue Version veröffentlicht.
- Sie benötigen ein zusätzliches Paket zum Trainieren Ihres Machine Learning-Modells oder zum Vorbereiten Ihrer Daten.
- Sie haben ein besseres Paket gefunden und benötigen das ältere Paket nicht mehr.
- Ihr Team hat ein benutzerdefiniertes Paket erstellt, das Sie in Ihrem Apache Spark-Pool zur Verfügung stellen müssen.

Wenn Sie Code eines Drittanbieters oder lokal erstellten Code für Ihre Anwendungen verfügbar machen möchten, können Sie eine Bibliothek in einem Ihrer serverlosen Apache Spark-Pools oder in einer Notebook-Sitzung installieren.
  
## <a name="default-installation"></a>Standardinstallation
Apache Spark in Azure Synapse Analytics verfügt über eine vollständige Anacondas-Installation und zusätzliche Bibliotheken. Die vollständige Liste der Bibliotheken finden Sie unter [Apache Spark-Versionsunterstützung](apache-spark-version-support.md). 

Wenn eine Spark-Instanz gestartet wird, werden diese Bibliotheken automatisch eingeschlossen. Weitere Pakete können auf der Ebene des Spark-Pools oder der Sitzung hinzugefügt werden.

## <a name="workspace-packages"></a>Arbeitsbereichspakete
Wenn Sie benutzerdefinierte Anwendungen oder Modelle entwickeln, erstellt Ihr Team möglicherweise verschiedene Codeartefakte wie Wheel- oder JAR-Dateien zum Packen Ihres Codes. 

In Synapse können Arbeitsbereichspakete benutzerdefinierte oder private Wheel- oder JAR-Dateien sein. Sie können diese Pakete in Ihren Arbeitsbereich hochladen und später einem bestimmten Spark-Pool zuweisen. Nach der Zuweisung werden diese Arbeitsbereichspakete automatisch in allen Spark-Poolsitzungen installiert.

Weitere Informationen zum Verwalten von Arbeitsbereichsbibliotheken finden Sie in den folgenden Schrittanleitungen:

- [Python-Arbeitsbereichspakete:](./apache-spark-manage-python-packages.md#install-wheel-files) Laden Sie Python-Wheel-Dateien als Arbeitsbereichspaket hoch, und fügen Sie diese Pakete später bestimmten serverlosen Apache Spark-Pools hinzu.
- [Scala/Java-Arbeitsbereichspakete:](./apache-spark-manage-scala-packages.md#workspace-packages) Laden Sie Scala- und Java-JAR-Dateien als Arbeitsbereichspaket hoch, und fügen Sie diese Pakete später bestimmten serverlosen Apache Spark-Pools hinzu.

## <a name="pool-packages"></a>Poolpakete
In einigen Fällen möchten Sie möglicherweise den Satz von Paketen standardisieren, die für einen bestimmten Apache Spark-Pool verwendet werden. Diese Standardisierung kann nützlich sein, wenn dieselben Pakete häufig von mehreren Personen Ihres Teams installiert werden. 

Mithilfe der Verwaltungsfunktionen des Azure Synapse Analytics-Pools können Sie den Standardsatz von Bibliotheken konfigurieren, die in einem bestimmten serverlosen Apache Spark-Pool installiert werden sollen. Diese Bibliotheken werden zusätzlich zur [Basisruntime](./apache-spark-version-support.md) installiert. 

Derzeit wird die Poolverwaltung nur für Python unterstützt. Bei Python nutzen Synapse-Spark-Pools Conda, um Python-Paketabhängigkeiten zu installieren und zu verwalten. Wenn Sie Ihre Bibliotheken auf Poolebene angeben, können Sie jetzt eine Datei „requirements.txt“ oder „environment.yml“ bereitstellen. Diese Umgebungskonfigurationsdatei wird jedes Mal verwendet, wenn eine Spark-Instanz aus diesem Spark-Pool erstellt wird. 

Weitere Informationen zu diesen Funktionen finden Sie in der Dokumentation zur [Verwaltung von Python-Pools](./apache-spark-manage-python-packages.md#pool-libraries).

> [!IMPORTANT]
> - Wenn das Paket, das Sie installieren, groß ist oder seine Installation lange dauert, wirkt sich dies auf die Startzeit der Spark-Instanz aus.
> - Das Ändern der Version von PySpark, Python, Scala/Java, .NET oder Spark wird nicht unterstützt.
> - Das Installieren von Paketen aus PypI wird in DEP-fähigen Arbeitsbereichen nicht unterstützt.

## <a name="session-scoped-packages"></a>Sitzungsbezogene Pakete
Sie werden bei der interaktiven Datenanalyse oder beim maschinellen Lernen häufig feststellen, dass Sie neuere Pakete ausprobieren möchten. Oder Sie benötigen Pakete, die in Ihrem Apache Spark-Pool noch nicht verfügbar sind. Anstatt die Poolkonfiguration zu aktualisieren, können Benutzer jetzt sitzungsbezogene Pakete verwenden, um Sitzungsabhängigkeiten hinzuzufügen, zu verwalten und zu aktualisieren.

Mit sitzungsbezogenen Paketen können Benutzer zu Beginn ihrer Sitzung Paketabhängigkeiten definieren. Wenn Sie ein sitzungsbezogenes Paket installieren, kann nur die aktuelle Sitzung auf die angegebenen Pakete zugreifen. Daher wirken sich diese sitzungsbezogenen Pakete nicht auf andere Sitzungen oder Aufträge aus, die denselben Apache Spark-Pool verwenden. Außerdem werden diese Bibliotheken zusätzlich zu den Paketen der Basisruntime und der Poolebene installiert. 

Weitere Informationen zum Verwalten von sitzungsbezogenen Paketen finden Sie in den folgenden Schrittanleitungen:

- [Python-Sitzungspakete:](./apache-spark-manage-python-packages.md) Stellen Sie zu Beginn einer Sitzung eine Conda-Datei namens *environment.yml* bereit, um zusätzliche Python-Pakete aus beliebten Repositorys zu installieren. 
- [Scala/Java-Sitzungspakete:](./apache-spark-manage-scala-packages.md) Geben Sie zu Beginn der Sitzung mithilfe von `%%configure` eine Liste mit JAR-Dateien an, die installiert werden sollen.

## <a name="next-steps"></a>Nächste Schritte
- Anzeigen der Standardbibliotheken: [Versionsunterstützung für Apache Spark](apache-spark-version-support.md)
