---
title: Migrieren großer Datenbanken zu Azure Database for MySQL mit „mydumper“/„myloader“
description: In diesem Artikel werden zwei allgemeine Verfahren zum Sichern und Wiederherstellen von Datenbanken in Azure-Datenbank für MySQL mit dem Tool „mydumper“/„myloader“ beschrieben.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 06/18/2021
ms.openlocfilehash: e295e967b9164e9ab4744d18f407a18feb32481e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351332"
---
# <a name="migrate-large-databases-to-azure-database-for-mysql-using-mydumpermyloader"></a>Migrieren großer Datenbanken zu Azure Database for MySQL mit „mydumper“/„myloader“

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

Azure Database for MySQL ist ein verwalteter Dienst, mit dem Sie hochverfügbare MySQL-Datenbanken in der Cloud ausführen, verwalten und skalieren können. Um MySQL-Datenbanken, die größer als 1 TB sind, zu Azure Database for MySQL zu migrieren, sollten Sie Communitytools wie [mydumper/myloader](https://centminmod.com/mydumper.html) verwenden, die die folgenden Vorteile bieten:

* Parallelität, um die Migrationszeit zu verkürzen.
* Bessere Leistung, indem aufwändige Routinen für die Zeichensatzkonvertierung vermieden werden.
* Ein Ausgabeformat mit separaten Dateien für Tabellen, Metadaten usw., wodurch das Anzeigen/Analysieren von Daten vereinfacht wird. Konsistenz, indem Momentaufnahmen über alle Threads hinweg verwaltet werden.
* Genaue Primär- und Replikatprotokollpositionen.
* Einfache Verwaltung, da sie Perl Compatible Regular Expressions (PCRE) zum Angeben von Ein- und Ausschlüssen für Datenbanken und Tabellen unterstützen.
* Schema und Daten werden zusammengeführt. Sie müssen nicht separat wie andere logische Migrationstools behandelt werden.

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine MySQL-Datenbank mit „mydumper“/„myloader“ installieren, sichern und wiederherstellen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit dem Migrieren Ihrer MySQL-Datenbank zu SQL Server beginnen, führen Sie folgende Schritte aus:

1. Erstellen Sie einen Server für Azure Database for MySQL über das [Azure-Portal](./flexible-server/quickstart-create-server-portal.md).

2. Erstellen Sie eine Azure-VM unter Linux über das [Azure-Portal](../virtual-machines/linux/quick-create-portal.md) (vorzugsweise Ubuntu).
    > [!Note]
    > Berücksichtigen Sie vor der Installation der Tools die folgenden Punkte:
    >
    > * Wenn es sich um eine lokale Quelle mit einer Verbindung mit hoher Bandbreite zu Azure (mithilfe von ExpressRoute) handelt, können Sie das Tool auf einer Azure-VM installieren.<br> 
    > * Wenn Sie Probleme mit der Bandbreite zwischen Quelle und Ziel haben, sollten Sie „mydumper“ in der Nähe der Quelle und „myloader“ in der Nähe des Zielservers installieren. Sie können **[Azcopy](../storage/common/storage-use-azcopy-v10.md)** verwenden, um die Daten von lokalen oder anderen Cloudlösungen nach Azure zu verschieben.

3. Installieren Sie den mysql-Client, und gehen Sie folgendermaßen vor:

4. 

    * Aktualisieren Sie den Paketindex auf dem virtuellen Azure-Computer unter Linux, indem Sie den folgenden Befehl ausführen:
        ```bash
        $ sudo apt update
        ```
    * Installieren Sie das mysql-Clientpaket, indem Sie den folgenden Befehl ausführen:
        ```bash
        $ sudo apt install mysql-client
        ```

## <a name="install-mydumpermyloader"></a>Installieren von mydumper/myloader

Gehen Sie folgendermaßen vor, um „mydumper“/„myloader“ zu installieren.

1. Laden Sie je nach Betriebssystemverteilung das entsprechende Paket für mydumper/myloader herunter, und führen Sie den folgenden Befehl aus:
2. 
    ```bash
    $ wget https://github.com/maxbube/mydumper/releases/download/v0.10.1/mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Note]
    > $(lsb_release -cs): Hilft bei der Identifizierung Ihrer Distribution.

3. Führen Sie den folgenden Befehl aus, um das DEB-Paket für „mydumper“ zu installieren:

    ```bash
    $ dpkg -i mydumper_0.10.1-2.$(lsb_release -cs)_amd64.deb
    ```

    > [!Tip]
    > Der Befehl, den Sie zum Installieren des Pakets verwenden, unterscheidet sich abhängig von der Linux-Distribution, über die Sie verfügen, da sich die Installationsprogramme unterscheiden. „mydumper“/„myloader“ ist für die folgenden Distributionen verfügbar: Fedora, RedHat, Ubuntu, Debian, CentOS, openSUSE und MacOSX. Weitere Informationen finden Sie unter **[Installieren von mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader)** .

## <a name="create-a-backup-using-mydumper"></a>Erstellen einer Sicherung mit mydumper

* Führen Sie den folgenden Befehl aus, um eine Sicherung mit mydumper zu erstellen:

    ```bash
    $ mydumper --host=<servername> --user=<username> --password=<Password> --outputdir=./backup --rows=100000 --compress --build-empty-files --threads=16 --compress-protocol --trx-consistency-only --ssl  --regex '^(<Db_name>\.)' -L mydumper-logs.txt
    ```

Dieser Befehl verwendet die folgenden Variablen:

* **--host:** Der Host, mit dem eine Verbindung hergestellt werden soll.
* **--user:** Der Benutzername mit den erforderlichen Berechtigungen. 
* **--password:** Das Benutzerkennwort.
* **--rows:** Versuchen Sie, Tabellen in Blöcke mit so vielen Zeilen aufzuteilen.
* **--outputdir:** Das Verzeichnis, in das die Ausgabedateien gespeichert werden.
* **--regex:** Regulärer Ausdruck für den Datenbankabgleich.
* **--trx-consistency-only:** Nur Transaktionskonsistenz.
* **--threads:** Die Anzahl der zu verwendenden Threads, Standardwert ist 4. Es wird empfohlen, einen Wert zu verwenden, der dem Zweifachen der virtuellen Kerne des Computers entspricht.

    >[!Note] 
    >Führen Sie den folgenden Befehl aus, um weitere Informationen zu anderen Optionen zu erhalten, die Sie mit „mydumper“ verwenden können: **mydumper --help**. Weitere Informationen finden Sie in der [Dokumentation zu mydumper\myloader](https://centminmod.com/mydumper.html).<br>
    Um mehrere Datenbanken parallel zu sichern, können Sie die regex-Variable modifizieren, wie im Beispiel gezeigt: **regex ’^(DbName1\.|DbName2\.)**

## <a name="restore-your-database-using-myloader"></a>Wiederherstellen Ihrer Datenbank mithilfe von myloader

* Führen Sie den folgenden Befehl aus, um die Datenbank wiederherzustellen, die Sie mit „mydumper“ gesichert haben:

    ```bash
    $ myloader --host=<servername> --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=500 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

Dieser Befehl verwendet die folgenden Variablen:

* **--host:** Der Host, mit dem eine Verbindung hergestellt werden soll.
* **--user:** Der Benutzername mit den erforderlichen Berechtigungen. 
* **--password:** Das Benutzerkennwort.
* **--directory:** Der Speicherort, an dem die Sicherung gespeichert wird. 
* **--queries-per-transaction:** Es wird empfohlen, keinen Wert über 500 zu verwenden.
* **--threads:** Die Anzahl der zu verwendenden Threads, Standardwert ist 4. Es wird empfohlen, einen Wert zu verwenden, der dem Zweifachen der virtuellen Kerne des Computers entspricht.

> [!Tip]
> Führen Sie den folgenden Befehl aus, um weitere Informationen zu anderen Optionen zu erhalten, die Sie mit „myloader“ verwenden können: **myloader --help**.

Nach der Wiederherstellung der Datenbank wird immer empfohlen, die Datenkonsistenz zwischen der Quell- und der Zieldatenbank zu überprüfen.

> [!Note]
> Senden Sie uns **[here](https://github.com/maxbube/mydumper/issues)** Probleme oder Feedback zu den Tools „mydumper“/„myloader“.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über das [mydumper/myloader-Projekt in GitHub](https://github.com/maxbube/mydumper).
* Erfahren Sie mehr über das [Migrieren großer MySQL-Datenbanken](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
* [Tutorial: Migrieren von Azure Database for MySQL – Single Server zu Azure Database for MySQL – Flexibler Server mit minimaler Downtime](howto-migrate-single-flexible-minimum-downtime.md)
* Weitere Informationen zur Datenreplikation unter [Replizieren von Daten in Azure Database for MySQL Flexible Server](flexible-server/concepts-data-in-replication.md) und [Konfigurieren der Datenreplikation in Azure Database for MySQL Flexible Server](./flexible-server/how-to-data-in-replication.md)
* Häufig auftretende [Migrationsfehler](./howto-troubleshoot-common-errors.md)