---
title: Integration in Logic Apps
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie Sie Logic Apps mit Azure Digital Twins verbinden können, indem Sie einen benutzerdefinierten Connector verwenden.
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: aa8ea41233cad42bcad9fc387b8f984503c6e75e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124797742"
---
# <a name="integrate-with-logic-apps-using-a-custom-connector"></a>Integration in Logic Apps mit einem benutzerdefinierten Connector

[Azure Logic Aapps](../logic-apps/logic-apps-overview.md) ist ein Clouddienst, der Sie bei der Automatisierung von Workflows in Apps und Diensten unterstützt. Indem Sie Logic Apps mit den Azure Digital Twins-APIs verbinden, können Sie solche automatisierten Flows um Azure Digital Twins und die zugehörigen Daten erstellen.

Azure Digital Twins verfügt derzeit nicht über einen zertifizierten (vorgefertigten) Connector für Logic Apps. Stattdessen besteht der aktuelle Prozess für die Verwendung von Logic Apps mit Azure Digital Twins darin, einen [benutzerdefinierten Logic Apps-Connector zu erstellen](../logic-apps/custom-connector-overview.md), der eine [benutzerdefinierte Azure Digital Twins-Swagger-Definitionsdatei](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) verwendet, die für die Arbeit mit Logic Apps geändert wurde.

> [!NOTE]
> Es gibt mehrere Versionen der Swagger-Definitionsdatei, die in dem oben verlinkten benutzerdefinierten Swagger-Beispiel enthalten ist. Die neueste Version befindet sich im Unterordner mit dem aktuellsten Datum. Aber auch die im Beispiel enthaltenen früheren Versionen werden noch unterstützt.

