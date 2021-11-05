---
title: Hinzufügen von Benutzern zu Ihrem Azure Dev/Test-Entwicklerverzeichnis-Mandanten
description: Eine Anleitung zum Hinzufügen von Benutzern zu Ihrem Azure-Kredit-Abonnement und zur Verwaltung ihres Zugriffs mit rollenbasierten Kontrollen.
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/12/2021
ms.custom: devtestoffer
ms.openlocfilehash: 14ded977ab78e8fbf6baf70d1c59e4aa632927b1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095006"
---
# <a name="add-users-to-your-azure-credit-subscription"></a>Hinzufügen von Benutzern zu Ihrem Azure-Guthaben-Abonnement  

Um Abonnement-Ressourcen zu verwalten und darauf zuzugreifen, müssen Sie ein Benutzer innerhalb des Verzeichnisses sein. In den Abonnements selbst wird die Hierarchie von den Azure Active Directory (Azure AD) Tenancies gesteuert. Azure AD ist ein leichtgewichtiges Verzeichnisprotokoll (LDAP), das Kontonamen und Passwörter speichert.  

Bevor Sie Benutzer hinzufügen, müssen Sie Ihre Unternehmenshierarchie festlegen und bestimmen, welche Zugriffsebene sie innerhalb Ihres Abonnements benötigen.  

## <a name="why-do-i-need-to-add-users"></a>Warum muss ich Benutzer hinzufügen?

Schritt 1 ist die Feststellung, ob Sie einen neuen Benutzer zu Ihrem Abonnement hinzufügen müssen. Hier sind einige Beispiele, warum Sie neue Benutzer hinzufügen müssen:  

- Sie arbeiten an einem Projekt und müssen der IT-Abteilung Zugang zur Sicherheitsüberwachung gewähren.  
- Arbeit an neuen API-Protokollen, die für ein anderes Mitglied einer Organisation von Nutzen sein könnten  
- Sie müssen den Zugang auf der Abonnementebene gewähren, damit sie auf alle Ressourcengruppen zugreifen können.  
- Auf Unternehmensebene muss sich eine einzelne Ressourcengruppe an einem Computer anmelden, benötigt aber keinen Zugriff auf das Abonnement  
- Bietet mehr Sichtbarkeit und Transparenz für die Projekte, an denen Sie arbeiten, und isoliert dennoch bestimmte Teile der Arbeit, falls erforderlich  
- Fügen Sie einen Berater als Benutzer oder innerhalb einer Gruppe hinzu, um einen Beitrag zu leisten  
- Sie möchten mit jemandem zusammenarbeiten, um die Vorproduktion zu testen und zu überwachen  

## <a name="where-do-i-add-users-and-their-roles-within-my-subscription"></a>Wo füge ich Benutzer und ihre Rollen innerhalb meines Abonnements hinzu?

Innerhalb von Azure AD ist die Zugriffsverwaltung eine wichtige Funktion. [Azure rollenbasierte Zugriffskontrolle](../../role-based-access-control/overview.md) \(Azure RBAC\) ist das Autorisierungssystem, das eine feinkörnige Zugriffsverwaltung von Azure-Ressourcen ermöglicht.  

Wenn Sie sich entschieden haben, dass Sie einen Benutzer hinzufügen müssen, müssen Sie wissen, wo Sie ihn hinzufügen und auf welche Ressourcen er Zugriff haben muss. Die Menge der Ressourcen, auf die ein Benutzer zugreifen kann, wird als Geltungsbereich bezeichnet.  

Wenn das Projekt und die Aufgaben, an denen Sie arbeiten, von der IT-Abteilung überwacht werden müssen, um Sicherheitsprotokolle zu aktivieren, muss dieser Benutzer eine Admin-Rolle innerhalb der Management-Gruppe haben, um vollen Zugriff und Berechtigungen innerhalb des Abonnements zu erhalten.  

![Ein Screenshot der Zugriffsebenen in Azure.](media/how-to-add-users-directory/access-management.png "Verwalten von Rollen mit Verwaltungsgruppen in Azure.")

Wenn Sie jedoch mit einem anderen Entwickler oder einem Kollegen zusammenarbeiten, benötigt dieser möglicherweise nur Zugriff auf der Ebene der Ressource oder der Ressourcengruppe.  

Lesen Sie den [Azure RBAC Überblick](../../role-based-access-control/overview.md), um besser zu verstehen, wie Azure RBAC funktioniert und welchen Zweck es innerhalb Ihres Abonnements erfüllt.  

Hinzufügen von Benutzern oder Löschen von Benutzern mit Azure Active Directory  

- [Hinzufügen oder Löschen von Benutzern – Azure Active Directory | Microsoft-Dokumentation](../../active-directory/fundamentals/add-users-azure-active-directory.md)  

- [Schritte zur Zuweisung einer Azure-Rolle - Azure RBAC | Microsoft Docs](../../role-based-access-control/role-assignments-steps.md)  
