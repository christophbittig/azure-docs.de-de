---
title: Anzeigen und Verwalten von Kunden und delegierten Ressourcen im Azure-Portal
description: Als Dienstanbieter oder Unternehmen, der bzw. das Azure Lighthouse verwendet, können Sie alle Ihre delegierten Ressourcen und Abonnements anzeigen, indem Sie im Azure-Portal zu „Meine Kunden“ wechseln.
ms.date: 09/17/2021
ms.topic: how-to
ms.openlocfilehash: 4cce38b435ee2613b885dfad959871579579ddce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673611"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Anzeigen und Verwalten von Kunden und delegierten Ressourcen im Azure-Portal

Dienstanbieter, die [Azure Lighthouse](../overview.md) verwenden, können die Seite **Meine Kunden** im [Azure-Portal](https://portal.azure.com) verwenden, um delegierte Kundenressourcen und -abonnements anzuzeigen.

> [!TIP]
> Zwar beziehen wir uns hier auf Dienstanbieter und Kunden, doch können [Unternehmen, die mehrere Mandanten verwalten](../concepts/enterprise.md), denselben Prozess verwenden, um ihre Verwaltungserfahrung zu konsolidieren.

Um auf die Seite **Meine Kunden** im Azure-Portal zuzugreifen, wählen Sie **Alle Dienste** aus, und suchen Sie dann nach **Meine Kunden**, und wählen Sie es aus. Sie können die Seite auch finden, indem Sie im Suchfeld am oberen Rand des Azure-Portals „Meine Kunden“ eingeben.

Beachten Sie, dass der obere Bereich **Kunden** der Seite **Meine Kunden** nur Informationen zu Kunden anzeigt, die Abonnements oder Ressourcengruppen über Azure Lighthouse an Ihren Azure AD-Mandanten delegiert haben. Wenn Sie mit anderen Kunden zusammenarbeiten (z. B. über das [CSP-Programm (Cloud Solution Provider)](/partner-center/csp-overview)), werden Ihnen keine Informationen zu diesen Kunden im Abschnitt **Kunden** angezeigt, es sei denn, Sie haben ein [Onboarding für ihre Ressourcen in Azure Lighthouse durchgeführt](onboard-customer.md). (Ihnen werden jedoch Informationen zu bestimmten CSP-Kunden im Abschnitt [Cloud Solution Provider (Vorschau)](#cloud-solution-provider-preview) unten auf der Seite angezeigt.)

> [!NOTE]
> Ihre Kunden können Informationen zu Dienstanbietern anzeigen, indem sie im Azure-Portal zu **Dienstanbieter** navigieren. Weitere Informationen finden Sie unter [Anzeigen und Verwalten von Dienstanbietern](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Anzeigen und Verwalten von Kundendetails

Um Kundendetails anzuzeigen, wählen Sie im linken Bereich der Seite **Meine Kunden** die Option **Kunden** aus.

> [!IMPORTANT]
> Damit diese Informationen für Benutzer angezeigt werden, muss diesen während des Onboardings die Rolle [Leser](../../role-based-access-control/built-in-roles.md#reader) (oder eine andere integrierte Rolle mit Lesezugriff) erteilt werden.

Für jeden Kunden werden der Name, die Kunden-ID (Mandanten-ID) und die **Angebots-ID** sowie die **Angebotsversion** angezeigt, die zur Kundenbindung gehören. In der Spalte **Delegierungen** wird die Anzahl der delegierten Abonnements und/oder die Anzahl der delegierten Ressourcengruppen angezeigt.

Mithilfe der Optionen ganz oben auf der Seite können Sie die Kundeninformationen nach Kunden, Angeboten oder Schlüsselwörtern sortieren, filtern und gruppieren.

Auf dieser Seite können Sie die folgenden Informationen anzeigen:

- Um alle mit einem Kunden verbundenen Abonnements, Angebote und Delegierungen anzuzeigen, wählen Sie den Namen des Kunden aus.
- Um weitere Details zu einem Angebot und seinen Delegierungen anzuzeigen, wählen Sie den Angebotsnamen aus.
- Um weitere Details zu Rollenzuweisungen für delegierte Abonnements oder Ressourcengruppen anzuzeigen, wählen Sie den Eintrag in der Spalte **Delegierungen** aus.

> [!NOTE]
> Wenn ein Kunde ein Abonnement nach der Delegierung umbenennt, wird der aktualisierte Abonnementname angezeigt. Wenn der Mandant umbenannt wird, wird an manchen Stellen im Azure-Portal möglicherweise weiterhin der ältere Mandantenname angezeigt.

## <a name="view-and-manage-delegations"></a>Anzeigen und Verwalten von Delegierungen

Delegierungen zeigen das Abonnement oder die Ressourcengruppe an, das bzw. die delegiert wurde, zusammen mit den Benutzern und Berechtigungen, die Zugriff darauf haben. Um diese Informationen anzuzeigen, wählen Sie im linken Bereich der Seite **Meine Kunden** die Option **Delegierungen** aus.

Mithilfe der Optionen ganz oben auf der Seite können Sie die diese Informationen nach Kunden, Angeboten oder Schlüsselwörtern sortieren, filtern und gruppieren.

### <a name="view-role-assignments"></a>Anzeigen von Rollenzuweisungen

Die den einzelnen Delegierungen zugeordneten Benutzer und Berechtigungen werden in der Spalte **Rollenzuweisungen** angezeigt. Sie können jeden Eintrag auswählen, um die vollständige Liste der Benutzer, Gruppen und Dienstprinzipale anzuzeigen, denen der Zugriff auf das Abonnement oder die Ressourcengruppe gewährt wurde. Von dort aus können Sie einen bestimmten Benutzer, eine bestimmte Gruppe oder einen bestimmten Dienstprinzipalnamen auswählen, um weitere Details zu erhalten.

### <a name="remove-delegations"></a>Entfernen von Delegierungen

Wenn Sie beim Onboarding eines Kunden in Azure Lighthouse Benutzer mit der Rolle [Löschen von Zuweisungen für Registrierungen bei verwalteten Diensten](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) eingeschlossen haben, können diese Benutzer eine Delegierung entfernen, indem sie das Papierkorbsymbol in der Zeile für diese Delegierung auswählen. In diesem Fall können keine Benutzer im Mandanten des Dienstanbieters auf die zuvor delegierten Ressourcen zugreifen.

Weitere Informationen finden Sie unter [Entfernen des Zugriffs auf eine Delegierung](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Anzeigen der Aktivität zum Ändern einer Delegierung

Der Abschnitt **Aktivitätsprotokoll** der Seite **Meine Kunden** überwacht jedes Mal, wenn Kundenabonnements oder Ressourcengruppen an Ihren Mandanten delegiert werden und wenn zuvor delegierte Ressourcen entfernt werden. Diese Informationen können nur von Benutzern angezeigt werden, denen [die Rolle „Benutzer mit Leseberechtigung für Überwachungsdaten“ im Stammbereich zugewiesen wurde](monitor-delegation-changes.md).

Weitere Informationen finden Sie unter [Anzeigen von Delegierungsänderungen im Azure-Portal](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Arbeiten im Kontext eines delegierten Abonnements

Sie können direkt im Kontext eines delegierten Abonnements innerhalb des Azure-Portals arbeiten, ohne das Verzeichnis wechseln zu müssen, bei dem Sie angemeldet sind. Gehen Sie folgendermaßen vor:

1. Klicken Sie auf **Verzeichnisse + Abonnements** oder auf das Symbol **Einstellungen** oben im Azure-Portal.
1. Stellen Sie auf der Seite [Verzeichnisse + Abonnements](../../azure-portal/set-preferences.md#directories--subscriptions) für Einstellungen sicher, dass die Umschaltfläche **Erweiterte Filter** [deaktiviert](../../azure-portal/set-preferences.md#subscription-filters) ist.
1. Wählen Sie im Abschnitt **Standardabonnementfilter** das entsprechende Verzeichnis und Abonnement aus. (Wenn Ihnen der Zugriff auf eine oder mehrere Ressourcengruppen anstelle des gesamten Abonnements erteilt wurde, wählen Sie das Abonnement aus, zu dem diese Ressourcengruppe gehört. Sie arbeiten dann im Kontext dieses Abonnements, können aber nur auf die vorgesehenen Ressourcengruppen zugreifen.)

:::image type="content" source="../media/subscription-filter-delegated.png" alt-text="Screenshot: Standardabonnementfilter mit einem ausgewählten delegierten Abonnement.":::

Wenn Sie anschließend auf einen Dienst zugreifen, der [mandantenübergreifende Verwaltungsfunktionen](../concepts/cross-tenant-management-experience.md) unterstützt, wird der Dienst standardmäßig den Kontext des delegierten Abonnements verwenden, das Sie in Ihren Filter eingeschlossen haben.

Sie können das Standardabonnement jederzeit ändern, indem Sie die oben genannten Schritte ausführen und ein anderes Abonnement oder mehrere Abonnements auswählen. Sie können alternativ **Alle Verzeichnisse** auswählen und dann das Kontrollkästchen **Alle auswählen** aktivieren, wenn der Filter alle Abonnements enthalten soll, auf die Sie Zugriff haben.

:::image type="content" source="../media/subscription-filter-all.png" alt-text="Screenshot des Standardabonnementfilters mit Auswahl aller Verzeichnisse und Abonnements":::

> [!IMPORTANT]
> Durch Aktivieren des Kontrollkästchens **Alle auswählen** wird der Filter dafür eingestellt, alle Abonnements anzuzeigen, auf die Sie *aktuell* Zugriff haben. Wenn Sie später Zugriff auf zusätzliche Abonnements erhalten – z. B. nachdem Sie einen neuen Kunden in Azure Lighthouse integriert haben –, werden diese Abonnements Ihrem Filter nicht automatisch hinzugefügt. Sie müssen zu **Verzeichnisse und Abonnements** zurückkehren und die zusätzlichen Abonnements auswählen (oder **Alle auswählen** deaktivieren und anschließend wieder aktivieren).

Sie können auch auf Funktionen im Zusammenhang mit delegierten Abonnements oder Ressourcengruppen aus Diensten heraus zugreifen, die mandantenübergreifende Verwaltungsfunktionen unterstützen, indem Sie das Abonnement oder die Ressourcengruppe aus einem einzelnen Dienst heraus auswählen.

## <a name="cloud-solution-provider-preview"></a>Cloud Solution Provider (Vorschau)

Im separaten Abschnitt **Cloud Solution Provider (Vorschau)** der Seite **Meine Kunden** finden Sie Rechnungsinformationen und Ressourcen für Ihre CSP-Kunden, die der [Microsoft-Kundenvereinbarung zugestimmt haben](/partner-center/confirm-customer-agreement) und dem [Azure-Plan unterliegen](/partner-center/azure-plan-get-started). Weitere Informationen finden Sie unter [Erste Schritte mit Ihrem Abrechnungskonto für die Microsoft Partner-Vereinbarung](../../cost-management-billing/understand/mpa-overview.md).

Diese CSP-Kunden werden unabhängig davon in diesem Abschnitt angezeigt, ob Sie für sie das Onboarding in Azure Lighthouse durchgeführt haben. Ebenso muss ein CSP-Kunde nicht im Abschnitt **Cloud Solution Provider (Vorschau)** auf der Seite **Meine Kunden** angezeigt werden, damit Sie das Onboarding dieses Kunden in Azure Lighthouse durchführen können.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie über [Mandantenübergreifende Verwaltungsmöglichkeiten](../concepts/cross-tenant-management-experience.md).
- Erfahren Sie, wie Ihre Kunden [Dienstanbieter anzeigen und verwalten können](view-manage-service-providers.md), indem sie im Azure-Portal zu **Dienstanbieter** navigieren.
