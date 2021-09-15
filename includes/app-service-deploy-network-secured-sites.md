---
title: include file
description: Datei einfügen
services: app-service
author: jasonfreeberg
ms.service: app-service
ms.topic: include
ms.date: 08/27/2021
ms.author: jafreebe
ms.custom: include file
ms.openlocfilehash: a3030eedcc6b00457338f4a71c27965261280a80
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225462"
---
Abhängig von der Netzwerkkonfiguration Ihrer Web-Apps kann der direkte Zugriff von Ihrer lokalen Umgebung aus auf die Website blockiert werden. Um Ihren Code in diesem Szenario bereitzustellen, können Sie Ihre ZIP-Datei in einem Speichersystem veröffentlichen, das über die Web-App zugänglich ist. Anschließend können Sie die App auslösen, um die ZIP-Datei per *Pull* aus dem Speicherort abzurufen, statt sie per *Push* in die Web-App zu übertragen. Weitere Informationen zur Bereitstellung für im Netzwerk geschützte Web-Apps finden Sie in [diesem Artikel](https://azure.github.io/AppService/2021/03/01/deploying-to-network-secured-sites-2.html). 
