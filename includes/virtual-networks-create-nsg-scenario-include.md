## Scénario

Pour mieux illustrer la création de NSG, ce document utilise le scénario ci-dessous.

![Scénario de réseau virtuel](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

Dans ce scénario, vous allez créer un NSG pour chaque sous-réseau du réseau virtuel **TestVNet**, comme décrit ci-dessous :

- **NSG-FrontEnd**. Le NSG frontal est appliqué au sous-réseau *FrontEnd*, et contient deux règles :	
	- **rdp-rule**. Cette règle autorise le trafic RDP vers le sous-réseau *FrontEnd*.
	- **web-rule**. Cette règle autorise le trafic HTTP vers le sous-réseau *FrontEnd*.
- **Back-end NSG**. Le NSG principal est appliqué au sous-réseau *BackEnd*, et contient deux règles :	
	- **sql-rule**. Cette règle autorise le trafic SQL uniquement à partir du sous-réseau *FrontEnd*.
	- **web-rule**. Cette règle refuse tout trafic lié à Internet à partir du sous-réseau *BackEnd*.

La combinaison de ces règles crée un scénario de type DMZ, où le sous-réseau principal peut recevoir uniquement du trafic entrant pour SQL en provenance du sous-réseau frontal, et n’a pas accès à Internet, tandis que le sous-réseau frontal peut communiquer avec Internet et recevoir uniquement des requêtes HTTP entrantes.
 

<!---HONumber=AcomDC_0525_2016-->