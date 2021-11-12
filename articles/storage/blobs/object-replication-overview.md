---
title: Übersicht über die Objektreplikation
titleSuffix: Azure Storage
description: Die Objektreplikation kopiert Blockblobs asynchron zwischen einem Quellspeicherkonto und einem Zielkonto. Mithilfe der Objektreplikation können Sie die Wartezeit bei Leseanforderungen minimieren, die Effizienz für Computeworkloads erhöhen, die Datenverteilung optimieren und die Kosten minimieren.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1c3f90087c2e016cdfd160c98df28cf3da25ca28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471951"
---
# <a name="object-replication-for-block-blobs"></a>Objektreplikation für Blockblobs

Die Objektreplikation kopiert Blockblobs asynchron zwischen einem Quellspeicherkonto und einem Zielkonto. Folgende Szenarien werden unter anderem von der Objektreplikation unterstützt:

- **Minimieren der Wartezeit.** Die Objektreplikation kann die Wartezeit für Leseanforderungen verringern, indem Clients die Nutzung von Daten aus einer Region ermöglicht wird, die sich in größerer physischer Nähe befindet.
- **Erhöhen der Effizienz für Computeworkloads.** Bei der Objektreplikation können Computeworkloads dieselben Sätze von Blockblobs in verschiedenen Regionen verarbeiten.
- **Optimieren der Datenverteilung.** Sie können Daten an einem einzigen Standort verarbeiten bzw. analysieren und dann nur die Ergebnisse in zusätzliche Regionen replizieren.
- **Optimieren der Kosten.** Nachdem Ihre Daten repliziert wurden, können Sie die Kosten senken, indem Sie sie mithilfe von Lebenszyklus-Verwaltungsrichtlinien auf die Archivebene verschieben.

Das folgende Diagramm zeigt, wie die Objektreplikation Blockblobs aus einem Quellspeicherkonto in einer Region in Zielkonten in zwei verschiedenen Regionen repliziert.

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="Diagramm zur Veranschaulichung der Funktionsweise der Objektreplikation":::

Informationen zum Konfigurieren der Objektreplikation finden Sie unter [Konfigurieren der Objektreplikation](object-replication-configure.md).

## <a name="prerequisites-for-object-replication"></a>Voraussetzungen für die Objektreplikation

Die Objektreplikation erfordert, dass außerdem die folgenden Azure Storage-Funktionen aktiviert sind:

