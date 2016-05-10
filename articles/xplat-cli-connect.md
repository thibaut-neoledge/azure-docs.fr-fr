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
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2016"
	ms.author="danlep"/>

# Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)

L’interface Azure CLI offre un ensemble de commandes multiplateforme open-source pour l’utilisation de la plateforme Azure. Cet article décrit les méthodes permettant de fournir vos informations d’identification de compte Azure pour connecter l’interface de ligne de commande Azure à votre abonnement Azure. Si vous n'avez pas déjà installé l’interface de ligne de commande, consultez [Installer l’interface de ligne de commande Azure](xplat-cli-install.md). Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](http://azure.microsoft.com/free/) en quelques minutes.

Pour vous connecter à votre abonnement à partir de l’interface de ligne de commande Azure, vous pouvez au choix :

* **Se connecter à Azure à l’aide d’un compte professionnel ou scolaire ou d’une identité de compte Microsoft** : Utilisez la commande `azure login` dans l’interface de ligne de commande version 0.9.10 et versions ultérieures un type d’identité de compte quelconque pour l’authentification à l’aide d’Azure Active Directory. L’interface de ligne de commande version 0.9.9 et versions ultérieures prend également en charge l’authentification interactive à l’aide d’un portail web pour les comptes pour lesquels l’authentification multifacteur est activée. Utilisez également la commande `azure login` pour authentifier un principal du service pour une application Azure Active Directory, ce qui est utile pour l’exécution des services automatisés. Une fois connecté avec une identité de compte prise en charge, vous pouvez utiliser les commandes du mode Azure Resource Manager ou du mode Azure Service Management.

* **Télécharger et utiliser un fichier de paramètres de publication** : cette méthode installe sur votre ordinateur local un certificat qui vous permet d'exécuter les tâches de gestion pendant toute la durée de validité de l'abonnement et du certificat. Cette méthode vous permet d’utiliser uniquement les commandes du mode Azure Service Management.

>[AZURE.NOTE] Si vous utilisez une version de l’interface de ligne de commande Azure antérieure à la version 0.9.10, vous pouvez utiliser la commande `azure login` uniquement avec un compte professionnel ou scolaire. Les identités de comptes Microsoft ne fonctionnent pas. Toutefois, si vous le souhaitez, vous pouvez [créer un ID professionnel ou scolaire à partir de votre ID de compte Microsoft](virtual-machines/virtual-machines-windows-create-aad-work-id.md).

Pour obtenir des informations sur les différentes identités de comptes et des différents abonnements Azure, consultez [Association des abonnements Azure avec Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).

## Utiliser la commande azure login pour effectuer une authentification interactive à l’aide d’un portail web

Utilisez la commande `azure login` (sans arguments) pour vous identifier de manière interactive avec :

- une identité de compte professionnel ou scolaire (également appelé *compte de société*) qui requiert une authentification multifacteur, ou
- une identité de compte Microsoft pour accéder aux commandes du mode Resource Manager.

> [AZURE.NOTE]  Dans les deux cas, l'authentification et l'autorisation sont effectuées à l'aide d'Azure Active Directory. Si vous utilisez une identité de compte Microsoft, le processus de connexion accède à votre domaine par défaut Azure Active Directory. (Si vous disposez d’un compte Azure gratuit, vous ne savez peut-être pas qu’Azure Active Directory a créé un domaine par défaut pour votre compte.)

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

## Utiliser la commande azure login avec le nom d’utilisateur et le mot de passe d’un compte de société


Utilisez la commande `azure login` avec un paramètre de nom d’utilisateur ou bien avec un nom d’utilisateur et un mot de passe pour effectuer l’authentification quand vous voulez utiliser un compte professionnel ou scolaire ne nécessitant pas l’authentification multifacteur. L’exemple suivant passe le nom d’utilisateur d’un compte de société :

	azure login -u ahmet@contoso.onmicrosoft.com
	info:    Executing command login
	Password: *********
	|info:    Added subscription Visual Studio Ultimate with MSDN
	+
	info:    login command OK

Entrez votre mot de passe lorsque vous y êtes invité.

Si c’est la première fois que vous vous connectez avec ces informations d'identification, vous êtes invité à vérifier que vous souhaitez bien mettre en cache un jeton d'authentification. Ce message s’affiche également si vous avez préalablement utilisé la commande `azure logout` (décrite plus loin dans cet article). Si vous voulez ignorer cette invite pour les scénarios d’automatisation, exécutez `azure login` avec le paramètre `-q`.

   

## Utiliser la commande azure login avec un principal du service

