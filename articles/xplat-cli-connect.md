<properties
	pageTitle="Se connecter à Azure à partir de l'interface de ligne de commande | Microsoft Azure"
	description="Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI) pour Mac, Linux et Windows"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="danlep"/>

# Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)

L’interface Azure CLI offre un ensemble de commandes multiplateforme open-source pour l’utilisation de la plateforme Azure. Cet article vous explique comment vous connecter à votre abonnement Azure à partir de l’interface de ligne de commande Azure afin d’utiliser l’ensemble des commandes de l’interface de ligne de commande. Si vous n'avez pas déjà installé l’interface de ligne de commande, consultez [Installer l’interface de ligne de commande Azure](xplat-cli-install.md).


[AZURE.INCLUDE [learn-about-deployment-models](../includes/learn-about-deployment-models-both-include.md)]


Pour vous connecter à votre abonnement à partir de l’interface de ligne de commande Azure, vous pouvez au choix :

* **Vous connecter à Azure à l'aide d'un compte professionnel ou scolaire** : cette méthode utilise Azure Active Directory pour authentifier les informations d'identification. À partir de la version 0.9.9, l’interface de ligne de commande prend en charge les comptes professionnels activés pour l'authentification multifacteur. Après la connexion, vous pouvez utiliser les commandes Gestionnaire des ressources ou classiques (Service Management).

* **Télécharger et utiliser un fichier de paramètres de publication** : cette méthode installe un certificat qui vous permet d'exécuter les tâches de gestion pendant toute la durée de validité de l'abonnement et du certificat. Cette méthode vous permet d'utiliser uniquement les commandes classiques (Service Management).

Pour plus d'informations sur la gestion de l'authentification et de l'abonnement, consultez la rubrique [Quelle est la différence entre l'authentification basée sur les comptes et l'authentification basée sur les certificats][authandsub].

Si vous ne possédez pas de compte Azure, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][free-trial].


## Utilisation de la méthode par connexion

