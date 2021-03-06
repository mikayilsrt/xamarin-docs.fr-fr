---
title: 'Procédure pas à pas : enregistrement de l’état de l’activité'
description: Nous avons abordé la théorie sous-jacente à l’enregistrement de l’État dans le Guide de cycle de vie des activités. à présent, examinons un exemple.
ms.prod: xamarin
ms.assetid: A6090101-67C6-4BDD-9416-F2FB74805A87
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/01/2018
ms.openlocfilehash: e449e6a62d0c8ca283f20c689477c1f1482611c5
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73017004"
---
# <a name="walkthrough---saving-the-activity-state"></a>Procédure pas à pas : enregistrement de l’état de l’activité

_Nous avons abordé la théorie sous-jacente à l’enregistrement de l’État dans le Guide de cycle de vie des activités. à présent, examinons un exemple._

## <a name="activity-state-walkthrough"></a>Procédure pas à pas d’état d’activité

Nous allons ouvrir le projet **ActivityLifecycle_Start** (dans l’exemple [ActivityLifecycle](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle) ), le générer et l’exécuter. Il s’agit d’un projet très simple qui a deux activités pour illustrer le cycle de vie de l’activité et la façon dont les différentes méthodes de cycle de vie sont appelées. Lorsque vous démarrez l’application, l’écran de `MainActivity` s’affiche :

[![un écran de l’activité](saving-state-images/01-activity-a-sml.png)](saving-state-images/01-activity-a.png#lightbox)

### <a name="viewing-state-transitions"></a>Affichage des transitions d’État

Chaque méthode de cet exemple écrit dans la fenêtre de sortie de l’application IDE pour indiquer l’état de l’activité. (Pour ouvrir la fenêtre sortie dans Visual Studio, tapez **Ctrl-Alt-O**; pour ouvrir la fenêtre sortie dans Visual Studio pour Mac, cliquez sur **afficher les PAD > > sortie**de l’application.)

Lorsque l’application démarre pour la première fois, la fenêtre sortie affiche les changements d’état de l' *activité A*: 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Lorsque nous cliquons sur le bouton **Start Activity b (démarrer l’activité b** ), l’activité *A* pause et Stop alors que l' *activité b* passe en revue les changements d’État : 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.SecondActivity] Activity B - OnCreate
[ActivityLifecycle.SecondActivity] Activity B - OnStart
[ActivityLifecycle.SecondActivity] Activity B - OnResume
[ActivityLifecycle.MainActivity] Activity A - OnStop
```

Par conséquent, l' *activité B* est démarrée et s’affiche à la place de l' *activité a*: 

[écran![de l’activité B](saving-state-images/02-activity-b-sml.png)](saving-state-images/02-activity-b.png#lightbox)

Lorsque nous cliquons sur le bouton **précédent** , l' *activité B* est détruite et l' *activité a* reprend : 

```shell
[ActivityLifecycle.SecondActivity] Activity B - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnRestart
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
[ActivityLifecycle.SecondActivity] Activity B - OnStop
[ActivityLifecycle.SecondActivity] Activity B - OnDestroy
```

### <a name="adding-a-click-counter"></a>Ajout d’un compteur de clic

Ensuite, nous allons modifier l’application afin de disposer d’un bouton qui compte et affiche le nombre de clics effectués. Tout d’abord, nous allons ajouter une variable d’instance de `_counter` à `MainActivity`:

```csharp
int _counter = 0;
```

Ensuite, nous allons modifier le fichier de disposition **Resource/layout/main. AXML** et ajouter un nouveau `clickButton` qui affiche le nombre de fois où l’utilisateur a cliqué sur le bouton. Le **AXML principal** qui en résulte doit ressembler à ce qui suit : 

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <Button
        android:id="@+id/myButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/mybutton_text" />
    <Button
        android:id="@+id/clickButton"
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/counterbutton_text" />
</LinearLayout>
```

Nous allons ajouter le code suivant à la fin de la méthode [OnCreate](xref:Android.App.Activity.OnCreate*) dans `MainActivity` &ndash; ce code gère les événements Click à partir du `clickButton`:

```csharp
var clickbutton = FindViewById<Button> (Resource.Id.clickButton);
clickbutton.Text = Resources.GetString (
    Resource.String.counterbutton_text, _counter);
clickbutton.Click += (object sender, System.EventArgs e) =>
{
    _counter++;
    clickbutton.Text = Resources.GetString (
        Resource.String.counterbutton_text, _counter);
} ;
```

