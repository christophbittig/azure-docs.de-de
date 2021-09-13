---
title: Herstellen einer Verbindung mit einem virtuellen Linux-Computer unter Verwendung von Azure Bastion
description: Erfahren Sie, wie Sie über Azure Bastion eine Verbindung mit einer Linux-VM herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/12/2021
ms.author: cherylmc
ms.openlocfilehash: 56cbcf841d26cc7f8f26235728ef0dd1c596daa0
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114289191"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Herstellen einer SSH-Verbindung mit einem virtuellen Linux-Computer über Azure Bastion

Dieser Artikel zeigt Ihnen, wie Sie in einem virtuellen Azure-Netzwerk sicher und problemlos eine SSH-Verbindung mit Linux-VMs herstellen können. Sie können sich direkt vom Azure-Portal aus mit einer VM verbinden. Wenn Sie Azure Bastion verwenden, benötigen VMs keinen Client, keinen Agent und auch keine zusätzliche Software. Weitere Informationen über Azure Bastion finden Sie in der [Übersicht](bastion-overview.md).

Sie können Azure Bastion verwenden, um eine SSH-Verbindung mit einem virtuellen Linux-Computer herzustellen. Sie können sowohl Benutzername/Kennwort als auch SSH-Schlüssel für die Authentifizierung verwenden. Sie können sich auch über SSH-Schlüssel mit Ihrer VM verbinden, indem Sie Folgendes verwenden:

* einen privaten Schlüssel, den Sie manuell eingeben
* eine Datei, die den privaten Schlüssel enthält

Der private SSH-Schlüssel muss in einem Format vorliegen, das mit `"-----BEGIN RSA PRIVATE KEY-----"` beginnt und mit `"-----END RSA PRIVATE KEY-----"` endet.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie einen Azure Bastion-Host für das virtuelle Netzwerk eingerichtet haben, in dem sich die VM befindet. Weitere Informationen finden Sie unter [Erstellen eines Azure Bastion-Hosts](./tutorial-create-host-portal.md). Sobald der Bastion-Dienst provisioniert und in Ihrem virtuellen Netzwerk bereitgestellt wurde, können Sie ihn verwenden, um sich mit jeder VM in diesem virtuellen Netzwerk zu verbinden. 

