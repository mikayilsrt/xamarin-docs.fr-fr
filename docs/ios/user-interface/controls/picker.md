---
title: Contrôle Picker dans Xamarin. iOS
description: Ce document explique comment concevoir et utiliser des contrôles sélecteur dans une application Xamarin. iOS. Il explique comment implémenter un sélecteur dans le code et dans le concepteur iOS.
ms.prod: xamarin
ms.assetid: A2369EFC-285A-44DD-9E80-EC65BC3DF041
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 08/14/2018
ms.openlocfilehash: 43dbafe16d7cbabdb3b7902dd3d46d845f213fcd
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78292650"
---
# <a name="picker-control-in-xamarinios"></a>Contrôle Picker dans Xamarin. iOS

Une [`UIPickerView`](xref:UIKit.UIPickerView) permet de choisir une valeur dans une liste en faisant défiler les composants individuels d’une interface de type roue.

Les sélecteurs sont fréquemment utilisés pour sélectionner une date et une heure ; Apple fournit les [`UIDatePicker`](xref:UIKit.UIDatePicker)
classe à cet effet.

L’article explique comment implémenter et utiliser les contrôles `UIPickerView` et `UIDatePicker`.

## <a name="uipickerview"></a>UIPickerView

### <a name="implementing-a-picker"></a>Implémentation d’un sélecteur

Implémentez un sélecteur en instanciant un nouveau `UIPickerView`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width, 
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
    )
);
```

### <a name="pickers-and-storyboards"></a>Sélecteurs et storyboards

Pour créer un sélecteur dans le **Concepteur iOS**, faites glisser une **vue sélecteur** de la **boîte à outils** vers l’aire de conception.

![Faire glisser une vue sélecteur sur l’aire de conception](picker-images/image1.png "Faire glisser une vue sélecteur sur l’aire de conception")

### <a name="working-with-a-picker-control"></a>Utilisation d’un contrôle sélecteur

Un sélecteur utilise un _modèle_ pour interagir avec les données :

```csharp
public override void ViewDidLoad()
{
    base.ViewDidLoad();
    var pickerModel = new PeopleModel(personLabel);
    personPicker.Model = pickerModel;
}
```

La classe de base [`UIPickerViewModel`](xref:UIKit.UIPickerViewModel) implémente deux interfaces, [`IUIPickerDataSource`](xref:UIKit.IUIPickerViewDataSource)
et [`IUIPickerViewDelegate`](xref:UIKit.IUIPickerViewDelegate), qui déclarent différentes méthodes qui spécifient les données d’un sélecteur et comment il gère l’interaction :

```csharp
public class PeopleModel : UIPickerViewModel
{
    public string[] names = new string[] {
            "Amy Burns",
            "Kevin Mullins",
            "Craig Dunn",
            "Joel Martinez",
            "Charles Petzold",
            "David Britch",
            "Mark McLemore",
            "Tom Opegenorth",
            "Joseph Hill",
            "Miguel De Icaza"
        };

    private UILabel personLabel;

    public PeopleModel(UILabel personLabel)
    {
        this.personLabel = personLabel;
    }

    public override nint GetComponentCount(UIPickerView pickerView)
    {
        return 2;
    }

    public override nint GetRowsInComponent(UIPickerView pickerView, nint component)
    {
        return names.Length;
    }

    public override string GetTitle(UIPickerView pickerView, nint row, nint component)
    {
        if (component == 0)
            return names[row];
        else
            return row.ToString();
    }

    public override void Selected(UIPickerView pickerView, nint row, nint component)
    {
        personLabel.Text = $"This person is: {names[pickerView.SelectedRowInComponent(0)]},\n they are number {pickerView.SelectedRowInComponent(1)}";
    }

    public override nfloat GetComponentWidth(UIPickerView picker, nint component)
    {
        if (component == 0)
            return 240f;
        else
            return 40f;
    }