In diesem Artikel werden Sie das [Azure-Portal](https://portal.azure.com) verwenden, um einen **benutzerdefinierten Connector zu erstellen**, mit dem Sie Logic Apps mit einer Azure Digital Twins-Instanz verbinden können. Anschließend **erstellen Sie eine Logik-App**, die diese Verbindung für ein Beispielszenario verwendet, in dem durch einen Timer ausgelöste Ereignisse automatisch einen Zwilling in Ihrer Azure Digital Twins-Instanz aktualisieren. 

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
Melden Sie sich mit diesem Konto am [Azure-Portal](https://portal.azure.com) an. 

Zu den Voraussetzungen zählen auch die folgenden Punkte. Im Rest dieses Abschnitts werden die folgenden Schritte erläutert:
- Einrichten einer Azure Digital Twins-Instanz
- Hinzufügen eines digitalen Zwillings
- Einrichten einer Azure Active Directory-App-Registrierung (Azure AD)

### <a name="set-up-azure-digital-twins-instance"></a>Einrichten einer Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="add-a-digital-twin"></a>Hinzufügen eines digitalen Zwillings

In diesem Artikel wird Logic Apps verwendet, um einen Zwilling in Ihrer Azure Digital Twins-Instanz zu aktualisieren. Um fortzufahren, sollten Sie mindestens einen Zwilling in Ihrer Instanz hinzufügen. 

Sie können Zwillinge mit den [DigitalTwins-APIs](/rest/api/digital-twins/dataplane/twins), dem [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) oder der [Azure Digital Twins-CLI](/cli/azure/dt?view=azure-cli-latest&preserve-view=true) hinzufügen. Ausführliche Schritte zum Erstellen von Zwillingen mithilfe dieser Methoden finden Sie unter [Verwalten digitaler Zwillinge](how-to-manage-twin.md).

Sie benötigen die **Twin-ID** eines Zwillings, den Sie in Ihrer Instanz erstellt haben.

### <a name="set-up-app-registration"></a>Einrichten der App-Registrierung

[!INCLUDE [digital-twins-prereq-registration.md](../../includes/digital-twins-prereq-registration.md)]

## <a name="create-custom-logic-apps-connector"></a>Erstellen eines benutzerdefinierten Logic Apps-Connectors

Nun sind Sie bereit, einen [benutzerdefinierten Logic Apps-Connector](../logic-apps/custom-connector-overview.md) für die Azure Digital Twins-APIs zu erstellen. Auf diese Weise können Sie Azure Digital Twins einbinden, wenn Sie im nächsten Abschnitt eine Logik-App erstellen.

Navigieren Sie zu diesem Zweck zur Seite [Benutzerdefinierter Logic Apps-Connector](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Web%2FcustomApis) im Azure-Portal (Sie können diesen Link verwenden oder nach ihm in der Suchleiste des Portals suchen). Wählen Sie *+ Erstellen* aus.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-custom-connector.png" alt-text="Screenshot der Seite „Benutzerdefinierter Logic Apps-Connector“ im Azure-Portal. Die Schaltfläche „Hinzufügen“ ist hervorgehoben.":::

Wählen Sie auf der folgenden Seite **„Benutzerdefinierten Logic-Apps-Connector“** erstellen Ihr Abonnement und Ihre Ressourcengruppe sowie einen Namen und einen Bereitstellungsort für Ihren neuen Connector aus. Klicken Sie auf **Überprüfen + erstellen**. 

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-apps-custom-connector.png" alt-text="Screenshot der Seite „Benutzerdefinierten Logic-Apps-Connector&quot; im Azure-Portal.":::

Auf diese Weise gelangen Sie zur Registerkarte **„Überprüfen + Erstellen“** , wo Sie unten die Option **„Erstellen“** wählen können, um Ihre Ressource zu erstellen.

:::image type="content" source="media/how-to-integrate-logic-apps/review-logic-apps-custom-connector.png" alt-text="Screenshot der Registerkarte „Überprüfen und erstellen“ der Seite zum Überprüfen des benutzerdefinierten Logic Apps-Connectors im Azure-Portal.":::

Sie gelangen zur Bereitstellungsseite für den Connector. Wenn die Bereitstellung abgeschlossen ist, wählen Sie die Schaltfläche **Zur Ressource gehen**, um die Details des Connectors im Portal anzuzeigen.

### <a name="configure-connector-for-azure-digital-twins"></a>Konfigurieren des Connectors für Azure Digital Twins

Als Nächstes konfigurieren Sie den von Ihnen erstellten Connector für die Verbindung mit Azure Digital Twins.

Laden Sie zunächst eine benutzerdefinierte Azure Digital Twins Swagger-Datei herunter, die geändert wurde, um mit Logic Apps zusammenzuarbeiten. Laden Sie das Beispiel [Benutzerdefinierte Azure Digital Twins-Swagger (Logic Apps-Connector)](/samples/azure-samples/digital-twins-custom-swaggers/azure-digital-twins-custom-swaggers/) herunter, indem Sie die Schaltfläche **ZIP herunterladen** auswählen. Navigieren Sie zum heruntergeladenen Ordner *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_.zip*, und entzippen Sie ihn. 

Der benutzerdefinierte Swagger für dieses Lernprogramm befindet sich im *Ordner „digital-twins-custom-swaggers-main\LogicApps“* . Dieser Ordner enthält die Unterordner *stable* und *preview*, die jeweils verschiedene Versionen des Swagger nach Datum geordnet enthalten. Der Ordner mit dem neuesten Datum enthält die neueste Kopie der Swagger-Definitionsdatei. Unabhängig von der ausgewählten Version hat die Swagger-Datei die Bezeichnung _digitaltwins.json_.

> [!NOTE]
> Sofern Sie nicht mit einer Vorschaufunktion arbeiten, ist es generell empfehlenswert, die neueste *stabile* Version der Swagger-Datei zu verwenden. Allerdings werden auch frühere Versionen und Vorabversionen der Swagger-Datei weiterhin unterstützt. 

Wechseln Sie als Nächstes im [Azure-Portal](https://portal.azure.com) zur Übersichtsseite Ihres Connectors, und wählen Sie **Bearbeiten** aus.

:::image type="content" source="media/how-to-integrate-logic-apps/edit-connector.png" alt-text="Screenshot der Seite „Übersicht“ für den im vorherigen Schritt erstellten Connector. Die Schaltfläche „Bearbeiten“ ist hervorgehoben.":::

Konfigurieren Sie auf der Seite **Benutzerdefinierten Logic Apps-Connector bearbeiten**, die anschließend angezeigt wird, die folgenden Informationen:
* **Benutzerdefinierte Connectors**
    - API-Endpunkt: REST (Standardwert beibehalten)
    - Importmodus: OpenAPI-Datei (Standardwert beibehalten)
    - Datei: Diese Konfiguration wird die benutzerdefinierte Swagger-Datei sein, die Sie zuvor heruntergeladen haben. Wählen Sie **Importieren** aus, suchen Sie auf Ihrem Computer die Datei *Azure_Digital_Twins_custom_Swaggers__Logic_Apps_connector_\LogicApps\...\digitaltwins.json*, und wählen Sie **Öffnen** aus.
* **Allgemeine Informationen**
    - Icon: Laden Sie ein Icon hoch, das Ihnen gefällt.
    - Symbolhintergrundfarbe: Geben Sie für Ihre Farbe einen Hexadezimalcode im Format „#xxxxxx“ ein.
    - Beschreibung: Geben Sie beliebige Werte ein.
    - Verbindung über lokales Daten-Gateway: Ausgeschaltet (Standard belassen)
    - Schema: HTTPS (Standardwert beibehalten)
    - Host: Der **Hostname** Ihrer Azure Digital Twins-Instanz.
    - Basis-URL: / (Standardwert beibehalten)

Wählen Sie dann unten im Fenster die Schaltfläche **Sicherheit** aus, um mit dem nächsten Konfigurationsschritt fortzufahren.

:::image type="content" source="media/how-to-integrate-logic-apps/configure-next.png" alt-text="Screenshot des unteren Bereichs der Seite „Benutzerdefinierten Logic Apps-Connector bearbeiten“. Hervorhebung der Schaltfläche, um mit „Sicherheit“ fortzufahren.":::

Wählen Sie im Schritt „Sicherheit“ **Bearbeiten** aus, und konfigurieren Sie die folgenden Informationen:
* **Authentifizierungstyp:** OAuth 2.0
* **OAuth 2.0**:
    - Identitätsanbieter: Azure Active Directory
    - Client-ID: Die **Anwendungs-ID (Client)** für die Azure AD-App-Registrierung, die Sie in [Voraussetzungen](#prerequisites) erstellt haben.
    - Geheimer Clientschlüssel: Der **Geheime Clientschlüssel** aus der App-Registrierung. 
    - Anmelde-URL: https://login.windows.net (Standardwert beibehalten)
    - Mandanten-ID: Die **Verzeichnis-ID (Mandant)** für Ihre Azure-AD-App-Registrierung.
    - Ressourcen-URL: 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    - Bereich: Directory.AccessAsUser.All
    - Umleitungs-URL: (Standardeinstellung vorerst beibehalten)

Im Feld Umleitungs-URL steht *Speichern Sie den benutzerdefinierten Anschluss, um die Umleitungs-URL zu generieren*. Erzeugen Sie ihn jetzt, indem Sie oben im Fenster auf **Connector aktualisieren** klicken, um Ihre Connectoreinstellungen zu bestätigen.

:::image type="content" source="media/how-to-integrate-logic-apps/update-connector.png" alt-text="Screenshot des oberen Bereichs der Seite „Benutzerdefinierten Logic Apps-Connector bearbeiten“. Hervorhebung der Schaltfläche „Connector aktualisieren“.":::

Kehren Sie zum Feld „Umleitungs-URL“ zurück, und kopieren Sie den generierten Wert. Sie verwenden ihn im nächsten Schritt.

:::image type="content" source="media/how-to-integrate-logic-apps/copy-redirect-url.png" alt-text="Screenshot des Felds „Umleitungs-URL“ auf der Seite „Benutzerdefinierten Logic Apps-Connector bearbeiten“. Die Schaltfläche zum Kopieren des Werts ist hervorgehoben.":::

Jetzt haben Sie alle Informationen eingegeben, die für die Erstellung Ihres Verbinders erforderlich sind (Sie brauchen nicht über den Schritt „Sicherheit“ hinaus zum Schritt „Definition“ überzugehen). Sie können den Bereich **Benutzerdefinierten Logic Apps -Connector bearbeiten** schließen.

>[!NOTE]
>Beachten Sie zurück auf der Übersichtsseite Ihres Connectors, auf der Sie ursprünglich **Bearbeiten** ausgewählt haben, dass durch erneutes Auswählen von Bearbeiten der gesamte Prozess der Eingabe Ihrer Konfigurationsoptionen neu gestartet wird. Wenn Sie also eine aktualisierte Konfiguration mit geänderten Werten speichern möchten, müssen Sie auch alle anderen Werte erneut eingeben, um zu verhindern, dass sie durch die Standardwerte überschrieben werden.

### <a name="grant-connector-permissions-in-the-azure-ad-app"></a>Erteilen von Berechtigungen in der Azure AD-App für den Connector

Verwenden Sie nun die **Umleitungs-URL** des benutzerdefinierten Connector Wert, die Sie im letzten Schritt kopiert haben, um den Connector Berechtigungen in der Registrierung der Azure AD-App zu erteilen.

Navigieren Sie im Azure-Portal zur Seite [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade), und wählen Sie Ihre Registrierung aus der Liste aus. 

Fügen Sie im Menü der Registrierung unter **Authentifizierungs** einen URI hinzu.

:::image type="content" source="media/how-to-integrate-logic-apps/add-uri.png" alt-text="Screenshot der Seite „Authentifizierung“ für die App-Registrierung im Azure-Portal mit hervorgehobener Schaltfläche „URI hinzufügen“ und dem hervorgehobenen Menü „Authentifizierung“."::: 

Geben Sie die **Umleitungs-URL** des benutzerdefinierten Connectors in das neue Feld ein, und wählen Sie das Symbol **Speichern** aus.

:::image type="content" source="media/how-to-integrate-logic-apps/save-uri.png" alt-text="Screenshot der Seite „Authentifizierung“ für die App-Registrierung im Azure-Portal mit hervorgehobener neuer Umleitungs-URL und Schaltfläche „Speichern“.":::

Sie sind nun fertig mit dem Einrichten eines benutzerdefinierten Connectors, der auf die Azure Digital Twins APIs zugreifen kann. 

## <a name="create-logic-app"></a>Erstellen einer Logik-App

Nun erstellen Sie eine Logik-App, die Ihren neuen Connector zum Automatisieren von Azure Digital Twins-Updates verwendet.

Suchen Sie im [Azure-Portal](https://portal.azure.com) über die Suchleiste nach **Logik-Apps**. Durch Auswählen dieser Option gelangen Sie zur Seite **Logic-Apps**. Wählen Sie **„+ Hinzufügen“** , um eine neue logische Anwendung zu erstellen.

:::image type="content" source="media/how-to-integrate-logic-apps/create-logic-app.png" alt-text="Screenshot der Seite „Logic Apps“ im Azure-Portal mit hervorgehobener Schaltfläche „Logik-App erstellen“.":::

Geben Sie auf der anschließend angezeigten Seite **Logik-App** Ihr Abonnement und Ihre Ressourcengruppe ein. Wählen Sie unter **„Instanzdetails“** einen **Verbrauchsinstanztyp** aus, wählen Sie einen Namen für Ihre logische Anwendung und wählen Sie den Bereitstellungsort. Wählen Sie, ob Sie die Protokollanalyse aktivieren oder deaktivieren möchten.

Wählen Sie die Schaltfläche _Überprüfen + erstellen_ aus.

Dadurch gelangen Sie auf die Registerkarte **„Überprüfen + Erstellen“** , wo Sie Ihre Angaben überprüfen und unten auf **„Erstellen“** klicken können, um Ihre Ressource zu erstellen.

Sie gelangen zur Bereitstellungsseite für die Logik-App. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf die Schaltfläche **„Zur Ressource gehen“** , um zum **Logic Apps Designer** zu gelangen, wo Sie die Logik des Workflows ausfüllen können.

### <a name="design-workflow"></a>Entwerfen des Workflows

Wählen Sie im Logic Apps-Designer unter **Starten Sie mit einem gängigen Trigger** die Option **Serie** aus.

:::image type="content" source="media/how-to-integrate-logic-apps/logic-apps-designer-recurrence.png" alt-text="Screenshot der Seite „Designer für Logik-Apps“ im Azure-Portal mit hervorgehobenem allgemeinem Trigger „Wiederholung“.":::

Ändern Sie auf der Seite Designer für Logik-Apps, die anschließend angezeigt wird, die Häufigkeit der **Wiederholung** in **Sekunde**, sodass das Ereignis alle 3 Sekunden ausgelöst wird. Wenn Sie diese Häufigkeit wählen, können Sie die Ergebnisse später leicht einsehen, ohne lange warten zu müssen.

Wählen Sie **+ Neuer Schritt** aus.

Daraufhin öffnet sich ein Feld Aktion auswählen. Wechseln Sie zur Registerkarte **Benutzerdefiniert**. Der zuvor erstellte benutzerdefinierte Connector sollte im oberen Feld angezeigt werden.

:::image type="content" source="media/how-to-integrate-logic-apps/custom-action.png" alt-text="Screenshot des Erstellens eines Flows im Designer für Logik-Apps im Azure-Portal, wobei der benutzerdefinierte Connector hervorgehoben ist.":::

Wählen Sie diese Option aus, um die Liste der in diesem Connector enthaltenen APIs anzuzeigen. Verwenden Sie die Suchleiste, oder scrollen Sie durch die Liste, um **DigitalTwins_Add** auszuwählen. (Die Aktion **„DigitalTwins_Add“** ist der API-Aufruf, der in diesem Artikel verwendet wird, aber Sie können auch jede andere API als gültige Wahl für eine Logic Apps-Verbindung auswählen).

Sie werden möglicherweise aufgefordert, sich mit ihren Azure-Anmeldeinformationen anzumelden, um eine Verbindung mit dem Connector herzustellen. Wenn ein Dialogfeld **Angeforderte Berechtigungen** angezeigt wird, folgen Sie den Eingabeaufforderungen, um die Einwilligung für Ihre App zu erteilen und zu akzeptieren.

Füllen Sie die Felder im neuen Feld **DigitalTwinsAdd** wie folgt aus:
* id: Geben Sie die **Zwillings-ID** des digitalen Zwillings in Ihrer Instanz ein, den die Logik-App aktualisieren soll.
* twin: In dieses Feld geben Sie den Text ein, der für die ausgewählte API-Anforderung erforderlich ist. Für **DigitalTwinsUpdate** ist dieser Text JSON-Patchcode. Weitere Informationen zum Strukturieren eines JSON-Patches zum Aktualisieren Ihres Zwillings finden Sie im Abschnitt [Aktualisieren eines digitalen Zwillings](how-to-manage-twin.md#update-a-digital-twin) von *Vorgehensweise: Verwalten digitaler Zwillinge*.
* api-version: Die neueste API-Version. Derzeit ist dieser Wert *2020-10-31*.

Wählen Sie im Logik-App-Designer die Option **Speichern** aus.

Sie können weitere Vorgänge auswählen, indem Sie im gleichen Fenster _+ Neuer Schritt_ auswählen.

:::image type="content" source="media/how-to-integrate-logic-apps/save-logic-app.png" alt-text="Screenshot der fertigen Ansicht der App im Logik-App-Connector. Das Feld „DigitalTwinsAdd“ enthält die oben beschriebenen Werte.":::

## <a name="query-twin-to-see-the-update"></a>Abfragen des Zwillings, um das Update anzuzeigen

Nachdem Ihre Logik-App erstellt wurde, sollte das im Designer für Logik-Apps definierte Zwillingsaktualisierungsereignis alle drei Sekunden auftreten. Diese konfigurierte Frequenz bedeutet, dass Sie nach drei Sekunden Ihren Zwilling abfragen können und die neuen gepatchten Werte angezeigt werden.

Sie können Ihren Zwilling mit der Methode Ihrer Wahl abfragen (z. B. mit einer [benutzerdefinierten Client-App](tutorial-command-line-app.md), dem [Azure Digital Twins-Explorer](concepts-azure-digital-twins-explorer.md), den [SDKs und APIs](concepts-apis-sdks.md) oder der [CLI](concepts-cli.md)). 

Weitere Informationen zum Abfragen ihrer Azure Digital Twins-Instanz finden Sie unter [Abfragen des Zwillingsdiagramms](how-to-query-graph.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Logik-App erstellt, die regelmäßig einen Zwilling in Ihrer Azure Digital Twins-Instanz mit einem von Ihnen bereitgestellten Patch aktualisiert. Sie können die Auswahl anderer APIs im benutzerdefinierten Connector ausprobieren, um Logic Apps für verschiedene Aktionen auf Ihrer Instanz zu erstellen.

Weitere Informationen zu den verfügbaren API-Vorgängen und den dazu erforderlichen Details finden Sie unter [Azure Digital Twins-APIs und SDKs](concepts-apis-sdks.md).
