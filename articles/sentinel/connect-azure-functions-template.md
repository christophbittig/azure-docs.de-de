---
title: Verwenden von Azure Functions zum Verbinden von Azure Sentinel mit Ihrer Datenquelle | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Datenconnectors konfigurieren, die Azure Functions verwenden, um Daten aus Datenquellen in Azure Sentinel abzurufen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/07/2021
ms.author: yelevin
ms.openlocfilehash: f776c39a5a1dadde2e6ee01fe211e0769e5e06eb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123261056"
---
# <a name="use-azure-functions-to-connect-azure-sentinel-to-your-data-source"></a>Verwenden von Azure Functions zum Verbinden von Azure Sentinel mit Ihrer Datenquelle

Sie können [Azure Functions](/azure/azure-functions/functions-overview) in Verbindung mit verschiedenen Programmiersprachen wie [PowerShell](../azure-functions/functions-reference-powershell.md) oder Python verwenden, um einen serverlosen Connector mit den REST-API-Endpunkten Ihrer kompatiblen Datenquellen zu erstellen. Mit Azure Functions-Apps können Sie dann Azure Sentinel mit der REST-API Ihrer Datenquelle verbinden, um Protokolle zu pullen.

In diesem Artikel wird beschrieben, wie Sie Azure Sentinel für die Verwendung von Azure Functions-Apps konfigurieren. Möglicherweise müssen Sie auch Ihr Quellsystem konfigurieren. Anbieter- und produktspezifische Informationslinks finden Sie auf der Seite jedes Datenconnectors im Portal oder im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).




> [!NOTE]
> - Nach der Erfassung in Azure Sentinel werden Daten am geografischen Standort des Arbeitsbereichs gespeichert, in dem Sie Azure Sentinel ausführen.
>
>     Zur langfristigen Aufbewahrung können Sie Daten auch in Azure Data Explorer speichern. Weitere Informationen finden Sie unter [Integrieren von Azure Data Explorer](store-logs-in-azure-data-explorer.md).
>
> - Die Verwendung von Azure Functions zum Erfassen von Daten in Azure Sentinel kann zusätzliche Kosten für die Datenerfassung verursachen. Weitere Informationen finden Sie auf der Seite [Azure Functions – Preise](https://azure.microsoft.com/pricing/details/functions/).

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über die folgenden Berechtigungen und Anmeldeinformationen verfügen, bevor Sie Azure Functions verwenden, um Azure Sentinel mit Ihrer Datenquelle zu verbinden und deren Protokolle in Azure Sentinel zu pullen:

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

- Sie müssen über Leseberechtigungen für freigegebene Schlüssel für den Arbeitsbereich verfügen. [Weitere Informationen zu Arbeitsbereichsschlüsseln](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)

- Sie benötigen Lese- und Schreibberechtigungen für Azure Functions, um eine Funktions-App erstellen zu können. [Informieren Sie sich ausführlicher über Azure Functions](../azure-functions/index.yml).

- Sie benötigen auch Anmeldeinformationen für den Zugriff auf die API des Produkts – entweder einen Benutzernamen und ein Kennwort, ein Token, einen Schlüssel oder eine andere Kombination. Möglicherweise benötigen Sie auch andere API-Informationen, z. B. einen Endpunkt-URI.

    Weitere Informationen finden Sie in der Dokumentation für den Dienst, mit dem Sie eine Verbindung herstellen, und im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).

## <a name="configure-and-connect-your-data-source"></a>Konfigurieren und Verbinden Ihrer Datenquelle

> [!NOTE]
> - Sie können Arbeitsbereichs- und API-Autorisierungsschlüssel oder -token sicher in Azure Key Vault speichern. Azure Key Vault bietet einen sicheren Mechanismus zum Speichern und Abrufen von Schlüsselwerten. [Befolgen Sie diese Anweisungen](../app-service/app-service-key-vault-references.md), um Azure Key Vault mit einer Azure-Funktions-App zu verwenden.
>
> - Die erwartungsgemäße Funktion einiger Datenconnectors ist von einem Parser abhängig, der auf einer [Kusto-Funktion](/azure/data-explorer/kusto/query/functions/user-defined-functions) basiert. Links zu Anweisungen zum Erstellen der Kusto-Funktion und des Alias finden Sie im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).


### <a name="step-1---get-your-source-systems-api-credentials"></a>SCHRITT 1: Abrufen der API-Anmeldeinformationen Ihres Quellsystems

