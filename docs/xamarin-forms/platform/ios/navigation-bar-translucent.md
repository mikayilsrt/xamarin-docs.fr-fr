---
titre : « NavigationPage bar translucidité on iOS » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui modifie la transparence de la barre de navigation dans un NavigationPage.
ms. Prod : xamarin ms. AssetID : 1150941B-56DB-4781-BE36-A4C4F9F2C500 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="navigationpage-bar-translucency-on-ios"></a>NavigationPage bar translucidité sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce spécifique à la plateforme iOS est utilisé pour modifier la transparence de la barre de navigation sur un [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) et est consommé en XAML en affectant [`NavigationPage.IsNavigationBarTranslucent`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage.IsNavigationBarTranslucentProperty) une valeur à la propriété jointe `boolean` :

```xaml
<NavigationPage ...
                xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core"
                BackgroundColor="Blue"
                ios:NavigationPage.IsNavigationBarTranslucent="true">
  ...
</NavigationPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

(App.Current.MainPage as Xamarin.Forms.NavigationPage).BackgroundColor = Color.Blue;
(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().EnableTranslucentNavigationBar();
```

La `NavigationPage.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. [ `NavigationPage.EnableTranslucentNavigationBar` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. EnableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})), dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisé pour rendre la barre de navigation translucide. En outre, la [`NavigationPage`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific.NavigationPage) classe de l' `Xamarin.Forms.PlatformConfiguration.iOSSpecific` espace de noms possède également un [ `DisableTranslucentNavigationBar` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. DisableTranslucentNavigationBar ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage})) qui restaure la barre de navigation à son état par défaut et un [ `SetIsNavigationBarTranslucent` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. SetIsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}, System. Boolean)) qui peut être utilisée pour activer ou désactiver la transparence de la barre de navigation en appelant la méthode [ `IsNavigationBarTranslucent` ] (XREF : Xamarin.Forms . PlatformConfiguration. iOSSpecific. NavigationPage. IsNavigationBarTranslucent ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. iOS, Xamarin.Forms . NavigationPage}))) :

```csharp
(App.Current.MainPage as Xamarin.Forms.NavigationPage)
  .On<iOS>()
  .SetIsNavigationBarTranslucent(!(App.Current.MainPage as Xamarin.Forms.NavigationPage).On<iOS>().IsNavigationBarTranslucent());
```

Le résultat est que la transparence de la barre de navigation peut être modifiée :

![](navigation-bar-translucent-images/translucent-navigation-bar.png "Translucent Navigation Bar Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
