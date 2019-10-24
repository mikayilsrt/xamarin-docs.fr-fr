---
title: Xamarin. Forms CarouselView défilement
description: Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. En outre, CarouselView définit deux méthodes ScrollTo, qui défilent par programmation des éléments dans la vue.
ms.prod: xamarin
ms.assetid: 92D7B618-07FA-4343-9D0F-212525E92C39
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/14/2019
ms.openlocfilehash: dc72dc7549a697c7231045601851ba4108f29e1b
ms.sourcegitcommit: 21d8be9571a2fa89fb7d8ff0787ff4f957de0985
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72697583"
---
# <a name="xamarinforms-carouselview-scrolling"></a>Xamarin. Forms CarouselView défilement

![](~/media/shared/preview.png "This API is currently pre-release")

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit les propriétés suivantes liées au défilement :

- `HorizontalScrollBarVisibility`, de type `ScrollBarVisibility`, qui spécifie quand la barre de défilement horizontale est visible.
- `IsDragging`, de type `bool`, qui indique si le `CarouselView` défile. Il s’agit d’une propriété en lecture seule, dont la valeur par défaut est `false`.
- `IsScrollAnimated`, de type `bool`, qui spécifie si une animation doit se produire lors du défilement du `CarouselView`. La valeur par défaut est `true`.
- `ItemsUpdatingScrollMode`, de type `ItemsUpdatingScrollMode`, qui représente le comportement de défilement de la `CarouselView` lors de l’ajout de nouveaux éléments.
- `VerticalScrollBarVisibility`, de type `ScrollBarVisibility`, qui spécifie quand la barre de défilement verticale est visible.

Toutes ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie qu’ils peuvent être des cibles de liaisons de données.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit également deux méthodes de [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) , qui défilent les éléments dans la vue. L’une des surcharges fait défiler l’élément à l’index spécifié dans la vue, tandis que l’autre fait défiler l’élément spécifié dans la vue. Les deux surcharges ont des arguments supplémentaires qui peuvent être spécifiés pour indiquer la position exacte de l’élément une fois le défilement terminé, et s’il faut animer le défilement.

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un événement [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) qui est déclenché lorsque l’une des méthodes [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) est appelée. L’objet [`ScrollToRequestedEventArgs`](xref:Xamarin.Forms.ScrollToRequestedEventArgs) qui accompagne l’événement `ScrollToRequested` possède de nombreuses propriétés, notamment `IsAnimated`, `Index`, `Item` et `ScrollToPosition`. Ces propriétés sont définies à partir des arguments spécifiés dans les appels de la méthode `ScrollTo`.