Lorsque nous générons et exécutons à nouveau l’application, un nouveau bouton apparaît, qui incrémente et affiche la valeur de `_counter` à chaque clic :

[![Add Touch Count](saving-state-images/03-touched-sml.png)](saving-state-images/03-touched.png#lightbox)

Toutefois, lorsque vous faites pivoter l’appareil en mode paysage, ce nombre est perdu :

[![la rotation en mode paysage rétablit le nombre à zéro](saving-state-images/05-rotate-nosave-sml.png)](saving-state-images/05-rotate-nosave.png#lightbox)

En examinant la sortie de l’application, nous voyons que l' *activité a* a été suspendue, arrêtée, détruite, recréée, redémarrée, puis reprise lors de la rotation du mode portrait au mode paysage : 

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Étant donné que l' *activité a* est détruite et recréée lorsque l’appareil est pivoté, son état d’instance est perdu. Ensuite, nous allons ajouter du code pour enregistrer et restaurer l’état de l’instance.

### <a name="adding-code-to-preserve-instance-state"></a>Ajout de code pour conserver l’état de l’instance

Nous allons ajouter une méthode pour `MainActivity` pour enregistrer l’état de l’instance. Avant *la destruction de l’activité a* , Android appelle automatiquement [OnSaveInstanceState](xref:Android.App.Activity.OnSaveInstanceState*) et transmet un [Bundle](xref:Android.OS.Bundle) que nous pouvons utiliser pour stocker l’état de l’instance. Nous allons l’utiliser pour enregistrer le nombre de clic sous la forme d’une valeur entière :

```csharp
protected override void OnSaveInstanceState (Bundle outState)
{
    outState.PutInt ("click_count", _counter);
    Log.Debug(GetType().FullName, "Activity A - Saving instance state");

    // always call the base implementation!
    base.OnSaveInstanceState (outState);    
}
```

Lorsque l' *activité a* est recréée et reprise, Android transmet ce `Bundle` dans notre méthode `OnCreate`. Nous allons ajouter du code à `OnCreate` pour restaurer la valeur de `_counter` à partir de l' `Bundle`passée. Ajoutez le code suivant juste avant la ligne où `clickbutton` est définie : 

```csharp
if (bundle != null)
{
    _counter = bundle.GetInt ("click_count", 0);
    Log.Debug(GetType().FullName, "Activity A - Recovered instance state");
}
```

Générez et exécutez à nouveau l’application, puis cliquez plusieurs fois sur le deuxième bouton. Lorsque nous faisons pivoter l’appareil en mode paysage, le nombre est préservé !

[![la rotation de l’écran affiche le nombre de quatre conservés](saving-state-images/06-rotate-save-sml.png)](saving-state-images/06-rotate-save.png#lightbox)

Jetons un coup d’œil à la fenêtre sortie pour voir ce qui s’est passé :

```shell
[ActivityLifecycle.MainActivity] Activity A - OnPause
[ActivityLifecycle.MainActivity] Activity A - Saving instance state
[ActivityLifecycle.MainActivity] Activity A - OnStop
[ActivityLifecycle.MainActivity] Activity A - On Destroy

[ActivityLifecycle.MainActivity] Activity A - OnCreate
[ActivityLifecycle.MainActivity] Activity A - Recovered instance state
[ActivityLifecycle.MainActivity] Activity A - OnStart
[ActivityLifecycle.MainActivity] Activity A - OnResume
```

Avant l’appel de la méthode [OnStop](xref:Android.App.Activity.OnStop) , la nouvelle méthode `OnSaveInstanceState` a été appelée pour enregistrer la valeur `_counter` dans un `Bundle`. Android nous a transmis ce `Bundle` lorsqu’il a appelé notre méthode `OnCreate`, et nous avons pu l’utiliser pour restaurer la valeur de `_counter` à l’emplacement où nous nous sommes arrêtés.

## <a name="summary"></a>Récapitulatif

Dans ce Walkthough, nous avons utilisé notre connaissance du cycle de vie de l’activité pour conserver les données d’État.

## <a name="related-links"></a>Liens associés

- [ActivityLifecycle (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/activitylifecycle)
- [Cycle de vie des activités](~/android/app-fundamentals/activity-lifecycle/index.md)
- [Activité Android](xref:Android.App.Activity)
