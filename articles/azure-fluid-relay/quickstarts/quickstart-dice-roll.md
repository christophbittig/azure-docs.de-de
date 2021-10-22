---
title: 'Schnellstart: Würfelroller'
description: Schnelles Erstellen einer würfelrollenden App mit dem Azure Fluid Relay-Dienst
author: hickeys
ms.service: azure-fluid
ms.topic: quickstart
ms.date: 09/09/2021
ms.author: hickeys
ms.openlocfilehash: f3a25a47cc1bee143f562e936f7927f48d452b4c
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129662083"
---
# <a name="quickstart-dice-roller"></a>Schnellstart: Würfelroller

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

In diesem Schnellstart erfahren Sie, wie Sie eine Würfelroller-App erstellen, die den Azure Fluid Relay-Dienst verwendet. Der Schnellstart ist in zwei Teile unterteilt. Im ersten Teil erstellen wir die App selbst und führen sie auf einem lokalen Fluid-Server aus. Im zweiten Teil wird die App neu konfiguriert, sodass sie für den Azure Fluid Relay-Dienst anstatt auf dem lokalen Entwicklungsserver ausgeführt wird.

Den in diesem Schnellstart verwendeten Beispielcode finden Sie [hier](https://github.com/microsoft/FluidHelloWorld/tree/main-azure).

## <a name="set-up-your-development-environment"></a>Einrichten der Entwicklungsumgebung

Für diesen Schnellstart benötigen Sie ein Azure-Konto und einen [bereitgestellten Azure Fluid Relay-Dienst](../how-tos/provision-fluid-azure-portal.md). Wenn Sie noch kein Konto haben, können Sie [Azure kostenlos testen](https://azure.com/free).

Außerdem muss die folgende Software auf Ihrem Computer installiert sein:

- ein Code-Editor – wir empfehlen [Visual Studio Code](https://code.visualstudio.com/)
- [Git-Client](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/en/download), Version 12.17 oder höher

## <a name="getting-started-locally"></a>Erste Schritte in der lokalen Umgebung

Laden Sie zunächst die Beispiel-App von GitHub herunter. Öffnen Sie ein neues Befehlsfenster, und navigieren Sie zum Ordner, in den Sie den Code herunterladen möchten. Klonen Sie dann das Repository [FluidHelloWorld](https://github.com/microsoft/FluidHelloWorld) mithilfe von Git. Beim Klonen wird ein Unterordner mit dem Namen „FluidHelloWorld“ erstellt, in dem sich die Projektdateien befinden.

```cli
git clone -b main-azure https://github.com/microsoft/FluidHelloWorld.git
```

Navigieren Sie zum neu erstellten Ordner, installieren Sie Abhängigkeiten, und starten Sie die App.

```cli
cd FluidHelloWorld
npm install
...
npm start
```


Wenn Sie den Befehl `npm start` ausführen, geschehen zwei Dinge. Zunächst wird ein Fluid-Server in einem lokalen Prozess gestartet. Dieser Server ist nur für die Entwicklung vorgesehen. Sie führen später ein Upgrade auf einen in Azure gehosteten Produktionsserver durch. Zweitens wird eine neue Browserregisterkarte mit einer Seite geöffnet, die eine neue Instanz der Würfelroller-App enthält. Sie können neue Registerkarten mit der gleichen URL öffnen, um weitere Instanzen der Würfelroller-App zu erstellen. Jede Instanz der App ist standardmäßig für die Verwendung Ihres lokalen Fluid-Diensts konfiguriert. Klicken Sie in einer beliebigen Instanz der App auf die Schaltfläche **Rollen**, und beachten Sie, dass sich der Status des Würfels in jedem Client ändert.

## <a name="upgrading-to-azure-fluid-relay"></a>Upgrade auf Azure Fluid Relay

Damit Ihre App für den Azure Fluid Relay-Dienst ausgeführt wird, müssen Sie ihre Konfiguration aktualisieren, um eine Verbindung mit Ihrem Azure-Dienst anstelle Ihres lokalen Servers herzustellen.

### <a name="configure-and-create-an-azure-client"></a>Konfigurieren und Erstellen eines Azure-Clients

Zum Konfigurieren des Azure-Clients ersetzen Sie die Werte im `serviceConfig`-Objekt in `app.js` durch Ihre Azure Fluid Relay-Dienstkonfigurationswerte. Diese Werte finden Sie im Abschnitt „Zugriffsschlüssel“ der Fluid Relay-Ressource im Azure-Portal.

```javascript
const serviceConfig = {
    connection: {
        tenantId: LOCAL_MODE_TENANT_ID, // REPLACE WITH YOUR TENANT ID
        tokenProvider: new InsecureTokenProvider("" /* REPLACE WITH YOUR PRIMARY KEY */, { id: "userId" }),
        orderer: "http://localhost:7070", // REPLACE WITH YOUR ORDERER ENDPOINT
        storage: "http://localhost:7070", // REPLACE WITH YOUR STORAGE ENDPOINT
    }
};
```

> [!WARNING]
> Während der Entwicklung können Sie `InsecureTokenProvider` verwenden, um Authentifizierungstoken zu generieren und zu signieren, die der Azure Fluid Relay-Dienst akzeptiert. Wie der Name schon sagt, ist er jedoch unsicher und sollte in Produktionsumgebungen nicht verwendet werden. Während der Azure Fluid Relay-Ressourcenerstellung erhalten Sie einen geheimen Schlüssel, der zum Signieren sicherer Anforderungen verwendet werden kann. **Um sicherzustellen, dass dieses Geheimnis nicht zugänglich gemacht wird, sollte muss es durch eine andere Implementierung von „ITokenProvider“ ersetzt werden, die das Token von einem sicheren, vom Entwickler bereitgestellten Back-End-Dienst abruft, bevor es für die Produktion freigegeben wird.**

### <a name="build-and-run-the-client-only"></a>Erstellen und Ausführen nur des Clients

Da Sie Ihre Anwendung nun auf die Verwendung von Azure anstelle eines lokalen Servers verwiesen haben, müssen Sie den lokalen Fluid-Server nicht zusammen mit Ihrer Client-Anwendung starten. Sie können den Client mit diesem Befehl starten, ohne auch den Server zu starten. 

```bash
npm run start:client
```

🥳**Herzlichen Glückwunsch**🎉 Sie haben den ersten Schritt zur Erschließung der Welt der Fluid-Zusammenarbeit erfolgreich unternommen.
