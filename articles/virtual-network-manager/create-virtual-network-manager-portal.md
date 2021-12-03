---
title: 'Schnellstart: Erstellen einer Mesh-Netzwerktopologie mit Azure Virtual Network Manager über das Azure-Portal'
description: In diesem Schnellstart erfahren Sie, wie Sie eine Mesh-Netzwerktopologie mit Azure Virtual Network Manager über das Azure-Portal erstellen.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: template-quickstart, ignite-fall-2021
ms.openlocfilehash: 134f8b28d6505963cde1f44f0b6850643dae3436
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421747"
---
# <a name="quickstart-create-a-mesh-network-topology-with-azure-virtual-network-manager-using-the-azure-portal"></a>Schnellstart: Erstellen einer Mesh-Netzwerktopologie mit Azure Virtual Network Manager über das Azure-Portal

Erste Schritte mit Azure Virtual Network Manager über das Azure-Portal zum Verwalten der Konnektivität für alle virtuellen Netzwerke

In diesem Schnellstart stellen Sie drei virtuelle Netzwerke bereit und verwenden Azure Virtual Network Manager, um eine Mesh-Netzwerktopologie zu erstellen. Anschließend überprüfen Sie, ob die Konnektivitätskonfiguration angewandt wurde.

> [!IMPORTANT]
> Azure Virtual Network Manager ist derzeit als öffentliche Vorschauversion verfügbar.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="register-subscription-for-public-preview"></a>Registrieren eines Abonnements für die Public Preview