Befolgen Sie die Anweisungen Ihres Quellsystems, um seine **API-Anmeldeinformationen/-Autorisierungsschlüssel/-Token** abzurufen. Kopieren Sie sie, und fügen Sie sie zur späteren Verwendung in eine Textdatei ein.

Details zu den genauen Anmeldeinformationen, die Sie benötigen, sowie Links zu den Anweisungen für Ihr Produkts zu deren Suche oder Erstellung finden Sie auf der Datenconnectorseite im Portal und im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).

Möglicherweise müssen Sie auch die Protokollierung oder andere Einstellungen auf Ihrem Quellsystem konfigurieren. Sie finden die relevanten Anweisungen hierfür bei den Anweisungen im vorherigen Absatz.
### <a name="step-2---deploy-the-connector-and-the-associated-azure-function-app"></a>Schritt 2: Bereitstellen des Connectors und der zugeordneten Azure-Funktions-App

#### <a name="choose-a-deployment-option"></a>Auswählen einer Bereitstellungsoption

# <a name="azure-resource-manager-arm-template"></a>[ARM-Vorlage (Azure Resource Manager)](#tab/ARM)

Diese Methode stellt eine automatisierte Bereitstellung Ihres Azure-Funktionsbasierten Connectors mithilfe einer ARM-Vorlage bereit.

1. Wählen Sie im Azure Sentinel-Portal **Datenconnectors** aus. Wählen Sie ihren Azure Functions-basierten Connector aus der Liste und dann **Connectorseite öffnen** aus.

1. Kopieren Sie unter **Konfiguration** die **Arbeitsbereich-ID** von Azure Sentinel und den **Primärschlüssel**, und fügen Sie so ein, dass Sie diese Informationen zur Hand haben.

1. Wählen Sie **Bereitstellung in Azure** aus. (Möglicherweise müssen Sie nach unten scrollen, um die Schaltfläche zu finden.)

1. Der Bildschirm **Benutzerdefinierte Bereitstellung** wird angezeigt.
    - Wählen Sie ein **Abonnement**, eine **Ressourcengruppe** und eine **Region** aus, in dem bzw. der Ihre Funktions-App bereitgestellt werden soll.

    - Geben Sie Ihre API-Anmeldeinformationen/-Autorisierungsschlüssel/-Token ein, die Sie oben in [Schritt 1](#step-1---get-your-source-systems-api-credentials) gespeichert haben.

    - Fügen Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** von Azure Sentinel ein, die Sie zuvor kopiert haben.

        > [!NOTE]
        > Wenn Sie Azure Key Vault-Geheimnisse für einen der oben genannten Werte verwenden, verwenden Sie das `@Microsoft.KeyVault(SecretUri={Security Identifier})`-Schema anstelle der Zeichenfolgenwerte. Weitere Informationen finden Sie in der Referenzdokumentation zu Key Vault.

    - Füllen Sie alle anderen Felder im Formular auf dem Bildschirm **Benutzerdefinierte Bereitstellung** aus. Weitere Informationen finden Sie auf der Seite ihres Datenconnectors im Portal oder im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).

    - Klicken Sie auf **Überprüfen + erstellen**. Wählen Sie nach Abschluss der Überprüfung die Option **Erstellen** aus.

# <a name="manual-deployment-with-powershell"></a>[Manuelle Bereitstellung mit PowerShell](#tab/MPS)

Verwenden Sie die folgenden Schritt-für-Schritt-Anweisungen, um Azure Functions-basierte Connectors, die PowerShell-Funktionen verwenden, manuell bereitzustellen.

1. Wählen Sie im Azure Sentinel-Portal **Datenconnectors** aus. Wählen Sie ihren Azure Functions-basierten Connector aus der Liste und dann **Connectorseite öffnen** aus.

1. Kopieren Sie unter **Konfiguration** die **Arbeitsbereich-ID** von Azure Sentinel und den **Primärschlüssel**, und fügen Sie so ein, dass Sie diese Informationen zur Hand haben.

