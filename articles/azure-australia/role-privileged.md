---
title: Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Privileged Identity Management
titleSuffix: Azure Australia
description: Leitfaden zur Implementierung von rollenbasierter Zugriffssteuerung von Azure (Azure RBAC) und Privileged Identity Management in den australischen Regionen, um die spezifischen Anforderungen der Richtlinien, Bestimmungen und Gesetze der australischen Regierung zu erfüllen.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 819d8398395b9cccd4066b216cccd39a8a6fbfcc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/21/2021
ms.locfileid: "122338829"
---
# <a name="azure-role-based-access-control-azure-rbac-and-privileged-identity-management-pim"></a>Rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) und Privileged Identity Management (PIM)

Die Verwaltung der Administratorberechtigungen ist eine wichtige Maßnahme, um die Sicherheit von IT-Umgebungen zu gewährleisten. Die Beschränkung von Administratorberechtigungen gemäß dem Sicherheitsmodell der geringsten Rechte ist eine Vorgabe des [ACSC-ISM](https://acsc.gov.au/infosec/ism/index.htm) (ACSC-Handbuch zur Informationssicherheit) und Teil der [Essential Eight](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) (Liste mit acht grundlegenden Sicherheitsempfehlungen des ACSC).

Microsoft stellt verschiedene Optionen für die Implementierung von Just-In-Time und Just-Enough-Access innerhalb von Microsoft Azure bereit. Diese Optionen müssen bekannt sein, um die Sicherheit in der Cloud effektiv gewährleisten zu können. Dieser Leitfaden enthält eine Übersicht über die Optionen an sich sowie Informationen zu wichtigen Entwurfsaspekten bei der Implementierung.

## <a name="azure-rbac"></a>Azure RBAC

Die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC) ist für die Verwaltung des Zugriffs auf alle Ressourcen in Microsoft Azure sowie für die Verwaltung von Azure Active Directory (Azure AD) von zentraler Bedeutung. Azure RBAC kann zusammen mit einer Reihe ergänzender Features implementiert werden, die in Azure zur Verfügung stehen. Der Schwerpunkt dieses Artikels liegt auf der Implementierung einer effektiven RBAC mithilfe von Azure-Verwaltungsgruppen, Azure Active Directory Gruppen und Azure Privileged Identity Management (PIM).

Ganz allgemein sind für die Implementierung der Azure RBAC drei Komponenten erforderlich:

![Das Diagramm zeigt die drei Komponenten, die für die Implementierung von RBAC erforderlich sind. Dabei handelt es sich um den Sicherheitsprinzipal, die Rollendefinition und den Bereich, die alle in die Rollenzuweisung einfließen.](media/rbac-overview.png)

* **Sicherheitsprinzipale**: Bei einem Sicherheitsprinzipal kann es sich um einen Benutzer, eine Gruppe, [Dienstprinzipale](../active-directory/develop/app-objects-and-service-principals.md) oder eine [verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) handeln. Sicherheitsprinzipalen sollten Berechtigungen über Azure Active Directory-Gruppen zugewiesen werden.

* **Rollendefinition**: Eine Rollendefinition wird auch als Rolle bezeichnet und ist eine Sammlung von Berechtigungen. Diese Berechtigungen definieren die Vorgänge, die durch die Sicherheitsprinzipale ausgeführt werden können, die der Rollendefinition zugewiesen sind. Diese Funktion wird durch Azure-Ressourcenrollen und Azure Active Directory Administratorrollen ermöglicht. Azure verfügt über eine Reihe integrierter Rollen, die durch benutzerdefinierte Rollen erweitert werden können.

* **Bereich**: Der Bereich ist die Gruppe von Azure-Ressourcen, für die eine Rollendefinition gilt. Azure-Rollen können Azure-Ressourcen mithilfe von Azure-Verwaltungsgruppen zugewiesen werden.

