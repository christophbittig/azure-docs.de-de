---
title: Hinzufügen einer Vorschauzielgruppe für ein VM-Angebot im Azure Marketplace
description: Hinzufügen einer Vorschauzielgruppe für ein VM-Angebot im Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.reviewer: amhindma
ms.date: 11/12/2021
ms.openlocfilehash: b4ae30a1573534fbf0ead8b913a6a397e9b0cd27
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400484"
---
# <a name="add-a-preview-audience-for-a-virtual-machine-offer"></a>Hinzufügen einer Vorschauzielgruppe für ein VM-Angebot

Konfigurieren Sie auf der Seite Vorschau der Zielgruppe (wählen Sie im linken Navigationsmenü in Partner Center aus), eine eingeschränkte Vorschau-Zielgruppe so, dass sie autorisiert wird, Ihr Angebot zu überprüfen, bevor Sie es für die größere Zielgruppe des kommerziellen Marketplace live veröffentlichen. Wenn Sie Ihr Angebot veröffentlichen, werden den Mitgliedern der von Ihnen angegebenen Vorschau-Zielgruppe Vorschaulinks zur Verfügung gestellt. Nur die Vorschau-Zielgruppe, die Sie konfigurieren, kann auf die Vorschaulinks zugreifen und die Details Ihrer Angebotsvorschau überprüfen, bevor Sie sich bei **Live schalten anmelden.**

Vorschaulinks sind für ausgeblendete Pläne nicht verfügbar. Bei ausgeblendeten Plänen kann die Vorschau-Zielgruppe das Angebot über die Eingabeaufforderung testen.

Ihre Vorschau-Zielgruppe wird durch **Azure-Abonnement-IDs** sowie eine optionale **Beschreibung für** jede Gruppe identifiziert. Keines dieser Felder ist für Kunden sichtbar. Sie finden Ihre Azure-Abonnement-ID auf der Seite **[Abonnement](https://go.microsoft.com/fwlink/?LinkId=2122490)** im Azure-Portal.

Fügen Sie mindestens eine Azure-Abonnement-ID hinzu, entweder einzeln oder durch Hochladen einer CSV-Datei. Auch wenn Ihr Angebot bereits live ist, können Sie die Zielgruppe in der Vorschau ändern, um Änderungen oder Aktualisierungen an Ihrem Angebot zu testen.

> [!IMPORTANT]
> Alle Änderungen, die an der Vorschau-Zielgruppe für Ihr Angebot vorgenommen werden, müssen gespeichert werden und werden erst wirksam, nachdem Sie Ihr Angebot erneut veröffentlicht haben.

> [!NOTE]
> Eine Vorschauzielgruppe unterscheidet sich von einer privaten Zielgruppe. Eine Vorschau-Zielgruppe ist eine Liste der Abonnement-IDs, die Ihr Angebot testen und überprüfen können. Dies schließt alle privaten Pläne ein, bevor sie Ihren Benutzern zur Verfügung gestellt werden. Wenn Sie dagegen ein Angebot privat machen, müssen Sie eine private Zielgruppe angeben, um die Sichtbarkeit Ihres Angebots für Kunden Ihrer Wahl einzuschränken. Eine private Zielgruppe (definiert auf der Seite **Preise und Verfügbarkeit** für jeden Ihrer Pläne) ist eine Liste der Abonnement-IDs und/oder Mandanten-IDs, die nach der Live-Nutzung des Angebots Zugriff auf einen bestimmten Plan haben.

Nachdem Sie die Vorschau-Zielgruppe konfiguriert haben, wählen Sie **Entwurf** speichern aus, bevor Sie mit der nächsten Registerkarte im linken Navigationsmenü **Planübersicht fortfahren.**

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen von Plänen](azure-vm-create-plans.md)
