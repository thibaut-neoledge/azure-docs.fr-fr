<properties 
	pageTitle="Offres de sécurité pour les machines virtuelles Azure" 
	description="Aperçu rapide des principales fonctions de sécurité des machines virtuelles Azure - Liens vers des informations détaillées" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="kathydav"/>

#Offres de sécurité pour les machines virtuelles Azure

<p>Les tâches de sécurité et la configuration des machines virtuelles Azure sont globalement identiques à celles des autres machines virtuelles ou ordinateurs physiques. Ces tâches incluent des tâches de base, comme l'utilisation de mots de passe forts, l'utilisation de noms d'utilisateur complexes et la mise en place d'une politique et d'une planification pour la mise à jour du système d'exploitation et des applications. De plus, nous recommandons de suivre les méthodes suivantes et nous vous indiquons les critères que vous pouvez utiliser pour les mettre en pratique : 

- Exécution d'un logiciel antivirus/anti-programmes malveillants

- Utilisation de listes de contrôle d'accès réseau (ACL) sur les points de terminaison
 
##Exécution d'un logiciel antivirus/anti-programmes malveillants

Azure propose plusieurs options pour les solutions antivirus/anti-programmes malveillants. Cependant, c'est à vous de les gérer. Vous devrez par exemple décider de l'exécution d'analyses et de l'installation des mises à jour. Vous pouvez ajouter la prise en charge de l'antivirus lorsque vous créez la machine virtuelle ou ultérieurement. Actuellement, trois solutions sont proposées en tant qu'extensions de sécurité. Elles peuvent être installées sur des machines virtuelles nouvelles ou existantes.

- [Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Azure](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Azure](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Déploiement de solutions anti-programmes malveillants sur des machines virtuelles Azure](http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)
 

##Utilisation de listes de contrôle d'accès réseau (ACL) sur les points de terminaison de machine virtuelle

Les listes de contrôle d'accès réseau vous permettent d'accepter ou de refuser le trafic entrant vers un point de terminaison de machine virtuelle. Cette capacité de filtrage des paquets offre une couche de sécurité supplémentaire. Pour plus d'informations sur ce sujet et des liens vers des instructions, consultez la page [À propos des listes de contrôle d'accès (ACL) réseau](http://go.microsoft.com/fwlink/?LinkId=506655).

##Ressources supplémentaires
[Ressources](http://azure.microsoft.com/support/trust-center/resources/) du Centre de gestion de la confidentialité Microsoft Azure



<!--HONumber=45--> 
