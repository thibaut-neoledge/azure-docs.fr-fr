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
	ms.date="12/29/2015"
	ms.author="danlep"/>

# Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)

L’interface Azure CLI offre un ensemble de commandes multiplateforme open-source pour l’utilisation de la plateforme Azure. Cet article vous explique comment vous connecter à votre abonnement Azure à partir de l’interface de ligne de commande Azure afin d’utiliser l’ensemble des commandes de l’interface de ligne de commande. Si vous n'avez pas déjà installé l’interface de ligne de commande, consultez [Installer l’interface de ligne de commande Azure](xplat-cli-install.md).



Pour vous connecter à votre abonnement à partir de l’interface de ligne de commande Azure, vous pouvez au choix :

* **Se connecter à Azure à l'aide d'un compte professionnel ou scolaire ou d'une identité de compte Microsoft**. Cette méthode utilise un type d'identité de compte quelconque pour l'authentification. L'interface de ligne de commande actuelle prend aussi en charge l'authentification interactive pour les comptes pour lesquels l'authentification multifacteur (multi-factor authentication) est activée. Après l'établissement d'une connexion de manière interactive, vous pouvez utiliser les commandes Resource Manager ou classiques (Service Management).

* **Télécharger et utiliser un fichier de paramètres de publication** : cette méthode installe sur votre ordinateur local un certificat qui vous permet d'exécuter les tâches de gestion pendant toute la durée de validité de l'abonnement et du certificat. Cette méthode vous permet d'utiliser uniquement les commandes classiques (Service Management).

Pour plus d'informations sur la gestion de l'authentification et de l'abonnement, consultez la rubrique [Quelle est la différence entre l'authentification basée sur les comptes et l'authentification basée sur les certificats][authandsub].

Si vous ne possédez pas de compte Azure, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][free-trial].

>[AZURE.NOTE] Si vous utilisez une version de l'interface de ligne de commande Azure antérieure à la version 0.9.10, vous pouvez utiliser la commande `azure login` uniquement avec des identités de comptes professionnels ou scolaires. Les ID de compte Microsoft ne fonctionnent pas. Cependant, vous pouvez utiliser une identité quelconque pour vous connecter à votre compte avec la commande `azure login` avec les versions 0.9.10 et ultérieures de l'interface de ligne de commande Azure.
>
Les versions 0.9.9 et ultérieures de l'interface de ligne de commande prennent en charge l'authentification multifacteur (multi-factor authentication).



## Utilisation de la méthode de connexion interactive

Utilisez la commande `azure login` (sans arguments) pour vous identifier de manière interactive avec :

- une identité de compte professionnel ou scolaire qui requiert une authentification multifacteur, ou
- une identité de compte Microsoft pour accéder à la fonctionnalité Resource Manager en mode déploiement

