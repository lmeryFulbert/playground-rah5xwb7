# Débutter en PHP !

	* 	Script d'initiation à PHP CRUD
	*	C --> Création --> Insert
	*	R --> Read --> SELECT
	*	U --> Update
	*	D --> Delete
	*
	*	Pour toi Public de SIO 1  !!!
	*
	*	Penser à avoir votre serveur web apache démarré qui écoute sur le port 80
	*	Penser à avoir votre Serveur MySQL démarré qui écoute localement (127.0.0.1) sur le port 3306
	*	Placer ce fichier dans un dossier correspondant à votre projet (mkdir debutterphp)
	*	Placer ce dossier dans le repertoire de publication d'apache (C:\wamp\www) pour ceux qui bossent sous wamp.
	*	Créer la base de données "debutterphp" ou avec un autre nom mais adapter votre code sous MySQL (perso j'utilise phpmyadmin mais vous pouvez faire autrement)
	*	Créer la table "profs" (ou un autre nom mais il faudra adapter le code notamment la requete SQL)
	*	Insérez y des données.
	*	Tester votre code avec un navigateur en faisant http://127.0.0.1/debutterphp
	*	La gestion des noms de domaine de type www.monsite.fr peut se faire si on maitrise DNS et la configuration de VHost sous Apache
	*	Et oui le Systeme (SI1, SI5) ça peut servir !
	
	
	N'oublie pas de virer les commentaires sur les instructions importantes (qui ne peuvent être testés sur cette plateforme, et oui je n'ai pas accès à un serveur MySQL sur tech.io ) !
	
#Fichier commun de connexion au Serveur SQL

```php runnable	
<?php
        /*
            Fichier: connexion.php
        */
        
		//Se connecter au serveur SQL
		$user='root';
		$pass='';
		$connexion = new PDO('mysql:host=127.0.0.1;dbname=debutterphp', $user, $pass, array(PDO::ATTR_ERRMODE => PDO::ERRMODE_WARNING));
		//var_dump($connexion);
?>

```
#Fichier d'accueil : Opération Read : Listing

```php runnable
<?php
        /*
            Fichier: index.php
        */
//Se connecter au serveur SQL
include("connexion.php");
	
	//Toujours debugguer sa requete SQL en la plaçant dans une variable
	$sql="SELECT * from profs";
	//var_dump($sql);
	
	//Exécution de la requete et recupération du jeu d'enregistrement (aussi appelé Curseur)
	$jeu=$connexion->query($sql);
	//var_dump($jeu);
	
?>

<html>
	<h1>Listing des profs</h1>
	<table border="1">
		<tr>
			<th></th>
			<th></th>
			<th>id</th>
			<th>nom</th>
			<th>prenom</th>
		</tr>
		<?php
		//Itération Pour chaque Ligne (Row) du Jeu d'enregistrement($jeu)
		foreach($jeu as $row){?>
		<tr>
			<td><a href="delete.php?id=<?php echo $row["id"] ?>">Supprimer</a></td>
			<td><a href="update.php?id=<?php echo $row["id"] ?>">modifier</a></td>
			<td><?php echo $row["id"] ?></td>
			<td><?php echo $row["nom"]  ?></td>
			<td><?php echo $row["prenom"]  ?></td>
		</tr>
		<?php } ?>
	</table>
	
	<a href="formprofs.php">Créer un prof</a>
</html>
```
#Fichier formprofs : Opération Create : Formulaire de saisie + traitement associé

```php runnable
<?php
        /*
            Fichier: formprofs.php
        */
	if(isset($_POST["bt_submit"])){
		var_dump($_POST);
		
		//Se connecter au serveur SQL
		include("connexion.php");
		
		//Toujours debugguer sa requete SQL en la plaçant dans une variable
		$_id=htmlentities($_POST["txt_id"]);
		$_nom=htmlentities($_POST["txt_nom"]);
		$_prenom=htmlentities($_POST["txt_prenom"]);


		$sql="INSERT INTO profs VALUES (:leid, :lenom, :leprenom)";
		//var_dump($sql);
			
		try{
			$stmt = $connexion->prepare($sql);		
			
			$stmt->bindParam(':leid', $_id);
			$stmt->bindParam(':lenom', $_nom);
			$stmt->bindParam(':leprenom', $_prenom);
			
			$stmt->execute();
			//redirection sur la page d'accueil
			header('Location: index.php'); 
		}
		catch(PDOException $exception){
			var_dump($exception);
		}
	}
	else{			
	?>
	<html>
		<form action="#" method="POST">
		
			<label for="_id">Identifiant</label>
			<input id="_id" type="text" name="txt_id">
			
			<br/>
			
			<label for="_nom">Nom</label>
			<input id="_nom" type="text" name="txt_nom">
			
			<br/>
			
			<label for="_prenom">Prenom</label>
			<input id="_prenom" type="text" name="txt_prenom">
			
			<br/>
			
			<input type="submit" name="bt_submit" value="Valider">
			
		</form>
	</html>
	<?php }?>
```
#Fichier delete.php : Opération Delete 
```php runnable
<?php
        /*
            Fichier: delete.php
        */
	if(isset($_GET["id"])){
		
		//Se connecter au serveur SQL
		include("connexion.php");
		
		$leid=$_GET["id"];
		
		$sql="DELETE FROM profs WHERE (id=$leid)";
		var_dump($sql);
		
		$count = $connexion->exec($sql);
		
		echo "$count enregistrements supprimés";
		echo "<br/>";
		echo "<a href=\"index.php\">retour</a>";
	}
	else{
	    //redirection sur la page d'accueil
		header('Location: index.php'); 
	}
?>
