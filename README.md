# Ngx Translate Kurulumu ve Konfigürasyonu

Bu belge, Ngx Translate paketini kullanarak çok dilli bir Angular uygulaması nasıl oluşturulacağını açıklar.

## Kurulum

### Ngx-translate çekirdek ve HTTP yükleyiciyi kurmak için aşağıdaki komutu kullanın:

```bash
npm i @ngx-translate/core @ngx-translate/http-loader --save
```
Dil Dosyaları
Dil dosyalarını assets/i18n klasörüne yerleştirin.

Türkçe Dil Dosyası (tr.json)
```json
{
  "language": "Türkçe",
  "tr": "Türkçe",
  "en": "İngilizce",
  "ru": "Rusça",
  "title": "Başlık",
  "welcome": "Hoş geldiniz"
}
```

İngilizce Dil Dosyası (en.json)
```json
{
  "language": "English",
  "tr": "Turkish",
  "en": "English",
  "ru": "Russian",
  "title": "Title",
  "welcome": "Welcome"
}
```

Rusça Dil Dosyası (ru.json)
```json
{
  "language": "Русский",
  "tr": "турецкий",
  "en": "Английский",
  "ru": "Русский",
  "title": "Заголовок",
  "welcome": "Добро пожаловать"
}
```

Uygulama Konfigürasyonu
Ngx-translate'in HTTP yükleyicisini ve modülleri konfigüre etmek için app.config.ts dosyasını kullanın.
import { ApplicationConfig, importProvidersFrom } from '@angular/core';
import { provideRouter } from '@angular/router;
import { HttpClient, HttpClientModule } from '@angular/common/http;
import { TranslateHttpLoader } from '@ngx-translate/http-loader;
import { TranslateLoader, TranslateModule } from '@ngx-translate/core;

export function HttpLoaderFactory(http: HttpClient) {
  return new TranslateHttpLoader(http, './assets/i18n/', '.json');
}

export const appConfig: ApplicationConfig = {
  providers: [
    provideRouter(routes),
    importProvidersFrom(
      HttpClientModule,
      TranslateModule.forRoot({
        loader: {
          provide: TranslateLoader,
          useFactory: HttpLoaderFactory,
          deps: [HttpClient],
        },
      }),
    ),
  ],
};

AppComponent Kodları
Angular bileşeni app.component.ts, Ngx-translate'i kullanarak çoklu dil desteğini nasıl sağlayacağını gösterir.
import { Component, inject, OnInit } from '@angular/core;
import { RouterOutlet } from '@angular/router;
import { TranslateModule, TranslateService } from '@ngx-translate/core;

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, TranslateModule],
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css'],
})
export class AppComponent implements OnInit {
  languages = ['tr', 'en', 'ru'];
  private translateService = inject(TranslateService);

  ngOnInit(): void {
    const defaultLang = localStorage.getItem('language') || 'tr';
    this.translateService.setDefaultLang(defaultLang);
    this.translateService.use(defaultLang);
  }

  changeLanguage(lang: string) {
    this.translateService.use(lang);
    localStorage.setItem('language', lang);
  }
}

app.component.html Kodları
Bileşen HTML şablonunun çeviri kullanımını ve dil değiştirme özelliğini gösteren kodlar.
<div>
  <h1>{{ 'welcome' | translate }}</h1>
  <div>
    <button *ngFor="let language of languages" (click)="changeLanguage(language)">
      {{ language | translate }}
    </button>
  </div>
  <router-outlet></router-outlet>
</div>
