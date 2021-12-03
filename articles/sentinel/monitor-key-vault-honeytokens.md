---
title: Bereitstellung und Überwachung von Azure Key Vault-Honeytokens mit Microsoft Sentinel
description: Setzen Sie Azure Key Vault Honeytoken-Schlüssel und Geheimnisse und überwachen diese mit Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: d63dd9b3b8f7b81b5955ce431cd5477d9e63ea1a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517024"
---
# <a name="deploy-and-monitor-azure-key-vault-honeytokens-with-microsoft-sentinel-public-preview"></a>Stellen Sie Azure Key Vault Honeytoken bereit und überwachen sie mit Microsoft Sentinel (öffentliche Vorschau)

> [!IMPORTANT]
> Die Lösung Microsoft Sentinel Deception (Honeytoken) befindet sich derzeit in der VORSCHAU. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

In diesem Artikel wird beschrieben, wie Sie die Lösung **Microsoft Sentinel Deception (Honeytoken)** zur Täuschung verwenden, um [Azure Key Vault](/azure/key-vault/) Lockvogel-Schlüssel und Geheimnisse, die als *Honeytoken* bezeichnet werden, in vorhandene Workloads einzubringen.

Verwenden Sie [die Analyseregeln](detect-threats-built-in.md), [Watchlists](watchlists.md)und [Arbeitsmappen](monitor-your-data.md), die von der Lösung bereitgestellt werden, um den Zugriff auf die bereitgestellten Honeytoken zu überwachen.

Wenn Sie Honeytoken in Ihrem System verwenden, bleiben die Erkennungsprinzipien unverändert. Da es keinen legitimen Grund für den Zugriff auf einen Honeytoken gibt, deutet jede Aktivität auf das Vorhandensein eines Benutzers hin, der nicht mit der Umgebung vertraut ist und möglicherweise ein Angreifer sein könnte.

## <a name="before-you-begin"></a>Voraussetzungen

Um mit der Verwendung der Lösung **Microsoft Sentinel Deception (Honeytoken)** zu beginnen, stellen Sie sicher, dass Sie über die folgenden Komponenten verfügen:

- **Erforderliche Rollen**: Sie müssen Mandantenadministrator sein, um die Lösung **Microsoft Sentinel Deception (Honeytoken)** zu installieren. Sobald die Lösung installiert ist, können Sie die Arbeitsmappe für Key Vault-Besitzer freigeben, damit sie ihre eigenen Honeytoken bereitstellen können.

