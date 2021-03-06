---
title: EventKit dans Xamarin. iOS
description: Ce document décrit EventKit et comment l’utiliser dans Xamarin. iOS. Il aborde les calendriers, les événements de calendrier et les rappels, examine les classes couramment utilisées lors de la programmation avec EventKit, et bien plus encore.
ms.prod: xamarin
ms.assetid: 00E88629-357D-1FCD-4FCE-1330D5D9D32C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/19/2017
ms.openlocfilehash: 1be6da2bbaf4aeffe00d90945bd06867f929c334
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73032548"
---
# <a name="eventkit-in-xamarinios"></a>EventKit dans Xamarin. iOS

iOS a deux applications liées au calendrier intégrées : l’application de calendrier et l’application de rappels. Il est assez simple de comprendre comment l’application de calendrier gère les données de calendrier, mais l’application de rappels est moins évidente. Les rappels peuvent en fait être associés à des dates lorsqu’ils sont échus, quand ils sont terminés, etc. Par conséquent, iOS stocke toutes les données de calendrier, qu’il s’agisse d’événements de calendrier ou de rappels, à un seul emplacement, appelé *base de données de calendrier*.

L’infrastructure EventKit permet d’accéder aux *données calendriers*, *événements de calendrier*et *rappels* stockées dans la base de données de calendrier. L’accès aux calendriers et aux événements de calendrier est disponible depuis iOS 4, mais l’accès aux rappels est nouveau dans iOS 6.

Dans ce guide, nous allons aborder les éléments suivants :

- **Notions de base de EventKit** : cette rubrique présente les éléments fondamentaux de EventKit via les principales classes et fournit une compréhension de leur utilisation. Cette section est nécessaire pour la lecture avant la partie suivante du document. 
- **Tâches courantes** : la section tâches courantes est destinée à servir de référence rapide sur la façon d’effectuer des opérations courantes, telles que ; énumération des calendriers, création, enregistrement et récupération des événements de calendrier et des rappels, et utilisation des contrôleurs intégrés pour la création et la modification d’événements de calendrier. Cette section n’a pas besoin d’être lue avant-arrière, car elle est destinée à être une référence pour des tâches particulières. 

