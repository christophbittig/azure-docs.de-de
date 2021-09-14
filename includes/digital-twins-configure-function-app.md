---
author: baanders
description: Include-Datei, die beschreibt, wie eine Azure-Funktion für die Zusammenarbeit mit Azure Digital Twins konfiguriert wird
ms.service: digital-twins
ms.topic: include
ms.date: 7/14/2021
ms.author: baanders
ms.openlocfilehash: 6aabec311df33c1d08d12b48117d9763ccf1761d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801721"
---
Sie können den Sicherheitszugriff für die Funktions-App mithilfe der Azure-Befehlszeilenschnittstelle oder über das Azure-Portal einrichten. Führen Sie die folgenden Schritte für Ihre gewählte Option aus.

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/cli)

[!INCLUDE [digital-twins-configure-function-app-cli.md](digital-twins-configure-function-app-cli.md)]

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

Führen Sie im [Azure-Portal](https://portal.azure.com/) die folgenden Schritte aus.

#### <a name="assign-an-access-role"></a>Zuweisen einer Zugriffsrolle

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

Azure-Ressourcen können über eine systemseitig zugewiesene verwaltete Identität bei Clouddiensten (z. B. Azure Key Vault) authentifiziert werden, ohne dass Anmeldeinformationen im Code gespeichert werden. Nachdem Sie die systemseitig zugewiesene verwaltete Identität aktiviert haben, können alle erforderlichen Berechtigungen über die rollenbasierte Zugriffssteuerung von Azure gewährt werden. 

Der Lebenszyklus dieser Art verwalteter Identitäten ist an den Lebenszyklus dieser Ressource gebunden. Zusätzlich darf jede Ressource nur eine systemseitig zugewiesene verwaltete Identität aufweisen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach Ihrer Funktions-App, indem Sie im Suchfeld ihren Namen eingeben. Wählen Sie in den Ergebnissen Ihre App aus. 

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Screenshot des Azure-Portals. Der Name der Funktions-App wird auf der Suchleiste des Portals gesucht, und das Suchergebnis ist hervorgehoben.":::

1. Wählen Sie auf der Seite der Funktions-App links im Menü die Option __Identität__ aus, um eine verwaltete Identität für die Funktion zu verwenden. Vergewissern Sie sich auf der Seite __Systemseitig zugewiesen__, dass der __Status__ auf **Ein** festgelegt ist. Wenn nicht, sollten Sie dies nun durchführen und **Speichern** auswählen, um die Änderung zu speichern.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png" alt-text="Screenshot des Azure-Portals mit der Seite „Identität“ für die Funktions-App. „Status“ ist auf „Ein“ festgelegt." lightbox="../articles/digital-twins/media/includes/azure-functions/verify-system-managed-identity.png":::

1. Wählen Sie __Azure-Rollenzuweisungen__ aus.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png" alt-text="Screenshot des Azure-Portals. Auf der Seite „Identität“ der Azure-Funktion ist unter „Berechtigungen“ die Schaltfläche „Azure-Rollenzuweisungen“ hervorgehoben." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-1.png":::

    Wählen Sie die Option __+ Rollenzuweisung hinzufügen (Vorschau)__ aus.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png" alt-text="Screenshot des Azure-Portals. Auf der Seite „Azure-Rollenzuweisungen“ ist „+ Rollenzuweisung hinzufügen (Vorschau)“ hervorgehoben." lightbox="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-2.png":::

1. Wählen Sie auf der Seite __Rollenzuweisung hinzufügen (Vorschau)__ die folgenden Werte aus:

    * **Bereich:** _Ressourcengruppe_
    * **Abonnement**: Wählen Sie Ihr Azure-Abonnement.
    * **Ressourcengruppe:** Wählen Sie Ihre Ressourcengruppe aus.
    * **Rolle:** _Azure Digital Twins-Datenbesitzer_

    Wählen Sie __Speichern__ aus, um die Details zu speichern.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-role-assignment-3.png" alt-text="Screenshot des Azure-Portals, der zeigt, wie eine neue Rollenzuweisung hinzugefügt wird. Im Dialogfeld werden Felder für Bereich, Abonnement, Ressourcengruppe und Rolle angezeigt.":::

#### <a name="configure-application-settings"></a>Konfigurieren von Anwendungseinstellungen

Um die URL Ihrer Azure Digital Twins-Instanz für Ihre Funktion zugänglich zu machen, können Sie eine Umgebungsvariable festlegen. Anwendungseinstellungen werden als Umgebungsvariablen verfügbar gemacht, um den Zugriff auf die Azure Digital Twins-Instanz zu ermöglichen. Weitere Informationen zu Umgebungsvariablen finden Sie unter [Verwalten Ihrer Funktions-App](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal). 

Ermitteln Sie zunächst den Hostnamen Ihrer Instanz, um dann eine Umgebungsvariable mit der URL dieser Instanz festzulegen: 

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach Ihrer Instanz. 
1. Wählen Sie im Menü auf der linken Seite die Option __Übersicht__ aus. 
1. Kopieren Sie den Wert von __Hostname__.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/instance-host-name.png" alt-text="Screenshot des Azure-Portals mit hervorgehobenem Hostnamen auf der Übersichtsseite für die Instanz":::

Sie können nun eine Anwendungseinstellung erstellen:

1. Suchen Sie auf der Suchleiste des Portals nach Ihrer Funktions-App, und wählen Sie sie aus den Ergebnissen aus.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/portal-search-for-function-app.png" alt-text="Screenshot des Azure-Portals mit einer Suche nach dem Namen der Funktions-App auf der Suchleiste. Das Suchergebnis ist hervorgehoben.":::

1. Wählen Sie links die Option __Konfiguration__ aus. Wählen Sie dann auf der Registerkarte __Anwendungseinstellungen__ die Option __+ Neue Anwendungseinstellung__ aus.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting.png" alt-text="Screenshot des Azure-Portals mit hervorgehobener Schaltfläche zum Erstellen einer neuen Anwendungseinstellung auf der Konfigurationsseite für die Funktions-App":::

1. Verwenden Sie im daraufhin geöffneten Fenster den oben kopierten Hostnamenwert, um eine Anwendungseinstellung zu erstellen.
    * **Name**: `ADT_SERVICE_URL`
    * **Wert**: `https://<your-Azure-Digital-Twins-host-name>`
    
    Wählen Sie __OK__ aus, um eine Anwendungseinstellung zu erstellen.
    
    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/add-application-setting.png" alt-text="Screenshot des Azure-Portals. Auf der Seite „Anwendungseinstellung hinzufügen/bearbeiten“ sind die Felder „Name“ und „Wert“ ausgefüllt. Die Schaltfläche „OK“ ist hervorgehoben.":::

1. Nachdem Sie die Einstellung erstellt haben, sollte sie auf der Registerkarte __Anwendungseinstellungen__ angezeigt werden. Überprüfen Sie, ob **ADT_SERVICE_URL** in der Liste angezeigt wird. Speichern Sie dann die neue Anwendungseinstellung, indem Sie __Speichern__ auswählen.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/application-setting-save-details.png" alt-text="Screenshot des Azure-Portals. Auf der Registerkarte „Anwendungseinstellungen“ sind die neue Einstellung „ADT_SERVICE_URL“ und die Schaltfläche „Speichern“ hervorgehoben.":::

1. Bei jeder Änderung an Anwendungseinstellungen muss die Anwendung neu gestartet werden. Wählen Sie daher __Weiter__ aus, um Ihre Anwendung neu zu starten, wenn die entsprechende Aufforderung angezeigt wird.

    :::image type="content" source="../articles/digital-twins/media/includes/azure-functions/save-application-setting.png" alt-text="Screenshot des Azure-Portals mit einer Benachrichtigung, dass für Änderungen an Anwendungseinstellungen ein Neustart Ihrer Anwendung vorgenommen wird.":::

---
