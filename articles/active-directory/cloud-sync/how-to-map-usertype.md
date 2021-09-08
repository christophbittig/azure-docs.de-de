---
title: Verwenden der Zuordnung von UserType mit Azure AD Connect-Cloudsynchronisierung
description: In diesem Artikel wird beschrieben, wie das UserType-Attribut mit Cloudsynchronisierung zugeordnet wird.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/04/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b06381472549fd7d9b3f3b5dcd222fcd96f4f15
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506203"
---
# <a name="map-usertype-with-cloud-sync"></a>Zuordnen von UserType mit Cloudsynchronisierung

Cloudsynchronisierung unterstützt die Synchronisierung des **UserType**-Attributs für User-Objekte.

Standardmäßig ist das **UserType**-Attribut nicht für die Synchronisierung aktiviert, da kein entsprechendes **UserType**-Attribut im lokalen Active Directory vorhanden ist. Sie müssen diese Zuordnung für die Synchronisierung manuell hinzufügen. Bevor Sie diesen Schritt ausführen, müssen Sie das folgende von Azure Active Directory (Azure AD) erzwungene Verhalten beachten:

- Azure AD akzeptiert nur zwei Werte für das **UserType**-Attribut: „Member“ und „Guest“.
- Wenn das **UserType**-Attribut in der Cloudsynchronisierung nicht zugeordnet ist, ist das **UserType**-Attribut von Azure AD-Benutzern, die über die Verzeichnissynchronisierung erstellt wurden, auf „Member“ festgelegt.

Vor dem Hinzufügen einer Zuordnung für das **UserType**-Attribut müssen Sie zunächst entscheiden, wie das Attribut vom lokalen Active Directory abgeleitet wird. Folgende Vorgehensweisen sind am gängigsten:

 - Legen Sie ein nicht verwendetes lokales Active Directory-Attribut wie extensionAttribute1 als Quellattribut fest. Das festgelegte lokale Active Directory-Attribut sollte vom Typ Zeichenfolge und einwertig sein und den Wert „Member“ oder „Guest“ enthalten.
 - Wenn Sie sich für diesen Ansatz entscheiden, müssen Sie vor dem Aktivieren der Synchronisierung des **UserType**-Attributs sicherstellen, dass das ausgewählte Attribut mit dem richtigen Wert für alle vorhandenen Benutzerobjekte im lokalen Active Directory, die mit Azure AD synchronisiert werden, aufgefüllt wird.

## <a name="add-the-usertype-mapping"></a>Hinzufügen der UserType-Zuordnung
So fügen Sie die **UserType**-Zuordnung hinzu:

 1. Wählen Sie im Azure-Portal die Option **Azure Active Directory** aus.
 1. Wählen Sie **Azure AD Connect** aus.
 1. Wählen Sie **Cloudsynchronisierung verwalten** aus.
 1. Wählen Sie unter **Konfiguration** Ihre Konfiguration aus.
 1. Wählen Sie unter **Attribute verwalten** die Option **Zum Bearbeiten von Zuordnungen klicken**.
 
    ![Screenshot: Bearbeiten der Attributzuordnungen.](media/how-to-map-usertype/usertype-1.png) 

 1. Wählen Sie **Attributzuordnung hinzufügen** aus.
 
    ![Screenshot: Hinzufügen einer neuen Attributzuordnung.](media/how-to-map-usertype/usertype-2.png) 
1. Wählen Sie den Zuordnungstyp aus. Sie können die Zuordnung auf eine von drei Arten vornehmen:
   - Eine direkte Zuordnung, z. B. aus einem Active Directory-Attribut
   - Ein Ausdruck wie (IIF(InStr([userPrincipalName], "@partners") > 0,"Guest","Member"))
   - Eine Konstante, die z. B. alle Benutzerobjekte zu „Guest“ macht
 
     ![Screenshot: Hinzufügens eines UserType-Attributs.](media/how-to-map-usertype/usertype-3.png)

1. Wählen Sie im Dropdownfeld **Zielattribut** die Option **UserType** aus.
1. Wählen Sie unten auf der Seite **Anwenden** aus, um eine Zuordnung für das Azure AD-Attribut **UserType** zu erstellen.

## <a name="next-steps"></a>Nächste Schritte 

- [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](reference-expressions.md)
- [Konfiguration der Cloudsynchronisierung](how-to-configure.md)
