---
title: Herstellen einer Verbindung mit einer Linux-VM über SSH
titleSuffix: Azure Bastion
description: Hier erfahren Sie, wie Sie mithilfe von Azure Bastion über SSH eine Verbindung mit einer Linux-VM herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0c852cf6df97619f7a319da4e13a406c572d4e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069290"
---
# <a name="create-an-ssh-connection-to-a-linux-vm-using-azure-bastion"></a>Erstellen einer SSH-Verbindung mit einer Linux-VM mithilfe von Azure Bastion

In diesem Artikel erfahren Sie, wie Sie sicher und nahtlos eine SSH-Verbindung mit Ihren Linux-VMs in einem virtuellen Azure-Netzwerk direkt über das Azure-Portal erstellen. Wenn Sie Azure Bastion verwenden, benötigen Ihre virtuellen Computer keinen Client, keinen Agent und auch keine zusätzliche Software. Sie können auch mithilfe von RDP eine Verbindung mit einer Linux-VM herstellen. Weitere Informationen finden Sie unter [Erstellen einer RDP-Verbindung mit einer Linux-VM](bastion-connect-vm-rdp-linux.md).

Azure Bastion bietet sichere Verbindungen mit allen virtuellen Computern in dem virtuellen Netzwerk, in dem der Dienst bereitgestellt wird. Durch die Verwendung von Azure Bastion wird verhindert, dass Ihre virtuellen Computer RDP- und SSH-Ports öffentlich verfügbar machen. Gleichzeitig wir weiterhin der sichere Zugriff per RDP/SSH ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

Wenn Sie eine Verbindung mit einem virtuellen Computer unter Linux mithilfe von SSH herstellen, können Sie sowohl Benutzername/Kennwort als auch SSH-Schlüssel zur Authentifizierung verwenden. Sie können sich auch über SSH-Schlüssel mit Ihrer VM verbinden, indem Sie Folgendes verwenden:

* einen privaten Schlüssel, den Sie manuell eingeben
* eine Datei, die den privaten Schlüssel enthält

Der private SSH-Schlüssel muss in einem Format vorliegen, das mit `"-----BEGIN RSA PRIVATE KEY-----"` beginnt und mit `"-----END RSA PRIVATE KEY-----"` endet.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie einen Azure Bastion-Host für das virtuelle Netzwerk eingerichtet haben, in dem sich die VM befindet. Weitere Informationen finden Sie unter [Erstellen eines Azure Bastion-Hosts](./tutorial-create-host-portal.md). Sobald der Bastion-Dienst provisioniert und in Ihrem virtuellen Netzwerk bereitgestellt wurde, können Sie ihn verwenden, um sich mit jeder VM in diesem virtuellen Netzwerk zu verbinden. 

### <a name="required-roles"></a>Erforderliche Rollen

Zum Herstellen einer Verbindung sind die folgenden Rollen erforderlich:

* Rolle „Leser“ auf dem virtuellen Computer
* Rolle „Leser“ auf dem Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource

### <a name="ports"></a>Ports

Zum Herstellen einer Verbindung mit dem virtuellen Linux-Computer über SSH müssen die folgenden Ports auf Ihrer VM geöffnet sein:

* Eingehender Port: SSH (22) ***oder***
* Eingehender Port: Benutzerdefinierter Wert (Sie müssen diesen benutzerdefinierten Port dann angeben, wenn Sie über Azure Bastion eine Verbindung mit der VM herstellen)

   > [!NOTE]
   > Wenn Sie einen benutzerdefinierten Portwert angeben möchten, muss Azure Bastion mithilfe der Standard-SKU konfiguriert werden. Mit der Basic-SKU können Sie keine benutzerdefinierten Ports angeben.
   >

## <a name="connect-using-username-and-password"></a><a name="username"></a>Verbinden: Verwenden von Benutzernamen und Kennwort

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Der Screenshot zeigt die Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“." lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::

