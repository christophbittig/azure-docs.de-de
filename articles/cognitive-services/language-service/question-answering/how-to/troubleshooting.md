---
title: Fehlersuche - Beantwortung von Fragen
description: Die kuratierte Liste der am häufigsten gestellten Fragen zur Beantwortung von Fragen wird Ihnen helfen, die Funktion schneller und mit besseren Ergebnissen zu übernehmen.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: dfafd5185c62d3011dfef66eb9d244ebe8a9b549
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478612"
---
# <a name="troubleshooting-for-question-answering"></a>Fehlersuche bei der Beantwortung von Fragen

Die kuratierte Liste der am häufigsten gestellten Fragen zur Beantwortung von Fragen wird Ihnen helfen, die Funktion schneller und mit besseren Ergebnissen zu übernehmen.

## <a name="manage-predictions"></a>Verwalten von Vorhersagen

<details>
<summary><b>Wie kann ich die Durchsatzleistung für Abfragevorhersagen verbessern?</b></summary>

**Antwort:** Probleme bei der Durchsatzleistung weisen darauf hin, dass Sie Ihre Cognitive Search-Instanz hochskalieren müssen. Fügen Sie Ihrer Cognitive Search-Instanz ggf. ein Replikat hinzu, um die Leistung zu verbessern.

[Hier](../Concepts/azure-resources.md) finden Sie weitere Informationen zu Tarifen.
</details>

## <a name="manage-your-project"></a>Verwalten Ihres Projekts

<details>
<summary><b>Warum extrahieren meine URLs/Dateien keine Frage-Antwort-Paare?</b></summary>

