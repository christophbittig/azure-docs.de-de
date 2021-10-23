---
author: anastasia-ms
ms.service: iot-dps
ms.topic: include
ms.date: 10/10/2021
ms.author: v-stharr
ms.openlocfilehash: fa416ff3e3ef5b502b65ac414558692ccd7071b5
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129866132"
---
> [!NOTE]
> In einigen Bereichen dieses Diensts lassen sich Grenzwerte anpassen. Diese sind in der nachfolgenden Tabelle in der Spalte *Anpassbar?* entsprechend gekennzeichnet. Wenn der Grenzwert angepasst werden kann, enthält die Spalte *Anpassbar?* den Wert *Ja*.
>
>Der tatsächliche Wert, an den ein Grenzwert angepasst werden kann, kann je nach Bereitstellung der einzelnen Kunden variieren. Für sehr große Bereitstellungen sind möglicherweise mehrere Instanzen von DPS erforderlich.
>
> Falls Ihr Unternehmen die Anhebung eines anpassbaren Grenzwertes oder Kontingents über den Standardgrenzwert hinaus benötigt, können Sie zusätzliche Ressourcen anfordern, indem Sie [ein Supportticket eröffnen](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Die folgende Tabelle enthält die Grenzwerte, die für Ressourcen des Azure IoT Hub Device Provisioning-Diensts gelten.

| Resource | Begrenzung | Anpassbar? |
| --- | --- | --- |
| Maximale Anzahl von Gerätebereitstellungsdiensten pro Azure-Abonnement | 10 | Ja |
| Maximale Anzahl von Registrierungen (Registrations) | 1\.000.000 | Ja |
| Maximale Anzahl individueller Registrierungen | 1\.000.000 | Ja |
| Maximale Anzahl von Registrierungsgruppen *(X.509-Zertifikat)* | 100 | Ja |
| Maximale Anzahl von Registrierungsgruppen *(symmetrischer Schlüssel)* | 100 | Nein |
| Maximale Anzahl von Zertifizierungsstellen | 25 | Nein |
| Maximale Anzahl der verknüpften IoT-Hubs | 50 | Nein |
| Maximale Nachrichtengröße | 96 KB| Nein |

> [!TIP]
> Wenn der harte Grenzwert für Registrierungsgruppen mit symmetrischem Schlüssel ein Blockierproblem ist, wird empfohlen, individuelle Registrierungen als Problemumgehung zu verwenden.

Für den Device Provisioning-Dienst gelten die folgenden Ratenbegrenzungen.

| Rate | Wert pro Einheit | Anpassbar? |
| --- | --- | --- |
| Vorgänge | 200/Minute/Dienst | Ja |
| Geräteregistrierungen | 200/Minute/Dienst | Ja |
| Abrufvorgang für Geräte | 5/10 Sekunden/Gerät | Nein |

Jeder API-Aufruf für DPS ist als ein *Vorgang* abrechenbar. Dies schließt alle Dienst-APIs und die Geräteregistrierungs-API ein. Der Abrufvorgang für die Geräteregistrierung wird nicht abgerechnet.