Diese drei Komponenten werden miteinander kombiniert, um Sicherheitsprinzipalen den in den Rollendefinitionen definierten Zugriff auf alle Ressourcen zu gewähren, die innerhalb des Bereichs der Azure-Verwaltungsgruppen liegen. Dies wird als Rollenzuweisung bezeichnet. Einem Sicherheitsprinzipal können mehrere Rollendefinitionen zugewiesen werden, und einem einzelnen Bereich können mehrere Sicherheitsprinzipale zugewiesen werden.

### <a name="azure-active-directory-groups"></a>Azure Active Directory-Gruppen

Beim Zuweisen von Berechtigungen zu Einzelpersonen oder Teams sollte die Zuweisung nach Möglichkeit immer mit einer Azure Active Directory-Gruppe verknüpft werden, anstatt die Berechtigungen direkt zuzuweisen. Diese empfohlene Vorgehensweise wurde aus lokalen Active Directory-Implementierungen übernommen. Azure Active Directory-Gruppen sollten nach Möglichkeit pro Team erstellt werden (ergänzend zur logischen Struktur der erstellten Azure-Verwaltungsgruppen).

In einem Hybrid Cloud-Szenario können lokale Windows Server Active Directory-Sicherheitsgruppen mit Ihrer Azure Active Directory-Instanz synchronisiert werden. Wenn Sie die Azure RBAC bereits lokal mithilfe dieser Windows Server Active Directory-Sicherheitsgruppen implementiert haben, können diese Gruppen nach der Synchronisierung verwendet werden, um die Azure RBAC für Ihre Azure-Ressourcen zu implementieren. Andernfalls können Sie Ihre Cloudumgebung von Grund auf neu gestalten und auf der Grundlage Ihrer Azure Active Directory-Implementierung einen Plan für eine robuste Berechtigungsverwaltung implementieren.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Gegenüberstellung von Azure-Ressourcenrollen und Azure Active Directory-Administratorrollen

Microsoft Azure bietet ein breites Spektrum an integrierten Rollen für [Azure-Ressourcen](../role-based-access-control/built-in-roles.md) und die [Azure Active Directory-Verwaltung](../active-directory/roles/permissions-reference.md). Beide Arten von Rollen ermöglichen spezifischen, präzisen Zugriff auf Azure-Ressourcen bzw. für Azure AD Administratoren. Wichtig: Azure-Ressourcenrollen können nicht verwendet werden, um Administratorzugriff auf Azure AD zu gewähren, und Azure AD-Rollen gewähren keinen spezifischen Zugriff auf Azure-Ressourcen.

Mithilfe einer integrierten Rolle können einer Azure-Ressource beispielsweise folgende Zugriffsarten zugewiesen werden:

* Ein Benutzer kann virtuelle Computer in einem Abonnement verwalten, während ein anderer Benutzer virtuelle Netzwerke verwalten kann
* Eine DBA-Gruppe kann SQL-Datenbanken in einem Abonnement verwalten
* Ein Benutzer kann sämtliche Ressourcen in einer Ressourcengruppe verwalten, wie z.B. virtuelle Computer, Websites und Subnetze
* Eine Anwendung kann auf sämtliche Ressourcen in einer Ressourcengruppe zugreifen

Für die Azure AD-Verwaltung können beispielsweise folgende Zugriffsarten zugewiesen werden:

* Helpdesk-Mitarbeiter können Benutzerkennwörter zurücksetzen
* Mitarbeiter können externe Benutzer zu einer Azure AD-Instanz für die B2B-Zusammenarbeit einladen
* Administratoren erhalten Lesezugriff auf Anmelde- und Überwachungsberichte
* Mitarbeiter können alle Benutzer und Gruppen verwalten (einschließlich Kennwortzurücksetzung)

