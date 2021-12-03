---
title: Registrierung für Preise für benutzerspezifischen Zugriff für Azure Virtual Desktop – Azure
description: So registrieren Sie sich für Preise für benutzerspezifischen Zugriff für Azure Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 67e16d2cf0a9ddbc7b4447b22207c908be4a3ac9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131036654"
---
# <a name="enroll-your-subscription-in-per-user-access-pricing"></a>Registrieren Ihres Abonnements für Preise für den Zugriff auf Benutzerebene

Bevor externe Benutzer eine Verbindung mit Ihrer Bereitstellung herstellen können, müssen Sie Ihr Abonnement für die Preise für benutzerspezifischen Zugriff registrieren. Die Preise für den Zugriff pro Nutzer berechtigen Nutzer außerhalb Ihrer Organisation, mithilfe von Identitäten, die Sie bereitstellen und verwalten, auf Apps und Desktops in Ihrem Abonnement zu zugreifen. Ihr registriertes Abonnement wird monatlich basierend auf der Anzahl unterschiedlicher Benutzer abgerechnet, die eine Verbindung mit Azure Virtual Desktop-Ressourcen herstellen.

>[!NOTE]
>Achten Sie darauf, externe *Nutzer* nicht mit externen *Identitäten* zu verwechseln. Azure Virtual Desktop unterstützt derzeit keine externen Identitäten, einschließlich Gastzugänge und B2B-Identitäten (Business-to-Business). Unabhängig davon, ob Sie interne oder externe Nutzer mit dem Azure Virtual Desktop bedienen, müssen Sie Identitäten für diese Nutzer selbst erstellen und verwalten. Die Preise für den Zugriff pro Nutzer sind keine Möglichkeit, Gastnutzerkonten mit dem Azure Virtual Desktop zu aktivieren. Für weitere Informationen, siehe [Grundlegendes zu Lizenzen und zu den Preisen für den Zugriff pro Nutzer](licensing.md).

## <a name="how-to-enroll"></a>Registrierung

So registrieren Sie Ihr Azure-Abonnement für Preise für benutzerspezifischen Zugriff:

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.

2. Geben Sie **Azure Virtual Desktop** in die Suchleiste ein, und wählen Sie dann unter „Dienste“ den Eintrag **Azure Virtual Desktop** aus.

3. Wählen Sie auf der Übersichtsseite für **Azure Virtual Desktop** die Option **Preise für benutzerspezifischen Zugriff** aus.

4. Wählen Sie in der Liste der Abonnements das Abonnement aus, in dem Sie Azure Virtual Desktop-Ressourcen bereitstellen möchten.

5. Wählen Sie **Registrieren** aus.

6. Lesen Sie die Produktbedingungen, und wählen Sie dann **Registrieren** aus, um mit der Registrierung zu beginnen. Es kann bis zu eine Stunde dauern, bis der Registrierungsprozess abgeschlossen ist.

7. Überprüfen Sie nach Abschluss der Registrierung den Wert in der Spalte **Preise für den benutzerspezifischen Zugriff** der Abonnementliste, der jetzt von „Registrieren“ in „Registriert“ geändert sein sollte.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Preisen für benutzerspezifischen Zugriff finden Sie unter [Grundlegendes zu Lizenzen und zu den Preisen für benutzerspezifischen Zugriff](licensing.md). Wenn Sie erfahren möchten, wie Sie die Streamingkosten pro Benutzer-App für Ihre Bereitstellung schätzen, finden Sie weitere Informationen unter [Schätzen der Kosten für das App-Streaming pro Benutzer für Azure Virtual Desktop](streaming-costs.md). Weitere Informationen zum Schätzen der Gesamtkosten der Bereitstellung finden Sie unter [Grundlegendes zu den Gesamtkosten für die Bereitstellung von Azure Virtual Desktop](total-costs.md).
