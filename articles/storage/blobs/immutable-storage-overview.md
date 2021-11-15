---
title: Übersicht über unveränderliche Speicher für Blobdaten
titleSuffix: Azure Storage
description: Azure Storage bietet WORM-Unterstützung (Write Once, Read Many – einmal schreiben, oft lesen) für Blob Storage. Dadurch können Benutzer Daten in einem nicht löschbaren, nicht änderbaren Zustand speichern. Mithilfe zeitbasierter Aufbewahrungsrichtlinien werden Blobdaten für ein angegebenes Intervall in einem WORM-Zustand gespeichert. Richtlinien zur Aufbewahrung für juristische Zwecke bleiben dabei weiterhin so lange wirksam, bis sie explizit gelöscht werden.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 7514531d77b145061d8993ccf75cb78e2976ad20
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131441455"
---
# <a name="store-business-critical-blob-data-with-immutable-storage"></a>Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher

Unveränderlicher Speicher für Azure Blob Storage ermöglicht es Benutzern, unternehmenskritische Daten in einem WORM-Zustand (Write Once, Read Many) zu speichern. Im WORM-Zustand können Daten für einen vom Benutzer angegebenen Zeitraum nicht geändert oder gelöscht werden. Durch die Konfiguration von Unveränderlichkeitsrichtlinien für Blobdaten können Sie Ihre Daten vor Überschreibungen und Löschvorgängen schützen.

Unveränderlicher Speicher für Azure Blob Storage unterstützt zwei Arten von Unveränderlichkeitsrichtlinien:

- **Zeitbasierte Aufbewahrungsrichtlinien**: Mit einer zeitbasierten Aufbewahrungsrichtlinie können Benutzer Richtlinien zum Speichern von Daten für ein angegebenes Intervall festlegen. Wenn eine zeitbasierte Aufbewahrungsrichtlinie festgelegt wird, können Objekte erstellt und gelesen, aber nicht geändert oder gelöscht werden. Nach Ablauf des Aufbewahrungszeitraums können Objekte gelöscht, aber nicht überschrieben werden. Weitere Informationen zu zeitbasierten Aufbewahrungsrichtlinien finden Sie unter [Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten](immutable-time-based-retention-policy-overview.md).

- **Richtlinien zur Aufbewahrung für juristische Zwecke**: Ist die Aufbewahrung für juristische Zwecke festgelegt, werden unveränderliche Daten so lange gespeichert, bis die Richtlinie explizit gelöscht wird. Wenn eine Richtlinie zur Aufbewahrung für juristische Zwecke definiert ist, können Objekte erstellt und gelesen, aber nicht geändert oder gelöscht werden. Weitere Informationen zu Richtlinien zur Aufbewahrung für juristische Zwecke finden Sie unter [Legal holds for immutable blob data](immutable-legal-hold-overview.md) (Aufbewahrung für juristische Zwecke für unveränderliche Blobdaten).

Das folgende Diagramm zeigt, wie zeitbasierte Aufbewahrungsrichtlinien und gesetzliche Aufbewahrungspflichten Schreib-und Löschvorgänge verhindern, solange sie in Kraft sind.

:::image type="content" source="media/immutable-storage-overview/worm-diagram.png" alt-text="Diagramm, das zeigt, wie zeitbasierte Aufbewahrungsrichtlinien und gesetzliche Aufbewahrungspflichten Schreib-und Löschvorgänge verhindern":::

## <a name="about-immutable-storage-for-blobs"></a>Informationen zu unveränderlichem Speicher für Blobs

Unveränderlicher Speicher unterstützt Organisationen im Gesundheitswesen sowie Finanzinstitute und ähnliche Branchen (insbesondere Broker-Dealer-Organisationen) bei der sicheren Speicherung von Daten. Unveränderlicher Speicher kann in allen Szenarios verwendet werden, in denen kritische Daten vor Änderungen oder Löschvorgängen geschützt werden sollen.

Beispiele für typische Anwendungen:

- **Einhaltung gesetzlicher Bestimmungen**: Unveränderlicher Speicher für Azure-Blobspeicher unterstützt Organisationen dabei, SEC 17a-4(f), CFTC 1.31(d), FINRA und weitere Bestimmungen einzuhalten.

