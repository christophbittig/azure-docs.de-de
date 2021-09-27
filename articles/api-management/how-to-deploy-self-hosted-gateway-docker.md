---
title: Bereitstellen eines selbstgehosteten Gateways in Docker
description: Erfahren Sie, wie Sie eine selbstgehostete Gatewaykomponente von Azure API Management für Docker bereitstellen.
author: dlepow
manager: gwallace
ms.service: api-management
ms.topic: article
ms.date: 04/19/2021
ms.author: danlep
ms.openlocfilehash: 3ef8e0316b6df0b95f2163b6df8ae139ebb8fe6b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128580944"
---
# <a name="deploy-an-azure-api-management-self-hosted-gateway-to-docker"></a>Bereitstellen eines selbstgehosteten Azure API Management-Gateways für Docker

In diesem Artikel werden die Schritte für die Bereitstellung einer selbstgehosteten Gatewaykomponente von Azure API Management für eine Docker-Umgebung beschrieben.

> [!NOTE]
> Das Hosten eines selbstgehosteten Gateways in Docker eignet sich am besten für Anwendungsfälle, die sich auf die Auswertung und Entwicklung beziehen. Kubernetes wird für den Einsatz in der Produktionsumgebung empfohlen. Informationen zur Bereitstellung eines selbstgehosteten Gateways für Kubernetes finden Sie in [diesem](how-to-deploy-self-hosted-gateway-kubernetes.md) Dokument.

## <a name="prerequisites"></a>Voraussetzungen

- Absolvieren Sie den folgende Schnellstart: [Erstellen einer Azure API Management-Instanz](get-started-create-service-instance.md)
- Erstellen Sie eine Docker-Umgebung. [Docker für Desktop](https://www.docker.com/products/docker-desktop) ist eine gute Option für Entwicklungs- und Evaluierungszwecke. Weitere Informationen zu allen Docker-Editionen, ihren Features sowie eine umfassende Dokumentation zu Docker selbst finden Sie unter [Docker-Dokumentation](https://docs.docker.com).
- [Bereitstellen einer Gatewayressource in Ihrer API Management-Instanz](api-management-howto-provision-self-hosted-gateway.md)

> [!NOTE]
> Das selbstgehostete Gateway ist als x86-64 Linux-basierter Docker-Container gepackt.

## <a name="deploy-the-self-hosted-gateway-to-docker"></a>Bereitstellen des selbstgehosteten Gateways für Docker

1. Wählen Sie **Gateways** unter **Bereitstellung und Infrastruktur** aus.
2. Wählen Sie die Gatewayressource aus, die Sie bereitstellen möchten.
3. Wählen Sie **Bereitstellung** aus.
4. Beachten Sie, dass im Textfeld **Token** automatisch für Sie ein Zugriffstoken mit den Standardwerten **Ablauf** und **Geheimer Schlüssel** generiert wurde. Wählen Sie bei Bedarf die gewünschten Werte in einem oder beiden Steuerelementen aus, um ein neues Token zu generieren.
5. Stellen Sie sicher, dass **Docker** unter **Bereitstellungsskripts** ausgewählt ist.
6. Wählen Sie den Link zur Datei **env.conf** neben **Umgebung** aus, um die Datei herunterzuladen.
7. Wählen Sie das Symbol **Kopieren** rechts neben dem Textfeld **Ausführen** aus, um den Docker-Befehl in die Zwischenablage zu kopieren.
8. Fügen Sie den Befehl in das Terminalfenster (oder Befehlsfenster) ein. Passen Sie die Portzuordnungen und den Containernamen nach Bedarf an. Beachten Sie, dass der Befehl annimmt, dass die heruntergeladene Umgebungsdatei im aktuellen Verzeichnis vorhanden ist.

   ```
   docker run -d -p 80:8080 -p 443:8081 --name <gateway-name> --env-file env.conf mcr.microsoft.com/azure-api-management/gateway:<tag>
   ```

9. Führen Sie den Befehl aus. Der Befehl weist Ihre Docker-Umgebung an, den Container auszuführen. Dabei wird das aus Microsoft Container Registry heruntergeladene [Containerimage](https://aka.ms/apim/sputnik/dhub) verwendet, und die HTTP-Ports (8080) und HTTPS-Ports (8081) des Containers werden den Ports 80 und 443 auf dem Host zugeordnet.
10. Führen Sie den folgenden Befehl aus, um zu überprüfen, ob der Gatewaycontainer ausgeführt wird:
    ```console
    docker ps
    CONTAINER ID        IMAGE                                                 COMMAND                  CREATED             STATUS              PORTS                                         NAMES
    895ef0ecf13b        mcr.microsoft.com/azure-api-management/gateway:latest   "/bin/sh -c 'dotnet …"   5 seconds ago       Up 3 seconds        0.0.0.0:80->8080/tcp, 0.0.0.0:443->8081/tcp   my-gateway
    ```
10. Kehren Sie zum Azure-Portal zurück, klicken Sie auf **Übersicht** und bestätigen Sie, dass der selbstgehostete Gatewaycontainer, den Sie gerade bereitgestellt haben, einen fehlerfreien Status meldet.

    ![Gatewaystatus](media/how-to-deploy-self-hosted-gateway-docker/status.png)

> [!TIP]
> Verwenden Sie den Befehl `console docker container logs <gateway-name>`, um eine Momentaufnahme des Protokolls des selbstgehosteten Gateways anzuzeigen.
>
> Verwenden Sie den Befehl `docker container logs --help`, um alle Optionen für die Protokollanzeige anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum selbstgehosteten Gateway finden Sie unter [Azure API Management: Übersicht über das selbstgehostete Gateway](self-hosted-gateway-overview.md).
* [Konfigurieren eines benutzerdefinierten Domänennamens für das selbstgehostete Gateway](api-management-howto-configure-custom-domain-gateway.md)
