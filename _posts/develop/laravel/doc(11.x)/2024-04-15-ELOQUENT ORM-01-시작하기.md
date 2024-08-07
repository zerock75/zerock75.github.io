---
title: ELOQUENT ORM - 1. 시작하기
categories: [Laravel, 문서(11.x), ELOQUENT ORM]
---

## 시작하기
라라벨은 데이터베이스와 상호작용하는 것을 즐겁게 만들어 주는 객제-관계 매핑(ORM)인 Eloquent를 포함하고 있다.
Eloquent를 사용할 때, 각 데이터베이스 테이블은 해당 테이블과 상호작용하는 "모델"이 있다. 
데이터베이스 테이블에서 레코드를 검색하는 것 이외에도, Eloquent 모델을 사용하여 테이블에서 레코드를 삽입, 업데이트, 삭제할 수 있다.

> 시직하기 전에, 애플리케이션의 `config/database.php` 설정 파일에서 데이터베이스 연결을 구성해야 한다. 
> 데이터베이스를 구성하는 방법에 대한 자세한 정보는 [데이터베이스 구성 문서](https://laravel.com/docs/11.x/database#configuration){: target="_blank"}를 확인해라.
{: .prompt-tip }

### 라라벨 부트캠프
라라벨이 처음이라면 [라라벨 부트캠프](https://bootcamp.laravel.com/){: target="_blank"}를 해보기 바란다. 
라라벨 부트캠프는 Eloquentㄹ르 사용하여 첫번째 라라벨 애플리케이션을 구축하는 과정을 안내한다.
이것은 라라벨과 Eloquent가 제공하는 모든 것을 살펴보는 훌륭한 방법이다.


## 모델 클래스 생성
Eloquent 모델을 생성하면 모델은 `app\Models` 디렉토리에 위치하며 `Illuminate\Database\Eloquent\Model` 클래스를 확장한다.
새 모델을 생성하기 위해 [Artisan 명령어](https://laravel.com/docs/11.x/artisan){:target="_blank"}인 `make:model`를 사용할 수 있다.
```shell
php artisan make:model Flight
```

모델을 생성할 때 [데이터베이스 마이그레이션](https://laravel.com/docs/11.x/migrations){: target="_blank"}도 함께 생성하고 싶다면, `---migration` 또는 `-m` 옵션을 사용할 수 있다.
```shell
php artisan make:model Flight --migration
```

모델을 생성할 때 팩토리, 시더, 정책, 컨트롤러, 폼 요청과 같은 다양한 유형의 클래스도 함께 생성할 수 있다.
또한, 이러한 옵션들을 조합하여 한번에 여러 클래스를 생성할 수 있다.
```shell
# Generate a model and a FlightFactory class...
php artisan make:model Flight --factory
php artisan make:model Flight -f
 
# Generate a model and a FlightSeeder class...
php artisan make:model Flight --seed
php artisan make:model Flight -s
 
# Generate a model and a FlightController class...
php artisan make:model Flight --controller
php artisan make:model Flight -c
 
# Generate a model, FlightController resource class, and form request classes...
php artisan make:model Flight --controller --resource --requests
php artisan make:model Flight -crR
 
# Generate a model and a FlightPolicy class...
php artisan make:model Flight --policy
 
# Generate a model and a migration, factory, seeder, and controller...
php artisan make:model Flight -mfsc
 
# Shortcut to generate a model, migration, factory, seeder, policy, controller, and form requests...
php artisan make:model Flight --all
php artisan make:model Flight -a
 
# Generate a pivot model...
php artisan make:model Member --pivot
php artisan make:model Member -p
```

### 모델 검사하기
`artisan model:show <ModelName>` 명령어로 모델의 모든 속성과 관계에 대한 개요를 제공한다.
```shell
php artisan model:show Flight
```

## Eloquent 모델 컨벤션
```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    // ...
}
```

### 테이블 이름
위의 예제를 살펴보면, 우리가 Eloquent에 `Flight` 모델에 해당하는 데이터베이스 테이블을 명시적으로 지정하지 않았다는 것을 알 수 있다.
규칙에 따라 클래스의 이름을 "**스네이크 케이스**"로 변환하고 복수형으로 만든 이름이 테이블 이름으로 사용된다.
다른 이름을 명시적으로 지정하지 않는 Eloquent 모델이 `flights` 테이블에 레코드를 저장한다고 가정하며, 
만약 `AirTrafficController`모델은 `air_traffic_controllers` 테이블에 레코드를 저장한다.

이러한 규칙은 모델과 데이터베이스 테이블 간의 명명 관계를 명확하게 하여, 개발자가 추가적인 구성 없이도 빠르게 개발할 수 있게 도와준다.

만약 모델에 해당하는 데이터베이스 테이블 일므이 이 규칙에 맞지 않는다면, 모델에서 `table` 속성을 정의하여 테이블 이름을 수동으로 지정할 수 있다.
```php
<?phpㅇ
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The table associated with the model.
     *
     * @var string
     */
    protected $table = 'my_flights';
}
```

### Primary Keys
Eloquent는 기본적으로 각 모델의 해당 데이터베이스 테이블에 `id`라는 이름의 기본 키 열이 있다고 가정한다.
필요한 경우, 모델에서 `$primaryKey` 속성을 정의하여 모델의 기본 키로 사용되는 다른 열을 지정할 수 있다.
```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The primary key associated with the table.
     *
     * @var string
     */
    protected $primaryKey = 'flight_id';
}
```

또한, Eloquent는 기본키가 증가하는 정수값이라고 가정하며, 이는 Eloquent가 기본키를 자동으로 정수로 캐스팅한다는 것을 의미한다.
증가하지 않거나 숫자가 아닌 기본키를 사용하고 싶다면, 모델에서 `$incrementing`속성을 `false`로 설정해야 한다.
```php
<?php
 
class Flight extends Model
{
    /**
     * Indicates if the model's ID is auto-incrementing.
     *
     * @var bool
     */
    public $incrementing = false;
}
```

만약, 모델의 기본키가 정수가 아닌 경우, 모델에 `protected $keyType` 속성을 정의해야 한다.
이 속성은 `string`값을 가져야 한다.
```php
<?php
 
class Flight extends Model
{
    /**
     * The data type of the primary key ID.
     *
     * @var string
     */
    protected $keyType = 'string';
}
```

### "복합" 기본키
Eloquent는 각 모델이 적어도 하나의 고유한 "ID"를 가지고 있어야 하며, 이 "ID"는 모델의 기본키로 사용된다.
"복합" 기본키는 Eloqent 모델에서 지원되지 않는다. 이를 해결하기 위해서는 uniquid 키로 두개의 열을 묶어서 사용해야 한다.


### UUID 와 ULID 키
Eloquent 모델의 기본키로 자동 증가하는 정수 대시 UUID를 사용할 수 있다. UUID는 고유한 알파벳-숫자 형식의 식별자로, 36자리 길이를 가진다.

UUID를 기본키로 사용하고자 하는 모델에 대해 자동증가하는 정수키 대시 UUID를 사용하기 위해서는 `Illuminate\Database\Eloquent\Concers\HasUuids` 트레이트를 모델에서 사용할 수 있다.
물론, 모델이 [UUID에 해당하는 기본키 칼럼](https://laravel.com/docs/11.x/migrations#column-method-uuid){: target="_blank"}을 가지고 있어야 한다.
```PHP
use Illuminate\Database\Eloquent\Concerns\HasUuids;
use Illuminate\Database\Eloquent\Model;
 
class Article extends Model
{
    use HasUuids;
 
    // ...
}
 
$article = Article::create(['title' => 'Traveling to Europe']);
 
$article->id; // "8f8e8478-9035-4d23-b9a7-62f4d2612ce5"
```

기본적으로 `HasUuids` 트레이트는 모델에 대해 "정렬된" UUID를 생성한다. 이러한 [UUID는 사전식으로 정렬](https://laravel.com/docs/11.x/strings#method-str-ordered-uuid){: target="_blank"}될 수 있어 인덱스된 데이터베이스 저장에 더 효율적이다.

주어진 모델에 대한 UUID 생성 프로세스를 오버로이드하려면, 오델에 `newUniqueId` 메서드를 정의함으로써 가능하다.
또한, 모델에 `uniqueIds` 메서드를 정의하여 UUID를 받아야 할 칼럼들을 지정할 수도 있다.
```php
use Ramsey\Uuid\Uuid;
 
/**
 * Generate a new UUID for the model.
 */
public function newUniqueId(): string
{
    return (string) Uuid::uuid4();
}
 
/**
 * Get the columns that should receive a unique identifier.
 *
 * @return array<int, string>
 */
public function uniqueIds(): array
{
    return ['id', 'discount_code'];
}
```

원한다면 UUID 대신 `ULID`를 사용할 수 있다. ULID는 UUID와 유사하지만 길이가 26자이다. 
정렬된 UUID와 같이 ULID도 사전식으로 정렬 가능하여 데이터베이스 인덱싱에 효율적이다. 
ULID를 사용하려면 모델에 `Illuminate\Database\Eloquent\Concers\HasUlids` 트레이트를 적용해야 한다.
또한 모델이 ULID에 해당하는 기본키 칼럼을 갖추고 있는지 확인해야 한다.
```php
use Illuminate\Database\Eloquent\Concerns\HasUlids;
use Illuminate\Database\Eloquent\Model;
 
class Article extends Model
{
    use HasUlids;
 
    // ...
}
 
$article = Article::create(['title' => 'Traveling to Asia']);
 
$article->id; // "01gd4d3tgrrfqeda94gdbtdk5c"
```


### 타임스탬프
기본적으로 Eloquent는 모델의 해당 데이터베이스 테이블에 `created_at`와 `updated_at` 칼럼이 존재하기를 기대한다.
모델이 생성되거나 업데이트될 때, Eloquent는 이 컬럼들의 값을 자동으로 설정한다.
이 컬럼들을 Eloquent가 자동으로 관리하지 않는다면, 모델에 `$timestamps`속성을 `false`로 정의해야 한다.
```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * Indicates if the model should be timestamped.
     *
     * @var bool
     */
    public $timestamps = false;
}
```

모델의 타임스탬프 형식을 사용자 정의하려면, 모델이 `$dateFormat` 속성을 설정해라.
이 속성은 모델이 배열 또는 JSON으로 직렬화될 때 날짜 속싱이 데이터베이스에 저장되는 방식과 형식을 결정한다.
```php
<?php
 
namespace App\Models;
 
use Illuminate\Database\Eloquent\Model;
 
class Flight extends Model
{
    /**
     * The storage format of the model's date columns.
     *
     * @var string
     */
    protected $dateFormat = 'U';
}
```

타임스탬프를 저장하는데 사용되는 칼럼의 이름을 사용자 정의하려면, 모델에 `CRATED_AT` 및 `UPDATED_AT` 상수를 정의할 수 있다.
```php
<?php
 
class Flight extends Model
{
    const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'updated_date';
}
```

`updated_at` 필드의 자동업데이를 방지하려면, `withoutTimestamps` 메서드에 주어진 클로저 내에서 작업하면 된다.
```php
Model::withoutTimestamps(fn () => $post->increment(['reads']));
```

### 데이터베이스 연결

