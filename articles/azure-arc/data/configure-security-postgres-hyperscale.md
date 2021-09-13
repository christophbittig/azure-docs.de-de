---
title: Konfigurieren der Sicherheit für Azure Arc-fähige PostgreSQL Hyperscale-Servergruppen
description: Konfigurieren der Sicherheit für Azure Arc-fähige PostgreSQL Hyperscale-Servergruppen
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 8841c3abae51de0cfcd1391940f9232c4585c02f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347005"
---
# <a name="configure-security-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Konfigurieren der Sicherheit für Azure Arc-fähige PostgreSQL Hyperscale-Servergruppen

In dieser Dokumentation werden verschiedene Aspekte der Sicherheit Ihrer Servergruppe beschrieben:
- Verschlüsselung ruhender Daten
- Benutzerverwaltung
   - Allgemeine Perspektiven
   - Ändern des Kennworts des _postgres_-Administrators
- Audit

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten
Sie können die Verschlüsselung ruhender Daten implementieren, indem Sie entweder die Datenträger verschlüsseln, auf denen Sie Ihre Datenbanken speichern, und/oder indem Sie Datenbankfunktionen zum Verschlüsseln der Daten verwenden, die Sie einfügen oder aktualisieren.

### <a name="hardware-linux-host-volume-encryption"></a>Hardware: Verschlüsselung des Linux-Hostvolumes
Implementieren Sie die Systemdatenverschlüsselung, um jegliche Daten zu schützen, die sich auf den Datenträgern befinden, die von Ihren Azure Arc-fähigen Data Services verwendet werden. Weitere Informationen hierzu finden Sie hier:
- [Verschlüsselung ruhender Daten](https://wiki.archlinux.org/index.php/Data-at-rest_encryption) unter Linux im Allgemeinen 
- Datenträgerverschlüsselung mit dem LUKS-Verschlüsselungsbefehl `cryptsetup` (Linux) (https://www.cyberciti.biz/security/howto-linux-hard-disk-encryption-with-luks-cryptsetup-command/) – Da Azure Arc-fähige Data Services in der physischen Infrastruktur ausgeführt werden, die Sie bereitstellen, sind Sie für die Sicherheit der Infrastruktur verantwortlich.

### <a name="software-use-the-postgresql-pgcrypto-extension-in-your-server-group"></a>Software: Verwenden der PostgreSQL-Erweiterung `pgcrypto` in Ihrer Servergruppe
Zusätzlich zur Verschlüsselung der Datenträger, die zum Hosten Ihres Azure Arc-Setups verwendet werden, können Sie Ihre Azure Arc-fähige PostgreSQL Hyperscale-Servergruppe so konfigurieren, dass Mechanismen zur Verfügung gestellt werden, die Ihre Anwendungen zum Verschlüsseln von Daten in Ihren Datenbanken verwenden können. Die `pgcrypto`-Erweiterung ist Teil der `contrib`-Erweiterungen von Postgres und in Ihrer Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppe verfügbar. Weitere Informationen zur `pgcrypto`-Erweiterung finden Sie [hier](https://www.postgresql.org/docs/current/pgcrypto.html).
Zusammenfassend können Sie die folgenden Befehle zum Aktivieren, Erstellen und Verwenden der Erweiterung verwenden:


#### <a name="create-the-pgcrypto-extension"></a>Erstellen der `pgcrypto`-Erweiterung
Stellen Sie mit dem Clienttool Ihrer Wahl eine Verbindung mit Ihrer Servergruppe her, und führen Sie die PostgreSQL-Standardabfrage aus:
```console
CREATE EXTENSION pgcrypto;
```

> Ausführliche Informationen zum Herstellen einer Verbindung finden Sie [hier](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).

#### <a name="verify-the-list-the-extensions-ready-to-use-in-your-server-group"></a>Überprüfen den Liste der einsatzbereiten Erweiterungen in Ihrer Servergruppe
Sie können überprüfen, ob die `pgcrypto`-Erweiterung einsatzbereit ist, indem Sie die verfügbaren Erweiterungen in Ihrer Servergruppe auflisten.
Stellen Sie mit dem Clienttool Ihrer Wahl eine Verbindung mit Ihrer Servergruppe her, und führen Sie die PostgreSQL-Standardabfrage aus:
```console
select * from pg_extension;
```
`pgcrypto` sollte angezeigt werden, wenn Sie die Erweiterung mit den oben angegebenen Befehlen aktiviert und erstellt haben.

#### <a name="use-the-pgcrypto-extension"></a>Verwenden der `pgcrypto`-Erweiterung
Sie können nun den Code Ihrer Anwendungen so anpassen, dass sie von `pgcrypto` bereitgestellte Funktionen verwenden:
- Allgemeine Hashfunktionen
- Kennworthashfunktionen
- PGP-Verschlüsselungsfunktionen
- Rohverschlüsselungsfunktionen
- Funktionen für zufällige Daten

(Beispielsweise zum Generieren von Hashwerten) Führen Sie den folgenden Befehl aus:

```console
Select crypt('Les sanglots longs des violons de l_automne', gen_salt('md5'));
```

Daraufhin wird der folgende Hash zurückgegeben:

```console
              crypt
------------------------------------
 $1$/9ACBYOV$z52PAGjQ5WTU9xvEECBNv/   
```

Alternativ gibt folgendes Beispiel:

```console
select hmac('Les sanglots longs des violons de l_automne', 'md5', 'sha256');
```

den folgenden Hash zurück:

```console
                                hmac
--------------------------------------------------------------------
 \xd4e4790b69d2cc8dbce3385ee63272bc7760f1603640bb211a7b864e695570c5
```

Alternativ können Sie verschlüsselte Daten beispielsweise wie ein Kennwort speichern:

Angenommen, meine Anwendungen speichert Geheimnisse in der folgenden Tabelle:

```console
create table mysecrets(USERid int, USERname char(255), USERpassword char(512));
```

und ich verschlüssele das Kennwort beim Erstellen eines Benutzers:

```console
insert into mysecrets values (1, 'Me', crypt('MySecretPasswrod', gen_salt('md5')));
```

Jetzt sehe ich, dass mein Kennwort verschlüsselt ist:

```console
select * from mysecrets;
```

Ausgabe:

- USERid: 1
- USERname: Bei mir selbst
- USERpassword: $1$Uc7jzZOp$NTfcGo7F10zGOkXOwjHy31

Wenn ich eine Verbindung mit meiner Anwendung herstelle und ein Kennwort übergebe, wird in der Tabelle `mysecrets` gesucht und der Name des Benutzers zurückgegeben, sofern eine Übereinstimmung zwischen dem für die Anwendung angegebenen Kennwort und den in der Tabelle gespeicherten Kennwörtern vorliegt. Zum Beispiel:

- Ich übergebe das falsche Kennwort:
   ```console
   select USERname from mysecrets where (USERpassword = crypt('WrongPassword', USERpassword));
   ```

   Ausgabe 

   ```returns
    USERname
   ---------
   (0 rows)
   ```
- Ich übergebe das richtige Kennwort:

   ```console
   select USERname from mysecrets where (USERpassword = crypt('MySecretPasswrod', USERpassword));
   ``` 

   Ausgabe:

   ```output
    USERname
   ---------
   Me
   (1 row)
   ```

In diesem kleinen Beispiel wird veranschaulicht, dass Sie ruhende Daten (Speichern verschlüsselter Daten) in Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppen mithilfe der Postgres-Erweiterung `pgcrypto` verschlüsseln und Ihre Anwendungen die von `pgcrypto` bereitgestellten Funktionen zum Bearbeiten dieser verschlüsselten Daten verwenden können.

## <a name="user-management"></a>Benutzerverwaltung
### <a name="general-perspectives"></a>Allgemeine Perspektiven
Sie können die Postgres-Standardvorgehensweise zum Erstellen von Benutzern und Rollen verwenden. Wenn Sie dies jedoch tun, stehen diese Artefakte nur der Coordinator-Rolle zur Verfügung. Während der Vorschau können diese Benutzer/Rollen noch nicht auf Daten zugreifen, die außerhalb des Koordinatorknotens und auf den Workerknoten Ihrer Servergruppe verteilt sind. Der Grund hierfür ist, dass die Benutzerdefinition während der Vorschau nicht auf die Workerknoten repliziert wird.

### <a name="change-the-password-of-the-_postgres_-administrative-user"></a>Ändern des Kennworts des _postgres_-Administrators
Azure Arc-fähige PostgreSQL Hyperscale-Lösungen umfassen den Postgres-Standardadministrator _postgres_, für den Sie das Kennwort festlegen, wenn Sie Ihre Servergruppe erstellen.
Das allgemeine Format für den Befehl zum Ändern des Kennworts lautet wie folgt:
```azurecli
az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
```

Dabei ist `--admin-password` ein boolescher Wert, der sich auf das Vorhandensein eines Werts in der Umgebungsvariablen „AZDATA_PASSWORD“ vom Typ **Sitzung** bezieht.
Wenn die Umgebungsvariable „AZDATA_PASSWORD“ vom Typ **Sitzung** vorhanden ist und einen Wert besitzt, wird durch Ausführen des obigen Befehls das Kennwort des Postgres-Benutzers auf den Wert dieser Umgebungsvariablen festgelegt.

Wenn die Umgebungsvariable „AZDATA_PASSWORD“ vom Typ **Sitzung** vorhanden ist, aber keinen Wert besitzt, oder die Umgebungsvariable „AZDATA_PASSWORD“ vom Typ **Sitzung** nicht vorhanden ist, wird der Benutzer durch Ausführen des obigen Befehls zur interaktiven Eingabe eines Kennworts aufgefordert.

#### <a name="change-the-password-of-the-postgres-administrative-user-in-an-interactive-way"></a>Interaktives Ändern des Kennworts des Postgres-Administrators

1. Löschen Sie die Umgebungsvariable „AZDATA_PASSWORD“ vom Typ **Sitzung** oder ihren Wert.
2. Führen Sie den folgenden Befehl aus:

   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   Beispiel:
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   Sie werden aufgefordert, das Kennwort einzugeben und zu bestätigen:
   ```console
   Postgres Server password:
   Confirm Postgres Server password:
   ```
   Während der Kennwortaktualisierung wird in der Ausgabe des Befehls Folgendes angezeigt:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```
   
#### <a name="change-the-password-of-the-postgres-administrative-user-using-the-azdata_password-session-environment-variable"></a>Ändern des Kennworts des Postgres-Administrators mithilfe der Umgebungsvariablen „AZDATA_PASSWORD“ vom Typ **Sitzung**:
1. Legen Sie den Wert der Umgebungsvariablen „AZDATA_PASSWORD“ vom Typ **Sitzung** auf das gewünschte Kennwort fest.
2. Führen Sie den Befehl aus:
   ```azurecli
   az postgres arc-server edit --name <server group name> --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   Beispiel:
   ```azurecli
   az postgres arc-server edit -n postgres01 --admin-password --k8s-namespace <namespace> --use-k8s
   ```
   
   Während der Kennwortaktualisierung wird in der Ausgabe des Befehls Folgendes angezeigt:
   ```console
   Updating password
   Updating postgres01 in namespace `arc`
   postgres01 is Ready
   ```

> [!NOTE]
> Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Umgebungsvariable der AZDATA_PASSWORD-Sitzung vorhanden ist und welchen Wert sie ggf. hat:
> - Auf einem Linux-Client:
> ```console
> printenv AZDATA_PASSWORD
> ```
>
> - Auf einem Windows-Client mit PowerShell:
> ```console
> echo $env:AZDATA_PASSWORD
> ```

## <a name="audit"></a>Audit

Konfigurieren Sie für Überwachungsszenarien Ihre Servergruppe so, dass sie die Postgres-Erweiterungen vom Typ `pgaudit` verwendet. Ausführlichere Informationen zu `pgaudit` finden Sie im [GitHub-Projekt `pgAudit`](https://github.com/pgaudit/pgaudit/blob/master/README.md). Informationen zum Aktivieren der Erweiterung `pgaudit` in Ihrer Servergruppe finden Sie unter [Verwenden von PostgreSQL-Erweiterungen in Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppen](using-extensions-in-postgresql-hyperscale-server-group.md).



## <a name="next-steps"></a>Nächste Schritte
- [Erweiterung `pgcrypto`](https://www.postgresql.org/docs/current/pgcrypto.html)
- [Verwenden von PostgreSQL-Erweiterungen in Azure Arc-fähigen PostgreSQL Hyperscale-Servergruppen](using-extensions-in-postgresql-hyperscale-server-group.md)

