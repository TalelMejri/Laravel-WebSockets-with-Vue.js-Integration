# Laravel WebSockets with Vue.js Integration

This guide provides step-by-step instructions on how to integrate Laravel WebSockets with Vue.js for real-time communication.
Follow these steps to set up the environment and start using WebSockets in your Laravel application.

## Laravel WebSockets Installation And Test

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
     php artisan make:event NameEvent
    ```
    ### Replace `Notif` with the name of the event you created (`Notif` in this case). This is how you broadcast the event in your controller to send real-time notifications.
    Update `Notif.php` to implement `ShouldBroadcastNow`:

    ```php
    use Illuminate\Contracts\Broadcasting\ShouldBroadcastNow;
    use Illuminate\Queue\SerializesModels;

    class Notif implements ShouldBroadcastNow
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
    use App\Events\Notif;

    // Your code logic here

    broadcast(new Notif("test"));
    ```

10. Run the WebSockets server:
  ```bash
    php artisan websockets:serve
  ```

11. Open the Laravel WebSockets dashboard in your browser. You can access it at to confirm that a new connection has been established.

  ```bash
   http://localhost:8000/laravel-websockets 
  ```
<div style="display: flex; justify-content: center;">
  <img src="after_connect.png" alt="After Connect" style="border: 1px solid red;" width="500px" >
  <img src="before_connect.png" alt="Before Connect" style="border: 1px solid red;" width="500px" >
</div>
 
12. To verify that WebSocket communication is working, you can perform a simple test by broadcasting an event in your Laravel application.

 Create a test route in your routes/web.php file

  ```php
    use App\Events\Notif;
    
    Route::get('/broadcast', function () {
        broadcast(new Notif("test"));
        return "Event has been sent!";
    });
  ```
 <img src="CheckEvent Work.png" alt=" CheckEvent Work">

## Vue.js Integration

 1. Install required packages:
 
  ```bash
    npm install  laravel-echo pusher-js
  ```
 2. Update your 'main.js' file:
 
 ```javascript
 import Echo from "laravel-echo"
 window.Pusher = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: "local",
    wsHost:"127.0.0.1",
    wsPort: 6001,
    cluster: "mt1",
    forceTLS: false,
    disableStats: true,
});
````
 3. Usage in Vue Component:

    Listen for the 'Notif' event on the 'public' channel.

    ```javascript
    window.Echo.channel('public').listen(Notif, (e) => {
        // Your code logic here
        // Handle the received notification to get new notifications in real-time.
    });
    ```

## Contributing
If you encounter any issues or have suggestions for improvements, please feel free to open an issue or submit a pull request. Your contributions are welcome!

---

**Guide by: Talel Mejri**

*Dedicated to [Mohamed Youssef CHLENDI](https://github.com/youssefchlendi) for continuous support.*
