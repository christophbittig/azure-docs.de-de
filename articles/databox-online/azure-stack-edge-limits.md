---
title: Grenzwerte für Azure Stack Edge Pro GPU/Pro FPGA | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über Grenzwerte und empfohlene Größen beim Bereitstellen und Betreiben von Azure Stack Edge Pro GPU/Pro FPGA, einschließlich Grenzwerte für Dienste, Geräte und Speicher.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: 64be85db0e2d11d2aee5a61742f427087a77cef2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346515"
---
# <a name="azure-stack-edge-limits"></a>Grenzwerte für Azure Stack Edge

Berücksichtigen Sie beim Bereitstellen und Betreiben Ihrer Microsoft Azure Stack Edge Pro GPU- oder Azure Stack Edge Pro FPGA-Lösung die folgenden Grenzwerte. 

## <a name="azure-stack-edge-service-limits"></a>Grenzwerte für den Azure Stack Edge-Dienst

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Grenzwerte für Azure Stack Edge-Geräte

In der folgenden Tabelle werden die für ein Azure Stack Edge-Gerät geltenden Grenzwerte beschrieben.

| BESCHREIBUNG | Wert |
|---|---|
|Nein. der Dateien pro Gerät |100 Mio. |
|Nein. der Freigaben pro Container |1 |
|Maximale Anzahl von Freigabeendpunkten und REST-Endpunkten pro Gerät (nur GPU-Geräte)| 24 |
|Maximale Anzahl von mehrstufigen Speicherkonten pro Gerät (nur GPU-Geräte)| 24|
|Maximale in eine Freigabe geschriebene Dateigröße| 5 TB |
|Maximale Anzahl von Ressourcengruppen pro Gerät| 800 |

## <a name="azure-storage-limits"></a>Speichergrenzwerte für Azure

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>Einschränkungen beim Hochladen von Daten

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Größenbeschränkungen für das Azure-Speicherkonto und Objekte

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Größenbeschränkungen für das Azure-Objekt

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>Nächste Schritte

- [Vorbereiten der Bereitstellung von Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md)
- [Vorbereiten der Bereitstellung von Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)
