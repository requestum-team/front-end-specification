## Angular cheat sheets

### Independent app config

Создание независимого файла конфигурации для Angular приложения необходимо чтобы отвязаться от 
процесса билда в случае когда надо деплоить код на различные инстансы с различными переменными 
окружения - например: ссылки на API, токены для карт и т.д.

Для реализации такого подхода необходимо создать файл `config.json` и положить его в директорию 
`src/assets` вашего проекта.

Чтобы получить переменные из файла конфигурации перед загрузкой приложения 
сначала нужно загрузить файл конфигурации, а уже затем инициализировать приложение.
Поскольку `platformBrowserDynamic` принимает провайдеров, есть
возможность определить конфигурацию здесь.

Для начала нам необходимо создать `InjectionToken` в файле с константами. Он будет использоваться 
нами для того чтобы получать доступ к содержимому файла конфигурации в нашем приложении. 

```typescript
export const APP_CONFIG = new InjectionToken<string>('APP_CONFIG');
```

Затем нам необходимо загрузить файл конфигурации через `Fetch API` - так мы можем точно знать, 
что файл конфигурации будет загружен до того, как мы инициализируем приложение. Наш токен 
APP_CONFIG используется здесь для хранения переменных конфигурации.

```typescript
fetch('/assets/config.json')
  .then((response: Response) => response.json())
  .then((config: object) => {
    if (environment.production) {
      enableProdMode();
    }

    platformBrowserDynamic([{ provide: APP_CONFIG, useValue: config }])
      .bootstrapModule(AppModule)
      .catch(err => console.error(err));
  });
```

Благодаря этой конструкции мы можем получить доступ содержимому файла `config.json` в любом месте 
нашего приложения просто используя `@Inject`, например:

```typescript
constructor(@Inject(APP_CONFIG) private config) {
  console.log(this.config) // Выведет содержимое config.json
}
```

Для использования в описании модулей, например `@agm` - нужно задействовать провайдеры:

```typescript
  @ngModule({
    imports: [
      AgmCoreModule.forRoot()
    ],
    providers: [
      {
        provide: LAZY_MAPS_API_CONFIG,
        useFactory: config => ({ apiKey: config.googleApiKey }),
        deps: [APP_CONFIG]
      },
    ]
  })
```

При всём этом сам файл конфигурации после сборки находится в директории `assets`, благодаря чего при 
его редактировании приложение будет подхватывать новые переменные без пересборки проекта.

### TypeScript

Создание псевдонимов для наших папок приложений и сред позволит нам осуществлять чистый импорт,
который будет согласован с нашим приложением. Рассмотрим гипотетическую, но обычную ситуацию.
Мы работаем над компонентом, который расположен в трех папках глубоко в компоненте A, и мы хотим 
импортировать сервис из ядра, которое расположено в две папки. Это приведет к тому, что оператор
`import` будет выглядеть примерно так: 

```typescript
  import { SomeService } from '../../../core/subpackage1/subpackage2/some.service';
```

И что еще хуже, каждый раз, когда мы хотим изменить расположение любого из этих двух файлов, 
наш оператор импорта будет нарушен. Сравните это с гораздо более коротким импортом

```typescript
  import { SomeService } from "@app/core"
```

Выглядит лучше, не правда ли?

Чтобы использовать псевдонимы, мы должны добавить свойства `baseUrl` и `paths` в наш файл
`tsconfig.json` следующим образом:

```json
{
  "compilerOptions": {
    "...": "убрано для краткости",

    "baseUrl": "./",
    "paths": {
      "@app/*": ["src/app/*"],
      "@misc/*": ["src/app/misc/*"],
      "@env/*": ["src/environments/*"],
      "@shared/*": ["src/app/shared/*"],
      "@views/*": ["src/app/views/*"],
      "@pipes/*": ["src/app/pipes/*"],
      "@models/*": ["src/app/models/*"],
      "@directives/*": ["src/app/directives/*"],
      "@interceptors/*": ["src/app/interceptors/*"],
      "@services/*": ["src/app/services/*"],
      "@components/*": ["src/app/components/*"],
      "@modules/*": ["src/app/modules/*"]
    }
  }
}
```

### SCSS

Sass является выбором по умолчанию для большинства проектов.
Одной вещью, которую Angular не добавляет по умолчанию, является `stylePreprocessorOptions` с
`includePaths`. используя их мы можем указать где SCSS должен искать файлы в первую очередь.
Так же, для того чтобы это работало, необходимо указать `sourceRoot`.
Например:
```json
{
  "...": "убрано для краткости",

  "projects": {
    "project-name": {
      "...": "убрано для краткости",

      "sourceRoot": "src",
      "architect": {
        "build": {
          "options": {
            "...": "убрано для краткости",

            "stylePreprocessorOptions": {
              "includePaths": ["src/scss"]
            }
          }
        },
        "test": {
          "options": {
            "...": "убрано для краткости",

            "stylePreprocessorOptions": {
              "includePaths": ["src/scss"]
            }
          }
        }
      }
    }
  }
}
```

###  404 on refresh page

Так как приложение написанное на Angular является [SPA](https://en.wikipedia.org/wiki/Single-page_application), то после того как приложение будет вылито на сервер, может возникнуть ситуация когда 
при открытии/перезагрузке страницы, вместо нужного роута открывается страница с примерно таким сообщением `404 - Not Found`. В такой ситуации
необходимо настроить перенаправление на `index.html` на вашем сервере.

Для **Apache** необходим файл `.htaccess` со следующим содержимым:

```apacheconfig
DirectoryIndex index.html

<IfModule mod_rewrite.c>
  RewriteEngine On

  RewriteRule ^index\.html$ - [L,NC]

  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteRule . index.html [L]
</IfModule>
```

Для удобства можно положить данный файл в папку `src` вашего проекта, и указать путь к нему в поле `assets` в `angular.json`:

```json
{
  "...": "убрано для краткости",

  "projects": {
    "project-name": {
      "...": "убрано для краткости",

      "sourceRoot": "src",
      "architect": {
        "build": {
          "options": {
            "...": "убрано для краткости",
            
            "assets": ["src/.htaccess", "..."]
          }
        },
        "test": {
          "options": {
            "...": "убрано для краткости",
                                                     
            "assets": ["src/.htaccess", "..."]
          }
        }
      }
    }
  }
}
```

Благодаря этому файл `.htaccess` будет попадать в папку `dist` при сборке проекта.
