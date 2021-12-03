---
title: Verbinden einer VM mithilfe des nativen Windows-Clients und Azure Bastion
titleSuffix: Azure Bastion
description: Erfahren Sie, wie Sie mithilfe von Bastion und dem nativen Windows-Client eine Verbindung zu einer VM von einem Windows Computer herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 11/01/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6332b7443e3abc36810fc3085f3f2c413a5d7d1c
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137799"
---
# <a name="connect-to-a-vm-using-bastion-and-the-native-client-on-your-windows-computer-preview"></a>Verbindung zu einer VM mit Bastion und dem nativen Client auf Ihrem Windows-Computer (Vorschau)

Azure Bastion bietet jetzt Unterstützung für das Herstellen einer Verbindung mit Ziel-VMs in Azure mithilfe eines nativen Clients auf Ihrer Windows-Arbeitsstation. Mit dieser Funktion können Sie über Bastion mithilfe von Azure CLI eine Verbindung mit Ihren Ziel-VMs herstellen und Ihre Anmeldeoptionen um ein lokales SSH-Schlüsselpaar und Azure Active Directory (Azure AD) erweitern. In diesem Artikel wird beschrieben, wie Sie Bastion mit den erforderlichen Einstellungen konfigurieren und dann eine Verbindung mit einem VM im VNET herstellen. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

> [!NOTE]
> Diese Konfiguration erfordert die Standard-SKU für Azure Bastion.
>

> [!IMPORTANT]
> Diese Funktion wird noch weltweit eingeführt. Wenn Sie im Azure-Portal nicht darauf zugreifen können, warten Sie bitte ein paar Tage und versuchen Sie es erneut.


Derzeit gelten für diese Funktion folgende Einschränkungen:

* Die Anmeldung bei Ihrer Ziel-VM mit einem benutzerdefinierten Port oder Protokoll ist mit nativem Client-Support noch nicht verfügbar. Wenn Sie einen benutzerdefinierten Port oder ein benutzerdefiniertes Protokoll zur Anmeldung bei Ihrer Ziel-VM verwenden möchten, verwenden Sie die Azure-Portallösung.

* Die Anmeldung mit einem lokalen Benutzernamen und Kennwort bei Ihrer Ziel-VM wird noch nicht unterstützt. Wenn Sie einen lokalen Benutzernamen mit Kennwort als Anmeldeinformationen für die Anmeldung bei Ihrer Ziel-VM über Bastion verwenden möchten, verwenden Sie das Azure-Portal.

* Die Anmeldung mit einem privaten SSH-Schlüssel, der in Azure Key Vault gespeichert ist, wird bei dieser Funktion nicht unterstützt. Laden Sie Ihren privaten Schlüssel in eine Datei auf Ihrem lokalen Computer herunter, bevor Sie sich mithilfe eines SSH-Schlüsselpaars bei Ihrer Linux-VM anmelden.

## <a name="prerequisites"></a><a name="prereq"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass die folgenden Kriterien erfüllt sind:

* Die neueste Version der CLI-Befehle (Version 2.30 oder höher) ist installiert. Informationen zum Installieren der CLI-Befehle finden Sie unter [Installieren von Azure CLI 2.0](/cli/azure/install-azure-cli) und [Erste Schritte mit Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).
* Ein virtuelles Azure-Netzwerk
* Ein VM in einem virtuellen Netzwerk.

## <a name="configure-bastion"></a>Konfigurieren von Bastion

Folgen Sie den Anweisungen für Ihre Umgebung.

### <a name="to-modify-an-existing-bastion-host"></a>So ändern Sie einen vorhandenen Bastion-Host

Wenn Sie Bastion bereits für Ihr VNet konfiguriert haben, ändern Sie die folgenden Einstellungen:

1. Navigieren Sie zur Seite **Konfiguration** für Ihre Bastion-Ressource. Stellen Sie sicher, dass die SKU **Standard** ist. Falls nicht, ändern Sie es in der Dropdownliste in **Standard**.
1. Aktivieren Sie das Kontrollkästchen **nativer Client-Support** und übernehmen Sie Ihre Änderungen.

    :::image type="content" source="./media/connect-native-client-windows/update-host.png" alt-text="Einstellungen zum Aktualisieren eines vorhandenen Hosts mit aktiviertem nativen Client-Support Kontrollkästchen." lightbox="./media/connect-native-client-windows/update-host-expand.png":::

### <a name="to-configure-a-new-bastion-host"></a>So konfigurieren Sie einen neuen Bastion-Host

