---
title: 'Tutorial: Manuelles Konfigurieren von Azure Front Door für Azure Static Web Apps'
description: Erfahren Sie, wie Sie Azure Front Door für Azure Static Web Apps einrichten.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cshoe
ms.openlocfilehash: aa377c63f0282bb43e8f69088f7925d9ac5ffa0d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400684"
---
# <a name="tutorial-manually-configure-azure-front-door-for-azure-static-web-apps"></a>Tutorial: Manuelles Konfigurieren von Azure Front Door für Azure Static Web Apps

Erfahren Sie, wie Sie [Azure Front Door](../frontdoor/front-door-overview.md) als CDN für Ihre statische Web-App hinzufügen.  Azure Front Door ist ein skalierbarer und sicherer Einstiegspunkt für die schnelle Bereitstellung Ihrer Webanwendungen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen einer Azure Front Door-Instanz
> - Zuordnen von Azure Font Door zu Ihrer Azure Static Web Apps-Website

> [!NOTE]
> Für dieses Tutorial ist der Standard-Plan von Azure Static Web Apps erforderlich.

## <a name="copy-url"></a>Kopieren der URL

1. Navigieren Sie zum Azure-Portal.

1. Öffnen Sie die statische Web-App, auf die Sie Azure Front Door anwenden möchten.

1. Navigieren Sie zum Abschnitt *Übersicht*.

1. Kopieren Sie die *URL* für die spätere Verwendung in die Zwischenablage.

## <a name="add-azure-front-door"></a>Hinzufügen von Azure Front Door

1. Navigieren Sie zum Azure-Portal.

1. Wählen Sie **Ressource erstellen**.

1. Suchen Sie nach **Azure Front Door**.

1. Wählen Sie **Front Door** aus.

    Achten Sie darauf, nicht den Dienst mit der Bezeichnung *Front Door Standard/Premium* auszuwählen, da sich die Schritte für den Standard/Premium-Dienst von den in diesem Tutorial vorgestellten Schritten unterscheiden.

1. Klicken Sie auf **Erstellen**.

1. Geben Sie auf der Registerkarte *Grundeinstellungen* die folgenden Werte ein:

    | Einstellung | Wert |
    |---|---|
    | Subscription | Wählen Sie Ihr Azure-Abonnement. |
    | Resource group | Geben Sie einen Ressourcengruppennamen ein. Dieser Name ist häufig der gleiche Gruppenname, der von Ihrer statischen Web-App verwendet wird. |
    | Ressourcengruppenstandort | Wenn Sie eine neue Ressourcengruppe erstellen, geben Sie den nächstgelegenen Standort ein. |

    Klicken Sie auf **Weiter: Konfiguration >** .

1. Wählen Sie auf der Registerkarte *Konfiguration* das **Pluszeichen** neben *Front-Ends/Domänen* aus, und geben Sie den folgenden Wert ein:

    | Einstellung | Wert |
    |---|---|
    | Hostname | Geben Sie einen eindeutigen Namen für Ihren Front Door-Host ein. |

    Übernehmen Sie im restlichen Formular die Standardwerte, und wählen Sie **Hinzufügen** aus.

1. Wählen Sie neben *Back-End-Pools* das **Pluszeichen** aus, und geben Sie dann den folgenden Wert ein:

    | Einstellung | Wert |
    |---|---|
    | Name | Geben Sie **my-static-web-app-pool** ein. |

1. Wählen Sie **+ Back-End hinzufügen** aus, und geben Sie die folgenden Werte ein:

    | Einstellung | Wert |
    |---|---|
    | Back-End-Hosttyp | Wählen Sie **Benutzerdefinierter Host** aus. |
    | Back-End-Hostname | Geben Sie den Hostnamen Ihrer statischen Web-App ein. Stellen Sie sicher, dass Ihr Wert keinen nachgestellten Schrägstrich und kein Protokoll enthält. (Beispiel: `desert-rain-04056.azurestaticapps.net`)  |
    | Back-End-Hostheader | Geben Sie den Hostnamen Ihrer statischen Web-App ein. Stellen Sie sicher, dass Ihr Wert keinen nachgestellten Schrägstrich und kein Protokoll enthält. (Beispiel: `desert-rain-04056.azurestaticapps.net`) |

    Übernehmen Sie im restlichen Formular die Standardwerte, und wählen Sie **Hinzufügen** aus.

1. Wählen Sie **Hinzufügen**.

1. Wählen Sie neben *Routingregel* das **Pluszeichen** aus, und geben Sie dann den folgenden Wert ein:

    | Einstellung | Wert |
    |---|---|
    | Name | Geben Sie **my-routing-rule** ein. |

    Übernehmen Sie im restlichen Formular die Standardwerte, und wählen Sie **Hinzufügen** aus.

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Klicken Sie auf **Erstellen**.

    Der Erstellungsvorgang kann einige Minuten dauern.

1. Wählen Sie **Zu Ressource wechseln** aus.

1. Wählen Sie **Übersicht**.

