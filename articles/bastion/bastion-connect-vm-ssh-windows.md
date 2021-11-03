---
title: Herstellen einer Verbindung mit einer Windows-VM über SSH
titleSuffix: Azure Bastion
description: Hier erfahren Sie, wie Sie mithilfe von Azure Bastion über SSH eine Verbindung mit einer Windows-VM herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 433b86cb879fbe0541b9da3a6ae2edcb99e98b7b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071551"
---
# <a name="create-an-ssh-connection-to-a-windows-vm-using-azure-bastion"></a>Erstellen einer SSH-Verbindung mit einer Windows-VM mithilfe von Azure Bastion

In diesem Artikel erfahren Sie, wie Sie sicher und nahtlos eine RDP-Verbindung mit Ihren Windows-VMs in einem virtuellen Azure-Netzwerk direkt über das Azure-Portal erstellen. Wenn Sie Azure Bastion verwenden, benötigen Ihre virtuellen Computer keinen Client, keinen Agent und auch keine zusätzliche Software. Sie können auch mithilfe von SSH eine Verbindung mit einer Windows-VM herstellen. Weitere Informationen finden Sie unter [Erstellen einer RDP-Verbindung mit einer Windows-VM](bastion-connect-vm-rdp-windows.md).

Azure Bastion bietet sichere Verbindungen mit allen virtuellen Computern in dem virtuellen Netzwerk, in dem der Dienst bereitgestellt wird. Durch die Verwendung von Azure Bastion wird verhindert, dass Ihre virtuellen Computer RDP- und SSH-Ports öffentlich verfügbar machen. Gleichzeitig wir weiterhin der sichere Zugriff per RDP/SSH ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

> [!NOTE]
> Wenn Sie eine SSH-Verbindung mit einer Windows-VM erstellen möchten, muss Azure Bastion mithilfe der Standard-SKU konfiguriert werden.
>

Wenn Sie eine Verbindung mit einem virtuellen Computer unter Windows mithilfe von SSH herstellen, können Sie sowohl Benutzername/Kennwort als auch SSH-Schlüssel zur Authentifizierung verwenden. Sie können sich auch über SSH-Schlüssel mit Ihrer VM verbinden, indem Sie Folgendes verwenden:

* einen privaten Schlüssel, den Sie manuell eingeben
* eine Datei, die den privaten Schlüssel enthält

Der private SSH-Schlüssel muss in einem Format vorliegen, das mit `"-----BEGIN RSA PRIVATE KEY-----"` beginnt und mit `"-----END RSA PRIVATE KEY-----"` endet.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie einen Azure Bastion-Host für das virtuelle Netzwerk eingerichtet haben, in dem sich die VM befindet. Weitere Informationen finden Sie unter [Erstellen eines Azure Bastion-Hosts](tutorial-create-host-portal.md). Sobald der Bastion-Dienst provisioniert und in Ihrem virtuellen Netzwerk bereitgestellt wurde, können Sie ihn verwenden, um sich mit jeder VM in diesem virtuellen Netzwerk zu verbinden. 

Um per SSH auf einen virtuellen Windows-Computer zuzugreifen, müssen Sie außerdem Folgendes sicherstellen:
* Auf Ihrem virtuellen Windows-Computer wird Windows Server 2019 oder höher ausgeführt.
* Auf Ihrem virtuellen Windows-Computer ist OpenSSH Server installiert und aktiv. Informationen zur Vorgehensweise finden Sie unter [Installieren von OpenSSH](/windows-server/administration/openssh/openssh_install_firstuse).
* Azure Bastion wurde für die Verwendung der Standard-SKU konfiguriert.

### <a name="required-roles"></a>Erforderliche Rollen

Zum Herstellen einer Verbindung sind die folgenden Rollen erforderlich:

* Rolle „Leser“ auf dem virtuellen Computer
* Rolle „Leser“ auf dem Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource

### <a name="ports"></a>Ports

Zum Herstellen einer Verbindung mit dem virtuellen Windows-Computer über SSH müssen die folgenden Ports auf Ihrer VM geöffnet sein:

* Eingehender Port: SSH (22) *oder*
* Eingehender Port: Benutzerdefinierter Wert (Sie müssen diesen benutzerdefinierten Port dann angeben, wenn Sie über Azure Bastion eine Verbindung mit der VM herstellen)

### <a name="supported-configurations"></a>Unterstützte Konfigurationen

Derzeit unterstützt Azure Bastion nur das Herstellen einer Verbindung mit Windows-VMs über SSH mithilfe von **OpenSSH**.

## <a name="connect-using-username-and-password"></a><a name="username"></a>Verbinden: Verwenden von Benutzernamen und Kennwort

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Screenshot: Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::

1. Wählen Sie nach der Auswahl von „Bastion“ die Option **Bastion verwenden** aus. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Erweitern Sie auf der Seite **Verbindung über Azure Bastion herstellen** den Abschnitt **Verbindungseinstellungen**, und wählen Sie **SSH** aus. Wenn Sie einen anderen eingehenden Port als den SSH-Standardport (22) verwenden möchten, geben Sie den **Port** ein.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings.png" alt-text="Screenshot: Seite „Verbindungseinstellungen“" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings.png":::

