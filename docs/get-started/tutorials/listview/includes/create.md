---
ms.openlocfilehash: c864f5d97e72a58e4b3c28fdeb97b7cfaea75992
ms.sourcegitcommit: 89b3e383a37db5b940f0c63bbfe9cb806dc7d5d1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81389917"
---
# <a name="visual-studio"></a>[Visual Studio](#tab/vswin)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio 2019 (dernière version) sur lequel est installée la charge de travail **Développement mobile en .NET**. Vous aurez également besoin d’un Mac couplé pour générer l’application du didacticiel sur iOS. Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md). Pour plus d’informations sur la connexion de Visual Studio 2019 à un hôte de build Mac, consultez l’article [Appairer avec un Mac pour le développement Xamarin.iOS](~/ios/get-started/installation/windows/connecting-to-mac/index.md).

1. Lancez Visual Studio et créez une nouvelle application Xamarin.Forms vierge nommée **ListViewTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **ListViewTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans **l’Explorateur de solutions**, dans le projet **ListViewTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ListViewTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <ListView>
                <ListView.ItemsSource>
                    <x:Array Type="{x:Type x:String}">
                      <x:String>Baboon</x:String>
                      <x:String>Capuchin Monkey</x:String>
                      <x:String>Blue Monkey</x:String>
                      <x:String>Squirrel Monkey</x:String>
                      <x:String>Golden Lion Tamarin</x:String>
                      <x:String>Howler Monkey</x:String>
                      <x:String>Japanese Macaque</x:String>
                    </x:Array>
                </ListView.ItemsSource>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`ListView`](xref:Xamarin.Forms.ListView) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propriété [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) spécifie les éléments à afficher, lesquels sont définis dans un tableau de chaînes.

1. Dans la barre d’outils Visual Studio, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application à l’intérieur du simulateur iOS distant ou de l’émulateur Android de votre choix :

    [![Capture d’écran d’une vue Liste, sur iOS et Android](../images/create-listview.png "Vue Liste comprenant des données")](../images/create-listview-large.png#lightbox "Vue Liste comprenant des données")

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/vsmac)

Pour suivre ce didacticiel, vous devez disposer de Visual Studio pour Mac (dernière version) avec une prise en charge des plateformes iOS et Android. Vous aurez également besoin de Xcode (dernière version). Pour plus d’informations sur l’installation de la plateforme Xamarin, consultez [Installation de Xamarin](~/get-started/installation/index.md).

1. Lancez Visual Studio pour Mac et créez une nouvelle application Xamarin.Forms vierge nommée **ListViewTutorial**. Assurez-vous que l’application utilise bien .NET Standard comme mécanisme de code partagé.

    > [!IMPORTANT]
    > Pour les extraits C# et XAML de ce didacticiel, la solution doit se nommer **ListViewTutorial**. L’utilisation d’un autre nom entraîne des erreurs de build quand vous copiez le code à partir de ce didacticiel dans la solution.

    Pour plus d’informations sur la bibliothèque .NET Standard créée, consultez la rubrique [Anatomie d’une application Xamarin.Forms](~/get-started/first-app/index.md) dans l’article [Immersion dans Xamarin.Forms - démarrage rapide](~/get-started/first-app/index.md).

1. Dans le **Panneau Solutions**, dans le projet **ListViewTutorial**, double-cliquez sur **MainPage.xaml** pour l’ouvrir. Puis, dans **MainPage.xaml**, supprimez tout le code du modèle et remplacez-le par le code suivant :

    ```xaml
    <?xml version="1.0" encoding="utf-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                 xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                 x:Class="ListViewTutorial.MainPage">
        <StackLayout Margin="20,35,20,20">
            <ListView>
                <ListView.ItemsSource>
                    <x:Array Type="{x:Type x:String}">
                      <x:String>Baboon</x:String>
                      <x:String>Capuchin Monkey</x:String>
                      <x:String>Blue Monkey</x:String>
                      <x:String>Squirrel Monkey</x:String>
                      <x:String>Golden Lion Tamarin</x:String>
                      <x:String>Howler Monkey</x:String>
                      <x:String>Japanese Macaque</x:String>
                    </x:Array>
                </ListView.ItemsSource>
            </ListView>
        </StackLayout>
    </ContentPage>
    ```

    Ce code définit de manière déclarative l’interface utilisateur de la page, qui comprend un élément [`ListView`](xref:Xamarin.Forms.ListView) dans [`StackLayout`](xref:Xamarin.Forms.StackLayout). La propriété [`ListView.ItemsSource`](xref:Xamarin.Forms.ItemsView`1.ItemsSource) spécifie les éléments à afficher, lesquels sont définis dans un tableau de chaînes.

1. Dans la barre d’outils Visual Studio pour Mac, appuyez sur le bouton **Démarrer** (le bouton triangulaire qui ressemble à un bouton de lecture) pour lancer l’application dans le simulateur iOS ou dans l’émulateur Android de votre choix :

    [![Capture d’écran d’une vue Liste, sur iOS et Android](../images/create-listview.png "Vue Liste comprenant des données")](../images/create-listview-large.png#lightbox "Vue Liste comprenant des données")
