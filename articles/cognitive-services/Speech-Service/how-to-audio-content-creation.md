---
title: 'Speech-Dienst: Audio Content Creation'
titleSuffix: Azure Cognitive Services
description: Audio Content Creation ist ein Onlinetool, mit dem Sie die Sprachsynthese von Microsoft an Ihre Apps und Produkte anpassen und optimieren können.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: eur
ms.openlocfilehash: 866bb0c361856a906e71d47dd19891c276a450ca
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507662"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Verbessern der Synthese mit dem Audio Content Creation-Tool

[Audioinhaltserstellung](https://aka.ms/audiocontentcreation) ist ein benutzerfreundliches und leistungsfähiges Tool, mit dem Sie sehr natürliche Audioinhalte für eine Vielzahl von Szenarien erstellen können, wie etwa Hörbücher, Nachrichtensendungen, Videoerzählungen und Chatbots. Mit Audioinhaltserstellung können Sie Stimmen der Sprachsynthese optimieren und benutzerdefinierte Audioerlebnisse auf effiziente und preiswerte Weise gestalten.

Das Tool basiert auf der [Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML)](speech-synthesis-markup.md). Es ermöglicht Ihnen das Anpassen von Ausgabeattributen der Sprachsynthese im Echtzeit- oder Stapelmodus, wie etwa Stimmmerkmale, Sprachstile, Sprechgeschwindigkeit, Aussprache und Prosodie.

Sie erhalten einfachen Zugang zu mehr als 150 vordefinierten Stimmen für über 60 verschiedene Sprachen, einschließlich der neuronalen TTS-Stimmen nach dem neuesten Stand der Technik und Ihrer benutzerdefinierten Stimme, wenn Sie eine erstellt haben.

