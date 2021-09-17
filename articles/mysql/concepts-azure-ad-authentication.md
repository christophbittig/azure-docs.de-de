---
title: Active Directory-Authentifizierung – Azure Database for MySQL
description: Erfahren Sie mehr über die Konzepte von Azure Active Directory für die Authentifizierung mit Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: ea70b094c1b53a5ec4a7c80705e4ac795e6d8629
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598357"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Verwenden von Azure Active Directory für die Authentifizierung mit MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Microsoft Azure AD-Authentifizierung (Azure Active Directory) ist ein Mechanismus zum Herstellen einer Verbindung mit Azure Database for MySQL unter Verwendung der in Azure AD definierten Identitäten.
Mit Azure AD-Authentifizierung können Sie Datenbankbenutzeridentitäten und andere Microsoft-Dienste an einem zentralen Ort verwalten, wodurch die Berechtigungsverwaltung vereinfacht wird.

Beispiele für die Vorteile der Verwendung von Azure AD:

- Einheitliche Authentifizierung von Benutzern über Azure-Dienste hinweg
- Verwaltung von Kennwortrichtlinien und Kennwortrotation an einem zentralen Ort
- Mehrere Formen der Authentifizierung, die von Azure Active Directory unterstützt werden, wodurch das Speichern von Kennwörtern entfällt
- Kunden können Datenbankberechtigungen mithilfe von externen Gruppen (Azure AD) verwalten.
- Azure AD-Authentifizierung verwendet MySQL-Datenbankbenutzer zum Authentifizieren von Identitäten auf Datenbankebene
- Unterstützung von tokenbasierter Authentifizierung für Anwendungen, die eine Verbindung mit Azure Database for MySQL herstellen

Um Azure Active Directory-Authentifizierung zu konfigurieren und zu verwenden, verwenden Sie den folgenden Vorgang:

1. Erstellen Sie nach Bedarf Benutzeridentitäten, und füllen Sie Azure Active Directory mit diesen auf.
2. Ordnen Sie optional das Active Directory zu, das derzeit mit Ihrem Azure-Abonnement verknüpft ist, oder ändern Sie es.
3. Erstellen Sie einen Azure AD-Administrator für den Azure Database for MySQL-Server.
4. Erstellen Sie Datenbankbenutzer in der Datenbank, die Azure AD-Identitäten zugeordnet sind.
5. Stellen Sie eine Verbindung mit der Datenbank her, indem Sie ein Token für eine Azure AD-Identität abrufen und sich anmelden.

