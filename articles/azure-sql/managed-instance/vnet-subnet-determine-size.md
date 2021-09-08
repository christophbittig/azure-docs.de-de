---
title: Bestimmen der erforderlichen Subnetzgröße und des Bereichs
titleSuffix: Azure SQL Managed Instance
description: In diesem Artikel erfahren Sie, wie Sie die Größe des Subnetzes berechnen, in dem Azure SQL Managed Instance bereitgestellt wird.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 06/14/2021
ms.openlocfilehash: e3c789ec59e66189753c8515235b2375aa20e5f1
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687392"
---
# <a name="determine-required-subnet-size-and-range-for-azure-sql-managed-instance"></a>Bestimmen von Subnetzgröße und -bereich für Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance muss in einem [virtuellen Azure-Netzwerk](../../virtual-network/virtual-networks-overview.md) bereitgestellt werden. Die Anzahl verwalteter Instanzen, die im Subnetz eines VNet bereitgestellt werden können, hängt von der Größe des Subnetzes (dem Subnetzbereich) ab.

Bei der Erstellung einer verwalteten Instanz ordnet Azure abhängig vom Tarif, den Sie bei der Bereitstellung ausgewählt haben, eine Reihe virtueller Computer zu. Da diese virtuellen Computer Ihrem Subnetz zugeordnet sind, benötigen sie IP-Adressen. Um bei regulären Vorgängen und der Wartung von Diensten Hochverfügbarkeit sicherzustellen, kann Azure zusätzliche virtuelle Computer zuteilen. Daher ist die Anzahl der erforderlichen IP-Adressen in einem Subnetz größer als die Anzahl der verwalteten Instanzen in diesem Subnetz.

Prinzipiell benötigt eine verwaltete Instanz mindestens 32 IP-Adressen in einem Subnetz. Daher können Sie bei der Definition der IP-Adressbereiche für das Subnetz die Subnetzmaske /27 verwenden. Wir empfehlen eine sorgfältige Planung der Subnetzgröße für die Bereitstellungen verwalteter Instanzen. Berücksichtigen Sie bei der Planung die folgenden Eingaben:

- Anzahl verwalteter Instanzen mit den folgenden Instanzparametern:
  - Dienstebene
  - Hardwaregeneration
  - Anzahl der virtuellen Kerne
  - [Wartungsfenster](../database/maintenance-window.md)
- Pläne zum Hoch- oder Herunterskalieren bzw. Ändern der Dienstebene

> [!IMPORTANT]
> Eine Subnetzgröße von 16 IP-Adressen (Subnetzmaske /28) ermöglicht die Bereitstellung einer einzelnen verwalteten Instanz darin. Sie sollte nur für Auswertungs- oder Entwicklungs-/Testszenarien verwendet werden, in denen keine Skalierungsvorgänge erfolgen. 

## <a name="determine-subnet-size"></a>Bestimmen der Subnetzgröße

Dimensionieren Sie Ihr Subnetz abhängig von den Anforderungen hinsichtlich Bereitstellung und Skalierung künftiger Instanzen. Die folgenden Parameter helfen Ihnen bei der Berechnung:

- Azure benötigt fünf IP-Adressen im Subnetz für den eigenen Bedarf.
- Von jedem virtuellen Cluster wird eine zusätzliche Anzahl von Adressen zugeteilt. 
- Jede verwaltete Instanz nutzt die Anzahl von Adressen, die vom Tarif und der Hardwaregeneration abhängig ist.
- Mit jeder Skalierungsanforderung wird vorübergehend eine zusätzliche Anzahl von Adressen zugeteilt.

> [!IMPORTANT]
> Es ist nicht möglich, den Subnetzadressbereich zu ändern, wenn Ressourcen im Subnetz vorhanden sind. Außerdem ist es nicht möglich, verwaltete Instanzen aus einem Subnetz in ein anderes zu verschieben. Erwägen Sie den Einsatz größerer statt kleinerer Subnetze, um Probleme künftig zu vermeiden.

GP = universell; BC = unternehmenskritisch; VC = virtueller Cluster

| **Hardwaregeneration** | **Preisstufe** | **Azure-Nutzung** | **Nutzung virtueller Cluster** | **Instanznutzung** | **Gesamt** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

Für die obige Tabelle gilt Folgendes:

- In der Spalte **Gesamt** wird die Gesamtanzahl der Adressen angezeigt, die von einer einzelnen bereitgestellten Instanz im Subnetz verwendet werden. 
- Wenn Sie dem Subnetz weitere Instanzen hinzufügen, erhöht sich die Anzahl der von der Instanz verwendeten Adressen. Die Gesamtanzahl der Adressen nimmt dann ebenfalls zu. Das Hinzufügen einer weiteren von Gen4 GP verwalteten Instanz würde beispielsweise den Wert **Instanznutzung** auf 10 und den Wert **Gesamt** der verwendeten Adressen auf 16 erhöhen. 
- Die Adressen in der Spalte **Azure-Nutzung** werden von mehreren virtuellen Clustern gemeinsam genutzt.  
- Die Adressen in der Spalte **VC-Nutzung** werden von allen Instanzen im jeweiligen virtuellen Cluster gemeinsam genutzt.

Berücksichtigen Sie auch das Feature [Wartungsfenster](../database/maintenance-window.md), wenn Sie die Subnetzgröße bestimmen, insbesondere wenn mehrere Instanzen innerhalb desselben Subnetzes bereitgestellt werden. Wenn Sie während oder nach der Erstellung ein anderes Wartungsfenster für eine verwaltete Instanz angeben, muss sie im virtuellen Cluster mit dem entsprechenden Wartungsfenster platziert werden. Wenn kein solcher virtueller Cluster im Subnetz vorhanden ist, muss zuerst ein neuer Cluster erstellt werden, in dem die Instanz gehostet wird.

