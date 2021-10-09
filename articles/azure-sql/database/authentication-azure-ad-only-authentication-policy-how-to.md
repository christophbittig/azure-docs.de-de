---
title: Verwendung von Azure-Richtlinien zur Erzwingung der ausschließlichen Authentifizierung mit Azure Active Directory
description: Dieser Artikel führt Sie durch die Verwendung von Azure Policy, um die Authentifizierung nur mit Azure Active Directory (Azure AD) mit Azure SQL Database und Azure SQL Managed Instance durchzusetzen
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/22/2021
ms.openlocfilehash: 24cb978a20bc3a6e2385ed37994389abb6d32a32
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700231"
---
# <a name="using-azure-policy-to-enforce-azure-active-directory-only-authentication-with-azure-sql"></a>Verwendung von Azure-Richtlinien zur Erzwingung der ausschließlichen Azure Active Directory-Authentifizierung mit Azure SQL

[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> Die **Azure AD-only-Authentifizierung** und die damit verbundene Azure Policy-Funktion, die in diesem Artikel besprochen wird, befindet sich in der **öffentlichen Vorschau**. 

Dieser Artikel führt Sie durch die Erstellung einer Azure-Richtlinie, die eine reine Azure AD-Authentifizierung erzwingen würde, wenn Benutzer eine Azure SQL Managed Instance oder einen [logischen Server](logical-servers.md) für Azure SQL Database erstellen. Um mehr über die Azure AD-only-Authentifizierung während der Ressourcenerstellung zu erfahren, siehe [Server mit aktivierter Azure AD-only-Authentifizierung in Azure SQL erstellen](authentication-azure-ad-only-authentication-create-server.md).

In diesem Artikel werden folgende Vorgehensweisen behandelt:

> [!div class="checklist"]
> - Erstellen Sie eine Azure-Richtlinie, die die Erstellung von logischen Servern oder verwalteten Instanzen mit aktivierter [Azure AD-only-Authentifizierung](authentication-azure-ad-only-authentication.md) erzwingt.
> - Überprüfung der Einhaltung der Azure-Richtlinien

## <a name="prerequisite"></a>Voraussetzung

- Sie haben die Berechtigung, Azure-Richtlinien zu verwalten. Weitere Informationen finden Sie unter [Azure RBAC-Berechtigungen in Azure Policy](/azure/governance/policy/overview#azure-rbac-permissions-in-azure-policy).

## <a name="create-an-azure-policy"></a>Azure-Richtlinie erstellen

Beginnen Sie mit der Erstellung einer Azure-Richtlinie, die die Bereitstellung von SQL-Datenbanken oder verwalteten Instanzen mit aktivierter reiner Azure AD-Authentifizierung erzwingt.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Suchen Sie nach der **Dienstrichtlinie**.
1. Wählen Sie unter den Authoring-Einstellungen die Option **Definitionen**.
1. Suchen Sie im **Suchfeld** nach *Azure Active Directory only authentication*.

   Es stehen zwei integrierte Richtlinien zur Verfügung, um eine reine Azure AD-Authentifizierung durchzusetzen. Die eine ist für SQL Database, die andere für Managed Instance.

   - Für Azure SQL-Datenbank darf nur die Azure Active Directory-Authentifizierung aktiviert sein
   - Für Azure SQL Managed Instance darf nur die Azure Active Directory-Authentifizierung aktiviert sein

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/policy-azure-ad-only-authentication.png" alt-text="Screenshot der Azure-Richtlinie für die reine Azure AD-Authentifizierung":::

1. Wählen Sie den Namen der Richtlinie für Ihren Dienst. In diesem Beispiel werden wir Azure SQL Database verwenden. Wählen Sie **Für Azure SQL-Datenbank darf nur die Azure Active Directory-Authentifizierung aktiviert sein**.
1. Wählen Sie im neuen Menü **Zuordnen**.

   > [!NOTE]
   > Das JSON-Skript im Menü zeigt die integrierte Richtliniendefinition, die als Vorlage für die Erstellung einer benutzerdefinierten Azure-Richtlinie für SQL-Datenbank verwendet werden kann. Der Standardwert ist auf `Audit` eingestellt.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/assign-policy-azure-ad-only-authentication.png" alt-text="Screenshot der Zuweisung einer Azure-Richtlinie für die reine Azure AD-Authentifizierung":::

1. Fügen Sie auf der Registerkarte **Grundlagen** einen **Umfang** hinzu, indem Sie den Selektor ( **...** ) an der Seite des Feldes verwenden.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/selecting-scope-policy-azure-ad-only-authentication.png" alt-text="Screenshot der Auswahl des Azure-Richtlinienbereichs für die reine Azure AD-Authentifizierung":::

1. Wählen Sie im Anwendungsbereich **Umfang** Ihr **Abonnement** aus dem Dropdown-Menü aus und wählen Sie eine **Ressourcengruppe** für diese Richtlinie. Wenn Sie fertig sind, verwenden Sie die Schaltfläche **Auswählen**, um die Auswahl zu speichern.

   > [!NOTE]
   > Wenn Sie keine Ressourcengruppe auswählen, gilt die Richtlinie für das gesamte Abonnement.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/adding-scope-policy-azure-ad-only-authentication.png" alt-text="Screenshot des Hinzufügens des Azure-Richtlinienbereichs für die reine Azure AD-Authentifizierung":::

1. Wenn Sie wieder auf der Registerkarte **Grundlagen** sind, passen Sie den **Namen der Aufgabe** an und geben Sie optional eine **Beschreibung** ein. Stellen Sie sicher, dass die **Durchsetzung von Richtlinien** **aktiviert** ist.
1. Wechseln Sie auf die Registerkarte **Parameter**. Deaktivieren Sie die Option **Nur Parameter anzeigen, die eine Eingabe erfordern**.
1. Wählen Sie unter **Wirkung** die Option **Verweigern**. Diese Einstellung verhindert die Erstellung eines logischen Servers ohne aktivierte Azure AD-only-Authentifizierung.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/deny-policy-azure-ad-only-authentication.png" alt-text="Screenshot des Parameters für die Auswirkung der Azure-Richtlinie für die reine Azure AD-Authentifizierung":::

1. Auf der Registerkarte **Meldungen bei Nichteinhaltung** können Sie die Richtlinienmeldung anpassen, die angezeigt wird, wenn ein Verstoß gegen die Richtlinie vorliegt. Die Meldung informiert die Benutzer darüber, welche Richtlinie bei der Servererstellung durchgesetzt wurde.

   :::image type="content" source="media/authentication-azure-ad-only-authentication-policy/non-compliance-message-policy-azure-ad-only-authentication.png" alt-text="Screenshot der Meldung über die Nichteinhaltung von Azure-Richtlinien bei reiner Azure AD-Authentifizierung":::

1. Klicken Sie auf **Überprüfen + erstellen**. Überprüfen Sie die Richtlinie und klicken Sie auf die Schaltfläche **Erstellen**.

> [!NOTE]
> Es kann einige Zeit dauern, bis die neu geschaffene Richtlinie durchgesetzt wird.

## <a name="check-policy-compliance"></a>Überprüfen der Richtlinienkonformität

Sie können die **Compliance**-Einstellung unter dem **Richtliniendienst** überprüfen, um den Status der Compliance zu sehen.

Suchen Sie nach dem Zuweisungsnamen, den Sie der Police zuvor gegeben haben.

:::image type="content" source="media/authentication-azure-ad-only-authentication-policy/compliance-policy-azure-ad-only-authentication.png" alt-text="Screenshot der Azure-Richtlinienkonformität für die reine Azure AD-Authentifizierung":::

Sobald der logische Server mit reiner Azure AD-Authentifizierung erstellt wurde, erhöht der Richtlinienbericht den Zähler unter **Ressourcen nach Konformitätsstatus** visuell. Sie können sehen, welche Ressourcen konform oder nicht konform sind.

Wenn die Ressourcengruppe, die von der Richtlinie abgedeckt werden soll, bereits erstellte Server enthält, zeigt der Richtlinienbericht an, welche Ressourcen konform und welche nicht konform sind.

> [!NOTE]
> Das Aktualisieren des Konformitätsberichts kann einige Zeit dauern. Änderungen, die sich auf die Erstellung von Ressourcen oder reine Azure AD-Authentifizierungseinstellungen beziehen, werden nicht sofort gemeldet.    

## <a name="provision-a-server"></a>Bereitstellung eines Servers

Sie können dann versuchen, einen logischen Server oder eine verwaltete Instanz in der Ressourcengruppe bereitzustellen, der Sie die Azure-Richtlinie zugewiesen haben. Wenn die reine Azure AD-Authentifizierung während der Servererstellung aktiviert ist, wird die Bereitstellung erfolgreich sein. Wenn die reine Azure AD-Authentifizierung nicht aktiviert ist, schlägt die Bereitstellung fehl.

Weitere Informationen finden Sie unter [Erstellen eines Servers mit aktivierter reiner Azure AD-Authentifizierung in Azure SQL](authentication-azure-ad-only-authentication-create-server.md).

## <a name="next-steps"></a>Nächste Schritte

- Überblick über die [Azure-Richtlinie für die reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication-policy.md)
- [Erstellen eines Servers mit aktivierter reiner Azure AD-Authentifizierung in Azure SQL](authentication-azure-ad-only-authentication-create-server.md)
- Überblick über die [reine Azure AD-Authentifizierung](authentication-azure-ad-only-authentication.md)