- [Änderungsfeed](storage-blob-change-feed.md): Muss für das Quellkonto aktiviert sein. Informationen zum Aktivieren des Änderungsfeeds finden Sie unter [Aktivieren und Deaktivieren des Änderungsfeeds](storage-blob-change-feed.md#enable-and-disable-the-change-feed).
- [Blobversionsverwaltung](versioning-overview.md): Muss für das Quell- und das Zielkonto aktiviert sein. Informationen zum Aktivieren der Versionsverwaltung finden Sie unter [Aktivieren und Verwalten der Blobversionsverwaltung](versioning-enable.md).

Durch das Aktivieren des Änderungsfeeds und der Blobversionsverwaltung können zusätzliche Kosten entstehen. Weitere Informationen finden Sie auf der Seite [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Die Objektreplikation wird für Speicherkonten vom Typ „Universell V2“ und in der Vorschauversion für Blockblobkonten vom Typ „Premium“ unterstützt. Sowohl Quell- als auch Zielkonten müssen Blockblobkonten des Typs „Universell V2“ oder „Premium“ sein. Die Objektreplikation unterstützt nur Blockblobs. Anfügeblobs und Seitenblobs werden nicht unterstützt.

> [!IMPORTANT]
> Die Objektreplikation für Blockblobkonten vom Typ „Premium“ befindet sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="how-object-replication-works"></a>Funktionsweise der Objektreplikation

Bei der Objektreplikation werden Blockblobs in einem Container entsprechend den von Ihnen konfigurierten Regeln asynchron kopiert. Der Inhalt des Blobs, alle dem Blob zugeordneten Versionen sowie die Metadaten und Eigenschaften des Blobs werden aus dem Quellcontainer in den Zielcontainer kopiert.

> [!IMPORTANT]
> Da Blockblobdaten asynchron repliziert werden, sind das Quell- und das Zielkonto nicht sofort synchron. Zurzeit gibt es keine SLA darüber, wie lange es dauert, Daten in das Zielkonto zu replizieren. Sie können den Replikationsstatus des Quellblobs überprüfen, um zu ermitteln, ob die Replikation abgeschlossen ist. Weitere Informationen finden Sie unter [Überprüfen des Replikationsstatus eines Blobs](object-replication-configure.md#check-the-replication-status-of-a-blob).

### <a name="blob-versioning"></a>Blobversionsverwaltung

Die Objektreplikation erfordert, dass die Blobversionsverwaltung sowohl für das Quell- als auch das Zielkonto aktiviert ist. Wenn ein repliziertes Blob im Quellkonto geändert wird, wird vor der Änderung eine neue Blobversion im Quellkonto erstellt, die den vorherigen Zustand des Blobs widerspiegelt. Die aktuelle Version im Quellkonto spiegelt die neuesten Updates wider. Sowohl die aktuelle Version als auch vorherige Versionen werden in das Zielkonto repliziert. Weitere Informationen zur Auswirkung von Schreibvorgängen auf Blobversionen finden Sie unter [Versionsverwaltung für Schreibvorgänge](versioning-overview.md#versioning-on-write-operations).

Wenn ein Blob im Quellkonto gelöscht wird, wird die aktuelle Version des Blobs zu einer früheren Version, und es gibt keine aktuelle Version mehr. Alle vorhandenen vorherigen Versionen des Blobs bleiben erhalten. Dieser Status wird in das Zielkonto repliziert. Weitere Informationen zur Auswirkung von Löschvorgängen auf Blobversionen finden Sie unter [Versionsverwaltung für Löschvorgänge](versioning-overview.md#versioning-on-delete-operations).

### <a name="snapshots"></a>Momentaufnahmen

Die Objektreplikation unterstützt keine Blobmomentaufnahmen. Momentaufnahmen für ein Blob im Quellkonto werden nicht in das Zielkonto repliziert.

### <a name="blob-tiering"></a>Blobtiering

Die Objektreplikation wird unterstützt, wenn sich das Quell- und das Zielkonto in der Ebene „Heiß“ oder „Kalt“ befinden. Quell- und Zielkonten befinden sich möglicherweise in unterschiedlichen Ebenen. Die Objektreplikation schlägt jedoch ein fehl, wenn ein Blob im Quell- oder Zielkonto in die Archivzugriffsebene verschoben wurde. Weitere Informationen zu Blob-Ebenen finden Sie unter [Zugriffsebenen für Blobdaten „Heiß“, „Kalt“ und „Archiv“](access-tiers-overview.md).

### <a name="immutable-blobs"></a>Unveränderliche Blobs

Unveränderlichkeitsrichtlinien für Azure Blob Storage umfassen zeitbasierte Aufbewahrungsrichtlinien und Aufbewahrungrichtlinien für juristische Zwecke. Wenn eine Unveränderlichkeitsrichtlinie für das Zielkonto wirksam ist, kann die Objektreplikation betroffen sein. Weitere Informationen zu Unveränderlichkeitsrichtlinien finden Sie unter [Speichern unternehmenskritischer Blobdaten mit unveränderlichem Speicher](immutable-storage-overview.md).

Wenn eine Unveränderlichkeitsrichtlinie auf Containerebene für einen Container im Zielkonto in Kraft ist und ein Objekt im Quellcontainer aktualisiert oder gelöscht wird, kann der Vorgang für den Quellcontainer erfolgreich sein, aber die Replikation dieses Vorgangs in den Zielcontainer ist nicht erfolgreich. Weitere Informationen dazu, welche Vorgänge mit einer Unveränderlichkeitsrichtlinie, die einem Container zugeordnet ist, nicht zulässig sind, finden Sie unter [Szenarien mit Bereich auf Containerebene](immutable-storage-overview.md#scenarios-with-container-level-scope).

Wenn eine Unveränderlichkeitsrichtlinie auf Versionsebene für eine Blobversion im Zielkonto in Kraft ist und ein Lösch- oder Aktualisierungsvorgang für die Blobversion im Quellcontainer ausgeführt wird, kann der Vorgang für das Quellobjekt erfolgreich sein, aber die Replikation dieses Vorgangs in das Zielobjekt ist nicht erfolgreich. Weitere Informationen dazu, welche Vorgänge mit einer Unveränderlichkeitsrichtlinie, die einem Container zugeordnet ist, nicht zulässig sind, finden Sie unter [Szenarien mit Bereich auf Versionsebene](immutable-storage-overview.md#scenarios-with-version-level-scope).

## <a name="object-replication-policies-and-rules"></a>Objektreplikationsrichtlinien und -regeln

Wenn Sie die Objektreplikation konfigurieren, erstellen Sie eine Replikationsrichtlinie, in der das Quellspeicherkonto und das Zielkonto angegeben werden. Eine Replikationsrichtlinie enthält mindestens eine Regel, die einen Quellcontainer und einen Zielcontainer angibt sowie anzeigt, welche Blockblobs im Quellcontainer repliziert werden.

Nachdem Sie die Objektreplikation konfiguriert haben, überprüft Azure Storage den Änderungsfeed für das Quellkonto regelmäßig und repliziert asynchron alle Schreib- oder Löschvorgänge in das Zielkonto. Die Replikationswartezeit hängt von der Größe des Blockblobs ab, das repliziert wird.

### <a name="replication-policies"></a>Replikationsrichtlinien

Wenn Sie die Objektreplikation konfigurieren, wird über den Azure Storage-Ressourcenanbieter eine Replikationsrichtlinie für das Zielkonto erstellt. Nachdem die Replikationsrichtlinie erstellt wurde, weist Azure Storage ihr eine Richtlinien-ID zu. Anschließend müssen Sie diese Replikationsrichtlinie dem Quellkonto mithilfe der Richtlinien-ID zuordnen. Die Richtlinien-ID muss für das Quell- und das Zielkonto identisch sein, damit die Replikation stattfinden kann.

Ein Quellkonto kann in maximal zwei Zielkonten repliziert werden, mit einer einzigen Richtlinie für jedes Zielkonto. Ebenso kann ein Konto als Zielkonto für maximal zwei Replikationsrichtlinien dienen.

Die Quell- und Zielkonten dürfen sich in derselben oder in unterschiedlichen Regionen befinden. Sie können sich auch in demselben oder in unterschiedlichen Abonnements befinden. Optional können die Quell- und Zielkonten in unterschiedlichen Azure Active Directory-Mandanten (Azure AD) enthalten sein. Für jedes Paar aus Quell- und Zielkonto darf nur eine Replikationsrichtlinie erstellt werden.

### <a name="replication-rules"></a>Replikationsregeln

Replikationsregeln geben an, wie Azure Storage Blobs aus einem Quellcontainer in einen Zielcontainer repliziert. Sie können bis zu 10 Replikationsregeln für jede Replikationsrichtlinie angeben. Jede Replikationsregel definiert einen einzelnen Quell- und Zielcontainer, und jeder Quell- und Zielcontainer kann nur in einer Regel verwendet werden. Dies bedeutet, dass maximal 10 Quellcontainer und 10 Zielcontainer an einer einzelnen Replikationsrichtlinie teilnehmen können.

Beim Erstellen einer Replikationsregel werden standardmäßig nur neue Blockblobs kopiert, die anschließend dem Quellcontainer hinzugefügt werden. Sie können angeben, dass sowohl neue als auch vorhandene Blockblobs kopiert werden, oder Sie können einen benutzerdefinierten Kopierumfang definieren, der Blockblobs kopiert, die ab einem bestimmten Zeitpunkt erstellt wurden.

Sie können ferner einen oder mehrere Filter als Teil einer Replikationsregel angeben, um Blockblobs anhand eines Präfixes zu filtern. Wenn Sie ein Präfix angeben, werden nur Blobs in den Zielcontainer kopiert, die mit diesem Präfix im Quellcontainer übereinstimmen.

Die Quell- und Zielcontainer müssen beide vorhanden sein, bevor Sie sie in einer Regel angeben können. Nachdem Sie die Replikationsrichtlinie erstellt haben, sind Schreibvorgänge im Zielcontainer nicht zulässig. Alle Versuche, in den Zielcontainer zu schreiben, schlagen mit dem Fehlercode 409 (Konflikt) fehl. Wenn Sie in einen Zielcontainer schreiben möchten, für den eine Replikationsregel konfiguriert ist, müssen Sie entweder die für diesen Container konfigurierte Regel löschen oder die Replikationsrichtlinie entfernen. Lese- und Löschvorgänge im Zielcontainer sind zulässig, wenn die Replikationsrichtlinie aktiv ist.

Sie können den Vorgang [Blobebene festlegen](/rest/api/storageservices/set-blob-tier) für einen Blob im Zielcontainer aufrufen, um ihn auf die Archivebene zu verschieben. Weitere Informationen zur Archivebene finden Sie unter [Zugriffsebenen für Blobdaten „Heiß“, „Kalt“ und „Archiv“](access-tiers-overview.md#archive-access-tier).

## <a name="policy-definition-file"></a>Richtliniendefinitionsdatei

Eine Objektreplikationsrichtlinie wird durch eine JSON-Datei definiert. Sie können die Richtliniendefinitionsdatei aus einer vorhandenen Objektreplikationsrichtlinie abrufen. Sie können auch eine Objektreplikationsrichtlinie erstellen, indem Sie eine Richtliniendefinitionsdatei hochladen.

### <a name="sample-policy-definition-file"></a>Beispiel für eine Richtliniendefinitionsdatei

Im folgenden Beispiel wird eine Replikationsrichtlinie für das Zielkonto mit einer einzigen Regel definiert, die eine Übereinstimmung mit dem Präfix *b* sucht und die Mindesterstellungszeit für Blobs festlegt, die repliziert werden sollen. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "destinationAccount": "/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2021-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

### <a name="specify-full-resource-ids-for-the-source-and-destination-accounts"></a>Angeben vollständiger Ressourcen-IDs für das Quell- und das Zielkonto

Geben Sie beim Erstellen der Richtliniendefinitionsdatei die vollständigen Azure Resource Manager-Ressourcen-IDs für die Einträge **sourceAccount** und **destinationAccount** an, wie im Beispiel im vorherigen Abschnitt gezeigt. Informationen zum Ermitteln der Ressourcen-ID für ein Speicherkonto finden Sie unter [Abrufen der Ressourcen-ID für ein Speicherkonto](../common/storage-account-get-info.md#get-the-resource-id-for-a-storage-account).

Die vollständige Ressourcen-ID weist folgendes Format auf:

```http
/subscriptions/<subscriptionId>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Die Richtliniendefinitionsdatei erforderte zuvor nur den Kontonamen anstelle der vollständigen Ressourcen-ID für das Speicherkonto. Mit der Einführung der Sicherheitseigenschaft **AllowCrossTenantReplication** in Version 2021-02-01 der REST-API des Azure Storage-Ressourcenanbieters müssen Sie jetzt die vollständige Ressourcen-ID für alle Objektreplikationsrichtlinien bereitstellen, die erstellt werden, solange die mandantenübergreifende Replikation für ein von der Replikationsrichtlinie betroffenes Speicherkonto nicht zulässig ist. Azure Storage verwendet die vollständige Ressourcen-ID, um zu überprüfen, ob sich das Quell- und das Zielkonto innerhalb desselben Mandanten befinden. Weitere Informationen zum Verhindern von Richtlinien für die mandantenübergreifende Replikation finden Sie unter [Verhindern der Replikation zwischen Azure AD-Mandanten](#prevent-replication-across-azure-ad-tenants).

Die Angabe des Kontonamens wird weiterhin unterstützt, wenn die mandantenübergreifende Replikation für ein Speicherkonto zulässig ist. Als bewährte Methode empfiehlt Microsoft jedoch, immer die vollständige Ressourcen-ID anzugeben. Alle früheren Versionen der REST-API des Azure Storage-Ressourcenanbieters unterstützen die Verwendung des vollständigen Ressourcen-ID-Pfads in Objektreplikationsrichtlinien.

In der folgenden Tabelle wird beschrieben, was geschieht, wenn Sie eine Replikationsrichtlinie unter Angabe der vollständigen Ressourcen-ID im Vergleich zum Kontonamen in Szenarien erstellen, in denen die mandantenübergreifende Replikation für das Speicherkonto zulässig oder nicht zulässig ist.

| Speicherkontobezeichner in der Richtliniendefinition | Mandantenübergreifende Replikation zulässig | Mandantenübergreifende Replikation unzulässig |
|--|--|--|
| Vollständige Ressourcen-ID | Richtlinien für denselben Mandanten können erstellt werden.<br /><br /> Mandantenübergreifende Richtlinien können erstellt werden. | Richtlinien für denselben Mandanten können erstellt werden.<br /><br /> Mandantenübergreifende Richtlinien können nicht erstellt werden. |
| Nur Kontoname | Richtlinien für denselben Mandanten können erstellt werden.<br /><br /> Mandantenübergreifende Richtlinien können erstellt werden. | Es können weder Richtlinien für denselben Mandanten noch mandantenübergreifende Richtlinien erstellt werden. Ein Fehler tritt auf, weil Azure Storage nicht verifizieren kann, dass sich Quell- und Zielkonten in demselben Mandanten befinden. Der Fehler weist darauf hin, dass Sie in der Richtliniendefinitionsdatei die vollständige Ressourcen-ID für die Einträge **sourceAccount** und **destinationAccount** angeben müssen. |

### <a name="specify-the-policy-and-rule-ids"></a>Angeben der Richtlinien- und Regel-IDs

In der folgenden Tabelle sind die Werte zusammengefasst, die in den einzelnen Szenarien in der Richtliniendefinitionsdatei für **policyId** und **ruleId** verwendet werden müssen.

| Wenn Sie die Richtliniendefinitionsdatei für dieses Konto erstellen... | Legen Sie die Richtlinien-ID auf diesen Wert fest | Legen Sie die Regel-IDs auf diesen Wert fest |
|-|-|-|
| Zielkonto | Zeichenfolgenwert *default*. Azure Storage erstellt den Richtlinien-ID-Wert für Sie. | Eine leere Zeichenfolge. Azure Storage erstellt die Regel-ID-Werte für Sie. |
| Quellkonto | Der Wert der Richtlinien-ID, der zurückgegeben wird, wenn Sie die Richtliniendefinitionsdatei für das Zielkonto herunterladen. | Die Werte der Regel-IDs, die zurückgegeben werden, wenn Sie die Richtliniendefinitionsdatei für das Zielkonto herunterladen. |

## <a name="prevent-replication-across-azure-ad-tenants"></a>Verhindern der Replikation zwischen Azure AD-Mandanten

Ein Azure Active Directory-Mandant (Azure AD) ist eine dedizierte Azure AD-Instanz, die eine Organisation zum Zweck der Identitäts- und Zugriffsverwaltung darstellt. Jedes Azure-Abonnement weist eine Vertrauensstellung mit einem einzigen Azure AD-Mandanten auf. Alle Ressourcen in einem Abonnement, einschließlich Speicherkonten, sind demselben Azure AD-Mandanten zugeordnet. Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).

Standardmäßig kann ein Benutzer mit entsprechenden Berechtigungen die Objektreplikation mit einem Quellspeicherkonto konfigurieren, das sich in einem Azure AD-Mandanten befindet, und einem Zielkonto, das sich in einem anderen Mandanten befindet. Wenn Ihre Sicherheitsrichtlinien erfordern, dass Sie die Objektreplikation auf Speicherkonten innerhalb desselben Mandanten beschränken, können Sie die mandantenübergreifende Replikation verhindern, indem Sie die Sicherheitseigenschaft **AllowCrossTenantReplication** (Vorschau) festlegen. Wenn Sie die mandantenübergreifende Objektreplikation für ein Speicherkonto deaktivieren, erfordert Azure Storage für jede Objektreplikationsrichtlinie, die mit diesem Speicherkonto als Quell- oder Zielkonto konfiguriert ist, dass sich sowohl das Quell- als auch das Zielkonto in demselben Azure AD-Mandanten befinden. Weitere Informationen zum Verhindern der mandantenübergreifenden Objektreplikation finden Sie unter [Verhindern der Objektreplikation zwischen Azure Active Directory-Mandanten](object-replication-prevent-cross-tenant-policies.md).

Um die mandantenübergreifende Objektreplikation für ein Speicherkonto zu verhindern, legen Sie die Eigenschaft **AllowCrossTenantReplication** auf *false* fest. Wenn für das Speicherkonto derzeit keine Richtlinien für die mandantenübergreifende Objektreplikation gelten, verhindert das Festlegen der Eigenschaft **AllowCrossTenantReplication** auf *false* die zukünftige Konfiguration von Richtlinien für die mandantenübergreifende Objektreplikation, bei denen dieses Speicherkonto als Quelle oder Ziel verwendet wird.

Wenn das Speicherkonto derzeit an mindestens einer Richtlinie für die mandantenübergreifende Objektreplikation beteiligt ist, kann die Eigenschaft **AllowCrossTenantReplication** nicht auf *false* festgelegt werden. Sie müssen die vorhandenen mandantenübergreifenden Richtlinien löschen, bevor Sie die mandantenübergreifende Replikation unterbinden können.

Standardmäßig ist die Eigenschaft **AllowCrossTenantReplication** nicht für ein Speicherkonto festgelegt, und ihr Wert lautet *null*, was gleichbedeutend mit *true* ist. Wenn der Wert der Eigenschaft **AllowCrossTenantReplication** für ein Speicherkonto *null* oder *true* lautet, können autorisierte Benutzer Richtlinien zur mandantenübergreifenden Objektreplikation mit diesem Konto als Quelle oder Ziel konfigurieren. Weitere Informationen zum Konfigurieren mandantenübergreifender Richtlinien finden Sie unter [Konfigurieren der Objektreplikation für Blockblobs](object-replication-configure.md).

Mithilfe von Azure Policy können Sie eine Gruppe von Speicherkonten überwachen, um sicherzustellen, dass die Eigenschaft **AllowCrossTenantReplication** festgelegt ist und die mandantenübergreifende Objektreplikation verhindert wird. Sie können Azure Policy auch zum Erzwingen von Governance für eine Gruppe von Speicherkonten verwenden. Beispielsweise können Sie eine Richtlinie mit der Auswirkung „Deny“ erstellen, um einen Benutzer am Erstellen eines Speicherkontos zu hindern, für das die Eigenschaft **AllowCrossTenantReplication** auf *true* festgelegt ist. Ebenso können Sie damit verhindern, dass ein Benutzer ein vorhandenes Speicherkonto ändert, indem er dessen Eigenschaftswert auf *true* festlegt.

## <a name="replication-status"></a>Replikationsstatus

Sie können den Replikationsstatus für ein Blob im Quellkonto überprüfen. Weitere Informationen finden Sie unter [Überprüfen des Replikationsstatus eines Blobs](object-replication-configure.md#check-the-replication-status-of-a-blob).

Wenn der Replikationsstatus für ein Blob im Quellkonto auf einen Fehler hinweist, untersuchen Sie die folgenden möglichen Ursachen:

- Stellen Sie sicher, dass die Objektreplikationsrichtlinie im Zielkonto konfiguriert ist.
- Überprüfen Sie, ob der Zielcontainer noch vorhanden ist.
- Wenn das Quellblob im Rahmen eines Schreibvorgangs mit einem vom Kunden bereitgestellten Schlüssel verschlüsselt wurde, tritt bei der Objektreplikation ein Fehler auf. Weitere Informationen zu vom Kunden bereitgestellten Schlüsseln finden Sie unter [Angeben eines Verschlüsselungsschlüssels bei Stellen einer Anforderung für Blob Storage](encryption-customer-provided-keys.md).

## <a name="feature-support"></a>Featureunterstützung

In der folgenden Tabelle wird gezeigt, wie dieses Feature in Ihrem Konto unterstützt wird und welche Auswirkungen die Aktivierung bestimmter Funktionen auf die Unterstützung hat.

| Speicherkontotyp                | Blob Storage (Standardunterstützung)   | Data Lake Storage Gen2 <sup>1</sup>                        | NFS 3.0 <sup>1</sup>
|-----------------------------|---------------------------------|------------------------------------|--------------------------------------------------|
| Standard, Universell V2 | ![Ja](../media/icons/yes-icon.png) |![Nein](../media/icons/no-icon.png)              | ![Nein](../media/icons/no-icon.png) |
| Premium-Blockblobs          | ![Ja](../media/icons/yes-icon.png) |![Nein](../media/icons/no-icon.png)              | ![Nein](../media/icons/no-icon.png) |

<sup>1</sup>    Für Data Lake Storage Gen2 und das NFS 3.0-Protokoll (Network File System) ist ein Speicherkonto mit aktiviertem hierarchischem Namespace erforderlich.

## <a name="billing"></a>Abrechnung

Die Objektreplikation verursacht zusätzliche Kosten für Lese- und Schreibtransaktionen in den Quell- und Zielkonten sowie Gebühren für ausgehenden Datenverkehr für die Replikation von Daten aus dem Quellkonto in das Zielkonto und Lesegebühren für den Prozessänderungsfeed.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren der Objektreplikation](object-replication-configure.md)
- [Verhindern der Objektreplikation zwischen Azure Active Directory-Mandanten](object-replication-prevent-cross-tenant-policies.md)
- [Blobversionsverwaltung](versioning-overview.md)
- [Unterstützung für Änderungsfeeds in Azure Blob Storage](storage-blob-change-feed.md)
