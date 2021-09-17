---
title: Übersicht zu Azure-Richtlinien
description: Azure Policy ist ein Dienst in Azure, mit dem Sie Richtliniendefinitionen in Ihrer Azure-Umgebung erstellen, zuweisen und verwalten können.
ms.date: 07/27/2021
ms.topic: overview
ms.openlocfilehash: 9462281ce37e066a920c17d1dc142a177b97fb2a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770548"
---
# <a name="what-is-azure-policy"></a>Was ist Azure Policy?

Azure Policy hilft bei der Durchsetzung von Organisationsstandards und bei der Bewertung der Compliance nach Bedarf. Über sein Compliance-Dashboard bietet der Dienst eine aggregierte Ansicht zur Bewertung des Gesamtzustands der Umgebung mit der Möglichkeit, einen Drilldown zur Granularität pro Ressource und Richtlinie durchzuführen. Außerdem trägt er durch Massenwartung für vorhandene Ressourcen und automatische Wartung dazu bei, dass Ihre Ressourcen Compliance-Anforderungen erfüllen.

Häufige Anwendungsfälle für Azure Policy sind die Implementierung von Governance für Ressourcenkonsistenz, Einhaltung gesetzlicher Bestimmungen, Sicherheit, Kosten und Verwaltung. Richtliniendefinitionen für diese häufigen Anwendungsfälle sind in ihrer Azure-Umgebung bereits integriert bereitgestellt, um Ihnen den Einstieg zu erleichtern.

