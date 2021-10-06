---
title: 'Azure ExpressRoute: Konfigurieren von ExpressRoute Direct: Portal'
description: Diese Seite unterstützt Sie beim Konfigurieren von ExpressRoute Direct über das Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 05/05/2021
ms.author: duau
ms.openlocfilehash: d5b7bba4774eb81c684875a4db9ffb1afb2a60aa
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811222"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Erstellen von ExpressRoute Direct mit dem Portal

In diesem Artikel wird gezeigt, wie Sie ExpressRoute Direct mit dem Azure-Portal erstellen.
ExpressRoute Direct ermöglicht es, sich direkt mit dem globalen Netzwerk von Microsoft zu verbinden, und zwar an strategisch über die ganze Welt verteilten Peeringstandorten. Weitere Informationen über ExpressRoute finden Sie unter [Informationen zu ExpressRoute Direct](expressroute-erdirect-about.md).

## <a name="before-you-begin"></a><a name="before"></a>Voraussetzungen

Bevor Sie ExpressRoute Direct nutzen können, müssen Sie zunächst Ihr Abonnement registrieren. Führen Sie zu Ihrer Registrierung folgende Schritte über Azure PowerShell aus:
1.  Melden Sie sich bei Azure an, und wählen Sie das Abonnement aus, für das Sie sich registrieren möchten.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registrieren Sie Ihr Abonnement für die Public Preview mit dem folgenden Befehl:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Überprüfen Sie nach der Registrierung, ob der Ressourcenanbieter **Microsoft.Network** in Ihrem Abonnement registriert ist. Durch Registrieren eines Ressourcenanbieters wird Ihr Abonnement für die Verwendung mit dem Ressourcenanbieter konfiguriert.

1. Greifen Sie wie unter [Azure-Ressourcenanbieter und -typen](../azure-resource-manager/management/resource-providers-and-types.md) beschrieben auf die Abonnementeinstellungen zu.
1. Überprüfen Sie, ob in Ihrem Abonnement unter **Ressourcenanbieter** der Anbieter **Microsoft.Network** den Status **Registriert** aufweist. Wenn der Ressourcenanbieter Microsoft.Network nicht in der Liste der registrierten Anbieter aufgeführt ist, fügen Sie ihn hinzu.

## <a name="create-expressroute-direct"></a><a name="create-erdir"></a>Erstellen von ExpressRoute Direct

