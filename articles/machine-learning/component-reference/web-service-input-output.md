---
title: 'Webdiensteingabe/-ausgabe: Komponentenreferenz'
description: Erfahren Sie, wie Sie mit den Webdienstkomponenten im Azure Machine Learning-Designer Ein- und Ausgaben verwalten.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 67741a786f60b45e2e0cc7f728aa136397220899
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566521"
---
# <a name="web-service-input-and-web-service-output-components"></a>Komponenten „Web Service Input“ und „Web Service Output“ (Webdiensteingabe und Webdienstausgabe)

In diesem Artikel werden die Komponenten „Web Service Input“ (Webdiensteingabe) und „Web Service Output“ (Webdienstausgabe) im Azure Machine Learning-Designer beschrieben.

Die Komponente „Web Service Input“ (Webdiensteingabe) kann nur eine Verbindung mit einem Eingabeport vom Typ **DataFrameDirectory** herstellen. Die Komponente „Web Service Output“ (Webdienstausgabe) kann nur eine Verbindung mit einem Ausgabeport vom Typ **DataFrameDirectory** herstellen. Sie finden die beiden Komponenten in der Komponentenstruktur unter der Kategorie **Web Service** (Webdienst). 

Die Komponente „Web Service Input“ (Webdiensteingabe) gibt an, wo Benutzerdaten in die Pipeline gelangen. Die Komponente „Web Service Output“ (Webdienstausgabe) gibt an, wo Benutzerdaten in einer Echtzeit-Rückschlusspipeline zurückgegeben werden.

## <a name="how-to-use-web-service-input-and-output"></a>Verwenden von Web Service Input und Output

Wenn Sie aus Ihrer Trainingspipeline [eine Echtzeit-Rückschlusspipeline erstellen](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline), werden automatisch die Komponenten „Web Service Input“ (Webdiensteingabe) und „Web Service Output“ (Webdienstausgabe) hinzugefügt, um anzuzeigen, wo Benutzerdaten in die Pipeline gelangen und wo Daten zurückgegeben werden. 

> [!NOTE]
> Das automatische Generieren einer Echtzeit-Rückschlusspipeline ist ein regelbasierter Prozess nach bestem Wissen. Dabei gibt es keine Garantie für die Richtigkeit. 

Sie können die Komponenten „Web Service Input“ (Webdiensteingabe) und „Web Service Output“ (Webdienstausgabe) entsprechend Ihren Anforderungen manuell hinzufügen oder entfernen. Stellen Sie sicher, dass Ihre Echtzeit-Rückschlusspipeline mindestens über eine Komponente „Web Service Input“ (Webdiensteingabe) und eine Komponente „Web Service Output“ (Webdienstausgabe) verfügt. Wenn Sie über mehrere Komponenten „Web Service Input“ (Webdiensteingabe) oder „Web Service Output“ (Webdienstausgabe) verfügen, stellen Sie sicher, dass diese eindeutige Namen aufweisen. Sie können den Namen im rechten Bereich der Komponente eingeben.

Sie können eine Echtzeit-Rückschlusspipeline auch manuell erstellen, indem Sie Ihrer noch nicht übermittelten Pipeline die Komponenten „Web Service Input“ (Webdiensteingabe) und „Web Service Output“ (Webdienstausgabe) hinzufügen.

> [!NOTE]
> Der Typ der Pipeline wird beim ersten Übermitteln festgelegt. Stellen Sie also sicher, dass Sie die Komponenten „Web Service Input“ (Webdiensteingabe) und „Web Service Output“ (Webdienstausgabe) vor dem ersten Übermitteln hinzufügen.

Im nachstehenden Beispiel wird gezeigt, wie Sie aus der Komponente „Execute Python Script“ (Python-Skript ausführen) manuell eine Echtzeit-Rückschlusspipeline erstellen. 

![Beispiel](media/module/web-service-input-output-example.png)
   
Nachdem Sie die Pipeline übermittelt haben und die Ausführung erfolgreich abgeschlossen wurde, können Sie den Echtzeitendpunkt bereitstellen.
   
> [!NOTE]
>  Im obigen Beispiel stellt die **manuelle Dateneingabe** das Datenschema für die Webdiensteingabe bereit und ist für die Bereitstellung des Echtzeitendpunkts erforderlich. Im Allgemeinen sollten Sie immer eine Komponente oder ein Dataset mit dem Port verbinden, mit dem **Web Service Input** (Webdiensteingabe) verbunden ist, um das Datenschema bereitzustellen.
   
## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über das [Bereitstellen des Echtzeitendpunkts](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Sehen Sie sich die [verfügbaren Komponenten](component-reference.md) für Azure Machine Learning an.