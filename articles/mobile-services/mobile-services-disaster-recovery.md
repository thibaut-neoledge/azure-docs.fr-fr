<properties 
	pageTitle="Récupération de votre service mobile en cas d'incident - Azure Mobile Services" 
	description="Découvrez comment récupérer votre service mobile en cas d'urgence." 
	services="mobile-services" 
	documentationCenter="" 
	authors="christopheranderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="christopheranderson"/>

# Récupération de votre service mobile en cas d'incident

Lorsque vous déployez une application à l'aide d'Azure Mobile Services, vous pouvez utiliser ses fonctionnalités intégrées pour assurer la continuité des activités de l'entreprise en cas de problèmes de disponibilité liés à des défaillances serveur, des perturbations réseau, une perte de données et une perte étendue de fonctionnalités. En déployant votre application à l'aide d'Azure Mobile Services, vous profitez des nombreuses fonctionnalités de tolérance de panne et d'infrastructure que vous auriez à concevoir, à implémenter et à gérer si vous deviez déployer une solution locale classique. Azure vous permet de limiter une grande partie des risques de défaillance à moindre frais.

## <a name="prepare"></a> Préparation aux catastrophes

Pour faciliter une récupération en cas de problème de disponibilité, vous pouvez vous y préparer à l'avance :

+ **Sauvegardez vos données dans la base de données SQL Azure Mobile Services** Les données de votre application de service mobile sont stockées dans une base de données SQL Azure. Nous vous recommandons de les sauvegarder en suivant les prescriptions fournies par la page [Continuité des activités de l'entreprise dans la base de données SQL].
+ **Sauvegardez les scripts de votre service mobile** Nous vous recommandons de stocker les scripts de votre service mobile dans un système de contrôle de code source, tel que [Team Foundation Service] ou [GitHub], et de ne pas compter uniquement sur les copies contenues dans le service mobile lui-même. Vous pouvez télécharger les scripts via le portail Azure à l'aide de la [fonctionnalité de contrôle de code source] de Mobile Service ou de l'[outil en ligne de commande Azure]. Portez une attention particulière aux fonctionnalités dites « préliminaires » (ou « preview »), car il n'est pas garanti que ces scripts puissent être récupérés et vous serez peut-être amené à les récupérer à partir de l'original de votre propre contrôle de code source.
+ **Réservez un service mobile secondaire** En cas de problème de disponibilité de votre service mobile, vous risquez de devoir le redéployer dans une autre région Azure. Pour assurer la disponibilité de la capacité (par exemple, dans des cas exceptionnels comme la perte d'une région entière), nous vous recommandons de créer un service mobile secondaire dans votre autre région et de lui attribuer un mode équivalent ou supérieur à celui de votre service principal (si votre service principal est en mode partagé, vous pouvez attribuer au service secondaire le mode partagé ou réservé. Toutefois, si le service principal est réservé, le service secondaire doit l'être également).


## <a name="watch"></a>Vigilance face aux signes révélateurs d'un problème

Les cas de figure suivants indiquent l'existence d'un problème susceptible d'exiger une opération de récupération :

+ Les applications connectées à votre service mobile ne peuvent pas communiquer avec celui-ci pendant une période prolongée.
+ Le service mobile indique l'état **Unhealthy** (défectueux) dans le [portail Azure].
+ Une bannière **Unhealthy** (défectueux) apparaît en haut de chaque onglet de votre service mobile dans le portail Azure, et les opérations de gestion génèrent des messages d'erreur.
+ Le [tableau de bord du service Azure] indique un problème de disponibilité.

## <a name="recover"></a>Récupération d'urgence

Lorsqu'un problème se produit, consultez le tableau de bord du service pour obtenir des instructions et des informations à jour.
 
Si le tableau de bord du service vous y invite, exécutez les étapes suivantes pour rétablir l'état d'exécution de votre service mobile dans une autre région Azure. Si vous avez créé un service secondaire par anticipation, sa capacité sert à restaurer le service principal. Sachant que l'URL et la clé d'application du service principal sont inchangées à l'issue de la récupération, vous n'avez pas besoin de mettre à jour les applications qui y font référence.

Pour récupérer votre service mobile après une panne :

1. Dans le portail Azure, assurez-vous que l'état de votre service indique **Unhealthy** (défectueux).

2. Si vous avez déjà réservé un service mobile secondaire, vous pouvez ignorer cette étape.

   Si vous n'avez pas déjà réservé de service mobile secondaire, créez-en un maintenant dans une autre région Azure. Attribuez-lui un mode équivalent ou supérieur à celui de votre service principal (si votre service principal est en mode partagé, vous pouvez attribuer au service secondaire le mode partagé ou réservé. Toutefois, si le service principal est réservé, le service secondaire doit l'être également).