1. Wählen Sie im Menü des [Azure-Portals](https://portal.azure.com) oder auf der **Homepage** die Option **Ressource erstellen** aus.

1. Geben Sie auf der Seite **Neu** im Feld **_Marketplace durchsuchen_ *_ den Suchbegriff _* ExpressRoute Direct** ein, und drücken Sie anschließend die **EINGABETASTE**, um zu den Suchergebnissen zu gelangen.

1. Wählen Sie in den Ergebnissen **ExpressRoute Direct** aus.

1. Wählen Sie auf der Seite **ExpressRoute Direct** die Option **Erstellen** aus, um die Seite **ExpressRoute Direct erstellen** zu öffnen.

1. Beginnen Sie, indem Sie die Felder auf der Seite **Grundlagen** ausfüllen.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Seite „Grundlagen“":::

    * **Abonnement**: Das Azure-Abonnement, das Sie zum Erstellen einer neuen ExpressRoute Direct-Ressource verwenden möchten. Die ExpressRoute Direct-Ressource und ExpressRoute-Leitungen müssen sich im selben Abonnement befinden.
    * **Ressourcengruppe**: Die Azure-Ressourcengruppe, in der die neue ExpressRoute Direct-Ressource erstellt wird. Erstellen Sie eine Ressourcengruppe, wenn noch keine vorhanden ist.
    * **Region**: Die öffentliche Azure-Region, in der die Ressource erstellt wird.
    * **Name**: Der Name der neuen ExpressRoute Direct-Ressource.

1. Vervollständigen Sie dann die Felder auf der Seite **Konfiguration**.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Screenshot, der die Seite „Erstellen von ExpressRoute Direct“ mit ausgewählter Registerkarte „Konfiguration“ zeigt.":::

    * **Peeringstandort**: Der Peeringstandort, an dem Sie eine Verbindung mit der ExpressRoute Direct-Ressource herstellen. Weitere Informationen zu Peeringstandorten finden Sie unter [ExpressRoute-Standorte](expressroute-locations-providers.md).
   * **Bandbreite**: Die Portpaarbandbreite, die Sie reservieren möchten. ExpressRoute Direct unterstützt die Bandbreitenoptionen 10 GB und 100 GB. Wenn die gewünschte Bandbreite am angegebenen Peeringstandort nicht verfügbar ist, [öffnen Sie eine Supportanfrage im Azure-Portal](https://aka.ms/azsupt).
   * **Kapselung**: ExpressRoute Direct unterstützt die Kapselungen QinQ und Dot1Q.
     * Bei Wahl von QinQ wird jeder ExpressRoute-Leitung dynamisch ein S-Tag zugewiesen, das in der gesamten ExpressRoute Direct-Ressource eindeutig ist.
     *  Jedes C-Tag für die Leitung muss innerhalb der Leitung eindeutig sein, jedoch nicht innerhalb von ExpressRoute Direct.
     * Wenn die Kapselung Dot1Q ausgewählt wird, müssen Sie die Eindeutigkeit des C-Tags (VLAN) für die gesamte ExpressRoute Direct-Ressource sicherstellen.
     >[!IMPORTANT]
     >Für ExpressRoute Direct kann nur ein Kapselungstyp festgelegt werden. Nach der ExpressRoute Direct-Erstellung kann die Kapselung nicht geändert werden.
     >

1. Geben Sie beliebige Ressourcentags an, und wählen Sie dann **Überprüfen und erstellen** aus, um die ExpressRoute Direct-Ressourceneinstellungen zu überprüfen.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Screenshot, der die Seite „ExpressRoute erstellen“ mit ausgewählter Registerkarte „Überprüfen und erstellen“ zeigt.":::

1. Klicken Sie auf **Erstellen**. Daraufhin wird eine Meldung mit dem Hinweis angezeigt, dass Ihre Bereitstellung ausgeführt wird. Der Status der Ressourcenerstellung wird auf dieser Seite angezeigt. 

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>Generieren des Genehmigungsschreibens (Letter of Authorization, LOA)

1. Wechseln Sie zur Übersichtsseite der ExpressRoute Direct-Ressource, und wählen Sie **Schriftliche Vollmacht generieren** aus.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/overview.png" alt-text="Screenshot der Schaltfläche „Schriftliche Vollmacht generieren“ auf der Übersichtsseite":::

1. Geben Sie den Namen Ihres Unternehmens ein, und wählen Sie **Herunterladen** aus, um die Vollmacht zu generieren.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/letter-of-authorization-page.png" alt-text="Screenshot der Seite „Schriftliche Vollmacht“":::

## <a name="change-admin-state-of-links"></a><a name="state"></a>Ändern des Verwaltungsstatus von Verknüpfungen

Dieser Prozess sollte verwendet werden, um einen Layer-1-Test durchzuführen, der sicherstellt, dass jede Kreuzverbindung ordnungsgemäß in jeden Router für primäre und sekundäre Verbindungen eingebunden ist.

1. Wählen Sie auf der Seite **Übersicht** der ExpressRoute Direct-Ressource im Abschnitt **Links** die Angabe **Link1** aus.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="Link 1" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Schalten Sie die Einstellung **Administratorstatus** auf **Aktiviert** um, und wählen Sie dann **Speichern** aus.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Administratorstatus":::

    >[!IMPORTANT]
    >Die Abrechnung beginnt, wenn der Administratorstatus für einen der beiden Links aktiviert ist.
    >

1. Wiederholen Sie den gleichen Vorgang für **link2**.

## <a name="create-a-circuit"></a><a name="circuit"></a>Erstellen einer Leitung

Sie können standardmäßig 10 Leitungen im Abonnement erstellen, in dem sich die ExpressRoute Direct-Ressource befindet. Dieser Wert kann vom Support erhöht werden. Sie sind für die Nachverfolgung der Werte für die bereitgestellte und belegte Bandbreite zuständig. Die bereitgestellte Bandbreite ist die Summe der Bandbreiten aller Verbindungen der ExpressRoute Direct-Ressource. Die genutzte Bandbreite steht für die physische Nutzung der zugrunde liegenden physischen Schnittstellen.

* Es gibt zusätzliche Leitungsbandbreiten, die für ExpressRoute Direct nur zur Unterstützung der oben beschriebenen Szenarien genutzt werden können. Dies sind: 40 GBit/s und 100 GBit/s.

* SkuTier kann „Local“, „Standard“ oder „Premium“ sein.

* SkuFamily kann nur „MeteredData“ sein. „Unlimited“ wird für ExpressRoute Direct nicht unterstützt.

Die folgenden Schritte unterstützen Sie beim Erstellen einer ExpressRoute-Verbindung aus dem ExpressRoute Direct-Workflow. Wenn Sie es vorziehen, können Sie eine Verbindung auch über den regulären Verbindungsworkflow erstellen, obwohl es für diese Konfiguration keinen Vorteil mit sich bringt, die Workflowschritte des regulären Verbindungsworkflows zu verwenden. Weitere Informationen finden Sie unter [Erstellen und Ändern einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md).

1. Wählen Sie im Abschnitt **ExpressRoute Direct-Einstellungen** die Option **Verbindungen** aus, und wählen Sie dann **+Hinzufügen** aus. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Der Screenshot zeigt die ExpressRoute-Einstellungen mit ausgewählten Verbindungen und hervorgehobener Option „Hinzufügen“." lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Konfigurieren Sie die Einstellungen auf der Seite **Konfiguration**.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Seite „Konfiguration“ – ExpressRoute Direct":::

1. Geben Sie beliebige Ressourcen Tags an, und wählen Sie **Überprüfen und erstellen** aus, um die Werte vor dem Erstellen der Ressource zu validieren.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Überprüfen und erstellen – ExpressRoute Direct":::

1. Klicken Sie auf **Erstellen**. Daraufhin wird eine Meldung mit dem Hinweis angezeigt, dass Ihre Bereitstellung ausgeführt wird. Der Status der Ressourcenerstellung wird auf dieser Seite angezeigt. 

## <a name="next-steps"></a>Nächste Schritte

Nach dem Erstellen der ExpressRoute-Verbindung können Sie [virtuelle Netzwerke mit Ihrer ExpressRoute-Verbindung verknüpfen](expressroute-howto-add-gateway-portal-resource-manager.md).
