---
title: Azure SQL-Datenbank-Ledger – Übersicht
description: Machen Sie sich mit den Grundlagen des Features Azure SQL-Datenbank Ledgers.
ms.date: 09/09/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: rothja
ms.author: jroth
ms.openlocfilehash: 07e999f70effc7141d66e01c8fea8686c6c133c7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056984"
---
# <a name="azure-sql-database-ledger"></a>Azure SQL-Datenbank-Ledger

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Der Azure SQL-Datenbank-Ledger ist zurzeit als Public Preview (Öffentliche Vorschau) verfügbar.

Das Einrichten einer Vertrauensstellung rund um die Integrität von in Datenbanksystemen gespeicherten Daten ist ein seit langem bestehendes Problem bei allen Organisationen, die Finanzdaten, Patientendaten oder andere vertrauliche Daten verwalten. Das Ledgerfeature von [Azure SQL-Datenbank](sql-database-paas-overview.md) bietet Manipulationsbeweisfunktionen in Ihrer Datenbank. Sie können kryptografisch gegenüber Dritten, (z. B. Prüfern oder anderen Geschäftspartnern) nachweisen, dass Ihre Daten nicht manipuliert wurden.

Ledger hilft beim Schutz von Daten vor Angreifern oder Benutzern mit umfangreichen Berechtigungen, einschließlich Datenbankadministratoren (Database Administrators, DBAs) sowie System- und Cloudadministratoren. Wie bei einem herkömmlichen Ledger werden Verlaufsdaten beibehalten. Bei der Aktualisierung einer Zeile in der Datenbank wird der vorherige Wert in einer Verlaufstabelle verwaltet und geschützt. Ledger stellt eine Chronik aller Änderungen bereit, die im Laufe der Zeit an der Datenbank vorgenommen wurden. 

Ledger und die Verlaufsdaten werden transparent verwaltet und bieten so Schutz ohne Anwendungsänderungen. Verlaufsdaten werden in einer relationalen Form verwaltet, um SQL-Abfragen für Überwachung, Forensik und andere Zwecke zu unterstützen. Ledger bietet Garantien kryptografischer Datenintegrität, während Leistungsfähigkeit, Flexibilität und Funktion von Azure SQL-Datenbank erhalten bleiben.

:::image type="content" source="media/ledger/ledger-table-architecture.png" alt-text="Diagramm der Ledgertabellenarchitektur.":::

## <a name="use-cases-for-azure-sql-database-ledger"></a>Anwendungsfälle für Azure SQL-Datenbank-Ledger 

### <a name="streamlining-audits"></a>Optimieren von Überwachungen

Der Wert jedes Produktionssystems basiert auf der Fähigkeit, den vom System genutzten und erzeugten Daten zu vertrauen. Wenn die Daten in Ihrer Datenbank von einem böswilligen Benutzer manipuliert wurden, kann dies zu katastrophalen Ergebnissen in den Geschäftsprozessen führen, die auf diesen Daten basieren. 

Die Aufrechterhaltung des Vertrauens in Ihre Daten erfordert eine Kombination aus der Aktivierung der richtigen Sicherheitskontrollen, um potenzielle Angriffe, Sicherungs- und Wiederherstellungsmethoden sowie gründliche Notfallwiederherstellungsverfahren zu reduzieren. Überwachungen durch externe Parteien stellen die Anwendung dieser Methoden sicher. 

Überwachungsprozesse sind sehr zeitintensive Aktivitäten. Für die Überwachung ist eine Untersuchung der implementierten Methoden vor Ort erforderlich, wie die Überprüfung von Überwachungsprotokollen sowie die Untersuchung der Authentifizierung und der Zugriffssteuerungen. Diese manuellen Prozesse können zwar potenzielle Sicherheitslücken offen legen, aber sie können keinen belegbaren Nachweis bieten, dass die Daten nicht böswillig geändert wurden. 

Ledger liefert Prüfern den kryptografischen Nachweis der Datenintegrität. Dieser Nachweis kann dazu beitragen, den Überwachungsprozess zu optimieren. Darüber hinaus bietet er Nichtabstreitbarkeit in Bezug auf die Integrität der Systemdaten.

### <a name="multiple-party-business-processes"></a>Geschäftsprozesse mit mehreren Parteien

In einigen Systemen (z. B. Lieferkettenverwaltungssystemen) müssen mehrere Organisationen den Status von einem Geschäftsprozess miteinander teilen. Diese Systeme kämpfen mit dem Problem der gemeinsamen Nutzung und Vertrauenswürdigkeit von Daten. Viele Organisationen nutzen herkömmliche Blockchains wie Ethereum oder Hyperledger Fabric, um ihre Geschäftsprozesse mit mehreren Parteien digital zu transformieren. 

