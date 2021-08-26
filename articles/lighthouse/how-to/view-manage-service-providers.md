---
title: Dienstanbieter anzeigen und verwalten
description: Kunden können Informationen zu Azure Lighthouse-Dienstanbietern, Angeboten von Dienstanbietern und delegierten Ressourcen im Azure-Portal einsehen.
ms.date: 07/16/2021
ms.topic: how-to
ms.openlocfilehash: fef9d23209e0551f105263c019297df8208e940a
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389167"
---
# <a name="view-and-manage-service-providers"></a>Dienstanbieter anzeigen und verwalten

Die Seite **Dienstanbieter** im [Azure-Portal](https://portal.azure.com) bietet Kunden Steuerungsmöglichkeiten und Transparenz im Zusammenhang mit ihren Dienstanbietern, die [Azure Lighthouse](../overview.md) nutzen. Kunden können bestimmte Ressourcen delegieren, neue oder aktualisierte Angebote prüfen, den Dienstanbieterzugriff entfernen und mehr.

Um diese Seite im Azure-Portal anzuzeigen, wählen Sie **Alle Dienste** aus. Suchen Sie dann nach **Dienstanbieter**, und wählen Sie die Option aus. Diese Seite können Sie auch suchen, indem Sie im Suchfeld am oberen Rand des Azure-Portals „Dienstanbieter“ oder „Azure Lighthouse“ eingeben.

> [!NOTE]
> Um die Seite **Dienstanbieter** anzuzeigen, muss ein Benutzer im Mandanten des Kunden über die integrierte Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) (oder eine andere integrierte Rolle, die Leserzugriff umfasst) verfügen.
>
> Zum Hinzufügen oder Aktualisieren von Angeboten, Delegieren von Ressourcen und Entfernen von Angeboten muss dem Benutzer eine Rolle mit der Berechtigung `Microsoft.Authorization/roleAssignments/write` zugewiesen sein, z. B. [Besitzer](../../role-based-access-control/built-in-roles.md#owner).

Beachten Sie, dass auf der Seite **Dienstanbieter** nur Informationen zu den Dienstanbietern angezeigt werden, die über Azure Lighthouse auf die Abonnements oder Ressourcengruppen des Kunden zugreifen können. Es werden keine Informationen zu zusätzlichen Dienstanbietern angezeigt, die Azure Lighthouse nicht verwenden.

## <a name="view-service-provider-details"></a>Anzeigen von Dienstanbieterdetails

Um Details zu den aktuellen Dienstanbietern anzuzeigen, die Azure Lighthouse im Mandanten des Kunden verwenden, wählen Sie links auf der Seite **Dienstanbieter** die Option **Dienstanbieterangebote** aus.

Für jedes Angebot werden der Name des Dienstanbieters und das zugehörige Angebot angezeigt. Sie können ein Angebot auswählen, um eine Beschreibung und andere Details anzuzeigen, einschließlich der Rollenzuweisungen, die dem Dienstanbieter gewährt wurden.

In der Spalte **Delegierungen** können Sie sehen, wie viele Abonnements und/oder Ressourcengruppen an den Dienstanbieter für dieses Angebot delegiert wurden. Der Dienstanbieter ist in der Lage, auf diese Abonnements und/oder Ressourcengruppen entsprechend den im Angebot angegebenen Zugriffsebenen zuzugreifen und diese dementsprechend zu verwalten.

## <a name="add-service-provider-offers"></a>Hinzufügen von Dienstanbieterangeboten

Zum Hinzufügen eines neuen Dienstanbieterangebots wählen Sie auf der Seite **Dienstanbieterangebote** die Option **Angebot hinzufügen** aus. Wählen Sie **Private Angebote** aus, um Angebote anzuzeigen, die ein Dienstanbieter für diesen Kunden veröffentlicht hat. Sie können dann ein Angebot und anschließend **Einrichten und abonnieren** auswählen.

## <a name="update-service-provider-offers"></a>Aktualisieren von Dienstanbieterangeboten

Nachdem ein Kunde ein Angebot hinzugefügt hat, kann ein Dienstanbieter eine aktualisierte Version desselben Angebots im Azure Marketplace veröffentlichen, z. B. das Hinzufügen einer neuen Rollendefinition. Wenn eine neue Version des Angebots veröffentlicht wurde, wird auf der Seite **Dienstanbieterangebote** ein „Aktualisierungssymbol“ in der Zeile für das Angebot angezeigt. Wählen Sie dieses Symbol aus, um die Unterschiede zwischen der aktuellen Version und der neuen Version des Angebots anzuzeigen.

 ![Symbol für Aktualisierungsangebot](../media/update-offer.jpg)

Nachdem er die Änderungen überprüft hat, kann der Kunde auf die neue Version aktualisieren. Anschließend gelten die in der neuen Version angegebenen Autorisierungen und anderen Einstellungen für alle Abonnements und/oder Ressourcengruppen, die für das Angebot delegiert wurden.

## <a name="remove-service-provider-offers"></a>Entfernen von Dienstanbieterangeboten

Sie können ein Dienstanbieterangebot jederzeit entfernen. Wählen Sie dazu das Papierkorbsymbol in der Zeile des entsprechenden Angebots aus.

Nachdem Sie den Löschvorgang bestätigt haben, hat der Dienstanbieter keinen Zugriff mehr auf die Ressourcen, die zuvor für dieses Angebot delegiert wurden.

## <a name="delegate-resources"></a>Delegieren von Ressourcen

Damit ein Dienstanbieter auf die Ressourcen eines Kunden zugreifen und diese verwalten kann, muss mindestens ein spezifischeres Abonnement und/oder eine spezifischere Ressourcengruppe delegiert werden. Wenn ein Kunde ein Angebot hinzugefügt, aber noch keine Ressourcen delegiert hat, wird am oberen Rand des Abschnitts **Dienstanbieterangebote** ein Hinweis angezeigt. Der Dienstanbieter kann erst nach Abschluss der Delegierung an Ressourcen im Mandanten des Kunden arbeiten.

So delegieren Sie Abonnements oder Ressourcengruppen

1. Aktivieren Sie das Kontrollkästchen für die Zeile, die den Dienstanbieter, das Angebot und den Namen enthält. Wählen Sie dann am oberen Rand des Bildschirms **Ressourcen delegieren** aus.
1. Überprüfen Sie im Abschnitt **Angebotsdetails** der Seite **Ressourcen delegieren** die Details zum Dienstanbieter und Angebot. Um Rollenzuweisungen für das Angebot zu überprüfen, wählen Sie **Klicken Sie hier, um die Details des ausgewählten Angebots anzuzeigen** aus.
1. Wählen Sie im Abschnitt **Delegieren** den Befehl **Abonnements delegieren** oder **Ressourcengruppen delegieren** aus.
1. Wählen Sie die Abonnements und/oder Ressourcengruppen aus, die Sie für dieses Angebot delegieren möchten, und wählen Sie dann **Hinzufügen** aus.
1. Aktivieren Sie das Kontrollkästchen unten auf der Seite, um zu bestätigen, dass Sie diesem Dienstanbieter Zugriff auf die Ressourcen gewähren möchten, die Sie ausgewählt haben, und wählen Sie dann **Delegieren** aus.

## <a name="view-delegations"></a>Anzeigen von Delegierungen

Delegierungen stellen eine Zuordnung bestimmter Kundenressourcen (Abonnements und/oder Ressourcengruppen) zu Rollenzuweisungen dar, die dem Dienstanbieter Berechtigungen für diese Ressourcen gewähren. Um Delegierungsdetails anzuzeigen, wählen Sie im linken Bereich der Seite **Dienstanbieter** die Option **Delegierungen** aus.

Mithilfe von Filtern am oberen Rand der Seite können Sie Ihre Delegierungsinformationen sortieren. Sie können auch nach bestimmten Kunden, Angeboten oder Schlüsselwörtern filtern.

> [!NOTE]
> Den Kunden werden weder diese Rollenzuweisungen noch Benutzer des Dienstanbietermandanten angezeigt, denen diese Rollen zugewiesen wurden. Dies gilt für die [Anzeige der Rollenzuweisungen für den delegierten Bereich im Azure-Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) ebenso wie für APIs.

## <a name="audit-and-restrict-delegations-in-your-environment"></a>Überwachen und Einschränken von Delegierungen in Ihrer Umgebung

Kunden möchten möglicherweise alle Abonnements und/oder Ressourcengruppen prüfen, die an Azure Lighthouse delegiert wurden. Dies ist besonders nützlich für Kunden mit einer großen Anzahl von Abonnementen oder mit vielen Benutzern, die Verwaltungsaufgaben ausführen.

Wir bieten eine in [Azure Policy integrierte Richtliniendefinition](../../governance/policy/samples/built-in-policies.md#lighthouse), um die [Delegierung von Bereichen an einen verwaltenden Mandanten zu überwachen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Sie können diese Richtlinie einer Verwaltungsgruppe zuordnen, die alle Abonnements umfasst, die Sie überwachen möchten. Wenn Sie die Compliance mit dieser Richtlinie überprüfen, werden alle delegierten Abonnements und/oder Ressourcengruppen (innerhalb der Verwaltungsgruppe, der die Richtlinie zugeordnet ist) im Zustand „nicht kompatibel“ angezeigt. Sie können dann die Ergebnisse überprüfen und bestätigen, dass es keine unerwarteten Delegierungen gibt.

Mit einer weiteren [integrierten Richtliniendefinition](../../governance/policy/samples/built-in-policies.md#lighthouse) können Sie [Delegierungen auf bestimmte verwaltende Mandanten beschränken](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Diese Richtlinie kann einer Verwaltungsgruppe zugewiesen werden, in der alle Abonnements enthalten sind, für die Sie Delegierungen begrenzen möchten. Nach der Bereitstellung der Richtlinie werden alle Versuche abgelehnt, ein Abonnement an einen Mandanten außerhalb der von Ihnen angegebenen zu delegieren.

Weitere Informationen zum Zuweisen einer Richtlinie und zum Anzeigen der Ergebnisse in Bezug auf den Konformitätszustand finden Sie unter [Schnellstart: Erstellen einer Richtlinienzuweisung](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Lighthouse](../overview.md).
- Informieren Sie sich über das [Überwachen der Dienstanbieteraktivität](view-service-provider-activity.md).
- Erfahren Sie, wie Dienstanbieter auf der Seite **Meine Kunden** im Azure-Portal [Kunden anzeigen und verwalten](view-manage-customers.md) können.
- Erfahren Sie, wie [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), die Verwaltung mithilfe von Azure Lighthouse konsolidieren können.
