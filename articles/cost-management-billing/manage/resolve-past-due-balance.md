---
title: E-Mail zu überfälligen Zahlungen von Azure
description: In diesem Artikel wird beschrieben, wie Sie eine Zahlung leisten können, wenn Ihr Azure-Abonnement einen überfälligen Saldo aufweist.
author: bandersmsft
ms.reviewer: lishepar
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/27/2021
ms.author: banders
ms.openlocfilehash: b29689069c16a59c134939a5b8cbf8b6b0df4c98
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464849"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Begleichen überfälliger Zahlungen für Ihr Azure-Abonnement

Dieser Artikel gilt für Kund*innen, die sich online mit einer Kreditkarte bei Azure registriert haben und über ein Abrechnungskonto für das Microsoft Online Services-Programm verfügen, das auch als nutzungsbasierte Zahlung bezeichnet wird. Informationen zum Überprüfen des Abrechnungskontotyps finden Sie [hier](#check-the-type-of-your-account). Falls Sie über ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung verfügen, lesen Sie stattdessen den Artikel [Bezahlen Ihrer Rechnung für Microsoft Azure](../understand/pay-bill.md).

Falls Ihre Zahlung nicht eingegangen ist oder wir die Zahlung nicht verarbeiten können, erhalten Sie eine E-Mail mit der Information, dass die Zahlung für Ihr Abonnement überfällig ist, und im Azure-Portal wird eine entsprechende Warnung angezeigt. Die E-Mail enthält einen Link, über den Sie zur Seite „Saldo ausgleichen“ gelangen.

Wenn Sie standardmäßig per Kreditkarte bezahlen, kann der [Kontoadministrator](add-change-subscription-administrator.md#whoisaa) die ausstehenden Gebühren über das Azure-Portal begleichen. Bei Zahlung auf Rechnung (Scheck/Überweisung) muss die Zahlung an den Empfänger gesendet werden, der unten auf der Rechnung angegeben ist.

> [!IMPORTANT]
> * Wenn Sie über mehrere Abonnements verfügen, für die dieselbe Kreditkarte genutzt wird und die alle überfällige Zahlungen aufweisen, müssen Sie die gesamte ausstehende Summe auf einmal begleichen.
> * Die Kreditkarte, die Sie zum Begleichen der ausstehenden Gebühren verwenden, wird zur neuen Standardzahlungsmethode für alle Abonnements, für die die nicht erfolgreiche Zahlungsmethode verwendet wurde.

## <a name="resolve-past-due-balance-in-the-azure-portal"></a>Begleichen überfälliger Zahlungen im Azure-Portal

1. Melden Sie sich als Kontoadministrator am [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
1. Wählen Sie auf der Seite **Übersicht** das Abonnement mit der überfälligen Zahlung aus.
1. Klicken Sie auf der Seite **Abonnementübersicht** auf das rote Überfälligkeitsbanner, um den Saldo auszugleichen.
    > [!NOTE]
    > Der Saldo kann nur vom Kontoadministrator ausgeglichen werden.
    - Wenn keine Probleme mit Ihrem Konto vorliegen, werden Ihnen keine Banner angezeigt.
    - Wenn für Ihr Konto eine Rechnung zu bezahlen ist, wird Ihnen ein blaues Banner angezeigt, über das Sie zur Seite „Saldo ausgleichen“ gelangen. Sie erhalten außerdem eine E-Mail, in der ein Link zur Seite „Saldo ausgleichen“ vorhanden ist.
    - Wenn Ihr Konto überfällig ist, wird Ihnen ein rotes Banner mit der Aufschrift „Your account is past due“ (Ihr Konto ist überfällig) angezeigt, über das Sie zur Seite „Saldo ausgleichen“ gelangen. Sie erhalten außerdem eine E-Mail, in der ein Link zur Seite „Saldo ausgleichen“ vorhanden ist.
1. Klicken Sie auf der neuen Seite **Saldo ausgleichen** auf **Zahlungsmethode auswählen**.
1. Wählen Sie auf dem neuen Blatt auf der rechten Seite eine Kreditkarte aus der Dropdownliste aus, oder klicken Sie auf den blauen Link **Neue Zahlungsmethode hinzufügen**, um eine neue Karte hinzuzufügen. Diese Kreditkarte wird zur aktiven Methode für alle Abonnements, für die derzeit die nicht erfolgreiche Zahlungsmethode verwendet wird.
     > [!NOTE]
     > * Die Gesamtsumme umfasst die ausstehenden Beträge aller Microsoft-Dienste, für die die fehlgeschlagene Zahlungsmethode genutzt wurde.
     > * Falls die ausgewählte Zahlungsmethode ebenfalls ausstehende Gebühren für Microsoft-Dienste aufweist, wird dies für die Summe der ausstehenden Beträge berücksichtigt. Sie müssen auch diese ausstehenden Gebühren begleichen.
1. Klicken Sie auf **Bezahlen**.

## <a name="troubleshoot-declined-credit-card"></a>Problembehandlung bei Ablehnung der Kreditkarte

Falls die Belastung Ihrer Kreditkarte von Ihrem Finanzinstitut abgelehnt wurde, wenden Sie sich zur Behebung des Problems an das Finanzinstitut. Stellen Sie in Rücksprache mit Ihrer Bank Folgendes sicher:
- Internationale Transaktionen sind für die Karte aktiviert.
- Die Karte verfügt über ein ausreichendes Kreditlimit oder Guthaben, um den Saldo zu begleichen.
- Wiederkehrende Zahlungen sind für die Karte aktiviert.

## <a name="not-getting-billing-email-notifications"></a>Sie erhalten keine E-Mail-Benachrichtigungen zur Abrechnung?

Wenn Sie Kontoadministrator sind, [überprüfen Sie, welche E-Mail-Adresse für Benachrichtigungen verwendet wird](change-azure-account-profile.md). Es wird empfohlen, dass Sie eine E-Mail-Adresse verwenden, die Sie regelmäßig überprüfen. Wenn die E-Mail-Adresse richtig ist, überprüfen Sie Ihren Spam-Ordner.

## <a name="if-i-forget-to-pay-what-happens"></a>Was geschieht, wenn ich eine Zahlung vergesse?

Der Dienst wird gekündigt, und Ihre Ressourcen sind nicht mehr verfügbar. Alle Ihre Azure-Daten werden 90 Tage nach dem Kündigen des Diensts gelöscht. Weitere Informationen finden Sie in [Microsoft Trust Center – Wie wir Ihre Daten verwalten](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

Wenn Sie sicher sind, dass Ihre Zahlung verarbeitet wurde, Ihr Abonnement jedoch immer noch deaktiviert ist, wenden Sie sich an den [Azure-Support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="check-the-type-of-your-account"></a>Überprüfen Sie den Typ Ihres Abrechnungskontos.
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
