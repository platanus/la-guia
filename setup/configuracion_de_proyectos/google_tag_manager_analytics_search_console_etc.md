# Google Tag Manager, Analytics, Search Console, etc.

En esta guía comento como fue el proceso de usar Google Tag Manager, Analytics, Ads y Search console para poder indexar, crear y anuncio y ver métricas de [Bencinas Chile](https://bencinaschile.cl/). Explico algunas configuraciones y también dejo referencias a otros recursos que yo utilicé.

En general dejé algunas fotos, videos y otras referencias (que en mi opinión son concisas) para explicar.

## Luego de tener el dominio, qué hago?

Una vez configurados el nombre de la app y su [dominio](https://www.notion.so/platanus/Saliendo-con-un-producto-a-producci-n-Dominio-Mailing-83d1bc24343f465483bf9f06b4887946), nos gustaría que nuestra página esté indexada en Google, queremos saber que tal le va a la página, poder saber quienes la ven, cómo los usuarios interactúan en la app, e incluso crear anuncios en Google para poder promocionar nuestra aplicación. 

Veamos los pasos que a seguir con detalle:

[Google Tag Manager](google_tag_manager_analytics_search_console_etc/google_tag_manager.md)

[Google Analytics](google_tag_manager_analytics_search_console_etc/google_analytics.md)

[Indexación en Google](google_tag_manager_analytics_search_console_etc/indexacion_en_google.md)

[Google Ads](google_tag_manager_analytics_search_console_etc/google_ads.md)

## Extra: Hotjar

Es posible que queramos saber como interactua el usuario con nuestra aplicación, dónde hace click o como se mueve en la página. Para esto, podemos usar Hotjar. Teniendo Google Tag Manager, la integracion con Hotjar es muy facil, puedes seguir [esta guía](https://help.hotjar.com/hc/en-us/articles/115009499708-Google-Tag-Manager-).

En hotjar, hay 2 secciones de analytics: Heatmaps y Recordings. En los heatmaps puedes ver los clicks y scroll que hacen los usuarios y en los recordings vez literalmente una grabación de la sesión del usuario en tu páginas. Ambos analytics puedes configurarlos para que se activen con distintas urls: puede ser que se activen con cualquier url (es decir, entren a cualquier url de tu dominio, por ejemplo: /login /products?name=producto, etc.) o también puedes ajustarlo para que trackee algunas URLs en específico.
