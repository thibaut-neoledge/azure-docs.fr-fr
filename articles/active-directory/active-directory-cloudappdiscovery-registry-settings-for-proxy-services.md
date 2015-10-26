<properties 
	pageTitle="Paramètres de Registre de Cloud App Discovery pour les services de proxy" 
	description="L’objectif de cette rubrique est de présenter les étapes que vous devez effectuer pour définir le port nécessaire sur les ordinateurs exécutant l’agent Cloud App Discovery." 
	services="active-directory" 
	documentationCenter="" 
	authors="markusvi" 
	manager="swadhwa" 
	editor="lisatoft"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/23/2015" 
	ms.author="markusvi"/>

# Paramètres de Registre de Cloud App Discovery pour les services de proxy

Par défaut, l’agent Cloud App Discovery est configuré pour utiliser uniquement les ports 80 ou 443. Si vous envisagez d’installer Cloud App Discovery dans un environnement avec un serveur proxy qui utilise un port personnalisé (ni 443, ni 80), vous devez configurer vos agents pour utiliser ce port. La configuration est basée sur une clé de Registre.


L’objectif de cette rubrique est de présenter les étapes que vous devez effectuer pour définir le port nécessaire sur les ordinateurs exécutant l’agent Cloud App Discovery.



**Pour modifier le port utilisé par l’ordinateur exécutant l’agent Cloud App Discovery, effectuez les opérations suivantes :**


1. Démarrez l’Éditeur du Registre. <br> ![Exécuter](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)

2. Créez ou accédez à la clé de Registre suivante : <br> **HKLM\_LOCAL\_MACHINE\\Software\\Microsoft\\Cloud App Discovery\\Endpoint**

3. Créez une valeur de **chaînes multiples** nommée **Ports**. ![Nouveau](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)

4. Pour ouvrir la boîte de dialogue**Modifier les chaînes multiples**, double-cliquez sur la valeur de Ports.


5. Dans la zone de texte Données de la valeur, tapez les valeurs suivantes et ajoutez tous les ports personnalisés qui sont utilisés par votre organisation : <br><br> **80** <br> **8080** <br> **8118** <br> **8888** <br> **81** <br> **12080** <br> **6999** <br> **30606** <br> **31595** <br> **4080** <br> **443** <br> **1110** <br><br> ![Modifier les chaînes multiples](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Modifier les chaînes multiples**.



**Ressources supplémentaires**


* [Comment puis-je détecter les applications cloud non approuvées utilisées au sein de mon organisation ?](active-directory-cloudappdiscovery-whatis.md) 

<!---HONumber=Oct15_HO3-->