Wenn Sie die Verbindung über Bastion herstellen, wird davon ausgegangen, dass Sie RDP für die Verbindung mit einer Windows-VM und SSH für die Verbindung mit Linux-VMs verwenden. Informationen zur Verbindung mit einer Windows-VM finden Sie unter [Herstellen einer Verbindung mit einer VM – Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Erforderliche Rollen

Zum Herstellen einer Verbindung sind die folgenden Rollen erforderlich:

* Rolle „Leser“ auf dem virtuellen Computer
* Rolle „Leser“ auf dem Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource

### <a name="ports"></a>Ports

Zum Herstellen einer Verbindung mit dem virtuellen Linux-Computer über SSH müssen die folgenden Ports auf Ihrer VM geöffnet sein:

* Eingehender Port: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Verbinden: Verwenden von Benutzernamen und Kennwort

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Der Screenshot zeigt die Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“.":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** den **Benutzernamen** und das **Kennwort** ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh/password.png" alt-text="Kennwortauthentifizierung":::
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen.

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Verbinden: Manuelle Eingabe eines privaten Schlüssels

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Der Screenshot zeigt die Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“.":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** den **Benutzernamen** und den **Privaten SSH-Schlüssel** ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-private-key.png" alt-text="Authentifizierung mit privatem SSH-Schlüssel":::
1. Geben Sie Ihren privaten Schlüssel in das Textfeld **Privater SSH-Schlüssel** ein (bzw. fügen Sie ihn direkt ein).
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen.

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Verbinden: Verwenden einer Datei mit einem privaten Schlüssel

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Der Screenshot zeigt die Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“.":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** in **Benutzername** und **Privater SSH-Schlüssel aus lokaler Datei** die entsprechenden Informationen ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh/private-key-file.png" alt-text="Datei mit dem privaten SSH-Schlüssel":::

1. Suchen Sie nach der Datei, und wählen Sie dann **Öffnen** aus.
1. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen. Sobald Sie auf „Verbinden“ klicken, wird SSH zu diesem virtuellen Computer direkt im Azure-Portal geöffnet. Die Verbindung erfolgt über HTML5 mit Port 443 im Bastion-Dienst und über die private IP-Adresse Ihres virtuellen Computers.

## <a name="connect-using-a-private-key-stored-in-azure-key-vault"></a><a name="akv"></a>Verbinden: Verwenden eines in Azure Key Vault gespeicherten privaten Schlüssels

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu dem virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, klicken Sie dann auf **Verbinden**, und wählen Sie **Bastion** aus der Dropdownliste aus.

   :::image type="content" source="./media/bastion-connect-vm-ssh/connect.png" alt-text="Der Screenshot zeigt die Übersicht für einen virtuellen Computer im Azure-Portal bei ausgewähltem „Verbinden“.":::
1. Klicken Sie nach der Auswahl von „Bastion“ auf **Bastion verwenden**. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, lesen Sie bitte den Abschnitt zum [Konfigurieren von Bastion](./quickstart-host-portal.md).
1. Geben Sie auf der Seite **Verbindung über Azure Bastion herstellen** in **Benutzername** und **Privater SSH-Schlüssel aus Azure Key Vault** die entsprechenden Informationen ein.

   :::image type="content" source="./media/bastion-connect-vm-ssh/ssh-key-vault.png" alt-text="Privater SSH-Schlüssel aus Azure Key Vault":::
1. Wählen Sie im Dropdownmenü **Azure Key Vault** die Ressource aus, in der Sie Ihren privaten SSH-Schlüssel gespeichert haben. Wenn Sie keine Azure Key Vault-Ressource eingerichtet haben, ziehen Sie [Erstellen eines Key Vault](../key-vault/secrets/quick-create-powershell.md) zurate, und speichern Sie Ihren privaten SSH-Schlüssel als Wert eines neuen Key Vault-Geheimnisses.

   >[!NOTE]
   >Speichern Sie mithilfe von **PowerShell** oder der **Azure CLI** Ihren privaten SSH-Schlüssel als Geheimnis. Wenn Sie Ihren privaten Schlüssel über das Azure Key Vault-Portal speichern, wird dadurch die Formatierung beeinträchtigt und die Anmeldung schlägt fehl. Für den Fall, dass Sie Ihren privaten Schlüssel über das Portal als Geheimnis gespeichert und keinen Zugriff mehr auf die ursprüngliche private Schlüsseldatei haben, finden Sie unter [Aktualisieren eines SSH-Schlüssels](../virtual-machines/extensions/vmaccess.md#update-ssh-key) Informationen zum Aktualisieren des Zugriffs auf den virtuellen Zielcomputer mit einem neuen SSH-Schlüsselpaar.
   >

   :::image type="content" source="./media/bastion-connect-vm-ssh/key-vault.png" alt-text="Azure Key Vault":::

   Stellen Sie sicher, dass Sie über die Zugriffsrechte **Auflisten** und **Abrufen** für die in der Key Vault-Ressource gespeicherten Geheimnisse verfügen. Um Zugriffsrichtlinien für Ihre Key Vault-Ressource zuzuweisen und zu ändern, lesen Sie [Zuweisen einer Key Vault-Zugriffsrichtlinie](../key-vault/general/assign-access-policy-portal.md).
1. Wählen Sie im Dropdownmenü **Azure Key Vault-Geheimnis** das Key Vault-Geheimnis aus, das den Wert Ihres privaten SSH-Schlüssels enthält.
3. Wählen Sie **Verbinden** aus, um die Verbindung zum virtuellen Computer herzustellen. Sobald Sie auf **Verbinden** klicken, wird die SSH-Verbindung mit diesem virtuellen Computer direkt im Azure-Portal geöffnet. Die Verbindung erfolgt über HTML5 mit Port 443 im Bastion-Dienst und über die private IP-Adresse Ihres virtuellen Computers.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Bastion finden Sie in den [häufig gestellten Fragen zu Bastion](bastion-faq.md).