---
title: Datei einfügen
description: Datei einfügen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491abcae4bf73d6b2dcd8172fcb1b69cb7b60d32
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807477"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Erstellen eines Themas mit dem Azure-Portal
1. Wählen Sie auf der Seite **Service Bus-Namespace** im linken Menü die Option **Themen** aus.
2. Wählen Sie auf der Symbolleiste die Option **+ Thema** aus. 
4. Geben Sie unter **Name** einen Namen für das Thema ein. Behalten Sie bei den anderen Optionen die Standardwerte bei.
5. Wählen Sie **Erstellen** aus.

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-topic.png" alt-text="Abbildung: Seite „Thema erstellen“":::

## <a name="create-a-subscription-to-the-topic"></a>Erstellen eines Abonnements für das Thema
1. Wählen Sie das **Thema** aus, das Sie im vorherigen Abschnitt erstellt haben. 
    
    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/select-topic.png" alt-text="Abbildung: Auswahl des Themas aus der Themenliste":::
2. Wählen Sie auf der Seite **Service Bus-Thema** auf der Symbolleiste die Option **+ Abonnement** aus. 

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png" alt-text="Abbildung: Schaltfläche zum Hinzufügen eines Abonnements":::    
3. Führen Sie auf der Seite **Abonnement erstellen** die folgenden Schritte aus:
    1. Geben Sie **S1** für **Name** des Abonnements ein.
    1. Geben Sie **3** für **Maximale Lieferungsanzahl** ein.
    1. Wählen Sie dann **Erstellen**, um das Abonnement zu erstellen. 

        :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png" alt-text="Abbildung: Seite zum Erstellen eines Abonnements":::
