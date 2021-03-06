---
titre : « SearchBar style on iOS » Description : «les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le propre à la plateforme iOS qui contrôle si un SearchBar a un arrière-plan.»
ms. Prod : xamarin ms. AssetID : 3D512DD6-078E-4BC6-926E-62BA6F4DE640 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 03/05/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="searchbar-style-on-ios"></a>Style SearchBar sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Cette plateforme iOS détermine si un [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un arrière-plan. Il est consommé en XAML en affectant `SearchBar.SearchBarStyle` à la propriété pouvant être liée la valeur de l' `UISearchBarStyle` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout>
        <SearchBar ios:SearchBar.SearchBarStyle="Minimal"
                   Placeholder="Enter search term" />
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

SearchBar searchBar = new SearchBar { Placeholder = "Enter search term" };
searchBar.On<iOS>().SetSearchBarStyle(UISearchBarStyle.Minimal);
```

La `SearchBar.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `SearchBar.SetSearchBarStyle` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler si le [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un arrière-plan. L' `UISearchBarStyle` énumération fournit trois valeurs possibles :

- `Default`indique que le [`SearchBar`](xref:Xamarin.Forms.SearchBar) a le style par défaut. Il s’agit de la valeur par défaut de la `SearchBar.SearchBarStyle` propriété pouvant être liée.
- `Prominent`indique que le [`SearchBar`](xref:Xamarin.Forms.SearchBar) a un arrière-plan translucide et que le champ de recherche est opaque.
- `Minimal`indique que n' [`SearchBar`](xref:Xamarin.Forms.SearchBar) a pas d’arrière-plan et que le champ de recherche est transparent.

En outre, la `SearchBar.GetSearchBarStyle` méthode peut être utilisée pour retourner le `UISearchBarStyle` appliqué au `SearchBar` .

Le résultat est qu’un `UISearchBarStyle` membre spécifié est appliqué à un [`SearchBar`](xref:Xamarin.Forms.SearchBar) , qui contrôle si le `SearchBar` a un arrière-plan :

![Capture d’écran des styles SearchBar, sur iOS](searchbar-style-images/searchbar-styles.png "Styles SearchBar sur iOS")

Les captures d’écran suivantes montrent les `UISearchBarStyle` membres appliqués aux [`SearchBar`](xref:Xamarin.Forms.SearchBar) objets dont la `BackgroundColor` propriété est définie :

![Capture d’écran des styles SearchBar avec la couleur d’arrière-plan, sur iOS](searchbar-style-images/searchbar-background-styles.png "Styles SearchBar avec couleur d’arrière-plan sur iOS")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
