---
title : "sélection de l’élément de sélecteur sur iOS" Description : "les spécificités de la plateforme vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plateforme iOS qui contrôle le moment où la sélection de l’élément se produit dans un sélecteur.
ms. Prod : xamarin ms. AssetID : 26B0604A-BD30-49FD-83A6-F0EDFBB0524B ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 10/24/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="picker-item-selection-on-ios"></a>Sélection de l’élément de sélecteur sur iOS

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Ce contrôle spécifique à la plateforme iOS lorsque la sélection d’élément se produit dans un [`Picker`](xref:Xamarin.Forms.Picker) , ce qui permet à l’utilisateur de spécifier la sélection de cet élément lors de l’exploration des éléments du contrôle, ou uniquement une fois que le bouton **terminé** est enfoncé. Il est consommé en XAML en affectant `Picker.UpdateMode` à la propriété jointe une valeur de l' `UpdateMode` énumération :

```xaml
<ContentPage ...
             xmlns:ios="clr-namespace:Xamarin.Forms.PlatformConfiguration.iOSSpecific;assembly=Xamarin.Forms.Core">
    <StackLayout Margin="20">
        <Picker ... Title="Select a monkey" ios:Picker.UpdateMode="WhenFinished">
          ...
        </Picker>
        ...
    </StackLayout>
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.iOSSpecific;
...

picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
```

La `Picker.On<iOS>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur iOS. La `Picker.SetUpdateMode` méthode, dans l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms, est utilisée pour contrôler le moment où la sélection de l’élément se produit, avec l' `UpdateMode` énumération qui fournit deux valeurs possibles :

- `Immediately`: la sélection de l’élément se produit lorsque l’utilisateur parcourt des éléments dans le [`Picker`](xref:Xamarin.Forms.Picker) . Il s’agit du comportement par défaut dans Xamarin.Forms .
- `WhenFinished`: la sélection de l’élément n’est effectuée qu’une fois que l’utilisateur a appuyé sur le bouton **terminé** dans le [`Picker`](xref:Xamarin.Forms.Picker) .

En outre, la `SetUpdateMode` méthode peut être utilisée pour basculer les valeurs d’énumération en appelant la `UpdateMode` méthode, qui retourne le actuel `UpdateMode` :

```csharp
switch (picker.On<iOS>().UpdateMode())
{
    case UpdateMode.Immediately:
        picker.On<iOS>().SetUpdateMode(UpdateMode.WhenFinished);
        break;
    case UpdateMode.WhenFinished:
        picker.On<iOS>().SetUpdateMode(UpdateMode.Immediately);
        break;
}
```

Le résultat est qu’un spécifié `UpdateMode` est appliqué au [`Picker`](xref:Xamarin.Forms.Picker) , qui contrôle le moment où la sélection de l’élément se produit :

[![](picker-selection-images/picker-updatemode.png "Picker UpdateMode Platform-Specific")](picker-selection-images/picker-updatemode-large.png#lightbox "Picker UpdateMode Platform-Specific")

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API iOSSpecific](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific)
