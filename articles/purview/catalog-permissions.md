---
title: Katalogberechtigungen (Vorschauversion)
description: Dieser Artikel enthält eine Übersicht über die Konfiguration der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) in Azure Purview.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: 131f30c523c3a99bcbd3494833e060ffd3b5ff16
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444816"
---
# <a name="access-control-in-azure-purview"></a>Zugriffssteuerung in Azure Purview

Azure Purview verwendet **Sammlungen**, um den Zugriff auf seine Quellen, Ressourcen und anderen Artefakte zu organisieren und zu verwalten. In diesem Artikel werden Sammlungen und die Zugriffsverwaltung in Ihrem Azure Purview-Konto beschrieben.

> [!NOTE]
> Derzeit gelten diese Informationen nur für Purview-Konten, die **am oder nach dem 18. August 2021** erstellt wurden. Vor dem 18. August erstellte Instanzen können Sammlungen erstellen, aber keine Berechtigungen über diese Sammlungen verwalten. Informationen über die Zugriffssteuerung für Purview-Instanzen, die vor dem 18. August erstellt wurden, finden Sie im [**Leitfaden zu Legacyberechtigungen**](#legacy-permission-guide) am Ende dieses Artikels.

## <a name="collections"></a>Auflistungen

Eine Sammlung ist ein Tool, das in Azure Purview zum Gruppieren von Ressourcen, Quellen und anderen Artefakte in einer Hierarchie eingesetzt wird. Dies dient der verbesserten Auffindbarkeit und der Verwaltung der Zugriffssteuerung. Der gesamte Zugriff auf die Purview-Ressourcen wird über die Sammlungen im Purview-Konto verwaltet.

## <a name="roles"></a>Rollen

Azure Purview verwendet eine Reihe vordefinierter Rollen, um zu steuern, wer innerhalb des Kontos worauf zugreifen kann. Diese Rollen sind derzeit:

- **Sammlungsadministratoren**: Sie können Purview-Sammlungen und deren Details bearbeiten und untergeordnete Sammlungen hinzufügen. Darüber hinaus können sie Benutzer in Sammlungen, in denen sie Administratoren sind, zu anderen Purview-Rollen hinzufügen.
- **Datenquellenadministratoren**: Sie können Datenquellen und Datenüberprüfungen verwalten.
- **Datenkuratoren**: Sie können Katalogdatenassets erstellen, lesen, ändern und löschen und Beziehungen zwischen Assets herstellen.
- **Datenleseberechtigte**: Sie können auf Katalogdatenassets zugreifen, diese aber nicht ändern.

## <a name="who-should-be-assigned-to-what-role"></a>Wem sollte welche Rolle zugewiesen werden?

|Benutzerszenario|Geeignete Rollen|
|-------------|-----------------|
|Ich muss Ressourcen nur finden können und möchte keine Bearbeitungen durchführen|Datenleseberechtigter|
|Ich muss Informationen zu Ressourcen bearbeiten, entsprechende Klassifizierungen zuweisen, Glossareinträge dafür zuordnen usw.|Datenkurator|
|Ich muss das Glossar bearbeiten oder neue Klassifizierungsdefinitionen einrichten|Datenkurator|
|Der Dienstprinzipal meiner Anwendung muss Daten per Pushvorgang an Azure Purview übertragen|Datenkurator|
|Ich muss Überprüfungen über das Studio-Feature von Purview einrichten|Datenquellenadministrator und mindestens Datenleser **oder** Datenkurator für die Sammlung, in der die Quelle registriert ist.|
|Ich muss einen Dienstprinzipal oder eine Gruppe aktivieren, um Überprüfungen in Azure Purview einzurichten und zu überwachen, ohne dass diese Zugriff auf die Informationen des Katalogs haben. |Datenquellenadministrator|
|Ich muss Benutzern in Azure Purview Rollen zuweisen | Sammlungsadministrator |

## <a name="understand-how-to-use-azure-purviews-roles-and-collections"></a>Grundlegendes zur Verwendung der Rollen und Sammlungen von Azure Purview

Die gesamte Zugriffssteuerung wird in den Purview-Sammlungen verwaltet. Die Purview-Sammlungen befinden sich im [Purview Studio](use-purview-studio.md). Öffnen Sie Ihr Purview-Konto im [Azure-Portal](https://portal.azure.com) und wählen Sie auf der Seite „Übersicht“ die Kachel „Purview Studio“ aus. Navigieren Sie von dort aus zur Data Map im linken Menü und wählen Sie dann die Registerkarte „Sammlungen“ aus.

Wenn ein Azure Purview-Konto erstellt wird, beginnt es mit einer Stammsammlung, die den gleichen Namen wie das Purview-Konto trägt. Der Ersteller des Purview-Kontos wird dieser Stammsammlung automatisch als Sammlungsadministrator, Datenquellenadministrator, Datenverwalter und Datenleser hinzugefügt und kann diese Sammlung bearbeiten und verwalten.

Quellen, Ressourcen und Objekte, aber auch andere Sammlungen, können dieser Stammsammlung direkt hinzugefügt werden. Durch das Hinzufügen von Sammlungen erhalten Sie mehr Kontrolle darüber, wer Zugriff auf die Daten in Ihrem Purview-Konto hat.

Alle anderen Benutzer können nur auf Informationen innerhalb des Azure Purview-Kontos zugreifen, wenn sie oder eine Gruppe, in der sie sich befinden, einer der oben genannten Rollen zugewiesen ist. Das bedeutet, dass nach dem Erstellen eines Azure Purview-Kontos niemand außer dem Ersteller auf die APIs zugreifen oder diese nutzen kann, bis er [eine oder mehrere der oben genannten Rollen in einer Sammlung hinzugefügt hat](how-to-create-and-manage-collections.md#add-role-assignments).

Benutzer können einer Sammlung nur durch den Sammlungsadministrator hinzugefügt werden, oder indem ihnen eine entsprechende Berechtigung vererbt wird. Die Berechtigungen einer übergeordneten Sammlung werden automatisch von ihren untergeordneten Sammlungen geerbt. Sie können die [Berechtigungsvererbung jedoch bei jeder Sammlung beliebig einschränken](how-to-create-and-manage-collections.md#restrict-inheritance). Wenn Sie dies tun, werden Berechtigungen nicht mehr an untergeordnete Sammlungen vererbt, sondern müssen direkt hinzugefügt werden. Sammlungsadministratoren, die automatisch von einer übergeordneten Sammlung geerbt wurden, können allerdings nicht entfernt werden.

## <a name="assign-permissions-to-your-users"></a>Zuweisen von Berechtigungen für Ihre Benutzer

Nachdem Sie ein Azure Purview-Konto erstellt haben, müssen Sie als Nächstes Sammlungen erstellen und den Benutzern Rollen innerhalb dieser Sammlungen zuweisen.

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

Die Rollenzuweisung wird über die Sammlungen verwaltet. Nur ein Benutzer mit der Rolle [Sammlungsadministrator](#roles) kann anderen Benutzern in dieser Sammlung Berechtigungen zuweisen. Wenn neue Berechtigungen hinzugefügt werden müssen, greift ein Sammlungsadministrator auf Purview Studio zu, navigiert zur Data Map und dann zur Registerkarte „Sammlungen“. Dort wählt er die Sammlung aus, in der ein Benutzer hinzugefügt werden muss. Auf der Registerkarte „Rollenzuweisungen“ kann er Benutzer hinzufügen und verwalten, die Berechtigungen benötigen.

Eine vollständige Anleitung finden Sie in unserer [Anleitung zum Hinzufügen von Rollenzuweisungen](how-to-create-and-manage-collections.md#add-role-assignments).

## <a name="legacy-permission-guide"></a>Leitfaden zu Legacyberechtigungen

> [!NOTE]
> Dieser Leitfaden für Legacysammlungen gilt nur für Purview-Konten, die vor dem 18. August 2021 erstellt wurden. Für Instanzen, die nach diesem Zeitpunkt erstellt wurden, sollten Sie dem obigen Leitfaden folgen.

In diesem Artikel wird beschrieben, wie die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) für Purview-Ressourcen, die vor dem 18. August erstellt wurden, auf der [Datenebene](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane) von Azure Purview implementiert wird.

> [!IMPORTANT]
> Der Prinzipal, der ein Purview-Konto erstellt hat, erhält automatisch alle Datenebenenberechtigungen. Dies gilt unabhängig davon, welche Datenebenenrollen für den Prinzipal ggf. bereits vorhanden sind. Damit andere Benutzer Schritte in Azure Purview ausführen können, müssen sie mindestens über eine der vordefinierten Datenebenenrollen verfügen.

### <a name="azure-purviews-pre-defined-legacy-data-plane-roles"></a>Vordefinierte Legacy-Datenebenenrollen von Azure Purview

In Azure Purview wird eine Gruppe von vordefinierten Datenebenenrollen definiert, mit denen gesteuert werden kann, worauf Benutzer in Azure Purview zugreifen können. Die folgenden Rollen sind vorhanden:

* **Rolle „Datenleseberechtigter für Purview“** : Verfügt über Zugriff auf das Purview-Portal und kann den gesamten Inhalt in Azure Purview lesen, mit Ausnahme von Scanbindungen.
* **Rolle „Azure Purview-Datenkurator“** : Verfügt über Zugriff auf das Purview-Portal und kann den gesamten Inhalt in Azure Purview lesen (mit Ausnahme von Scanbindungen), Informationen zu Ressourcen sowie Klassifizierungsdefinitionen und Glossarbegriffe bearbeiten und Klassifizierungen und Glossarbegriffe auf Ressourcen anwenden.
* **Rolle „Datenquellenadministrator für Purview“** : Verfügt nicht über Zugriff auf das Purview-Portal (der Benutzer muss auch über die Rolle „Datenleser“ oder „Datenkurator“ verfügen) und kann alle Aspekte der Datenüberprüfung in Azure Purview verwalten, verfügt aber weder über Schreib- noch über Lesezugriff auf den Inhalt in Azure Purview, der über das Überprüfen hinausgeht.

### <a name="understand-how-to-use-azure-purviews-legacy-data-plane-roles"></a>Grundlegendes zur Verwendung der Legacy-Datenebenenrollen von Azure Purview

Bei der Erstellung eines Azure Purview-Kontos wird der Ersteller so behandelt, als ob er sowohl über die Rolle „Datenkurator für Purview“ als auch die Rolle „Datenquellenadministrator für Purview“ verfügt. Im Rollenspeicher ist der Kontoersteller diesen Rollen jedoch nicht zugewiesen. Azure Purview erkennt, dass der Prinzipal der Ersteller des Kontos ist, und stellt diese Funktionen auf der Grundlage ihrer Identität zur Verfügung.

Alle anderen Benutzer können das Azure Purview-Konto nur verwenden, wenn sie mindestens einer dieser Rollen zugewiesen werden. Dies bedeutet Folgendes: Nach der Erstellung eines Azure Purview-Kontos kann ausschließlich der Ersteller auf das Konto zugreifen und dessen APIs verwenden, bis die Benutzer mindestens einer der vorab definierten Rollen zugewiesen wurden.

Beachten Sie, dass die Rolle „Datenquellenadministrator für Purview“ zwei unterstützte Szenarien aufweist. Das erste Szenario ist für Benutzer bestimmt, die bereits Datenleseberechtigter oder Datenkurator für Purview sind und auch die Berechtigung zum Erstellen von Überprüfungen benötigen. Diese Benutzer müssen über zwei Rollen verfügen, nämlich mindestens über eine der beiden Rollen „Datenleseberechtigter für Purview“ und „Datenkurator für Purview“ sowie zusätzlich über die Rolle „Datenquellenadministrator für Purview“.

Das andere Szenario für „Datenquellenadministrator für Purview“ ist für programmgesteuerte Prozesse bestimmt, z. B. Dienstprinzipale, für die das Einrichten und Überwachen von Überprüfungen möglich sein soll, aber kein Zugriff auf die Daten des Katalogs.

Dieses Szenario kann implementiert werden, indem dem Dienstprinzipal die Rolle „Datenquellenadministrator für Purview“ zugewiesen wird, ohne dass er über eine der beiden anderen Rollen verfügt. Der Prinzipal hat keinen Zugriff auf das Purview-Portal. Dies stellt aber kein Problem dar, weil es sich um einen programmgesteuerten Prinzipal handelt, der nur über APIs kommuniziert.

### <a name="putting-users-into-legacy-roles"></a>Zuweisen von Legacy-Rollen für Benutzer

Als Erstes müssen nach der Erstellung eines Azure Purview-Kontos Benutzern die Rollen zugewiesen werden.

Die Rollenzuweisung wird über die [rollenbasierte Zugriffssteuerung (RBAC) von Azure](../role-based-access-control/overview.md) verwaltet.

Rollen für Benutzer können nur über zwei integrierte Rollen der Steuerungsebene in Azure zugewiesen werden: entweder „Besitzer“ oder „Benutzerzugriffsadministrator“. Damit Benutzer über diese Rollen für Azure Purview verfügen, müssen sie sich also entweder an einen Besitzer oder Benutzerzugriffsadministrator wenden oder selbst eine dieser Rollen innehaben.

#### <a name="an-example-of-assigning-someone-to-a-legacy-role"></a>Beispiel für die Zuweisung eines Benutzers zu einer Legacy-Rolle

1. Navigieren Sie zu https://portal.azure.com und dann zu Ihrem Azure Purview-Konto.
1. Klicken Sie links auf „Zugriffssteuerung (IAM)“.
1. Befolgen Sie dann [diese allgemeine Anleitung](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group).

### <a name="legacy-role-definitions-and-actions"></a>Legacy-Rollendefinitionen und -aktionen

Eine Rolle ist als eine Sammlung mit Aktionen definiert. Weitere Informationen zum Definieren von Rollen finden Sie [hier](../role-based-access-control/role-definitions.md). Die Rollendefinitionen für die Azure Purview-Rollen finden Sie [hier](../role-based-access-control/built-in-roles.md).

### <a name="getting-added-to-a-legacy-data-plane-role-in-an-azure-purview-account"></a>Erreichen der Hinzufügung zu einer Legacy-Datenebenenrolle in einem Azure Purview-Konto

Wenn Sie Zugriff auf ein Azure Purview-Konto erhalten möchten, damit Sie das Studio-Feature nutzen oder die zugehörigen APIs aufrufen können, müssen Sie einer Datenebenenrolle von Azure Purview hinzugefügt werden. Dies kann nur von Personen durchgeführt werden, die ein Besitzer oder Benutzerzugriffsadministrator des Azure Purview-Kontos sind. Für die meisten Benutzer ist der nächste Schritt dann die Kontaktaufnahme mit einem lokalen Administrator, der die richtigen Personen für die Erteilung des Zugriffs nennen kann.

Benutzer, die Zugriff auf das [Azure-Portal](https://portal.azure.com) des Unternehmens haben, können nach dem gewünschten Azure Purview-Konto für den Beitritt suchen, auf die zugehörige Registerkarte „Zugriffssteuerung (IAM)“ klicken und die Besitzer bzw. Benutzerzugriffsadministrator ermitteln. Beachten Sie hierbei aber Folgendes: In einigen Fällen werden unter Umständen Azure Active Directory-Gruppen oder Dienstprinzipale als Besitzer oder Benutzerzugriffsadministrator verwendet, mit denen eine direkte Kontaktaufnahme dann ggf. nicht möglich ist. Stattdessen müssen Sie sich an einen Administrator wenden.

### <a name="legacy---who-should-be-assigned-to-what-role"></a>Legacy: Wem sollte welche Rolle zugewiesen werden?

|Benutzerszenario|Geeignete Rollen|
|-------------|-----------------|
|Ich muss Ressourcen nur finden können und möchte keine Bearbeitungen durchführen|Rolle „Datenleseberechtigter für Purview“|
|Ich muss Informationen zu Ressourcen bearbeiten, entsprechende Klassifizierungen einfügen, Glossareinträge dafür zuordnen usw.|Rolle „Datenkurator für Purview“|
|Ich muss das Glossar bearbeiten oder neue Klassifizierungsdefinitionen einrichten|Rolle „Datenkurator für Purview“|
|Der Dienstprinzipal meiner Anwendung muss Daten per Pushvorgang an Azure Purview übertragen|Rolle „Datenkurator für Purview“|
|Ich muss Überprüfungen über das Studio-Feature von Purview einrichten|Rolle „Datenquellenadministrator für Purview“ sowie mindestens die Rolle „Datenleseberechtigter für Purview“ oder „Datenkurator für Purview“|
|Ich muss einen Dienstprinzipal oder eine andere programmgesteuerte Identität aktivieren, um Überprüfungen in Azure Purview einzurichten und zu überwachen, ohne dass die programmgesteuerte Identität Zugriff auf die Informationen des Katalogs hat |Rolle „Datenquellenadministrator für Purview“|
|Ich muss Benutzern in Azure Purview Rollen zuweisen | „Besitzer“ oder „Benutzerzugriffsadministrator“ |

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun über grundlegende Kenntnisse zu Sammlungen und zur Zugriffssteuerung verfügen, können Sie entweder mithilfe der folgenden Anleitungen Sammlungen erstellen und verwalten oder mit der Registrierung von Quellen in Ihrer Purview-Ressource beginnen.

- [Erstellen und Verwalten von Sammlungen](how-to-create-and-manage-collections.md)
- [Liste der von Purview unterstützten Datenquellen](purview-connector-overview.md)
