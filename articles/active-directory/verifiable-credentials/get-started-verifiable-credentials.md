---
title: 'Tutorial: Erste Schritte mit Azure Active Directory-Nachweisen im Rahmen einer Beispiel-App (Vorschau)'
description: In diesem Tutorial erfahren Sie anhand einer Beispiel-App und eines Testmandanten, wie Sie Nachweise ausstellen.
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
manager: karenh444
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ROBOTS: NOINDEX
ms.openlocfilehash: 096ca46c2a2c7c54553005fcb4a264c6ef8221d2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049176"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>Tutorial: Erste Schritte mit Azure Active Directory-Nachweisen im Rahmen einer Beispiel-App (Vorschau)

In diesem Tutorial erfahren Sie, welche Schritte erforderlich sind, um Ihren ersten Nachweis auszustellen: eine Karte für verifizierte Anmeldeinformationsexperten. Mithilfe dieser Karte können Sie dann gegenüber eines Verifizierers belegen, dass Sie ein verifizierter Anmeldeinformationsexperte sind, der bestens mit digitalen Leistungsnachweisen vertraut ist. Verwenden Sie für die ersten Schritte mit Azure Active Directory-Nachweisen die Beispiel-App für Nachweise, um Ihren ersten Nachweis auszustellen.

![Abbildung einer Beispielkarte](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory-Nachweise befinden sich derzeit in der Public Preview-Phase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

- Installation von [Node.js](https://nodejs.org/en/download/) ab Version 10.14 auf dem Testsystem
- Wenn Sie das Repository klonen möchten, das als Host für die Beispiel-App fungiert, muss [Git](https://git-scm.com/downloads) installiert sein.
- [Visual Studio Code](https://code.visualstudio.com/Download)
- System zum Hosten unseres Beispielwebsite
- Mobiles Gerät, auf dem mindestens die Version 6.2005.3599 von Microsoft Authenticator installiert ist
- [ngrok](https://ngrok.com/) (kostenlos)

## <a name="download-the-sample-code"></a>Herunterladen des Beispielcodes

Um sich selbst eine Karte für verifizierte Anmeldeinformationsexperten ausstellen zu können, müssen Sie eine Website auf Ihrem lokalen Computer ausführen. Die Website wird verwendet, um die Ausstellung eines Nachweises zu initiieren. In diesem Tutorial wird eine einfache, in Node.js geschriebene Website verwendet.

Laden Sie zuerst unseren [Beispielcode auf GitHub](https://github.com/Azure-Samples/active-directory-verifiable-credentials) herunter, oder klonen Sie das Repository auf Ihrem lokalen Computer:

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

Wir empfehlen Ihnen, sich mit dem Code der Beispielwebsites vertraut zu machen. Der Ordner `issuer` enthält den gesamten Code, der zum Ausstellen eines Nachweises verwendet wird. Ausführlichere Informationen finden Sie in der [Infodatei](https://github.com/Azure-Samples/active-directory-verifiable-credentials) des Beispiels.

## <a name="run-the-issuer-website"></a>Ausführen der Ausstellerwebsite

Die Schritte können in Visual Studio Code oder an einer beliebigen Befehlszeile Ihres Betriebssystems ausgeführt werden. 

1. Navigieren Sie zum Ordner `issuer`. 

    ```terminal
    cd issuer
    ```

2. Installieren Sie alle erforderlichen Pakete, und starten Sie die Website.

   ```terminal
    npm install
    node app.js
    ```

3. Im Terminal sehen Sie nun, dass Ihre Aussteller-App am Port 8081 lauscht. Richten Sie nun mit ngrok einen Reverseproxy ein, damit Authenticator mit Ihrer App kommunizieren kann. 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>Erstellen eines Reverseproxys mit ngrok

Wenn Sie die Beispielwebsite ausführen, muss Ihr Gerät mit dem Knotenserver kommunizieren, der auf Ihrem lokalen Computer ausgeführt wird. Mit [ngrok](https://ngrok.com/) können Sie Ihren lokalen Entwicklungsserver ganz einfach über das Internet verfügbar machen.

1.  Führen Sie nach dem Herunterladen und Extrahieren von **ngrok** Folgendes aus:

    ```terminal
     ngrok http 8081
    ```

Die Beispielwebsite wird standardmäßig am Port `8081` ausgeführt. Von **ngrok** werden zwei Weiterleitungs-URLs für Ihren Server ausgegeben. Kopieren Sie die URL mit dem Präfix `https://`.

![ngrok hilft Ihnen dabei, Ihre Anwendungsendpunkte über das Internet verfügbar zu machen.](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> Bei Verwendung von PowerShell müssen Sie ggf. `./ngrok` eingeben, damit der Befehl erkannt wird.

Nachdem Ihr lokaler Port nun mithilfe von ngrok über das Internet verfügbar gemacht wurde, verwendet die Beispielwebsite automatisch den von ngrok generierten Hostnamen. Öffnen Sie Ihren Browser, und navigieren Sie zur HTTPS-Weiterleitungs-URL von ngrok. Daraufhin sollte die Homepage der Beispielwebsite angezeigt werden. Wenn die Seite geöffnet wird, kann Ihr Gerät mit der auf Ihrem lokalen Server ausgeführten Beispiel-App kommunizieren. Nun können Sie sich selbst eine Karte für verifizierte Anmeldeinformationsexperten ausstellen.

## <a name="issue-a-credential"></a>Erstellen eines Nachweises

1. Installieren Sie Authenticator auf Ihrem mobilen Gerät. Mit Microsoft Authenticator können Sie Nachweise empfangen, speichern und interessierten Parteien präsentieren.

2. Stellen Sie sich als Nächstes einen Nachweis aus. **Klicken** Sie auf die Schaltfläche **Get Credential** (Nachweis abrufen). Nach dem Klicken auf die Schaltfläche **Get Credential** (Nachweis abrufen) wird auf der Beispielwebsite ein QR-Code angezeigt, den Sie mithilfe von Authenticator scannen können. Wenn Sie die Website im Browser auf Ihrem mobilen Gerät anzeigen, wird durch Klicken auf die Schaltfläche **Get Credential** (Nachweis abrufen) ein Deep-Link ausgelöst, der die Authenticator-App ohne Scannen eines QR-Codes öffnet.

   ![Schaltfläche zum Abrufen eines Nachweises](media/get-started-verifiable-credentials/credential-expert-get.png)

3. Scannen Sie den auf der Website angezeigten QR-Code mithilfe von Authenticator, oder klicken Sie bei Verwendung eines mobilen Geräts auf die Schaltfläche zum Abrufen eines Nachweises, um den Deep-Link auszulösen. 

   ![QR-Code ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. Wie Sie sehen, ist die Schaltfläche **Add** (Hinzufügen) momentan abgeblendet. Wählen Sie unter dem Kartenbild die Option **Sign in to your account** (Bei Ihrem Konto anmelden) aus.

   ![Anmelden ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. Bevor Sie Ihre Karte für Anmeldeinformationsexperten erhalten, müssen Sie gegenüber dem von uns verwendeten Mandanten Authentifizierungsinformationen angeben. Wenn Sie das Tutorial zum ersten Mal absolvieren, verfügen Sie noch nicht über ein Konto für Anmeldeinformationsexperten. Erstellen Sie in diesem Fall ein neues Benutzerkonto in unserem B2C-Mandanten.

   ![Authentifizieren Sie sich, bevor Sie fortfahren.](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. Nach der Anmeldung ist die Schaltfläche **Add** (Hinzufügen) nicht mehr abgeblendet. Wählen Sie **Add** (Hinzufügen) aus, um Ihren neuen Nachweis zu akzeptieren.

   ![Wählen Sie „Add“ (Hinzufügen) aus, nachdem Sie sich authentifiziert haben.](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. Herzlichen Glückwunsch! Sie verfügen nun über einen Nachweis für verifizierte Anmeldeinformationsexperten.

   ![Nachweis für verifizierte Anmeldeinformationsexperten hinzugefügt](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
Nun muss Ihr Nachweis verifiziert werden.

## <a name="validate-credentials"></a>Anmeldeinformationen überprüfen

Nachdem Sie nun den Ausstellungsteil des Tutorials abgeschlossen haben und in Authenticator über einen Nachweis verfügen, muss dieser in Ihrer eigenen Verifizierungs-App validiert werden.

1.  Beenden Sie die Ausführung des ngrok-Diensts für Ihren Aussteller.

    ```terminal
     control+c
    ```


2. Öffnen Sie in einem anderen Terminalfenster den Ordner der Verifizierungs-App, und führen Sie sie aus. (Orientieren Sie sich dabei an der Vorgehensweise für die Aussteller-App.)

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. Führen Sie nun ngrok mit dem Verifizierungs-Port 8082 aus.

    ```terminal
    ngrok http 8082
    ```

4. Öffnen Sie in Ihrem Browser die HTTPS-Weiterleitungs-URL von ngrok, und tippen Sie auf die Schaltfläche zum **Verifizieren der Anmeldeinformationen**.  

   ![Schaltfläche zum Verifizieren des Expertennachweises](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. Öffnen Sie Authenticator, und scannen Sie den QR-Code.

   ![Scannen des QR-Codes, um den Nachweis zu verifizieren](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > iOS: rechts oben. Android: rechts unten. Scannen Sie den QR-Code.

6. Wählen Sie auf dem Authenticator-Bildschirm „Neue Berechtigungsanforderung“ die Option **Zulassen** aus. Durch Auswählen von „Zulassen“ wird eine verifizierbare Präsentation mit Ihrem dezentralisierten Bezeichner (Decentralized Identifier, DID) signiert, um nachzuweisen, dass Sie tatsächlich die Kontrolle über diesen Nachweis haben.

   ![Neue Berechtigungsanforderung](media/get-started-verifiable-credentials/new-permission-request.png)

    Nach erfolgreicher Präsentation sollten drei Dinge aktualisiert werden:

   1. Auf der Webseite sollte nun „Congratulations, <Ihr Name> is a Verified Credential Expert!“ (Glückwunsch! <Ihr Name> ist ein verifizierter Anmeldeinformationsexperte.) angezeigt werden.
   
    ![Glückwunsch und Schaltfläche für erneute Verifizierung](media/get-started-verifiable-credentials/congratulations.png)


   2. Im Terminal der Verifizierungs-App sollte die gleiche Nachricht aus den Protokollen angezeigt werden.
   
    ![Anwendungsaktivität an der Eingabeaufforderung](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. In Authenticator sollte unter „Letzte Aktivität“ ein Eintrag für diese Präsentation vorhanden sein.

    ![Aktivität in Authenticator](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> Während der Ausführung der Verifizierungs-App kann es vorkommen, dass ngrok nicht mehr funktioniert und einen Fehler mit einem Hinweis auf zu viele Verbindungen anzeigt. Dies lässt sich vermeiden, indem Sie Ihr Konto bei ngrok registrieren. 

## <a name="next-steps"></a>Nächste Schritte

Nach erfolgreichem Abschluss der Schnellstartanleitung können Sie im Dienst für Azure AD-Nachweise Ihren eigenen dezentralisierte Bezeichner erstellen und Ihren eigenen Nachweis ausstellen.

>[!div class="nextstepaction"] 
>[Tutorial - Konfigurieren Sie Ihr Azure Active Directory so, dass es überprüfbare Anmeldedaten ausgibt (Vorschau)](./enable-your-tenant-verifiable-credentials.md)
