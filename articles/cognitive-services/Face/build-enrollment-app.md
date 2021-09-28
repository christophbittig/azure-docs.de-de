---
title: Erstellen einer React-App zum Hinzufügen von Benutzern zu einem Gesichtserkennungs-Dienst
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie Ihre Entwicklungsumgebung einrichten und eine Gesichtserkennungs-App bereitstellen, um die Zustimmung von Kunden zu erhalten.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: pafarley
ms.openlocfilehash: db6ee292a088d530ea858d75e6649684e03d92b6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123540476"
---
# <a name="build-a-react-app-to-add-users-to-a-face-service"></a>Erstellen einer React-App zum Hinzufügen von Benutzern zu einem Gesichtserkennungs-Dienst

In dieser Anleitung lernen Sie die ersten Schritte mit der Registrierungsbeispielanwendung für Gesichtserkennung kennen. Die App veranschaulicht bewährte Methoden, um eine aussagekräftige Zustimmung zum Hinzufügen von Benutzern bei einem Gesichtserkennungsdienst und zum Abrufen von hoch exakten Gesichtserkennungsdaten zu erhalten. Ein integriertes System könnte eine App wie diese verwenden, um basierend auf den entsprechenden Gesichtserkennungsdaten eine berührungslose Zugriffssteuerung, eine Identitätsprüfung, eine Anwesenheitserfassung oder einen Personalisierungskiosk bereitzustellen.

Wenn die Anwendung gestartet wird, zeigt sie den Benutzern einen detaillierten Zustimmungsbildschirm an. Wenn der Benutzer seine Zustimmung erteilt, fordert die App ihn zur Eingabe eines Benutzernamens und Kennworts auf und erfasst dann ein qualitativ hochwertiges Bild des Gesichts mithilfe der Kamera des Geräts.

Die Beispiel-App wird mit JavaScript und dem React Native-Framework geschrieben. Sie kann derzeit auf Android- und iOS-Geräten bereitgestellt werden. Weitere Bereitstellungsoptionen werden in Zukunft verfügbar gemacht.

## <a name="prerequisites"></a>Voraussetzungen 

* Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)).  
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal [eine Gesichtserkennungsressource erstellen](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace), um Ihren Schlüssel und Endpunkt zu erhalten. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.  
  * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Gesichtserkennungs-API zu verbinden.  
  * Nur für die lokale Entwicklung und Tests entsprechen der API-Schlüssel und der Endpunkt Umgebungsvariablen. Speichern Sie den API-Schlüssel für die endgültige Bereitstellung an einem sicheren Ort und nie im Code oder den Umgebungsvariablen.  

### <a name="important-security-considerations"></a>Wichtige Sicherheitsaspekte
* Für die lokale Entwicklung und anfängliche eingeschränkte Tests ist es akzeptabel (wenn auch keine bewährte Methode), Umgebungsvariablen zum Speichern des API-Schlüssels und Endpunkts zu verwenden. Bei Pilotversuchen und endgültigen Bereitstellungen sollte der API-Schlüssel sicher gespeichert werden. Dies umfasst wahrscheinlich die Verwendung eines Zwischendiensts, um ein während der Anmeldung generiertes Benutzertoken zu überprüfen. 
* Speichern Sie den API-Schlüssel oder Endpunkt in Code, oder committen Sie sie an ein Versionskontrollsystem (z. B. Git). Wenn dies versehentlich geschieht, sollten Sie sofort einen neuen API-Schlüssel bzw. Endpunkt generieren und die vorherigen widerrufen.
* Eine bewährte Methode besteht darin, separate API-Schlüssel für die Entwicklung und Produktion zu verwenden.

## <a name="set-up-the-development-environment"></a>Einrichten der Entwicklungsumgebung

#### <a name="android"></a>[Android](#tab/android)
 
