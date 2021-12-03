---
title: 'Unterstützte Versionen: Azure Database for MySQL'
description: Erfahren Sie, welche Versionen des MySQL-Servers im Dienst Azure Database for MySQL unterstützt werden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.custom: ''
ms.date: 11/4/2021
ms.openlocfilehash: 466b3706c2814f4cb833e441c7d19f5d7b2895fe
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845891"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Unterstützte Azure-Datenbank für MySQL-Serverversionen

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL wurde basierend auf [MySQL Community Edition](https://www.mysql.com/products/community/) mit der InnoDB-Speicher-Engine entwickelt. Dieser Dienst unterstützt alle aktuellen Hauptversionen, die von der Community unterstützt werden: MySQL 5.7 und 8.0. Für MySQL wird das Benennungsschema „X.Y.Z“ verwendet. „X“ steht dabei für die Hauptversion, „Y“ für die Nebenversion und „Z“ für das Programmfehlerbehebungs-Release. Weitere Informationen zum Schema finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

## <a name="connect-to-a-gateway-node-that-is-running-a-specific-mysql-version"></a>Stellen Sie eine Verbindung zu einem Gateway-Knoten her, auf dem eine bestimmte MySQL-Version läuft

Bei der Bereitstellungsoption „Einzelserver“ werden die Verbindungen mit einem Gateway zu Serverinstanzen umgeleitet. Sobald die Verbindung hergestellt ist, zeigt der MySQL-Client die im Gateway festgelegte Version von MySQL an, nicht die tatsächliche Version, die auf Ihrer MySQL-Serverinstanz ausgeführt wird. Um die Version Ihrer MySQL-Serverinstanz zu ermitteln, geben Sie den `SELECT VERSION();`-Befehl an der MySQL-Eingabeaufforderung ein. Überprüfen Sie die [Konnektivitätsarchitektur](./concepts-connectivity-architecture.md#connectivity-architecture), um mehr über Gateways in der Dienstarchitektur von Azure Database for MySQL zu erfahren.

Da Azure Database for MySQL die Hauptversionen v5.7 und v8.0 unterstützt, führt der Standardport 3306 zum Herstellen einer Verbindung mit Azure Database for MySQL die MySQL-Clientversion 5.6 (kleinster gemeinsamer Nenner) aus, um Verbindungen mit Servern beider unterstützter Hauptversionen zu unterstützen. Wenn Ihre Anwendung jedoch eine Verbindung zu einer bestimmten Hauptversion, z. B. v5.7 oder v8.0, herstellen muss, können Sie dies tun, indem Sie den Port in der Verbindungszeichenfolge Ihres Servers ändern.

Im Azure Database für den MySQL-Dienst lauschen die Gateway-Knoten auf Port 3308 für v5.7-Clients und Port 3309 für v8.0-Clients. Mit anderen Worten: Wenn Sie eine Verbindung zum v5.7-Gateway-Client herstellen möchten, sollten Sie Ihren voll qualifizierten Servernamen und Port 3308 verwenden, um von der Client-Anwendung aus eine Verbindung zu Ihrem Server herzustellen. Ähnlich verhält es sich, wenn Sie eine Verbindung zum v8.0-Gateway-Client herstellen möchten. Sie können Ihren voll qualifizierten Servernamen und Port 3309 verwenden, um sich mit Ihrem Server zu verbinden. Prüfen Sie das folgende Beispiel, um mehr Klarheit zu erhalten.

:::image type="content" source="./media/concepts-supported-versions/concepts-supported-versions-gateway.png" alt-text="Beispiel für das Herstellen einer Verbindung über verschiedene Gateway MySQL-Versionen":::

> [!NOTE]
> Verbindungen mit Azure Database for MySQL über die Ports 3308 und 3309 werden nur für öffentliche Verbindungen unterstützt, Private Link und VNET-Dienst-Endpunkte können nur mit Port 3306 verwendet werden.

## <a name="azure-database-for-mysql-currently-supports-the-following-major-and-minor-versions-of-mysql"></a>Azure Database for MySQL unterstützt derzeit die folgenden Haupt- und Nebenversionen von MySQL:

| Version | [Single Server](overview.md) <br/> Aktuelle Nebenversion |[Flexible Server (Vorschau)](./flexible-server/overview.md) <br/> Aktuelle Nebenversion  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL-Version 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) (eingestellt) | Nicht unterstützt|
|MySQL-Version 5.7 | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html) | [5.7.32](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-32.html)|
|MySQL, Version 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

Lesen Sie die Richtlinie zur Versionsunterstützung für eingestellte Versionen in der [Dokumentation zur Richtlinie für die Versionsunterstützung](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql).

## <a name="managing-updates-and-upgrades"></a>Verwalten von Updates und Upgrades

Das Patchen von Versionsupdates zur Fehlerbehebung wird vom Dienst automatisch verwaltet. Beispiel: 5.7.20 bis 5.7.21.  

Ein Upgrade der Hauptversion wird zurzeit vom Dienst für Upgrades von MySQL v5.6 auf v5.7 unterstützt. Weitere Einzelheiten finden Sie unter [Upgraden von Hauptversionen](how-to-major-version-upgrade.md). Wenn Sie von 5.7 auf 8.0 upgraden möchten, sollten Sie eine [Sicherung und dann die Wiederherstellung](./concepts-migrate-dump-restore.md) auf einem Server ausführen, der mit der neuen Engine-Version erstellt wurde.

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zu Versionsrichtlinien für Azure Database for MySQL finden Sie in [diesem Dokument](concepts-version-policy.md).
- Informationen zu bestimmten Ressourcenkontingenten und -beschränkungen basierend auf Ihrer **Dienstebene** finden Sie unter [Dienstebenen](./concepts-pricing-tiers.md).
