---
title: Aktualisieren einer Delegierung
description: Hier erfahren Sie, wie Sie eine Delegierung für einen Kunden aktualisieren, für den zuvor ein Onboarding in Azure Lighthouse durchgeführt wurde.
ms.date: 09/08/2021
ms.topic: how-to
ms.openlocfilehash: 41ba14433f1af7abb60034e4034f0585ed9968f4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736679"
---
# <a name="update-a-delegation"></a>Aktualisieren einer Delegierung

Nachdem Sie ein Onboarding eines Abonnements (oder einer Ressourcengruppe) in Azure Lighthouse durchgeführt haben, müssen Sie möglicherweise Änderungen vornehmen. Möglicherweise wünscht Ihr Kunde, dass Sie zusätzliche Verwaltungsaufgaben übernehmen, die eine andere integrierte Azure-Rolle erfordern, oder vielleicht müssen Sie den Mandanten ändern, an den ein Kundenabonnement delegiert ist.

> [!TIP]
> In diesem Thema ist zwar von Dienstanbietern und Kunden die Rede, aber auch [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), können mithilfe desselben Verfahrens Azure Lighthouse einrichten und ihre Verwaltungsumgebung konsolidieren.

Wenn Sie das [Onboarding Ihres Kunden mithilfe von ARM-Vorlagen (Azure Resource Manager) durchgeführt haben](onboard-customer.md), muss für diesen Kunden eine neue Bereitstellung erfolgen. Je nachdem, was Sie ändern, müssen Sie möglicherweise das ursprüngliche Angebot aktualisieren oder das ursprüngliche Angebot entfernen und ein neues erstellen.

- **Wenn Sie nur Autorisierungen ändern, gilt Folgendes**: Sie können die Delegierung aktualisieren, indem Sie nur den Abschnitt **authorizations** der ARM-Vorlage ändern.
- **Wenn Sie den Verwaltungsmandanten ändern, gilt Folgendes**: Sie müssen eine neue ARM-Vorlage mit einem anderen **mspOfferName** als bei Ihrem vorherigen Angebot erstellen.

## <a name="update-your-arm-template"></a>Aktualisieren Ihrer ARM-Vorlage

Um die Delegierung zu aktualisieren, müssen Sie eine ARM-Vorlage bereitstellen, die die von Ihnen gewünschten Änderungen enthält.

