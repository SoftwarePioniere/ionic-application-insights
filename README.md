# IONIC 3+ and Microsoft Azure Application Insights implementation

This repository based on the implementation "Angular 5+ and Microsoft Azure Application Insights implementation" by [DevHelp.Online](http://www.DevHelp.Online) and was modified to use the solution with IONIC.
If you need a implementation for Angular 5+ you could find it [here](https://github.com/MarkPieszak/angular-application-insights).


## Installation

Install & save the library to your package.json:

```bash
$ npm i -S @softwarepioniere/ionic-application-insights
```

Then add the library to your Angular Root `AppModule`:

```typescript
// Import the Application Insights module and the service provider
import { ApplicationInsightsModule, AppInsightsService } from '@softwarepioniere/ionic-application-insights';

@NgModule({
  imports: [
    // ... your imports

    // Add the Module to your imports
    ApplicationInsightsModule.forRoot({
      instrumentationKey: 'Your-Application-Insights-instrumentationKey'
    })
  ],
  // ... providers / etc
  providers: [ ..., AppInsightsService ],
})
export class YourRootModule { }
```

### What if you don't know your instrumentationKey right away?

```typescript
// Use instrumentationKeySetlater
ApplicationInsightsModule.forRoot({
  instrumentationKeySetlater: true // <--
})

// Then later in your Application somewhere
constructor(
  private appInsightsService: AppInsightsService
) {
  appInsightsService.config = {
    instrumentationKey: __env.APPINSIGHTS_INSTRUMENTATIONKEY // <-- set it later sometime
  }
  // then make sure to initialize and start-up app insights
  appInsightsService.init();
}

```

## Usage

Through out your application you can now use the AppInsightsService class to fire off AppInsights functionality.

```typescript
import { AppInsightsService } from '@softwarepioniere/ionic-application-insights';

export class ShoppingCartComponent {
  public cart: [];
  constructor(private appInsightsService: AppInsightsService) {}

  saveCart(user) {
    // MOCK Example of sending a trackEvent()
    // Saving some sample user & cart product data
    this.appInsightsService.trackEvent('ShoppingCart Saved', { 'user': user.id, 'cart': cart.id });
  }
}
```

## Usage with IONIC in production build

If you build your app in production mode like

```bash
npm run build  
```

the code get minified and the page name was gone. In this case your must set a variable named _aiName_. If this variable is set, it was used as page name to send it to application insights. For Example 

```typescript
...
@IonicPage()
@Component({
    selector: 'page-contact',
    templateUrl: 'contact.page.html',
})
export class ContactPage {
    private aiName: string = "ContactPage";
    
    ...
```

## Usage with Aspnetcore-Angular2-Universal repo or JavaScriptServices ( apps w/ Server-side rendering )

> ie: https://github.com/MarkPieszak/aspnetcore-angular2-universal

First, make sure you are only importing the library & the server within the **browser-app.module** NgModule (do not share it within a common one, as the server isn't able to use this library during it's server-renders).

Secondly, make sure you are calling the `injector` to get AppInsightsService during **ngOnInit**:

```typescript
export class HomeComponent implements OnInit {

    private AIService: AppInsightsService;
    private isBrowser: boolean;

    constructor(@Inject(PLATFORM_ID) private platformId, private injector: Injector) {
        this.isBrowser = isPlatformBrowser(this.platformId);
    }

    ngOnInit() { // <-- 
        if (this.isBrowser) { // <-- only run if isBrowser
            this.AIService = <AppInsightsService>this.injector.get(AppInsightsService); // <-- using the Injector, get the Service
            this.AIService.trackEvent('Testing', { 'user': 'me' });
        } 
    }
}
```

## API

You can see a list of the API here: https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#class-appinsights

```typescript
AppInsightsService.trackEvent()
AppInsightsService.startTrackEvent()
AppInsightsService.stopTrackEvent()
AppInsightsService.trackPageView()
AppInsightsService.startTrackPage()
AppInsightsService.stopTrackPage()
AppInsightsService.trackMetric()
AppInsightsService.trackException()
AppInsightsService.trackTrace()
AppInsightsService.trackDependency()
AppInsightsService.flush()
AppInsightsService.setAuthenticatedUserContext()
AppInsightsService.clearAuthenticatedUserContext()
```

## If using SystemJS

Modify systemjs.config.js...

In System.Config.map, add:

```typescript
      'applicationinsights-js': 'npm:applicationinsights-js/JavaScript/JavaScriptSDK.Module/AppInsightsModule.js',
      '@softwarepioniere/ionic-application-insights': 'npm:@softwarepioniere/ionic-application-insights/dist/index.js'
```

and in System.Config.packages, add:

```typescript
      '.': {
         defaultExtension: 'js'
      }
```

---

# Want to Contribute

## ng-Application-Insights Development

To generate all `*.js`, `*.js.map` and `*.d.ts` files:

```bash
npm run build
```

To lint all `*.ts` files:

```bash
npm run lint
```

## License

MIT © [Mark Pieszak | DevHelp Online](mailto:hello@devhelp.online)

Twitter: [@MarkPieszak](https://twitter.com/MarkPieszak)

## Modified by

MIT © [Torsten Zwoch | Software Pioniere GmbH & Co. KG](mailto:info@softwarepioniere.de)

Twitter: [@TorstenZwoch](https://twitter.com/TZwoch) | [@SoftwarePionier](https://twitter.com/SoftwarePionier)  