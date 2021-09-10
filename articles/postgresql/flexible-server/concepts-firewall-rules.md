---
title: Firewallregeln bei Azure Database for PostgreSQL – Flexibler Server
description: In diesem Artikel wird die Verwendung von Firewallregeln für Verbindungen mit Azure Database for PostgreSQL – Flexibler Server mit der Netzwerkbereitstellung mit öffentlichem Zugriff beschrieben.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/21/2021
ms.openlocfilehash: bdda8163bacd596eafab2a9b2d10d914012a3efb
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866868"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---flexible-server"></a>Firewallregeln bei Azure Database for PostgreSQL – Flexibler Server
Wenn Sie Azure Database for PostgreSQL – Flexible Server ausführen, haben Sie zwei Hauptoptionen für das Netzwerk. Die Optionen sind der private Zugriff (Integration in ein virtuelles Netzwerk) und der öffentliche Zugriff (zugelassene IP-Adressen). 

Bei öffentlichem Zugriff erfolgt der Zugriff auf den Azure Database for PostgreSQL-Server über einen öffentlichen Endpunkt. Standardmäßig blockiert die Firewall den gesamten Zugriff auf den Server. Um festzulegen, welche IP-Hosts auf den Server zugreifen können, erstellen Sie *Firewallregeln* auf Serverebene. Firewallregeln geben die zulässigen Bereiche für öffentliche IP-Adressen an. Die Firewall gewährt den Serverzugriff auf der Grundlage der Ursprungs-IP-Adresse der jeweiligen Anforderung.

Sie können Firewallregeln über das Azure-Portal oder mithilfe von Azure CLI-Befehlen erstellen. Sie müssen der Besitzer oder ein Mitwirkender des Abonnements sein.

Firewallregeln auf Serverebene wirken sich auf alle Datenbanken auf dem gleichen Azure Database for PostgreSQL-Server aus. Die Regeln wirken sich nicht auf den Zugriff auf die Website des Microsoft Azure-Portals aus.

In den folgenden Diagrammen wird veranschaulicht, wie Verbindungsversuche über das Internet und über Azure zunächst die Firewall passieren müssen, bevor sie PostgreSQL-Datenbanken erreichen:

:::image type="content" source="../media/concepts-firewall-rules/1-firewall-concept.png" alt-text="Diagramm mit einer Übersicht über die Funktionsweise der Firewall.":::

## <a name="connect-from-the-internet"></a>Verbindung aus dem Internet
Liegt die IP-Quelladresse der Anforderung innerhalb eines der in den Firewallregeln auf Serverebene angegebenen Bereiche, wird die Verbindung gewährt. Andernfalls wird sie abgelehnt. 

Wenn Ihre Anwendung beispielsweise eine Verbindung mit dem Java Database Connectivity-Treiber (JDBC) für PostgreSQL herstellt, kann folgender Fehler auftreten, weil die Verbindung durch die Firewall blockiert wird:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: KRITISCH: kein Pg\_hba.conf-Eintrag für Host „123.45.67.890“, Benutzer „Adminuser“, Datenbank „postgresql“, SSL

## <a name="connect-from-azure"></a>Herstellen einer Verbindung von Azure
Es wird empfohlen, die IP-Adresse für ausgehenden Datenverkehr einer beliebigen Anwendung oder eines Diensts zu ermitteln und den Zugriff auf die einzelnen IP-Adressen oder -Bereiche explizit zuzulassen. Sie können zum Beispiel die IP-Adresse für ausgehenden Datenverkehr einer Azure App Service-App suchen oder eine öffentliche IP-Adresse verwenden, die an einen virtuellen Computer gebunden ist. 

Wenn eine feste IP-Adresse für ausgehenden Datenverkehr für Ihren Azure-Dienst nicht verfügbar ist, ziehen Sie die Aktivierung von Verbindungen von allen IP-Adressen in Azure-Rechenzentren in Erwägung:

