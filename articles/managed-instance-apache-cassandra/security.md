---
title: 'Sicherheit in Azure Managed Instance for Apache Cassandra: Übersicht'
description: Erfahren Sie mehr über bewährte Methoden für die Datenbanksicherheit und wichtige Features, die von Azure Managed Instance for Apache Cassandra angeboten werden. Diese helfen Ihnen, Datenbanksicherheitsverletzungen zu verhindern, zu erkennen und darauf zu reagieren.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: a5a34f539c8f2820c46736783f6d23d5b265164c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858591"
---
# <a name="security-in-azure-managed-instance-for-apache-cassandra---overview"></a>Sicherheit in Azure Managed Instance for Apache Cassandra: Übersicht

In diesem Artikel werden bewährte Methoden für die Datenbanksicherheit und die wichtigsten Features von Azure Managed Instance for Apache Cassandra erörtert, die Sie bei der Verhinderung, Erkennung und Reaktion auf Sicherheitsverletzungen in Datenbanken unterstützen.

## <a name="how-do-i-secure-my-database"></a>Wie schütze ich meine Datenbank?

Für die Sicherheit der Daten sind Sie, Ihr Kunde und Ihr Datenbankanbieter gemeinsam verantwortlich. Der Umfang Ihres Verantwortungsbereichs kann je nach Datenbankanbieter unterschiedlich ausfallen. Wenn Sie sich für eine lokale Lösung entschieden haben, müssen Sie weitgehend selbst für die Sicherheit sorgen – vom Endpunktschutz bis hin zur physischen Sicherung Ihrer Hardware. Das ist keine leichte Aufgabe. Wenn Sie sich für einen verwalteten Dienst wie Azure Managed Instance for Apache Cassandra entscheiden, verringert sich Ihr Problembereich. 

## <a name="how-does-azure-managed-instance-secure-my-database"></a>Wie sichert Azure Managed Instance for Apache Cassandra meine Datenbank?


|Sicherheitsanforderung|Sicherheitsansatz von Azure Managed Instance for Apache Cassandra|
|---|---|
|Netzwerksicherheit| Azure Managed Instance for Apache Cassandra-Ressourcen werden auf einem Microsoft-Mandanten gehostet, wobei Netzwerkschnittstellenkarten (NICs) für jede Ressource ausschließlich mit privaten IP-Adressen in virtuelle Netzwerke eingefügt werden. Mit diesem Dienst werden keine öffentlichen IP-Adressen verfügbar gemacht.|
|Automatisierte Onlinesicherungen|Azure Managed Instance for Apache Cassandra-Rechenzentren werden alle vier Stunden gesichert und die Sicherungen für zwei Tage aufbewahrt. Sicherungen werden in lokalen Speicherkonten gespeichert.|
|Wiederherstellen gelöschter Daten|Automatisierte Onlinesicherungen können zum Wiederherstellen von Daten verwendet werden, die versehentlich gelöscht wurden. Sie können die Daten bis zu einem beliebigen Zeitpunkt innerhalb von etwa zwei Tagen nach dem Löschvorgang sichern.|
|HTTPS/SSL/TLS und Datenträgerverschlüsselung | In Azure Managed Instance for Apache Cassandra werden alle Daten im Ruhezustand verschlüsselt. Server-SSL (TLS 1.2) und Knoten-zu-Knoten-Verschlüsselung werden erzwungen. Client-SSL ist eine optionale Konfiguration. Kundenseitig verwaltete Schlüssel zum Verschlüsseln von Daten auf dem Datenträger werden unterstützt. Weitere Informationen finden Sie [hier](customer-managed-keys.md). |
|Überwachen auf Angriffe|Azure Managed Instance for Apache Cassandra ist in [Azure Monitor](../azure-monitor/overview.md) integriert. Mithilfe von Überwachungsprotokollierung und Aktivitätsprotokollen können Sie Ihr Konto auf normale und ungewöhnliche Aktivitäten überwachen. Sie können anzeigen, welche Vorgänge für Ihre Ressourcen ausgeführt wurden, wer den jeweiligen Vorgang initiiert hat, wann der Vorgang ausgeführt wurde, welchen Status der Vorgang aufweist und viele weitere Aufgaben durchführen.|
|Reagieren auf Angriffe|Nachdem Sie den Azure-Support kontaktiert haben, um einen möglichen Angriff zu melden, wird ein aus fünf Schritten bestehender Prozess zur Reaktion auf den Incident angestoßen. Ziel dieses Prozesses ist es, die normale Dienstsicherheit und den normalen Betrieb so schnell wie möglich wiederherzustellen, nachdem ein Problem erkannt und eine Untersuchung gestartet wurde.|
|Gepatchte Server|Da Azure Managed Instance for Apache Cassandra eine verwaltete Datenbank ist, müssen Sie keine Server verwalten oder patchen – das wird automatisch für Sie erledigt.|
|Zertifizierungen| Die aktuellste Liste der Zertifizierungen finden Sie auf der [Website mit Complianceangeboten für Azure](https://www.microsoft.com/trustcenter/compliance/complianceoffering).


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Microsoft-Zertifizierungen finden Sie im [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).