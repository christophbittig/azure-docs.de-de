---
title: 'Schnellstart: Interaktive Kartensuche mit Azure Maps'
description: 'Schnellstart: Hier wird erläutert, wie Sie interaktive, durchsuchbare Karten erstellen. Hier erfahren Sie, wie Sie ein Azure Maps-Konto erstellen, einen Primärschlüssel abrufen und mithilfe des Web SDK Kartenanwendungen einrichten.'
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: a61fdab9814ecaff7ef8a54749968d2ba1b6dd38
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750194"
---
# <a name="quickstart-create-an-interactive-search-map-with-azure-maps"></a>Schnellstart: Erstellen einer interaktiven Kartensuche mit Azure Maps

In diesem Artikel wird gezeigt, wie Sie Azure Maps verwenden, um eine Karte mit einer interaktiven Sucherfahrung zu erstellen. Die folgenden grundlegenden Schritte werden erläutert:

* Erstellen eines eigenen Azure Maps-Kontos
* Abrufen Ihres Primärschlüssels zur Verwendung in der Demowebanwendung
* Herunterladen und Öffnen der Karten-Demoanwendung.

In dieser Schnellstartanleitung wird das Azure Maps Web SDK verwendet, aber die Azure Maps-Dienste können mit jedem Kartensteuerelement verwendet werden. [Hier](open-source-projects.md#third-part-map-control-plugins) finden Sie einige beliebte Open-Source-Kartensteuerelemente, für die das Azure Maps-Team Plug-Ins erstellt hat.

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

<a id="createaccount"></a>

## <a name="create-an-azure-maps-account"></a>Erstellen eines Azure Maps-Kontos

Erstellen Sie mithilfe der folgenden Schritte ein neues Azure Maps-Konto:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) links oben auf **Ressource erstellen**.
2. Geben Sie im Feld *Marketplace durchsuchen* die Begriffe **Azure Maps** ein.
3. Wählen Sie in den *Ergebnissen* die Option **Azure Maps** aus. Klicken Sie auf die unterhalb der Karte angezeigte Schaltfläche **Erstellen**.
4. Geben Sie auf der Seite **Azure Maps-Konto erstellen** die folgenden Werte ein:
    * *Abonnement*, das Sie für dieses Konto verwenden möchten
    * Name der *Ressourcengruppe* für dieses Konto. Sie können für die Ressourcengruppe die Option *Neu erstellen* oder die Option *Vorhandene verwenden* auswählen.
    * *Name* des neuen Kontos
    * Der *Tarif* für dieses Konto.
    * Lesen Sie die *Lizenzbedingungen* und die *Datenschutzerklärung*, und aktivieren Sie zum Akzeptieren der Bestimmungen das Kontrollkästchen.
    * Klicken Sie auf die Schaltfläche **Erstellen** .

    :::image type="content" source="./media/quick-demo-map-app/create-account.png" alt-text="Erstellen eines Maps-Kontos im Portal":::

<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Abrufen des Primärschlüssels für Ihr Konto

Rufen Sie nach der Erstellung des Maps-Kontos den Primärschlüssel ab, mit dem Sie die Maps-APIs abfragen können.

1. Öffnen Sie Ihr Maps-Konto im Portal.
2. Wählen Sie im Abschnitt „Einstellungen“ die Option **Authentifizierung** aus.
3. Kopieren Sie den **Primärschlüssel** in die Zwischenablage. Speichern Sie ihn lokal zur späteren Verwendung in diesem Tutorial.

>[!NOTE]
> Wenn Sie den Abonnementschlüssel anstelle des Primärschlüssels verwenden, wird Ihre Karte nicht ordnungsgemäß gerendert. Außerdem wird aus Sicherheitsgründen empfohlen, dass Sie zwischen Ihrem Primär- und Sekundärschlüssel wechseln. Aktualisieren Sie zur Schlüsselrotation Ihre App, um den Sekundärschlüssel zu verwenden. Stellen Sie dann die App bereit, und drücken Sie die Taste für die Aktualisierung neben dem Primärschlüssel, um einen neuen Primärschlüssel zu generieren. Der alte Primärschlüssel wird deaktiviert. Weitere Informationen zur Schlüsselrotation finden Sie unter [Einrichten von Azure Key Vault mit Schlüsselrotation und Überwachung](../key-vault/secrets/tutorial-rotation-dual.md)

