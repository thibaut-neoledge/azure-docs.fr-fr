<properties services="virtual-machines" title="Using Azure CLI with Azure Resource Manager" authors="squillace" solutions="" manager="timlt" editor="tysonn" />

<tags
   ms.service="virtual-machine"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="linux"
   ms.workload="infrastructure"
   ms.date="04/13/2015"
   ms.author="rasquill" />

## Utilisation de l’interface de ligne de commande Azure (CLI) avec Azure Resource Manager (ARM)

Pour utiliser l’interface de ligne de commande Azure avec les commandes et modèles de Resource Manager afin de déployer des ressources et charges de travail Azure à l’aide de groupes de ressources, vous devez (bien entendu) disposer d’un compte Azure. Si vous ne disposez pas d’un compte, vous pouvez obtenir une [version d’essai gratuite d’Azure ici](http://azure.microsoft.com/pricing/free-trial/).

> [AZURE.NOTE]Si vous ne disposez pas d’un compte Azure, mais d’un abonnement MSDN, vous pouvez obtenir des crédits Azure gratuits en activant vos [avantages d’abonné MSDN ici](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou utiliser le compte gratuit. Les deux possibilités permettent d’accéder à Azure.

### Étape 1 : vérification de la version de l’interface de ligne de commande Azure

Pour utiliser l’interface de ligne de commande pour des modèles ARM et des commandes impératives, vous devez disposer au moins de la version 0.8.17. Pour vérifier votre version, tapez `azure --version`. Le résultat suivant doit s’afficher :

    $ azure --version
    0.8.17 (node: 0.10.25)

Si vous souhaitez mettre à jour votre version de l’interface de ligne de commande, consultez la page [Interface de ligne de commande](https://github.com/Azure/azure-xplat-cli).

### Étape 2 : vérification de l’utilisation d’une identité professionnelle ou scolaire avec Azure

Vous ne pouvez utiliser le mode de commande ARM que si vous utilisez un [client Azure Active Directory](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) ou un [nom principal de service](https://msdn.microsoft.com/library/azure/dn132633.aspx). (Ils sont également appelés *ID d’organisation*.)

Pour voir si vous en avez un, connectez-vous en tapant `azure login` et en indiquant votre nom d’utilisateur professionnel ou scolaire et votre mot de passe à l’invite. Si vous en avez un, voici ce que vous devez voir :

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

Si tel n’est pas le cas, vous devez créer un client (ou un principal de service) avec votre identité de compte Microsoft. (Cela est souvent le cas avec les abonnements MSDN personnels ou les abonnements d’essai gratuits.) Pour créer un ID professionnel ou scolaire à partir de votre compte Azure créé avec un ID Microsoft, consultez la page [Association d’un annuaire Azure AD à un nouvel abonnement Azure](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant). Si vous pensez que vous devriez avoir un ID d’organisation, il se peut que vous deviez contacter la personne qui a créé le compte pour vous.

### Étape 3 : choix de votre abonnement Azure

Si votre compte Azure ne contient qu’un abonnement, l’interface de ligne de commande Azure s’associe à cet abonnement par défaut. Si vous avez plusieurs abonnements, vous devez sélectionner l’abonnement que vous souhaitez utiliser en tapant `azure account set <subscription id or name> true`, où _id ou nom d’abonnement_ est l’ID ou le nom d’abonnement que vous souhaitez utiliser dans la session active.

Un résultat tel que celui-ci doit s’afficher :

    $ azure account set "Azure Free Trial" true
    info:    Executing command account set
    info:    Setting subscription to "Azure Free Trial" with id "2lskd82-434-4730-9df9-akd83lsk92sa".
    info:    Changes saved
    info:    account set command OK

### Étape 4 : configuration de votre interface de ligne de commande en mode ARM

Pour utiliser le mode ARM (Azure Resource Management) avec l’interface de ligne de commande, tapez `azure config mode arm`. Un résultat tel que celui-ci doit s’afficher :

    $ azure config mode arm
    info:    New mode is arm

> [AZURE.NOTE]Vous pouvez revenir en arrière pour utiliser les commandes de gestion des services Azure en tapant `azure config mode asm`.

<!---HONumber=Oct15_HO3-->