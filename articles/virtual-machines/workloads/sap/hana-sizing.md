---
title: Festlegen der Größe für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Erfahren Sie mehr über das Festlegen der Größe für SAP HANA in Azure (große Instanzen).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/16/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4050a986c98b492f71383bd5c677d68288882611
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/18/2021
ms.locfileid: "114404657"
---
# <a name="sizing"></a>Festlegen der Größe

In diesem Artikel erhalten Sie Informationen zur Dimensionierung für HANA (große Instanzen). Im Allgemeinen unterscheidet sich die Dimensionierung für HANA (große Instanzen) nicht von der Dimensionierung für HANA. 

## <a name="moving-an-existing-system-to-sap-hana-large-instances"></a>Verschieben eines vorhandenen Systems nach SAP HANA (große Instanzen)

Angenommen, Sie möchten ein vorhandenes bereitgestelltes System von einem anderen Managementsystem für relationale Datenbanken (RDBMS) zu HANA verschieben. SAP stellt Berichte bereit, die auf Ihrem vorhandenen SAP-System ausgeführt werden. Wenn die Datenbank zu HANA migriert wird, überprüfen diese Berichte die Daten und berechnen Arbeitsspeicheranforderungen für die HANA-Instanz. 

Weitere Informationen zur Ausführung dieser Berichte und zum Abrufen der aktuellen Patches oder Versionen finden Sie in den folgenden SAP-Hinweisen:

- [SAP-Hinweis 1793345 – Größenanpassung für die SAP-Suite für HANA](https://launchpad.support.sap.com/#/notes/1793345)
- [SAP-Hinweis 1872170 – Bericht zur Größenanpassung für die Suite für HANA und S/4 HANA](https://launchpad.support.sap.com/#/notes/1872170)
- [SAP-Hinweis 2121330 – Häufig gestellte Fragen: Bericht zur Größenanpassung für SAP BW unter HANA](https://launchpad.support.sap.com/#/notes/2121330)
- [SAP-Hinweis 1736976 – Bericht zur Größenanpassung für BW unter HANA](https://launchpad.support.sap.com/#/notes/1736976)
- [SAP-Hinweis 2296290 – Neuer Bericht zur Größenanpassung für BW unter HANA](https://launchpad.support.sap.com/#/notes/2296290)

## <a name="sizing-greenfield-implementations"></a>Dimensionieren von Greenfield-Implementierungen

Wenn Sie eine Implementierung von Grund auf neu starten, berechnet SAP Quick Sizer die Arbeitsspeicheranforderungen der Implementierung von SAP-Software auf HANA.

## <a name="memory-requirements"></a>Speicheranforderungen

Der Arbeitsspeicherbedarf für HANA nimmt mit zunehmender Datenmenge zu. Beobachten Sie Ihre aktuelle Arbeitsspeichernutzung, um den zukünftigen Bedarf zu prognostizieren. Basierend auf den Arbeitsspeicheranforderungen können Sie Ihren Bedarf dann einer der SKUs von HANA (große Instanz) zuordnen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Anforderungen für das Onboarding von HANA (große Instanzen).

> [!div class="nextstepaction"]
> [Onboardinganforderungen](hana-onboarding-requirements.md)
