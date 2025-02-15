---
title: Verschlüsselung in Azure Synapse Analytics
description: In diesem Artikel wird die Verschlüsselung in Azure Synapse Analytics erläutert.
author: meenalsri
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 07/20/2021
ms.author: mesrivas
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 55585013df7c0fde6cb2eea1a9598d494cb4b2cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128583635"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Verschlüsselung für Azure Synapse Analytics-Arbeitsbereiche

In diesem Artikel wird Folgendes beschrieben:
* Verschlüsselung ruhender Daten in Synapse Analytics-Arbeitsbereichen
* Konfiguration von Synapse-Arbeitsbereichen zum Aktivieren der Verschlüsselung mit einem vom Kunden verwalteten Schlüssel
* Verwalten von Schlüsseln, die zum Verschlüsseln von Daten in Arbeitsbereichen verwendet werden

## <a name="encryption-of-data-at-rest"></a>Verschlüsselung für ruhende Daten

Mit einer Komplettlösung für die Verschlüsselung ruhender Daten wird sichergestellt, dass die Daten niemals in unverschlüsselter Form gespeichert werden. Die doppelte Verschlüsselung ruhender Daten verringert Bedrohungen durch zwei separate Verschlüsselungsebenen, um vor Kompromittierungen einer einzelnen Ebene zu schützen. Azure Synapse Analytics bietet eine zweite Verschlüsselungsebene für die Daten in Ihrem Arbeitsbereich mit einem vom Kunden verwalteten Schlüssel. Dieser Schlüssel wird in Ihrer [Azure Key Vault](../../key-vault/general/overview.md)-Instanz geschützt, sodass Sie den Besitz der Schlüsselverwaltung und -rotation übernehmen können.

Die erste Verschlüsselungsebene für Azure-Dienste wird mit plattformseitig verwalteten Schlüsseln aktiviert. Standardmäßig werden Azure-Datenträger und Daten in Azure Storage-Konten automatisch im Ruhezustand verschlüsselt. Weitere Informationen zur Verwendung der Verschlüsselung in Microsoft Azure finden Sie in der [Übersicht über die Azure-Verschlüsselung](../../security/fundamentals/encryption-overview.md).

## <a name="azure-synapse-encryption"></a>Azure Synapse-Verschlüsselung

Dieser Abschnitt vermittelt Ihnen ein besseres Verständnis dafür, wie die Verschlüsselung mit einem vom Kunden verwalteten Schlüssel in Synapse-Arbeitsbereichen aktiviert und erzwungen wird. Bei dieser Verschlüsselung werden vorhandene Schlüssel oder neue Schlüssel verwendet, die in Azure Key Vault generiert wurden. Alle Daten in einem Arbeitsbereich werden mit einem einzigen Schlüssel verschlüsselt. Synapse-Arbeitsbereiche unterstützen RSH-Schlüssel mit 2048 und 3072 Bytes sowie RSA-HSM-Schlüssel.

> [!NOTE]
> Synapse-Arbeitsbereiche unterstützen nicht die Verwendung von EC-, EC-HSM- und oct-HSM-Schlüsseln für die Verschlüsselung. 

Die Daten in den folgenden Synapse-Komponenten werden mit dem vom Kunden verwalteten Schlüssel verschlüsselt, der auf der Arbeitsbereichsebene konfiguriert wurde:
* SQL-Pools
 * Dedizierte SQL-Pools
 * Serverlose SQL-Pools
* Apache Spark-Pools
* Integration Runtimes, Pipelines und Datasets von Azure Data Factory.

## <a name="workspace-encryption-configuration"></a>Konfiguration der Arbeitsbereichsverschlüsselung

Bei der Erstellung können Arbeitsbereiche so konfiguriert werden, dass die doppelte Verschlüsselung mit einem vom Kunden verwalteten Schlüssel aktiviert wird. Wenn Sie einen neuen Arbeitsbereich erstellen, aktivieren Sie auf der Registerkarte „Sicherheit“ die Mehrfachverschlüsselung mit einem kundenseitig verwalteten Schlüssel. Sie können einen Schlüsselbezeichner-URI eingeben oder aus einer Liste von Schlüsseltresoren auswählen, die sich in **derselben Region** befinden wie der Arbeitsbereich. Für den Schlüsseltresor selbst muss der **Löschschutz aktiviert** sein.

> [!IMPORTANT]
> Die Konfigurationseinstellung für die doppelte Verschlüsselung kann nach der Einstellung des Arbeitsbereichs nicht mehr geändert werden.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="In diesem Diagramm sehen Sie die Option, die ausgewählt werden muss, um einen Arbeitsbereich für die doppelte Verschlüsselung mit einem vom Kunden verwalteten Schlüssel zu aktivieren." lightbox="./media/workspaces-encryption/workspaces-encryption.png":::

### <a name="key-access-and-workspace-activation"></a>Schlüsselzugriff und Arbeitsbereichsaktivierung

