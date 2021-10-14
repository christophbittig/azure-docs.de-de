---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: df7a63c57674417bbee5bf04a154cf64bcdc474f
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910550"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

Mit unserem Android SDK können Sie die meisten Objekte und Sammlungen abonnieren, um bei Änderungen von Werten benachrichtigt zu werden.

## <a name="properties"></a>Eigenschaften
So abonnieren Sie `property changed`-Ereignisse

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```
Binden Sie bei der Verwendung von Ereignislistenern, die innerhalb derselben Klasse definiert sind, den Listener an eine Variable. Übergeben Sie die Variable als Argument, um Listenermethoden hinzuzufügen und zu entfernen.

Wenn Sie versuchen, den Listener direkt als Argument zu übergeben, geht der Verweis auf diesen Listener verloren. Java erstellt neue Instanzen dieser Listener und verweist nicht auf zuvor erstellte Instanzen. Sie werden weiterhin ordnungsgemäß ausgelöst, können aber nicht entfernt werden, da kein Verweis mehr darauf vorhanden ist.


## <a name="collections"></a>Auflistungen
So abonnieren Sie `collection updated`-Ereignisse

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
