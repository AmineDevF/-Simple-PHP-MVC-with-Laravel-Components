# Simple PHP MVC avec Laravel Components

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
|   |-- home.php
|-- composer.json
|-- bootstrap.php
```

- `app/Controllers/`: Contient les contrôleurs
- `app/Models/`: Contient les modèles
- `public/`: Fichiers publics et point d'entrée du projet
- `routes/web.php`: Déclaration des routes
- `views/`: Modèles PHP pour l'affichage

---

## Exemple de Contrôleur
Voici un exemple de `HomeController.php` :

```php
<?php
namespace App\Controllers;

use App\Models\ExampleModel;

class HomeController
{
    public function index()
    {
        $model = new ExampleModel();
        $data = $model->getData();

        // Inclusion de la vue
        return view('home', ['data' => $data]);
    }
}
```

## Fonction Utilitaire `view()`
Ajoutez cette fonction dans `bootstrap.php` :

```php
<?php 
use Dotenv\Dotenv;

require __DIR__ . '/vendor/autoload.php';

use Illuminate\Database\Capsule\Manager as Capsule;

$dotenv = Dotenv::createImmutable(__DIR__);
$dotenv->load();
$capsule = new Capsule;

$capsule->addConnection([
    'driver'    => $_ENV['DB_CONNECTION'],
    'host'      => $_ENV['DB_HOST'],
    'database'  => $_ENV['DB_DATABASE'],
    'username'  => $_ENV['DB_USERNAME'],
    'password'  => $_ENV['DB_PASSWORD'],
    'charset'   => 'utf8',
    'collation' => 'utf8_unicode_ci',
    'prefix'    => '',
]);

$capsule->setAsGlobal();
$capsule->bootEloquent();


function view($view, $data = [])
{
    extract($data);
    require __DIR__ . "/views/{$view}.php";
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

## Exemple de Vue
Voici un exemple de modèle PHP `home.php` :

```php
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page d'accueil</title>
</head>
<body>
    <h1><?php echo htmlspecialchars($data['message']); ?></h1>
</body>
</html>
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

// Create an event dispatcher
$events = new Dispatcher;

// Instantiate the router
$router = new Router($events);

// Load your routes
require_once __DIR__ . '/../routes/web.php';

// Handle the request
$request = Request::capture();
$response = $router->dispatch($request);
$response->send();
```

## Gestion des Routes et Permisssions
Pour gérer des permissions, vous pouvez implémenter une classe Middleware comme suit :

```php
<?php
namespace App\Middleware;

class AuthMiddleware
{
    public function handle($request, $next)
    {
        if (!isset($_SESSION['user'])) {
            header('Location: /login');
            exit;
        }

        return $next($request);
    }
}
```
Pour appliquer ce middleware, ajoutez cette gestion dans `web.php`.

## Conclusion
Avec cette base, vos apprenants peuvent étendre leur projet en ajoutant des routes, contrôleurs, modèles et vues dynamiques en PHP. Si vous avez besoin d'autres fonctionnalités, faites-le-moi savoir !
