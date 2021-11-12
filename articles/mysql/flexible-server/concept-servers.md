---
title: 'Serverkonzepte: Azure Database for MySQL Flexible Server'
description: Dieses Thema enthält Aspekte und Richtlinien für die Arbeit mit Azure Database for MySQL Flexible Server.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: ea421471de279edc512c22c80d21daeebec106fe
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131468212"
---
# <a name="server-concepts-in-azure-database-for-mysql-flexible-server"></a>Serverkonzepte in Azure Database für MySQL-flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

Dieser Artikel enthält Erwägungen und Richtlinien für die Arbeit mit Azure Database for MySQL Flexible Server.

## <a name="what-is-an-azure-database-for-mysql-flexible-server"></a>Was ist ein Azure Database for MySQL Flexible Server?

Azure Database for MySQL Flexible Server ist ein vollständig verwalteter Datenbankdienst, auf dem die Communityversion von MySQL ausgeführt wird. Im Allgemeinen ist der Dienst so konzipiert, dass er Flexibilität und Konfigurationsanpassungen auf der Grundlage der Benutzeranforderungen bietet. Sie weist dasselbe MySQL-Serverkonstrukt auf, das Sie möglicherweise aus lokalen Umgebungen kennen. Insbesondere der flexible Server wird verwaltet, bietet vorkonfigurierte Leistung, bessere Serververwaltung und -kontrolle und stellt Zugriff und Features auf Serverebene zur Verfügung.

Eine Azure Database for MySQL Flexible Server-Instanz:

- Sie wird im Rahmen eines Azure-Abonnements erstellt.
- Sie stellt die übergeordnete Ressource für Datenbanken dar.
- Gestattet die MySQL-Konfiguration, die durch Serverparameter verfügbar gemacht wird (Link zu Serverparameterkonzepten).
- Führt automatische Sicherungen durch und unterstützt Point-in-Time-Wiederherstellungen.
- Sie stellt einen Namespace für Datenbanken bereit.
- Sie ist ein Container mit einer Semantik von hoher Lebensdauer. Beim Löschen eines Servers werden die enthaltenen Datenbanken gelöscht.
- Sie stellt Ressourcen in einer Region zusammen.
- Unterstützung für kundenseitige Serverwartungszeitpläne
- Möglichkeit zur Bereitstellung flexibler Server in einer zonenredundanten Einrichtung für verbesserte Hochverfügbarkeit
- Bietet eine virtuelle Netzwerkintegration für den Zugriff auf den Datenbankserver
- Bietet die Möglichkeit, Kosten zu sparen, indem der flexible Server angehalten wird, wenn er nicht benutzt wird
- Sie stellt den Bereich für Verwaltungsrichtlinien bereit, die auf die jeweiligen Datenbanken angewendet werden (Anmeldungen, Firewalls, Benutzer, Rollen, Konfigurationen etc.).
- Unterstützt die Hauptversion MySQL 5.7 und MySQL 8.0. Weitere Informationen finden Sie unter [Unterstützte Azure Database for MySQL-Engine-Versionen](./../concepts-supported-versions.md).

Sie können in einer Instanz von Azure Database for MySQL Flexible Server eine oder mehrere Datenbanken erstellen. Sie können eine Einzeldatenbank pro Server erstellen, die alle Ressourcen nutzt, oder Sie können mehrere Datenbanken erstellen, um die Ressourcen gemeinsam zu verwenden. Die Preise gelten pro Server und basieren auf der Konfiguration der Computeebene, der virtuellen Kerne und des Speichers (GB). Weitere Informationen finden Sie unter [Compute und Speicher](./concepts-compute-storage.md).

## <a name="stopstart-an-azure-database-for-mysql-flexible-server"></a>Anhalten/Starten einer Azure Database for MySQL Flexible Server-Instanz

Azure Database for MySQL Flexible Server bietet Ihnen die Möglichkeit, den Server **anzuhalten**, wenn er nicht benutzt wird, und den Server zu **starten**, wenn Sie die Aktivität wieder aufnehmen. Dies erfolgt im Wesentlichen, um Kosten auf den Datenbankservern zu sparen und für die Ressource nur dann zu bezahlen, wenn sie in Gebrauch ist. Dies wird noch wichtiger bei Dev-Test-Workloads und wenn Sie den Server nur für einen Teil des Tages benutzen. Wenn Sie den Server anhalten, werden alle aktiven Verbindungen beendet. Wenn Sie den Server später wieder online bringen möchten, können Sie entweder das [Azure-Portal](how-to-stop-start-server-portal.md) oder die CLI verwenden.

Wenn sich der Server im Status **Angehalten** befindet, wird das Compute des Servers nicht in Rechnung gestellt. Der Speicher wird jedoch weiterhin in Rechnung gestellt, da der Speicher des Servers verbleibt, um sicherzustellen, dass die Datendateien verfügbar sind, wenn der Server wieder gestartet wird.

> [!IMPORTANT]
> Wenn Sie den Server **anhalten**, bleibt er die nächsten 30 Tage lang in diesem Zustand. Wenn Sie den Server während dieser Zeit nicht manuell **starten**, wird er nach Ablauf der 30 Tage automatisch gestartet. Wenn Sie den Server nicht verwenden, können Sie ihn wieder **anhalten**.

Während der Zeit, in der der Server angehalten ist, können keine Verwaltungsvorgänge auf dem Server durchgeführt werden. Sie müssen [den Server starten](how-to-stop-start-server-portal.md), um Konfigurationseinstellungen auf dem Server zu ändern. Weitere Informationen finden Sie unter den [Einschränkungen zum Anhalten/Starten](./concepts-limitations.md#stopstart-operation).

## <a name="how-do-i-manage-a-server"></a>Wie verwalte ich einen Server?

Sie können die Erstellung, Löschung, Serverparameterkonfiguration (my.cnf), Skalierung, Sicherheit, Hochverfügbarkeit, Sicherung und Wiederherstellung, Überwachung und das Netzwerk Ihres flexiblen Azure Database for MySQL-Servers mithilfe des [Azure-Portals](./quickstart-create-server-portal.md) oder der [Azure CLI](./quickstart-create-server-cli.md) verwalten. Außerdem sind die folgenden gespeicherten Prozeduren in Azure Database for MySQL verfügbar, um bestimmte Datenbankverwaltungsaufgaben auszuführen, die erforderlich sind, da Administratorrechte auf dem Server nicht unterstützt werden.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|–|Entspricht dem Befehl [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die der angegebenen „processlist_id“ zugeordnete Verbindung nach dem Beenden einer beliebigen Anweisung, die von der Verbindung ausgeführt wird.|
|*mysql.az_kill_query*|processlist_id|–|Entspricht dem Befehl [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die Anweisung, die derzeit von der Verbindung ausgeführt wird. Die Verbindung bleibt unverändert erhalten.|
|*mysql.az_load_timezone*|–|–|Lädt [Zeitzonentabellen](../howto-server-parameters.md#working-with-the-time-zone-parameter), damit der `time_zone`-Parameter auf benannte Werte festgelegt werden kann (z. B. „USA/Pazifik“).|


## <a name="next-steps"></a>Nächste Schritte

-   Informieren Sie sich über die [Servererstellung](./quickstart-create-server-portal.md).
-   Weitere Informationen zu [Überwachung und Warnungen](./how-to-alert-on-metric.md).

