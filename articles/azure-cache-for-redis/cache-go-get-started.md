---
title: Verwenden von Azure Cache for Redis mit Go
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Python-App erstellen, für die Azure Cache for Redis verwendet wird.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: ab7bd55dd7b66d613f99b4d54a8fdf4445542545
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591822"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>Schnellstart: Verwenden von Azure Cache for Redis mit Go

In diesem Artikel wird beschrieben, wie Sie eine REST-API in Go erstellen, mit der Benutzerinformationen basierend auf einer [HASH](https://redis.io/topics/data-types-intro#redis-hashes)-Datenstruktur in [Azure Cache for Redis](./cache-overview.md) gespeichert und abgerufen werden.

## <a name="skip-to-the-code-on-github"></a>Überspringen und mit dem Code auf GitHub fortfahren

Wenn Sie direkt mit dem Code fortfahren möchten, finden Sie im [Go-Schnellstart](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/) auf GitHub weitere Informationen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install) (Version 1.13 oder höher empfohlen)
- [Git-Client](https://git-scm.com/downloads)
- HTTP-Client, z. B. [curl](https://curl.se/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Erstellen einer Azure Cache for Redis-Instanz

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Überprüfen des Codes (optional)

Falls Sie erfahren möchten, wie der Code funktioniert, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Ausführen der Anwendung](#run-the-application) fortfahren.

Die Open Source-Bibliothek [go-redis](https://github.com/go-redis/redis) wird für die Interaktion mit Azure Cache for Redis genutzt.

Mit der Funktion `main` werden zunächst der Hostname und das Kennwort (Zugriffsschlüssel) für die Azure Cache for Redis-Instanz gelesen.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

Anschließend stellen wir eine Verbindung mit Azure Cache for Redis her. Wir verwenden [tls.Config](https://golang.org/pkg/crypto/tls/#Config). Von Azure Cache for Redis werden nur sichere Verbindungen mit [TLS 1.2 als mindestens erforderliche Version](cache-remove-tls-10-11.md) akzeptiert.

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

Wenn die Verbindungsherstellung erfolgreich ist, werden [HTTP-Handler](https://golang.org/pkg/net/http/#HandleFunc) für die Verarbeitung der `POST`- und `GET`-Vorgänge konfiguriert, und der HTTP-Server wird gestartet.

> [!NOTE]
> Die [gorilla mux-Bibliothek](https://github.com/gorilla/mux) wird für das Routing verwendet (auch wenn dies nicht unbedingt erforderlich ist und für diese Beispielanwendung auch die Standardbibliothek ausreichen würde).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

Mit der `userHandler`-Struktur wird ein [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client) gekapselt, der von den Methoden `createUser` und `getUser` genutzt wird. Der Code für diese Methoden ist hier nicht angegeben, um den Artikel kurz zu halten.

- `createUser`: Akzeptiert eine JSON-Nutzlast (mit Benutzerinformationen) und speichert sie als `HASH` in Azure Cache for Redis.
- `getUser`: Ruft Benutzerinformationen aus `HASH` ab oder gibt die HTTP-Antwort `404` zurück, falls nichts gefunden wird.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
```

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen Sie mit dem Klonen der Anwendung über GitHub.

1. Öffnen Sie eine Eingabeaufforderung, und erstellen Sie einen Ordner namens `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

1. Öffnen Sie ein Git-Terminalfenster, z.B. git bash. Verwenden Sie den Befehl `cd`, um in den neuen Ordner zu wechseln, in dem Sie die Beispiel-App klonen möchten.

    ```bash
    cd "C:\git-samples"
    ```

1. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

Die Anwendung akzeptiert Konnektivitäts- und Anmeldeinformationen in Form von Umgebungsvariablen.

1. Rufen Sie den **Hostnamen** und die **Zugriffsschlüssel** (über Zugriffsschlüssel verfügbar) für die Azure Cache for Redis-Instanz im [Azure-Portal](https://portal.azure.com/) ab.

1. Legen Sie hierfür die jeweiligen Umgebungsvariablen fest:

    ```console
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Wechseln Sie im Terminalfenster zum richtigen Ordner. Beispiel:

    ```console
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. Führen Sie im Terminal den folgenden Befehl aus, um die Anwendung zu starten:

    ```console
    go run main.go
    ```

Der HTTP-Server beginnt an Port `8080`.

## <a name="test-the-application"></a>Testen der Anwendung

1. Erstellen Sie einige Benutzereinträge. Im folgenden Beispiel wird curl verwendet:

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. Abrufen eines vorhandenen Benutzers mit seiner `id`:

    ```bash
    curl -i localhost:8080/users/1
    ```

    Die JSON-Antwort sollte in etwa wie folgt lauten:

    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. Wenn Sie versuchen, einen nicht vorhandenen Benutzer abzurufen, erhalten Sie ein „HTTP `404`“. Beispiel:

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Azure-Ressourcengruppe und die Ressourcen, die Sie in diesem Schnellstart erstellt haben, nicht mehr benötigen, löschen Sie sie, um weitere Kosten zu vermeiden.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden also dauerhaft gelöscht. Wenn Sie Ihre Azure Cache for Redis-Instanz in einer vorhandenen Ressourcengruppe erstellt haben, die Sie beibehalten möchten, können Sie einfach den Cache löschen, indem Sie auf der Seite **Übersicht** des Caches **Löschen** auswählen.

So löschen Sie die Ressourcengruppe und die Azure Cache for Redis-Instanz

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Ressourcengruppen**, und wählen Sie die Option aus.
1. Geben Sie im Textfeld **Nach Name filtern** den Namen der Ressourcengruppe mit Ihrer Cache-Instanz ein, und wählen Sie sie dann in den Suchergebnissen aus.
1. Wählen Sie auf der Seite für die Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Ressourcengruppennamen ein, und wählen Sie dann **Löschen** aus.

   ![Löschen der Ressourcengruppe für Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde beschrieben, wie Sie die ersten Schritte für Go mit Azure Cache for Redis ausführen. Sie haben eine einfache REST-API-basierte Anwendung konfiguriert und ausgeführt, um Benutzerinformationen zu erstellen und abzurufen, die auf einer `HASH`-Datenstruktur von Redis basieren.

> [!div class="nextstepaction"]
> [Erstellen einer einfachen ASP.NET-Web-App, die eine Azure Cache for Redis-Instanz verwendet](./cache-web-app-howto.md)