1. Klonen Sie das Git-Repository für die [Beispiel-App](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Um Ihre Entwicklungsumgebung einzurichten, befolgen Sie die <a href="https://reactnative.dev/docs/environment-setup"  title=""  target="_blank">React Native-Dokumentation<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Klicken Sie auf **React Native CLI Quickstart** (Schnellstart React Native-CLI). Wählen Sie Ihr Entwicklungsbetriebssystem und **Android** als das Zielbetriebssystem aus. Vervollständigen Sie die Abschnitte **Installieren von Abhängigkeiten** und **Android-Entwicklungsumgebung**.
1. Laden Sie Ihren bevorzugten Text-Editor herunter (z. B. [Visual Studio Code](https://code.visualstudio.com/)).
1. Rufen Sie den Endpunkt und den Schlüssel für die Gesichtserkennungs-API im Azure-Portal über die Registerkarte **Übersicht** Ihrer Ressource ab. Checken Sie Ihren API-Schlüssel für die Gesichtserkennung nicht bei Ihrem Remoterepository ein.
1. Führen Sie die App entweder mit dem Android-Emulator für virtuelle Geräte aus Android Studio oder Ihrem eigenen Android-Gerät aus. Um Ihre App auf einem physischen Gerät zu testen, befolgen Sie die relevante <a href="https://reactnative.dev/docs/running-on-device"  title=""  target="_blank">React Native-Dokumentation<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

#### <a name="ios"></a>[iOS](#tab/ios)

1. Klonen Sie das Git-Repository für die [Beispiel-App](https://github.com/azure-samples/cognitive-services-FaceAPIEnrollmentSample).
1. Um Ihre Entwicklungsumgebung einzurichten, befolgen Sie die <a href="https://reactnative.dev/docs/environment-setup"  title=""  target="_blank">React Native-Dokumentation<span class="docon docon-navigate-external x-hidden-focus"></span></a>. Klicken Sie auf **React Native CLI Quickstart** (Schnellstart React Native-CLI). Wählen Sie **macOS** als Entwicklungsbetriebssystem und **iOS** als Zielbetriebssystem aus. Schließen Sie den Abschnitt **Installing dependencies** (Installieren von Abhängigkeiten) ab.
1. Laden Sie Ihren bevorzugten Text-Editor herunter (z. B. [Visual Studio Code](https://code.visualstudio.com/)). Außerdem müssen Sie Xcode herunterladen. 
1. Rufen Sie den Endpunkt und den Schlüssel für die Gesichtserkennungs-API im Azure-Portal über die Registerkarte **Übersicht** Ihrer Ressource ab. Checken Sie Ihren API-Schlüssel für die Gesichtserkennung nicht bei Ihrem Remoterepository ein.
1. Führen Sie die App entweder mithilfe eines simulierten Geräts von Xcode oder mit Ihrem eigenen iOS-Gerät aus. Um Ihre App auf einem physischen Gerät zu testen, befolgen Sie die relevante <a href="https://reactnative.dev/docs/running-on-device"  title=""  target="_blank">React Native-Dokumentation<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="create-a-user-add-experience"></a>Erstellen einer Benutzeroberfläche zum Hinzufügen von Benutzern  

Nachdem Sie nun die Beispiel-App eingerichtet haben, können Sie sie an Ihre eigenen Anforderungen anpassen.

Beispielsweise können Sie auf Ihrer Zustimmungsseite situationsabhängige Informationen hinzufügen:

> [!div class="mx-imgBorder"]
> ![App-Zustimmungsseite](./media/enrollment-app/1-consent-1.jpg)

Viele Gesichtserkennungsprobleme werden durch Referenzbilder von niedriger Qualität verursacht. Einige Faktoren, die die Leistung des Modells beeinträchtigen können, sind:
* Gesichtsgröße (Gesichter, die weit von der Kamera entfernt sind)
* Gesichtsausrichtung (geneigte oder von der Kamera abgewendete Gesichter)
* Schlechte Beleuchtungsbedingungen (zu wenig Licht oder Gegenlicht), bei denen das Bild unterbelichtet wird oder zu viel Rauschen auftritt.
* Verdeckung (teilweise verborgene oder verdeckte Gesichter), einschließlich Accessoires wie Hüten oder Brillen mit dickem Gestell.
* Verschwommen/Weichzeichnung (z. B. durch schnelle Bewegung des Gesichts während der Aufnahme des Fotos). 

Der Dienst bietet Bildqualitätstests, um Ihnen bei der Entscheidung zu helfen, ob die Qualität des Bilds auf der Grundlage der oben genannten Faktoren ausreichend ist, um den Kunden hinzuzufügen oder die Gesichtserkennung zu versuchen. Diese App veranschaulicht, wie Sie von der Kamera des Geräts aus auf Frames zugreifen, Qualität erkennen und dem Benutzer Benutzeroberflächennachrichten anzeigen, um ihm zu helfen, ein Bild mit höherer Qualität zu erfassen, die Frames mit der höchsten Qualität auszuwählen und das erkannte Gesicht dem Gesichtserkennungs-API-Dienst hinzuzufügen. 


> [!div class="mx-imgBorder"]
> ![Seite mit Anweisungen für die App-Bilderfassung](./media/enrollment-app/4-instruction.jpg)

Beachten Sie, dass die App auch Funktionen zum Löschen der Information des Benutzers sowie die Option zum erneuten Hinzufügen bietet.

> [!div class="mx-imgBorder"]
> ![Profilverwaltungsseite](./media/enrollment-app/10-manage-2.jpg)

Um die Funktionalität der App so zu erweitern, dass Sie die gesamte Erfahrung abdeckt, lesen Sie die [Übersicht](enrollment-overview.md), wo Sie zusätzliche zu implementierende Funktionen und bewährte Methoden finden.

## <a name="deploy-the-app"></a>Bereitstellen der App

#### <a name="android"></a>[Android](#tab/android)

Stellen Sie zunächst sicher, dass Ihre App für die Produktionsbereitstellung bereit ist: Entfernen Sie alle Schlüssel oder Geheimnisse aus dem App-Code, und stellen Sie sicher, dass Sie die [bewährten Sicherheitsmethoden](../cognitive-services-security.md?tabs=command-line%2ccsharp) befolgt haben.

Wenn Sie bereit sind, Ihre App für die Produktion freizugeben, generieren Sie eine releasebereite APK-Datei, bei der es sich um das Paketdateiformat für Android-Apps handelt. Diese APK-Datei muss mit einem privaten Schlüssel signiert werden. Mit diesem Releasebuild können Sie beginnen, die App direkt an Ihre Geräte zu verteilen. 

Befolgen Sie die Dokumentation zur <a href="https://developer.android.com/studio/publish/preparing#publishing-build"  title="Vorbereiten für das Release"  target="_blank">Vorbereiten für das Release<span class="docon docon-navigate-external x-hidden-focus"></span></a>, um zu erfahren, wie Sie einen privaten Schlüssel generieren, Ihre Anwendung signieren und ein APK-Releasepaket generieren.  

Wenn Sie ein signiertes APK-Paket erstellt haben, finden Sie weitere Informationen zum Freigeben Ihrer App in der Dokumentation <a href="https://developer.android.com/studio/publish"  title="Veröffentlichen Ihrer App"  target="_blank">Veröffentlichen Ihrer App<span class="docon docon-navigate-external x-hidden-focus"></span></a>.

#### <a name="ios"></a>[iOS](#tab/ios)

Stellen Sie zunächst sicher, dass Ihre App für die Produktionsbereitstellung bereit ist: Entfernen Sie alle Schlüssel oder Geheimnisse aus dem App-Code, und stellen Sie sicher, dass Sie die [bewährten Sicherheitsmethoden](../cognitive-services-security.md?tabs=command-line%2ccsharp) befolgt haben. Sie müssen zur Vorbereitung der Veröffentlichung ein App-Symbol und einen Startbildschirm erstellen und Einstellungen für die Bereitstellungsinformationen konfigurieren. Befolgen Sie die [Dokumentation von Xcode](https://developer.apple.com/documentation/Xcode/preparing_your_app_for_distribution), um Ihre App für die Veröffentlichung vorzubereiten. 

Wenn Sie bereit sind, Ihre App für die Produktion freizugeben, erstellen Sie ein Archiv Ihrer App. Befolgen Sie die [Xcode-Dokumentation](https://developer.apple.com/documentation/Xcode/distributing_your_app_for_beta_testing_and_releases) zum Erstellen eines Archivbuilds und die Optionen zum Verteilen Ihrer App.  

---

## <a name="next-steps"></a>Nächste Schritte  

In dieser Anleitung haben Sie erfahren, wie Sie Ihre Entwicklungsumgebung einrichten und mit der Beispiel-App beginnen. Wenn Sie nicht mit React Native vertraut sind, können Sie die dazugehörige [Dokumentation zu den ersten Schritten](https://reactnative.dev/docs/getting-started) lesen, um weitere Hintergrundinformationen zu erhalten. Es kann auch hilfreich sein, sich selbst mit der [Gesichtserkennungs-API](Overview.md) vertraut zu machen. Lesen Sie die anderen Abschnitte zum Hinzufügen von Benutzern zur App, bevor Sie mit der Entwicklung beginnen.
