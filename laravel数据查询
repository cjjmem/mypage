#

```
$name = $request->get('name');
$skill_id = $request->get('skill_id');
$number = $request->get('number');
$state = $request->get('state');
$userslist = User::where(function ($query) use ($name,$number,$skill_id,$state){
        if($skill_id>0)
            $query->where('skill_id',$skill_id);
        if($name)
            $query->where('name','like','%'.$name.'%');
        if($number)
            $query->where('number','like','%'.$number.'%');
    })
    ->paginate($request->get('pageSize'));
```
```
        $userslist = User::where('name','like','%'.$options['name'].'%')
            ->where('number','like','%'.$options['number'].'%')
            ->where('skill_id',$options['skill_id'])
            ->paginate($request->get('pageSize'));
```
