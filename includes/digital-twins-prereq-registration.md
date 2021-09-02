---
author: baanders
description: 'Include-Datei für Azure Digital Twins: Voraussetzung zum Einrichten einer App-Registrierung'
ms.service: digital-twins
ms.topic: include
ms.date: 10/29/2020
ms.author: baanders
ms.openlocfilehash: 128aa82762b80958f02cf22e9e7017bb334fde0c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437833"
---
Für die Authentifizierung aller Ressourcen, die in diesem Artikel verwendet werden, müssen Sie eine **[App-Registrierung für Azure Active Directory (Azure AD)](../articles/active-directory/fundamentals/active-directory-whatis.md) einrichten**. Führen Sie die Anweisungen unter [Erstellen einer App-Registrierung](../articles/digital-twins/how-to-create-app-registration-portal.md) aus, um diese einzurichten. 

Wenn Sie über eine App-Registrierung verfügen, benötigen Sie die **Anwendungs-ID (Client)** , **Verzeichnis-ID (Mandant)** und den **geheimen Clientschlüssel** ([Ermittlung über das Azure-Portal](../articles/digital-twins/how-to-create-app-registration-portal.md#collect-important-values)) der Registrierung. Notieren Sie sich diese Werte, damit Sie sie später zum Gewähren des Zugriffs auf die Azure Digital Twins-APIs verwenden können.