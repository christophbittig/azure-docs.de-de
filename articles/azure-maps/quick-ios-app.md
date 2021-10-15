---
title: Erstellen einer iOS-App
description: Schritte zum Erstellen eines Azure Maps-Kontos und der ersten iOS-App.
author: deniseatmicrosoft
ms.author: v-dbogomolov
ms.date: 09/30/2021
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 46e5ec41c0cea9a417b920e8441ff1129040c209
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389606"
---
#  <a name="create-an-ios-app-public-preview"></a>Erstellen einer iOS-App (Public Preview)

In diesem Artikel wird beschrieben, wie Sie Azure Maps einer iOS-App hinzufügen. Die folgenden grundlegenden Schritte werden erläutert:

* Einrichten Ihrer Entwicklungsumgebung
* Erstellen eines eigenen Azure Maps-Kontos
* Abrufen Ihres primären Azure Maps-Schlüssels für die Verwendung in der App
* Verweisen auf die Azure Maps-Bibliotheken aus dem Projekt
* Hinzufügen eines Azure Maps-Steuerelements zur App

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen Sie ein Azure Maps-Konto, indem Sie sich beim [Azure-Portal](https://portal.azure.com/) anmelden. Sollten Sie über kein Azure-Abonnement verfügen, können Sie zunächst ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen.
* [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account)
* [Rufen Sie einen Primärschlüssel ab](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt). Weitere Informationen zur Authentifizierung in Azure Maps finden Sie unter [Verwalten der Authentifizierung in Azure Maps](how-to-manage-authentication.md).
* Laden Sie [Xcode](https://apps.apple.com/cz/app/xcode/id497799835?mt=12) kostenlos aus dem Mac App Store herunter.

## <a name="create-an-azure-maps-account"></a>Erstellen eines Azure Maps-Kontos

Erstellen Sie mithilfe der folgenden Schritte ein neues Azure Maps-Konto:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) links oben auf **Ressource erstellen**.

2. Geben Sie im Feld _Marketplace durchsuchen_ die Begriffe **Azure Maps** ein.

3. Wählen Sie in den _Ergebnissen_ die Option **Azure Maps** aus. Klicken Sie auf die unterhalb der Karte angezeigte Schaltfläche **Erstellen**.

4. Geben Sie auf der Seite **Azure Maps-Konto erstellen** die folgenden Werte ein:
   - _Abonnement_, das Sie für dieses Konto verwenden möchten
   - Name der _Ressourcengruppe_ für dieses Konto. Sie können für die Ressourcengruppe die Option _Neu erstellen_ oder die Option _Vorhandene verwenden_ auswählen.
   - _Name_ des neuen Kontos
   - Der _Tarif_ für dieses Konto.
   - Lesen Sie die _Lizenzbedingungen_ und die _Datenschutzerklärung_, und aktivieren Sie zum Akzeptieren der Bestimmungen das Kontrollkästchen.
   - Klicken Sie auf die Schaltfläche **Erstellen** .

   :::image source="./media/quick-ios-app/create-account.png" alt-text="Screenshot: Erstellen eines Azure Maps-Kontos":::

## <a name="get-the-primary-key-for-your-account"></a>Abrufen des Primärschlüssels für Ihr Konto

Rufen Sie nach der Erstellung des Maps-Kontos den Primärschlüssel ab, mit dem Sie die Maps-APIs abfragen können.

1. Öffnen Sie Ihr Maps-Konto im Portal.

2. Wählen Sie im Abschnitt „Einstellungen“ die Option **Authentifizierung** aus.

3. Kopieren Sie den **Primärschlüssel** in die Zwischenablage. Speichern Sie ihn lokal zur späteren Verwendung in diesem Tutorial.

   > [!Note]
   > Wenn Sie den Azure-Abonnementschlüssel anstelle des Azure Maps-Primärschlüssels verwenden, wird Ihre Karte nicht richtig gerendert. Außerdem wird aus Sicherheitsgründen empfohlen, dass Sie zwischen Ihrem Primär- und Sekundärschlüssel wechseln. Aktualisieren Sie zur Schlüsselrotation Ihre App, um den Sekundärschlüssel zu verwenden. Stellen Sie dann die App bereit, und drücken Sie die Taste für die Aktualisierung neben dem Primärschlüssel, um einen neuen Primärschlüssel zu generieren. Der alte Primärschlüssel wird deaktiviert. Weitere Informationen zur Schlüsselrotation finden Sie unter [Einrichten von Azure Key Vault mit Schlüsselrotation und Überwachung](../key-vault/secrets/tutorial-rotation-dual.md)

   :::image source="./media/quick-ios-app/get-key.png" alt-text="Screenshot: Abrufen des Abonnementschlüssels":::

## <a name="create-a-project-in-xcode"></a>Erstellen eines Projekts in Xcode

Erstellen Sie zunächst ein neues iOS-App-Projekt. Gehen Sie wie folgt vor, um ein Xcode-Projekt zu erstellen:

1. Wählen Sie unter **Datei** die Option **Neu** -> **Projekt** aus.

2. Wählen Sie auf der Registerkarte **iOS** die Option **App** aus, und klicken Sie dann auf **Weiter**.

3. Geben Sie den App-Namen und die Paket-ID ein, und klicken Sie auf **Weiter**.

   Weitere Hilfe zum Erstellen eines neuen Projekts finden Sie unter [Erstellen eines Xcode-Projekts für eine App](https://developer.apple.com/documentation/xcode/creating-an-xcode-project-for-an-app).

:::image source="./media/quick-ios-app/create-app.png" alt-text="Erstellen der ersten iOS-Anwendung":::

## <a name="install-the-azure-maps-ios-sdk"></a>Installieren des iOS SDK für Azure Maps

Als Nächstes müssen Sie zur Erstellung Ihrer Anwendung das iOS SDK für Azure Maps installieren. Führen Sie die folgenden Schritte aus, um das SDK zu installieren:

1. Wählen Sie die Projektdatei im **Projektnavigator** aus.

2. Wählen Sie das Projekt im geöffneten **Projekt-Editor** aus.

3. Wechseln Sie zur Registerkarte **Swift-Paket**.

4. Fügen Sie das iOS SDK für Azure Maps hinzu: `{link goes here}`

   :::image source="./media/quick-ios-app/add-project.png" alt-text="Screenshot: Hinzufügen eines iOS-Projekts":::
  
## <a name="add-mapcontrol-view"></a>Hinzufügen der MapControl-Ansicht

1. Fügen Sie dem Ansichtscontroller das benutzerdefinierte `UIView`-Element hinzu.

2. Wählen Sie die Klasse `MapControl` aus dem Modul `AzureMapsControl` aus.

   :::image source="./media/quick-ios-app/add-map-control.png" alt-text="Screenshot: Hinzufügen des Azure Maps-Steuerelements":::

3. Gehen Sie in der Datei **AppDelegate.swift** folgendermaßen vor:

   - Fügen Sie einen Import für das Azure Maps SDK hinzu.
   - Legen Sie Ihre Azure Maps-Authentifizierungsinformationen fest.
   
   Wenn Sie die Authentifizierungsinformationen für die AzureMaps-Klasse global über die Methode `AzureMaps.configure(subscriptionKey:)` oder `AzureMaps.configure(aadClient:aadAppId:aadTenant:)` festlegen, müssen Sie nicht für jede Ansicht Ihre Authentifizierungsinformationen hinzufügen.

4. Klicken Sie wie in der folgenden Grafik dargestellt auf die Schaltfläche „Ausführen“ (bzw. drücken Sie `CMD` + `R`), um Ihre Anwendung zu erstellen.

   :::image source="./media/quick-ios-app/run.png" alt-text="Screenshot: Ausführen der iOS-Anwendung":::

   Es dauert ein paar Sekunden, bis Xcode die Anwendung erstellt hat. Nach Abschluss des Builds können Sie Ihre Anwendung auf dem simulierten iOS-Gerät testen. Es sollte eine Zuordnung wie diese angezeigt werden:

   :::image source="./media/quick-ios-app/example.png" alt-text="Screenshot: Ihre erste Karte in der iOS-Anwendung":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

> [!WARNING]
> In den Tutorials, die im Abschnitt [Nächste Schritte](#next-steps) aufgeführt sind, wird ausführlich erläutert, wie Sie Azure Maps mit Ihrem Konto verwenden und konfigurieren. Wenn Sie mit den Tutorials fortfahren möchten, sollten Sie die in diesem Schnellstart erstellten Ressourcen nicht bereinigen.

Falls Sie nicht mit den Tutorials fortfahren möchten, führen Sie die folgenden Schritte aus, um die Ressourcen zu bereinigen:

1. Schließen Sie Xcode, und löschen Sie das erstellte Projekt.
2. Falls Sie die Anwendung auf einem externen Gerät getestet haben, sollten Sie die Anwendung auf diesem Gerät deinstallieren.

Gehen Sie wie folgt vor, falls Sie die Entwicklungsarbeit mit dem iOS SDK für Azure Maps nicht fortsetzen möchten:

1. Navigieren Sie zur Seite mit dem Azure-Portal. Wählen Sie auf der Hauptseite des Portals **Alle Ressourcen** aus. Oder klicken Sie oben links auf das Menüsymbol. Wählen Sie **Alle Ressourcen**.
2. Klicken Sie auf Ihr Azure Maps-Konto. Klicken Sie oben auf der Seite auf **Löschen**.
3. Sie können Xcode deinstallieren, falls Sie die Entwicklung von iOS-Apps nicht fortsetzen möchten.

<!--
For more code examples, see these guides:

*  [Manage authentication in Azure Maps](how-to-manage-authentication.md)
*  [Change map styles in iOS maps](Set%20map%20style%20%28iOS%20SDK%29.md)
*  [Add a symbol layer](Add%20a%20symbol%20layer%20%28iOS%20SDK%29.md)
*  [Add a line layer](Add%20a%20line%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
*  [Add a polygon layer](Add%20a%20polygon%20layer%20to%20the%20map%20%28iOS%20SDK%29.md)
-->

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Ihr Azure Maps-Konto und eine Demoanwendung erstellt. Sehen Sie sich die folgenden Tutorials an, um mehr über Azure Maps zu erfahren:

> [!div class="nextstepaction"]
> [Laden von GeoJSON-Daten in das Android SDK für Azure Maps](tutorial-load-geojson-file-android.md)
