---
title: E-Mail zu überfälligen Zahlungen von Azure
description: Informationen zur Zahlung, wenn das Azure-Abonnement einen überfälligen Saldo aufweist.
author: genlin
ms.reviewer: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: a61eaa10420a9a4a413074559a158e2a32f5cf95
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634993"
---
# <a name="resolve-past-due-balance-for-your-azure-subscription"></a>Begleichen überfälliger Zahlungen für Ihr Azure-Abonnement

Dieser Artikel gilt für Kunden, die sich online mit einer Kreditkarte für Azure registriert haben und über ein Abrechnungskonto für das Microsoft Online Services-Programm verfügen. Informationen zum Überprüfen des Abrechnungskontotyps finden Sie [hier](#check-the-type-of-your-account). Falls Sie über ein Abrechnungskonto für eine Microsoft-Kundenvereinbarung verfügen, lesen Sie stattdessen den Artikel [Bezahlen Ihrer Rechnung für Microsoft Azure](../understand/pay-bill.md).

Falls Ihre Zahlung nicht eingegangen ist oder wir die Zahlung nicht verarbeiten können, erhalten Sie eine E-Mail mit der Information, dass die Zahlung für Ihr Abonnement überfällig ist, und im Azure-Portal wird eine entsprechende Warnung angezeigt. Wenn Sie standardmäßig per Kreditkarte bezahlen, kann der [Kontoadministrator](../understand/subscription-transfer.md#whoisaa) die ausstehenden Gebühren über das Azure-Portal begleichen. Bei Zahlung auf Rechnung (Scheck/Überweisung) muss die Zahlung an den Empfänger gesendet werden, der unten auf der Rechnung angegeben ist.

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
1. Klicken Sie auf der neuen Seite **Saldo ausgleichen** auf **Zahlungsmethode auswählen**.
    ![Screenshot: Link zum Auswählen der Zahlungsmethode](./media/resolve-past-due-balance/settle-balance-screen.png)

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