La méthode de connexion ne fonctionne qu’avec un *compte professionnel* ou scolaire. Ce compte est géré par votre organisation et est défini dans l’instance Azure Active Directory de l’organisation. Vous pouvez [créer un compte professionnel](#create-an-organizational-account) si vous n'en avez pas.


* **Pour vous connecter** à partir d’Azure CLI à l’aide d’un compte professionnel ou scolaire, utilisez la commande suivante :

	```
	azure login -u <username>
	```

	Entrez votre mot de passe lorsque vous y êtes invité.

	Si c’est la première fois que vous vous connectez avec ces informations d'identification, vous êtes invité à vérifier que vous souhaitez bien mettre en cache un jeton d'authentification. Ce message s’affiche également si vous avez préalablement utilisé la commande `azure logout` (décrite plus loin). Si vous voulez ignorer cette invite pour les scénarios d’automatisation, exécutez `azure login` avec le paramètre `-q`.

* **Pour vous déconnecter**, utilisez la commande suivante :

	```
	azure logout -u <username>
	```

	Si les abonnements associés au compte étaient authentifiés uniquement avec Active Directory, la déconnexion supprime les informations d'abonnement du profil local. Toutefois, si un fichier de paramètres de publication avait également été importé pour les abonnements, la déconnexion supprime uniquement les informations Active Directory associées du profil local.

### Authentification multifacteur
À partir de la version 0.9.9 de l’interface de commande Azure, vous pouvez vous connecter avec un compte professionnel qui utilise l'authentification multifacteur (authentification avec un mot de passe et une ou plusieurs méthodes de vérification supplémentaires, par exemple un appareil de confiance ou d’autres données personnelles).

Une fois que vous avez exécuté `azure login` avec le nom d'utilisateur et un mot de passe du compte, l’interface de ligne de commande fournit l'adresse d'une page web que vous devez ouvrir. Les instructions vous conduisent à entrer un code sur cette page pour poursuivre l'authentification. Une fois la stratégie d'authentification satisfaite, l’interface de ligne de commande Azure termine le processus de connexion.


### Créer un compte professionnel

Si vous ne possédez pas de compte professionnel ou scolaire et que vous utilisez un compte personnel pour vous connecter à votre abonnement Azure, vous pouvez créer un compte professionnel facilement en procédant comme suit.

1. Connectez-vous au [portail Azure][portal], puis sélectionnez **Active Directory**.

2. S'il n'existe aucun annuaire, sélectionnez **Create your directory** et fournissez les informations demandées.

3. Sélectionnez votre annuaire et ajoutez un nouvel utilisateur. Ce nouvel utilisateur est un compte professionnel ou scolaire.

	Pendant la création de l’utilisateur, une adresse de messagerie est fournie pour l’utilisateur, ainsi qu’un mot de passe temporaire. Conservez ces informations, car vous en aurez besoin par la suite.

4. Dans le portail Azure, sélectionnez **Paramètres**, puis **Administrateurs**. Sélectionnez **Ajouter** et ajoutez le nouvel utilisateur en tant que co-administrateur. Cela permet au compte professionnel ou scolaire de gérer votre abonnement Azure.

5. Pour finir, déconnectez-vous du portail Azure, puis reconnectez-vous en utilisant le nouveau compte professionnel ou scolaire. Si c’est la première fois que vous vous connectez avec ce compte, vous êtes invité à changer le mot de passe.

	Assurez-vous que vos abonnements soient visibles lorsque vous vous connectez avec le nouveau compte.

Pour plus d’informations sur les comptes professionnels ou scolaires, voir l’article [Inscription à Azure en tant qu’organisation][signuporg].

## Utilisation de la méthode basée sur un fichier de paramètres de publication

Si vous n’avez besoin d’utiliser que les commandes d’interface de ligne de commande classiques (Service Management), vous pouvez vous connecter à l'aide d'un fichier de paramètres de publication.

* **Pour télécharger le fichier de paramètres de publication** de votre compte, utilisez la commande suivante :

	```
	azure account download
	```

	Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au [portail Azure][portal]. Une fois que vous êtes connecté, un fichier `.publishsettings` se télécharge. Prenez note de son emplacement.

	> [AZURE.NOTE]Si votre compte est associé à plusieurs clients Azure Active Directory, vous pouvez être invité à sélectionner l'annuaire Active Directory pour lequel vous voulez télécharger un fichier de paramètres de publication.
	>
	> Une fois l’annuaire Active Directory sélectionné à partir de la page de téléchargement ou par le biais du portail Azure, il est utilisé par défaut par le portail et par la page de téléchargement. Une fois qu'un paramètre par défaut a été défini, le texte « __cliquez ici pour revenir à la page de sélection__ » apparaît en haut de la page de téléchargement. Utilisez le lien affiché pour revenir à la page de sélection.

* **Pour importer le fichier de paramètres de publication**, exécutez la commande suivante :

	```
	azure account import <path to your .publishsettings file>
	```

	Après l’importation de vos paramètres de publication, vous devez supprimer le fichier `.publishsettings`, car il n’est plus nécessaire à l’interface de ligne de commande Azure, et représente un risque pour la sécurité dans la mesure où il peut être utilisé pour accéder à votre abonnement.


## Abonnements multiples

Si vous possédez plusieurs abonnements Azure, la connexion à Azure donne accès à tous les abonnements associés à vos informations d’identification. Un abonnement spécifique est sélectionné comme abonnement par défaut et est utilisé par l’interface de ligne de commande Azure durant l’exécution des opérations. Vous pouvez afficher les abonnements et identifier l’abonnement par défaut à l’aide de la commande `azure account list`. Cette commande renvoie des informations similaires aux suivantes :

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

Dans la liste ci-dessus, la colonne **Current** indique que l’abonnement par défaut actuel est Azure-sub-1. Pour modifier l’abonnement par défaut, utilisez la commande `azure account set` et spécifiez l’abonnement à utiliser par défaut. Par exemple :

	azure account set Azure-sub-2

Cette commande sélectionne Azure-sub-2 comme abonnement par défaut.

> [AZURE.NOTE]Le changement d’abonnement par défaut prend effet immédiatement et au niveau global. Les nouvelles commandes Azure CLI, qu’elles soient exécutées à partir de la même instance de ligne de commande ou d’une autre instance, utilisent le nouvel abonnement par défaut.

Si vous souhaitez utiliser avec Azure CLI un abonnement autre que l’abonnement par défaut, mais sans changer ce dernier, vous pouvez utiliser l’option `--subscription` et fournir le nom de l’abonnement à utiliser pour l’opération.

Une fois connecté à votre abonnement Azure, vous pouvez commencer à utiliser les commandes Azure CLI. Pour plus d’informations, voir l’article [Utilisation de l’interface de ligne de commande Azure](xplat-cli-install.md).

## Stockage des paramètres de l'interface de ligne de commande

Que vous vous connectiez avec un compte professionnel ou scolaire ou que vous importiez des paramètres de publication, votre profil CLI et vos journaux sont stockés dans un répertoire `.azure` situé dans votre répertoire `user`. Le répertoire `user` est protégé par le système d’exploitation ; toutefois, il est recommandé de prendre des mesures supplémentaires pour chiffrer le répertoire `user`. Pour ce faire, procédez comme suit :

* Sur Windows, modifiez les propriétés d'annuaire ou utilisez BitLocker.
* Sur Mac, activez FileVault pour l'annuaire.
* Sous Ubuntu, utilisez la fonctionnalité d’annuaire Encrypted Home. Les autres versions de Linux offrent des fonctionnalités similaires.

## Ressources supplémentaires

* [Utilisation de l’interface de ligne de commande Azure avec les commandes (classiques) de gestion des services][cliasm]

* [Utilisation de l’interface de ligne de commande Azure avec les commandes du Gestionnaire des commandes][cliarm]

* Pour plus d’informations sur l’interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir [Référentiel GitHub pour l’interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli).

* Si vous rencontrez des problèmes lors de l’utilisation d’Azure ou de l’interface de ligne de commande Azure, consultez les [forums Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=Oct15_HO3-->