Bei einem Aktualisierungsvorgang ist in der Regel eine [Änderung der Größe des virtuellen Clusters](management-operations-overview.md) erforderlich. Wenn eine neue Erstellungs- oder Aktualisierungsanforderung eingeht, meldet der SQL Managed Instance-Dienst der Computeplattform eine Anforderung neuer Knoten, die hinzugefügt werden müssen. Basierend auf der Computeantwort erweitert das Bereitstellungssystem entweder den vorhandenen virtuellen Cluster oder erstellt einen neuen. Auch wenn der Vorgang in den meisten Fällen innerhalb desselben virtuellen Clusters erfolgt, wird auf Computeseite ggf. ein neuer Cluster erstellt. 


## <a name="update-scenarios"></a>Updateszenarien

Bei Skalierungsvorgängen benötigen Instanzen vorübergehend zusätzliche IP-Kapazität, die vom Tarif und der Hardwaregeneration abhängig ist:

| **Hardwaregeneration** | **Preisstufe** | **Szenario** | **Zusätzliche Adressen**  |
| --- | --- | --- | --- |
| Gen4<sup>1</sup> | universell oder unternehmenskritisch | Skalieren von virtuellen Kernen | 5 |
| Gen4<sup>1</sup> | universell oder unternehmenskritisch | Skalieren des Speichers | 5 |
| Gen4 | universell oder unternehmenskritisch | Wechseln von universell zu unternehmenskritisch oder von unternehmenskritisch zu universell | 5 |
| Gen4 | GP | Umstieg auf Gen5 | 9 |
| Gen4 | BC | Umstieg auf Gen5 | 11 |
| Gen5 | GP | Skalieren von virtuellen Kernen | 3 |
| Gen5 | GP | Skalieren des Speichers | 0 |
| Gen5 | GP | Wechseln zu unternehmenskritisch | 5 |
| Gen5 | BC | Skalieren von virtuellen Kernen | 5 |
| Gen5 | BC | Skalieren des Speichers | 5 |
| Gen5 | BC | Wechseln zu universell | 3 |

<sup>1</sup> Gen4-Hardware wird eingestellt und steht für neue Bereitstellungen nicht mehr zur Verfügung. Durch die Aktualisierung der Hardwaregeneration von Gen4 auf Gen5 können die besonderen Möglichkeiten von Gen5 genutzt werden.
  
## <a name="calculate-the-number-of-ip-addresses"></a>Berechnen der Anzahl von IP-Adressen

Für die Berechnung der Gesamtanzahl der IP-Adressen empfehlen wir folgende Formel. Sie berücksichtigt die mögliche Erstellung eines neuen virtuellen Clusters bei einer späteren Anforderung zum Erstellen oder Aktualisieren einer Instanz. Außerdem werden die Wartungsfensteranforderungen virtueller Cluster berücksichtigt.

**Formel: 5 + (a x 12) + (b x 16) + (c x 16)**

- a = Anzahl der GP-Instanzen
- a = Anzahl der BC-Instanzen
- c = Anzahl verschiedener Wartungsfensterkonfigurationen

Erläuterung:
- 5 = Anzahl der von Azure reservierten IP-Adressen
- 12 Adressen pro GP-Instanz = 6 für virtuellen Cluster, 3 für verwaltete Instanz, 3 weitere für Skalierungsvorgang
- 16 Adressen pro BC-Instanz = 6 für virtuellen Cluster, 5 für verwaltete Instanz, 5 weitere für Skalierungsvorgang
- 16 Adressen als Reserve = Szenario, in dem ein neuer virtueller Cluster erstellt wird

Beispiel: 
- Sie planen, drei universelle und zwei geschäftskritische verwaltete Instanzen im selben Subnetz bereitzustellen. Für alle Instanzen ist dasselbe Wartungsfenster konfiguriert. Dies bedeutet, dass Sie 5 + (3 x 12) + (2 x 16) + (1 x 16) = 89 IP-Adressen benötigen. 

  Da IP-Bereiche in Zweierpotenzen angegeben werden, erfordert Ihr Subnetz für diese Bereitstellung einen minimalen IP-Adressbereich von 128 (2 ^ 7). Sie müssen das Subnetz mit der Subnetzmaske /25 reservieren.

> [!NOTE]
> Obwohl es möglich ist, verwaltete Instanzen in einem Subnetz mit einer Anzahl von IP-Adressen bereitzustellen, die geringer ist als die Ausgabe der Subnetzformel, sollten Sie stets größere Subnetze verwenden. Ein größeres Subnetz kann dazu beitragen, künftige Probleme zu vermeiden, die sich aus einem Mangel an IP-Adressen ergeben, z. B. die fehlende Möglichkeit, zusätzliche Instanzen innerhalb des Subnetzes zu erstellen oder bestehende Instanzen zu skalieren. 

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Machen Sie sich mit der [Verbindungsarchitektur für SQL Managed Instance](connectivity-architecture-overview.md) vertraut.
- Informieren Sie sich, wie Sie [ein virtuelles Netzwerk erstellen, in dem Sie SQL Managed Instance bereitstellen](virtual-network-subnet-create-arm-template.md).
- Informationen zu Problemen im Zusammenhang mit dem DNS finden Sie unter [Konfigurieren eines benutzerdefinierten DNS](custom-dns-configure.md).
