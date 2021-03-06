---
titre : « installation de Xamarin Preview sur Windows » Description : « ce document décrit comment installer une version préliminaire de Xamarin sur Visual Studio 2019 à l’aide de la version préliminaire du canal. »
ms. Prod : xamarin ms. AssetID : 9F730444-06E8-4B3F-8A19-CA95CD484FFA auteur : conceptdev ms. Author : crdun ms. Date : 03/20/2018 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="installing-xamarin-preview-on-windows"></a>Installation de la préversion Xamarin sur W

Visual Studio 2019 et Visual Studio 2017 ne prennent pas en charge les canaux alpha, bêta et stables de la même manière que les versions antérieures. Au lieu de cela, il n’existe que deux options :

- **Mise en production** : équivalente au canal _Stable_ dans Visual Studio pour Mac
- **Préversion** : équivalent aux canaux _Alpha_ et _Bêta_ dans Visual Studio pour Mac

> [!TIP]
> Pour tester les fonctionnalités de préversion, vous devez [télécharger le programme d’installation de Visual Studio Preview](https://visualstudio.microsoft.com/vs/preview/), qui permet d’installer des préversions (**Preview**) de Visual Studio côte à côte avec la version stable (version release). Pour plus d’informations sur les nouveautés de Visual Studio 2019, consultez les [notes de publication](https://docs.microsoft.com/visualstudio/releases/2019/release-notes).

La Préversion de Visual Studio peut inclure Des Préversions correspondantes de la fonctionnalité Xamarin, y compris :

- Xamarin.Forms
- Xamarin.iOS
- Xamarin.Android
- Xamarin Profiler
- Xamarin Inspector
- Xamarin Remote iOS Simulator

La capture d’écran **Programme d’installation de la préversion** ci-dessous affiche les options Préversion et Mise en production (Notez les numéros de version en gris : la version 15.0 est mise en production et la version 15.1 est une Préversion) :

![Programme d’installation affichant les options de préversion](windows-images/vs2017-installer.jpg)

Pendant le processus d’installation, un **Surnom de l’Installation** peuvent être appliqués à l’installation côte à côte (afin de pouvoir les distinguer dans le menu Démarrer), comme indiqué ci-dessous :

[![modifier le pseudo avant l’installation](windows-images/vs2017-nickname-sml.png "modifier le pseudo avant l’installation")](windows-images/vs2017-nickname.png#lightbox)

### <a name="uninstalling-visual-studio-2019-preview"></a>Désinstallation de Visual Studio 2019 Preview

Vous devez également utiliser le programme **Visual Studio Installer** pour désinstaller les préversions de Visual Studio 2019. Lisez le [guide de désinstallation Xamarin](uninstalling-xamarin.md#uninstallvs2017) pour plus d’informations.
