---
title: Lizenzieren des Portierungskits für den Microsoft&reg; Smooth Streaming-Client
description: Weitere Informationen zur Lizenzierung des Microsoft&reg; Smooth Streaming Client Porting Kit
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: xpouyat
ms.openlocfilehash: 5ad7cd598e3970532e3a14795b78edcf1f9f61c4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226136"
---
# <a name="licensing-microsoftreg-smooth-streaming-client-porting-kit"></a>Lizenzieren des Portierungskits für den Microsoft&reg; Smooth Streaming-Client

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
## <a name="overview"></a>Übersicht
Microsoft Smooth Streaming Client Porting Kit (**SSPK**) ist eine Smooth Streaming Client-Implementierung, die Herstellern eingebetteter Geräte, Kabel- und Mobilnetzbetreibern, Inhaltsdienstanbietern, Mobilgeräteherstellern, unabhängigen Softwareanbietern (ISV) und Lösungsanbietern beim Anbieten von Produkten und Dienstleistungen zum Streamen adaptiver Inhalte im Smooth Streaming-Format helfen soll. SSPK ist eine geräte- und plattformunabhängige Implementierung von Smooth Streaming Client, die vom Lizenznehmer auf jedes Gerät und jede Plattform portiert werden kann. 

Nachfolgend finden Sie eine allgemeine Architektur. IIS Smooth Streaming Porting Kit ist die Smooth Streaming Client-Implementierung von Microsoft und umfasst die gesamte grundlegende Logik für die Wiedergabe von Smooth Streaming-Inhalten. Diese Inhalte werden dann von Partnern für ein spezifisches Gerät oder eine spezifische Plattform portiert, indem entsprechende Schnittstellen implementiert werden. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>BESCHREIBUNG
SSPK ist zu Bedingungen lizenziert, die einen großen Mehrwert für Ihr Unternehmen schaffen. Die SSPK-Lizenz umfasst:

* Smooth Streaming Porting Kit-Quelle in C++ 
  * implementiert Smooth Streaming Client-Funktionen
  * bietet Formatanalyse, Heuristik, Pufferungslogik usw.
* Playeranwendungs-APIs 
  * Programmierschnittstellen für die Interaktion mit einer Medienwiedergabeanwendung
* Plattformabstraktionsschicht (PAL)-Schnittstelle 
  * Programmierschnittstellen für die Interaktion mit dem Betriebssystem (Threads, Sockets)
* Hardwareabstraktionsschicht (HAL)-Schnittstelle 
  * Programmierschnittstellen für die Interaktion mit Hardware-A/V-Decodern (Decodieren, Rendern)
