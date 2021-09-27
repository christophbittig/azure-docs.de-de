---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 023f3413fbd7b642300de19ad479029c94e7c4d5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "128700123"
---
## <a name="setting-up"></a>Einrichten

### <a name="creating-the-visual-studio-project"></a>Erstellen des Visual Studio-Projekts

Erstellen Sie in Visual Studio 2019 ein neues `Blank App (Universal Windows)`-Projekt. Nach der Eingabe des Projektnamens können Sie ein beliebiges Windows SDK höher als `10.0.17134` auswählen. 

### <a name="install-the-package-and-dependencies-with-nuget-package-manager"></a>Installieren des Pakets und der Abhängigkeiten mit NuGet-Paket-Manager

Die Calling SDK-APIs und -Bibliotheken sind über ein NuGet-Paket öffentlich verfügbar.
In den folgenden Schritten wird veranschaulicht, wie Sie das Calling SDK-NuGet-Paket suchen, herunterladen und installieren.

1. Öffnen des NuGet-Paket-Managers (`Tools` -> `NuGet Package Manager` -> `Manage NuGet Packages for Solution`)
2. Klicken Sie auf `Browse`, und geben Sie dann `Azure.Communication.Calling` in das Suchfeld ein.
3. Stellen Sie sicher, dass das Kontrollkästchen `Include prerelease` aktiviert ist.
4. Klicken Sie auf das `Azure.Communication.Calling`-Paket.
5. Aktivieren Sie auf der rechten Registerkarte das Kontrollkästchen, das dem CS-Projekt entspricht.
6. Klicken Sie auf die Schaltfläche `Install`.