Blockchain ist eine hervorragende Lösung für Netzwerke mit mehreren Parteien, bei denen das Vertrauen zwischen am Netzwerk beteiligten Parteien gering ist. Viele dieser Netzwerke sind grundsätzlich zentralisierte Lösungen, bei denen Vertrauen wichtig ist. Eine vollständig dezentralisierte Infrastruktur ist aber eine „schwergewichtige“ Lösung. 

Ledger bietet eine Lösung für diese Netzwerke. Teilnehmer können die Integrität der zentral gespeicherten Daten überprüfen, und zwar ohne die Komplexität und die Leistungsbeeinträchtigungen, die der Netzwerkkonsens in einem Blockchain-Netzwerk mit sich bringt.

### <a name="trusted-off-chain-storage-for-blockchain"></a>Vertrauenswürdiger Off-Chain-Speicher für Blockchain

Dort, wo ein Blockchain-Netzwerk für einen Geschäftsprozess mit mehreren Parteien erforderlich ist, ist es eine Herausforderung, die Daten in der Blockchain ohne Leistungseinbußen abfragen zu können. 

Typische Muster zur Lösung dieses Problems umfassen das Replizieren von Daten aus der Blockchain in einen Off-Chain-Speicher, z. B. eine Datenbank. Sobald die Daten jedoch aus der Blockchain in die Datenbank repliziert wurden, garantiert die Datenintegrität, dass ein Blockchainangebot verloren geht. Ledger bietet Datenintegrität für die Off-Chain-Speicherung von Blockchain-Netzwerken und stellt so eine vollständige Datenvertrauensstellung für das gesamte System sicher.

## <a name="how-it-works"></a>Funktionsweise

Jede von der Datenbank empfangene Transaktion wird kryptografisch mit SHA-256 gehasht. Die Hashfunktion verwendet den Wert der Transaktion zusammen mit dem Hash der vorherigen Transaktion als Eingabe. (Der Wert enthält Hashes der Zeilen, die in der Transaktion enthalten sind.) Die Funktion verknüpft kryptografisch alle Transaktionen, z. B. eine Blockchain. 