De plus, [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un événement `Scrolled` déclenché pour indiquer que le défilement s’est produit. L’objet `ItemsViewScrolledEventArgs` qui accompagne l’événement `Scrolled` possède de nombreuses propriétés. Pour plus d’informations, consultez [détecter le défilement](#detect-scrolling).

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. Cette fonctionnalité est appelée alignement, car les éléments sont alignés à la position lorsque le défilement s’arrête. Pour plus d’informations, consultez [points d’alignement](#snap-points).

[`CarouselView`](xref:Xamarin.Forms.CarouselView) pouvez également charger des données de façon incrémentielle au fur et à mesure que l’utilisateur fait défiler. Pour plus d’informations, consultez [charger des données de façon incrémentielle](populate-data.md#load-data-incrementally).

## <a name="detect-scrolling"></a>Détecter le défilement

La propriété `IsDragging` peut être examinée pour déterminer si le [`CarouselView`](xref:Xamarin.Forms.CarouselView) fait actuellement défiler les éléments.

De plus, [`CarouselView`](xref:Xamarin.Forms.CarouselView) définit un événement `Scrolled` déclenché pour indiquer que le défilement s’est produit. Cet événement doit être consommé lorsque les données relatives au défilement sont requises.

L’exemple de code XAML suivant montre une `CarouselView` qui définit un gestionnaire d’événements pour l’événement `Scrolled` :

```xaml
<CarouselView Scrolled="OnCollectionViewScrolled">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView();
carouselView.Scrolled += OnCarouselViewScrolled;
```

Dans cet exemple de code, le gestionnaire d’événements `OnCarouselViewScrolled` est exécuté lorsque l’événement `Scrolled` se déclenche :

```csharp
void OnCarouselViewScrolled(object sender, ItemsViewScrolledEventArgs e)
{
    Debug.WriteLine("HorizontalDelta: " + e.HorizontalDelta);
    Debug.WriteLine("VerticalDelta: " + e.VerticalDelta);
    Debug.WriteLine("HorizontalOffset: " + e.HorizontalOffset);
    Debug.WriteLine("VerticalOffset: " + e.VerticalOffset);
    Debug.WriteLine("FirstVisibleItemIndex: " + e.FirstVisibleItemIndex);
    Debug.WriteLine("CenterItemIndex: " + e.CenterItemIndex);
    Debug.WriteLine("LastVisibleItemIndex: " + e.LastVisibleItemIndex);
}
```

Dans cet exemple, le gestionnaire d’événements `OnCarouselViewScrolled` génère les valeurs de l’objet `ItemsViewScrolledEventArgs` qui accompagne l’événement.

> [!IMPORTANT]
> L’événement `Scrolled` est déclenché pour les défilement initiés par l’utilisateur et pour les défilement par programmation.

## <a name="scroll-an-item-at-an-index-into-view"></a>Faire défiler un élément d’un index dans la vue

La première [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) surcharge de méthode fait défiler l’élément à l’index spécifié dans la vue. À partir d’un objet [`CarouselView`](xref:Xamarin.Forms.CarouselView) nommé `carouselView`, l’exemple suivant montre comment faire défiler l’élément à l’index 6 dans la vue :

```csharp
carouselView.ScrollTo(6);
```

> [!NOTE]
> L’événement [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) est déclenché lorsque la méthode [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) est appelée.

## <a name="scroll-an-item-into-view"></a>Faire défiler un élément dans l’affichage

La deuxième [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) surcharge de méthode fait défiler l’élément spécifié dans la vue. À partir d’un objet [`CarouselView`](xref:Xamarin.Forms.CarouselView) nommé `carouselView`, l’exemple suivant montre comment faire défiler l’élément de singe Proboscis dans la vue :

```csharp
MonkeysViewModel viewModel = BindingContext as MonkeysViewModel;
Monkey monkey = viewModel.Monkeys.FirstOrDefault(m => m.Name == "Proboscis Monkey");
carouselView.ScrollTo(monkey);
```

> [!NOTE]
> L’événement [`ScrollToRequested`](xref:Xamarin.Forms.ItemsView.ScrollToRequested) est déclenché lorsque la méthode [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) est appelée.

## <a name="disable-scroll-animation"></a>Désactiver l’animation de défilement

Une animation de défilement s’affiche lorsque vous passez d’un élément à un autre dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView). Cette animation se produit à la fois pour les défilement initiés par l’utilisateur et pour les défilement par programmation. Si vous affectez à la propriété `IsScrollAnimated` la valeur `false`, l’animation est désactivée pour les deux catégories de défilement.

L’argument `animate` de la méthode `ScrollTo` peut également avoir la valeur `false` pour désactiver l’animation de défilement sur les défilement par programmation :

```csharp
carouselView.ScrollTo(monkey, animate: false);
```

## <a name="control-scroll-position"></a>Position de défilement du contrôle

Lorsque vous faites défiler un élément dans la vue, la position exacte de l’élément après la fin du défilement peut être spécifiée avec l’argument `position` des méthodes [`ScrollTo`](xref:Xamarin.Forms.ItemsView.ScrollTo*) . Cet argument accepte un membre de l’énumération [`ScrollToPosition`](xref:Xamarin.Forms.ScrollToPosition) .

### <a name="makevisible"></a>MakeVisible

Le membre [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) indique que l’élément doit faire l’objet d’un défilement jusqu’à ce qu’il soit visible dans la vue :

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.MakeVisible);
```

Cet exemple de code entraîne le défilement minimal requis pour faire défiler l’élément dans l’affichage.

> [!NOTE]
> Le membre [`ScrollToPosition.MakeVisible`](xref:Xamarin.Forms.ScrollToPosition) est utilisé par défaut, si l’argument `position` n’est pas spécifié lors de l’appel de la méthode `ScrollTo`.

### <a name="start"></a>Start

Le membre [`ScrollToPosition.Start`](xref:Xamarin.Forms.ScrollToPosition) indique que l’élément doit défiler jusqu’au début de la vue :

```csharp
carouselView.ScrollTo(monkey, position: ScrollToPosition.Start);
```

Cet exemple de code entraîne le défilement de l’élément jusqu’au début de la vue.

### <a name="center"></a>Center

Le membre [`ScrollToPosition.Center`](xref:Xamarin.Forms.ScrollToPosition) indique que l’élément doit défiler jusqu’au centre de la vue :

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.Center);
```

Cet exemple de code entraîne le défilement de l’élément au centre de la vue.

### <a name="end"></a>Fin

Le membre [`ScrollToPosition.End`](xref:Xamarin.Forms.ScrollToPosition) indique que l’élément doit défiler jusqu’à la fin de la vue :

```csharp
carouselViewView.ScrollTo(monkey, position: ScrollToPosition.End);
```

Cet exemple de code entraîne le défilement de l’élément à la fin de la vue.

## <a name="control-scroll-position-when-new-items-are-added"></a>Contrôler la position de défilement quand de nouveaux éléments sont ajoutés

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit une propriété `ItemsUpdatingScrollMode`, qui est stockée par une propriété pouvant être liée. Cette propriété obtient ou définit une `ItemsUpdatingScrollMode` valeur d’énumération qui représente le comportement de défilement du `CarouselView` quand de nouveaux éléments y sont ajoutés. L’énumération `ItemsUpdatingScrollMode` définit les membres suivants :

- `KeepItemsInView` ajuste le décalage de défilement pour maintenir le premier élément visible affiché lorsque de nouveaux éléments sont ajoutés.
- `KeepScrollOffset` maintient le décalage de défilement par rapport au début de la liste lorsque de nouveaux éléments sont ajoutés.
- `KeepLastItemInView` ajuste le décalage de défilement pour conserver le dernier élément visible lorsque de nouveaux éléments sont ajoutés.

La valeur par défaut de la propriété `ItemsUpdatingScrollMode` est `KeepItemsInView`. Par conséquent, lorsque de nouveaux éléments sont ajoutés à un [`CarouselView`](xref:Xamarin.Forms.CarouselView) le premier élément visible dans la liste reste affiché. Pour vous assurer que les éléments récemment ajoutés sont toujours visibles au bas de la liste, la propriété `ItemsUpdatingScrollMode` doit être définie sur `KeepLastItemInView` :

```xaml
<CarouselView ItemsUpdatingScrollMode="KeepLastItemInView">
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsUpdatingScrollMode = ItemsUpdatingScrollMode.KeepLastItemInView
};
```

## <a name="scroll-bar-visibility"></a>Visibilité de la barre de défilement

[`CarouselView`](xref:Xamarin.Forms.CarouselView) définit `HorizontalScrollBarVisibility` et `VerticalScrollBarVisibility` propriétés, qui sont sauvegardées par des propriétés pouvant être liées. Ces propriétés obtiennent ou définissent une [`ScrollBarVisibility`](xref:Xamarin.Forms.ScrollBarVisibility) valeur d’énumération qui représente le moment où la barre de défilement horizontale ou verticale est visible. L’énumération `ScrollBarVisibility` définit les membres suivants :

- [`Default`](xref:Xamarin.Forms.ScrollBarVisibility) indique le comportement par défaut de la barre de défilement pour la plateforme, et est la valeur par défaut pour les propriétés `HorizontalScrollBarVisibility` et `VerticalScrollBarVisibility`.
- [`Always`](xref:Xamarin.Forms.ScrollBarVisibility) indique que les barres de défilement sont visibles, même lorsque le contenu s’ajuste à la vue.
- [`Never`](xref:Xamarin.Forms.ScrollBarVisibility) indique que les barres de défilement ne sont pas visibles, même si le contenu ne tient pas dans la vue.

## <a name="snap-points"></a>Points d’alignement

Lorsqu’un utilisateur effectue un balayage pour lancer un défilement, la position de fin du défilement peut être contrôlée afin que les éléments soient entièrement affichés. Cette fonctionnalité est appelée alignement, car les éléments sont alignés à la position lorsque le défilement s’arrête et sont contrôlés par les propriétés suivantes de la classe [`ItemsLayout`](xref:Xamarin.Forms.ItemsLayout) :

- [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType), de type [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType), spécifie le comportement des points d’alignement lors du défilement.
- [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment), de type [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment), spécifie la manière dont les points d’ancrage sont alignés avec les éléments.

Ces propriétés sont sauvegardées par des objets [`BindableProperty`](xref:Xamarin.Forms.BindableProperty) , ce qui signifie que les propriétés peuvent être des cibles de liaisons de données.

> [!NOTE]
> Lorsque l’alignement se produit, il se produit dans la direction qui produit le moins de mouvement.

### <a name="snap-points-type"></a>Type des points d’alignement

L’énumération [`SnapPointsType`](xref:Xamarin.Forms.SnapPointsType) définit les membres suivants :

- `None` indique que le défilement n’est pas aligné sur les éléments.
- `Mandatory` indique que le contenu s’aligne toujours sur le point d’alignement le plus proche jusqu’où le défilement s’arrête naturellement, le long de la direction d’inertie.
- `MandatorySingle` indique le même comportement que `Mandatory`, mais ne fait que faire défiler un élément à la fois.

Par défaut, sur un [`CarouselView`](xref:Xamarin.Forms.CarouselView), la propriété [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) a la valeur `SnapPointsType.MandatorySingle`, ce qui garantit que le défilement ne fait que faire défiler un élément à la fois.

### <a name="snap-points-alignment"></a>Alignement des points d’alignement

L’énumération [`SnapPointsAlignment`](xref:Xamarin.Forms.SnapPointsAlignment) définit les membres `Start`, `Center` et `End`.

> [!IMPORTANT]
> La valeur de la propriété [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) est respectée uniquement lorsque la propriété [`SnapPointsType`](xref:Xamarin.Forms.ItemsLayout.SnapPointsType) a la valeur `Mandatory` ou `MandatorySingle`.

#### <a name="start"></a>Start

Le membre de `SnapPointsAlignment.Start` indique que les points d’ancrage sont alignés sur le bord de tête des éléments. L’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Start" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Start
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView)horizontalement, l’élément de gauche est aligné sur la gauche de la vue.

#### <a name="center"></a>Center

Le membre `SnapPointsAlignment.Center` indique que les points d’ancrage sont alignés sur le Centre des éléments.

Par défaut, sur un [`CarouselView`](xref:Xamarin.Forms.CarouselView), la propriété [`SnapPointsAlignment`](xref:Xamarin.Forms.ItemsLayout.SnapPointsAlignment) est définie sur `Center`. Toutefois, à des fins d’exhaustivité, l’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="Center" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.Center
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView)horizontalement, l’élément central est aligné sur le centre de la vue.

