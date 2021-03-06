## 基础查询

swoft提供一套基础查询，类ActiveRecord方法，方便快捷的实现数据库操作，但是实体必须继承Model类，且不能使用事务，如需使用事务，请使用高级查询。基础查询提供延迟收包和非延迟收包两种方式，非延迟收包一般用于并发使用。

## 新增

```php
$user = new User();
$user->setName("stelin");
$user->setSex(1);
$user->setDesc("this my desc");
$user->setAge(mt_rand(1, 100));

// 直接返回结果
// $result = $user->save();

// 延迟操作
$dataResult = $user->save(true);
$deferResult = $dataResult->getResult();
```

# 删除

**实体删除**

```php
$user = new User();
$user->setAge(126);

// 直接返回结果
// $result = $user->delete();

// 延迟操作
$defer = $user->delete(true);
$result = $defer->getResult();
```

**ID删除**

```php
// 直接返回结果
// $result = User::deleteById(284);

// 延迟操作
$deferResult = User::deleteById(287, true);
$result = $deferResult->getResult();
```

**IDS删除**

```php
// 直接返回结果
// $result = User::deleteByIds([291, 292]);

// 延迟操作
$deferResult = User::deleteByIds([288, 289], true);
$result = $deferResult->getResult();
```

## 更新

```php
$query = User::findById(285);

/* @var User $user */
$user = $query->getResult(User::class);
$user->setName("upateNameUser2");
$user->setSex(0);

// 直接返回结果
$result = $user->update();

// 延迟操作
//$result = $user->update(true);
//$result = $result->getResult();
```

## 查询

**实体查询**

```php
$user = new User();
$user->setSex(1);
$user->setAge(93);
$query = $user->find();

// 直接返回数组结果
// $result = $query->getResult();

// 直接返回User实体对象结果
/* @var User $userResult */
// $userResult = $query->getResult(User::class);

// 延迟操作处理
$defer = $query->getDefer();
// $result = $defer->getResult();

$result = $defer->getResult(User::class);

// sql语句
$ql = $query->getSql();
var_dump($result);
```

**ID查询**

```php
$query = User::findById(236);

// 直接返回数组结果
$result = $query->getResult();

// 直接返回User实体对象结果
/* @var User $userObject */
$userObject = $query->getResult(User::class);

$query = User::findById(238);

// 延迟操作处理
//$deferResult = $query->getDefer()->getResult();

/* @var User $deferResult */
$deferResult = $query->getDefer()->getResult(User::class);
```

**IDS查询**

```php
$query = User::findByIds([416, 417]);
$sql = $query->getSql();

// 延迟操作处理
// $defer = $query->getDefer();
// $result = $defer->getResult(User::class);

// 直接返回数组结果
$result = $query->getResult();
```

**查询器**

```php
// $query = User::query()->select('*')->andWhere('sex', 1)->orderBy('id',QueryBuilder::ORDER_BY_DESC)->limit(3);
//$query = User::query()->selects(['id', 'sex' => 'sex2'])->andWhere('sex', 1)->orderBy('id',QueryBuilder::ORDER_BY_DESC)->limit(3);
$query = User::query()->selects(['id', 'sex' => 'sex2'])->leftJoin(Count::class, 'count.uid=user.id')->andWhere('id', 416)
    ->orderBy('user.id', QueryBuilder::ORDER_BY_DESC)->limit(2);

// 延迟操作处理    
// $result = $query->getResult();

// 直接返回数组结果
$defer = $query->getDefer();
$result = $defer->getResult();
```

## 并发查询

```php
// getDefer方法延迟收包结果
$query1 = User::query()->selects(['id', 'sex' => 'sex2'])->leftJoin(Count::class, 'count.uid=user.id')->andWhere('id', 419)
    ->orderBy('user.id', QueryBuilder::ORDER_BY_DESC)->limit(2)->getDefer();

// getDefer方法延迟收包结果
$query2 = User::query()->select("*")->leftJoin(Count::class, 'count.uid=user.id')->andWhere('id', 420)
    ->orderBy('user.id', QueryBuilder::ORDER_BY_DESC)->limit(2)->getDefer();

// 同时接受数据
$result1 = $query1->getResult();
$result2 = $query2->getResult();
```



