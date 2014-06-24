<properties  writer="kathydav" editor="tysonn" manager="jeffreyg" />
# Gestion de la disponibilité des machines virtuelles

Pour garantir la disponibilité de votre application, nous conseillons
vivement d'utiliser plusieurs machines virtuelles offrant une
redondance. Pour cela, configurez plusieurs machines virtuelles
effectuant la même fonction ou le même rôle. Cette redondance est
requise pour bénéficier d'un niveau de service garanti. Veillez
également à prendre en considération les dépendances. Par exemple, si la
machine virtuelle << IIS1 >> dépend de services fournis par la machine
virtuelle << SQL1 >>, la redondance pour la machine virtuelle << SQL1 >>
est fournie par << SQL2 >> pour éviter les interruptions de service.
Pour plus d'informations sur les contrats de niveau de service,
consultez la section << Services cloud, machines virtuelles et réseau
virtuel >> de la rubrique [Contrats de niveau de service][1].

Cette méthode permet de s'assurer que votre application est disponible
en cas de défaillances du réseau local, de défaillances matérielles du
disque local et de tout temps d'arrêt planifié requis par la plateforme.

Vous gérez la disponibilité d'une application qui utilise plusieurs
machines virtuelles en ajoutant les machines virtuelles aux groupes à
haute disponibilité. Ces derniers sont directement liés aux domaines de
défaillance et de mise à jour. Un domaine de défaillance dans Azure est
défini en évitant les points de défaillance uniques, comme le
commutateur réseau ou l'unité d'alimentation d'un rack de serveurs. En
fait, un domaine de défaillance équivaut pratiquement à un rack de
serveurs physiques. Lorsque plusieurs machines virtuelles sont
connectées dans un service cloud, un groupe à haute disponibilité les
place dans différents domaines de défaillance. Le schéma suivant
illustre deux groupes à haute disponibilité avec deux machines
virtuelles dans chaque groupe.

![Domaines de mise à
jour](./media/manage-vm-availability/UpdateDomains.png)

Régulièrement, Azure met à jour le système d'exploitation qui héberge
les instances d'une application. Une machine virtuelle est arrêtée lors
de l'application d'une mise à jour. Un domaine de mise à jour permet de
s'assurer que toutes les instances de machines virtuelles ne soient pas
mises à jour au même moment. Lorsque vous attribuez plusieurs machines
virtuelles à un groupe à haute disponibilité, Azure s'assure que les
machines sont affectées à différents domaines de mise à jour. Le schéma
précédent illustre deux machines virtuelles exécutant IIS (Internet
Information Services) dans des domaines de mise à jour séparés et deux
machines virtuelles exécutant SQL Server également dans des domaines de
mise à jour distincts.

Veillez à utiliser une combinaison de groupes à haute disponibilité et
de points de terminaison à équilibrage de charge pour vous assurer que
votre application soit toujours disponible et qu'elle s'exécute
correctement. Pour plus d'informations sur l'utilisation de points de
terminaison à charge équilibrée, consultez la page [Machines virtuelles
à équilibrage de charge](../load-balance-virtual-machines).

Cette procédure comprend les étapes suivantes :

