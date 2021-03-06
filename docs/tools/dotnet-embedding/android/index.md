---
title: Intégration .NET sur Android
ms.prod: xamarin
ms.assetid: EB2F967A-6D95-4448-994B-6D5C7BFAC2C7
author: davidortinau
ms.author: daortin
ms.date: 06/15/2018
ms.openlocfilehash: fef422b799ab5280aef205f4d5e55fd91050da39
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73007336"
---
# <a name="net-embedding-on-android"></a>Intégration .NET sur Android

Dans certains cas, vous souhaiterez peut-être ajouter une bibliothèque .NET Xamarin à un projet Android natif existant. Pour ce faire, vous pouvez utiliser l’outil [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/) pour transformer votre bibliothèque .net en une bibliothèque native qui peut être incorporée dans une application Android basée sur Java native.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

## <a name="xamarinandroid-requirements"></a>Configuration requise pour Xamarin. Android

Pour que Xamarin. Android fonctionne avec l’incorporation .NET, vous avez besoin des éléments suivants :

- **Xamarin. android** &ndash;[Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou version ultérieure doit être installé.

- **Android Studio** &ndash;[Android Studio 3. x](https://developer.android.com/studio/) ou version ultérieure doit être installé.

- Le **Kit de développement java** &ndash;[Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure doit être installé.

## <a name="using-embeddinator-4000"></a>Utilisation de Embeddinator-4000

Pour utiliser une bibliothèque .NET dans un projet Android natif, procédez comme suit :

1. Créez un C# projet de bibliothèque Android.

2. Installez [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Recherchez **Embeddinator-4000. exe** et ajoutez-le à votre **chemin d’accès**. Exemple :

    ```cmd
    set PATH=%PATH%;C:\Users\USERNAME\.nuget\packages\embeddinator-4000\0.4.0\tools
    ```

4. Exécutez Embeddinator-4000 sur l’assembly de bibliothèque. Exemple :

    ```cmd
    Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Utilisez le fichier AAR généré dans un projet Java dans Android Studio.

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

## <a name="xamarinandroid-requirements"></a>Configuration requise pour Xamarin. Android

Pour que Xamarin. Android fonctionne avec l’incorporation .NET, vous avez besoin des éléments suivants :

- **Xamarin. android** &ndash;[Xamarin. Android 7,5](https://visualstudio.microsoft.com/xamarin/) ou version ultérieure doit être installé.

- **Android Studio** &ndash;[Android Studio 3. x](https://developer.android.com/studio/) ou version ultérieure doit être installé.

- Le **Kit de développement java** &ndash;[Java 1,8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) ou version ultérieure doit être installé.

- **Mono** &ndash;[mono 5,0](https://www.mono-project.com/download/) ou version ultérieure doit être installé (mono est installé avec Visual Studio pour Mac).

## <a name="using-embeddinator-4000"></a>Utilisation de Embeddinator-4000

Pour utiliser une bibliothèque .NET dans un projet Android natif, procédez comme suit :

1. Créez un C# projet de bibliothèque Android.

2. Installez [Embeddinator-4000](https://www.nuget.org/packages/Embeddinator-4000/).

3. Recherchez **Embeddinator-4000. exe** et ajoutez **mono** à votre chemin d’accès. Exemple :

    ```bash
    export TOOLS=~/.nuget/packages/embeddinator-4000/0.4.0/tools
    export PATH=$PATH:/Library/Frameworks/Mono.framework/Commands
    ```

4. Exécutez Embeddinator-4000 sur l’assembly de bibliothèque. Exemple :

    ```bash
    mono $TOOLS/Embeddinator-4000.exe -gen=Java -out=foo Xamarin.Foo.dll
    ```

5. Utilisez le fichier AAR généré dans un projet Java dans Android Studio.

-----

Les options d’utilisation et de ligne de commande sont décrites dans la documentation [Embeddinator-4000](https://github.com/mono/Embeddinator-4000/blob/master/Usage.md#java--c) .

## <a name="callbacks"></a>Rappels

En savoir plus sur [les C# appels entre et Java](callbacks.md).

## <a name="samples"></a>Exemples

- [Exemple d’application météo](https://github.com/jamesmontemagno/embeddinator-weather)
