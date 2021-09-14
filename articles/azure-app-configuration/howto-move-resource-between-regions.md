---
title: Einen App Configuration-Speicher in eine andere Region verschieben
description: Hier erfahren Sie, wie Sie einen App Configuration-Speicher in eine andere Region verschieben.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: how-to
ms.date: 8/23/2021
ms.openlocfilehash: c3ffe773e16eb7a658f219e980711eca2add1aac
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123077422"
---
# <a name="move-an-app-configuration-store-to-another-region"></a>Einen App Configuration-Speicher in eine andere Region verschieben 

App Configuration-Speicher sind regionsspezifisch und können nicht automatisch in andere Regionen verschoben werden. Sie müssen einen neuen App Configuration-Speicher in der Zielregion erstellen und dann Ihren Inhalt aus dem Quellspeicher in den neuen Zielspeicher verschieben. Sie können Ihre Ressourcen aus verschiedenen Gründen in eine andere Region verschieben. Beispielsweise, um die Vorteile einer neuen Azure-Region mit Availability Zone-Unterstützung zu nutzen, um Funktionen oder Dienste bereitzustellen, die nur in bestimmten Regionen verfügbar sind, oder um interne Richtlinien und Governance-Anforderungen zu erfüllen. 

Die folgenden Schritte führen Sie durch den Prozess der Erstellung eines neuen Zielspeichers und des Exports Ihres aktuellen Speichers in die neue Region. 

## <a name="design-considerations"></a>Überlegungen zum Entwurf

Bevor Sie beginnen, bedenken Sie folgende Konzepte:

* Namen von Konfigurationsspeichern sind global eindeutig. 
* Sie müssen Ihre Zugriffsrichtlinien und Netzwerkkonfigurationseinstellungen in dem neuen Konfigurationsspeicher erneut konfigurieren.

## <a name="create-the-target-configuration-store"></a>Erstellen des Zielkonfigurationsspeichers

### <a name="portal"></a>[Portal](#tab/portal)     
Führen Sie die folgenden Schritte aus, um einen neuen App Configuration-Speicher im Portal zu erstellen: 
1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie links oben auf der Startseite die Option **Ressource erstellen** aus. Geben Sie im Feld **Marketplace durchsuchen** den Suchbegriff *App Configuration* ein, und drücken Sie die <kbd>EINGABETASTE</kbd>. 

    ![Suchen nach App Configuration](../../includes/media/azure-app-configuration-create/azure-portal-search.png)
1. Wählen Sie **App Configuration** in den Suchergebnissen und dann **Erstellen** aus.

    ![Wählen Sie „Erstellen“ aus.](../../includes/media/azure-app-configuration-create/azure-portal-app-configuration-create.png)
