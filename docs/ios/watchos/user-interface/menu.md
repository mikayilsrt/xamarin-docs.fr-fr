---
title: Contrôle de menu Watchos (Force Touch) dans Xamarin
description: Ce document explique comment utiliser le mouvement de force tactile Watchos dans Xamarin. Il explique comment répondre à une pression tactile, comment ajouter un menu et comment modifier les éléments de menu.
ms.prod: xamarin
ms.assetid: 5A7F83FB-9BC4-4812-92C5-CEC8DAE8211E
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: f848433a5a668e247142aa4f47374c2c6531b55d
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032692"
---
# <a name="watchos-menu-control-force-touch-in-xamarin"></a>Contrôle de menu Watchos (Force Touch) dans Xamarin

Le kit Watch fournit un mouvement Force Touch qui déclenche un menu lorsqu’il est implémenté sur un écran d’application Watch.

![](menu-images/menu.png "Apple Watch showing a menu")
<!-- watch image courtesy of http://infinitapps.com/bezel/ -->

## <a name="responding-to-force-touch"></a>Réponse aux Force Touch

Si un `Menu` a été implémenté pour un contrôleur d’interface, lorsqu’un utilisateur exécute une Force Touch le menu s’affiche. Si aucun menu n’a été implémenté, l’écran est brièvement animé. aucune autre action ne se produit.

Les touches de force ne sont pas associées à un élément particulier à l’écran ; un seul menu peut être attaché à un contrôleur d’interface et il s’affiche, quel que soit l’endroit où se trouve le Force Touch sur l’écran.

Il est possible de présenter entre une et quatre options de menu.

## <a name="adding-a-menu"></a>Ajout d’un menu

Un `Menu` doit être ajouté à un `InterfaceController` sur le Storyboard au moment du Design. Lorsqu’un contrôle de menu est glissé sur un contrôleur d’interface, il n’existe aucune indication visuelle sur l’aperçu de la table de montage séquentiel, mais le **menu** s’affiche dans le bloc de **structure du document** :

![](menu-images/menu-action.png "Editing a menu at design time")

Vous pouvez ajouter jusqu’à quatre éléments de menu au contrôle Menu. Ils peuvent être configurés dans le panneau **Propriétés** . Les attributs suivants peuvent être définis :

- Titre et
- Image personnalisée ou
- Une image système : accepter, ajouter, bloquer, refuser, info, peut-être, plus, muet, suspendre, lire, répéter, reprendre, partager, lire en lecture seule, orateur, corbeille.

Créez un `Action` en sélectionnant la section **événements** du panneau **Propriétés** et en tapant le nom de la méthode d’action. Une méthode partielle est créée dans le code, qui peut être implémentée dans la classe de contrôleur d’interface, comme suit :

```csharp
partial void MenuItemTapped ()
{
    Console.WriteLine ("A menu item was tapped.");
}
```

### <a name="custom-images"></a>Images personnalisées

Comme pour les images avec onglets dans iOS, les images d’élément de menu requièrent un modèle opaque avec un canal alpha qui permet d’afficher l’arrière-plan.

Pour des performances optimales, vous devez ajouter les images utilisées pour le menu au projet d’application Watch (et non au projet d’extension de l’application Watch).

## <a name="changing-the-menu-items"></a>Modification des éléments de menu

<!--
### Design Time Items

Menu items added the storyboard can be shown and hidden programmatically.
-->

### <a name="adding-at-runtime"></a>Ajouter au moment de l’exécution

Vous ne pouvez pas provoquer l’ajout d’un `Menu` à un contrôleur d’interface au moment de l’exécution, même si la collection de `MenuItem`s *peut* être modifiée par programme.
Utilisez la méthode `AddMenuItem` comme indiqué ci-dessous :

```csharp
AddMenuItem (WKMenuItemIcon.Accept, "Yes", new ObjCRuntime.Selector ("tapped"));
```

L’API du kit de surveillance Xamarin. iOS requiert actuellement une `selector` pour la méthode `AdMenuItem`, qui doit être déclarée comme suit :

```csharp
[Export("tapped")]
void MenuItemTapped ()
{
    Console.WriteLine ("The dynamically added 'Yes' menu item was tapped.");
}
```

### <a name="removing-at-runtime"></a>Supprimer au moment de l’exécution

La méthode `ClearAllMenuItems` peut être appelée pour supprimer tous les éléments de menu *ajoutés par programmation* .

Impossible d’effacer les éléments de menu configurés dans la table de montage séquentiel.

## <a name="related-links"></a>Liens associés

- [WatchKitCatalog (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/watchos-watchkitcatalog)
- [Doc menu d’Apple](https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/WatchKitProgrammingGuide/Menus.html)