- **Erforderliche Datenconnectors:** Stellen Sie sicher, dass Sie die [Azure Key Vault](data-connectors-reference.md#azure-key-vault) und die [ Azure-Aktivitätsdatenconnectors](data-connectors-reference.md#azure-activity) in Ihrem Arbeitsbereich bereitgestellt haben und dass sie verbunden sind.

  Vergewissern Sie sich, dass das Datenrouting erfolgreich war und die **KeyVault** und **AzureActivity**-Daten zu Microsoft Sentinel fließen. Weitere Informationen finden Sie unter

  - [Verbinden von Microsoft Sentinel mit Azure-, Windows-, Microsoft- und Amazon-Diensten](connect-azure-windows-microsoft-services.md?tabs=AP#diagnostic-settings-based-connections)
  - [Suchen Ihres Microsoft Sentinel-Datenconnectors](data-connectors-reference.md)

## <a name="install-the-solution"></a>Installieren der Lösung

Installieren Sie die **Microsoft Sentinel Deception (Honeytoken)** Lösung so wie [jede andere Lösung](sentinel-solutions-deploy.md) auch. Wählen Sie auf der Seite für die **Azure Sentinel Deception** Lösung die Option **Start**, um zu beginnen.

:::image type="content" source="media/monitor-key-vault-honeytokens/honeytoken-create-solution.png" alt-text="Screenshot der Erstellungsvalidierungsseite.":::

**So installieren Sie die Lösung Deception**:

In den folgenden Schritten werden bestimmte Aktionen beschrieben, die für die Lösung **Microsoft Sentinel Deception (Honeytoken)** erforderlich sind.

1. Wählen Sie auf der Registerkarte **Grundlagen** dieselbe Ressourcengruppe aus, in der sich Ihr Microsoft Sentinel Arbeitsbereich befindet.

1. Geben Sie auf der Registerkarte **Voraussetzungen** im Feld **Name der Funktions-App** einen aussagekräftigen Namen für die Azure-Funktions-App ein, die Honeytoken in Ihren Schlüsseltresoren erstellt.

    Der Name der Funktions-App muss eindeutig sein, zwischen 2 und 22 Zeichen lang, und darf nur alphanumerische Zeichen enthalten.

    Unten wird ein Befehl mit dem von Ihnen definierten Namen angezeigt. Beispiel:

    :::image type="content" source="media/monitor-key-vault-honeytokens/prerequisites.png" alt-text="Screenshot der Registerkarte &quot;Voraussetzungen&quot; mit dem aktualisierten curl-Befehl.":::

1. Wählen Sie **Hier Klicken, um eine Cloud Shell zu öffnen**,damit Sie eine Cloud Shell Registerkarte öffnen können. Melden Sie sich an, wenn Sie dazu aufgefordert werden, und führen Sie dann den angezeigten Befehl aus.

    Das Skript, das Sie ausführen, erstellt eine Azure AD-Funktions-App (AAD), die Ihre Honeytoken bereitstellen wird.    Beispiel:

    ```bash
    Requesting a Cloud Shell.Succeeded
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    maria@Azure:~$curl -sL https://aka.ms/sentinelhoneytokensappcreate | bash -s HoneyTokenFunctionApp
    ```

    Die Skriptausgabe enthält die AAD App-ID und das Geheimnis. Beispiel:

    ```bash
    WARNING: The output includes credentials that you must protect. Be sure that you do not include these credentials in your code or check the credentials into your source control. For more information, see https://aka.ms/azadsp-cli
    function app name: HoneyTokenFunctionApp
    AAD App Id: k4js48k3-97gg-3958=sl8d=48620nne59k4
    AAD App secret: v9kUtSoy3u~K8DKa8DlILsCM_K-s9FR3Kj
    maria@Azure:~$
    ```

1. Geben Sie Microsoft Sentinel unten auf der Registerkarte **Voraussetzungen** ein und die AAD App-ID sowie das Geheimnis in die relevanten Felder. Beispiel:

    :::image type="content" source="media/monitor-key-vault-honeytokens/client-app-secret-values.png" alt-text="Screenshot der Client-App-Funktion mit hinzugefügten Geheimniswerten.":::

1. Wählen Sie **Hier Klicken, um in den Einstellungen Ihrer Funktions-App fortzufahren** unter Schritt 4. Eine neue Browserregisterkarte wird in den Azure AD Einstellungen geöffnet.

    Melden Sie sich an, wenn Sie dazu aufgefordert werden, und wählen Sie dann **Administratoreinwilligung`<your directory name>` erteilen für** , um fortzufahren. Beispiel:

    :::image type="content" source="media/monitor-key-vault-honeytokens/grant-admin-access.png" alt-text="Screenshot der Schaltfläche &quot;Administratoreinwilligung für Ihr Verzeichnis erteilen.":::

    Für weitere Informationen, siehe [Erteilen der Administratoreinwilligung in App-Registrierungen](/azure/active-directory/manage-apps/grant-admin-consent).

1. Wieder im Microsoft Sentinel auf den Registerkarten **Arbeitsmappen**, **Analyse**, **Watchlists** und **Playbooks**, beachten Sie die Sicherheitsinhalte, die erstellt werden, und ändern Sie die Namen nach Bedarf.

    > [!NOTE]
    > Weitere Anweisungen in diesem Artikel beziehen sich auf die Arbeitsmappen **HoneyTokensIncidents** und **SOCHTManagement**. Wenn Sie die Namen dieser Arbeitsmappen ändern, achten Sie darauf, die neuen Arbeitsmappennamen für Ihre eigenen Referenzzwecke zu notieren und sie nach Bedarf anstelle der Standardnamen zu verwenden.

1. Definieren Sie auf der **Azure Functions** Registerkarte die folgenden Werte:

    **Konfiguration des Schlüsseltresors**: In den folgenden Feldern werden Werte für den Schlüsseltresor definiert, in dem Sie das App Geheimnis Ihrer AAD speichern. Diese Felder definieren dabei *nicht* den Schlüsseltresor, in dem Sie Honeytoken bereitstellen werden.

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    | **Service-Plan**     |   Wählen Sie aus, ob Sie einen **Premium** oder **Verbrauchs** Plan für Ihre Funktions-App verwenden möchten. Weitere Informationen zu diesem Plan finden Sie unter [Hosting des Azure Functions Verbrauchstarifs](/azure/azure-functions/consumption-plan) und [Azure Functions Premium-Tarif](/azure/azure-functions/functions-premium-plan).      |
    | **Soll ein neuer KeyVault erstellt werden**     |    Wählen Sie **neu** aus, um einen neuen Schlüsseltresor für das Geheimnis Ihrer App zu erstellen, oder einen **vorhandenen**, um einen bereits existierenden Schlüsseltresor zu verwenden.   |
    | **KeyVault-Name**     | Wird nur angezeigt, wenn Sie ausgewählt haben, einen neuen Schlüsseltresor zu erstellen. <br><br>Geben Sie den Namen des Schlüsseltresors ein, den Sie zum Speichern des Geheimnisses Ihrer App verwenden möchten. Dieser Name muss global eindeutig sein.     |
    | **KeyVault** Ressourcengruppe     |Wird nur angezeigt, wenn Sie ausgewählt haben, einen neuen Schlüsseltresor zu erstellen. <br><br> Wählen Sie den Namen der Ressourcengruppe aus, in der Sie den Schlüsseltresor für Ihren Anwendungsschlüssel speichern möchten.      |
    | **Bstehende Key Vaults werden aufgelistet** | Wird nur angezeigt, wenn Sie ausgewählt haben, einen vorhandenen Schlüsseltresor zu verwenden. Wählen Sie den Schlüsseltresor aus, den Sie verwenden möchten. |
    | **KeyVault Geheimnis-Name**     |   Geben Sie den Namen des Geheimnisses ein, das zum Speichern des Clientgeheimnisses verwendet wird.      |

    **Honeytokenkonfiguration**: Die folgenden Felder definieren Einstellungen, die für die Schlüssel und Geheimnisse benutzt werden, die in Ihren Honeytoken verwendet werden. Verwenden Sie Namenskonventionen, die mit den Benennungsanforderungen Ihrer Organisation kombiniert werden, damit Angreifer den Unterschied nicht erkennen können.

    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |**Schlüssel für Schlüsselwörter**     |  Geben Sie durch Komma getrennte Listen von Werten ein, die Sie mit Ihren Lockvogel-Honeytokennamen verwenden möchten.  Beispiel: `key,prod,dev`.  Werte dürfen nur alphanumerisch sein.   |
    |**Geheimnisse**     |   Geben Sie durch Komma getrennte Listen von Werten ein, die Sie mit Ihren Lockvogel-Honeytokengeheimnissen verwenden möchten.  Beispiel: `secret,secretProd,secretDev`. Werte dürfen nur alphanumerisch sein.    |
    |**Zusätzliche HoneyToken-Wahrscheinlichkeit**     |  Geben Sie einen Wert im Bereich von `0` und `1` ein, wie beispielsweise `0.6`. Dieser Wert definiert die Wahrscheinlichkeit, dass mehr als ein Honeytoken dem Key Vault hinzugefügt wird.       |
    |     |         |

1. Wählen Sie **Weiter: Überprüfen + Erstellen** aus, um die Installation Ihrer Lösung fertigzustellen.

    Nach der Installation der Projektmappe werden die folgenden Elemente angezeigt:

    - Ein Link zu Ihrer **SOCHTManagement**-Arbeitsmappe. Möglicherweise haben Sie diesen Namen weiter oben in diesem Verfahren auf der Registerkarte **Arbeitsmappen** geändert.

    - Der URL für eine benutzerdefinierte ARM-Vorlage. Sie können diese ARM-Vorlage verwenden, um eine Azure Policy-Initiative bereitzustellen, die mit einer benutzerdefinierten Microsoft Defender für Cloud-Empfehlung verbunden ist, welche die **SOCHTManagement**-Arbeitsmappe an Key Vault-Besitzer in Ihrer Organisation verteilt.

1. Auf der Registerkarte **Schritte nach der Bereitstellung** wird darauf hingewiesen, dass Sie die in der Bereitstellungsausgabe angezeigten Informationen verwenden können, um die benutzerdefinierte Microsoft Defender für Cloud-Empfehlung an alle Key Vault-Besitzer in Ihrer Organisation zu verteilen und zu empfehlen, Honeytoken in ihren Schlüsseltresoren bereitzustellen.

    Verwenden Sie den [URL der benutzerdefinierten ARM-Vorlage](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2faka.ms%2fsentinelhoneytokenspolicy), die in der Installationsausgabe angezeigt wird, um die Seite **Benutzerdefinierte Bereitstellung** der verknüpften Vorlage zu öffnen.

    Weitere Informationen finden Sie unter [Verteilen der SOCHTManagement-Arbeitsmappe](#distribute-the-sochtmanagement-workbook).

## <a name="deploy-your-honeytokens"></a>Bereitstellen Ihrer Honeytoken

Nachdem Sie die Lösung **Microsoft Sentinel Deception (Honeytoken)** installiert haben, können Sie mit der Bereitstellung von Honeytoken in Ihren Schlüsseltresoren beginnen, indem Sie die Schritte in der **SOCHTManagement**-Arbeitsmappe ausführen.

Wir empfehlen, dass Sie die **SOCHTManagement**-Arbeitsmappe für Key Vault-Besitzer in Ihrer Organisation freigeben, damit diese ihre eigenen Honeytoken in ihren jeweiligen Schlüsseltresoren erstellen können. Möglicherweise haben Sie diese Arbeitsmappe bei der [Installation der Projektmappe](#install-the-solution) umbenannt. Achten Sie bei der Freigabe darauf, nur Leseberechtigungen zu erteilen.

**Stellen Sie Honeytoken in Ihren Schlüsseltresoren bereit**:

1. Wechseln Sie in Microsoft Sentinel zu **Arbeitsmappen > Meine Arbeitsmappen** und öffnen Sie die Arbeitsmappe **SOCHTManagement.** Möglicherweise haben Sie diesen Namen bei der Bereitstellung der Lösung geändert.

1. Wählen Sie **Gespeicherte Arbeitsmappe anzeigen** >  **aus und fügen als vertrauenswürdig** hinzu. Beispiel:

    :::image type="content" source="media/monitor-key-vault-honeytokens/add-as-trusted.png" alt-text="Screenshot: Schaltfläche &quot;Als vertrauenswürdig hinzufügen&quot; in der SOCHTManagement-Arbeitsmappe.":::

    Die Infrastruktur wird in Ihren Schlüsseltresoren bereitgestellt, um die Honeytokenbereitstellung zu ermöglichen.

1. Erweitern Sie auf der **Key Vault**-Registerkarte der Arbeitsmappe Ihr Abonnement, um die Schlüsseltresore für die Bereitstellung von Honeytoken und alle Schlüsseltresore mit schon bereitgestellten Honeytoken anzeigen zu können.

    In der Spalte **Von SOC überwacht** :::image type="icon" source="media/monitor-key-vault-honeytokens/checkmark.png" border="false":::gibt ein grünes Häkchen an, dass der Schlüsseltresor bereits über Honeytoken verfügt. Ein rotes X-Zeichen :::image type="icon" source="media/monitor-key-vault-honeytokens/xmark.png" border="false":::zeigt an, dass der Schlüsseltresor noch keine Honeytoken enthält. Beispiel:

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-deployed.png" alt-text="Screenshot der SOCHTManagement-Arbeitsmappen mit bereitgestellten Honeytokens.":::

1. Scrollen Sie auf der Arbeitsmappenseite nach unten, und verwenden Sie die Anweisungen und Links im Abschnitt **Ausführen einer Aktion**, um Honeytoken für alle Schlüsseltresore in großem Umfang bereitzustellen, oder setzen Sie sie manuell nacheinander.

    # <a name="deploy-at-scale"></a>[Bedarfsorientiertes Bereitstellen](#tab/deploy-at-scale)

    **So stellen Sie Honeytoken in großen Umfang bereit**:

    1. Wählen Sie den Link **Benutzer aktivieren** aus, um eine ARM-Vorlage bereitzustellen, die eine Key Vault-Zugriffsrichtlinie bereitstellt, und gewähren Sie der Benutzer-ID spezifizierte Rechte zum Erstellen der Honeytoken.

        Melden Sie sich an, wenn Sie dazu aufgefordert werden, und geben Sie Werte für **Projektdetails** und **Instanzdetails** für Ihre ARM-Vorlagenbereitstellung ein. Suchen Sie **Ihre Mandanten-ID** und **Benutzerobjekt-ID** auf der Azure Active Directory-Startseite für Ihre Benutzer.

        Wenn Sie fertig sind, klicken Sie auf **Überprüfen + Erstellen**, um die ARM-Vorlage bereitzustellen. Beispiel:

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-arm-template.png" alt-text="Screenshot: Seite „Benutzerdefinierte Bereitstellung“":::

        Ihre Einstellungen werden überprüft, und wenn die Überprüfung erfolgreich ist, wird eine Bestätigung angezeigt: **Überprüfung erfolgreich**

        Wählen Sie unten auf der Seite **Erstellen** aus, um Ihre ARM-Vorlage bereitzustellen, und sehen Sie nach einer Bestätigungsseite für die erfolgreiche Bereitstellung.

    1. Zurück in Microsoft Sentinel in ihrer **SOCHTManagement**-Arbeitsmappe > **Tätig werden** >  **Skalierte Bereitstellung**, wählen Sie den Link  **Klicken zur Bereitstellung**, um Honeytoken zu allen Schlüsseltresoren hinzuzufügen, auf die Sie im ausgewählten Abonnement Zugriff haben.

        Wenn Sie fertig sind, werden ihre Honeytoken-Bereitstellungsergebnisse in einer Tabelle auf einer neuen Registerkarte angezeigt.

    1. Stellen Sie sicher, dass Sie den Link **Benutzer deaktivieren** auswählen, um die zuvor erstellte Zugriffsrichtlinie zu entfernen. Melden Sie sich erneut an, wenn Sie dazu aufgefordert werden, geben Sie Werte für Ihre benutzerdefinierte ARM-Bereitstellung ein, und stellen Sie dann die ARM-Vorlage bereit. In diesem Schritt wird eine Key Vault-Zugriffsrichtlinie bereitgestellt, welche die Benutzerrechte zum Erstellen von Schlüsseln und Geheimnissen entfernt.

    # <a name="deploy-a-single-honeytoken"></a>[Bereitstellen eines einzelnen Honeytoken](#tab/deploy-a-single-honeytoken)

    **So stellen Sie einen einzelnen Honeytoken manuell bereit**:

    1. Wählen Sie in der Tabelle oben auf der Seite den Schlüsseltresor aus, in dem Sie Ihren Honeytoken bereitstellen möchten. Der Abschnitt **In einem bestimmten Schlüsseltresor bereitstellen:** wird unten auf der Seite angezeigt.

    1. Scrollen Sie nach unten, und wählen Sie in der Dropdownliste **Honeytoken-Typ** aus, ob Sie einen Schlüssel oder ein Geheimnis erstellen möchten. Geben Sie **im Feld Neuer Honeytokenname** einen aussagekräftigen Namen für Ihren Honeytoken ein. Beispiel:

        :::image type="content" source="media/monitor-key-vault-honeytokens/deploy-manually.png" alt-text="Screenshot: Bereitstellung in einem bestimmten Schlüsseltresorbereich.":::

    1. Erweitern Sie in der Tabelle **Operation** den Abschnitt **Honeytoken Bereitstellen**, und wählen Sie jeden Aufgabennamen aus, um die erforderlichen Schritte auszuführen. Melden Sie sich an, wenn Sie dazu aufgefordert werden.

        - Wählen Sie **Klicken, um zu überprüfen, ob der Schlüsseltresor überwacht wurde**. Vergewissern Sie sich im Azure Key Vault, dass Ihre Key Vault-Diagnoseeinstellungen so festgelegt sind, dass Überwachungsereignisse an Log Analytics gesendet werden.
        - Wählen Sie **Benutzer in der Richtlinie des Schlüsseltresors aktivieren aus, wenn er dort fehlt**. Stellen Sie im Azure Key Vault sicher, dass Ihr Benutzer Zugriff zum Bereitstellen von Honeytoken an Ihren erforderlichen Standorten hat. Wählen Sie **Speichern** aus, um die Änderungen zu speichern.
        - Wählen Sie **Klicken, um dem Schlüsseltresor einen Honeytoken hinzuzufügen**, um den Azure Key Vault zu öffnen. Fügen Sie dem konfigurierten Schlüsseltresor einen neuen Honeytoken so hinzu wie ein neues Geheimnis.
        - Wählen Sie **Klicken, um die Überwachung im SOC hinzuzufügen**. Bei Erfolg wird auf einer neuen Registerkarte eine Bestätigungsmeldung angezeigt:`Honey-token was successfully added to monitored list`.

        Weitere Informationen finden Sie in der [Dokumentation zu Azure Key Vault](/azure/key-vault/secrets/about-secrets).

    > [!NOTE]
    > Stellen Sie sicher, dass Sie den Link **Ihren Benutzer in der Richtlinie des Key-Vaults ggf. wieder deaktivieren** wählen, um die Zugriffsrichtlinien zu entfernen, die Rechte zur Erstellung der Honigtoken gewähren.
    >

    # <a name="remove-a-honeytoken"></a>[Entfernen eines Honeytokens](#tab/remove-a-honeytoken)

    **So entfernen Sie einen bestimmten Honeytoken**:

    1. Wählen Sie in der Tabelle oben auf der Seite den Schlüsseltresor aus, in dem Sie Ihren Honeytoken bereitstellen möchten. Der Abschnitt **In einem bestimmten Schlüsseltresor bereitstellen:** wird unten auf der Seite angezeigt.

    1. Erweitern Sie in der Tabelle **Operation** den Abschnitt **Honeytoken Bereitstellen**, und wählen Sie jeden Aufgabennamen aus, um die erforderlichen Schritte auszuführen. Melden Sie sich an, wenn Sie dazu aufgefordert werden.

        - Wählen Sie **Klicken, um den Honeytoken aus dem Schlüsseltresor zu löschen**, um den Azure Key Vault für die Seite zu öffnen, auf der Sie Ihren Honeytoken entfernen können.
        - Wählen Sie **E-Mail senden aus, um den SOC zu aktualisieren**. In Ihrem Standard-E-Mail-Client wird eine E-Mail an den SOC geöffnet, in der empfohlen wird, die Honeytokenüberwachung für den ausgewählten Schlüsseltresor zu entfernen.

    > [!TIP]
    > Wir empfehlen, eindeutig mit Ihrem SOC über Honeytoken zu kommunizieren, die Sie löschen.
    >

    ---

Möglicherweise müssen Sie einige Minuten warten, während die Daten aufgefüllt und die Berechtigungen aktualisiert werden. Aktualisieren Sie die Seite, um alle Updates in Ihrer Key Vault-Bereitstellung anzuzeigen.

## <a name="test-the-solution-functionality"></a>Testen der Lösungsfunktionalität

**Um zu testen, ob Sie tatsächlich bei jedem Zugriffsversuch auf Ihre Honeytoken benachrichtigt werden**:

1. Wählen Sie auf der Microsoft Sentinel Seite **Watchlists** die Registerkarte **Meine Watchlists** und dann die Watchlist **HoneyTokens** aus.

    Wählen Sie **In Log Analytics Anzeigen** aus, um eine Liste der aktuellen gefundenen Honeytokenwerte zu erhalten. Die Elemente in Ihrer Watchlist werden automatisch für Ihre Abfrage extrahiert und auf der Registerkarte **Logs** angezeigt. Beispiel:

    :::image type="content" source="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png" alt-text="Screenshot der Honeytoken-Watchlistwerte im Log Analytics." lightbox="media/monitor-key-vault-honeytokens/honeytokens-watchlist.png":::

    Weitere Informationen finden Sie unter [Verwenden von Microsoft Sentinel-Watchlists](watchlists.md).

1. Wählen Sie in der Liste in Log Analytics einen zu testenden Honeytokenwert aus.

    Wechseln Sie dann zu Azure Key Vault, und laden Sie den öffentlichen Schlüssel herunter, oder zeigen Sie das Geheimnis für den ausgewählten Honeytoken an.

    Wählen Sie beispielsweise Ihren Honeytoken und dann **Öffentlichen Schlüssel herunterladen** aus. Mit dieser Aktion wird ein `KeyGet` oder `SecretGet`-Protokoll erstellt, das eine Warnung in Microsoft Sentinel auslöst.

    Weitere Informationen finden Sie in der [Key Vault](/azure/key-vault/)-Dokumentation.

1. Zurück in Microsoft Sentinel, wechseln Sie zur Seite **Incidents**. Möglicherweise müssen Sie etwa fünf Minuten warten, aber sie sollten einen neuen Vorfall sehen, beispielsweise mit dem Namen **HoneyToken: Zugriff auf Schlüssel für KeyVault HoneyToken**.

    Wählen Sie den Vorfall aus, um seine Details anzuzeigen, z. B. den ausgeführten Schlüsselvorgang, den Benutzer, der auf den Honeytokenschlüssel zugegriffen hat, und den Namen des kompromittierten Schlüsseltresors.

    > [!TIP]
    > Jeder Zugriff oder Vorgang mit den Honeytokenschlüsseln und Geheimnissen generiert Vorfälle, die Sie in der Microsoft Sentinel untersuchen können. Da es keinen Grund gibt, Honeytokenschlüssel und Geheimnisse tatsächlich zu verwenden, kann jede ähnliche Aktivität in Ihrem Arbeitsbereich bösartig sein und sollte untersucht werden.

1. Zeigen Sie die Honeytokenaktivität in der **Arbeitsmappe HoneyTokensIncident** an. Suchen Sie auf der Microsoft Sentinel Seite **Arbeitsmappen** nach der Arbeitsmappe **HoneyTokensIncident**, und öffnen Sie diese.

    Diese Arbeitsmappe zeigt alle Honeytoken-bezogenen Incidents, die zugehörigen Entitäten, kompromittierten Schlüsseltresore, ausgeführte Schlüsselvorgänge und den Zugriff auf Honeytoken an.

    Wählen Sie bestimmte Incidents und Vorgänge aus, um alle zugehörigen Aktivitäten weitergehend zu untersuchen.

## <a name="distribute-the-sochtmanagement-workbook"></a>Verteilen der SOCHTManagement-Arbeitsmappe

Wir empfehlen, Honeytoken in so vielen Schlüsseltresoren wie möglich bereitzustellen, um optimale Erkennungsfähigkeiten in Ihrer Organisation sicherzustellen.

Viele SOC-Teams haben jedoch keinen Zugriff auf Schlüsseltresore. Um diese Lücke zu schließen, sollten Sie die **SOCHTManagement**-Arbeitsmappe an alle Schlüsseltresorbesitzer in Ihrem Mandanten verteilen, damit Ihre SOC-Teams ihre eigenen Honeytoken bereitstellen können. Möglicherweise haben Sie den Namen dieser Arbeitsmappe geändert, als Sie die [Projektmappe installiert](#install-the-solution) haben.

Sie können jederzeit den direkten Link zur Arbeitsmappe freigeben. Altenativ können Sie diese ARM-Vorlage verwenden, um eine Azure Policy-Initiative bereitzustellen, die mit einer benutzerdefinierten Microsoft Defender für Cloud-Empfehlung verbunden ist, welche die **SOCHTManagement**-Arbeitsmappe an Key Vault-Besitzer in Ihrer Organisation verteilt.

> [!NOTE]
> Achten Sie bei der Verteilung der Arbeitsmappe darauf, nur Lesezugriff zu gewähren.
>

**So verteilen Sie die SOCHTManagement-Arbeitsmappe per Azure Policy Initiative**

1. Wählen Sie in der folgenden Tabelle die Schaltflächen **In Azure Bereitstellen** aus, um die ARM-Vorlage auf der Seite **Benutzerdefinierte Bereitstellung** zu öffnen, je nachdem, wie Sie die ARM-Vorlage bereitstellen möchten.  Verwenden Sie die GitHUb-Links, um die Details zu den in der ARM-Vorlage enthaltenen Informationen anzuzeigen oder die ARM-Vorlage für Ihre Umgebung anzupassen.

    Die Schaltflächen **In Azure Bereitstellen** verwenden dieselben URLs, die auf der Registerkarte **Ausgabe** nach der [Installation der Lösung](#install-the-solution) angezeigt werden.

    | Bereitstellungsoption | BESCHREIBUNG | In Azure bereitstellen | GitHub-Link |
    |-------------------|-------------|-------------|-----------------|
    | Verwaltungsgruppe | Empfohlen für die unternehmensweite Bereitstellung| [![DTA-Button-MG]][DTA-MG]  |[Beispiel auf GitHub][GitHub-MG] |
    | Subscription | Empfohlen zum Testen in einem einzelnen Abonnement | [![DTA-Button-Sub]][DTA-Sub]  | [Beispiel auf GitHub][GitHub-Sub] |

    Melden Sie sich an, wenn Sie dazu aufgefordert werden.

1. Wählen Sie auf der Registerkarte **Bereitstellung einer Lockvogel-Lösungs-Richtlinie** > **Grundlagen** der ARM-Vorlage den Wert für Ihre Verwaltungsgruppe und die Region aus. Wählen Sie dann **Weiter: Bereitstellungsziel->** , um fortzufahren.

1. Wählen Sie auf der Registerkarte **Bereitstellungsziel** erneut Ihre Verwaltungsgruppe und dann **Nex: Management Workbook >** .

1. Fügen Sie **auf der Registerkarte Verwaltungsarbeitsmappe** den Link zu Ihrer **SOCHTManagement-Arbeitsmappe** ein.

    Sie finden den Arbeitsmappenlink aus der Arbeitsmappe **SOCHTManagement** in Microsoft Sentinel, und er ist auch auf der Registerkarte Ausgabe der Lösungsbereitstellung **enthalten**.

    Um beispielsweise den Link in der Arbeitsmappe zu finden, wählen Sie **Arbeitsmappen** > **Meine Arbeitsmappen** > **SOCHTManagement** und dann auf der Symbolleiste **Link** kopieren aus.

1. Klicken Sie nach dem Eingeben des Arbeitsmappenlinks auf **Weiter: Überprüfen +  Erstellen>** , um fortzufahren. Warten Sie auf eine Bestätigungsmeldung, dass die Überprüfung erfolgreich war, und wählen Sie dann **Erstellen**.

1. Nach Abschluss der Bereitstellung sehen Sie, dass die Bereitstellung eine neue **HoneyToken-Initiative** und zwei neue Richtlinien mit den Namen **KeyVault HoneyTokens** und **KVReviewTag enthält.** Beispiel:

    :::image type="content" source="media/monitor-key-vault-honeytokens/policy-deployment.png" alt-text="Screenshot einer erfolgreich bereitgestellten ARM-Vorlagenrichtlinie." lightbox="media/monitor-key-vault-honeytokens/policy-deployment.png":::

1. Weisen Sie in Azure **Policy** die neue **KVReviewTag-Richtlinie** dem benötigten Bereich zu. Diese Zuweisung fügt allen Schlüsseltresoren im ausgewählten Bereich den **KVReview-Tag** und den Wert **ReviewNeeded** hinzu.

    1. Klicken Siein Azure Policy im linken Bereich unter **Authoring** (Erstellen) auf **Definitionen**. Suchen Sie ihre **KVReviewTag**-Richtlinienzeile, und wählen Sie das Menü Optionen auf der rechten Seite aus.

    1. Geben Sie auf der Seite **Diagnoseeinstellungen für das Aktivitätsprotokoll auf den Log Analytics-Arbeitsbereich übertragen** die erforderlichen Werte ein, um die Diagnoseeinstellungen für Ihre Umgebung zu bereitzustellen.

        Wählen Sie auf der Registerkarte **Wartung** die Option **Wartungsaufgabe Erstellen** aus, um den Tag auf vorhandene Schlüsseltresore anzuwenden.

    Weitere Informationen finden Sie in der [Dokumentation zu Azure Policy](/azure/governance/policy/assign-policy-portal).

1. Fügen Sie im Azure **Security Center** allen Schlüsseltresoren im ausgewählten Bereich eine Überwachungsempfehlung hinzu:

    1. Wählen Sie **Einhaltung gesetzlicher Bestimmungen > Konformitätsrichtlinien verwalten**, und wählen Sie dann Ihren Bereich aus.

    1. Scrollen Sie auf der Detailseite für den ausgewählten Bereich nach unten, und wählen Sie im Abschnitt Ihre **Benutzerdefinierten Initativen** die Option **Benutzerdefinierte Initiative hinzufügen** aus.

    1. Wählen Sie in der Zeile **HoneyTokens** Initiative die Option **Hinzufügen** aus.

Allen Schlüsseltresoren im ausgewählten Bereich wird eine Überwachungsempfehlung mit einem Link zur Arbeitsmappe **SOCHTManagement** hinzugefügt. Möglicherweise haben Sie diese Arbeitsmappe bei der [Installation der Projektmappe](#install-the-solution) umbenannt.

Weitere Informationen finden Sie in der Dokumentation zu [Microsoft Defender für Cloud](/azure/security-center/security-center-recommendations).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Informationen zu Microsoft Sentinel-Lösungen](sentinel-solutions.md)
- [Entdecken und Bereitstellen von Microsoft Sentinel-Lösungen](sentinel-solutions-deploy.md)
- [Microsoft Sentinel-Lösungskatalog](sentinel-solutions-catalog.md)
- [Standardmäßig verfügbare Erkennung von Bedrohungen](detect-threats-built-in.md)
- [Häufig verwendete Microsoft Sentinel-Arbeitsmappen](top-workbooks.md)

<!-- The following section is used to store references to external images and links to reduce maintenance overhead and enable tooltips -->

[//]: # (*******************************)
[//]: # (EXTERNE BILDVERWEISE: SIEHE UNTEN)
[//]: # (*******************************)

[DTA-Button-MG]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "Bereitstellen von ASC-Policen im Verwaltungsgruppenbereich."
[DTA-Button-Sub ]: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true "Bereitstellen von ASC-Policen im Abonnementbereich."

[//]: # (**************************)
[//]: # (BEZEICHNUNGEN FÜR EXTERNE LINKS: SIEHE UNTEN)
[//]: # (**************************)

[GitHub-MG]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicy.json
[GitHub-Sub]: https://github.com/Azure/Azure-Sentinel/blob/master/Solutions/HoneyTokens/ASCRecommendationPolicySub.json

[DTA-MG]: https://portal.azure.com/#blade/Microsoft_Azure_CreateUIDef/CustomDeploymentBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicy.json/uiFormDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicyUI.json
[DTA-Sub]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Sentinel%2Fmaster%2FSolutions%2FHoneyTokens%2FASCRecommendationPolicySub.json
