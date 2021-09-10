---
title: Erstellen und Verwalten von LUIS-Ressourcen
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Azure-Ressourcen für die LUIS-App verwenden und verwalten.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 07/12/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 4fe1e8cd4c78d4411a7cb9058648fc0e782cb0ee
ms.sourcegitcommit: aaaa6ee55f5843ed69944f5c3869368e54793b48
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/13/2021
ms.locfileid: "113666301"
---
# <a name="how-to-create-and-manage-luis-resources"></a>Erstellen und Verwalten von LUIS-Ressourcen

In diesem Artikel erfahren Sie mehr über die Typen von Azure-Ressourcen, die Sie mit LUIS verwenden können, und wie Sie sie verwalten.

## <a name="authoring-resource"></a>Erstellungsressource

Mit einer Erstellungsressource können Sie Ihre Anwendungen erstellen, verwalten, trainieren, testen und veröffentlichen. Für die LUIS-Erstellungsressource ist ein [Tarif](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) verfügbar, der Tarif „Free“ (F0), der Folgendes bietet:

* 1 Million Erstellungstransaktionen 
* 1\.000 Testanforderungen für den Vorhersageendpunkt pro Monat.

Sie können die [programmgesteuerten LUIS-APIs (v3.0-preview)](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) verwenden, um Erstellungsressourcen zu verwalten. 

## <a name="prediction-resource"></a>Vorhersageressource

Mit einer Vorhersageressource können Sie Ihren Vorhersageendpunkt über die von der Erstellungsressource bereitgestellten 1.000 Anforderungen hinaus abfragen. Für die Vorhersageressource sind zwei [Tarife](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) verfügbar:

* Die Vorhersageressource „Free“ (F0), die Ihnen monatlich 10.000 Anforderungen für den Vorhersageendpunkt bietet.
* Die Vorhersageressource „Standard“ (S0), die der kostenpflichtige Tarif ist. 

