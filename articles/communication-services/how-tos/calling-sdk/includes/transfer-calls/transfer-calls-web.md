---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0a3e3d80e5b669884d19d5eb60260a31b1836de3
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700134"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

> [!NOTE]
> Diese API wird als Vorschau für Entwickler bereitgestellt. Je nachdem, welches Feedback wir dazu erhalten, werden möglicherweise Änderungen vorgenommen. Verwenden Sie diese API nicht in einer Produktionsumgebung. Um diese API einzusetzen, verwenden Sie das Betarelease des ACS Calling Web SDK.

Die Anrufdurchstellung ist ein erweitertes Feature der zentralen `Call`-API. Sie müssen zunächst das API-Objekt der Durchstellungsfunktion abrufen:

```js
const callTransferApi = call.api(Features.Transfer);
```

Am Durchstellen von Anrufen sind drei Parteien beteiligt:

- *Durchsteller*: die Person, die die Durchstellungsanforderung auslöst.
- *Durchgestellter*: die Person, deren Anruf durchgestellt wird.
- *Durchstellungsziel*: die Person, an die der Anruf durchgestellt wird.

Beim Durchstellen werden folgende Schritt durchgeführt:

1. Es besteht bereits eine Anrufverbindung zwischen dem *Durchsteller* und dem *Durchgestellten*. Der *Durchsteller* beschließt, den Anruf vom *Durchgestellten* an das *Durchstellungsziel* zu übertragen.
1. Der *Durchsteller* ruft die `transfer`-API auf.
1. Der *Durchgestellte* entscheidet bei einem `transferRequested`-Ereignis mithilfe von `accept` oder `reject`, ob er die Durchstellungsanforderung an das *Durchstellungsziel* annimmt oder ablehnt.
1. Das *Durchstellungsziel* empfängt nur dann einen eingehenden Anruf, wenn der *Durchgestellte* die Durchstellungsanforderung akzeptiert.

Zum Durchstellen eines aktuellen Anrufs können Sie die `transfer`-API verwenden. `transfer` verwendet die optionalen `transferCallOptions`, mit denen Sie ein Flag `disableForwardingAndUnanswered` festlegen können:

- `disableForwardingAndUnanswered = false`: Wenn das *Durchstellungsziel* den durchgestellten Anruf nicht annimmt, werden die Einstellungen für Weiterleitung und Nichtannahme des *Durchstellungsziels* befolgt.
- `disableForwardingAndUnanswered = true`: Wenn das *Durchstellungsziel* den durchgestellten Anruf nicht annimmt, wird der Durchstellungsversuch beendet.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Die `transferRequested`-API erlaubt Ihnen das Abonnieren der Ereignisse `transfer` und `transferStateChanged`. Ein `transferRequested`-Ereignis stammt aus einer `call`-Instanz. Ein `transferStateChanged`-Ereignis sowie `state` und `error` des Durchstellungsvorgangs stammen aus einer `transfer`-Instanz.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Der *Durchgestellte* kann die vom *Durchsteller* ausgelöste Durchstellungsanforderung im Ereignis `transferRequested` über `accept()` oder `reject()` in `transferRequestedEventArgs` annehmen oder ablehnen. Sie können auf `targetParticipant`-Informationen und auf die Methoden `accept` und `reject` in `transferRequestedEventArgs` zugreifen.

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
    args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
    args.reject();
});
```