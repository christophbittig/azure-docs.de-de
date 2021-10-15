---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 41f8fc2adefd29bb7cfda31e3dbaa83de599b154
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838209"
---
## <a name="install-the-sdk"></a>Installieren des SDKs

Wählen Sie Ihre Datei „build.gradle“ auf Projektebene aus, und stellen Sie sicher, dass Sie `mavenCentral()` zur Liste der Repositorys unter `buildscript` und `allprojects` hinzufügen.
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Fügen Sie dann in der Datei „build.gradle“ auf Modulebene die folgenden Zeilen zum Abschnitt „dependencies“ hinzu.

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0'
    ...
}
```

### <a name="initialize-the-required-objects"></a>Initialisieren der erforderlichen Objekte

Zum Erstellen einer `CallAgent`-Instanz müssen Sie die `createCallAgent`-Methode für eine `CallClient`-Instanz aufzurufen. Dadurch wird ein `CallAgent`-Instanzobjekt asynchron zurückgegeben.
Die `createCallAgent`-Methode verwendet `CommunicationUserCredential` als Argument, womit ein [Zugriffstoken](../../../../quickstarts/access-tokens.md) gekapselt wird.
Für den Zugriff auf `DeviceManager` muss zuerst eine CallAgent-Instanz erstellt werden. Anschließend können Sie mit der `CallClient.getDeviceManager`-Methode DeviceManager abrufen.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential).get();
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
```
Zum Festlegen eines Anzeigenamens für den Anrufer verwenden Sie diese alternative Methode:

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationTokenCredential tokenCredential = new CommunicationTokenCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgentOptions callAgentOptions = new CallAgentOptions();
callAgentOptions.setDisplayName("Alice Bob");
DeviceManager deviceManager = callClient.getDeviceManager(appContext).get();
CallAgent callAgent = callClient.createCallAgent(appContext, tokenCredential, callAgentOptions).get();
```