* [Étape 1 : création d'une machine virtuelle et d'un groupe à haute
  disponibilité](#createset)
* [Étape 2 : ajout d'une machine virtuelle au service cloud et
  attribution de celle-ci au groupe à haute disponibilité pendant le
  processus de création](#addmachine)
* [Étape 3 : (facultatif) création d'un groupe à haute disponibilité
  pour des machines virtuelles créées précédemment](#previousmachine)
* [Étape 4 : (facultatif) ajout d'une machine virtuelle créée
  précédemment à un groupe à haute disponibilité](#existingset)
## <a  id="createset"> </a>Étape 1 : création d'une machine virtuelle et d'un groupe à haute disponibilité

Pour créer un groupe à haute disponibilité contenant des machines
virtuelles, vous pouvez créer la première machine virtuelle et le groupe
à haute disponibilité au même moment. Ensuite, vous pouvez ajouter des
machines virtuelles au groupe à haute disponibilité au fur et à mesure
de leur création. Vous pouvez également créer des machines virtuelles,
créer un groupe à haute disponibilité, puis ajouter toutes les machines
au groupe.

**Création d'une machine virtuelle et d'un groupe à haute
disponibilité**

1.  Si ce n'est pas déjà fait, connectez-vous au portail de gestion
    Azure.

2.  Dans la barre de commandes, cliquez sur **New**.
    
    ![Créer une machine
    virtuelle](./media/manage-vm-availability/Create.png)

3.  Cliquez sur **Virtual Machine**, puis sur **From Gallery**.
    
    La boîte de dialogue **Select the virtual machine operating system**
    s'affiche.

4.  Dans **Platform Images**, sélectionnez une image, puis cliquez sur
    la flèche pour continuer.
    
    La boîte de dialogue **Virtual machine configuration** s'affiche.

5.  Dans **Virtual Machine Name**, tapez le nom que vous souhaitez
    utiliser pour la machine virtuelle.

6.  Dans **New User Name**, tapez le nom du compte administratif que
    vous voulez utiliser pour gérer le serveur.

7.  Dans **New Password**, tapez un mot de passe fort pour le compte
    administratif. Dans **Confirm Password**, tapez à nouveau le mot de
    passe que vous avez entré précédemment.

8.  Dans **Taille**, sélectionnez la taille à utiliser pour la machine
    virtuelle. Celle-ci dépend du nombre de cœurs nécessaires à votre
    application.

9.  Cliquez sur la flèche pour continuer.
    
    La boîte de dialogue **Virtual machine mode** s'affiche.

10. Choisissez **Stand-alone virtual machine**.

11. Dans **DNS Name**, tapez un nom pour le service cloud créé pour la
    machine. Le nom peut comporter entre 3 et 24 lettres minuscules et
    chiffres.

12. Dans **Storage Account**, sélectionnez le compte de stockage du
    fichier de disque dur virtuel. Vous pouvez également choisir la
    création automatique d'un compte de stockage. Un seul compte de
    stockage est automatiquement créé par région. Toutes les autres
    machines virtuelles créées avec ce paramètre sont placées dans ce
    compte de stockage. Vous êtes limité à 20 comptes de stockage.

13. Dans **Region/Affinity Group/Virtual Network**, sélectionnez la
    région, le groupe d'affinités ou le réseau virtuel devant contenir
    la machine virtuelle. Pour plus d'informations sur les groupes
    d'affinités, consultez la page [À propos des groupes d'affinités de
    réseau virtuel][2].

14. Cliquez sur la flèche pour continuer.
    
    La boîte de dialogue **Virtual machine options** s'affiche.

15. Dans **Availability Set**, sélectionnez **Create availability set**.

16. Dans **Availability Set Name**, entrez le nom du groupe à haute
    disponibilité.

17. Cliquez sur la flèche pour créer la machine virtuelle et le groupe à
    haute disponibilité.
    
    Dans le tableau de bord de la nouvelle machine virtuelle, vous
    pouvez cliquer sur **Configure** pour voir que la machine virtuelle
    est membre du nouveau groupe à haute disponibilité.
## <a  id="addmachine"> </a>Étape 2 : ajout d'une machine virtuelle au service cloud et attribution de celle-ci au groupe à haute disponibilité pendant le processus de création

L'étape précédente vous a montré comment créer une machine virtuelle et
un groupe à haute disponibilité au même moment. Vous pouvez à présent
créer une machine virtuelle, la connecter au service cloud de la
première machine virtuelle, puis l'ajouter au groupe à haute
disponibilité que vous avez créé précédemment.

**Connexion d'une nouvelle machine virtuelle et ajout de celle-ci au
groupe à haute disponibilité**

1.  Si ce n'est pas déjà fait, connectez-vous au portail de gestion
    Azure.

2.  Dans la barre de commandes, cliquez sur **New**.
    
    ![Créer une machine
    virtuelle](./media/manage-vm-availability/Create.png)

3.  Cliquez sur **Virtual Machine**, puis sur **From Gallery**.
    
    ![Créer à partir de la
    galerie](./media/manage-vm-availability/CreateNew.png)
    
    La boîte de dialogue **Select the virtual machine operating system**
    s'affiche. Vous pouvez à présent sélectionner une image à partir de
    la galerie d'images.

4.  Cliquez sur **Platform Images**, sélectionnez l'image de plateforme
    que vous voulez utiliser, puis cliquez sur la flèche pour continuer.
    
    La boîte de dialogue **Virtual machine configuration** s'affiche.

5.  Dans **Virtual Machine Name**, tapez le nom que vous souhaitez
    utiliser pour la machine virtuelle.

6.  Dans **New User Name**, tapez le nom du compte administratif que
    vous voulez utiliser pour gérer le serveur.

7.  Dans **New Password**, tapez un mot de passe fort pour le compte
    administratif de la machine virtuelle. Dans **Confirm Password**,
    entrez de nouveau le mot de passe.

8.  Dans **Taille**, sélectionnez la taille à utiliser pour la machine
    virtuelle. Celle-ci dépend du nombre de cœurs nécessaires à votre
    application.

9.  Pour une machine virtuelle s'exécutant sous le système
    d'exploitation Linux, vous pouvez choisir de sécuriser la machine
    avec une clé SSH.

10. Cliquez sur la flèche pour continuer.
    
    La boîte de dialogue **Virtual machine mode** s'affiche.

11. Sélectionnez **Connect to existing Virtual Machine** pour créer une
    machine virtuelle qui sera connectée à la première machine virtuelle
    dans le groupe à haute disponibilité. Sélectionnez le service cloud
    contenant la machine virtuelle dans le groupe à haute disponibilité.

12. Dans **Storage Account**, sélectionnez un compte sur lequel le
    fichier de disque dur virtuel est stocké.

13. Dans **Region/Affinity Group/Virtual Network**, sélectionnez la
    région dans laquelle placer la machine virtuelle.

14. Cliquez sur la flèche pour continuer.
    
    La boîte de dialogue **Virtual machine options** s'affiche.

15. Sélectionnez le groupe à haute disponibilité qui a été créé en même
    temps que la première machine virtuelle.

16. Cliquez sur la coche pour créer la machine virtuelle connectée et
    l'ajouter au groupe à haute disponibilité.
    
    Dans le tableau de bord de la nouvelle machine virtuelle, vous
    pouvez cliquer sur **Configure** pour voir que la machine virtuelle
    est membre du nouveau groupe à haute disponibilité.
## <a  id="previousmachine"> </a>Étape 3 : (facultatif) création d'un groupe à haute disponibilité pour des machines virtuelles créées précédemment

Vous pouvez créer un groupe à haute disponibilité et y ajouter une
machine virtuelle une fois celle-ci créée. Après avoir créé une machine
virtuelle, vous pouvez configurer sa taille et indiquer si elle est
membre d'un groupe à haute disponibilité.

**Création d'un groupe à haute disponibilité**

1.  Si ce n'est pas déjà fait, connectez-vous au portail de gestion
    Azure.

2.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine
    virtuelle à configurer.

3.  Cliquez sur **Configure**.

4.  Dans la section **Availability Set**, sélectionnez **Create
    Availability Set**, puis entrez un nom pour le groupe.

5.  Cliquez sur **Save**.
    
    **Remarque :** cette procédure peut entraîner un redémarrage de la
    machine virtuelle visant à finaliser l'appartenance au groupe à
    haute disponibilité.
## <a  id="existingset"> </a>Étape 4 : (facultatif) ajout d'une machine virtuelle créée précédemment à un groupe à haute disponibilité

Vous pouvez facilement ajouter une machine virtuelle existante à un
groupe à haute disponibilité qui a été créé précédemment. Pour cela, la
machine virtuelle doit être connectée au même service cloud que les
autres machines virtuelles du groupe. Pour plus d'informations sur la
connexion des machines virtuelles, consultez la rubrique [Connexion de
machines virtuelles dans un service
cloud](../virtual-machines-connect-cloud-service).

**Ajout d'une machine virtuelle existante à un groupe à haute
disponibilité**

1.  Si ce n'est pas déjà fait, connectez-vous au portail de gestion
    Azure.

2.  Cliquez sur **Virtual Machines**, puis sélectionnez la machine
    virtuelle que vous souhaitez ajouter au groupe à haute
    disponibilité.

3.  Cliquez sur **Configure**.

4.  Dans la section **Availability Set**, sélectionnez le groupe à haute
    disponibilité que vous avez créé précédemment.

5.  Cliquez sur **Save**.
    
    **Remarque :** cette procédure peut entraîner un redémarrage de la
    machine virtuelle visant à finaliser l'appartenance au groupe à
    haute disponibilité.

<!-- LINKS -->



[1]: http://www.windowsazure.com/en-us/support/legal/sla/
[2]: http://msdn.microsoft.com/library/windowsazure/jj156085.aspx
