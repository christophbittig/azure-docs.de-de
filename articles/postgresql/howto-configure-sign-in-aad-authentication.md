---
title: Verwenden von Azure Active Directory – Azure Database for PostgreSQL – Einzelserver
description: Informieren Sie sich, wie Sie Azure Active Directory (AAD) für die Authentifizierung mit Azure Database for PostgreSQL (Einzelserver) einrichten.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/26/2021
ms.openlocfilehash: 03f0ab53b4d2db74a18073808295e12fe5adaaaa
ms.sourcegitcommit: bb9a6c6e9e07e6011bb6c386003573db5c1a4810
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2021
ms.locfileid: "110494782"
---
# <a name="use-azure-active-directory-for-authentication-with-postgresql"></a>Verwenden von Azure Active Directory für die Authentifizierung mit PostgreSQL

Dieser Artikel führt Sie durch die Schritte zum Konfigurieren des Zugriffs auf Azure Active Directory mit Azure Database for PostgreSQL sowie zum Herstellen einer Verbindung mit einem Azure AD-Token.

## <a name="setting-the-azure-ad-admin-user"></a>Festlegen des Azure AD-Administratorbenutzers

Nur Azure AD-Administratorbenutzer können Benutzer für die Azure AD-basierte Authentifizierung erstellen oder aktivieren. Es wird empfohlen, den Azure AD-Administrator nicht für reguläre Datenbankvorgänge zu verwenden, da er über erhöhte Benutzerberechtigungen verfügt (z. B. CREATEDB).

Führen Sie die folgenden Schritte aus, um den Azure AD-Administrator (Benutzer oder Gruppe) festzulegen:

1. Wählen Sie im Azure-Portal die Instanz von Azure Database for PostgreSQL aus, die Sie für Azure AD aktivieren möchten.
2. Wählen Sie unter „Einstellungen“ die Option „Active Directory-Administrator“ aus:

![Festlegen des Azure AD-Administrators][2]

3. Wählen Sie im Kundenmandanten einen gültigen Azure AD-Benutzer aus, der als Azure AD-Administrator fungieren soll.

> [!IMPORTANT]
> Beim Festlegen des Administrators wird dem Azure Database for PostgreSQL-Server ein neuer Benutzer mit vollständigen Administratorberechtigungen hinzugefügt. Der Azure AD Administratorbenutzer in Azure Database for PostgreSQL übt die Rolle `azure_ad_admin` aus.
> Es kann nur ein Azure AD-Administrator pro PostgreSQL-Server erstellt werden, und die Auswahl eines anderen Administrators überschreibt den vorhandenen Azure AD-Administrator, der für den Server konfiguriert ist. Sie können eine Azure AD-Gruppe anstelle eines einzelnen Benutzers angeben, damit mehrere Administratoren vorhanden sind. 

Es kann nur ein Azure AD-Administrator pro PostgreSQL-Server erstellt werden, und die Auswahl eines anderen Administrators überschreibt den vorhandenen Azure AD-Administrator, der für den Server konfiguriert ist. Sie können eine Azure AD-Gruppe anstelle eines einzelnen Benutzers angeben, damit mehrere Administratoren vorhanden sind. Beachten Sie, dass Sie sich dann zu Verwaltungszwecken mit dem Gruppennamen anmelden.

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Herstellen einer Verbindung mit Azure Database for PostgreSQL mithilfe von Azure AD

Die folgende Abbildung bietet eine Übersicht über den Workflow bei der Verwendung von Azure AD-Authentifizierung mit Azure Database for PostgreSQL:

![Authentifizierungsablauf][1]

Wir haben die Azure AD-Integration so konzipiert, dass sie mit gängigen PostgreSQL-Tools wie psql funktioniert, die nicht Azure AD-fähig sind und nur die Angabe von Benutzername und Kennwort beim Herstellen einer Verbindung mit PostgreSQL unterstützen. Wir übergeben das Azure AD-Token als Kennwort, wie in der Abbildung oben gezeigt.

Zurzeit haben wir die folgenden Clients getestet:

