---
title: Konfigurieren von Unix-Berechtigungen und des „Besitz ändern“-Modus für Azure NetApp Files NFS- und Dual-Protokoll-Volumes | Microsoft-Dokumentation
description: Beschreibt, wie die Unix-Berechtigungen und die „Besitz ändern“-Modusoptionen für Azure NetApp Files NFS- und Dual-Protokoll-Volumes festgelegt werden.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/06/2021
ms.author: b-juche
ms.openlocfilehash: bfb794b1344dbc4723ca699f572b487b54d60c77
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356031"
---
# <a name="configure-unix-permissions-and-change-ownership-mode-for-nfs-and-dual-protocol-volumes"></a>Konfigurieren von Unix-Berechtigungen und des „Besitz ändern“-Modus für NFS- und Dual-Protokoll-Volumes

Bei Azure NetApp Files NFS-Volumes oder Dual-Protokoll-Volumes mit dem Sicherheitsstil von `Unix` haben Sie die Möglichkeit, die **Unix-Berechtigungen** und die Optionen für den **„Besitz ändern“-Modus** ( **`Chown Mode`** ) festzulegen. Sie können diese Einstellungen während der Volumeerstellung oder danach angeben. 

## <a name="unix-permissions"></a>Unix-Berechtigungen   

Mit der Azure NetApp Files-Funktion **Unix-Berechtigungen** können Sie Änderungsberechtigungen für den Einbindepfad angeben. Die Einstellung gilt nicht für die Dateien unter dem Einbindepfad.   

Die Einstellung der Unix-Berechtigungen ist standardmäßig auf `0770` festgelegt. Diese Standardeinstellung gewährt dem Besitzer und der Gruppe Lese-, Schreib- und Ausführungsberechtigungen, anderen Benutzern werden jedoch keine Berechtigungen gewährt. 

 Sie können einen benutzerdefinierten Unix-Berechtigungswert (z. B. `0755`) angeben, {1} um dem Besitzer, der Gruppe oder anderen Benutzern die gewünschte Berechtigung zu erteilen.  

## <a name="change-ownership-mode"></a>„Besitz ändern“-Modus   

Die Funktion des „Besitz ändern“-Modus ( **`Chown Mode`** ) ermöglicht es Ihnen, die Besitzverwaltungsfunktionen von Dateien und Verzeichnissen festzulegen.  Sie können die Einstellung unter der Exportrichtlinie eines Volumes angeben oder ändern. Für den **`Chown Mode`** sind zwei Möglichkeiten verfügbar:   

* `Restricted` (Standardeinstellung): Nur der Stammbenutzer (root) kann den Besitz von Dateien und Verzeichnissen ändern.
* `Unrestricted`: Nichtstammbenutzer können den Besitz für Dateien und Verzeichnisse ändern, deren Besitzer sie sind.

## <a name="considerations"></a>Weitere Überlegungen  

* Die von Ihnen angegebenen Unix-Berechtigungen gelten nur für den Volumebereitstellungspunkt (Stammverzeichnis).  
* Sie können die Unix-Berechtigungen für Quell- oder Zielvolumes, die sich in einer regionsübergreifenden Replikationskonfiguration befinden, nicht ändern. 

## <a name="steps"></a>Schritte

1. Die Unix-Berechtigungen und Funktionen des „Besitz ändern“-Modus befinden sich derzeit in der Vorschauphase. Bevor Sie diese Funktionen zum ersten Mal verwenden, müssen Sie sie zuerst registrieren:   

    1. Registrieren der Funktion **Unix-Berechtigungen**:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions
        ```

    2.  Registrieren der Funktion **„Besitz ändern“-Modus**:    

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```

    3. Überprüfen Sie den Status der Funktionsregistrierung:    

        > [!NOTE]
        > Der **RegistrationState** kann bis zu 60 Minuten lang den Wert `Registering` aufweisen, bevor er sich in `Registered` ändert. Warten Sie, bis der Status `Registered` lautet, bevor Sie fortfahren.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFUnixPermissions  
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFChownMode
        ```
        
    Sie können auch die [Azure CLI-Befehle](/cli/azure/feature) `az feature register` und `az feature show` verwenden, um das Feature zu registrieren und den Registrierungsstatus anzuzeigen. 

2. Sie können die **Unix-Berechtigungen** und die Einstellungen des „Besitz ändern“-Modus ( **`Chown Mode`** ) auf der Registerkarte **Protokoll** angeben, wenn Sie [ein NFS-Volume erstellen](azure-netapp-files-create-volumes.md) oder [ein Dual-Protokoll-Volume erstellen](create-volumes-dual-protocol.md). 

    Das folgende Beispiel zeigt den Bildschirm „Volume erstellen“ für ein NFS-Volume. 

    ![Screenshots mit dem Bildschirm „Volume erstellen“ für NFS.](../media/azure-netapp-files/unix-permissions-create-nfs-volume.png)

3. Für vorhandene NFS- oder Dual-Protokoll-Volumes können Sie **Unix-Berechtigungen** und den **„Besitz ändern“-Modus** wie folgt festlegen oder ändern:  

    1. Um Unix-Berechtigungen zu ändern, klicken Sie mit der rechten Maustaste auf das **Volume**, und wählen Sie **Bearbeiten** aus. Geben Sie im daraufhin angezeigten Fenster „Bearbeiten“ einen Wert für **Unix-Berechtigungen** an.  
        ![Screenshots mit dem Bildschirm „Bearbeiten“ für „Unix-Berechtigungen“.](../media/azure-netapp-files/unix-permissions-edit.png)

    2. Um den „Besitz ändern“-Modus zu ändern, klicken Sie auf das **Volume**, auf **Exportrichtlinie**, und ändern Sie dann die Einstellung **`Chown Mode`** .  
        ![Screenshots mit dem Bildschirm „Exportrichtlinie“.](../media/azure-netapp-files/chown-mode-edit.png)

## <a name="next-steps"></a>Nächste Schritte  

* [Erstellen eines NFS-Volumes für Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Erstellen eines Volumes mit dualem Protokoll für Azure NetApp Files](create-volumes-dual-protocol.md) 
* [Konfigurieren der Exportrichtlinie](azure-netapp-files-configure-export-policy.md)
