# Laravel WebSockets with Vue.js Integration

This guide provides step-by-step instructions on how to integrate Laravel WebSockets with Vue.js for real-time communication.
Follow these steps to set up the environment and start using WebSockets in your Laravel application.

## Laravel WebSockets Installation

1. Install the Laravel WebSockets package:

    ```bash
    composer require beyondcode/laravel-websockets
    ```
    
2. Publish migrations and run them:

    ```bash
    php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="migrations"
    php artisan migrate
    ```
3. Publish the WebSocket configuration file:

    ```bash
    php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="config"
    ```
    
4. Uncomment `App\Providers\BroadcastServiceProvider::class` in `config/app.php`.

5. Install Pusher PHP server:

    ```bash
    composer require pusher/pusher-php-server 
    ```
 6. Update your `.env` file:

    ```env
    BROADCAST_DRIVER=pusher

    PUSHER_APP_ID=local
    PUSHER_APP_KEY=local
    PUSHER_APP_SECRET=local
    PUSHER_APP_CLUSTER=mt1
    ```
7. Configure broadcasting in `config/broadcasting.php`:

    ```php
    'pusher' => [
        'driver' => 'pusher',
        'key' => env('PUSHER_APP_KEY'),
        'secret' => env('PUSHER_APP_SECRET'),
        'app_id' => env('PUSHER_APP_ID'),
        'options' => [
            'cluster' => env('PUSHER_APP_CLUSTER'),
            'encrypted' => true,
            'host' => '127.0.0.1',
            'port' => 6001,
            'scheme' => 'http',
        ],
    ],
    ```
8. Create a new event

```bash
php artisan make:event NameEvent
  ```
