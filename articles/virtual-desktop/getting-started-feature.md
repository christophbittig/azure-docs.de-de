---
title: 'Feature „Erste Schritte“ zum Bereitstellen von Azure Virtual Desktop: Azure'
description: Eine Schnellstartanleitung zum schnellen Einrichten von Azure Virtual Desktop mit dem Feature „Erste Schritte“ aus dem Azure-Portal.
author: Heidilohr
ms.topic: quickstart
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 62ba9a8fa578ab12408160d9a6d19243474921fc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277218"
---
# <a name="deploy-azure-virtual-desktop-with-the-getting-started-feature"></a>Feature „Erste Schritte“ zum Bereitstellen von Azure Virtual Desktop

Das neue Azure-Portal-Feature „Erste Schritte“ ist eine schnelle und einfache Möglichkeit, Azure Virtual Desktop in Ihrer Bereitstellung zu installieren und konfigurieren.

## <a name="requirements"></a>Anforderungen

Sie benötigen die folgenden erforderlichen Komponenten, um die ersten Schritte ausführen zu können:

- Einen Azure Active Directory-Mandanten (AD)
- Ein Konto mit globalen Administratorberechtigungen für Azure AD.

   >[!NOTE]
   >Das Feature „Erste Schritte“ unterstützt derzeit keine MSA-, B2B- oder Gastkonten.

- Ein aktives Azure-Abonnement.

   >[!NOTE]
   >Das Feature „Erste Schritte“ unterstützt derzeit keine Konten mit mehrstufiger Authentifizierung.

- Ein Konto mit **Besitzerberechtigungen** für das Abonnement.

Wenn Sie das Feature „Erste Schritte“ in einer Umgebung mit Active Directory Domain Services (AD DS) verwenden, müssen Sie auch die folgenden Anforderungen erfüllen:

- Anmeldeinformationen für AD DS-Domänenadministratoren.
- Sie müssen Azure AD Connect in Ihrem Abonnement konfigurieren und sicherstellen, dass der Container „USERS“ mit Azure AD synchronisiert ist.
- Der Domänencontroller auf Ihrem virtuellen Computer (VM) darf keine DSC-Erweiterungen des Typs **Microsoft.Powershell.DSC** aufweisen.

Wenn Sie das Feature „Erste Schritte“ in einer Umgebung ohne Identitätsanbieter verwenden, sollten Sie die folgenden zusätzlichen Anforderungen erfüllen:

- Ihr UPN für den AD-Domänenbeitritt darf keine Schlüsselwörter enthalten, [die in der Richtlinienliste für Benutzernamen nicht zulässig sind](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-), und Sie müssen einen eindeutigen Benutzernamen verwenden, der nicht bereits in Ihrem Azure AD-Abonnement vorhanden ist.
- Sie müssen einen neuen Hostpool erstellen, um Sitzungshosts hinzuzufügen, die Sie mit dem Feature „Erste Schritte“ erstellen. Wenn Sie versuchen, einen Sitzungshost in einem vorhandenen Hostpool zu erstellen, wird dies nicht funktionieren.

## <a name="for-subscriptions-with-azure-ad-ds-or-ad-ds"></a>Für Abonnements mit Azure AD DS oder AD DS

Hier erfahren Sie, wie Sie das Feature „Erste Schritte“ in einem Abonnement verwenden, das bereits über Azure AD DS oder AD DS verfügt:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Melden Sie sich bei Azure an, öffnen Sie die **Azure Virtual Desktop-Verwaltung**, und wählen Sie dann die Registerkarte **Erste Schritte** aus. Damit wird die Landing Page für das Feature „Erste Schritte“ geöffnet.

3. Klicken Sie auf **Erstellen**.

4. Wählen Sie auf der Registerkarte **Basic** die folgenden Werte aus:

    - Wechseln Sie für **Abonnement** zu **Wie ist Ihr Abonnement konfiguriert?** , und wählen Sie **Vorhandenes Setup** aus.

    - Wählen Sie unter **Speicherort** den Speicherort aus, an dem Sie Ihre Ressourcen bereitstellen möchten.

    - Geben Sie unter **Azure-Administrator-UPN** den vollständigen Benutzerprinzipalnamen (UPN) des Kontos mit Administratorberechtigungen in Azure AD und Besitzerberechtigungen in dem Abonnement ein, das Sie verwenden möchten.

    - Geben Sie für **UPN für AD-Domänenbeitritt** den vollständigen UPN des Kontos mit Berechtigungen ein, den Sie zum Hinzufügen der VMs zu Ihrer Domäne verwenden möchten.

    - Wählen Sie für **Identität** abhängig von Ihrer Umgebung entweder **Azure AD DS** oder **AD DS** aus. Was Sie hier auswählen, wirkt sich auf die Eingabe aus, die Ihre VMs benötigen.