1. Wählen Sie den Link mit der Bezeichnung *Front-End-Host* aus.

    Wenn Sie diesen Link auswählen, wird möglicherweise der Fehler 404 angezeigt, wenn die Website nicht vollständig propagiert ist. Anstatt die Seite zu aktualisieren, warten Sie einige Minuten. Wechseln Sie dann zum Fenster *Übersicht* zurück, und wählen Sie den Link mit der Bezeichnung *Front-End-Host* aus.

1. Kopieren Sie im Fenster *Übersicht* den Wert mit der Bezeichnung **Front Door-ID**, und fügen Sie ihn zur späteren Verwendung in eine Datei ein.

> [!IMPORTANT]
> Standardmäßig konfiguriert Azure Front Door [Integritätstests](../frontdoor/front-door-health-probes.md), die sich auf Ihre Datenverkehrsstatistik auswirken können. Sie können die Standardwerte für die [Integritätstests](../frontdoor/front-door-health-probes.md) ggf. anpassen.

## <a name="update-static-web-app-configuration"></a>Aktualisieren der Konfiguration statischer Web-Apps

Um die Integration mit Front Door abzuschließen, müssen Sie die Anwendungskonfigurationsdatei wie folgt anpassen:

- Beschränken des Datenverkehrs zu Ihrer Website auf Datenverkehr über Front Door
- Beschränken des Datenverkehrs zu Ihrer Website auf Ihre Front Door-Instanz
- Definieren der Domänen mit Zugriff auf Ihre Website

Öffnen Sie die Datei [staticwebapp.config.json](configuration.md) für Ihre Website, und nehmen Sie die folgenden Änderungen vor.

1. Beschränken Sie den Datenverkehr ausschließlich auf Front Door, indem Sie in der Konfigurationsdatei den folgenden Abschnitt hinzufügen:

    ```json
    "networking": {
      "allowedIpRanges": ["AzureFrontDoor.Backend"]
    }
    ```

1. Um festzulegen, welche Azure Front Door-Instanzen und welche Domänen auf Ihre Website zugreifen können, fügen Sie den Abschnitt `forwardingGateway` hinzu.

    ```json
    "forwardingGateway": {
      "requiredHeaders": {
        "X-Azure-FDID" : "<YOUR-FRONT-DOOR-ID>"
      },
      "allowedForwardedHosts": [
        "my-sitename.azurefd.net"
      ]
    }
    ```

    Konfigurieren Sie zunächst Ihre App so, dass nur Datenverkehr von Ihrer Front Door-Instanz zulässig ist. Front Door fügt automatisch jeder Back-End-Anforderung den Header `X-Azure-FDID` hinzu, der die ID Ihrer Front Door-Instanz enthält. Wenn Sie Ihre statische Web-App so konfigurieren, dass dieser Header erforderlich ist, wird damit der Datenverkehr ausschließlich auf Ihre Front Door-Instanz beschränkt. Fügen Sie im Abschnitt `forwardingGateway` Ihrer Konfigurationsdatei den Abschnitt `requiredHeaders` hinzu, und definieren Sie den Header `X-Azure-FDID`. Ersetzen Sie `<YOUR-FRONT-DOOR-ID>` durch die *Front Door-ID*, die Sie zuvor festgelegt haben.

    Fügen Sie als Nächstes den Azure Front Door-Hostnamen (nicht den Azure Static Web Apps-Hostnamen) in das `allowedForwardedHosts`-Array ein. Wenn Sie benutzerdefinierte Domänen in Ihrer Front Door-Instanz konfiguriert haben, fügen Sie sie ebenfalls dieser Liste hinzu.

    Ersetzen Sie in diesem Beispiel `my-sitename.azurefd.net` durch den Azure Front Door-Hostnamen für Ihre Website.

Mit dieser Konfiguration ist Ihre Website nicht mehr über den generierten Hostnamen `*.azurestaticapps.net` verfügbar, sondern ausschließlich über die Hostnamen, die in Ihrer Front Door-Instanz konfiguriert sind.

> [!NOTE]
> Wenn Sie Updates für vorhandene Dateien in Ihrer statischen Web-App bereitstellen, stellt Azure Front Door möglicherweise weiterhin ältere Versionen Ihrer Dateien bereit, bis deren [Gültigkeitsdauer](https://wikipedia.org/wiki/Time_to_live) abläuft. [Löschen Sie den Azure Front Door-Cache](../frontdoor/front-door-caching.md#cache-purge) für die betroffenen Pfade, um sicherzustellen, dass die neuesten Dateien bereitgestellt werden.

Nachdem Front Door nun Ihre Website verwaltet, müssen Sie das Azure Static Web Apps-Feature für benutzerdefinierte Domänen nicht mehr verwenden. Azure Front Door nutzt einen eigenen Prozess zum Hinzufügen benutzerdefinierter Domänen. Weitere Informationen finden Sie unter [Hinzufügen einer benutzerdefinierten Domäne zu Ihrer Azure Front Door Service-Konfiguration](../frontdoor/front-door-custom-domain.md). Wenn Sie in Front Door eine benutzerdefinierte Domäne hinzufügen, müssen Sie die Konfigurationsdatei Ihrer statischen Web-App so aktualisieren, dass sie in die Liste `allowedForwardedHosts` aufgenommen wird.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen einer API zu Azure Static Web Apps (Vorschauversion) mit Azure Functions](apis.md)
