---
title: Datei einfügen
description: include file
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: 615346c16fbbb31bcefc53fc68d04c1255ece88c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454036"
---
Die folgenden Rollen werden für die Kollaboration bereitgestellt:

|Role|Funktionen|API-Zugriff|API-Berechtigungen|
|--|--|--|--|
|Besitzer|All|Authentifizierungsschlüssel|All|
|Mitwirkender|Alle, mit Ausnahme der Möglichkeit, Rollen neue Mitglieder hinzuzufügen|Authentifizierungsschlüssel|Alle, mit Ausnahme der Möglichkeit, Rollen neue Mitglieder hinzuzufügen|
|QnA Maker lesen<br>(lesen)|Exportieren/herunterladen<br>Test|Bearertoken|1. KB-API herunterladen<br>2. KBs für Benutzer-API auflisten<br>3. Abrufen der Details der Wissensdatenbank<br>4. Änderungen herunterladen<br>Antwort generieren |
|QnA Maker Editor<br>(lesen/schreiben)|Exportieren/herunterladen<br>Test<br>KB aktualisieren<br>KB exportieren<br>KB importieren<br>KB ersetzen<br>Erstellen der Knowledge Base|Bearertoken|1. KB-API erstellen<br>2. KB-API aktualisieren<br>3. KB-API ersetzen<br>4. Änderungen ersetzen<br>5. „API trainieren“ [im neuen Dienstmodell Version 5]|
|Cognitive Service-Benutzer<br>(lesen/schreiben/veröffentlichen)|All|Authentifizierungsschlüssel|Alle Zugriffe auf Cognitive Services Ressource mit Ausnahme der Möglichkeit zum: <br>1. Hinzufügen neuer Mitglieder zu Rollen.<br>2. Erstellen neuer Ressourcen.|