- psql-Befehlszeile (Verwenden Sie die PGPASSWORD-Variable, um das Token zu übergeben: Weitere Informationen finden Sie unter Schritt 3.)
- Azure Data Studio (mit der PostgreSQL-Erweiterung)
- Andere libpq-basierte Clients (z.B. gängige Anwendungsframeworks und ORMs)
- PgAdmin (Deaktivieren Sie bei der Servererstellung die Option „Jetzt verbinden“. Weitere Informationen finden Sie unter Schritt 4.)

Dies sind die Schritte, die ein Benutzer oder eine Anwendung für die Authentifizierung mit Azure AD ausführen muss, die im Folgenden beschrieben werden:

### <a name="prerequisites"></a>Voraussetzungen

Sie können den Schritten in Azure Cloud Shell, auf einer Azure-VM oder auf Ihrem lokalen Computer folgen. Stellen Sie sicher, dass die [Azure CLI](/cli/azure/install-azure-cli) installiert ist.

## <a name="authenticate-with-azure-ad-as-a-single-user"></a>Authentifizieren mit Azure AD als einzelner Benutzer

### <a name="step-1-login-to-the-users-azure-subscription"></a>Schritt 1: Anmelden beim Azure-Abonnement des Benutzers

Authentifizieren Sie sich zunächst über Azure AD mithilfe des Azure CLI-Tools. Dieser Schritt ist in Azure Cloud Shell nicht erforderlich.

```
az login
```

Mit dem Befehl wird ein Browserfenster auf der Seite „Azure AD-Authentifizierung“ gestartet. Sie müssen Ihre Azure AD-Benutzer-ID und das Kennwort angeben.

### <a name="step-2-retrieve-azure-ad-access-token"></a>Schritt 2: Abrufen eines Azure AD-Zugriffstokens

Rufen Sie das Azure CLI-Tool auf, um ein Zugriffstoken für den über Azure AD authentifizierten Benutzer aus Schritt 1 für den Zugriff auf Azure Database for PostgreSQL abzurufen.

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

Das Token ist eine Base-64-Zeichenfolge, die alle Informationen zum authentifizierten Benutzer codiert und die für den Azure Database for PostgreSQL-Dienst bestimmt ist.


### <a name="step-3-use-token-as-password-for-logging-in-with-client-psql"></a>Schritt 3: Verwenden des Tokens als Kennwort für die Anmeldung mit psql für Clients

Beim Herstellen einer Verbindung müssen Sie das Zugriffstoken als PostgreSQL-Benutzerkennwort verwenden.

Wenn der `psql`-Befehlszeilenclient verwendet wird, muss das Zugriffstoken über die Umgebungsvariable `PGPASSWORD` übergeben werden, da das Zugriffstoken die Kennwortlänge überschreitet, die `psql` direkt annehmen kann:

Windows-Beispiel:

```cmd
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

```PowerShell
$env:PGPASSWORD='<copy/pasted TOKEN value from step 2>'
```

Beispiel für Linux/macOS:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Nun können Sie eine Verbindung mit Azure Database for PostgreSQL wie gewohnt initiieren:

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres sslmode=require"
```
### <a name="step-4-use-token-as-a-password-for-logging-in-with-pgadmin"></a>Schritt 4: Verwenden des Tokens als Kennwort für die Anmeldung mit PgAdmin

Führen Sie die folgenden Schritte aus, um mithilfe eines Azure AD-Tokens eine Verbindung mit pgAdmin herzustellen:
1. Deaktivieren Sie bei der Servererstellung die Option „Jetzt verbinden“.
2. Geben Sie Ihre Serverdetails auf der Registerkarte „Verbindung“ ein, und speichern Sie sie.
3. Klicken Sie im Browsermenü auf die Option zum Herstellen einer Verbindung mit dem Azure Database for PostgreSQL-Server.
4. Geben Sie das AD-Tokenkennwort ein, wenn Sie dazu aufgefordert werden.


Wichtige Aspekte beim Herstellen einer Verbindung:

* `user@tenant.onmicrosoft.com` ist der Name des Azure AD-Benutzers. 
* Achten Sie darauf, die genaue Schreibweise des Azure-Benutzers zu verwenden, da bei den Namen für Azure AD Benutzer und -Gruppen die Groß-/Kleinschreibung beachtet wird.
* Verwenden Sie `\` als Escapezeichen vor Leerzeichen, falls der Name Leerzeichen enthält.
* Die Gültigkeitsdauer des Zugriffstokens liegt zwischen 5 Minuten und 60 Minuten. Es wird empfohlen, das Zugriffstoken erst kurz vor dem Initiieren der Anmeldung bei Azure Database for PostgreSQL abzurufen.

Sie sind jetzt über Azure AD-Authentifizierung bei Ihrem Azure Database for PostgreSQL-Server authentifiziert.

## <a name="authenticate-with-azure-ad-as-a-group-member"></a>Authentifizieren mit Azure AD als Gruppenmitglied

### <a name="step-1-create-azure-ad-groups-in-azure-database-for-postgresql"></a>Schritt 1: Erstellen von Azure AD-Gruppen in Azure Database for PostgreSQL

Um eine Azure AD-Gruppe für den Zugriff auf Ihre Datenbank zu aktivieren, verwenden Sie den gleichen Mechanismus wie für Benutzer, geben aber stattdessen den Gruppennamen an:

Beispiel:

```
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```
Wenn sie sich anmelden, verwenden die Mitglieder der Gruppe ihre persönlichen Zugriffstoken, melden sich aber mit dem Gruppennamen an, der als Benutzername angegeben wurde.

### <a name="step-2-login-to-the-users-azure-subscription"></a>Schritt 2: Anmelden beim Azure-Abonnement des Benutzers

Authentifizieren Sie sich mithilfe des Azure CLI-Tools bei Azure AD. Dieser Schritt ist in Azure Cloud Shell nicht erforderlich. Der Benutzer muss Mitglied der Azure AD-Gruppe sein.

```
az login
```

### <a name="step-3-retrieve-azure-ad-access-token"></a>Schritt 3: Abrufen eines Azure AD-Zugriffstokens

Rufen Sie das Azure CLI-Tool auf, um ein Zugriffstoken für den über Azure AD authentifizierten Benutzer aus Schritt 2 für den Zugriff auf Azure Database for PostgreSQL abzurufen.

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

### <a name="step-4-use-token-as-password-for-logging-in-with-psql-or-pgadmin-see-above-steps-for-user-connection"></a>Schritt 4: Verwenden des Tokens als Kennwort für die Anmeldung mit psql oder PgAdmin (siehe die obigen Schritte für die Benutzerverbindung)

Wichtige Überlegungen beim Herstellen einer Verbindung als Gruppenmitglied:
* groupname@mydb ist der Name der Azure AD-Gruppe, mit der Sie die Verbindung herstellen möchten.
* Fügen Sie dem Namen des Azure AD-Benutzers oder der Azure AD-Gruppe immer den Servernamen an (z. B. @mydb).
* Stellen Sie sicher, dass Sie die korrekte Schreibweise des Azure AD-Gruppennamens verwenden.
* Bei Azure AD-Benutzer- und -Gruppennamen wird Groß-/Kleinschreibung beachtet.
* Verwenden Sie beim Herstellen einer Verbindung als Gruppe nur den Gruppennamen (z. B. GroupName@mydb) und nicht den Alias eines Gruppenmitglieds.
* Verwenden Sie  als Escapezeichen vor Leerzeichen, falls der Name Leerzeichen enthält.
* Die Gültigkeitsdauer des Zugriffstokens liegt zwischen 5 Minuten und 60 Minuten. Es wird empfohlen, das Zugriffstoken erst kurz vor dem Initiieren der Anmeldung bei Azure Database for PostgreSQL abzurufen.
  
Sie sind jetzt bei Ihrem PostgreSQL-Server über Azure AD-Authentifizierung authentifiziert.


## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Erstellen von Azure AD-Benutzern in Azure Database for PostgreSQL

Um Ihrer Azure Database for PostgreSQL-Datenbank einen Azure AD-Benutzer hinzuzufügen, führen Sie nach dem Herstellen einer Verbindung die folgenden Schritte aus (weitere Informationen finden Sie in einem späteren Abschnitt zum Herstellen einer Verbindung):

1. Stellen Sie zunächst sicher, dass der Azure AD-Benutzer `<user>@yourtenant.onmicrosoft.com` ein gültiger Benutzer im Azure AD-Mandanten ist.
2. Melden Sie sich bei Ihrer Azure Database for PostgreSQL-Instanz als Azure AD-Administratorbenutzer an.
3. Erstellen Sie die Rolle `<user>@yourtenant.onmicrosoft.com` in Azure Database for PostgreSQL.
4. Erklären Sie `<user>@yourtenant.onmicrosoft.com` zum Mitglied der Rolle azure_ad_user. Dies darf nur für Azure AD-Benutzer erfolgen.

**Beispiel:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Die Authentifizierung eines Benutzers über Azure AD verleiht dem Benutzer keine Berechtigung für den Zugriff auf Objekte innerhalb der Azure Database for PostgreSQL-Datenbank. Sie müssen dem Benutzer die erforderlichen Berechtigungen manuell erteilen.

## <a name="token-validation"></a>Tokenüberprüfung

Azure AD-Authentifizierung in Azure Database for PostgreSQL stellt sicher, dass der Benutzer auf dem PostgreSQL-Server vorhanden ist, und überprüft die Gültigkeit des Tokens, indem der Inhalt des Tokens überprüft wird. Die folgenden Tokenvalidierungsschritte werden ausgeführt:

- Das Token ist von Azure AD signiert und wurde nicht manipuliert.
- Das Token wurde von Azure AD für den dem Server zugeordneten Mandanten ausgestellt.
- Das Token ist nicht abgelaufen.
- Das Token gilt für die Azure Database for PostgreSQL-Ressource (und nicht für eine andere Azure-Ressource).

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>Migrieren vorhandener PostgreSQL-Benutzer zu Azure AD-basierter Authentifizierung

Sie können Azure AD-Authentifizierung für vorhandene Benutzer aktivieren. Es müssen zwei Fälle berücksichtigt werden:

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>Fall 1: Der PostgreSQL-Benutzername entspricht dem Azure AD-Benutzerprinzipalnamen

In dem unwahrscheinlichen Fall, dass Ihre vorhandenen Benutzer bereits den Azure AD-Benutzernamen entsprechen, können Sie ihnen die Rolle `azure_ad_user` zuweisen, um sie für die Azure AD-Authentifizierung zu aktivieren:

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

Sie sind jetzt in der Lage, sich mit Azure AD-Anmeldeinformationen anzumelden, anstatt ihr zuvor konfiguriertes PostgreSQL-Benutzerkennwort zu verwenden.

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>Fall 2: Der PostgreSQL-Benutzername entspricht nicht dem Azure AD-Benutzerprinzipalnamen

Wenn ein PostgreSQL-Benutzer entweder nicht in Azure AD vorhanden ist oder einen anderen Benutzernamen aufweist, können Sie Azure AD-Gruppen verwenden, um sich als dieser PostgreSQL-Benutzer zu authentifizieren. Sie können vorhandene Azure Database for PostgreSQL-Benutzer zu Azure AD migrieren, indem Sie eine Azure AD-Gruppe mit einem Namen erstellen, der mit dem PostgreSQL-Benutzer übereinstimmt, und dem vorhandenen PostgreSQL-Benutzer dann die Rolle azure_ad_user zuweisen:

```sql
GRANT azure_ad_user TO "DBReadUser";
```

Dabei wird davon ausgegangen, dass Sie eine Gruppe „DBReadUser“ in Azure AD erstellt haben. Benutzer, die zu dieser Gruppe gehören, können sich nun bei der Datenbank als dieser Benutzer anmelden.

## <a name="next-steps"></a>Nächste Schritte

* Überprüfen der Gesamtkonzepte für die [Azure Active Directory-Authentifizierung mit Azure Database for PostgreSQL: Einzelserver](concepts-aad-authentication.md)

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