Kryptografische Hashes ([Datenbankhashes](#database-digests)) stellen den Status der Datenbank dar. Sie werden regelmäßig außerhalb von Azure SQL-Datenbank an einem manipulationssicheren Speicherort generiert und gespeichert. Ein Beispiel für einen Speicherort ist das [unveränderliche Speicherfeature von Azure Blob Storage](../../storage/blobs/immutable-storage-overview.md) oder [Azure Confidential Ledger](../../confidential-ledger/index.yml). Datenbankhashes werden später zur Überprüfung der Datenbankintegrität verwendet, indem der Wert des Hashes im Digest mit den berechneten Hashes in der Datenbank verglichen wird. 

Ledgerfunktionen werden in Azure SQL-Datenbank-Tabellen in zwei Formen eingeführt:

- [Aktualisierbare Ledgertabellen](#updatable-ledger-tables), bei denen Sie Zeilen in Ihren Tabellen aktualisieren und löschen können.
- [Ledgertabellen, die nur Anfügevorgänge unterstützen](#append-only-ledger-tables) und somit nur Einfügungen in Ihre Tabellen zulassen.

Sowohl aktualisierbare Ledgertabellen als auch Ledgertabellen, die nur Anfügevorgänge unterstützen, bieten Funktionen für Manipulationsbeweis und digitale Forensik. Es ist wichtig zu verstehen, welche Transaktionen von welchen Benutzern übermittelt wurden, die zu Änderungen an der Datenbank geführt haben, wenn Sie potenzielle Manipulationsereignisse beheben oder gegenüber Dritten nachweisen, dass an das System übermittelte Transaktionen von autorisierten Benutzern stammen. 

Mithilfe des Ledgerfeatures können Benutzer, deren Partner oder Prüfer alle Vorgänge im Verlauf analysieren und potenzielle Manipulationen erkennen. Jeder Zeilenvorgang wird von der ID der Transaktion begleitet, von der er ausgeführt wurde. Mit der ID können Benutzer weitere Informationen zum Zeitpunkt der Transaktion und zur Identität des Benutzers, der sie ausgeführt hat, abrufen. Benutzer können dann die ID mit anderen Vorgängen korrelieren, die von der Transaktion ausgeführt wurden.

Weitere Informationen zu Einschränkungen von Ledgertabellen finden Sie unter [Einschränkungen des Azure SQL-Datenbank-Ledgers](ledger-limits.md).

### <a name="ledger-database"></a>Ledgerdatenbank

In einer Ledgerdatenbank sind alle Benutzerdaten manipulationssicher und in Ledgertabellen gespeichert. Eine Ledgerdatenbank kann nur Ledgertabellen enthalten. Jede Tabelle wird standardmäßig als aktualisierbare Ledgertabelle erstellt. Ledgerdatenbanken bieten eine benutzerfreundliche Lösung für Anwendungen, die die Integrität aller zu schützenden Daten erfordern. 

### <a name="updatable-ledger-tables"></a>Aktualisierbare Ledgertabellen

[Aktualisierbare Ledgertabellen](ledger-updatable-ledger-tables.md) eignen sich ideal für Anwendungsmuster, die eine Aktualisierung von Problemen und Löschungen bei Tabellen in Ihrer Datenbank erwarten, z. B. System of Record(SOR)-Anwendungen. Vorhandene Datenmuster für Ihre Anwendung müssen nicht geändert werden, um Ledgerfunktionen zu aktivieren.  

In aktualisierbaren Ledgertabellen wird der Verlauf von Änderungen an beliebigen Zeilen in Ihrer Datenbank nachverfolgt, wenn es zu Transaktionen kommt, die Updates oder Löschungen durchführen. Eine aktualisierbare Ledgertabelle ist eine Tabelle mit Systemversionsverwaltung, die einen Verweis auf eine andere Tabelle mit einem gespiegelten Schema enthält. 

Die andere Tabelle wird als *Verlaufstabelle* bezeichnet. Das System verwendet diese Tabelle, um bei jeder Aktualisierung oder Löschung einer Zeile in der Ledgertabelle automatisch die vorherige Version der Zeile zu speichern. Die Verlaufstabelle wird bei der Erstellung einer aktualisierbaren Ledgertabelle automatisch erstellt. 

Die Werte in der aktualisierbaren Ledgertabelle und der zugehörigen Verlaufstabelle stellen eine zeitliche Chronik der Werte Ihrer Datenbank bereit. Die aktualisierbare Ledgertabelle wird durch eine vom System generierte Ledgeransicht mit der Verlaufstabelle verknüpft, sodass Sie diese Chronik Ihrer Datenbank problemlos abfragen können.

Weitere Informationen zu aktualisierbaren Ledgertabellen finden Sie unter [Erstellen und Verwenden aktualisierbarer Ledgertabellen](ledger-how-to-updatable-ledger-tables.md).

### <a name="append-only-ledger-tables"></a>Ledgertabellen, die nur Anfügevorgänge unterstützen

[Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-append-only-ledger-tables.md), eignen sich ideal für Anwendungsmuster, in denen nur Einfügungen erfolgen, z. B. Security Information & Event Management(SEIM)-Anwendungen. Ledgertabellen, die nur Anfügevorgänge unterstützen, blockieren Updates und Löschungen auf API-Ebene. Diese Blockierung bietet mehr Manipulationsschutz gegenüber privilegierten Benutzern wie Systemadministratoren und DBAs. 

Da nur Einfügungen in das System zulässig sind, gibt es bei Ledgertabellen, die nur Anfügevorgänge unterstützen, keine entsprechende Verlaufstabelle, weil kein Verlauf erfasst werden muss. Wie bei aktualisierbaren Ledgertabellen liefert eine Ledgeransicht Einblicke in die Transaktion, mit der Zeilen in die Tabelle (die nur Anfügevorgänge unterstützt) eingefügt wurden, und den Benutzer, der die Einfügung ausgeführt hat.

Weitere Informationen zu Ledgertabellen, die nur Anfügevorgänge unterstützen, finden Sie unter [Erstellen und Verwenden von Ledgertabellen, die nur Anfügevorgänge unterstützen](ledger-how-to-append-only-ledger-tables.md).

### <a name="database-ledger"></a>Datenbankledger

Der [Datenbankledger](ledger-database-ledger.md) besteht aus Systemtabellen, in denen die kryptografischen Hashes der im System verarbeiteten Transaktionen gespeichert werden. Da Transaktionen die Einheit der [Unteilbarkeit](/windows/win32/cossdk/acid-properties) für die Datenbank-Engine darstellen, ist dies die im Datenbankledger erfasste Arbeitseinheit. 

Insbesondere beim Commit einer Transaktion wird der SHA-256-Hash aller Zeilen, die von der Transaktion in der Ledgertabelle geändert wurden, als *Transaktionseintrag* im Datenbankledger angefügt. Der Transaktionseintrag enthält auch einige Metadaten für die Transaktion, wie die Identität des Benutzers, der sie ausgeführt hat, und den zugehörigen Commitzeitstempel. 

Alle 30 Sekunden werden die von der Datenbank verarbeiteten Transaktionen mithilfe einer Merkle-Baumdatenstruktur mit SHA-256 zusammengehasht. Das Ergebnis ist ein Stammhash, der einen -Block bildet. Der Block wird dann mithilfe seines Stammhashes zusammen mit dem Stammhash des vorherigen Blocks als Eingabe für die Hashfunktion mit SHA-256 gehasht. Durch diesen Hashvorgang wird eine Blockchain gebildet.

### <a name="database-digests"></a>Datenbankhashes

Der Hash des letzten Blocks im Datenbankledger wird als [Datenbankhash](ledger-digest-management-and-database-verification.md) bezeichnet. Er stellt den Status aller Ledgertabellen in der Datenbank zum Zeitpunkt der Blockgenerierung dar. 

Bei der Bildung eines Blocks wird dessen zugeordneter Datenbankhash veröffentlicht und außerhalb von Azure SQL-Datenbank in einem manipulationssicheren Speicher gespeichert. Da Datenbankhashes den Status der Datenbank zum Zeitpunkt ihrer Generierung darstellen, ist der Schutz der Hashes vor Manipulationen von entscheidender Bedeutung. Ein Angreifer, der Änderungszugriff auf die Hashes hat, hätte folgende Möglichkeiten:

1. Manipulation der Daten in der Datenbank
2. Generieren der Hashes, die die Datenbank mit diesen Änderungen darstellen
3. Ändern der Hashes, sodass sie den aktualisierten Hash der Transaktionen im Block darstellen 

Ledger bietet die Möglichkeit, Hashes automatisch zu generieren und in einem [unveränderlichen Speicher](../../storage/blobs/immutable-storage-overview.md) oder in [Azure Confidential Ledger](../../confidential-ledger/index.yml) zu speichern, um Manipulationen zu verhindern. Alternativ können Benutzer Datenbankhashes manuell generieren und am Speicherort ihrer Wahl speichern. Datenbankhashes werden verwendet, um später zu überprüfen, ob die in Ledgertabellen gespeicherten Daten manipuliert wurden.

### <a name="ledger-verification"></a>Ledgerüberprüfung

Das Ledgerfeature lässt Änderungen des Ledgers durch Benutzer nicht zu. Ein Angreifer oder Systemadministrator, der Kontrolle über den Computer hat, kann jedoch alle Systemüberprüfungen umgehen und die Daten direkt manipulieren. So kann beispielsweise ein Angreifer oder Systemadministrator die Datenbankdateien im Speicher bearbeiten. Ledger kann solche Angriffe nicht verhindern, garantiert aber, dass Manipulationen bei einer Überprüfung der Ledgerdaten erkannt werden. 

Der [Ledgerüberprüfungsprozess](ledger-digest-management-and-database-verification.md) verwendet einen oder mehrere zuvor generierte Datenbankhashes als Eingabe und berechnet die im Datenbankledger gespeicherten Hashes basierend auf dem aktuellen Status der Ledgertabellen neu. Wenn die berechneten Hashes nicht mit den Eingabehashes übereinstimmen, schlägt die Überprüfung fehl, was auf eine Manipulation der Daten hinweist. Ledger meldet dann alle erkannten Inkonsistenzen.

Da die Ledgerüberprüfung alle Hashes für Transaktionen in der Datenbank neu berechnet, kann dies bei Datenbanken mit großen Datenmengen ein ressourcenintensiver Prozess sein. Benutzer sollten die Ledgerüberprüfung nicht kontinuierlich ausführen, sondern nur dann, wenn sie die Integrität ihrer Datenbank überprüfen müssen. 

Idealerweise sollten Benutzer die Ledgerüberprüfung nur dann ausführen, wenn die Organisation, die die Daten hostet, eine Überwachung durchläuft und kryptografische Beweise zur Integrität ihrer Daten für eine andere Partei bereitstellen muss. Zur Senkung der Überprüfungskosten macht das Feature Optionen verfügbar, um einzelne Ledger-Tabellen oder nur eine Teilmenge der Ledgertabellen zu überprüfen.

## <a name="next-steps"></a>Nächste Schritte
 
- [Schnellstart: Erstellen einer SQL-Datenbank mit aktiviertem Ledger](ledger-create-a-single-database-with-ledger-enabled.md)
- [Zugreifen auf die in Azure Confidential Ledger gespeicherten Hashes](ledger-how-to-access-acl-digest.md)
- [Überprüfen einer Ledgertabelle zum Erkennen von Manipulationen](ledger-verify-database.md)
