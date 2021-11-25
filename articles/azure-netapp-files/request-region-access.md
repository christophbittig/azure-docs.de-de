---
title: Anforderung des Regionszugriffs für Azure NetApp files | Microsoft-Dokumentation
description: Beschreibt, wie der Zugriff auf eine Region für die Verwendung von Azure NetApp Files angefordert wird.
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
ms.date: 11/15/2021
ms.author: b-juche
ms.openlocfilehash: 9d610a74e63c85a66cd0be42cfdda3139c673f67
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520634"
---
# <a name="request-region-access-for-azure-netapp-files"></a>Anforderung des Regionszugriffs für Azure NetApp files

In einigen besonderen Situationen müssen Sie möglicherweise explizit Zugriff auf eine Region anfordern. In diesem Artikel wird erläutert, wie Sie eine Anforderung übermitteln. 

## <a name="steps"></a>Schritte

1. Wechseln Sie unter **Support + Problembehandlung** zu **Neue Supportanfrage**.   

2. Geben Sie auf der Registerkarte **Problembeschreibung** die erforderlichen Informationen an:
    1. Wählen Sie für **Ausgabentyp** die Option **Dienstleistungs- und Abonnementgrenzen (Kontingente)** .
    2. Wählen Sie unter **Abonnement** Ihr Abonnement aus. 
    3. Wählen Sie für **Kontingenttyp** die Option **Speicher: Azure NetApp Files Grenzen**.

    ![Screenshot, der die Registerkarte Problembeschreibung zeigt.](../media/azure-netapp-files/support-problem-descriptions.png)

3. Klicken Sie auf der Registerkarte **Zusätzliche Details** im Feld „Anforderungsdetails“ auf **Details eingeben**.  

    ![Screenshot: Registerkarte „Details“ mit dem Feld „Details eingeben“](../media/azure-netapp-files/quota-additional-details.png)

4. Um einen Regionszugriff zu beantragen, geben Sie die folgenden Informationen im Fenster Kontingentdetails an:   
    1. Wählen Sie unter **Kontingenttyp** **Regionszugriff** aus.
    2. Wählen Sie unter **Angeforderte Region** Ihre Region aus.

    ![Screenshot, der das Fenster „Kontingentdetails“ zum Anfordern des Regionzugriffs zeigt.](../media/azure-netapp-files/quota-details-region-access.png)

5. Klicken Sie auf **Speichern und fortfahren**. Klicken Sie auf **Überprüfen + Erstellen**, um den Antrag zu erstellen.

## <a name="next-steps"></a>Nächste Schritte  

- [Grundlegendes zur Speicherhierarchie von Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Cost model for Azure NetApp Files (Kostenmodell für Azure NetApp Files)](azure-netapp-files-cost-model.md)
- [Regionales Kapazitätskontingent für Azure NetApp Files](regional-capacity-quota.md)
- [Ressourcenlimits für Azure NetApp Files](azure-netapp-files-resource-limits.md)