1. **Erstellen einer Funktions-App**
    1. Suchen Sie im Azure-Portal nach **Funktions-App**, und wählen Sie diese Option aus.

    1. Wählen Sie auf der Seite **Funktions-App** die Option **Erstellen** aus. Der Assistent **Funktions-App erstellen** wird geöffnet.

    1. Auf der Registerkarte **Grundlagen**:
        - Wählen Sie ein **Abonnement** und eine **Ressourcengruppe** aus.
        - Benennen Sie Ihre Funktions-App.
        - Legen Sie **Laufzeitstapel** auf *PowerShell Core* fest.
        - Wählen Sie die entsprechende Versionsnummer aus.
        - Wählen Sie die **Region** aus, in der Sie bereitstellen möchten, und wählen Sie dann **Weiter: Hosting** aus.

    1. Auf der Registerkarte **Hosting**:
        - Wählen Sie das **Speicherkonto** aus, das Sie verwenden möchten, oder erstellen Sie ein neues.
        - Wählen Sie *Verbrauch (serverlos)* als Ihren **Plantyp** aus.

    1. Nehmen Sie alle anderen Konfigurationsänderungen vor, die Sie benötigen, und wählen Sie dann **Überprüfen + erstellen** aus.

    1. Warten Sie auf die Anzeige der Meldung „Überprüfung erfolgreich“, und wählen Sie dann **Erstellen** aus.

    1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.

1. **Importieren von Funktions-App-Code**
    1. Wählen Sie in der neu erstellten Funktions-App im Navigationsmenü **Funktionen** aus.

    1. Wählen Sie auf der Seite **Funktionen** die Option **+ Hinzufügen** aus.
    
    1. Wählen Sie im Bereich **Funktion hinzufügen** den **Timer**-Trigger aus.

    1. Geben Sie einen eindeutigen Namen für Ihre Funktion und einen *cron*-Ausdruck ein, um den Zeitplan anzugeben. Das Standardintervall ist alle 5 Minuten.

    1. Wenn die Funktion erstellt wurde, wählen Sie im linken Bereich **Programmieren und testen** aus.

    1. Laden Sie den vom Anbieter Ihres Quellsystems bereitgestellten Funktions-App-Code herunter, kopieren Sie ihn, und fügen Sie ihn in den **Funktions-App** *run.ps1*-Editor ein, und ersetzen Sie dabei alle Standardvorgaben. Den Downloadlink finden Sie auf der Connectorseite oder im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).

    1. Wählen Sie **Speichern** aus.

1. **Konfigurieren der Funktions-App**
    1. Wählen Sie auf der Seite Ihrer Funktions-App im Navigationsmenü unter **Einstellungen** die Option **Konfiguration** aus.

    1. Wählen Sie auf der Registerkarte **Anwendungseinstellungen** die Option **+ Neue Anwendungseinstellung** aus.

    1. Fügen Sie die vorgeschriebenen Anwendungseinstellungen für Ihr Produkt einzeln mit den entsprechenden Zeichenfolgenwerten hinzu, bei denen die Groß-/Kleinschreibung beachtet wird. Weitere Informationen finden Sie auf der Seite ihres Datenconnectors oder im Abschnitt Ihres Produkts im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).

        > [!TIP]
        > Verwenden Sie ggf. die Anwendungseinstellung *logAnalyticsUri*, um den Log Analytics-API-Endpunkt zu überschreiben, wenn Sie eine dedizierte Cloud verwenden. Wenn Sie also beispielsweise die öffentliche Cloud verwenden, lassen Sie den Wert leer. Für die Azure-Cloudumgebung „GovUS“ geben Sie den Wert im folgenden Format an: `https://<CustomerId>.ods.opinsights.azure.us`.
        >

# <a name="manual-deployment-with-python"></a>[Manuelle Bereitstellung mit Python](#tab/MPY)

Verwenden Sie die folgenden Schritt-für-Schritt-Anweisungen, um Azure Functions-basierte Connectors, die Python-Funktionen verwenden, manuell bereitzustellen. Diese Art der Bereitstellung erfordert Visual Studio Code.

1. Wählen Sie im Azure Sentinel-Portal **Datenconnectors** aus. Wählen Sie ihren Azure Functions-basierten Connector aus der Liste und dann **Connectorseite öffnen** aus.

1. Kopieren Sie unter **Konfiguration** die **Arbeitsbereich-ID** von Azure Sentinel und den **Primärschlüssel**, und fügen Sie so ein, dass Sie diese Informationen zur Hand haben.

