---
title: Verwenden von Azure Active Directory – Azure Database for MySQL
description: Informieren Sie sich, wie Sie Azure Active Directory (Azure AD) für die Authentifizierung mit Azure Database for MySQL einrichten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 07/23/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d05b48432fd976bf7e5b8add01532aae361968ec
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130167181"
---
# <a name="use-azure-active-directory-for-authentication-with-mysql"></a>Verwenden von Azure Active Directory für die Authentifizierung mit MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Dieser Artikel führt Sie durch die Schritte zum Konfigurieren des Zugriffs auf Azure Active Directory mit Azure Database for MySQL sowie zum Herstellen einer Verbindung mit einem Azure AD-Token.

> [!IMPORTANT]
> Azure Active Directory-Authentifizierung ist nur für MySQL 5.7 und höher verfügbar.

## <a name="setting-the-azure-ad-admin-user"></a>Festlegen des Azure AD-Administratorbenutzers

Nur ein Azure AD-Administratorbenutzer kann Benutzer für Azure AD-basierte Authentifizierung erstellen/aktivieren. Führen Sie die folgenden Schritte aus, um einen Azure AD-Administratorbenutzer zu erstellen:

1. Wählen Sie im Azure-Portal die Instanz von Azure Database for MySQL aus, die Sie für Azure AD aktivieren möchten.
2. Wählen Sie unter „Einstellungen“ die Option „Active Directory-Administrator“ aus:

![Festlegen des Azure AD-Administrators][2]

3. Wählen Sie im Kundenmandanten einen gültigen Azure AD-Benutzer aus, der als Azure AD-Administrator fungieren soll.

> [!IMPORTANT]
> Beim Festlegen des Administrators wird dem Azure Database for MySQL-Server ein neuer Benutzer mit vollständigen Administratorberechtigungen hinzugefügt.

Es kann nur ein Azure AD-Administrator pro MySQL-Server erstellt werden, und die Auswahl eines anderen Administrators überschreibt den vorhandenen Azure AD-Administrator, der für den Server konfiguriert ist.

Nachdem Sie den Administrator konfiguriert haben, können Sie sich jetzt anmelden:

## <a name="connecting-to-azure-database-for-mysql-using-azure-ad"></a>Herstellen einer Verbindung mit Azure Database for MySQL mithilfe von Azure AD

Die folgende Abbildung bietet eine Übersicht über den Workflow bei der Verwendung von Azure AD-Authentifizierung mit Azure Database for MySQL:

![Authentifizierungsablauf][1]

Wir haben die Azure AD-Integration so konzipiert, dass sie mit gängigen MySQL-Tools wie der MySQL-CLI funktioniert, die nicht Azure AD-fähig sind und nur die Angabe von Benutzername und Kennwort beim Herstellen einer Verbindung mit MySQL unterstützen. Wir übergeben das Azure AD-Token als Kennwort, wie in der Abbildung oben gezeigt.

Zurzeit haben wir die folgenden Clients getestet:

- MySQLWorkbench 
- MySQL-CLI

Wir haben auch die gängigsten Anwendungstreiber getestet. Details finden Sie am Ende dieser Seite.

Dies sind die Schritte, die ein Benutzer oder eine Anwendung für die Authentifizierung mit Azure AD ausführen muss, die im Folgenden beschrieben werden:

### <a name="prerequisites"></a>Voraussetzungen

Sie können den Schritten in Azure Cloud Shell, auf einer Azure-VM oder auf Ihrem lokalen Computer folgen. Stellen Sie sicher, dass die [Azure CLI](/cli/azure/install-azure-cli) installiert ist.

### <a name="step-1-authenticate-with-azure-ad"></a>Schritt 1: Authentifizierung über Azure AD

Authentifizieren Sie sich zunächst über Azure AD mithilfe des Azure CLI-Tools. Dieser Schritt ist in Azure Cloud Shell nicht erforderlich.

```
az login
```

Mit dem Befehl wird ein Browserfenster auf der Seite „Azure AD-Authentifizierung“ gestartet. Sie müssen Ihre Azure AD-Benutzer-ID und das Kennwort angeben.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Schritt 2: Abrufen eines Azure AD-Zugriffstokens

Rufen Sie das Azure CLI-Tool auf, um ein Zugriffstoken für den über Azure AD authentifizierten Benutzer aus Schritt 1 für den Zugriff auf Azure Database for MySQL abzurufen.

Beispiel (für die öffentliche Cloud):

```azurecli-interactive
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```
Der oben genannte Ressourcenwert muss genau wie gezeigt angegeben werden. Für andere Clouds kann der Ressourcenwert wie folgt nachgeschlagen werden:

```azurecli-interactive
az cloud show
```

Für Version 2.0.71 oder höher der Azure CLI kann der Befehl in der folgenden, bequemeren Version für alle Clouds angegeben werden:

