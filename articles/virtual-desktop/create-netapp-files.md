---
title: Erstellen von Azure NetApp Files in Azure Virtual Desktop – Azure
description: In diesem Artikel wird beschrieben, wie Sie Azure NetApp Files in Azure Virtual Desktop erstellen.
author: Heidilohr
ms.topic: how-to
ms.date: 08/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 244ff34b462d519d111aad6d6b66d7261e612595
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446785"
---
# <a name="upload-msix-images-to-azure-netapp-files-in-azure-virtual-desktop"></a>Hochladen von MSIX-Images in Azure NetApp Files in Azure Virtual Desktop

In diesem Artikel wird beschrieben, wie Sie MSIX-Images in Azure NetApp Files in Azure Virtual Desktop hochladen.

## <a name="requirements"></a>Requirements (Anforderungen)

Bevor Sie mit dem Hochladen der Bilder beginnen können, müssen Sie Azure NetApp Files einrichten, sofern das noch nicht geschehen ist.

Zum Einrichten Azure NetApp Files benötigen Sie Folgendes:

- Ein Azure-Konto mit Zugriff als Mitwirkender oder Administrator

- Einen virtueller Computer (VM) oder einen physischer Computer, der mit Active Directory Domain Services (AD DS) und Zugriffsberechtigungen verbunden ist

- Ein Azure Virtual Desktop-Hostpool, der aus in die Domäne eingebundenen Sitzungshosts besteht. Jeder Sitzungshost muss sich in derselben Region in der sich die Region befindet, in der Sie Ihre Azure NetApp-Dateien erstellen. Weitere Informationen finden Sie unter [Regionale Verfügbarkeit](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Wenn sich Ihre vorhandenen Sitzungshosts nicht in einer der verfügbaren Regionen befinden, müssen Sie neue erstellen.

## <a name="start-using-azure-netapp-files"></a>Beginnen Sie mit der Verwendung von Azure NetApp Files

Um mit der Verwendung von Azure NetApp Files zu beginnen:

1. Befolgen Sie die Anweisungen unter [Registrieren für Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md), um eine Wartelistenanforderung zu übermitteln und Ihren NetApp-Anbieter zu registrieren.
2. Richten Sie Ihr Azure NetApp Files-Konto ein, indem Sie die Anweisungen unter [Einrichten Ihres Azure NetApp Files-Konto](create-fslogix-profile-container.md#set-up-your-azure-netapp-files-account) befolgen.
3. Befolgen Sie die Anweisungen im Artikel [Einrichten eines Kapazitätspools](../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md), um einen Kapazitätspool zu erstellen.
4. Verknüpfen Sie eine Azure Active Directory-Verbindung (Azure AD), indem Sie die Anweisungen unter [Verknüpfen einer Active Directory-Verbindung](create-fslogix-profile-container.md#join-an-active-directory-connection) befolgen.
5. Erstellen Sie ein neues Volume, indem Sie die Anweisungen in [Neues Volume erstellen](create-fslogix-profile-container.md#create-a-new-volume) und [Volume-Zugriffsparameter konfigurieren](create-fslogix-profile-container.md#configure-volume-access-parameters) befolgen.
6. Stellen Sie sicher, dass Ihre Verbindung mit der Azure NetApp Files-Freigabe funktioniert, indem Sie die Anweisungen unter [Sicherstellen, dass Benutzer auf die Azure NetApp Files-Freigabe zugreifen können](create-fslogix-profile-container.md#make-sure-users-can-access-the-azure-netapp-file-share).

## <a name="upload-an-msix-image-to-the-azure-netapp-file-share"></a>Hochladen eines MSIX-Images in die Azure NetApp-Dateifreigabe

Nachdem Sie Ihre Azure NetApp Files-Freigabe eingerichtet haben, können Sie damit beginnen, Bilder in die Freigabe hochzuladen.

So laden Sie ein MSIX-Image in Ihre Azure NetApp Files-Freigabe:

1. Installieren Sie auf jedem Sitzungshost das Zertifikat, mit dem Sie das MSIX-Paket signiert haben. Stellen Sie sicher, dass Sie die Zertifikate im Ordner mit dem Namen **Vertrauenswürdige Personen** speichern.
2. Kopieren Sie das MSIX-Image, das Sie der Azure NetApps Files-Freigabe hinzufügen möchten.
3. Geben Sie im **Datei-Explorer** den Einbindepfad ein und fügen Sie dann das MSIX-Image in den Ordner im Einbindepfad ein.

Ihr MSIX-Image sollte nun für Ihre Sitzungshosts zugänglich sein, wenn sie ein MSIX-Paket mithilfe des Microsoft Azure-Portals oder der PowerShell hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine Freigabe für Azure NetApp Files erstellt haben, finden Sie hier einige Ressourcen darüber, wofür Sie diese in Azure Virtual Desktop verwenden können:

- [Erstellen eines Profilcontainers mit Azure NetApp Files und AD DS](create-fslogix-profile-container.md)
- [Speicheroptionen für FSLogix-Profilcontainer in Azure Virtual Desktop](store-fslogix-profile.md)
- [Erstellen von Replikationspeering für Azure NetApp Files](../azure-netapp-files/cross-region-replication-create-peering.md)
