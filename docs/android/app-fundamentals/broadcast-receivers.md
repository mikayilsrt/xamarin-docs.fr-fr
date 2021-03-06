---
title: Récepteurs de diffusion dans Xamarin. Android
description: Cette section explique comment utiliser un récepteur de diffusion.
ms.prod: xamarin
ms.assetid: B2727160-12F2-43EE-84B5-0B15C8FCF4BD
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 04/20/2018
ms.openlocfilehash: 2dd0a9a98c05204606f157cd9cd1028582af375b
ms.sourcegitcommit: 5d75830fca6f2e58452d4445806e3653a3145dc0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75870907"
---
# <a name="broadcast-receivers-in-xamarinandroid"></a>Récepteurs de diffusion dans Xamarin. Android

_Cette section explique comment utiliser un récepteur de diffusion._

## <a name="broadcast-receiver-overview"></a>Présentation du récepteur de diffusion

Un _récepteur de diffusion_ est un composant Android qui permet à une application de répondre aux messages (un [`Intent`](xref:Android.Content.Intent)Android) qui sont diffusés par le système d’exploitation Android ou par une application. Les diffusions suivent un modèle de _publication-abonnement_ &ndash; un événement entraîne la publication et la réception d’une diffusion par les composants qui sont intéressés par l’événement.

Android identifie deux types de diffusions :

- **Diffusion explicite** &ndash; ces types de diffusion ciblent une application spécifique. L’utilisation la plus courante d’une diffusion explicite consiste à démarrer une activité. Exemple de diffusion explicite lorsqu’une application doit composer un numéro de téléphone ; il distribue une intention qui cible l’application téléphonique sur Android et transmet le numéro de téléphone à composer. Android achemine ensuite l’intention vers l’application téléphonique.
- **Diffusion implicite** &ndash; ces diffusions sont distribuées à toutes les applications sur l’appareil. La `ACTION_POWER_CONNECTED` intention est un exemple de diffusion implicite. Cette intention est publiée à chaque fois qu’Android détecte que la batterie de l’appareil est en charge. Android acheminera cet objectif vers toutes les applications qui se sont inscrites pour cet événement.

Le récepteur de diffusion est une sous-classe du type de `BroadcastReceiver` et il doit remplacer la méthode [`OnReceive`](xref:Android.Content.BroadcastReceiver.OnReceive*) . Android exécutera `OnReceive` sur le thread principal. cette méthode devrait donc être conçue pour s’exécuter rapidement. Soyez vigilant lorsque vous générez des threads dans `OnReceive`, car Android peut mettre fin au processus lorsque la méthode se termine. Si un récepteur de diffusion doit exécuter un travail de longue durée, il est recommandé de planifier un _travail_ à l’aide du `JobScheduler` ou du _répartiteur de tâches Firebase_. La planification d’un travail avec un travail sera traitée dans un guide distinct.

