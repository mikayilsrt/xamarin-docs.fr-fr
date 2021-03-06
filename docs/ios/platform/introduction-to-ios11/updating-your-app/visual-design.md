---
title: Mises à jour de la conception visuelle dans iOS 11
description: Ce document décrit les mises à jour de la conception visuelle introduites dans iOS 11. Il traite des modifications apportées aux barres de navigation, aux contrôleurs de recherche, aux marges, au contenu plein écran et aux vues de table.
ms.prod: xamarin
ms.assetid: 7C300B94-0FAF-492E-A326-877419A1824B
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/13/2016
ms.openlocfilehash: 532a26de422846d81f6ba6dead7dee6341bcee72
ms.sourcegitcommit: 93e6358aac2ade44e8b800f066405b8bc8df2510
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84570893"
---
# <a name="visual-design-updates-in-ios-11"></a>Mises à jour de la conception visuelle dans iOS 11

Dans iOS 11, Apple a introduit de nouvelles modifications visuelles, notamment des mises à jour de la barre de navigation, de la barre de recherche et des vues de table. En outre, des améliorations ont été apportées pour offrir une plus grande flexibilité sur les marges et le contenu en plein écran. Ces modifications sont traitées dans ce guide. 

Pour plus d’informations sur la conception de iPhone X, regardez la vidéo sur la conception d’Apple [pour iPhone x](https://developer.apple.com/videos/play/fall2017/801/) .

## <a name="uikit"></a>UIKit

Les barres UIKit ont été adaptées à iOS 11 pour les rendre plus accessibles aux utilisateurs finaux.

Une telle modification est un nouvel affichage HUD qui apparaît lorsque l’utilisateur appuie longuement sur un élément de la barre. Pour ce faire, définissez la `largeContentSizeImage` propriété sur `UIBarItem` et ajoutez une image plus grande via un [catalogue de composants](~/ios/app-fundamentals/images-icons/displaying-an-image.md):

```csharp
barItem.LargeContentSizeImage = UIImage.FromBundle("AccessibleImage");
```

### <a name="navigation-bar"></a>Barre de navigation
iOS 11 a introduit de nouvelles fonctionnalités pour faciliter la lecture des titres de barre de navigation. Les applications peuvent afficher ce titre plus grand en attribuant `PrefersLargeTitles` à la propriété la valeur true :

```csharp
NavigationController.NavigationBar.PrefersLargeTitles = true;
```

Si vous définissez des titres plus grands dans votre application, _tous les_ titres des barres de navigation de votre application apparaissent plus grands, comme illustré dans la capture d’écran suivante :

![Titre de navigation de grande taille](visual-design-images/image7.png)

Pour contrôler quand un titre de grande taille est affiché dans une barre de navigation, affectez `LargeTitleDisplayMode` à sur l’élément de navigation la valeur `Always` , `Never` ou `Automatic` .

### <a name="search-controller"></a>Contrôleur de recherche

iOS 11 a facilité l’ajout d’un contrôleur de recherche directement à la barre de navigation. Une fois que vous avez créé un contrôleur de recherche, ajoutez-le à votre barre de navigation avec la `SearchController` propriété :

```csharp
NavigationItem.SearchController = searchController;
```

[![Grand titre de navigation avec la barre de recherche](visual-design-images/image8-sml.png)](visual-design-images/image8-sml.png#lightbox)

Selon les fonctionnalités de votre application, vous pouvez ou non que la barre de recherche se masque quand un utilisateur fait défiler une liste. Vous pouvez ajuster cette propriété à l’aide de la `HidesSearchBarWhenScrolling` propriété.

## <a name="margins"></a>Marges

Apple a créé une nouvelle propriété, `directionalLayoutMargins` qui peut être utilisée pour définir l’espace entre les vues et les sous-vues. Utilisez `directionalLayoutMargins` avec `leading` ou `trailing` indéfinis. Que le système soit une langue de gauche à droite ou de droite à gauche, l’espacement dans votre application est correctement défini par iOS.

Dans iOS 10 et avant, toutes les vues avaient une taille de marge minimale à laquelle elles s’aligneraient. iOS 11 a introduit la possibilité de remplacer cette valeur à l’aide de `ViewRespectsSystemMinimumLayoutMargins` . Par exemple, si vous affectez la valeur false à cette propriété, vous pouvez ajuster la valeur de votre bord à zéro :

```csharp
ViewRespectsSystemMinimumLayoutMargins = false;
View.LayoutMargins = UIEdgeInsets.Zero;
```

![Image représentant une marge avec zéro incrustation dans iOS 11](visual-design-images/image9.png)

<a name="fullscreen"></a>

## <a name="full-screen-content"></a>Contenu plein écran

iOS 7 [introduit](~/ios/platform/introduction-to-ios7/ios7-ui.md#fullscreen) `topLayoutGuide` et `bottomLayoutGuide` comme un moyen de contraindre vos vues afin qu’elles ne soient pas masquées par des barres UIKit et se trouvent dans une zone visible de l’écran. Celles-ci ont été dépréciées dans iOS 11 en faveur de la _zone sécurisée_.

La zone sécurisée est une nouvelle façon de réfléchir à l’espace visible de votre application et à la façon dont les contraintes sont ajoutées entre une vue et une vue super. Par exemple, considérez l’image suivante :

[![Zone sécurisée et guide de disposition en haut et en bas](visual-design-images/image10-sml.png)](visual-design-images/image10.png#lightbox)

Auparavant, si vous aviez ajouté une vue et souhaitez qu’elle soit visible dans la zone verte ci-dessus, vous devez la contraindre au _bas_ de `TopLayoutGuide` et au _haut_ de `BottomLayoutGuide` . Dans iOS 11, vous devez le limiter au _sommet_ et au _bas_ de la zone sécurisée. Exemple :

```csharp
var safeGuide = View.SafeAreaLayoutGuide;
imageView.TopAnchor.ConstraintEqualTo(safeGuide.TopAnchor).Active = true;
safeGuide.BottomAnchor.ConstraintEqualTo(imageView.BottomAnchor).Active = true;
```

## <a name="table-view"></a>Affichage Table

Le UITableView a subi un grand nombre de modifications mineures, mais importantes, dans iOS 11.

Par défaut, les en-têtes, les pieds de page et les cellules sont maintenant dimensionnés automatiquement en fonction de leur contenu. Pour désactiver ce comportement de dimensionnement automatique, affectez la valeur `EstimatedRowHeight` `EstimatedSectionHeaderHeight` à, ou `EstimatedSectionFooterHeight` à zéro.

Toutefois, dans certains cas (par exemple, lors de l’ajout de UITableViewController dans le concepteur iOS ou lors de l’utilisation de Storboards existants dans Interface Builder), vous devrez peut-être activer manuellement les cellules à redimensionnement automatique. Pour ce faire, assurez-vous que vous avez défini les propriétés suivantes sur la vue table pour les cellules, les en-têtes et les pieds de page, respectivement :

```csharp
// Cells
TableView.RowHeight = UITableView.AutomaticDimension;
TableView.EstimatedRowHeight = UITableView.AutomaticDimension;

// Header
TableView.SectionHeaderHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionHeaderHeight = 40f;

//Footer
TableView.SectionFooterHeight = UITableView.AutomaticDimension;
TableView.EstimatedSectionFooterHeight = 40f;

```

iOS 11 a étendu les fonctionnalités des actions de ligne. `UISwipeActionsConfiguration`a été introduit pour définir un ensemble d’actions qui doivent se produire lorsque l’utilisateur effectue un glissement dans l’une ou l’autre des directions sur une ligne d’une vue de table. Ce comportement est similaire à celui de l’application de messagerie native. Pour plus d’informations, consultez le guide des [actions de ligne](~/ios/user-interface/controls/tables/row-action.md) .

Les vues de table prennent en charge le glisser-déplacer dans iOS 11. Pour plus d’informations, consultez le Guide de [glisser-déplacer](~/ios/platform/introduction-to-ios11/drag-and-drop.md#uitableview) .

## <a name="related-links"></a>Liens connexes

- [Nouveautés d’iOS 11 (Apple)](https://developer.apple.com/ios/)
- [Page produit App Store mise à jour (Apple)](https://developer.apple.com/app-store/product-page/)
- [Conception pour iPhone X (Apple) (vidéo)](https://developer.apple.com/videos/play/fall2017/801/)
- [Mise à jour de votre application pour iOS 11 (WWDC) (vidéo)](https://developer.apple.com/videos/play/wwdc2017/204/)
