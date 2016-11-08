---
title: Comment définir une API personnalisée dans un service mobile de backend .NET | Microsoft Docs
description: Découvrez comment définir un point de terminaison d’API personnalisée dans un service mobile de backend .NET.
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Procédure : définition d’un point de terminaison d’API personnalisée dans un service mobile de backend .NET
> [!div class="op_single_selector"]
> * [Backend JavaScript](mobile-services-javascript-backend-define-custom-api.md)
> * [Backend .NET](mobile-services-dotnet-backend-define-custom-api.md)
> 
> 

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Pour la version Mobile Apps équivalente de cette rubrique, consultez [Définir un contrôleur d’API personnalisé](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-custom-api-controller).
> 
> 

Cette rubrique montre comment définir un point de terminaison d’API personnalisée dans un service mobile de backend .NET. Une API personnalisée vous permet de définir des points de terminaison avec une fonctionnalité de serveur, mais elle ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture de base de données. En utilisant une API personnalisée, vous avez plus de contrôle sur la messagerie, notamment sur les en-têtes HTTP et le format du corps.

[!INCLUDE [mobile-services-dotnet-backend-create-custom-api](../../includes/mobile-services-dotnet-backend-create-custom-api.md)]

Pour plus d’informations sur la façon d’appeler une API personnalisée dans votre application à l’aide d’une bibliothèque cliente Mobile Services, consultez [Appeler une API personnalisée](mobile-services-windows-dotnet-how-to-use-client-library.md#custom-api) dans la référence du Kit de développement logiciel (SDK) du client.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

<!---HONumber=AcomDC_0727_2016-->