---
title: "Se connecter à Azure à partir de l&quot;interface de ligne de commande | Microsoft Docs"
description: "Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI) pour Mac, Linux et Windows"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.lasthandoff: 03/21/2017


---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Connectez-vous à Azure à partir de l’interface de ligne de commande (CLI) Azure
La CLI Azure offre un ensemble de commandes multiplateforme open-source pour l’utilisation des ressources Azure. Cet article décrit les différentes méthodes permettant de fournir vos informations d’identification de compte Azure pour connecter l’interface de ligne de commande Azure à votre abonnement Azure :

* Exécutez la commande CLI `azure login` pour vous authentifier via Azure Active Directory. Cette méthode vous permet d’accéder aux commandes CLI dans les deux [modes de commande](#cli-command-modes). Lorsque vous exécutez la commande sans options supplémentaires, `azure login` vous invite à poursuivre la procédure de connexion de manière interactive via un portail web. Pour plus d’options de commande `azure login`, consultez les scénarios décrits dans cet article, ou tapez `azure login --help`.
* Si vous devez uniquement utiliser les commandes CLI en mode Azure Service Management (non recommandé pour la plupart des nouveaux déploiements), vous pouvez télécharger et installer un fichier de paramètres de publication sur votre ordinateur.

Si vous n'avez pas déjà installé l’interface de ligne de commande, consultez [Installer l’interface de ligne de commande Azure](cli-install-nodejs.md). Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](http://azure.microsoft.com/free/) en quelques minutes.

Pour obtenir des informations sur les différentes identités de comptes et des différents abonnements Azure, consultez la rubrique [Association des abonnements Azure avec Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="scenario-1-azure-login-with-interactive-login"></a>Scénario 1 : connexion à Azure avec une connexion interactive
Avec certains comptes, la CLI vous demande d’exécuter `azure login` puis de poursuivre le processus de connexion avec un navigateur web via un portail web. Il s’agit de ce que l’on appelle une *connexion interactive*. Ce processus est souvent utilisé avec un compte professionnel ou scolaire (également appelé *compte organisationnel*) qui est configuré pour exiger l’authentification multifacteur. Vous pouvez également utilisez la connexion interactive avec votre compte Microsoft lorsque vous souhaitez utiliser les commandes en mode Resource Manager.

La connexion interactive est simple d’utilisation : il vous suffit de taper `azure login` sans aucune option, comme illustré dans l’exemple suivant :

```
azure login
```                                                                                             

La sortie doit ressembler à ceci :

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
Copiez le code proposé dans la sortie de commande et ouvrez un navigateur sur la page http://aka.ms/devicelogin ou une autre page, le cas échéant. (Vous pouvez ouvrir un navigateur sur le même ordinateur ou sur un autre ordinateur ou appareil.) Entrez le code et vous serez invité à entrer le nom d'utilisateur et le mot de passe pour l'identité que vous souhaitez utiliser. Une fois le processus terminé, l’interpréteur de commandes exécute le processus de connexion. Le résultat suivant peut s'afficher :

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> Avec la connexion interactive, l’authentification et l’autorisation sont effectuées à l’aide d’Azure Active Directory. Si vous utilisez une identité de compte Microsoft, le processus de connexion accède à votre domaine par défaut Azure Active Directory. (Si vous disposez d’un compte Azure gratuit, Azure Active Directory a peut-être créé automatiquement un domaine par défaut pour votre compte.)
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>Scénario 2 : connexion à azure avec un nom d’utilisateur et un mot de passe
Utilisez la commande `azure login` avec un paramètre de nom d’utilisateur (`-u`) pour vous authentifier quand vous voulez utiliser un compte professionnel ou scolaire ne nécessitant pas l’authentification multifacteur. La ligne de commande vous invite à saisir le mot de passe (ou vous pouvez également passer le mot de passe en tant que paramètre supplémentaire de la commande `azure login`). L’exemple suivant passe le nom d’utilisateur d’un compte de société :

    azure login -u myUserName@contoso.onmicrosoft.com

Vous êtes ensuite invité à saisir votre mot de passe :

    info:    Executing command login
    Password: *********

Le processus de connexion est terminé.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

Si c’est la première fois que vous vous connectez avec ces informations d'identification, vous êtes invité à vérifier que vous souhaitez bien mettre en cache un jeton d'authentification. Ce message s’affiche également si vous avez préalablement utilisé la commande `azure logout` (décrite plus loin dans cet article). Si vous voulez ignorer cette invite pour les scénarios d’automatisation, exécutez `azure login` avec le paramètre `-q`.

## <a name="scenario-3-azure-login-with-a-service-principal"></a>Scénario 3 : connexion à azure avec un principal du service
Si vous créez un principal du service pour une application Active Directory, et que ce principal du service dispose d’autorisations sur votre abonnement, vous pouvez utiliser la commande `azure login` pour authentifier le principal du service. En fonction de votre scénario, vous pouvez fournir les informations d’identification du principal du service sous la forme de paramètres explicites de la commande `azure login`. Par exemple, la commande suivante transmet le nom du principal du service et l’identifiant de locataire Active Directory :

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

Vous êtes ensuite invité à saisir votre mot de passe. Vous pouvez également fournir les informations d’identification via un script CLI ou un code d’application, ou utiliser un certificat pour authentifier le principal du service en mode non interactif pour les scénarios d’automatisation. Pour obtenir plus d’informations et des exemples, consultez l’article [Authentification d’un principal du service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal-cli.md).

## <a name="scenario-4-use-a-publish-settings-file"></a>Scénario 4 : utilisation d’un fichier de paramètres de publication
Si vous n’avez besoin d’utiliser que les commandes d’interface de ligne de commande du mode Azure Service Management (par exemple, pour déployer des machines virtuelles Azure dans le modèle de déploiement Classic), vous pouvez vous connecter à l’aide d’un fichier de paramètres de publication. Cette méthode installe sur votre ordinateur local un certificat qui vous permet d’exécuter les tâches de gestion pendant toute la durée de validité de l’abonnement et du certificat.

* **Pour télécharger le fichier de paramètres de publication** de votre compte, tapez `azure config mode asm` pour vérifiez que la CLI est en mode Service Management. Exécutez ensuite la commande suivante :

        azure account download

Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au [portail Azure Classic](https://manage.windowsazure.com). Une fois que vous êtes connecté, un fichier `.publishsettings` se télécharge. Prenez note de son emplacement.

> [!NOTE]
> Si votre compte est associé à plusieurs clients Azure Active Directory, vous pouvez être invité à sélectionner l'annuaire Active Directory pour lequel vous voulez télécharger un fichier de paramètres de publication.
>
>

Une fois l’annuaire Active Directory sélectionné à partir de la page de téléchargement ou par le biais du portail Azure Classic, il est utilisé par défaut par le portail Classic et par la page de téléchargement. Une fois qu’un paramètre par défaut a été défini, le texte **« cliquez ici pour revenir à la page de sélection »** apparaît en haut de la page de téléchargement. Utilisez le lien affiché pour revenir à la page de sélection.

* Exécutez la commande suivante **pour importer le fichier de paramètres de publication** :

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> Après avoir importé vos paramètres de publication, vous devez supprimer le fichier `.publishsettings`. Il n’est plus nécessaire à l’interface de ligne de commande Azure, et représente un risque pour la sécurité dans la mesure où il pourrait être utilisé pour accéder à votre abonnement.
>
>

## <a name="cli-command-modes"></a>Modes des commandes de l’interface de ligne de commande
L’interface de ligne de commande Azure propose deux modes de commande pour l’utilisation des ressources Azure, avec divers jeux de commandes :

* **Mode Resource Manager** : pour utiliser des ressources Azure dans le modèle de déploiement Resource Manager. Pour définir ce mode, exécutez `azure config mode arm`.
* **Mode Service Management** : pour utiliser des ressources Azure dans le modèle de déploiement Classic. Pour définir ce mode, exécutez `azure config mode asm`.

Lors de la première installation, la version actuelle de l’interface CLI est en mode Resource Manager.

> [!NOTE]
> Le mode Resource Manager et le mode Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.
>
>

## <a name="multiple-subscriptions"></a>Abonnements multiples
Si vous possédez plusieurs abonnements Azure, la connexion à Azure donne accès à tous les abonnements associés à vos informations d’identification. Un abonnement spécifique est sélectionné comme abonnement par défaut et est utilisé par l’interface de ligne de commande Azure durant l’exécution des opérations. Vous pouvez afficher les abonnements, y compris l’abonnement par défaut actuel, à l’aide de la commande `azure account list`. Cette commande renvoie des informations similaires aux suivantes :

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

Dans la liste ci-dessus, la colonne **Current** indique que l’abonnement par défaut actuel est Azure-sub-1. Pour modifier l’abonnement par défaut, utilisez la commande `azure account set` et spécifiez l’abonnement à utiliser par défaut. Par exemple :

    azure account set Azure-sub-2

Cette commande sélectionne Azure-sub-2 comme abonnement par défaut.

> [!NOTE]
> La modification de l’abonnement par défaut prend effet immédiatement à un niveau global ; les nouvelles commandes CLI Azure, que vous les exécutiez à partir de la même instance de ligne de commande ou d’une autre instance, utilisent le nouvel abonnement par défaut.
>
>

Si vous souhaitez utiliser avec Azure CLI un abonnement autre que l’abonnement par défaut, mais sans changer ce dernier, vous pouvez utiliser l’option `--subscription` et fournir le nom de l’abonnement à utiliser pour l’opération.

Une fois connecté à votre abonnement Azure, vous pouvez commencer à utiliser les commandes de l’interface de ligne de commande Azure pour travailler avec les ressources Azure.

## <a name="storage-of-cli-settings"></a>Stockage des paramètres de l'interface de ligne de commande
Que vous vous connectiez avec la commande `azure login` ou que vous importiez des paramètres de publication, votre profil d’interface de ligne de commande et vos journaux sont stockés dans un répertoire `.azure` situé dans votre répertoire `user`. Votre répertoire `user` est protégé par votre système d’exploitation. Nous vous recommandons toutefois de prendre des mesures supplémentaires pour chiffrer votre répertoire `user`. Pour ce faire, procédez comme suit :

* Sur Windows, modifiez les propriétés d'annuaire ou utilisez BitLocker.
* Sur Mac, activez FileVault pour l'annuaire.
* Sous Ubuntu, utilisez la fonctionnalité d’annuaire Encrypted Home. Les autres versions de Linux offrent des fonctionnalités similaires.

## <a name="logging-out"></a>Déconnexion
Pour vous déconnecter, utilisez la commande suivante :

    azure logout -u <username>

Si les abonnements associés au compte sont authentifiés uniquement avec Active Directory, la déconnexion supprime les informations d’abonnement du profil local. Toutefois, si un fichier de paramètres de publication a également été importé pour les abonnements, la déconnexion supprime uniquement les informations Active Directory associées du profil local.

## <a name="next-steps"></a>Étapes suivantes
* Pour utiliser les commandes de l’interface de ligne de commande Azure, consultez les pages [Commandes de l’interface de ligne de commande Azure en mode Resource Manager](virtual-machines/azure-cli-arm-commands.md) et [Commandes de l’interface de ligne de commande Azure en mode Service Management](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* Pour plus d'informations sur l'interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir [Référentiel GitHub pour l'interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli)(en anglais).
* Si vous rencontrez des problèmes lors de l’utilisation d’Azure ou de l’interface de ligne de commande Azure, consultez les [forums Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

