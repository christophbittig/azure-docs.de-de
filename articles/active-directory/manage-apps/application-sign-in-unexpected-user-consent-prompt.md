---
title: Unerwartete Aufforderung zur Genehmigung bei der Anmeldung bei einer Anwendung | Microsoft-Dokumentation
description: Informationen zum Behandeln von Problemen, wenn einem Benutzer eine Aufforderung zu einer von Ihnen nicht erwarteten Genehmigung für eine Anwendung angezeigt wird, die Sie in Azure AD integriert haben.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81640356eb60167e8cdefc67b962bf7f38783556
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346644"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Unerwartete Aufforderung zur Genehmigung bei der Anmeldung bei einer Anwendung

Viele Anwendungen, die in Azure Active Directory integriert sind, erfordern Berechtigungen für verschiedene Ressourcen, um ausgeführt werden zu können. Wenn diese Ressourcen auch in Azure Active Directory integriert sind, werden Berechtigungen für den Zugriff darauf häufig mithilfe des allgemeinen Azure AD-Genehmigungsframeworks angefordert.

Dies führt zum Anzeigen einer Aufforderung zur Genehmigung bei der ersten Nutzung der Anwendung, was häufig nur einmal erfolgt.

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Szenarien, in denen Benutzern Aufforderungen zur Genehmigung angezeigt werden

Zusätzliche Aufforderungen können in verschiedenen Szenarien erwartet werden:

* Die Anwendung wurde so konfiguriert, dass eine Zuweisung erforderlich ist. Die Benutzereinwilligung wird derzeit nicht für Apps unterstützt, die eine Zuweisung erfordern. Wenn Sie eine Anwendung so konfigurieren, dass eine Zuweisung erforderlich ist, stellen Sie sicher, dass Sie auch die mandantenweite Administratoreinwilligung gewähren, damit sich der zugewiesene Benutzer anmelden kann.

* Die von der Anwendung angeforderten Berechtigungen haben sich geändert.

* Der Benutzer, der die Anwendung ursprünglich genehmigt hat, war kein Administrator, und jetzt nutzt ein anderer Benutzer (ohne Administratorrechte) die Anwendung zum ersten Mal.

* Der Benutzer, der die Anwendung ursprünglich genehmigt hat, war ein Administrator, der jedoch die Genehmigung nicht im Auftrag der gesamten Organisation erteilt hat.

* Die Anwendung arbeitet mit einer [inkrementellen und dynamischen Genehmigung](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent), um zusätzliche Berechtigungen anzufordern, nachdem die Genehmigung anfänglich erteilt wurde. Dies erfolgt häufig, wenn optionale Features einer Anwendung zusätzliche Berechtigungen benötigen, die über diejenigen hinausgehen, die für die Basisfunktionen erforderlich sind.

* Die Genehmigung wurde aufgehoben, nachdem sie zunächst erteilt wurde.

* Der Entwickler hat die Anwendung so konfiguriert, dass bei jeder Nutzung eine Aufforderung zur Genehmigung angezeigt wird (Hinweis: Dies ist nicht empfehlenswert).

   > [!NOTE]
   > Viele Organisationen haben gemäß den Empfehlungen und bewährten Methoden von Microsoft die Berechtigungen von Benutzern zum Erteilen der Einwilligung für Apps deaktiviert oder eingeschränkt. Wenn eine Anwendung einen Benutzer bei jeder Anmeldung zum Erteilen der Einwilligung zwingt, werden die meisten Benutzer für die Nutzung dieser Anwendung blockiert, selbst wenn ein Administrator eine mandantenweite Administratoreinwilligung erteilt. Wenn Sie eine Anwendung nutzen, die selbst nach Erteilen der Administratoreinwilligung eine Benutzereinwilligung erfordert, kontaktieren Sie den Herausgeber der App, um herauszufinden, ob es eine Einstellung oder Option gibt, mit der das Erzwingen der Benutzereinwilligung bei jeder Anmeldung verhindert werden kann.

## <a name="next-steps"></a>Nächste Schritte

* [Apps, Berechtigungen und Zustimmung in Azure Active Directory (Endpunkt, Version 1.0)](../develop/quickstart-register-app.md)

* [Bereiche, Berechtigungen und Zustimmung im Azure Active Directory (Endpunkt, Version 2.0)](../develop/v2-permissions-and-consent.md)
