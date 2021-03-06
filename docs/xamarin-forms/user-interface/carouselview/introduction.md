---
titre : « Xamarin.Forms CarouselView Introduction » Description : « CarouselView est une vue pour la présentation des données dans une disposition à défilement, où les utilisateurs peuvent parcourir pour se déplacer dans une collection d’éléments ».
ms. Prod : xamarin ms. AssetID : 2a96e4bd-c29b-4658-bb4c-ab00872b0f8f ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/08/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-carouselview-introduction"></a>Xamarin.FormsPrésentation de CarouselView

![](~/media/shared/preview.png "This API is currently pre-release")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)est une vue qui permet de présenter des données dans une disposition à défilement, où les utilisateurs peuvent effectuer un balayage pour se déplacer dans une collection d’éléments. Par défaut, `CarouselView` affiche ses éléments dans une orientation horizontale. Un seul élément s’affiche à l’écran, avec des mouvements de balayage qui entraînent la navigation vers l’avant et vers l’arrière dans la collection d’éléments. En outre, des indicateurs peuvent s’afficher pour représenter chaque élément dans le `CarouselView` :

[![Capture d’écran d’un CarouselView et d’un IndicatorView, sur iOS et Android](populate-data-images/indicators.png "Cercles IndicatorView")](populate-data-images/indicators-large.png#lightbox "Cercles IndicatorView")

[`CarouselView`](xref:Xamarin.Forms.CarouselView)est disponible dans Xamarin.Forms 4,3. Toutefois, il est actuellement expérimental et ne peut être utilisé qu’en ajoutant la ligne de code suivante à votre `AppDelegate` classe sur iOS, ou à votre `MainActivity` classe sur Android, avant d’appeler `Forms.Init` :

```csharp
Forms.SetFlags("CarouselView_Experimental");
```

> [!IMPORTANT]
> [`CarouselView`](xref:Xamarin.Forms.CarouselView)est disponible sur iOS et Android, mais certaines fonctionnalités peuvent uniquement être partiellement disponibles sur le plateforme Windows universelle.

[`CarouselView`](xref:Xamarin.Forms.CarouselView)partage une grande partie de son implémentation avec [`CollectionView`](xref:Xamarin.Forms.CollectionView) . Toutefois, les deux contrôles ont des cas d’usage différents. `CollectionView`est généralement utilisé pour présenter des listes de données de n’importe quelle longueur, tandis que `CarouselView` est utilisé en général pour mettre en évidence les informations dans une liste de longueur limitée.
