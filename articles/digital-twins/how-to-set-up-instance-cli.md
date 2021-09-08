---
title: Einrichten einer Instanz und der Authentifizierung (CLI)
titleSuffix: Azure Digital Twins
description: Informationen zum Einrichten einer Instanz des Azure Digital Twins-Diensts mithilfe der CLI
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6f64791df0c564183900fd75c718ac4e5f9d0976
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468842"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Einrichten einer Azure Digital Twins-Instanz und der Authentifizierung (CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Dieser Artikel behandelt die Schritte zum **Einrichten einer neuen Azure Digital Twins-Instanz**, einschließlich des Erstellens der Instanz und des Einrichtens der Authentifizierung. Nachdem Sie diesen Artikel durchgearbeitet haben, verfügen Sie über eine Azure Digital Twins-Instanz, die für die Programmierung bereitsteht.

In der vorliegenden Version dieses Artikels werden diese Schritte manuell nacheinander mithilfe der CLI durchlaufen.
* Wenn Sie diese Schritte manuell im Azure-Portal durchlaufen möchten, finden Sie weitere Informationen in der Portal-Version dieses Artikels unter [Einrichten einer Instanz und der Authentifizierung (Portal)](how-to-set-up-instance-portal.md).
* Ein Beispiel zur Ausführung einer automatisierten Einrichtung mit einem Bereitstellungsskript finden Sie in der Skriptversion dieses Artikels unter [Einrichten einer Instanz und der Authentifizierung (per Skript)](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="set-up-cloud-shell-session"></a>Einrichten einer Cloud Shell-Sitzung
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Erstellen der Azure Digital Twins-Instanz

In diesem Abschnitt **erstellen Sie eine neue Azure Digital Twins-Instanz** mithilfe des Cloud Shell-Befehls. Hierzu müssen Sie Folgendes bereitstellen:
* Eine Ressourcengruppe, in der die Instanz bereitgestellt wird. Wenn Sie noch nicht über eine Ressourcengruppe verfügen, können Sie diese jetzt mit dem folgenden Befehl erstellen:
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* Eine Region für die Bereitstellung. Informationen zu Regionen mit Unterstützung von Azure Digital Twins finden Sie unter [Verfügbare Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* Ein Name für Ihre Instanz. Wenn in Ihrem Abonnement für die Region eine weitere Azure Digital Twins-Instanz vorhanden ist, die den angegeben Namen bereits verwendet, werden Sie aufgefordert, einen anderen Namen auszuwählen.

Verwenden Sie im folgenden [az dt](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)-Befehl diese Werte, um die Instanz zu erstellen:

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> --resource-group <your-resource-group> --location <region>
```

### <a name="verify-success-and-collect-important-values"></a>Überprüfen des Erfolgs und Erfassen wichtiger Werte

Wenn die Instanz erfolgreich erstellt wurde, sieht das Ergebnis in Cloud Shell in etwa wie folgt aus und gibt Informationen über die von Ihnen erstellte Ressource aus:

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Screenshot: Fenster „Cloud Shell“ mit erfolgreicher Erstellung einer Ressourcengruppe und Azure Digital Twins-Instanz im Azure-Portal.":::

Beachten Sie die Angaben für **hostName**, **name** und **resourceGroup** der Azure Digital Twins-Instanz in der Ausgabe. Dies sind alles wichtige Werte, die Sie möglicherweise benötigen, wenn Sie mit Ihrer Azure Digital Twins-Instanz weiterarbeiten, um die Authentifizierung und die zugehörigen Azure-Ressourcen einzurichten. Wenn andere Benutzer in der Instanz programmieren, sollten Sie ihnen diese Werte mitteilen.

> [!TIP]
> Sie können diese Eigenschaften sowie alle Eigenschaften Ihrer Instanz jederzeit durch Ausführen von `az dt show --dt-name <your-Azure-Digital-Twins-instance>` einsehen.

Die Azure Digital Twins-Instanz ist jetzt einsatzbereit. Im nächsten Schritt stellen Sie die entsprechenden Azure-Benutzerberechtigungen für die Verwaltung der Instanz zur Verfügung.

## <a name="set-up-user-access-permissions"></a>Einrichten von Benutzerzugriffsberechtigungen

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>Voraussetzungen: Berechtigungsanforderungen

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>Zuweisen der Rolle

Um einem Benutzer Berechtigungen zum Verwalten einer Azure Digital Twins-Instanz zu gewähren, müssen Sie ihm die Rolle **Azure Digital Twins Data Owner** (Azure Digital Twins-Datenbesitzer) innerhalb der Instanz zuweisen.

Verwenden Sie den folgenden Befehl, um die Rolle zuzuweisen (dieser Vorgang muss von einem Benutzer mit [ausreichenden Berechtigungen](#prerequisites-permission-requirements) im Azure-Abonnement ausgeführt werden). In diesem Befehl müssen Sie den *Benutzerprinzipalnamen* im Azure AD-Konto für den Benutzer übergeben, dem die Rolle zugewiesen werden soll. In den meisten Fällen entspricht dieser Name der E-Mail-Adresse des Benutzers im Azure AD-Konto.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

Die Ausgabe dieses Befehls enthält Informationen zur erstellten Rollenzuweisung.

> [!NOTE]
> Wenn dieser Befehl den Fehler zurückgibt, dass die CLI **den Benutzer- oder Dienstprinzipal in der Graphdatenbank nicht findet**, gehen Sie folgendermaßen vor:
>
> Weisen Sie die Rolle stattdessen mithilfe der *Objekt-ID* des Benutzers zu. Dies kann bei Benutzern mit persönlichen [Microsoft-Konten (MSA)](https://account.microsoft.com/account) passieren. 
>
> Verwenden Sie die [Azure-Portalseite mit den Azure Active Directory-Benutzern](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers), um das Benutzerkonto auszuwählen und die zugehörigen Details zu öffnen. Kopieren Sie die *Objekt-ID* des Benutzers:
>
> :::image type="content" source="media/includes/user-id.png" alt-text="Screenshot: Benutzerseite im Azure-Portal mit Hervorhebung der GUID im Feld „Objekt-ID“." lightbox="media/includes/user-id.png":::
>
> Wiederholen Sie dann den list-Befehl für die Rollenzuweisung mit der *Objekt-ID* des Benutzers für den oben genannten Parameter `assignee`.

### <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

Sie verfügen nun über eine einsatzbereite Azure Digital Twins-Instanz und haben Berechtigungen zugewiesen, um diese zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

Testen Sie einzelne REST-API-Aufrufe für Ihre Instanz mithilfe der Befehle der Azure Digital Twins-CLI: 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Befehlssatz der Azure Digital Twins-Befehlszeilenschnittstelle](concepts-cli.md)

Sie können auch eine Verbindung zwischen Ihrer Clientanwendung und Ihrer Instanz herstellen, indem Sie Authentifizierungscode verwenden:
* [Schreiben von App-Authentifizierungscode](how-to-authenticate-client.md)