---
title: Grundlegendes zu Zugriff und Berechtigungen
description: Dieser Artikel enthält eine Übersicht über Berechtigungen, Zugriffssteuerung und Sammlungen in Azure Purview. Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) wird in Azure Purview selbst verwaltet. Daher werden in diesem Leitfaden die Grundlagen für den Schutz Ihrer Informationen behandelt.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: de0904275100f0d72dac8e401736ad276f1c928a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401483"
---
# <a name="access-control-in-azure-purview"></a>Zugriffssteuerung in Azure Purview

Azure Purview verwendet **Sammlungen**, um den Zugriff auf seine Quellen, Ressourcen und anderen Artefakte zu organisieren und zu verwalten. In diesem Artikel werden Sammlungen und die Zugriffsverwaltung in Ihrem Azure Purview-Konto beschrieben.

## <a name="collections"></a>Auflistungen

Eine Sammlung ist ein Tool, das in Azure Purview zum Gruppieren von Ressourcen, Quellen und anderen Artefakte in einer Hierarchie eingesetzt wird. Dies dient der verbesserten Auffindbarkeit und der Verwaltung der Zugriffssteuerung. Der gesamte Zugriff auf die Purview-Ressourcen wird über die Sammlungen im Purview-Konto verwaltet.

> [!NOTE]
> Ab dem 8. November 2021 ist ***Insights*** für Datenkuratoren zugänglich. Datenleser haben keinen Zugriff auf Insights.
>
>
## <a name="roles"></a>Rollen

Azure Purview verwendet eine Reihe vordefinierter Rollen, um zu steuern, wer innerhalb des Kontos worauf zugreifen kann. Diese Rollen sind derzeit:

- **Collection admin** (Sammlungsadministrator): Dies ist eine Rolle für Benutzer*innen, die anderen Benutzer*innen in Azure Purview Rollen zuweisen oder Sammlungen verwalten müssen. Sammlungsadministrator*innen können Benutzer*innen zu Rollen für Sammlungen hinzufügen, deren Administrator*innen sie sind. Darüber hinaus können sie Sammlungen und deren Details bearbeiten und untergeordnete Sammlungen hinzufügen.
- **Datenkuratoren** - eine Rolle, die Zugriff auf den Datenkatalog bietet, um Assets zu verwalten, benutzerdefinierte Klassifizierungen zu konfigurieren, Glossarbegriffe einzurichten und Einblicke zu erhalten. Datenkuratoren können Assets erstellen, lesen, ändern, verschieben und löschen. Sie können auch Anmerkungen auf Assets anwenden.
- **Datenleser** - eine Rolle, die nur Lesezugriff auf Datenbestände, Klassifizierungen, Klassifizierungsregeln, Sammlungen und Glossarbegriffe bietet.
- **Datenquellen-Administratoren** - eine Rolle, die es einem Benutzer ermöglicht, Datenquellen und Scans zu verwalten. Ein Benutzer in der Rolle Datenquellen-Admin hat keinen Zugriff auf Azure Purview Studio. Die Kombination dieser Rolle mit den Rollen Datenleser oder Datenkurator in einem beliebigen Sammlungsbereich bietet Zugriff auf Azure Purview Studio.

## <a name="who-should-be-assigned-to-what-role"></a>Wem sollte welche Rolle zugewiesen werden?

|Benutzerszenario|Geeignete Rollen|
|-------------|-----------------|
|Ich muss Ressourcen nur finden können und möchte keine Bearbeitungen durchführen|Datenleseberechtigter|
|Ich muss Informationen zu Ressourcen bearbeiten, entsprechende Klassifizierungen zuweisen, Glossareinträge dafür zuordnen usw.|Datenkurator|
|Ich muss das Glossar bearbeiten oder neue Klassifizierungsdefinitionen einrichten|Datenkurator|
|Ich muss Insights anzeigen, um den Governancestatus meines Datenbestands zu verstehen.|Datenkurator|
|Der Dienstprinzipal meiner Anwendung muss Daten per Pushvorgang an Azure Purview übertragen|Datenkurator|
|Ich muss Überprüfungen über das Studio-Feature von Purview einrichten|Datenkurator **oder** Datenkurator **und** Datenquellenadministrator für die Sammlung, in der die Quelle registriert ist|
|Ich muss einen Dienstprinzipal oder eine Gruppe aktivieren, um Überprüfungen in Azure Purview einzurichten und zu überwachen, ohne dass diese Zugriff auf die Informationen des Katalogs haben. |Datenquellenadministrator|
|Ich muss Benutzern in Azure Purview Rollen zuweisen | Sammlungsadministrator |

