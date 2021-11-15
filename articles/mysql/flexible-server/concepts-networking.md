---
title: Übersicht über Netzwerkkonzepte – Azure Database for MySQL Flexible Server
description: In diesem Artikel erfahren Sie mehr über die Konnektivitäts- und Netzwerkoptionen in der Bereitstellungsoption „Flexible Server“ für Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 2ae111660b2faa6f5bba17e9f94b17caa2b2c642
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438195"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server"></a>Konnektivitäts- und Netzwerkkonzepte für Azure Database for MySQL – Flexible Server

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Artikel werden die Konzepte zum Steuern der Konnektivität mit Ihrem flexiblen Azure MySQL-Server vorgestellt. Außerdem werden die Netzwerkkonzepte für den flexiblen Azure Database for MySQL-Server ausführlich erläutert, die für die sichere Erstellung eines Servers und den Zugriff darauf in Azure erforderlich sind.

Azure Database for MySQL: Der flexible Server unterstützt zwei Möglichkeiten zum Konfigurieren von Konnektivität mit Ihren Servern:
> [!NOTE]
> Nach der Servererstellung kann diese Netzwerkoption nicht mehr geändert werden.

   * **Privater Zugriff (VNet-Integration)** [Privater Zugriff](./concepts-networking-vnet.md) Sie können Ihren flexiblen Server in Ihrer [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md)-Instanz bereitstellen. Virtuelle Azure-Netzwerke ermöglichen eine private und sichere Netzwerkkommunikation. Ressourcen in einem virtuellen Netzwerk können über private IP-Adressen kommunizieren.
   
   * **Öffentlicher Zugriff (zugelassene IP-Adressen)** [Öffentlicher Zugriff](./concepts-networking-public.md) Auf Ihren flexiblen Server wird über einen öffentlichen Endpunkt zugegriffen. Der öffentliche Endpunkt ist eine öffentlich auflösbare DNS-Adresse. Der Ausdruck „zugelassene IP-Adressen“ bezieht sich auf einen Bereich von IP-Adressen, denen Sie die Berechtigung erteilen, auf den Server zuzugreifen. Diese Berechtigungen heißen **Firewallregeln**.

## <a name="choosing-a-networking-option"></a>Auswählen einer Netzwerkoption

Wählen Sie **Privater Zugriff (VNet-Integration)** , wenn Sie die folgenden Funktionen benötigen:
   * Verbinden von Azure-Ressourcen im gleichen virtuellen Netzwerk oder [Peered virtuellen Netzwerk](../../virtual-network/virtual-network-peering-overview.md) mit Ihrem flexiblen Server
   * Herstellen einer Verbindung von Azure-externen Ressourcen mit Ihrem flexiblen Server über ein VPN oder eine ExpressRoute-Verbindung
   * Kein öffentlicher Endpunkt

Wählen Sie die Methode **Öffentlicher Zugriff (zulässige IP-Adressen)** aus, wenn Sie die folgenden Funktionen benötigen:
   * Herstellen einer Verbindung von Azure-Ressourcen aus, die keine virtuellen Netzwerke unterstützen
   * Herstellen einer Verbindung von Azure-externen Ressourcen aus, die weder ein VPN noch ExpressRoute unterstützen 
   * Der flexible Server hat keinen öffentlichen Endpunkt.

Die folgenden Eigenschaften gelten unabhängig davon, ob Sie den privaten oder den öffentlichen Zugriff wählen:
* Verbindungen von zugelassenen IP-Adressen aus müssen sich beim MySQL-Server mit gültigen Anmeldeinformationen authentifizieren.
* Für den Netzwerkdatenverkehr ist die [Verbindungsverschlüsselung](#tls-and-ssl) verfügbar.
* Der Server verfügt über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN). Für die Hostnamenseigenschaft in Verbindungszeichenfolgen wird empfohlen, den FQDN anstelle einer IP-Adresse zu verwenden.
* Über beide Optionen wird der Zugriff auf Serverebene, nicht auf Datenbank- oder auf Tabellenebene gesteuert. Der Zugriff auf Datenbanken, Tabellen und andere Objekte wird mithilfe der Rolleneigenschaften in MySQL gesteuert.


### <a name="unsupported-virtual-network-scenarios"></a>Nicht unterstützte virtuelle Netzwerkszenarios

* Öffentlicher Endpunkt (oder öffentliche IP-Adresse oder DNS): Ein flexibler Server, der in einem virtuellen Netzwerk bereitgestellt wird, kann keinen öffentlichen Endpunkt haben.
* Nachdem der flexible Server in einem virtuellen Netzwerk und Subnetz bereitgestellt wurde, können Sie ihn in kein anderes virtuelles Netzwerk oder Subnetz verschieben. * Nachdem der flexible Server bereitgestellt wurde, können Sie das vom flexiblen Server verwendete virtuelle Netzwerk nicht in eine andere Ressourcengruppe oder ein anderes Abonnement verschieben.
* Die Subnetzgröße (Adressräume) kann nicht erhöht werden, sobald Ressourcen im Subnetz vorhanden sind.
* Der Umstieg vom öffentlichen auf privaten Zugriff ist nach Erstellen des Servers nicht zulässig. Es wird empfohlen, die Zeitpunktwiederherstellung zu verwenden.