* Digital Rights Management (DRM)-Schnittstelle 
  * Programmierschnittstellen für die Handhabung von DRM über die DRM-Abstraktionsschicht (DRM Abstraction Layer, DAL)
  * Das Microsoft PlayReady Porting Kit ist separat erhältlich und wird über diese Schnittstelle integriert. Weitere Informationen zur Lizenzierung von Microsoft PlayReady-Geräten finden Sie [hier](https://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Beispiele für die Implementierung 
  * PAL-Implementierungsbeispiel für Linux
  * HAL-Implementierungsbeispiel für GStreamer

## <a name="licensing-options"></a>Lizenzoptionen
Microsoft Smooth Streaming Client Porting Kit steht Lizenznehmern mit zwei verschiedenen Lizenzvereinbarungen zur Verfügung: eine für das Entwickeln von Smooth Streaming Client-Interimsprodukten und eine zweite für das Verteilen der finalen Smooth Streaming Client-Produkte an die Endbenutzer.

* Im Falle von Chipsatzherstellern, Systemintegratoren und unabhängigen Softwareanbietern (ISV), die ein Quellcode-Portierungskit zum Entwickeln von Interimsprodukten benötigen, empfiehlt sich die **Interimsproduktlizenz** für Microsoft Smooth Streaming Client Porting Kit.
* Gerätehersteller oder ISV, die Verteilungsrechte für finale Smooth Streaming Client-Produkte an die Endbenutzer benötigen, empfiehlt sich die **Finale Produktlizenz** für Microsoft Smooth Streaming Client Porting Kit.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft Smooth Streaming Client Porting Kit Interim Product License
Mit dieser Lizenz bietet Microsoft ein Smooth Streaming Client Porting Kit und die erforderlichen Schutz- und Urheberrechte zum Entwickeln und Verteilen von Smooth Streaming Client-Interimsprodukten an andere Lizenznehmer von Smooth Streaming Client Porting Kit-Geräten, die finale Smooth Streaming Client-Produkte verteilen.

#### <a name="fee-structure"></a>Gebührenstruktur
Eine einmalige Lizenzgebühr von 50.000 US-Dollar ermöglicht den Zugriff auf Smooth Streaming Client Porting Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Finale Produktlizenz für das Microsoft Smooth Streaming Client Porting Kit
Mit dieser Lizenz bietet Microsoft alle erforderlichen Schutz- und Urheberrechte zum Erhalten von Smooth Streaming Client-Interimsprodukten von anderen Smooth Streaming Client Porting Kit-Lizenznehmern und zum Verteilen von firmeneigenen finalen Smooth Streaming Client-Produkten an Endbenutzer.

#### <a name="fee-structure"></a>Gebührenstruktur
Finale Smooth Streaming Client-Produkte werden mit dem folgenden Lizenzmodell angeboten:

* 0,10 US-Dollar pro gelieferte Geräteimplementierung
* Die Lizenz ist pro Jahr auf 50.000 US-Dollar begrenzt.
* Für die ersten 10.000 Geräteimplementierungen im Jahr fällt keine Lizenzgebühr an. 

## <a name="licensing-procedure-and-sspk-access"></a>Lizenzierungsverfahren und SSPK-Zugriff
Wenden Sie sich bei Fragen zur Lizenzierung per E-Mail an [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com).

Registrierte Interimslizenznehmer können auf das SSPK-Verteilungsportal zugreifen.

Lizenznehmer von SSPK-Lizenzen (Interim und final) können sich bei technischen Fragen an [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Lizenznehmer der Microsoft Smooth Streaming Client Interim Product Agreement
* Enseo, Inc.
* Fluendo S.A.
* Guangzhou Dimai Digital Limited Co.
* Guangzhou Shikun Electronics., Ltd.
* Hisilicon Technologies Co., Ltd.
* LG Electronics, Inc.
* MediaTek Inc.
* Montage LZ Technologies Hong Kong Limited
* Panasonic Corporation
* Synamedia Limited
* Tatung Technology Inc.
* Top Victory Investments, Ltd.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Lizenznehmer der Microsoft Smooth Streaming Client Final Product Agreement
* Advanced Digital Broadcast SA
* AmTRAN Technology Co., Ltd 
* Arcadyan Technology Corporation
* Arcelik A.S
* Compal Electronics, Inc.
* Enseo, LLC
* EXPRESS LUCK TECHNOLOGY LIMITED
* FAIRWIT HONGKONG CO., LIMITED
* Fluendo S.A.
* FUNAI ELECTRIC CO., LTD
* Guangdong Asano Technology CO.,Ltd.
* Hisense Broadband Multimedia Technologies Co. Ltd.
* Hisense International Co., Ltd.
* Hisense Visual Technology Co., Ltd
* HKC Corporation Limited
* Hong Kong Konka Ltd
* Jinpin Electrical Company Ltd.Zhuhai.S.E.Z
* KAONMEDIA CO., Ltd.
* KDDI Corporation
* K-Tronics (Suzhou) Technology Co., Ltd.
* LG Electronics, Inc.
* Liberty Global Technology Services BV
* Mega Fame Electronics Co. Limited
* MINGCAI NEW CENTURY (HK) CO., LIMITED
* MIRC Electronics Limited
* MOKA INTERNATIONAL LIMITED
* ONEPLUS ELECTRONICS (SHENZHEN) CO., LTD.
* Panasonic Corporation
* Qingdao Haier Optronics Co., Ltd.
* SAGEMCOM BROADBAND SAS
* Sharp Consumer Electronics Poland Sp. z o.o.
* Shenzhen ATEKO PHOTO Electricity Co.,Ltd.
* Shenzhen Chuangwei-RGB Electronics Co.,Ltd.
* Shenzhen Jiuzhou Electric Co., Ltd
* Shenzhen KTC Technology Co., Ltd. 
* Shenzhen Maxmade Technology Co., Ltd
* Shenzhen MTC Co., Ltd
* SHENZHEN QIYUE OPTRONICS CO.,LTD
* Shenzhen Skyworth Digital Technology Co., Ltd
* Sichuan Changhong Electric Co., Ltd.
* SKARDIN INDUSTRIAL CORP
* Sky CP Ltd
* SMARDTV GLOBAL SAS
* Sony Corporation
* SoftAtHome
* Technicolor Delivery Technologies, SAS
* Top Victory Investments, Ltd.
* Vizio, Inc.
* Walton Hi-Tech Industries Ltd.
* ZTE CORPORATION

## <a name="media-services-learning-paths"></a>Media Services-Lernpfade
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

