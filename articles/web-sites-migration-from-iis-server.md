<properties urlDisplayName="Migrate from IIS to Azure Websites with Migration Assistant" pageTitle="Migration de vos sites web IIS sur Sites Web Azure avec l'Assistant Migration" metaKeywords="Azure Websites,migration,migrate,IIS" description="Montre comment utiliser l'Assistant Migration de Sites Web Azure pour migrer rapidement les sites web IIS existants vers des sites web Azure" metaCanonical="" services="web-sites" documentationCenter="" title="Migrate your IIS Websites to Azure Websites using the Migration Assistant" authors="cephalin,anwestg"  solutions="" writer="cephalin" manager="wpickett" editor=""  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/20/2014" ms.author="cephalin" />

# Migration de vos sites web IIS vers Sites Web Azure à l'aide de l'Assistant Migration #
Vous pouvez migrer en toute simplicité sur Sites Web Azure vos sites web existants s'exécutant sur Internet Information Service (IIS) 6 ou une version ultérieure. [L'Assistant Migration Sites Web Azure](https://www.movemetothecloud.net/) permet d'analyser votre installation de serveur IIS, d'identifier les sites pouvant être migrés sur Sites Web Azure, de mettre en surbrillance les éléments impossible à migrer ou non pris en charge sur la plateforme, puis de migrer vos sites web et les bases de données associées sur Azure.

>[WACOM.NOTE]La prise en charge de Windows Server 2003 prendra fin le 14 juillet 2015. Si vous hébergez actuellement vos sites web sur un serveur IIS sous Windows Server 2003, Sites Web Azure constitue une solution sûre, économique et stable pour conserver vos sites web en ligne. Quant à l'Assistant Migration Sites Web Azure, il facilite l'automatisation de votre processus de migration. 

## Éléments vérifiés au cours de l'analyse de compatibilité ##
L'Assistant Migration Sites Web Azure crée un rapport d'analyse visant à déceler tout problème ou obstacle potentiel à la réussite de la migration d'un serveur IIS local sur Sites Web Azure. Attention aux éléments essentiels suivants :

-	Liaisons de port : Sites Web Azure prend uniquement en charge le port 80 pour le trafic HTTP et le port 443 pour le trafic HTTPS. Toute autre configuration de port est ignorée et le trafic est alors redirigé vers les ports 80 ou 443.
-	Authentification : le service Sites Web Azure prend en charge l'authentification anonyme par défaut et l'authentification par formulaire lorsqu'une application le spécifie. L'authentification Windows peut être utilisée uniquement en cas d'intégration à Azure Active Directory et ADFS. Toutes les autres formes d'authentification, p.ex. l'authentification de base, ne sont actuellement pas prises en charge. 
-	Global Assembly Cache (GAC) : GAC n'est pas pris en charge par le service Sites Web Azure. Si votre application fait référence à des assemblys que vous déployez en règle générale vers GAC, vous devrez déployer le dossier Bin de l'application vers Sites Web Azure. 
-	Mode de compatibilité IIS5 : non pris en charge par le service Sites Web Azure. 
-	Pool d'applications : dans Sites Web Azure, chaque site et ses applications enfant s'exécutent dans le même pool d'application. Si votre site comporte plusieurs applications enfants utilisant plusieurs pools d'applications, regroupez-les dans un seul pool d'applications en optant pour les paramètres classiques ou migrez chaque application vers un site web séparé.
-	Composants COM : Sites Web Azure n'autorise pas l'enregistrement de composants COM sur la plateforme. Si vos sites web ou applications utilisent des composants COM, vous devez les réécrire en code managé et les déployer avec le site web ou l'application.
-	Filtres ISAPI : Sites Web Azure prend en charge les filtres ISAPI. Effectuez les actions suivantes :
	-	Déployez les DLL vers votre site web. 
	-	Enregistrez les DLL à l'aide de [Web.config.](http://www.iis.net/configreference/system.webserver/isapifilters)
	-	Placez un fichier applicationHost.xdt à la racine du site en utilisant le code ci-dessous :

			?xml version="1.0"?
			<configuration xmlns:xdt="http://schemas.microsoft.com/XML-Document-Transform">
			<configSections>
			    <sectionGroup name="system.webServer">
			      <section name="isapiFilters" xdt:Transform="SetAttributes(overrideModeDefault)" overrideModeDefault="Allow" />
			    </sectionGroup>
			  </configSections>
			</configuration>

		Pour plus d'exemples de l'utilisation d'XDT (XML Document Transformation) sur votre site web, consultez la page [Transformation de votre site web Microsoft Azure](http://blogs.msdn.com/b/waws/archive/2014/06/17/transform-your-microsoft-azure-web-site.aspx).

-	La migration ne concerne pas les autres composants comme SharePoint, les extensions serveur FrontPage (FPSE), les serveurs FTP et les certificats SSL.

## Utilisation de l'Assistant Migration Sites Web Azure ##
Cette section présente pas à pas un exemple de migration de sites web qui utilisent une base de données SQL Server et s'exécutent sur un serveur local sous Windows Server 2003 R2 (IIS 6.0) :

1.	Sur le serveur IIS ou votre ordinateur client, accédez à [https://www.movemetothecloud.net/](https://www.movemetothecloud.net/) 

	![](./media/web-sites-migration-from-iis-server/migration-tool-homepage.png)

2.	Installez l'Assistant Migration Sites Web Azure en cliquant sur le bouton **Serveur IIS dédié**. D'autres options seront ajoutées dans un proche avenir. 
4.	Cliquez sur le bouton **Outil d'installation** pour installer l'Assistant Migration Sites Web Azure sur votre ordinateur.

	![](./media/web-sites-migration-from-iis-server/install-page.png)

	>[WACOM.NOTE] Vous pouvez également cliquer sur **Télécharger pour installation hors ligne** afin de télécharger un fichier ZIP pour une installation sur des serveurs non connectés à Internet. Vous avez par ailleurs le choix de cliquer sur **Télécharger un rapport d'analyse de compatibilité pour la migration**, une option avancée permettant d'utiliser un rapport d'analyse de compatibilité pour la migration existant précédemment généré (voir explication ci-après).

5.	Dans l'écran **Installation de l'application**, cliquez sur **Installer** pour effectuer l'installation sur votre ordinateur. Cette opération installe également les dépendances associées telles que Web Deploy, DacFX et IIS, le cas échéant. 

	![](./media/web-sites-migration-from-iis-server/install-progress.png)

	Une fois installé, l'Assistant Migration Sites Web Azure démarre automatiquement.
  
6.	Sélectionnez **Migrer des sites et des bases de données depuis un serveur distant vers Azure**. Entrez les informations d'identification d'administration du serveur distant, puis cliquez sur **Continuer**. 

	![](./media/web-sites-migration-from-iis-server/migrate-from-remote.png)

	Bien entendu, vous pouvez choisir de procéder à la migration depuis le serveur local. L'option de migration distante est utile lorsque vous souhaitez migrer des sites web depuis un serveur de production IIS.
 
	L'outil de migration analyse ensuite la configuration de votre serveur ISS, notamment les sites, les applications, les pools d'applications et les dépendances afin d'identifier les sites web candidats à la migration. 

8.	La capture d'écran ci-dessous présente trois sites web : **Site web par défaut**, **TimeTracker** et **CommerceNet4**. Tous ces sites sont associés à une base de données à migrer. Sélectionnez tous les sites que vous souhaitez évaluer, puis cliquez sur **Suivant**.

	![](./media/web-sites-migration-from-iis-server/select-migration-candidates.png)
 
9.	Cliquez sur **Télécharger** pour télécharger le rapport d'analyse de compatibilité. Si vous cliquez sur **enregistrer le fichier localement**, vous pouvez exécuter l'outil de migration une nouvelle fois ultérieurement et télécharger le rapport d'analyse de compatibilité enregistré comme indiqué ci-dessus.

	![](./media/web-sites-migration-from-iis-server/upload-readiness-report.png)
 
	Une fois le rapport d'analyse de compatibilité téléchargé, Azure procède à l'analyse de compatibilité et affiche les résultats. Consultez les détails de l'évaluation pour chaque site web et vérifiez que vous comprenez bien la nature des problèmes rencontrés ou que vous les avez résolus avant de poursuivre. 
 
	![](./media/web-sites-migration-from-iis-server/readiness-assessment.png)

12.	Cliquez sur **Démarrer la migration** pour démarrer la migration. Vous êtes alors redirigé vers Azure pour vous connecter à votre compte. Il est important que vous vous connectiez à un compte dont l'abonnement Azure est actif. Si vous n'avez pas de compte Azure, vous pouvez vous inscrire pour bénéficier d'une version d'évaluation gratuite. 

13.	Sélectionnez le compte client, l'abonnement Azure et la région à utiliser pour vos sites web et bases de données Azure migrés, puis cliquez sur **Démarrer la migration**. Vous pouvez sélectionner les sites web à migrer ultérieurement.

	![](./media/web-sites-migration-from-iis-server/choose-tenant-account.png)

14.	L'écran suivant vous permet d'effectuer des changements par rapport aux paramètres de migration par défaut, tels que :

	- utiliser une base de données SQL Azure existante ou créer une base de données SQL Azure et configurer ses informations d'identification ;
	- sélectionner les sites web à migrer ;
	- définir des noms pour les sites web Azure et les bases de données SQL qui y sont associées ;
	- personnaliser les paramètres globaux et les paramètres au niveau du site.

	La capture d'écran ci-dessous montre tous les sites web à migrer sélectionnés. Les paramètres définis sont les paramètres par défaut.

	![](./media/web-sites-migration-from-iis-server/migration-settings.png)

	>[WACOM.NOTE] La case à cocher **Activer Azure Active Directory** dans les paramètres personnalisés permet d'intégrer le site web Azure à [Azure Active Directory](http://azure.microsoft.com/fr-fr/documentation/articles/active-directory-whatis/) (**annuaire par défaut**). Pour plus d'informations sur la synchronisation d'Azure Active Directory avec votre annuaire Azure Directory local, consultez la page [Intégration d'annuaire](http://msdn.microsoft.com/library/jj573653).

16.	 Une fois que vous avez apporté toutes les modifications souhaitées, cliquez sur **Créer** pour démarrer le processus de migration. L'outil de migration crée la base de données SQL Azure et le site web Azure, puis publie le contenu et les bases de données du site web. La progression de la migration est clairement indiquée dans l'outil de migration. À la fin du processus, vous pouvez également consulter un écran de résumé qui présente les détails des sites migrés et, si leur migration a réussi, donne des liens vers les sites web Azure qui viennent d'être créés. 

	Si une erreur se produit pendant la migration, l'outil de migration indique clairement l'échec de la migration et restaure les changements. Vous avez également la possibilité d'envoyer le rapport d'erreurs directement à l'équipe d'ingénierie en cliquant sur le bouton **Envoyer le rapport d'erreurs**, ainsi que la pile des appels de l'échec capturé et le corps du message de la génération. 

	![](./media/web-sites-migration-from-iis-server/migration-error-report.png)

	Si la migration réussit sans erreur, vous pouvez également cliquer sur le bouton **Envoyer des commentaires** pour communiquer directement vos commentaires. 
 
20.	Cliquez sur les liens vers les sites web Azure et vérifiez que la migration s'est déroulée correctement.


