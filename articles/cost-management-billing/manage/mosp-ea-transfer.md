---
title: Übertragen eines Azure-Abonnements an ein Enterprise Agreement
description: In diesem Artikel werden die Schritte zum Übertragen eines Abonnements mit Microsoft-Kundenvereinbarung oder MOSP-Abonnements an ein Enterprise Agreement erläutert.
author: bandersmsft
ms.reviewer: jatracey
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2021
ms.author: banders
ms.custom: ''
ms.openlocfilehash: 4350a36c63e8ea6be4284baeaba3a60d1de9099b
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007328"
---
# <a name="transfer-an-azure-subscription-to-an-enterprise-agreement-ea"></a>Übertragen eines Azure-Abonnements an ein Enterprise Agreement (EA)

In diesem Artikel werden die erforderlichen Schritte zum Übertragen eines Abonnements mit Microsoft-Kundenvereinbarung oder MOSP-Abonnements (Abonnements mit nutzungsbasierter Bezahlung) an ein EA erläutert. Bei der Übertragung kommt es zu keinen Ausfallzeiten, doch müssen viele Schritte ausgeführt werden, um die Übertragung zu ermöglichen.

> [!NOTE]
> Durch den Übertragungsvorgang werden keine Azure AD-Verzeichnisinformationen geändert, mit denen die Abonnements verknüpft sind. Wenn Sie eine Azure AD-Verzeichnisänderung vornehmen möchten, finden Sie Informationen dazu unter [Übertragen eines Azure-Abonnements in ein anderes Azure AD-Verzeichnis](../../role-based-access-control/transfer-subscription.md).

## <a name="prerequisites"></a>Voraussetzungen

In den folgenden Abschnitten erfahren Sie, wie Sie den Abonnementkontoadministrator ermitteln, den erforderlichen Zugriff auf das Ziel-EA erhalten und die EA-Authentifizierungsebene festlegen.

### <a name="identify-the-subscription-account-admin"></a>Ermitteln des Abonnementkontoadministrators

Sie können den Abonnementkontoadministrator ermitteln, indem Sie sich beim Azure-Portal mit einem Konto anmelden, das mindestens über die Azure RBAC-Rolle „Leser“ für das Abonnement verfügt. Navigieren Sie dann zu **Abonnements**, und wählen Sie das Abonnement und anschließend **Eigenschaften** aus. Der **Kontoadministrator** des Abonnements wird angezeigt. Notieren Sie sich die Benutzerinformationen.

:::image type="content" source="./media/mosp-ea-transfer/subscription-account-admin.png" alt-text="Abbildung der Abonnementeigenschaften, unter denen der Kontoadministrator angezeigt wird" lightbox="./media/mosp-ea-transfer/subscription-account-admin.png" :::

> [!NOTE]
> Sie müssen sich später beim Enterprise Agreement-Portal (EA-Portal) mit demselben Benutzerkonto für den Kontoadministrator anmelden, wie zuvor beschrieben. Wenn Sie keinen Zugriff auf das Konto haben, müssen Sie zuerst Zugriff erhalten, bevor Sie fortfahren. Weitere Informationen finden Sie unter [Übertragen des Abrechnungsbesitzes eines Azure-Abonnements an ein anderes Konto](billing-subscription-transfer.md).

### <a name="access-to-the-destination-ea"></a>Zugriff auf das Ziel-EA

Sie benötigen Zugriff auf das Ziel-EA, um einen EA-Kontobesitzer zu erstellen. Eine vorhandene oder neue Abteilung ist erforderlich. In der Abteilung wird das Abonnement bei seiner Übertragung abgelegt. Der EA-Kontobesitzer ist Besitzer des Abonnements, das in der Abteilung abgelegt wird.

Sie müssen über eine der folgenden Rollen verfügen, um einen EA-Kontobesitzer zu erstellen. Weitere Informationen über EA-Rollen finden Sie unter [Verwalten von Azure Enterprise Agreement-Rollen](understand-ea-roles.md).

- Unternehmensadministrator
- Abteilungsadministrator

### <a name="set-ea-authentication-level"></a>Festlegen der EA-Authentifizierungsebene

