---
title : « stylisation Xamarin.Forms d’applications à l’aide de styles XAML » Description : « ce guide explique comment personnaliser l’apparence d’une Xamarin.Forms application à l’aide de styles XAML ».
ms. Prod : xamarin ms. AssetID : 344A34AA-B19A-4765-BC8A-875D9A6B5EA8 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 01/30/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="styling-xamarinforms-apps-using-xaml-styles"></a>Styliser des Xamarin.Forms applications à l’aide de styles XAML

## <a name="introduction"></a>[Introduction](introduction.md)

Xamarin.Formsles applications contiennent souvent plusieurs contrôles qui ont une apparence identique. La définition de l’apparence de chaque contrôle individuel peut être répétitive et sujette aux erreurs. Au lieu de cela, vous pouvez créer des styles qui personnalisent l’apparence du contrôle en regroupant et en définissant des propriétés disponibles sur le type de contrôle.

## <a name="explicit-styles"></a>[Styles explicites](explicit.md)

Un style *explicite* est appliqué de manière sélective aux contrôles en définissant leurs [`Style`](xref:Xamarin.Forms.NavigableElement.Style) Propriétés.

## <a name="implicit-styles"></a>[Styles implicites](implicit.md)

Un style *implicite* est utilisé par tous les contrôles du même [`TargetType`](xref:Xamarin.Forms.Style.TargetType) , sans qu’il soit nécessaire que chaque contrôle référence le style.

## <a name="global-styles"></a>[Styles globaux](application.md)

Les styles peuvent être rendus disponibles globalement en les ajoutant au de l’application [`ResourceDictionary`](xref:Xamarin.Forms.ResourceDictionary) . Cela permet d’éviter la duplication des styles entre les pages ou les contrôles.

## <a name="style-inheritance"></a>[Héritage de style](inheritance.md)

Les styles peuvent hériter d’autres styles pour réduire la duplication et permettre la réutilisation.

## <a name="dynamic-styles"></a>[Styles dynamiques](dynamic.md)

Les styles ne répondent pas aux modifications de propriété et restent inchangés pour la durée d’une application. Toutefois, les applications peuvent répondre de manière dynamique aux modifications de style au moment de l’exécution à l’aide de ressources dynamiques.

## <a name="device-styles"></a>[Styles d’appareils](device.md)

Xamarin.Formscomprend six styles *dynamiques* , appelés styles de *périphérique* , dans la [`Devices.Styles`](xref:Xamarin.Forms.Device.Styles) classe. Les six styles ne peuvent être appliqués [`Label`](xref:Xamarin.Forms.Label) qu’à des instances.

## <a name="style-classes"></a>[Classes de style](style-class.md)

Xamarin.Formsles classes de style permettent d’appliquer plusieurs styles à un contrôle, sans recourir à l’héritage de style.