5. Wählen Sie auf der Registerkarte **Virtuelle Computer** folgende Werte aus:

    - Wählen Sie für **Möchten Sie, dass die Benutzer diesen Computer freigeben?** je nach Ihren Anforderungen eine der folgenden Optionen aus:
      - Wenn Sie einen Einzelsitzungs- oder persönlichen Hostpool erstellen möchten, wählen Sie **Nein** aus.
      - Wenn Sie einen Hostpool mit mehreren Sitzungen oder gepoolten Hostpool erstellen möchten, wählen Sie **Ja (mehrere Sitzungen)** aus. Dadurch wird auch ein Azure Files-Speicherkonto erstellt, das entweder Azure AD DS oder AD DS hinzugefügt wird.

    - Wählen Sie unter **Imagetyp** ein Image aus dem Azure-Imagekatalog, ein benutzerdefiniertes Image oder eine VHD aus einem Speicherblob aus.

    - Wählen Sie für **VM-Größe** die Größe und SKU aus, die Sie für die bereitzustellenden VMs wünschen.

    - Wählen Sie unter **Anzahl der VMs** aus, wie viele VMs Sie im Hostpool bereitstellen möchten.

    - Wenn Sie ein vorhandenes Setup mit AD DS verwenden, werden die folgenden Optionen angezeigt:

       - Wählen Sie für **Subnetz** ein Subnetz im VNET aus. Das von Ihnen gewählte Subnetz muss sich entweder am gleichen Speicherort wie die Identität (AD DS oder Azure AD DS) befinden oder per Peering damit verbunden sein.

       - Wählen Sie für **Domänencontroller-Ressourcengruppe** die Ressourcengruppe aus, in der sich die AD DS-VM entweder befindet, oder mit der sie per Peering verbunden ist. Die Ressourcengruppe mit dem Domänencontroller muss sich im selben Abonnement befinden. Das Feature „Erste Schritte“ unterstützt derzeit keine Abonnements mit Peering.

       - Geben Sie für **Domänencontroller-VM** den Namen des virtuellen Computers ein, auf dem die AD DS Ihrer Bereitstellung ausgeführt werden.

    - Wenn Sie „Azure AD-Benutzer oder -Benutzergruppen auswählen“ öffnen möchten, aktivieren Sie das Kontrollkästchen **Vorhandene Benutzer zuweisen**.

    - Wenn Sie ein Validierungsbenutzerkonto erstellen möchten, um Ihre Bereitstellung zu testen, aktivieren Sie das Kontrollkästchen **Validierungsbenutzer erstellen**, und geben Sie in der dann angezeigten Eingabeaufforderung einen Benutzernamen und ein Kennwort ein.

       >[!NOTE]
       >Bei den ersten Schritte wird die Validierungsbenutzergruppe im Container „USERS“ erstellt. Sie müssen sicherstellen, dass Ihre Validierungsgruppe mit dem Azure AD synchronisiert wird. Wenn die Synchronisierung nicht funktioniert, erstellen Sie vorab die Gruppe AVDValidationUsers in einer Organisationseinheit, die mit dem Azure AD synchronisiert wird.

## <a name="for-subscriptions-without-azure-ad-ds-or-ad-ds"></a>Für Abonnements ohne Azure AD DS oder AD DS

In diesem Abschnitt erfahren Sie, wie Sie das Feature „Erste Schritte“ für ein Abonnement ohne Azure AD DS oder AD DS verwenden. Als Referenz werden diese Abonnements manchmal als „leere“ Abonnements bezeichnet.

So stellen Sie Azure Virtual Desktop in einem Abonnement ohne Azure AD DS oder AD DS bereit:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Melden Sie sich bei Azure an, öffnen Sie die **Azure Virtual Desktop-Verwaltung**, und wählen Sie dann die Registerkarte **Erste Schritte** aus. Damit wird die Landing Page für das Feature „Erste Schritte“ geöffnet.