> [AZURE.NOTE]  Dans les deux cas, l'authentification et l'autorisation sont effectuées à l'aide d'Azure Active Directory. Si vous utilisez une identité de compte Microsoft, le processus de connexion accède à votre domaine par défaut Azure Active Directory. (Si vous disposez d'une version d'évaluation gratuite, vous ne remarquerez peut-être pas qu'Azure Active Directory a créé un domaine par défaut pour votre compte.)

L'établissement d'une connexion interactive est simple : saisissez `azure login`, puis suivez les invites comme indiqué ci-dessous :

	azure login                                                                                                                                                                                         
	info:    Executing command login
	info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate. If you're signing in as an Azure AD application, use the --username and --password parameters.

Copiez le code proposé ci-dessus et ouvrez un navigateur à l'adresse http://aka.ms/devicelogin. Entrez le code et vous serez invité à entrer le nom d'utilisateur et le mot de passe pour l'identité que vous souhaitez utiliser. Une fois le processus terminé, l'interface de commande termine le processus de connexion. Le résultat suivant peut s'afficher :

	info:    Added subscription Visual Studio Ultimate with MSDN
	info:    Added subscription Azure Free Trial
	info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
	+
	info:    login command OK

## Utilisation d'une connexion non interactive avec un compte professionnel ou scolaire


La méthode de connexion non interactive ne fonctionne qu'avec un compte professionnel ou scolaire, également appelé *compte d'organisation*. Ce compte est géré par votre organisation et est défini dans l’instance Azure Active Directory de l’organisation. Vous pouvez [créer un compte d'organisation](#create-an-organizational-account) si vous n'en avez pas, ou vous pouvez [créer un ID professionnel ou scolaire depuis votre ID de compte Microsoft](./virtual-machines/resource-group-create-work-id-from-personal.md). Pour cela, vous devez spécifier un nom d'utilisateur ou un nom d'utilisateur et un mot de passe pour la commande `azure login`, comme dans l'exemple suivant :

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Entrez votre mot de passe lorsque vous y êtes invité.

	If this is your first time logging in with these credentials, you are asked to verify that you wish to cache an authentication token. This prompt also occurs if you have previously used the `azure logout` command (described below). To bypass this prompt for automation scenarios, run `azure login` with the `-q` parameter.

* **Pour vous déconnecter**, utilisez la commande suivante :

		azure logout -u <username>

	Si les abonnements associés au compte étaient authentifiés uniquement avec Active Directory, la déconnexion supprime les informations d'abonnement du profil local. Toutefois, si un fichier de paramètres de publication avait également été importé pour les abonnements, la déconnexion supprime uniquement les informations Active Directory associées du profil local.

## Utilisation de la méthode basée sur un fichier de paramètres de publication

Si vous n’avez besoin d’utiliser que les commandes d’interface de ligne de commande classiques (Service Management), vous pouvez vous connecter à l'aide d'un fichier de paramètres de publication.

* **Pour télécharger le fichier de paramètres de publication** de votre compte, utilisez la commande suivante :

		azure account download

Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au [portail Azure Classic][portal]. Une fois que vous êtes connecté, un fichier `.publishsettings` se télécharge. Prenez note de son emplacement.

> [AZURE.NOTE] Si votre compte est associé à plusieurs clients Azure Active Directory, vous pouvez être invité à sélectionner l'annuaire Active Directory pour lequel vous voulez télécharger un fichier de paramètres de publication.
>
> Une fois l’annuaire Active Directory sélectionné à partir de la page de téléchargement ou par le biais du portail Azure Classic, il est utilisé par défaut par le portail Classic et par la page de téléchargement. Une fois qu'un paramètre par défaut a été défini, le texte « __cliquez ici pour revenir à la page de sélection__ » apparaît en haut de la page de téléchargement. Utilisez le lien affiché pour revenir à la page de sélection.

* **Pour importer le fichier de paramètres de publication**, exécutez la commande suivante :

		azure account import <path to your .publishsettings file>

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

> [AZURE.NOTE] Le changement d’abonnement par défaut prend effet immédiatement et au niveau global. Les nouvelles commandes Azure CLI, qu’elles soient exécutées à partir de la même instance de ligne de commande ou d’une autre instance, utilisent le nouvel abonnement par défaut.

Si vous souhaitez utiliser avec Azure CLI un abonnement autre que l’abonnement par défaut, mais sans changer ce dernier, vous pouvez utiliser l’option `--subscription` et fournir le nom de l’abonnement à utiliser pour l’opération.

Une fois connecté à votre abonnement Azure, vous pouvez commencer à utiliser les commandes Azure CLI.

## Stockage des paramètres de l'interface de ligne de commande

Que vous vous connectiez avec un compte professionnel ou scolaire ou que vous importiez des paramètres de publication, votre profil d'interface de ligne de commande et vos journaux sont stockés dans un répertoire `.azure` situé dans votre répertoire `user`. Le répertoire `user` est protégé par le système d’exploitation ; toutefois, il est recommandé de prendre des mesures supplémentaires pour chiffrer le répertoire `user`. Pour ce faire, procédez comme suit :

* Sur Windows, modifiez les propriétés d'annuaire ou utilisez BitLocker.
* Sur Mac, activez FileVault pour l'annuaire.
* Sous Ubuntu, utilisez la fonctionnalité d’annuaire Encrypted Home. Les autres versions de Linux offrent des fonctionnalités similaires.

## Ressources supplémentaires

* [Utilisation de l’interface de ligne de commande Azure avec les commandes (classiques) de gestion des services][cliasm]

* [Utilisation de l’interface de ligne de commande Azure avec les commandes du Gestionnaire des commandes][cliarm]

* Pour plus d'informations sur l'interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir [Référentiel GitHub pour l'interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous rencontrez des problèmes lors de l’utilisation d’Azure ou de l’interface de ligne de commande Azure, consultez les [forums Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0204_2016-->