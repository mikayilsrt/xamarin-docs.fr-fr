---
titre : « Xamarin.Essentials convertisseurs de couleurs » : « la classe ColorConverters dans Xamarin.Essentials fournit plusieurs méthodes d’assistance et méthodes d’extension pour fonctionner avec System. Drawing. Color ».
ms. AssetID : B10428D6-89E2-4714-A39F-7E6E626391B2 auteur : jamesmontemagno ms. Author : Jamont ms. Date : 01/06/2020 ms. Custom : Video No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-color-converters"></a>Xamarin.Essentials: Convertisseurs de couleurs

La classe **ColorConverters** dans Xamarin.Essentials fournit plusieurs méthodes d’assistance pour System. Drawing. Color.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-color-converters"></a>Utilisation de convertisseurs de couleurs

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Lorsque vous travaillez avec `System.Drawing.Color` , vous pouvez utiliser les convertisseurs intégrés de Xamarin.Forms pour créer une couleur à partir de TSL, hex ou uint.

```csharp
var blueHex = ColorConverters.FromHex("#3498db");
var blueHsl = ColorConverters.FromHsl(204, 70, 53);
var blueUInt = ColorConverters.FromUInt(3447003);
```

## <a name="using-color-extensions"></a>Utilisation d’extensions de couleurs

Les méthodes d’extension sur `System.Drawing.Color` vous permettent d’appliquer différentes propriétés :

```csharp
var blue = ColorConverters.FromHex("#3498db");

// Multiplies the current alpha by 50%
var blueWithAlpha = blue.MultiplyAlpha(.5f);
```

Il existe plusieurs autres méthodes d’extension, notamment :

- GetComplementary
- MultiplyAlpha
- ToUInt
- WithAlpha
- WithHue
- WithLuminosity
- WithSaturation

## <a name="using-platform-extensions"></a>Utilisation d’extensions de plateforme

De plus, vous pouvez convertir System.Drawing.Color en structure de couleurs spécifique de la plateforme. Ces méthodes peuvent uniquement être appelées à partir de projets iOS, Android et UWP.

```csharp
var system = System.Drawing.Color.FromArgb(255, 52, 152, 219);

// Extension to convert to Android.Graphics.Color, UIKit.UIColor, or Windows.UI.Color
var platform = system.ToPlatformColor();
```

```csharp
var platform = new Android.Graphics.Color(52, 152, 219, 255);

// Back to System.Drawing.Color
var system = platform.ToSystemColor();
```

La méthode `ToSystemColor` s’applique à Android.Graphics.Color, UIKit.UIColor et Windows.UI.Color.

## <a name="api"></a>API

- [Code source des convertisseurs de couleurs](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentation sur les API de convertisseurs de couleurs](xref:Xamarin.Essentials.ColorConverters)
- [Code source des extensions de couleurs](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Types/ColorConverters.shared.cs)
- [Documentation sur les API d’extensions de couleurs](xref:Xamarin.Essentials.ColorExtensions)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Color-Converters-XamarinEssentials-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
