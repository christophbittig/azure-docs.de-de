---
title: Verschieben einer öffentlichen Azure-IP-Konfiguration in eine andere Azure-Region mit dem Azure-Portal
description: In diesem Artikel erhalten Sie Informationen zum Verwenden einer Vorlage, um eine öffentliche Azure-IP-Konfiguration mit dem Azure-Portal aus einer Azure-Region in eine andere zu verschieben.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6deb906d55f74cc3d418473a669a81d3d2ddf5e4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130214661"
---
# <a name="move-azure-public-ip-configuration-to-another-region-using-the-azure-portal"></a>Verschieben einer öffentlichen Azure-IP-Konfiguration in eine andere Region mit dem Azure-Portal

Es gibt verschiedene Szenarios, in denen Sie Ihre vorhandenen öffentlichen Azure-IP-Konfigurationen aus einer Region in eine andere verschieben sollten. Beispielsweise könnte es sein, dass Sie für Testzwecke eine öffentliche IP-Adresse mit derselben Konfiguration und demselben Tarif erstellen möchten. Möglicherweise sollten Sie eine öffentliche IP-Konfiguration auch im Rahmen der Planung einer Notfallwiederherstellung in eine andere Region verschieben.

**Öffentliche Azure-IP-Adressen sind regionsspezifisch und können nicht aus einer Region in eine andere verschoben werden.** Sie können jedoch eine Azure Resource Manager-Vorlage verwenden, um die vorhandene Konfiguration einer öffentlichen IP-Adresse zu exportieren.  Anschließend können Sie die Ressource in einer anderen Region stagen, indem Sie die öffentliche IP-Adresse in eine Vorlage exportieren, die Parameter so ändern, dass sie der Zielregion entsprechen, und die Vorlage dann in der neuen Region bereitstellen.  Weitere Informationen zu Resource Manager und Vorlagen finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).


## <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass sich die öffentliche IP-Adresse in der Azure-Region befindet, aus der Sie verschieben möchten.

- Öffentliche Azure-IP-Adressen können nicht zwischen Regionen verschoben werden.  Sie müssen die neue öffentliche IP-Adresse Ressourcen in der Zielregion zuordnen.

- Damit Sie eine öffentliche IP-Adresse exportieren und eine Vorlage bereitstellen können, um die öffentliche IP-Adresse in einer anderen Region zu erstellen, benötigen Sie mindestens die Rolle „Netzwerkmitwirkender“.

- Identifizieren Sie das Layout des Quellnetzwerks und alle Ressourcen, die Sie aktuell verwenden. Dieses Layout umfasst Load Balancer, Netzwerksicherheitsgruppen (NSGs) und virtuelle Netzwerke, ohne darauf beschränkt zu sein.

- Vergewissern Sie sich, dass Sie mit Ihrem Azure-Abonnement öffentliche IP-Adressen in der verwendeten Zielregion erstellen können. Wenden Sie sich an den Support, um das erforderliche Kontingent zu aktivieren.

- Stellen Sie sicher, dass Ihr Abonnement genügend Ressourcen hat, um das Hinzufügen von öffentlichen IP-Adressen für diesen Prozess zu unterstützen.  Weitere Informationen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).


## <a name="prepare-and-move"></a>Vorbereiten und Verschieben
In den folgenden Schritten wird gezeigt, wie Sie die öffentliche IP-Adresse für die Konfigurationsverschiebung mit einer Resource Manager-Vorlage vorbereiten und die Konfiguration der öffentlichen IP-Adresse dann über das Azure-Portal in die Zielregion verschieben.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportieren der Vorlage und Bereitstellen über ein Skript

