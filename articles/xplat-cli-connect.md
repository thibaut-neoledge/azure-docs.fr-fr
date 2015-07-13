<properties
	pageTitle="Connexion à partir de l’interface de ligne de commande Azure | Microsoft Azure"
	description="Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)"
	editor="tysonn"
	manager="timlt"
	documentationCenter=""
	authors="dlepow"
	services=""/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/09/2015"
	ms.author="danlep"/>

# Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)

L’interface Azure CLI offre un ensemble de commandes multiplateforme open source pour l’utilisation de la Plateforme Azure. Ce document vous explique comment vous connecter à votre abonnement Azure à partir de l’interface de ligne de commande Azure. Pour connaître la procédure d’installation, voir l’article [Installer l’interface de ligne de commande Azure](xplat-cli-install.md).

<a id="configure"></a>
## Connexion à votre abonnement Azure

La plupart des commandes fournies par l’interface Azure CLI nécessitent une connexion à un compte Azure. Pour configurer Azure CLI de manière à utiliser votre abonnement, vous pouvez au choix :

* Vous connecter à Azure à l’aide d’un compte professionnel ou scolaire. Cette méthode utilise Azure Active Directory pour authentifier les informations d’identification.

ou

* Télécharger et utiliser un fichier de paramètres de publication. Cette méthode installe un certificat qui vous permet d’exécuter les tâches de gestion pendant toute la durée de validité de l’abonnement et du certificat.

Pour plus d'informations sur la gestion de l'authentification et de l'abonnement, consultez la rubrique [« Quelle est la différence entre l'authentification basée sur les comptes et l'authentification basée sur les certificats ? »][authandsub].

Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure][free-trial].

> [AZURE.NOTE]Les services Azure les plus flexibles et avancés utilisent Azure Resource Manager ou le [mode ARM](xplat-cli-azure-resource-manager.md). Le mode ARM nécessite une connexion à Azure à l’aide d’un compte professionnel ou scolaire au moyen de la méthode de connexion décrite ci-dessous.

### Utilisation de la méthode par connexion

La méthode de connexion fonctionne uniquement avec un compte professionnel ou scolaire. Ce compte est géré par votre organisation et est défini dans l’instance Azure Active Directory de l’organisation.

> [AZURE.NOTE]Si vous ne possédez pas de compte professionnel ou scolaire et que vous utilisez un compte personnel pour vous connecter à votre abonnement Azure, vous pouvez en créer un facilement en procédant comme suit.
>
> 1. Connectez-vous au [portail Azure][portal], puis sélectionnez **Active Directory**.
>
> 2. S'il n'existe aucun annuaire, sélectionnez **Create your directory** et fournissez les informations demandées.
>
> 3. Sélectionnez votre annuaire et ajoutez un nouvel utilisateur. Ce nouvel utilisateur est un compte professionnel ou scolaire.
>
>     Pendant la création de l’utilisateur, une adresse de messagerie est fournie pour l’utilisateur, ainsi qu’un mot de passe temporaire. Conservez ces informations, car vous en aurez besoin par la suite.
>
> 4. Dans le portail Azure, sélectionnez **Paramètres**, puis **Administrateurs**. Sélectionnez **Ajouter** et ajoutez le nouvel utilisateur en tant que co-administrateur. Cela permet au compte professionnel ou scolaire de gérer votre abonnement Azure.
>
> 5. Pour finir, déconnectez-vous du portail Azure, puis reconnectez-vous en utilisant le nouveau compte professionnel ou scolaire. Si vous vous connectez pour la première fois avec ce compte, vous êtes invité à changer le mot de passe.
>
> 6. Assurez-vous que vos abonnements sont visibles lorsque vous vous connectez avec le compte professionnel ou scolaire.
>
>Pour plus d’informations sur les comptes professionnels ou scolaires, voir l’article [Inscription à Azure en tant qu’organisation][signuporg].

Pour vous connecter à partir d’Azure CLI à l’aide d’un compte professionnel ou scolaire, utilisez la commande suivante :

	azure login -u <username>

puis entrez votre mot de passe lorsque vous y êtes invité.

> [AZURE.NOTE]Si vous vous connectez pour la première fois avec ces informations d'identification, vous recevez un message vous invitant à vérifier que vous souhaitez bien mettre en cache un jeton d'authentification. Ce message s’affiche également si vous avez préalablement utilisé la commande `azure logout` décrite plus loin. Si vous voulez ignorer cette invite pour les scénarios d’automatisation, utilisez le paramètre `-q` avec la commande `azure login`.

Pour vous déconnecter, utilisez la commande suivante :

	azure logout -u <username>

> [AZURE.NOTE]Si les abonnements associés au compte étaient authentifiés uniquement avec Active Directory, la déconnexion supprime les informations d'abonnement du profil local. Toutefois, si un fichier de paramètres de publication avait également été importé pour les abonnements, la déconnexion supprime uniquement les informations Active Directory associées du profil local.

### Utilisation de la méthode basée sur un fichier de paramètres de publication

> [AZURE.NOTE]Si vous vous connectez à l’aide de cette méthode, vous pouvez uniquement utiliser les commandes de gestion des services Azure (ou le mode ASM).

Pour télécharger les paramètres de publication de votre compte, utilisez la commande suivante :

	azure account download

