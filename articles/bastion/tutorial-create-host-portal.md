---
title: 'Tutorial: Erstellen eines Azure Bastion-Hosts: Virtueller Windows-Computer: Portal'
description: Erfahren Sie, wie Sie einen Azure Bastion-Host erstellen und eine Verbindung mit einem virtuellen Windows-Computer herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d2e071fd7dc368df867b17a53f9e994ee5c16a6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131430969"
---
# <a name="tutorial-configure-bastion-and-connect-to-a-windows-vm"></a>Tutorial: Konfigurieren von Bastion und Herstellen einer Verbindung mit einer Windows-VM

In diesem Tutorial erfahren Sie, wie Sie über Ihren Browser eine Verbindung mit einem virtuellen Computer herstellen und dabei Azure Bastion und das Azure-Portal verwenden. In diesem Tutorial stellen Sie mit dem Azure-Portal Bastion für Ihr virtuelles Netzwerk bereit. Nachdem der Dienst bereitgestellt wurde, ist die RDP/SSH-Umgebung für alle virtuellen Computer des jeweiligen virtuellen Netzwerks verfügbar. Wenn Sie Bastion zum Herstellen einer Verbindung verwenden, benötigt der virtuelle Computer keine öffentliche IP-Adresse oder spezielle Software. Nach der Bereitstellung von Bastion können Sie die öffentliche IP-Adresse von Ihrem virtuellen Computer entfernen, wenn sie nicht für andere Zwecke benötigt wird. Anschließend stellen Sie über das Azure-Portal eine Verbindung mit einer VM unter Verwendung ihrer privaten IP-Adresse her. Weitere Informationen zu Azure Bastion finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Bastionhosts für Ihr VNet
> * Entfernen der öffentlichen IP-Adresse von einem virtuellen Computer
> * Herstellen einer Verbindung mit einem virtuellen Windows-Computer

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [virtuelles Netzwerk](../virtual-network/quick-create-portal.md)
* Ein virtueller Windows-Computer im virtuellen Netzwerk Wenn Sie nicht über einen virtuellen Computer verfügen, erstellen Sie einen über [Schnellstart: Erstellen einer VM](../virtual-machines/windows/quick-create-portal.md).
* Die folgenden Rollen sind für Ihre Ressourcen erforderlich:
   * Erforderliche VM-Rollen:
     * Rolle „Leser“ für den virtuellen Computer
     * Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers

* Ports: Zum Herstellen einer Verbindung mit dem virtuellen Windows-Computer müssen die folgenden Ports auf Ihrer Windows-VM geöffnet sein:
  * Eingehende Ports: RDP (3389)

 >[!NOTE]
 >Die Verwendung von Azure Bastion in Verbindung mit privaten Azure DNS-Zonen wird derzeit nicht unterstützt. Ehe Sie loslegen, stellen Sie bitte sicher, dass das virtuelle Netzwerk, in dem Sie Ihre Bastion-Ressource bereitstellen möchten, nicht mit einer privaten DNS-Zone verknüpft ist.
 >

### <a name="example-values"></a><a name="values"></a>Beispielwerte

Sie können beim Erstellen dieser Konfiguration die folgenden Beispielwerte verwenden oder Ihre eigenen Werte einsetzen.

**Grundlegende VNet- und VM-Werte:**

|**Name** | **Wert** |
| --- | --- |
| Virtueller Computer| TestVM, |
| Ressourcengruppe | TestRG1 |
| Region | USA, Osten |
| Virtuelles Netzwerk | VNet1 |
| Adressraum | 10.1.0.0/16 |
| Subnetze | FrontEnd: 10.1.0.0/24 |

**Azure Bastion-Werte:**

|**Name** | **Wert** |
| --- | --- |
| Name | VNet1-bastion |
| + Subnetzname | AzureBastionSubnet |
| AzureBastionSubnet-Adressen | Ein Subnetz innerhalb Ihres VNET-Adressraums mit einer Subnetzmaske von „/26“ oder höher.<br> Beispiel: 10.1.1.0/26.  |
| Tarif/SKU | Standard |
| Instanzanzahl (Hostskalierung)| 3 oder größer |
| Öffentliche IP-Adresse |  Neu erstellen |
| Name der öffentlichen IP-Adresse | VNet1-ip  |
| SKU der öffentlichen IP-Adresse |  Standard  |
| Zuweisung  | statischen |

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Erstellen eines Bastion-Hosts

