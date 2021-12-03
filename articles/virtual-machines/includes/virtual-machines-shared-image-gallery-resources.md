---
title: Datei einfügen
description: include file
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/13/2021
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1ae085270a28bfd27542585d5df6c1dd918943d9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131421632"
---
| Resource | BESCHREIBUNG|
|----------|------------|
| **Imagequelle** | Dies ist eine Ressource, die zum Erstellen einer **Imageversion** in einem Katalog verwendet werden kann. Eine Imagequelle kann eine vorhandene Azure-VM, die entweder [generalisiert oder spezialisiert](../shared-image-galleries.md#generalized-and-specialized-images) ist, ein verwaltetes Image, eine Momentaufnahme oder eine Imageversion in einem anderen Katalog sein. |
| **Galerie** | Wie der Azure Marketplace ist ein **Katalog** ein Repository zum Verwalten und Teilen von Images und [VM-Anwendungen](../vm-applications.md), aber Sie kontrollieren, wer Zugriff hat. |
| **Imagedefinition** | Imagedefinitionen werden in einem Katalog erstellt und enthalten Informationen zum Image und zu den Anforderungen für seine interne Verwendung. Dies schließt ein, ob das Image Windows oder Linux ist, Anmerkungen zu dieser Version und Anforderungen an den minimalen und maximalen Arbeitsspeicher. Es ist eine Definition eines Imagetyps. |
| **Imageversion** | Eine **Imageversion** ist, was Sie verwenden, um einen virtuellen Computer zu erstellen, wenn Sie einen Katalog verwenden. Sie können nach Bedarf mehrere Versionen eines Images für Ihre Umgebung haben. Wie bei einem verwalteten Image wird, wenn Sie eine **Imageversion** zum Erstellen einer VM verwenden, wird die Imageversion verwendet, um neue Datenträger für den virtuellen Computer zu erstellen. Imageversionen können mehrmals verwendet werden. |