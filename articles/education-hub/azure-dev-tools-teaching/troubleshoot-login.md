---
title: Beheben eines Anmeldefehlers für Azure Dev Tools für den Unterricht
description: Beschreibt die Aktionen, die ein Kursteilnehmer durchführen sollte, wenn er eine Fehlermeldung erhält, wenn er sich bei Azure Dev Tools für den Unterricht anmeldet.
author: rymend
ms.author: rymend
ms.topic: troubleshooting
ms.service: azure-education
ms.subservice: education-hub
ms.date: 06/30/2020
ms.openlocfilehash: 937a321a0edb51f0bab9168706977d9b798429e8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339685"
---
# <a name="troubleshooting-student-login-issues"></a>Behandeln von Problemen bei der Kursteilnehmeranmeldung
Für den Zugriff auf Azure Dev Tools für den Unterricht muss ein Benutzer über ein Microsoft-Konto (MSA) verfügen. Kursteilnehmer werden automatisch angewiesen, ein Microsoft-Konto zu erstellen, wenn Ihr Konto nicht bereits ein MSA ist oder mit einem MSA verknüpft ist. Wenn Ihre Domäne Active Directory zugeordnet ist, gelten alle Konten unter dieser Domäne bereits als MSA.

Wenn ein Kursteilnehmer versucht, sich anzumelden, und die folgende Fehlermeldung empfängt, verwenden Sie eine der unten beschriebenen Lösungen.

:::image type="content" source="media/troubleshoot-login/error-login-message.png" alt-text="Fehlermeldung bei der Anmeldung." border="false":::

Es gibt zwei Lösungen: Erstellen Sie ein neues Microsoft-Konto, oder verwenden Sie ein vorhandenes Microsoft-Konto.

## <a name="create-a-new-microsoft-account"></a>Erstellen eines neuen Microsoft-Kontos
### <a name="use-a-university-email-address"></a>Verwenden einer E-Mail-Adresse der Universität
Wenn Sie sich mit einer E-Mail-Adresse der Universität anmelden (z. B. `John.Smith@university.edu`), müssen Sie ein Microsoft-Konto mit dieser E-Mail-Adresse erstellen. Das Erstellen eines Kontos ist kostenlos und dauert nur wenige Minuten. Wenn Sie über ein Microsoft-Konto verfügen, können Sie sich automatisch mit einem einzigen Benutzernamen und Kennwort bei allen Ihren Microsoft-Produkten anmelden.

### <a name="use-a-personal-email-address"></a>Verwenden einer persönlichen E-Mail-Adresse
Wenn Sie sich mit einer privaten E-Mail-Adresse (z. B. `John.Smith@email.com`) anmelden, aber auch über eine E-Mail-Adresse der Universität verfügen, verwenden Sie die E-Mail-Adresse der Universität. Wenn Sie sich zuvor mit einer persönlichen E-Mail-Adresse beim Webstore Ihrer Einrichtung angemeldet haben oder keine E-Mail-Adresse der Universität besitzen, müssen Sie ein Microsoft-Konto mit Ihrer persönlichen E-Mail-Adresse erstellen oder verknüpfen.

## <a name="use-an-existing-microsoft-account"></a>Verwenden eines vorhandenen Microsoft-Kontos
Wenn ein Kursteilnehmer über ein vorhandenes Microsoft-Konto (z. B. Xbox) verfügt, kann er dieses Konto mit einem Azure Dev Tools-Konto verbinden.

1. Gehe zu https://account.microsoft.com.
1. Melden Sie sich mit den Anmeldeinformationen Ihres Microsoft-Kontos an.
1. Wählen Sie im oberen Menü des Menübands die Option **Ihre Infos** aus.

1. Klicken Sie auf **Anmeldung bei Microsoft verwalten**. Sie werden aufgefordert, Ihre Identität zu bestätigen. Sie erhalten per E-Mail einen Sicherheitscode.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in.png" alt-text="Verwalten der Anmeldung." border="false":::

1. Geben Sie den Sicherheitscode ein, den Sie per E-Mail erhalten haben.

    :::image type="content" source="media/troubleshoot-accounts/sign-in-enter-code.png" alt-text="Eingeben des Anmeldecodes." border="false":::

1. Klicken Sie auf **E-Mail hinzufügen** für Ihr Konto, und geben Sie die E-Mail-Adresse Ihrer Universität ein.
Wenn Sie sich das nächste Mal anmelden, können Sie die E-Mail-Adresse Ihrer Universität verwenden, um auf Azure Dev Tools für den Unterricht zuzugreifen.

    :::image type="content" source="media/troubleshoot-accounts/manage-sign-in-add-email.png" alt-text="Anmeldung bei Microsoft verwalten." border="false":::

## <a name="next-steps"></a>Nächste Schritte
- [Häufig gestellte Fragen](program-faq.yml)

- [Supportoptionen](program-support.md)
