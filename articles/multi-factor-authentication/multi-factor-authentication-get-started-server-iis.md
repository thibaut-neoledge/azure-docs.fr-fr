<properties 
	pageTitle="Authentification IIS et serveur Azure Multi-Factor Authentication" 
	description="Il s'agit de la page d'authentification multifacteur Azure qui facilite le déploiement de l’authentification IIS et du serveur Azure Multi-Factor Authentication." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="02/18/2016" 
	ms.author="billmath"/>

# Authentification IIS

La section Authentification IIS du serveur Azure Multi-Factor Authentication vous permet d'activer et de configurer l'authentification IIS pour l'intégration avec les applications Web Microsoft IIS. Le serveur Azure Multi-Factor Authentication installe un plug-in capable de filtrer les demandes envoyées au serveur Web IIS afin d'ajouter Azure Multi-Factor Authentication. Le plug-in IIS prend en charge l'authentification par formulaire et l'authentification intégrée Windows HTTP. Les adresses IP de confiance peuvent également être configurées pour exempter les adresses IP internes de l'authentification à deux facteurs.


![Authentification IIS](./media/multi-factor-authentication-get-started-server-iis/iis.png)


## Utilisation de l'authentification IIS basée sur le formulaire avec le serveur Azure Multi-Factor Authentication

Pour sécuriser une application Web IIS qui utilise l'authentification par formulaire, installez le serveur Azure Multi-Factor Authentication sur le serveur Web IIS et configurez le serveur conformément à la procédure suivante.

