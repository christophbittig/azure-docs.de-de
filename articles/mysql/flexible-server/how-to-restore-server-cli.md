---
title: Wiederherstellen – Azure Database for MySQL – Flexible Server mit Azure CLI
description: In diesem Artikel wird beschrieben, wie Sie Wiederherstellungsvorgänge in Azure Database for MySQL mit Azure CLI durchführen.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 46602d13441fd972f3d292d0bfd48b9fd2919231
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467984"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Zeitpunktwiederherstellung eines Azure Database for MySQL-Flexible Servers mit Azure CLI

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

In diesem Artikel wird Schritt für Schritt beschrieben, wie Sie Zeitpunktwiederherstellungen in einer Flexible Server-Instanz mithilfe von Sicherungen durchführen.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. 

    [!INCLUDE [flexible-server-free-trial-note](../includes/flexible-server-free-trial-note.md)]
- Installieren Sie die Azure CLI, oder upgraden Sie sie auf die neueste Version. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).
-  Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index#az_login) beim Azure-Konto an. Beachten Sie die Eigenschaft **id**, die auf die **Abonnement-ID** für Ihr Azure-Konto verweist.

    ```azurecli-interactive
    az login
    ````

- Wenn Sie über mehrere Abonnements verfügen, wählen Sie das entsprechende Abonnement aus, in dem Sie den Server mit dem ```az account set```-Befehl erstellen.
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Erstellen Sie einen MySQL Flexible Server, wenn Sie noch keinen mit dem ```az mysql flexible-server create```-Befehl erstellt haben.

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Wiederherstellen eines Servers aus einer Sicherung auf einem neuen Server

Sie können den folgenden Befehl ausführen, um einen Server in einer frühesten vorhandenen Sicherung wiederherzustellen.

**Verwendung**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Beispiel**: Stellen Sie einen Server aus dieser ```2021-03-03T13:10:00Z```-Sicherungsmomentaufnahme wieder her.

```azurecli
az mysql flexible-server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
Die Dauer der Wiederherstellung hängt von der Größe der auf dem Server gespeicherten Daten ab.

## <a name="perform-post-restore-tasks"></a>Durchführen der Aufgaben nach der Wiederherstellung
Nachdem die Wiederherstellung abgeschlossen ist, sollten Sie die folgenden Aufgaben durchführen, um Ihre Benutzer und Anwendungen wieder in den betriebsbereiten Zustand zu versetzen:

- Umleiten von Clients und Clientanwendungen an den neuen Server, wenn der neue Server den ursprünglichen Server ersetzen soll
- Stellen Sie sicher, dass geeignete VNET-Regeln vorhanden sind, damit Benutzer eine Verbindung herstellen können. Diese Regeln werden nicht vom ursprünglichen Server kopiert.
- Sicherstellen, dass geeignete Anmeldungen und Berechtigungen auf Datenbankebene vorhanden sind
- Konfigurieren von Warnungen nach Bedarf für den neuen Wiederherstellungsserver

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur [Geschäftskontinuität](concepts-business-continuity.md)

