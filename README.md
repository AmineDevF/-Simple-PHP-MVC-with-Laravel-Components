# Simple PHP MVC with Laravel Components

## Prérequis
Assurez-vous d'avoir installé les éléments suivants :

- [Composer](https://getcomposer.org/download/)
- [PHP 8.x](https://www.php.net/downloads)
- Serveur local comme Laragon, XAMPP ou WAMP

---

## Installation du Projet

### 1. Cloner le Projet

```bash
git clone https://github.com/votre-projet/php-mvc-tutorial.git
cd php-mvc-tutorial
```

### 2. Installer les Dépendances

```bash
composer install
```

### 3. Configuration de l'Environnement
Créez un fichier `.env` à la racine et configurez les variables suivantes :

```
APP_ENV=local
APP_DEBUG=true
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nom_base_donnees
DB_USERNAME=utilisateur
DB_PASSWORD=mot_de_passe
```

### 4. Configuration du Serveur Web
Ajoutez un fichier `.htaccess` dans le dossier `public/` pour rediriger toutes les requêtes vers `index.php`.

#### `.htaccess`
```apache
Options -Indexes
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule ^ index.php [QSA,L]
```

### 5. Lancer le Serveur

```bash
php -S localhost:8000 -t public
```
Accédez à [http://localhost:8000](http://localhost:8000).

---

## Architecture du Projet
```
|-- app/
|   |-- Controllers/
|       |-- HomeController.php
|   |-- Models/
|       |-- ExampleModel.php
|-- public/
|   |-- .htaccess
|   |-- index.php
|-- routes/
|   |-- web.php
|-- views/
|   |-- home.twig
|-- composer.json
|-- bootstrap.php
```

- `app/Controllers/`: Contient les contrôleurs
- `app/Models/`: Contient les modèles
- `public/`: Fichiers publics et point d'entrée du projet
- `routes/web.php`: Déclaration des routes
- `views/`: Modèles Twig pour l'affichage

---

## Exemple de Contrôleur
Voici un exemple de `HomeController.php` :

```php
<?php
namespace App\Controllers;

use Illuminate\Routing\Controller;
use App\Models\ExampleModel;

class HomeController extends Controller
{
    public function index()
    {
        $model = new ExampleModel();
        $data = $model->getData();
        echo "Données : " . json_encode($data);
    }
}
```

## Exemple de Modèle
Voici un exemple de `ExampleModel.php` :

```php
<?php
namespace App\Models;

class ExampleModel
{
    public function getData()
    {
        return ["message" => "Bienvenue dans notre modèle simple !"];
    }
}
```

## Exemple de Route
Fichier `routes/web.php` :

```php
<?php
use App\Controllers\HomeController;

$router->get('/', [HomeController::class, 'index']);
```

## Point d'Entrée `index.php`

```php
<?php
require __DIR__ . '/../bootstrap.php';

use Illuminate\Events\Dispatcher;
use Illuminate\Routing\Router;
use Illuminate\Http\Request;

$events = new Dispatcher;
$router = new Router($events);

require_once __DIR__ . '/../routes/web.php';

$request = Request::capture();
$response = $router->dispatch($request);
$response->send();
```

## Conclusion
Avec cette base, vos apprenants peuvent étendre leur projet en ajoutant des routes, contrôleurs, modèles et vues dynamiques. Si vous avez besoin d'autres fonctionnalités, faites-le-moi savoir !
