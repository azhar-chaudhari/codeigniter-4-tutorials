# codeigniter-4 Localization
CodeIgniter provides several tools to help you localize your application for different languages. While full localization of an application is a complex subject, it’s simple to swap out strings in your application with different supported languages.

This can be set in app/Config/App.php:
```php
<?php

namespace Config;

use CodeIgniter\Config\BaseConfig;

class App extends BaseConfig
{
    // ...

    public string $defaultLocale = 'en';

    public array $supportedLocales = ['en','hi'];
    // ...
}
``` 
## Controller (app\Controllers\LanguageController.php)
This can be set in app\Controllers\LanguageController.php:

```php

<?php

namespace App\Controllers;

use App\Controllers\BaseController;
use CodeIgniter\HTTP\IncomingRequest;
 
class LanguageController extends BaseController
{
	public function index()
	{
		$session = session();
        $locale = service('request')->getLocale();
        $session->remove('lang');
        $session->set('lang',$locale);
        return redirect()->back(); 
	}   
}

``` 


## In Routes (app/Config/Routes.php)
This can be set in app/Config/Routes.php:

```php

$routes->get('lang/{locale}', 'LanguageController::index');

``` 

## Language file 

English  (Language/en/App.php)

```php
<?php

return [
	'app_name' => 'Language Tutorial',
    'validationGroup' => [
        'error' => [
            'invalid_email' => 'The provided email is invalid',
        ],
    ],
    'dashboard' =>[
        'users_count'=>'Total no. of users {0}'
    ]
];

``` 

Hindi  (Language/hi/App.php)

```php
<?php

return [
	'app_name' => 'भाषा ट्यूटोरियल',
    'validationGroup' => [
        'error' => [
            'invalid_email' => 'प्रदान किया गया ईमेल अमान्य है',
        ],
    ],
    'dashboard' =>[
        'users_count'=>'कुल संख्या उपयोगकर्ताओं की संख्या {0}'
    ]
];

``` 
## Usage of language text (in View files)
```php
<?php
//single
echo lang('App.app_name');

//group
echo lang('App.validationGroup.error.invalid_email');

//with parameter
$no_of_users=10;
echo lang('App.dashboard.users_count',[$no_of_users]);
``` 

## Seperate files can be created for pagewise/formwise or as per needed
English  (Language/en/User.php)

```php
<?php

return [
		'first_name' => 'First Name',
		'last_name' => 'Last Name',
		'email' => 'Email',
        'mobile' => 'Mobile',
		'password' => 'Password',
		'confirm_password' => 'Confirm Password',
];

``` 

Hindi  (Language/hi/User.php)

```php
<?php

return [
		'first_name' =>'पहला नाम',
		'last_name' =>  'उपनाम',
		'email' =>  'ईमेल',
        'mobile' =>  'गतिमान',
		'password' =>  'पासवर्ड',
		'confirm_password' =>  'पासवर्ड की पुष्टि कीजिये',
];

``` 
