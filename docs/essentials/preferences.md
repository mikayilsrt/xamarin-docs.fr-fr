---
titre : « Xamarin.Essentials : Preferences » Description : «ce document décrit la classe de préférences dans Xamarin.Essentials , qui enregistre les préférences de l’application dans un magasin de clés/valeurs. Il explique comment utiliser la classe et les types de données qui peuvent être stockés.»
ms. AssetID : AA81BCBD-79BA-448F-942B-BA4415CA50FF auteur : jamesmontemagno ms. Author : Jamont ms. Date : 01/15/2019 ms. Custom : Video No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinessentials-preferences"></a>Xamarin.Essentials: Préférences

La classe **Preferences** permet de stocker les préférences d’application dans un magasin de clés/valeurs.

## <a name="get-started"></a>Prendre en main

[!include[](~/essentials/includes/get-started.md)]

## <a name="using-preferences"></a>Utilisation des préférences

Ajoutez une référence à Xamarin.Essentials dans votre classe :

```csharp
using Xamarin.Essentials;
```

Pour enregistrer la valeur d’une _clé_ donnée dans les préférences :

```csharp
Preferences.Set("my_key", "my_value");
```

Pour récupérer une valeur à partir des préférences, ou une valeur par défaut si elle n’est pas définie :

```csharp
var myValue = Preferences.Get("my_key", "default_value");
```

Pour vérifier si une _clé_ donnée existe dans les préférences :

```csharp
bool hasKey = Preferences.ContainsKey("my_key");
```

Pour supprimer la _clé_ des préférences :

```csharp
Preferences.Remove("my_key");
```

Pour supprimer toutes les préférences :

```csharp
Preferences.Clear();
```

En plus de ces méthodes, vous pouvez utiliser un `sharedName` facultatif qui permet de créer des conteneurs supplémentaires pour des préférences. Lisez les spécificités d’implémentation en fonction de la plateforme, ci-dessous.

## <a name="supported-data-types"></a>Types de données pris en charge

Les types de données suivants sont pris en charge dans **Preferences** :

- **bool**
- **double**
- **int**
- **float**
- **long**
- **string**
- **DateTime**

## <a name="integrate-with-system-settings"></a>Intégrer avec les paramètres système

Les préférences sont stockées en mode natif, ce qui vous permet d’intégrer vos paramètres dans les paramètres système natifs. Suivez les documetnation et les exemples de la plateforme pour les intégrer à la plateforme :

* Apple : [implémentation d’un bundle de paramètres iOS](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/UserDefaults/Preferences/Preferences.html)
* [Exemple de préférences application iOS](https://docs.microsoft.com/samples/xamarin/ios-samples/appprefs/)
* [Paramètres Watchos](https://developer.xamarin.com/guides/ios/watch/working-with/settings/)
* Android : [prise en main avec les écrans de paramètres](https://developer.android.com/guide/topics/ui/settings.html)

## <a name="implementation-details"></a>Informations d’implémentation

Les valeurs de `DateTime` sont stockées dans un format binaire 64 bits (entier long) à l’aide de deux méthodes définies par la `DateTime` classe : la [`ToBinary`](xref:System.DateTime.ToBinary) méthode est utilisée pour `DateTime` encoder la valeur, et la [`FromBinary`](xref:System.DateTime.FromBinary(System.Int64)) méthode décode la valeur. Consultez la documentation de ces méthodes pour connaître les ajustements qui peuvent être apportés aux valeurs décodées quand un `DateTime` stocké n’est pas une valeur UTC (temps universel coordonné).

## <a name="platform-implementation-specifics"></a>Caractéristiques de mise en œuvre de la plateforme

# <a name="android"></a>[Android](#tab/android)

Toutes les données sont stockées dans les [Préférences partagées](https://developer.android.com/training/data-storage/shared-preferences.html). Si aucun `sharedName` n’est spécifié, les préférences partagées par défaut sont utilisées. Sinon, le nom sert à obtenir des préférences partagées **privées** avec le nom spécifié.

# <a name="ios"></a>[iOS](#tab/ios)

[NSUserDefaults](https://docs.microsoft.com/xamarin/ios/app-fundamentals/user-defaults) permet de stocker des valeurs sur les appareils iOS. Si aucun `sharedName` n’est spécifié, `StandardUserDefaults` est utilisé. Sinon, le nom sert à créer un `NSUserDefaults` avec le nom spécifié utilisé pour `NSUserDefaultsType.SuiteName`.

# <a name="uwp"></a>[UWP](#tab/uwp)

[ApplicationDataContainer](https://docs.microsoft.com/uwp/api/windows.storage.applicationdatacontainer) permet de stocker les valeurs sur l’appareil. Si aucun `sharedName` n’est spécifié, `LocalSettings` est utilisé. Sinon, le nom sert à créer un conteneur dans `LocalSettings`.

`LocalSettings`présente également la restriction suivante : le nom de chaque paramètre peut avoir une longueur de 255 caractères au maximum. Chaque paramètre peut avoir une taille maximale de 8 Ko et chaque paramètre composite peut comporter jusqu’à 64 Ko d’octets.

--------------

## <a name="persistence"></a>Persistance

La désinstallation de l’application entraîne la suppression de toutes les _Préférences_. Il existe une exception à cette règle. Il s’agit du cas où les applications ciblent (et s’exécutent sur) Android 6.0 (niveau d’API 23) ou version ultérieure, et utilisent la [__sauvegarde automatique__](https://developer.android.com/guide/topics/data/autobackup). Cette fonctionnalité est activée par défaut et conserve les données de l’application, notamment les __Préférences partagées__, utilisées par l’API de **Préférences**. Vous pouvez désactiver cette fonctionnalité en suivant la [documentation](https://developer.android.com/guide/topics/data/autobackup) de Google.

## <a name="limitations"></a>Limites

Quand vous stockez une chaîne, cette API permet de stocker de petites quantités de texte.  Les performances risquent d’être médiocres si vous essayez de l’utiliser pour stocker de grandes quantités de texte.

## <a name="api"></a>API

- [Code source de la fonctionnalité des préférences](https://github.com/xamarin/Essentials/tree/master/Xamarin.Essentials/Preferences)
- [Documentation sur l’API de préférences](xref:Xamarin.Essentials.Preferences)

## <a name="related-video"></a>Vidéo associée

> [!Video https://channel9.msdn.com/Shows/XamarinShow/Preferences-Essential-API-of-the-Week/player]

[!include[](~/essentials/includes/xamarin-show-essentials.md)]
