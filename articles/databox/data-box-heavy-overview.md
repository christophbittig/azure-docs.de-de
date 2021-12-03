---
title: Microsoft Azure Data Box Heavy – Übersicht | Microsoft-Dokumentation
description: In diesem Artikel wird Azure Data Box beschrieben, eine Hybridlösung, die zum Übertragen sehr großer Datenmengen in Azure dient.
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 10/20/2021
ms.author: alkohli
ms.openlocfilehash: 57a5ccb94f94fdc12083f49394235123b0e26d96
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251868"
---
# <a name="what-is-azure-data-box-heavy"></a>Was ist Azure Data Box Heavy?

Azure Data Box Heavy ermöglicht Ihnen, hunderte Terabyte an Daten schnell, kostengünstig und zuverlässig an Azure zu senden. Die Daten werden nach Azure übertragen, indem Ihnen ein Data Box Heavy-Gerät mit einer Speicherkapazität von 1 PB geliefert wird, das Sie mit Ihren Daten füllen und an Microsoft zurücksenden. Das Gerät verfügt über ein widerstandsfähiges Gehäuse zum Schützen und Absichern Ihrer Daten während des Transports.

Richten Sie das Gerät nach Empfang in Ihrem Rechenzentrum mithilfe der lokalen Webbenutzeroberfläche ein. Kopieren Sie die Daten von Ihren Servern auf das Gerät, und senden Sie es zurück an Azure. Im Azure-Rechenzentrum werden Ihre Daten auf Ihre Azure Storage-Konten hochgeladen. Sie können den gesamten Prozess End-to-End im Azure-Portal nachverfolgen.


