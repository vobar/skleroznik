## PWA for laravel

- [Подготовка сервера](https://vite-pwa-org.netlify.app/deployment/)
- [Особливо тут](https://vite-pwa-org.netlify.app/deployment/nginx.html)

- up path for sw.js
```
location /build/sw.js {
    add_header 'Service-Worker-Allowed' '/';
}
```


