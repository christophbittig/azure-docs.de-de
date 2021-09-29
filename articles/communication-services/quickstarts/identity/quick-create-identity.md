---
title: 'Schnellstart: Schnelles Erstellen von Azure Communication Services Identitäten zum Testen'
titleSuffix: An Azure Communication Services quickstart
description: Erfahren Sie, wie Sie das Identitäten- und Zugriffstool in dem Azure-Portal für die Verwendung mit Beispielen und für die Problembehandlung verwenden.
author: manoskow
manager: chpalm
services: azure-communication-services
ms.author: manoskow
ms.date: 07/19/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 63a08ba4d117160177124d1951720f6d9a4fedcc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128671923"
---
# <a name="quickstart-quickly-create-azure-communication-services-access-tokens-for-testing"></a>Eine Schnellstartanleitung für das schnelle Erstellen von Azure Communication Services Identitäten zum Testen

In der Communication Services-Erweiterung des [Azure-Portal](https://portal.azure.com) können Sie eine Communication Services-Identität und Zugriffstoken generieren. Auf diese Weise können Sie die Erstellung eines Authentifizierungsdiensts überspringen. Das erleichtert Ihnen das Testen der Beispiel-Apps und einfacher Entwicklungsszenarien. Diese Funktion ist für kleinere Überprüfungen und Tests vorgesehen und sollte nicht für die Produktionsszenarien verwendet werden. Produktionscode finden Sie in der [Schnellstartanleitung zum Erstellen von Zugriffstoken](../access-tokens.md).

Das Tool zeigt das Verhalten von ```Identity SDK``` in einer einfachen Benutzeroberfläche. Token und Identitäten, die mit diesem Tool erstellt werden, folgen dem gleichen Verhalten und den gleichen Regeln, als ob sie mit der ```Identity SDK``` erstellt würden.  Beispielsweise laufen Zugriffstoken nach 24 Stunden ab.

## <a name="prerequisites"></a>Voraussetzungen

- Eine [Azure Communication Services-Ressource](../create-communication-resource.md)

## <a name="create-the-access-tokens"></a>Erstellen der Zugriffstoken

Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu dem Blatt **Identitäten und Benutzerzugriffstoken** in Ihrer Communication Services-Ressource. 

Wählen Sie den Umfang der Zugriffstoken aus. Sie können keine, eine oder mehrere auswählen. Klicken Sie auf **Generate (Generieren)** .

![Wählen Sie die Bereiche der Identitäts- und Zugriffstoken aus.](../media/quick-create-identity-choose-scopes.png)

Sie werden sehen, dass eine Identität und ein entsprechendes Benutzerzugriffstoken generiert werden. Sie können diese Zeichenfolgen kopieren und in den [Beispiel-Apps](../../samples/overview.md) und anderen Testszenarien verwenden.

![Die Identitäts- und Zugriffstoken werden generiert und zeigen das Ablaufdatum an.](../media/quick-create-identity-generated.png)

## <a name="next-steps"></a>Nächste Schritte


Das könnte Sie auch interessieren:

 - [Informationen zur Authentifizierung](../../concepts/authentication.md)
 - [Erfahren Sie mehr über die Client- und Serverarchitektur](../../concepts/client-and-server-architecture.md)
