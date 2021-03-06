# கட்டமைப்பு

- [அறிமுகம்](#introduction)
- [நிறுவியதன் பின்னர்](#after-installation)
- [உள்ளமைவு மதிப்புகளை அணுகுதல்](#accessing-configuration-values)
- [சூழல் கட்டமைப்பு](#environment-configuration)
- [கட்டமைப்பு விரைவாக்க](#configuration-caching)
- [பராமரிப்பு-முறை](#maintenance-mode)
- [நுண்ணயம் வாய்ந்த URLs](#pretty-urls)

<a name="introduction"></a>
## அறிமுகம்

All of the configuration files for the Laravel framework are stored in the `config` directory. Each option is documented, so feel free to look through the files and get familiar with the options available to you.

<a name="after-installation"></a>
## நிறுவியதன் பின்னர்

### உங்கள் பிரயோகங்களிற்கு   பெயரிடுதல்

After installing Laravel, you may wish to "name" your application. By default, the `app` directory is namespaced under `App`, and autoloaded by Composer using the [PSR-4 autoloading standard](http://www.php-fig.org/psr/psr-4/). However, you may change the namespace to match the name of your application, which you can easily do via the `app:name` Artisan command.

For example, if your application is named "Horsefly", you could run the following command from the root of your installation:

	php artisan app:name Horsefly

Renaming your application is entirely optional, and you are free to keep the `App` namespace if you wish.

### மற்ற கட்டமைப்பு

Laravel needs very little configuration out of the box. You are free to get started developing! However, you may wish to review the `config/app.php` file and its documentation. It contains several options such as `timezone` and `locale` that you may wish to change according to your location.

Once Laravel is installed, you should also [configure your local environment](/docs/5.0/configuration#environment-configuration).

> **Note:** You should never have the `app.debug` configuration option set to `true` for a production application.

<a name="permissions"></a>
### அனுமதிகள்

Laravel may require one set of permissions to be configured: folders within `storage` and `vendor` require write access by the web server.

<a name="accessing-configuration-values"></a>
## உள்ளமைவு மதிப்புகளை அணுகுதல்

You may easily access your configuration values using the `Config` facade:

	$value = Config::get('app.timezone');

	Config::set('app.timezone', 'America/Chicago');

You may also use the `config` helper function:

	$value = config('app.timezone');

<a name="environment-configuration"></a>
## சூழல் கட்டமைப்பு

It is often helpful to have different configuration values based on the environment the application is running in. For example, you may wish to use a different cache driver locally than you do on your production server. It's easy using environment based configuration.

To make this a cinch, Laravel utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library by Vance Lucas. In a fresh Laravel installation, the root directory of your application will contain a `.env.example` file. If you install Laravel via Composer, this file will automatically be renamed to `.env`. Otherwise, you should rename the file manually.

All of the variables listed in this file will be loaded into the `$_ENV` PHP super-global when your application receives a request. You may use the `env` helper to retrieve values from these variables. In fact, if you review the Laravel configuration files, you will notice several of the options already using this helper!

Feel free to modify your environment variables as needed for your own local server, as well as your production environment. However, your `.env` file should not be committed to your application's source control, since each developer / server using your application could require a different environment configuration.

If you are developing with a team, you may wish to continue including a `.env.example` file with your application. By putting place-holder values in the example configuration file, other developers on your team can clearly see which environment variables are needed to run your application.

#### தற்போதைய பயன்பாடு சூழலை  அணுகுதல்

You may access the current application environment via the `environment` method on the `Application` instance:

	$environment = $app->environment();

You may also pass arguments to the `environment` method to check if the environment matches a given value:

	if ($app->environment('local'))
	{
		// The environment is local
	}

	if ($app->environment('local', 'staging'))
	{
		// The environment is either local OR staging...
	}

To obtain an instance of the application, resolve the `Illuminate\Contracts\Foundation\Application` contract via the [service container](/docs/5.0/container). Of course, if you are within a [service provider](/docs/5.0/providers), the application instance is available via the `$this->app` instance variable.

An application instance may also be accessed via the `app` helper or the `App` facade:

	$environment = app()->environment();

	$environment = App::environment();

<a name="configuration-caching"></a>
## கட்டமைப்பு விரைவாக்க

To give your application a little speed boost, you may cache all of your configuration files into a single file using the `config:cache` Artisan command. This will combine all of the configuration options for your application into a single file which can be loaded quickly by the framework.

You should typically run the `config:cache` command as part of your deployment routine.

<a name="maintenance-mode"></a>
## பராமரிப்பு-முறை

When your application is in maintenance mode, a custom view will be displayed for all requests into your application. This makes it easy to "disable" your application while it is updating or when you are performing maintenance. A maintenance mode check is included in the default middleware stack for your application. If the application is in maintenance mode, an `HttpException` will be thrown with a status code of 503.

To enable maintenance mode, simply execute the `down` Artisan command:

	php artisan down

To disable maintenance mode, use the `up` command:

	php artisan up

### பராமரிப்பு முறை பதில் மாதிரி

பராமரிப்பு முறை பதில்களுக்கான  இயல்புநிலை மாதிரி  இங்கே அமைந்துள்ளது `resources/views/errors/503.blade.php`.

### Maintenance Mode & Queues

While your application is in maintenance mode, no [queued jobs](/docs/5.0/queues) will be handled. The jobs will continue to be handled as normal once the application is out of maintenance mode.

<a name="pretty-urls"></a>
## நுண்ணயம் வாய்ந்த URLs

### Apache

The framework ships with a `public/.htaccess` file that is used to allow URLs without `index.php`. நீங்கள் உங்கள் Laravel பயன்பாட்டுச் செய்நிரலில் Apacheயை பயன்படுத்தினால்  `mod_rewrite` தொகுதியை  செயல்படுத்த வேண்டும்..

If the `.htaccess` file that ships with Laravel does not work with your Apache installation, try this one:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]

If your web host doesn't allow the `FollowSymlinks` option, try replacing it with `Options +SymLinksIfOwnerMatch`.

### Nginx

Nginx-ல் , பின்வரும் உத்தரவு   உங்கள் தளத்தின்  கட்டமைப்பில்  "நுன்னயமான " URL-ஐ அனுமதிக்கும்:

	location / {
		try_files $uri $uri/ /index.php?$query_string;
	}


[Homestead](/docs/5.0/homestead)-ஐ பயன்ப்படுத்தும் பொழுது நிச்சயமாக நுன்னயமான URL-கள் தானாக உள்ளமைக்கப்படும்