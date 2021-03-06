---
title : « gestion des fichiers dans Xamarin.Forms «Description » : la gestion des fichiers avec Xamarin.Forms peut être réalisée à l’aide du code dans une bibliothèque .NET standard, ou à l’aide de ressources incorporées.»
ms. Prod : xamarin ms. AssetID : 9987C3F6-5F04-403B-BBB4-ECB024EA6CC8 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 06/21/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="file-handling-in-xamarinforms"></a>Gestion des fichiers dansXamarin.Forms

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles)

_La gestion des fichiers avec Xamarin.Forms peut être effectuée à l’aide de code dans une bibliothèque .NET standard, ou à l’aide de ressources incorporées._

## <a name="overview"></a>Vue d’ensemble

Xamarin.Formsle code s’exécute sur plusieurs plateformes, chacune ayant son propre système de fichiers. Auparavant, la lecture et l’écriture des fichiers étaient plus faciles à effectuer avec les API de fichiers natives à chaque plateforme. Pour distribuer des fichiers de données à l’aide d’une application, une solution plus simple consiste à utiliser des ressources incorporées. Toutefois, avec .NET Standard 2.0, il est possible de partager du code d’accès de fichier dans les bibliothèques .NET Standard.

Pour plus d’informations sur la gestion des fichiers image, consultez [Utilisation des images](~/xamarin-forms/user-interface/images.md).

## <a name="saving-and-loading-files"></a>Enregistrement et chargement de fichiers

Les classes `System.IO` peuvent être utilisées pour accéder au système de fichiers de chaque plateforme. La classe `File` permet de créer, supprimer et lire des fichiers, et la classe `Directory` permet de créer, supprimer ou énumérer le contenu des répertoires. Vous pouvez également utiliser les sous-classes `Stream`, qui fournissent un plus grand contrôle sur les opérations de fichier (par exemple, la compression ou la recherche de position dans un fichier).

Un fichier texte peut être écrit à l’aide de la méthode `File.WriteAllText` :

```csharp
File.WriteAllText(fileName, text);
```

Un fichier texte peut être lu à l’aide de la méthode `File.ReadAllText` :

```csharp
string text = File.ReadAllText(fileName);
```

En outre, la méthode `File.Exists` détermine si le fichier spécifié existe :

```csharp
bool doesExist = File.Exists(fileName);
```

Le chemin d’accès du fichier sur chaque plateforme peut être déterminé à partir d’une bibliothèque de .NET Standard en utilisant une valeur de l' [`Environment.SpecialFolder`](xref:System.Environment.SpecialFolder) énumération comme premier argument de la `Environment.GetFolderPath` méthode. Il peut être combiné avec un nom de fichier, à l’aide de la méthode `Path.Combine` :

```csharp
string fileName = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.LocalApplicationData), "temp.txt");
```

Ces opérations sont démontrées dans l’exemple d’application, qui inclut une page qui enregistre et charge du texte :