> [!IMPORTANT]
> - Registrieren Sie sich im [Azure-Portal](https://portal.azure.com), um ein Gerät zu bestellen.


## <a name="use-cases"></a>Anwendungsfälle

Data Box Heavy eignet sich am besten für Datenvolumen in der Größenordnung von mehreren Hundert Terabyte, wenn die Netzwerkverbindung für den Upload zu Azure unzureichend ist. Die Datenverschiebung kann eine einmalige, eine periodische oder eine erste Massenübertragung von Daten sein, auf die regelmäßige Übertragungen folgen. Es folgen die verschiedenen Szenarien, in denen Data Box Heavy für die Datenübertragung verwendet werden kann.

 - **Einmalige Migration**: Wird verwendet, wenn eine große Menge von lokalen Daten in Azure verschoben wird.
     - Verschieben einer Medienbibliothek von Offlinebändern in Azure, um eine Onlinemedienbibliothek zu erstellen.
     - Migrieren Ihrer VM-Farm, SQL-Server und Anwendungen zu Azure.
     - Verschieben von Verlaufsdaten in Azure für eine detaillierte Analyse und Berichterstellung mithilfe von HDInsight.

 - **Erste Massenübertragung**: Die Ausführung einer ersten Massenübertragung mithilfe von Data Box Heavy (Seeding) gefolgt von inkrementellen Übertragungen über das Netzwerk.
     - Beispielsweise werden Data Box Heavy und ein Partner im Bereich Sicherungslösungen herangezogen, um die erste große Sicherung von Verlaufsdaten in Azure zu verschieben. Im Anschluss werden die inkrementellen Daten über das Netzwerk in Azure Storage übertragen.

 - **Periodische Uploads**: Werden verwendet, wenn regelmäßig große Datenmengen generiert werden und in Azure verschoben werden müssen. Ein Beispiel hierfür ist die Exploration in der Energiebranche, bei der Videodaten auf Bohrinseln und für Windfarmen generiert werden.

## <a name="benefits"></a>Vorteile

Data Box Heavy ist so ausgelegt, dass sehr große Datenmengen ohne bzw. mit geringfügigen negativen Auswirkungen auf Ihr Netzwerk in Azure verschoben werden können. Diese Lösung hat die folgenden Vorteile:

- **Geschwindigkeit**: Data Box Heavy verwendet sehr leistungsfähige Netzwerkschnittstellen mit 40 Gbit/s.

- **Sicherheit**: Data Box Heavy bietet einen integrierten Sicherheitsschutz für Geräte, Daten und den Dienst.
    - Das Gerät verfügt über ein widerstandsfähiges Gehäuse, das durch manipulationssichere Schrauben und Etiketten geschützt ist.
    - Die Daten auf dem Gerät sind jederzeit durch AES-256-Bit-Verschlüsselung geschützt.
    - Das Gerät kann nur mit einem Kennwort entsperrt werden, das über das Azure-Portal bereitgestellt wird.
    - Der Dienst ist durch Azure-Sicherheitsfunktionen geschützt.
    - Nachdem Ihre Daten in Azure hochgeladen wurden, werden die Datenträger auf dem Gerät gemäß NIST-Standard 800-88r1 (National Institute of Standards and Technology) vollständig bereinigt.


## <a name="features-and-specifications"></a>Funktionen und Spezifikationen

Das Data Box Heavy-Gerät weist in dieser Version folgende Merkmale auf.

| Spezifikationen                                          | BESCHREIBUNG              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | ~ 227 kg <br>Gerät auf Feststellrädern für den Transport|
| Dimensionen                                              | Breite: 66 cm Höhe: 71 cm Länge: 122 cm |
| Platzbedarf im Rack                                              | Nicht im Rack einbaubar|
| Erforderliche Kabel                                         | 4 geerdete 120 V/10 A-Netzkabel (NEMA 5-15) enthalten <br> Das Gerät unterstützt bis zu 240 V Netzspannung und weist C-13-Kaltgerätekupplungen auf <br> Verwenden Sie Netzwerkkabel, die mit [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) kompatibel sind.  |
| Power                                                    | 4 eingebaute Netzteile (PSUs), die gemeinsam übergreifend über beide Geräteknoten verwendet werden <br> 1\.200 Watt Nennleistung|
| Speicherkapazität                                        | ~ 1 PB unformatiert, 70 Datenträger mit jeweils 14 TB <br> 770 TB nutzbare Kapazität|
| Anzahl von Knoten                                          | 2 unabhängige Knoten pro Gerät (je 500 TB) |
| Netzwerkschnittstellen pro Knoten                             | 4 Netzwerkschnittstellen pro Knoten <br><br> MGMT, DATA3 <ul><li> 2 X 1-GbE-Schnittstellen </li><li> MGMT dient zur Verwaltung und Ersteinrichtung und ist nicht vom Benutzer konfigurierbar </li><li> DATA3 ist vom Benutzer konfigurierbar und standardmäßig DHCP (Dynamic Host Configuration Protocol)</li></ul>DATA1-, DATA2-Datenschnittstellen <ul><li>2 X 40-GbE-Schnittstellen </li><li> Für DHCP (Standard) vom Benutzer konfigurierbar oder statisch</li></ul>|


## <a name="components"></a>Komponenten

Data Box Heavy umfasst die folgenden Komponenten:

* **Data Box Heavy-Gerät**: Ein physisches Gerät mit einem widerstandsfähigen Gehäuse, in dem Daten sicher gespeichert werden. Dieses Gerät bietet eine nutzbare Speicherkapazität von 770 TB.
    
* **Data Box-Dienst:** Eine Erweiterung des Azure-Portals, mit der Sie ein Data Box Heavy-Gerät auf einer zentralen Webbenutzeroberfläche verwalten können, auf die an verschiedenen geografischen Standorten zugegriffen werden kann. Verwalten Ihres Data Box Heavy-Geräts mithilfe des Data Box-Diensts. Zu den Aufgaben des Diensts gehören das Erstellen und Verwalten von Aufträgen, das Anzeigen und Verwalten von Warnmeldungen und das Verwalten von Freigaben.  

* **Lokale Webbenutzeroberfläche:** Eine webbasierte Benutzeroberfläche zum Konfigurieren des Geräts für das Herstellen der Verbindung mit dem lokalen Netzwerk und anschließende Registrieren des Geräts beim Data Box-Dienst. Auf der lokalen Webbenutzeroberfläche können Sie das auch Gerät herunterfahren und neu starten, Kopierprotokolle anzeigen und den Microsoft-Support kontaktieren, um eine Serviceanfrage zu stellen.


## <a name="the-workflow"></a>Workflow

Der Workflow umfasst üblicherweise die folgenden Schritte:

1. **Auftrag**: Erstellen Sie im Azure-Portal einen Auftrag, und geben Sie die Versandinformationen und das Azure-Zielspeicherkonto für Ihre Daten an. Wenn das Gerät verfügbar ist, wird es von Azure vorbereitet und mit einer Sendungsverfolgungs-ID versendet.

2. **Empfang**: Nach Erhalt des Geräts verbinden Sie es mit den vorgesehenen Kabeln mit dem Netzwerk und der Stromversorgung. Schalten Sie das Gerät ein, und verbinden Sie sich damit. Konfigurieren Sie die Netzwerk- und Bereitstellungsfreigaben auf dem Hostcomputer, von dem aus Sie die Daten kopieren möchten.

3. **Kopieren von Daten**: Kopieren Sie Daten auf die Data Box Heavy-Freigaben.

4. **Rücksendung**: Bereiten Sie das Gerät vor, schalten Sie es aus, und senden Sie es an das Azure-Rechenzentrum zurück.

5. **Hochladen**: Daten werden automatisch vom Gerät in Azure kopiert. Die Datenträger des Geräts werden gemäß NIST-Richtlinien (National Institute of Standards and Technology) auf sichere Weise gelöscht.

Bei diesem Prozess werden Sie per E-Mail über alle Statusänderungen informiert.

## <a name="region-availability"></a>Regionale Verfügbarkeit

Data Box Heavy kann Daten basierend auf der Region, in der der Dienst bereitgestellt wird, dem Land/der Region, in das/die das Gerät versendet wird, und dem Azure Storage-Zielkonto übertragen, in das Sie die Daten übertragen.

- **Dienstverfügbarkeit**: Data Box Heavy ist in den USA und in der EU verfügbar.


- **Zielspeicherkonten**: Die Speicherkonten, in denen die Daten gespeichert werden, sind in allen Azure-Regionen verfügbar, in denen der Dienst verfügbar ist.

Die aktuellsten Informationen zur regionalen Verfügbarkeit von Data Box Heavy finden Sie in [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

<!--## Sign up

Take the following steps to sign up for Data Box Heavy:

1. [Sign into the Azure portal](https://portal.azure.com).
2. Click **+ Create a resource** to create a new resource. Search for **Azure Data Box**. Select **Azure Data Box** service.
3. Click **Create**.
4. Pick the subscription that you want to use for Data Box Heavy. Select the region where you want to deploy the Data Box Heavy resource. In the **Data Box Heavy** option, click **Sign up**.
5. Answer the questions regarding data residence country/region, time-frame, target Azure service for data transfer, network bandwidth, and data transfer frequency. Review Privacy and terms and select the checkbox against Microsoft can use your email address to contact you.

Once you're signed up, you can order a Data Box Heavy.-->

    
