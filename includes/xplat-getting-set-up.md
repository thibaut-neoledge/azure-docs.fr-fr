<properties services="virtual-machines" title="Setting up xplat-cli for service management" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Utilisation de l'interface xplat-cli

Les étapes suivantes vous aident à utiliser facilement l'interface xplat-cli avec la version la plus récente et l'abonnement approprié. Si vous devez installer l'interface xplat-cli et la connecter d'abord à votre compte, consultez la rubrique [Interface de ligne de commande Azure (xplat-cli)](xplat-cli.md).

### Étape 1 : mise à jour de la version de l'interface xplat-cli

Pour utiliser l'interface xplat-cli dans le cas de commandes impératives avec le mode de gestion des services, vous devez avoir une version récente, si possible. Pour vérifier votre version, tapez `azure --version`. Le résultat suivant devrait s'afficher :

    $ azure --version
    0.8.17 (node: 0.10.25)
    
Si vous souhaitez mettre à jour votre version de l'interface xplat-cli, consultez la rubrique [Interface xplat-cli](https://github.com/Azure/azure-xplat-cli).

### Étape 2 : configuration de votre compte et de votre abonnement Azure

Une fois que vous avez connecté votre interface xplat-cli au compte que vous souhaitez utiliser, vous pouvez obtenir plus d'un abonnement. Le cas échéant, vous devez examiner les abonnements disponibles pour votre compte en tapant `azure account list`, puis en sélectionnant l'abonnement que vous souhaitez utiliser en tapant `azure account set <subscription id or name> true` où _id ou nom d'abonnement_ est l'id d'abonnement ou le nom d'abonnement que vous souhaitez utiliser dans la session active. Le résultat suivant pourrait s'afficher :

    $ azure account set "Visual Studio Ultimate with MSDN" true
    info:    Executing command account set
    info:    Setting subscription to "Visual Studio Ultimate with MSDN" with id "0e220bf6-5caa-4e9f-8383-51f16b6c109f".
    info:    Changes saved
    info:    account set command OK
    
> [AZURE.NOTE]Si vous ne disposez pas d'un compte Azure, mais d'un abonnement MSDN, vous pouvez obtenir des crédits Azure gratuits en activant vos [avantages d'abonné MSDN ici](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ; ou vous pouvez utiliser le compte gratuit. Les deux possibilités fonctionneront pour accéder à Azure.

<!--HONumber=52-->
