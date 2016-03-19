Lorsque vous ajoutez une connexion site à site à votre passerelle de réseau virtuel, vous devez d’abord créer une passerelle de réseau local afin de vous y rapporter à partir de votre configuration. Vérifiez que vous disposez d’une passerelle de réseau local configurée. Vous pouvez rechercher les passerelles de réseau local à l’aide de l’option **Parcourir** en filtrant sur **Passerelles de réseau local**.

1. À partir du panneau **Réseaux virtuels**, recherchez votre réseau virtuel, puis cliquez pour ouvrir le panneau. Dans le panneau, vous verrez votre passerelle répertoriée comme un *Périphérique connecté*.
2. Cliquez sur le ***nom de votre passerelle de réseau virtuel*** -> **Passerelle de réseau virtuel** -> **Paramètres** -> **Connexions**, puis sur **Ajouter**.
3. Attribuez un **nom** à votre connexion. Pour cet exemple, nous allons utiliser *GW1S2S*.
4. Pour **Type de connexion**, sélectionnez **Site à site (IPSec)**.
5. Pour **Passerelle de réseau virtuel**, la valeur est fixe, car vous vous connectez à partir de cette passerelle.
6. Pour **Passerelle de réseau local**, cliquez sur **Choisir une passerelle de réseau local** et sélectionnez la passerelle de réseau local que vous souhaitez utiliser. Pour cet exemple, nous allons utiliser *GW1LocalNet*.
7. Pour **Clé partagée**, les valeurs doivent correspondre à ce que vous avez pour votre périphérique VPN local. Si votre périphérique VPN sur votre réseau local ne fournit pas de clé partagée, vous pouvez en créer une, puis la saisir ici et sur votre périphérique local. Il est important que les deux correspondent.
8. Les valeurs restantes pour **Abonnement**, **Groupe de ressources** et **Emplacement** sont fixes.
9. Cliquez sur **OK** pour créer votre connexion. Le message *Création de la connexion* clignotera à l’écran.
10. Une fois la connexion créée, elle apparaît dans le panneau **Connexions** de votre passerelle.

<!---HONumber=AcomDC_0107_2016-->