    public override nfloat GetRowHeight(UIPickerView picker, nint component)
    {
        return 40f;
    }
```

Un sélecteur peut avoir plusieurs colonnes, ou _composants_. Les composants partitionnent un sélecteur en plusieurs sections, ce qui permet une sélection de données plus simple et plus spécifique :

![Sélecteur avec deux composants](picker-images/image3.png "Sélecteur avec deux composants")

Pour spécifier le nombre de composants dans un sélecteur, utilisez l' [`GetComponentCount`](xref:UIKit.UIPickerViewModel.GetComponentCount(UIKit.UIPickerView)) 
.

### <a name="customizing-a-pickers-appearance"></a>Personnalisation de l’apparence d’un sélecteur

Pour personnaliser l’apparence d’un sélecteur, utilisez l' [`UIPickerView.UIPickerViewAppearance`](xref:UIKit.UIPickerView.UIPickerViewAppearance)
classe ou substituez les méthodes [`GetView`](xref:UIKit.UIPickerViewModel.GetView(UIKit.UIPickerView,System.nint,System.nint,UIKit.UIView)) et [`GetRowHeight`](xref:UIKit.UIPickerViewModel.GetRowHeight(UIKit.UIPickerView,System.nint)) dans le `UIPickerViewModel`.

## <a name="uidatepicker"></a>UIDatePicker

### <a name="implementing-a-date-picker"></a>Implémentation d’un sélecteur de dates

Implémentez un sélecteur de dates en instanciant un `UIDatePicker`:

```csharp
UIPickerView pickerView = new UIPickerView(
    new CGRect(
        UIScreen.MainScreen.Bounds.X - UIScreen.MainScreen.Bounds.Width,
        UIScreen.MainScreen.Bounds.Height - 230,
        UIScreen.MainScreen.Bounds.Width,
        180
     )
);
```

### <a name="date-pickers-and-storyboards"></a>Sélecteurs de dates et storyboards

Pour créer un sélecteur de dates dans le **Concepteur iOS**, faites glisser un **Sélecteur de dates** de la boîte à **Outils** vers l’aire de conception.

![Faire glisser un sélecteur de dates sur l’aire de conception](picker-images/image2.png "Faire glisser un sélecteur de dates sur l’aire de conception")

### <a name="date-picker-properties"></a>Propriétés du sélecteur de dates

#### <a name="minimum-and-maximum-date"></a>Date minimale et maximale

[`MinimumDate`](xref:UIKit.UIDatePicker.MinimumDate) et [`MaximumDate`](xref:UIKit.UIDatePicker.MaximumDate) limitent la plage de dates disponibles dans le sélecteur de dates. Par exemple, le code suivant limite un sélecteur de dates aux 60 années qui mènent à l’heure actuelle :

```csharp
var calendar = new NSCalendar(NSCalendarType.Gregorian);
var currentDate = NSDate.Now;
var components = new NSDateComponents();
components.Year = -60;
NSDate minDate = calendar.DateByAddingComponents(components, currentDate, NSCalendarOptions.None);
datePickerView.MinimumDate = minDate;
datePickerView.MaximumDate = currentDate;
```

> [!TIP]
> Il est possible d’effectuer un cast explicite d’un `DateTime` en `NSDate`:
>
> ```csharp
> DatePicker.MinimumDate = (NSDate)DateTime.Today.AddDays (-7);
> DatePicker.MaximumDate = (NSDate)DateTime.Today.AddDays (7);
> ```

#### <a name="minute-interval"></a>Intervalle de minutes

La propriété [`MinuteInterval`](xref:UIKit.UIDatePicker.MinuteInterval) définit l’intervalle auquel le sélecteur affiche les minutes :

```csharp
datePickerView.MinuteInterval = 10;
```

#### <a name="mode"></a>Mode

Les sélecteurs de dates prennent en charge quatre [modes](xref:UIKit.UIDatePickerMode), décrits ci-dessous :

##### <a name="uidatepickermodetime"></a>UIDatePickerMode.Time

`UIDatePickerMode.Time` affiche l’heure avec un sélecteur d’heures et de minutes et une désignation AM ou PM facultative :

```csharp
datePickerView.Mode = UIDatePickerMode.Time;
```

![UIDatePickerMode. heure](picker-images/image8.png "UIDatePickerMode.Time")

##### <a name="uidatepickermodedate"></a>UIDatePickerMode.Date

`UIDatePickerMode.Date` affiche la date avec un sélecteur de mois, de jour et d’année :

```csharp
datePickerView.Mode = UIDatePickerMode.Date;
```

![UIDatePickerMode. date](picker-images/image7.png "UIDatePickerMode.Date")

L’ordre des sélecteurs dépend des paramètres régionaux du sélecteur de dates, qui utilise par défaut les paramètres régionaux du système. L’image ci-dessus montre la disposition des sélecteurs dans les paramètres régionaux de la `en_US`, mais les modifications suivantes sont apportées à la commande jour | Mois | Year

```csharp
datePickerView.Locale = NSLocale.FromLocaleIdentifier("en_GB");
```

![Jour | Mois | Year](picker-images/image9.png "Jour | Mois | Year")

##### <a name="uidatepickermodedateandtime"></a>UIDatePickerMode.DateAndTime

`UIDatePickerMode.DateAndTime` affiche une vue abrégée de la date, l’heure en heures et minutes et une désignation AM ou PM facultative (selon qu’une horloge de 12 ou 24 heures est utilisée) :

```csharp
datePickerView.Mode = UIDatePickerMode.DateAndTime;
```

![UIDatePickerMode. DateAndTime](picker-images/image6.png "UIDatePickerMode.DateAndTime")

Comme pour [`UIDatePickerMode.Date`](#uidatepickermodedate), l’ordre des sélecteurs et l’utilisation d’une horloge de 12 ou 24 heures dépend des paramètres régionaux du sélecteur de dates.

> [!TIP]
> Utilisez la propriété `Date` pour capturer la valeur d’un sélecteur de dates en mode `UIDatePickerMode.Time`, `UIDatePickerMode.Date`ou `UIDatePickerMode.DateAndTime`. Cette valeur est stockée sous la forme d’un `NSDate`.

##### <a name="uidatepickermodecountdowntimer"></a>UIDatePickerMode.CountDownTimer

`UIDatePickerMode.CountDownTimer` affiche les valeurs d’heure et de minute :

```csharp
datePickerView.Mode = UIDatePickerMode.CountDownTimer;
```

!["UIDatePickerMode.CountDownTimer"](picker-images/image5.png "UIDatePickerMode.CountDownTimer")

La propriété `CountDownDuration` capture la valeur d’un sélecteur de dates en mode `UIDatePickerMode.CountDownTimer`. Par exemple, pour ajouter la valeur de compte à rebours à la date actuelle :

```csharp
var currentTime = NSDate.Now;
var countDownTimerTime = datePickerView.CountDownDuration;
var finishCountdown = currentTime.AddSeconds(countDownTimerTime);

dateLabel.Text = "Alarm set for:" + coundownTimeformat.ToString(finishCountdown);
```

#### <a name="nsdateformatter"></a>NSDateFormatter

Pour mettre en forme un `NSDate`, utilisez une [`NSDateFormatter`](xref:Foundation.NSDateFormatter).

Pour utiliser un `NSDateFormatter`, appelez sa méthode [`ToString`](xref:Foundation.NSDateFormatter.ToString(Foundation.NSDate)) . Par exemple :

```csharp
var date = NSDate.Now;
var formatter = new NSDateFormatter();
formatter.DateStyle = NSDateFormatterStyle.Full;
formatter.TimeStyle = NSDateFormatterStyle.Full;
var formattedDate = formatter.ToString(d);
// Tuesday, August 14, 2018 at 11:20:42 PM Mountain Daylight Time
```

##### <a name="dateformat"></a>DateFormat

La propriété [`DateFormat`](xref:Foundation.NSDateFormatter.DateFormat) (une chaîne) d’un `NSDateFormatter` permet une spécification de format de date personnalisable :

```csharp
NSDateFormatter dateFormat = new NSDateFormatter();
dateFormat.DateFormat = "yyyy-MM-dd";
```

##### <a name="timestyle"></a>TimeStyle

La propriété [`TimeStyle`](xref:Foundation.NSDateFormatter.TimeStyle) (un [`NSDateFormatterStyle`](xref:Foundation.NSDateFormatterStyle) d’un `NSDateFormatter` spécifie la mise en forme de l’heure en fonction de styles prédéterminés :

```csharp
NSDateFormatter timeFormat = new NSDateFormatter();
timeFormat.TimeStyle = NSDateFormatterStyle.Short;
```

Plusieurs valeurs de `NSDateFormatterStyle` affichent les heures comme suit :

- `NSDateFormatterStyle.Full`: 7:46:00 PM heure d’été
- `NSDateFormatterStyle.Long`: 7:47:00 P.M. EDT
- `NSDateFormatterStyle.Medium`: 7:47:00
- `NSDateFormatterSytle.Short`: 7:47

##### <a name="datestyle"></a>DateStyle

La propriété [`DateStyle`](xref:Foundation.NSDateFormatter.DateStyle) (une `NSDateFormatterStyle`) d’un `NSDateFormatter` spécifie la mise en forme de la date basée sur des styles prédéterminés :

```csharp
NSDateFormatter dateTimeformat = new NSDateFormatter();
dateTimeformat.DateStyle = NSDateFormatterStyle.Long;
```

Différentes valeurs de `NSDateFormatterStyle` affichent des dates comme suit :

- `NSDateFormatterStyle.Full`: mercredi 2 août 2017 à 7:48 PM
- `NSDateFormatterStyle.Long`: 2 août 2017 à 7:49 PM
- `NSDateFormatterStyle.Medium`: 2 août, 2017, 7:49 PM
- `NSDateFormatterStyle.Short`: 8/2/17, 7:50 PM

> [!NOTE]
> `DateFormat` et `DateStyle`/`TimeStyle` offrent différentes manières de spécifier la mise en forme de la date et de l’heure. Les propriétés les plus récentes déterminent la sortie du formateur de date.

## <a name="related-links"></a>Liens connexes

- [PickerControl (exemple)](https://docs.microsoft.com/samples/xamarin/ios-samples/pickercontrol)