Wichtig: Machen Sie sich mit der gesamten Liste der möglichen Aktionen einer integrierten Rolle vertraut, um zu gewährleisten, dass kein unangemessener Zugriff gewährt wird. Die Liste mit den integrierten Rollen und dem jeweils gewährten Zugriff wird ständig weiterentwickelt. Die vollständige Liste mit den Rollen und den zugehörigen Definitionen finden Sie in der weiter oben verlinkten Dokumentation. Sie können aber auch das folgende Azure PowerShell-Cmdlet verwenden:

```PowerShell
Get-AzRoleDefinition
```

```output
Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

Oder den folgenden Azure CLI-Befehl:

```azurecli-interactive
az role definition list
```

```output
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Darüber hinaus können bei Bedarf auch benutzerdefinierte Azure-Ressourcenrollen erstellt werden. Diese benutzerdefinierten Rollen können im Azure-Portal, mithilfe von PowerShell oder über die Azure-Befehlszeilenschnittstelle erstellt werden. Bei der Erstellung einer benutzerdefinierten Rolle ist darauf zu achten, dass die Rolle einen spezifischen Zweck hat und ihre Funktion nicht bereits durch eine vorhandene Azure-Ressourcenrolle bereitgestellt wird. Dies vereinfacht die laufende Verwaltung und verringert die Gefahr, dass Sicherheitsprinzipale mit unnötigen Berechtigungen ausgestattet werden. Ein Beispiel wäre etwa die Erstellung einer benutzerdefinierten Azure-Ressourcenrolle zwischen den integrierten Azure-Ressourcenrollen „Mitwirkender für virtuelle Computer“ und „Anmeldeinformationen des VM-Administrators“.

Als Grundlage für die benutzerdefinierte Rolle kann etwa die vorhandene Rolle „Mitwirkender“ verwendet werden, die folgenden Zugriff gewährt:

| Azure-Ressource | Zugriffsebene |
| --- | --- |
| Virtual Machines | Verwaltung, aber kein Zugriff |
| An VM angefügtes virtuelles Netzwerk | Kein Zugriff |
| An VM angefügter Speicher | Kein Zugriff |
|

Die benutzerdefinierte Rolle kann diesen grundlegenden Zugriff beibehalten und den vorgesehenen Benutzern beispielsweise zusätzliche grundlegende Berechtigungen zum Ändern der Netzwerkkonfiguration der virtuellen Computer erteilen.

Azure-Ressourcenrollen haben außerdem den Vorteil, dass sie Ressourcen über Azure-Verwaltungsgruppen zugewiesen werden können.

### <a name="azure-management-groups"></a>Azure-Verwaltungsgruppen

Mit Azure Verwaltungsgruppen kann eine Organisation die Zuweisung von Rollen zu allen Abonnements und deren Ressourcen innerhalb eines Azure-Mandanten verwalten. Azure-Verwaltungsgruppen ermöglichen die Erstellung von Verwaltungshierarchien in Azure und somit unter anderem die hierarchische Nachbildung der Organisationsstruktur. Durch die Erstellung organisatorischer Geschäftseinheiten als separate logische Entitäten können Berechtigungen innerhalb einer Organisation basierend auf den spezifischen Anforderungen des jeweiligen Teams angewendet werden. Mit Azure-Verwaltungsgruppen lässt sich eine Verwaltungshierarchie mit bis zu sechs Ebenen definieren.

![Verwaltungsgruppen](media/management-groups.png)

