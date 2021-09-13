---
title: 'Doppelte Infrastrukturverschlüsselung: Azure Database for MySQL'
description: Erfahren Sie mehr über die doppelte Infrastrukturverschlüsselung, um eine zweite Verschlüsselungsebene mit dienstseitig verwalteten Schlüsseln hinzuzufügen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 6/30/2020
ms.openlocfilehash: 9b6d87c3bfabf3884d9a90966994eea002a45dd8
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2021
ms.locfileid: "114674433"
---
# <a name="azure-database-for-mysql-infrastructure-double-encryption"></a>Azure Database for MySQL: doppelte Infrastrukturverschlüsselung

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Database for MySQL arbeitet mit der [Verschlüsselung gespeicherter ruhender Daten](concepts-security.md#at-rest) unter Verwendung von Schlüsseln, die von Microsoft verwaltet werden. Daten, einschließlich Sicherungen, werden auf Datenträgern verschlüsselt, wobei diese Verschlüsselung stets aktiviert ist und nicht deaktiviert werden kann. Die Verschlüsselung beruht auf einem gemäß FIPS 140-2 validierten kryptografischen Modul und einem AES-256-Bit-Verschlüsselungsverfahren für die Azure-Speicherverschlüsselung.

Die doppelte Infrastrukturverschlüsselung fügt eine zweite Verschlüsselungsebene mit dienstseitig verwalteten Schlüsseln hinzu. Sie bedient sich eines gemäß FIPS 140-2 validierten kryptografischen Moduls, jedoch mit einem anderen Verschlüsselungsalgorithmus. Dies bietet eine zusätzliche Schutzebene für Ihre ruhenden Daten. Der bei der doppelten Infrastrukturverschlüsselung verwendete Schlüssel wird auch vom Dienst Azure Database for MySQL verwaltet. Die doppelte Infrastrukturverschlüsselung ist nicht standardmäßig aktiviert, da die zusätzliche Verschlüsselungsebene die Leistung beeinträchtigen kann.

> [!NOTE]
> Wie die Verschlüsselung von Daten im Ruhezustand wird dieses Feature nur für den Speichertyp „Universell V2“ (Unterstützung von bis zu 16 TB) unterstützt, der in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ verfügbar ist. Weitere Informationen finden Sie unter [Speicherkonzepte](concepts-pricing-tiers.md#storage). Weitere Einschränkungen finden Sie im Abschnitt [Einschränkungen](concepts-infrastructure-double-encryption.md#limitations).

Die Verschlüsselung auf Infrastrukturebene hat den Vorteil, dass sie auf der Ebene implementiert wird, die dem Speichergerät oder den Netzwerkleitungen am nächsten ist. Azure Database for MySQL implementiert die beiden Verschlüsselungsebenen mithilfe dienstseitig verwalteter Schlüssel. Obwohl technisch gesehen immer noch auf Dienstebene, ist sie sehr nahe an der Hardware, die die ruhenden Daten speichert. Optional können Sie weiterhin die Verschlüsselung ruhender Daten aktivieren, indem Sie einen [kundenseitig verwalteten Schlüssel](concepts-data-encryption-mysql.md) für den bereitgestellten MySQL-Server verwenden. 

Die Implementierung auf Infrastrukturebene unterstützt zudem eine Vielfalt von Schlüsseln. Die Infrastruktur muss die verschiedenen Cluster von Computern und Netzwerken kennen. Daher werden verschiedene Schlüssel verwendet, um den Angriffsradius von Infrastrukturangriffen und eine Vielzahl von Hardware- und Netzwerkfehlern zu minimieren. 

> [!NOTE]
> Die Verwendung der doppelten Infrastrukturverschlüsselung beeinträchtigt aufgrund des zusätzlichen Verschlüsselungsverfahrens den Durchsatz Ihres Azure Database for MySQL-Servers um 5–10 %.

## <a name="benefits"></a>Vorteile

Die doppelte Infrastrukturverschlüsselung für Azure Database for MySQL bietet die folgenden Vorteile:

1. **Mehr Vielfalt bei der Implementierung von Kryptografie**: Durch die geplante Umstellung auf hardwarebasierte Verschlüsselung werden die Implementierungen weiter diversifiziert, indem zusätzlich zur softwarebasierten Implementierung eine hardwarebasierte Implementierung bereitgestellt wird.
2. **Implementierungsfehler**: Zwei Verschlüsselungsebenen auf Infrastrukturebene schützen vor Fehlern bei der Zwischenspeicherung oder Speicherverwaltung auf höheren Ebenen, die Klartextdaten verfügbar machen. Darüber hinaus gewährleisten die beiden Ebenen auch Schutz vor Fehlern bei der Implementierung der Verschlüsselung im Allgemeinen.

Diese Kombination bietet einen wirksamen Schutz gegen häufige Bedrohungen und Schwachstellen, die für Angriffe auf die Kryptographie ausgenutzt werden.

## <a name="supported-scenarios-with-infrastructure-double-encryption"></a>Unterstützte Szenarien mit doppelter Infrastrukturverschlüsselung

Die Verschlüsselungsfunktionen, die von Azure Database for MySQL geboten werden, können kombiniert werden. Es folgt eine Übersicht über die verschiedenen möglichen Szenarien:

|  ##   | Standardverschlüsselung | Doppelte Infrastrukturverschlüsselung | Datenverschlüsselung mithilfe kundenseitig verwalteter Schlüssel  |
|:------|:------------------:|:--------------------------------:|:--------------------------------------------:|
| 1     | *Ja*              | *Nein*                             | *Nein*                                         |
| 2     | *Ja*              | *Ja*                            | *Nein*                                         |
| 3     | *Ja*              | *Nein*                             | *Ja*                                        |
| 4     | *Ja*              | *Ja*                            | *Ja*                                        |
|       |                    |                                  |                                              |

> [!Important]
> - In den Szenarien 2 und 4 kann der Durchsatz basierend auf dem Workloadtyp für den Azure Database for MySQL-Server aufgrund der zusätzlichen Ebene der Infrastrukturverschlüsselung um 5 bis 10 Prozent sinken.
> - Das Konfigurieren der doppelten Infrastrukturverschlüsselung für Azure Database for MySQL ist nur bei der Erstellung des Servers zulässig. Nachdem der Server bereitgestellt wurde, können Sie die Speicherverschlüsselung nicht mehr ändern. Sie können jedoch weiterhin die Datenverschlüsselung mit kundenseitig verwalteten Schlüsseln für den Server aktivieren, der mit/ohne doppelte Infrastrukturverschlüsselung erstellt wurde.

## <a name="limitations"></a>Einschränkungen

Bei Azure Database for MySQL unterliegt die Unterstützung der doppelten Infrastrukturverschlüsselung folgenden Einschränkungen:

* Die Unterstützung dieser Funktionalität ist auf die Tarife **Universell** und **Arbeitsspeicheroptimiert** beschränkt.
* Dieses Feature wird nur in Regionen und für Server unterstützt, die den Speichertyp „Universell V2“ (bis zu 16 TB) unterstützen. Eine Liste der Azure-Regionen, die Speicher mit bis zu 16 TB unterstützen, finden Sie in [diesem](concepts-pricing-tiers.md#storage) Abschnitt zum Speicher in der Dokumentation.

    > [!NOTE]
    > - Für alle neuen MySQL-Server, die in **Azure-Regionen** mit Unterstützung des Speichertyps „Universell V2“ erstellt werden, ist die Unterstützung der Verschlüsselung mit kundenseitig verwalteten Schlüsseln [verfügbar](concepts-pricing-tiers.md#storage). Server für die Zeitpunktwiederherstellung oder Lesereplikate kommen nicht in Frage, obwohl sie theoretisch „neu“ sind.
    > - Sie können im Portal zum Blatt „Tarif“ navigieren und die maximale Speichergröße anzeigen, die Ihr bereitgestellter Server unterstützt, um zu überprüfen, ob Ihr bereitgestellter Server den Speichertyp „Universell V2“ unterstützt,. Wenn Sie den Schieberegler auf bis zu 4 TB festlegen können, wird der Speichertyp „Universell V1“ verwendet, und Ihr Server unterstützt keine Verschlüsselung mit kundenseitig verwalteten Schlüsseln. Die Daten sind jedoch jederzeit mit dienstseitig verwalteten Schlüsseln verschlüsselt. Wenn Sie weitere Fragen haben, wenden Sie sich an AskAzureDBforMySQL@service.microsoft.com.



## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie die [doppelte Infrastrukturverschlüsselung für Azure Database for MySQL einrichten](howto-double-encryption.md).
