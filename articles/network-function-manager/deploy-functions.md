---
title: 'Tutorial: Bereitstellen von Netzwerkfunktionen in Azure Stack Edge'
titleSuffix: Azure Network Function Manager
description: In diesem Tutorial lernen Sie, wie Sie eine Netzwerkfunktion als verwaltete Anwendung bereitstellen.
author: prmitt
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: prmitt
ms.openlocfilehash: 0e45f8db150505b323f45cd79eed40215b3a44f8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223327"
---
# <a name="tutorial-deploy-network-functions-on-azure-stack-edge-preview"></a>Tutorial: Bereitstellen von Netzwerkfunktionen in Azure Stack Edge (Vorschauversion)

In diesem Tutorial lernen Sie, wie Sie eine Netzwerkfunktion über den Azure Marketplace in Azure Stack Edge bereitstellen. Der Netzwerkfunktions-Manager ermöglicht die vereinfachte Bereitstellung von Azure Managed Applications-Apps in Azure Stack Edge.

> [!div class="checklist"]
> * [Voraussetzungen](overview.md#prereq) überprüfen
> * Netzwerkfunktion erstellen
> * Details der Netzwerkfunktion prüfen

## <a name="prerequisites"></a>Voraussetzungen

* Sie haben eine Geräteressource für den Netzwerkfunktions-Manager erstellt. Wenn Sie diese Schritte nicht abgeschlossen haben, finden Sie weitere Informationen unter [Erstellen einer Geräteressource](create-device.md).
* Überprüfen Sie auf der Registerkarte **Übersicht** für das Gerät, ob die folgenden Werte vorhanden sind:
  * Bereitstellungsstatus = Erfolgreich
  * Gerätestatus = Registriert

## <a name="create-a-network-function"></a><a name="create"></a>Erstellen einer Netzwerkfunktion

1. Melden Sie sich beim [Azure-Vorschauportal](https://aka.ms/AzureNetworkFunctionManager) an.
1. Navigieren Sie zu der **Geräteressource**, in der Sie eine Netzwerkfunktion bereitstellen möchten, und wählen Sie **+Netzwerkfunktion erstellen** aus.

   :::image type="content" source="./media/deploy-functions/create-network-function.png" alt-text="Screenshot: Option „+Netzwerkfunktion erstellen“" lightbox="./media/deploy-functions/create-network-function.png":::
1. Wählen Sie aus der Dropdownliste die **Anbieter-SKU** aus, die Sie verwenden möchten, und wählen Sie **Erstellen** aus.

   :::image type="content" source="./media/deploy-functions/select.png" alt-text="Screenshot: Anbieter-SKU" lightbox="./media/deploy-functions/select.png":::
1. Abhängig von der ausgewählten SKU werden Sie zum Marketplace-Portal für die verwaltete Netzwerkfunktionsanwendung umgeleitet.
 
   Jeder Netzwerkfunktionspartner hat unterschiedliche Anforderungen an die Bereitstellung seiner Netzwerkfunktion in Azure Stack Edge. Außerdem ist es für einige Netzwerkfunktionen wie Mobile Packet Core und den SD-WAN-Edge erforderlich, Verwaltungsports, LAN- und WAN-Ports zu konfigurieren und diesen Ports IP-Adressen zuzuweisen, bevor Sie die Netzwerkfunktionen bereitstellen. Informieren Sie sich bei Ihrem Partner über die erforderlichen Eigenschaften und die Azure Stack Edge-Gerätenetzwerkkonfiguration.
   
   > [!IMPORTANT]
   > Stellen Sie für alle Netzwerkfunktionen sicher, die statische IP-Adressen für Verwaltungs-, LAN- oder WAN-Netzwerkschnittstellen unterstützen, dass Sie nicht die ersten vier IP-Adressen aus dem IP-Adressbereich verwenden, die dem jeweiligen Port zugewiesen sind. Diese IP-Adressen sind reservierte IP-Adressen für den Azure Stack Edge-Dienst.
   >

1. Führen Sie die Schritte im Marketplace-Portal aus, um die vom Partner verwaltete Anwendung bereitzustellen. Nachdem die verwaltete Anwendung erfolgreich bereitgestellt wurde, können Sie zur Ressourcengruppe wechseln, um die verwaltete App anzeigen zu lassen. Wechseln Sie zur verwalteten Ressourcengruppe, um zu überprüfen, ob der Bereitstellungsstatus des Anbieters für die Netzwerkfunktionsressource „Bereitgestellt“ lautet. So können Sie sich vergewissern, dass die Bereitstellung der Netzwerkfunktion erfolgreich war und die erforderlichen zusätzlichen Konfigurationen über das Verwaltungsportal für Netzwerkfunktionspartner vorgenommen werden können. Stimmen Sie sich mit dem Netzwerkfunktionspartner über die Verwaltung nach der Bereitstellung mit dem Netzwerkfunktions-Manager ab.

### <a name="example"></a>Beispiel

1. Suchen Sie das Angebot **Fusion Core – 5G packet core** im Marketplace, und wählen Sie **Erstellen** aus, um eine Netzwerkfunktion zu erstellen.

   :::image type="content" source="./media/deploy-functions/metaswitch.png" alt-text="Screenshot: Metaswitch-Seite" lightbox="./media/deploy-functions/metaswitch.png":::
1. Konfigurieren Sie die Grundeinstellungen.

   :::image type="content" source="./media/deploy-functions/basics-blade.png" alt-text="Screenshot: Grundeinstellungen" lightbox="./media/deploy-functions/basics-blade.png":::
1. Wenden Sie die verwaltete Identität an. Weitere Informationen finden Sie unter [Verwaltete Identität](overview.md#managed-identity).

   :::image type="content" source="./media/deploy-functions/managed-identity.png" alt-text="Screenshot: verwaltete Identität" lightbox="./media/deploy-functions/managed-identity.png":::
1. Geben Sie die IP-Adressinformationen für die Verwaltungs-, LAN- und WAN-Schnittstellen für die Fusion Core-VM an.

   :::image type="content" source="./media/deploy-functions/ip-settings.png" alt-text="Screenshot: Verwaltungs-, LAN- und WAN-Schnittstellen für die Fusion Core-VM" lightbox="./media/deploy-functions/ip-settings.png":::
1. Geben Sie optionale Einstellungen für 5G und die Konfiguration der Test-UEs ein.

   :::image type="content" source="./media/deploy-functions/5g-settings-blade.png" alt-text="Screenshot: 5G" lightbox="./media/deploy-functions/5g-settings-blade.png":::

   :::image type="content" source="./media/deploy-functions/test-blade.png" alt-text="Screenshot: Konfiguration der Test-UEs" lightbox="./media/deploy-functions/test-blade.png":::
1. Stimmen Sie nach der erfolgreichen Validierung den Geschäftsbedingungen zu. Wählen Sie dann **Erstellen** aus, um die verwaltete Fusion Core-Anwendung in der Ressourcengruppe des Kunden und die Netzwerkfunktionsressource in der verwalteten Ressourcengruppe zu erstellen. Sie müssen das Kontrollkästchen **Ausgeblendete Typen anzeigen** in der Ansicht für die verwaltete Ressourcengruppe aktivieren, damit die Netzwerkfunktionsressource angezeigt wird.

   :::image type="content" source="./media/deploy-functions/managed-app-customer.png" alt-text="Screenshot: Verwaltete App in der Ressourcengruppe des Kunden" lightbox="./media/deploy-functions/managed-app-customer.png":::

   :::image type="content" source="./media/deploy-functions/managed-resource-group.png" alt-text="Screenshot: Netzwerkfunktion in der verwalteten Ressourcengruppe" lightbox="./media/deploy-functions/managed-resource-group.png":::

## <a name="next-steps"></a>Nächste Schritte

Navigieren Sie zum Anbieterportal, um die Konfiguration der Netzwerkfunktion abzuschließen.