Informationen dazu finden Sie im [Videotutorial](https://youtu.be/ygApYuOOG6w) zu Audio Content Creation.

## <a name="how-to-get-started"></a>Erste Schritte

Audioinhaltserstellung ist ein kostenloses Tool, die Nutzung des in Anspruch genommenen Azure Speech-Diensts wird Ihnen aber in Rechnung gestellt. Damit Sie mit dem Tool arbeiten können, müssen Sie sich mit einem Azure-Konto anmelden und eine Sprachressource erstellen. Für jedes Azure-Konto verfügen Sie über monatliche kostenlose Sprachkontingente, die 500.000 Zeichen für neuronale TTS-Stimmen (pro Monat), 5 Millionen Zeichen für Standard-und benutzerdefinierte Stimmen (pro Monat) und 1 Hostingdienst für einen benutzerdefinierten Sprachendpunkt (pro Monat) beinhalten. Die monatlich zugeteilte Menge ist in der Regel ausreichend für ein kleines Inhaltsteam aus ca. 3–5 Personen. Dies sind die Schritte zum Erstellen eines Azure-Kontos und zum Abrufen einer Sprachressource.

### <a name="step-1---create-an-azure-account"></a>Schritt 1: Erstellen eines Azure-Kontos

Für das Arbeiten mit der Audioinhaltserstellung benötigen Sie ein [Microsoft-Konto](https://account.microsoft.com/account) und ein [Azure-Konto](https://azure.microsoft.com/free/ai/). Befolgen Sie diese Anweisungen, um [das Konto einzurichten](./overview.md#try-the-speech-service-for-free).

Das [Azure-Portal](https://portal.azure.com/) ist der zentrale Ort zum Verwalten Ihres Azure-Kontos. Sie können die Sprachressource erstellen, den Zugriff auf das Produkt verwalten und alles von einfachen Web-Apps bis zu komplexen Cloudbereitstellungen überwachen.

### <a name="step-2---create-a-speech-resource"></a>Schritt 2: Erstellen einer Speech-Ressource

Nachdem Sie sich für das Azure-Konto registriert haben, müssen Sie eine Speech-Ressource unter Ihrem Azure-Konto erstellen, um auf die Speech-Dienste zuzugreifen. Sehen Sie sich die Anweisungen zum [Erstellen einer Speech-Ressource](./overview.md#create-the-azure-resource) an.

Es dauert einen Moment, bis die neue Speech-Ressource bereitgestellt wird. Sobald die Bereitstellung abgeschlossen ist können Sie Ihre Reise in der Audioinhaltserstellung antreten.

 > [!NOTE]
   > Wenn Sie neuronale Stimmen verwenden möchten, achten Sie darauf, Ihre Ressource in einer [Region, die neuronale Stimmen unterstützt](regions.md#neural-and-standard-voices) zu erstellen.

### <a name="step-3---log-into-the-audio-content-creation-with-your-azure-account-and-speech-resource"></a>Schritt 3: Anmelden bei der Audioinhaltserstellung mit Ihrem Azure-Konto und der Speech-Ressource

1. Nachdem Sie das Azure-Konto und die Speech-Ressource erhalten haben, können Sie sich bei der [Audioinhaltserstellung](https://aka.ms/audiocontentcreation) anmelden, indem Sie auf **Erste Schritte** klicken.
2. Auf der Homepage werden alle Produkte unter Speech Studio aufgeführt. Klicken Sie zum Starten auf **Audioinhaltserstellung**.
3. Die **Begrüßungsseite von Speech Studio** wird angezeigt, damit Sie den Speech-Dienst einrichten können. Wählen Sie das Azure-Abonnement und die Speech-Ressource aus, an der Sie arbeiten möchten. Klicken Sie auf **Ressource verwenden**, um die Einstellungen zu vervollständigen. Bei Ihrer nächsten Anmeldung beim Audioinhaltserstellungs-Tool stellen wir eine direkte Verknüpfung Ihres Kontos mit den Audioarbeitsdateien unter der aktuellen Sprachressource her. Sie können die Details und den Status Ihrer Azure-Abonnements im [Azure-Portal](https://portal.azure.com/) überprüfen. Wenn Sie über keine Speech-Ressource verfügen und Besitzer oder Administrator eines Azure-Abonnements sind, können Sie auch eine neue Speech-Ressource in Speech Studio erstellen, indem Sie auf **Neue Ressource erstellen** klicken. Wenn Sie eine Benutzerrolle für ein bestimmtes Azure-Abonnement besitzen, verfügen Sie möglicherweise nicht über die Berechtigung zum Erstellen einer neuen Speech-Ressource. Wenden Sie sich an Ihren Administrator, um Zugriff auf die Speech-Ressource zu erhalten. 
4. Sie können Ihre Speech-Ressource jederzeit ändern, indem Sie im oberen Navigationsbereich die Option **Einstellungen** auswählen.
5. Wenn Sie das Verzeichnis wechseln möchten, wechseln Sie zu **Einstellungen** oder zu Ihrem Profil, um zu arbeiten. 

## <a name="how-to-use-the-tool"></a>So verwenden Sie das Tool:

Das folgende Diagramm zeigt die erforderlichen Schritte zum Optimieren der Sprachsyntheseausgaben. Verwenden Sie die unten angegebenen Links, um mehr über die einzelnen Schritte zu erfahren.

:::image type="content" source="media/audio-content-creation/audio-content-creation-diagram.jpg" alt-text="Ein Diagramm der erforderlichen Schritte zum Optimieren der Sprachsyntheseausgaben":::

1. Wählen Sie die Sprachressource aus, mit der Sie arbeiten möchten.
2. [Erstellen Sie eine Datei zur Audiooptimierung](#create-an-audio-tuning-file) als Nur-Text- oder SSML-Skript. Geben Sie Ihre Inhalte in der Audioinhaltserstellung ein.
3. Wählen Sie die Stimme und die Sprache für Ihren Skriptinhalt aus. Audio Content Creation umfasst alle [Stimmen der Microsoft-Sprachsynthese](language-support.md#text-to-speech). Sie können eine Standardstimme, eine neuronale Stimme oder Ihre eigene benutzerdefinierte Stimme verwenden.
   > [!NOTE]
   > Der Zugriff auf benutzerdefinierte neuronale Stimmen ist nur eingeschränkt möglich. Hiermit können Sie High-Definition-Stimmen erstellen, die wie natürliche Stimmen klingen. Weitere Einzelheiten finden Sie unter [Zulassung](./text-to-speech.md).

4. Wählen Sie den Inhalt aus, den Sie in der Vorschau anzeigen möchten, und klicken Sie auf das **Wiedergabesymbol** (ein Dreieck), um eine Vorschau der Standardsyntheseausgabe anzuzeigen. Beachten Sie, dass Sie beim Durchführen von Änderungen am Text auf das Symbol **Beenden** und dann erneut auf das **Wiedergabesymbol** klicken müssen, um die Audiodatei mit geänderten Skripts erneut zu generieren. 
5. Verbessern Sie die Ausgabe, indem Sie Aussprache, Pausen, Tonhöhe, Geschwindigkeit, Intonation, Sprachstil usw. anpassen. Eine umfassende Liste der Optionen finden Sie unter [Markupsprache für Sprachsynthese](speech-synthesis-markup.md). In [diesem Video](https://youtu.be/ygApYuOOG6w) wird gezeigt, wie Sie die Sprachausgabe mit Audio Content Creation optimieren.
6. Speichern und [exportieren Sie die optimierten Audiodaten](#export-tuned-audio). Nachdem Sie die Optimierungsspur im System gespeichert haben, können Sie die Ausgabe weiter bearbeiten. Wenn Sie mit der Ausgabe zufrieden sind, können Sie mit der Exportfunktion eine Aufgabe zur Audioerstellung erstellen. Sie können den Status der Exportaufgabe beobachten und die Ausgabe für die Verwendung in Ihren Apps und Produkten herunterladen.

## <a name="create-an-audio-tuning-file"></a>Erstellen einer Audiooptimierungsdatei

Es gibt zwei Möglichkeiten, Ihre Inhalte in das Audio Content Creation-Tool zu übernehmen.

**Option 1:**

1. Klicken Sie auf **Neue** > **Datei**, um eine neue Audiooptimierungsdatei zu erstellen.
2. Geben oder fügen Sie Ihren Inhalt in das Bearbeitungsfenster ein. Eine Datei kann jeweils bis zu 20.000 Zeichen enthalten. Ist Ihr Skript länger als 20.000 Zeichen, können Sie Option 2 verwenden, um den Inhalt automatisch in mehrere Dateien zu unterteilen.
3. Vergessen Sie nicht zu speichern.

**Option 2:**

1. Klicken Sie auf **Hochladen**, um Textdateien zu importieren. Sowohl Nur-Text als auch SSML werden unterstützt. Wenn die Skriptdatei mehr als 20.000 Zeichen umfasst, teilen Sie die Datei nach Absätzen, Zeichen oder regulären Ausdrücken.
3. Stellen Sie beim Hochladen Ihrer Textdateien sicher, dass die Datei die folgenden Anforderungen erfüllt:

   | Eigenschaft | Wert/Hinweise |
   |----------|---------------|
   | Dateiformat | Nur-Text (.txt)<br/> SSML-Text (.txt)<br/> ZIP-Dateien werden nicht unterstützt. |
   | Codierungsformat | UTF-8 |
   | Dateiname | Jede Datei muss einen eindeutigen Namen haben. Duplikate werden nicht unterstützt. |
   | Textlänge | Die Zeichenbeschränkung der Textdatei liegt bei 20.000. Wenn Ihre Dateien die Beschränkung überschreiten, teilen Sie die Dateien nach den Anweisungen im Tool. |
   | SSML-Einschränkungen | Jede SSML-Datei darf nur ein einziges SSML-Element enthalten. |

**Beispiel für Nur-Text**

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

**Beispiel für SSML-Text**

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportieren der optimierten Audiodatei

Nachdem Sie Ihre Audioausgabe überprüft haben und mit der Optimierung und den Anpassungen zufrieden sind, können Sie die Audiodatei exportieren.

1. Klicken Sie auf **Exportieren**, um eine Aufgabe zur Audioerstellung zu erstellen. Der **Export in eine Audiobibliothek** wird empfohlen, da dadurch die lange Audioausgabe und die gesamten Audioausgabefunktionen unterstützt werden. Sie können die Audiodaten auch direkt auf den lokalen Datenträger herunterladen, allerdings sind nur die ersten zehn Minuten verfügbar.
2. Wählen Sie das Ausgabeformat für die Audiooptimierung aus. Eine Liste der unterstützten Formate und Abtastraten finden Sie unten.
3. Sie können den Status der Aufgabe auf der Registerkarte der **Exportaufgabe** anzeigen. Wenn die Aufgabe zu einem Fehler führt, finden Sie auf der Seite mit den ausführlichen Informationen einen vollständigen Bericht.
4. Wenn die Aufgabe abgeschlossen wurde, steht Ihre Audiodatei auf der Registerkarte **Audio Library** (Audiobibliothek) zum Download zur Verfügung.
5. Klicken Sie auf **Download**. Sie können nun Ihre benutzerdefinierten und optimierten Audiodaten in Ihren Apps oder Produkten verwenden.

**Unterstützte Audioformate**

| Format | 16-kHz-Abtastrate | 24-kHz-Abtastrate |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| MP3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="how-to-addremove-audio-content-creation-users"></a>Wie werden der Audioinhaltserstellung Benutzer hinzugefügt bzw. aus ihr entfernt?

Wenn mehrere Benutzer die Audioinhaltserstellung verwenden möchten, können Sie Benutzerzugriff auf das Azure-Abonnement und die Sprachressource erteilen. Wenn Sie einem Azure-Abonnement einen Benutzer hinzufügen, kann der Benutzer auf alle Ressourcen unter dem Azure-Abonnement zugreifen. Wenn Sie einen Benutzer jedoch nur zu einer Sprachressource hinzufügen, hat der Benutzer nur Zugriff auf die Sprachressource und kann nicht auf weitere Ressourcen unter diesem Azure-Abonnement zugreifen. Ein Benutzer mit Zugriff auf die Sprachressource kann die Audioinhaltserstellung verwenden.

Der Benutzer muss ein [Microsoft-Konto](https://account.microsoft.com/account) vorbereiten. Wenn der Benutzer kein Microsoft-Konto besitzt, können Sie eines in wenigen Minuten erstellen. Der Benutzer kann die vorhandene E-Mail-Adresse und den Link als Microsoft-Konto verwenden oder eine neue E-Mail-Adresse von Outlook als Microsoft-Konto erstellen.


### <a name="add-users-to-a-speech-resource"></a>Hinzufügen von Benutzern zu einer Sprachressource

Führen Sie diese Schritte aus, um einen Benutzer zu einer Sprachressource hinzuzufügen, damit er/sie die Audioinhaltserstellung verwenden kann.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com/) nach **Cognitive Services**, und wählen Sie die Sprachressource aus, der Sie Benutzer hinzufügen möchten.
2. Klicken Sie auf **Zugriffssteuerung (IAM)** . Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen. 
1. Wählen Sie auf der Registerkarte **Rolle** die Rolle **Cognitive Service-Benutzer** aus. Wenn Sie den Benutzer zum Besitzer dieser Sprachressource machen möchten, können Sie die Rolle **Besitzer** auswählen.
1. Geben Sie auf der Registerkarte **Mitglieder** die E-Mail-Adresse des Benutzers ein, und wählen Sie den Benutzer im Verzeichnis aus. Bei der E-Mail-Adresse muss es sich um ein **Microsoft-Konto** handeln, dem Azure Active Directory vertraut. Benutzer können sich ganz einfach mit einer persönlichen E-Mail-Adresse für ein [Microsoft-Konto](https://account.microsoft.com/account) registrieren. 
1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.
1. Der Benutzer erhält eine E-Mail-Einladung. Akzeptieren Sie die Einladung, indem Sie in der E-Mail auf **Einladung annehmen** > **Beitritt zu Azure akzeptieren** klicken. Anschließend wird der Benutzer zum Azure-Portal weitergeleitet. Der Benutzer muss keine weiteren Aktionen im Azure-Portal durchführen. Nach wenigen Augenblicken wird dem Benutzer die Rolle im Bereich der Speech-Ressource zugewiesen, und er erhält Zugriff auf diese Ressource. Wenn der Benutzer keine E-Mail-Einladung erhält, können Sie unter „Rollenzuweisungen“ nach dem Benutzerkonto suchen und das Benutzerprofil öffnen. Suchen Sie nach „Identität“ -> „Einladung angenommen“, und klicken Sie auf **Verwalten**, um die E-Mail-Einladung erneut zu senden. Sie können den Einladungslink auch für Benutzer kopieren. 
1. Der Benutzer besucht oder aktualisiert die Produktseite [Audioinhaltserstellung](https://aka.ms/audiocontentcreation), und meldet sich mit dem Microsoft-Konto an. Wählen Sie den Block **Audioinhaltserstellung** aus den Speech-Produkten aus. Wählen Sie die Speech-Ressource im Popupfenster oder in den Einstellungen rechts oben auf der Seite aus. Wenn keine Speech-Ressource zu finden ist, überprüfen Sie, ob Sie sich im richtigen Verzeichnis befinden. Klicken Sie dazu auf das Kontoprofil in der oberen rechten Ecke, und klicken Sie neben dem aktuellen Verzeichnis auf **Wechseln**. Wenn mehr als ein Verzeichnis verfügbar ist, bedeutet dies, dass Sie Zugriff auf mehrere Verzeichnisse haben. Wechseln Sie zu einem anderen Verzeichnis, und öffnen Sie die Einstellungen, um zu ermitteln, ob die richtige Speech-Ressource verfügbar ist. 

    :::image type="content" source="media/audio-content-creation/add-role-first.png" alt-text="Dialogfeld „Rolle hinzufügen“":::


Benutzer, die sich in der gleichen Sprachressource befinden, sehen ihre Arbeit gegenseitig im Audioinhaltserstellungs-Studio. Wenn Sie möchten, dass jeder einzelne Benutzer über einen einzelnen und privaten Arbeitsbereich in der Audioinhaltserstellung verfügt, erstellen Sie [eine neue Sprachressource](#step-2---create-a-speech-resource) für jeden Benutzer, und geben Sie jedem Benutzer den eindeutigen Zugriff auf die Sprachressource.

### <a name="remove-users-from-a-speech-resource"></a>Entfernen von Benutzern aus einer Sprachressource

1. Suchen Sie im Azure-Portal nach **Cognitive Services**, und wählen Sie die Sprachressource aus, aus der Sie Benutzer entfernen möchten.
2. Klicken Sie auf **Zugriffssteuerung (IAM)** . Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um alle Rollenzuweisungen für die betreffende Sprachressource anzuzeigen.
3. Wählen Sie die Benutzer aus, die Sie entfernen möchten, und klicken Sie auf **Entfernen** > **Ok**.
    :::image type="content" source="media/audio-content-creation/remove-user.png" alt-text="Schaltfläche „Entfernen“":::

### <a name="enable-users-to-grant-access"></a>Benutzern das Erteilen von Zugriff ermöglichen

Wenn einer der Benutzer imstande sein soll, anderen Benutzern Zugriff zu erteilen, müssen Sie dem Benutzer die Besitzerrolle für die Sprachressource übertragen und ihn als leseberechtigt für das Azure-Verzeichnis festlegen.
1. Fügen Sie den Benutzer als Besitzer der Sprachressource hinzu. Weitere Informationen dazu finden Sie unter [Hinzufügen von Benutzern zu einer Sprachressource](#add-users-to-a-speech-resource).
    :::image type="content" source="media/audio-content-creation/add-role.png" alt-text="Feld „Rollenbesitzer“":::
2. Wählen Sie im [Azure-Portal](https://portal.azure.com/) oben links das reduzierte Menü aus. Klicken Sie auf **Azure Active Directory** und dann auf **Benutzer**.
3. Durchsuchen Sie das Microsoft-Konto des Benutzers, und navigieren Sie zur Detailseite des Benutzers. Klicken Sie auf **Zugewiesene Rollen**.
4. Klicken Sie auf **Zuweisungen hinzufügen** -> **Verzeichnisleseberechtigte**. Wenn die Schaltfläche „Zuweisungen hinzufügen“ abgeblendet ist, bedeutet das, dass Sie keinen Zugriff haben. Nur der globale Administrator dieses Verzeichnisses kann Benutzern Zuweisungen hinzufügen.

## <a name="see-also"></a>Weitere Informationen

* [API für lange Audioinhalte](./long-audio-api.md)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
