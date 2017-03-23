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

# 文件上传案列

confing/filesystems.php 配置层
```
<?php

return [
    'default' => 'local',
    'cloud' => 's3',
    'disks' => [
        'local' => [
            'driver' => 'local',
            'root' => storage_path('app'),
        ],
        'public' => [
            'driver' => 'local',
            'root' => storage_path('app/public'),
            'visibility' => 'public',
        ],
        // 添加配置
        'uploads' => [
            'driver' => 'local',
            // 文件将上传到storage/app/uploads目录
            'root' => storage_path('app/uploads'),
            // 文件将上传到public/uploads目录 如果需要浏览器直接访问 请设置成这个
            //'root' => public_path('uploads'),
        ],
    ],
];
```

view 视图层
```
<form method="post" enctype="multipart/form-data" >    
    <input type="file" name="picture">
    <button type="submit"> 提交 </button>
</form>
```

Controllers 层
```

public function file(Request $request){
    if ($request->isMethod('post')) {
        $file = Input::file('file');
//            $file->getPathname(),
//            $file->getClientOriginalName(),
//            $file->getClientMimeType(),
//            $file->getClientSize(),
//            $file->getError(),
        if($file->isValid()){
            //获取文件格式
            $entension = $file -> getClientOriginalExtension();
            // 设置文件名
            $filename = date('Y-m-d-H-i-s') . '-' . uniqid() . '.' . $entension;
            // 使用我们新建的uploads本地存储空间（目录）
            $realPath = $file->getRealPath();   //临时文件的绝对路径
            $bool = Storage::disk('uploads')->put($filename, file_get_contents($realPath));
            if($bool)
            //上传成功
    }

}



```

# 页面错误信息显示
```
示例1

    return redirect()->back()->withInput()->withErrors('密码有误！');

    @if (count($errors) > 0)
        <div class="tools-alert tools-alert-red">
            <strong>错误</strong>你填写数据有问题！请重新填写
                @foreach ($errors->all() as $error)
                    <p class="text-danger">{{ $error }}</p>
                @endforeach
        </div>
    @endif



示例2

    return redirect()->back()->with('status', 'Update Success! 成功！ :)');

    @if (session('status'))
        <div class="tools-alert tools-alert-green">
            {{ session('status') }}
        </div>
    @endif


```