Beim Azure Synapse-Verschlüsselungsmodell mit vom Kunden verwalteten Schlüsseln greift der Arbeitsbereich auf die Schlüssel in Azure Key Vault zu, um nach Bedarf Ver- und Entschlüsselungsvorgänge auszuführen. Die Schlüssel werden dem Arbeitsbereich entweder über eine Zugriffsrichtlinie oder den [Azure Key Vault-RBAC-Zugriff](../../key-vault/general/rbac-guide.md) zugänglich gemacht. Wenn Sie Berechtigungen über eine Azure Key Vault-Zugriffsrichtlinie erteilen, wählen Sie während der Richtlinienerstellung die Option [Nur Anwendung](../../key-vault/general/security-features.md#key-vault-authentication-options) aus. (Wählen Sie die verwaltete Identität des Arbeitsbereichs aus, und fügen Sie diese nicht als autorisierte Anwendung hinzu.)

 Der verwalteten Identität des Arbeitsbereichs müssen die erforderlichen Berechtigungen für den Schlüsseltresor erteilt werden, bevor der Arbeitsbereich aktiviert werden kann. Durch diesen phasenbasierten Ansatz für die Arbeitsbereichsaktivierung wird sichergestellt, dass die Daten im Arbeitsbereich mit dem vom Kunden verwalteten Schlüssel verschlüsselt werden. Beachten Sie, dass die Verschlüsselung für dedizierte SQL-Pools aktiviert oder deaktiviert werden kann. Nicht jeder Pool ist standardmäßig für die Verschlüsselung aktiviert.

#### <a name="using-a-user-assigned-managed-identity"></a>Verwenden einer benutzerseitig zugewiesenen verwalteten Identität
Arbeitsbereiche können so konfiguriert werden, dass sie eine [benutzerseitig zugewiesene verwaltete Identität](../../active-directory/managed-identities-azure-resources/overview.md) verwenden, um auf Ihren kundenseitig verwalteten Schlüssel zuzugreifen, der in Azure Key Vault gespeichert ist. Konfigurieren Sie eine benutzerseitig zugewiesene verwaltete Identität, um eine Aktivierung Ihres Azure Synapse-Arbeitsbereichs in mehreren Stufen zu vermeiden, wenn Sie die Mehrfachverschlüsselung mit kundenseitig verwalteten Schlüsseln verwenden. Die integrierte Rolle „Mitwirkender für verwaltete Identitäten“ ist erforderlich, um einem Azure Synapse-Arbeitsbereich eine benutzerseitig zugewiesene verwaltete Identität zuzuweisen.
> [!NOTE]
> Eine benutzerseitig zugewiesene verwaltete Identität kann nicht für den Zugriff auf kundenseitig verwaltete Schlüssel konfiguriert werden, wenn Azure Key Vault sich hinter einer Firewall befindet.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption-uami.png" alt-text="Dieses Diagramm zeigt die Option, die ausgewählt werden muss, um einem Arbeitsbereich die Verwendung einer benutzerseitig zugewiesenen verwalteten Identität für die Mehrfachverschlüsselung mit einem kundenseitig verwalteten Schlüssel zu ermöglichen." lightbox="./media/workspaces-encryption/workspaces-encryption-uami.png":::


#### <a name="permissions"></a>Berechtigungen

Um ruhende Daten verschlüsseln oder entschlüsseln zu können, muss die verwaltete Identität über die folgenden Berechtigungen verfügen:
* WrapKey (um beim Erstellen eines neuen Schlüssels einen Schlüssel in Key Vault einzufügen)
* UnwrapKey (um den Schlüssel für die Entschlüsselung abzurufen)
* Get (um den öffentlichen Teil eines Schlüssels zu lesen)

#### <a name="workspace-activation"></a>Arbeitsbereichsaktivierung

Wenn Sie während der Arbeitsbereichserstellung keine benutzerseitig zugewiesene verwaltete Identität für den Zugriff auf kundenseitig verwaltete Schlüssel konfigurieren, verbleibt Ihr Arbeitsbereich im Status „Ausstehend“, bis die Aktivierung erfolgreich ist. Der Arbeitsbereich muss aktiviert werden, damit Sie alle Funktionen vollständig nutzen können. Beispielsweise können Sie erst nach erfolgreicher Aktivierung einen neuen dedizierten SQL-Pool erstellen. Gewähren Sie der verwalteten Identität des Arbeitsbereichs Zugriff auf den Schlüsseltresor, und klicken Sie im Azure-Portal auf dem Arbeitsbereichsbanner auf den Aktivierungslink. Nachdem die Aktivierung erfolgreich abgeschlossen wurde, kann Ihr Arbeitsbereich mit der Gewissheit verwendet werden, dass alle darin enthaltenen Daten durch Ihren vom Kunden verwalteten Schlüssel geschützt sind. Wie bereits erwähnt, muss für den Schlüsseltresor der Löschschutz aktiviert sein, damit die Aktivierung erfolgreich durchgeführt werden kann.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="In diesem Diagramm ist das Banner mit dem Aktivierungslink für den Arbeitsbereich dargestellt." lightbox="./media/workspaces-encryption/workspace-activation.png":::


### <a name="manage-the-workspace-customer-managed-key"></a>Verwalten des vom Kunden verwalteten Schlüssels für den Arbeitsbereich 

Sie können den vom Kunden verwalteten Schlüssel, der zum Verschlüsseln von Daten verwendet wird, im Azure-Portal auf der Seite **Verschlüsselung** ändern. Hier können Sie auch einen neuen Schlüssel mithilfe eines Schlüsselbezeichners auswählen oder einen Schlüssel aus Schlüsseltresoren auswählen, auf die Sie Zugriff haben und die sich in derselben Region befinden wie der Arbeitsbereich. Wenn Sie einen Schlüssel in einem anderen Schlüsseltresor als den zuvor verwendeten auswählen, erteilen Sie der verwalteten Identität des Arbeitsbereichs die Berechtigungen zum Abrufen, Packen und Entpacken von Schlüsseln für den neuen Schlüsseltresor. Der Arbeitsbereich überprüft den Zugriff auf den neuen Schlüsseltresor, und alle Daten im Arbeitsbereich werden mit dem neuen Schlüssel erneut verschlüsselt.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="In diesem Diagramm sehen Sie den Bereich „Verschlüsselung“ für den Arbeitsbereich im Azure-Portal." lightbox="./media/workspaces-encryption/workspace-encryption-management.png":::

>[!IMPORTANT]
>Wenn Sie den Verschlüsselungsschlüssel eines Arbeitsbereichs ändern, behalten Sie den Schlüssel bei, bis Sie ihn im Arbeitsbereich durch einen neuen Schlüssel ersetzen. Dies ermöglicht das Entschlüsseln von Daten mit dem alten Schlüssel, bevor sie mit dem neuen Schlüssel erneut verschlüsselt werden.

Azure Key Vault-Richtlinien für die automatische, regelmäßige Rotation von Schlüsseln oder Aktionen für die Schlüssel können zur Erstellung neuer Schlüsselversionen führen. Sie können auswählen, dass alle Daten im Arbeitsbereich mit der aktuellen Version des aktiven Schlüssels erneut verschlüsselt werden. Wenn die Daten erneut verschlüsselt werden sollen, ändern Sie im Azure-Portal den Schlüssel in einen temporären Schlüssel, und wechseln Sie dann zurück zu dem Schlüssel, den Sie für die Verschlüsselung verwenden möchten. Wenn Sie beispielsweise die Datenverschlüsselung mit der neuesten Version des aktiven Schlüssels „Schlüssel1“ aktualisieren möchten, ändern Sie den vom Kunden verwalteten Schlüssel für den Arbeitsbereich in den temporären Schlüssel „Schlüssel2“. Warten Sie, bis die Verschlüsselung mit Schlüssel2 abgeschlossen ist. Ändern Sie dann den vom Kunden verwalteten Schlüssel für den Arbeitsbereich wieder in Schlüssel1. Die Daten im Arbeitsbereich werden dann mit der neuesten Version von Schlüssel1 erneut verschlüsselt.

> [!NOTE]
> Das Azure Synapse Analytics-Team arbeitet aktiv daran, Funktionen hinzuzufügen, um Daten automatisch erneut zu verschlüsseln, wenn neue Schlüsselversionen erstellt werden. Bis diese Funktionalität verfügbar ist, erzwingen Sie zur Gewährleistung der Konsistenz in Ihrem Arbeitsbereich die erneute Verschlüsselung der Daten mit dem oben beschriebenen Vorgang.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>SQL Transparent Data Encryption mit dienstseitig verwalteten Schlüsseln

SQL Transparent Data Encryption (TDE) ist für dedizierte SQL-Pools in Arbeitsbereichen verfügbar, die *nicht* für die doppelte Verschlüsselung aktiviert sind. Bei diesem Typ von Arbeitsbereich wird ein vom Dienst verwalteter Schlüssel verwendet, um die doppelte Verschlüsselung der Daten in den dedizierten SQL-Pools zu ermöglichen. TDE mit dem vom Dienst verwalteten Schlüssel kann für einzelne dedizierte SQL-Pools aktiviert oder deaktiviert werden.

## <a name="next-steps"></a>Nächste Schritte

[Verwenden integrierter Azure Policy-Richtliniendefinitionen zum Implementieren von Verschlüsselungsschutz für Synapse-Arbeitsbereiche](../policy-reference.md)

[Erstellen eines Azure-Schlüsseltresors und eines Schlüssels mithilfe einer ARM-Vorlage](../../key-vault/keys/quick-create-template.md)