```azurecli-interactive
az account get-access-token --resource-type oss-rdbms
```
Mithilfe von PowerShell können Sie den folgenden Befehl verwenden, um ein Zugriffstoken abzurufen:

```azurepowershell-interactive
$accessToken = Get-AzAccessToken -ResourceUrl https://ossrdbms-aad.database.windows.net
$accessToken.Token | out-file C:\temp\MySQLAccessToken.txt
```


Nachdem die Authentifizierung erfolgreich war, gibt Azure AD ein Zugriffstoken zurück:

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

Das Token ist eine Base-64-Zeichenfolge, die alle Informationen zum authentifizierten Benutzer codiert und die für den Azure Database for MySQL-Dienst bestimmt ist.

Die Gültigkeitsdauer des Zugriffstokens liegt zwischen ***5 Minuten und 60 Minuten***. Es wird empfohlen, das Zugriffstoken erst kurz vor dem Initiieren der Anmeldung bei Azure Database for MySQL abzurufen. Sie können den folgenden PowerShell-Befehl verwenden, um die Gültigkeitsdauer des Tokens anzuzeigen. 

```azurepowershell-interactive
$accessToken.ExpiresOn.DateTime
```

### <a name="step-3-use-token-as-password-for-logging-in-with-mysql"></a>Schritt 3: Verwenden des Tokens als Kennwort für die Anmeldung bei MySQL

Beim Herstellen einer Verbindung müssen Sie das Zugriffstoken als MySQL-Benutzerkennwort verwenden. Wenn Sie GUI-Clients wie MySQLWorkbench verwenden, können Sie das Token mit der oben beschriebenen Methode abrufen. 

#### <a name="using-mysql-cli"></a>Verwenden der MySQL CLI
Bei Verwendung der CLI können Sie zum Herstellen einer Verbindung die folgende Kurzform verwenden: 

**Beispiel (Linux/macOS):**
```
mysql -h mydb.mysql.database.azure.com \ 
  --user user@tenant.onmicrosoft.com@mydb \ 
  --enable-cleartext-plugin \ 
  --password=`az account get-access-token --resource-type oss-rdbms --output tsv --query accessToken`
```
#### <a name="using-mysql-workbench"></a>Über MySQL Workbench
* Starten Sie MySQL Workbench, und klicken Sie auf die Option „Database“ (Datenbank) und dann auf „Connect to database“ (Verbindung mit der Datenbank herstellen).
* Geben Sie in das Feld „Hostname“ den MySQL-FQDN ein. Beispiel: mydb.mysql.database.azure.com
* Geben Sie im Feld „username“ (Benutzername) den Namen des MySQL Azure Active Directory-Administrators ein, und fügen Sie diesen an den MySQL-Servernamen an, nicht an den FQDN. Beispiel: user@tenant.onmicrosoft.com@mydb.
* Klicken Sie im Feld „Password“ (Kennwort) auf „Store in Vault“ (Im Tresor speichern), und fügen Sie das Zugriffstoken aus der Datei ein. Beispiel: „C:\temp\MySQLAccessToken.txt“.
* Klicken Sie auf die Registerkarte „Advanced“ (Erweitert), und stellen Sie sicher, dass Sie „Enable Cleartext Authentication Plugin“ (Plug-In für die Klartextauthentifizierungs aktivieren) aktivieren.
* Klicken Sie auf „OK“, um die Verbindung mit der Datenbank herzustellen.

#### <a name="important-considerations-when-connecting"></a>Wichtige Aspekte beim Herstellen einer Verbindung:

