<properties 
	pageTitle="Activation de votre annuaire pour la gestion hybride avec Azure AD Connect." 
	description="Cette page décrit Azure AD Connect et vous explique pourquoi l’utiliser." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="billmath"/>

# Activation de votre annuaire pour la gestion hybride avec Azure AD Connect

<div class="dev-center-tutorial-selector sublanding">
<a href="../active-directory-aadconnect/" title="Présentation" class="current">Présentation</a> <a href="../active-directory-aadconnect-how-it-works/" title="Fonctionnement">Fonctionnement</a> <a href="../active-directory-aadconnect-get-started/" title="Prise en main">Prise en main</a> <a href="../active-directory-aadconnect-whats-next/" title="Étapes suivantes">Étapes suivantes</a> <a href="../active-directory-aadconnect-learn-more/" title="En savoir plus">En savoir plus</a>
</div>


Aujourd'hui, les utilisateurs veulent pouvoir accéder à des applications à la fois en local et dans le cloud. Ils veulent pouvoir le faire à partir de n’importe quel appareil, qu’il s’agisse d’un ordinateur portable, d’un smartphone ou d’une tablette. C’est pourquoi vous et votre organisation devez être en mesure de leur fournir une méthode pour accéder à ces applications. Cependant, le basculement intégral vers le cloud n’est pas toujours une option possible.

<center>![What is Azure AD Connect](./media/active-directory-aadconnect/arch.png)</center>

Avec l’introduction d’Azure Active Directory Connect, l’accès à ces applications et le basculement vers le cloud sont devenus plus faciles. Azure AD Connect offre les avantages suivants :

- Vos utilisateurs peuvent se connecter avec une identité commune à la fois dans le cloud et en local. Ils n’ont pas besoin de mémoriser plusieurs mots de passe ou comptes et les administrateurs n’ont plus à se soucier de la surcharge supplémentaire due à une multiplication des comptes.
- Un outil unique et une utilisation encadrée facilitent la connexion à vos annuaires locaux avec Azure Active Directory. Une fois installé, l’Assistant déploie et configure tous les composants requis pour le démarrage et le bon fonctionnement de votre intégration d’annuaire, notamment les services de synchronisation, la synchronisation des mots de passe ou AD FS, ainsi que les prérequis tels que le module Azure AD PowerShell.



<center>![What is Azure AD Connect](./media/active-directory-aadconnect/azuread.png)</center>




## Pourquoi utiliser Azure AD Connect 

L’intégration de vos annuaires locaux avec Azure AD améliore la productivité de vos utilisateurs en leur fournissant une identité commune pour accéder aux ressources cloud et locales. Cette intégration procure aux utilisateurs et aux organisations les avantages suivants :
	
* Les organisations peuvent fournir aux utilisateurs une identité hybride commune entre différents services locaux ou cloud exploitant Windows Server Active Directory et la connexion à Azure Active Directory. 
* Les administrateurs peuvent fournir un accès conditionnel basé sur des ressources d’application, des identités d’appareil et d’utilisateur, un emplacement réseau et une authentification multifacteur.
* Les utilisateurs peuvent exploiter leur identité commune via des comptes dans d’Azure AD vers Office 365, Intune, des applications SaaS et des applications tierces.  
* Les développeurs peuvent créer des applications qui exploitent le modèle d’identité commune en intégrant des applications dans une version locale d’Active Directory ou des applications cloud Azure.

Azure AD Connect facilite cette intégration et simplifie la gestion de votre infrastructure d’identité locale et cloud.



----------------------------------------------------------------------------------------------------------
## Téléchargez Azure AD Connect

Pour commencer à utiliser Azure AD Connect, vous pouvez télécharger la dernière version à l’adresse suivante : [Télécharger Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

----------------------------------------------------------------------------------------------------------








 

<!---HONumber=62-->