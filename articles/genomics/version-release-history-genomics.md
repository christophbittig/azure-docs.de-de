---
title: Versionsverlauf
titleSuffix: Microsoft Genomics
description: Der Verlauf der Aktualisierungsveröffentlichungen für den Microsoft Genomics-Python-Client, um Fehlerbehebungen und neue Funktionen einzuführen.
services: genomics
author: vigunase
manager: cgronlun
ms.author: vigunase
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 3023f01b9a9ccbc3a56c509b1edc86885f2af41e
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255682"
---
# <a name="version-release-history"></a>Versionsverlauf
Das Microsoft Genomics-Team aktualisiert regelmäßig den Microsoft Genomics-Python-Client um Fehlerbehebungen und neue Funktionen einzuführen. 

## <a name="latest-release"></a>Aktuelles Release
Der aktuelle Python-Client ist Version 0.9.0. Die Version wurde am 6. Februar 2019 veröffentlicht und unterstützt die Ausführung von Workflows mit GATK 3.5 und GATK 4. Sie unterstützt die gVCF-Ausgabe und akzeptiert ein optionales Argument für die Ausgabekomprimierung.


## <a name="release-history"></a>Releaseverlauf 
Neue Versionen des Microsoft Genomics-Python-Clients werden etwa einmal pro Jahr veröffentlicht. Wenn neue Versionen des Microsoft Genomics-Python-Clients veröffentlicht werden, wird hier die Liste der Fehlerbehebungen und Features aktualisiert. Wenn neue Versionen veröffentlicht werden, sollten frühere Versionen mindestens 90 Tage lang weiterhin unterstützt werden. Wenn frühere Versionen nicht mehr unterstützt werden, wird dies auf dieser Seite angegeben. 

### <a name="version-090"></a>Version 0.9.0
Version 0.9.0 bietet Unterstützung für die Ausgabekomprimierung. Dies entspricht dem Ausführen von `-bgzip` gefolgt von `-tabix` in der VCF- oder gVCF-Ausgabe. Weitere Informationen finden Sie unter [Häufig gestellte Fragen](frequently-asked-questions-genomics.yml). 

### <a name="version-081"></a>Version 0.8.1
Version 0.8.1 enthält Behebungen geringfügiger Programmfehler.  

### <a name="version-080"></a>Version 0.8.0
Version 0.8.0 umfasst Unterstützung für GATK4 und gVCF-Ausgaben.  

### <a name="version-074"></a>Version 0.7.4
Version 0.7.4 bietet Unterstützung für das Akzeptieren von SAS-Token anstelle von Kontoschlüsseln in der `config.txt` Eingabe. Weitere Informationen finden Sie unter [Übermitteln eines Workflows per SAS anstelle eines Speicherkontoschlüssels](quickstart-input-sas.md). 

### <a name="version-073"></a>Version 0.7.3
Version 0.7.3 enthält Behebungen geringfügiger Programmfehler.

### <a name="version-072"></a>Version 0.7.2
0.7.2 ist die erste Version. Sie wurde am 1. November 2017 veröffentlicht.
