---
title: Services de données et Cloud Computing dans les applications Xamarin. iOS
description: Ce document contient des liens vers des guides qui décrivent comment utiliser les données locales, iCloud et CloudKit dans une application Xamarin. iOS.
ms.prod: xamarin
ms.assetid: 945719F7-7CE6-4207-BF0F-23195125FC84
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/13/2017
ms.openlocfilehash: e9f910804f3da9d173206d51c59e1d7ddd9b90a6
ms.sourcegitcommit: bad1ab3f78d7f94d48511666626b54f8ba155689
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75663510"
---
# <a name="data-and-cloud-services-in-xamarinios-apps"></a>Services de données et Cloud Computing dans les applications Xamarin. iOS

## <a name="data-accessiosdata-clouddataindexmd"></a>[Accès aux données](~/ios/data-cloud/data/index.md)

La plupart des applications ont besoin d’enregistrer des données sur l’appareil localement. À moins que la quantité de données ne soit réduite de façon triviale, cela nécessite généralement une base de données et une couche de données dans l’application pour gérer l’accès aux bases de données. iOS a le moteur de base de données SQLite « intégré » et l’accès aux données est simplifié par la plateforme de Xamarin, qui est fournie avec le Fournisseur de données SQLite.

## <a name="icloudiosdata-cloudintroduction-to-icloudmd"></a>[iCloud](~/ios/data-cloud/introduction-to-icloud.md)

L’API de stockage iCloud permet aux applications d’enregistrer des documents utilisateur et des données spécifiques à l’application dans un emplacement central et d’accéder à ces éléments à partir de tous les appareils de l’utilisateur.

## <a name="cloudkitiosdata-cloudintro-to-cloudkitmd"></a>[CloudKit](~/ios/data-cloud/intro-to-cloudkit.md)

L’infrastructure CloudKit simplifie le développement d’applications qui accèdent à iCloud. Cela comprend la récupération des données d’application et des droits d’actifs, ainsi que la possibilité de stocker des informations d’application en toute sécurité. Ce kit offre aux utilisateurs une couche d’anonymat en autorisant l’accès aux applications avec leurs ID iCloud sans partager les informations personnelles.
