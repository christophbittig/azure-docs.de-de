---
title: Verschlüsselung ruhender Azure IoT Hub-Daten über kundenseitig verwaltete Schlüssel | Microsoft-Dokumentation
description: Verschlüsselung ruhender Azure IoT Hub-Daten über kundenseitig verwaltete Schlüssel
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: asrastog
ms.openlocfilehash: 4dd3ee01504c2777ad72e32e11932b3b63d07448
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515536"
---
# <a name="encryption-of-azure-iot-hub-data-at-rest-using-customer-managed-keys"></a>Verschlüsselung ruhender Azure IoT Hub-Daten über kundenseitig verwaltete Schlüssel

IoT Hub unterstützt die Verschlüsselung ruhender Daten mit kundenseitig verwalteten Schlüsseln (Customer-Managed Keys, CMK), auch bezeichnet als „Bring Your Own Key“ (BYOK). Azure IoT Hub ermöglicht die Verschlüsselung ruhender Daten und von übertragenen Daten, die in unsere Rechenzentren geschrieben werden. Die Daten werden beim Lesen verschlüsselt und beim Schreiben entschlüsselt. 

Standardmäßig verwendet IoT Hub von Microsoft verwaltete Schlüssel, um die Daten zu verschlüsseln. Mit CMK können Sie eine weitere Verschlüsselungsebene zusätzlich zur Standardverschlüsselung erhalten, indem Sie sich dafür entscheiden, ruhende Daten mit einem Schlüsselverschlüsselungsschlüssel zu verschlüsseln, der über [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) verwaltet wird. Dadurch erhalten Sie die Flexibilität, Zugriffssteuerungen zu erstellen, zu rotieren, zu deaktivieren und zu widerrufen. Wenn für Ihren IoT Hub BYOK konfiguriert ist, bieten wir darüber hinaus doppelte Verschlüsselung an, mit der eine zweite Schutzebene zur Verfügung steht, deren Verschlüsselungsschlüssel Sie immer noch über Ihre Azure Key Vault-Instanz kontrollieren können.

Diese Funktion erfordert die Erstellung eines neuen IoT-Hubs (Tarif Basic oder Standard): Um diese Funktion auszuprobieren, kontaktieren Sie uns über den [Microsoft-Support](https://azure.microsoft.com/support/create-ticket/). Geben Sie Ihren Firmennamen und Ihre Abonnement-ID an, wenn Sie sich an den Microsoft-Support wenden.

## <a name="next-steps"></a>Nächste Schritte

* [Was ist IoT Hub?](./about-iot-hub.md)

* [Weitere Informationen zu Azure Key Vault](../key-vault/general/overview.md)
