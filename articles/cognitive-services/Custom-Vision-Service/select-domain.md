---
title: 'Auswählen einer Domäne für ein Custom Vision-Projekt: maschinelles Sehen'
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erfahren Sie, wie Sie eine Domäne für Ihr Projekt im Custom Vision Service auswählen.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 5838185328920587b11bb66d21c9402ce93e9b6b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458245"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Auswählen einer Domäne für ein Custom Vision-Projekt

Auf der Registerkarte „Einstellungen“ Ihres Custom Vision-Projekt können Sie eine Domäne für das Projekt auswählen. Wählen Sie die Domäne, die Ihrem Szenario am nächsten kommt. Wenn Sie auf Custom Vision über eine Clientbibliothek oder eine REST-API zugreifen, brauchen Sie beim Erstellen des Projekts keine Domänen-ID anzugeben. Sie können eine Liste der Domänen-IDs mithilfe von [Get Domains](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab) abrufen oder die Tabelle unten verwenden.

## <a name="image-classification"></a>Bildklassifizierung

|Domain|Zweck|
|---|---|
|__Allgemein__| Für eine breite Palette von Aufgaben in der Bildklassifizierung optimiert. Wenn keine der anderen speziellen Domänen geeignet erscheint oder Sie unsicher sind, welche Domäne Sie wählen sollen, verwenden Sie eine der allgemeinen Domänen. ID: `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__General [A1]__ (Allgemein [A1])| Diese ist optimiert für eine bessere Genauigkeit mit vergleichbarer Rückschlusszeit wie die allgemeine Domäne. Sie wird für größere Datasets oder schwierigere Benutzerszenarios empfohlen. Diese Domäne erfordert mehr Trainingszeit. ID: `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Allgemein [A2]__| Diese ist optimiert für eine bessere Genauigkeit mit kürzerer Rückschlusszeit als die Domänen „Allgemein [A1]“ und „Allgemein“. Wird für die meisten Datasets empfohlen. Diese Domäne erfordert weniger Trainingszeit als die Domänen „Allgemein“ und „Allgemein [A1]“. ID: `2e37d7fb-3a54-486a-b4d6-cfc369af0018` |
|__Food (Lebensmittel)__|Für Fotos von Gerichten optimiert, wie sie beispielsweise auf der Speisekarte von Restaurants abgebildet werden. Wenn Sie Fotos von einzelnen Früchten oder Gemüsen klassifizieren möchten, verwenden Sie die Domäne „Food“. ID: `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Landmarks (Wahrzeichen)__|Optimiert für erkennbare Wahrzeichen (Naturdenkmäler oder künstlich geschaffene Wahrzeichen). Diese Domäne funktioniert am besten, wenn das Wahrzeichen im Foto deutlich zu sehen ist. Die Domäne funktioniert auch, wenn das Wahrzeichen etwas von Personen im Vordergrund verdeckt wird. ID: `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Retail (Einzelhandel)__|Für Bilder optimiert, wie man sie in einem Einkaufskatalog oder auf einer Einkaufswebsite findet. Wenn Sie eine präzise Klassifizierung zwischen Kleidern, Hosen und Hemden wünschen, verwenden Sie diese Domäne. ID: `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Kompaktdomänen__| Für die Bedingungen der Echtzeitklassifizierung auf Edgegeräten optimiert.|


> [!NOTE]
> Die Domänen „Allgemein [A1]“ und „Allgemein [A2]“ können für eine Vielzahl von Szenarien verwendet werden und sind für Genauigkeit optimiert. Verwenden Sie das Modell „Allgemein [A2]“ , um eine höhere Rückschlussgeschwindigkeit und eine kürzere Trainingszeit zu erzielen. Bei größeren Datasets empfiehlt es sich unter Umständen, „Allgemein [A1]“ zu verwenden, um eine bessere Genauigkeit als mit „Allgemein [A2]“ zu erzielen, obwohl mehr Trainings- und Rückschlusszeit erforderlich sind. Das Modell „Allgemein“ erfordert mehr Rückschlusszeit als „Allgemein [A1]“ und „Allgemein [A2]“.

## <a name="object-detection"></a>Objekterkennung

|Domain|Zweck|
|---|---|
|__Allgemein__| Für eine Vielzahl von Aufgaben der Objekterkennung optimiert. Wenn keine der anderen Domänen geeignet erscheint oder Sie unsicher sind, welche Domäne Sie wählen sollen, verwenden Sie die allgemeine Domäne. ID: `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__General [A1]__ (Allgemein [A1])| Diese ist optimiert für eine bessere Genauigkeit mit vergleichbarer Rückschlusszeit wie die allgemeine Domäne. Sie wird für genauere Anforderungen an die Region, größere Datasets oder schwierigere Benutzerszenarios empfohlen. Diese Domäne erfordert mehr Trainingszeit, und die Ergebnisse sind nicht deterministisch. Bei Verwendung der gleichen Trainingsdaten ist mit einem mAP-Unterschied (Mean Average Precision, Präzision des gewogenen Mittelwerts) von +-1 % zu rechnen. ID: `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logo__|Für die Suche nach Markenlogos in Bildern optimiert. ID: `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Produkte in Regalen__|Für die Erkennung und Klassifizierung von Produkten in Regalen optimiert. ID: `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Kompaktdomänen__| Für die Bedingungen der Echtzeitobjekterkennung auf Edgegeräten optimiert.|

## <a name="compact-domains"></a>Kompaktdomänen

Die von Kompaktdomänen generierten Modelle können für die lokale Ausführung exportiert werden. In der öffentlichen Vorschauversion 3.4 der API von Custom Vision können Sie eine Liste der exportierbaren Plattformen für kompakte Domänen erhalten, indem Sie die GetDomains-API aufrufen.

Alle folgenden Domänen unterstützen den Export im ONNX-, TensorFlow-, TensorFlowLite-, TensorFlow.js-, CoreML- und VAIDK-Format, mit der Ausnahme, dass die Domäne **Objekterkennung Allgemein (kompakt)** nicht VAIDK unterstützt.

Die Modellleistung variiert je nach ausgewählter Domäne. In der folgenden Tabelle geben wir die Modellgröße und die Rückschlusszeit auf der Intel Desktop-CPU und der NVIDIA-GPU \[1\] an. Diese Zahlen enthalten keine Vor- und Nachverarbeitungszeit.

|Aufgabe|Domain|id|Modellgröße|CPU-Rückschlusszeit|GPU-Rückschlusszeit|
|---|---|---|---|---|---|
|Klassifizierung|General (compact) (Allgemein (kompakt))|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 ms|5 ms|
|Klassifizierung|Allgemein (kompakt) [$1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 ms|5 ms|
|Objekterkennung|General (compact) (Allgemein (kompakt))|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 ms|5 ms|
|Objekterkennung|Allgemein (kompakt) [$1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 ms|7 ms|

>[!NOTE]
>Die Domäne __Allgemein (kompakt)__ für die Objekterkennung erfordert eine spezielle Nachverarbeitungslogik. Die Einzelheiten entnehmen Sie einem Beispielskript im exportierten ZIP-Paket. Wenn Sie ein Modell ohne die Nachverarbeitungslogik benötigen, verwenden Sie __Allgemein (kompakt) [S1]__ .

>[!IMPORTANT]
>Es gibt keine Garantie dafür, dass die exportierten Modelle genau das gleiche Ergebnis wie die Vorhersage-API in der Cloud liefern. Geringfügige Unterschiede bei der Ausführungsplattform oder der Vorverarbeitungsimplementierung können größere Unterschiede in den Modellausgaben verursachen. Die Einzelheiten zur Vorverarbeitungslogik finden Sie in [diesem Dokument](quickstarts/image-classification.md).

\[1\] Intel Xeon E5-2690 CPU und NVIDIA Tesla M60