Alle Azure Policy-Daten und -Objekte werden im Ruhezustand verschlüsselt. Weitere Informationen finden Sie unter [Datenverschlüsselung ruhender Azure-Daten](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Übersicht

Azure Policy wertet Ressourcen in Azure aus, indem die Eigenschaften dieser Ressourcen mit Geschäftsregeln verglichen werden. Diese im [JSON-Format](./concepts/definition-structure.md) beschriebenen Geschäftsregeln werden als [Richtliniendefinitionen](#policy-definition) bezeichnet. Um die Verwaltung zu vereinfachen, können mehrere Geschäftsregeln gruppiert werden, um eine [Richtlinieninitiative](#initiative-definition) (manchmal auch als _policySet_ bezeichnet) zu bilden. Nachdem Ihre Geschäftsregeln erstellt wurden, wird die Richtliniendefinition oder -initiative jedem von Azure unterstützten Ressourcenbereich [zugewiesen](#assignments), z. B. [Verwaltungsgruppen](../management-groups/overview.md), Abonnements, [Ressourcengruppen](../../azure-resource-manager/management/overview.md#resource-groups) oder einzelnen Ressourcen. Die Zuweisung gilt für alle Ressourcen innerhalb des [Resource Manager-Bereichs](../../azure-resource-manager/management/overview.md#understand-scope) dieser Zuweisung. Unterbereiche können ggf. ausgeschlossen werden. Weitere Informationen finden Sie unter [Erläuterungen zum Bereich in Azure Policy](./concepts/scope.md).

Azure Policy verwendet ein [JSON-Format](./concepts/definition-structure.md) zum Erstellen der Logik, mit der die Auswertung bestimmt, ob eine Ressource konform ist. Definitionen enthalten Metadaten und die Richtlinienregel. Die definierte Regel kann Funktionen, Parameter, logische Operatoren, Bedingungen und Eigenschaften[aliase](./concepts/definition-structure.md#aliases) verwenden, um genau dem gewünschten Szenario zu entsprechen. Die Richtlinienregel bestimmt, welche Ressourcen im Bereich der Zuweisung ausgewertet werden.

### <a name="understand-evaluation-outcomes"></a>Grundlegendes zu den Auswertungsergebnissen

Die Auswertung von Ressourcen erfolgt zu bestimmten Zeitpunkten während des Ressourcenlebenszyklus, des Lebenszyklus der Richtlinienzuweisung und für regelmäßige kontinuierliche Compliance-Auswertungen. Nachfolgend sind die Uhrzeiten oder Ereignisse aufgeführt, die dazu führen, dass eine Ressource ausgewertet wird:

- Eine Ressource wird in einem Bereich mit einer Richtlinienzuweisung erstellt, aktualisiert oder gelöscht.
- Eine Richtlinie oder Initiative wird einem Bereich neu zugewiesen.
- Eine Richtlinie oder Initiative, die bereits einem Bereich zugewiesen ist, wird aktualisiert.
- Die standardmäßige Compliance-Auswertung, die alle 24 Stunden erfolgt, wird durchgeführt.

Ausführliche Informationen darüber, wann und wie die Richtlinienauswertung erfolgt, finden Sie unter [Auswertungsauslöser](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Steuern der Reaktion auf eine Auswertung

Geschäftsregeln für die Behandlung nicht konformer Ressourcen sind von Organisation zu Organisation sehr unterschiedlich. Beispiele dafür, wie die Plattform einer Organisation auf eine nicht konforme Ressource reagieren soll:

- Ablehnen der Ressourcenänderung
- Protokollieren der Änderung an der Ressource
- Ändern der Ressource vor der Änderung
- Ändern der Ressource nach der Änderung
- Bereitstellen von zugehörigen konformen Ressourcen

Azure Policy ermöglicht jede dieser geschäftlichen Reaktionen durch die Anwendung von [Effekten](./concepts/effects.md). Effekte werden in der **Richtlinienregel** der [Richtliniendefinition](./concepts/definition-structure.md) festgelegt.

### <a name="remediate-non-compliant-resources"></a>Korrigieren nicht konformer Ressourcen

Diese Effekte wirken sich in erster Linie auf eine Ressource aus, wenn die Ressource erstellt oder aktualisiert wird. Azure Policy unterstützt auch den Umgang mit vorhandenen nicht konformen Ressourcen, ohne diese Ressource ändern zu müssen. Weitere Informationen zum Erreichen von Konformität für vorhandene Ressourcen finden Sie unter [Warten von Ressourcen](./how-to/remediate-resources.md).

### <a name="video-overview"></a>Videoübersicht

Die folgende Übersicht über Azure Policy stammt von der Build 2018. Folien und den Videodownload finden Sie unter [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Steuern Ihrer Azure-Umgebung per Azure Policy) auf Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Erste Schritte

### <a name="azure-policy-and-azure-rbac"></a>Azure Policy und Azure RBAC

Zwischen Azure Policy und der rollenbasierten Zugriffssteuerung von Azure (Azure RBAC) gibt es einige entscheidende Unterschiede. Azure Policy wertet den Status aus, indem Eigenschaften für Ressourcen, die in Resource Manager dargestellt sind, und Eigenschaften einiger Ressourcenanbieter überprüft werden. Azure Policy schränkt Aktionen (auch als _Vorgänge_ bezeichnet) nicht ein. Azure Policy stellt sicher, dass der Ressourcenzustand Ihren Geschäftsregeln entspricht, unabhängig davon, wer die Änderung vorgenommen hat oder wer die Berechtigung hat, eine Änderung vorzunehmen. Einige Azure Policy-Ressourcen wie [Richtliniendefinitionen](#policy-definition), [Initiativdefinitionen](#initiative-definition) und [Zuweisungen](#assignments) sind für alle Benutzer sichtbar. So wissen alle Benutzer und Dienste, welche Richtlinienregeln in einer Umgebung festgelegt wurden.

Bei Azure RBAC steht die Verwaltung von [Benutzeraktionen](../../role-based-access-control/resource-provider-operations.md) in verschiedenen Bereichen im Mittelpunkt. Wenn die Steuerung einer Aktion erforderlich ist, ist Azure RBAC das richtige Tool dafür. Auch wenn eine Person Zugriff zum Ausführen einer Aktion hat und das Ergebnis eine nicht konforme Ressource ist, blockiert Azure Policy weiterhin das Erstellen oder Aktualisieren.

Die Kombination aus Azure RBAC und Azure Policy bietet vollständige Bereichskontrolle in Azure.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Azure RBAC-Berechtigungen in Azure Policy

Azure Policy verfügt über verschiedene Berechtigungen (als Vorgänge bezeichnet) in zwei Ressourcenanbietern:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Zahlreiche integrierte Rollen erteilen Berechtigungen für Azure Policy-Ressourcen. Die Rolle **Mitwirkender bei Ressourcenrichtlinien** umfasst die meisten Vorgänge in Azure Policy. Die Rolle **Besitzer** verfügt über die vollständigen Berechtigungen. Sowohl **Mitwirkender** als auch **Leser** haben Zugriff auf alle Azure Policy-_Lesevorgänge_. **Mitwirkende** können eine Ressourcenwartung auslösen, aber keine Definitionen oder Zuweisungen _erstellen_. Die Berechtigung **Benutzerzugriffsadministrator** ist erforderlich, um der verwalteten Identität unter den Zuweisungen **deployIfNotExists** oder **modify** die benötigten Berechtigungen zu gewähren.

Wenn keine der integrierten Rollen über die erforderlichen Berechtigungen verfügt, erstellen Sie eine [benutzerdefinierte Rolle](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Die verwaltete Identität einer Richtlinienzuweisung vom Typ **deployIfNotExists** oder **modify** benötigt ausreichende Berechtigungen zum Erstellen oder Aktualisieren von Zielressourcen. Weitere Informationen finden Sie unter [Konfigurieren von Richtliniendefinitionen für die Wartung](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Von Azure Policy abgedeckte Ressourcen

Azure Policy wertet alle Azure-Ressourcen auf oder unterhalb der Abonnementebene aus, einschließlich Ressourcen mit Arc-Unterstützung. Für bestimmte Ressourcenanbieter wie [Gastkonfiguration](./concepts/guest-configuration.md), [Azure Kubernetes Service](../../aks/intro-kubernetes.md) und [Azure Key Vault](../../key-vault/general/overview.md) gibt es eine tiefergreifende Integration für die Verwaltung von Einstellungen und Objekten. Weitere Informationen finden Sie unter [Ressourcenanbietermodi](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Empfehlungen für die Verwaltung von Richtlinien

Hier sind einige Hinweise und Tipps aufgeführt, die Sie beachten sollten:

- Beginnen Sie mit einem Überwachungseffekt anstelle eines Ablehnungseffekts, um die Auswirkung Ihrer Richtliniendefinition auf die Ressourcen in Ihrer Umgebung nachzuverfolgen. Wenn Sie bereits Skripts für die automatische Skalierung Ihrer Anwendungen eingerichtet haben, kann ein Ablehnungseffekt die bereits eingerichteten Automatisierungsaufgaben verhindern.

- Berücksichtigen Sie beim Erstellen von Definitionen und Anweisungen die Hierarchien der Organisation. Wir empfehlen Ihnen, Definitionen auf allgemeiner Ebene zu erstellen, z.B. Verwaltungsgruppen- oder Abonnementebene. Erstellen Sie anschließend die Zuweisung auf der nächsten untergeordneten Ebene. Wenn Sie eine Definition für eine Verwaltungsgruppe erstellen, kann die Zuweisung für ein Abonnement oder eine Ressourcengruppe innerhalb dieser Verwaltungsgruppe festgelegt werden.

- Wir empfehlen Ihnen, auch für nur eine einzelne Richtliniendefinition Initiativdefinitionen zu erstellen und zuzuweisen.
  Es kann beispielsweise sein, dass Sie über die Richtliniendefinition _policyDefA_ verfügen und sie unter der Initiativdefinition _initiativeDefC_ erstellen. Wenn Sie später eine andere Richtliniendefinition für _policyDefB_ erstellen, deren Ziele denen von _policyDefA_ ähneln, können Sie sie unter _initiativeDefC_ hinzufügen und beide zusammen nachverfolgen.

- Nachdem Sie eine Initiativzuweisung erstellt haben, werden Richtliniendefinitionen, die der Initiative hinzugefügt werden, ebenfalls Teil der Zuweisungen dieser Initiative.

- Wenn eine Initiativzuweisung ausgewertet wird, werden auch alle Richtlinien innerhalb der Initiative ausgewertet.
  Falls Sie eine Richtlinie einzeln auswerten möchten, empfiehlt es sich, sie nicht in eine Initiative aufzunehmen.

## <a name="azure-policy-objects"></a>Azure Policy-Objekte

### <a name="policy-definition"></a>Richtliniendefinition

Die Erstellung und Implementierung einer Richtlinie in Azure Policy beginnt mit dem Erstellen einer Richtliniendefinition. Jede Richtliniendefinition verfügt über Bedingungen, unter denen sie erzwungen wird. Des Weiteren verfügt sie über einen definierten Effekt, der wirksam wird, wenn die Bedingungen erfüllt sind.

In Azure Policy bieten wir mehrere integrierte Richtlinien an, die standardmäßig zur Verfügung stehen. Beispiel:

- **Zulässige Speicherkonten-SKUs** (ablehnen): Bestimmt, ob ein bereitzustellendes Speicherkonto innerhalb einer Gruppe von SKU-Größen liegt. Alle Speicherkonten, die nicht der definierten Gruppe von SKU-Größen entsprechen, werden abgelehnt.
- **Zulässiger Ressourcentyp** (ablehnen): Definiert die Ressourcentypen, die Sie bereitstellen können. Alle Ressourcen, die nicht in dieser Liste enthalten sind, werden abgelehnt.
- **Zulässige Standorte** (ablehnen): Schränkt die verfügbaren Standorte für neue Ressourcen ein. Der dazugehörige Effekt dient zur Erzwingung Ihrer Geokonformitätsanforderungen.
- **Zulässige SKUs für virtuelle Computer** (ablehnen): Gibt eine Gruppe von SKUs für virtuelle Computer an, die Sie bereitstellen können.
- **Tag zu Ressourcen hinzufügen** (ändern): Wendet ein erforderliches Tag und dessen Standardwert an, falls dies nicht mit der Bereitstellungsanforderung angegeben wird.
- **Nicht zulässige Ressourcentypen** (ablehnen): Verhindert, dass die in der Liste enthaltenen Ressourcentypen bereitgestellt werden.

Um diese Richtliniendefinitionen implementieren zu können (integrierte und benutzerdefinierte Definitionen), müssen Sie sie zuweisen. Sie können diese Richtlinien über das Azure-Portal, PowerShell oder die Azure CLI zuweisen.

Bei der Richtlinienauswertung werden mehrere unterschiedliche Aktionen durchgeführt, z.B. Richtlinienzuweisung oder Richtlinienaktualisierungen. Eine vollständige Liste finden Sie unter [Evaluation Triggers](./how-to/get-compliance-data.md#evaluation-triggers) (Auswertungsauslöser).

Weitere Informationen zu den Strukturen von Richtliniendefinitionen finden Sie unter [Struktur von Richtliniendefinitionen](./concepts/definition-structure.md).

Richtlinienparameter vereinfachen die Richtlinienverwaltung für Sie, da sie die Anzahl der Richtliniendefinitionen, die Sie erstellen müssen, reduzieren. Beim Erstellen einer Richtliniendefinition können Sie Parameter definieren, damit sie allgemeingültiger ist. Anschließend können Sie diese Richtliniendefinition für verschiedene Szenarien wiederverwenden. Dazu übergeben Sie unterschiedliche Werte beim Zuweisen der Richtliniendefinition. Geben Sie z.B. eine Gruppe von Speicherorten für ein Abonnement an.

Parameter werden beim Erstellen einer Richtliniendefinition definiert. Bei der Definition eines Parameters wird ein Name und optional ein Wert vergeben. Sie können z.B. einen Parameter für eine Richtlinie mit dem Titel _location_ definieren. Dann können Sie ihm beim Zuweisen einer Richtlinie unterschiedliche Werte wie z.B. _EastUS_ oder _WestUS_ geben.

Weitere Informationen zu Richtlinienparametern finden Sie unter [Struktur von Azure Policy-Definitionen – Parameter](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Initiativdefinition

Eine Initiativendefinition ist eine Auflistung von Richtliniendefinitionen, die auf das Erreichen eines einzigen übergeordneten Ziels ausgerichtet sind. Initiativdefinitionen vereinfachen das Verwalten und Zuweisen von Richtliniendefinitionen. Die Vereinfachung besteht im Gruppieren einer Reihe von Richtlinien zu einem einzelnen Element. Beispielsweise können Sie eine Initiative mit dem Titel **Überwachung im Azure Security Center aktivieren** mit dem Ziel erstellen, alle vorhandenen Sicherheitsempfehlungen in Ihrem Azure Security Center zu überwachen.

> [!NOTE]
> Das SDK, etwa Azure CLI und Azure PowerShell, verwendet zum Verweisen auf Initiativen Eigenschaften und Parameter mit dem Namen **PolicySet**.

Im Rahmen dieser Initiative würden Sie Richtliniendefinitionen wie etwa Folgende haben:

- **Unverschlüsselte SQL-Datenbank in Security Center überwachen**: Zum Überwachen von unverschlüsselten SQL-Datenbanken und -Servern
- **Betriebssystem-Sicherheitsrisiken in Security Center überwachen**: Zum Überwachen von Servern, die die konfigurierte Baseline nicht erfüllen
- **Fehlende Endpoint Protection-Instanz in Security Center überwachen**: Zum Überwachen von Servern ohne installierten Endpoint Protection-Agent

Genau wie Richtlinienparameter vereinfachen auch Initiativparameter die Initiativverwaltung, indem sie die Redundanz verringern. Bei Initiativparametern handelt es sich um Parameter, die von den Richtliniendefinitionen innerhalb der Initiative verwendet werden.

Stellen Sie sich beispielsweise ein Szenario vor, in dem Sie die Initiativdefinition **initiativeC** mit den Richtliniendefinitionen **policyA** und **policyB** verwenden, die jeweils einen anderen Parametertyp erwarten:

| Richtlinie | Name des Parameters |Typ des Parameters  |Hinweis |
|---|---|---|---|
| policyA | allowedLocations | array  |Dieser Parameter erwartet eine Liste von Zeichenfolgen als Wert, da der Parametertyp als Array definiert wurde. |
| policyB | allowedSingleLocation |Zeichenfolge |Dieser Parameter erwartet ein Wort als Wert, da der Parametertyp als Zeichenfolge definiert wurde. |

In diesem Szenario haben Sie bei der Definition der Initiativparameter für **initiativeC** drei Optionen:

- Verwenden Sie die Parameter der Richtliniendefinitionen innerhalb dieser Initiative: In diesem Beispiel werden _allowedLocations_ und _allowedSingleLocation_ zu Initiativparametern für **initiativeC**.
- Geben Sie Werte für die Parameter der Richtliniendefinitionen innerhalb dieser Initiativdefinition ein. In diesem Beispiel können Sie eine Liste von Standorten für den Parameter von **policyA** (**allowedLocations**) und den Parameter von **policyB** (**allowedSingleLocation**) zur Verfügung stellen. Bei der Zuweisung dieser Initiative können Sie auch Werte bereitstellen.
- Geben Sie eine Liste von _Wertoptionen_ an, die bei der Zuweisung dieser Initiative verwendet werden können. Damit dürfen die von den Richtliniendefinitionen geerbten Parameter innerhalb der Initiative bei der Zuweisung dieser Initiative nur Werte aus der angegebenen Liste enthalten.

Beim Erstellen von Wertoptionen in einer Initiativdefinition können Sie während der Initiativenzuweisung keinen anderen Wert eingeben, da er nicht Teil der Liste ist.

Weitere Informationen zu den Strukturen von Initiativdefinitionen finden Sie unter [Struktur der Initiativdefinition](./concepts/initiative-definition-structure.md).

### <a name="assignments"></a>Zuweisungen

Eine Zuweisung ist eine zugewiesene Richtliniendefinition oder -initiative, die innerhalb eines bestimmten Bereichs angewendet werden soll. Ein solcher Bereich kann sich von einer [Verwaltungsgruppe](../management-groups/overview.md) bis zu einer einzelnen Ressource erstrecken. Der Begriff _Bereich_ bezieht sich auf alle Ressourcen, Ressourcengruppen, Abonnements oder Verwaltungsgruppen, denen die Definition zugewiesen ist. Zuweisungen werden von allen untergeordneten Ressourcen geerbt. Dies bedeutet, dass eine auf eine Ressourcengruppe angewendete Definition auch auf die Ressourcen in dieser Ressourcengruppe angewendet wird. Sie können jedoch einen Unterbereich von der Zuweisung ausschließen.

Im Abonnementbereich können Sie beispielsweise eine Definition zuweisen, die die Erstellung von Netzwerkressourcen verhindert. Sie können eine Ressourcengruppe in diesem Abonnement ausschließen, die für die Netzwerkinfrastruktur vorgesehen ist. Sie gewähren Benutzern, denen Sie in Bezug auf das Erstellen von Netzwerkressourcen vertrauen, dann Zugriff auf diese Netzwerkressourcengruppe.

Als weiteres Beispiel möchten Sie möglicherweise eine Definition für eine Ressourcentyp-Positivliste auf der Verwaltungsgruppenebene zuweisen. Dann weisen Sie einer untergeordneten Verwaltungsgruppe (oder sogar Abonnements direkt) eine weniger restriktive Richtlinie zu, die mehr Ressourcentypen zulässt. Dieses Beispiel funktioniert jedoch nicht, da es sich bei Azure Policy um ein explizites Verbotssystem handelt. Stattdessen müssen Sie die untergeordnete Verwaltungsgruppe oder das Abonnement aus der Zuweisung auf der Verwaltungsgruppenebene ausschließen. Anschließend müssen Sie die weniger restriktive Definition auf der Ebene der untergeordneten Verwaltungsgruppe oder des Abonnements zuweisen. Falls eine Ressource aufgrund einer Zuweisung abgelehnt wird, muss zum Zulassen der Ressource die ablehnende Zuweisung geändert werden.

Weitere Informationen zum Einrichten von Zuweisungen über das Portal finden Sie unter [Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen in Ihrer Azure-Umgebung](./assign-policy-portal.md). Schritte für [PowerShell](./assign-policy-powershell.md) und die [Azure-Befehlszeilenschnittstelle](./assign-policy-azurecli.md) sind ebenfalls verfügbar. Informationen zur Zuweisungsstruktur finden Sie unter [Zuweisungsstruktur](./concepts/assignment-structure.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Maximale Anzahl von Azure Policy-Objekten

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun einen Überblick über Azure Policy erhalten und einige Schlüsselbegriffe kennengelernt haben, geht es mit den nächsten empfohlenen Schritten weiter:

- [Befassen Sie sich mit der Struktur von Azure Policy-Definitionen](./concepts/definition-structure.md).
- [Zuweisen einer Richtliniendefinition mit dem Portal](./assign-policy-portal.md)