> [!NOTE]
> Informationen zum Erstellen und Auffüllen von Azure AD und zum anschließenden Konfigurieren von Azure AD mit Azure Database for MySQL finden Sie unter [Konfigurieren und Anmelden mit Azure AD für Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="architecture"></a>Aufbau

Die folgende Abbildung bietet eine Übersicht über die Funktionsweise der Authentifizierung bei der Verwendung von Azure AD-Authentifizierung mit Azure Database for MySQL. Die Pfeile zeigen die Kommunikationswege.

![Authentifizierungsablauf][1]

## <a name="administrator-structure"></a>Administratorstruktur

Bei Verwendung von Azure AD-Authentifizierung sind zwei Administratorkonten für den MySQL-Server vorhanden: der ursprüngliche MySQL-Administrator und der Azure AD-Administrator. Nur der auf einem Azure AD-Konto basierende Administrator kann den ersten eigenständigen Azure AD-Datenbankbenutzer in einer Benutzerdatenbank erstellen. Das Konto für die Azure AD-Administratoranmeldung kann ein Azure AD-Benutzer oder eine Azure AD-Gruppe sein. Wenn es sich bei dem Administrator um ein Gruppenkonto handelt, kann es von einem beliebigen Gruppenmitglied verwendet werden, sodass mehrere Azure AD-Administratoren den MySQL-Server verwalten können. Die Verwendung eines Gruppenkontos für den Administrator erleichtert die Verwaltung, da Sie Gruppenmitglieder in Azure AD zentral hinzufügen und entfernen können, ohne die Benutzer oder Berechtigungen auf dem MySQL-Server zu ändern. Es kann jeweils nur ein Azure AD-Administrator (ein Benutzer oder eine Gruppe) konfiguriert werden.

![Administratorstruktur][2]

## <a name="permissions"></a>Berechtigungen

Zum Erstellen neuer Benutzer, die sich mit Azure AD authentifizieren können, müssen Sie der designierte Azure AD-Administrator sein. Dieser Benutzer wird zugewiesen, indem das Azure AD-Administratorkonto für einen bestimmten Azure Database for MySQL-Server konfiguriert wird.

Um einen neuen Azure AD-Datenbankbenutzer zu erstellen, müssen Sie als Azure AD-Administrator eine Verbindung herstellen. Dies wird unter [Konfigurieren und Anmelden mit Azure AD für Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md) gezeigt.

Eine Azure AD-Authentifizierung ist nur möglich, wenn der Azure AD-Administrator für Azure Database for MySQL erstellt wurde. Falls der Azure Active Directory-Administrator vom Server entfernt wurde, können vorhandene Azure Active Directory-Benutzer, die zuvor erstellt wurden, nicht mehr mithilfe ihrer Azure Active Directory-Anmeldeinformationen auf die Datenbank zugreifen.

## <a name="connecting-using-azure-ad-identities"></a>Herstellen einer Verbindung mit Azure AD-Identitäten

Die Azure Active Directory-Authentifizierung unterstützt die folgenden Methoden der Verbindungsherstellung mit einer Datenbank unter Verwendung von Azure AD-Identitäten:

- Azure Active Directory Password
- Azure Active Directory Integrated
- Azure Active Directory: universell mit MFA
- Verwenden von Active Directory Anwendungszertifikaten oder Clientgeheimnissen
- [Verwaltete Identität](howto-connect-with-managed-identity.md)

Nachdem Sie sich über Active Directory authentifiziert haben, rufen Sie ein Token ab. Dieses Token ist Ihr Kennwort für die Anmeldung.

Beachten Sie, dass Verwaltungsvorgänge, z. B. das Hinzufügen neuer Benutzer, zu diesem Zeitpunkt nur für Azure AD-Benutzerrollen unterstützt werden.

> [!NOTE]
> Weitere Informationen zum Herstellen einer Verbindung mit einem Active Directory-Token finden Sie unter [Konfigurieren und Anmelden mit Azure AD für Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).

## <a name="additional-considerations"></a>Weitere Überlegungen

- Azure Active Directory-Authentifizierung ist nur für MySQL 5.7 und höher verfügbar.
- Es kann immer nur ein einzelner Azure AD-Administrator für einen Azure Database for MySQL-Server konfiguriert werden.
- Nur ein Azure AD-Administrator für MySQL kann zunächst über ein Azure Active Directory-Konto eine Verbindung mit Azure Database for MySQL herstellen. Der Active Directory-Administrator kann weitere Azure AD-Datenbankbenutzer konfigurieren.
- Wenn ein Benutzer aus Azure AD gelöscht wird, kann dieser Benutzer sich nicht mehr bei Azure AD authentifizieren, und daher ist es nicht mehr möglich, ein Zugriffstoken für diesen Benutzer abzurufen. In diesem Fall ist es nicht möglich, mit diesem Benutzer eine Verbindung mit dem Server herzustellen, obwohl der entsprechende Benutzer weiterhin in der Datenbank vorhanden ist.
> [!NOTE]
> Die Anmeldung mit dem gelöschten Azure AD-Benutzer kann bis zum Ablauf des Token (bis zu 60 Minuten nach Ausstellung des Token) dennoch erfolgen.  Wenn Sie den Benutzer auch aus Azure Database for MySQL entfernen, wird dieser Zugriff sofort widerrufen.
- Wenn der Azure AD-Administrator vom Server entfernt wird, ist der Server keinem Azure AD-Mandanten mehr zugeordnet, und daher werden alle Azure AD-Anmeldungen für den Server deaktiviert. Wenn Sie einen neuen Azure AD-Administrator aus demselben Mandanten hinzufügen, werden Azure AD-Anmeldungen erneut aktiviert.
- Azure Database for MySQL gleicht Zugriffstoken für den Azure Database for MySQL-Benutzer mit der eindeutigen Azure AD-Benutzer-ID des Benutzers ab, anstatt den Benutzernamen zu verwenden. Dies bedeutet Folgendes: Wenn ein Azure AD-Benutzer in Azure AD gelöscht und ein neuer Benutzer mit dem gleichen Namen erstellt wird, geht Azure Database for MySQL davon aus, dass dies ein anderer Benutzer ist. Wenn ein Benutzer aus Azure AD gelöscht und dann ein neuer Benutzer mit dem gleichen Namen hinzugefügt wird, kann der neue Benutzer daher keine Verbindung mit dem vorhandenen Benutzer herstellen.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen und Auffüllen von Azure AD und zum anschließenden Konfigurieren von Azure AD mit Azure Database for MySQL finden Sie unter [Konfigurieren und Anmelden mit Azure AD für Azure Database for MySQL](howto-configure-sign-in-azure-ad-authentication.md).
- Eine Übersicht über Anmeldungen und Datenbankbenutzer in Azure Database for MySQL finden Sie unter [Erstellen von Benutzern in Azure Database for MySQL](howto-create-users.md).

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