[![Enregistrement et chargement de texte](files-images/saveandload-sml.png "Enregistrement et chargement de fichiers dans l’application")](files-images/saveandload.png#lightbox "Enregistrement et chargement de fichiers dans l’application")

## <a name="loading-files-embedded-as-resources"></a>Chargement de fichiers incorporés en tant que ressources

Pour incorporer un fichier dans un assembly **.NET Standard**, créez ou ajoutez un fichier, puis vérifiez que **Action de génération = EmbeddedResource**.

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Configuration de l’action de génération de ressource incorporée](files-images/vs-embeddedresource-sml.png "Définition de EmbeddedResource BuildAction")](files-images/vs-embeddedresource.png#lightbox "Définition de EmbeddedResource BuildAction")

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Fichier texte incorporé dans la bibliothèque .NET standard, configuration de l’action de génération de ressource incorporée](files-images/xs-embeddedresource-sml.png "Définition de EmbeddedResource BuildAction")](files-images/xs-embeddedresource.png#lightbox "Définition de EmbeddedResource BuildAction")

-----

`GetManifestResourceStream` est utilisé pour accéder au fichier incorporé à l’aide de son **ID de ressource**. Par défaut, l’ID de ressource est le nom préfixé avec l’espace de noms par défaut pour le projet dans lequel il est incorporé. dans ce cas, l’assembly est **WorkingWithFiles** et le nom de fichier est **LibTextResource. txt**, donc l’ID de ressource est `WorkingWithFiles.LibTextResource.txt` .

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.LibTextResource.txt");
string text = "";
using (var reader = new System.IO.StreamReader (stream))
{  
    text = reader.ReadToEnd ();
}
```

La variable`text` peut ensuite être utilisée pour afficher le texte ou l’utiliser dans le code. Cette capture d’écran de l’[exemple d’application](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles) montre le texte affiché dans un contrôle `Label`.

 [![Fichier texte incorporé dans la bibliothèque .NET standard](files-images/pcltext-sml.png "Fichier texte incorporé dans .NET Standard bibliothèque affichée dans l’application")](files-images/pcltext.png#lightbox "Fichier texte incorporé dans .NET Standard bibliothèque affichée dans l’application")

Le chargement et la désérialisation d’un document XML sont tout aussi simples. Le code suivant montre un fichier XML qui est chargé et désérialisé à partir d’une ressource, puis lié à un `ListView` pour l’affichage. Le fichier XML contient un tableau d’objets `Monkey` (la classe est définie dans l’exemple de code).

```csharp
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(LoadResourceText)).Assembly;
Stream stream = assembly.GetManifestResourceStream("WorkingWithFiles.LibXmlResource.xml");
List<Monkey> monkeys;
using (var reader = new System.IO.StreamReader (stream)) {
    var serializer = new XmlSerializer(typeof(List<Monkey>));
    monkeys = (List<Monkey>)serializer.Deserialize(reader);
}
var listView = new ListView ();
listView.ItemsSource = monkeys;
```

 [![Fichier XML incorporé dans la bibliothèque .NET standard, affiché dans ListView](files-images/pclxml-sml.png "Fichier XML incorporé dans la bibliothèque .NET standard affichée dans ListView")](files-images/pclxml.png#lightbox "Fichier XML incorporé dans la bibliothèque .NET standard affichée dans ListView")

## <a name="embedding-in-shared-projects"></a>Incorporation dans des projets partagés

Les projets partagés peuvent également contenir des fichiers agissant comme des ressources incorporées. Toutefois, étant donné que le contenu d’un projet partagé est compilé dans les projets de référence, le préfixe utilisé pour les ID de ressource des fichiers incorporés peut changer. Cela signifie que l’ID de ressource de chaque fichier incorporé peut être différent pour chaque plateforme.

Il existe deux solutions à ce problème de projet partagé :

- **Synchroniser les projets** : modifiez les propriétés du projet pour que chaque plateforme utilise les **mêmes** nom d’assembly et espace de noms par défaut. Cette valeur peut ensuite être « codée en dur » comme préfixe pour les ID de ressource incorporée dans le projet partagé.
- **Directives de compilateur #if** : utilisez des directives de compilateur pour définir le préfixe d’ID de ressource approprié, et utilisez cette valeur pour construire de façon dynamique l’ID de ressource approprié.

Voici le code qui illustre la deuxième option : Les directives de compilateur sont utilisées pour sélectionner le préfixe de ressource codé en dur (qui est normalement le même que l’espace de noms par défaut du projet de référence). La variable `resourcePrefix` est ensuite utilisée pour créer un ID de ressource valide, en le concaténant avec le nom de fichier de la ressource incorporée.

```csharp
#if __IOS__
var resourcePrefix = "WorkingWithFiles.iOS.";
#endif
#if __ANDROID__
var resourcePrefix = "WorkingWithFiles.Droid.";
#endif

Debug.WriteLine("Using this resource prefix: " + resourcePrefix);
// note that the prefix includes the trailing period '.' that is required
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
Stream stream = assembly.GetManifestResourceStream
    (resourcePrefix + "SharedTextResource.txt");
```

### <a name="organizing-resources"></a>Organisation des ressources

Les exemples ci-dessus supposent que le fichier est incorporé dans la racine du projet bibliothèque .NET Standard, auquel cas l’ID de ressource est au format **Namespace.Filename.Extension**, tel que `WorkingWithFiles.LibTextResource.txt` et `WorkingWithFiles.iOS.SharedTextResource.txt`.

Il est possible d’organiser les ressources incorporées dans des dossiers. Lorsqu’une ressource incorporée est placée dans un dossier, le nom du dossier est intégré à l’ID de ressource (séparé par des points). Le format de l’ID de ressource devient alors **EspaceDeNoms.Dossier.NomDeFichier.Extension**. Si vous placez les fichiers de l’exemple d’application dans un dossier **MyFolder**, les ID de ressource correspondants deviennent `WorkingWithFiles.MyFolder.LibTextResource.txt` et `WorkingWithFiles.iOS.MyFolder.SharedTextResource.txt`.

### <a name="debugging-embedded-resources"></a>Débogage des ressources incorporées

Il est parfois difficile de comprendre pourquoi une ressource n’est pas chargée. Pour vérifier que les ressources sont correctement configurées, vous pouvez ajouter temporairement le code de débogage suivant à une application. Il affichera toutes les ressources incorporées connues d’un assembly donné dans le volet **Erreurs** pour vous aider à déboguer les problèmes de chargement des ressources.

```csharp
using System.Reflection;
// ...
// use for debugging, not in released app code!
var assembly = IntrospectionExtensions.GetTypeInfo(typeof(SharedPage)).Assembly;
foreach (var res in assembly.GetManifestResourceNames()) {
    System.Diagnostics.Debug.WriteLine("found resource: " + res);
}
```

## <a name="summary"></a>Résumé

Cet article vous a montré certaines opérations de fichier simples pour enregistrer et charger du texte sur l’appareil, et pour charger des ressources incorporées. Avec .NET Standard 2.0, il est possible de partager du code d’accès de fichier dans les bibliothèques .NET Standard.

## <a name="related-links"></a>Liens connexes

- [FilesSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/workingwithfiles)
- [Xamarin.FormsExtraits](https://github.com/xamarin/xamarin-forms-samples)
- [Utilisation du système de fichiers dans Xamarin.iOS](~/ios/app-fundamentals/file-system.md)
