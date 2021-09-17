---
title: Erstellen einer VM in einer Zone über das Azure-Portal
description: Erstellen einer VM in einer Verfügbarkeitszone über das Azure-Portal
documentationcenter: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 5/10/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 59250d3e2ed69da44e1d5e9187a12c1abac69d25
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688844"
---
# <a name="create-a-virtual-machine-in-an-availability-zone-using-the-azure-portal"></a>Erstellen einer VM in einer Verfügbarkeitszone über das Azure-Portal

**Gilt für**: :heavy_check_mark: Windows-VMs 

In diesem Artikel werden die Schritte zum Erstellen eines virtuellen Computers in einer Azure-Verfügbarkeitszone mithilfe des Azure-Portals erläutert. Eine [Verfügbarkeitszone](../../availability-zones/az-overview.md) ist eine physisch separate Zone in einer Azure-Region. Verwenden Sie Verfügbarkeitszonen, um Ihre Apps und Daten vor einem unwahrscheinlichen Fehler oder Ausfall eines gesamten Rechenzentrums zu schützen.

Um eine Verfügbarkeitszone verwenden zu können, muss der virtuelle Computer in einer [unterstützten Azure-Region](../../availability-zones/az-region.md) erstellt werden.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure 

1. Melden Sie sich unter https://portal.azure.com beim Azure-Portal an.

1. Klicken Sie auf **Ressource erstellen** > **Compute** > **Virtueller Computer**. 

3. Geben Sie die Informationen zum virtuellen Computer ein. Der Benutzername und das Kennwort werden zum Anmelden auf der VM verwendet. Das Kennwort muss mindestens zwölf Zeichen lang sein und die [definierten Anforderungen an die Komplexität](faq.yml#what-are-the-password-requirements-when-creating-a-vm-) erfüllen. 

4. Wählen Sie eine Region wie z. B. „USA, Osten 2“ aus, die Verfügbarkeitszonen unterstützt. 

5. Wählen Sie unter **Verfügbarkeitsoptionen** die Dropdownliste **Verfügbarkeitszone** aus. 

1. Wählen Sie unter **Verfügbarkeitszone** eine Zone in der Dropdownliste aus.
        
4. Wählen Sie eine Größe für den virtuellen Computer. Wählen Sie eine empfohlene Größe aus, oder filtern Sie basierend auf Features. Vergewissern Sie sich, dass die Größe in der Zone verfügbar ist, die Sie verwenden möchten.

6. Füllen Sie die Informationen zu Ihrer VM aus. Wählen Sie abschließend **Überprüfen + Erstellen** aus.

7. Nachdem die Informationen überprüft wurden, wählen Sie **Erstellen** aus.

1. Nachdem die VM erstellt wurde, wird die Verfügbarkeitszone auf der Seite der VM im Abschnitt **Grundlagen** aufgeführt.

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bestätigen der Zone für verwalteten Datenträger und IP-Adresse

Bei der Bereitstellung des virtuellen Computers in einer Verfügbarkeitszone wird in der gleichen Verfügbarkeitszone ein verwalteter Datenträger bereitgestellt. Standardmäßig wird in dieser Zone ebenfalls eine öffentliche IP-Adresse erstellt.

Sie können die Zoneneinstellungen für diese Ressourcen im Portal überprüfen.  

1. Wählen Sie im linken Menü **Datenträger** und dann den Betriebssystemdatenträger aus. Die Seite für den Datenträger enthält Details über den Standort und die Verfügbarkeitszone des Datenträgers.

1. Wählen Sie zurück auf der Seite der VM die öffentliche IP-Adresse aus. Wählen Sie im Menü auf der linken Seite die Option **Eigenschaften** aus. Die Seite „Eigenschaften“ enthält Details über den Standort und die Verfügbarkeitszone der öffentlichen IP-Adresse.

    
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie einen virtuellen Computer in einer Verfügbarkeitszone erstellen. Erfahren Sie mehr über die [Verfügbarkeit](../availability.md) virtueller Azure-Computer.