1. Aktivieren Sie im Azure-Portal im Bereich **Netzwerk** das Kontrollkästchen **Öffentlichen Zugriff auf diesen Server über beliebigen Azure-Dienst in Azure gestatten**. 
1. Wählen Sie **Speichern** aus. 

> [!IMPORTANT]
> Die Option **Öffentlichen Zugriff auf diesen Server über beliebigen Azure-Dienst in Azure gestatten** konfiguriert die Firewall so, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich der Verbindungen aus Abonnements anderer Kunden. Stellen Sie bei Verwendung dieser Option sicher, dass Ihre Anmelde- und Benutzerberechtigungen den Zugriff auf autorisierte Benutzer beschränken. 

:::image type="content" source="./media/concepts-firewall-rules/allow-public-access.png" alt-text="Screenshot: Auswahl zum Zulassen des Zugriffs auf Azure-Dienste im Portal.":::

## <a name="programmatically-manage-firewall-rules"></a>Programmgesteuertes Verwalten von Firewallregeln
Neben der Verwendung des Microsoft Azure-Portals können Sie Firewall-Regeln auch programmseitig über die Azure-Befehlszeilenschnittstelle verwalten. 

In der Azure-Befehlszeilenschnittstelle entspricht eine Firewallregeleinstellung mit der Start- und Endadresse „0.0.0.0“ der Option **Öffentlichen Zugriff auf diesen Server über beliebigen Azure-Dienst in Azure gestatten** im Portal. Wenn die Firewallregeln den Verbindungsversuch ablehnen, kann die App den Azure Database for PostgreSQL-Server nicht erreichen.

## <a name="troubleshoot-firewall-problems"></a>Behandeln von Firewallproblemen
Wenn der Zugriff auf den Azure Database for PostgreSQL-Dienst nicht das erwartete Verhalten aufweist, ziehen Sie folgende Möglichkeiten in Betracht:

* **Änderungen an der Zulassungsliste sind noch nicht wirksam**: Änderungen an der Firewallkonfiguration eines Azure Database for PostgreSQL-Servers können bis zu fünf Minuten dauern.

* **Die Anmeldung ist nicht autorisiert, oder ein falsches Kennwort wurde verwendet**: Wenn eine Anmeldung nicht über Berechtigungen für den Azure Database for PostgreSQL-Server verfügt oder das Kennwort falsch ist, wird die Verbindung zum Server verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, einen Verbindungsversuch zum Server zu unternehmen. Jeder Client muss weiterhin die erforderlichen Sicherheitsanmeldeinformationen bereitstellen.

  Der folgende Fehler kann beispielsweise auftreten, wenn die Authentifizierung für einen JDBC-Client fehlschlägt:

  > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: KRITISCH: Fehler bei der Kennwortauthentifizierung für Benutzer „IhrBenutzername“

* **Die Firewall lässt keine dynamischen IP-Adressen zu**: Wenn Sie über eine Internetverbindung mit dynamischer IP-Adressierung verfügen und Probleme beim Passieren der Firewall auftreten, probieren Sie eine der folgenden Lösungen aus:

  * Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, mit denen auf den Azure Database for PostgreSQL-Server zugegriffen wird. Fügen Sie dann den IP-Adressbereich als Firewallregel hinzu.

  * Verwenden Sie stattdessen statische IP-Adressen für die Clientcomputer, und fügen Sie die statischen IP-Adressen dann als Firewallregel hinzu.

* **Firewallregeln sind für das IPv6-Format nicht verfügbar**: Firewallregeln müssen das IPv4-Format aufweisen. Wenn Sie Firewallregeln im IPv6-Format angeben, wird ein Validierungsfehler angezeigt.


## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Verwalten von Firewallregeln für Azure Database for PostgreSQL mithilfe des Microsoft Azure-Portals](how-to-manage-firewall-portal.md)
* [Erstellen und Verwalten von Firewallregeln für Azure Database for PostgreSQL mithilfe der Azure-Befehlszeilenschnittstelle](how-to-manage-firewall-cli.md)
