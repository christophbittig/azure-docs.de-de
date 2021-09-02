---
title: Problembehandlung – Azure IoT Hub-Fehler „409001 DeviceAlreadyExists“
description: Grundlegendes zum Beheben des Fehlers „409001 DeviceAlreadyExists“
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: f6ef32537da22324ac2d4991fac9df785374e16f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829199"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

In diesem Artikel werden die Ursachen des Fehlers **409001 DeviceAlreadyExists** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

Wenn Sie versuchen, ein Gerät in IoT Hub zu registrieren, schlägt die Anforderung mit der Meldung **409001 DeviceAlreadyExists** fehl.

## <a name="cause"></a>Ursache

Es gibt im IoT-Hub bereits ein Gerät mit derselben Geräte-ID. 

## <a name="solution"></a>Lösung

Verwenden Sie eine andere Geräte-ID, und wiederholen Sie den Vorgang.