3. Wählen Sie auf der Registerkarte **Basic** die folgenden Werte aus:

    - Wählen Sie unter **Abonnement** das Abonnement aus, unter dem Sie Azure Virtual Desktop bereitstellen möchten.

    - Wählen Sie für **Wie ist Ihr Abonnement konfiguriert?** die Option **Leeres Abonnement** aus. Ein „leeres“ Abonnement ist ein Abonnement, das keinen Identitätsanbieter wie Azure AD oder AD DS benötigt.

    - Geben Sie unter **Ressourcengruppenpräfix** die Präfixe für die Ressourcengruppe ein, die Sie erstellen möchten: *-prerequisite*, *-deployment* und *-avd*.

    - Geben Sie unter **Speicherort** den Ressourcenspeicherort ein, den Sie für Ihre Bereitstellung verwenden möchten.

    - Geben Sie für **Azure-Administrator-UPN** den vollständigen UPN eines Kontos mit Administratorberechtigungen für Azure AD und Besitzerberechtigungen für das Abonnement ein.

    - Geben Sie für **UPN für AD-Domänenbeitritt** den vollständigen UPN für ein Konto ein, das der Gruppe **AAD DC-Administratoren** hinzugefügt wird.

    >[!NOTE]
    >Der Benutzername für „UPN für AD-Domänenbeitritt“ sollte ein eindeutiger Benutzername sein, der noch nicht in Azure AD vorhanden ist. Das Feature „Erste Schritte“ unterstützt derzeit nicht die Verwendung vorhandener Azure AD-Benutzernamen für Konten ohne Azure AD oder AD DS.

4. Wählen Sie auf der Registerkarte **Virtuelle Computer** folgende Werte aus:

    - Wählen Sie für **Möchten Sie, dass die Benutzer diesen Computer freigeben?** je nach Ihren Anforderungen eine der folgenden Optionen aus:
      - Wenn Sie einen Einzelsitzungs- oder persönlichen Hostpool erstellen möchten, wählen Sie **Nein** aus.
      - Wenn Sie einen Hostpool mit mehreren Sitzungen oder gepoolten Hostpool erstellen möchten, wählen Sie **Ja (mehrere Sitzungen)** aus. Dadurch wird auch ein Azure Files-Speicherkonto erstellt, das entweder Azure AD DS oder AD DS hinzugefügt wird.

    - Wählen Sie unter **Imagetyp** ein Image aus dem Azure-Imagekatalog, ein benutzerdefiniertes Image oder eine VHD aus einem Speicherblob aus.

    - Wählen Sie für **VM-Größe** die Größe und SKU aus, die Sie für die bereitzustellenden VMs wünschen.

    - Wählen Sie unter **Anzahl der VMs** aus, wie viele VMs Sie im Hostpool bereitstellen möchten.

5. Wählen Sie auf der Registerkarte **Zuweisungen** die Option **Validierungsbenutzer erstellen** aus, und geben Sie dann einen Benutzernamen und ein Kennwort in die Felder **Benutzername des Validierungsbenutzers** und **Kennwort des Validierungsbenutzers** ein. Der Validierungsbenutzer ist ein Benutzer, der Ihre Bereitstellung testen wird, sobald sie bereit ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie nach der Bereitstellung Ihre Meinung ändern und Azure Virtual Desktop-Ressourcen aus Ihrer Umgebung entfernen möchten, ohne dass zusätzliche Abrechnungskosten entstehen, können Sie diese problemlos entfernen, indem Sie die Anweisungen in diesem Abschnitt befolgen.

Wenn Sie Ihre Ressourcen in einem Abonnement mit Azure AD DS oder AD DS erstellt haben, hat das Feature zwei Ressourcengruppen mit den Präfixen „ *-deployment*“ und „ *-avd*“ erstellt. Wechseln Sie im Azure-Portal zu **Ressourcengruppen**, und löschen Sie alle Ressourcengruppen mit diesen Präfixen, um die Bereitstellung zu entfernen.

Wenn Sie Ihre Ressourcen in einem Abonnement ohne Azure AD DS oder AD DS erstellt haben, hat das Feature drei Ressourcengruppen mit den Präfixen *-prerequisite*, *-deployment* und *-avd* erstellt. Wechseln Sie im Azure-Portal zu **Ressourcengruppen**, und löschen Sie alle Ressourcengruppen mit diesen Präfixen, um die Bereitstellung zu entfernen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie erfahren möchten, wie Sie Azure Virtual Desktop auf detailliertere Weise bereitstellen, lesen Sie unsere Tutorials zum manuellen Einrichten Ihrer Bereitstellung, zuerst [Tutorial: Erstellen eines Hostpools mit dem Azure-Portal](create-host-pools-azure-marketplace.md).
