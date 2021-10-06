---
title: Datei einfügen
description: Datei einfügen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/20/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3a5413591ba637364bbb42a7e7aedf77b40fb898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565696"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Erstellen eines Themas mit dem Azure-Portal
1. Wählen Sie auf der Seite **Service Bus-Namespace** im linken Menü die Option **Themen** aus.
2. Wählen Sie auf der Symbolleiste die Option **+ Thema** aus. 
4. Geben Sie unter **Name** einen Namen für das Thema ein. Behalten Sie bei den anderen Optionen die Standardwerte bei.
5. Wählen Sie **Erstellen** aus.

    ![Thema erstellen](./media/service-bus-create-topics-subscriptions-portal/create-topic.png)

## <a name="create-subscriptions-to-the-topic"></a>Erstellen von Abonnements für das Thema
1. Wählen Sie das **Thema** aus, das Sie im vorherigen Abschnitt erstellt haben. 
    
    ![Auswählen des Themas](./media/service-bus-create-topics-subscriptions-portal/select-topic.png)
2. Wählen Sie auf der Seite **Service Bus-Thema** im linken Menü die Option **Abonnements** und anschließend auf der Symbolleiste die Option **+ Abonnement** aus. 
    
    ![Schaltfläche „Abonnement hinzufügen“](./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png)
3. Führen Sie auf der Seite **Abonnement erstellen** die folgenden Schritte aus:
    1. Geben Sie **S1** für **Name** des Abonnements ein.
    1. Geben Sie **3** für **Maximale Lieferungsanzahl** ein.
    1. Wählen Sie dann **Erstellen**, um das Abonnement zu erstellen. 

        ![Seite „Abonnement erstellen“](./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png)
4. Wiederholen Sie den vorherigen Schritt zweimal, um die Abonnements mit den Namen **S2** und **S3** zu erstellen.