Un _filtre d’intention_ est utilisé pour inscrire un récepteur de diffusion afin qu’Android puisse acheminer correctement les messages. Le filtre d’intention peut être spécifié au moment de l’exécution (on parle parfois de _récepteur inscrit au contexte_ ou en tant qu' _inscription dynamique_) ou il peut être défini de manière statique dans le manifeste Android (un _récepteur inscrit au manifeste_). Xamarin. Android fournit un C# attribut, `IntentFilterAttribute`, qui inscrira de manière statique le filtre d’intention (ce sujet sera abordé plus en détail plus loin dans ce guide). Depuis Android 8,0, il n’est pas possible pour une application de s’inscrire de manière statique pour une diffusion implicite.

La principale différence entre le récepteur inscrit dans le manifeste et le récepteur inscrit dans le contexte est qu’un récepteur inscrit au contexte répond uniquement aux diffusions pendant qu’une application est en cours d’exécution, tandis qu’un récepteur inscrit au manifeste peut répondre à les diffusions même si l’application n’est peut-être pas en cours d’exécution.  

Il existe deux ensembles d’API pour la gestion d’un récepteur de diffusion et l’envoi de diffusions :

1. **`Context`** &ndash; la classe `Android.Content.Context` peut être utilisée pour inscrire un récepteur de diffusion qui répond aux événements au niveau du système. Le `Context` est également utilisé pour publier des diffusions à l’ensemble du système.
2. **[`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))** &ndash; il s’agit d’une API qui est disponible via le [package NuGet de la bibliothèque de prise en charge Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/). Cette classe est utilisée pour maintenir l’isolation des diffusions et des récepteurs de diffusion dans le contexte de l’application qui les utilise. Cette classe peut être utile pour empêcher d’autres applications de répondre à des diffusions d’application uniquement ou d’envoyer des messages à des destinataires privés.

Un récepteur de diffusion peut ne pas afficher des boîtes de dialogue et il est fortement déconseillé de démarrer une activité à partir d’un récepteur de diffusion. Si un récepteur de diffusion doit notifier l’utilisateur, il doit publier une notification.

Il n’est pas possible de lier ou de démarrer un service à partir d’un récepteur de diffusion. 

Ce guide explique comment créer un récepteur de diffusion et comment l’inscrire afin qu’il puisse recevoir des diffusions.

## <a name="creating-a-broadcast-receiver"></a>Création d’un récepteur de diffusion

Pour créer un récepteur de diffusion dans Xamarin. Android, une application doit sous-définir la classe `BroadcastReceiver`, l’orner avec le `BroadcastReceiverAttribute`et remplacer la méthode `OnReceive` :

```csharp
[BroadcastReceiver(Enabled = true, Exported = false)]
public class SampleReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here.

        String value = intent.GetStringExtra("key");
    }
}
```

Quand Xamarin. Android compile la classe, elle met également à jour le fichier AndroidManifest avec les métadonnées nécessaires pour inscrire le destinataire. Pour un récepteur de diffusion inscrit de manière statique, le `Enabled` correctement doit être défini sur `true`, sinon Android ne peut pas créer une instance du récepteur.

La propriété `Exported` contrôle si le récepteur de diffusion peut recevoir des messages provenant de l’extérieur de l’application. Si la propriété n’est pas définie explicitement, la valeur par défaut de la propriété est déterminée par Android en fonction de s’il existe des filtres intentionnels associés au récepteur de diffusion. S’il existe au moins un filtre intentionnel pour le récepteur de diffusion, Android suppose que la propriété `Exported` est `true`. S’il n’existe aucun filtre intentionnel associé au récepteur de diffusion, Android suppose que la valeur est `false`. 

La méthode `OnReceive` reçoit une référence au `Intent` qui a été distribué au récepteur de diffusion. Cela permet à l’expéditeur de l’intention de transmettre des valeurs au récepteur de diffusion.

### <a name="statically-registering-a-broadcast-receiver-with-an-intent-filter"></a>Enregistrement statique d’un récepteur de diffusion à l’aide d’un filtre d’intention

Lorsqu’un `BroadcastReceiver` est décoré avec le [`IntentFilterAttribute`](xref:Android.App.IntentFilterAttribute), Xamarin. Android ajoute l’élément `<intent-filter>` nécessaire au manifeste Android au moment de la compilation. L’extrait de code suivant est un exemple de récepteur de diffusion qui s’exécute quand un appareil a fini de démarrer (si les autorisations Android appropriées ont été accordées par l’utilisateur) :

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { Android.Content.Intent.ActionBootCompleted })]
public class MyBootReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Work that should be done when the device boots.     
    }
}
```

