## neo4j csv import
- 데이터를 import 함에 있어 csv 파일만 가능하다.
	- xlsx등의 기타 포맷은 지원하지 않는다. (neo4j 밖에서 특정한 처리를 통해 csv로 변경해줘야 함)
- Neo4j Desktop에서 로컬 컴퓨터에서 원하는 디렉토리 파일을 cypher 구문으로 직접 읽어올 수는 없는 것으로 보인다. 때문에 Neo4j Desktop 프로그램 GUI상에서 먼저 세팅을 해주어야 한다.
- ![image](https://github.com/DAU-FAIRDAY-TEAM6/Documents/assets/97269799/41107b07-5f54-49c6-93c4-90ddbf3fb893)


<br><br><br>


### 1. csv파일을 import folder에 넣기
- 작업하고자 하는 DBMS 인스턴스에서 [Open folder]-[Import]를 누른 후 [neo4j 설치 경로/import] 경로에 csv 파일을 업로드 해준다.

<br><br><br>

### 2. CSV 파일 LOAD
```
LOAD CSV WITH HEADERS FROM 'file:///test.csv' AS row
RETURN row;
```

- LOAD CSV 구문을 활용해서 파일을 읽어올 수 있다.
- 만약 본인의 csv파일에 헤더(header)가 있다면 중간에 WITH HEADERS를 붙이면 된다.
>  위 명령어는 데이터를 저장하는 것이 아닌 단순히 읽는 명령어임.


<br><br><br>


### 3. CSV 파일 내의 데이터를 LOAD한 뒤 CREATE, SET하기
```
LOAD CSV WITH HEADERS FROM 'file:///products.csv' AS row CREATE (n:Product) SET n = row
```

- 데이터를 노드로 저장하기 위해서는 CREATE, SET 구문을 추가적으로 사용해줘야 한다.
- ![image](https://github.com/DAU-FAIRDAY-TEAM6/Documents/assets/97269799/5bf84bbf-184e-4b92-b172-1db7d65bf715)


<br><br><br>


### 4. CREATE한 NODE들 전부 조회
- 조회하기 위해 MATCH 를 사용한다. sql에서 select와 동일
```
MATCH (n) RETURN n
```
- ![image](https://github.com/DAU-FAIRDAY-TEAM6/Documents/assets/97269799/6632e76f-56ce-4c11-b8df-0d88a1b9434f)
- Node와의 Relation을 연결해주기 위해선 관계를 나타내는 별도의 csv 파일이 필요하다.


<br><br><br>


#### 5. Relation 연결
- relation csv 파일에는 n_element_id => m_element_id 를 나타내는 정보가 들어있다.
```
LOAD CSV WITH HEADERS FROM "file:///relation.csv" AS row

MATCH (p1:Person {id:row.id_from}), (p2:Person {id:row.id_to})

CREATE (p1)-[:KNOWS]->(p2);
```

- 이렇게 node와 edge의 import를 통해 다음과 같은 data를 만들어 줄 수 있다.
- ![image](https://github.com/DAU-FAIRDAY-TEAM6/Documents/assets/97269799/b91ac25d-9a22-4ff5-b285-b337a894b487)


<br><br><br>


### Python neo4j 연동
```python
from neo4j import GraphDatabase

  

# Neo4j 서버 정보

uri = "bolt://localhost:7687"  # 본인의 Neo4j 서버 정보에 맞게 변경

username = "neo4j"

password = "rlawlstn1"

  

# Neo4j에 연결하는 함수

def connect_to_neo4j(uri, username, password):

    try:

        driver = GraphDatabase.driver(uri, auth=(username, password))

        return driver

    except Exception as e:

        print("Failed to connect to Neo4j:", str(e))

        return None

  

# 쿼리 실행하는 함수

def run_query(tx, query):

    result = tx.run(query)

    return list(result)

  

# 메인 함수

def main():

    # Neo4j에 연결

    driver = connect_to_neo4j(uri, username, password)

  

    if driver is not None:

        # 쿼리 정의

        query = """

        MATCH (n)

        OPTIONAL MATCH (n)-[r:KNOWS]-(m)

        RETURN n.id AS n_element_id, n.name AS n_name, r AS r_type, m.id AS m_element_id, m.name AS m_name;

        """

  

        # 트랜잭션 시작

        with driver.session() as session:

            # 쿼리 실행

            result = session.write_transaction(run_query, query)

  

            # 결과 출력

            for record in result:

                print("n_element_id:", record["n_element_id"], "n_name:", record["n_name"])

                print("r_type:", record["r_type"])

                print("m_element_id:", record["m_element_id"], "m_name:", record["m_name"])

                print()

                print()

  

        # Neo4j 연결 해제

        driver.close()

  

if __name__ == "__main__":

    main()
```

<br><br><br>

python에서 neo4j에 cypher 쿼리를 날리면 Text형태로 나오기 때문에 결과는 아래와 같이 나온다.
```
c:\Users\kimjinsu\Desktop\4학년1학기\졸업작품\connect_neo4j.py:38: DeprecationWarning: write_transaction has been renamed to execute_write
  result = session.write_transaction(run_query, query)
n_element_id: 3 n_name: donguk
r_type: <Relationship element_id='5:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:2' nodes=(<Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:0' labels=frozenset() properties={}>, <Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:1' labels=frozenset() properties={}>) type='KNOWS' properties={}>
m_element_id: 4 m_name: teahee


n_element_id: 4 n_name: teahee
r_type: <Relationship element_id='5:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:4' nodes=(<Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:1' labels=frozenset() properties={}>, <Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:5' labels=frozenset() properties={}>) type='KNOWS' properties={}>
m_element_id: 2 m_name: bumgue


n_element_id: 4 n_name: teahee
r_type: <Relationship element_id='5:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:3' nodes=(<Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:1' labels=frozenset() properties={}>, <Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:4' labels=frozenset() properties={}>) type='KNOWS' properties={}>
m_element_id: 1 m_name: kimjinsu


n_element_id: 1 n_name: kimjinsu
r_type: <Relationship element_id='5:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:0' nodes=(<Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:4' labels=frozenset() properties={}>, <Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:5' labels=frozenset() properties={}>) type='KNOWS' properties={}>
m_element_id: 2 m_name: bumgue


n_element_id: 2 n_name: bumgue
r_type: <Relationship element_id='5:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:1' nodes=(<Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:5' labels=frozenset() properties={}>, <Node element_id='4:fcb118a2-ffb6-4d13-bbbe-ebb2c5ba2aed:0' labels=frozenset() properties={}>) type='KNOWS' properties={}>
m_element_id: 3 m_name: donguk
```

<br><br>

neo4j desktop에서 본 반환 값은 아래 사진과 같다
![image](https://github.com/DAU-FAIRDAY-TEAM6/Documents/assets/97269799/c19b2c0b-ff44-47f1-a5ba-f5813b7f5a98)


