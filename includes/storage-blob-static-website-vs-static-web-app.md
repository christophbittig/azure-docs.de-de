---
title: include file
description: include file
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 11/04/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 74ee76424ccbbdcaaebb4652acbe42bf569ac86d
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132096948"
---
Für statische Websites gelten einige Einschränkungen. Wenn Sie beispielsweise Header konfigurieren möchten, müssen Sie Azure CDN (Azure Content Delivery Network) verwenden. Es gibt keine Möglichkeit, Header im Rahmen des Features für die statische Website selbst zu konfigurieren. Außerdem werden AuthN und AuthZ nicht unterstützt. 

Wenn diese Features für Ihr Szenario wichtig sind, sollten Sie die Verwendung von [Azure Static Web Apps](https://azure.microsoft.com/services/app-service/static/) in Erwägung ziehen. Dies ist eine hervorragende Alternative zu statischen Websites und auch geeignet, wenn Sie keinen Webserver zum Rendern von Inhalten benötigen. Sie können Header konfigurieren, und AuthN/AuthZ wird vollständig unterstützt. Azure Static Web Apps bietet außerdem einen vollständig verwalteten Continuous Integration- und Continuous Delivery-Workflow (CI/CD) von der GitHub-Quelle bis zur globalen Bereitstellung.