Azure-Verwaltungsgruppen werden Azure-Abonnements innerhalb eines Azure-Mandanten zugeordnet. Dadurch kann eine Organisation Azure-Ressourcen bestimmter Unternehmenseinheiten voneinander trennen und präzise Steuerungsmöglichkeiten für die Kostenverwaltung und die Berechtigungszuweisung bereitstellen.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Microsoft hat Just-In-Time (JIT) und Just-Enough-Access (JEA) über Azure Privileged Identity Management implementiert. Mit diesem Dienst können Administratoren den privilegierten Zugriff auf Azure-Ressourcen steuern, verwalten und überwachen. Mithilfe von PIM können Administratoren Sicherheitsprinzipale als für eine Rolle geeignet konfigurieren, sodass Benutzer über das Azure-Portal oder mit PowerShell-Cmdlets die Aktivierung der Rolle anfordern können. Standardmäßig kann die Rollenzuweisung für einen Zeitraum zwischen einer Stunde und 72 Stunden aktiviert werden. Bei Bedarf kann der Benutzer eine Verlängerung der Rollenzuweisung anfordern, und auch eine permanente Rollenzuweisung ist möglich. Optional kann eine mehrstufige Authentifizierung erzwungen werden, wenn Benutzer die Aktivierung ihrer geeigneten Rollen anfordern. Nach Ablauf der zugeordneten Rollenaktivierungsdauer verfügt der Sicherheitsprinzipal nicht mehr über den privilegierten Zugriff, der durch die Rolle gewährt wird.

Durch die Verwendung von PIM werden häufige Probleme beim Zuweisen von Berechtigung vermieden, die in Umgebungen ohne Just-In-Time-Zugriff und ohne regelmäßige Überprüfung der Berechtigungszuweisung auftreten können. Eines dieser häufigen Probleme besteht darin, dass die Zuweisung erhöhter Berechtigungen in Vergessenheit gerät und bestehen bleibt, obwohl die Aufgabe, für die die erhöhten Berechtigungen erforderlich waren, längst abgeschlossen ist. Ein weiteres Problem ist die Verbreitung erhöhter Berechtigungen innerhalb einer Umgebung, wenn der zugewiesene Zugriff eines Sicherheitsprinzipals beim Konfigurieren weiterer ähnlicher Sicherheitsprinzipale geklont wird.

## <a name="key-design-considerations"></a>Wichtige Entwurfsaspekte

Beim Entwerfen einer Azure RBAC-Strategie zur Erzwingung des Sicherheitsmodells der geringsten Rechte müssen folgende Sicherheitsanforderungen berücksichtigt werden:

* Überprüfung von Anforderungen für privilegierten Zugriff
* Beschränkung von Administratorberechtigungen auf den geringstmöglichen Zugriff, der zum Ausführen der spezifischen Aufgaben erforderlich ist
* Beschränkung von Administratorberechtigungen auf den kürzestmöglichen Zeitraum, der zum Ausführen der spezifischen Aufgaben erforderlich ist
* Regelmäßige Überprüfung der gewährten Administratorberechtigungen

Im Zuge der Ausarbeitung einer Azure RBAC-Strategie ist eine ausführliche Prüfung der Geschäftsfunktionen erforderlich, um die Zugriffsunterschiede zwischen individuellen geschäftlichen Rollen sowie Art und Häufigkeit der Arbeiten nachzuvollziehen, für die erhöhte Berechtigungen benötigt werden. Aufgrund ihrer unterschiedlichen Aufgaben benötigen Sicherungsoperatoren, Sicherheitsadministratoren und Prüfer jeweils unterschiedliche Zugriffsebenen zu unterschiedlichen Zeiten und mit unterschiedlicher laufender Überprüfung.

## <a name="validate-requests-for-access"></a>Überprüfen von Zugriffsanforderungen

Erhöhte Berechtigungen müssen explizit genehmigt werden. Hierzu müssen ein Genehmigungsprozess entwickelt und geeignete Mitarbeiter damit beauftragt werden, die Zulässigkeit der einzelnen Anforderungen für zusätzliche Berechtigungen zu überprüfen. Privileged Identity Management bietet mehrere Genehmigungsoptionen für die Rollenzuweisung. Eine Rollenaktivierungsanforderung kann mit einer automatischen Genehmigung konfiguriert oder abgegrenzt werden, um eine manuelle Prüfung und Genehmigung aller Rollenaktivierungsanforderungen durch nominierte genehmigende Personen zu erzwingen. Aktivierungsanforderungen können auch so konfiguriert werden, dass Zusatzinformationen in die Aktivierungsanforderung eingeschlossen werden müssen (beispielsweise Ticketnummern).

