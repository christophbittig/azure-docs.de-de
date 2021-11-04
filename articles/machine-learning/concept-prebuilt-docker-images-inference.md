---
title: Vordefinierte Docker-Images
titleSuffix: Azure Machine Learning
description: Vordefinierte Docker-Images für Rückschlüsse (Bewertung) in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, curated environments
ms.openlocfilehash: 0f7fc034ee6df962ecfe6e00b62b8899e223505c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563699"
---
# <a name="prebuilt-docker-images-for-inference"></a>Vordefinierte Docker-Images für Rückschlüsse

Vordefinierte Docker-Containerimages für Rückschlüsse werden beim Bereitstellen eines Modells mit Azure Machine Learning verwendet.  Die Images sind mit gängigen Frameworks für maschinelles Lernen und Python-Paketen vordefiniert. Sie können die Pakete auch erweitern, um andere Pakete hinzuzufügen. Verwenden Sie dazu eine der folgenden Methoden:

* [Hinzufügen von Python-Paketen](how-to-prebuilt-docker-images-inference-python-extensibility.md)
* [Verwenden eines vordefinierten Rückschlussimages als Grundlage für ein neues Dockerfile.](how-to-extend-prebuilt-docker-image-inference.md) Mit dieser Methode können Sie sowohl **Python-Pakete als auch apt-Pakete installieren.**

## <a name="why-should-i-use-prebuilt-images"></a>Was spricht für die Verwendung vordefinierter Images?

* Verringern der Wartezeit bei der Modellbereitstellung
* Verbessern der Erfolgsrate bei der Modellbereitstellung
* Vermeiden unnötiger Imageerstellungen während der Modellbereitstellung
* Sicherstellen, dass nur erforderliche Abhängigkeiten und Zugriffsrechte im Image/Container bereitgestellt werden 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>Liste der vordefinierten Docker-Images für Rückschlüsse 

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Python-Paketen zu vordefinierten Images](how-to-prebuilt-docker-images-inference-python-extensibility.md)
* [Verwenden eines vordefinierten Pakets als Basis für ein neues Dockerfile](how-to-extend-prebuilt-docker-image-inference.md)