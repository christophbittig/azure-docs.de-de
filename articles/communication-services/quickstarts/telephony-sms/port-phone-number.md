---
title: 'Schnellstart: Portieren einer Telefonnummer in Azure Communication Services'
description: Hier erfahren Sie, wie Sie eine Telefonnummer in Ihre Communication Services-Ressource portieren.
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: pstn
ms.custom: references_regions
ms.openlocfilehash: 611f6b50d1d7ab6cd2578ca7d4e56a97f25b6f51
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128677015"
---
# <a name="quickstart-port-a-phone-number"></a>Schnellstart: Portieren einer Telefonnummer

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Steigen Sie in Azure Communication Services ein, indem Sie Ihre Telefonnummer in Ihre Azure Communication Services-Ressource portieren. Portiert werden können gebührenfreie und geografische US-Telefonnummern. Weitere Informationen zu Telefonnummerntypen finden Sie unter [Telefonnummerntypen in Azure Communication Services](../../concepts/telephony-sms/plan-solution.md).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Eine aktive Communication Services-Ressource.](../create-communication-resource.md)

## <a name="gather-your-azure-resource-details"></a>Sammeln der Details für Ihre Azure-Ressource

Navigieren Sie vor dem Initiieren einer Portierungsanforderung zum Azure-Portal, und wählen Sie Ihre Communication Services-Ressource aus. Wählen Sie den Bereich `Overview` aus, und klicken Sie in der rechten oberen Ecke auf den Link `JSON View`:

:::image type="content" source="./media/number-port/json-view.png" alt-text="Screenshot: Auswählen der JSON-Ansicht":::

Notieren Sie sich die **Azure-ID** und die **unveränderliche Ressourcen-ID** Ihrer Ressource:

:::image type="content" source="./media/number-port/json-properties.png" alt-text="Screenshot: Auswählen der JSON-Eigenschaften":::

## <a name="initiate-the-port-request"></a>Initiieren der Portierungsanforderung

Portiert werden können gebührenfreie und geografische US-Telefonnummern. Verwenden Sie eines der folgenden Formulare, um Ihre Portierungsanforderung zu übermitteln:

- Gebührenfreie Telefonnummer: [Portierungsanforderung für gebührenfreie Telefonnummer](https://aka.ms/acs-port-form-tollfree)
- Geografische Telefonnummer in den USA: [Portierungsanforderung für geografische Telefonnummer](https://aka.ms/acs-port-form-geographic)

Senden Sie Ihr vollständig ausgefülltes Portierungsanforderungsformular an acsporting@microsoft.com. Achten Sie darauf, dass die Betreffzeile Ihrer E-Mail mit „ACS Port-In Request“ beginnt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erfassen der Metadaten Ihrer Communication Services-Ressource
> * Übermitteln einer Anforderung zum Portieren Ihrer Telefonnummer

> [!div class="nextstepaction"]
> [Senden einer SMS](../telephony-sms/send.md)
> [Erste Schritte für das Tätigen von Anrufen](../voice-video-calling/getting-started-with-calling.md)