1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** den **Benutzernamen** und das **Kennwort** ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/password.png" alt-text="Screenshot: Kennwortauthentifizierung":::
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Verbinden: Manuelle Eingabe eines privaten Schlüssels

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Screenshot: Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** den **Benutzernamen** und den **Privaten SSH-Schlüssel** ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-private-key.png" alt-text="Screenshot: SSH-Schlüsselauthentifizierung mit privatem Schlüssel":::
1. Geben Sie Ihren privaten Schlüssel in das Textfeld **Privater SSH-Schlüssel** ein (bzw. fügen Sie ihn direkt ein).
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Verbinden: Verwenden einer Datei mit einem privaten Schlüssel

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Screenshot: Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** in **Benutzername** und **Privater SSH-Schlüssel aus lokaler Datei** die entsprechenden Informationen ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-file.png" alt-text="Screenshot: Datei für privaten SSH-Schlüssel":::

1. Suchen Sie nach der Datei, und wählen Sie dann **Öffnen** aus.
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen. Sobald Sie auf „Verbinden“ klicken, wird SSH zu diesem virtuellen Computer direkt im Azure-Portal geöffnet. Die Verbindung erfolgt über HTML5 mit Port 443 im Bastion-Dienst und über die private IP-Adresse Ihres virtuellen Computers.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Verbinden: Verwenden eines in Azure Key Vault gespeicherten privaten Schlüssels

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/connect.png" alt-text="Screenshot: Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“" lightbox="./media/bastion-connect-vm-ssh-linux/connect.png":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** in **Benutzername** und **Privater SSH-Schlüssel aus Azure Key Vault** die entsprechenden Informationen ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/ssh-key-vault.png" alt-text="Screenshot: Privater SSH-Schlüssel aus Azure Key Vault":::
1. Wählen Sie im Dropdownmenü **Azure Key Vault** die Ressource aus, in der Sie Ihren privaten SSH-Schlüssel gespeichert haben. 

   * Wenn Sie keine Azure Key Vault-Ressource eingerichtet haben, ziehen Sie [Erstellen eines Key Vault](../key-vault/secrets/quick-create-powershell.md) zurate, und speichern Sie Ihren privaten SSH-Schlüssel als Wert eines neuen Key Vault-Geheimnisses.

   * Stellen Sie sicher, dass Sie über die Zugriffsrechte **Auflisten** und **Abrufen** für die in der Key Vault-Ressource gespeicherten Geheimnisse verfügen. Um Zugriffsrichtlinien für Ihre Key Vault-Ressource zuzuweisen und zu ändern, lesen Sie [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md).

     > [!NOTE]
     > Speichern Sie mithilfe von **PowerShell** oder der **Azure CLI** Ihren privaten SSH-Schlüssel als Geheimnis. Wenn Sie Ihren privaten Schlüssel über das Azure Key Vault-Portal speichern, wird dadurch die Formatierung beeinträchtigt und die Anmeldung schlägt fehl. Für den Fall, dass Sie Ihren privaten Schlüssel über das Portal als Geheimnis gespeichert und keinen Zugriff mehr auf die ursprüngliche private Schlüsseldatei haben, finden Sie unter [Aktualisieren eines SSH-Schlüssels](../virtual-machines/extensions/vmaccess.md#update-ssh-key) Informationen zum Aktualisieren des Zugriffs auf den virtuellen Zielcomputer mit einem neuen SSH-Schlüsselpaar.
     >

   :::image type="content" source="./media/bastion-connect-vm-ssh-linux/private-key-stored.png" alt-text="Screenshot: Azure Key Vault" lightbox="./media/bastion-connect-vm-ssh-linux/private-key-stored.png":::

1. Wählen Sie im Dropdownmenü **Azure Key Vault-Geheimnis** das Key Vault-Geheimnis aus, das den Wert Ihres privaten SSH-Schlüssels enthält.
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen. Sobald Sie auf **Verbinden** klicken, wird die SSH-Verbindung mit diesem virtuellen Computer direkt im Azure-Portal geöffnet. Die Verbindung erfolgt über HTML5 mit Port 443 im Bastion-Dienst und über die private IP-Adresse Ihres virtuellen Computers.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Bastion finden Sie in den [häufig gestellten Fragen zu Bastion](bastion-faq.md).