- **Sichere Dokumentaufbewahrung**: Mit unveränderlichem Speicher für Blobs wird sichergestellt, dass Daten selbst dann nicht von Benutzern geändert oder gelöscht werden können, wenn diese über Administratorrechte für ein Konto verfügen.

- **Aufbewahrung für juristische Zwecke**: Mit unveränderlichem Speicher für Blobs können Benutzer vertrauliche Informationen, die für Rechtsstreitigkeiten oder die geschäftliche Nutzung wichtig sind, für den gewünschten Zeitraum in einem manipulationsgeschützten Zustand speichern, bis die Aufbewahrungspflicht nicht mehr gilt. Dieses Feature ist nicht allein auf rechtliche Anwendungsfälle beschränkt, sondern kann auch als ereignisbasierter Aufbewahrungsvorgang oder Unternehmenssperre angesehen werden, wenn Daten basierend auf Ereignisauslösern oder Unternehmensrichtlinien geschützt werden müssen.

## <a name="regulatory-compliance"></a>Compliance

Microsoft hat Cohasset Associates, ein führendes, auf Datensatzverwaltung und Information Governance spezialisiertes Unternehmen für unabhängige Bewertungen, damit beauftragt, unveränderlichen Speicher für Blobs und dessen Konformität mit den spezifischen Anforderungen der Finanzdienstleistungsbranche zu bewerten. Cohasset hat bestätigt, dass die Verwendung von unveränderlichem Speicher für Blobs im WORM-Zustand die relevanten Speicheranforderungen der CFTC-Regel 1.31(c)-(d), der FINRA-Regel 4511 und der SEC-Regel 17a-4(f) erfüllt. Microsoft hat die Einhaltung dieser Regeln angestrebt, da sie die weltweit striktesten Richtlinien zur Aufbewahrung von Datensätzen für Finanzinstitute darstellen.