Wenn Sie nur Autorisierungen aktualisieren (beispielsweise durch Hinzufügen einer neuen Benutzergruppe mit einer dieser bisher nicht zugewiesenen Rolle oder durch Ändern der Rolle für einen vorhandenen Benutzer), können Sie denselben **mspOfferName** verwenden wie in der [ARM-Vorlage](onboard-customer.md#create-an-azure-resource-manager-template), die Sie für die vorherige Delegierung verwendet haben. Sie können Ihre vorherige Vorlage als Ausgangspunkt verwenden. Dann können Sie die gewünschten Änderungen vornehmen: Sie können z. B. eine integrierte Azure-Rolle durch eine andere ersetzen oder der Vorlage eine vollständig neue Autorisierung hinzufügen.

Wenn Sie den **mspOfferName** ändern, wird dies als neues, separates Angebot betrachtet. Dies ist erforderlich, wenn Sie den Verwaltungsmandanten ändern.

Es ist nicht notwendig, den **mspOfferName** zu ändern, wenn der Verwaltungsmandant unverändert bleibt. In den meisten Fällen empfiehlt es sich, für ein und denselben Kunden und Verwaltungsmandanten nur einen einzigen **mspOfferName** zu verwenden. Wenn Sie den Namen ändern, vergewissern Sie sich, dass die vorherige Delegierung des Kunden entfernt wurde, bevor Sie die neue bereitstellen.

## <a name="remove-the-previous-delegation"></a>Entfernen der vorherigen Delegierung

Bevor Sie eine neue Bereitstellung durchführen, müssen Sie den [Zugriff auf die vorherige Delegierung entfernen](remove-delegation.md). Damit stellen Sie sicher, dass alle vorherigen Berechtigungen entfernt werden, sodass Sie mit genau den Benutzern, Gruppen und Rollen von vorne beginnen können, die in Zukunft gelten sollen.

> [!IMPORTANT]
> Wenn Sie einen neuen **mspOfferName** verwenden und dieselben **principalid**-Werte beibehalten, müssen Sie den Zugriff auf die vorherige Delegierung entfernen, bevor Sie das neue Angebot bereitstellen können. Wenn Sie das Angebot nicht zuerst entfernen, können Benutzer, denen zuvor Berechtigungen zugewiesen worden waren, aufgrund von in Konflikt stehenden Zuweisungen den Zugriff möglicherweise ganz verlieren.

Bei einer Änderung des Verwaltungsmandanten können Sie das vorherige Angebot beibehalten, wenn beide Mandanten weiterhin Zugriff haben sollen. Wenn nur der neue Verwaltungsmandant Zugriff haben soll, müssen Sie das vorherige Angebot entfernen. Dies kann vor oder nach dem Onboarding des neuen Angebots erfolgen.

Wenn Sie nur Autorisierungen im Angebot anpassen und denselben **mspOfferName** beibehalten, müssen Sie die vorherige Delegierung nicht entfernen. Die neue Bereitstellung ersetzt die vorherige Delegierung, und nur die Autorisierungen in der neuesten Vorlage werden angewendet.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagramm, das zeigt, wann Sie mspOfferName ändern und eine vorherige Delegierung entfernen müssen":::

Jeder Benutzer im Verwaltungsmandanten, dem in der ursprünglichen Delegierung die [Rolle „Registrierungszuweisung für verwaltete Dienste löschen“](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) zugewiesen wurde, kann den Zugriff auf die Delegierung entfernen. Wenn kein Benutzer im Verwaltungsmandanten über diese Rolle verfügt, können Sie den Kunden bitten, [den Zugriff auf das Angebot im Azure-Portal zu entfernen](view-manage-service-providers.md#remove-service-provider-offers).

> [!TIP]
> Wenn Sie die vorherige Delegierung anhand der oben beschriebenen Schritte entfernt haben und die neue ARM-Vorlage dennoch nicht bereitstellen können, müssen Sie möglicherweise [die Registrierungsdefinition vollständig entfernen](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Dieser Vorgang kann von jedem Benutzer mit einer Rolle im Kundenmandanten durchgeführt werden, die die Berechtigung `Microsoft.Authorization/roleAssignments/write` beinhaltet, z. B. [Besitzer](../../role-based-access-control/built-in-roles.md#owner).  

## <a name="deploy-the-arm-template"></a>Bereitstellen der ARM-Vorlage

Zum [Bereitstellen der aktualisierten Vorlage](onboard-customer.md#deploy-the-azure-resource-manager-template) kann Ihr Kunde dieselben Methoden verwenden wie bei der ursprünglichen Vorlage: Azure-Portal, PowerShell oder Azure CLI.

Nach Abschluss der Bereitstellung [vergewissern Sie sich, dass sie erfolgreich war](onboard-customer.md#confirm-successful-onboarding). Die aktualisierten Autorisierungen treten dann für das Abonnement oder die Ressourcengruppe in Kraft, das bzw. die der Kunde delegiert hat.

## <a name="updating-managed-service-offers"></a>Aktualisieren von Angeboten für verwaltete Dienste

Wenn Sie das Onboarding Ihres Kunden über ein im Azure Marketplace veröffentlichtes Angebot für verwaltete Dienste durchgeführt haben und die Autorisierungen aktualisieren möchten, erreichen Sie dies wie folgt: [Veröffentlichen Sie eine neue Version Ihres Angebots](../../marketplace/update-existing-offer.md) mit den [Autorisierungen](../../marketplace/create-managed-service-offer-plans.md#authorizations), die Sie im Plan für diesen Kunden in aktualisierter Form verwenden möchten. Kund*innen können dann die [Änderungen im Azure-Portal überprüfen und die neue Version akzeptieren](view-manage-service-providers.md#update-service-provider-offers).

Wenn Sie den Verwaltungsmandanten ändern möchten, müssen Sie [ein neues Angebot für verwaltete Dienste erstellen und veröffentlichen](publish-managed-services-offers.md), das der Kunde akzeptieren kann.

> [!IMPORTANT]
> Wie bereits erwähnt, empfiehlt es sich, für ein und dieselben Kund*innen und Verwaltungsmandanten nicht mehrere verschiedene Angebote zu verwenden. Wenn Sie ein neues Angebot für einen Kunden veröffentlichen, das denselben Verwaltungsmandanten verwendet, stellen Sie sicher, dass das ältere Angebot entfernt wird, bevor der Kunde das neuere Angebot annimmt.

## <a name="next-steps"></a>Nächste Schritte

- [Anzeigen und Verwalten von Kunden](view-manage-customers.md), indem sie im Azure-Portal zu **Meine Kunden** navigieren.
- Erfahren Sie, wie Sie den [Zugriff auf eine Delegierung entfernen](remove-delegation.md), für die zuvor ein Onboarding durchgeführt wurde.
- Weitere Informationen zur [Azure Lighthouse-Architektur](../concepts/architecture.md).
