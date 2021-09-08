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
ms.openlocfilehash: ab38a17f96bf10d5ae6745ede5c89c90eb47fda7
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/07/2021
ms.locfileid: "113432061"
---
> [!WARNING]
> Versuchen Sie nicht, Token für eine API zu überprüfen oder zu lesen, die Sie nicht besitzen, einschließlich der Token in Ihrem Code in diesem Beispiel.  Token für Microsoft-Dienste können ein spezielles Format verwenden, das nicht als JWT überprüft und auch für Consumerbenutzer (Microsoft-Konto) verschlüsselt werden kann. Das Lesen von Token ist zwar ein nützliches Debug- und Lerntool, aber integrieren Sie weder Abhängigkeiten davon in Ihren Code noch setzen Sie Einzelheiten über Token voraus, die nicht für eine von Ihnen kontrollierte API gelten.