Unter den folgenden Links erfahren Sie, wie Sie den privaten Zugriff (VNET-Integration) über das [Azure-Portal](how-to-manage-virtual-network-portal.md) oder die [Azure CLI](how-to-manage-virtual-network-cli.md) aktivieren.

> [!NOTE]
> Wenn Sie den benutzerdefinierten DNS-Server verwenden, müssen Sie eine DNS-Weiterleitung verwenden, um den FQDN von Azure Database for MySQL: Flexible Server aufzulösen. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="hostname"></a>Hostname
Unabhängig von Ihrer gewählten Netzwerkoption empfiehlt es sich, beim Herstellen einer Verbindung mit Ihrem flexiblen Server den vollqualifizierten Domänenname (FQDN) `<servername>.mysql.database.azure.com` in der Verbindungszeichenfolge anzugeben. 

## <a name="tls-and-ssl"></a>TLS und SSL
Azure Database for MySQL Flexible Server unterstützt das Herstellen einer Verbindung zwischen Ihren Clientanwendungen und dem MySQL-Server über Secure Sockets Layer (SSL) mit TLS-Verschlüsselung (Transport Layer Security). TLS ist ein Standardprotokoll der Branche, das verschlüsselte Netzwerkverbindungen zwischen dem Datenbankserver und Clientanwendungen gewährleistet, sodass Sie Konformitätsanforderungen einhalten können.

Azure Database for MySQL Flexible Server unterstützt standardmäßig verschlüsselte Verbindungen mit Transport Layer Security (TLS 1.2), und alle eingehenden Verbindungen mit TLS 1.0 und TLS 1.1 werden standardmäßig verweigert. Die Erzwingung einer verschlüsselten Verbindung oder die TLS-Versionskonfiguration bei Ihrem flexiblen Server kann konfiguriert und geändert werden. 

Im Folgenden sind die verschiedenen Konfigurationen von SSL- und TLS-Einstellungen aufgeführt, die Sie bei Ihrem flexiblen Server verwenden können:

| Szenario   | Einstellungen für Serverparameter      | BESCHREIBUNG                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL deaktivieren (verschlüsselte Verbindungen) | require_secure_transport = OFF |Wenn Ihre Legacy-Anwendung keine verschlüsselten Verbindungen mit dem MySQL-Server unterstützt, können Sie die Erzwingung von verschlüsselten Verbindungen mit Ihrem flexiblen Server deaktivieren, indem Sie „require_secure_transport=OFF“ festlegen.|
|Erzwingen von SSL bei der TLS-Version < 1.2 | require_secure_transport = ON und tls_version = TLSV1 oder TLSV1.1| Wenn Ihre Legacy-Anwendung verschlüsselte Verbindungen unterstützt, aber TLS-Version < 1.2 erfordert, können Sie verschlüsselte Verbindungen aktivieren, müssen aber Ihren flexiblen Server so konfigurieren, dass Verbindungen mit der von Ihrer Anwendung unterstützten TLS-Version (v1.0 oder v1.1) zulässig sind.|
|SSL mit TLS-Version = 1.2 erzwingen (Standardkonfiguration)|require_secure_transport = ON und tls_version = TLSV1.2| Dies ist die empfohlene Standardkonfiguration für einen flexiblen Server.|
|SSL mit TLS-Version = 1.3 erzwingen (unterstützt bei MySQL v8.0 und höher)| require_secure_transport = ON und tls_version = TLSV1.3| Dies ist nützlich und wird für die Entwicklung neuer Anwendungen empfohlen.|

> [!Note]
> Änderungen am SSL-Verschlüsselungsverfahren bei einem flexiblen Server werden nicht unterstützt. FIPS-Verschlüsselungssammlungen werden standardmäßig erzwungen, wenn „tls_version“ auf „TLS-Version 1.2“ festgelegt wird. Bei anderen TLS-Versionen als Version 1.2 wird das SSL-Verschlüsselungsverfahren auf die Standardeinstellungen festgelegt, die in der MySQL-Communityinstallation enthalten sind.

Weitere Informationen finden Sie unter [Herstellen einer Verbindung mithilfe von SSL/TLS](how-to-connect-tls-ssl.md). 


## <a name="next-steps"></a>Nächste Schritte
* Unter den folgenden Links erfahren Sie, wie Sie den privaten Zugriff (VNET-Integration) über das [Azure-Portal](how-to-manage-virtual-network-portal.md) oder die [Azure CLI](how-to-manage-virtual-network-cli.md) aktivieren.
* Unter den folgenden Links erfahren Sie, wie Sie den öffentlichen Zugriff (zugelassene IP-Adressen) über das [Azure-Portal](how-to-manage-firewall-portal.md) oder die [Azure CLI](how-to-manage-firewall-cli.md) aktivieren.
* Weitere Informationen zur [Verwendung der TLS](how-to-connect-tls-ssl.md)
