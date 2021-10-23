---
title: Regionale Verfügbarkeit und Datenresidenz
titleSuffix: Azure AD B2C
description: Hier finden Sie Informationen zur regionalen Verfügbarkeit, zur Datenresidenz und zu Azure Active Directory B2C-Vorschaumandanten.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: references_regions
ms.openlocfilehash: 91b2c287b42d0a60a3b813b87f8e1eb2368da395
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130040225"
---
# <a name="azure-active-directory-b2c-region-availability--data-residency"></a>Azure Active Directory B2C: Regionale Verfügbarkeit und Datenresidenz

Azure AD B2C-Identitätsdaten werden an einem geografischen Standort gespeichert, basierend auf dem Land/der Region, das/die beim Erstellen des Tenants angegeben wurde.

Regionale Verfügbarkeit und Datenresidenz sind zwei unterschiedliche Konzepte, die für Azure AD B2C gelten. In diesem Artikel werden die Unterschiede zwischen diesen beiden Konzepten erläutert und ihre Anwendung in Azure und Azure AD B2C miteinander verglichen.

Azure AD B2C ist **grundsätzlich weltweit verfügbar**, mit der Option für **Datenresidenz** in den **USA, in Europa, in der Region „Asien-Pazifik“ und in Australien**.

[Regionale Verfügbarkeit](#region-availability) gibt an, wo ein Dienst für die Verwendung verfügbar ist.

[Datenresidenz](#data-residency) gibt an, wo die Benutzerdaten gespeichert werden.

## <a name="region-availability"></a>Regionale Verfügbarkeit

Azure AD B2C ist über die öffentliche Azure-Cloud weltweit verfügbar. Beispiele für diese Funktion finden Sie sowohl auf der Seite [Produkte verfügbar nach Region](https://azure.microsoft.com/regions/services/) von Azure als auch im [Preisrechner des Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="data-residency"></a>Datenresidenz

Azure AD B2C speichert Benutzerdaten in den USA, in Europa, in der Region „Asien-Pazifik“ oder in Australien.

Die Datenresidenz wird durch das Land oder die Region bestimmt, das bzw. die Sie beim [Erstellen eines Azure AD B2C-Mandanten](tutorial-create-tenant.md) auswählen:

![Screenshot des Formulars „Mandanten erstellen“, Auswahl von Land oder Region.](./media/data-residency/data-residency-b2c-tenant.png)

Für die folgenden Länder/Regionen werden die Daten in den **USA** gespeichert:

> USA (US), Kanada (CA), Costa Rica (CR), Dominikanische Republik (DO), El Salvador (SV), Guatemala (GT), Mexiko (MX), Panama (PA), Puerto Rico (PR) sowie Trinidad und Tobago (TT)

Für die folgenden Länder/Regionen werden die Daten in **Europa** gespeichert:

> Algerien (DZ), Österreich (AT), Aserbaidschan (AZ), Bahrain (BH), Belarus (BY), Belgien (BE), Bulgarien (BG), Kroatien (HR), Zypern (CY), Tschechische Republik (CZ), Dänemark (DK), Ägypten (EG), Estland (EE), Finnland (FT), Frankreich (FR), Deutschland (DE), Griechenland (GR), Ungarn (HU), Island (IS), Irland (IE), Israel (IL), Italien (IT), Jordanien (JO), Kasachstan (KZ), Kenia (KE), Kuwait (KW), Lettland (LV), Libanon (LB), Liechtenstein (LI), Litauen (LT), Luxemburg (LU), Nordmazedonien (ML), Malta (MT), Montenegro (ME), Marokko (MA), Niederlande (NL), Nigeria (NG), Norwegen (NO), Oman (OM), Pakistan (PK), Polen (PL), Portugal (PT), Katar (QA), Rumänien (RO), Russland (RU), Saudi-Arabien (SA), Serbien (RS), Slowakei (SK), Slowenien (ST), Südafrika (ZA), Spanien (ES), Schweden (SE), Schweiz (CH), Tunesien (TN), Türkei (TR), Ukraine (UA), Vereinigte Arabische Emirate (AE) und Vereinigtes Königreich (GB)

Für die folgenden Länder/Regionen werden die Daten in der Region **Asien-Pazifik** gespeichert:

> Afghanistan (AF), Hongkong (SAR) (HK), Indien (IN), Indonesien (ID), Japan (JP), Südkorea (KR), Malaysia (MY), Philippinen (PH), Singapur (SG), Sri Lanka (LK), Taiwan (TW) und Thailand (TH)

Für die folgenden Länder bzw. Regionen werden die Daten in der Region **Australien** gespeichert:

> Australien und Neuseeland

Derzeit wird die Liste um die folgenden Länder/Regionen ergänzt. Sie können vorerst Azure AD B2C verwenden, indem Sie eines bzw. eine der oben genannten Länder/Regionen auswählen.

> Argentinien, Brasilien, Chile, Ecuador, Irak, Kolumbien, Paraguay, Peru, Uruguay und Venezuela

## <a name="remote-profile-solution"></a>Remoteprofillösung

[Benutzerdefinierte Richtlinien](custom-policy-overview.md) von Azure AD B2C ermöglichen die Integration in [RESTful-API-Dienste](api-connectors-overview.md), was wiederum das Speichern und Lesen von Benutzerprofilen aus einer Remotedatenbank (etwa eine Marketingdatenbank, ein CRM-System oder eine beliebige Branchenanwendung) ermöglicht.  
- Während der Registrierung und der Profilbearbeitung ruft Azure AD B2C eine benutzerdefinierte REST-API auf, um das Benutzerprofil in der Remotedatenquelle zu speichern. Die Anmeldeinformationen des Benutzers werden im Azure AD B2C-Verzeichnis gespeichert. 
- Bei der Anmeldung ruft Azure AD B2C nach der Überprüfung der Anmeldeinformationen mit einem lokalen Konto oder einem Social Media-Konto die REST-API auf. Diese sendet daraufhin den eindeutigen Bezeichner des Benutzers als primären Benutzerschlüssel (E-Mail-Adresse oder Benutzerobjekt-ID). Die REST-API liest die Daten aus der Remotedatenbank und gibt das Benutzerprofil zurück.  

Nach Abschluss der Registrierung, Profilbearbeitung oder Anmeldung schließt Azure AD B2C das Benutzerprofil in das Zugriffstoken ein, das an die Anwendung zurückgegeben wird. Weitere Informationen finden Sie auf GitHub in der [Beispiellösung für Azure AD B2C-Remoteprofile](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Azure AD B2C-Mandanten](tutorial-create-tenant.md)
