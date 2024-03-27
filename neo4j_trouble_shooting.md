## neo4j desktop에서 csv 파일 import
몇주전에 neo4j를 학습하면서 test로 csv파일을 만들고 import 한적이 있다. [여기](https://github.com/dgjinsu/POISON_Docs/blob/main/neo4j/Neo4j.md) <br>
하지만 실제 yelp json파일을 csv로 넣을 땐 다양한 문제들이 생겼고 해결 과정들을 정리해보려 한다. 
<br><br><br><br>

### Review 데이터의 따옴표 처리
 - csv파일로 변경 후 review데이터를 import할 시 다음과 같은 에러가 떴다. 
 ```
At C:\Users\kimjinsu\.Neo4jDesktop\relate-data\dbmss\dbms-eaebc26e-b938-495f-a3fd-8aa68a29ac5c\import\review.csv @ position 7523852 -  there's a field starting with a quote and whereas it ends that quote there seems to be characters in that field after that ending quote. That isn't supported. This is what I read: 'Small practice, moderate prices. They listen to pet owners' concerns, accurate diagnosis. Boarding place is very small, maybe not ideal for big dogs.
"
uKZaU4nnY9EI8Ir7BSA5nA,uKZaU4nnY9EI8Ir7BSA5nA,AfassS1QI0DHsqwuof9-lA,4.0,2008-12-08 17:08:57,"II'
```
- 위 에러를 해석해보면
- `따옴표로 둘러싸인 필드가 문제가 있는 것으로 보인다. 따옴표로 둘러싸인 필드가 끝나는 따옴표 이후에 문자가 남아있는 것으로 보인다. 이는 Neo4j에서 지원되지 않는 형식이다.`
- review 필드 중 Text 필드에 " " 혹은 ' '가 있고, 이 문자 때문에 csv 형식이 맞지 않는 것으로 추측됐다.
- 데이터가 약 700만개가 있어 코드로 파일을 읽는 것을 제외하면 확인할 수 있는 방법이 없어 해결에 어려움을 겪었다.
- " " 과 ' ' 문자를 전부 없애고 시작과 끝에 ' '문자를 넣어주어 해결할 수 있었다.
  - 문자 시작과 끝에 " "은 안 됨
``` python
text = entry['text']
tmp = text.replace("'", "").replace('"', '')
if "'" in cleaned_text or '"' in cleaned_text:
    print(entry['review_id'])
cleaned_text = f"'{tmp}'"
```
<br><br><br><br>


### 대용량 csv 데이터 import 시 메모리 초과
![image](https://github.com/dgjinsu/POISON_Docs/assets/97269799/a511e547-ee51-4de1-b833-7e60dfa1dcd1)
- poi csv 까진 잘 들어갔는데 review 데이터와 user 데이터는 메모리를 초과해버렸다.
- 각 데이터들의 개수는 다음과 같다
  - USER: 1987897 (198만개)
  - REVIEW: 6990280 (699만개)
  - POI: 150346 (15만개)

- **위 문제를 APOC Library를 사용하여 해결했다. **
- APOC LIBRARY란?
  - APOC(Awesome Procedures on Cypher)는 많은 유용한 기능을 추가하는 수백 가지 프로시저와 기능을 제공하는 Neo4j용 추가 기능 라이브러리이다.
- 그 중 사용한 기능은 대용량 데이터를 작은 배치 사이즈로 나눠 하나씩 처리하는 기능이다.
- 코드 부터 보면 아래와 같다.
```
CALL apoc.periodic.iterate(
  'LOAD CSV WITH HEADERS FROM "file:///review.csv" AS row RETURN row',
  'CREATE (n:Review) SET n = row',
  {batchSize:10000, parallel:true}
)
```
- batchSize로 10000을 주어 10000개의 데이터씩 쪼개어 처리할 수 있도록 해주었고, parallel: true 옵션을 주어 병렬로 처리하도록 하였다.
  - 각자 pc마다 버티는 메모리양이 다르기 때문에 알아서 조절해야 함
![image](https://github.com/dgjinsu/POISON_Docs/assets/97269799/196637bb-9524-47a3-a241-084555a25e48)
<br><br><br><br>


### 노드간 연결 시 소요 시간
- yelp 데이터 셋을 전부 삽입까진 성공했고, 이제 relation 정보를 찾아 edge로 연결해주어야 했다.
- 이전에 csv 데이터를 import 하듯이 apoc.periodic.iterate 함수를 사용해서 데이터를 넣으려 했다.
- 작성한 코드는 아래와 같다.
```
CALL apoc.periodic.iterate(
  'MATCH (r:Review) RETURN r',
  'MATCH (r:Review)
   MATCH (u:User {user_id: r.user_id})
   MATCH (p:Poi {business_id: r.business_id})
   MERGE (u)-[:WROTE]->(r)
   MERGE (r)-[:VISIT]->(p)',
  { batchSize: 10000, parallel: true }
)
YIELD batches, total, errorMessages
RETURN batches, total, errorMessages;
```
- 모든 review데이터를 조회한 후 각각 review데이터를 작성한 User와 POI(Business)를 찾아 연결하는 쿼리이다.
- 한 review데이터를 처리할 때 198만개의 user데이터를 탐색해야 하고, 15만개의 poi데이터를 탐색해야 한다. 이 과정을 700만번 해야했기 때문에 몇 시간이 지나도 작업이 끝나질 않았다.
- **위 문제를 Index를 걸어 해결하였다. **
![image](https://github.com/dgjinsu/POISON_Docs/assets/97269799/eeea0127-b733-48a3-812f-34ffc5d701f4)
- 위와 같이 Index를 걸어 조회의 성능을 개선했다.
- 몇시간이 지나도 끝나질 않던 작업이 10초도 걸리지 않는 시간으로 처리할 수 있었다.