1. **Bereitstellen einer Funktions-App**

    > [!NOTE]
    > Sie müssen [Visual Studio Code](../azure-functions/create-first-function-vs-code-python.md) (VS Code) für die Azure Functions-Entwicklung vorbereiten.

    1. Laden Sie die Azure-Funktions-App-Datei mithilfe des Links herunter, der auf der Seite „Datenconnector“ und im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md) bereitgestellt ist. Extrahieren Sie das Archiv auf Ihren lokalen Entwicklungscomputer.

    1. Starten Sie Visual Studio Code. Klicken Sie auf der Menüleiste auf **Datei > Ordner öffnen...** .

    1. Wählen Sie unter den aus dem Archiv extrahierten Dateien den Ordner der obersten Ebene aus.

    1. Wählen Sie das Azure-Symbol in der VS Code-Aktivitätsleiste (auf der linken Seite) aus. Wählen Sie dann im Bereich **Azure: Funktionen** die Schaltfläche **In Funktions-App bereitstellen...** aus. 

        > [!NOTE]
        > Sollten Sie noch nicht angemeldet sein, wählen Sie auf der Aktivitätsleiste das Azure-Symbol aus. Wählen Sie dann im Bereich **Azure: Funktionen** die Option **Bei Azure anmelden...** aus.  
        > Sollten Sie bereits angemeldet sein, fahren Sie mit dem nächsten Schritt fort.

    1. Geben Sie nach entsprechender Aufforderung Folgendes ein:
        - **Wählen Sie einen Ordner aus:** Wählen Sie einen Ordner in Ihrem Arbeitsbereich aus, oder navigieren Sie zu einem Ordner, der Ihre Funktions-App enthält.
        - **Wählen Sie das Abonnement aus:** Wählen Sie das zu verwendende Abonnement aus.
        - Wählen Sie **Neue Funktions-App in Azure erstellen** aus. (Wählen Sie nicht die Option **Erweitert** aus.)
        - **Geben Sie einen global eindeutigen Namen für die Funktions-App ein**: Geben Sie ihrer Funktions-App einen Namen, der in einem URL-Pfad gültig wäre. Der Name, den Sie auswählen, wird überprüft, um sicherzustellen, dass er Azure Functions-weit eindeutig ist.
        - **Wählen Sie eine Laufzeit aus**: Wählen Sie *Python 3.8* aus.

    1. Die Bereitstellung beginnt. Nach der Erstellung der Funktions-App wird eine Benachrichtigung angezeigt, und das Bereitstellungspaket wird angewendet.

    1. Kehren Sie zur Seite Ihrer Funktions-App zurück, um sie zu konfigurieren.

1. **Konfigurieren der Funktions-App**
    1. Wählen Sie auf der Seite Ihrer Funktions-App im Navigationsmenü unter **Einstellungen** die Option **Konfiguration** aus.

    1. Wählen Sie auf der Registerkarte **Anwendungseinstellungen** die Option **+ Neue Anwendungseinstellung** aus.

    1. Fügen Sie die vorgeschriebenen Anwendungseinstellungen für Ihr Produkt einzeln mit den entsprechenden Zeichenfolgenwerten hinzu, bei denen die Groß-/Kleinschreibung beachtet wird. Informationen zu den hinzuzufügenden Anwendungseinstellungen finden Sie auf der Seite ihres Datenconnectors oder im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md).

        - Verwenden Sie ggf. die Anwendungseinstellung *logAnalyticsUri*, um den Log Analytics-API-Endpunkt zu überschreiben, wenn Sie eine dedizierte Cloud verwenden. Wenn Sie also beispielsweise die öffentliche Cloud verwenden, lassen Sie den Wert leer. Für die Azure-Cloudumgebung „GovUS“ geben Sie den Wert im folgenden Format an: `https://<CustomerId>.ods.opinsights.azure.us`.

---

## <a name="find-your-data"></a>Suchen von Daten

Nach dem erfolgreichen Herstellen einer Verbindung werden die Daten in **Protokollen** unter *CustomLogs* in den Tabellen angezeigt, die im Abschnitt für Ihren Dienst auf der Seite [Referenz zu Azure Sentinel-Datenconnectors](data-connectors-reference.md) aufgeführt sind.

Um Daten abzufragen, geben Sie einen dieser Tabellennamen (oder den relevanten Kusto-Funktionsalias) in das Abfragefenster ein.

Einige hilfreiche Beispielabfragen finden Sie auf der Registerkarte **Nächste Schritte** auf der Connectorseite.

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann bis zu 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Azure Sentinel mittels Azure Functions-basierter Connectors mit Ihrer Datenquelle verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](tutorial-monitor-your-data.md), um Ihre Daten zu überwachen.