Der Cohasset-Bericht ist im [Microsoft Service Trust Center](https://aka.ms/AzureWormStorage) verfügbar. Ausführliche Informationen zu den Compliancezertifizierungen von Microsoft finden Sie im [Azure Trust Center](https://www.microsoft.com/trustcenter/compliance/compliance-overview). Wenden Sie sich an den [Azure-Support](https://azure.microsoft.com/support/options/), um einen Nachweis der WORM-Unveränderlichkeitskonformität von Microsoft anzufordern.

## <a name="immutability-policy-scope"></a>Geltungsbereich von Unveränderlichkeitsrichtlinien

Unveränderlichkeitsrichtlinien können für eine Blobversion (Vorschau) oder einen Container festgelegt werden. Das Verhalten eines Objekts im Rahmen einer Unveränderlichkeitsrichtlinie hängt vom Geltungsbereich der Richtlinie ab. Weitere Informationen zum Geltungsbereich der einzelnen Typen von Unveränderlichkeitsrichtlinien finden Sie in den folgenden Abschnitten:

- [Geltungsbereich von Richtlinien zur zeitbasierten Aufbewahrung](immutable-time-based-retention-policy-overview.md#time-based-retention-policy-scope)
- [Geltungsbereich von Richtlinien zur Aufbewahrung für juristische Zwecke](immutable-legal-hold-overview.md#legal-hold-scope)

Sie können je nach Bereich sowohl eine Richtlinie für die zeitbasierte Aufbewahrung als auch eine Richtlinie zur Aufbewahrung für juristische Zwecke für eine Ressource (Container oder Blobversion) konfigurieren. In der folgenden Tabelle sind die Unveränderlichkeitsrichtlinien aufgeführt, die für die einzelnen Ressourcenbereiche unterstützt werden:

| `Scope` | Der Container unterstützt Unveränderlichkeitsrichtlinien auf Versionsebene. | Der Container unterstützt keine Unveränderlichkeitsrichtlinien auf Versionsebene. |
|--|--|--|
| Container | Unterstützt eine Standardunveränderlichkeitsrichtlinie auf Versionsebene. Die Standardrichtlinie wird auf alle neuen Versionen angewendet, die nach der Konfiguration im Container erstellt wurden.<br /><br /> Unterstützt keine Aufbewahrung für juristische Zwecke. | Unterstützt eine Unveränderlichkeitsrichtlinie auf Containerebene und eine Richtlinie zur Aufbewahrung für juristische Zwecke. Eine Richtlinie für eine Blobversion kann eine im Container angegebene Standardrichtlinie überschreiben. |
| Blobversion | Unterstützt eine Unveränderlichkeitsrichtlinie auf Versionsebene und eine Richtlinie zur Aufbewahrung für juristische Zwecke. | – |

### <a name="about-the-preview"></a>Informationen zur Vorschau

Die Vorschauversion der Unveränderlichkeitsrichtlinien auf Versionsebene ist in den folgenden Regionen verfügbar:

- Kanada, Mitte
- Kanada, Osten
- Frankreich, Mitte
- Frankreich, Süden

> [!IMPORTANT]
> Unveränderlichkeitsrichtlinien auf Versionsebene befinden sich derzeit in der **VORSCHAU**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="summary-of-immutability-scenarios"></a>Übersicht über Unveränderlichkeitsszenarios

Welchen Schutz eine Unveränderlichkeitsrichtlinie bietet, hängt vom Geltungsbereich der Richtlinie sowie bei einer zeitbasierten Aufbewahrungsrichtlinie davon ab, ob sie gesperrt oder entsperrt und ob sie aktiv oder abgelaufen ist.

### <a name="scenarios-with-version-level-scope"></a>Szenarios mit Geltungsbereich auf Versionsebene

In der folgenden Tabelle sind die Schutzmaßnahmen zusammengefasst, die Unveränderlichkeitsrichtlinien auf Versionsebene bieten.

| Szenario | Unzulässige Vorgänge | Blobschutz | Containerschutz | Kontoschutz |
|--|--|--|--|--|
| Eine Blobversion wird durch eine *aktive* Aufbewahrungsrichtlinie und/oder eine Richtlinie zur Aufbewahrung für juristische Zwecke geschützt | [Delete Blob](/rest/api/storageservices/delete-blob), [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page) und [Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | Die Blobversion kann nicht gelöscht werden. Benutzermetadaten können nicht geschrieben werden. <br /><br /> Beim Überschreiben eines Blobs mit [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) oder [Copy Blob](/rest/api/storageservices/copy-blob) wird eine neue Version erstellt.<sup>2</sup> | Beim Löschen des Containers tritt ein Fehler auf, wenn mindestens ein Blob im Container vorhanden ist (unabhängig davon, ob die Richtlinie gesperrt ist oder nicht). | Beim Löschen des Speicherkontos tritt ein Fehler auf, wenn mindestens ein Container mit aktiviertem unveränderlichen Speicher auf Versionsebene vorhanden ist. |
| Eine Blobversion wird durch eine *abgelaufene* Aufbewahrungsrichtlinie geschützt, und es ist keine Richtlinie zur Aufbewahrung für juristische Zwecke in Kraft | [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page) und [Append Block](/rest/api/storageservices/append-block)<sup>1</sup> | Die Blobversion kann gelöscht werden. Benutzermetadaten können nicht geschrieben werden. <br /><br /> Beim Überschreiben eines Blobs mit [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) oder [Copy Blob](/rest/api/storageservices/copy-blob) wird eine neue Version erstellt<sup>2</sup>. | Beim Löschen des Containers tritt ein Fehler auf, wenn mindestens ein Blob im Container vorhanden ist (unabhängig davon, ob die Richtlinie gesperrt ist oder nicht). | Beim Löschen eines Speicherkontos tritt ein Fehler auf, wenn mindestens ein Container vorhanden ist, der eine Blobversion mit einer gesperrten zeitbasierten Aufbewahrungsrichtlinie aufweist.<br /><br />Entsperrte Richtlinien bieten keinen Schutz vor Löschvorgängen. |

<sup>1</sup> Der Vorgang [Append Block](/rest/api/storageservices/append-block) ist nur bei Richtlinien für die zeitbasierte Aufbewahrung zulässig, bei denen die Eigenschaft **allowProtectedAppendWrites** aktiviert ist. Weitere Informationen finden Sie unter [Zulassen von Schreibvorgängen in geschützten Anfügeblobs](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).
<sup>2</sup> Blobversionen sind für Inhalte immer unveränderlich. Wenn die Versionsverwaltung für das Speicherkonto aktiviert ist, wird durch einen Schreibvorgang in einen Blockblob eine neue Version erstellt. Die einzige Ausnahme ist der Vorgang [Put Block](/rest/api/storageservices/put-block).

### <a name="scenarios-with-container-level-scope"></a>Szenarios mit Geltungsbereich auf Containerebene

In der folgenden Tabelle sind die Schutzmaßnahmen zusammengefasst, die Unveränderlichkeitsrichtlinien auf Containerebene bieten.

| Szenario | Unzulässige Vorgänge | Blobschutz | Containerschutz | Kontoschutz |
|--|--|--|--|--|
| Ein Container wird durch eine *aktive* zeitbasierte Aufbewahrungsrichtlinie mit Geltungsbereich auf Containerebene geschützt, und/oder eine Richtlinie zur Aufbewahrung für juristische Zwecke ist in Kraft | [Delete Blob](/rest/api/storageservices/delete-blob), [Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>, [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page), [Set Blob Properties](/rest/api/storageservices/set-blob-properties), [Snapshot Blob](/rest/api/storageservices/snapshot-blob), [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), [Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | Alle Blobs im Container sind für Inhalte und Benutzermetadaten unveränderlich | Beim Löschen eines Containers tritt ein Fehler auf, wenn eine Richtlinie auf Containerebene wirksam ist. | Beim Löschen eines Speicherkontos tritt ein Fehler auf, wenn ein Container mit mindestens einem Blob vorhanden ist. |
| Ein Container wird durch eine *abgelaufene* zeitbasierte Aufbewahrungsrichtlinie mit Geltungsbereich auf Containerebene geschützt, und es ist keine Richtlinie zur Aufbewahrung für juristische Zwecke in Kraft | [Put Blob](/rest/api/storageservices/put-blob)<sup>1</sup>, [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata), [Put Page](/rest/api/storageservices/put-page), [Set Blob Properties](/rest/api/storageservices/set-blob-properties), [Snapshot Blob](/rest/api/storageservices/snapshot-blob), [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), [Append Block](/rest/api/storageservices/append-block)<sup>2</sup> | Löschvorgänge sind zulässig. Überschreibungsvorgänge sind nicht zulässig. | Beim Löschen des Containers tritt ein Fehler auf, wenn mindestens ein Blob im Container vorhanden ist (unabhängig davon, ob die Richtlinie gesperrt ist oder nicht). | Beim Löschen eines Speicherkontos tritt ein Fehler auf, wenn mindestens ein Container vorhanden ist, der eine gesperrte zeitbasierte Aufbewahrungsrichtlinie aufweist.<br /><br />Entsperrte Richtlinien bieten keinen Schutz vor Löschvorgängen. |

<sup>1</sup> Bei Azure Storage ist der Vorgang [Put Blob](/rest/api/storageservices/put-blob) zum Erstellen eines neuen Blobs zulässig. Nachfolgende Überschreibungsvorgänge für einen vorhandenen Blobpfad eines unveränderlichen Containers sind nicht zulässig.

<sup>2</sup> Der Vorgang [Append Block](/rest/api/storageservices/append-block) ist nur bei Richtlinien für die zeitbasierte Aufbewahrung zulässig, bei denen die Eigenschaft **allowProtectedAppendWrites** aktiviert ist. Weitere Informationen finden Sie unter [Zulassen von Schreibvorgängen in geschützten Anfügeblobs](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Einige Workloads, z. B. [SQL Server-Sicherung über URLs](/sql/relational-databases/backup-restore/sql-server-backup-to-url), erstellen ein Blob und fügen es dann hinzu. Wenn für einen Container eine aktive zeitbasierte Aufbewahrungsrichtlinie oder eine gesetzliche Aufbewahrungspflicht gilt, wird dieses Muster nicht erfolgreich ausgeführt.

## <a name="supported-account-configurations"></a>Unterstützte Kontokonfigurationen

Unveränderlichkeitsrichtlinien werden sowohl für neue als auch für vorhandene Speicherkonten unterstützt. Die folgende Tabelle zeigt, welche Arten von Speicherkonten für die verschiedenen Richtlinientypen unterstützt werden:

| Typ der Unveränderlichkeitsrichtlinie | Geltungsbereich der Richtlinie | Unterstützte Speicherkontotypen | Unterstützt hierarchischen Namespace (Vorschau) |
|--|--|--|--|
| Richtlinie für zeitbasierte Aufbewahrung | Geltungsbereich auf Versionsebene (Vorschau) | General Purpose v2<br />Premium, Blockblob | Nein |
| Richtlinie für zeitbasierte Aufbewahrung | Geltungsbereich auf Containerebene | General Purpose v2<br />Premium, Blockblob<br />Universell V1 (Legacy)<sup>1</sup><br> Blobspeicher (Legacy) | Ja |
| Gesetzliche Aufbewahrungspflicht | Geltungsbereich auf Versionsebene (Vorschau) | General Purpose v2<br />Premium, Blockblob | Nein |
| Gesetzliche Aufbewahrungspflicht | Geltungsbereich auf Containerebene | General Purpose v2<br />Premium, Blockblob<br />Universell V1 (Legacy)<sup>1</sup><br> Blobspeicher (Legacy) | Ja |

<sup>1</sup> Microsoft empfiehlt, für Konten vom Typ „Universell V1“ ein Upgrade auf „Universell V2“ durchzuführen, um von weiteren Features zu profitieren. Informationen zum Upgraden eines vorhandenen Kontos vom Typ „Universell v1“ finden Sie unter [Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“](../common/storage-account-upgrade.md).

### <a name="access-tiers"></a>Zugriffsebenen

Alle Blobzugriffsebenen unterstützen unveränderlichen Speicher. Sie können die Zugriffsebene eines Blobs mit dem Vorgang „Set Blob Tier“ ändern. Weitere Informationen finden Sie unter [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](access-tiers-overview.md).

### <a name="redundancy-configurations"></a>Redundanzkonfigurationen

Alle Redundanzkonfigurationen unterstützen unveränderlichen Speicher. Bei georedundanten Konfigurationen wird kein kundenseitig verwaltetes Failover unterstützt. Weitere Informationen zu Redundanzkonfigurationen finden Sie unter [Azure Storage-Redundanz](../common/storage-redundancy.md).

### <a name="hierarchical-namespace-support"></a>Unterstützung von hierarchischen Namespaces

Die Unterstützung des unveränderlichen Speichers für Konten mit hierarchischem Namespace befindet sich in der Vorschauphase. Informationen zur Registrierung für die Vorschau finden Sie unter [Preview Features on Azure Data Lake Storage](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR2EUNXd_ZNJCq_eDwZGaF5VUOUc3NTNQSUdOTjgzVUlVT1pDTzU4WlRKRy4u) (Previewfunktionen in Azure Data Lake Storage).

Bedenken Sie, dass Sie Blobs nicht umbenennen oder verschieben können, wenn sie sich im unveränderlichen Zustand befinden und für das Konto ein hierarchischer Namespace aktiviert ist. Sowohl der Blobname als auch die Verzeichnisstruktur liefern wichtige Daten auf Containerebene, die nicht mehr geändert werden können, sobald die unveränderliche Richtlinie in Kraft ist.

> [!IMPORTANT]
> Unveränderlicher Speicher für Azure-Blobspeicher in Konten, für die das Feature für hierarchische Namespaces aktiviert ist, befindet sich derzeit in der VORSCHAUPHASE. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="recommended-blob-types"></a>Empfohlene Blobtypen

Microsoft empfiehlt, Unveränderlichkeitsrichtlinien hauptsächlich für Blockblobs und Anfügeblobs zu konfigurieren. Unveränderlichkeitsrichtlinien sollten nicht für Seitenblobs konfiguriert werden, in denen eine virtuelle Festplatte für eine aktive VM gespeichert wird. Der Grund dafür ist, dass Schreibvorgänge auf den Datenträger blockiert werden. Microsoft empfiehlt, die Dokumentation gründlich durchzugehen und Ihre Szenarios zu testen, bevor Sie zeitbasierte Richtlinien sperren.

## <a name="immutable-storage-with-blob-soft-delete"></a>Unveränderlicher Speicher bei vorläufigen Löschvorgängen für Blobs

Wenn das vorläufige Löschen von Blobs für ein Speicherkonto konfiguriert ist, gilt diese Konfiguration unabhängig davon, ob eine Richtlinie zur Aufbewahrung für juristische Zwecke oder eine zeitbasierte Aufbewahrungsrichtlinie in Kraft ist, für alle Blobs innerhalb des Kontos. Microsoft empfiehlt, das vorläufige Löschen als zusätzliche Schutzmaßnahme zu aktivieren, bevor Unveränderlichkeitsrichtlinien angewendet werden.

Wenn Sie das vorläufige Löschen aktivieren und dann eine Unveränderlichkeitsrichtlinie konfigurieren, werden alle Blobs, die bereits vorläufig gelöscht wurden, dauerhaft gelöscht, sobald die Aufbewahrungsrichtlinie für vorläufiges Löschen abgelaufen ist. Vorläufig gelöschte Blobs können während des Aufbewahrungszeitraums für vorläufiges Löschen wiederhergestellt werden. Ein Blob oder eine Version, die noch nicht vorläufig gelöscht wurde, wird durch die Unveränderlichkeitsrichtlinie geschützt und kann erst vorläufig gelöscht werden, nachdem die zeitbasierte Aufbewahrungsrichtlinie abgelaufen ist oder die Richtlinie zur Aufbewahrung für juristische Zwecke entfernt wurde.

## <a name="use-blob-inventory-to-track-immutability-policies"></a>Verwenden des Blobbestands zum Nachverfolgen von Unveränderlichkeitsrichtlinien

Der Azure Storage-Blobbestand bietet eine Übersicht über die Container in Ihren Speicherkonten sowie die Blobs, Momentaufnahmen und Blobversionen innerhalb dieser Container. Sie können den Bericht zum Blobbestand nutzen, um sich über die Attribute von Blobs und Containern zu informieren. Diese Informationen umfassen u. a. die Angabe dazu, ob für eine Ressource eine Unveränderlichkeitsrichtlinie konfiguriert ist.

Wenn Sie den Blobbestand aktivieren, generiert Azure Storage täglich einen Bestandsbericht. Der Bericht enthält eine Übersicht über Ihre Daten, die Sie auch für Geschäfts- und Complianceanforderungen nutzen können.

Weitere Informationen zum Blobbestand finden Sie unter [Verwalten von Blobdaten mit dem Azure Storage-Blobbestand (Vorschau)](blob-inventory.md).

## <a name="pricing"></a>Preise

Für die Verwendung von unveränderlichem Speicher fällt keine zusätzliche Kapazitätsgebühr an. Unveränderliche Daten werden auf die gleiche Weise abgerechnet wie änderbare Daten. Ausführliche Informationen zu Preisen für Azure Blob Storage finden Sie auf der [Seite mit den Preisen für Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Beim Erstellen, Ändern oder Löschen einer zeitbasierten Aufbewahrungsrichtlinie oder einer Richtlinie zur Aufbewahrung für juristische Zwecke für eine Blobversion fällt eine Gebühr für Schreibtransaktionen an.

Wenn Sie Ihre Rechnung nicht begleichen und Ihr Konto über eine aktive zeitbasierte Aufbewahrungsrichtlinie verfügt, werden die normalen Datenaufbewahrungsrichtlinien gemäß den Bestimmungen Ihres Vertrags mit Microsoft angewendet. Weitere Informationen finden Sie unter [Datenverwaltung bei Microsoft](https://www.microsoft.com/trust-center/privacy/data-management).

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp                | Blob Storage (Standardunterstützung)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) |![Ja](../media/icons/yes-icon.png)  <sup>2</sup>              | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |
| Premium-Blockblobs          | ![Ja](../media/icons/yes-icon.png) |![Ja](../media/icons/yes-icon.png)  <sup>2</sup> | ![Ja](../media/icons/yes-icon.png)  <sup>2</sup> |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

<sup>2</sup>    Die Funktion wird auf der Vorschauebene unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht zum Datenschutz](data-protection-overview.md)
- [Zeitbasierte Aufbewahrungsrichtlinien für unveränderliche Blobdaten](immutable-time-based-retention-policy-overview.md)
- [Gesetzliche Aufbewahrungspflichten für unveränderliche Blobdaten](immutable-legal-hold-overview.md)
- [Konfigurieren von Unveränderlichkeitsrichtlinien für Blobversionen (Vorschau)](immutable-policy-configure-version-scope.md)
- [Konfigurieren von Unveränderlichkeitsrichtlinien für Container](immutable-policy-configure-container-scope.md)
