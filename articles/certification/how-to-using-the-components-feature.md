---
title: 'Vorgehensweise: Verwenden des Komponentenfeatures im Azure Certified Device-Portal'
description: Eine Anleitung zur optimalen Verwendung des Komponentenfeatures des Abschnitts „Gerätedetails“ zur genauen Beschreibung Ihres Geräts.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 05/04/2021
ms.custom: template-how-to
ms.openlocfilehash: 494ff350866d8de1386a5fd98539f0e3e5ff2c3f
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/01/2021
ms.locfileid: "113126300"
---
# <a name="add-components-on-the-portal"></a>Hinzufügen von Komponenten im Portal

Wenn Sie das [Tutorial: Hinzufügen von Gerätedetails](tutorial-02-adding-device-details.md) zu Ihrem Zertifizierungsprojekt absolvieren, wird von Ihnen erwartet, dass Sie die Hardwarespezifikationen Ihres Geräts beschreiben. Zu diesem Zweck können Sie mehrere (als **Komponenten** bezeichnete) separate Hardwareprodukte hervorheben, die zu Ihrem Gerät gehören. Auf diese Weise können Sie besser für Geräte werben, die zusätzliche Hardware enthalten, und Kunden erleichtern, bei der Suche im Katalog auf Basis dieser Features das richtige Produkt zu finden.

## <a name="prerequisites"></a>Voraussetzungen

