---
title: Verwenden von Azure Cache for Redis mit Rust
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Rust mit Azure Cache for Redis interagieren.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 23e1bdc895da7ba9941901832d7fc89cd9c6196a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592620"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>Schnellstart: Verwenden von Azure Cache for Redis mit Rust

In diesem Artikel erfahren Sie, wie Sie die Programmiersprache [Rust](https://www.rust-lang.org/) für die Interaktion mit [Azure Cache for Redis](./cache-overview.md) verwenden. Außerdem erfahren Sie mehr über häufig verwendete Redis-Datenstrukturen: 

* [String](https://redis.io/topics/data-types-intro#redis-strings) 
* [Hash](https://redis.io/topics/data-types-intro#redis-hashes) 
* [Liste](https://redis.io/topics/data-types-intro#redis-lists) 

In diesem Beispiel verwenden Sie die Bibliothek [redis-rs](https://github.com/mitsuhiko/redis-rs) für Redis. Dieser Client macht sowohl High-Level- als auch Low-Level-APIs verfügbar, und Sie werden beide Varianten in Aktion sehen.

## <a name="skip-to-the-code-on-github"></a>Überspringen und mit dem Code auf GitHub fortfahren

Wenn Sie direkt mit dem Code fortfahren möchten, finden Sie im [Rust-Schnellstart](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/) auf GitHub weitere Informationen.

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install) (ab Version 1.39)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Erstellen einer Azure Cache for Redis-Instanz
[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>Überprüfen des Codes (optional)

Falls Sie erfahren möchten, wie der Code funktioniert, können Sie sich die folgenden Codeausschnitte ansehen. Andernfalls können Sie mit [Ausführen der Anwendung](#run-the-application) fortfahren.

Die Funktion `connect` wird verwendet, um eine Verbindung mit Azure Cache for Redis herzustellen. Der Hostname und das Kennwort (Zugriffsschlüssel) müssen mithilfe der Umgebungsvariablen `REDIS_HOSTNAME` und `REDIS_PASSWORD` übergeben werden. Die Verbindungs-URL hat das Format `rediss://<username>:<password>@<hostname>`. Von Azure Cache for Redis werden nur sichere Verbindungen mit [TLS 1.2 als erforderliche Mindestversion](cache-remove-tls-10-11.md) akzeptiert.

Beim Aufruf von [redis::Client::open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open) wird eine grundlegende Validierung durchgeführt, mit [get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection) wird die Verbindung tatsächlich gestartet. Wenn bei der Konnektivität ein Fehler auftritt, wird das Programm angehalten. Eine mögliche Ursache für Konnektivitätsprobleme kann z. B. ein falsches Kennwort sein.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

Die Funktion `basics` umfasst die Befehle [SET](https://redis.io/commands/set), [GET](https://redis.io/commands/get) und [INCR](https://redis.io/commands/incr). 

Die Low-Level-API wird für `SET` und `GET`verwendet, um den Wert für einen Schlüssel mit dem Namen `foo` festzulegen und abzurufen. 

Der Befehl `INCRBY` wird unter Verwendung einer High-Level-API ausgeführt. [INCR](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr) erhöht also den Wert eines Schlüssels (mit dem Namen `counter`) um `2`, und danach wird [get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get) aufgerufen, um ihn abzurufen.

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

Im folgenden Codeausschnitt wird die Funktion einer Redis-Datenstruktur vom Typ `HASH` veranschaulicht. [HSET](https://redis.io/commands/hset) wird mithilfe der Low-Level-API aufgerufen, um Informationen (`name`, `version`, `repo`) zu Redis-Treibern (Clients) zu speichern. So werden beispielsweise Details für den Rust-Treiber (aus diesem Beispielcode) in Form eines Elements vom Typ [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) erfasst und dann an die Low-Level-API übergeben. Zum Abrufen wird dann [HGETALL](https://redis.io/commands/hgetall) verwendet.

`HSET` kann mithilfe von [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple) auch unter Verwendung einer High-Level-API ausgeführt werden. In diesem Fall wird ein Array von Tupeln akzeptiert. Anschließend wird [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget) ausgeführt, um den Wert für ein einzelnes Attribut abzurufen (in diesem Fall: `repo`).

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

In der folgenden Funktion wird die Verwendung einer Datenstruktur vom Typ `LIST` veranschaulicht. [LPUSH](https://redis.io/commands/lpush) wird mit der Low-Level-API ausgeführt, um der Liste einen Eintrag hinzuzufügen. Zum Abrufen aus der Liste wird die High-Level-Methode [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) verwendet. Anschließend werden der Liste mithilfe der Methode [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) einige Einträge hinzugefügt, die dann mithilfe der Low-Level-Methode [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) abgerufen werden.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

Hier sehen Sie einige der Vorgänge vom Typ `SET`. Die Methode [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd) (High-Level-API) wird verwendet, um einem Element vom Typ `SET` mit dem Namen `users` einige Einträge hinzuzufügen. Danach wird [SISMEMBER](https://redis.io/commands/hset) (Low-Level-API) ausgeführt, um zu überprüfen, ob `user1` vorhanden ist. Abschließend wird [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers) verwendet, um alle festgelegten Einträge in Form eines Vektors ([Vec\<String\>](https://doc.rust-lang.org/std/vec/struct.Vec.html)) abzurufen und zu durchlaufen.

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

Die im Anschluss verwendete Funktion `sorted_set` dient zur Veranschaulichung der Datenstruktur „Sortierte Gruppe“. [ZADD](https://redis.io/commands/zadd) wird mit der Low-Level-API aufgerufen, um eine zufällige ganzzahlige Punktzahl für einen Spieler (`player-1`) hinzuzufügen. Als Nächstes werden mithilfe der Methode [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) (High-Level-API) weitere Spieler (`player-2` bis `player-5`) und deren jeweiliges (nach dem Zufallsprinzip generiertes) Ergebnis hinzugefügt. Die Anzahl von Einträgen in der sortierten Gruppe wird mithilfe von [ZCARD](https://redis.io/commands/zcard) bestimmt. Dieser Wert wird als Grenzwert für den (mit der Low-Level-API aufgerufenen) Befehl [ZRANGE](https://redis.io/commands/zrange) verwendet, um die Spieler mit zugehöriger Punktzahl in aufsteigender Reihenfolge aufzulisten.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Beginnen Sie mit dem Klonen der Anwendung über GitHub.

1. Öffnen Sie eine Eingabeaufforderung, und erstellen Sie einen Ordner namens `git-samples`.

    ```bash
    md "C:\git-samples"
    ```

1. Öffnen Sie ein Git-Terminalfenster, z.B. git bash. Verwenden Sie `cd`, um in den neuen Ordner zu wechseln, in dem Sie die Beispiel-App klonen.

    ```bash
    cd "C:\git-samples"
    ```

1. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>Ausführen der Anwendung

Die Anwendung akzeptiert Konnektivitäts- und Anmeldeinformationen in Form von Umgebungsvariablen. 

1. Rufen Sie **Hostname** und **Zugriffsschlüssel** (über Zugriffsschlüssel verfügbar) für die Azure Cache for Redis-Instanz im [Azure-Portal](https://portal.azure.com/) ab.

1. Legen Sie hierfür die jeweiligen Umgebungsvariablen fest:

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. Wechseln Sie im Terminalfenster zum richtigen Ordner. Beispiel:

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. Führen Sie im Terminal den folgenden Befehl aus, um die Anwendung zu starten:

    ```shell
    cargo run
    ```
    
    Die folgende Ausgabe wird angezeigt:
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    Wenn Sie eine bestimmte Funktion ausführen möchten, kommentieren Sie andere Funktionen in der Funktion `main` aus:
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Sie können die Ressourcengruppe und die Ressourcen löschen, wenn Sie damit fertig sind. Indem Sie die Ressourcen und Elemente löschen, die Sie in dieser Schnellstartanleitung erstellt haben, vermeiden Sie, dass Ihnen diese Ressourcen in Rechnung gestellt werden.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden also dauerhaft gelöscht. Wenn Sie Ihre Azure Cache for Redis-Instanz in einer vorhandenen Ressourcengruppe erstellt haben, die Sie beibehalten möchten, können Sie einfach den Cache löschen, indem Sie auf der Seite **Übersicht** des Caches **Löschen** auswählen. 

So löschen Sie die Ressourcengruppe und die Azure Cache for Redis-Instanz

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Ressourcengruppen**, und wählen Sie die Option aus.
1. Geben Sie im Textfeld **Nach Name filtern** den Namen der Ressourcengruppe ein, die Ihre Cache-Instanz enthält. Wählen Sie sie dann in den Suchergebnissen aus. 
1. Wählen Sie auf der Seite für die Ressourcengruppe die Option **Ressourcengruppe löschen** aus.
1. Geben Sie den Ressourcengruppennamen ein, und wählen Sie dann **Löschen** aus.
   
   ![Löschen der Ressourcengruppe für Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie den Rust-Treiber für Redis verwenden, um eine Verbindung herzustellen und Vorgänge in Azure Cache for Redis auszuführen.

> [!div class="nextstepaction"]
> [Erstellen einer einfachen ASP.NET-Web-App, die eine Azure Cache for Redis-Instanz verwendet](./cache-web-app-howto.md)
