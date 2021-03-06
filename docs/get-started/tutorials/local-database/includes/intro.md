---
ms.openlocfilehash: 8f379e8639846d9a6424c4aaadf83ff89d7a8684
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "71107230"
---
Avant de tenter ce didacticiel, vous devez avoir suivi les guides et didacticiels suivants :

- Guide de démarrage rapide [Générer votre première application Xamarin.Forms](~/get-started/first-app/index.md).
- Didacticiel [StackLayout](~/get-started/tutorials/stacklayout/index.yml).
- Didacticiel [Button](~/get-started/tutorials/button/index.yml).
- Didacticiel [Entry](~/get-started/tutorials/entry/index.yml).
- Didacticiel [ListView](~/get-started/tutorials/listview/index.yml).

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
>
> - Utiliser le Gestionnaire de package NuGet pour ajouter un package SQLite.NET à un projet Xamarin.Forms.
> - Créer les classes d’accès aux données.
> - Utiliser les classes d’accès aux données.

Vous allez utiliser Visual Studio 2019 ou Visual Studio pour Mac pour créer une application simple qui montre comment stocker des données dans une base de données SQLite.NET locale. Les captures d’écran suivantes montrent l’application finale :

[![Capture d’écran montrant la persistance des données dans une base de données SQLite.NET locale, sur iOS et Android](../images/consume-data-access-classes-reduced.png "Persistance des données dans une base de données locale")](../images/consume-data-access-classes-large.png#lightbox "Persistance des données dans une base de données locale")
