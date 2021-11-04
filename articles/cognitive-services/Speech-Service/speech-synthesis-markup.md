---
title: Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML) – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Verwenden der Markupsprache für Sprachsynthese zum Steuern der Aussprache und des Satzrhythmus in Text-zu-Sprache
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: eur
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: dbb9f42f7f0b58b14ae9a5c8824475c0fc15fd05
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505079"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Verbessern der Synthese mit Markupsprache für Sprachsynthese (Speech Synthesis Markup Language, SSML)

Speech Synthesis Markup Language (SSML) ist eine XML-basierte Markupsprache, die Entwicklern ermöglicht, anzugeben, wie der Eingabetext mithilfe des Sprachsynthesediensts in synthetisierte Sprache konvertiert werden soll. Verglichen mit Nur-Text ermöglicht SSML Entwicklern, die Tonhöhe, Aussprache, Sprechgeschwindigkeit, Lautstärke und mehr für die Ausgabe der Sprachsynthese zu optimieren. Die normale Interpunktion, z.B. das Pausieren nach einem Punkt, oder die Verwendung der korrekten Intonation, wenn ein Satz mit einem Fragezeichen endet, werden automatisch verarbeitet.

Die Speech-Dienstimplementierung von SSML basiert auf der [Markupsprache für Sprachsynthese, Version 1.0](https://www.w3.org/TR/2004/REC-speech-synthesis-20040907/) des World Wide Web Consortiums.

> [!IMPORTANT]
> Chinesische, japanische und koreanische Zeichen zählen bei der Abrechnung jeweils als zwei Zeichen. Weitere Informationen finden Sie unter [Preise](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="neural-and-custom-voices"></a>Neuronale und benutzerdefinierte Stimmen

Verwenden Sie eine menschenähnliche neuronale Stimme, oder erstellen Sie Ihre eigene benutzerdefinierte Stimme speziell für Ihr Produkt oder Ihre Marke. Eine vollständige Liste der unterstützten Sprachen, Gebietsschemas und Stimmen finden Sie unter [Sprach- und Stimmunterstützung für den Speech-Dienst](language-support.md). Weitere Informationen zu neuronalen und benutzerdefinierten Stimmen finden Sie unter [Was ist Text-zu-Sprache?](text-to-speech.md).


> [!NOTE]
> Auf der Seite für [Sprachsynthese](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features) können Sie Stimmen Beispieltext in verschiedenen Stilen und Tonhöhen vorlesen hören.


## <a name="special-characters"></a>Sonderzeichen

Beachten Sie bei Verwendung von SSML, dass Sonderzeichen wie Anführungszeichen, Apostrophe und Klammern mit Escapezeichen versehen werden müssen. Weitere Informationen finden Sie unter [Extensible Markup Language (XML) 1.0: Anhang D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Unterstützte SSML-Elemente

Jedes SSML-Dokument wird mit SSML-Elementen (oder Tags) erstellt. Diese Elemente werden zum Anpassen von Tonhöhe, Satzrhythmus, Lautstärke und mehr verwendet. In den folgenden Abschnitten wird erläutert, wie die einzelnen Elemente verwendet werden und wann ein Element erforderlich oder optional ist.

> [!IMPORTANT]
> Vergessen Sie nicht die Eingabe von doppelten Anführungszeichen um Attributwerte. Die Standards für wohlgeformtes, gültiges XML erfordern es, dass Attributwerte in doppelten Anführungszeichen stehen. So ist `<prosody volume="90">` ein wohlgeformtes, gültiges Element, `<prosody volume=90>` aber nicht. SSML erkennt möglicherweise keine Attributwerte, die nicht in Anführungszeichen stehen.

## <a name="create-an-ssml-document"></a>Erstellen eines SSML-Dokuments

`speak` ist das Stammelement und bei allen SSML-Dokumenten **erforderlich**. Das `speak`-Element enthält wichtige Informationen, z.B. die Version, Sprache und die Definition des Markupvokabulars.

**Syntax**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `version` | Gibt die Version der SSML-Spezifikation an, die zum Interpretieren des Dokumentmarkups verwendet wird. Die aktuelle Version ist 1.0. | Erforderlich |
| `xml:lang` | Gibt die Sprache des Stammdokuments an. Der Wert kann einen Kleinbuchstaben, einen Sprachcode aus zwei Buchstaben (z. B. `en`) oder den Sprachcode und Land/Region aus Großbuchstaben (z. B. `en-US`) enthalten. | Erforderlich |
| `xmlns` | Gibt den URI zu dem Dokument an, in dem das Markupvokabular (die Elementtypen und Attributnamen) des SSML-Dokuments definiert werden. Der aktuelle URI ist http://www.w3.org/2001/10/synthesis. | Erforderlich |

## <a name="choose-a-voice-for-text-to-speech"></a>Auswählen einer Stimme für Sprachsynthese

Das `voice`-Element ist erforderlich. Hiermit wird die Stimme angeben, für die Sprachsynthese verwendet wird.

**Syntax**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `name` | Identifiziert die Stimme, die für die Ausgabe der Sprachsynthese verwendet wird. Eine vollständige Liste der unterstützten Stimmen finden Sie unter [Sprachunterstützung](language-support.md#text-to-speech). | Erforderlich |

**Beispiel**

> [!NOTE]
> In diesem Beispiel wird die Stimme `en-US-ChristopherNeural` verwendet. Eine vollständige Liste der unterstützten Stimmen finden Sie unter [Sprachunterstützung](language-support.md#text-to-speech).

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Verwenden mehrerer Stimmen

Innerhalb des `speak`-Elements können Sie mehrere Stimmen für die Ausgabe der Sprachsynthese angeben. Diese Stimmen können in verschiedenen Sprachen sein. Der Text muss bei jeder Stimme von einem `voice`-Element umschlossen werden.

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `name` | Identifiziert die Stimme, die für die Ausgabe der Sprachsynthese verwendet wird. Eine vollständige Liste der unterstützten Stimmen finden Sie unter [Sprachunterstützung](language-support.md#text-to-speech). | Erforderlich |

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-ChristopherNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Anpassen von Sprechweisen

Standardmäßig synthetisiert der Sprachsynthesedienst Text mit einer neutralen Sprechweise für neuronale Stimmen. Mithilfe des Elements `mstts:express-as` können Sie die Sprechweise anpassen, um verschiedene Emotionen wie Fröhlichkeit, Mitgefühl oder Gelassenheit auszudrücken, oder die Stimme für verschiedene Szenarien wie Kundenservice, Nachrichtenpräsentation oder Sprach-Assistent optimieren. Dies ist ein optionales Element und für den Speech-Dienst eindeutig.

Anpassungen der Sprechweise werden derzeit für folgende neuronale Stimmen unterstützt:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `en-US-SaraNeural`
* `ja-JP-NanamiNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural`
* `zh-CN-XiaohanNeural`
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`
* `zh-CN-XiaoruiNeural`
* `zh-CN-XiaoshuangNeural`

Die Intensität der Sprechweise kann weiter verändert werden, damit sie besser zu Ihrem Anwendungsfall passt. Sie können mit `styledegree` eine kräftigere oder sanftere Sprechweise angeben, um die Sprache ausdrucksstärker oder gedämpfter zu gestalten. Zurzeit werden Anpassungen in der Sprechweise der  neuronalen Stimmen für Chinesisch (Mandarin, vereinfacht) unterstützt.

Abgesehen von der Anpassung der Sprechweisen und ihrer Abstufungen können Sie auch den `role`-Parameter anpassen, damit die Stimme ein anderes Alter und Geschlecht imitiert. Beispielsweise kann eine männliche Stimme die Tonhöhe erhöhen und die Intonation so ändern, dass eine weibliche Stimme imitiert wird, aber der Stimmname wird nicht geändert. Aktuell werden Rollenanpassungen bei folgenden neuronalen Stimmen für Chinesisch (Mandarin, vereinfacht) unterstützt:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Die voranstehenden Änderungen werden auf Satzebene angewendet, und die Sprechweisen und Rollen variieren je nach Stimme. Wenn eine Sprechweise oder Rolle nicht unterstützt wird, gibt der Dienst Sprache in der neutralen Standardsprechweise zurück. Die unterstützten Sprechweisen und Rollen für die jeweilige Stimme können mithilfe der [Stimmlisten-API](rest-text-to-speech.md#get-a-list-of-voices) oder über die codefreie Plattform [Audioinhaltserstellung](https://aka.ms/audiocontentcreation) angezeigt werden.

**Syntax**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> Zurzeit unterstützt `styledegree` nur chinesische (Mandarin, vereinfacht) neuronale Stimmen. `role` unterstützt nur zh-CN-XiaomoNeural und zh-CN-XiaoxuanNeural.

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `style` | Gibt die Sprechweise an. Sprechweisen sind derzeit stimmenspezifisch. | Erforderlich, wenn die Sprechweise für eine neuronale Stimme angepasst wird. Bei Verwendung von `mstts:express-as` muss die Sprechweise angegeben werden. Bei Angabe eines ungültigen Werts wird dieses Element ignoriert. |
| `styledegree` | Gibt die Intensität der Sprechweise an. **Zulässige Werte**: 0,01 bis 2 (einschließlich). Der Standardwert ist 1, d. h. die vordefinierte Intensität für die Sprechweise. Die minimale Einheit ist 0,01, was zu einer leichten Tendenz zur Zielsprechweise führt. Ein Wert von 2 führt zu einer Verdoppelung der standardmäßigen Intensität der Sprechweise.  | Optional (Zurzeit unterstützt `styledegree` nur chinesische (Mandarin, vereinfacht) neuronale Stimmen.)|
| `role` | Gibt die Sprechrolle an. Die Stimme imitiert ein anderes Alter und Geschlecht, aber der Sprachname wird nicht geändert.  | Optional (zurzeit unterstützt `role` nur zh-CN-XiaomoNeural und zh-CN-XiaoxuanNeural)|

Ermitteln Sie anhand dieser Tabelle, welche Sprechweisen für die einzelnen neuronalen Stimmen unterstützt werden.

| Sprache                   | Style                     | BESCHREIBUNG                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Formaler, souveräner und verbindlicher Ton für die Mitteilung von Nachrichten |
|                         | `style="newscast-casual"` | Gewandter und ungezwungener Ton für die Mitteilung allgemeiner Nachrichten        |
|                         | `style="narration-professional"` | Professioneller und objektiver Ton für das Lesen von Inhalten        |
|                         | `style="customerservice"` | Freundlicher und hilfsbereiter Ton für den Kundensupport  |
|                         | `style="chat"`            | Lockerer und zwangloser Ton                         |
|                         | `style="cheerful"`        | Positiver und fröhlicher Ton                         |
|                         | `style="empathetic"`      | Drückt ein Gefühl von Anteilnahme und Verständnis aus               |
| `en-US-JennyNeural`     | `style="customerservice"` | Freundlicher und hilfsbereiter Ton für den Kundensupport  |
|                         | `style="chat"`            | Lockerer und zwangloser Ton                         |
|                         | `style="assistant"`       | Herzlicher und zwangloser Ton für digitale Assistenten    |
|                         | `style="newscast"`        | Gewandter und ungezwungener Ton für die Mitteilung allgemeiner Nachrichten   |
| `en-US-GuyNeural`       | `style="newscast"`        | Formeller und professioneller Ton für Nachrichten |
| `en-US-SaraNeural`      | `style="cheerful"`        | Positiver und fröhlicher Ton    |
|                         | `style="sad"`             | Trauriger Ton   |
|                         | `style="angry"`           | Wütender und verärgerter Ton   |
| `ja-JP-NanamiNeural`    | `style="cheerful"`        | Positiver und fröhlicher Ton   |
|                         | `style="chat"`            | Lockerer und zwangloser Ton   |
|                         | `style="customerservice"` | Freundlicher und hilfsbereiter Ton für den Kundensupport    |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Kühle, gesammelte und gelassene Haltung beim Sprechen Ton, Tonhöhe und Intonation sind im Vergleich zu anderen Sprachtypen viel einheitlicher                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Formeller und professioneller Ton für Nachrichten |
|                         | `style="customerservice"` | Freundlicher und hilfsbereiter Ton für den Kundensupport  |
|                         | `style="assistant"`       | Herzlicher und zwangloser Ton für digitale Assistenten    |
|                         | `style="chat"`            | Lockerer und zwangloser Ton für Smalltalk           |
|                         | `style="calm"`            | Kühle, gesammelte und gelassene Haltung beim Sprechen Ton, Tonhöhe und Intonation sind im Vergleich zu anderen Sprachtypen viel einheitlicher                                |
|                         | `style="cheerful"`        | Optimistischer und enthusiastischer Ton mit höherer Tonhöhe und stimmlicher Energie                         |
|                         | `style="sad"`             | Trauriger Ton mit höherer Tonhöhe, geringerer Intensität und geringerer stimmlicher Energie Häufige Indikatoren für diese Emotion wären Wimmern oder Weinen während der Rede            |
|                         | `style="angry"`           | Wütender und verärgerter Ton mit geringerer Tonhöhe, höherer Intensität und höherer stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er wütend, unzufrieden und beleidigt ist.       |
|                         | `style="fearful"`         | Ängstlicher und nervöser Ton mit höherer Tonhöhe, höherer stimmlicher Energie und höherem Tempo Der Sprecher befindet sich in einem Zustand der Anspannung und Beunruhigung.                          |
|                         | `style="disgruntled"`     | Verächtlicher und klagender Ton Eine Rede mit dieser Emotion zeugt von Unmut und Verachtung.              |
|                         | `style="serious"`         | Strenger und gebieterischer Ton Der Sprecher klingt oft steifer und viel weniger entspannt mit festem Rhythmus.          |
|                         | `style="affectionate"`    | Warmer und herzlicher Ton mit höherer Tonhöhe und stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er die Aufmerksamkeit der Zuhörer auf sich zieht. Die „Persönlichkeit“ des Sprechers ist oft von liebenswerter Art.          |
|                         | `style="gentle"`          | Sanfter, höflicher und angenehmer Ton mit geringerer Tonhöhe und stimmlicher Energie         |
|                         | `style="lyrical"`         | Melodischer und gefühlvoller Ton zum Ausdrücken von Emotionen         |
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Freundlicher und hilfsbereiter Ton für den Kundensupport  |
| `zh-CN-YunyeNeural`     | `style="calm"`            | Kühle, gesammelte und gelassene Haltung beim Sprechen Ton, Tonhöhe und Intonation sind im Vergleich zu anderen Sprachtypen viel einheitlicher    |
|                         | `style="cheerful"`        | Optimistischer und enthusiastischer Ton mit höherer Tonhöhe und stimmlicher Energie                         |
|                         | `style="sad"`             | Trauriger Ton mit höherer Tonhöhe, geringerer Intensität und geringerer stimmlicher Energie Häufige Indikatoren für diese Emotion wären Wimmern oder Weinen während der Rede            |
|                         | `style="angry"`           | Wütender und verärgerter Ton mit geringerer Tonhöhe, höherer Intensität und höherer stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er wütend, unzufrieden und beleidigt ist.       |
|                         | `style="fearful"`         | Ängstlicher und nervöser Ton mit höherer Tonhöhe, höherer stimmlicher Energie und höherem Tempo Der Sprecher befindet sich in einem Zustand der Anspannung und Beunruhigung.                          |
|                         | `style="disgruntled"`     | Verächtlicher und klagender Ton Eine Rede mit dieser Emotion zeugt von Unmut und Verachtung.              |
|                         | `style="serious"`         | Strenger und gebieterischer Ton Der Sprecher klingt oft steifer und viel weniger entspannt mit festem Rhythmus.          |
|   `zh-CN-YunxiNeural`   | `style="assistant"`       | Herzlicher und zwangloser Ton für digitale Assistenten    |
|                         | `style="cheerful"`        | Optimistischer und enthusiastischer Ton mit höherer Tonhöhe und stimmlicher Energie                         |
|                         | `style="sad"`             | Trauriger Ton mit höherer Tonhöhe, geringerer Intensität und geringerer stimmlicher Energie Häufige Indikatoren für diese Emotion wären Wimmern oder Weinen während der Rede            |
|                         | `style="angry"`           | Wütender und verärgerter Ton mit geringerer Tonhöhe, höherer Intensität und höherer stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er wütend, unzufrieden und beleidigt ist.       |
|                         | `style="fearful"`         | Ängstlicher und nervöser Ton mit höherer Tonhöhe, höherer stimmlicher Energie und höherem Tempo Der Sprecher befindet sich in einem Zustand der Anspannung und Beunruhigung.                          |
|                         | `style="disgruntled"`     | Verächtlicher und klagender Ton Eine Rede mit dieser Emotion zeugt von Unmut und Verachtung.              |
|                         | `style="serious"`         | Strenger und gebieterischer Ton Der Sprecher klingt oft steifer und viel weniger entspannt mit festem Rhythmus.    |
|                         | `style="depressed"`       | Melancholischer und niedergeschlagener Ton mit geringerer Tonhöhe und weniger Energie    |
|                         | `style="embarrassed"`     | Unsicherer und zögerlicher Ton, wenn sich der Sprecher unwohl fühlt   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Optimistischer und enthusiastischer Ton mit höherer Tonhöhe und stimmlicher Energie                         |
|                         | `style="sad"`             | Trauriger Ton mit höherer Tonhöhe, geringerer Intensität und geringerer stimmlicher Energie Häufige Indikatoren für diese Emotion wären Wimmern oder Weinen während der Rede            |
|                         | `style="angry"`           | Wütender und verärgerter Ton mit geringerer Tonhöhe, höherer Intensität und höherer stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er wütend, unzufrieden und beleidigt ist.       |
|                         | `style="fearful"`         | Ängstlicher und nervöser Ton mit höherer Tonhöhe, höherer stimmlicher Energie und höherem Tempo Der Sprecher befindet sich in einem Zustand der Anspannung und Beunruhigung.                          |
|                         | `style="disgruntled"`     | Verächtlicher und klagender Ton Eine Rede mit dieser Emotion zeugt von Unmut und Verachtung.              |
|                         | `style="serious"`         | Strenger und gebieterischer Ton Der Sprecher klingt oft steifer und viel weniger entspannt mit festem Rhythmus.    |
|                         | `style="embarrassed"`     | Unsicherer und zögerlicher Ton, wenn sich der Sprecher unwohl fühlt   |
|                         | `style="affectionate"`    | Warmer und herzlicher Ton mit höherer Tonhöhe und stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er die Aufmerksamkeit der Zuhörer auf sich zieht. Die „Persönlichkeit“ des Sprechers ist oft von liebenswerter Art.          |
|                         | `style="gentle"`          | Sanfter, höflicher und angenehmer Ton mit geringerer Tonhöhe und stimmlicher Energie         |
| `zh-CN-XiaomoNeural`    | `style="calm"`            | Kühle, gesammelte und gelassene Haltung beim Sprechen Ton, Tonhöhe und Intonation sind im Vergleich zu anderen Sprachtypen viel einheitlicher                         |
|                         | `style="cheerful"`        | Optimistischer und enthusiastischer Ton mit höherer Tonhöhe und stimmlicher Energie                 |
|                         | `style="angry"`           | Wütender und verärgerter Ton mit geringerer Tonhöhe, höherer Intensität und höherer stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er wütend, unzufrieden und beleidigt ist.       |
|                         | `style="fearful"`         | Ängstlicher und nervöser Ton mit höherer Tonhöhe, höherer stimmlicher Energie und höherem Tempo Der Sprecher befindet sich in einem Zustand der Anspannung und Beunruhigung.                       |
|                         | `style="disgruntled"`     | Verächtlicher und klagender Ton Eine Rede mit dieser Emotion zeugt von Unmut und Verachtung.         |
|                         | `style="serious"`         | Strenger und gebieterischer Ton Der Sprecher klingt oft steifer und viel weniger entspannt mit festem Rhythmus.  |
|                         | `style="depressed"`       | Melancholischer und niedergeschlagener Ton mit geringerer Tonhöhe und weniger Energie    |
|                         | `style="gentle"`          | Sanfter, höflicher und angenehmer Ton mit geringerer Tonhöhe und stimmlicher Energie         |
| `zh-CN-XiaoxuanNeural`  | `style="calm"`            | Kühle, gesammelte und gelassene Haltung beim Sprechen Ton, Tonhöhe und Intonation sind im Vergleich zu anderen Sprachtypen viel einheitlicher                         |
|                         | `style="cheerful"`        | Optimistischer und enthusiastischer Ton mit höherer Tonhöhe und stimmlicher Energie                              |
|                         | `style="angry"`           | Wütender und verärgerter Ton mit geringerer Tonhöhe, höherer Intensität und höherer stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er wütend, unzufrieden und beleidigt ist.       |
|                         | `style="fearful"`         | Ängstlicher und nervöser Ton mit höherer Tonhöhe, höherer stimmlicher Energie und höherem Tempo Der Sprecher befindet sich in einem Zustand der Anspannung und Beunruhigung.                       |
|                         | `style="disgruntled"`     | Verächtlicher und klagender Ton Eine Rede mit dieser Emotion zeugt von Unmut und Verachtung.         |
|                         | `style="serious"`         | Strenger und gebieterischer Ton Der Sprecher klingt oft steifer und viel weniger entspannt mit festem Rhythmus.  |
|                         | `style="depressed"`       | Melancholischer und niedergeschlagener Ton mit geringerer Tonhöhe und weniger Energie    |
|                         | `style="gentle"`          | Sanfter, höflicher und angenehmer Ton mit geringerer Tonhöhe und stimmlicher Energie         |
| `zh-CN-XiaoruiNeural`   | `style="sad"`             | Trauriger Ton mit höherer Tonhöhe, geringerer Intensität und geringerer stimmlicher Energie Häufige Indikatoren für diese Emotion wären Wimmern oder Weinen während der Rede         |
|                         | `style="angry"`           | Wütender und verärgerter Ton mit geringerer Tonhöhe, höherer Intensität und höherer stimmlicher Energie Der Sprecher ist in einem Zustand, in dem er wütend, unzufrieden und beleidigt ist.       |
|                         | `style="fearful"`         | Ängstlicher und nervöser Ton mit höherer Tonhöhe, höherer stimmlicher Energie und höherem Tempo Der Sprecher befindet sich in einem Zustand der Anspannung und Beunruhigung.                       |
| `zh-CN-XiaoshuangNeural`   | `style="chat"` | Lockerer und zwangloser Ton |

Verwenden Sie diese Tabelle, um die unterstützten Rollen und deren Definitionen zu überprüfen.

|Rolle                     | BESCHREIBUNG                |
|-------------------------|----------------------------|
|`role="Girl"`            | Die Stimme imitiert ein Mädchen. |
|`role="Boy"`             | Die Stimme imitiert einen Jungen. |
|`role="YoungAdultFemale"`| Die Stimme imitiert eine junge erwachsene Frau.|
|`role="YoungAdultMale"`  | Die Stimme imitiert einen jungen erwachsenen Mann.|
|`role="OlderAdultFemale"`| Die Stimme imitiert eine ältere erwachsene Frau.|
|`role="OlderAdultMale"`  | Die Stimme imitiert einen älteren erwachsenen Mann.|
|`role="SeniorFemale"`    | Die Stimme imitiert eine reife Frau.|
|`role="SeniorMale"`      | Die Stimme imitiert einen reifen Mann.|


**Beispiel**

Dieser SSML-Codeausschnitt veranschaulicht, wie die Sprechweise mithilfe des `<mstts:express-as>`-Elements in `cheerful` (fröhlich) geändert wird.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Dieser SSML-Codeausschnitt veranschaulicht, wie das Attribut `styledegree` verwendet wird, um die Intensität der Sprechweise für XiaoxiaoNeural zu ändern.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Dieser SSML-Codeausschnitt veranschaulicht, wie das Attribut `role` verwendet wird, um die Rolle für XiaomoNeural zu ändern.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="adjust-speaking-languages"></a>Anpassen der gesprochenen Sprachen

Sie können die gesprochenen Sprachen für neuronale Stimmen anpassen.
Ermöglichen Sie es einer Stimme, mithilfe des `<lang xml:lang>`-Elements verschiedene Sprachen (z. B. Englisch, Spanisch und Chinesisch) flüssig zu sprechen. Dies ist ein optionales Element und für den Speech-Dienst eindeutig. Ohne dieses Element spricht die Stimme ihre primäre Sprache.
Anpassungen der gesprochenen Sprache werden derzeit bei den folgenden neuronalen Stimmen unterstützt: `en-US-JennyMultilingualNeural`. Die obigen Änderungen werden auf der Satz- und Wortebene vorgenommen. Wenn eine Sprache nicht unterstützt wird, gibt der Dienst keinen Audiodatenstrom zurück.

> [!NOTE]
> Derzeit ist das `<lang xml:lang>`-Element nicht mit dem `prosody`- und `break`-Element kompatibel. Sie können in diesem Element weder Pause noch Satzrhytmus wie Tonhöhe, Kontur, Geschwindigkeit, Dauer, Lautstärke anpassen.

**Syntax**

```xml
<lang xml:lang="string"></lang>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `lang` | Gibt die gesprochenen Sprachen an. Derzeit ist das Sprechen verschiedener Sprachen von der Stimme abhängig. | Erforderlich, wenn die gesprochene Sprache für eine neuronale Stimme angepasst wird. Bei Verwendung von `lang xml:lang` muss das Gebietsschema angegeben werden. |

Ermitteln Sie anhand dieser Tabelle, welche gesprochenen Sprachen für die einzelnen neuronalen Stimmen unterstützt werden.

| Sprache                            | Gebietsschemasprache           | Beschreibung                                                 |
|----------------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-JennyMultilingualNeural`  | `lang="en-US"`            | Sprechen für das Gebietsschema en-US, dem primären Gebietsschema dieser Stimme |
|                                  | `lang="en-CA"`            | Sprechen der Gebietsschemasprache en-CA                                  |
|                                  | `lang="en-AU"`            | Sprechen der Gebietsschemasprache en-AU                                  |
|                                  | `lang="en-GB"`            | Sprechen der Gebietsschemasprache en-GB                                  |
|                                  | `lang="de-DE"`            | Sprechen der Gebietsschemasprache de-DE                                  |
|                                  | `lang="fr-FR"`            | Sprechen der Gebietsschemasprache fr-FR                                  |
|                                  | `lang="fr-CA"`            | Sprechen der Gebietsschemasprache fr-CA                                  |
|                                  | `lang="es-ES"`            | Sprechen der Gebietsschemasprache es-ES                                  |
|                                  | `lang="es-MX"`            | Sprechen der Gebietsschemasprache es-MX                                  |
|                                  | `lang="zh-CN"`            | Sprechen der Gebietsschemasprache zh-CN                                  |
|                                  | `lang="ko-KR"`            | Sprechen der Gebietsschemasprache ko-KR                                  |
|                                  | `lang="ja-JP"`            | Sprechen der Gebietsschemasprache ja-JP                                  |
|                                  | `lang="it-IT"`            | Sprechen der Gebietsschemasprache it-IT                                  |
|                                  | `lang="pt-BR"`            | Sprechen der Gebietsschemasprache pt-BR                                  |

**Beispiel**

Dieser SSML-Codeausschnitt zeigt, wie sie `<lang xml:lang>` verwenden, um die gesprochenen Sprachen in `en-US`, `es-MX` und `de-DE` zu ändern.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-JennyMultilingualNeural">
        I am looking forward to the exciting things.
        <lang xml:lang="es-MX">
            Estoy deseando que lleguen las cosas emocionantes.
        </lang>
        <lang xml:lang="de-DE">
            Ich freue mich auf die spannenden Dinge.
        </lang>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Hinzufügen oder Entfernen einer Unterbrechung/Pause

Verwenden Sie das `break`-Element zum Einfügen von Pausen (oder Unterbrechungen) zwischen Wörtern oder um Pausen zu verhindern, die vom Sprachsynthesedienst automatisch hinzugefügt werden.

> [!NOTE]
> Mithilfe dieses Elements können Sie das Standardverhalten von TTS (Text-To-Speech, Text-zu-Sprache, Sprachsynthese) bei einem Wort oder Ausdruck außer Kraft setzen, wenn die synthetisierte Sprache dafür unnatürlich klingt. Legen Sie `strength` auf `none` fest, um eine Unterbrechung des Satzrhythmus zu verhindern. Dieses Element wird automatisch vom Text-zu-Sprache-Dienst eingefügt.

**Syntax**

```xml
<break strength="string" />
<break time="string" />
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `strength` | Gibt die relative Dauer einer Pause mit einem der folgenden Werte an:<ul><li>none</li><li>x-weak</li><li>weak</li><li>medium (Standard)</li><li>strong</li><li>x-strong</li></ul> | Optional |
| `time` | Gibt die absolute Dauer einer Pause in Sekunden oder Millisekunden an. Dieser Werte sollte weniger als 5000 ms betragen. Beispiele für gültige Werte sind `2s` und `500ms` | Optional |

| Strength                      | BESCHREIBUNG |
|-------------------------------|-------------|
| „None“, oder wenn kein Wert angegeben | 0 ms        |
| x-weak                        | 250 ms      |
| weak                          | 500 ms      |
| mittel                        | 750 ms      |
| strong                        | 1\.000 ms     |
| x-strong                      | 1\.250 ms     |

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Hinzufügen von Stille

Verwenden Sie das `mstts:silence`-Element, um Pausen vor oder nach Text oder zwischen den 2 benachbarten Sätzen einzufügen.

> [!NOTE]
>Der Unterschied zwischen `mstts:silence` und `break` besteht darin, dass `break` an einer beliebigen Stelle im Text hinzugefügt werden kann. Stille jedoch funktioniert nur am Anfang oder Ende von Eingabetext oder an der Grenze zwischen 2 benachbarten Sätzen.


**Syntax**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `type` | Gibt die Position zum Hinzufügen von Stille an: <ul><li>`Leading` – am Anfang von Text </li><li>`Tailing` – am Ende von Text </li><li>`Sentenceboundary` – zwischen benachbarten Sätzen </li></ul> | Erforderlich |
| `Value` | Gibt die absolute Dauer einer Pause in Sekunden oder Millisekunden an. Dieser Werte sollte weniger als 5000 ms betragen. Beispiele für gültige Werte sind `2s` und `500ms` | Erforderlich |

**Beispiel** In diesem Beispiel wird `mtts:silence` verwendet, um 200 ms Stille zwischen zwei Sätzen hinzuzufügen.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
<voice name="en-US-ChristopherNeural">
<mstts:silence  type="Sentenceboundary" value="200ms"/>
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way.
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time.
</voice>
</speak>
```

## <a name="specify-paragraphs-and-sentences"></a>Angeben von Absätzen und Sätzen

`p`- und `s`-Elemente werden verwendet, um Abschnitte bzw. Sätze zu bezeichnen. Wenn diese Elemente fehlen, ermittelt der Sprachsynthesedienst automatisch die Struktur des SSML-Dokuments.

Das `p`-Element kann Text und die folgenden Elemente enthalten: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `sub`, `mstts:express-as` und `s`.

Das `s`-Element kann Text und die folgenden Elemente enthalten: `audio`, `break`, `phoneme`, `prosody`, `say-as`, `mstts:express-as` und `sub`.

**Syntax**

```xml
<p></p>
<s></s>
```

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Verwenden von Phonemen zur Verbesserung der Aussprache

Das `ph`-Element wird für die phonetische Aussprache in SSML-Dokumenten verwendet. Das `ph`-Element kann nur Text und keine anderen Elemente enthalten. Geben Sie immer lesbare Sprache als ein Fallback an.

Phonetische Alphabete bestehen aus Phonen (Lauten), die sich aus Buchstaben, Zahlen oder Zeichen (manchmal in Kombination) zusammensetzen. Jedes Phon beschreibt einen eindeutigen Sprachklang. Dies steht im Gegensatz zum lateinischen Alphabet, in dem jeder Buchstabe mehrere gesprochene Klänge darstellen kann. Überlegen Sie die unterschiedliche Aussprache des Buchstabens „C“ in den Städtenamen „Coburg“ und „Celle“ oder die unterschiedliche Aussprache der Buchstabenkombination „ch“ in den Wörtern „ich“ und „ach“.

> [!NOTE]
> Das „Phoneme“-Tag wird zurzeit für diese 5 Stimmen (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural und mt-MT-GarceNeural) nicht unterstützt.

**Syntax**

```xml
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `alphabet` | Gibt das phonetische Alphabet an, das verwendet werden soll, wenn die Aussprache der Zeichenfolge im `ph`-Attribut synthetisiert wird. Die Zeichenfolge, die das Alphabet angibt, muss in Kleinbuchstaben angegeben werden. Nachstehend sind die Alphabete aufgeführt, die Sie angeben können.<ul><li>`ipa` &ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Internationales phonetisches Alphabet </a></li><li>`sapi` &ndash; [Phonetisches Alphabet des Speech-Diensts](speech-ssml-phonetic-sets.md)</li><li>`ups` &ndash;<a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank"> Universal Phone Set</a></li></ul><br>Das Alphabet gilt nur für das `phoneme` im Element. | Optional |
| `ph` | Eine Zeichenfolge mit Phonen, die die Aussprache des Worts im `phoneme`-Element angeben. Wenn die angegebene Zeichenfolge nicht erkannte Phone enthält, weist der Sprachsynthesedienst das gesamte SSML-Dokument zurück und erzeugt keine der im Dokument angegebenen Sprachausgaben. | Erforderlich, wenn Phoneme verwendet werden. |

**Beispiele**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <phoneme alphabet="ipa" ph="təˈmeɪtoʊ"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Verwenden eines benutzerdefinierten Lexikons zum Verbessern der Aussprache

Manchmal kann ein Wort vom Sprachsynthesedienst nicht korrekt ausgesprochen werden. Beispielsweise der Name eines Unternehmens, ein medizinischer Begriff oder ein Emoji. Mit den Tags `phoneme` und `sub` können Entwickler die Aussprache einzelner Entitäten in SSML definieren. Wenn Sie dagegen die Aussprache mehrerer Entitäten definieren möchten, können Sie mithilfe des Tags `lexicon` ein benutzerdefiniertes Lexikon erstellen.

> [!NOTE]
> Für das benutzerdefinierte Lexikon wird derzeit die UTF-8-Codierung unterstützt.

> [!NOTE]
> Das benutzerdefinierte Lexikon wird zurzeit für diese 5 Stimmen (et-EE-AnuNeural, ga-IE-OrlaNeural, lt-LT-OnaNeural, lv-LV-EveritaNeural und mt-MT-GarceNeural) nicht unterstützt.


**Syntax**

```xml
<lexicon uri="string"/>
```

**Attribute**

| attribute | BESCHREIBUNG                               | Erforderlich/optional |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Die Adresse des externen PLS-Dokuments. | Erforderlich.           |

**Verwendung**

Wenn Sie die Aussprache mehrerer Entitäten definieren möchten, können Sie ein benutzerdefiniertes Lexikon erstellen. Dieses wird als XML- oder als PLS-Datei gespeichert. Das folgende Beispiel zeigt eine XML-Datei:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias>By the way</alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
  <lexeme>
    <grapheme>😀</grapheme>
    <alias>test emoji</alias>
  </lexeme>
</lexicon>
```

Das `lexicon`-Element enthält mindestens ein `lexeme`-Element. Jedes `lexeme`-Element enthält mindestens ein `grapheme`-Element und mindestens eines der Elemente `grapheme`, `alias` und `phoneme`. Das `grapheme`-Element enthält Text, der die <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">Orthografie</a> beschreibt. Mithilfe der `alias`-Elemente wird die Aussprache eines Akronyms oder eines abgekürzten Begriffs angegeben. Das `phoneme`-Element stellt Text bereit, der die Aussprache von `lexeme` beschreibt. Wenn die Elemente `alias` und `phoneme` mit demselben `grapheme`-Element bereitgestellt werden, weist `alias` eine höhere Priorität auf.

> [!IMPORTANT]
> Beim Element `lexeme` wird die Groß-/Kleinschreibung im benutzerdefinierten Lexikon beachtet. Wenn Sie beispielsweise nur ein Phonem für das `lexeme`„Hallo“ bereitstellen, funktioniert es nicht für das `lexeme` „hallo“.

Das Lexikon enthält das erforderliche `xml:lang`-Attribut, um anzugeben, auf welches Gebietsschema es angewendet werden soll. Ein benutzerdefiniertes Lexikon ist standardmäßig auf ein einzelnes Gebietsschema beschränkt, sodass die Anwendung auf ein anderes Gebietsschema nicht funktioniert.

Wichtig: Die Aussprache eines Ausdrucks kann mit dem benutzerdefinierten Lexikon nicht direkt festgelegt werden. Wenn Sie die Aussprache für ein Akronym oder einen abgekürzten Begriff festlegen möchten, müssen Sie zuerst einen Alias (`alias`) angeben und anschließend das Phonem (`phoneme`) diesem Alias (`alias`) zuordnen. Beispiel:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>ScotlandMV</alias>
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme>
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

> [!NOTE]
> Im internationalen phonetischen Alphabet (IPA) wird die Silbengrenze mit „.“ dargestellt.

Sie können auch direkt ihren erwarteten `alias` für das Akronym oder einen abgekürzten Begriff angeben. Beispiel:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme>
    <alias>Scotland Media Wave</alias>
  </lexeme>
```

> [!IMPORTANT]
> Das Element `phoneme` darf bei Verwendung des IPA keine Leerzeichen enthalten.

Weitere Informationen zu benutzerdefinierten Lexikondateien finden Sie unter [Spezifikation für Aussprachelexika (Pronunciation Lexicon Specification, PLS), Version 1.0](https://www.w3.org/TR/pronunciation-lexicon/).

Veröffentlichen Sie als Nächstes Ihre benutzerdefinierte Lexikondatei. Diese Datei kann zwar an einem beliebigen Ort gespeichert werden, es empfiehlt sich jedoch, [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-portal.md) zu verwenden.

Nachdem Sie Ihr benutzerdefiniertes Lexikon veröffentlicht haben, können Sie von SSML aus darauf verweisen.

> [!NOTE]
> Das Element `lexicon` muss sich innerhalb des Elements `voice` befinden.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
          xmlns:mstts="http://www.w3.org/2001/mstts"
          xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Wenn Sie dieses benutzerdefinierte Lexikon verwenden, wird „BTW“ als „By the way“ gelesen. „Benigni“ wird gemäß der IPA-Angabe als „bɛˈniːnji“ gelesen.

Da es im benutzerdefinierten Lexikon leicht zu Fehlern kommen kann, stellt Microsoft ein [Überprüfungstool für das benutzerdefinierte Lexikon](https://github.com/jiajzhan/Custom-Lexicon-Validation) bereit. Es enthält detaillierte Fehlermeldungen, um nach Fehlern zu suchen. Bevor Sie SSML mit dem benutzerdefinierten Lexikon an den Speech-Dienst senden, sollten Sie Ihr benutzerdefiniertes Lexikon mit diesem Tool überprüfen. 

**Einschränkungen**
- Dateigröße: Die maximale Größe benutzerdefinierter Lexikondateien beträgt 100 KB. Wenn diese Größe überschritten wird, treten bei Syntheseanforderungen Fehler auf.
- Lexikoncacheaktualisierung: Das benutzerdefinierte Lexikon wird beim ersten Laden mit dem URI als Schlüssel im TTS-Dienst zwischengespeichert. Ein Lexikon mit demselben URI wird innerhalb von 15 Minuten nicht neu geladen, sodass Änderungen am benutzerdefinierten Lexikon nach maximal 15 Minuten in Kraft treten.

**Phonetische Sätze des Speech-Diensts**

Im obigen Beispiel wird das als IPA-Phonemsatz bezeichnete internationale phonetische Alphabet verwendet. Wir empfehlen Entwicklern, das IPA zu verwenden, da es der internationale Standard ist. Einige IPA-Zeichen können in Unicode als zusammengesetzte und als aufgelöste Version dargestellt werden. Im benutzerdefinierten Lexikon wird nur die aufgelöste Unicodedarstellung unterstützt.

Da das IPA nicht leicht zu merken ist, definiert der Speech-Dienst einen phonetischen Satz für sieben Sprachen (`en-US`, `fr-FR`, `de-DE`, `es-ES`, `ja-JP`, `zh-CN` und `zh-TW`).

`x-microsoft-sapi` kann wie unten gezeigt als Wert des Attributs `alphabet` mit benutzerdefinierten Lexika verwendet werden:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0"
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="x-microsoft-sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Ausführliche Informationen zum phonetischen Alphabet des Speech-Diensts finden Sie unter [Phonetische Sätze des Speech-Diensts](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Anpassen des Satzrhythmus

Das `prosody`-Element wird verwendet, um Änderungen an Tonhöhe, Kontur, Bereich, Geschwindigkeit, Dauer und Lautstärke für die Ausgabe der Sprachsynthese anzugeben. Das `prosody`-Element kann Text und die folgenden Elemente enthalten: `audio`, `break`, `p`, `phoneme`, `prosody`, `say-as`, `sub` und `s`.

Weil Attributwerte für den Satzrhythmus über einen breiten Bereich variieren können, interpretiert die Spracherkennung die zugewiesenen Werte als einen Vorschlag dazu, wie die tatsächlichen Satzrhythmuswerte für die ausgewählte Stimme lauten sollten. Der Sprachsynthesedienst beschränkt oder ersetzt nicht unterstützte Werte. Beispiele für nicht unterstützte Werte sind eine Tonhöhe von 1 MHz oder eine Lautstärke von 120.

**Syntax**

```xml
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `pitch` | Gibt die Basistonhöhe für den Text an. Sie können die Tonhöhe ausdrücken als:<ul><li>Ein absoluter Wert, der ausgedrückt wird als eine Zahl, hinter der„Hz“ (Hertz) steht. Beispiel: `<prosody pitch="600Hz">some text</prosody>`.</li><li>Ein relativer Wert, der ausgedrückt wird als eine Zahl, vor der „+“ oder „–“ und hinter der „Hz“ oder „st“steht, das einen Betrag zur Änderung der Tonhöhe angibt. Beispiel: `<prosody pitch="+80Hz">some text</prosody>` oder `<prosody pitch="-2st">some text</prosody>`. Das „st“ gibt an, dass die Änderungseinheit ein Halbton ist, bei dem es sich um die Hälfte eines Tons (ein halber Schritt) auf der diatonischen Standardtonleiter handelt.</li><li>Einen konstanten Wert:<ul><li>x-low</li><li>niedrig</li><li>mittel</li><li>high</li><li>x-high</li><li>default</li></ul></li></ul> | Optional |
| `contour` |Die Kontur unterstützt jetzt sowohl neuronale als auch Standardstimmen. Die Kontur stellt Änderungen der Tonhöhe dar. Diese Änderungen werden als ein Array von Zielen an den angegebenen Zeitpositionen in der Sprachausgabe dargestellt. Jedes Ziel wird durch Gruppen von Parameterpaaren definiert. Beispiel: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Der erste Wert in jeder Gruppe von Parametern gibt den Ort der Tonhöhenänderung als Prozentsatz der Textdauer an. Der zweite Wert gibt den Betrag an, um den die Tonhöhe erhöht oder verringert werden soll. Dazu wird ein relativer Wert oder ein Aufzählungswert für die Tonhöhe verwendet (siehe `pitch`). | Optional |
| `range` | Ein Wert, der den Tonhöhenbereich für den Text darstellt. Sie können `range` mit denselben absoluten Werten, relativen Werten oder Aufzählungswerten ausdrücken, mit denen `pitch` beschrieben wird. | Optional |
| `rate` | Gibt die Sprechgeschwindigkeit für den Text an. Sie können `rate` ausdrücken als:<ul><li>Ein relativer Wert, der ausgedrückt wird als eine Zahl, die als Multiplikator des Standards fungiert. So führt beispielsweise der Wert *1* zu keiner Änderung der Geschwindigkeit. Der Wert *0,5* führt zu einer Halbierung der Geschwindigkeit. Der Wert *3* führt zu einer Verdreifachung der Geschwindigkeit.</li><li>Einen konstanten Wert:<ul><li>x-slow</li><li>langsam</li><li>mittel</li><li>fast</li><li>x-fast</li><li>default</li></ul></li></ul> | Optional |
| `duration` | Die Zeitspanne in Sekunden oder Millisekunden, die vergehen sollte, während der Sprachsynthesedienst den Text liest. Beispiel: *2s* oder *1800ms*. Die Dauer unterstützt nur Standardstimmen.| Optional |
| `volume` | Gibt die Lautstärke der Sprechstimme an. Sie können die Lautstärke ausdrücken als:<ul><li>Ein absoluter Wert, der ausgedrückt wird als eine Zahl im Bereich von 0,0 bis 100,0 – von *am leisesten* bis zu *am lautesten*. Beispiel: „75“. Der Standardwert ist „100,0“.</li><li>Ein relativer Wert, der ausgedrückt wird als eine Zahl, vor der ein „+“ oder „–“ steht und die einen Betrag zum Ändern der Lautstärke angibt. Beispiel: „+10“ oder „-5,5“.</li><li>Einen konstanten Wert:<ul><li>silent</li><li>x-soft</li><li>soft</li><li>mittel</li><li>loud</li><li>x-loud</li><li>default</li></ul></li></ul> | Optional |

### <a name="change-speaking-rate"></a>Ändern der Sprechgeschwindigkeit

Die Sprechgeschwindigkeit kann auf neuronale Stimmen und Standardstimmen auf Wort- oder Satzebene angewendet werden.

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Ändern der Lautstärke

Änderungen der Lautstärke können auf Standardstimmen auf Wort- oder Satzebene angewendet werden. Änderungen der Lautstärke können dagegen nur auf neuronale Stimmen auf Satzebene angewendet werden.

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Ändern der Tonhöhe

Änderungen der Tonhöhe können auf Standardstimmen auf Wort- oder Satzebene angewendet werden. Änderungen der Tonhöhe können dagegen nur auf neuronale Stimmen auf Satzebene angewendet werden.

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Ändern der Tonhöhenkontur

> [!IMPORTANT]
> Änderungen der Tonhöhenkontur werden jetzt für neuronale Stimmen unterstützt.

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room?
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>say-as-Element

`say-as` ist ein optionales Element, das den Inhaltstyp (z. B. Zahl oder Datum) für den Text des Elements angibt. Es informiert die Sprachsynthese-Engine, wie der Text ausgesprochen wird.

**Syntax**

```xml
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `interpret-as` | Gibt an, welchen Inhaltstyp der Text des Elements darstellt. Die folgende Tabelle listet die unterschiedlichen Typen auf. | Erforderlich |
| `format` | Enthält weitere Informationen, wie genau der Elementtext formatiert ist, für Inhaltstypen, die mehrdeutige Formate haben können. SSML definiert Formate für Inhaltstypen, die diese verwenden (siehe Tabelle unten). | Optional |
| `detail` | Gibt die Menge der auszusprechenden Details an. Dieses Attribut könnte beispielsweise bei der Sprachsynthese-Engine die Aussprache von Satzzeichen anfordern. Für `detail` sind keine Standardwerte definiert. | Optional |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Im Folgenden finden Sie die unterstützten Inhaltstypen für die Attribute `interpret-as` und `format`. Fügen Sie das `format`-Attribut nur dann ein, wenn `interpret-as` auf Datum und Uhrzeit festgelegt ist.

| interpret-as | format | Interpretation |
|--------------|--------|----------------|
| `address` | | Der Text wird als Adresse ausgesprochen. Aussprache der Sprachsynthese-Engine:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />Als „Ich bin am 150th Court Nordost Redmond Washington“. |
| `cardinal`, `number` | | Der Text wird als Kardinalzahl ausgesprochen. Aussprache der Sprachsynthese-Engine:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />Als „Es gibt drei Alternativen.“ |
| `characters`, `spell-out` | | Der Text wird als einzelner Buchstabe (buchstabiert) ausgesprochen. Aussprache der Sprachsynthese-Engine:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Als „T E S T.“ |
| `date` | TMJ, MTJ, JMT, JTM, JM, MT, TM, T, M, J | Der Text wird als Datum ausgesprochen. Das `format`-Attribut gibt das Datumsformat an (*T = Tag, M = Monat und J = Jahr*). Aussprache der Sprachsynthese-Engine:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Als „heute ist der neunzehnte Oktober zweitausendsechzehn“. |
| `digits`, `number_digit` | | Der Text wird als Sequenz einzelner Ziffern gesprochen. Aussprache der Sprachsynthese-Engine:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Als „1 2 3 4 5 6 7 8 9“. |
| `fraction` | | Der Text wird als Bruchzahl ausgesprochen. Aussprache der Sprachsynthese-Engine:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />Als „drei achtel Zoll“. |
| `ordinal` | | Der Text wird als Ordinalzahl ausgesprochen. Aussprache der Sprachsynthese-Engine:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Als „Wählen Sie die dritte Option aus.“ |
| `telephone` | | Der Text wird als Telefonnummer ausgesprochen. Das Attribut `format` kann Ziffern enthalten, die eine Landeskennzahl darstellen. Beispiel: „1“ für die USA oder „39“ für Italien. Die Sprachsynthese-Engine kann sich anhand dieser Informationen orientieren, wie eine Telefonnummer auszusprechen ist. Wenn die Telefonnummer ebenfalls die Landeskennzahl enthält, hat diese Vorrang vor der Landeskennzahl in `format`. Aussprache der Sprachsynthese-Engine:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />Als „Meine Nummer lautet Vorwahl acht acht acht fünf fünf fünf eins zwei eins zwei.“ |
| `time` | hms12, hms24 | Der Text wird als Uhrzeit ausgesprochen. Das `format`-Attribut gibt an, ob die Uhrzeit im 12-Stunden-Format (hms12) oder 24-Stunden-Format (hms24) angegeben wird. Verwenden Sie einen Doppelpunkt zum Trennen von Zahlen, die Stunden, Minuten und Sekunden darstellen. Beispielsweise ist Folgendes zulässig: 12:35, 1:14:32, 08:15 und 02:50:45. Aussprache der Sprachsynthese-Engine:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Als „Der Zug fährt um vier Uhr morgens.“ |
| `name` | | Der Text wird als Name einer Person ausgesprochen. Aussprache der Sprachsynthese-Engine:<br /><br />`<say-as interpret-as="name">ED</say-as>`<br /><br />as [æd]. <br />In chinesischen Namen werden einige Zeichen unterschiedlich ausgesprochen, wenn sie in einem Familiennamen vorkommen. Die Sprachsynthese-Engine sagt z. B. 仇 in <br /><br />`<say-as interpret-as="address">仇先生</say-as>`<br /><br /> als [qiú] anstelle von [chóu]. |

**Verwendung**

Das `say-as`-Element kann nur Text enthalten.

**Beispiel**

Die Sprachsynthese-Engine spricht den Beispielsatz wie folgt aus: „Ihre erste Anfrage war für ein Zimmer am neunzehnten Oktober zweitausendzehn mit frühzeitiger Ankunft um zwölf Uhr fünfunddreißig nachmittags“.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Hinzufügen von Audioaufzeichnungen

`audio` ist ein optionales Element, mit dem Sie MP3-Audioaufzeichnungen in ein SSML-Dokument einfügen können. Der Text des Elements „audio“ kann Nur-Text oder SSML-Markup enthalten, das verwendet wird, wenn die Audiodatei nicht verfügbar oder nicht abspielbar ist. Außerdem kann das Element `audio` Text und die folgenden Elemente enthalten: `audio`, `break`, `p`, `s`, `phoneme`, `prosody`, `say-as` und `sub`.

Alle Audiodaten, die im SSML-Dokument enthalten sind, müssen die folgenden Anforderungen erfüllen:

* Die MP3-Datei muss auf einem HTTPS-Endpunkt gehostet werden, der über das Internet zugänglich ist. HTTPS ist erforderlich, und die Domäne, die die MP3-Datei hostet, muss über ein gültiges vertrauenswürdiges TSL/SSL-Zertifikat verfügen.
* Es muss es sich um eine gültige MP3-Datei (MPEG v2) handeln.
* Die Bitrate muss 48 KBit/s betragen.
* Die Abtastrate muss bei 16.000 Hz liegen.
* Die Gesamtzeit für alle Text- und Audiodateien in einer einzelnen Antwort darf nicht über 90 Sekunden liegen.
* Die MP3-Datei darf keine kundenspezifischen oder andere vertrauliche Informationen enthalten.

**Syntax**

```xml
<audio src="string"/></audio>
```

**Attribute**

| attribute | BESCHREIBUNG                                   | Erforderlich/optional                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Gibt den Speicherort bzw. die URL der Audiodatei an. | Erforderlich, wenn Sie das Element „audio“ im SSML-Dokument verwenden. |

**Beispiel**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-ChristopherNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Hinzufügen von Hintergrundaudioaufnahmen

Das Element `mstts:backgroundaudio` ermöglicht es Ihnen, Hintergrundaudioaufnahmen zu Ihren SSML-Dokumenten hinzuzufügen (oder eine Audiodatei mit Text-zu-Sprache zu vermischen). Mithilfe von `mstts:backgroundaudio` können Sie im Hintergrund eine Audiodatei in Dauerschleife abspielen, die am Anfang der Text-zu-Sprache-Aufnahme ein und am Ende wieder ausgeblendet wird.

Wenn die bereitgestellte Hintergrundaudiospur kürzer ist als die Text-zu-Sprache-Aufnahme oder das Ausblenden, wird diese wieder von vorne abgespielt. Wenn sie länger als die Text-zu-Sprache-Aufnahme ist, wird sie angehalten, sobald der Ausblendevorgang abgeschlossen ist.

Pro SSML-Dokument ist nur eine Hintergrundaudiodatei zulässig. Sie können jedoch `audio`-Tags in das Element `voice` integrieren, um dem SSML-Dokument zusätzliche Audioaufnahmen hinzuzufügen.

**Syntax**

```xml
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attribute**

| attribute | BESCHREIBUNG | Erforderlich/optional |
|-----------|-------------|---------------------|
| `src` | Gibt den Speicherort bzw. die URL der Hintergrundaudiodatei an. | Erforderlich, wenn Sie eine Hintergrundaudioaufnahme in Ihrem SSML-Dokument verwenden. |
| `volume` | Gibt die Lautstärke der Hintergrundaudiodatei an. **Akzeptierte Werte**: `0` bis `100` (einschließlich). Standardwert: `1`. | Optional |
| `fadein` | Gibt (in Millisekunden) an, wie lange die Hintergrundaudiodatei eingeblendet wird. Der Standardwert ist `0`, was dem „Nicht einblenden“ entspricht. **Akzeptierte Werte**: `0` bis `10000` (einschließlich).  | Optional |
| `fadeout` | Gibt (in Millisekunden) an, wie lange die Hintergrundaudiodatei ausgeblendet wird. Der Standardwert ist `0`, was dem „Nicht ausblenden“ entspricht. **Akzeptierte Werte**: `0` bis `10000` (einschließlich).  | Optional |

**Beispiel**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, ChristopherNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="bookmark-element"></a>Lesezeichen-Element

Mit dem Lesezeichen-Element können Sie benutzerdefinierte Marker in SSML einfügen, um den Offset der einzelnen Marker im Audiostream zu erhalten.
Die Lesezeichen-Elemente werden nicht gelesen.
Das Lesezeichen-Element kann verwendet werden, um auf eine bestimmte Position in der Text-oder Tag-Sequenz zu verweisen.

> [!NOTE]
> `bookmark` Das Element funktioniert zurzeit nur für `en-US-AriaNeural` Voice.

**Syntax**

```xml
<bookmark mark="string"/>
```

**Attribute**

| attribute | BESCHREIBUNG                                   | Erforderlich/optional                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
|  `mark`   | Gibt den Verweistext des `bookmark` Elements an. | Erforderlich. |

**Beispiel**

Beispielsweise können Sie den Zeitversatz (Offset) jedes Blumenworts wie folgt ermitteln:

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        We are selling <bookmark mark='flower_1'/>roses and <bookmark mark='flower_2'/>daisies.
    </voice>
</speak>
```

### <a name="get-bookmark-using-speech-sdk"></a>Lesezeichen mit Sprach-SDK aufrufen

Sie können das `BookmarkReached` Ereignis in Sprach-SDK abonnieren, um die Lesezeichen-Offsets zu erhalten.

> [!NOTE]
> Das `BookmarkReached`-Ereignis ist erst ab Speech SDK Version 1.16 verfügbar.

Ereignisse vom Typ `BookmarkReached` werden ausgelöst, wenn die ausgegebenen Audiodaten verfügbar werden, was schneller passiert als die Wiedergabe über ein Ausgabegerät.

* `AudioOffset` meldet die verstrichene Zeit der Audioausgabe zwischen dem Beginn der Synthese und dem Lesezeichen-Element. Dieser Wert wird in HNS-Einheiten (hundert Nanosekunden) gemessen. 10.000 HNS entsprechen einer Millisekunde.
* `Text` ist der Verweistext des Lesezeichen-Elements, bei dem es sich um die Zeichenfolge handelt, die im Attribut `mark` festgelegt wird.

# <a name="c"></a>[C#](#tab/csharp)

Weitere Informationen finden Sie unter <a href="/dotnet/api/microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```csharp
synthesizer.BookmarkReached += (s, e) =>
{
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    Console.WriteLine($"Bookmark reached. Audio offset: " +
        $"{e.AudioOffset / 10000}ms, bookmark text: {e.Text}.");
};
```

Für das obige SSML-Beispiel wird das `BookmarkReached` Ereignis zweimal ausgelöst, und die Konsolenausgabe ist
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="c"></a>[C++](#tab/cpp)

Weitere Informationen finden Sie unter <a href="/cpp/cognitive-services/speech/speechsynthesizer#bookmarkreached" target="_blank"> `BookmarkReached` </a>.

```cpp
synthesizer->BookmarkReached += [](const SpeechSynthesisBookmarkEventArgs& e)
{
    cout << "Bookmark reached. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "bookmark text: " << e.Text << "." << endl;
};
```

Für das obige SSML-Beispiel wird das `BookmarkReached` Ereignis zweimal ausgelöst, und die Konsolenausgabe ist
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="java"></a>[Java](#tab/java)

Weitere Informationen finden Sie unter <a href="/java/api/com.microsoft.cognitiveservices.speech.speechsynthesizer.bookmarkReached#com_microsoft_cognitiveservices_speech_SpeechSynthesizer_BookmarkReached" target="_blank"> `BookmarkReached` </a>.

```java
synthesizer.BookmarkReached.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Bookmark reached. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("bookmark text: " + e.getText() + ".");
});
```

Für das obige SSML-Beispiel wird das `BookmarkReached` Ereignis zweimal ausgelöst und die Konsolenausgabe ist
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="python"></a>[Python](#tab/python)

Weitere Informationen finden Sie unter <a href="/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechsynthesizer#bookmark-reached" target="_blank"> `bookmark_reached` </a>.

```python
# The unit of evt.audio_offset is tick (1 tick = 100 nanoseconds), divide it by 10,000 to convert to milliseconds.
speech_synthesizer.bookmark_reached.connect(lambda evt: print(
    "Bookmark reached: {}, audio offset: {}ms, bookmark text: {}.".format(evt, evt.audio_offset / 10000, evt.text)))
```

Für das obige SSML-Beispiel wird das `bookmark_reached` Ereignis zweimal ausgelöst und die Konsolenausgabe ist
```text
Bookmark reached, audio offset: 825ms, bookmark text: flower_1.
Bookmark reached, audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Weitere Informationen finden Sie unter <a href="/javascript/api/microsoft-cognitiveservices-speech-sdk/speechsynthesizer#bookmarkReached" target="_blank"> `bookmarkReached`</a>.

```javascript
synthesizer.bookmarkReached = function (s, e) {
    window.console.log("(Bookmark reached), Audio offset: " + e.audioOffset / 10000 + "ms, bookmark text: " + e.text);
}
```

Für das obige SSML-Beispiel wird das `bookmarkReached` Ereignis zweimal ausgelöst und die Konsolenausgabe ist
```text
(Bookmark reached), Audio offset: 825ms, bookmark text: flower_1.
(Bookmark reached), Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Weitere Informationen finden Sie unter <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer#addbookmarkreachedeventhandler" target="_blank"> `addBookmarkReachedEventHandler` </a>.

```objectivec
[synthesizer addBookmarkReachedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisBookmarkEventArgs *eventArgs) {
    // The unit of AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to converted to milliseconds.
    NSLog(@"Bookmark reached. Audio offset: %fms, bookmark text: %@.", eventArgs.audioOffset/10000., eventArgs.text);
}];
```

Für das obige SSML-Beispiel wird das `BookmarkReached` Ereignis zweimal ausgelöst und die Konsolenausgabe ist
```text
Bookmark reached. Audio offset: 825ms, bookmark text: flower_1.
Bookmark reached. Audio offset: 1462.5ms, bookmark text: flower_2.
```

# <a name="swift"></a>[Swift](#tab/swift)

Weitere Informationen finden Sie unter <a href="/objectivec/cognitive-services/speech/spxspeechsynthesizer" target="_blank"> `addBookmarkReachedEventHandler` </a>.

---

## <a name="next-steps"></a>Nächste Schritte

* [Sprachunterstützung: Stimmen, Gebietsschemas, Sprachen](language-support.md)
