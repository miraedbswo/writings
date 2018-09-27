###### 이 md 파일은 https://docs.mongodb.com/manual/introduction/ 
###### document를 기반으로 작성된 문서입니다.
###### 본인의 번역과 번역기를 통한 번역으로 오역이 있을 수 있습니다.

# MongoDB
* MongoDB란?
 - 고성능, 고 가용성 및 자동 확장 기능을 제공하는 오픈 소스 문서 데이터베이스

* MongoDB는 필드와 값 쌍으로 구성된 데이터 구조인 문서이다. JSON 객체와 유사한 구조를 가지고 있다.
```python
{
    name: "Goming",
    age: 17,
    groups: [ "news", "sports" ]
}
```

field: value 형태 이다.

--------------------------------------

# MongoDB의 주요 특징

### 1. 고성능
* index가 더욱 빠른 쿼리들을 지원하고, embedded document, embedded array의 key를 포함할 수 있다.

### 2. 풍부한 쿼리 언어
* MongoDB는 읽기 및 쓰기 작업을 지원하는 풍부한 쿼리 언어를 지원한다.

### 3. 고 가용성
* MongoDB의 복제 기능은 automatic failover과 data 중복을 지원합니다. (고유 id가 존재하기 때문에 중복된 값을 지원함.)

### 4. 수평 확장성
MongoDB는 핵심 기능의 일부로 수평 확장성을 제공한다.

* Sharding은 여러 대의 컴퓨터에서 데이터를 분산시킨다.

#### ... 여러 저장소 엔진 지원
* MongoDB는 다중 저장 엔진을 제공한다.

-------------------------------------

# MongoDB Shell

Mongo shell은 JavaScript 인터페이스와 MongoDB가 상호작용 한다. 
* Mongo shell을 사용하여 데이터를 쿼리하고, 업데이트 하며, 관리 작업을 수행할 수 있다.

--------------------------------

# Getting start MongoDB
* mongo 쉘을 시작하기위해, 터미널 창에 들어간다.
``` 
cd <mongodb 설치 디렉토리>
./bin/mongo
```
환경 변수 path에 "<mongodb 설치  디렉토리>Server\4.0\bin" 을 추가해주면  
디렉토리를 입력해 주지 않고도 mongo shell에 접근할 수 있다.

----------------------------------------

## Options

어떤 인자도 없이 mongo를 실행하면, MongoDB instance는 hocalhost, port 27017로 실행된다.  

* 다른 호스트 또는 포트 번호를 지정해 줄 수도 있다.

-------------------------------------------

# Working with the mongo Shell
사용중인 데이터베이스를 표시하려면,
```sql
db
```
를 입력해라.

default database이며 기본 값인 test가 return 되어야 한다.

database를 switch(전환)하려면, use <db> 명령어를 사용하자.
```sql
use <database>
use MyNewDatabase
```

그러면, 당신은 아무것도 존재하지 않은 database로 switch 할 수 있습니다. 
데이터베이스에 첫번째 데이터를 저장할 때, MongoDB는 collection을 생성한다.
```sql
use MyNewDatabase
db.myCollection.insertOne( { x: 1 } );
```

db.myCollection.insertOne()은 몽고 쉘에서 사용할 수 있는 메서드 중 하나이다.

* db는 current database를 지칭한다.
* myCollection은 collection의 이름이다.

mongo shell이 collection의 이름을 accept하지 못한다면, db.getCollection() syntax를 사용하는 대안을 사용할 수 있습니다.
* 사용하는 예 )
  * collection 이름에 공백이나 하이픈이 포함되는 경우, 숫자로 시작되는 경우
  * 내장 함수와 충돌하는 경우

```sql
db.getCollection("3 test").find()
db.getCollection("3-test").find()
db.getCollection("stats").find()
```

mongo shell prompt는 각 줄에 4095의 코드 포인트의 제한이 있다.  
코드 포인트가 4095 개가 넘는 행을 입력하면 쉘이 이를 자르게 된다.

mongo shell에서의 기본적인 MongoDB 작업에 대한 자세한 내용을 더 다루어 보겠다.

-------------------------------------

# Insert a Single Document

db.collection.insertOne()는 single document를 collection으로 삽입한다.

밑의 예제는 새로운 document를 inventory collection에 넣는 예제이다.
document가 _id 필드를 지정하지 않으면, MongoDB는 ObjectId value를 _id 필드에 추가한다.

```sql
db.inventory.insertOne(
   { item: "canvas", qty: 100, tags: ["cotton"], size: { h: 28, w: 35.5, uom: "cm" } }
)
```

InsertOne()은 _id field가 포함되었고 새로 삽입된 document를 return 한다.

방금 삽입한 document를 검색하려면, query해라.
```sql
db.inventory.find( { item: "canvas" } )
```

# Insert Multiple Documents

db.collection.insertMany()를 사용해서 multiple document를 collection으로 삽입할 수 있다.
* 메서드에 배열을 인자로 보내는 방식을 사용한다.

밑의 예제는 세 개의 새로운 document를 inventory collection에 넣는 예제이다.  
이 또한 역시 _id field를 지정해주지 않으면, MongoDb가 ObjectId값이 포함된 _id field를 각 document에 추가한다.