* `user@tenant.onmicrosoft.com` ist der Name des Azure AD-Benutzers bzw. der Azure AD-Gruppe, mit dem oder der Sie die Verbindung herstellen möchten.
* Fügen Sie dem Namen des Azure AD-Benutzers oder der Azure AD-Gruppe immer den Servernamen an (z. B. `@mydb`).
* Stellen Sie sicher, dass Sie die korrekte Schreibweise des Azure AD-Benutzers oder der Azure AD-Gruppe verwenden.
* Bei Azure AD-Benutzer- und -Gruppennamen wird Groß-/Kleinschreibung beachtet.
* Wenn Sie sich als Gruppe verbinden, verwenden Sie nur den Gruppennamen (z. B. `GroupName@mydb`).
* Verwenden Sie `\` als Escapezeichen vor Leerzeichen, falls der Name Leerzeichen enthält.

Beachten Sie die Einstellung „enable-cleartext-plugin“. Sie müssen bei anderen Clients eine ähnliche Konfiguration verwenden, um sicherzustellen, dass das Token ohne Hashing an den Server gesendet wird.

Sie sind jetzt bei Ihrem MySQL-Server über Azure AD-Authentifizierung authentifiziert.

## <a name="creating-azure-ad-users-in-azure-database-for-mysql"></a>Erstellen von Azure AD-Benutzern in Azure Database for MySQL

Um Ihrer Azure Database for MySQL-Datenbank einen Azure AD-Benutzer hinzuzufügen, führen Sie nach dem Herstellen einer Verbindung die folgenden Schritte aus (weitere Informationen finden Sie in einem späteren Abschnitt zum Herstellen einer Verbindung):

1. Stellen Sie zunächst sicher, dass der Azure AD-Benutzer `<user>@yourtenant.onmicrosoft.com` ein gültiger Benutzer im Azure AD-Mandanten ist.
2. Melden Sie sich bei Ihrer Azure Database for MySQL-Instanz als Azure AD-Administratorbenutzer an.
3. Erstellen Sie den Benutzer `<user>@yourtenant.onmicrosoft.com` in Azure Database for MySQL.

**Beispiel:**

```sql
CREATE AADUSER 'user1@yourtenant.onmicrosoft.com';
```

Bei Benutzernamen mit mehr als 32 Zeichen empfiehlt es sich, stattdessen einen Alias zu verwenden, der beim Herstellen einer Verbindung genutzt wird: 

Beispiel:

```sql
CREATE AADUSER 'userWithLongName@yourtenant.onmicrosoft.com' as 'userDefinedShortName'; 
```
> [!NOTE]
> 1. MySQL ignoriert führende und nachgestellte Leerzeichen, daher sollte der Benutzername keine führenden oder nachgestellten Leerzeichen enthalten. 
> 2. Die Authentifizierung eines Benutzers über Azure AD verleiht dem Benutzer keine Berechtigungen für den Zugriff auf Objekte innerhalb der Azure Database for MySQL-Datenbank. Sie müssen dem Benutzer die erforderlichen Berechtigungen manuell erteilen.

## <a name="creating-azure-ad-groups-in-azure-database-for-mysql"></a>Erstellen von Azure AD-Gruppen in Azure Database for MySQL

Um eine Azure AD-Gruppe für den Zugriff auf Ihre Datenbank zu aktivieren, verwenden Sie den gleichen Mechanismus wie für Benutzer, geben aber stattdessen den Gruppennamen an:

**Beispiel:**

```sql
CREATE AADUSER 'Prod_DB_Readonly';
```

Wenn sie sich anmelden, verwenden die Mitglieder der Gruppe ihre persönlichen Zugriffstoken, melden sich aber mit dem Gruppennamen an, der als Benutzername angegeben wurde.

## <a name="token-validation"></a>Tokenüberprüfung

Azure AD-Authentifizierung in Azure Database for MySQL stellt sicher, dass der Benutzer auf dem MySQL-Server vorhanden ist, und überprüft die Gültigkeit des Tokens, indem der Inhalt des Tokens überprüft wird. Die folgenden Tokenvalidierungsschritte werden ausgeführt:

-   Das Token ist von Azure AD signiert und wurde nicht manipuliert.
-   Das Token wurde von Azure AD für den dem Server zugeordneten Mandanten ausgestellt.
-   Das Token ist nicht abgelaufen.
-   Das Token gilt für die Azure Database for MySQL-Ressource (und nicht für eine andere Azure-Ressource).

## <a name="compatibility-with-application-drivers"></a>Kompatibilität mit Anwendungstreibern

Die meisten Treiber werden unterstützt. Stellen Sie jedoch sicher, dass die Einstellungen zum Senden des Kennworts in Klartext verwendet werden, damit das Token ohne Änderungen gesendet wird.

* C/C++
  * libmysqlclient: Unterstützt
  * mysql-connector-c++: Unterstützt
* Java
  * Connector/J (mysql-connector-java): Unterstützt; `useSSL`-Einstellung muss verwendet werden
* Python
  * Connector/Python: Unterstützt
* Ruby
  * mysql2: Unterstützt
* .NET
  * mysql-connector-net: Unterstützt; Plug-In für „mysql_clear_password“ muss hinzugefügt werden
  * mysql-net/MySqlConnector: Unterstützt
* Node.js
  * mysqljs: Nicht unterstützt (sendet das Token nicht in Klartext ohne Patch)
  * node-mysql2: Unterstützt
* Perl
  * DBD::mysql: Unterstützt
  * Net::MySQL: Nicht unterstützt
* Go
  * go-sql-driver: Unterstützt; `?tls=true&allowCleartextPasswords=true` zur Verbindungszeichenfolge hinzufügen

## <a name="next-steps"></a>Nächste Schritte

* Überprüfen der Gesamtkonzepte für [Azure Active Directory-Authentifizierung mit Azure Database for MySQL](concepts-azure-ad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/set-azure-ad-admin.png
