<properties  authors="kathydav" editor="tysonn" manager="jeffreyg" />

# Création d'une machine virtuelle personnalisée

La création d'une machine virtuelle personnalisée vous permet de choisir des options qui ne sont pas disponibles avec la méthode **Quick Create**. Ces options incluent la connexion à un réseau virtuel, à un service cloud existant et à un groupe à haute disponibilité.

Chaque machine virtuelle est associée à un service cloud, soit par elle-même, soit avec les autres machines virtuelles du même service cloud. Une des raisons justifiant souvent le placement de plusieurs machines virtuelles dans le même service cloud est de permettre l'équilibrage de charge pour l'application. Si une seule machine virtuelle est nécessaire à votre application ou si vous créez la première machine virtuelle, vous pouvez créer le service cloud lors de la création de la machine virtuelle. Sinon, vous pouvez ajouter la nouvelle machine virtuelle à un service cloud existant.

**Important** : si vous souhaitez que la machine virtuelle utilise un réseau virtuel pour pouvoir vous y connecter directement par nom d'hôte ou configurer des connexions entre différents locaux, veillez à spécifier le réseau virtuel lors de la création de la machine virtuelle. Vous pouvez configurer une machine virtuelle pour qu'elle rejoigne uniquement un réseau virtuel lorsque vous la créez. Pour plus d'informations sur les réseaux virtuels, consultez la page [Présentation du réseau virtuel Azure][1].

1.  Connectez-vous au [portail de gestion Azure][2].

2.  Dans la barre de commandes, cliquez sur **New**.

3.  Cliquez sur **Compute**, **Virtual Machine**, puis sur **From Gallery**.

4.  Dans **Platform Images**, sélectionnez l'image de plateforme à utiliser, puis cliquez sur la flèche pour continuer.

5.  Si plusieurs versions de l'image sont disponibles, dans **Version Release Date**, choisissez la version à utiliser.

6.  Dans **Virtual Machine Name**, tapez le nom que vous souhaitez utiliser pour la machine virtuelle.

7.  Dans **Taille**, sélectionnez la taille à utiliser pour la machine virtuelle. Celle-ci dépend du nombre de cœurs nécessaires à votre application.

8.  Dans **New User Name**, tapez le nom du compte administratif que vous voulez utiliser pour gérer le serveur.

9.  Dans **New Password**, tapez un mot de passe fort pour le compte administratif de la machine virtuelle. Dans **Confirm Password**, entrez de nouveau le mot de passe.

10. Cliquez sur la flèche pour continuer.

11. Dans **Cloud Service**, effectuez l'une des actions suivantes :

* S'il s'agit de la première ou de la seule machine virtuelle du service cloud, sélectionnez **Create a New Cloud Service**. Dans **Cloud Service DNS Name**, tapez un nom comprenant entre 3 et 24 caractères (minuscules et chiffres uniquement). Ce nom fait alors partie de l'URI servant à contacter la machine virtuelle via le service cloud.
* Si cette machine virtuelle est ajoutée à un service cloud, sélectionnez-la dans la liste.
  
  	**Remarque** : Pour plus d'informations sur le placement des machines virtuelles dans le même service cloud, consultez la rubrique [Connexion de machines virtuelles dans un service cloud][3].

1.  Dans **Region/Affinity Group/Virtual Network**, sélectionnez la région, le groupe d'affinités ou le réseau virtuel à utiliser pour la machine virtuelle. Pour plus d'informations sur les groupes d'affinités, consultez la page [À propos des groupes d'affinités de réseau virtuel][4].

2.  Dans **Storage Account**, sélectionnez un compte de stockage existant pour le fichier de disque dur virtuel ou utilisez un compte de stockage automatiquement généré. Un seul compte de stockage est automatiquement créé par région. Toutes les autres machines virtuelles créées avec ce paramètre sont placées dans ce compte de stockage. Vous êtes limité à 20 comptes de stockage.

3.  Si vous souhaitez que la machine virtuelle fasse partie d'un groupe à haute disponibilité, dans **Availability Set**, sélectionnez **Create availability set** ou ajoutez-la à un groupe à haute disponibilité existant.
    
    **Remarque** : les machines virtuelles qui sont membres d'un groupe à haute disponibilité sont déployées sur des domaines de défaillance différents. Le placement de plusieurs machines virtuelles dans un groupe à haute disponibilité permet de vous assurer de la disponibilité de votre application lors de défaillances réseau, de défaillances matérielles d'un disque dur local et de temps d'arrêt planifiés.

4.  Sous **VM Agent**, indiquez si vous souhaitez installer l'agent MV. Cet agent fournit l'environnement dans lequel vous installez les extensions qui permettent d'interagir avec la machine virtuelle. Pour plus d'informations, consultez la page [Utilisation d'extensions][5].

5.  Sous **Endpoints**, passez en revue les nouveaux points de terminaison qui seront créés pour autoriser les connexions à la machine virtuelle, tels que le Bureau à distance ou un client Secure Shell (SSH), par exemple. Vous pouvez également ajouter des points de terminaison maintenant ou les créer ultérieurement. Pour obtenir des instructions sur leur création à une date ultérieure, consultez la page [Configuration de la communication avec une machine virtuelle][6].

6.  Cliquez sur la flèche pour créer la machine virtuelle.
    
    ![Création d’une machine virtuelle personnalisée réussie](./media/howto-custom-create-vm/VMSuccessWindows.png)



[1]: http://go.microsoft.com/fwlink/p/?LinkID=294063
[2]: http://manage.windowsazure.com
[3]: http://www.windowsazure.com/fr-fr/manage/windows/how-to-guides/connect-to-a-cloud-service/
[4]: http://msdn.microsoft.com/fr-fr/library/windowsazure/
[5]: http://go.microsoft.com/FWLink/p/?LinkID=390493
[6]: http://www.windowsazure.com/fr-fr/manage/linux/how-to-guides/setup-endpoints/