1. Navigieren Sie zur Seite [Previewfunktionen](https://portal.azure.com/#blade/Microsoft_Azure_Resources/PreviewFeaturesBlade).

1. Suchen Sie nach **AllowAzureNetworkManager**.

1. Aktivieren Sie das Kontrollkästchen neben *AllowAzureNetworkManager*, und wählen Sie dann **+ Registrieren** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/registration.png" alt-text="Screenshot: Registrierungsseite für Previewfunktionen":::

## <a name="create-virtual-network-manager"></a>Erstellen einer Virtual Network Manager-Instanz

1. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie nach **Network Manager**. Wählen Sie dann **Erstellen** aus, um mit der Einrichtung von Azure Virtual Network Manager zu beginnen.

1. Geben Sie auf der Registerkarte *Grundlagen* die folgenden Informationen an, oder wählen Sie sie aus:

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-basics.png" alt-text="Screenshot der Seite „Grundlagen“ bei der Network Manager-Erstellung":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie das Abonnement aus, in dem Sie Azure Virtual Network Manager bereitstellen möchten. |
    | Ressourcengruppe | Wählen Sie eine Ressourcengruppe zum Speichern von Azure Virtual Network Manager aus, oder erstellen Sie sie. In diesem Beispiel wird die zuvor erstellte Gruppe **myAVNMResourceGroup** verwendet.
    | Name | Geben Sie einen Namen für diese Azure Virtual Network Manager-Instanz ein. In diesem Beispiel wird der Name **myAVNM** verwendet. |
    | Region | Wählen Sie die Region für diese Bereitstellung aus. Azure Virtual Network Manager kann virtuelle Netzwerke in beliebigen Regionen verwalten. Die ausgewählte Region ist der Ort, an dem die Virtual Network Manager-Instanz bereitgestellt wird. |
    | BESCHREIBUNG | *(Optional)* Geben Sie eine Beschreibung für diese Virtual Network Manager-Instanz und die Aufgabe an, für die er verantwortlich ist. |
    | [Umfang](concept-network-manager-scope.md#scope) | Definieren Sie den Bereich, den Azure Virtual Network Manager verwalten kann.
    | [Funktionen](concept-network-manager-scope.md#features) | Wählen Sie die Funktionen aus, die Sie für Azure Virtual Network Manager aktivieren möchten. Als Funktionen stehen *Konnektivität*, *SecurityAdmin* oder *Alle auswählen* zur Verfügung. </br> Konnektivität: ermöglicht die Erstellung einer vollständigen Mesh- oder Hub-and-Spoke-Netzwerktopologie zwischen virtuellen Netzwerken innerhalb des Bereichs </br> SecurityAdmin: ermöglicht das Erstellen globaler Netzwerksicherheitsregeln |

1. Wählen Sie **Überprüfen und erstellen** und nach Abschluss der Überprüfung **Erstellen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-manager-validation.png" alt-text="Screenshot der Überprüfungsseite zum Erstellen einer Network Manager-Ressource":::

## <a name="create-three-virtual-networks"></a>Erstellen von drei virtuellen Netzwerken

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an.

1. Wählen Sie **+ Ressource erstellen** aus, und suchen Sie nach **Virtuelles Netzwerk**. Wählen Sie dann **Erstellen** aus, um mit dem Konfigurieren des virtuellen Netzwerks zu beginnen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-vnet.png" alt-text="Screenshot der Seite zum Erstellen eines virtuellen Netzwerks":::

1. Geben Sie auf der Registerkarte *Grundlagen* die folgenden Informationen an, oder wählen Sie sie aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-basic.png" alt-text="Screenshot der Seite „Grundlagen“ beim Erstellen eines virtuellen Netzwerks":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie das Abonnement aus, in dem Sie dieses virtuelle Netzwerk bereitstellen möchten. |
    | Ressourcengruppe | Wählen Sie eine neue Ressourcengruppe zum Speichern des virtuellen Netzwerks aus, oder erstellen Sie sie. In diesem Schnellstart wird die neue Ressourcengruppe **myAVNMResourceGroup** verwendet.
    | Name | Geben Sie als Namen für das virtuelle Netzwerk **VNetA** ein. |
    | Region | Wählen Sie **USA, Westen** aus. |

1. Wählen Sie **Weiter: IP-Adressen** aus, und konfigurieren Sie die folgenden Netzwerkadressräume:

     :::image type="content" source="./media/create-virtual-network-manager-portal/create-mesh-vnet-ip.png" alt-text="Screenshot der Seite „IP-Adressen“ beim Erstellen eines virtuellen Netzwerks":::

    | Einstellung | Wert |
    | -------- | ----- |
    | IPv4-Adressraum | 10.0.0.0/16 |
    | Subnetzname | default |
    | Subnetzadressraum | 10.0.0.0/24 |

1. Wählen Sie **Überprüfen und erstellen** und nach Abschluss der Überprüfung **Erstellen** aus, um das virtuelle Netzwerk bereitzustellen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-validation.png" alt-text="Screenshot der Seite „Überprüfung“ beim Erstellen eines virtuellen Netzwerks":::

1. Wiederholen Sie die Schritte 2 bis 5, um zwei weitere virtuelle Netzwerke mit den folgenden Informationen zu erstellen:

    | Einstellung | Wert |
    | ------- | ----- |
    | Subscription | Wählen Sie dasselbe Abonnement aus, das Sie in Schritt 3 ausgewählt haben. |
    | Ressourcengruppe | Wählen Sie **myAVNMResourceGroup** aus. |
    | Name | Geben Sie als Namen für das zweite virtuelle Netzwerk **VNetB** und für das dritte virtuelle Netzwerk **VNetC** ein. |
    | Region | Die Region wird für Sie ausgewählt, wenn Sie die Ressourcengruppe auswählen. |
    | IP-Adressen für VNetB | IPv4-Adressraum: 10.1.0.0/16 </br> Subnetzname: Standard </br> Subnetzadressraum: 10.1.0.0/24|
    | IP-Adressen für VNetC | IPv4-Adressraum: 10.2.0.0/16 </br> Subnetzname: Standard </br> Subnetzadressraum: 10.2.0.0/24|

## <a name="create-a-network-group"></a>Erstellen einer Netzwerkgruppe

1. Navigieren Sie zu der Azure Virtual Network Manager-Instanz, die Sie erstellt haben.

1. Wählen Sie unter *Einstellungen* die Option **Netzwerkgruppen** und dann **+ Hinzufügen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group.png" alt-text="Screenshot der Schaltfläche „Netzwerkgruppe hinzufügen“":::

1. Geben Sie auf der Seite *Netzwerkgruppe hinzufügen* einen **Namen** für die Netzwerkgruppe ein. In diesem Beispiel wird der Name **myNetworkGroup** verwendet. Wählen Sie **Weiter: Statische Gruppenmitglieder** aus, um mit dem Hinzufügen virtueller Netzwerke zur Netzwerkgruppe zu beginnen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-group-basics.png" alt-text="Screenshot der Registerkarte „Grundlagen“ beim Erstellen einer Netzwerkgruppe":::

1. Wählen Sie auf der Registerkarte *Statische Gruppenmitglieder* die Option **+ Virtuelle Netzwerke hinzufügen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks-button.png" alt-text="Screenshot der Schaltfläche „Virtuelle Netzwerke hinzufügen“":::

1. Wählen Sie auf der Seite *Virtuelle Netzwerke hinzufügen* alle drei zuvor erstellten virtuellen Netzwerke aus (VNetA, VNetB und VNetC). Wählen Sie dann **Hinzufügen** aus, um die Auswahl zu bestätigen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-virtual-networks.png" alt-text="Screenshot der Seite zum Hinzufügen virtueller Netzwerke zur Netzwerkgruppe":::

1. Wählen Sie **Überprüfen und erstellen** und nach Abschluss der Überprüfung **Erstellen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/review-create.png" alt-text="Screenshot der Schaltfläche „Überprüfen und Erstellen“ für eine neue Netzwerkgruppe":::

1. Die neue Netzwerkgruppe wurde auf der Seite *Netzwerkgruppen* hinzugefügt.

    :::image type="content" source="./media/create-virtual-network-manager-portal/network-groups-list.png" alt-text="Screenshot der Seite „Netzwerkgruppen“ mit der neu hinzugefügten Netzwerkgruppe":::

## <a name="create--a-connectivity-configuration"></a>Erstellen einer Konnektivitätskonfiguration

1. Wählen Sie unter *Einstellungen* die Option **Konfigurationen** und anschließend **+ Konfiguration hinzufügen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-configuration.png" alt-text="Screenshot der Schaltfläche „Konfiguration hinzufügen“ für Network Manager":::

1. Wählen Sie im Dropdownmenü **Konnektivität** aus, um mit dem Erstellen einer Konnektivitätskonfiguration zu beginnen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/configuration-menu.png" alt-text="Screenshot des Dropdownmenüs „Konfiguration“":::

1. Geben Sie auf der Seite *Konnektivitätskonfiguration hinzufügen* die folgenden Informationen ein, oder wählen Sie sie aus:

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration.png" alt-text="Screenshot der Seite „Konnektivitätskonfiguration hinzufügen“":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Name | Geben Sie einen Namen für diese Konnektivitätskonfiguration ein. |
    | Beschreibung | *(Optional)* Geben Sie eine Beschreibung dieser Konnektivitätskonfiguration an. |
    | Topologie | Wählen Sie den Typ der Topologie aus, die Sie mit dieser Konfiguration erstellen möchten. In diesem Beispiel wird die Topologie **Mesh** verwendet. |

1. Nachdem Sie die *Mesh*-Topologie ausgewählt haben, werden die Optionen **Globales Mesh** und **Netzwerkgruppen** angezeigt. *Globales Mesh* ist für diese Konfiguration nicht erforderlich, da sich alle virtuellen Netzwerke in derselben Region befinden. Wählen Sie **+ Netzwerkgruppen hinzufügen** und dann die Netzwerkgruppe aus, die Sie im letzten Abschnitt erstellt haben. Klicken Sie auf **Auswählen**, um die Netzwerkgruppe der Konfiguration hinzuzufügen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/add-network-group-configuration.png" alt-text="Screenshot des Hinzufügens einer Netzwerkgruppe zu einer Konnektivitätskonfiguration":::

1. Wählen Sie **Hinzufügen** aus, um die Konfiguration zu erstellen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/create-connectivity-configuration.png" alt-text="Screenshot der Erstellung einer Konnektivitätskonfiguration":::

1. Die neue Konnektivitätskonfiguration wird auf der Seite *Konfiguration* hinzugefügt.

    :::image type="content" source="./media/create-virtual-network-manager-portal/connectivity-configuration-list.png" alt-text="Screenshot der Liste der Konnektivitätskonfigurationen":::

## <a name="deploy-the-connectivity-configuration"></a>Bereitstellen der Konnektivitätskonfiguration

Damit Ihre Konfigurationen auf Ihre Umgebung angewandt werden, müssen Sie die Konfiguration nach der Bereitstellung committen. Sie müssen die Konfiguration in der Region **USA, Westen** bereitstellen, in der auch die virtuellen Netzwerke bereitgestellt wurden.

1. Wählen Sie zunächst unter *Einstellungen* die Option **Bereitstellungen** und anschließend **Konfiguration bereitstellen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployments.png" alt-text="Screenshot der Seite „Bereitstellungen“ in Network Manager":::

1. Wählen Sie die folgenden Einstellungen aus:

    :::image type="content" source="./media/create-virtual-network-manager-portal/deploy-configuration.png" alt-text="Screenshot der Seite „Konfiguration bereitstellen“":::

    | Einstellung | Wert |
    | ------- | ----- |
    | Konfigurationstyp | Wählen Sie den Konfigurationstyp aus, den Sie bereitstellen möchten. In diesem Beispiel wird eine **Konnektivitätskonfiguration** bereitgestellt. |
    | Configurations | Wählen Sie die Konfiguration **myConnectivityConfig** aus, die Sie im vorherigen Abschnitt erstellt haben. |
    | Zielregionen | Wählen Sie die Region aus, in der diese Konfiguration bereitgestellt werden soll. Die Region **USA, Westen** ist ausgewählt, da alle virtuellen Netzwerke in dieser Region erstellt wurden. |

1. Wählen Sie **Bereitstellen** und dann **OK** aus, um zu bestätigen, dass Sie eine vorhandene Konfiguration ggf. überschreiben möchten.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-confirmation.png" alt-text="Screenshot der Bestätigungsmeldung für die Bereitstellung":::

1. Die Bereitstellung sollte nun in der Liste für die ausgewählte Region angezeigt werden. Die Bereitstellung der Konfiguration kann etwa 15 bis 20 Minuten dauern.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-in-progress.png" alt-text="Screenshot einer Konfigurationsbereitstellung mit dem Status „In Bearbeitung“":::

## <a name="confirm-configuration-deployment"></a>Bestätigen der Konfigurationsbereitstellung

1. Wählen Sie auf der Seite *Bereitstellung* die Option **Aktualisieren** aus, um den aktualisierten Status der Konfiguration anzuzeigen, die Sie committet haben.

    :::image type="content" source="./media/create-virtual-network-manager-portal/deployment-status.png" alt-text="Screenshot der Schaltfläche „Aktualisieren“ für den aktualisierten Bereitstellungsstatus":::

1. Wechseln Sie zum virtuellen Netzwerk **VNetA**, und wählen Sie unter *Einstellungen* die Option **Network Manager** aus. Sie können erkennen, dass die Konfiguration, die Sie mit Azure Virtual Network Manager bereitgestellt haben, dem virtuellen Netzwerk zugeordnet ist.

    :::image type="content" source="./media/create-virtual-network-manager-portal/vnet-configuration-association.png" alt-text="Screenshot der Konnektivitätskonfiguration, die dem virtuellen Netzwerk VNetA zugeordnet ist":::

1. Sie können dies auch für **VNetB** und **VNetC** bestätigen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie Azure Virtual Network Manager nicht mehr benötigen, müssen Sie sicherstellen, dass alle der folgenden Bedingungen erfüllt sind, bevor Sie die Ressource löschen:

* In keiner Region sind Konfigurationen bereitgestellt.
* Alle Konfigurationen wurden gelöscht.
* Alle Netzwerkgruppen wurden gelöscht.

1. Um alle Konfigurationen aus einer Region zu entfernen, stellen Sie in der Zielregion eine Konfiguration vom Typ **None** (Keine) bereit. Wählen Sie **Bereitstellen** und anschließend zur Bestätigung **OK** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/none-configuration.png" alt-text="Screenshot der Bereitstellung einer Konnektivitätskonfiguration vom Typ „None“":::

1. Um eine Konfiguration zu löschen, wählen Sie im linken Bereich von Azure Virtual Network Manager unter *Einstellungen* die Option **Konfigurationen** aus. Aktivieren Sie das Kontrollkästchen neben der Konfiguration, die Sie entfernen möchten, und wählen Sie dann oben auf der Ressourcenseite **Löschen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-configuration.png" alt-text="Screenshot der Schaltfläche „Löschen“ für eine Konnektivitätskonfiguration":::

1. Wählen Sie zum Löschen einer Netzwerkgruppe im linken Bereich von Azure Virtual Network Manager unter *Einstellungen* die Option **Netzwerkgruppen** aus. Aktivieren Sie das Kontrollkästchen neben der Netzwerkgruppe, die Sie entfernen möchten, und wählen Sie dann oben auf der Ressourcenseite **Löschen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-group.png" alt-text="Screenshot der Schaltfläche „Löschen“ für eine Netzwerkgruppe":::

1. Nachdem alle Netzwerkgruppen entfernt wurden, können Sie die Ressource löschen, indem Sie in der Liste mit der rechten Maustaste auf Azure Virtual Network Manager klicken und **Löschen** auswählen.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-network-manager.png" alt-text="Screenshot der Schaltfläche „Löschen“ für eine Azure Virtual Network Manager-Instanz":::

1. Um die Ressourcengruppe zu löschen, suchen Sie sie, und wählen Sie dann **Ressourcengruppe löschen** aus. Bestätigen Sie den Löschvorgang, indem Sie den Namen der Ressourcengruppe eingeben, und wählen Sie dann **Löschen** aus.

    :::image type="content" source="./media/create-virtual-network-manager-portal/delete-resource-group.png" alt-text="Screenshot der Schaltfläche „Löschen“ für eine Ressourcengruppe":::

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Azure Virtual Network Manager-Instanz erstellt haben, informieren Sie sich, wie Sie den Netzwerkdatenverkehr mithilfe einer Sicherheitsadministratorkonfiguration blockieren:

> [!div class="nextstepaction"]
> [Blockieren von Netzwerkdatenverkehr mit Sicherheitsadministratorregeln](how-to-block-network-traffic-portal.md)