Cette commande ouvre votre navigateur par défaut et vous invite à vous connecter au [portail Azure][portal]. Une fois que vous êtes connecté, un fichier `.publishsettings` est téléchargé. Prenez note de son emplacement.

> [AZURE.NOTE]Si votre compte est associé à plusieurs clients Azure Active Directory, vous pouvez être invité à sélectionner l'annuaire Active Directory pour lequel vous voulez télécharger un fichier de paramètres de publication.
>
> Une fois l’annuaire Active Directory sélectionné à partir de la page de téléchargement ou par le biais du portail Azure, il est utilisé par défaut par le portail et par la page de téléchargement. Une fois qu'un paramètre par défaut a été défini, le texte « cliquez ici pour revenir à la page de sélection » apparaît en haut de la page de téléchargement. Utilisez le lien affiché pour revenir à la page de sélection.

Importez ensuite le fichier `.publishsettings` en exécutant la commande suivante :

	azure account import <path to your .publishsettings file>

Après l’importation de vos paramètres de publication, vous devez supprimer le fichier `.publishsettings`, car il n’est plus nécessaire à l’interface de ligne de commande Azure, et représente un risque pour la sécurité dans la mesure où il peut être utilisé pour accéder à votre abonnement.

> [AZURE.NOTE]Que vous vous connectiez avec un compte professionnel ou scolaire ou que vous importiez des paramètres de publication, les informations d’accès à votre abonnement Azure sont stockées dans un annuaire `.azure` situé dans votre répertoire `user`. Le répertoire `user` est protégé par le système d’exploitation ; toutefois, il est recommandé de prendre des mesures supplémentaires pour chiffrer le répertoire `user`. Pour ce faire, procédez comme suit :
>
> * Sur Windows, modifiez les propriétés d'annuaire ou utilisez BitLocker.
> * Sur Mac, activez FileVault pour l'annuaire.
> * Sous Ubuntu, utilisez la fonctionnalité d’annuaire Encrypted Home. Les autres versions de Linux fournissent des fonctionnalités équivalentes.

### Abonnements multiples

Si vous possédez plusieurs abonnements Azure, la connexion à Azure donne accès à tous les abonnements associés à vos informations d’identification. Un abonnement spécifique est sélectionné comme abonnement par défaut et est utilisé par l’interface de ligne de commande Azure durant l’exécution des opérations. Vous pouvez afficher les abonnements et identifier l’abonnement par défaut à l’aide de la commande `azure account list`. Cette commande renvoie des informations similaires aux suivantes :

	info:    Executing command account list
	data:    Name              Id                                    Current
	data:    ----------------  ------------------------------------  -------
	data:    Azure-sub-1       ####################################  true
	data:    Azure-sub-2       ####################################  false

Dans la liste ci-dessus, la colonne **Current** indique que l’abonnement par défaut actuel est Azure-sub-1. Pour modifier l’abonnement par défaut, utilisez la commande `azure account set` et spécifiez l’abonnement à utiliser par défaut. Par exemple :

	azure account set Azure-sub-2

Cette commande sélectionne Azure-sub-2 comme abonnement par défaut.

> [AZURE.NOTE]Le changement de l’abonnement par défaut prend effet immédiatement et au niveau global. Les nouvelles commandes Azure CLI, qu’elles soient exécutées à partir de la même instance de ligne de commande ou d’une autre instance, utiliseront le nouvel abonnement par défaut.

Si vous souhaitez utiliser avec Azure CLI un abonnement autre que l’abonnement par défaut, mais sans changer ce dernier, vous pouvez utiliser l’option `--subscription` et fournir le nom de l’abonnement à utiliser pour l’opération.

Une fois connecté à votre abonnement Azure, vous pouvez commencer à utiliser les commandes Azure CLI. Pour plus d’informations, voir l’article [Utilisation de l’interface de ligne de commande Azure](xplat-cli.md).

<a id="additional-resources"></a>
## Ressources supplémentaires

* [Utilisation de l’interface de ligne de commande Azure avec les commandes de gestion des services (ou le mode ASM)][cliasm]

* [Utilisation de l’interface de ligne de commande Azure avec les commandes de gestion des ressources (ou le mode ARM)][cliarm]

* Pour en savoir plus sur l’interface de ligne de commande Azure, télécharger un code source, signaler des problèmes ou contribuer au projet, voir la page [Référentiel GitHub pour l’interface de ligne de commande Azure](https://github.com/azure/azure-xplat-cli) (en anglais).

* Si vous rencontrez des problèmes lors de l’utilisation d’Azure ou de l’interface de ligne de commande Azure, consultez les [forums Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home).

* Pour plus d’informations sur Azure, consultez le site [http://azure.microsoft.com/](http://azure.microsoft.com).





[authandsub]: http://msdn.microsoft.com/library/windowsazure/hh531793.aspx#BKMK_AccountVCert
[free-trial]: http://azure.microsoft.com/pricing/free-trial/
[portal]: https://manage.windowsazure.com
[signuporg]: http://azure.microsoft.com/documentation/articles/sign-up-organization/
[cliasm]: virtual-machines/virtual-machines-command-line-tools.md
[cliarm]: virtual-machines/xplat-cli-azure-resource-manager.md

<!---HONumber=July15_HO1-->