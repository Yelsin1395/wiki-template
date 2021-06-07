
```
  window.addEventListener('load', function () {
       (function (widgets) {
           let jw = 'juntoz-widget';

           if (!document.getElementById(jw)) {
               let baseUrl = '@_appOptions.Value.JwUrl',
                   head = document.getElementsByTagName('head')[0],
                   script = document.createElement('script');

               script.id = jw;
               script.src = `${baseUrl}widgets?json=${JSON.stringify(widgets)}`;

               head.appendChild(script);
           }
       })({
           targets: ['jw-reviews', 'jw-raiting'],
           components: ['reviews', 'reviews_raiting']
       });
  })
```