3. Configurez les outils en ligne de commande Azure afin de pouvoir les utiliser avec votre abonnement, comme décrit dans la rubrique [Automatisation des services mobiles avec les outils en ligne de commande].

4. Vous pouvez à présent utiliser votre service secondaire pour récupérer votre service principal.

    > [AZURE.IMPORTANT]L'exécution de la commande dans cette étape a pour effet de supprimer le service secondaire pour mettre sa capacité au service de la récupération du service principal. Si vous tenez à conserver vos scripts et paramètres, nous vous recommandons de les sauvegarder avant d'exécuter la commande.
    
   Lorsque vous êtes prêt, exécutez la commande suivante :

		azure mobile recover PrimaryService SecondaryService
		info:    Executing command mobile recover
		Warning: this action will use the capacity from the mobile service 'SecondaryService' and delete it. Do you want to recover the mobile service 'PrimaryService'? (y/n): y
		+ Performing recovery
		+ Cleaning up
		info:    Recovery complete
		info:    mobile recover command OK


	> [AZURE.NOTE] Plusieurs minutes peuvent être nécessaires avant que l'exécution de la commande aboutisse et que les modifications soient visibles dans le portail.

5. Vérifiez que tous les scripts ont été correctement récupérés en les comparant à vos originaux dans le contrôle de code source. Dans la plupart des cas, les scripts sont récupérés automatiquement sans perte de données. Si toutefois vous notez des différences dans un script déterminé, vous pouvez le récupérer manuellement.

6. Vérifiez que le service récupéré communique avec votre base de données SQL Azure. La commande de récupération récupère le service mobile, mais elle conserve la connexion à la base de données d'origine. Si le problème qui touche la région Azure principale affecte aussi la base de données, le service récupéré risque de ne pas fonctionner correctement. Vous pouvez utiliser le tableau de bord du service Azure pour examiner l'état de la base de données pour une région donnée. Si la base de données d'origine ne fonctionne pas, vous pouvez la récupérer :
	+ Récupérez votre base de données SQL Azure pour la région Azure dans laquelle vous venez de récupérer votre service mobile, comme décrit à la page [Continuité des activités de l'entreprise dans la base de données SQL].
	+ Dans le portail Azure, sous l'onglet **Configurer** de votre service mobile, choisissez « Change database » et sélectionnez la base de données nouvellement récupérée.

Votre service mobile a maintenant été récupéré pour une nouvelle région Azure et il accepte désormais le trafic en provenance de vos applications Windows Store en utilisant son URL d'origine.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Continuité des activités de l'entreprise dans la base de données SQL]: http://msdn.microsoft.com/library/windowsazure/hh852669.aspx
[Team Foundation Service]: http://tfs.visualstudio.com/

[fonctionnalité de contrôle de code source]: http://www.windowsazure.com/develop/mobile/tutorials/store-scripts-in-source-control/
[outil en ligne de commande Azure]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/
[portail Azure]: http://manage.windowsazure.com/
[tableau de bord du service Azure]: http://www.windowsazure.com/support/service-dashboard/
[Automatisation des services mobiles avec les outils en ligne de commande]: http://www.windowsazure.com/develop/mobile/tutorials/command-line-administration/

<!--HONumber=54--> 