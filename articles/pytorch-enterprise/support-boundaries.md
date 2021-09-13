---
title: Unterstützungsgrenzen für PyTorch Enterprise in Azure
description: In diesem Artikel werden die Unterstützungsgrenzen für PyTorch Enterprise definiert.
author: alonbochman
ms.author: alonbochman
ms.service: pytorch-enterprise
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: ea2f70af6611f6bed5a36f72324874e0461182f3
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598611"
---
# <a name="support-boundaries-for-pytorch-enterprise"></a>Unterstützungsgrenzen für PyTorch Enterprise

In diesem Dokument werden die unter PyTorch Enterprise unterstützten Module und Komponenten beschrieben.


|Bereich|Unterstützte Versionen|Notizen|
|----|----|----|
|Betriebssystem|Windows 10, Debian 9, Debian 10, Ubuntu 16.04.7 LTS, Ubuntu 18.04.5 LTS|Wir unterstützen die LTS-Versionen von Debian- und Ubuntu-Verteilungen und nur die x86_64-Architektur.|
|Python|3.6 und höher||
|PyTorch|1.8.1 oder höher||
|CUDA Toolkit|10.2, 11.1||
|ONNX-Runtime|1.7 oder höher||
|torchtext, torchvision, torch-tb-profiler, torchaudio| - |Bei Bibliotheken, die nicht über ein 1.0-Release verfügen, bieten wir Unterstützung für die spezifischen Versionen, die mit der entsprechenden unterstützten PyTorch-Version kompatibel sind. Sehen Sie sich beispielsweise diese Tabellen an: [TorchVision](https://github.com/pytorch/vision#installation), [TorchText](https://github.com/pytorch/text#installation), [TorchAudio](https://github.com/pytorch/audio/#dependencies)|
|torchserve|0.4.0 oder höher||
