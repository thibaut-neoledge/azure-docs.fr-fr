<properties services="virtual-machines" title="Using the xplat-cli with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Utilisation de l'interface xplat-cli avec Azure Resource Manager \(ARM\)

Avant de pouvoir utiliser l'interface xplat-cli avec les commandes et les modèles du Gestionnaire de ressources pour déployer des ressources et des charges de travail Azure à l'aide de groupes de ressources, vous devez disposer d'un compte Azure \(naturellement\). Si vous ne disposez pas d'un compte, vous pouvez obtenir un [essai d'Azure gratuit ici](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE] Si vous ne disposez pas d'un compte Azure, mais d'un abonnement MSDN, vous pouvez obtenir des crédits Azure gratuits en activant vos [avantages d'abonné MSDN ici](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ; ou vous pouvez utiliser le compte gratuit. Les deux possibilités fonctionneront pour accéder à Azure.

### Étape 1 : vérification de la version de l'interface xplat-cli

Pour utiliser l'interface xplat-cli pour des commandes impératives et des modèles ARM, vous devez disposer au moins de la version 0.8.17. Pour vérifier votre version, tapez `azure --version`. Le résultat suivant devrait s'afficher :

    $ azure --version
    0.8.17 (node: 0.10.25)
    
Si vous devez mettre à jour votre version de l'interface xplat-cli, consultez la rubrique [Interface xplat-cli](https://github.com/Azure/azure-xplat-cli).

### Étape 2 : vérification de l'utilisation d'une identité professionnelle ou scolaire avec Azure

Vous pouvez uniquement utiliser le mode de commande ARM si vous utilisez un [client Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) ou un [nom du principal du service](https://msdn.microsoft.com/library/azure/dn132633.aspx). \(Ils sont également appelés *ID professionnels*.\)

Pour voir si vous en disposez, connectez-vous en tapant `azure login` et en indiquant votre nom d'utilisateur de l'établissement et votre mot de passe lorsque vous y êtes invité. Si vous n'en avez pas, voici ce que vous devez voir :

    $ azure login
    info:    Executing command login
    warn:    Please note that currently you can login only via Microsoft organizational account or service principal. For instructions on how to set them up, please read http://aka.ms/Dhf67j.
    Username: ahmet@contoso.onmicrosoft.com
    Password: *********
    |info:    Added subscription Visual Studio Ultimate with MSDN                  
    info:    Setting subscription Visual Studio Ultimate with MSDN as default
    info:    Added subscription Azure Free Trial
    +
    info:    login command OK
    
Si vous ne voyez pas cette option, vous devez créer un client \(ou principal du service\) avec votre identité de compte Microsoft. \(Cela est souvent le cas avec les abonnements MSDN personnels ou les abonnements d'essai gratuits.\) Pour créer un id professionnel ou scolaire à partir de votre compte Azure créé avec un id Microsoft, consultez la page [Association d'un annuaire Azure AD à un nouvel abonnement Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Si vous pensez déjà posséder un id professionnel, vous devrez contacter la personne qui a créé le compte pour vous.

### Étape 3 : choix de votre abonnement Azure

Si vous avez un seul abonnement dans votre compte Azure, l'interface xplat-cli associe cet abonnement par défaut. Si vous avez plusieurs abonnements, vous devez sélectionner l'abonnement que vous souhaitez utiliser en tapant `azure account set <subscription id or name> true`, où _id ou nom d'abonnement_ est l'id d'abonnement ou le nom d'abonnement que vous souhaitez utiliser dans la session active.

Le résultat suivant pourrait s'afficher :

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK
    
### Étape 4 : placement de votre interface xplat-cli en mode ARM

Pour utiliser le mode de gestion de ressources Azure \(ARM, Azure Resource Management\) avec l'interface xplat-cli, tapez `azure config mode arm`. Le résultat suivant pourrait s'afficher :

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE] Vous pouvez revenir en arrière pour utiliser les commandes de gestion des services Azure en tapant `azure config mode asm`.



<!--HONumber=52-->
