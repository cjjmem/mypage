# HTTP路由实例教程（三）—— CSRF攻击原理及其防护

某些路由需要避过laravel Middleware 的中间件VerifyCsrfToken

```php
namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as BaseVerifier;

class VerifyCsrfToken extends BaseVerifier
{
    /**
     * The URIs that should be excluded from CSRF verification.
     *
     * @var array
     */
    protected $except = [
        'callback',
        'ivr'
    ];
    //路由 http://127.0.0.1/callback
}
```


