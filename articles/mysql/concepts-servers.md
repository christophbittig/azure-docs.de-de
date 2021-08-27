---
title: Serverkonzepte – Azure Database for MySQL
description: Dieses Thema enthält Aspekte und Richtlinien für die Arbeit mit Azure-Datenbank für MySQL-Servern.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 7a359c71ab3882c810b8594f4e4e6d73f8124e70
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639759"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Serverkonzepte in Azure-Datenbank für MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Dieser Artikel enthält Erwägungen und Richtlinien für die Arbeit mit Azure Database for MySQL-Servern.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Was ist ein Azure-Datenbank für MySQL-Server?

Ein Azure-Datenbank für MySQL-Server fungiert als zentraler Verwaltungspunkt für mehrere Datenbanken. Sie weist dasselbe MySQL-Serverkonstrukt auf, das Sie möglicherweise aus lokalen Umgebungen kennen. Im Besonderen ist der Azure Database for MySQL-Dienst verwaltet, bietet Leistungsgarantien und stellt Zugriff sowie Funktionen auf Serverebene bereit.

Ein Azure-Datenbank für MySQL-Server zeichnet sich durch folgende Eigenschaften aus:

- Sie wird im Rahmen eines Azure-Abonnements erstellt.
- Sie stellt die übergeordnete Ressource für Datenbanken dar.
- Sie stellt einen Namespace für Datenbanken bereit.
- Sie ist ein Container mit einer Semantik von hoher Lebensdauer. Beim Löschen eines Servers werden die enthaltenen Datenbanken gelöscht.
- Sie stellt Ressourcen in einer Region zusammen.
- Sie stellt einen Verbindungsendpunkt für den Server- und Datenbankzugriff bereit.
- Sie stellt den Bereich für Verwaltungsrichtlinien bereit, die auf die jeweiligen Datenbanken angewendet werden (Anmeldungen, Firewalls, Benutzer, Rollen, Konfigurationen etc.).
- Sie ist in mehreren Versionen verfügbar. Weitere Informationen finden Sie unter [Unterstützte Azure-Datenbank für MySQL-Datenbankversionen](./concepts-supported-versions.md).

