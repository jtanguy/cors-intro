---
layout: ribbon

style: |
    .slide:after {
        display: none;
    }
    #Cover h2 {
        color:#FFF;
        text-align:center;
        font-size:70px;
        }
    #Cover p {
        margin:10px 0 0;
        text-align:center;
        color:#FFF;
        font-style:italic;
        font-size:20px;
        }
        #Cover p a {
            color:#FFF;
            }
    .small {
        font-size: 0.8em;
        line-height: 35px;
    }
    .slide.cover {
        background-color: #fff;
    }
    .slide pre code {
        line-height: 30px;
    }
    #Picture h2 {
        color:#FFF;
        }
    #SeeMore h2 {
        font-size:100px
        }
    #SeeMore img {
        width:0.72em;
        height:0.72em;
        }
---

# Introduction à CORS {#Cover}

![](pictures/cover.jpg)
<!-- wallpaper from xtremewalls.com -->


## **Cross-Origin Resource Sharing**

## **Pourquoi ?**

## Partager des ressources entre différents domaines

- Médias: images, vidéos, ~~flash~~ ;
- Styles ;
- _iframes_ ;
- ...fonts ;
- ..._ajax_.


## Content Security Policies

- Same origin
    - example.com -> example.com
    - sub.example.com -> example.com (document.domain = example.com)

- Cross origin

{:.note}
Si on modifie document.domain, il faut le faire sur **toutes** les pages (sub.example.com _et_ example.com)

## CORS

- Configuré par le propriétaire des ressources
- Restreint les requêtes en fonction
    - du domaine
    - des _headers_

## **#NoCORS**

## Requêtes CORS simples
{:.small}

- GET / HEAD / POST
- Headers spécifiés par le navigateur
- Headers spécifiés manuellement:
    - Accept
    - Accept-Language
    - Content-Language
    - Content-Type:
        - application/x-www-form-urlencoded
        - multipart/form-data
        - text/plain

## Exemple JS

~~~javascript
fetch(`https://example.com/resource`, {
    method: 'GET',
    headers: { 'Accept': 'application/json' },
}).then(res => {
    if (res.ok) {
        return res.json()
    } else {
        return Promise.reject(res.statusText)
    }
}).then(console.log)
.catch(console.error)
~~~

## **Preflight**

## Requêtes nécessitant un _preflight_

Tout ce qui n'est pas une requête simple

- PUT
- DELETE
- Headers custom
- application/json

## Exemple JS
{:.small}

~~~javascript
fetch(`https://example.com/resource`, {
    method: 'POST',
    headers: { 'Accept': 'application/json',
               'Content-Type': 'application/json' },
    body: { id: '9408e0e9-9dcc-46a1-a75b-be553e39da15' }
}).then(res => {
    if (res.ok) {
        return res.json()
    } else {
        return Promise.reject(res.statusText)
    }
}).then(console.log)
.catch(console.error)
~~~

## Requête _preflight_

~~~http
OPTIONS /resource HTTP/1.1
Host: example.com
User-Agent: ...
Accept: ...
Connection: ...
Origin: https://my.domain.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type
~~~

## Réponse _preflight_

~~~http
HTTP/1.1 200 OK
Date: ...
Server: ...
Access-Control-Allow-Origin: https://my.domain.com
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers: Content-Type
Access-Control-Max-Age: 86400
Content-Encoding: ...
Content-Length: ...
Keep-Alive: ...
Connection: ...
Content-Type: ...
~~~

## Requête

~~~http
POST /resource HTTP/1.1
Host: example.com
User-Agent: ...
Accept: ...
Content-Type: application/json
Referer: ...
Content-Length: ...
Origin: https://my.domain.com

{ "id": "9408e0e9-9dcc-46a1-a75b-be553e39da15" }
~~~

## Réponse
~~~http
HTTP/1.1 200 OK
Date: ...
Server: ...
Access-Control-Allow-Origin: https://my.domain.com
Content-Encoding: ..
Content-Length: ...
Keep-Alive: ...
Connection: ...
Content-Type: ...
~~~


## **_Credentials_**

## Credentials ?

- GET avec cookies
- Ne nécessite pas de _preflight_
- Est rejeté par le navigateur si CORS mal configuré

Exemple de mauvaise config:

~~~http
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true
~~~

## Exemple JS

~~~javascript
fetch(`https://example.com/resource`, {
    method: 'GET',
    credentials: 'include',
    headers: { 'Accept': 'application/json' },
}).then(res => {
    if (res.ok) {
        return res.json()
    } else {
        return Promise.reject(res.statusText)
    }
}).then(console.log)
.catch(console.error)
~~~

## Requête

~~~http
GET /resource HTTP/1.1
Host: example.com
User-Agent: ..
Accept: ..
Origin: http://my.domain.com
Cookie: session_id=2
~~~

## Réponse

~~~http
HTTP/1.1 200 OK
Date: ...
Server: ...
Access-Control-Allow-Origin: http://my.domain.com
Access-Control-Allow-Credentials: true
Set-Cookie: session_id=2; expires=...
Content-Encoding: ...
Content-Length: ...
Content-Type: ...
~~~

## **Récap**

## ![](https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Flowchart_showing_Simple_and_Preflight_XHR.svg/2000px-Flowchart_showing_Simple_and_Preflight_XHR.svg.png)
{:.cover .w}

## Côté serveur

- Bibliothèques CORS
- Configurer `Access-Control-Content-Origin`
    - De préférence en listant les domaines
    - `*` si api publique
        - Pas de `Access-Control-Allow-Credentials`


## References

- https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS
- https://jtanguy.github.io/cors-intro

