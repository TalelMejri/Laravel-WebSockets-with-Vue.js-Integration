# Laravel WebSockets with Vue.js Integration

This guide provides step-by-step instructions on how to integrate Laravel WebSockets with Vue.js for real-time communication. Follow these steps to set up the environment and start using WebSockets in your Laravel application.

## Laravel WebSockets Installation and Test

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

8. Create a new event:

    ```bash
    php artisan make:event NotifEvent
    ```

    Update `NotifEvent.php` to implement `ShouldBroadcastNow`:

    ```php
    use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;
    use Illuminate\Queue\SerializesModels;

    class NotifEvent implements ShouldBroadcastNow
    {
        use SerializesModels;

        private $notif;

        public function __construct($notif)
        {
            $this->notif = $notif;
        }

        public function broadcastWith()
        {
            return ['message' => $this->notif];
        }

        public function broadcastOn()
        {
            return new Channel('public');
        }
    }
    ```

9. Broadcast the event in your controller:

    In your controller where you want to broadcast the event:

    ```php
    use App\Events\NotifEvent;

    // Your code logic here

    broadcast(new NotifEvent("test"));
    ```

10. Run the WebSockets server:

    ```bash
    php artisan websockets:serve
    ```

11. Open the Laravel WebSockets dashboard in your browser:

    ```bash
    http://localhost:8000/laravel-websockets 
    ```

    ![After Connect](after_connect.png)
    ![Before Connect](before_connect.png)

12. To verify WebSocket communication, create a test route:

    In your `routes/web.php` file:

    ```php
    use App\Events\NotifEvent;
    
    Route::get('/broadcast', function () {
        broadcast(new NotifEvent("test"));
        return "Event has been sent!";
    });
    ```

    ![Check Event Work](check_event_work.png)

## Vue.js Integration

1. Install required packages:

    ```bash
    npm install laravel-echo pusher-js
    ```

2. Update your 'main.js' file:

    ```javascript
    import Echo from "laravel-echo"
    window.Pusher = require('pusher-js');

    window.Echo = new Echo({
        broadcaster: 'pusher',
        key: "local",
        wsHost: "127.0.0.1",
        wsPort: 6001,
        cluster: "mt1",
        forceTLS: false,
        disableStats: true,
    });
    ```

3. Usage in Vue Component:

    Listen for the 'NotifEvent' event on the 'public' channel.

    ```javascript
    import NotifEvent from '../../events/NotifEvent'; // Update the path accordingly

    window.Echo.channel('public').listen(NotifEvent.event, (e) => {
        // Your code logic here
        // Handle the received notification to get new notifications in real-time.
    });
    ```

Feel free to customize and expand on each step based on your project's specific requirements.
