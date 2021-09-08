---
title: Einrichten einer Instanz und der Authentifizierung (Portal)
titleSuffix: Azure Digital Twins
description: Informationen zum Einrichten einer Instanz des Azure Digital Twins-Diensts mithilfe des Azure-Portals
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2, subject-rbac-steps
ms.openlocfilehash: f520ed1054c5d0d9dd6c053e22a984a813840131
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468774"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung (Portal)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Dieser Artikel behandelt die Schritte zum **Einrichten einer neuen Azure Digital Twins-Instanz**, einschließlich des Erstellens der Instanz und des Einrichtens der Authentifizierung. Nachdem Sie diesen Artikel durchgearbeitet haben, verfügen Sie über eine Azure Digital Twins-Instanz, die für die Programmierung bereitsteht.

In dieser Version dieses Artikels werden diese Schritte manuell nacheinander mithilfe des Azure-Portals durchlaufen. Das Azure-Portal ist eine webbasierte, zentrale Konsole, die eine Alternative zu Befehlszeilentools darstellt.
* Wenn Sie diese Schritte manuell mithilfe der Befehlszeilenschnittstelle durchführen möchten, finden Sie weitere Informationen in der entsprechenden Version dieses Artikels unter [Einrichten einer Instanz und der Authentifizierung (CLI)](how-to-set-up-instance-cli.md).
* Ein Beispiel zur Ausführung einer automatisierten Einrichtung mit einem Bereitstellungsskript finden Sie in der Skriptversion dieses Artikels unter [Einrichten einer Instanz und der Authentifizierung (per Skript)](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Erstellen der Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. Geben Sie auf der folgenden Seite **Ressource erstellen** die unten angegebenen Werte ein:
    * **Abonnement**: Das Azure-Abonnement, das Sie verwenden.
      - **Ressourcengruppe**: Eine Ressourcengruppe, in der die Instanz bereitgestellt werden soll. Wenn Sie nicht bereits eine vorhandene Ressourcengruppe in Erwägung ziehen, können Sie hier eine Ressourcengruppe erstellen, indem Sie den Link *Neu erstellen* auswählen und einen Namen für eine neue Ressourcengruppe eingeben
    * **Standort**: Eine für Azure Digital Twins aktivierte Region für die Bereitstellung. Weitere Informationen zur regionalen Unterstützung finden Sie unter [Verfügbare Azure-Produkte nach Region (Azure Digital Twins)](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins) .
    * **Ressourcenname**: Ein Name für Ihre Azure Digital Twins-Instanz. Wenn in Ihrem Abonnement für die Region eine weitere Azure Digital Twins-Instanz vorhanden ist, die den angegeben Namen bereits verwendet, werden Sie aufgefordert, einen anderen Namen auszuwählen.
    * **Zugriff auf Ressource gewähren**: Wenn Sie das Kontrollkästchen in diesem Abschnitt aktivieren, erhält Ihr Azure-Konto die Berechtigung, auf Daten in der Instanz zuzugreifen und diese zu verwalten. Wenn Sie die Person sind, die die Instanz verwaltet, sollten Sie dieses Kontrollkästchen jetzt aktivieren. Wenn es ausgegraut ist, weil Sie über keine Berechtigung im Abonnement verfügen, können Sie die Erstellung der Ressource fortsetzen und sich die Rolle später von einer Person mit den erforderlichen Berechtigungen zuweisen lassen. Weitere Informationen zu dieser Rolle und zum Zuweisen von Rollen zu Ihrer Instanz finden Sie im nächsten Abschnitt [Einrichten von Benutzerzugriffsberechtigungen](#set-up-user-access-permissions).

    :::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Screenshot des Prozesses „Ressource erstellen“ für Azure Digital Twins im Azure-Portal. Die beschriebenen Werte sind eingetragen.":::

4. Wenn Sie fertig sind, können Sie auf **Überprüfen + erstellen** klicken, wenn Sie keine weiteren Einstellungen für Ihre Instanz konfigurieren möchten. Dadurch gelangen Sie zu einer Zusammenfassungsseite, auf der Sie die eingegebenen Instanzdetails überprüfen und den Vorgang durch Klicken auf **Erstellen** abschließen können. 

    Wenn Sie weitere Details für Ihre Instanz konfigurieren möchten, finden Sie Informationen zu den übrigen Registerkarten für die Einrichtung im nächsten Abschnitt.

### <a name="additional-setup-options"></a>Zusätzliche Optionen für die Einrichtung

Im Folgenden finden Sie die zusätzlichen Optionen, die Sie während der Einrichtung mithilfe der anderen Registerkarten im Prozess **Ressource erstellen** verwenden können.

* **Netzwerk**: Auf dieser Registerkarte können Sie private Endpunkte mithilfe von [Azure Private Link](../private-link/private-link-overview.md) aktivieren, um zu verhindern, dass über ein öffentliches Netzwerk auf Ihre Instanz zugegriffen werden kann. Anweisungen dazu finden Sie unter [Aktivieren des privaten Zugriffs mit Private Link (Vorschau)](./how-to-enable-private-link.md?tabs=portal#add-a-private-endpoint-during-instance-creation).
* **Erweitert**: Auf dieser Registerkarte können Sie eine vom System verwaltete Identität für Ihre Instanz aktivieren, die beim Weiterleiten von Ereignissen an [Endpunkte](concepts-route-events.md) verwendet werden kann. Weitere Informationen zur Verwendung systemseitig verwalteter Identitäten mit Azure Digital Twins finden Sie unter [Sicherheit für Azure Digital Twins-Lösungen](concepts-security.md#managed-identity-for-accessing-other-resources-preview).
* **Tags**: Auf dieser Registerkarte können Sie Ihrer Instanz Tags hinzufügen, die Ihnen dabei helfen, die Instanz und andere Azure-Ressourcen zu verwalten. Weitere Informationen zu den Azure-Ressourcentags finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen und Verwaltungshierarchie](../azure-resource-manager/management/tag-resources.md).

### <a name="verify-success-and-collect-important-values"></a>Überprüfen des Erfolgs und Erfassen wichtiger Werte

Nachdem Sie die Einrichtung Ihrer Instanz durch das Klicken auf **Erstellen** abgeschlossen haben, können Sie in Ihren Azure-Benachrichtigungen auf der Symbolleiste des Portals den Bereitstellungsstatus Ihrer Instanz anzeigen. In der Benachrichtigung wird angezeigt, wenn die Bereitstellung erfolgreich war, und Sie können die Schaltfläche _Zu Ressource wechseln_ auswählen, um Ihre erstellte Instanz anzuzeigen.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Screenshot der Azure-Benachrichtigungen mit einer erfolgreichen Bereitstellung und Hervorhebung der Schaltfläche „Zu Ressource wechseln“ im Azure-Portal.":::

Wenn bei der Bereitstellung ein Fehler auftritt, wird in der Benachrichtigung alternativ die Ursache angegeben. Beachten Sie die Ratschläge in der Fehlermeldung, und versuchen Sie dann erneut, die Instanz zu erstellen.

>[!TIP]
>Nachdem die Instanz erstellt wurde, können Sie jederzeit zu ihrer Seite zurückkehren, indem Sie in der Suchleiste des Azure-Portals nach dem Namen Ihrer Instanz suchen.

Notieren Sie sich von der Seite *Übersicht* der Instanz den *Namen*, die *Ressourcengruppe* und den *Hostnamen*. Dies sind alles wichtige Werte, die Sie möglicherweise benötigen, wenn Sie mit Ihrer Azure Digital Twins-Instanz weiterarbeiten. Wenn andere Benutzer in der Instanz programmieren, sollten Sie diese Werte mit ihnen teilen.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Screenshot des Azure-Portals, der die wichtigen Werte auf der Seite „Übersicht“ der Azure Digital Twins-Instanz hervorhebt.":::

Die Azure Digital Twins-Instanz ist jetzt einsatzbereit. Im nächsten Schritt stellen Sie die entsprechenden Azure-Benutzerberechtigungen für die Verwaltung der Instanz zur Verfügung.

## <a name="set-up-user-access-permissions"></a>Einrichten von Benutzerzugriffsberechtigungen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Es gibt zwei Möglichkeiten zum Erstellen einer Rollenzuweisung für einen Benutzer in Azure Digital Twins:
* [Während Azure Digital Twins-Instanzerstellung](#assign-the-role-during-instance-creation)
* [Verwenden von Azure Identity & Access Management (IAM)](#assign-the-role-using-azure-identity-management-iam)

Beide erfordern die gleichen Berechtigungen.

### <a name="prerequisites-permission-requirements"></a>Voraussetzungen: Berechtigungsanforderungen

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role-during-instance-creation"></a>Zuweisen der Rolle während der Instanzerstellung

Bei der Erstellung Ihrer Azure Digital Twins-Ressource durch den [weiter oben in diesem Artikel](#create-the-azure-digital-twins-instance) beschriebenen Prozess wählen Sie unter **Zugriff auf Ressource gewähren** die Option **Azure Digital Twins Rolle „Datenbesitzer“ zuweisen** aus. Dadurch erhalten Sie Vollzugriff auf die APIs der Datenebene.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role.png" alt-text="Screenshot des Prozesses „Ressource erstellen“ für Azure Digital Twins im Azure-Portal. Das Kontrollkästchen unter „Zugriff auf Ressource gewähren“ ist hervorgehoben.":::

Wenn Sie nicht über die Berechtigung zum Zuweisen einer Rolle zu einer Identität verfügen, wird das Feld ausgegraut angezeigt.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2-role-greyed.png" alt-text="Screenshot des Prozesses „Ressource erstellen“ für Azure Digital Twins im Azure-Portal. Das Kontrollkästchen unter „Zugriff auf Ressource gewähren“ ist deaktiviert.":::

In diesem Fall können Sie die Azure Digital Twins-Ressource weiterhin erfolgreich erstellen, aber jemand mit den entsprechenden Berechtigungen muss Ihnen oder der Person, die die Daten der Instanz verwalten wird, diese Rolle zuweisen.

### <a name="assign-the-role-using-azure-identity-management-iam"></a>Zuweisen der Rolle mithilfe von Azure Identity & Access Management (IAM)

Sie können die Rolle **Azure Digital Twins-Datenbesitzer** auch mithilfe der Zugriffssteuerungsoptionen in Azure Identity & Access Management (IAM) zuweisen.

1. Öffnen Sie zunächst die Seite für Ihre Azure Digital Twins-Instanz im Azure-Portal. 

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** aus.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die Rolle **Azure Digital Twins-Datenbesitzer** zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Role | [Azure Digital Twins Data Owner (Azure Digital Twins-Datenbesitzer)](../role-based-access-control/built-in-roles.md#azure-digital-twins-data-owner) |
    | Zugriff zuweisen zu | Benutzer, Gruppe oder Dienstprinzipal |
    | Member | Suchen Sie nach dem Namen oder der E-Mail-Adresse des Benutzers, der zugewiesen werden soll. |
    
    ![Seite „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-page.png)

### <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

Sie können die Rollenzuweisung, die Sie eingerichtet haben, unter *Zugriffssteuerung (IAM) > Rollenzuweisungen* anzeigen. Der Benutzer sollte in der Liste mit der Rolle *Azure Digital Twins Data Owner* (Azure Digital Twins-Datenbesitzer) angezeigt werden. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Screenshot der Rollenzuweisungen für eine Azure Digital Twins-Instanz im Azure-Portal.":::

Sie verfügen nun über eine einsatzbereite Azure Digital Twins-Instanz und haben Berechtigungen zugewiesen, um diese zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie einzelne REST-API-Aufrufe für Ihre Instanz mithilfe der Befehle der Azure Digital Twins-CLI: 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Befehlssatz der Azure Digital Twins-Befehlszeilenschnittstelle](concepts-cli.md)

Sie können auch eine Verbindung zwischen Ihrer Clientanwendung und Ihrer Instanz herstellen, indem Sie Authentifizierungscode verwenden:
* [Schreiben von App-Authentifizierungscode](how-to-authenticate-client.md)