> [!NOTE]
> Dans Android 8,0 (API 26 et versions ultérieures), [Google a placé des limitations](https://developer.android.com/about/versions/oreo/background) sur ce que les applications peuvent faire alors que les utilisateurs n’interagissent pas directement avec eux. Ces limitations affectent les services en arrière-plan et les récepteurs de diffusion implicite, comme `Android.Content.Intent.ActionBootCompleted`. En raison de ces limitations, vous risquez de rencontrer des difficultés lors de l’inscription d’un récepteur de diffusion `Boot Completed` sur les versions plus récentes d’Android. Si c’est le cas, Notez que ces restrictions ne s’appliquent pas aux services de premier plan, qui peuvent être appelés à partir de votre récepteur de diffusion.

Il est également possible de créer un filtre intentionnel qui répondra aux intentions personnalisées. Prenons l'exemple suivant : 

```csharp
[BroadcastReceiver(Enabled = true)]
[IntentFilter(new[] { "com.xamarin.example.TEST" })]
public class MySampleBroadcastReceiver : BroadcastReceiver
{
    public override void OnReceive(Context context, Intent intent)
    {
        // Do stuff here
    }
}
```

Les applications qui ciblent Android 8,0 (niveau d’API 26) ou version ultérieure peuvent ne pas s’inscrire de manière statique pour une diffusion implicite. Les applications peuvent toujours s’inscrire de manière statique pour une diffusion explicite. Il existe une petite liste de diffusions implicites exemptes de cette restriction. Ces exceptions sont décrites dans le guide des [exceptions de diffusion implicite](https://developer.android.com/guide/components/broadcast-exceptions.html) dans la documentation Android. Les applications qui s’intéressent à des diffusions implicites doivent le faire de manière dynamique à l’aide de la méthode `RegisterReceiver`. Cela est décrit ci-après.

### <a name="context-registering-a-broadcast-receiver"></a>Enregistrement d’un récepteur de diffusion en contexte

L’inscription de contexte (également appelée inscription dynamique) d’un récepteur est effectuée en appelant la méthode `RegisterReceiver`, et le récepteur de diffusion doit être désinscrit avec un appel à la méthode `UnregisterReceiver`. Pour empêcher les fuites de ressources, il est important d’annuler l’inscription du destinataire lorsqu’il n’est plus pertinent pour le contexte (l’activité ou le service). Par exemple, un service peut diffuser une intention d’informer une activité que des mises à jour sont disponibles pour être affichées à l’utilisateur. Lorsque l’activité démarre, elle s’inscrit pour ces intentions. Lorsque l’activité est déplacée en arrière-plan et n’est plus visible pour l’utilisateur, elle doit annuler l’inscription du récepteur, car l’interface utilisateur pour l’affichage des mises à jour n’est plus visible. L’extrait de code suivant est un exemple d’inscription et d’annulation de l’inscription d’un récepteur de diffusion dans le contexte d’une activité :

```csharp
[Activity(Label = "MainActivity", MainLauncher = true, Icon = "@mipmap/icon")]
public class MainActivity: Activity 
{
    MySampleBroadcastReceiver receiver;

    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        receiver = new MySampleBroadcastReceiver();

        // Code omitted for clarity
    }

    protected override void OnResume() 
    {
        base.OnResume();
        RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
        // Code omitted for clarity
    }

    protected override void OnPause() 
    {
        UnregisterReceiver(receiver);
        // Code omitted for clarity
        base.OnPause();
    }
}
```

Dans l’exemple précédent, lorsque l’activité passe au premier plan, elle inscrit un récepteur de diffusion qui écoute une intention personnalisée à l’aide de la méthode de cycle de vie `OnResume`. Lorsque l’activité passe en arrière-plan, la méthode `OnPause()` annule l’inscription du récepteur.

## <a name="publishing-a-broadcast"></a>Publication d’une diffusion

Une diffusion peut être publiée dans toutes les applications installées sur l’appareil qui crée un objet d’intention et la distribue avec la `SendBroadcast` ou la méthode `SendOrderedBroadcast`.  

1. Les **méthodes Context. SendBroadcast** &ndash; il existe plusieurs implémentations de cette méthode.
   Ces méthodes diffusent l’intention sur l’ensemble du système. Récepteurs de diffusion qui vont recevoir l’intention dans un ordre indéterminé. Cela offre une grande souplesse, mais signifie qu’il est possible pour d’autres applications de s’inscrire et de recevoir l’intention. Cela peut poser un risque de sécurité potentiel. Les applications devront peut-être implémenter une sécurité supplémentaire pour empêcher tout accès non autorisé. Une solution possible consiste à utiliser le `LocalBroadcastManager` qui distribuera uniquement les messages dans l’espace privé de l’application. Cet extrait de code est un exemple de la façon de distribuer une intention à l’aide de l’une des méthodes de `SendBroadcast` :

   ```csharp
   Intent message = new Intent("com.xamarin.example.TEST");
   // If desired, pass some values to the broadcast receiver.
   message.PutExtra("key", "value");
   SendBroadcast(message);
   ```

    Cet extrait de code est un autre exemple d’envoi d’une diffusion à l’aide de la méthode `Intent.SetAction` pour identifier l’action :

    ```csharp
    Intent intent = new Intent();
    intent.SetAction("com.xamarin.example.TEST");
    intent.PutExtra("key", "value");
    SendBroadcast(intent);
    ```

2. **Context. SendOrderedBroadcast** &ndash; il s’agit d’une méthode très similaire à `Context.SendBroadcast`, la seule différence étant que l’intention sera publiée une à la fois pour les destinataires, dans l’ordre dans lequel les destinataires ont été inscrits.

### <a name="localbroadcastmanager"></a>LocalBroadcastManager

La [bibliothèque de prise en charge Xamarin v4](https://www.nuget.org/packages/Xamarin.Android.Support.v4/) fournit une classe d’assistance appelée [`LocalBroadcastManager`](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html). Le `LocalBroadcastManager` est destiné aux applications qui ne souhaitent pas envoyer ou recevoir des diffusions à partir d’autres applications sur l’appareil. Le `LocalBroadcastManager` publiera uniquement les messages dans le contexte de l’application, et uniquement les destinataires de diffusion inscrits auprès du `LocalBroadcastManager`. Cet extrait de code est un exemple d’inscription d’un récepteur de diffusion avec `LocalBroadcastManager`:

```csharp
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this). RegisterReceiver(receiver, new IntentFilter("com.xamarin.example.TEST"));
```

Les autres applications sur l’appareil ne peuvent pas recevoir les messages publiés avec l' `LocalBroadcastManager`. Cet extrait de code montre comment distribuer une intention à l’aide de la `LocalBroadcastManager`:

```csharp
Intent message = new Intent("com.xamarin.example.TEST");
// If desired, pass some values to the broadcast receiver.
message.PutExtra("key", "value");
Android.Support.V4.Content.LocalBroadcastManager.GetInstance(this).SendBroadcast(message);
```

## <a name="related-links"></a>Liens associés

- [API BroadcastReceiver](xref:Android.Content.BroadcastReceiver)
- [API Context. RegisterReceiver](xref:Android.Content.Context.RegisterReceiver*)
- [API Context. SendBroadcast](xref:Android.Content.Context.SendBroadcast*)
- [API Context. UnregisterReceiver](xref:Android.Content.Context.UnregisterReceiver*)
- [API d’intention](xref:Android.Content.Intent)
- [API IntentFilter](xref:Android.App.IntentFilterAttribute)
- [LocalBroadcastManager (docs Android)](https://developer.android.com/reference/android/support/v4/content/LocalBroadcastManager.html#sendBroadcast(android.content.Intent))
- [Notifications locales dans](~/android/app-fundamentals/notifications/local-notifications.md) le guide Android
- [Bibliothèque de prise en charge Android V4 (NuGet)](https://www.nuget.org/packages/Xamarin.Android.Support.v4/)
