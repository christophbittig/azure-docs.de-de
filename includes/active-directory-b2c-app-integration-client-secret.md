---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: 80f8f4080b068d840460dfd9acb811174c267d04
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007780"
---
Erstellen Sie einen geheimen Clientschlüssel für die registrierte Webanwendung. Die Webanwendung verwendet den geheimen Clientschlüssel beim Anfordern von Token als Identitätsnachweis.

1. Wählen Sie unter **Verwalten** die Option **Zertifikate und Geheimnisse** aus.
1. Wählen Sie **Neuer geheimer Clientschlüssel**.
1. Geben Sie im Feld **Beschreibung** eine Beschreibung für das Clientgeheimnis ein (z. B. *clientsecret1*).
1. Wählen Sie unter **Läuft ab** einen Zeitraum aus, für den das Geheimnis gültig ist, und wählen Sie dann **Hinzufügen** aus.
1. Notieren Sie den **Wert** des Geheimnisses. Dieser Wert wird in einem späteren Schritt für die Konfiguration verwendet.