Toutes les tâches de ce guide sont disponibles dans l’exemple d’application auxiliaire :

 [![](eventkit-images/01.png "The companion sample application screens")](eventkit-images/01.png#lightbox)

## <a name="requirements"></a>spécifications

EventKit a été introduit dans iOS 4,0, mais l’accès aux données de rappels a été introduit dans iOS 6,0. Par conséquent, pour effectuer un développement EventKit général, vous devez cibler au moins la version 4,0 et 6,0 pour les rappels.

En outre, l’application rappels n’est pas disponible sur le simulateur, ce qui signifie que les données de rappel ne seront pas disponibles, sauf si vous les ajoutez d’abord. En outre, les demandes d’accès sont affichées uniquement à l’utilisateur sur l’appareil réel. Par conséquent, le développement EventKit est le meilleur test sur l’appareil.

## <a name="event-kit-basics"></a>Notions de base du kit d’événements

Lorsque vous travaillez avec EventKit, il est important de comprendre les classes communes et leur utilisation. Toutes ces classes se trouvent dans les `EventKit` et `EventKitUI` (pour le `EKEventEditController`).

### <a name="eventstore"></a>EventStore

La classe *EventStore* est la classe la plus importante dans EventKit, car elle est requise pour effectuer des opérations dans EventKit. Il peut être considéré comme le stockage persistant ou le moteur de base de données pour toutes les données de EventKit. À partir de `EventStore` vous avez accès aux calendriers et aux événements de calendrier de l’application de calendrier, ainsi qu’aux rappels dans l’application rappels.

Étant donné que `EventStore` est comme un moteur de base de données, il doit être de longue durée, ce qui signifie qu’il doit être créé et détruit le moins possible au cours de la durée de vie d’une instance d’application. En fait, il est recommandé qu’une fois que vous avez créé une instance d’un `EventStore` dans une application, vous conservez cette référence pour toute la durée de vie de l’application, sauf si vous êtes sûr de ne plus en avoir besoin. en outre, tous les appels doivent être dirigés vers une instance de `EventStore` unique. C’est la raison pour laquelle le modèle Singleton est recommandé pour conserver une seule instance disponible.

#### <a name="creating-an-event-store"></a>Création d’un magasin d’événements

Le code suivant illustre un moyen efficace de créer une instance unique de la classe `EventStore` et de la rendre disponible de manière statique à partir d’une application :

```csharp
public class App
{
    public static App Current {
            get { return current; }
    }
    private static App current;

    public EKEventStore EventStore {
            get { return eventStore; }
    }
    protected EKEventStore eventStore;

    static App ()
    {
            current = new App();
    }
    protected App () 
    {
            eventStore = new EKEventStore ( );
    }
}
```

Le code ci-dessus utilise le modèle Singleton pour instancier une instance du `EventStore` lors du chargement de l’application. Le `EventStore` est ensuite accessible globalement à partir de l’application, comme suit :

```csharp
App.Current.EventStore;
```

Notez que tous les exemples fournis ici utilisent ce modèle, afin qu’ils fassent référence au `EventStore` via `App.Current.EventStore`.

#### <a name="requesting-access-to-calendar-and-reminder-data"></a>Demande d’accès aux données de calendrier et de rappel

Avant d’être autorisé à accéder à des données via EventStore, une application doit tout d’abord demander l’accès aux données du calendrier ou aux rappels, en fonction de celui dont vous avez besoin. Pour faciliter cette opération, l' `EventStore` expose une méthode appelée `RequestAccess` qui, lorsqu’elle est appelée, affiche un affichage des alertes à l’utilisateur, lui indiquant que l’application demande l’accès aux données du calendrier ou aux données de rappel, en fonction de la `EKEntityType` passée à tel. Étant donné qu’il déclenche un affichage des alertes, l’appel est asynchrone et appelle un gestionnaire d’achèvement passé comme `NSAction` (ou lambda) qui recevra deux paramètres ; valeur booléenne indiquant si l’accès a été accordé, et un `NSError`qui, si NOT-NULL, contient des informations d’erreur dans la demande. Par exemple, le code suivant demande l’accès aux données d’événement de calendrier et affiche un affichage des alertes si la demande n’a pas été accordée.

```csharp
App.Current.EventStore.RequestAccess (EKEntityType.Event, 
    (bool granted, NSError e) => {
            if (granted)
                    //do something here
            else
                    new UIAlertView ( "Access Denied", 
"User Denied Access to Calendar Data", null,
"ok", null).Show ();
            } );
```

Une fois que la demande a été accordée, elle est mémorisée tant que l’application est installée sur l’appareil et n’affiche pas d’alerte à l’utilisateur.
Toutefois, l’accès est accordé uniquement au type de ressource, à savoir les événements de calendrier ou les rappels accordés. Si une application a besoin d’accéder aux deux, elle doit demander les deux.

Étant donné que l’autorisation est mémorisée, il est relativement peu coûteux de faire la demande à chaque fois. il est donc judicieux de toujours demander l’accès avant d’effectuer une opération.

En outre, étant donné que le gestionnaire d’achèvement est appelé sur un thread séparé (non-IU), toutes les mises à jour de l’interface utilisateur dans le gestionnaire d’achèvement doivent être appelées via `InvokeOnMainThread`. sinon, une exception est levée et, si elle n’est pas interceptée, l’application se bloque.

### <a name="ekentitytype"></a>EKEntityType

`EKEntityType` est une énumération qui décrit le type d' `EventKit` élément ou de données. Il a deux valeurs : `Event` et rappel. Elle est utilisée dans un certain nombre de méthodes, notamment `EventStore.RequestAccess` pour indiquer à `EventKit` quel type de données accéder ou récupérer.

### <a name="ekcalendar"></a>EKCalendar

 *EKCalendar* représente un calendrier, qui contient un groupe d’événements de calendrier. Les calendriers peuvent être stockés dans de nombreux emplacements différents, par exemple localement, dans *icloud*, dans un emplacement de fournisseur tiers, tel qu’un *serveur Exchange* ou *Google*, etc. La plupart du temps `EKCalendar` est utilisé pour indiquer à `EventKit` où rechercher des événements ou où les enregistrer.

### <a name="ekeventeditcontroller"></a>EKEventEditController

 *EKEventEditController* se trouve dans l’espace de noms `EventKitUI` et est un contrôleur intégré qui peut être utilisé pour modifier ou créer des événements de calendrier. À l’instar des contrôleurs d’appareil photo intégrés, `EKEventEditController` fait le plus lourd pour afficher l’interface utilisateur et gérer les économies.

### <a name="ekevent"></a>EKEvent

 *EKEvent* représente un événement de calendrier. Les `EKEvent` et `EKReminder` héritent de `EKCalendarItem` et ont des champs tels que `Title`, `Notes`, etc.

### <a name="ekreminder"></a>EKReminder

 *EKReminder* représente un élément de rappel.

### <a name="ekspan"></a>EKSpan

*EKSpan* est une énumération qui décrit l’étendue des événements lors de la modification d’événements susceptibles de se reproduire, et a deux valeurs : *cette* et *FutureEvents*. `ThisEvent` signifie que toutes les modifications ne se produiront que dans l’événement particulier de la série référencée, tandis que `FutureEvents` affectera cet événement et toutes les futures récurrences.

## <a name="tasks"></a>Tâches

Pour faciliter l’utilisation, l’utilisation de EventKit a été divisée en tâches courantes, décrites dans les sections suivantes.

### <a name="enumerate-calendars"></a>Énumérer les calendriers

Pour énumérer les calendriers que l’utilisateur a configurés sur l’appareil, appelez `GetCalendars` sur le `EventStore` et transmettez le type de calendriers (rappels ou événements) que vous souhaitez recevoir :

```csharp
EKCalendar[] calendars = 
App.Current.EventStore.GetCalendars ( EKEntityType.Event );
```

### <a name="add-or-modify-an-event-using-the-built-in-controller"></a>Ajouter ou modifier un événement à l’aide du contrôleur intégré

Le *EKEventEditViewController* effectue beaucoup de tâches importantes pour vous si vous souhaitez créer ou modifier un événement avec la même interface utilisateur que celle présentée à l’utilisateur lors de l’utilisation de l’application de calendrier :

 [![](eventkit-images/02.png "The UI that is presented to the user when using the Calendar Application")](eventkit-images/02.png#lightbox)

Pour l’utiliser, vous souhaiterez le déclarer comme une variable au niveau de la classe afin qu’il ne soit pas récupéré par le garbage collector s’il est déclaré dans une méthode :

```csharp
public class HomeController : DialogViewController
{
        protected CreateEventEditViewDelegate eventControllerDelegate;
        ...
}
```

Ensuite, pour le lancer : instanciez-le, donnez-lui une référence au `EventStore`, associez un délégué *EKEventEditViewDelegate* à ce dernier, puis affichez-le à l’aide de `PresentViewController`:

```csharp
EventKitUI.EKEventEditViewController eventController = 
        new EventKitUI.EKEventEditViewController ();

// set the controller's event store - it needs to know where/how to save the event
eventController.EventStore = App.Current.EventStore;

// wire up a delegate to handle events from the controller
eventControllerDelegate = new CreateEventEditViewDelegate ( eventController );
eventController.EditViewDelegate = eventControllerDelegate;

// show the event controller
PresentViewController ( eventController, true, null );
```

Éventuellement, si vous souhaitez préremplir l’événement, vous pouvez créer un événement de personnalisation (comme indiqué ci-dessous) ou vous pouvez récupérer un événement enregistré :

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and exercise!";
newEvent.Notes = "This is your reminder to go and exercise for 30 minutes.”;
```

Si vous souhaitez préremplir l’interface utilisateur, veillez à définir la propriété d’événement sur le contrôleur :

```csharp
eventController.Event = newEvent;
```

Pour utiliser un événement existant, consultez la section *récupérer un événement par ID* plus tard.

Le délégué doit substituer la méthode `Completed`, qui est appelée par le contrôleur lorsque l’utilisateur a terminé avec la boîte de dialogue :

```csharp
protected class CreateEventEditViewDelegate : EventKitUI.EKEventEditViewDelegate
{
        // we need to keep a reference to the controller so we can dismiss it
        protected EventKitUI.EKEventEditViewController eventController;

        public CreateEventEditViewDelegate (EventKitUI.EKEventEditViewController eventController)
        {
                // save our controller reference
                this.eventController = eventController;
        }

        // completed is called when a user eith
        public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
        {
                eventController.DismissViewController (true, null);
                }
        }
}
```

Éventuellement, dans le délégué, vous pouvez vérifier l' *action* dans la méthode `Completed` pour modifier l’événement et le réenregistrer, ou effectuer d’autres opérations, s’il est annulé, etc :

```csharp
public override void Completed (EventKitUI.EKEventEditViewController controller, EKEventEditViewAction action)
{
        eventController.DismissViewController (true, null);

        switch ( action ) {

        case EKEventEditViewAction.Canceled:
                break;
        case EKEventEditViewAction.Deleted:
                break;
        case EKEventEditViewAction.Saved:
                // if you wanted to modify the event you could do so here,
// and then save:
                //App.Current.EventStore.SaveEvent ( controller.Event, )
                break;
        }
}
```

### <a name="creating-an-event-programmatically"></a>Création d’un événement par programmation

Pour créer un événement dans le code, utilisez la méthode de fabrique *FromStore* sur la classe `EKEvent` et définissez les données qu’il contient :

```csharp
EKEvent newEvent = EKEvent.FromStore ( App.Current.EventStore );
// set the alarm for 10 minutes from now
newEvent.AddAlarm ( EKAlarm.FromDate ( DateTime.Now.AddMinutes ( 10 ) ) );
// make the event start 20 minutes from now and last 30 minutes
newEvent.StartDate = DateTime.Now.AddMinutes ( 20 );
newEvent.EndDate = DateTime.Now.AddMinutes ( 50 );
newEvent.Title = "Get outside and do some exercise!";
newEvent.Notes = "This is your motivational event to go and do 30 minutes of exercise. Super important. Do this.";
```

Vous devez définir le calendrier dans lequel vous souhaitez enregistrer l’événement, mais si vous n’avez aucune préférence, vous pouvez utiliser la valeur par défaut :

```csharp
newEvent.Calendar = App.Current.EventStore.DefaultCalendarForNewEvents;
```

Pour enregistrer l’événement, appelez la méthode *SaveEvent* sur le `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveEvent ( newEvent, EKSpan.ThisEvent, out e );
```

Une fois enregistrée, la propriété *EventIdentifier* est mise à jour avec un identificateur unique qui peut être utilisé ultérieurement pour récupérer l’événement :

```csharp
Console.WriteLine ("Event Saved, ID: " + newEvent.CalendarItemIdentifier);
```

 `EventIdentifier` est un GUID mis en forme de chaîne.

### <a name="create-a-reminder-programmatically"></a>Créer un rappel par programmation

La création d’un rappel dans le code est très similaire à la création d’un événement de calendrier :

```csharp
EKReminder reminder = EKReminder.Create ( App.Current.EventStore );
reminder.Title = "Do something awesome!";
reminder.Calendar = App.Current.EventStore.DefaultCalendarForNewReminders;
```

Pour enregistrer, appelez la méthode *SaveReminder* sur le `EventStore`:

```csharp
NSError e;
App.Current.EventStore.SaveReminder ( reminder, true, out e );
```

### <a name="retrieving-an-event-by-id"></a>Récupération d’un événement par ID

Pour récupérer un événement à l’aide de son ID, utilisez la méthode *EventFromIdentifier* sur la `EventStore` et transmettez-lui le `EventIdentifier` qui a été extrait de l’événement :

```csharp
EKEvent mySavedEvent = App.Current.EventStore.EventFromIdentifier ( newEvent.EventIdentifier );
```

Pour les événements, il existe deux autres propriétés d’identificateur, mais `EventIdentifier` est le seul qui fonctionne pour ce.

### <a name="retrieving-a-reminder-by-id"></a>Récupération d’un rappel par ID

Pour récupérer un rappel, utilisez la méthode *GetCalendarItem* sur l' `EventStore` et transmettez-lui le *CalendarItemIdentifier*:

```csharp
EKCalendarItem myReminder = App.Current.EventStore.GetCalendarItem ( reminder.CalendarItemIdentifier );
```

Étant donné que `GetCalendarItem` retourne un `EKCalendarItem`, il doit être casté en `EKReminder` si vous avez besoin d’accéder aux données de rappel ou d’utiliser l’instance en tant que `EKReminder` ultérieurement.

N’utilisez pas `GetCalendarItem` pour les événements de calendrier, comme au moment de l’écriture, cela ne fonctionne pas.

### <a name="deleting-an-event"></a>Suppression d’un événement

Pour supprimer un événement de calendrier, appelez *RemoveEvent* sur votre `EventStore` et transmettez une référence à l’événement, ainsi que les `EKSpan`appropriées :

```csharp
NSError e;
App.Current.EventStore.RemoveEvent ( mySavedEvent, EKSpan.ThisEvent, true, out e);
```

Notez toutefois qu’après la suppression d’un événement, la référence d’événement sera `null`.

### <a name="deleting-a-reminder"></a>Suppression d’un rappel

Pour supprimer un rappel, appelez *RemoveReminder* sur la `EventStore` et transmettez une référence au rappel :

```csharp
NSError e;
App.Current.EventStore.RemoveReminder ( myReminder as EKReminder, true, out e);
```

Notez que dans le code ci-dessus, il y a un cast en `EKReminder`, car `GetCalendarItem` a été utilisé pour le récupérer

### <a name="searching-for-events"></a>Recherche d’événements

Pour rechercher des événements de calendrier, vous devez créer un objet *NSPredicate* à l’aide de la méthode *PredicateForEvents* sur le `EventStore`. Un `NSPredicate` est un objet de données de requête que iOS utilise pour localiser des correspondances :

```csharp
DateTime startDate = DateTime.Now.AddDays ( -7 );
DateTime endDate = DateTime.Now;
// the third parameter is calendars we want to look in, to use all calendars, we pass null
NSPredicate query = App.Current.EventStore.PredicateForEvents ( startDate, endDate, null );
```

Une fois que vous avez créé le `NSPredicate`, utilisez la méthode *EventsMatching* sur le `EventStore`:

```csharp
// execute the query
EKCalendarItem[] events = App.Current.EventStore.EventsMatching ( query );
```

Notez que les requêtes sont synchrones (blocage) et peuvent prendre beaucoup de temps, en fonction de la requête, vous pouvez par conséquent créer un nouveau thread ou une nouvelle tâche pour le faire.

### <a name="searching-for-reminders"></a>Recherche de rappels

La recherche de rappels est similaire aux événements ; elle requiert un prédicat, mais l’appel est déjà asynchrone. vous n’avez donc pas à vous soucier du blocage du thread :

```csharp
// create our NSPredicate which we'll use for the query
NSPredicate query = App.Current.EventStore.PredicateForReminders ( null );

// execute the query
App.Current.EventStore.FetchReminders (
        query, ( EKReminder[] items ) => {
                // do someting with the items
        } );
```

## <a name="summary"></a>Récapitulatif

Ce document a fourni une vue d’ensemble des éléments importants de l’infrastructure EventKit et un certain nombre de tâches les plus courantes. Toutefois, l’infrastructure EventKit est très volumineuse et puissante, et comprend des fonctionnalités qui n’ont pas été introduites ici, telles que les mises à jour par lot, la configuration des alarmes, la configuration de la récurrence sur les événements, l’inscription et l’écoute des modifications sur la base de données du calendrier, définir des limites de géolimites, etc.  Pour plus d’informations, consultez le [Guide de programmation du calendrier et des rappels](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)d’Apple.

## <a name="related-links"></a>Liens associés

- [Calendriers (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/calendars)
- [Introduction à iOS 6](~/ios/platform/introduction-to-ios6/index.md)
- [Présentation des calendriers et des rappels](https://developer.apple.com/library/prerelease/ios/#documentation/DataManagement/Conceptual/EventKitProgGuide/Introduction/Introduction.html)