1. Depuis le serveur Azure Multi-Factor Authentication, cliquez sur l'icône de l'authentification IIS dans le menu de gauche.
2. Cliquez sur l'onglet d’authentification basée sur formulaire.
3. Cliquez sur le bouton Ajouter...
4. Pour détecter automatiquement les variables de nom d'utilisateur, de mot de passe et de domaine, entrez l'URL de connexion (par exemple, https://localhost/contoso/auth/login.aspx) dans la boîte de dialogue Auto-configurer un site Web basé sur formulaire et cliquez sur OK.
5. Cochez la case de correspondance d'utilisateur Authentification multifacteur requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée.
6. Si les variables de page ne peuvent pas être détectées automatiquement, cliquez sur le bouton Spécifier manuellement... dans la boîte de dialogue Auto-configurer un site Web basé sur formulaire.
7. Dans la boîte de dialogue Ajouter un site Web basé sur formulaire, entrez l'URL de la page de connexion dans le champ URL de soumission et entrez un nom d'application (facultatif). Le nom de l'application apparaît dans les rapports du serveur Azure Multi-Factor Authentication et peut être affiché dans les messages d'authentification SMS ou d’application mobile. Voir le fichier d'aide pour plus d'informations sur l'URL de soumission. 
8. Sélectionnez le format de demande approprié. Pour la plupart des applications Web, le format est défini sur « POST or GET ».
9. Entrez les variables Nom d'utilisateur, Mot de passe et Domaine (si elles apparaissent sur la page de connexion). Vous devrez peut-être accéder à la page de connexion dans un navigateur Web. Pour ce faire, cliquez avec le bouton droit de la souris sur la page et sélectionnez « Afficher la Source » pour rechercher les noms des zones de saisie dans la page.
10. Cochez la case de correspondance d'utilisateur Authentification multifacteur Azure requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité.
11.  Cliquez sur le bouton Avancé... pour passer en revue les paramètres avancés, notamment la possibilité de sélectionner un fichier de page de refus personnalisé, de mettre en cache les authentifications réussies sur le site Web pendant une période définie à l'aide de cookies, et de sélectionner comment authentifier les informations d'identification principales en fonction du domaine Windows, d’un répertoire LDAP ou d’un serveur RADIUS. Lorsque vous avez terminé, cliquez sur le bouton OK pour revenir à la boîte de dialogue Ajouter un site Web basé sur formulaire. Voir le fichier d'aide pour plus d'informations sur les paramètres avancés.
12. Cliquez sur le bouton OK.
13. Une fois les variables de page et d’URL détectées ou entrées, les données de site Web s’affichent dans le panneau de configuration basé sur formulaire.
14. Consultez la section Activation des plug-ins IIS pour le serveur Azure Multi-Factor Authentication ci-dessous pour terminer la configuration de l'authentification IIS. 

## Utilisation de l'authentification Windows intégrée avec le serveur Azure Multi-Factor Authentication

Pour sécuriser une application Web IIS qui utilise l'authentification Windows HTTP intégrée, installez le serveur Azure Multi-Factor Authentication sur le serveur Web IIS et configurez le serveur conformément à la procédure suivante.

1. Depuis le serveur Azure Multi-Factor Authentication, cliquez sur l'icône de l'authentification IIS dans le menu de gauche.
2. Cliquez sur l'onglet HTTP. Cliquez sur l'onglet d’authentification basée sur formulaire.
3. Cliquez sur le bouton Ajouter...
4. Dans la boîte de dialogue Ajouter une URL de base, entrez l'URL du site Web où l'authentification HTTP est effectuée (par exemple, http://localhost/owa) dans le champ URL de base et entrez un nom d'application (facultatif). Le nom de l'application apparaît dans les rapports du serveur Azure Multi-Factor Authentication et peut être affiché dans les messages d'authentification SMS ou d’application mobile.
5. Réglez le délai de déconnexion en cas d'inactivité et la durée maximale des sessions si les valeurs par défaut sont insuffisantes
6. Cochez la case de correspondance d'utilisateur Authentification multifacteur requise si tous les utilisateurs ont été ou seront importés sur le serveur et soumis à l'authentification multifacteur. Si un grand nombre d'utilisateurs n'ont pas encore été importés sur le serveur et/ou ne seront pas soumis à l'authentification multifacteur, laissez la case désactivée. Voir le fichier d’aide pour plus d’informations sur cette fonctionnalité. 
7. Cochez la case de cache de cookie si vous le souhaitez.
8. Cliquez sur le bouton OK.
9. Consultez la section [Activation des plug-ins IIS pour le serveur Azure Multi-Factor Authentication](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) ci-dessous pour terminer la configuration de l'authentification IIS. 


## Activation des plug-ins IIS pour le serveur Azure Multi-Factor Authentication

Une fois que vous avez configuré les URL et les réglages d’authentification basés sur formulaire ou HTTP, vous devez sélectionner les emplacements où les plug-ins IIS d'Azure multi-Factor Authentification IIS doivent être chargés et activés dans IIS. Procédez comme suit :

1. Si vous utilisez IIS 6, cliquez sur l'onglet ISAPI et sélectionnez le site Web où l'application Web est exécutée (par exemple, Site Web par défaut) pour activer le plug-in de filtre ISAPI Azure Multi-Factor Authentication pour ce site.
2. Si vous utilisez IIS 7 ou version ultérieure, cliquez sur l'onglet Module natif et sélectionnez le serveur, les sites Web ou les applications pour activer le plug-in IIS aux niveaux de votre choix.
3. Cochez la case Activer l’authentification IIS en haut de l'écran. L’application IIS sélectionnée est désormais protégée par le serveur Azure Multi-Factor Authentication. Assurez-vous que les utilisateurs ont été importés sur le serveur. Consultez la section Adresses IP de confiance ci-dessous si vous souhaitez ajouter des adresses IP internes à la liste blanche, de sorte que l'authentification à deux facteurs ne soit pas requise lors de la connexion au site Web à partir de ces emplacements. 


## Adresses IP de confiance

Les adresses IP approuvées permettent aux utilisateurs de contourner l'authentification multifacteur Azure pour les requêtes de sites Web provenant d'adresses IP ou de sous-réseaux spécifiques. Il se peut par exemple que vous souhaitiez exempter certains utilisateurs de l'authentification multifacteur Azure lorsqu’ils se connectent du bureau. Pour ce faire, vous devez définir le sous-réseau du bureau comme adresse IP de confiance. Pour configurer les adresses IP de confiance, utilisez la procédure suivante :

1. Dans la section Authentification IIS, cliquez sur l'onglet Adresses IP de confiance. 
2. Cliquez sur le bouton Ajouter...
3. Lorsque la boîte de dialogue Ajouter des adresses IP de confiance s'affiche, sélectionnez le bouton Adresse IP unique, Plage d'adresses IP ou Sous-réseau.
4. Entrez l'adresse IP, la plage d'adresses IP ou le sous-réseau à ajouter à la liste blanche. Si vous entrez un sous-réseau, sélectionnez le masque réseau approprié et cliquez sur OK. La liste blanche est alors mise à jour.

<!---HONumber=AcomDC_0224_2016-->