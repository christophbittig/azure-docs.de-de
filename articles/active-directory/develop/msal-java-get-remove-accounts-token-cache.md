---
title: Abrufen und Entfernen von Konten aus dem Tokencache (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie den Tokencache mit der Microsoft Authentication Library für Java löschen anzeigen und Konten daraus entfernen können.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: marsma
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 8ea51bfd356e7479f421a6db804ec94bf656284a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612527"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Aufrufen und Entfernen von Konten aus dem Tokencache mithilfe von MSAL für Java

MSAL für Java stellt standardmäßig einen Tokencache im Arbeitsspeicher bereit. Der In-Memory-Tokencache bleibt für die Lebensdauer der Anwendungsinstanz gültig.

## <a name="see-which-accounts-are-in-the-cache"></a>Anzeigen, welche Konten sich im Cache befinden

Sie können überprüfen, welche Konten sich im Cache befinden, indem Sie `PublicClientApplication.getAccounts()` aufrufen, wie im folgenden Beispiel gezeigt:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Entfernen von Konten aus dem Cache

Um ein Konto aus dem Cache zu entfernen, suchen Sie das Konto, das entfernt werden muss, und rufen Sie dann `PublicClientApplication.removeAccount()` auf, wie im folgenden Beispiel gezeigt:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Weitere Informationen

Wenn Sie MSAL für Java verwenden, informieren Sie sich über [Benutzerdefinierte Tokencache-Serialisierung in MSAL für Java](msal-java-token-cache-serialization.md).