:::image type="content" source="./media/catalog-permissions/collection-permissions-roles.png" alt-text="Diagramm mit Purview-Rollen" lightbox="./media/catalog-permissions/collection-permissions-roles.png":::

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>Grundlegendes zur Verwendung der Rollen und Sammlungen von Azure Purview

Die gesamte Zugriffssteuerung wird in den Purview-Sammlungen verwaltet. Die Purview-Sammlungen befinden sich im [Purview Studio](https://web.purview.azure.com/resource/). Öffnen Sie Ihr Purview-Konto im [Azure-Portal](https://portal.azure.com) und wählen Sie auf der Seite „Übersicht“ die Kachel „Purview Studio“ aus. Navigieren Sie von dort aus zur Data Map im linken Menü und wählen Sie dann die Registerkarte „Sammlungen“ aus.

Wenn ein Azure Purview-Konto erstellt wird, beginnt es mit einer Stammsammlung, die den gleichen Namen wie das Purview-Konto trägt. Der Ersteller des Purview-Kontos wird dieser Stammsammlung automatisch als Sammlungsadministrator, Datenquellenadministrator, Datenverwalter und Datenleser hinzugefügt und kann diese Sammlung bearbeiten und verwalten.

Quellen, Ressourcen und Objekte, aber auch andere Sammlungen, können dieser Stammsammlung direkt hinzugefügt werden. Durch das Hinzufügen von Sammlungen erhalten Sie mehr Kontrolle darüber, wer Zugriff auf die Daten in Ihrem Purview-Konto hat.

Alle anderen Benutzer können nur auf Informationen innerhalb des Azure Purview-Kontos zugreifen, wenn sie oder eine Gruppe, in der sie sich befinden, einer der oben genannten Rollen zugewiesen ist. Das bedeutet, dass nach dem Erstellen eines Azure Purview-Kontos niemand außer dem Ersteller auf die APIs zugreifen oder diese nutzen kann, bis er [eine oder mehrere der oben genannten Rollen in einer Sammlung hinzugefügt hat](how-to-create-and-manage-collections.md#add-role-assignments).

Benutzer können einer Sammlung nur durch den Sammlungsadministrator hinzugefügt werden, oder indem ihnen eine entsprechende Berechtigung vererbt wird. Die Berechtigungen einer übergeordneten Sammlung werden automatisch von ihren untergeordneten Sammlungen geerbt. Sie können die [Berechtigungsvererbung jedoch bei jeder Sammlung beliebig einschränken](how-to-create-and-manage-collections.md#restrict-inheritance). Wenn Sie dies tun, werden Berechtigungen nicht mehr an untergeordnete Sammlungen vererbt, sondern müssen direkt hinzugefügt werden. Sammlungsadministratoren, die automatisch von einer übergeordneten Sammlung geerbt wurden, können allerdings nicht entfernt werden.

## <a name="assign-permissions-to-your-users"></a>Zuweisen von Berechtigungen für Ihre Benutzer

Nachdem Sie ein Azure Purview-Konto erstellt haben, müssen Sie als Nächstes Sammlungen erstellen und den Benutzern Rollen innerhalb dieser Sammlungen zuweisen.

> [!NOTE]
> Wenn Sie Ihr Azure Purview-Konto mithilfe eines Dienstprinzipals erstellt haben, um auf Purview Studio zugreifen und Benutzern Berechtigungen zuweisen zu können, müssen Sie einer Benutzersammlung Administratorberechtigungen für die Stammsammlung erteilen.
> Sie können dafür [diesen Azure CLI-Befehl](/cli/azure/purview/account#az_purview_account_add_root_collection_admin) verwenden:
>
>   ```azurecli
>   az purview account add-root-collection-admin --account-name --resource-group [--object-id]
>   ```
>

### <a name="create-collections"></a>Erstellen von Sammlungen

Sammlungen können entsprechend der Quellenstruktur in Ihrem Purview-Konto angepasst werden und fungieren wie organisierte Speicherbehälter für diese Ressourcen. Wenn Sie die benötigten Sammlungen planen, sollten Sie zuerst überlegen, wie Ihre Benutzer auf Informationen zugreifen oder diese durchsuchen. Sind Ihre Quellen nach Abteilungen aufgeteilt? Gibt es spezielle Gruppen innerhalb dieser Abteilungen, die nur auf einige Ressourcen zugreifen müssen? Gibt es bestimmte Quellen, die für alle Benutzer sichtbar sein müssen?

Dadurch können Sie Entscheidungen über die Sammlungen und Untersammlungen treffen, die Sie benötigen, um Ihre Data Map effektiv zu organisieren.

Sie können neue Sammlungen entweder zur Data Map hinzufügen, wo Sie die zugehörige übergeordnete Sammlung aus einem Dropdown-Menü auswählen müssen, oder Sie fügen sie direkt als untergeordnete Sammlung in einer übergeordneten Sammlung hinzu. In der Data Map-Ansicht können Sie alle Ihre Quellen und Ressourcen nach Sammlungen sortiert anzeigen lassen und in der Liste wird die jeweilige Sammlung der Quelle aufgeführt.

Weitere Anweisungen und Informationen finden Sie in unserem [Leitfaden zum Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md).

#### <a name="a-collections-example"></a>Beispiel für eine Sammlung

Nachdem wir nun über grundlegende Kenntnisse zu Sammlungen, Berechtigungen und deren Funktionsweisen verfügen, sehen wir uns ein Beispiel an.

:::image type="content" source="./media/catalog-permissions/collection-example.png" alt-text="Diagramm einer nach Region und Abteilung unterteilten Beispiel-Sammlungshierarchie." border="true":::

Dies ist eine Möglichkeit, wie eine Organisation ihre Daten strukturieren kann: Beginnend mit ihrer Stammsammlung (in diesem Beispiel Contoso) werden die Sammlungen in Regionen und dann in Abteilungen und Unterabteilungen organisiert. Datenquellen und Ressourcen können zu jeder dieser Sammlungen hinzugefügt werden, um die Datenressourcen nach Regionen und Abteilungen aufzuteilen und die Zugriffssteuerung entsprechend zu verwalten. Es gibt eine Unterabteilung, „Umsatz“, mit strengen Zugriffsrichtlinien, d. h. dass Berechtigungen besonders streng verwaltet werden müssen.

Die Rolle [Datenleser](#roles) kann zwar auf Informationen innerhalb des Katalogs zugreifen, aber diese weder verwalten noch bearbeiten. In unserem obigen Beispiel erhalten alle Benutzer in einer Gruppe Leseberechtigungen für Purview-Quellen und -Ressourcen, wenn Sie der Gruppe in der Stammsammlung die Berechtigung „Datenleser“ hinzufügen und eine Vererbung zulassen. Dadurch können diese Ressourcen von allen Benutzern dieser Gruppe durchsucht, aber nicht bearbeitet werden. Durch das [Einschränken der Vererbung](how-to-create-and-manage-collections.md#restrict-inheritance) für die Gruppe „Umsatz“ wird der Zugriff auf diese Ressourcen gesteuert. Benutzer, die Zugriff auf Umsatzinformationen benötigen, können der Umsatz-Sammlung separat hinzugefügt werden.
Ebenso wie bei den Rollen „Datenkurator“ und „Datenquellenadministrator“ gelten die Berechtigungen für diese Gruppen zunächst für die Sammlung, in der sie erstmals zugewiesen wurden, und werden auf untergeordnete Sammlungen übertragen, für die die Vererbung nicht eingeschränkt wurde. Im untenstehenden Diagramm haben wir auf Sammlungsebene Berechtigungen für mehrere Gruppen in der Untersammlung „Nord-, Mittel- und Südamerika“ zugewiesen.

:::image type="content" source="./media/catalog-permissions/collection-permissions-example.png" alt-text="Diagramm einer nach Region und Abteilung unterteilten Beispiel-Sammlungshierarchie, in dem verschiedene Berechtigungen und die Verteilung dargestellt wird." border="true":::

### <a name="add-users-to-roles"></a>Benutzer zu Rollen hinzufügen

Die Rollenzuweisung wird über die Sammlungen verwaltet. Nur ein Benutzer mit der Rolle [Sammlungsadministrator](#roles) kann anderen Benutzern in dieser Sammlung Berechtigungen zuweisen. Wenn neue Berechtigungen hinzugefügt werden müssen, greift ein Sammlungsadministrator oder eine Sammlungsadministratorin auf [Purview Studio](https://web.purview.azure.com/resource/) zu und navigiert dort zu Data Map und dann zur Registerkarte „Sammlungen“. Dort wählt er bzw. sie die Sammlung aus, in der ein Benutzer oder eine Benutzerin hinzugefügt werden muss. Auf der Registerkarte „Rollenzuweisungen“ kann er Benutzer hinzufügen und verwalten, die Berechtigungen benötigen.

Eine vollständige Anleitung finden Sie in unserer [Anleitung zum Hinzufügen von Rollenzuweisungen](how-to-create-and-manage-collections.md#add-role-assignments).

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun über grundlegende Kenntnisse zu Sammlungen und zur Zugriffssteuerung verfügen, können Sie entweder mithilfe der folgenden Anleitungen Sammlungen erstellen und verwalten oder mit der Registrierung von Quellen in Ihrer Purview-Ressource beginnen.

- [Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md)
- [Liste der von Purview unterstützten Datenquellen](purview-connector-overview.md)