**Antwort**: Es ist möglich, dass die Fragebeantwortung einige Frage-und-Antwort-Inhalte (QnA) aus gültigen FAQ-URLs nicht automatisch extrahieren kann. In solchen Fällen können Sie den QnA-Inhalt in eine TXT-Datei einfügen und prüfen, ob das Tool diesen Inhalt erfassen kann. Alternativ können Sie über das Portal [Language Studio](https://language.azure.com) redaktionell Inhalte zu Ihrem Projekt/Wissensbasis hinzufügen.

</details>

<details>
<summary><b>Wie groß darf eine von mir erstellte Wissensdatenbank sein?</b></summary>

**Antwort:** Die Größe der Wissensdatenbank hängt von der beim Erstellen des QnA Maker-Diensts ausgewählten SKU für Azure Search ab. Ausführlichere Informationen finden Sie [hier](../concepts/azure-resources.md).

</details>

<details>
<summary><b>Wie gebe ich eine Wissensdatenbank für andere Personen frei?</b></summary>

**Antwort**: Die gemeinsame Nutzung funktioniert auf der Ebene der Sprachressource, d.h. alle mit einer Sprachressource verbundenen Wissensbasen können gemeinsam genutzt werden.
</details>

<details>
<summary><b>Kann ich eine Wissensdatenbank zur Bearbeitung für einen Mitwirkenden freigeben, der sich nicht im selben Azure Active Directory-Mandanten befindet?</b></summary>

**Antwort**: Die Freigabe basiert auf der rollenbasierten Zugriffskontrolle von Azure (Azure Role-base access control). Wenn Sie _jede_ Ressource in Azure mit einem anderen Benutzer teilen können, können Sie auch die Beantwortung von Fragen teilen.

</details>

<details>
<summary><b>Kann man 5 verschiedenen Benutzern Lese-/Schreibrechte zuweisen, so dass jeder von ihnen nur auf eine Wissensdatenbank zur Beantwortung von Fragen zugreifen kann?</b></summary>

**Antwort**: Sie können eine ganze Sprachressource freigeben, nicht einzelne Wissensdatenbanken.

</details>

<details>
<summary><b>Die Aktualisierungen, die ich in meiner Wissensdatenbank vorgenommen habe, werden in der Produktion nicht berücksichtigt. Warum nicht?</b></summary>

**Antwort**: Jeder Bearbeitungsvorgang, ob bei einer Tabellenaktualisierung, einem Test oder einer Einstellung, muss gespeichert werden, bevor er eingesetzt werden kann. Stellen Sie sicher, dass Sie **Speichern** wählen, nachdem Sie Änderungen vorgenommen haben, und stellen Sie Ihr Projekt dann erneut bereit, damit diese Änderungen in der Produktion berücksichtigt werden.

</details>

<details>
<summary><b>Unterstützt die Wissensdatenbank komplexe Daten oder Multimedia?</b></summary>

**Antwort:**

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>Automatische Multimediaextraktion für Dateien und URLs

* URLs: eingeschränkte Konvertierungsfunktionen von HTML zu Markdown
* Dateien: nicht unterstützt

#### <a name="answer-text-in-markdown"></a>Antworttext in Markdown

Wenn die QnA-Paare in der Wissensdatenbank hinzugefügt wurden, können Sie den Markdowntext einer Antwort bearbeiten, um Links zu Medien einzufügen, die über öffentliche URLs verfügbar sind.

</details>

<details>
<summary><b>Unterstützt die Fragebeantwortung auch nicht-englische Sprachen?</b></summary>

**Antwort:** Weitere Informationen hierzu finden Sie unter [Unterstützte Sprachen](../language-support.md).

Wenn Sie Inhalte in mehreren Sprachen haben, sollten Sie für jede Sprache ein eigenes Projekt erstellen.

</details>

## <a name="manage-service"></a>Verwalten eines Diensts

<details>
<summary><b>Ich habe meinen Suchdienst gelöscht. Wie kann ich dieses Problem beheben?</b></summary>

**Antwort:** Wenn Sie einen Azure Cognitive Search-Index löschen, ist der Vorgang endgültig, weshalb der Index nicht wiederhergestellt werden kann.

</details>

<details>
<summary><b>Ich habe den Index `testkbv2` in meinem Suchdienst gelöscht. Wie kann ich dieses Problem beheben?</b></summary>

**Antwort**: Falls Sie den `testkbv2`-Index in Ihrem Suchdienst gelöscht haben, können Sie die Daten aus der zuletzt veröffentlichten Wissensdatenbank wiederherstellen. Verwenden Sie das Wiederherstellungstool [RestoreTestKBIndex](https://github.com/pchoudhari/QnAMakerBackupRestore/tree/master/RestoreTestKBFromProd), das auf GitHub verfügbar ist.

</details>

<details>
<summary><b>Kann ich die gleiche Azure Cognitive Search-Ressource für Wissensdatenbanken in mehreren Sprachen verwenden?</b></summary>

**Antwort**: Um mehrere Sprachen und mehrere Wissensdatenbanken zu verwenden, muss der Benutzer für jede Sprache ein Projekt erstellen und das erste Projekt, das für die Sprachressource erstellt wird, muss die Option **Ich möchte die Sprache auswählen, wenn ich ein Projekt in dieser Ressource erstelle**. Dadurch wird ein separater Azure Search-Dienst pro Sprache erstellt.

</details>

## <a name="integrate-with-other-services-including-bots"></a>Integration in andere Dienste, einschließlich Bots

<details>
<summary><b>Brauche ich das Bot Framework, um Fragen beantworten zu können?</b></summary>

**Antwort**: Nein, Sie müssen das [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) bei der Beantwortung von Fragen nicht verwenden. Die Beantwortung von Fragen wird jedoch als eine von mehreren Vorlagen in [Azure Bot Service](/azure/bot-service/) angeboten. Bot Service ermöglicht die schnelle, intelligente Botentwicklung über Microsoft Bot Framework und wird in einer serverlosen Umgebung ausgeführt.

</details>

<details>
<summary><b>Wie kann ich einen neuen Bot mit Fragenbeantwortung erstellen?</b></summary>

**Antwort:** Folgen Sie den Anweisungen in [dieser](../tutorials/bot-service.md) Dokumentation, um Ihren Bot mit Azure Bot Service zu erstellen.

</details>

<details>
<summary><b>Wie kann ich eine andere Wissensdatenbank mit einem bestehenden Azure Bot Service verwenden?</b></summary>

**Antwort:** Sie benötigen die folgenden Informationen über Ihre Wissensdatenbank:

* Wissensdatenbank-ID
* Sie finden den benutzerdefinierten Unterdomänennamen des veröffentlichten Endpunkts der Wissensdatenbank, der auch als `host` bezeichnet wird, nach der Veröffentlichung auf der Seite **Einstellungen**.
* Der Schlüssel des veröffentlichten Endpunkts der Wissensdatenbank – befindet sich nach der Veröffentlichung auf der Seite **Einstellungen**.

Wechseln Sie mit diesen Informationen zum App Service Ihres Bot im Azure-Portal. Ändern Sie unter **Einstellungen -> Konfiguration -> Anwendungseinstellungen** diese Werte.

Der Endpunktschlüssel der Wissensdatenbank ist im ABS-Dienst mit `QnAAuthkey` gekennzeichnet.

</details>

<details>
<summary><b>Können zwei oder mehr Clientanwendungen eine Wissensdatenbank gemeinsam nutzen?</b></summary>

**Antwort:** Ja, die Wissensdatenbank kann von beliebig vielen Clients abgefragt werden.

</details>

<details>
<summary><b>Wie kann ich die Beantwortung von Fragen in meine Website einbinden?</b></summary>

**Antwort**: Führen Sie diese Schritte aus, um den Fragebeantwortungsdienst als Web-Chat-Steuerung in Ihre Website einzubinden:

1. Erstellen Sie Ihren FAQ-Bot, indem Sie den [hier](../tutorials/bot-service.md) angegebenen Anweisungen folgen.
2. Aktivieren Sie den Webchat, indem Sie die [hier](../tutorials/bot-service.md#integrate-the-bot-with-channels) aufgeführten Schritte ausführen.

## <a name="data-storage"></a>Datenspeicher

<details>
<summary><b>Welche Daten werden gespeichert, und wo werden sie gespeichert?</b></summary>

**Antwort:**

Bei der Erstellung Ihrer Sprachressource für die Beantwortung von Fragen haben Sie eine Azure-Region ausgewählt. Ihre Wissensdatenbanken und Protokolldateien werden in dieser Region gespeichert.

</details>