### <a name="restrict-privilege-based-on-duties"></a>Einschränken von Berechtigungen auf der Grundlage von Aufgaben

Da die Zuweisung zu vieler Berechtigungen einen gängigen Angriffsvektor für die IT-Sicherheit darstellt, ist es wichtig, den Umfang der Berechtigungen für Sicherheitsprinzipale einzuschränken. Die Arten der verwalteten Ressourcen sowie die zuständigen Teams müssen bewertet werden, um ihnen die geringstmöglichen Berechtigungen zuweisen zu können, die sie für ihre täglichen Aufgaben benötigen. Zusätzliche Berechtigungen, die über die für die täglichen Aufgaben erforderlichen Berechtigungen hinausgehen, sollten nur für die Dauer einer spezifischen Aufgabe gewährt werden. Beispiel: Der Administrator eines Kunden verfügt über Zugriff vom Typ „Mitwirkender“, kann jedoch für eine spezifische Aufgabe, für die er vorübergehend mehr Zugriffsberechtigungen benötigt, Berechtigungen vom Typ „Besitzer“ für eine Azure-Ressource anfordern.

Dadurch wird sichergestellt, dass die einzelnen Administratoren jeweils nur für den kürzestmöglichen Zeitraum über erhöhte Zugriffsberechtigungen verfügen. Die Anwendung dieser Methoden reduziert die Angriffsfläche der IT-Infrastruktur von Organisationen.

### <a name="regular-evaluation-of-administrative-privilege"></a>Regelmäßige Auswertung von Administratorberechtigungen

Sicherheitsprinzipale in einer Umgebung müssen regelmäßig überprüft werden, um sicherzustellen, dass die korrekte Berechtigungsebene zugewiesen ist. Microsoft Azure bietet eine Reihe von Möglichkeiten zur Überprüfung und Auswertung der Berechtigungen, die Azure-Sicherheitsprinzipalen zugewiesen sind. Mit Privileged Identity Management können Administratoren regelmäßig Zugriffsüberprüfungen für die Rollen durchführen, die Sicherheitsprinzipalen gewährt wurden. Im Rahmen einer Zugriffsüberprüfung können sowohl die Rollenzuweisung für Azure-Ressourcen als auch die Administratorrollenzuweisung für Azure Active Directory überprüft werden. Eine Zugriffsüberprüfung kann mit folgenden Eigenschaften konfiguriert werden:

* **Überprüfungsname sowie Start-/Enddatum der Überprüfung**: Überprüfungen müssen so konfiguriert werden, dass die nominierten Benutzer genügend Zeit für die Durchführung haben.

* **Zu überprüfende Rolle**: Jede Zugriffsüberprüfung konzentriert sich auf eine einzelne Azure-Rolle.

* **Nominierte Prüfer**: Bei der Durchführung einer Überprüfung gibt es drei Möglichkeiten. Sie können die Überprüfungsdurchführung einer anderen Person zuweisen, Sie können die Überprüfung selbst durchführen, oder Sie können festlegen, dass jeder Benutzer seinen Zugriff selbst überprüfen soll.

* **Angabe eines Grunds für den Zugriff erforderlich machen**: Im Zuge der Zugriffsüberprüfung kann festgelegt werden, dass Benutzer einen Grund für die Beibehaltung ihrer Berechtigungsstufe angeben müssen.

Der Status ausstehender Zugriffsüberprüfungen kann jederzeit über ein Dashboard im Azure-Portal überwacht werden. Der Zugriff auf die Rolle, die überprüft wird, bleibt bis zum Abschluss der Zugriffsüberprüfung unverändert. Es ist auch möglich, alle PIM-Benutzerzuweisungen und -aktivierungen innerhalb eines bestimmten Zeitraums zu [überprüfen](../active-directory/privileged-identity-management/pim-how-to-use-audit-log.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den Artikel [Systemüberwachung aus Sicherheitsgründen für Azure Australien](system-monitor.md).
