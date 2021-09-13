---
title: Azure Bastion – Problembehandlung | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Probleme mit Azure Bastion beheben.
services: bastion
author: charwen
ms.service: bastion
ms.topic: troubleshooting
ms.date: 10/16/2019
ms.author: charwen
ms.openlocfilehash: 6b8ce0014524a407d6d35b85fed3bf5f41b556cf
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356197"
---
# <a name="troubleshoot-azure-bastion"></a>Problembehandlung für Azure Bastion

In diesem Artikel erfahren Sie, wie Sie die Problembehandlung für Azure Bastion ausführen.

## <a name="unable-to-create-an-nsg-on-azurebastionsubnet"></a><a name="nsg"></a>Fehler beim Erstellen einer NSG im AzureBastionSubnet

**F:** Beim Versuch, eine NSG im Azure Bastion-Subnetz zu erstellen, wird ein mit dem folgenden vergleichbarer Fehler angezeigt: *„Die Netzwerksicherheitsgruppe <NSG name> verfügt nicht über die erforderlichen Regeln für das Azure Bastion-Subnetz ‚AzureBastionSubnet‘“* .

**A:** Wenn Sie eine NSG erstellen und auf *AzureBastionSubnet* anwenden, stellen Sie sicher, dass Sie der NSG die erforderlichen Regeln hinzugefügt haben. Eine Liste der erforderlichen Regeln finden Sie unter [Verwenden von NSG-Zugriff und Azure Bastion](./bastion-nsg.md). Wenn Sie diese Regeln nicht hinzufügen, schlägt die Erstellung/Aktualisierung der NSG fehl.

Ein Referenzbeispiel für die NSG-Regeln finden Sie in der [Schnellstartvorlage](https://azure.microsoft.com/resources/templates/azure-bastion-nsg/).
Weitere Informationen finden Sie im [NSG-Leitfaden für Azure Bastion](bastion-nsg.md).

## <a name="unable-to-use-my-ssh-key-with-azure-bastion"></a><a name="sshkey"></a>Der SSH-Schlüssel kann nicht mit Azure Bastion verwendet werden.

**F:** Wenn ich versuche, meine SSH-Schlüsseldatei zu durchsuchen, wird ein mit dem folgenden vergleichbarer Fehler angezeigt: *„Der private SSH-Schlüssel muss mit ‚-----BEGIN RSA PRIVATE KEY-----‘ beginnen und mit ‚-----END RSA PRIVATE KEY-----‘ enden.“*

**A:** Azure Bastion unterstützt zu diesem Zeitpunkt nur RSA-SSH-Schlüssel. Stellen Sie sicher, dass Sie eine Schlüsseldatei durchsuchen, bei der es sich um einen privaten RSA-Schlüssel für SSH handelt, wobei der öffentliche Schlüssel auf der Ziel-VM bereitgestellt wird. 

Beispielsweise können Sie den folgenden Befehl verwenden, um einen neuen RSA-SSH-Schlüssel zu erstellen:

**ssh-keygen -t rsa -b 4096 -C "email@domain.com"**

Ausgabe:

```
ashishj@dreamcatcher:~$ ssh-keygen -t rsa -b 4096 -C "email@domain.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ashishj/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ashishj/.ssh/id_rsa.
Your public key has been saved in /home/ashishj/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:c+SBciKXnwceaNQ8Ms8C4h46BsNosYx+9d+AUxdazuE email@domain.com
The key's randomart image is:
+---[RSA 4096]----+
|      .o         |
| .. ..oo+. +     |
|=.o...B==.O o    |
|==o  =.*oO E     |
|++ .. ..S =      |
|oo..   + =       |
|...     o o      |
|         . .     |
|                 |
+----[SHA256]-----+
```

## <a name="unable-to-sign-in-to-my-windows-domain-joined-virtual-machine"></a><a name="domain"></a>Anmeldung bei einem virtuellen, in die Domäne eingebundenen Windows-Computer nicht möglich

**F:** Ich kann keine Verbindung mit meinem virtuellen Windows-Computer herstellen, der in die Domäne eingebunden ist.

**A:** Azure Bastion unterstützt die Anmeldung bei virtuellen, in die Domäne eingebundenen Computern nur für Domänenanmeldungen, die auf einem Benutzernamen und Kennwort basieren. Wenn Sie die Domänenanmeldeinformationen im Azure-Portal angeben, verwenden Sie das UPN-Format (username@domain) anstelle des *domain\username*-Formats, um sich anzumelden. Dies wird für in die Domäne oder in Hybridumgebungen eingebundene virtuelle Computer unterstützt (die sowohl in die Domäne als auch in Azure AD eingebunden sind). Virtuelle Computer, die nur in Azure AD eingebunden sind, werden nicht unterstützt.

## <a name="unable-to-connect-to-virtual-machine"></a><a name="connectivity"></a> Herstellen der Verbindung mit der VM nicht möglich

**F:** Ich kann keine Verbindung mit meiner VM herstellen (aber die oben genannten Probleme treten nicht auf).

**A:** Sie können Ihre Konnektivitätsprobleme beheben, indem Sie zur Registerkarte **Problembehandlung für Verbindungen** (im Abschnitt **Überwachung**) Ihrer Azure Bastion-Ressource im Azure-Portal navigieren. Die Problembehandlung von Verbindungen von Network Watcher bietet die Möglichkeit, eine direkte TCP-Verbindung von einer VM mit einer anderen VM, einem vollqualifizierten Domänennamen (FQDN), einem URI oder einer IPv4-Adresse zu überprüfen. Wählen Sie zu Beginn eine Quelle aus, von der aus die Verbindung gestartet werden soll, wählen Sie anschließend das Ziel aus, mit dem Sie eine Verbindung herstellen möchten, und wählen Sie dann „Überprüfen“ aus. [Weitere Informationen](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview)


## <a name="file-transfer-issues"></a><a name="filetransfer"></a>Probleme bei der Dateiübertragung

**F:** Wird die Dateiübertragung mit Azure Bastion unterstützt?

**A:** Die Dateiübertragung wird zurzeit nicht unterstützt. Wir arbeiten daran, die Unterstützung zu implementieren.

## <a name="black-screen-in-the-azure-portal"></a><a name="blackscreen"></a>Schwarzer Bildschirm im Azure-Portal

**F:** Wenn ich versuche, eine Verbindung mithilfe von Azure Bastion herzustellen, wird keine Verbindung mit der Ziel-VM hergestellt, und ich erhalte einen schwarzen Bildschirm im Azure-Portal.

**A:** Dies geschieht, wenn ein Problem mit der Netzwerkverbindung zwischen dem Webbrowser und Azure Bastion vorliegt (beispielsweise kann Ihre Client-Internetfirewall den WebSockets-Datenverkehr blockieren) oder zwischen Azure Bastion und der Ziel-VM. In den meisten Fälle wurde auch eine NSG entweder auf AzureBastionSubnet oder auf das Subnetz der Ziel-VM angewendet, die den RDP-/SSH-Datenverkehr in Ihrem virtuellen Netzwerk blockiert. Lassen Sie WebSockets-Datenverkehr über die Client-Internetfirewall zu, und überprüfen Sie die NSGs für das Subnetz Ihrer Ziel-VM.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den [häufig gestellten Fragen zu Bastion](bastion-faq.md).