In diesem Abschnitt erfahren Sie, wie Sie das Bastionhostobjekt in Ihrem VNET erstellen. Dies ist erforderlich, um eine sichere Verbindung mit einem virtuellen Computer im VNET zu erstellen.

1. Melden Sie sich beim [Azure-Portal](https://ms.portal.azure.com) an.
1. Geben Sie **Bastion** in die Suche ein.
1. Klicken Sie unter „Dienste“ auf **Bastions**.
1. Klicken Sie auf der Seite Bastions auf **+ Erstellen**, um die Seite **Bastion-Instanz erstellen** zu öffnen.
1. Konfigurieren Sie auf der Seite **Bastion-Instanz erstellen** eine neue Bastion-Ressource.

   :::image type="content" source="./media/tutorial-create-host-portal/review-create.png" alt-text="Screenshot: Portalseite „Bastion-Instanz erstellen“" lightbox="./media/tutorial-create-host-portal/create.png":::

### <a name="project-details"></a>Projektdetails

* **Abonnement**: Das Azure-Abonnement, das Sie verwenden möchten.

* **Ressourcengruppe**: Die Azure-Ressourcengruppe, in der die neue Bastion-Ressource erstellt wird. Erstellen Sie eine Ressourcengruppe, wenn noch keine vorhanden ist.

### <a name="instance-details"></a>Instanzendetails

* **Name**: Der Name der neuen Bastion-Ressource.

* **Region**: Die öffentliche Azure-Region, in der die Ressource erstellt wird. Wählen Sie die Region aus, in der sich Ihr virtuelles Netzwerk befindet.

* **Tarif**: Der Tarif wird auch als **SKU** bezeichnet. Für dieses Tutorial wählen wir die **Standard**-SKU in der Dropdownliste aus. Wenn Sie die Standard-SKU auswählen, können Sie die Anzahl der Instanzen für die Skalierung des Hosts konfigurieren. Von der Basic-SKU wird die Hostskalierung nicht unterstützt. Weitere Informationen finden Sie unter [Konfigurationseinstellungen – SKU](configuration-settings.md#skus).

* **Anzahl der Instanzen**: Dies ist die Einstellung für die **Hostskalierung**, die in Inkrementen von Skalierungseinheiten konfiguriert wird. Verwenden Sie den Schieberegler, um die Anzahl der Instanzen zu konfigurieren. Wenn Sie die Basic-Tarif-SKU angegeben haben, können Sie diese Einstellung nicht konfigurieren. Weitere Informationen finden Sie unter [Konfigurationseinstellungen – Hostskalierung](configuration-settings.md#instance). In diesem Tutorial können Sie die gewünschte Anzahl der Instanzen auswählen. Berücksichtigen Sie dabei jedoch die [Preis](https://azure.microsoft.com/pricing/details/azure-bastion)-Überlegungen für Skalierungseinheiten.

### <a name="configure-virtual-networks"></a>Konfigurieren virtueller Netzwerke

* **Virtuelles Netzwerk:** Das virtuelle Netzwerk, in dem die Bastion-Ressource erstellt wird. Im Rahmen dieses Prozesses können Sie über das Portal ein neues virtuelles Netzwerk erstellen oder eines der vorhandenen virtuellen Netzwerke verwenden. Achten Sie bei Verwendung eines bereits vorhandenen virtuellen Netzwerks darauf, dass der freie Adressraum die Anforderungen des Bastion-Subnetzes erfüllt. Sollte Ihr virtuelles Netzwerk nicht in der Dropdownliste angezeigt werden, überprüfen Sie, ob Sie die richtige Ressourcengruppe ausgewählt haben.

* **Subnetz**: Nach dem Erstellen oder Auswählen eines virtuellen Netzwerks wird das Subnetzfeld auf der Seite angezeigt. Dies ist das Subnetz, in dem Ihre Bastion-Instanzen bereitgestellt werden. Der Name muss **AzureBastionSubnet** lauten. Weitere Informationen finden Sie unter den folgenden Schritten zum Hinzufügen des Subnetzes.

#### <a name="manage-subnet-configuration"></a>Verwalten der Subnetzkonfiguration

Wahrscheinlich haben Sie noch kein AzureBastionSubnet konfiguriert. So konfigurieren Sie das Bastion-Subnetz: 

1. Wählen Sie **Subnetzkonfiguration verwalten** aus. Dadurch gelangen Sie auf die Seite **Subnetze**.

   :::image type="content" source="./media/tutorial-create-host-portal/subnet.png" alt-text="Screenshot: Verwalten der Subnetzkonfiguration.":::
1. Wählen Sie auf der Seite **Subnetze** die Option **+Subnetz** aus, um die Seite **Subnetz hinzufügen** zu öffnen. 

1. Erstellen Sie ein Subnetz unter Verwendung der folgenden Richtlinien:

   * Das Subnetz muss **AzureBastionSubnet** heißen.
   * Das Subnetz muss mindestens die Größe „/26“ haben. Für die Standard-SKU empfehlen wir „/26“ oder höher, um zukünftige zusätzliche Hostskalierungsinstanzen zu unterstützen.

   :::image type="content" source="./media/tutorial-create-host-portal/bastion-subnet.png" alt-text="Screenshot des Subnetzes „AzureBastionSubnet“.":::

1. Weitere Angaben sind auf dieser Seite nicht erforderlich. Wählen Sie unten auf der Seite **Speichern** aus, um die Einstellungen zu speichern und die Seite **Subnetz hinzufügen** zu schließen.

1. Wählen Sie oben auf der Seite **Subnetze** die Option **Bastion-Instanz erstellen** aus, um zur Bastion-Konfigurationsseite zurückzukehren.

   :::image type="content" source="./media/tutorial-create-host-portal/create-a-bastion.png" alt-text="Screenshot: „Bastion-Instanz erstellen“.":::

### <a name="public-ip-address"></a>Öffentliche IP-Adresse

Die öffentliche IP-Adresse der Bastion-Ressource für den RDP/SSH-Zugriff (über Port 443). Erstellen Sie eine **neue öffentliche IP-Adresse**. Die öffentliche IP-Adresse muss sich in der gleichen Region befinden wie die Bastion-Ressource, die Sie erstellen. Diese IP-Adresse hat nichts mit den virtuellen Computern zu tun, mit denen Sie eine Verbindung herstellen möchten. Vielmehr handelt es sich dabei um die öffentliche IP-Adresse für die Bastionhostressource.

   * **Öffentliche IP-Adresse**: Der Name der öffentlichen IP-Adressressource. Für dieses Tutorial können Sie den Standardwert beibehalten.
   * **SKU der öffentlichen IP-Adresse**: Diese Einstellung ist standardmäßig bereits auf **Standard** festgelegt. Azure Bastion verwendet/unterstützt nur die SKU „Standard“ für die öffentliche IP-Adresse.
   * **Zuweisung**: Diese Einstellung ist standardmäßig bereits auf **Statisch** festgelegt.

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Wählen Sie nach Angabe der Einstellungen die Option **Überprüfen + erstellen** aus. Daraufhin werden die Werte überprüft. Nach erfolgreicher Überprüfung können Sie die Bastion-Ressource erstellen.
1. Überprüfen Sie Ihre Einstellungen. 
1. Wählen Sie am unteren Rand der Seite die Option **Erstellen** aus.
1. Daraufhin wird eine Meldung mit dem Hinweis angezeigt, dass Ihre Bereitstellung ausgeführt wird. Der Status der Ressourcenerstellung wird auf dieser Seite angezeigt. Die Erstellung und Bereitstellung der Bastion-Ressource dauert etwa fünf Minuten.

## <a name="remove-vm-public-ip-address"></a>Entfernen einer öffentlichen IP-Adresse einer VM

[!INCLUDE [Remove a public IP address from a VM](../../includes/bastion-remove-ip.md)]

## <a name="connect-to-a-vm"></a>Herstellen einer Verbindung mit einem virtuellen Computer

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, löschen Sie Ihre Ressourcen wie folgt:

1. Geben Sie den Namen Ihrer Ressourcengruppe in das **Suchfeld** am oberen Rand des Portals ein. Wenn Ihre Ressourcengruppe in den Suchergebnissen angezeigt wird, wählen Sie sie aus.
1. Wählen Sie die Option **Ressourcengruppe löschen**.
1. Geben Sie unter **GEBEN SIE DEN RESSOURCENGRUPPENNAMEN EIN:** den Namen Ihrer Ressourcengruppe ein, und wählen Sie **Löschen** aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie einen Bastionhost erstellt und einem virtuellen Netzwerk zugeordnet. Anschließend haben Sie die öffentliche IP-Adresse von einem virtuellen Computer entfernt und eine Verbindung mit ihm hergestellt. Falls gewünscht, können Sie Netzwerksicherheitsgruppen mit Ihrem Azure Bastion-Subnetz verwenden. Weitere Informationen finden Sie hier:

> [!div class="nextstepaction"]
> [Arbeiten mit NSGs](bastion-nsg.md)
