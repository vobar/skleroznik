#### При настройке WS на серваке может немного задёргаться глаз. Или оба.

Error curl: (35) error:1408F10B:SSL routines:ssl3_get_record:wrong version number

<details>
  <summary>1. файл hosts, если за NAT'ом возможно потребуется прописать ссылку на ВНУТРЕННИЙ ip сервака, если он себя не находит</summary>
 
```
openssl s_client -connect your.domain.tld:443

даёт:
140439556511040:error:0200206F:system library:connect:Connection refused:../crypto/bio/b_sock2.c:110:
140439556511040:error:2008A067:BIO routines:BIO_connect:connect error:../crypto/bio/b_sock2.c:111:
connect:errno=111

или
curl -sS http://your.domain.tld:443 --verbose

wrong version number

даёт:
*   Trying [ВНЕШНИЙ!IP]:443...
* TCP_NODELAY set
* Connected to your.domain.tld ([ВНЕШНИЙ!IP]) port 443 (#0)
* ALPN, offering h2
* ALPN, offering http/1.1
* successfully set certificate verify locations:
*   CAfile: /etc/ssl/certs/ca-certificates.crt
  CApath: /etc/ssl/certs
* TLSv1.3 (OUT), TLS handshake, Client hello (1):
* error:1408F10B:SSL routines:ssl3_get_record:wrong version number
* Closing connection 0
curl: (35) error:1408F10B:SSL routines:ssl3_get_record:wrong version number

```
  
</details>

2. в конфе NGINX переставил default_server на 443 порт + http2 влепил, не знаю, на сколько надо, тестить не буду (2 суток наслаждался)

<details>
  <summary>3. .env</summary>
  
```
BROADCAST_DRIVER=pusher
PUSHER_APP_ID="your-best-ws-app"
PUSHER_APP_KEY="your-best-ws-app-key"
PUSHER_APP_SECRET="your-best-ws-app-secret"
PUSHER_HOST="your.domain.tld"
PUSHER_PORT=6001
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1
LARAVEL_WEBSOCKETS_PORT="${PUSHER_PORT}"

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"

LARAVEL_WEBSOCKETS_SSL_LOCAL_CERT="/etc/letsencrypt/live/your.domain.tld/fullchain.pem"
LARAVEL_WEBSOCKETS_SSL_LOCAL_PK="/etc/letsencrypt/live/your.domain.tld/privkey.pem"
LARAVEL_WEBSOCKETS_SSL_PASSPHRASE=""

```

</details>


<details>
  <summary>4. broadcasting.php</summary>
  
```

'pusher' => [
    'driver' => 'pusher',
    'key' => env('PUSHER_APP_KEY'),
    'secret' => env('PUSHER_APP_SECRET'),
    'app_id' => env('PUSHER_APP_ID'),
    'options' => [
        'cluster' => env('PUSHER_APP_CLUSTER'),
        'host' => env('PUSHER_HOST'),
        'port' => env('PUSHER_PORT'),
        'encrypted' => true,
        'scheme' => env('PUSHER_SCHEME'),
        'useTLS' => env('PUSHER_SCHEME', 'https') === 'https',
    ],
    'client_options' => [
        // Guzzle client options: https://docs.guzzlephp.org/en/stable/request-options.html
    ],
],

```

</details>

<details>
  <summary>5. *.vue </summary>
  
```
  
window.Echo = new Echo({
    broadcaster: 'pusher',
    key: import.meta.env.VITE_PUSHER_APP_KEY,
    cluster: import.meta.env.VITE_PUSHER_APP_CLUSTER,
    wsHost: window.location.hostname,
    wssPort: 6002, <--- ОБРАЩАЕМ ВНИМАНИЕ! В коде из .env не тянется чяднт?
    forceTLS: true,
    disableStats: true
});

```

</details>