- Sie müssen angemeldet sein und über ein Projekt für Ihr Gerät im [Azure Certified Device-Portal](https://certify.azure.com) verfügen. Weitere Informationen finden Sie im [Tutorial](tutorial-01-creating-your-project.md).

## <a name="how-to-add-components"></a>Vorgehensweise beim Hinzufügen von Komponenten

Jedes Projekt, das zur Zertifizierung übermittelt wird, enthält eine **Sofort einsatzfähiges Produkt**-Komponente (die in vielen Fällen das ganze Produkt selbst darstellt). Um die Besonderheit einer „Sofort einsatzfähiges Produkt“-Komponente besser zu verstehen, konsultieren Sie unser [Zertifizierungsglossar](./resources-glossary.md). Alle zusätzlichen Komponenten können Sie nach eigenem Ermessen einbeziehen, um Ihre Gerät präzise zu charakterisieren.

1. Wählen Sie im Hardware-Tab `Add a component` aus.

    ![Hinzufügen eines Komponentenlinks](./media/images/add-component-new.png)

1. Füllen Sie die relevanten Formularfelder für die Komponente aus.

    ![Komponentendetailsabschnitt](./media/images/component-details-section.png)

1. Speichern Sie Ihre Informationen mithilfe der Schaltfläche `Save Product Details` am unteren Rand der Seite:  

    ![Schaltfläche „Produktdetails speichern“](./media/images/save-product-details-button.png)

1. Nachdem Sie die Komponente gespeichert haben, können Sie die von ihr unterstützten Hardwarefunktionen weiter anpassen. Wählen Sie den Link `Edit` neben dem Komponentennamen aus.  

    ![Schaltfläche „Bearbeiten“ der Komponente](./media/images/component-edit.png)

1. Geben Sie ggf. relevante Informationen zur Hardwarefunktionalität ein.  

    ![Abbildung der bearbeitbaren Komponentenabschnitte](./media/images/component-selection-area.png)  

    Die bearbeitbaren Komponentenfelder (siehe oben) umfassen Folgendes:

    - **Allgemein**: Hardwaredetails wie Prozessoren und sichere Hardware
    - **Konnektivität**: Konnektivitätsoptionen, Protokolle und Schnittstellen wie z. B. Funk und GPIO
    - **Beschleuniger**: Hardwarebeschleunigung wie GPU und VPU
    - **Sensoren**: Verfügbare Sensoren wie GPS und Vibration
    - **Weitere Spezifikationen**: Zusätzliche Informationen über das Gerät, z. B. Abmessungen und Informationen zu Speicher/Akku

1. Wählen Sie `Save Product Details` unten auf der Seite „Produktdetails“ aus.

## <a name="component-use-requirements-and-recommendations"></a>Voraussetzungen der Komponentennutzung und Empfehlungen

Möglicherweise haben Sie Fragen dazu, wie viele Komponenten eingeschlossen werden sollen, oder welcher Komponententyp verwendet werden soll. Im Folgenden finden Sie Beispielszenarien für Geräte, die Sie möglicherweise zertifizieren, und Beispiele zur Verwendung des Komponentenfeatures.

| Produkttyp                                       | Nein. Komponenten | Komponente 1/Anlagentyp      | Komponenten 2+/Anlagentyp                    |
|----------------------------------------------------|------------|----------------------------------|--------------------------------------------------|
| Fertiggestelltes Produkt                                   | 1          | Sofort einsatzfähiges Produkt, Eigenständig | –                                              |
| Fertiges Produkt mit **abnehmbaren Peripheriegeräten** | 2 oder mehr  | Sofort einsatzfähiges Produkt, Eigenständig | Peripheriegerät/Eigenständig oder integriert              |
| Fertiges Produkt mit **integrierten Komponenten**  | 2 oder mehr  | Sofort einsatzfähiges Produkt, Eigenständig | Geeigneten Typ auswählen/Eigenständig oder Integriert |
| Implementierungsfertiges Entwicklungskit                             | Mindestens 1  | Kundenfertiges Produkt oder Entwicklungsboard, separat oder integriert| Geeigneten Typ auswählen/Eigenständig oder integriert |

## <a name="example-component-usage"></a>Beispiel für Komponentenverwendung

Im Folgenden finden Sie Beispiele dafür, wie ein OEM mit dem Namen Contoso mit dem Komponentenfeature sein Produkt mit dem Namen „Falcon“ zertifizieren würde.

1. Falcon ist ein komplett eigenständiges Gerät, das nicht in ein größeres Produkt integriert wird.
    1. Nein. der Komponenten: 1
    1. Komponentengerätetyp: Sofort einsatzfähiges Produkt
    1. Anlagentyp: Eigenständig

     ![Abbildung des sofort einsatzfähigen Produkts](./media/images/customer-ready-product.png)

1. Falcon ist ein Gerät, das ein integriertes, von INC Electronics hergestelltes peripheres Kameramodul umfasst, das über USB mit Falcon verbunden ist.
    1. Nein. der Komponenten: 2
    1. Komponentengerätetyp: Sofort einsatzfähiges Produkt, Peripher
    1. Anlagentyp: Eigenständig, integriert
    
    > [!Note]
    > Die Peripheriekomponente wird als integriert betrachtet, da sie nicht entfernt werden kann.

     ![Abbildung der Beispielperipheriekomponente](./media/images/peripheral.png)

1. Falcon ist ein Gerät, das ein integriertes System On Module von INC Electronics umfasst, das einen integrierten Prozessor Apollo52 des Unternehmens Espressif verwendet und eine ARM64-Architektur aufweist.
    1. Nein. der Komponenten: 2
    1. Komponentengerätetyp: Sofort einsatzfähiges Produkt, System On Module
    1. Anlagentyp: Eigenständig, integriert

    > [!Note]
    > Die Peripheriekomponente wird als integriert betrachtet, da sie nicht entfernt werden kann. Die SoM-Komponente würde auch Prozessorinformationen enthalten.

     ![Abbildung der SoM-Beispielkomponente ](./media/images/system-on-module.png)

## <a name="additional-tips"></a>Weitere Tipps

Im Folgenden finden Sie weitere Erläuterungen zu unserer Richtlinie zur Komponentenverwendung. Wenn Sie Fragen zur richtigen Komponentenverwendung haben, wenden Sie sich unter [iotcert@microsoft.com](mailto:iotcert@microsoft.com) an unser Team. Wir freuen uns darauf, Ihnen helfen zu dürfen!

1. Ein Projekt darf **nur** eine „Sofort einsatzfähiges Produkt“-Komponente enthalten. Wenn Sie ein Projekt mit zwei unabhängigen Geräten zertifizieren, sollten diese Geräte separat zertifiziert werden.
1. Es liegt in erster Linie an Ihnen, Komponenten zu verwenden (oder auch nicht), um bei potenziellen Kunden mit den Funktionen Ihres Geräts zu werben.
1. Während der Überprüfung Ihres Geräts setzt das Azure-Zertifizierungsteam nur voraus, dass mindestens eine „Sofort einsatzfähiges Produkt“-Komponente aufgelistet wird. Es kann jedoch vorkommen, dass Änderungen der Komponenteninformationen angefordert werden, wenn die Details nicht eindeutig sind oder unvollständig zu sein scheinen (wenn z. B. der Komponentenhersteller für eine „Sofort einsatzfähiges Produkt“-Komponente nicht angegeben ist).

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun bereit sind, unser Komponentenfeature zu verwenden, können Sie jetzt Ihre Gerätedetails vervollständigen oder Ihr Projekt bearbeiten und für mehr Transparenz sorgen.

- [Tutorial: Hinzufügen von Gerätedetails](tutorial-02-adding-device-details.md)
- [Bearbeiten Ihres veröffentlichten Geräts](how-to-edit-published-device.md)
