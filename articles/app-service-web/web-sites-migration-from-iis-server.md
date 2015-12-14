<properties 
	pageTitle="Effectuer la migration d’une application web d’entreprise vers Azure App Service" 
	description="Indique comment utiliser l’Assistant Migration Web Apps pour effectuer rapidement la migration de sites web IIS existants vers Azure App Service Web Apps" 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Effectuer la migration d’une application web d’entreprise vers Azure App Service

Vous pouvez facilement effectuer la migration de vos sites web qui s’exécutent sur Internet Information Services (IIS) version 6 ou ultérieure vers [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

>[AZURE.IMPORTANT]La fin du support de Windows Server 2003 est programmée pour le 14 juillet 2015. Si vous hébergez actuellement vos sites web sur un serveur IIS exécutant Windows Server 2003, la fonctionnalité Web Apps constitue une solution sûre, économique et stable pour conserver vos sites web en ligne. Quant à l’Assistant Migration d’applications web, il facilite l’automatisation de votre processus de migration.

L’[Assistant Migration Web Apps](https://www.movemetothecloud.net/) peut analyser votre installation de serveur IIS, identifier les sites pouvant faire l’objet d’une migration vers App Service, mettre en surbrillance les éléments dont la migration est impossible ou qui ne sont pas pris en charge sur la plateforme, puis effectuer la migration de vos sites web et des bases de données associées vers Azure.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## Éléments vérifiés au cours de l’analyse de compatibilité ##
L’Assistant Migration crée un rapport d’analyse visant à déceler tout problème ou obstacle potentiel à la réussite de la migration d’un serveur IIS local vers Azure App Service Web Apps. Certains des éléments clés à connaître sont les suivants :

-	Liaisons de port : Web Apps prend uniquement en charge le port 80 pour le trafic HTTP et le port 443 pour le trafic HTTPS. Toute autre configuration de port est ignorée, et le trafic est alors redirigé vers les ports 80 ou 443. 
-	Authentification : Web Apps prend en charge l’authentification anonyme par défaut et l’authentification par formulaire lorsqu’une application le spécifie. L'authentification Windows peut être utilisée uniquement en cas d'intégration à Azure Active Directory et ADFS. Aucune des autres formes d’authentification, telles que l’authentification de base, n’est prise en charge pour l’instant. 
-	Global Assembly Cache (GAC) : GAC n’est pas pris en charge dans Web Apps. Si votre application fait référence à des assemblys que vous déployez en règle générale vers GAC, vous devrez déployer le dossier Bin de l’application dans Web Apps. 
-	Mode de compatibilité IIS5 : non pris en charge dans Web Apps. 
-	Pools d’applications : dans Web Apps, chaque site et ses applications enfants s’exécutent dans le même pool d’applications. Si votre site comporte plusieurs applications enfants utilisant différents pools d’applications, regroupez-les dans un seul pool d’applications en optant pour les paramètres courants ou effectuez la migration de chaque application vers une application web distincte.
-	Composants COM : Web Apps n’autorise pas l’inscription de composants COM sur la plateforme. Si vos sites web ou applications utilisent des composants COM, vous devez les réécrire en code managé et les déployer avec le site web ou l’application.
-	Filtres ISAPI : Web Apps peut prendre en charge l’utilisation des filtres ISAPI. Effectuez les opérations suivantes :
	-	Déployez les DLL avec votre application web. 
	-	Enregistrez les DLL à l’aide de [Web.config](http://www.iis.net/configreference/system.webserver/isapifilters).
	-	Placez un fichier applicationHost.xdt à la racine du site en utilisant le code ci-dessous :

			<?xml version="1.0"?>
			<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
			<configSections>
			    <sectionGroup name="system.webServer">
			      <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
			    </sectionGroup>
			  </configSections>
			</configuration>

		Pour plus d’exemples de l’utilisation de XDT (XML Document Transformation) sur votre site web, consultez la page [Transformer votre site web Microsoft Azure (en anglais)](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-	La migration ne concerne pas les autres composants comme SharePoint, les extensions serveur FrontPage (FPSE), les serveurs FTP et les certificats SSL.

## Utilisation de l’Assistant Migration Web Apps ##
Cette section présente pas à pas un exemple de migration de sites web qui utilisent une base de données SQL Server et s'exécutent sur un serveur local sous Windows Server 2003 R2 (IIS 6.0) :

1.	Sur le serveur IIS ou votre ordinateur client, accédez à [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/). 

	![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.	Installez l’Assistant Migration Web Apps en cliquant sur le bouton **Serveur IIS dédié**. D'autres options seront ajoutées dans un proche avenir.
4.	Cliquez sur le bouton **Installer l’outil** pour installer l’Assistant Migration Web Apps sur votre machine.

	![](./media/web-sites-migration-from-iis-server/install-page.png)

	>[AZURE.NOTE]Vous pouvez également cliquer sur **Télécharger pour une installation hors ligne** afin de télécharger un fichier ZIP pour une installation sur des serveurs non connectés à Internet. L’autre possibilité consiste à cliquer sur **Charger un rapport d’analyse de comptabilité pour la migration**, une option avancée permettant d’utiliser un rapport d’analyse de compatibilité de la migration précédemment généré (voir l’explication ci-après).

5.	Dans l’écran **Installation de l’application**, cliquez sur **Installer** pour effectuer l’installation sur votre ordinateur. Cette opération installe également les dépendances associées telles que Web Deploy, DacFX et IIS, le cas échéant.

	![](./media/web-sites-migration-from-iis-server/install-progress.png)

	Une fois installé, l’Assistant Migration Web Apps démarre automatiquement.
  
6.	Sélectionnez **Migrer des sites et des bases de données depuis un serveur distant vers Azure**. Entrez les informations d’identification d’administration du serveur distant, puis cliquez sur **Continuer**.

	![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

	Bien entendu, vous pouvez choisir de procéder à la migration depuis le serveur local. L’option de migration distante est utile lorsque vous souhaitez effectuer la migration de sites web depuis un serveur de production IIS.
 
	L’outil de migration analyse ensuite la configuration de votre serveur ISS, notamment les sites, les applications, les pools d’applications et les dépendances afin d’identifier les sites web candidats à la migration.

8.	La capture d’écran ci-dessous présente trois sites web : **Site web par défaut**, **TimeTracker** et **CommerceNet4**. Tous ces sites sont associés à une base de données à migrer. Sélectionnez tous les sites que vous souhaitez évaluer, puis cliquez sur **Suivant**.

	![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.	Cliquez sur **Charger** pour charger le rapport d’analyse de compatibilité. Si vous cliquez sur **enregistrer le fichier localement**, vous pouvez relancer l’outil de migration ultérieurement et charger le rapport d’analyse de compatibilité enregistré, comme indiqué précédemment.

	![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
	Une fois le rapport d’analyse de compatibilité téléchargé, Azure procède à l’analyse de compatibilité et affiche les résultats. Consultez les détails de l'évaluation pour chaque site web et vérifiez que vous comprenez bien la nature des problèmes rencontrés ou que vous les avez résolus avant de poursuivre.
 
	![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12.	Cliquez sur **Démarrer la migration** pour démarrer la migration. Vous êtes redirigé vers Azure pour vous connecter à votre compte. Il est important que vous vous connectiez à un compte dont l'abonnement Azure est actif. Si vous n'avez pas de compte Azure, vous pouvez vous inscrire pour bénéficier d'une version d'évaluation gratuite.

13.	Sélectionnez le compte client, l’abonnement Azure et la région à utiliser pour vos applications web et bases de données Azure dont vous allez effectuer la migration, puis cliquez sur **Démarrer la migration**. Vous pouvez sélectionner les sites web qui doivent faire l’objet d’une migration ultérieure.

	![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14.	L'écran suivant vous permet d'effectuer des changements par rapport aux paramètres de migration par défaut, tels que :

	- utiliser une base de données SQL Azure existante ou créer une base de données SQL Azure et configurer ses informations d'identification ;
	- sélectionner les sites web à migrer ;
	- définir des noms pour les applications web Azure et les bases de données SQL qui y sont associées ;
	- personnaliser les paramètres globaux et les paramètres au niveau du site.

	La capture d'écran ci-dessous montre tous les sites web à migrer sélectionnés. Les paramètres définis sont les paramètres par défaut.

	![](./media/web-sites-migration-from-iis-server/migration-settings.png)

	>[AZURE.NOTE]La case à cocher **Activer Azure Active Directory** dans les paramètres personnalisés permet d’intégrer l’application web Azure dans [Azure Active Directory](active-directory-whatis.md) (**annuaire par défaut**). Pour plus d’informations sur la synchronisation d’Azure Active Directory avec votre annuaire Azure Directory local, consultez la page [Intégration d’annuaire](http://msdn.microsoft.com/library/jj573653).

16.	 Une fois que vous avez apporté toutes les modifications souhaitées, cliquez sur **Créer** pour lancer le processus de migration. L’outil de migration crée la Base de données SQL Azure et l’application web Azure, puis publie le contenu et les bases de données du site web. La progression de la migration est clairement indiquée dans l’outil de migration. À la fin du processus, un écran récapitulatif présente les détails des sites ayant fait l’objet d’une migration et, si leur migration a réussi, fournit des liens vers les applications web Azure qui viennent d’être créées.

	Si une erreur se produit pendant la migration, l’outil de migration indique clairement l’échec de la migration et restaure les changements. Vous pouvez également envoyer le rapport d’erreurs directement à l’équipe d’ingénierie en cliquant sur le bouton **Envoyer le rapport d’erreurs**, ainsi que la pile des appels de l’échec capturé et le corps du message de la génération.

	![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

	Si la migration aboutit, vous pouvez également cliquer sur le bouton **Envoyer des commentaires** pour communiquer directement vos commentaires.
 
20.	Cliquez sur les liens vers les applications web Azure et vérifiez que la migration s’est correctement déroulée.

21. Vous pouvez désormais gérer les applications web migrées dans Azure App Service. Pour ce faire, connectez-vous au [portail Azure](https://portal.azure.com).

22. Dans le portail Azure, ouvrez le panneau Applications web pour visualiser les sites web dont vous avez effectué la migration (représentés sous la forme d’applications web), puis cliquez sur l’un d’eux pour démarrer les tâches de gestion de l’application web, telles que la configuration de la publication continue, la création de sauvegardes, l’ajustement automatique et la surveillance de l’utilisation ou des performances.

	![](./media/web-sites-migration-from-iis-server/TimeTrackerMigrated.png)

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
 

<!---HONumber=AcomDC_1203_2015-->