1. Geben Sie **Benutzername** und **Kennwort** ein, und wählen Sie dann **Verbinden** aus, um eine Verbindung mit dem virtuellen Computer herzustellen.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication.png" alt-text="Screenshot: Kennwortauthentifizierung" lightbox="./media/bastion-connect-vm-ssh-windows/authentication.png":::

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Verbinden: Manuelle Eingabe eines privaten Schlüssels

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Screenshot: Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Erweitern Sie auf der Seite **Verbindung über Azure Bastion herstellen** den Abschnitt **Verbindungseinstellungen**, und wählen Sie **SSH** aus. Wenn Sie einen anderen eingehenden Port als den SSH-Standardport (22) verwenden möchten, geben Sie den **Port** ein.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png" alt-text="Screenshot: Konnektivitätseinstellungen" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-manual.png":::

1. Geben Sie **Benutzername** und **Privater SSH-Schlüssel** ein. Geben Sie Ihren privaten Schlüssel in das Textfeld **Privater SSH-Schlüssel** ein (bzw. fügen Sie ihn direkt ein).

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-manual.png" alt-text="Screenshot: SSH-Schlüsselauthentifizierung" lightbox="./media/bastion-connect-vm-ssh-windows/authentication-manual.png":::

1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Verbinden: Verwenden einer Datei mit einem privaten Schlüssel

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Screenshot: Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Erweitern Sie auf der Seite **Verbindung über Azure Bastion herstellen** den Abschnitt **Verbindungseinstellungen**, und wählen Sie **SSH** aus. Wenn Sie einen anderen eingehenden Port als den SSH-Standardport (22) verwenden möchten, geben Sie den **Port** ein.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png" alt-text="Screenshot: Verbindungseinstellungen" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-file.png":::

1. Geben Sie **Benutzername** und **Privater SSH-Schlüssel aus lokaler Datei** ein. Suchen Sie nach der Datei, und wählen Sie dann **Öffnen** aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/authentication-file.png" alt-text="Screenshot: SSH-Schlüsseldatei" lightbox="./media/bastion-connect-vm-ssh-windows/authentication-file.png":::

1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen. Sobald Sie auf „Verbinden“ klicken, wird SSH zu diesem virtuellen Computer direkt im Azure-Portal geöffnet. Die Verbindung erfolgt über HTML5 mit Port 443 im Bastion-Dienst und über die private IP-Adresse Ihres virtuellen Computers.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Verbinden: Verwenden eines in Azure Key Vault gespeicherten privaten Schlüssels

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connect.png" alt-text="Screenshot: Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“" lightbox="./media/bastion-connect-vm-ssh-windows/connect.png":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Erweitern Sie auf der Seite **Verbindung über Azure Bastion herstellen** den Abschnitt **Verbindungseinstellungen**, und wählen Sie **SSH** aus. Wenn Sie einen anderen eingehenden Port als den SSH-Standardport (22) verwenden möchten, geben Sie den **Port** ein.

    :::image type="content" source="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png" alt-text="Screenshot: Seite „Verbindungseinstellungen“" lightbox="./media/bastion-connect-vm-ssh-windows/connection-settings-akv.png":::

1. Geben Sie **Benutzername** ein, und wählen Sie **Privater SSH-Schlüssel aus Azure Key Vault** aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/ssh-key-vault.png" alt-text="Screenshot: Privater SSH-Schlüssel aus Azure Key Vault":::
1. Wählen Sie im Dropdownmenü **Azure Key Vault** die Ressource aus, in der Sie Ihren privaten SSH-Schlüssel gespeichert haben. 

   * Wenn Sie keine Azure Key Vault-Ressource eingerichtet haben, ziehen Sie [Erstellen eines Key Vault](../key-vault/secrets/quick-create-powershell.md) zurate, und speichern Sie Ihren privaten SSH-Schlüssel als Wert eines neuen Key Vault-Geheimnisses.
   * Stellen Sie sicher, dass Sie über die Zugriffsrechte **Auflisten** und **Abrufen** für die in der Key Vault-Ressource gespeicherten Geheimnisse verfügen. Um Zugriffsrichtlinien für Ihre Key Vault-Ressource zuzuweisen und zu ändern, lesen Sie [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md).

      >[!NOTE]
      >Speichern Sie mithilfe von **PowerShell** oder der **Azure CLI** Ihren privaten SSH-Schlüssel als Geheimnis. Wenn Sie Ihren privaten Schlüssel über das Azure Key Vault-Portal speichern, wird dadurch die Formatierung beeinträchtigt und die Anmeldung schlägt fehl. Für den Fall, dass Sie Ihren privaten Schlüssel über das Portal als Geheimnis gespeichert und keinen Zugriff mehr auf die ursprüngliche private Schlüsseldatei haben, finden Sie unter [Aktualisieren eines SSH-Schlüssels](../virtual-machines/extensions/vmaccess.md#update-ssh-key) Informationen zum Aktualisieren des Zugriffs auf den virtuellen Zielcomputer mit einem neuen SSH-Schlüsselpaar.
      >

   :::image type="content" source="./media/bastion-connect-vm-ssh-windows/private-key-stored.png" alt-text="Screenshot: Azure Key Vault" lightbox="./media/bastion-connect-vm-ssh-windows/private-key-stored.png":::

1. Wählen Sie im Dropdownmenü **Azure Key Vault-Geheimnis** das Key Vault-Geheimnis aus, das den Wert Ihres privaten SSH-Schlüssels enthält.
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen. Sobald Sie auf **Verbinden** klicken, wird die SSH-Verbindung mit diesem virtuellen Computer direkt im Azure-Portal geöffnet. Die Verbindung erfolgt über HTML5 mit Port 443 im Bastion-Dienst und über die private IP-Adresse Ihres virtuellen Computers.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Bastion finden Sie in den [häufig gestellten Fragen zu Bastion](bastion-faq.md).