```sql
db.inventory.insertMany([
   { item: "journal", qty: 25, tags: ["blank", "red"], size: { h: 14, w: 21, uom: "cm" } },
   { item: "mat", qty: 85, tags: ["gray"], size: { h: 27.9, w: 35.5, uom: "cm" } },
   { item: "mousepad", qty: 25, tags: ["gel", "blue"], size: { h: 19, w: 22.85, uom: "cm" } }
])
```

insertMany()는 새로 삽입된 document들을 _id field와 함께 return 한다.

document를 검색하려면, 또한 query 하자.
```
db.inventory.find( {} )
```

----------------------------------

# Query Documents

Python 드라이버의 PyMongo를 사용하면서 python에서 쿼리 연산을 제공한다.

* pymongo.collection.Collection.find() 

밑의 예제는 inventory collection를 사용한 예제이다.

```sql
db.inventory.insert_many([
    {"item": "journal",
     "qty": 25,
     "size": {"h": 14, "w": 21, "uom": "cm"},
     "status": "A"},
    {"item": "notebook",
     "qty": 50,
     "size": {"h": 8.5, "w": 11, "uom": "in"},
     "status": "A"},
    {"item": "paper",
     "qty": 100,
     "size": {"h": 8.5, "w": 11, "uom": "in"},
     "status": "D"},
    {"item": "planner",
     "qty": 75, "size": {"h": 22.85, "w": 30, "uom": "cm"},
     "status": "D"}
])
```

## Select All Documents in a Collection

모든 document에 있는 collection을 선택하기 위해서, 빈 document를 쿼리 필터의 매개 변수로 find 메서드에 전달한다.

* 쿼리 필터의 매개 변수는 선택 기준을 결정한다.

```python
cursor = db.inventory.find({})
```

이는 밑의 SQL문과 같은 역할을 한다.

```sql
SELECT * FROM inventory
```

### Specify Equality Condition

특별한 조건을 지정해 주려면, 쿼리 필터의 매개 변수로 
```python
{ <field>: <value> }
```
를 사용한다.

밑의 코드는 inventory에서 "status"가 "D" 인 document를 찾는 쿼리이다.

```python
cursor = db.inventory.find({"status": "D"})
```

이는 밑의 SQL문과 같은 역할을 한다.

```sql
SELECT * FROM inventory WHERE status = "D"
```

### Specify AND Conditions

collection의 documents에서 둘 이상의 필드에 대한 조건을 지정할 수 있다. 
<br></br>
논리 AND 결합은 복합 쿼리의 절을 연결하여 쿼리가 모든 조건과 일치하는 documents를 match 한다.

밑의 예제는 status가 A 이고, qty가 30보다 작은 document를 검색하는 예제이다.

```python
cursor = db.inventory.find({"status": "A", "qty": {"$lt": 30}})
```

($lt) 는 뒤의 숫자보다 작은 수 라는 의미이다.

이는 밑의 SQL 문과 같은 역할을 한다.
```sql
SELECT * FROM inventory WHERE status = "A" AND qty < 30
```

### Specify OR Conditions
$ or 연산자를 사용하여 각 절을 논리 OR 으로 복합 조회를 하여 최소한 하나의 조건과 일치하는 document를 검색할 수 있다.

밑의 예제에서는 status가 "A" 이거나 qty가 30보다 작은 document를 검색하는 예제이다.

```python
cursor = db.inventory.find(
    {"$or": [{"status": "A"}, { "qty": {"$lt": 30}}]})
```

이는 밑의 SQL 문과 같은 역할을 한다.
```sql
SELECT * FROM inventory WHERE status = "A" OR qty < 30
```

# Update Documents in a Collection

document를 업데이트 하기 위해서, Mongodb는 일부 업데이트 연산자를 지원한다.($ set과 같은)

### Update a Single Document

밑의 예제는 update_one()메서드를 사용한 예제이다. item이 "paper"인 document를 update한다.

```python
db.inventory.update_one(
    {"item": "paper"},
    {"$set": {"size.uom": "cm", "status": "P"},
    "$currentDate": {"lastModified": True}}
)
```

### Update Multiple Documents

밑의 예제는 update_many() 메서드를 사용한 예제이다.
qty가 50 미만인 모든 document들을 update한다.

```python
db.inventory.update_many(
    {"qty": {"$lt": 50}},
    {"$set": {"size.uom": "in", "status": "P"},
    "$currentDate": {"lastModified": True}}
)
```

### Replace a Document

_id 필드를 제외한 문서의 전체 내용을 바꾸려면 두번째 인수로 완전히 새로운 document를 전달해야 한다.

replace_one()이라는 메서드를 사용한다.

문서를 바꿀 때, 바꿀 document는 오직 field/value로 이루어진 짝의 값을 가져야 한다.

바뀐 문서는 원본 문서와 다른 필드를 가질 수 있다. 그러나 _id 필드에서의 값은 불변이므로 필드를 생략하거나, 기존 값과 같은 값을 가지는 _id 필드를 가져야 한다.

다음 예제에서는 item이 paper인 document를 새로운 document로 바꾸는 예제이다.

```python
db.inventory.replace_one(
    {"item": "paper"},
    {"item": "paper",
     "instock": [
         {"warehouse": "A", "qty": 60},
         {"warehouse": "B", "qty": 40}]})
```