#### <a name="end"></a>Fin

Le membre de `SnapPointsAlignment.End` indique que les points d’ancrage sont alignés sur le bord de fin des éléments. L’exemple de code XAML suivant montre comment définir ce membre de l’énumération :

```xaml
<CarouselView ItemsSource="{Binding Monkeys}"
              NumberOfSideItems="1">
    <CarouselView.ItemsLayout>
        <LinearItemsLayout Orientation="Horizontal"
                           SnapPointsType="MandatorySingle"
                           SnapPointsAlignment="End" />
    </CarouselView.ItemsLayout>
    ...
</CarouselView>
```

Le code C# équivalent est :

```csharp
CarouselView carouselView = new CarouselView
{
    ItemsLayout = new LinearItemsLayout(ItemsLayoutOrientation.Horizontal)
    {
        SnapPointsType = SnapPointsType.MandatorySingle,
        SnapPointsAlignment = SnapPointsAlignment.End
    },
    // ...
};
```

Lorsqu’un utilisateur fait défiler pour lancer un défilement dans un [`CarouselView`](xref:Xamarin.Forms.CarouselView)horizontalement, l’élément de droite est aligné sur la droite de la vue.

## <a name="related-links"></a>Liens connexes

- [CarouselView (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-carouselviewdemos/)