Für EAs wird eine Authentifizierungsebene festgelegt, die bestimmt, welche Benutzertypen als EA-Kontobesitzer für die Registrierung hinzugefügt werden können. Es sind vier Authentifizierungsebenen verfügbar, wie es unter [Arten von Authentifizierungsebenen](ea-portal-troubleshoot.md#authentication-level-types) beschrieben ist.

Stellen Sie sicher, dass Sie mit der für das EA festgelegten Authentifizierungsebene einen neuen EA-Kontobesitzer über den zuvor notierten Abonnementkontoadministrator erstellen können. Zum Beispiel:

- Wenn der Abonnementkontoadministrator über die E-Mail-Adressdomäne `@outlook.com` verfügt, muss die Authentifizierungsebene des EA entweder auf **Nur Microsoft-Konto** oder **Mischkonto** festgelegt sein.
- Wenn der Abonnementkontoadministrator über die E-Mail-Adressdomäne `@<YourAzureADTenantPrimaryDomain.com>` verfügt, muss die Authentifizierungsebene des EA entweder auf **Geschäfts-, Schul- oder Unikonto** oder **Geschäfts- oder Schulkonto – Mandanten-übergreifend** festgelegt sein. Die Möglichkeit zum Erstellen eines neuen EA-Kontobesitzers hängt davon ab, ob die Standarddomäne des EA mit der E-Mail-Adressdomäne des Abonnementkontoadministrators identisch ist.

> [!NOTE]
> Bei korrekter Festlegung hat eine Änderung der Authentifizierungsebene keine Auswirkung auf den Übertragungsvorgang. Weitere Informationen finden Sie unter [Arten von Authentifizierungsebenen](ea-portal-troubleshoot.md#authentication-level-types).

## <a name="transfer-the-subscription-to-the-ea"></a>Übertragen des Abonnements an das EA

Nachdem Sie alle erforderlichen [Voraussetzungen](#prerequisites) überprüft und erfüllt haben, können Sie mit der Übertragung des Abonnements an das EA beginnen.

### <a name="create-an-ea-account-owner"></a>Erstellen eines EA-Kontobesitzers

Wie im Abschnitt [Voraussetzungen](#access-to-the-destination-ea) beschrieben, müssen Sie einen EA-Kontobesitzer als Teil einer vorhandenen oder neuen EA-Abteilung erstellen.

1. Melden Sie sich beim EA-Portal unter https://ea.azure.com entweder als Unternehmens- oder Abteilungsadministrator an.
1. Informationen zum Erstellen einer neuen Abteilung finden Sie unter [Erstellen einer Azure Enterprise-Abteilung](ea-portal-administration.md#create-an-azure-enterprise-department).
1. Informationen zum Erstellen des EA-Kontobesitzers finden Sie unter [Hinzufügen eines Kontos](ea-portal-administration.md#add-an-account). Wenn Sie zur Eingabe der **E-Mail-Adresse des Kontobesitzers** aufgefordert werden, geben Sie die E-Mail-Adresse des **Kontoadministrators** für das Abonnement ein, den Sie zuvor ermittelt haben.
    > [!NOTE]
    > Wenn die EA-Authentifizierungsebene falsch festgelegt ist, schlägt die Erstellung des EA-Kontobesitzers fehl, und im EA-Portal wird ein Fehler angezeigt.

Nachdem der EA-Kontobesitzer erstellt wurde, erhält der Abonnementkontoadministrator, der jetzt auch ein EA-Kontobesitzer ist, eine E-Mail vom Azure EA-Portal. Darin wird der Benutzer darüber informiert, dass er jetzt ein EA-Kontobesitzer ist. Wenn der Benutzer keinen Zugriff auf ein E-Mail-Postfach hat, das dem angegebenen Konto zugeordnet ist, müssen Sie sich keine Gedanken machen. Die E-Mail ist nur eine Benachrichtigung. Die Informationen in der E-Mail sind nicht erforderlich, um fortzufahren. Ein E-Mail-Postfach wird jedoch für zukünftige Benachrichtigungen über das Abonnement empfohlen.

### <a name="complete-the-subscription-transfer"></a>Abschließen der Abonnementübertragung

Da der Abonnementkontoadministrator nun auch EA-Kontobesitzer ist, kann er Abonnements unter dem EA erstellen.

> [!IMPORTANT]
> Bevor Sie fortfahren, müssen Sie verstehen, was geschieht, wenn sich ein neuer EA-Kontobesitzer zum ersten Mal beim EA-Portal anmeldet. Lesen Sie die folgenden Informationen, bevor Sie sich als Abonnementkontoadministrator beim EA-Portal anmelden.

Wenn sich ein neuer EA-Kontobesitzer zum ersten Mal beim EA-Portal anmeldet, wird die folgende Warnung angezeigt:

```
WARNING

You are about to associate your account (email address) to the following enrollment:

Enrollment Name: <EnrollmentName>
Enrollment Number: <EnrollmentNumber>

All Enrollment Administrators can gain access to all of your subscriptions if you proceed.
Additionally, all Azure subscriptions for which you are the account owner will be converted to your Enterprise Agreement.
This includes subscriptions which include a monthly credit (e.g. Visual Studio, MPN, BizSpart, etc.) meaning you will lose the monthly credit by proceeding.
All subscriptions based on a Visual Studio subscriber offer (monthly credit for Visual Studio subscribers or Pay-As-You-Go Dev/Test) will be converted to use the Enterprise Dev/Test usage rates and be billed against this enrollment from today onwards.
If you wish to retain the monthly credits currently associated with any of your subscriptions, please cancel.
Please see additional details.

Cancel  Continue
```

Die Warnung besagt Folgendes, wie unter [Azure EA-Portalverwaltung](ea-portal-administration.md#enterprise-devtest-offer) dokumentiert:

***Wenn ein Benutzer über das Azure EA-Portal als Kontobesitzer hinzugefügt wird, werden alle dem Kontobesitzer zugeordneten Azure-Abonnements, die entweder auf dem MOSP (PAYG)-Dev/Test-Angebot oder auf den Angeboten mit monatlichem Guthaben für Visual Studio-Abonnenten basieren, in das EA Dev/Test-Angebot konvertiert. Abonnements, die auf anderen Angebotstypen wie MOSP (PAYG) basieren und dem Kontobesitzer zugeordnet sind, werden in das EA-Standardabonnementangebot konvertiert.***

Wenn der Benutzer die Folgen der Warnung verstanden hat, wählen Sie **Weiter** aus, und die seinem Konto zugeordneten Abonnements werden an das EA übertragen.

## <a name="more-help"></a>Weitere Hilfe

Mithilfe der oben genannten Informationen sollten Sie das Abonnement an das EA übertragen haben. Wenn Sie weitere Hilfe benötigen, stellen Sie eine [Anfrage an den Abrechnungssupport](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-step"></a>Nächster Schritt

- [Erste Schritte mit dem Azure Enterprise Portal](ea-portal-get-started.md)
