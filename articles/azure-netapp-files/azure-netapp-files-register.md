---
title: Registrierung für NetApp Resource Provider zur Verwendung mit Azure NetApp Files | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den NetApp Resource Provider für Azure NetApp Files registrieren.
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
ms.date: 10/04/2021
ms.author: b-juche
ms.openlocfilehash: b8b70a06a6bda9757f9019a6f753af226235a985
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407739"
---
# <a name="register-for-netapp-resource-provider"></a>Registrieren für NetApp-Ressourcenanbieter

Um den Azure NetApp Files Service zu nutzen, müssen Sie den NetApp Resource Provider registrieren.

1. Klicken Sie im Azure-Portal in der rechten oberen Ecke auf das Symbol für die Azure Cloud Shell:

      ![Azure Cloud Shell-Symbol](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Wenn Sie in Ihrem Azure-Konto über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das für Azure NetApp Files genehmigt wurde:
    
    ```azurecli
    az account set --subscription <subscriptionId>
    ```

3. Geben Sie in der Azure Cloud Shell-Konsole den folgenden Befehl ein, um zu überprüfen, ob Ihr Abonnement genehmigt wurde:
    
    ```azurecli
    az feature list | grep NetApp
    ```

   Die Ausgabe des Befehls sieht wie folgt aus:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` ist Ihre Abonnement-ID.



4. Geben Sie in der Azure Cloud Shell-Konsole den folgenden Befehl ein, um den Azure-Ressourcenanbieter zu registrieren: 
    
    ```azurecli
    az provider register --namespace Microsoft.NetApp --wait
    ```

   Der `--wait`-Parameter weist die Konsole an, auf den Abschluss der Registrierung zu warten. Der Registrierungsvorgang kann einige Zeit in Anspruch nehmen.

5. Geben Sie in der Azure Cloud Shell-Konsole den folgenden Befehl ein, um zu überprüfen, ob der Azure-Ressourcenanbieter registriert wurde: 
    
    ```azurecli
    az provider show --namespace Microsoft.NetApp
    ```

   Die Ausgabe des Befehls sieht wie folgt aus:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` ist Ihre Abonnement-ID.  Der Parameterwert `state` gibt `Registered` an.

6. Klicken Sie im Azure-Portal auf das Blatt **Abonnements**.
7. Klicken Sie auf dem Blatt „Abonnements“ auf Ihre Abonnement-ID. 
8. Klicken Sie in den Einstellungen des Abonnements auf **Ressourcenanbieter**, um zu überprüfen, ob der Microsoft.NetApp-Anbieter den Status „Registriert“ aufweist: 

      ![Registrierte Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines NetApp-Kontos](azure-netapp-files-create-netapp-account.md)
* [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