Si vous avez créé un principal du service pour une application Active Directory, et que ce principal du service dispose d’autorisations sur votre abonnement, vous pouvez utiliser la commande `azure login` pour authentifier le principal du service. En fonction de votre scénario, vous pouvez fournir les informations d’identification du principal du service sous la forme de paramètres explicites de la commande `azure login` ou à l’aide d’un script d’interface de ligne de commande ou d’un code d’application. Vous pouvez également utiliser un certificat pour authentifier le principal du service de façon non interactive pour les scénarios d’automatisation. Pour obtenir plus d’informations et des exemples, consultez l’article [Authentification d’un principal du service à l’aide d’Azure Resource Manager](resource-group-authenticate-service-principal.md).

## Utilisation de la méthode basée sur un fichier de paramètres de publication

Si vous n’avez besoin d’utiliser que les commandes d’interface de ligne de commande du mode Azure Service Management, vous pouvez vous connecter à l’aide d’un fichier de paramètres de publication.

* **Pour télécharger le fichier de paramètres de publication** de votre compte, utilisez la commande suivante (disponible uniquement en mode Service Management) :

		azure account download

    Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au [portail Azure Classic](https://manage.windowsazure.com). Une fois que vous êtes connecté, un fichier `.publishsettings` se télécharge. Prenez note de son emplacement.

    > [AZURE.NOTE] Si votre compte est associé à plusieurs clients Azure Active Directory, vous pouvez être invité à sélectionner l'annuaire Active Directory pour lequel vous voulez télécharger un fichier de paramètres de publication.

    Une fois l’annuaire Active Directory sélectionné à partir de la page de téléchargement ou par le biais du portail Azure Classic, il est utilisé par défaut par le portail Classic et par la page de téléchargement. Une fois qu'un paramètre par défaut a été défini, le texte « __cliquez ici pour revenir à la page de sélection__ » apparaît en haut de la page de téléchargement. Utilisez le lien affiché pour revenir à la page de sélection.

* Exécutez la commande suivante **pour importer le fichier de paramètres de publication** :

		azure account import <path to your .publishsettings file>

	>[AZURE.IMPORTANT]Après avoir importé vos paramètres de publication, vous devez supprimer le fichier `.publishsettings`. Il n’est plus nécessaire à l’interface de ligne de commande Azure, et représente un risque pour la sécurité dans la mesure où il pourrait être utilisé pour accéder à votre abonnement.

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

Une fois connecté à votre abonnement Azure, vous pouvez commencer à utiliser les commandes de l’interface de ligne de commande Azure pour travailler avec les ressources Azure.

## Modes des commandes de l’interface de ligne de commande

L’interface de ligne de commande Azure propose deux modes de commande pour l’utilisation des ressources Azure, avec divers jeux de commandes :

* **Mode Azure Resource Manager** : pour utiliser les ressources Azure dans le modèle de déploiement de Resource Manager. Pour définir ce mode, exécutez `azure config mode arm`.

* **Mode Azure Service Management** : pour utiliser les ressources Azure dans le modèle de déploiement classique. Pour définir ce mode, exécutez `azure config mode asm`.

Lors de la première installation, l’interface de ligne de commande est en mode Service Management.

>[AZURE.NOTE]Le mode Azure Resource Manager et le mode Azure Service Management s'excluent mutuellement. En d'autres termes, les ressources créées dans un mode ne peuvent pas être gérées dans l'autre mode.

## Stockage des paramètres de l'interface de ligne de commande

Que vous vous connectiez avec la commande `azure login` ou que vous importiez des paramètres de publication, votre profil d’interface de ligne de commande et vos journaux sont stockés dans un répertoire `.azure` situé dans votre répertoire `user`. Le répertoire `user` est protégé par le système d’exploitation ; toutefois, il est recommandé de prendre des mesures supplémentaires pour chiffrer le répertoire `user`. Pour ce faire, procédez comme suit :

* Sur Windows, modifiez les propriétés d'annuaire ou utilisez BitLocker.
* Sur Mac, activez FileVault pour l'annuaire.
* Sous Ubuntu, utilisez la fonctionnalité d’annuaire Encrypted Home. Les autres versions de Linux offrent des fonctionnalités similaires.

## Déconnexion

Pour vous déconnecter, utilisez la commande suivante :

	azure logout -u <username>

Si les abonnements associés au compte étaient authentifiés uniquement avec Active Directory, la déconnexion supprime les informations d'abonnement du profil local. Toutefois, si un fichier de paramètres de publication avait également été importé pour les abonnements, la déconnexion supprime uniquement les informations Active Directory associées du profil local.
## Étapes suivantes

* Pour utiliser les commandes de l’interface de ligne de commande Azure, consultez la rubrique [Commandes de l’interface de ligne de commande Azure en mode Azure Resource Manager](./virtual-machines/azure-cli-arm-commands.md) et [Commandes de l’interface de ligne de commande Azure en mode Azure Service Management ](virtual-machines-command-line-tools.md).

* Pour plus d'informations sur l'interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir [Référentiel GitHub pour l'interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous rencontrez des problèmes lors de l’utilisation d’Azure ou de l’interface de ligne de commande Azure, consultez les [forums Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

<!---HONumber=AcomDC_0427_2016-->