1. Melden Sie sich bei [Azure-Portal](https://portal.azure.com) > **Ressourcengruppen** an.
2. Suchen Sie die Ressourcengruppe, die die öffentliche IP-Adresse der Quelle enthält, und klicken Sie darauf.
3. Wählen Sie **Einstellungen** > **Vorlage exportieren** aus.
4. Wählen Sie **Bereitstellen** auf dem Blatt **Vorlage exportieren** aus.
5. Klicken Sie auf **VORLAGE** > **Parameter bearbeiten**, um die Datei **parameters.json** im Online-Editor zu öffnen.
8. Um den Parameter für den Namen der öffentlichen IP-Adresse zu bearbeiten, ändern Sie die Eigenschaft unter **Parameter** > **Wert** vom Namen der öffentlichen IP-Adresse der Quelle in den Namen der öffentliche IP-Adresse des Ziels. Setzen Sie den Namen unbedingt in Anführungszeichen:

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Klicken Sie im Editor auf **Speichern**.

9.  Klicken Sie auf **VORLAGE** > **Parameter bearbeiten**, um die Datei **parameters.json** im Online-Editor zu öffnen.

10. Zum Bearbeiten der Zielregion, in die die öffentliche IP-Adresse verschoben wird, ändern Sie unter **resources** die Eigenschaft **location**:

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Informationen zum Abrufen von Regionsstandortcodes finden Sie unter [Azure-Standorte](https://azure.microsoft.com/global-infrastructure/locations/).  Der Code für eine Region ist der Regionsname ohne Leerzeichen, **USA, Mitte** = **centralus**.

12. Sie können wahlweise auch andere Parameter in der Vorlage ändern, die abhängig von Ihren Anforderungen optional sind:

    * **SKU**: Sie können die SKU der öffentlichen IP-Adresse in der Konfiguration von „standard“ in „basic“ oder von „basic“ in „standard“ ändern, indem Sie die Eigenschaft **sku** > **name** in der Datei **template.json** ändern:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Weitere Informationen zu den Unterschieden zwischen den SKUs „basic“ und „standard“ für öffentliche IP-Adressen finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](./ip-services/virtual-network-public-ip-address.md):

    * **Zuweisungsmethode für öffentliche IP** und **Leerlauftimeout**: Sie können beide Optionen in der Vorlage ändern, indem Sie die Eigenschaft **publicIPAllocationMethod** aus **Dynamic** in **Static** oder aus **Static** in **Dynamic** ändern. Das Leerlauftimeout kann geändert werden, indem Sie die Eigenschaft **idleTimeoutInMinutes** auf den gewünschten Wert festlegen.  Der Standardwert ist **4**:

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Weitere Informationen zu den Zuweisungsmethoden und den Werten für das Leerlauftimeout finden Sie unter [Erstellen, Ändern oder Löschen einer öffentlichen IP-Adresse](./ip-services/virtual-network-public-ip-address.md).


13. Klicken Sie im Online-Editor auf **Speichern**.

14. Klicken Sie auf **GRUNDEINSTELLUNGEN** > **Abonnement**, um das Abonnement auszuwählen, in dem die IP-Adresse des Ziels bereitgestellt wird.

15. Klicken Sie auf **GRUNDEINSTELLUNGEN** > **Ressourcengruppe**, um die Ressourcengruppe auszuwählen, in der die IP-Adresse des Ziels bereitgestellt wird.  Sie können auf **Neu erstellen** klicken, um eine neue Ressourcengruppe für die IP-Adresse des Ziels zu erstellen.  Stellen Sie sicher, dass der Name nicht mit dem Namen der Quellressourcengruppe der vorhandenen IP-Adresse der Quelle übereinstimmt.

16. Stellen Sie sicher, dass **GRUNDEINSTELLUNGEN** > **Standort** auf den Zielstandort festgelegt ist, an dem die IP-Adresse bereitgestellt werden soll.

17. Stellen Sie unter **EINSTELLUNGEN** sicher, dass der Name mit dem Namen übereinstimmt, den Sie oben im Parameter-Editor eingegeben haben.

18. Aktivieren Sie das Kontrollkästchen unter **NUTZUNGSBEDINGUNGEN**.

19. Klicken Sie auf die Schaltfläche **Kaufen**, um die öffentliche IP-Adresse des Ziels bereitzustellen.

## <a name="discard"></a>Verwerfen

Wenn Sie die öffentliche IP-Adresse des Ziels verwerfen möchten, löschen Sie die Ressourcengruppe, die die öffentliche IP-Adresse des Ziels enthält.  Wählen Sie hierzu im Portal die Ressourcengruppe im Dashboard aus, und klicken Sie oben auf der Übersichtsseite auf **Löschen**.

## <a name="clean-up"></a>Bereinigung

Um die Änderungen zu übernehmen und das Verschieben der öffentlichen IP-Adresse abzuschließen, löschen Sie die öffentliche IP-Adresse der Quelle oder die Quellressourcengruppe. Wählen Sie hierzu im Portal die öffentliche IP-Adresse oder die Ressourcengruppe im Dashboard aus, und klicken Sie oben auf jeder Seite auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine öffentliche Azure-IP-Adresse aus einer Region in eine andere verschoben und die Quellressourcen bereinigt.  Weitere Informationen zum Verschieben von Ressourcen zwischen Regionen und zur Notfallwiederherstellung in Azure finden Sie unter:


- [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Verschieben virtueller Azure-Computer in eine andere Region](../site-recovery/azure-to-azure-tutorial-migrate.md)