Sie können in einer Instanz von Azure-Datenbank für MySQL-Server eine oder mehrere Datenbanken erstellen. Sie können eine Einzeldatenbank pro Server erstellen, die alle Ressourcen nutzt, oder Sie können mehrere Datenbanken erstellen, um die Ressourcen gemeinsam zu verwenden. Die Preise gelten pro Server und basieren auf der Konfiguration des Tarifs, der virtuellen Kerne und des Speichers (GB). Weitere Informationen finden Sie unter [Tarife](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Wie stelle ich eine Verbindung zu einem Azure-Datenbank für MySQL-Server her und authentifiziere diesen?

Mithilfe der folgenden Elemente kann ein sicherer Zugriff auf Ihre Datenbank sichergestellt werden.

| Sicherheitskonzept | Beschreibung     |
| :-- | :-- |
| **Authentifizierung und Autorisierung** | Azure-Datenbank für MySQL-Server unterstützen die systemeigene MySQL-Authentifizierung. Mithilfe der Anmeldeinformationen des Serveradministrators können Sie eine Verbindung mit dem Server herstellen und sich authentifizieren. |
| **Protokoll** | Der Dienst unterstützt ein nachrichtenbasiertes Protokoll, das von MySQL verwendet wird. |
| **TCP/IP** | Das Protokoll wird über TCP/IP- und Unix-Domänensockets unterstützt. |
| **Firewall** | Zum Schutz Ihrer Daten verhindert eine Firewallregel jeglichen Zugriff auf Ihren Datenbankserver, bis Sie angeben, welche Computer zugriffsberechtigt sind. Weitere Informationen finden Sie unter [Firewallregeln für Azure-Datenbank für MySQL-Server](./concepts-firewall-rules.md). |
| **SSL** | Der Dienst unterstützt die Erzwingung von SSL-Verbindungen zwischen Ihrer Anwendung und Ihrem Datenbankserver.  Weitere Informationen finden Sie unter [Konfigurieren von SSL-Konnektivität in der Anwendung für eine sichere Verbindung mit Azure-Datenbank für MySQL](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mysql"></a>Beenden/Starten einer Azure Database for MySQL-Instanz

Azure-Datenbank für MySQL bietet Ihnen die Möglichkeit, den Server **anzuhalten**, wenn er nicht genutzt wird, und ihn zu **starten**, wenn Sie die Aktivität wieder aufnehmen. Dies erfolgt im Wesentlichen, um Kosten auf den Datenbankservern zu sparen und für die Ressource nur dann zu bezahlen, wenn sie in Gebrauch ist. Dies wird noch wichtiger bei Dev-Test-Workloads und wenn Sie den Server nur für einen Teil des Tages benutzen. Wenn Sie den Server anhalten, werden alle aktiven Verbindungen beendet. Wenn Sie den Server später wieder online schalten möchten, können Sie dies entweder über das [Azure-Portal](how-to-stop-start-server.md) oder die [CLI](how-to-stop-start-server.md) erledigen.

Wenn sich der Server im Status **Angehalten** befindet, wird das Compute des Servers nicht in Rechnung gestellt. Der Speicher wird jedoch weiterhin in Rechnung gestellt, da der Speicher des Servers verbleibt, um sicherzustellen, dass die Datendateien verfügbar sind, wenn der Server wieder gestartet wird.

> [!IMPORTANT]
> Wenn Sie den Server **anhalten**, bleibt er für die nächsten sieben Tage in diesem Zustand. Wenn Sie ihn während dieser Zeit nicht manuell **starten**, wird der Server nach Ablauf von sieben Tagen automatisch gestartet. Wenn Sie den Server nicht verwenden, können Sie ihn wieder **anhalten**.

Während der Zeit, in der der Server angehalten ist, können keine Verwaltungsvorgänge auf dem Server durchgeführt werden. Sie müssen [den Server starten](how-to-stop-start-server.md), um Konfigurationseinstellungen auf dem Server zu ändern.

### <a name="limitations-of-stopstart-operation"></a>Einschränkungen beim Vorgang „Anhalten/Starten“
- Nicht unterstützt mit Lesereplikatkonfigurationen (sowohl Quelle als auch Replikate).

## <a name="how-do-i-manage-a-server"></a>Wie verwalte ich einen Server?

Sie können die Erstellung, Löschung, Serverparameterkonfiguration (my.cnf), Skalierung, Sicherheit, Hochverfügbarkeit, Sicherung und Wiederherstellung, Überwachung und das Netzwerk Ihrer Azure Database for MySQL-Server mithilfe des Azure-Portals oder der Azure CLI verwalten. Außerdem sind die folgenden gespeicherten Prozeduren in Azure Database for MySQL verfügbar, um bestimmte Datenbankverwaltungsaufgaben auszuführen, die erforderlich sind, da Administratorrechte auf dem Server nicht unterstützt werden.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|–|Entspricht dem Befehl [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die der angegebenen „processlist_id“ zugeordnete Verbindung nach dem Beenden einer beliebigen Anweisung, die von der Verbindung ausgeführt wird.|
|*mysql.az_kill_query*|processlist_id|–|Entspricht dem Befehl [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die Anweisung, die derzeit von der Verbindung ausgeführt wird. Die Verbindung bleibt unverändert erhalten.|
|*mysql.az_load_timezone*|–|–|Lädt [Zeitzonentabellen](howto-server-parameters.md#working-with-the-time-zone-parameter), damit der `time_zone`-Parameter auf benannte Werte festgelegt werden kann (z. B. „USA/Pazifik“).|

## <a name="next-steps"></a>Nächste Schritte

- Einen Überblick über den Dienst finden Sie unter [Azure-Datenbank für MySQL – Überblick](./overview.md).
- Informationen zu bestimmten Ressourcenkontingenten und -beschränkungen basierend auf Ihrem **Tarif** finden Sie unter [Tarife](./concepts-pricing-tiers.md).
- Informationen zum Herstellen einer Verbindung mit dem Dienst finden Sie unter [Datenverbindungsbibliotheken für Azure-Datenbank für MySQL](./concepts-connection-libraries.md).
