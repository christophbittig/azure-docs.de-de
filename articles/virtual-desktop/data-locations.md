---
title: 'Datenstandorte für Azure Virtual Desktop: Azure'
description: Eine kurze Übersicht der Standorte, an denen die Daten und Metadaten von Azure Virtual Desktop gespeichert werden.
author: Heidilohr
ms.topic: conceptual
ms.custom: references_regions
ms.date: 06/30/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 709721139fa652798c5cc67b7f54e1b7452505a3
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273873"
---
# <a name="data-locations-for-azure-virtual-desktop"></a>Datenstandorte für Azure Virtual Desktop

Azure Virtual Desktop ist zurzeit für alle geografischen Standorte verfügbar. Administratoren können den Standort zum Speichern von Benutzerdaten auswählen, wenn sie die virtuellen Computer des Hostpools und die zugehörigen Dienste wie Dateiserver erstellen. Weitere Informationen zu Azure-Geografien finden Sie unter [Datenresidenz in Azure](https://azure.microsoft.com/global-infrastructure/data-residency/#overview).

>[!NOTE]
>Microsoft kontrolliert oder beschränkt nicht die Regionen, in denen Sie oder Ihre Benutzer auf Ihre benutzer- und App-spezifischen Daten zugreifen können.

>[!IMPORTANT]
>Azure Virtual Desktop speichert verschiedene Informationen wie Hostpoolnamen, App-Gruppennamen, Arbeitsbereichsnamen und Benutzerprinzipalnamen in einem Rechenzentrum. Beim Erstellen eines der Dienstobjekte muss der Kunde den Standort eingeben, an dem das Objekt erstellt werden soll. Der Standort dieses Objekts bestimmt, wo die Metadaten für das Objekt gespeichert werden. Der Kunde wählt eine Azure-Region aus, und die zugehörigen Informationen werden in der zugehörigen Geografie gespeichert. Eine Liste aller Azure-Regionen und der zugehörigen Geografien finden Sie unter [https://azure.microsoft.com/global-infrastructure/geographies/](https://azure.microsoft.com/global-infrastructure/geographies/).

In diesem Artikel wird beschrieben, welche Informationen der Azure Virtual Desktop-Dienst speichert. Weitere Informationen zu den Definitionen von Kundendaten finden Sie unter [So kategorisiert Microsoft Daten in Onlinediensten](https://www.microsoft.com/trust-center/privacy/customer-data-definitions).

## <a name="customer-input"></a>Kundeneingabe

Zum Einrichten des Azure Virtual Desktop-Diensts muss der Kunde Hostpools und andere Dienstobjekte erstellen. Während der Konfiguration muss der Kunde Informationen wie den Hostpoolnamen, den Namen der Anwendungsgruppe usw. angeben. Diese Informationen gelten als Kundeneingaben. Die Kundeneingabe wird in der Geografie gespeichert, die der Region zugeordnet ist, in der das Objekt erstellt wird. Azure Resource Manager-Pfade zu den Objekten werden als Organisationsinformationen betrachtet, sodass die Datenresidenz nicht für sie gilt. Daten zu Azure Resource Manager-Pfaden werden außerhalb der ausgewählten Geografie gespeichert.

## <a name="customer-data"></a>Kundendaten

Der Dienst speichert benutzer- oder App-bezogene Informationen nicht direkt, vielmehr werden Kundendaten wie Anwendungsnamen und Benutzerprinzipalnamen gespeichert, da sie zum Objekteinrichtungsprozess gehören. Diese Informationen werden in der Geografie gespeichert, die der Region zugeordnet ist, in der der Kunde das Objekt erstellt hat.

## <a name="diagnostic-data"></a>Diagnosedaten

Azure Virtual Desktop sammelt vom Dienst generierte Diagnosedaten, wenn der Kunde oder Benutzer mit dem Dienst interagiert. Diese Daten werden nur in aggregierter Form und ausschließlich für die Problembehandlung, den Support und die Überprüfung der Integrität des Diensts verwendet. Wenn sich beispielsweise eine VM aufseiten des Sitzungshosts beim Dienst registriert, werden Informationen generiert (z. B. der Name der VM, zu welchem Hostpool die VM gehört usw.). Diese Informationen werden in der Geografie gespeichert, die der Region zugeordnet ist, in der der Hostpool erstellt wird. Wenn ein Benutzer eine Verbindung mit dem Dienst herstellt und einen Remotedesktop startet, werden außerdem Diagnoseinformationen generiert, die u. a. den Benutzerprinzipalnamen, den Clientstandort, die Client-IP-Adresse und den Hostpool, mit dem der Benutzer eine Verbindung herstellt, umfassen. Diese Informationen werden an zwei verschiedene Standorte übermittelt:

- Den Standort, der dem Benutzer am nächsten liegt, an dem sich die Dienstinfrastruktur (Client- und Benutzerüberwachung, Diagnosedaten) befinden
- Den Standort, an dem sich der Hostpool befindet

## <a name="service-generated-data"></a>Vom Dienst generierte Daten

Um Azure Virtual Desktop zuverlässig und skalierbar zu halten, werden Datenverkehrsmuster und -nutzung aggregiert, um die Integrität und Leistung der Infrastruktursteuerungsebene zu überwachen. Damit wir beispielsweise erfahren, ob die Kapazität der regionalen Infrastruktur erhöht werden muss, wenn die Dienstnutzung zunimmt, verarbeiten wir Protokolldaten zur Dienstnutzung. Anschließend überprüfen wir die Protokolle auf Spitzenzeiten und entscheiden, welche Rechenzentren hinzugefügt werden müssen, um diese Kapazität zu erreichen. Diese Informationen werden von allen Standorten aggregiert, an denen sich die Dienstinfrastruktur befindet, und dann an die Region „USA“ übermittelt. Für die an die US-Region gesendeten Daten wird ein Scrubbing ausgeführt, es werden keine Kundendaten übertragen.

Derzeit wird das Speichern der oben genannten Daten an den folgenden Standorten unterstützt:

- Vereinigte Staaten (USA) (allgemein verfügbar)
- Europa (EU) (allgemein verfügbar)
- Vereinigtes Königreich (UK) (allgemein verfügbar)
- Kanada (CA) (allgemein verfügbar)

Mit einer zunehmenden Verbreitung des Diensts werden weitere Geografien hinzugefügt. Die gespeicherten Informationen werden im Ruhezustand verschlüsselt. Außerdem werden georedundante Spiegelungen innerhalb der Geografie verwaltet. Kundendaten wie App-Einstellungen und Benutzerdaten befinden sich an dem vom Kunden ausgewählten Standort und werden nicht vom Dienst verwaltet.

Die genannten Daten werden innerhalb der Azure-Geografie für die Notfallwiederherstellung repliziert.
