* Dans **Ajouter un serveur vCenter** , spécifiez un nom convivial pour l’hôte vSphere ou le serveur vCenter, et indiquez l’adresse IP ou le nom de domaine complet public du serveur. Conservez le port 443, sauf si vos serveurs VMware sont configurés pour écouter les demandes sur un port différent. Sélectionnez le compte à connecter au serveur VMware vCenter ou vSphere ESXi. Cliquez sur **OK**.

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > Si vous ajoutez le serveur VMware vCenter ou un hôte VMware vSphere à l’aide d’un compte qui ne dispose pas des privilèges d’administrateur sur le serveur vCenter ou hôte, assurez-vous que le compte bénéficie des autorisations suivantes : centre de données, magasin de données, dossier, hôte, réseau, ressource, machine virtuelle, commutateur distribué vSphere. En outre, le serveur VMware vCenter a besoin du privilège d’affichage de stockage.


<!--HONumber=Jan17_HO3-->