Wenn Sie noch keinen Bastion-Host konfiguriert haben, finden Sie weitere Informationen unter [Erstellen eines Bastion-Hosts](tutorial-create-host-portal.md#createhost). Geben Sie beim Konfigurieren des Bastion-Hosts die folgenden Einstellungen an:

1. Wählen Sie auf der Registerkarte **Basics** für **Instanz Details -> Ebene** die Option **Standard** aus, um einen Bastion-Host mithilfe der Standard-SKU zu erstellen.

   :::image type="content" source="./media/connect-native-client-windows/standard.png" alt-text="Einstellungen für einen neuen Bastion-Host mit ausgewählter Standard-SKU." lightbox="./media/connect-native-client-windows/standard.png":::
1. Aktivieren Sie auf der Registerkarte **Erweitert** das Kontrollkästchen **Nativer Client-Support**.

   :::image type="content" source="./media/connect-native-client-windows/new-host.png" alt-text="Einstellungen für einen neuen Bastion-Host mit aktiviertem nativen Client-Support Kontrollkästchen." lightbox="./media/connect-native-client-windows/new-host-expand.png":::

## <a name="verify-roles-and-ports"></a>Überprüfen Sie die Rollen und Ports

Stellen Sie sicher, dass die folgenden Rollen und Ports konfiguriert sind, um eine Verbindung herzustellen.

### <a name="required-roles"></a>Erforderliche Rollen

* Rolle „Leser“ für den virtuellen Computer
* Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource
* Rolle „VM-Administratoranmeldung“ oder „VM-Benutzeranmeldung“, wenn Sie die Azure AD-Anmeldemethode verwenden.

### <a name="ports"></a>Ports

Um eine Verbindung mit einer Linux-VM mit nativem Client-Support herzustellen, müssen auf Ihrer Linux-VM die folgenden Ports offen sein:

* Eingehender Port: SSH (22)

Um eine Verbindung mit einer Windows-VM mit nativem Client-Support herzustellen, müssen auf Ihrer Windows-VM die folgenden Ports offen sein:

* Eingehender Port: RDP (3389)

Wenn Sie einen benutzerdefinierten Port verwenden möchten, um eine Verbindung mit Ihrer Ziel-VM herzustellen, verwenden Sie stattdessen die Azure-Portal Anweisungen.

## <a name="connect-to-a-vm"></a><a name="connect"></a>Herstellen einer Verbindung mit einem virtuellen Computer

In diesem Abschnitt wird beschrieben, wie Sie eine Verbindung mit Ihrem virtuellen Computer herstellen. Verwenden Sie die Schritte, die dem Typ der VM entsprechen, mit der Sie eine Verbindung herstellen möchten.

1. Melden Sie sich bei Ihrem Azure-Konto an und wählen Sie Ihr Abonnement aus, das Ihre Bastion-Ressource enthält.

   ```azurecli-interactive
   az login
   az account list
   az account set --subscription "<subscription ID>"
   ```

### <a name="connect-to-a-linux-vm"></a>Herstellen einer Verbindung mit einem virtuellen Linux-Computer

1. Melden Sie sich bei Ihrer Linux-Ziel-VM mit einer der folgenden Optionen an:

   * Wenn Sie sich bei einem für die Azure AD-Anmeldung aktivierten virtuellen Computer anmelden, verwenden Sie den folgenden Befehl. Weitere Informationen wie Sie sich mit Azure AD bei Ihren Azure Linux VMs anmelden, finden Sie unter [Azure Linux VMs und Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-linux.md).

     ```azurecli-interactive
     az network bastion ssh --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type  "AAD"
     ```

   * Wenn Sie sich mit einem SSH-Schlüsselpaar anmelden, verwenden Sie den folgenden Befehl.

      ```azurecli-interactive
      az network bastion ssh "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>" --auth-type "ssh-key" --username "<Username>" --ssh-key "<Filepath>"
      ```

### <a name="connect-to-a-windows-vm"></a>Herstellen einer Verbindung mit einem virtuellen Windows-Computer

1. Melden Sie sich mithilfe des folgenden Befehls mit der Azure AD-Anmeldung bei Ihrem virtuellen Windows-Zielcomputer an. Weitere Informationen, wie Sie sich mit Azure AD bei Ihren Azure Windows-VMs anmelden, finden Sie unter [Azure Windows-VMs und Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md).

      ```azurecli-interactive
      az network bastion rdp --name "<BastionName>" --resource-group "<ResourceGroupName>" --target-resource-id "<VMResourceId>"
      ```

1. Nachdem Sie sich bei Ihrer Ziel-VM angemeldet haben, wird der native Client auf Ihrem lokalen Computer mit Ihrer VM-Sitzung geöffnet (**mstc** für RDP-Sitzungen und **ssh CLI-Erweiterung** für SSH-Sitzungen).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).
