---
title: Indexerverbindung mit SQL Server auf Azure-VMs
titleSuffix: Azure Cognitive Search
description: Aktivieren Sie verschlüsselte Verbindungen, und konfigurieren Sie die Firewall für Verbindungen mit SQL Server auf einem virtuellen Azure-Computer (VM) über einen Indexer in der kognitiven Azure-Suche.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: ec637058810e5e195569db6a568d5c794b3df1f4
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892702"
---
# <a name="indexer-connections-to-sql-server-on-an-azure-virtual-machine"></a>Indexerverbindungen mit SQL Server auf einem virtuellen Azure-Computer

Wenn Sie einen [Azure SQL-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq) konfigurieren, um Inhalte aus einer Datenbank auf eine Azure-VM zu extrahieren, sind zusätzliche Schritte für sichere Verbindungen erforderlich. 

Eine Verbindung zwischen der Azure Cognitive Search und dem SQL Server auf einer virtuellen Maschine erfolgt über eine öffentliche Internetverbindung. Um sichere Verbindungen herzustellen, führen Sie die folgenden Schritte aus:

+ Beschaffen Sie sich ein Zertifikat von einem [Zertifizierungsstellenanbieter](https://en.wikipedia.org/wiki/Certificate_authority#Providers) für den vollständig qualifizierten Domänennamen der SQL Server-Instanz auf der Azure-VM.

+ Installieren Sie das Zertifikat auf der virtuellen Maschine, und aktivieren und konfigurieren Sie dann verschlüsselte Verbindungen auf der VM mithilfe der Anweisungen in diesem Artikel.

## <a name="enable-encrypted-connections"></a>Aktivieren von verschlüsselten Verbindungen

Bei der kognitiven Azure-Suche ist für alle Indexeranforderungen über eine öffentliche Internetverbindung ein verschlüsselter Kanal erforderlich. In diesem Abschnitt sind die entsprechenden Schritte angegeben.

1. Überprüfen Sie die Eigenschaften des Zertifikats, um sicherzustellen, dass der Antragstellername der vollqualifizierte Domänenname (FQDN) der Azure-VM ist. Sie können ein Tool wie CertUtils oder das Zertifikat-Snap-In zum Anzeigen der Eigenschaften verwenden. Sie finden den vollqualifizierten Domänennamen im **Azure-Portal** auf dem Blatt des VM-Diensts unter „Zusammenfassung“ im Feld [Öffentliche IP-Adresse/DNS-Namensbezeichnung](https://portal.azure.com/).
  
   + Für VMs, die mit der neueren **Resource Manager**-Vorlage erstellt wurden, hat der FQDN das Format `<your-VM-name>.<region>.cloudapp.azure.com`.

   + Für ältere virtuelle Computer, die als **klassische** VM erstellt wurden, hat der FQDN das Format `<your-cloud-service-name.cloudapp.net>`.

1. Konfigurieren Sie SQL Server für die Verwendung des Zertifikats mit dem Registrierungs-Editor (regedit). 

   SQL Server-Konfigurations-Manager wird zwar häufig für diese Aufgabe verwendet, aber für dieses Szenario ist dies nicht möglich. Das importierte Zertifikat kann nicht gefunden werden, da der FQDN der VM unter Azure nicht mit dem von der VM ermittelten FQDN übereinstimmt (Domäne wird entweder als lokaler Computer oder als Netzwerkdomäne identifiziert, für die der Beitritt durchgeführt wurde). Verwenden Sie „regedit“, um das Zertifikat anzugeben, wenn die Namen nicht übereinstimmen.

   + Navigieren Sie in „regedit“ zu diesem Registrierungsschlüssel: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.

     Der Teil `[MSSQL13.MSSQLSERVER]` variiert je nach Version und Instanzname. 

   + Legen Sie den Wert des Zertifikatschlüssels (**Certificate**) auf den Fingerabdruck (**Thumbprint**) des TLS/SSL-Zertifikats fest, das Sie in den virtuellen Computer importiert haben.

     Es gibt mehrere Möglichkeiten zur Beschaffung des Fingerabdrucks, von denen einige besser als andere geeignet sind. Wenn Sie ihn aus dem **Zertifikate**-Snap-In in MMC kopieren, ist unter Umständen ein unsichtbares vorangestelltes Zeichen enthalten. Dies führt zu einem Fehler, wenn Sie versuchen, die Verbindung herzustellen. [Informationen hierzu finden Sie in diesem Supportartikel](https://support.microsoft.com/kb/2023869/). Für dieses Problem gibt es mehrere Lösungen. Die einfachste Methode ist die Verwendung der Rückschritttaste und das erneute Eingeben des ersten Zeichens des Fingerabdrucks, um das vorangestellte Zeichen im Schlüsselwertfeld des Registrierungs-Editors zu entfernen. Alternativ dazu können Sie auch ein anderes Tool zum Kopieren des Fingerabdrucks verwenden.

1. Gewähren Sie Berechtigungen für das Dienstkonto. 

    Stellen Sie sicher, dass dem SQL Server-Dienstkonto die entsprechende Berechtigung für den privaten Schlüssel des TLS/SSL-Zertifikats gewährt wird. Wenn Sie diesen Schritt übersehen, startet SQL Server nicht. Sie können das **Zertifikate**-Snap-In oder **CertUtils** für diese Aufgabe verwenden.

1. Starten Sie den SQL Server-Dienst neu.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Konfigurieren der SQL Server-Konnektivität in der VM

Nachdem Sie die für die kognitive Azure-Suche erforderliche verschlüsselte Verbindung eingerichtet haben, müssen noch weitere Konfigurationsschritte für SQL Server auf Azure-VMs ausgeführt werden. Falls dies noch nicht erfolgt ist, ist der nächste Schritt das Abschließen der Konfiguration mit einem dieser Artikel:

+ Informationen für eine **Resource Manager** -VM finden unter [Verbinden mit SQL Server-Instanzen auf virtuellen Azure-Maschinen (Ressourcen-Manager)](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md). 

+ Informationen für eine **klassische** VM finden unter [Herstellen einer Verbindung mit einem virtuellen SQL Server-Computer in Azure (Klassische Bereitstellung)](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect).

Sehen Sie sich in den Artikeln vor allem jeweils den Abschnitt zum „Verbinden über das Internet“ an.

## <a name="configure-the-network-security-group-nsg"></a>Konfigurieren der Netzwerksicherheitsgruppe (NSG)

Es ist nicht ungewöhnlich, die NSG und den entsprechenden Azure-Endpunkt oder die Zugriffssteuerungsliste (Access Control List, ACL) zu konfigurieren, um Ihre Azure-VM für andere Parteien zugänglich zu machen. Wahrscheinlich haben Sie diese Konfiguration bereits durchgeführt, damit Ihre eigene Anwendungslogik die Verbindung mit Ihrer SQL Azure-VM herstellen kann. Für eine Verbindung zwischen der kognitiven Azure-Suche und Ihrer SQL Azure-VM ist es nicht anders. 

Die folgenden Links führen zu Anleitungen zur NSG-Konfiguration für VM-Bereitstellungen. Verwenden Sie diese Anleitungen, um für einen Endpunkt der kognitiven Azure-Suche basierend auf der IP-Adresse eine Zugriffssteuerungsliste einzurichten.

> [!NOTE]
> Ausführlichere Informationen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe?](../virtual-network/network-security-groups-overview.md)

+ Informationen für eine **Resource Manager** -VM finden unter [Verwalten von NSGs mithilfe des Azure-Portals](../virtual-network/tutorial-filter-network-traffic.md).

+ Informationen für eine **klassische** VM finden Sie unter [Erstellen von NSGs (klassisch) in PowerShell](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps).

Die IP-Adressierung kann mit einigen Schwierigkeiten verbunden sein, die leicht bewältigt werden können, wenn Sie mit den Problemen und den entsprechenden Lösungen bereits vertraut sind. Die restlichen Abschnitte enthalten Empfehlungen für die Behandlung von Problemen in Bezug auf IP-Adressen in der ACL.

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Einschränken des Zugriffs auf Azure Cognitive Search

Wir empfehlen dringend, den Zugriff auf die IP-Adresse Ihres Suchdiensts und den IP-Adressbereich des  [Diensttags](../virtual-network/service-tags-overview.md#available-service-tags)`AzureCognitiveSearch` in der ACL einzuschränken, anstatt Ihre SQL Azure-VMs für alle Verbindungsanfragen zu öffnen.

Sie können die IP-Adresse herausfinden, indem Sie den vollqualifizierten Domänennamen (z. B. `<your-search-service-name>.search.windows.net`) Ihres Suchdiensts pingen. Es ist zwar möglich, aber unwahrscheinlich, dass sich die IP-Adresse des Suchdiensts ändert. Die IP-Adresse ist in der Regel während der gesamten Lebensdauer des Diensts statisch.

Sie können den IP-Adressbereich des [Diensttags](../virtual-network/service-tags-overview.md#available-service-tags) `AzureCognitiveSearch` ermitteln, indem Sie entweder [herunterladbare JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) oder die [Diensttagermittlungs-API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) verwenden. Der IP-Adressbereich wird wöchentlich aktualisiert.

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Einbeziehen der IP-Adressen des Portals für die kognitive Azure-Suche

Falls Sie das Azure-Portal zum Erstellen eines Indexers verwenden, müssen Sie dem Portal eingehenden Zugriff auf Ihre SQL Azure-VM erteilen. Eine eingehende Regel in der Firewall erfordert, dass Sie die IP-Adresse des Portals angeben.

Um die IP-Adresse des Portals zu erhalten, pingen Sie `stamp2.ext.search.windows.net` an, also die Domäne des Traffic-Managers. Die Anfrage wird zu einem Timeout führen, aber die IP-Adresse wird in der Statusmeldung sichtbar werden. In der Meldung "Pinging azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]" lautet die IP-Adresse zum Beispiel "52.252.175.48".

> [!NOTE]
> Cluster in unterschiedlichen Regionen stellen eine Verbindung mit verschiedenen Traffic-Managern her. Unabhängig vom Domänenname ist die IP-Adresse, die vom Ping zurückgegeben wird, die richtige, wenn Sie eine eingehende Firewallregel für das Azure-Portal in Ihrer Region definieren.

## <a name="next-steps"></a>Nächste Schritte

Da die Konfiguration nun abgeschlossen ist, können Sie jetzt eine SQL Server-Instanz auf der Azure-VM als Datenquelle für einen Indexer der kognitiven Azure angeben. Weitere Informationen finden Sie unter [Verbinden von Azure SQL-Datenbank mit der Azure Cognitive Search mithilfe von Indexern](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md).