1. Geben Sie im Bereich **App Configuration erstellen** die folgenden Einstellungen ein:
    
    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    |---|---|---|
    | **Abonnement** | Ihr Abonnement | Wählen Sie das Azure-Abonnement Ihres ursprünglichen Speichers aus. |
    | **Ressourcengruppe** | Ihre Ressourcengruppe | Wählen Sie die Azure-Ressourcengruppe Ihres ursprünglichen Speichers aus. |
    | **Ressourcenname** | Global eindeutiger Name | Geben Sie einen eindeutigen Ressourcennamen ein, der für den Ziel-App Configuration-Speicher verwendet werden soll. Dies kann nicht derselbe Name wie der vorherige Konfigurationsspeicher sein. |
    | **Location** | Der Zielspeicherort. | Wählen Sie die Zielregion aus, in die Sie Ihren Konfigurationsspeicher verschieben möchten. |
    | **Preisstufe** | *Standard* | Wählen Sie den gewünschten Tarif aus. Weitere Informationen finden Sie auf der Seite [App Configuration – Preise](https://azure.microsoft.com/pricing/details/app-configuration). |
1. Wählen Sie zum Überprüfen Ihrer Einstellungen **Überprüfen + erstellen** aus.
1. Klicken Sie auf **Erstellen**. Die Bereitstellung kann einige Minuten dauern.
1. Nachdem die Ressource bereitgestellt wurde, erstellen Sie die Zugriffsrichtlinien und Netzwerkkonfigurationseinstellungen des Quellspeichers neu. Diese werden nicht mit der Konfiguration übertragen. Dies kann die Verwaltung von Identitäten, virtuellen Netzwerken und den Zugang zu öffentlichen Netzwerken umfassen. 
    
#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)
Führen Sie die folgenden Schritte aus, um einen neuen App Configuration-Speicher im CLI zu erstellen: 
1. Melden Sie sich mit Ihren Anmeldeinformationen im Azure-Portal an.
    ```azurecli
    az login
    ```
1. Erstellen Sie einen neuen Konfigurationsspeicher mit dem Befehl `create`
    ```azurecli
    az appconfig create -g MyResourceGroup -n MyResourceName -l targetlocation --sku Standard 
    ```
    und geben Sie folgenden Einstellungen ein:

    | Einstellung | Vorgeschlagener Wert | BESCHREIBUNG |
    |---|---|---|
    | **Ressourcengruppe** | Ihre Ressourcengruppe | Wählen Sie die Azure-Ressourcengruppe Ihres ursprünglichen Speichers aus. |
    | **Ressourcenname** | Global eindeutiger Name | Geben Sie einen eindeutigen Ressourcennamen ein, der für den Ziel-App Configuration-Speicher verwendet werden soll. Dies kann nicht derselbe Name wie der vorherige Konfigurationsspeicher sein. |
    | **Location** | Der Zielspeicherort. | Wählen Sie die Zielregion aus, in die Sie Ihren Konfigurationsspeicher verschieben möchten. |
    | **sku** | *Standard* | Wählen Sie den gewünschten Tarif aus. Weitere Informationen finden Sie auf der Seite [App Configuration – Preise](https://azure.microsoft.com/pricing/details/app-configuration). |
1. Die Bereitstellung kann einige Minuten dauern. Erstellen Sie nach Abschluss des Vorgangs die Zugriffsrichtlinien und Netzwerkkonfigurationseinstellungen des Quellspeichers neu. Diese werden nicht mit den Konfigurationswerten übertragen. Dies kann die Verwaltung von Identitäten, virtuellen Netzwerken und den Zugang zu öffentlichen Netzwerken umfassen. Weitere Informationen finden Sie in der [Dokumentation zur Befehlszeilenschnittstelle](./cli-samples.md).
---

## <a name="transfer-your-configuration-key-values"></a>Übertragen Sie die Schlüsselwerte der Konfiguration  

### <a name="portal"></a>[Portal](#tab/portal)
Führen Sie die folgenden Schritte aus, um Ihre Konfiguration über das Portal in den Zielspeicher zu exportieren:
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Quellkonfigurationsspeicher, und wählen Sie **Import/Export** unter **Vorgänge** aus.
1. Wählen Sie **Exportieren** und dann **App Configuration** in der Dropdownliste **Zieldienst** aus. 
    ![Exportieren in einen anderen Konfigurationsspeicher](media/export-to-config-store.png)
1. Klicken Sie auf **Ressource auswählen** und geben Sie Ihr **Abonnement** und Ihre **Ressourcengruppe** ein. Die **Ressource** ist der Name des zuvor erstellten Zielkonfigurationsspeichers. 
1. Wählen Sie **Übernehmen** aus, um Ihren Zielkonfigurationsspeicher zu überprüfen. 
1. Belassen Sie die Felder „From label“, „Time“ und „Label“ als Standardwerte, und wählen Sie **Übernehmen** aus. 
1. Navigieren Sie im Portal zu Ihrem Zielkonfigurationsspeicher, um zu überprüfen, ob Ihre Konfigurationen erfolgreich von Ihrer Quelle in Ihren Zielspeicher übertragen wurden. Wählen Sie unter **Vorgänge** die Option **Konfigurations-Explorer** aus, und vergewissern Sie sich, dass diese die gleichen Schlüssel-Wert-Paare wie in Ihrem ursprünglichen Speicher enthält. 
    > [!NOTE]
    > Dieser Prozess lässt nur zu, dass Schlüssel-Wert-Konfigurationen nacheinander exportiert werden. Wiederholen Sie die Schritte 2 bis 5 für jedes Etikett, um mehrere zu exportieren. 

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)
Führen Sie die folgenden Schritte aus, um Ihre Konfiguration über Azure in den Zielspeicher zu exportieren:
1. Geben Sie im Azure CLI den folgenden Befehl ein, der alle Werte aus Ihrem Quellkonfigurationsspeicher in Ihren Zielkonfigurationsspeicher exportiert. 
    ```azurecli
    az appconfig kv export -n SourceConfigurationStore -d appconfig --dest-name TargetConfigurationStore --key * --label * --preserve-labels
    ```
1. Um zu überprüfen, ob Ihre Konfigurationen erfolgreich von Ihrer Quelle in Ihren Zielspeicher übertragen wurden, listen Sie alle Schlüsselwerte in Ihrem Zielspeicher auf. 
    ```azurecli
    az appconfig kv list -n TargetAppConfiguration --all
    ```
---
## <a name="delete-your-source-configuration-store"></a>Löschen des Quellkonfigurationsspeichers 

Wenn die Konfiguration in den Zielspeicher übertragen wurde, können Sie ihren Quellkonfigurationsspeicher löschen. 

### <a name="portal"></a>[Portal](#tab/portal)
Führen Sie die folgenden Schritte aus, um Ihren Quellkonfigurationsspeicher im Portal zu löschen:
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Ressourcengruppen**.
1. Geben Sie im Feld **Nach Name filtern** den Namen Ihrer Ressourcengruppe ein. 
1. Wählen Sie in der Ergebnisliste den Ressourcengruppennamen aus, um eine Übersicht anzuzeigen.
1. Wählen Sie Ihren Quellkonfigurationsspeicher aus, und wählen Sie auf dem Blatt **Übersicht** die Option **Löschen** aus. 
1. Sie werden aufgefordert, das Löschen des Konfigurationsspeichers zu bestätigen, und wählen Sie **Ja** aus.

Nach wenigen Augenblicken ist der Quellkonfigurationsspeicher gelöscht.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azcli)
Führen Sie die folgenden Schritte aus, um Ihren Quellkonfigurationsspeicher im Azure CLI zu löschen:
1. Führen Sie in der Azure-Befehlszeilenschnittstelle den folgenden Befehl aus: 
    ```azurecli
    az appconfig delete -g ResourceGroupName -n SourceConfiguration
    ```
    Beachten Sie, dass die **Ressourcengruppe** dem Quellkonfigurationsspeicher zugeordnet ist. 
1. Das Löschen des Quellkonfigurationsspeichers kann einige Minuten dauern. Sie können überprüfen, ob der Vorgang erfolgreich war, indem Sie alle aktuellen Konfigurationsspeicher in Ihrer Ressourcengruppe auflisten. 
    ```azurecli
    az appconfig list -g MyResourceGroup
    ```
    Nach wenigen Augenblicken ist der Quellkonfigurationsspeicher gelöscht.

---
## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Automatisches Sichern von Schlüsselwerten aus Azure App Configuration Speichern](./howto-move-resource-between-regions.md)
>[Azure App Configuration Resilienz und Notfallwiederherstellung](./concept-disaster-recovery.md)