Sie können die [LUIS-Endpunkt-API (v3.0-preview)](https://westus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5f68f4d40a511ce5a7440859) verwenden, um Vorhersageressourcen zu verwalten.

> [!Note]
> * Sie können auch eine [Ressource mit mehreren Diensten](../cognitive-services-apis-create-account-cli.md?tabs=multiservice) verwenden, um einen einzelnen Endpunkt abzurufen, den Sie für mehrere Cognitive Services verwenden können.
> * LUIS bietet zwei Arten von F0-Ressourcen (kostenloser Tarif): eine für Erstellungstransaktionen und eine für Vorhersagetransaktionen. Wenn das Kontingent kostenloser Vorhersagetransaktionen erschöpft ist, stellen Sie sicher, dass Sie die F0-Vorhersageressource verwenden, die Ihnen monatlich 10.000 kostenlose Transaktionen bietet, und nicht die Erstellungsressource, die Ihnen monatlich 1.000 Vorhersagetransaktionen bietet.
> * Sie sollten LUIS-Apps in den [Regionen](luis-reference-regions.md#publishing-regions) erstellen, in denen sie veröffentlicht und abgefragt werden sollen.

## <a name="create-luis-resources"></a>Erstellen von LUIS-Ressourcen

Zum Erstellen von LUIS-Ressourcen können Sie das LUIS-Portal, das [Azure-Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) oder die Azure CLI verwenden. Nachdem Sie Ihre Ressourcen erstellt haben, müssen Sie sie Ihren Apps zuweisen, damit sie von diesen verwendet werden können.

# <a name="luis-portal"></a>[LUIS-Portal](#tab/portal)

### <a name="create-a-luis-authoring-resource-using-the-luis-portal"></a>Erstellen einer LUIS-Erstellungsressource über das LUIS-Portal

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, wählen Sie Ihr Land bzw. Ihre Region aus, und stimmen Sie den Nutzungsbedingungen zu. Wenn der Abschnitt **Meine Apps** im Portal angezeigt wird, ist bereits eine LUIS-Ressource vorhanden, und Sie können den nächsten Schritt überspringen.

2. Suchen Sie im angezeigten Fenster zum Auswählen einer Erstellungsressource nach Ihrem Azure-Abonnement und der LUIS-Erstellungsressource. Wenn keine Ressource vorhanden ist, können Sie eine neue erstellen.

    :::image type="content" source="./media/luis-how-to-azure-subscription/choose-authoring-resource.png" alt-text="Typ der Language Understanding-Erstellungsressource auswählen":::
    
    Geben Sie beim Erstellen einer neuen Erstellungsressource die folgenden Informationen an:
    * **Mandantenname**: Der Mandant, dem Ihr Azure-Abonnement zugeordnet ist.
    * **Azure-Abonnementname**: Das Abonnement, unter dem die Ressource abgerechnet wird.
    * **Name der Azure-Ressourcengruppe**: Ein benutzerdefinierter Ressourcengruppenname, den Sie auswählen oder erstellen. Mit Ressourcengruppen können Sie Azure-Ressourcen für den Zugriff und die Verwaltung gruppieren.
    * **Azure-Ressourcenname**: Ein von Ihnen gewählter benutzerdefinierter Name, der als Teil der URL für Ihre Abfragen für Erstellungs- und Vorhersageendpunkte verwendet wird.
    * **Tarif**: Der Tarif bestimmt die maximale Anzahl von Transaktionen pro Sekunde und Monat.

### <a name="create-a-luis-prediction-resource-using-the-luis-portal"></a>Erstellen einer LUIS-Vorhersageressource über das LUIS-Portal

[!INCLUDE [Create LUIS Prediction resource in LUIS portal](./includes/add-prediction-resource-portal.md)]

# <a name="without-luis-portal"></a>[Ohne LUIS-Portal](#tab/without-portal)

### <a name="create-luis-resources-without-using-the-luis-portal"></a>Erstellen von LUIS-Ressourcen ohne Verwendung des LUIS-Portals

Verwenden Sie die [Azure CLI](/cli/azure/install-azure-cli), um die einzelnen Ressourcen individuell zu erstellen.

> [!TIP]
> * Die Erstellungsressource `kind` ist `LUIS.Authoring`.  
> * Die Vorhersageressource `kind` ist `LUIS`. 

1. Melden Sie sich bei der Azure CLI an:

    ```azurecli
    az login
    ```

    Dieser Befehl öffnet einen Browser, in dem Sie das richtige Konto auswählen und die Authentifizierung bereitstellen können.

2. Erstellen Sie eine LUIS-Erstellungsressource der Art `LUIS.Authoring` namens `my-luis-authoring-resource`. Erstellen Sie sie in der _vorhandenen_ Ressourcengruppe namens `my-resource-group` für die Region `westus`.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

3. Erstellen Sie eine LUIS-Vorhersageendpunkt-Ressource der Art `LUIS` namens `my-luis-prediction-resource`. Erstellen Sie sie in der _vorhandenen_ Ressourcengruppe namens `my-resource-group` für die Region `westus`. Wenn Sie einen höheren Durchsatz als für den Free-Tarif wünschen, ändern Sie `F0` in `S0`. [Weitere Informationen zu Tarifen und Durchsatz.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

---


## <a name="assign-luis-resources"></a>Zuweisen von LUIS-Ressourcen

Das Erstellen einer Ressource bedeutet nicht unbedingt, dass sie auch verwendet wird. Sie müssen sie Ihren Apps zuweisen. Sie können eine Erstellungsressource für eine einzelne App oder für alle Apps in LUIS zuweisen.

# <a name="luis-portal"></a>[LUIS-Portal](#tab/portal)

### <a name="assign-resources-using-the-luis-portal"></a>Zuweisen von Ressourcen über das LUIS-Portal

**Zuweisen einer Erstellungsressource zu allen Ihren Apps** 

 In der folgenden Vorgehensweise wird die Erstellungsressource allen Apps zugewiesen.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an.
1. Wählen Sie oben rechts Ihr Benutzerkonto und dann in der Liste **Einstellungen** aus.
1. Wählen Sie auf der Seite **Benutzereinstellungen** die Option **Erstellungsressource hinzufügen** aus, und wählen Sie dann eine vorhandene Erstellungsressource aus. Wählen Sie **Speichern** aus.

**Zuweisen einer Ressource zu einer bestimmten App**

In der folgenden Vorgehensweise wird einer bestimmten App eine Ressource zugewiesen.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an. Wählen Sie in der Liste **Meine Apps** eine App aus.
1. Wechseln Sie zu **Verwalten** > **Azure-Ressourcen**:

    :::image type="content" source="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png" alt-text="Typ der Language Understanding-Vorhersageressource auswählen" lightbox="./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png":::

1. Wählen Sie auf der Registerkarte **Vorhersageressource** oder **Erstellungsressource** die Schaltfläche **Vorhersageressource hinzufügen** oder **Erstellungsressource hinzufügen** aus.
1. Verwenden Sie die Felder im Formular, um die richtige Ressource zu finden, und wählen Sie dann **Speichern** aus.

# <a name="without-luis-portal"></a>[Ohne LUIS-Portal](#tab/without-portal)

## <a name="assign-prediction-resource-without-using-the-luis-portal"></a>Zuweisen einer Vorhersageressource ohne Verwendung des LUIS-Portals

Für automatisierte Prozesse wie CI/CD-Pipelines können Sie die Zuweisung einer LUIS-Ressource zu einer LUIS-App mit den folgenden Schritten automatisieren:

1. Rufen Sie ein [Azure Resource Manager-Token](https://resources.azure.com/api/token?plaintext=true) ab, bei dem es sich um eine alphanumerische Zeichenfolge handelt. Dieses Token läuft ab, daher sollten Sie es sofort verwenden. Sie können auch den folgenden Azure CLI-Befehl verwenden:

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
1. Verwenden Sie das Token, um die LUIS-Runtimeressourcen abonnementübergreifend anzufordern. Verwenden Sie die API, um [das LUIS-Azure-Konto abzurufen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auf das Ihr Benutzerkonto Zugriff hat.

    Diese POST-API erfordert folgende Werte:

    |Header|Wert|
    |--|--|
    |`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Dem Tokenwert müssen das Wort `Bearer` und ein Leerzeichen vorangestellt werden.|
    |`Ocp-Apim-Subscription-Key`|Ihr Erstellungsschlüssel.|

    Die API gibt ein Array von JSON-Objekten zurück, die Ihre LUIS-Abonnements darstellen. Die zurückgegebenen Werte umfassen die Abonnement-ID, die Ressourcengruppe und den Ressourcennamen, der als `AccountName` zurückgegeben wurde. Finden Sie im Array das Element, das die LUIS-Ressource darstellt, die Sie der LUIS-App zuweisen möchten.

1. Weisen Sie das Token mit der API zum [Zuweisen eines LUIS-Azure-Kontos zu einer Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) der LUIS-Ressource zu.

    Diese POST-API erfordert folgende Werte:

    |type|Einstellung|Wert|
    |--|--|--|
    |Header|`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Dem Tokenwert müssen das Wort `Bearer` und ein Leerzeichen vorangestellt werden.|
    |Header|`Ocp-Apim-Subscription-Key`|Ihr Erstellungsschlüssel.|
    |Header|`Content-type`|`application/json`|
    |Abfragezeichenfolge|`appid`|die LUIS-App-ID
    |Body||{`AzureSubscriptionId`: Ihre Abonnement-ID,<br>`ResourceGroup`: Ressourcengruppenname mit Ihrer Vorhersageressource,<br>`AccountName`: Name Ihrer Vorhersageressource}|

    Wenn diese API erfolgreich ist, gibt sie `201 - created status` zurück.

---

## <a name="unassign-a-resource"></a>Aufheben der Zuweisung einer Ressource

Wenn Sie die Zuweisung einer Ressource aufheben, wird diese nicht aus Azure gelöscht. Lediglich die Verknüpfung mit LUIS wird aufgehoben.

# <a name="luis-portal"></a>[LUIS-Portal](#tab/portal)

## <a name="unassign-resources-using-luis-portal"></a>Aufheben der Zuweisung von Ressourcen über das LUIS-Portal

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie dann in der Liste **Meine Apps** eine App aus.
1. Wechseln Sie zu **Verwalten** > **Azure-Ressourcen**.
1. Wählen Sie die Schaltfläche **Ressourcenzuweisung aufheben** für die Ressource aus.

# <a name="without-luis-portal"></a>[Ohne LUIS-Portal](#tab/without-portal)

## <a name="unassign-prediction-resource-without-using-the-luis-portal"></a>Aufheben der Zuweisung einer Vorhersageressource ohne Verwendung des LUIS-Portals

1. Rufen Sie ein [Azure Resource Manager-Token](https://resources.azure.com/api/token?plaintext=true) ab, bei dem es sich um eine alphanumerische Zeichenfolge handelt. Dieses Token läuft ab, daher sollten Sie es sofort verwenden. Sie können auch den folgenden Azure CLI-Befehl verwenden:

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
 
1. Verwenden Sie das Token, um die LUIS-Runtimeressourcen abonnementübergreifend anzufordern. Verwenden Sie die [API zum Abrufen von LUIS-Azure-Konten](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), auf die Ihr Benutzerkonto Zugriff hat.

    Diese POST-API erfordert folgende Werte:

    |Header|Wert|
    |--|--|
    |`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Dem Tokenwert müssen das Wort `Bearer` und ein Leerzeichen vorangestellt werden.|
    |`Ocp-Apim-Subscription-Key`|Ihr Erstellungsschlüssel.|

    Die API gibt ein Array von JSON-Objekten zurück, die Ihre LUIS-Abonnements darstellen. Die zurückgegebenen Werte umfassen die Abonnement-ID, die Ressourcengruppe und den Ressourcennamen, der als `AccountName` zurückgegeben wurde. Finden Sie im Array das Element, das die LUIS-Ressource darstellt, die Sie der LUIS-App zuweisen möchten.

1. Weisen Sie das Token mit der API zum [Aufheben der Zuweisung eines LUIS-Azure-Kontos zu einer Anwendung](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32554f8591db3a86232e1/console) der LUIS-Ressource zu.

    Diese DELETE-API erfordert folgende Werte:

    |type|Einstellung|Wert|
    |--|--|--|
    |Header|`Authorization`|Der Wert von `Authorization` ist `Bearer {token}`. Dem Tokenwert müssen das Wort `Bearer` und ein Leerzeichen vorangestellt werden.|
    |Header|`Ocp-Apim-Subscription-Key`|Ihr Erstellungsschlüssel.|
    |Header|`Content-type`|`application/json`|
    |Abfragezeichenfolge|`appid`|die LUIS-App-ID
    |Body||{`AzureSubscriptionId`: Ihre Abonnement-ID,<br>`ResourceGroup`: Ressourcengruppenname mit Ihrer Vorhersageressource,<br>`AccountName`: Name Ihrer Vorhersageressource}|

    Wenn diese API erfolgreich ist, gibt sie `200 - OK status` zurück.

---

## <a name="resource-ownership"></a>Ressourcenbesitz

Eine Azure-Ressource gehört (wie eine LUIS-Ressource) zu dem Abonnement, das die Ressource enthält.

Um den Besitzer einer Ressource zu ändern, können Sie eine der folgenden Aktionen durchführen:
* Übertragen Sie das [Eigentum](../../cost-management-billing/manage/billing-subscription-transfer.md) Ihres Abonnements.
* Exportieren Sie die LUIS-App als Datei, und importieren Sie die App anschließend in einem anderen Abonnement. Die Exportfunktion finden Sie im LUIS-Portal auf der Seite **Meine Apps**.

## <a name="resource-limits"></a>Ressourceneinschränkungen

### <a name="authoring-key-creation-limits"></a>Grenzwerte für die Erstellung von Erstellungsschlüsseln

Sie können bis zu 10 Erstellungsschlüssel pro Region und Abonnement erstellen. Veröffentlichungsregionen unterscheiden sich von Erstellungsregionen. Stellen Sie sicher, dass Sie eine App in der Erstellungsregion erstellen, die der Veröffentlichungsregion entspricht, in der sich Ihre Clientanwendung befinden soll. Weitere Informationen wie Erstellungsregionen Veröffentlichungsregionen zugeordnet werden können, finden Sie unter [Erstellungs- und Veröffentlichungsregionen und die dazugehörigen Schlüssel](luis-reference-regions.md). 

Weitere Informationen finden Sie unter [Erstellungsressourcenlimits](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>Fehler für Grenzwerte bei der Schlüsselnutzung

Nutzungslimits sind tarifabhängig.

Wenn Sie Ihr TPS-Kontingent (Transaktionen pro Sekunde) überschreiten, erhalten Sie den HTTP-Fehler 429. Wenn Sie Ihr TPM-Kontingent (Transaktionen pro Monat) überschreiten, erhalten Sie den HTTP-Fehler 403.

## <a name="change-the-pricing-tier"></a>Ändern des Tarifs

1.  Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu **Alle Ressourcen**, und wählen Sie Ihre Ressource aus.

    :::image type="content" source="./media/luis-usage-tiers/find.png" alt-text="Der Screenshot zeigt ein LUIS-Abonnement im Azure-Portal." lightbox="./media/luis-usage-tiers/find.png":::

1.  Wählen Sie im Menü auf der linken Seite **Tarif** aus, um die verfügbaren Tarife anzuzeigen.
1.  Wählen Sie den gewünschten Tarif aus, und klicken Sie dann auf **Auswählen**, um Ihre Änderung zu speichern. Wenn die Preisänderung abgeschlossen ist, wird oben rechts eine Benachrichtigung mit dem Tarifupdate angezeigt.

## <a name="view-azure-resource-metrics"></a>Anzeigen von Azure-Ressourcenmetriken

## <a name="view-a-summary-of-azure-resource-usage"></a>Anzeigen einer Zusammenfassung der Azure-Ressourcennutzung
Sie können LUIS-Nutzungsinformationen im Azure-Portal anzeigen. Die Seite **Übersicht** zeigt eine Zusammenfassung, einschließlich der letzten Aufrufe und Fehler. Wenn Sie eine LUIS-Endpunktanforderung stellen, kann es bis zu fünf Minuten dauern, bis die Änderung angezeigt wird.

:::image type="content" source="./media/luis-usage-tiers/overview.png" alt-text="Screenshot: Seite „Übersicht“" lightbox="./media/luis-usage-tiers/overview.png":::

## <a name="customizing-azure-resource-usage-charts"></a>Anpassen von Nutzungsdiagrammen für Azure-Ressourcen
Die Seite **Metriken** bietet eine detailliertere Ansicht der Daten. Sie können Ihre Metrikdiagramme für einen bestimmten **Zeitraum** und eine bestimmte **Metrik** konfigurieren.

:::image type="content" source="./media/luis-usage-tiers/metrics.png" alt-text="Screenshot: Seite „Metriken“" lightbox="./media/luis-usage-tiers/metrics.png":::

## <a name="total-transactions-threshold-alert"></a>Warnung für Gesamttransaktions-Schwellenwert
Wenn Sie wissen möchten, wann Sie einen bestimmten Transaktionsschwellenwert erreichen, z. B. 10.000 Transaktionen, können Sie eine Warnung erstellen:

1. Wählen Sie auf der linken Seite im Menü **Warnungen** aus.
2. Wählen Sie im oberen Menü die Option **Neue Warnungsregel** aus.

    :::image type="content" source="./media/luis-usage-tiers/alerts.png" alt-text="Screenshot: Seite „Warnungsregeln“" lightbox="./media/luis-usage-tiers/alerts.png":::

3. Klicken Sie auf **Bedingung hinzufügen**.

    :::image type="content" source="./media/luis-usage-tiers/alerts-2.png" alt-text="Screenshot: Seite „Bedingung hinzufügen“ für Warnungsregeln" lightbox="./media/luis-usage-tiers/alerts-2.png":::

4. Wählen Sie **Aufrufe gesamt** aus.

    :::image type="content" source="./media/luis-usage-tiers/alerts-3.png" alt-text="Screenshot: Seite „Aufrufe gesamt“ für Warnungen" lightbox="./media/luis-usage-tiers/alerts-3.png":::

5. Scrollen Sie nach unten zum Abschnitt **Warnungslogik**, legen Sie die Attribute wie gewünscht fest, und klicken Sie auf **Fertig**.

    :::image type="content" source="./media/luis-usage-tiers/alerts-4.png" alt-text="Screenshot: Seite „Warnungslogik“" lightbox="./media/luis-usage-tiers/alerts-4.png":::

6. Um Benachrichtigungen zu senden oder Aktionen aufzurufen, wenn die Warnungsregel ausgelöst wird, wechseln Sie zum Abschnitt **Aktionen**, und fügen Sie Ihre Aktionsgruppe hinzu.

    :::image type="content" source="./media/luis-usage-tiers/alerts-5.png" alt-text="Screenshot: Aktionsseite für Warnungen" lightbox="./media/luis-usage-tiers/alerts-5.png":::

### <a name="reset-an-authoring-key"></a>Zurücksetzen des Erstellungsschlüssels

Apps, die [zur Erstellungsressource migriert](luis-migration-authoring.md) wurden: Wurde Ihr Erstellungsschlüssel kompromittiert, setzen Sie den Schlüssel für diese Erstellungsressource im Azure-Portal auf der Seite **Schlüssel** zurück.

Für Apps, die nicht migriert wurden: Der Schlüssel wird für all Ihre Apps im LUIS-Portal zurückgesetzt. Wenn Sie Ihre Apps mithilfe der Authoring-APIs verfassen, müssen Sie den Wert von `Ocp-Apim-Subscription-Key` auf den neuen Schlüssel ändern.

### <a name="regenerate-an-azure-key"></a>Erneutes Generieren eines Azure-Schlüssels

Sie können einen Azure-Schlüssel über die Seite **Schlüssel** im Azure-Portal neu generieren.

<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>App-Besitz, -Zugriff und -Sicherheit

Eine App ist durch ihre Azure-Ressourcen definiert, die durch das Abonnement des Besitzers bestimmt sind.

Sie können Ihre LUIS-App verschieben. Verwenden Sie die folgenden Azure-Ressourcen, damit diese Ihnen über das Azure-Portal oder die Azure CLI dabei helfen können:

* [Verschieben einer App zwischen LUIS-Erstellungsressourcen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Verschieben einer Ressource in eine neue Ressourcengruppe oder in ein Abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Verschieben einer Ressource innerhalb desselben Abonnements oder zwischen Abonnements](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, [wie Sie Versionen verwenden](luis-how-to-manage-versions.md), um den Lebenszyklus ihrer App zu steuern.


