---
title: Übersicht über den Zugriff auf öffentliche Netzwerke – Flexibler Azure Database for MySQL-Server
description: In diesem Artikel erfahren Sie mehr über die Optionen für den Zugriff auf öffentliche Netzwerke bei der Bereitstellungsoption „Flexibler Server“ für Azure Database for MySQL.
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: a8d2e1e6ef3f1aa2dcb736336232b1b149eb7b89
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438196"
---
# <a name="public-network-access-for-azure-database-for-mysql---flexible-server"></a>Zugriff aus öffentlichen Netzwerken für Azure Database for MySQL – Flexibler Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Artikel werden die öffentlichen Konnektivitätsoptionen für Ihren Server beschrieben. Sie erfahren im Detail die Konzepte zum Erstellen eines flexiblen Azure Database for MySQL-Servers, auf den über das Internet sicher zugegriffen werden kann.

## <a name="public-access-allowed-ip-addresses"></a>Öffentlicher Zugriff (zugelassene IP-Adressen)

Wenn Sie den öffentlichen Zugriff auf Ihren flexiblen Server konfigurieren, kann der Zugriff auf den Server über einen öffentlichen Endpunkt erfolgen, d. h. der Server ist über das Internet zugänglich. Der öffentliche Endpunkt ist eine öffentlich auflösbare DNS-Adresse. Der Ausdruck „zugelassene IP-Adressen“ bezieht sich auf einen Bereich von IP-Adressen, denen Sie die Berechtigung erteilen, auf den Server zuzugreifen. Diese Berechtigungen heißen Firewallregeln.

Die öffentliche Zugriffsmethode weist u. a. folgende Eigenschaften auf:

* Nur die IP-Adressen, die Sie zulassen, dürfen auf Ihre MySQL Flexible Server-Instanz zugreifen. Standardmäßig sind keine IP-Adressen zugelassen. Sie können IP-Adressen während der Servererstellung oder später hinzufügen.
* Ihr MySQL-Server verfügt über einen öffentlich auflösbaren DNS-Namen.
* Ihr flexibler Server befindet sich nicht in einem Ihrer virtuellen Azure-Netzwerke.
* Der Netzwerkdatenverkehr zu und von Ihrem Server erfolgt nicht über ein privates Netzwerk. Der Datenverkehr verwendet die allgemeinen Internetpfade.

### <a name="firewall-rules"></a>Firewallregeln

Eine Berechtigung, die einer IP-Adresse erteilt wird, wird als Firewallregel bezeichnet. Wenn ein Verbindungsversuch von einer IP-Adresse stammt, die Sie nicht zugelassen haben, wird dem Ursprungsclient ein Fehler zurückgegeben.

### <a name="allowing-all-azure-ip-addresses"></a>Zulassen aller Azure-IP-Adressen

Wenn eine feste IP-Adresse für ausgehenden Datenverkehr für Ihren Azure-Dienst nicht verfügbar ist, können Sie die Aktivierung von Verbindungen von allen IP-Adressen im Azure-Rechenzentrum in Erwägung ziehen.

> [!IMPORTANT]
> Die Option **Allow public access from Azure services and resources within Azure** (Öffentlichen Zugriff von Azure-Diensten und -Ressourcen aus gestatten) konfiguriert die Firewall so, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich der Verbindungen aus Abonnements anderer Kunden. Wenn Sie diese Option auswählen, stellen Sie sicher, dass die Anmelde- und die Benutzerberechtigungen den Zugriff nur auf autorisierte Benutzer beschränken.

Unter den folgenden Links erfahren Sie, wie Sie den öffentlichen Zugriff (zugelassene IP-Adressen) über das [Azure-Portal](how-to-manage-firewall-portal.md) oder die [Azure CLI](how-to-manage-firewall-cli.md) aktivieren und verwalten.

### <a name="troubleshooting-public-access-issues"></a>Behandeln von Problemen mit dem öffentlichen Zugriff

Wenn der Zugriff auf den Serverdienst „Microsoft Azure Database for MySQL“ nicht das erwartete Verhalten aufweist, sind folgende Punkte zu beachten:

* **Änderungen an der Zulassungsliste sind noch nicht wirksam:** Änderungen an der Firewallkonfiguration für Azure-Datenbank für MySQL-Server werden möglicherweise erst nach fünf Minuten wirksam.

* **Fehler bei der Authentifizierung:** Wenn ein Benutzer nicht über die Berechtigungen für den Azure Database for MySQL-Server verfügt oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure Database for MySQL-Server verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, einen Verbindungsversuch zum Server zu unternehmen. Jeder Client muss weiterhin die erforderlichen Sicherheitsanmeldeinformationen bereitstellen.

* **Dynamische Client-IP-Adresse:** Wenn Sie über eine Internetverbindung mit dynamischer IP-Adressierung verfügen und Probleme beim Passieren der Firewall auftreten, können Sie eine der folgenden Lösungen ausprobieren:

  * Fragen Sie Ihren Internetdienstanbieter nach dem IP-Adressbereich, der Ihren Clientcomputern zugewiesen ist, die auf den Azure Database for MySQL-Server zugreifen, und fügen Sie dann den IP-Adressbereich als Firewallregel hinzu.
  * Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die statische IP-Adresse als Firewallregel hinzu.

* **Die Firewallregel ist für das IPv6-Format nicht verfügbar:** Firewallregeln müssen das IPv4-Format aufweisen. Wenn Sie Firewallregeln im IPv6-Format angeben, wird ein Validierungsfehler angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* Unter den folgenden Links erfahren Sie, wie Sie den öffentlichen Zugriff (zugelassene IP-Adressen) über das [Azure-Portal](how-to-manage-firewall-portal.md) oder die [Azure CLI](how-to-manage-firewall-cli.md) aktivieren.
* Weitere Informationen zur [Verwendung der TLS](how-to-connect-tls-ssl.md)
