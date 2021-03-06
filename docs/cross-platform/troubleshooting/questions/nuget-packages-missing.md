---
title: Erreur de packages manquants après la mise à jour des packages NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: D61CC966-1D4A-49A5-8A6F-41572E28329B
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: a1ed4a2be63973e9e26dcb06163a3f9fd7eae649
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728094"
---
# <a name="missing-packages-error-after-updating-nuget-packages"></a>Erreur de packages manquants après la mise à jour des packages NuGet

Ce problème a été signalé principalement dans les exemples de solutions d’application Xamarin. Forms, mais le potentiel de ce problème peut se produire sur n’importe quel projet qui utilise des packages NuGet.

Si, après avoir mis à jour les packages NuGet dans votre projet ou votre solution, une erreur qui fait référence aux anciens numéros de version de package s’affiche, par exemple :

```csharp
Error: This project references NuGet package(s) that are missing on this computer.
Enable NuGet Package Restore to download them.
For more information, see http://go.microsoft.com/fwlink/?LinkID=322105

The missing file is ../../packages/Xamarin.Forms.1.3.1.6296/build/portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10/Xamarin.Forms.targets. (FormsGallery)
```

Dans cet exemple, *Xamarin. Forms. 1.3.1.6296* est l’ancien numéro de version qui a été supprimé avec la mise à jour du package NuGet.

Cela peut se produire si les éléments XML du fichier. csproj qui font référence à l’ancien numéro de version du package ont été ajoutés ou modifiés manuellement, que NuGet ne les supprime pas ou ne les met pas à jour s’ils avaient été ajoutés/modifiés manuellement, de sorte que le projet cherche à présent les packages qui ont été supprimés.

Pour résoudre ce problème, modifiez manuellement le ou les fichiers. csproj et supprimez tous les éléments qui font référence à l’ancien numéro de version.

Exemples d’éléments à supprimer (s’ils ont l’ancien numéro de version de package) :

```xml
<Reference Include="Xamarin.Forms.Maps">
    <HintPath>..\..\packages\Xamarin.Forms.Maps.1.3.1.6296\lib\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.Maps.dll</HintPath>
</Reference>

<Import Project="..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets" Condition="Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" />
<Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.3.1.6296\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10+Xamarin.iOS10\Xamarin.Forms.targets'))" />
```