:::image type="content" source="./media/quick-demo-map-app/get-key.png" alt-text="Abrufen des Azure Maps-Primärschlüssels im Azure-Portal":::

## <a name="download-the-demo-application"></a>Herunterladen der Demoanwendung

1. Wechseln Sie zu [interactiveSearch.html](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/interactiveSearch.html). Kopieren Sie den Inhalt der Datei.
2. Speichern Sie den Inhalt dieser Datei lokal unter **AzureMapDemo.html**. Öffnen Sie die Datei in einem Text-Editor.
3. Suchen Sie nach der Zeichenfolge `<Your Azure Maps Key>`. Ersetzen Sie sie durch den **Primärschlüssel**, den Sie im vorherigen Abschnitt kopiert haben.

## <a name="open-the-demo-application"></a>Öffnen der Demoanwendung

1. Öffnen Sie die Datei **AzureMapDemo.html** in einem Browser Ihrer Wahl.
2. Die Karte der Stadt Los Angeles wird angezeigt. Vergrößern und verkleinern Sie sie, um zu sehen, wie die Karte abhängig vom Zoomfaktor automatisch mit mehr oder weniger Informationen gerendert wird.
3. Ändern Sie den Standardmittelpunkt der Karte. Suchen Sie in der Datei **AzureMapDemo.html** nach der Variable **center**. Ersetzen Sie den Wert des Längengrad/Breitengrad-Paars für diese Variable durch die neuen Werte **[-74,0060, 40,7128]** . Speichern Sie die Datei, und aktualisieren Sie Ihren Browser.
4. Probieren Sie die interaktiven Suchfunktionen aus. Geben Sie **Restaurants** in das Suchfeld in der oberen linken Ecke der Demowebanwendung ein.
5. Bewegen Sie den Mauszeiger über die Liste der Adressen und Standorte, die unterhalb des Suchfelds angezeigt wird. Beachten Sie dabei, wie über dem entsprechenden Pin auf der Karte Informationen zum jeweiligen Standort angezeigt werden. Zum Schutz von Privatunternehmen werden fiktive Namen und Adressen angezeigt.

    :::image type="content" source="./media/quick-demo-map-app/interactive-search.png" alt-text="Webanwendung für die interaktive Kartensuche":::


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

>[!WARNING]
>In den Tutorials, die im Abschnitt [Nächste Schritte](#next-steps) aufgeführt sind, wird ausführlich erläutert, wie Sie Azure Maps mit Ihrem Konto verwenden und konfigurieren. Wenn Sie mit den Tutorials fortfahren möchten, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen.

Falls Sie nicht mit den Tutorials fortfahren möchten, führen Sie die folgenden Schritte aus, um die Ressourcen zu bereinigen:

1. Schließen Sie den Browser, in dem die Webanwendung **AzureMapDemo.html** ausgeführt wird.
2. Navigieren Sie zur Seite mit dem Azure-Portal. Wählen Sie auf der Hauptseite des Portals **Alle Ressourcen** aus. Oder klicken Sie oben links auf das Menüsymbol. Wählen Sie **Alle Ressourcen**.
3. Klicken Sie auf Ihr Azure Maps-Konto. Klicken Sie oben auf der Seite auf **Löschen**.

Weitere Codebeispiele und eine interaktive Codierumgebung finden Sie in den folgenden Anleitungen:

[Suchen nach einer Adresse mit dem Suchdienst von Azure Maps](how-to-search-for-address.md)

[Verwenden des Azure Maps-Kartensteuerelements](how-to-use-map-control.md)

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Ihr Azure Maps-Konto und eine Demoanwendung erstellt. Sehen Sie sich die folgenden Tutorials an, um mehr über Azure Maps zu erfahren:

> [!div class="nextstepaction"]
> [Suchen nach Points of Interest in der Nähe mit Azure Maps](tutorial-search-location.md)