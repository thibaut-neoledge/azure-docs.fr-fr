1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].
    Dans la barre de commandes, cliquez sur **Nouveau**.

2.  Cliquez sur **Virtual Machine**, puis sur **From Gallery**.

3.  Dans **Choose an Image**, sélectionnez une image dans l’une des listes. (Les images disponibles dépendent de l’abonnement que vous utilisez.) Cliquez sur la flèche pour continuer.

4.  Si plusieurs versions de l’image sont disponibles, dans **Version Release Date**, choisissez la version à utiliser.

5.  Dans **Nom de la machine virtuelle**, tapez le nom que vous souhaitez utiliser. Pour cette machine virtuelle, tapez **MonTestMV1**.

6.  Dans **Taille**, sélectionnez la taille à utiliser pour la machine virtuelle. Celle-ci dépend du nombre de cœurs nécessaires à votre application. Pour cette machine virtuelle, sélectionnez la taille la plus petite.

7.  Dans **Nouveau nom d’utilisateur**, tapez le nom du compte qui servira à administrer la machine virtuelle. Vous ne pouvez pas utiliser le nom d’utilisateur racine. Pour cette machine virtuelle, tapez **NouvelUtilisateur1**.

8.  Sous Authentification, cochez **Fournir un mot de passe**. Fournissez ensuite les informations requises, puis cliquez sur la flèche pour continuer.

9.  Bien qu’il soit possible de rassembler plusieurs machines virtuelles dans le service cloud, vous n’allez en créer qu’une seule dans le cadre de ce didacticiel. Pour ce faire, sélectionnez **Create a new cloud service**.

10. Dans **Cloud Service DNS Name**, tapez un nom comprenant entre 3 et 24 caractères (minuscules et chiffres uniquement). Vous devrez créer votre propre nom de service cloud, car il doit être unique dans Azure. Le nom du service cloud fait alors partie de l'URI servant à contacter la machine virtuelle via le service cloud.

11. Dans **Region/Affinity Group/Virtual Network**, sélectionnez l’emplacement de la machine virtuelle.

12. Vous pouvez sélectionner le compte sur lequel le fichier de disque dur virtuel est stocké. Pour ce didacticiel, acceptez le paramètre par défaut **Use an Automatically Generated Storage Account**.

13. Sous **Availability Set**, utilisez le paramètre par défaut **None**.

14. Sous **Points de terminaison**, vérifiez le point de terminaison créé automatiquement pour autoriser les connexions SSH (Secure Shell) à la machine virtuelle. Les points de terminaison permettent aux ressources disponibles sur Internet ou aux autres réseaux virtuels de communiquer avec une machine virtuelle. Vous pouvez ajouter d’autres points de terminaison maintenant ou en créer ultérieurement. Pour obtenir des instructions sur leur création à une date ultérieure, consultez la page [Configuration des points de terminaison sur une machine virtuelle][Configuration des points de terminaison sur une machine virtuelle].

15. Sous **Agent de machine virtuelle**, consultez les extensions disponibles. Ces extensions proposent différentes fonctionnalités qui facilitent l'utilisation et la gestion des machines virtuelles. Pour plus d'informations, consultez la page [Extensions de machine virtuelle Azure][Extensions de machine virtuelle Azure].

Une fois la machine virtuelle et le service cloud créés par Azure, le portail de gestion répertorie la nouvelle machine virtuelle sous **Machines virtuelles** et le service cloud sous **Services de cloud**. Ces derniers démarrent automatiquement.

  [portail de gestion Azure]: http://manage.windowsazure.com
  [Configuration des points de terminaison sur une machine virtuelle]: http://azure.microsoft.com/fr-fr/documentation/articles/virtual-machines-set-up-endpoints/
  [Extensions de machine virtuelle Azure]: http://go.microsoft.com/FWLink/p/?LinkID=390493
