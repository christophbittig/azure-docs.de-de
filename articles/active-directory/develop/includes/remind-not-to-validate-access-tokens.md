---
title: Keine Überprüfung von Zugriffstoken für Microsoft Graph
description: Schließen Sie die Dateiwarnung ein, dass Zugriffstoken für Microsoft Graph als nicht transparent betrachtet und nie vom Kundencode überprüft werden sollten. Nur Microsoft Graph überprüft Microsoft Graph-Zugriffstoken.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: include
ms.date: 06/25/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: e3dad40f1e4c1e8c463217720a190fd0e961334f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909790"
---
> [!WARNING]
> Versuchen Sie nicht, Token für eine API zu überprüfen oder zu lesen, die Sie nicht besitzen, einschließlich der Token in Ihrem Code in diesem Beispiel. Token für Microsoft-Dienste können ein spezielles Format verwenden, das nicht als JWT überprüft und auch für Consumerbenutzer (Microsoft-Konto) verschlüsselt werden kann. Das Lesen von Token ist zwar ein nützliches Debug- und Lerntool, aber integrieren Sie weder Abhängigkeiten davon in Ihren Code noch setzen Sie Einzelheiten über Token voraus, die nicht für eine von Ihnen kontrollierte API gelten.
