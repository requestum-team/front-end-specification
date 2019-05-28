## Angular cheat sheets

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
