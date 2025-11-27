# Pourquoi privilégier certains composants à d'autres ?

Lorsqu'on veut héberger un serveur chez soi, il y a de nombreux éléments à prendre en compte. Selon moi, le plus important est la consommation d'électricité. En effet, le prix de l'électricité en France est passé d'environ 0,15€ à 0,20€ le kWh en quelques années, ce qui rend essentiel de choisir un serveur qui consomme le moins d'électricité possible. Bien sûr, il est possible d'optimiser cette consommation par des ajustements logiciels, mais pour ça il faut des composants capables de supporter ces ajustements.

Dans cette documentation, je vais tenter d'expliquer certains détails techniques de la manière la plus simple possible. Nous allons aborder ici tous les composants nécessaires ainsi que certains composants facultatifs qui peuvent être utilisés dans certains cas. Enfin, le dernier point de cette documentation sera consacré à différentes façons d'optimiser la consommation énergétique de son serveur au niveau logiciel.

- [Pourquoi privilégier certains composants à d'autres ?](#pourquoi-privilégier-certains-composants-à-dautres-)
  - [Les questions à se poser avant de selectionner des composants](#les-questions-à-se-poser-avant-de-selectionner-des-composants)
    - [ECC](#ecc)
    - [HDD ou SSD ?](#hdd-ou-ssd-)
  - [Les composants nécessaires](#les-composants-nécessaires)
    - [CPU (Processeur)](#cpu-processeur)
    - [RAM](#ram)
    - [Carte mère](#carte-mère)
    - [HDD et SSD](#hdd-et-ssd)
      - [HDD](#hdd)
      - [SSD](#ssd)
    - [Case](#case)
    - [PSU (Alimentation)](#psu-alimentation)
  - [Les composants facultatifs](#les-composants-facultatifs)
    - [Les cartes PCIe (HBA, 10GbE, GPU, etc.)](#les-cartes-pcie-hba-10gbe-gpu-etc)
  - [Les optimisations logiciels](#les-optimisations-logiciels)
    - [Spin down des disques](#spin-down-des-disques)
    - [C-States](#c-states)

## Les questions à se poser avant de selectionner des composants

Avant de sélectionner des composants, certaines questions doivent être abordées.

### ECC

La première question concerne l'Error Correction Code Memory. L'ECC est un type de RAM qui permet de détecter et corriger les erreurs avant qu'elles ne soient enregistrées sur les disques. Ces erreurs peuvent avoir un impact significatif, comme rendre un fichier corrompu et donc illisible.

Pour bénéficier de l'ECC dans un serveur, trois composants doivent supporter cette technologie : la RAM, le processeur (CPU) et la carte mère. Les composants compatibles ECC sont souvent bien plus chers que ceux qui ne le sont pas.

Bien que l'ECC offre des avantages non négligeables, il est important de prendre du recul. L'ECC est principalement utilisé dans des configurations professionnelles et est très rarement employé dans des builds self-hosted. La raison principale est son coût, mais aussi le fait que la RAM classique est généralement peu sujette à des défaillances. L'étude statistique la plus poussée sur le sujet a été menée par [Google en 2009](https://www.cs.toronto.edu/~bianca/papers/sigmetrics09.pdf). La conclusion de cette étude, réalisée sur une flotte massive de serveurs, est que la RAM non-ECC a un taux d'erreur non-correctible inférieur à 3 % par an. L'étude montre également que ce taux diminue avec le temps.

Dans le cas d'un serveur self-hosted, ce type d'erreur est donc extrêmement rare. L'ECC n'est donc pas essentiel, mais il peut être envisagé si vous en avez les moyens.

Si jamais le prix des composants compatibles ECC venait à baisser ça pourrait devenir une option intéressante, mais ce n'est malheureusement pas le cas à l'heure où j'écris ces lignes (novembre 2025).

### HDD ou SSD ?

Les SSD peuvent sembler être une bonne solution sur le papier : ils consomment très peu d'énergie, sont plus rapides que les disques durs et sont de plus en plus abordables. Cependant, il est décourageant de vouloir créer un NAS uniquement avec des SSD. Comparés aux HDD, les SSD ont une durée de vie plus incertaine. Ils sont testés pour évaluer combien de fois on peut les remplir et les vider avant qu'ils ne lâchent. Malgré ces tests, il arrive fréquemment que des SSD tombent en panne après seulement quelques semaines d'utilisation.

Les HDD, quant à eux, offrent des indicateurs plus fiables pour signaler une défaillance : un bruit anormal, des secteurs défectueux, etc. En cas de panne d'un disque dur, il est généralement possible de récupérer une grande partie des données, car tout est écrit physiquement sur les disques, contrairement aux SSD.

Cela dit, il ne faut pas totalement exclure les SSD de l'équation. Parfois, il est préférable de combiner HDD et SSD pour bénéficier de meilleures performances. Par exemple, si vous utilisez fréquemment un service, vous pouvez stocker les données sur un disque dur tout en mettant en cache l'information sur un SSD pour accélérer les prochaines requêtes.

Dans la grande majorité des cas, il n'est pas nécessaire de *tout* stocker sur des SSD.

## Les composants nécessaires

### CPU (Processeur)

Le processeur est l'élément le plus important d'un serveur. C'est lui qui va déterminer la puissance du système, mais aussi celui qui consommera le plus d'énergie, il est donc crucial de bien le choisir pour éviter les mauvaises surprises.

Les processeurs disposent d'un système de C-States. Cette échelle, allant de C0 à C10, définit l'état de fonctionnement du CPU. Plus on va vers le C10, plus certaines parties du processeur seront "éteintes" et donc consommeront moins. C'est probablement l'un des critères les plus importants à prendre en compte lors de l'achat d'un CPU.

Nous avons donc deux choix principaux : Intel ou AMD.

Théoriquement, les processeurs des deux marques sont capables de supporter les C-States. Cependant, c'est connu dans le milieu du self-hosting qu'AMD a encore quelques difficultés sur ce point. Leurs processeurs sont globalement moins performants en matière de gestion de la consommation énergétique. Ils ont des problèmes pour gérer correctement les C-States, ce qui peut entraîner des bugs comme des freezes, etc. En général, les processeurs AMD sont donc moins recommandés pour des serveurs (je précise ça en ayant un serveur avec un Ryzen qui n'a jamais rencontré de problème et qui consomme peu).

De son côté, Intel est plutôt performant sur ce point. Depuis la 13ème génération, leurs processeurs sont puissants, consomment peu d'énergie et gèrent très bien les C-States.

En résumé, je recommande majoritairement les processeurs Intel pour les NAS.

### RAM

Pour la RAM, il convient de se référer aux explications dans la partie sur l'[ECC](#ecc), mais il faut également prendre en compte les exigences de l'OS que vous allez utiliser. Par exemple, si vous souhaitez utiliser TrueNAS, ils recommandent au minimum 8Go de RAM. En général, on recommande *au minimum* 16Go, voire 32Go de RAM, afin de disposer de suffisamment de marge.

Concernant le choix entre DDR4 et DDR5, ça dépend des prix et des disponibilités sur le marché. Si vous avez les moyens d'opter pour de la DDR5, privilégiez-la à la DDR4. Ça permettra de préparer votre serveur pour l'avenir parce que la DDR4 est vouée à disparaître.

### Carte mère

Ce que nous recherchons en priorité pour les NAS, c'est des cartes mères avec beaucoup de ports SATA pour les HDD/SSD. Je recommande d'en avoir au moins 6 afin de disposer d'une bonne marge de sécurité avant de devoir utiliser une HBA (voir plus bas dans [les composants facultatifs](#les-composants-facultatifs)).

Il est également préférable d'avoir au moins un emplacement pour un SSD NVMe (pour l'OS et/ou pour du cache) et un slot PCIe, au cas où vous souhaiteriez ajouter des extensions (SATA, 10GbE, etc.).

À ces contraintes s'ajoutent évidemment d'autres considérations liées au CPU (chipset, ECC, etc.) et à la RAM (DDR4 ou DDR5, ECC, etc.).

En ce qui concerne les marques, je recommande vivement de choisir parmi les trois suivantes : Asus, AsRock ou MSI. Ce sont les cartes avec lesquelles j'ai rencontré le moins de problèmes, et celles offrant les meilleurs BIOS à mes yeux.

### HDD et SSD

#### HDD

Afin de choisir le nombre de disques et la manière dont vous allez stocker vos données, référez-vous à [cette documentation](storage.md). Je vous recommande également de consulter [cette page](https://www.raidz-calculator.com/raidz-types-reference.aspx) et [celle-ci](https://www.raidz-calculator.com/Default.aspx) pour vous aider à faciliter votre choix.

Les HDD de serveur ne sont pas les mêmes que ceux destinés aux PC. En général, les marques spécifient quels modèles sont conçus pour les serveurs. Par exemple, Seagate a sa gamme IronWolf, Western Digital sa gamme Red, etc. En résumé, renseignez-vous bien avant de choisir ceux qui sont adaptés aux serveurs.

Ensuite, il vous faudra faire un choix entre les disques 5400 rpm et 7200 rpm. Ce choix aura un impact sur la consommation énergétique, la vitesse d'accès aux données (voir [les optimisations logiciels](les-optimisations-logiciels)) et le bruit généré. Les disques 7200rpm consomment plus d'énergie que les 5400rpm, sauf en mode veille où leur consommation est équivalente. Pour une utilisation normale, la différence entre des disques 5400rpm et 7200rpm n'affectera pas de manière significative les performances de votre serveur.

Concernant les marques à privilégier, je recommande principalement Seagate et Western Digital. Je vous conseille toutefois de faire vos recherches sur les marques avant d'acheter pour vous assurer que ces marques sont toujours aussi fiables. Vous pouvez également utiliser [ce site](https://diskprices.com/) pour trouver le meilleur rapport TB/euro.

<span style="color:#ff7b7b">**SI VOUS COMPTEZ UTILISER ZFS, NE PRENEZ ABSOLUMENT PAS DE DISQUES SMR, ILS FONT VRAIMENT DE LA MERDE AVEC ZFS**</span>

#### SSD

Il est recommandé d'installer votre OS sur un SSD. Dans ce cas, il est préférable d'utiliser un modèle NVMe.

Concernant l'utilisation des SSD en tant que cache, tant les NVMe que les SSD SATA feront très bien l'affaire.

Pour les SSD NVMe, je recommande principalement Samsung, qui est vraiment la marque offrant les meilleures performances sur le long terme.

Pour les SSD SATA, je suis personnellement fan des Crucial MX500. Il existe également de nombreuses marques no-name qui proposent de bons SSD, n'hésitez pas à faire vos recherches.


### Case

Pour les boîtiers, ça dépend de ce que vous recherchez. Vous avez deux choix : moyen ou grand.

Si vous souhaitez un boîtier qui ne prenne pas trop de place, il faudra chercher un peu plus. Il y a malheureusement peu de choix sur le marché pour des boîtiers de taille moyenne capables de contenir au moins 6 disques. Parmi les marques connues, voici quelques modèles à considérer :
- [Fractal Node 304](https://www.fractal-design.com/products/cases/node/node-304/black/) : 6 emplacements de disques (interne), support mini-ITX et mini-DTX, alimentation ATX
- [Silverstone DS380](https://www.silverstonetek.com/fr/product/info/server-nas/DS380/)  : 8 emplacements de disques, support mini-DTX et mini-ITX, alimentation SFX ou SFX-L
- [Silverstone CS381](https://www.silverstonetek.com/fr/product/info/server-nas/CS381/)  : 8 emplacements de disques, support micro-ATX, mini-DTX et mini-ITX, alimentation SFX ou SFX-L

Pour les marques moins connues, il existe les cases suivantes :
- [Jonsbo N3](https://www.jonsbo.com/en/products/N3.html) : 8 emplacements de disques, support mini-ITX, alimentation SFX | <span style="color:#dadada">*Attention : cette case est reconnue pour avoir des problèmes d'aération pour les HDD, il existe un fix à l'aide d'une imprimante 3D*</span>
- [Jonsbo N4](https://www.jonsbo.com/en/products/N6Black.html) : 9 emplacements de disques, support mini-ITX et mini-ATX, alimentation ATX ou SFX | <span style="color:#dadada">*Attention : quand j'écris ces lignes, la case n'est pas encore sortie mais elle est prometteuse. à voir avec les reviews*</span>
- [Sagittarius](https://fr.aliexpress.com/item/1005009727153277.html) : 8 emplacements de disques, support micro-ATX et mini-ITX, alimentation ATX

Si vous avez la place d'avoir un grand boitier, il y a beaucoup plus de choix :
- [Fractal Node 804](https://www.fractal-design.com/products/cases/node/node-804/Black/) : 8 emplacements de disques (interne), support mini-ITX et micro-ATX, alimentation ATX
- [Fractal Define R5](https://www.fractal-design.com/products/cases/define/define-r5/black/) : 8 emplacements de disques (interne), support ATX, micro-ATX et mini-ITX, alimentation ATX
- [Silverstone CS382](https://www.silverstonetek.com/en/product/info/server-nas/cs382/) : 8 emplacements de disques, support micro-ATX, mini-DTX et mini-ITX, alimentation ATX
- [Silverstone CS383](https://www.silverstonetek.com/en/product/info/server-nas/cs383/) : 8 emplacements de disques, support SSI-EEB, SSI-CEB, Extended ATX, ATX, micro-ATX et Mini-ITX, alimentation ATX


Je ne cite que ces modèles, mais il existe vraiment énormément de boîtiers capables d'accueillir au moins 8 disques. N'hésitez pas à consulter [partpicker](https://pcpartpicker.com/products/case) et à filtrer en fonction de vos besoins.

Il faut également prendre en compte que dans des boîtiers plus compacts, les composants auront plus de mal à évacuer la chaleur. Ça veut pas dire que c'est une mauvaise option, loin de là, mais il faut en être conscient.

### PSU (Alimentation)

L'alimentation est sans doute le point le plus important pour créer un serveur qui consomme peu d'électricité. Par défaut, toutes les alimentations ne sont pas optimisées pour fournir une faible puissance. La raison en est que le courant dans nos murs est alternatif (AC), tandis que celui utilisé par un PC est continu (DC). Le rôle de l'alimentation est donc de convertir l'AC en DC. Par conséquent, les alimentations ont un rendement qui suit généralement une courbe logarithmique. La majorité des alimentations auront un rendement assez médiocre lorsqu'il s'agit de fournir une faible puissance (<40W) en continu.

Pour mieux comprendre le principe de rendement, prenons un exemple (en utilisant un prix de l'électricité moyen de 0,17317€/kWh) :

Imaginons une alimentation avec un rendement de 60 % à 40W. Elle devra tirer 56W (40 % de plus) pour pouvoir fournir 40W en continu. Les 16W supplémentaires seront alors perdus sous forme de chaleur. En comparaison, si nous prenons une alimentation avec un rendement de 80 % à 40W, elle devra tirer 8W en plus. Ça peut sembler peu, mais si l'on compare sur une année (en supposant un serveur allumé 7j/7, 24h/24, consommant constamment 40W), voici les valeurs obtenues :
- l'alimentation avec un rendement de 60 % consommerait 490,56 kWh par an (soit 84,95 €).
- l'alimentation avec un rendement de 80 % consommerait 420,48 kWh par an (soit 73,06 €).

Pour trouver les informations nécessaires pour faire votre choix, les rapports réalisés par [Cybenetics](https://www.cybenetics.com/index.php?option=psu-performance-database) sont une bonne référence. Les rapports des deux dernières années ont été compilés dans [cet excel](https://docs.google.com/spreadsheets/d/1TnPx1h-nUKgq3MFzwl-OOIsuX_JSIurIq3JkFZVMUas/edit?gid=110239702#gid=110239702). Ce dernier n'est pas à jour, mais il peut néanmoins être utile.

Globalement, je ne recommande pas de modèle en particulier, mais il est important de choisir une alimentation ayant un rendement d'au moins 80% pour 20W. Ça limite un peu les choix, mais c'est tout à fait trouvable.

Si vous voulez faire vos calculs je vous met la formule ici :

`E = P x t / 1000`
- `E` étant des kWh/jour
- `P` étant des W
- `t` étant un nombre d'heure par jour 

Vous pouvez aussi vous aider de [ce calculateur](https://www.rapidtables.com/calc/electric/energy-consumption-calculator.html) si vous avez la flemme de le faire vous même.

## Les composants facultatifs

### Les cartes PCIe (HBA, 10GbE, GPU, etc.)

Si vous souhaitez ajouter plus de ports SATA, un port 10GbE ou une carte pour l'encodage, vous aurez besoin d'un slot PCIe sur votre carte mère. Cependant, ces cartes consomment forcément plus d'électricité et, surtout, elles peuvent empêcher votre processeur de passer dans des C-States plus profonds. Il est important de choisir une carte qui supporte l'Active State Power Management (ASPM).

Le problème, c'est que même si certaines cartes indiquent qu'elles supportent l'ASPM, elles le font mal et ne permettent pas de dépasser certains C-States avancés. Si vous avez besoin d'une carte de ce type, je vous conseille de bien vous renseigner sur la documentation et de consulter des forums pour vérifier si l'ASPM est correctement géré.

## Les optimisations logiciels

### Spin down des disques

Les HDD stockent les données en les inscrivant sur des disques physiques qui tournent. Le moteur qui fait tourner ces disques consomme évidemment de l'électricité. Il est possible de spin-down ces disques, c'est-à-dire de continuer à les alimenter tout en arrêtant le moteur lorsqu'ils ne sont pas utilisés. Cette méthode aura un impact sur les performances du serveur, car il y aura un léger délai avant de pouvoir accéder aux données après avoir effectué un spin-down. Ce délai est plus court si vous utilisez des disques 7200rpm.

### C-States

Comme mentionné précédemment, les processeurs possèdent un système de C-States. Cette échelle, allant de C0 à C10, définit l'état de fonctionnement du CPU : plus on va vers le C10, plus certaines parties du CPU seront "éteintes" et consommeront moins d'énergie. Ces états de fonctionnement sont également liés à l'utilisation des autres composants du serveur d'où l'existence de l'ASPM, etc.

Afin d'optimiser votre serveur, vérifiez que l'ASPM est bien activé pour vos composants (ce n'est pas toujours le cas par défaut). Ensuite, installez et utilisez Powertop pour optimiser toute votre configuration. Si vous n'avez pas envie de faire ça manuellement, il vous suffit de taper `powertop --auto-tune`, et Powertop se chargera des réglages automatiquement (l'auto-tune n'est pas forcément la configuration la plus optimisée pour votre serveur. Si vous souhaitez optimiser pleinement votre système, renseignez-vous sur [la doc de powertop](https://github.com/fenrus75/powertop)).

Je précise que bien que Powertop permette d'économiser quelques watts, ce n'est pas la solution ultime qui fera passer un serveur de 100W à 10W. Bien qu'il existe des optimisations logicielles, la meilleure optimisation reste de bien choisir ses composants dès le départ.
