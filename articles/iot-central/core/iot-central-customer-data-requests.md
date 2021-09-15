---
title: Features für Kundendatenanforderungen in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel werden das Identifizieren, Löschen und Exportieren von Kundendaten in der Azure IoT Central-Anwendung beschrieben.
author: dominicbetts
ms.author: dobett
ms.date: 08/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 704e5a8624f07e7d52729454a8956256208df6fa
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436030"
---
# <a name="azure-iot-central-customer-data-request-features"></a>Features für Kundendatenanforderungen in Azure IoT Central

Azure IoT Central ist eine vollständig verwaltete „Internet der Dinge“-SaaS-Lösung (IoT, Internet of Things; Software-as-a-Service), mit der Sie Ihre IoT-Ressourcen einfach nach Bedarf vernetzen, überwachen und verwalten, tiefe Einblicke in Ihre IoT-Daten gewinnen und Aktionen auf Basis von Informationen durchführen können.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifizieren von Kundendaten

Mit Azure Active Directory-Objekt-IDs werden Benutzer und Rollen identifiziert. Im Azure IoT Central-Portal werden E-Mail-Adressen von Benutzern für Rollenzuweisungen angezeigt, aber nur die Azure Active Directory-Objekt-ID gespeichert; die E-Mail-Adresse wird dynamisch von Azure Active Directory abgefragt. Azure IoT Central-Administratoren können Anwendungsbenutzer im Benutzerverwaltungsabschnitt einer Azure IoT Central-Anwendung anzeigen, exportieren und löschen.

In der Anwendung können E-Mail-Adressen konfiguriert werden, um Warnungen zu erhalten. In diesem Fall werden E-Mail-Adressen innerhalb von IoT Central gespeichert und müssen auf der Verwaltungsseite des In-App-Kontos verwaltet werden.

Bezüglich Geräten behält Microsoft keine Informationen und hat keinen Zugriff auf Daten, die eine Korrelation zwischen Gerät und Benutzer zulassen würden. Viele der in Azure IoT Central verwalteten Geräte sind keine persönlichen Geräte, z.B. ein Verkaufsautomat oder eine Kaffeemaschine. Kunden können jedoch einige Geräte als persönlich identifizierbar betrachten und nach eigenem Ermessen ihre eigenen Asset- oder Bestandsnachverfolgungssysteme anwenden, die Geräte an Personen binden. Azure IoT Central verwaltet und speichert alle Daten, die mit Geräten verknüpft sind, so, als wären es persönliche Daten.

Wenn Sie Microsoft Enterprise Services verwenden, generiert Microsoft einige Informationen, die als vom System generierte Protokolle bezeichnet werden. Diese Protokolle stellen faktische Aktionen dar, die innerhalb der Dienst- und Diagnosedaten einzelner Geräte ausgeführt werden, und sind nicht auf Benutzeraktivität bezogen. Auf vom System generierte Azure IoT Central-Protokolle können Anwendungsadministratoren weder zugreifen noch diese exportieren.

## <a name="deleting-customer-data"></a>Löschen von Kundendaten

Die Fähigkeit zum Löschen von Benutzerdaten wird nur über die Verwaltungsseite von IoT Central bereitgestellt. Anwendungsadministratoren können den zu löschenden Benutzer und in der oberen rechten Ecke der Anwendung **Löschen** auswählen, um den Datensatz zu löschen. Anwendungsadministratoren können auch einzelne Konten entfernen, die nicht mehr der fraglichen Anwendung zugeordnet sind.

Nachdem ein Benutzer gelöscht wurde, werden ihm keine weiteren Warnungen mehr per E-Mail gesendet. Seine E-Mail-Adresse muss jedoch einzeln aus jeder konfigurierten Warnung entfernt werden.

## <a name="exporting-customer-data"></a>Exportieren von Kundendaten

Die Fähigkeit zum Löschen von Exportdaten wird nur über die Verwaltungsseite von IoT Central bereitgestellt. Kundendaten, einschließlich der zugewiesenen Rollen, können ausgewählt, kopiert und von einem Anwendungsadministrator eingefügt werden.

## <a name="links-to-additional-documentation"></a>Links zu zusätzlicher Dokumentation

Weitere Informationen zu Kontoverwaltung einschließlich Rollendefinitionen finden Sie unter [Verwalten Ihrer Anwendung](howto-administer.md).
