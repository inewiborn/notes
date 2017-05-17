#### убрать index.php из url

```apache
RewriteCond %{REQUEST_URI} /index\.php$
RewriteRule (.*)index\.php$ /$1 [R=301,L]
```

#### добавить www

```apache
RewriteCond %{HTTP_HOST}   !^$
RewriteCond %{HTTP_HOST}   !^www\.          [NC]
RewriteCond %{HTTP_HOST}   (.+)$
RewriteRule ^(.*)          http://www.%1/$1 [L,R=301]
```

#### Убрать www

```apache
RewriteCond %{HTTP_HOST}   ^www\.(.*)          [NC]
RewriteRule ^(.*)          http://%1/$1 [L,R=301]
```

#### Добавить в конец /

```apache
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_URI} !(.*)/$
RewriteRule ^(.*[^/])$ $1/ [L,R=301]
```
