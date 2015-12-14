<properties
    pageTitle="Installation du kit de ressources Azure pour Eclipse"
    description="Apprenez à installer le Kit de ressources Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/fr-FR/library/azure/hh690946.aspx -->

# Installation du kit de ressources Azure pour Eclipse #

Le kit de ressources Azure pour Eclipse contient des modèles et des fonctionnalités qui vous permettent de créer, de développer, de tester et de déployer des applications Azure avec l'environnement de développement Eclipse. Ce projet Open Source possède un code source disponible sous licence Apache 2.0 sur le site du projet sur GitHub à l'adresse suivante :

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

Les étapes suivantes vous montrent comment installer le kit de ressources Azure pour Eclipse.

## Composants requis ##

* IDE (environnement de développement intégré) Eclipse pour développeurs Java EE, Indigo ou ultérieur, Vous pouvez le télécharger à partir de <http://www.eclipse.org/downloads/>.
* Kit de développement Java (JDK) version 1.7 ou ultérieure. 
* Systèmes d'exploitation - le Kit de ressources Azure pour Eclipse a été testé sur les systèmes d'exploitation suivants :
    * Windows 10
    * Windows 8 et Windows 8.1
    * Windows 7
    * Windows Server 2012
    * Windows Server 2008

> [AZURE.IMPORTANT]Si vous utilisez le kit de ressources Azure pour Eclipse sous Windows, le kit de ressources requiert l'installation du Kit de développement logiciel (SDK) Azure 2.7 ou version ultérieure. Vous avez deux options pour l'installation du Kit de développement logiciel (SDK) Azure :
> 
> * Vous pouvez télécharger et installer le Kit de développement logiciel (SDK) Azure à partir de [Web Platform Installer (WebPI)][].
> * Si le Kit de développement logiciel (SDK) Azure n'est pas installé lorsque vous créez votre premier projet de déploiement Azure, vous serez invité à télécharger et à installer automatiquement la version appropriée du Kit de développement logiciel (SDK) Azure.
> 
> Notez que le Kit de développement logiciel (SDK) Azure est requis uniquement sous Windows.

## Pour installer le Kit de ressources Azure pour Eclipse ##

1. Démarrez Eclipse.
2. Dans le menu d'Eclipse, cliquez sur <strong>Aide</strong>, puis cliquez sur <strong>Install New Software</strong> (Installer nouveau logiciel), comme illustré dans le diagramme suivant. ![][ic590123]
3. Dans la boîte de dialogue <strong>Available Software</strong> (Logiciels disponibles), dans la zone de texte <strong>Work With</strong> (Fonctionnement avec), tapez <strong>http://dl.msopentech.com/eclipse</strong> suivis de la touche <strong>Entrée</strong>.
4. Dans le volet <strong>Name</strong> (Nom), cochez <strong>Azure Toolkit for Eclipse</strong> (Kit de ressources Azure pour Eclipse) et décochez <strong>Contact all update sites during install to find required software</strong> (Contacter tous les sites de mise à jour durant l'installation pour trouver le logiciel requis). Votre écran doit se présenter comme suit : ![][ic719482]
5. Si vous développez le <strong>Kit de ressources Azure pour Eclipse</strong>, vous verrez les éléments suivants :
    * **Azure Access Control Services Filter** : ce composant prend en charge l'authentification des utilisateurs d'application avec Azure ACS.
    * **Azure Common Plugin** : ce composant contient la fonctionnalité partagée dont dépendent les autres composants.
    * **Kit de ressources Azure pour Eclipse** : ce composant contient la logique de configuration de projet, l'Assistant de publication sur le cloud et l'interface utilisateur.
    * **Microsoft JDBC Driver 4.0 for SQL Server** : ce composant simplifie le développement d'application à l'aide de la base de données SQL.
    * **Package for Apache Qpid Client Libraries for JMS** : ce composant fournit la bibliothèque cliente JMS du projet Apache Qpid pour permettre à votre application d'utiliser la messagerie Advanced Messaging Queuing Protocol (AMQP) dans Azure
    * **Package for Azure Libraries for Java** : ce composant vous permet de créer des applications Azure dans Java qui vous permettent de tirer parti des ressources cloud computing évolutives d'Azure.
    * **Application Insights Plugin for Java** : ce composant vous permet d'utiliser les services de journalisation et d'analyse de télémétrie d'Azure pour vos applications et instances de serveur.
6. Cliquez sur **Next**. (Si vous rencontrez des délais d'attente inhabituels lors de l'installation du kit de ressources, assurez-vous que l'option **Contact all update sites during install to find required software** est désactivée.)
7. Dans la boîte de dialogue **Install Details** (Détails d'installation), cliquez sur **Next** (Suivant).
8. Dans la boîte de dialogue **Review Licenses** (Vérifier les licences), passez en revue les termes des contrats de licence. Si vous acceptez les termes des contrats de licence, cliquez sur **I accept the terms of the license agreements** (J'accepte les termes des contrats de licence), puis sur **Finish** (Terminer). (Les étapes restantes supposent que vous acceptez les termes des contrats de licence. Si vous n'acceptez pas les termes des contrats de licence, quittez le processus d'installation.)
9. Si vous êtes invité à redémarrer Eclipse pour terminer l'installation, cliquez sur **Restart Now** (Redémarrer maintenant).

## Voir aussi ##

[Kit de ressources Azure pour Eclipse][]

[Création d'une application Hello World pour Azure dans Eclipse][]

[Nouveautés du kit de ressources Azure pour Eclipse][]

Pour plus d'informations sur l'utilisation d'Azure avec Java, consultez le [Centre de développement Java][].

<!-- URL List -->

[Kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Centre de développement Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Création d'une application Hello World pour Azure dans Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Web Platform Installer (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838
[Nouveautés du kit de ressources Azure pour Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png

<!---HONumber=AcomDC_1203_2015-->